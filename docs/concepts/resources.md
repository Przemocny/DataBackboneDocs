# Zasoby

## Wprowadzenie

Ten przewodnik przeprowadzi Cię przez proces tworzenia nowego zasobu w systemie Data Backbone. Zasób to struktura danych, która pozwala na gromadzenie i zarządzanie danymi z różnych źródeł.

## Definicja Struktury Zasobu

### Typ zasobu:

```python

@dataclass
class RequiredFields:
    id: str # wskazanie klucza id w zasobie
    updated: str # wskazanie klucza ostatniej modyfikacji w zasobie

# typ zasobu
@dataclass
class Resource:
    name: str # nazwa zasobu, jest to także nazwa kolekcji Mongo w bazie "RESOURCES"
    description: str # opis zasobu
    department: str # opis działu, do którego przypisany jest zasób
    fields: RequiredFields # pola wymagane  do zmapowania w zasobie
    action: str # nazwa akcji pobierającej dane. to jest referencja po kluczu do ResourceAction

# typ zawartości zasobu
ResourceData = List[Dict[str, Any]]

# typ akcji pobierającej zasób
ResourceAction = Callable[
    [Resource, MongoClient],
    ResourceData
]


```

### Struktura zasobu

Zasób jest definiowany w formacie JSON. Przykładowa struktura zasobu wygląda następująco:

```json
{
  "name": "ExampleResourceBoilerplate",
  "department": "ops",
  "description": "Example description for example resource",
  "fields": {
    "id": "id",
    "updated": "last_updated_time"
  },
  "action": "give_me_example_data"
}
```

## Opis Pól Zasobu

- **name**: (string) Nazwa zasobu, jest to także nazwa kolekcji Mongo w bazie "RESOURCES".
- **department**: (string) Dział, do którego przypisany jest zasób, np. "marketing", "finanse".
- **description**: (string) Opis zasobu, zawierający informacje o przeznaczeniu i zawartości zasobu.
- **fields**: (object) Pola wymagane do zmapowania w zasobie:
  - **id**: (string) Klucz główny identyfikujący zasób w jego docelowym API.
  - **updated**: (string) Klucz ostatniej modyfikacji zasobu w jego docelowym API.
- **action**: (string) Nazwa akcji pobierającej dane. Jest to referencja do funkcji, która pobiera dane z zewnętrznego źródła.

### Tworzenie pliku krok po kroku:

1. Utwórz nowy plik JSON (np. `ExampleResourceBoilerplate.json`).
2. Wklej powyższą strukturę do pliku.
3. Zapisz plik w katalogu `/__resources__/boilerplates/ExampleResourceBoilerplate.json`

## Przykładowe Struktury Zasobu

### Przykład 1: Zasób dla Kampanii Marketingowych

```json
{
  "name": "MarketingCampaigns",
  "department": "marketing",
  "description": "Dane dotyczące kampanii marketingowych z Google Ads",
  "fields": {
    "id": "campaign_id",
    "updated": "last_updated"
  },
  "action": "fetch_marketing_campaign_data"
}
```

### Przykład 2: Zasób dla Transakcji Finansowych

```json
{
  "name": "FinancialTransactions",
  "department": "finance",
  "description": "Dane dotyczące transakcji finansowych z konta bankowego",
  "fields": {
    "id": "transaction_id",
    "updated": "transaction_date"
  },
  "action": "fetch_financial_transactions"
}
```
