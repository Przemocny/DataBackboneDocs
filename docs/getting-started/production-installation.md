# Instalacja środowiska produkcyjnego

## Wprowadzenie

Data Backbone oferuje pełne środowisko produkcyjne oparte na Docker Compose. Ta metoda zapewnia łatwe wdrożenie i zarządzanie wszystkimi komponentami systemu w środowisku produkcyjnym.

## Wymagania wstępne

- Zainstalowany Docker
- Zainstalowany Docker Compose
- Serwer z systemem Linux (zalecany Ubuntu 20.04 LTS lub nowszy)
- Minimum 4 GB RAM i 20 GB przestrzeni dyskowej

## Kroki instalacji

### 1. Przygotowanie serwera

Zaktualizuj system i zainstaluj niezbędne narzędzia:

```bash
sudo apt update && sudo apt upgrade -y
sudo apt install -y git curl
```

### 2. Instalacja Docker i Docker Compose

Istnieją dwie metody instalacji Docker i Docker Compose: poprzez konsolę lub pobierając instalatory ze strony internetowej.

#### Metoda 1: Instalacja przez konsolę

Zainstaluj Docker:

```bash
curl -fsSL https://get.docker.com -o get-docker.sh
sudo sh get-docker.sh
sudo usermod -aG docker $USER
```

Zainstaluj Docker Compose:

```bash
sudo curl -L "https://github.com/docker/compose/releases/download/1.29.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose
```

#### Metoda 2: Instalacja poprzez pobranie ze strony

1. Przejdź na oficjalną stronę Docker: <a href="https://www.docker.com/products/docker-desktop" target="_blank">https://www.docker.com/products/docker-desktop</a>
2. Pobierz instalator odpowiedni dla Twojego systemu operacyjnego (Windows, macOS lub Linux).
3. Uruchom pobrany instalator i postępuj zgodnie z instrukcjami na ekranie.
4. Po zainstalowaniu Docker Desktop, Docker Compose będzie już dostępny w systemie.

Po zakończeniu instalacji, niezależnie od wybranej metody, zweryfikuj poprawność instalacji, wykonując w terminalu następujące polecenia:

```bash
docker --version
docker-compose --version
```

Jeśli instalacja przebiegła pomyślnie, powinieneś zobaczyć numery wersji zainstalowanych narzędzi.

### 3. Pobranie repozytorium Data Backbone

Sklonuj repozytorium Data Backbone:

```bash
git clone https://github.com/twoja-organizacja/data-backbone.git
cd data-backbone
```

### 4. Konfiguracja środowiska

Utwórz plik `.env` w katalogu `/app` i skonfiguruj w nim niezbędne zmienne środowiskowe:

Dostosuj wartości zmiennych do swojego środowiska produkcyjnego.

### 5. Uruchomienie środowiska

Uruchom środowisko za pomocą Docker Compose:

```bash
docker-compose up -d
```

## Podsumowanie

Teraz masz gotowe produkcyjne środowisko Data Backbone. Pamiętaj o regularnym monitorowaniu, aktualizacjach i tworzeniu kopii zapasowych. W przypadku problemów, zawsze możesz skorzystać z dokumentacji lub zwrócić się o pomoc do zespołu wsparcia Data Backbone.
