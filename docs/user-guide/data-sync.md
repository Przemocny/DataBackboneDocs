# Synchronizacja danych

## Wprowadzenie

Data Backbone to narzędzie, które pomaga firmom MŚP w analizie danych z różnych źródeł. Czasami zachodzi potrzeba ręcznej synchronizacji danych zasobów i wskaźników. Poniżej znajdują się instrukcje, jak wykonać te operacje.

## Synchronizacja Ręczna Zasobów i Wskaźników

Istnieje kilka metod ręcznej synchronizacji zasobów i wskaźników. Każda z nich jest realizowana poprzez wywołanie odpowiedniego adresu URL w przeglądarce lub za pomocą narzędzia do wykonywania zapytań HTTP (np. `curl`, Postman).

### Synchronizacja Pojedynczego Zasobu

Aby zsynchronizować pojedynczy zasób, taki jak `ExampleResource.json`, użyj poniższego URL:

```sh
curl -X GET localhost:8000/refresh-data/resources/ExampleResource
```

### Synchronizacja Pojedynczego Wskaźnika

Aby zsynchronizować pojedynczy wskaźnik, taki jak `ExampleIndicator.json`, użyj poniższego URL:

```sh
curl -X GET localhost:8000/refresh-data/indicators/ExampleIndicator
```

### Synchronizacja Wszystkich Zasobów

Aby zsynchronizować wszystkie zasoby, użyj poniższego URL:

```sh
curl -X GET localhost:8000/refresh-data/resources
```

### Synchronizacja Wszystkich Wskaźników

Aby zsynchronizować wszystkie wskaźniki, użyj poniższego URL:

```sh
curl -X GET localhost:8000/refresh-data/indicators
```

### Synchronizacja Wszystkich Zasobów i Wskaźników

Aby zsynchronizować wszystkie zasoby i wskaźniki jednocześnie, użyj poniższego URL:

```sh
curl -X GET localhost:8000/refresh-data/all
```

## Podsumowanie

Ręczna synchronizacja zasobów i wskaźników w Data Backbone jest prosta i szybka. Wystarczy użyć odpowiednich adresów URL, aby zsynchronizować konkretne zasoby, wskaźniki lub wszystkie dane na raz. Dzięki temu możesz łatwo utrzymywać swoje dane w aktualnym stanie, co pozwala na lepszą analizę i podejmowanie decyzji na podstawie aktualnych informacji.
