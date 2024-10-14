# Podstawowe przykłady

## Wprowadzenie

W tym rozdziale przedstawimy podstawowe przykłady pobierania zasobu w DataBackbone, wykorzystując funkcje `give_me_resource_from_gsheet` oraz `give_me_resource_from_webpage`. Te przykłady demonstrują, jak pobrać dane z arkusza Google Sheets oraz ze strony internetowej i przetworzyć je na format odpowiedni dla DataBackbone.

## Pobieranie zasobu z Google Sheets

### Funkcja `give_me_resource_from_gsheet`

Funkcja `give_me_resource_from_gsheet` służy do pobierania danych z arkusza Google Sheets i przetwarzania ich na format zasobu DataBackbone.

#### Użycie

```python
def give_me_resource_from_gsheet(resource_setting: Resource, mongo_client: MongoClient):
    # Implementacja funkcji
    # ...
    return data
```

#### Kluczowe cechy

- Autoryzacja dostępu do Google Sheets i Google Drive
- Sprawdzanie istnienia arkusza i worksheet
- Pobieranie danych z arkusza
- Przetwarzanie danych do formatu zasobu DataBackbone

### Implementacja

Poniżej przedstawiamy kluczowe elementy implementacji:

1. Autoryzacja:

```python
gsheets, _ = authorize_gspread_gdrive()
```

2. Sprawdzanie istnienia arkusza i worksheet:

```python
spreadsheet = sheet_exist(gsheets, spreadsheet_id)
worksheet = worksheet_exists(gsheets, spreadsheet, worksheet_name)
```

3. Pobieranie i przetwarzanie danych:

```python
vals = worksheet.get_all_values()
keys = vals[0]
vals = vals[1:]

data = []
for idx, entry in enumerate(vals):
    item = {}
    for key_idx, key in enumerate(keys):
        item[key] = entry[key_idx]
    data.append(item)
```

### Pełna implementacja funkcji

```python
from external_services.google.google import authorize_gspread_gdrive
from external_services.google.gsheet_utils import sheet_exist, worksheet_exists
from __resources__.types import Resource, MongoClient

def give_me_resource_from_gsheet(resource_setting: Resource, mongo_client: MongoClient):
    # Autoryzacja dostępu do Google Sheets i Google Drive
    gsheets, _ = authorize_gspread_gdrive()

    # ID arkusza Google Sheets
    spreadsheet_id = '1O4afNBV9pJdDjXmk0EGitKdDIzInjDNylgmQf6oHNKM'
    # Nazwa worksheet w arkuszu
    worksheet_name = '(AUTO) ExampleSheet'

    # Sprawdzenie czy arkusz istnieje
    spreadsheet = sheet_exist(gsheets, spreadsheet_id)
    if not spreadsheet:
        raise Exception(f"Google Sheet o id: {spreadsheet_id} nie istnieje")

    # Sprawdzenie czy worksheet istnieje w arkuszu
    worksheet = worksheet_exists(gsheets, spreadsheet, worksheet_name)

    # Pobranie wszystkich wartości z worksheet
    vals = worksheet.get_all_values()

    # Pierwszy wiersz jako klucze (nagłówki kolumn)
    keys = vals[0]
    # Pozostałe wiersze jako dane
    vals = vals[1:]

    # Lista na przetworzone dane
    data = []

    # Iteracja przez każdy wiersz danych
    for idx, entry in enumerate(vals):
        item = {}
        # Mapowanie kluczy i wartości
        for key_idx, key in enumerate(keys):
            item[key] = entry[key_idx]
        # Dodanie przetworzonego wiersza do listy danych
        data.append(item)

    # Zwrócenie przetworzonych danych
    return data

```

## Pobieranie zasobu ze strony internetowej

### Funkcja `give_me_resource_from_webpage`

Funkcja `give_me_resource_from_webpage` służy do pobierania danych z określonej strony internetowej i przetwarzania ich na format zasobu DataBackbone.

#### Użycie

```python
async def give_me_resource_from_webpage(resource_setting: Resource, mongo_client: MongoClient):
    # Implementacja funkcji
    # ...
    return rows
```

#### Kluczowe cechy

