# Dokumentacja Projektowa: System Walidacji "Arena Modeli"

Witaj w repozytorium projektu **Arena Modeli**. Poniżej znajduje się zestawienie dokumentów oraz prototypów aplikacji służących do walidacji i selekcji modeli predykcyjnych sprzedaży netto.

> **Status:** Dokumentacja ma charakter **roboczy (Draft / Proposal)**. Przedstawione tu rozwiązania prezentują główne idee i ramy logiczne systemu, które wymagają jeszcze dopracowania szczegółów oraz finalnej akceptacji przed wdrożeniem produkcyjnym.

---

## 1. Fundamenty Teoretyczne i Funkcjonalne

W tej sekcji znajdziesz opis logiki stojącej za systemem.

* **[01_miary.md](./01_miary.md) – Framework Matematyczny**
    Szczegółowy opis metodologii oceny. Dokument definiuje pojęcie **Wektora Rozszerzonego**, wprowadzając spójną matematycznie przestrzeń łączącą ocenę dokładności (błąd trafienia) ze stabilnością (błąd zmienności/shiftu). To tutaj zdefiniowane są parametry $\alpha$ i $\beta$.

* **[02_prezentacja_wynikow.md](./02_prezentacja_wynikow.md) – Specyfikacja Funkcjonalna**
    Opis logiki biznesowej i architektury wizualnej systemu. Dokument definiuje 6 kluczowych widoków decyzyjnych (od A do F), w tym Strategiczne Mapy Ciepła oraz Analizę Kosztu Stabilności. Określa zasady "walki" modeli na Arenie w podziale na Rynki i Grupy Produktowe.

---

## 2. Prototypy Aplikacji (Proof of Concept)

Interaktywne dema, które wizualizują działanie teorii w praktyce. Służą do weryfikacji założeń (działają w przeglądarce, brak backendu).

* **[aplikacja.html](./aplikacja.html) – Laboratorium Metryk**
    Narzędzie analityczne do "bebechów" matematyki. Pozwala zrozumieć wpływ funkcji wag czasu (płaska, liniowa, wykładnicza) na wynik końcowy oraz zobaczyć macierz wrażliwości rankingu. Służy do kalibracji parametrów.

* **[prezentacja.html](./prezentacja.html) – Dashboard "Arena Modeli"**
    Główna wizualizacja dla biznesu. Implementuje wszystkie widoki zdefiniowane w specyfikacji funkcjonalnej (Heatmapy, Ewolucja, Symulacja What-If). Pozwala na żywo zmieniać wagi priorytetów ($\alpha, \beta$) i obserwować zmiany liderów w rankingu modeli.