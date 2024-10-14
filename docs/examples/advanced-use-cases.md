# Zaawansowane przypadki użycia

## Wprowadzenie

W tym rozdziale przedstawimy zaawansowany przypadek użycia DataBackbone, koncentrując się na procesie budowania złożonego wskaźnika. Jako przykład wykorzystamy funkcję `build_worklogs_with_cycle_number`, która tworzy wskaźnik `WorklogsWithCycleNumbers`. Ten wskaźnik łączy dane z różnych źródeł i przetwarza je w celu uzyskania cennych informacji biznesowych o dziennikach pracy powiązanych z cyklami rozliczeniowymi.

## Budowanie złożonego wskaźnika

### Wskaźnik `WorklogsWithCycleNumbers`

Wskaźnik `WorklogsWithCycleNumbers` dostarcza informacji o dziennikach pracy (worklogs) powiązanych z cyklami rozliczeniowymi. Łączy on dane z różnych źródeł, takich jak faktury, wpisy czasowe i zadania, aby stworzyć kompleksowy obraz pracy wykonanej w ramach poszczególnych cykli rozliczeniowych.

### Funkcja budująca `build_worklogs_with_cycle_number`

Funkcja `build_worklogs_with_cycle_number` jest odpowiedzialna za tworzenie wskaźnika `WorklogsWithCycleNumbers`.

#### Użycie

```python
def build_worklogs_with_cycle_number(indicator_setting: Indicator, db_client: MongoClient):
    # Implementacja funkcji
    # ...
    return Validator.validate(final_results, schema_out).to_dict(orient='records')
```

#### Kluczowe cechy

- Pobieranie danych z wielu źródeł (faktury, wpisy czasowe, zadania)
- Przetwarzanie i łączenie danych z wykorzystaniem pandas
- Mapowanie płatności do dzienników pracy
- Aktualizacja arkuszy Google dla wybranych klientów
- Walidacja wynikowych danych

### Implementacja

Poniżej przedstawiamy kluczowe elementy implementacji:

1. Pobieranie danych z bazy MongoDB:

```python
stripe_invoices = list(db_client['RESOURCES']['StripeInvoices'].find())
clickup_time_entries = list(db_client['RESOURCES']['ClickupTimeEntries'].find())
clickup_tasks = list(db_client['RESOURCES']['ClickupTasks'].find())
clients = list(db_client['RESOURCES']['ClientMapping'].find())
```

2. Przetwarzanie danych dla każdego klienta:

```python
for client in clients:
    filtered_invoices = filter_stripe_invoices(stripe_invoices, client['stripe_customer_id'])
    filtered_time_entries = filter_clickup_entries(clickup_time_entries, client['clickup_folder_id'])
    filtered_tasks = filter_clickup_tasks(clickup_tasks, int(client['clickup_folder_id']))

    mapped_payments_to_worklogs = map_payments_to_all_worklogs(filtered_time_entries, filtered_invoices, filtered_tasks)
```

3. Aktualizacja arkuszy Google dla wybranych klientów:

```python
if client['company_name'] in project_spreadsheets:
    logs_with_cycle_number = [
        {**log, 'cycle_number': cycle['cycle_number']}
        for cycle in mapped_payments_to_worklogs
        for log in cycle['log_info']
    ]

    structure_for_google_sheet = {
        "logs_with_cycle_number": logs_with_cycle_number
    }

    update_client_google_sheet(client['company_name'], structure_for_google_sheet['logs_with_cycle_number'], worksheet_names['WorklogsWithCycleNumber'])
```

4. Tworzenie końcowego wyniku:

```python
new_row = {
    'client': client['company_name'],
    'id': client['company_name'],
    'date': datetime.now().strftime('%Y-%m-%d'),
    'mapped_payments_to_worklogs': mapped_payments_to_worklogs
}
final_results = pd.concat([final_results, pd.DataFrame([new_row])], ignore_index=True)
```

## Kluczowe komponenty

### Klasa `Indicator`

Klasa `Indicator` reprezentuje strukturę wskaźnika w DataBackbone. Zawiera informacje takie jak nazwa wskaźnika, opis i wymagane pola.