- Asynchroniczne pobieranie danych ze strony internetowej
- Wykorzystanie Playwright do renderowania strony
- Parsowanie HTML za pomocą BeautifulSoup
- Ekstrakcja danych z tabeli HTML

### Implementacja

Poniżej przedstawiamy kluczowe elementy implementacji:

1. Uruchomienie przeglądarki i nawigacja do strony:

```python
async with async_playwright() as p:
    browser = await p.chromium.launch(headless=False)
    page = await browser.new_page()
    await page.goto('https://hook.eu2.make.com/q07viqfnlysxe9z267tc2vwl2548luxq')
```

2. Pobieranie zawartości tabeli:

```python
await page.wait_for_selector('div#table')
table_html = await page.inner_html('div#table')
```

3. Parsowanie HTML i ekstrakcja danych:

```python
soup = BeautifulSoup(table_html, 'html.parser')
table = soup.find('table')
headers = [th.get_text(strip=True) for th in table.find_all('th')]
rows = []
for tr in table.find_all('tr'):
    cells = tr.find_all(['td', 'th'])
    if len(cells) != len(headers):
        continue
    row = {headers[ix]: cell.get_text(strip=True) for ix, cell in enumerate(cells)}
    rows.append(row)
```

## Kluczowe komponenty

### Klasa `Resource`

Klasa `Resource` reprezentuje strukturę zasobu w DataBackbone. Zawiera informacje takie jak nazwa zasobu, opis, dział i wymagane pola.

### Klasa `MongoClient`

`MongoClient` to klasa z biblioteki PyMongo, używana do interakcji z bazą danych MongoDB.

### Biblioteki zewnętrzne

- `gspread`: Używana do interakcji z Google Sheets API.
- `playwright`: Używana do renderowania stron internetowych i interakcji z nimi.
- `BeautifulSoup`: Używana do parsowania HTML i ekstrakcji danych.

### Pełna implementacja funkcji

```python
from __resources__.types import Resource, MongoClient
from playwright.async_api import async_playwright
from bs4 import BeautifulSoup

async def give_me_resource_from_webpage(resource_setting: Resource, mongo_client: MongoClient):
    # Rozpocznij asynchroniczne korzystanie z playwright
    async with async_playwright() as p:
        # Uruchom przeglądarkę w trybie z widocznym interfejsem
        browser = await p.chromium.launch(headless=False)
        # Utwórz nową kartę przeglądarki
        page = await browser.new_page()

        # Przejdź do żądanej strony
        await page.goto('https://hook.eu2.make.com/q07viqfnlysxe9z267tc2vwl2548luxq')

        # Czekaj, aż element div z id 'table' będzie dostępny na stronie
        await page.wait_for_selector('div#table')

        # Pobierz zawartość tabeli jako HTML
        table_html = await page.inner_html('div#table')

        # Zamknij przeglądarkę
        await browser.close()

        # Wgraj zawartość tabeli do BeautifulSoup
        soup = BeautifulSoup(table_html, 'html.parser')

        # Znajdź tabelę w HTML
        table = soup.find('table')
        if not table:
            raise ValueError("No table found in the provided HTML.")

        # Pobierz wszystkie nagłówki kolumn
        headers = [th.get_text(strip=True) for th in table.find_all('th')]

        # Pobierz wszystkie wartości wierszy
        rows = []
        for tr in table.find_all('tr'):
            # Znajdź wszystkie komórki w wierszu
            cells = tr.find_all(['td', 'th'])
            # Pomijaj wiersze, które nie mają takiej samej liczby komórek jak nagłówki
            if len(cells) != len(headers):
                continue
            # Utwórz słownik dla wiersza, łącząc nagłówki z odpowiednimi wartościami komórek
            row = {headers[ix]: cell.get_text(strip=True) for ix, cell in enumerate(cells)}
            rows.append(row)

        return rows

```

## Podsumowanie

Przykłady `give_me_resource_from_gsheet` i `give_me_resource_from_webpage` demonstrują, jak DataBackbone może pobierać dane z różnych zewnętrznych źródeł (Google Sheets i strony internetowe) i przetwarzać je na format zasobu. Procesy te obejmują autoryzację (w przypadku Google Sheets), pobieranie danych, parsowanie i przetwarzanie. Takie podejście pozwala na elastyczne integrowanie różnych źródeł danych z systemem DataBackbone.
