# Treść zadania
Programy powinny posiadać intuicyjny interfejs graficzny lub być opisane krótką dokumentacją (możliwe jest wykonanie w formie skryptu). Wszystkie powinny obsługiwać wielokanałowe pliki: metoda rozwiązania dowolna (na przykład przez wskazanie kanału, dla którego wykonywane są obliczenia lub rozdzielenie kanałów w interfejsie graficznym). Programy z kodami źródłowymi i dokumentacją mogą zostać przekazane przez teams lub w linku do repozytorium.

4. Filtrowanie sygnałów zapisanych w formie plików tekstowych (.csv) + wizualizacja
* Wczytanie pliku sygnału
* Wskazanie parametrów sygnału (liczba próbek, próbkowanie)
* Wyświetlenie sygnału w postaci oscylogramu
* Możliwość wyboru 3 rodzajów filtrowania
* Wykonanie filtrowania całego sygnału z możliwością wyboru okna
* Wyświetlenie sygnału przefiltrowanego w postaci oscylogramu

# Instrukcja uruchomienia
W celu poprawnego uruchomienia programu należy:
1. Stworzyć nowe środowisko wirtualne za pomocą polecenia:
```
python -m venv venv # Windows
virtualenv -p python3 venv # Linux
```
2. Aktywować środowisko wirtualne za pomocą polecenia:
```
venv\Scripts\activate # Windows
source venv/bin/activate # Linux
```
3. Zainstalować biblioteki wymienione w pliku requirements.txt za pomocą polecenia:
```
pip install -r requirements.txt # Windows
pip3 install -r requirements.txt # Linux
```
4. Uruchomić program za pomocą polecenia:
```
jupyter-lab signal_filtering.ipynb
```

Kroki 1 oraz 2 są opcjonalne.
Instrukcja ta została przetestowana na systemie Windows jednak powinna działać również na systemach Linuxowych.

# Instrukcja wykorzystania

W niniejszym notatniku znajdują się dwie klasy służące do filtrowania sygnałów metodą za pomocą:
- średniej kroczącej
- okna o jądrze gaussowskim
- okna o jądrze blackmana
- okna o jądrze hamminga
- filtru dolnoprzepustowego
- filtru górnoprzepustowego
- filtru pasmowoprzepustowego

W celu wykorzystania filtrów bazujących na oknie należy wykorzystać klasę MovingAverageFilterHandler.
Parametry klasy:
- kernel_type - typ okna (FilterType.GAUSSIAN: jądro Gaussowskie, FilterType.BLACKMAN: okno Blackmana, FilterType.HAMMING: okno Hamminga, FilterType.BOX: średnia krocząca)
- kernel_size - rozmiar okna
- sampling - częstotliwość próbkowania
- samples - liczba próbek
- signal - sygnał wejściowy
- selected_channel - numer analizowanego kanału sygnału wejściowego

Parametr "signal" reprezentuje dane w postaci druwymiarowej tablicy biblioteki numPy, w której każda kolumna reprezentuje kolejny kanał sygnału, a każdy wiersz kolejną próbkę sygnału.

Tablicy tej nie trzeba podawać w argumencie, zostanie ona automatycznie uzupełniona po wywołaniu metody read_signal_from_file(path), gdzie "path" to ścieżka do pliku z sygnałem w formacie .csv.

W celu wykorzystania filtrów przepustowych należy wykorzystać klasę FrequencyPassFilterHandler.
Parametry klasy:
- frequency_pass_type - typ filtru (FrequencyPassType.LOW: dolnoprzepustowy, FrequencyPassType.HIGH: górnoprzepustowy, FrequencyPassType.BAND: pasmowoprzepustowy)
- cutoff_low - częstotliwość odcięcia dolna (wykorzystywana przy filtrze dolnoprzepustowym i pasmowoprzepustowym)
- cutoff_high - częstotliwość odcięcia górna (wykorzystywana przy filtrze górnoprzepustowym i pasmowoprzepustowym)
- sampling - częstotliwość próbkowania
- samples - liczba próbek
- signal - sygnał wejściowy
- order - rząd filtru
- selected_channel - numer analizowanego kanału sygnału wejściowego

Parametr "signal" oraz wczytywanie danych z pliku odbywa się analogicznie jak w przypadku klasy MovingAverageFilterHandler.

## Schemat wykorzystania filtrów
1. Utworzenie obiektu klasy MovingAverageFilterHandler lub FrequencyPassFilterHandler
2. Ustawienie wymaganych parametrów:
    - dla klasy MovingAverageFilterHandler:
        - kernel_type
        - kernel_size
        - sampling
        - samples
        - selected_channel
    - dla klasy FrequencyPassFilterHandler:
        - frequency_pass_type
        - cutoff_low (w przypadku filtru dolnoprzepustowego i pasmowoprzepustowego)
        - cutoff_high (w przypadku filtru górnoprzepustowego i pasmowoprzepustowego)
        - sampling
        - samples
        - order
        - selected_channel
3. Wczytanie sygnału z pliku za pomocą metody read_signal_from_file(path)
4. Wywołanie metody filter_signal()
5. Wizualizacja sygnału za pomocą metody plot_signal()
6. Wywołanie metody save_signal_to_file(path), w celu zapisania przefiltrowanego sygnału do pliku

## Przykład wykorzystania
```python
# Wybór typu filtru (wykorzystanie odpowiedniej klasy i podanie odpowiedniego typu kernela bądź odcięcia),
# oraz ustawienie parametrów
freqPassFilter = FrequencyPassFilterHandler(
    frequency_pass_type=FrequencyPassType.LOW, 
    cutoff_low=1, 
    cutoff_high=12, 
    sampling=1000,
    order=10,
)

# Wczytanie sygnału z pliku
freqPassFilter.read_signal_from_file("Dane/sin1_1_1000.csv")

# Filtracja sygnału
freqPassFilter.filter_signal()

# Wyświetlenie wejściowego i przefiltrowanego sygnału oraz odpowiedzi charakteryzujcych wybrany filtr
freqPassFilter.plot_result()

# Zapisanie przefiltrowanego sygnału do pliku
freqPassFilter.save_filtered_signal("filtered_signal.csv")
```