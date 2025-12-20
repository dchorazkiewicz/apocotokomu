# Analiza Matematyczna Walidacji Modeli Predykcyjnych Sprzedaży Netto

## 1. Cel i Definicja Problemu

Celem dokumentu jest zdefiniowanie matematycznych ram walidacji modeli predykcyjnych prognozujących sprzedaż netto.

W niniejszym opracowaniu przyjmujemy zintegrowane podejście do oceny jakości modelu. Definiujemy "błąd modelu" jako pojęcie wielowymiarowe, na które składają się jednocześnie:

1.  Odchylenie od wartości rzeczywistej (dokładność).
2.  Odchylenie od własnych wcześniejszych predykcji (stabilność).

Model niestabilny jest matematycznie traktowany jako model obarczony błędem na wymiarach różnicowych. Realizujemy to poprzez konstrukcję **Wektora Rozszerzonego (Augmented Vector)**, który agreguje wszystkie typy błędów w jeden obiekt podlegający ocenie.

## 2. Założenia Wstępne

* **Poprawność Metodologiczna:** Zakładamy, że dostarczone szeregi czasowe są poprawne technicznie (poprawnie wytrenowane/wygenerowane).
* **Porównywalność:** Wszystkie modele (Data Science oraz Biznesowe) operują na tych samych danych wejściowych i celują w tę samą definicję sprzedaży netto.
* **Jednostka Czasu:** Analiza dotyczy predykcji dziennych w obrębie jednego miesiąca ($N$ dni).

## 3. Formalizacja Matematyczna: Rozszerzanie Przestrzeni

Niech $y_{real} \in \mathbb{R}$ oznacza rzeczywistą sprzedaż na koniec miesiąca.
Niech $\mathbf{P} = [\hat{y}_1, \dots, \hat{y}_N]$ oznacza wektor predykcji modelu w kolejnych dniach miesiąca.

### 3.1. Bazowy Wektor Błędów (Komponent 0-rzędu)

Podstawowym elementem oceny jest wektor błędów względem wartości rzeczywistej.

$$
\mathbf{E}^{(0)} = [e_1, e_2, \dots, e_N], \quad \text{gdzie } e_t = \mathcal{L}(\hat{y}_t, y_{real})
$$

Funkcja $\mathcal{L}$ określa miarę odległości (np. błąd bezwzględny, procentowy).

### 3.2. Wektory Różnicowe (Komponenty wyższych rzędów - Shift)

Uzupełniamy opis modelu o informację dotyczącą jego wewnętrznej spójności w czasie. Definiujemy komponenty dla każdego przesunięcia (shift) $k$ od $1$ do $S$.

Dla przesunięcia $k$:

$$
\mathbf{E}^{(k)} = [\delta^{(k)}_{k+1}, \dots, \delta^{(k)}_N]
$$

Gdzie element wektora to różnica między predykcjami oddalonymi o $k$ dni:

$$
\delta^{(k)}_t = \mathcal{L}(\hat{y}_t, \hat{y}_{t-k})
$$

Wektory $\mathbf{E}^{(k)}$ mają wymiar $N-k$.

### 3.3. Konstrukcja Wektora Rozszerzonego (Augmented Vector)

Definiujemy jeden, spójny **Wektor Rozszerzony $\mathbb{E}_{aug}$**, będący konkatenacją błędu bazowego i błędów różnicowych.

Dla zadanego maksymalnego rzędu przesunięcia $S$:

$$
\mathbb{E}_{aug} = \left[ \mathbf{E}^{(0)}, \mathbf{E}^{(1)}, \dots, \mathbf{E}^{(S)} \right]
$$

Każdy element tego wektora reprezentuje składową całkowitego błędu modelu.

## 4. Definicja Miary (Struktura Przestrzeni Hilberta)

Ocena jakości modelu sprowadza się do rzutowania wielowymiarowej przestrzeni błędów na pojedynczą wartość skalarną (score). Aby zachować rygor matematyczny i elastyczność biznesową, definicję metryki opieramy na ważonym iloczynie skalarnym, rozszerzonym o globalne parametry sterujące.

### 4.1. Przestrzeń Wag Czasowych

Analogicznie do definicji wektorów błędów $\mathbf{E}^{(k)}$, definiujemy odpowiadające im wektory wag czasu, które parametryzują funkcję kosztu w zależności od dnia miesiąca.

Dla komponentu bazowego ($k=0$) oraz każdego rzędu przesunięcia ($k \in \{1, \dots, S\}$) przyjmujemy wektory wag:

$$
\mathbf{W}^{(0)} \in \mathbb{R}^N, \quad \mathbf{W}^{(k)} \in \mathbb{R}^{N-k}
$$

Elementy tych wektorów, oznaczone odpowiednio $w_t^{(0)}$ oraz $w_j^{(k)}$, są wyznaczane przez przyjęte funkcje czasu (np. rosnąca penalizacja w miarę zbliżania się do końca miesiąca).

### 4.2. Globalne Parametry Sterujące

Aby umożliwić biznesowe sterowanie priorytetami (wybór między modelem "dokładnym" a "stabilnym"), wprowadzamy dwa globalne skalary (mnożniki):

* $\alpha$ (alpha) – **Waga Dokładności (Accuracy Weight):** Globalny mnożnik dla błędów względem wartości rzeczywistej.
* $\beta$ (beta) – **Waga Stabilności (Stability Weight):** Globalny mnożnik dla błędów różnicowych (zmienności predykcji).