### Klasa `MongoClient`

`MongoClient` to klasa z biblioteki PyMongo, używana do interakcji z bazą danych MongoDB.

### Funkcje pomocnicze

- `filter_stripe_invoices()`: Filtruje faktury Stripe dla danego klienta.
- `filter_clickup_entries()`: Filtruje wpisy czasowe ClickUp dla danego folderu.
- `filter_clickup_tasks()`: Filtruje zadania ClickUp dla danego folderu.
- `map_payments_to_all_worklogs()`: Mapuje płatności do wszystkich dzienników pracy.
- `update_client_google_sheet()`: Aktualizuje arkusz Google dla klienta.

## Schemat wyjściowy

Schemat wyjściowy (`schema_out`) definiuje strukturę danych wynikowych wskaźnika `WorklogsWithCycleNumbers`:

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

## Pełna implementacja

```python
import pandas as pd
import numpy as np
from __indicators__.types import Indicator, MongoClient
from datetime import datetime
from external_services.google.gsheet_utils import update_client_google_sheet
from utils.validator import Validator
from __indicators__.utils.build_worklogs_with_cycle_number import filter_clickup_entries, filter_clickup_tasks, filter_stripe_invoices, find_all_logs_for_cycles
from external_services.client_mappings_details.gsheet_mappings import project_spreadsheets, worksheet_names

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
    # Pobierz dane StripeInvoices i ClickupTimeEntries
    stripe_invoices = list(db_client['RESOURCES']['StripeInvoices'].find())
    clickup_time_entries = list(db_client['RESOURCES']['ClickupTimeEntries'].find())
    clickup_tasks = list(db_client['RESOURCES']['ClickupTasks'].find())
    clients = list(db_client['RESOURCES']['ClientMapping'].find())

    final_results = pd.DataFrame(columns=['client', 'id', 'date', 'mapped_payments_to_worklogs'])

    for client in clients:

        filtered_invoices = filter_stripe_invoices(stripe_invoices, client['stripe_customer_id'])
        filtered_time_entries = filter_clickup_entries(clickup_time_entries, client['clickup_folder_id'])
        filtered_tasks = filter_clickup_tasks(clickup_tasks, int(client['clickup_folder_id']))

        mapped_payments_to_worklogs = None

        mapped_payments_to_worklogs = map_payments_to_all_worklogs(filtered_time_entries, filtered_invoices, filtered_tasks)

        if client['company_name'] in project_spreadsheets:
            # Tworzymy listę słowników bezpośrednio z mapped_payments_to_worklogs
            logs_with_cycle_number = [
                {**log, 'cycle_number': cycle['cycle_number']}
                for cycle in mapped_payments_to_worklogs
                for log in cycle['log_info']
            ]

            # Tworzymy nowy słownik z przekształconymi danymi
            structure_for_google_sheet = {
                "logs_with_cycle_number": logs_with_cycle_number
            }

            update_client_google_sheet(client['company_name'],structure_for_google_sheet['logs_with_cycle_number'], worksheet_names['WorklogsWithCycleNumber'])
        new_row = {
            'client': client['company_name'],
            'id': client['company_name'],
            'date': datetime.now().strftime('%Y-%m-%d'),
            'mapped_payments_to_worklogs': mapped_payments_to_worklogs
        }
        final_results = pd.concat([final_results, pd.DataFrame([new_row])], ignore_index=True)

    return Validator.validate(final_results,schema_out).to_dict(orient='records')



```

## Podsumowanie

Funkcja `build_worklogs_with_cycle_number` demonstruje zaawansowane możliwości DataBackbone w zakresie budowania złożonych wskaźników. Proces tworzenia wskaźnika `WorklogsWithCycleNumbers` obejmuje pobieranie danych z bazy MongoDB, filtrowanie i przetwarzanie danych dla każdego klienta, mapowanie płatności do dzienników pracy, aktualizację zewnętrznych arkuszy Google oraz tworzenie końcowego raportu. Takie podejście pozwala na tworzenie kompleksowych wskaźników biznesowych, które mogą być wykorzystywane do analizy i optymalizacji procesów w firmie.
