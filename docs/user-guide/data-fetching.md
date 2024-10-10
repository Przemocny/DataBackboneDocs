# Pobieranie danych (zasobów)

## Wprowadzenie

Akcja pobierająca to funkcja w systemie Data Backbone, która służy do pobierania danych z zewnętrznych źródeł do zasobów (resources). Każdy zasób posiada w kluczu `action` określoną akcję pobierania, która jest głównym mechanizmem umożliwiającym zasilanie systemu danymi.

## Definicja Akcji Pobierającej

Akcja pobierająca jest zdefiniowana jako funkcja, która przyjmuje dwa parametry:

- `Resource`: obiekt zasobu, który opisuje strukturę i metadane zasobu
- `MongoClient`: instancja klienta MongoDB, którą można wykorzystać do interakcji z bazą danych

Rezultatem tej funkcji jest lista słowników, gdzie każdy słownik reprezentuje pojedynczy rekord danych zasobu.

### Typ Akcji Pobierającej

Typ akcji pobierającej zasób jest zdefiniowany jako:

```python

from typing import Callable, List, Dict, Any
from pymongo.mongo_client import MongoClient
from config.types import Resource

ResourceData = List[Dict[str, Any]]

ResourceAction = Callable[
   [Resource, MongoClient],
   ResourceData
]

```

## Przykładowa Implementacja

Oto przykład prostej akcji pobierającej, która pobiera dane z zewnętrznego API.

### Definiowanie struktury danych

```python
schema_out = {
    "id": str,
    "name": str,
    "status": str,
    "date_created": int,
    "date_updated": int,
    "creator_id": int,
    "creator_username": str,
    "creator_email": str,
    "tags": [{
        "name": str,
        "creator": int
    }],
    "time_spent": int,
    "folder_id": int
}
```

### Funkcja `morph_json_with_pandas`

Funkcja zmieniająca strukturę danych.

```python
def morph_json_with_pandas(data: list) -> Tuple[pd.DataFrame, pd.DataFrame]:
    # Określenie kolumn, które będą potrzebne
    columns_wanted = [
        'id', 'name', 'status', 'date_created', 'date_updated',
        'creator', 'tags', 'time_spent', 'folder'
    ]

    # Konwersja danych do DataFrame
    df = pd.DataFrame(data)
    df = df[columns_wanted]

    # Konwersja dat na typ int
    df['date_updated'] = df['date_updated'].astype(int)
    df['date_created'] = df['date_created'].astype(int)

    # Wyciąganie i przekształcanie danych z kolumn zagnieżdżonych struktur
    df['status'] = df['status'].apply(lambda x: x['status'])
    df['creator_id'] = df['creator'].apply(lambda x: x['id'])
    df['creator_username'] = df['creator'].apply(lambda x: x['username'])
    df['creator_email'] = df['creator'].apply(lambda x: x['email'])
    df['folder_id'] = df['folder'].apply(lambda x: x['id']).astype(int)

    # Wypełnianie brakujących wartości i konwersja na typ int
    df['time_spent'] = df['time_spent'].fillna(0).astype(int)

    # Usuwanie zbędnych kolumn
    df.drop(['creator', 'folder'], axis=1, inplace=True)

    return Validator.validate(df,schema_out)
```

### Funkcja `fetch_tasks`

Funkcja pobierania zasobu.

```python
async def fetch_tasks(resource_setting: Resource, db_client: MongoClient) -> list:
    # Zakładamy, że mamy batche po 10 * 100 tasków na ten moment
    assumed_pages = 10

    # Pobieranie zadań
    data, last_page = await collect_all_tasks(resource_setting, assumed_pages)

    # Przetwarzanie danych
    valid = morph_json_with_pandas(data)

    # Zwracanie poprawnych danych w formie listy słowników
    return valid.to_dict(orient='records')
```

### Całość

```python
from __resources__.types import Resource, MongoClient
import pandas as pd
from external_services.clickup.clickup_client import collect_all_tasks
from typing import TypedDict, List, Dict, Any, Tuple

# Definiowanie struktury danych wchodzących
schema_out = {
    "id": str,
    "name": str,
    "status": str,
    "date_created": int,
    "date_updated": int,
    "creator_id": int,
    "creator_username": str,
    "creator_email": str,
    "tags": [{
        "name": str,
        "creator": int
    }],
    "time_spent": int,
    "folder_id": int
}

# Funkcja zmieniająca strukturę danych
def morph_json_with_pandas(data: list) -> Tuple[pd.DataFrame, pd.DataFrame]:
    # Określenie kolumn, które będą potrzebne
    columns_wanted = [
        'id', 'name', 'status', 'date_created', 'date_updated',
        'creator', 'tags', 'time_spent', 'folder'
    ]

    # Konwersja danych do DataFrame
    df = pd.DataFrame(data)
    df = df[columns_wanted]

    # Konwersja dat na typ int
    df['date_updated'] = df['date_updated'].astype(int)
    df['date_created'] = df['date_created'].astype(int)

    # Wyciąganie i przekształcanie danych z kolumn zagnieżdżonych struktur
    df['status'] = df['status'].apply(lambda x: x['status'])
    df['creator_id'] = df['creator'].apply(lambda x: x['id'])
    df['creator_username'] = df['creator'].apply(lambda x: x['username'])
    df['creator_email'] = df['creator'].apply(lambda x: x['email'])
    df['folder_id'] = df['folder'].apply(lambda x: x['id']).astype(int)

    # Wypełnianie brakujących wartości i konwersja na typ int
    df['time_spent'] = df['time_spent'].fillna(0).astype(int)

    # Usuwanie zbędnych kolumn
    df.drop(['creator', 'folder'], axis=1, inplace=True)

    return Validator.validate(df, schema_out)

# Funkcja pobierania zasobu
async def fetch_tasks(resource_setting: Resource, db_client: MongoClient) -> list:
    # Zakładamy, że mamy batche po 10 * 100 tasków na ten moment
    assumed_pages = 10

    # Pobieranie zadań
    data, last_page = await collect_all_tasks(resource_setting, assumed_pages)

    # Przetwarzanie danych
    valid = morph_json_with_pandas(data)

    # Zwracanie poprawnych danych w formie listy słowników
    return valid.to_dict(orient='records')
```

## Podsumowanie

Akcje pobierające są kluczowym elementem systemu Data Backbone, umożliwiając zasilanie zasobów danymi z różnych zewnętrznych źródeł. Dzięki jasno zdefiniowanem typom, dobrej strukturze kodu oraz przestrzeganiu dobrych praktyk, akcje te mogą być efektywnie i bezawaryjnie implementowane, co pozwala na pełne wykorzystanie możliwości analizy danych w ramach platformy Data Backbone.
