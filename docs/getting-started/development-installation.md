# Instalacja środowiska developerskiego

## Wprowadzenie

Data Backbone oferuje dwie metody instalacji środowiska developerskiego: z użyciem Docker Compose oraz z wykorzystaniem wirtualnego środowiska Python (venv). Obie metody zapewniają kompletne środowisko do pracy z Data Backbone.

## Instalacja z użyciem Docker Compose

### Wymagania wstępne

- Zainstalowany Docker
- Zainstalowany Docker Compose

### Kroki instalacji

#### 1. Sklonuj repozytorium Data Backbone:

```bash
git clone https://github.com/twoja-organizacja/data-backbone.git
cd data-backbone
```

#### 2. Uruchom środowisko za pomocą Docker Compose:

```bash
docker-compose up -d
```

#### 3. Po zakończeniu procesu, środowisko developerskie będzie dostępne pod następującymi adresami:

- Aplikacja główna: `http://localhost:8000`
- Panel administracyjny MongoDB: `http://localhost:8081`
- Środowisko VS Code: `http://localhost:3991` (hasło: `PSWD`)

## Instalacja z użyciem wirtualnego środowiska Python (venv)

### Wymagania wstępne

- Python 3.8 lub nowszy
- pip (menedżer pakietów Python)
- Docker (dla baz danych)

### Kroki instalacji

#### 1. Sklonuj repozytorium Data Backbone:

```bash
git clone https://github.com/twoja-organizacja/data-backbone.git
cd data-backbone
```

#### 2. Utwórz i aktywuj wirtualne środowisko Python:

```bash
python -m venv venv
source venv/bin/activate # Na Windows użyj: venv\Scripts\activate
```

#### 3. Zainstaluj wymagane zależności:

```bash
pip install -r requirements.txt
```

#### 4. Uruchom bazy danych za pomocą Docker Compose:

```bash
docker-compose up -d mongo weaviate mongo-admin
```

#### 5. Uruchom aplikację Data Backbone:

```bash
python app/main.py
```

#### 6. Po zakończeniu procesu, środowisko developerskie będzie dostępne pod następującymi adresami:

- Aplikacja główna: `http://localhost:8000`
- Panel administracyjny MongoDB: `http://localhost:8081`

## Konfiguracja środowiska

Niezależnie od wybranej metody instalacji, konieczne jest utworzenie pliku `.env` w folderze `/app` i skonfigurowanie w nim niezbędnych zmiennych środowiskowych.

## Weryfikacja instalacji

Aby zweryfikować poprawność instalacji, wykonaj następujące kroki:

1. Otwórz przeglądarkę i przejdź do `http://localhost:8000/dev_app`

2. Powinieneś zobaczyć stronę powitalną Data Backbone

3. Spróbuj zalogować się do panelu administracyjnego MongoDB pod adresem `http://localhost:8081`

## Rozwiązywanie problemów

Jeśli napotkasz problemy podczas instalacji lub uruchamiania środowiska, sprawdź następujące rzeczy:

1. Upewnij się, że wszystkie wymagane porty są wolne (8000, 8081, 3991, 27017)

2. Sprawdź logi Docker Compose, używając polecenia `docker-compose logs`

3. Upewnij się, że wszystkie wymagane zmienne środowiskowe są poprawnie ustawione w pliku `.env`

## Podsumowanie

Teraz masz gotowe środowisko developerskie Data Backbone. Możesz rozpocząć pracę nad rozwojem i dostosowywaniem systemu do swoich potrzeb. Pamiętaj, że w przypadku problemów zawsze możesz skorzystać z dokumentacji lub zwrócić się o pomoc do zespołu wsparcia Data Backbone.
