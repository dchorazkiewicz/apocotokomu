# Specyfikacja Funkcjonalna: Arena Modeli i Prezentacja Wyników

## 1. Cel Dokumentu

Celem niniejszego opracowania jest zdefiniowanie logiki oraz struktury wizualnej systemu typu **"Arena Modeli"**. System służy do walidacji i selekcji najlepszych instancji modeli predykcyjnych sprzedaży netto.

Kluczowym zadaniem analitycznym jest precyzyjne "dofitowanie" rozwiązań: nie szukamy jednego uniwersalnego algorytmu dla całej organizacji. Dążymy do wyłonienia najlepszego modelu niezależnie dla każdej kombinacji Rynku i Grupy Produktowej lub zidentyfikowania obszarów, gdzie żaden z obecnych modeli nie spełnia wymogów jakościowych.

## 2. Fundamenty i Założenia Projektowe

### 2.1. Metodologia Oceny (Zgodność z Frameworkiem Matematycznym)

System opiera się na wielowymiarowej definicji błędu ("Wektor Rozszerzony"), zgodnie z przyjętą dokumentacją analityczną.

* **Wielowymiarowość:** Ocena uwzględnia jednocześnie **Dokładność** (odchylenie od celu) oraz **Stabilność** (zmienność predykcji w czasie).
* **Sterowanie Biznesowe:** Wynik końcowy zależy od konfiguracji parametrów globalnych:
    *  (Waga Dokładności),
    *  (Waga Stabilności),
    * Funkcja Wagi Czasu (np. liniowa, wykładnicza).



### 2.2. Przestrzeń Modeli i Kontekst (Zasada Niezależności)

Przyjmujemy następującą strukturę (bazę myślową) dla projektowanych widoków:

* **Instancja Modelu:** Model jest ściśle związany z kontekstem. Algorytm X wytrenowany dla Grupy S01 jest matematycznie i biznesowo innym bytem niż ten sam Algorytm X dla Grupy S02.
* **Heterogeniczność Danych:** Zakładamy, że Grupy Produktowe (nawet w obrębie tego samego Rynku) mają fundamentalnie inną charakterystykę. Oczekujemy, że mogą wymagać zupełnie innych modeli do poprawnego opisu.
* **Struktura Rynku:** Analiza obejmuje macierz **2x2**:
* **Rynki:** PL (Polska), RO (Rumunia).
* **Grupy Produktowe:** S01, S02.


* **Typy Algorytmów:** Porównujemy 5 typów podejść (1 Model Biznesowy/Referencyjny + 4 Modele Data Science). Łącznie daje to 10 niezależnych instancji podlegających ocenie (dla przyjętej bazy myślowej).

### 2.3. Horyzont Czasowy

* Ewaluacja obejmuje okres **6 miesięcy**.
* Analiza może być prowadzona na poziomie pojedynczego miesiąca lub agregowana dla całego okresu.

*> **Nota:** Powyższe liczby (5 typów modeli, 2 rynki, 6 miesięcy) służą do ustalenia uwagi projektowej. System produkcyjny musi być skalowalny i obsługiwać dynamiczną liczbę rynków, grup oraz modeli.*

---

## 3. Logika Analityczna

### 3.1. Zasada Jednej Metryki (The Single Metric Principle)

Fundamentalnym założeniem systemu jest rozłączność definicji sukcesu.

* **Wybór, nie porównanie:** Użytkownik najpierw konfiguruje metrykę (dobiera wagi  i ), definiując cel biznesowy. Dopiero po ustaleniu "zasad gry" przystępujemy do oceny zawodników na arenie.
* **Zakaz krzyżowania metryk:** Nigdy nie porównujemy wartości Metryki A z wartością Metryki B. Porównujemy wyłącznie różne modele w świetle tej samej, ustalonej metryki.

### 3.2. Zasada Lokalnej Optymalizacji (Granularność Decyzji)

System nie dąży do generalizacji.

* **Brak przymusu spójności algorytmicznej:** Fakt, że na Rynku PL dla grupy S01 wygrywa "Model Biznesowy", w żaden sposób nie determinuje wyboru dla grupy S02. Tam może wygrać "Model DS4". Jest to sytuacja pożądana i oczekiwana.
* **Cel:** Wybór najlepszej instancji dla konkretnej komórki macierzy (Rynek  Grupa) lub diagnoza, że dla danej komórki wszystkie dostępne modele wymagają poprawy (usprawnienia).

### 3.3. Metody Agregacji

Aby ocenić jakość modelu w dłuższym horyzoncie, system umożliwia agregację wyników miesięcznych:

