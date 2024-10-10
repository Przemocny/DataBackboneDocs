# Przetwarzanie danych (wskaźników)

## Wprowadzenie

Akcja przetwarzająca dane w systemie Data Backbone to funkcja, która operuje na danych zasobów (resources) w celu wygenerowania wskaźników (indicators). Te wskaźniki są kluczowymi metrykami do analizy i monitorowania różnych aspektów biznesowych. Każdy wskaźnik posiada w kluczu `action` określoną akcję pobierania, która jest głównym mechanizmem umożliwiającym zasilanie systemu danymi.

## Definicja Akcji Przetwarzającej

Akcja przetwarzająca jest definiowana jako funkcja, która przyjmuje dwa parametry:

- `Indicator`: obiekt wskaźnika, który zawiera opis, strukturę i metadane wskaźnika.
- `MongoClient`: instancja klienta MongoDB, dzięki której możliwa jest interakcja z bazą danych.

Rezultatem tej funkcji jest przetworzony zestaw danych, zazwyczaj w formie listy słowników, gdzie każdy słownik reprezentuje pojedynczy zrekord danych wskaźnika.

### Typ Akcji Przetwarzającej

Typ akcji przetwarzającej dane do wskaźnika jest zdefiniowany jako:

```python
from typing import Callable, List, Dict, Any
from pymongo.mongo_client import MongoClient
from config.types import Indicator

ResourceData = List[Dict[str, Any]]

IndicatorAction = Callable[
   [Indicator, MongoClient],
   ResourceData
]
```

## Przykładowa Implementacja

Poniżej przedstawiono przykładową akcję przetwarzającą, która agreguje dane z zasobów do wskaźnika.

### Definiowanie struktury danych

```python
schema_out = {
    "client": str,
    "id": str,
    "date": str,
    "mapped_payments_to_worklogs": [
        {
            "cycle_number": int,
            "log_info": [
                {
                    "date_of_log": str,
                    "worked_time": float,
                    "user_name": str,
                    "task_name": str,
                    "task_id": str,
                    "task_tag_name": (str, type(None)),
                    "description": str
                }
            ]
        }
    ]
}
```

### Funkcja `map_payments_to_all_worklogs`

Funkcja przetwarzająca struktury danych.

```python
def map_payments_to_all_worklogs(worklogs, invoices, tasks):
    # Konwertuj listy na DataFrame
    worklogs_df = pd.DataFrame(worklogs)
    invoices_df = pd.DataFrame(invoices)
    tasks_df = pd.DataFrame(tasks)

    # Sortowanie faktur według daty stworzenia (od najstarszej do najnowszej) i resetowanie indeksu
    sorted_invoices = invoices_df.sort_values(by='created').reset_index(drop=True)

    # Konwersja kolumny 'created' na datetime
    sorted_invoices['created'] = pd.to_datetime(sorted_invoices['created'], unit='s')

    # Tworzenie kolumny 'end_of_cycle'
    sorted_invoices['end_of_cycle'] = sorted_invoices['created'].shift(-1)
    sorted_invoices.loc[sorted_invoices.index[-1], 'end_of_cycle'] = sorted_invoices['created'].iloc[-1] + pd.Timedelta(days=14)

    # Dodanie kolumny 'cycle_number'
    sorted_invoices['cycle_number'] = np.arange(1, len(sorted_invoices) + 1)

    # Tworzenie DataFrame mapped_invoices_to_cycles
    mapped_invoices_to_cycles_df = sorted_invoices[['cycle_number', 'created', 'end_of_cycle']].rename(columns={'created': 'invoice_creation_date_object'})

    # Znalezienie wszystkich dzienników pracy dla cykli
    mapped_worklogs_to_invoices = find_all_logs_for_cycles(mapped_invoices_to_cycles_df, worklogs_df, tasks_df)

    # Zwróć zmapowane dzienniki pracy do faktur
    return mapped_worklogs_to_invoices
```

### Funkcja `build_worklogs_with_cycle_number`

Główna funkcja przetwarzająca.

```python
def build_worklogs_with_cycle_number(indicator_setting: Indicator, db_client: MongoClient):
    # Pobierz dane z zasobów StripeInvoices i ClickupTimeEntries
    stripe_invoices = list(db_client['RESOURCES']['StripeInvoices'].find())
    clickup_time_entries = list(db_client['RESOURCES']['ClickupTimeEntries'].find())
    clickup_tasks = list(db_client['RESOURCES']['ClickupTasks'].find())
    clients = list(db_client['RESOURCES']['ClientMapping'].find())

    final_results = pd.DataFrame(columns=['client', 'id', 'date', 'mapped_payments_to_worklogs'])

    for client in clients:
        filtered_invoices = filter_stripe_invoices(stripe_invoices, client['stripe_customer_id'])
        filtered_time_entries = filter_clickup_entries(clickup_time_entries, client['clickup_folder_id'])
        filtered_tasks = filter_clickup_tasks(clickup_tasks, int(client['clickup_folder_id']))

        mapped_payments_to_worklogs = map_payments_to_worklogs_from_first_invoice(filtered_time_entries, filtered_invoices, filtered_tasks)

        new_row = {
            'client': client['company_name'],
            'id': client['company_name'],
            'date': datetime.now().strftime('%Y-%m-%d'),
            'mapped_payments_to_worklogs': mapped_payments_to_worklogs
        }
        final_results = pd.concat([final_results, pd.DataFrame([new_row])], ignore_index=True)

    return Validator.validate(final_results, schema_out).to_dict(orient='records')
```

