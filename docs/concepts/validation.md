# Schematy walidacji

## Wprowadzenie

Klasa `Validator` służy do walidacji obiektów `DataFrame` z biblioteki Pandas zgodnie z określonym schematem. Umożliwia sprawdzenie poprawności typów danych w kolumnach DataFrame'a na podstawie dostarczonego schematu.

## Argument Schema

### schema_out

`schema_out` jest argumentem przekazywanym do Validatora, zdefiniowanym jako zagnieżdżony słownik, gdzie klucze reprezentują nazwy pól, a wartości reprezentują oczekiwane typy danych. W schemacie `schema_out`, wartości, takie jak `str`, `int` i `float`, to klasy wbudowane w Pythonie i reprezentują typy danych.

#### Przykład schema_out

```python
schema_out = {
    "client": str,
    "id": str,
    "date": str,
    "cycles_with_invoices": [
        {
            "id": str,
            "cycle_number": int,
            "date_of_payment": str,
            "end_of_cycle": str,
            "total_hours_with_buffer": float,
            "remaining_hours_with_buffer": float,
            "programmers": [
                {
                    "user_name": str,
                    "worked_time": float
                }
            ],
            "worked_time": float,
            "hour_cap": float
        }
    ]
}
```

## Metody klasy Validator

### 1. `validate`

Waliduje DataFrame zgodnie z podanym schematem. Sprawdza, czy wszystkie wymagane kolumny są obecne oraz czy wartości w poszczególnych kolumnach odpowiadają oczekiwanym typom. W przypadku błędów zgłasza wyjątek.

#### Argumenty

- `df` (pd.DataFrame): DataFrame do walidacji.
- `schema` (Dict[str, Any]): Schemat definiujący oczekiwane typy dla kolumn.

#### Zwraca

- `pd.DataFrame`: Zwalidowany DataFrame.

---

### 2. `_validate_nested_type`

Sprawdza, czy pojedyncza wartość odpowiada oczekiwanemu typowi, obsługując również zagnieżdżone struktury danych (np. słowniki i listy). Umożliwia walidację bardziej złożonych typów danych.

---

### 3. `_type_to_str`

Konwertuje typ lub strukturę typu na czytelną reprezentację w formie string. Używana przede wszystkim do debugowania i informowania o błędach podczas walidacji.

---
