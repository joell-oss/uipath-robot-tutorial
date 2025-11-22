# Instrukcja tworzenia robota w UiPath Studio

## 1. Utworzenie nowego projektu

Otwórz UiPath Studio.

W sekcji **New Project** kliknij na **Process**.

![Tworzenie nowego procesu](media/image1.png)

Nadaj nazwê projektu i opcjonalnie opis. Nastêpnie kliknij **Create**.

![Konfiguracja projektu](media/image2.png)

## 2. Dodanie sekwencji Use Application/Browser

Po pomyœlnym za³adowaniu projektu przejdŸ do sekcji **Activities** po lewej stronie i dodaj sekwencjê **Use Application/Browser**. Skorzystaj z wyszukiwarki pod ikon¹ lupki. Z³ap dan¹ sekwencjê i przeci¹gnij j¹ na canvas.

![Panel Activities](media/image3.png)
![Sekwencja na canvas](media/image4.png)

## 3. Wskazanie aplikacji do automatyzacji

Otwórz przegl¹darkê i przejdŸ na stronê: [https://stat.gov.pl/podstawowe-dane/](https://stat.gov.pl/podstawowe-dane/)

Wróæ do UiPath. Klikaj¹c na **Indicate application to automate** w dodanej sekwencji, zaznacz otwarte wczeœniej okno przegl¹darki klikaj¹c na zielony przycisk **Click to select browser**.

![Indicate application](media/image5.png)
![Wybór przegl¹darki](media/image6.png)

## 4. Ekstrakcja danych z tabeli

W sekcji **Do** naszej sekwencji bêdziemy dodawaæ kolejne kroki dzia³ania robota tzw. aktywnoœci. Pierwsz¹ aktywnoœci¹ bêdzie **Extract Table Data**. Wyszukaj nasz¹ aktywnoœæ w sekcji Activities i przeci¹gnij j¹ do sekcji Do.

![Wyszukiwanie Extract Table Data](media/image7.png)
![Dodanie aktywnoœci](media/image8.png)

W dodanej aktywnoœci kliknij przycisk **Indicate in Chrome**.

![Przycisk Indicate in Chrome](media/image9.png)

### 4.1. Konfiguracja ekstraktora tabeli

Otworzy siê kreator tworzenia tabeli w UiPath. W przegl¹darce na otwartej stronie GUS kliknij najpierw tytu³y poszczególnych kafelków.

![Kreator tabeli](media/image10.png)
![Zaznaczanie elementów](media/image11.png)

Zostan¹ zaznaczone wszystkie pasuj¹ce elementy. Kliknij przycisk **Confirm** w kreatorze ¿eby potwierdziæ wybór.

![Potwierdzone elementy](media/image12.png)
![Confirm](media/image13.png)

### 4.2. Dodanie drugiej kolumny

Nastêpnie do drugiej kolumny tabeli musimy dodaæ wartoœci liczbowe. Kliknij **Select Data to Extract**.

![Select Data to Extract](media/image14.png)

W przegl¹darce zaznacz pierwsz¹ wartoœæ liczbow¹ i zatwierdŸ.

![Pierwsza wartoœæ](media/image15.png)

Zaznacz kolejn¹ wartoœæ liczbow¹. Wszystkie pozosta³e zostan¹ dodane automatycznie.

![Automatyczne dodanie wartoœci](media/image16.png)

W kreatorze zatwierdŸ wybór przyciskiem **Confirm**.

![Potwierdzenie wyboru](media/image17.png)

Zapisz tabelê przyciskiem **Save & Close**.

![Zapisanie tabeli](media/image18.png)

## 5. Utworzenie zmiennej dla wyekstraktowanych danych

W aktywnoœci kliknij pole **Extract to**. U¿yj skrótu klawiaturowego **Ctrl + K** ¿eby utworzyæ now¹ zmienn¹. Zapisz nazwê zmiennej `ExtractDataTable` i kliknij enter.

![Utworzenie zmiennej](media/image19.png)

W dolnej czêœci okna w zak³adce **Data Manager** i **Variables** pojawi nam siê utworzona zmienna typu DataTable.

![Zmienna w Data Manager](media/image20.png)

## 6. Przetwarzanie danych - wiersze z wynagrodzeniem

Dodaj aktywnoœæ **Assign** do przypisania zmiennej.

- W polu **To variable** u¿yj skrótu **Ctrl + K** i nadaj nazwê zmiennej `wynagrodzenieRow`
- W polu **Set value** wpisz wyra¿enie:
```vb
ExtractDataTable.AsEnumerable().Where(Function(row) row(0).ToString.ToLower.Contains("wynagrodzenie")).FirstOrDefault()
```

![Aktywnoœæ Assign](media/image21.png)

### 6.1. Zmiana typu zmiennej

UiPath automatycznie zapisa³ nasz¹ zmienn¹ jako typ String co powoduje b³¹d. W **DataManager - Variables** przy zmiennej `wynagrodzenieRow` kliknij typ String. Nastêpnie **Browse for Types...**

Wyszukaj typ **DataRow** i zatwierdŸ.

![Zmiana typu zmiennej](media/image22.png)
![Wybór typu DataRow](media/image23.png)

## 7. Utworzenie pozosta³ych zmiennych

Analogicznie dodaj kolejne zmienne:

### Zmienna: emeryturaRow
- **Typ:** DataRow
- **Wartoœæ:**
```vb
ExtractDataTable.AsEnumerable().Where(Function(row) row(0).ToString.ToLower.Contains("emerytura")).FirstOrDefault()
```

### Zmienna: wynagrodzenieTitle
- **Typ:** String
- **Wartoœæ:**
```vb
wynagrodzenieRow(0).ToString
```

### Zmienna: wynagrodzenieValue
- **Typ:** Double
- **Wartoœæ:**
```vb
CDbl(wynagrodzenieRow(1).ToString.Replace(" ", "").Replace(",", "."))
```

### Zmienna: emeryturaTitle
- **Typ:** String
- **Wartoœæ:**
```vb
emeryturaRow(0).ToString
```

### Zmienna: emeryturaValue
- **Typ:** Double
- **Wartoœæ:**
```vb
CDbl(emeryturaRow(1).ToString.Replace(" ", "").Replace(",", "."))
```

![Lista zmiennych](media/image24.png)

## 8. Budowa tabeli danych

Dodaj aktywnoœæ **Build Data Table**.

![Build Data Table](media/image25.png)

Kliknij przycisk **DataTable...** i ustaw tabelê jak poni¿ej:

![Przycisk DataTable](media/image26.png)
![Konfiguracja kolumn](media/image27.png)

## 9. Dodanie wierszy do tabeli

### 9.1. Dane o wynagrodzeniu

Dodaj aktywnoœæ **Add Data Row**.

![Add Data Row](media/image28.png)

Uzupe³nij pola aktywnoœci jak poni¿ej:
- **ArrayRow:** `New Object() {wynagrodzenieTitle, wynagrodzenieValue}`
- **DataRow:** pozostaw puste
- **DataTable:** skorzystaj ze skrótu **Ctrl + K** i stwórz zmienn¹ `dataTable`

![Konfiguracja Add Data Row](media/image29.png)

### 9.2. Dane o emeryturze

Dodaj kolejn¹ aktywnoœæ **Add Data Row**, tym razem dla wartoœci œredniej miesiêcznej emerytury.
- **ArrayRow:** `New Object() {emeryturaTitle, emeryturaValue}`
- **DataRow:** pozostaw puste
- **DataTable:** wpisz nazwê utworzonej wczeœniej zmiennej - `dataTable`

![Druga aktywnoœæ Add Data Row](media/image30.png)

## 10. Zapis danych do pliku Excel

Dodaj aktywnoœæ **Write Range Workbook**.

![Write Range Workbook](media/image31.png)

Uzupe³nij pola aktywnoœci jak poni¿ej:
- **Workbook path:** u¿yj skrótu **Ctrl + K** i stwórz zmienn¹ `filePath`
- **Sheet Name:** `"Arkusz1"`
- **Data table:** wpisz nazwê zmiennej - `dataTable`

![Konfiguracja Write Range](media/image32.png)

W zak³adce **Variables** do utworzonej zmiennej `filePath` zapisz œcie¿kê zapisu pliku np. `"C:\gus.xlsx"`.

![Œcie¿ka pliku](media/image33.png)

---

**Gratulacje!** Robot jest gotowy do uruchomienia i automatycznego pobierania danych ze strony GUS oraz zapisywania ich do pliku Excel.
"# uipath-robot-tutorial" 
