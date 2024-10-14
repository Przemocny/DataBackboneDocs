# Działania równoległe

## Wprowadzenie

Data Backbone oferuje mechanizmy do wykonywania operacji równoległych, co pozwala na efektywne przetwarzanie dużych ilości danych. W tym rozdziale omówimy kluczowe komponenty umożliwiające równoległe działania: klasę `BatchRequest` oraz klasę `BatchFunction`, a także przedstawimy przykład ich praktycznego zastosowania.

## Klasa BatchRequest

`BatchRequest` to klasa umożliwiająca wykonywanie równoległych żądań HTTP.

### Główne metody

```python
async def __aenter__(self):
    # Inicjalizacja sesji aiohttp

async def __aexit__(self, exc_type, exc_val, exc_tb):
    # Zamknięcie sesji aiohttp

async def get_batch(self, requests: List[RequestInfo]) -> List[str]:
    # Wykonanie wielu żądań HTTP równolegle
```

### Kluczowe cechy

- Wykorzystanie kontekstu asynchronicznego (`async with`)
- Równoległe wykonywanie wielu żądań HTTP
- Automatyczne zarządzanie sesją aiohttp

## Klasa BatchFunction

`BatchFunction` to klasa do wykonywania funkcji w partiach, wspierająca zarówno funkcje synchroniczne, jak i asynchroniczne.

### Główne metody

```python
def __init__(self, func: Callable[..., Any], batch_size: int = 10):
    # Inicjalizacja z funkcją do wykonania i rozmiarem partii

@staticmethod
async def run_batch(func: Callable[..., Any], param_list: List[Tuple[Tuple[Any, ...], dict]], batch_size: int = 10) -> List[Any]:
    # Statyczna metoda do wykonywania funkcji w partiach
```

### Kluczowe cechy

- Obsługa zarówno funkcji synchronicznych, jak i asynchronicznych
- Wykonywanie funkcji w partiach o określonym rozmiarze
- Możliwość użycia jako kontekst asynchroniczny lub poprzez metodę statyczną

## Przykład użycia: Pobieranie zadań z ClickUp

Funkcja `collect_all_tasks` demonstruje praktyczne zastosowanie mechanizmów równoległego przetwarzania w kontekście pobierania danych z zewnętrznego API.

### Implementacja

```python
async def collect_all_tasks(resource_setting: Resource, pages=10):
    # Konfiguracja autoryzacji i parametrów zapytania
    # ...

    requests = []
    for i in range(pages):
        # Przygotowanie zapytań dla każdej strony
        # ...

    async with BatchRequest() as getter:
        # Równoległe wykonanie zapytań
        results = await getter.get_batch(requests)

        # Przetwarzanie wyników
        # ...

    return accumulator, with_last_page
```

### Kluczowe aspekty przykładu

- Wykorzystanie `BatchRequest` do równoległego wykonywania wielu zapytań HTTP
- Obsługa paginacji w API ClickUp
- Efektywne gromadzenie danych z wielu stron wyników

## Podsumowanie

Mechanizmy równoległego przetwarzania w Data Backbone, reprezentowane przez klasy `BatchRequest` i `BatchFunction`, umożliwiają efektywne zarządzanie dużymi ilościami danych. Przykład funkcji `collect_all_tasks` pokazuje, jak te mechanizmy mogą być praktycznie zastosowane do optymalizacji pobierania danych z zewnętrznych API, znacząco zwiększając wydajność operacji na dużych zbiorach danych.