### 4.3. Definicja Metryki Finalnej ($\mathcal{M}_{total}$)

Całkowitą miarę błędu modelu $\mathcal{M}_{total}$ definiujemy jako sumę ważonych iloczynów skalarnych, przemnożoną przez odpowiednie parametry globalne.

Niech $\langle \mathbf{A}, \mathbf{B} \rangle$ oznacza standardowy iloczyn skalarny wektorów w przestrzeni euklidesowej: $\langle \mathbf{A}, \mathbf{B} \rangle = \sum a_i b_i$.

Wzór ogólny przyjmuje postać:

$$
\mathcal{M}_{total} = \alpha \cdot \underbrace{ \langle \mathbf{W}^{(0)}, \mathbf{E}^{(0)} \rangle }_{\text{Składowa Dokładności}} + \beta \cdot \sum_{k=1}^{S} \underbrace{ \langle \mathbf{W}^{(k)}, \mathbf{E}^{(k)} \rangle }_{\text{Składowa Stabilności rzędu } k}
$$

### 4.4. Postać Jawna (Szeregowa)

Rozwijając powyższy zapis do postaci sumy po elementach, otrzymujemy jawną formułę obliczeniową zaimplementowaną w systemie:

$$
\mathcal{M}_{total} = \alpha \sum_{t=1}^{N} w_t^{(0)} e_t + \beta \sum_{k=1}^{S} \left( \sum_{j=1}^{N-k} w_j^{(k)} \delta_j^{(k)} \right)
$$

Gdzie:

* **Pierwszy człon** to ważona suma błędów predykcji, skalowana przez parametr $\alpha$.
* **Drugi człon** to suma kar za niestabilność (dla wszystkich przesunięć $k$), skalowana przez parametr $\beta$.

Tak zdefiniowana metryka $\mathcal{M}_{total}$ jest wielkością, którą należy minimalizować.

---

## 5. Przykład Obliczeniowy (Case Study)

Poniższy przykład obrazuje mechanikę obliczania metryki dla uproszczonego, **5-elementowego szeregu** z uwzględnieniem parametrów globalnych.

**Dane wejściowe:**

* Rzeczywista sprzedaż ($y_{real}$): **100**
* Predykcje modelu ($\mathbf{P}$): **[90, 95, 110, 102, 100]**
* **Konfiguracja Globalna:**
    * Waga Dokładności ($\alpha$): **1.0**
    * Waga Stabilności ($\beta$): **2.0** (Priorytetyzujemy stabilność nad precyzję).

### Krok 1: Budowa Komponentów Wektora (Błąd bezwzględny)

1.  **Komponent Bazowy (Błąd vs Real):**
    $\mathbf{E}^{(0)} = [10, 5, 10, 2, 0]$

2.  **Komponent Shift=1 (Zmienność dzień-do-dnia):**
    $\mathbf{E}^{(1)} = [5, 15, 8, 2]$

3.  **Komponent Shift=2 (Zmienność co 2 dni):**
    $\mathbf{E}^{(2)} = [20, 7, 10]$

### Krok 2: Aplikacja Wag Czasowych (Wewnątrz składowych)

Przyjmujemy wagi rosnące liniowo dla przykładu.
* $\mathbf{W}^{(0)} = [1.0, 2.0, 3.0, 4.0, 5.0]$
* $\mathbf{W}^{(1)} = [0.2, 0.4, 0.6, 0.8]$
* $\mathbf{W}^{(2)} = [0.3, 0.6, 0.9]$

Obliczamy "surowe" iloczyny skalarne (przed aplikacją $\alpha$ i $\beta$):

1.  **Surowa Dokładność:**
    $(10\cdot1) + (5\cdot2) + (10\cdot3) + (2\cdot4) + (0\cdot5) = 10 + 10 + 30 + 8 + 0 = \mathbf{58.0}$

2.  **Surowa Stabilność (Shift 1):**
    $(5\cdot0.2) + (15\cdot0.4) + (8\cdot0.6) + (2\cdot0.8) = 1.0 + 6.0 + 4.8 + 1.6 = \mathbf{13.4}$

3.  **Surowa Stabilność (Shift 2):**
    $(20\cdot0.3) + (7\cdot0.6) + (10\cdot0.9) = 6.0 + 4.2 + 9.0 = \mathbf{19.2}$

### Krok 3: Wyliczenie Miary Końcowej z Parametrami Globalnymi

Aplikujemy parametry $\alpha = 1.0$ oraz $\beta = 2.0$ do zagregowanych wartości.

$$
Metric = \alpha \cdot (\text{Surowa Dokładność}) + \beta \cdot (\text{Surowa Stabilność Shift 1} + \text{Surowa Stabilność Shift 2})
$$

Podstawiając liczby:

$$
Metric = 1.0 \cdot 58.0 + 2.0 \cdot (13.4 + 19.2)
$$

$$
Metric = 58.0 + 2.0 \cdot (32.6)
$$

$$
Metric = 58.0 + 65.2 = \mathbf{123.2}
$$

W tym scenariuszu wysoka wartość parametru $\beta$ (2.0) sprawiła, że błędy wynikające z nerwowości modelu (skoki predykcji) mają decydujący wpływ na ocenę końcową, przeważając nad prostym błędem trafności.