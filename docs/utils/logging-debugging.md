# Logowanie i debugowanie

## Wprowadzenie

System Data Backbone oferuje możliwości logowania i debugowania dzięki klasie `Logger`. Ta klasa umożliwia efektywne śledzenie działań systemu, co jest kluczowe dla utrzymania i rozwiązywania problemów.

## Klasa Logger

Klasa `Logger` jest głównym narzędziem do logowania w systemie Data Backbone.

### Inicjalizacja

Aby utworzyć instancję loggera, użyj następującego kodu:

```python
logger = Logger(log_file='logfile.log')
```

Parametr `log_file` jest opcjonalny i domyślnie ustawiony na 'logfile.log'.

### Metody

#### log(message: str) -> None

Metoda `log` zapisuje wiadomość z aktualnym znacznikiem czasu.

Przykład użycia:

```python
logger.log("Rozpoczęto przetwarzanie danych")
```

#### \_write_to_file(message: str) -> None

Metoda prywatna `_write_to_file` zapisuje wiadomość do pliku logów.

### Format logów

Każdy wpis w logu ma następujący format:

```
[RRRR-MM-DD GG:MM:SS] Treść wiadomości
```

## Dobre praktyki

1. Używaj logowania do śledzenia kluczowych operacji w systemie.
2. Zapisuj zarówno informacje o sukcesach, jak i błędach.
3. Regularnie przeglądaj logi w celu wykrycia potencjalnych problemów.

## Podsumowanie

Klasa `Logger` w Data Backbone zapewnia prosty, ale skuteczny mechanizm logowania. Dzięki niej możesz łatwo śledzić działanie systemu, co jest nieocenione w procesie debugowania i utrzymania aplikacji.
