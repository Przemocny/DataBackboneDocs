# Koncept zasobu i wskaźnika


## Czym jest zasób 
Koncept zasobu odnosi się do połączenia opisu



## Czym jest wskaźnik


Koncept zasobu odnosi się do abstrakcyjnego przedstawienia danych lub funkcji, które są dostępne w systemie, umożliwiając ich łatwe zarządzanie i integrację. Zasoby mogą reprezentować różnorodne elementy, takie jak dane finansowe, użytkownicy czy transakcje, i są często wykorzystywane w aplikacjach do organizacji i strukturyzacji informacji. 



## Struktura JSON Zasobu

```json
 
{
  "name": "StripeInvoices",
  "description": "Zasób faktur ze Stripe",
  "department": "finance",
  "fields": {
    "id": "id",
    "updated": "created"
  },
  "action": "fetch_stripe_invoices"
}

```

## Składowe funkcji fetch_stripe_invoices:

- Niezbędne zależności do realizacji logiki biznesowej
- Schema walidacji danych wychodzących
- Części akcji pobierania zasobu
    - Funkcje collect
    - Funkcja morph_json_with_pandas
    - Funkcja validate_output
- Cały kod akcji pobierania zasobu

### 1. Niezbędne zależności do realizacji logiki biznesowej

```python

import pandas as pd  # Import Pandas do pracy z danymi w formie tabel
from dotenv import load_dotenv  # Import funkcji load_dotenv do ładowania zmiennych środowiskowych z pliku .env
from __resources__.types import Resource, MongoClient
from external_services.stripe.utils import check_if_metadata_exists
from external_services.stripe.stripe_client import collect_invoices_from_stripe, collect_missing_invoices  # Import funkcji do pobierania faktur z Stripe
from typing import List, Dict, Any  # Import typów dla adnotacji
from utils.validator import Validator


```
### 2. Schema walidacji danych wychodzących

```python

schema_out = {
    "id": str,
    "amount_paid": float,
    "total": float,
    "created": int,
    "status": str,
    "customer": str,
    "hours_capacity_per_payment": int
}

```

### 3. Akcja pobierania zasobu

#### 3a. Funkcje collect

```python
# implementacja

import requests  # Import modułu requests do wykonywania żądań HTTP
from dotenv import load_dotenv  # Import funkcji load_dotenv do ładowania zmiennych środowiskowych z pliku .env
import os  # Import modułu os do pracy z systemem operacyjnym

def collect_invoices_from_stripe(accumulator:list=[]):
    NUMBER_OF_MISSING_INVOICES = 3
    load_dotenv()  # Załaduj zmienne środowiskowe z pliku .env
    STRIPE_KEY = os.getenv('STRIPE_KEY')  # Pobierz klucz API Stripe ze zmiennych środowiskowych
    headers = {
        "Authorization": f"Bearer {STRIPE_KEY}"  # Ustaw nagłówki autoryzacyjne dla żądań HTTP
    }
    
    invoices = accumulator  # Inicjalizuj listę faktur

    if len(invoices) == NUMBER_OF_MISSING_INVOICES:
        url = "https://api.stripe.com/v1/invoices?status=paid&limit=100"  # URL do pobierania faktur z Stripe
    if len(invoices) > NUMBER_OF_MISSING_INVOICES:
        url = f"https://api.stripe.com/v1/invoices?status=paid&limit=100&starting_after={invoices[-1]['id']}" 

    response = requests.get(url, headers=headers)  # Wykonaj żądanie HTTP GET do API Stripe

    if response.status_code != 200:  # Sprawdź, czy odpowiedź ma status 200 (OK)
        raise Exception(f"Błąd podczas pobierania danych z collect_invoices_from_stripe. Kod statusu: {response.status_code}")  # Rzuć wyjątek w przypadku błędu

    data = response.json()['data']  # Pobierz dane JSON z odpowiedzi
    if not data:  # Sprawdź, czy dane są puste
        return invoices  # Zakończ rekurencję i zwróć faktury
    
    invoices.extend(data)  # Dodaj pobrane faktury do listy invoices
    return collect_invoices_from_stripe(invoices)


# użycie

from external_services.stripe.utils import check_if_metadata_exists
from external_services.stripe.stripe_client import collect_invoices_from_stripe, collect_missing_invoices  # Import funkcji do pobierania faktur z Stripe


"""Pobierz i przekształć faktury ze Stripe"""
missing_invoices = collect_missing_invoices()  # Pobranie brakujących faktur ze Stripe
all_stripe_invoices = collect_invoices_from_stripe(missing_invoices)  # Pobranie wszystkich faktur ze Stripe

```
#### 3b. Funkcja morph_json_with_pandas

