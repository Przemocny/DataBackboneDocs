# Prompty używane podczas warszatów

## 1. Generowanie planu działań na podstawie informacji od klienta

```markdown

Jako analityk danych, stwórz szczegółową roadmapę działań przetwarzania danych.

Roadmapa zaczyna się od zasobów surowych - dane wejściowe
Łączenie i przetwarzanie zasobów surowych tworzy encje pośrednie.
Łączenie i przetwarzanie encji pośrednich tworzy wskaźniki - oczekiwane rezultaty

Sekcje roadmapy zawsze są 3:
- ekstrakcja danych z zasobów surowych
- przetwarzanie danych encji pośrednich
- łączenie encji pośrednich we wskaźniki

Każdy zasób, encja pośrednia oraz wskaźnik mają być oddzielnym etapem roadmapy w obrębie sekcji.

Dla każdego zasobu, encji pośredniej i wskaźnika wygeneruj po 2 punkty - część biznesową i część techniczną:
Część biznesowa (dla decydenta):
- Opracuj dokładną i zrozumiałą dla osoby technicznej definicję
- Wyjaśnij jej rolę w procesie
- Opisz, jak wpływa encja na końcowy rezultat
- Zaproponuj jaką wiedzę biznesową mogę wyciągnąć z encji
- Zaproponuj proces weryfikacji wyników pośrednich

Część techniczna (dla osoby technicznej):
- Opisz wymagane dane, które musi zawierać encja
- Określ źródła danych wraz z nazwą i numerem pozycji (z zasobów, z encji pośrednich)
- Opisz w punktach proces transformacji danych do oczekiwanego rezultatu
- Zaproponuj potencjalne punkty kontroli jakości danych 

Przedstaw plan w formie sekwencyjnej, pokazując przepływ danych od zasobów przez encje pośrednie aż do wskaźnika.

Twój plan powinien być szczegółowy, logiczny i pokazywać jasną ścieżkę od danych wejściowych do oczekiwanych rezultatów, z uwzględnieniem wszystkich istotnych kroków pośrednich i transformacji danych

Informacje od klienta:

Zasoby klienta (dane wejściowe):
- płatności w Stripe
- subskrybcje w Stripe
- klienci w Stripe
- płatności  w PayU
- klienci w PayU
- płatności w transakcjach bankowych
- klienci w transakcjach bankowych
- faktury z saldeo
- klienci z saldeo

Wskaźniki, które chce klient (oczekiwane rezultaty):
- total sprzedaż produktów subskrybcji  
- lista sprzedaży produktów subskrybcji  
- total sprzedaż produktów jednorazowych 
- lista sprzedaży produktów jednorazowych

```


## 2. Generowanie grafu Mermaid na podstawie planu działań z 1.


```markdown

na podstawie opisu roadmapy przetwarzania danych stwórz kod flowchart w mermaid.

konfiguracja flowcharta:

- defaultRenderer: elk
- strzałki:
-- łamiące się w 90 stopniach
-- kolor biały
-- relacje miedzy zasobami, encjami pośrednimi i wskaźnikami mają być zobrazowane w formie strzałek
-- strzałki bez labeli

- bloki:
-- nazwy funkcji prezentuj bez wywołania ()
-- tła sekcji i tło całego mają być ciemnoszare,
-- każdy blok ma mieć czarny font
-- bloki resourceFN/processFN/indicatorFN to bloki trapezowe
-- bloki resourceDB/entityDB/indicatorDB to bloki database
-- każdy blok ma mieć pogrubioną odzwierciedlającą nazwę kroku z roadmapy i opis roli w tagach <small>

użyj tych styli:
classDef getFN fill:#333,color:#fff,stroke:#f00,stroke-width:1px
classDef resourceDB fill:#333,color:#fff,stroke:#f00,stroke-width:1px
classDef processFN fill:#333,color:#fff,stroke:#ff0,stroke-width:1px
classDef entityDB fill:#333,color:#fff,stroke:#ff0,stroke-width:1px
classDef retrieveFN fill:#333,color:#fff,stroke:#0f0,stroke-width:1px
classDef indicatorDB fill:#333,color:#fff,stroke:#0f0,stroke-width:1px


flowchart ma mieć 6 sekcji:
- pobieranie zasobów - classDef getFN
- zasoby - classDef resourceDB
- przetwarzanie w encje pochodne - classDef processFN
- encje pochodne - classDef entityDB
- przetwarzanie wskaźników - classDef retrieveFN
- wskaźniki - classDef indicatorDB

Nie twórz opisów charta, potrzebuję tylko kodu bez dodatkowych komentarzy.


```