### Całość

```python
from collections import OrderedDict, defaultdict
import uuid
import pandas as pd
import numpy as np
from __indicators__.types import Indicator, MongoClient
from datetime import datetime, timedelta
from decimal import Decimal

from __indicators__.utils.build_worklogs_with_cycle_number import (
    filter_clickup_entries,
    filter_clickup_tasks,
    filter_stripe_invoices,
    map_payments_to_worklogs_from_first_invoice,
    find_all_logs_for_cycles
)

# Funkcja przetwarzająca struktury danych
def map_payments_to_all_worklogs(worklogs, invoices, tasks):
    # Konwertuj listy na DataFrame
    worklogs_df = pd.DataFrame(worklogs)
    invoices_df = pd.DataFrame(invoices)
    tasks_df = pd.DataFrame(tasks)

    # Sortowanie faktur według daty stworzenia (od najstarszej do najnowszej) i resetowanie indeksu
    sorted_invoices = invoices_df.sort_values(by='created').reset_index(drop=True)

    # Konwersja kolumny 'created' na datetime
    sorted_invoices['created'] = pd.to_datetime(sorted_invoices['created'], unit='s')

    # Tworzenie kolumny 'end_of_cycle'
    sorted_invoices['end_of_cycle'] = sorted_invoices['created'].shift(-1)
    sorted_invoices.loc[sorted_invoices.index[-1], 'end_of_cycle'] = sorted_invoices['created'].iloc[-1] + pd.Timedelta(days=14)

    # Dodanie kolumny 'cycle_number'
    sorted_invoices['cycle_number'] = np.arange(1, len(sorted_invoices) + 1)

    # Tworzenie DataFrame mapped_invoices_to_cycles
    mapped_invoices_to_cycles_df = sorted_invoices[['cycle_number', 'created', 'end_of_cycle']].rename(columns={'created': 'invoice_creation_date_object'})

    # Znalezienie wszystkich dzienników pracy dla cykli
    mapped_worklogs_to_invoices = find_all_logs_for_cycles(mapped_invoices_to_cycles_df, worklogs_df, tasks_df)

    # Zwróć zmapowane dzienniki pracy do faktur
    return mapped_worklogs_to_invoices

# Definiowanie struktury danych
schema_out = {
    "client": str,
    "id": str,
    "date": str,
    "mapped_payments_to_worklogs": [
        {
            "cycle_number": int,
            "log_info": [
                {
                    "date_of_log": str,
                    "worked_time": float,
                    "user_name": str,
                    "task_name": str,
                    "task_id": str,
                    "task_tag_name": (str, type(None)),
                    "description": str
                }
            ]
        }
    ]
}

# Główna funkcja przetwarzająca
def build_worklogs_with_cycle_number(indicator_setting: Indicator, db_client: MongoClient):
    # Pobierz dane z zasobów StripeInvoices i ClickupTimeEntries
    stripe_invoices = list(db_client['RESOURCES']['StripeInvoices'].find())
    clickup_time_entries = list(db_client['RESOURCES']['ClickupTimeEntries'].find())
    clickup_tasks = list(db_client['RESOURCES']['ClickupTasks'].find())
    clients = list(db_client['RESOURCES']['ClientMapping'].find())

    final_results = pd.DataFrame(columns=['client', 'id', 'date', 'mapped_payments_to_worklogs'])

    for client in clients:
        filtered_invoices = filter_stripe_invoices(stripe_invoices, client['stripe_customer_id'])
        filtered_time_entries = filter_clickup_entries(clickup_time_entries, client['clickup_folder_id'])
        filtered_tasks = filter_clickup_tasks(clickup_tasks, int(client['clickup_folder_id']))

        mapped_payments_to_worklogs = map_payments_to_worklogs_from_first_invoice(filtered_time_entries, filtered_invoices, filtered_tasks)

        new_row = {
            'client': client['company_name'],
            'id': client['company_name'],
            'date': datetime.now().strftime('%Y-%m-%d'),
            'mapped_payments_to_worklogs': mapped_payments_to_worklogs
        }
        final_results = pd.concat([final_results, pd.DataFrame([new_row])], ignore_index=True)

    return Validator.validate(final_results, schema_out).to_dict(orient='records')
```

## Podsumowanie

Akcje przetwarzające są kluczowym elementem systemu Data Backbone, umożliwiając generowanie wartościowych wskaźników na podstawie surowych danych zasobów. Dzięki jasno zdefiniowanym typom, dobrze zorganizowanemu kodu oraz przestrzeganiu sprawdzonych praktyk, akcje te mogą być skutecznie i niezawodnie implementowane, co pozwala na pełne wykorzystanie możliwości analizy danych w ramach platformy Data Backbone.