```python


def morph_json_with_pandas(data: List[Dict[str, Any]]) -> List[Dict[str, Any]]:
    """Przekształć dane JSON na DataFrame i przefiltruj kolumny"""
    columns_wanted = [
        'id', 'amount_paid', 'lines', 'total', 'created', 
        'status', 'customer'
    ]  # Definicja kolumn, które chcemy zachować

    df = pd.DataFrame(data)  # Stworzenie DataFrame z danych JSON
    df = df[columns_wanted]  # Zachowanie tylko wybranych kolumn w DataFrame
    
    df['created'] = df['created'].astype(int)  # Przekształcenie kolumny 'created' na typ int

    # Przypisanie wartości do odpowiednich kolumn
    df['id'] = df['id']
    df['amount_paid'] = df['amount_paid']
    df['hours_capacity_per_payment'] = df.apply(lambda x: int(x['lines']['data'][0]['plan']['metadata']['hours_capacity_per_payment']), axis=1)
    df['total'] = df['total']
    df['status'] = df['status']
    df['customer'] = df['customer']

    df.drop(['lines'], axis=1, inplace=True)

    return df

```
 
#### 3c. Funkcja validate_output

```python

def validate_output(df, schema_out):

    return Validator.validate(df,schema_out)


```

### 4. Cały kod akcji pobierania zasobu

```python

import pandas as pd  # Import Pandas do pracy z danymi w formie tabel
from dotenv import load_dotenv  # Import funkcji load_dotenv do ładowania zmiennych środowiskowych z pliku .env
from __resources__.types import Resource, MongoClient
from external_services.stripe.utils import check_if_metadata_exists
from external_services.stripe.stripe_client import collect_invoices_from_stripe, collect_missing_invoices  # Import funkcji do pobierania faktur z Stripe
from typing import List, Dict, Any  # Import typów dla adnotacji
from utils.validator import Validator


schema_out = {
    "id": str,
    "amount_paid": float,
    "total": float,
    "created": int,
    "status": str,
    "customer": str,
    "hours_capacity_per_payment": int
}


def morph_json_with_pandas(data: List[Dict[str, Any]]) -> List[Dict[str, Any]]:
    """Przekształć dane JSON na DataFrame i przefiltruj kolumny"""
    columns_wanted = [
        'id', 'amount_paid', 'lines', 'total', 'created', 
        'status', 'customer'
    ]  # Definicja kolumn, które chcemy zachować

    df = pd.DataFrame(data)  # Stworzenie DataFrame z danych JSON
    df = df[columns_wanted]  # Zachowanie tylko wybranych kolumn w DataFrame
    
    df['created'] = df['created'].astype(int)  # Przekształcenie kolumny 'created' na typ int

    # Przypisanie wartości do odpowiednich kolumn
    df['id'] = df['id']
    df['amount_paid'] = df['amount_paid']
    df['hours_capacity_per_payment'] = df.apply(lambda x: int(x['lines']['data'][0]['plan']['metadata']['hours_capacity_per_payment']), axis=1)
    df['total'] = df['total']
    df['status'] = df['status']
    df['customer'] = df['customer']

    df.drop(['lines'], axis=1, inplace=True)  # Usunięcie kolumny 'lines' z DataFrame
    return df


def validate_output(df, schema_out):
    """Waliduje dane w DataFrame zgodnie z podanym schematem."""
    return Validator.validate(df, schema_out)


def fetch_stripe_invoices(resource_setting: Resource, mongo_client: MongoClient) -> List[Dict[str, Any]]:
    """Pobierz i przekształć faktury ze Stripe"""

    missing_invoices = collect_missing_invoices()  # Pobranie brakujących faktur ze Stripe
    all_stripe_invoices = collect_invoices_from_stripe(missing_invoices)  # Pobranie wszystkich faktur ze Stripe

    df_invoices = pd.DataFrame(all_stripe_invoices)  # Stworzenie DataFrame ze wszystkich faktur

    # Przefiltrowanie faktur, aby usunąć te z określonymi ID
    filtered_all_invoices = df_invoices[~df_invoices['id'].isin(['in_1PTOUuE2fekoERPNjIgQdiNh', 'in_1PXip9E2fekoERPNATlFnNUR'])].to_dict('records') 
    df_invoices = pd.DataFrame(filtered_all_invoices)
    
    df_invoices['lines'] = df_invoices['lines'].apply(check_if_metadata_exists)
    
    filtered_all_invoices = df_invoices.to_dict(orient='records')
    
    filtered_all_invoices = df_invoices.to_dict(orient='records')

    complete_invoices = morph_json_with_pandas(filtered_all_invoices)

    complete_invoices = validate_output(complete_invoices, schema_out)
    
    dict_invoices = complete_invoices.to_dict(orient='records')

    return dict_invoices  # Zwrócenie kompletnych faktur

```