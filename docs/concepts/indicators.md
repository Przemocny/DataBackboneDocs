# Wskaźniki

## Wprowadzenie

Aby stworzyć nowy wskaźnik w systemie Data Backbone, postępuj zgodnie z poniższymi krokami. Wskaźnik to metryka, która pozwala na analizę i monitorowanie różnych aspektów biznesowych na podstawie przetworzonych danych.

## Definicja Struktury Wskaźnika

### Typ wskaźnika

```python

@dataclass
class RequiredFields:
    id: str # wskazanie klucza id w zasobie
    updated: str # wskazanie klucza ostatniej modyfikacji w zasobie

# typ wskaźnika
@dataclass
class Indicator:
    name: str # nazwa wskaźnika, jest to także nazwa kolekcji Mongo w bazie "INDICATORS"
    description: str # opis wskaźnika
    department: str # opis działu, do którego przypisany jest wskaźnik
    fields: RequiredFields # pola wymagane  do zmapowania we wskaźnika
    action: str # nazwa akcji przetwarzającej dane do wskaźnika. to jest referencja po kluczu do typu IndicatorAction
    out_action: str # nazwa akcji wystawiającej dane wskaźnika z zewnętrznym oprogramowaniem. to jest referencja po kluczu do typu IndicatorOutAction

# typ zawartości zasobu
ResourceData = List[Dict[str, Any]]

# typ akcji przetwarzającej dane do wskaźnika
IndicatorAction = Callable[
    [Indicator, MongoClient],
    ResourceData
]

# typ akcji wystawiającej dane wskaźnika dla zewnętrznego oprogramowania
IndicatorOutAction = Callable[
    [Indicator, ResourceData, MongoClient],
    Any
]

```

### Struktura wskaźnika

Struktura wskaźnika została zdefiniowana w pliku JSON, który należy utworzyć i wypełnić zgodnie z poniższym szablonem:

```json
{
  "name": "ExampleIndicatorBoilerplate",
  "department": "finance",
  "description": "Example description for example indicator",
  "fields": {
    "id": "id",
    "updated": "last_updated_time"
  },
  "action": "calculate_example_indicator"
}
```

#### Opis pól Wskaźnika:

- **name**: (string) Nazwa wskaźnika. Jest to także nazwa kolekcji Mongo w bazie "INDICATORS".
- **department**: (string) Dział, do którego przypisany jest wskaźnik, np. "marketing", "finanse".
- **description**: (string) Opis wskaźnika, zawierający informacje o przeznaczeniu i zawartości wskaźnika.
- **fields**: (object) Pola wymagane do zmapowania w wskaźniku:
  - **id**: (string) Klucz główny identyfikujący wskaźnik w jego docelowym API.
  - **updated**: (string) Klucz ostatniej modyfikacji wskaźnika w jego docelowym API.
- **action**: (string) Nazwa akcji przetwarzającej dane. Jest to referencja do funkcji, która przetwarza dane z zasobów do wskaźnika.

### Tworzenie pliku krok po kroku:

1. Utwórz nowy plik JSON (np. `MonthlyMargin.json`).
2. Wklej powyższą strukturę do pliku.
3. Zapisz plik w katalogu `/__indicators__/boilerplates/MonthlyMargin.json`

### Przykładowa struktura JSON wskaźnika:

```json
{
  "name": "MonthlyMargin",
  "department": "finance",
  "description": "Wskaźnik marży miesięcznej dla każdego klienta",
  "fields": {
    "id": "client_id",
    "updated": "last_calculated"
  },
  "action": "calculate_monthly_margin"
}
```