* **Średnia / Mediana:** Do oceny stabilnej wydajności w czasie.
* **Suma:** Do oceny całkowitego kosztu błędu.

---

## 4. Scenariusze Wizualizacji (Widoki Decyzyjne)

System oferuje cztery główne perspektywy (Views), wspierające proces decyzyjny na "Arenie Modeli".

### Widok A: Strategiczna Mapa Ciepła (Heatmapa Rynkowa)

Służy do szybkiej identyfikacji "lokalnych mistrzów" w każdym segmencie.

* **Pytanie:** *Jaki model najlepiej radzi sobie z konkretną specyfiką danej grupy produktowej na danym rynku?*

* **Konstrukcja:**
    * **Wiersze:** Rynki (PL, RO).
    * **Kolumny:** Grupy Produktowe (S01, S02).
    * **Zawartość komórki:** Nazwa zwycięskiego modelu (model z najniższą wartością metryki) lub wartość metryki zakodowana kolorem.


* **Wnioski Biznesowe:**
    *  Mozaika kolorów (różni zwycięzcy w różnych komórkach) jest sygnałem pozytywnym – świadczy o dobrej specjalizacji modeli.



### Widok B: Analiza Konsystencji w Czasie (Time Consistency)

Służy do weryfikacji, czy zwycięstwo modelu w danej komórce jest trwałe.

* **Pytanie:** *Czy wybrany dla danej grupy model wygrywa systematycznie, czy jego skuteczność jest przypadkowa?*

* **Konstrukcja:**
    * **Wiersze (Złożone):** Konkatenacja Rynku i Miesiąca (np. `PL_Styczeń` ... `RO_Czerwiec`).
    * **Kolumny:** Grupy Produktowe (S01, S02).


* **Wnioski Biznesowe:**
    * **Pionowa spójność:** Jeśli w kolumnie S01 przez 6 miesięcy dominuje ten sam model, mamy silną podstawę do jego wdrożenia produkcyjnego.



### Widok C: Głębokie Porównanie Modeli (Model Competition)

Służy do oceny marginesu zwycięstwa w konkretnym kontekście rynkowym.

* **Pytanie:** *O ile lepszy jest zwycięzca od drugiego miejsca w konkretnej komórce (Rynek+Grupa)?*

* **Kontekst:** Widok dla ustalonej, pojedynczej komórki macierzy (np. tylko PL + S01).

* **Konstrukcja:**
    * **Wiersze:** Miesiące (1...6) + Podsumowanie.
    * **Kolumny:** Wszystkie dostępne Modele.
    * **Zawartość:** Wartość liczbowa wybranej metryki.

* **Wnioski Biznesowe:**
    * Pozwala zauważyć, gdy "najlepszy" model wciąż ma nieakceptowalnie wysoką wartość metryki błędu.



### Widok D: Szczegółowa Ewolucja Konkurencji (Model Evolution)

Służy do precyzyjnej analizy dynamiki zmian wyników wszystkich modeli w czasie dla ustalonego, konkretnego wycinka biznesowego.

* **Pytanie:** *Jak zmienia się jakość poszczególnych modeli z miesiąca na miesiąc w tym konkretnym segmencie? Czy modele są blisko siebie (walka wyrównana), czy jeden dominuje przez cały czas?*

* **Kontekst (Filtry):** Widok dla **jednego wybranego Rynku** (np. PL) i **jednej wybranej Grupy Produktowej** (np. S01).

* **Konstrukcja:**
    * **Wiersze:** Lista dostępnych Modeli (Biznesowy, DS1, DS2, DS3, DS4).
    * **Kolumny:** Kolejne Miesiące ewaluacji (M1, M2, ..., M6).
    * **Zawartość:** Dokładna wartość liczbowa metryki.


* **Wnioski Biznesowe:**
    * **Analiza trendu:** Pozwala zobaczyć, czy dany model systematycznie się poprawia (metryka maleje w kolejnych kolumnach), czy degraduje.
    * **Ocena dystansu:** Widzimy czarno na białym, jak "ciasny" jest wyścig. Jeśli w M1 różnica między modelem A i B wynosi 0.01, a w M2 wynosi 50.0, to jest to kluczowa informacja o niestabilności, której nie pokaże sama informacja o zwycięzcy.
    * **Współgranie modeli:** Umożliwia ocenę korelacji błędów – czy w trudnym miesiącu (np. M4) wszystkie modele radzą sobie gorzej ("trudny rynek"), czy tylko niektóre ("zły model").