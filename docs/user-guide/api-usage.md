# Użycie DataBackbone API

## Wprowadzenie

Data Backbone udostępnia API, które pozwala na pobieranie danych wskaźników oraz zasobów z zewnętrznych aplikacji. Ten fragment dokumentacji opisuje, jak korzystać z API Data Backbone, na przykładzie integracji z platformami automatyzacji, takimi jak Make.com.

## Zasoby

### Endpoint API

Endpoint API Data Backbone do pobierania danych zasobu:
`GET /data/resource/{resourceName}`

gdzie `{resourceName}` to nazwa zasobu, którego dane chcesz pobrać.

### Przykład użycia

Załóżmy, że chcesz pobrać dane zasobu faktur Stripe z Make.com. Oto jak możesz to zrobić:

1. W Make.com, dodaj nowy moduł HTTP.
2. Skonfiguruj moduł HTTP w następujący sposób:

   - Metoda: GET
   - URL: `https://localhost:8000/data/resource/StripeInvoices`
   - Nagłówki: Dodaj odpowiednie nagłówki autoryzacji, jeśli są wymagane.

3. Po wykonaniu scenariusza, Make.com pobierze dane faktur Stripe z Data Backbone w formacie JSON.

### Format odpowiedzi

Odpowiedź API będzie w formacie JSON. Przykładowa struktura danych faktur Stripe może wyglądać następująco:

```json
{
  "data": [
    {
      "id": "in_1234567890",
      "customer": "cus_9876543210",
      "amount": 10000,
      "currency": "PLN",
      "status": "paid",
      "created": "2023-06-01T10:00:00Z"
    },
    {
      "id": "in_0987654321",
      "customer": "cus_1234567890",
      "amount": 15000,
      "currency": "PLN",
      "status": "open",
      "created": "2023-06-15T14:30:00Z"
    }
  ],
  "meta": {
    "lastUpdated": "2023-06-15T15:00:00Z"
  }
}
```

## Wskaźniki

### Endpoint API

Endpoint API Data Backbone do pobierania danych wskaźnika:
`GET /data/indicator/{indicatorName}`

gdzie `{indicatorName}` to nazwa wskaźnika, którego dane chcesz pobrać.

### Przykład użycia

Załóżmy, że chcesz pobrać dane wskaźnika RZIS (Rachunek Zysków i Strat) do Make.com. Oto jak możesz to zrobić:

1. W Make.com, dodaj nowy moduł HTTP.
2. Skonfiguruj moduł HTTP w następujący sposób:

   - Metoda: GET
   - URL: `https://localhost:8000/data/indicator/RZIS`
   - Nagłówki: Dodaj odpowiednie nagłówki autoryzacji, jeśli są wymagane.

3. Po wykonaniu scenariusza, Make.com pobierze dane RZIS z Data Backbone w formacie JSON.

### Format odpowiedzi

Odpowiedź API będzie w formacie JSON. Przykładowa struktura danych RZIS może wyglądać następująco:

```json
{
  "data": [
    {
      "miesiac": "Styczeń",
      "przychody": 100000,
      "koszty": 80000,
      "zysk": 20000
    },
    {
      "miesiac": "Luty",
      "przychody": 120000,
      "koszty": 90000,
      "zysk": 30000
    }
  ],
  "meta": {
    "lastUpdated": "2023-06-15T10:30:00Z"
  }
}
```

## Obsługa błędów

API może zwrócić następujące kody błędów:

- 400 Bad Request: Nieprawidłowe zapytanie
- 404 Not Found: Wskaźnik o podanej nazwie nie istnieje
- 500 Internal Server Error: Błąd serwera

## Ograniczenia

- Limit zapytań: 1000 na godzinę
- Maksymalny rozmiar odpowiedzi: 10 MB

## Podsumowanie

Korzystanie z API Data Backbone pozwala na łatwe integrowanie danych wskaźników z zewnętrznymi narzędziami. Dzięki prostym endpointom, możesz pobierać aktualne dane wskaźników i wykorzystywać je w swoich automatyzacjach i analizach.
