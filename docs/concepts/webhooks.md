# Webhooks

## Wprowadzenie

Webhooki w Data Backbone służą do aktualizowania zasobów i wskaźników w odpowiedzi na zdarzenia zachodzące w zewnętrznych systemach. Umożliwiają one automatyczne odświeżanie danych w Data Backbone, gdy nastąpią zmiany w zintegrowanych aplikacjach, takich jak Clickup czy Stripe.

## Zastosowanie

Webhooki są używane do:

- Reagowania na zmiany w innych systemach
- Automatycznego odświeżania danych w Data Backbone
- Synchronizacji danych na podstawie konkretnych zdarzeń

Przykłady:

1. Ustawienie automatyzacji w Clickup, która wywołuje webhook w Data Backbone, aby odświeżyć dane po określonym zdarzeniu.
2. Odbieranie informacji o nowej płatności w Stripe i synchronizacja odpowiednich danych w Data Backbone.

## Konfiguracja

1. Adres URL webhooka w Data Backbone ma format:

   ```
   https://twoja-domena.com/webhooks/$nazwa_fn
   ```

   gdzie `$nazwa_fn` to nazwa funkcji webhooka zdefiniowanej w pliku Python.

2. Na przykład, dla funkcji `example_webhook`, adres URL będzie:
   ```
   https://twoja-domena.com/webhooks/example_webhook
   ```

## Struktura Webhooka

Webhook w Data Backbone jest funkcją Python o następującej strukturze:

```python
def example_webhook(mongo_client: MongoClient,
                    request_body: RequestBody,
                    request_url_params: RequestUrlParams):
    # Logika webhooka
    return { "request_body": request_body, "request_url_params": request_url_params }
```

### Parametry:

- `mongo_client`: Instancja MongoClient do interakcji z bazą danych
- `request_body`: Słownik zawierający dane przesłane w ciele żądania
- `request_url_params`: Słownik zawierający parametry URL żądania

### Typy danych:

```python
RequestBody = Dict[str, Any]
RequestUrlParams = Dict[str, Any]

WebhookFunctionType = Callable[
    [MongoClient, RequestBody, RequestUrlParams],
    List[Dict[str, Any]]
]
```

## Implementacja Webhooka

Aby zaimplementować webhook:

1. Utwórz nowy plik Python w katalogu `app/__webhooks__/actions/`.
2. Zdefiniuj funkcję webhooka zgodnie z powyższą strukturą.
3. Zaimplementuj logikę przetwarzania danych:

   - Pobierz dane z webhooka
   - Dodaj lub zaktualizuj dane w bazie
   - Wykonaj niezbędne operacje lub zapytania

4. Zwróć wynik w formie słownika lub listy słowników.

## Przykład

```python
def example_webhook(mongo_client: MongoClient,
                    request_body: RequestBody,
                    request_url_params: RequestUrlParams):

    # Pobierz dane z webhooka
    # Przykład: payment_id = request_body.get('payment_id')

    # Dodaj dane do bazy
    # Przykład: mongo_client['payments'].insert_one({'payment_id': payment_id})

    # Wykonaj inne operacje
    # Przykład: sync_data(payment_id)

    return { "request_body": request_body, "request_url_params": request_url_params }
```

## Bezpieczeństwo

- Zawsze weryfikuj źródło przychodzących żądań webhook.
- Używaj szyfrowania HTTPS dla wszystkich połączeń webhook.
- Rozważ implementację mechanizmu podpisywania żądań dla dodatkowego bezpieczeństwa.

## Podsumowanie

Webhooki w Data Backbone zapewniają elastyczny sposób na utrzymanie aktualności danych poprzez automatyczne reagowanie na zdarzenia w zewnętrznych systemach. Prawidłowo skonfigurowane i zabezpieczone webhooki mogą znacznie usprawnić proces synchronizacji danych i zapewnić, że twoje analizy zawsze opierają się na najświeższych informacjach.
