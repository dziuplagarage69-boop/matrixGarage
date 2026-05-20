# MATRIX GARAGE v2.0

System zarządzania warsztatem samochodowym – aplikacja desktopowa (Windows/Linux) do ewidencji klientów, pojazdów, zleceń i magazynu. Z synchronizacją przez Google Drive i systemem backupu "jak rejestrator".

---

## Spis treści

1. [Funkcje](#funkcje)
2. [Wymagania](#wymagania)
3. [Szybki start (dev)](#szybki-start-dev)
4. [Kompilacja do EXE](#kompilacja-do-exe)
5. [Użytkowanie](#użytkowanie)
   - [Klienci](#klienci)
   - [Pojazdy](#pojazdy)
   - [Zlecenia](#zlecenia)
   - [Magazyn części](#magazyn-części)
   - [Dokumenty](#dokumenty)
   - [Presety robocizny](#presety-robocizny)
   - [Raporty](#raporty)
6. [Synchronizacja Google Drive](#synchronizacja-google-drive)
7. [System licencji](#system-licencji)
8. [Backup i przywracanie](#backup-i-przywracanie)
9. [Struktura plików](#struktura-plików)
10. [Konfiguracja własnego Google API](#konfiguracja-własnego-google-api)
11. [Technologie](#technologie)

---

## Funkcje

- **Klienci** – dodawanie, edycja, usuwanie, wyszukiwanie po imieniu/nazwisku/telefonie
- **Pojazdy** – VIN, rejestracja, marka/model, rocznik, silnik, skrzynia, naped + dekoder VIN dla Saabów (YS3, YTN, YK1, YK3, 5S3)
- **Zlecenia** – usługa + koszty (robocizna, części, inne), przebieg, notatki, części z magazynu
- **Magazyn części** – stany magazynowe, cena zakupu/sprzedaży, kategorie, kod producenta, gwarancja
- **Dokumenty** – załączniki do pojazdów (PDF, zdjęcia, TXT) z datą i nazwą
- **Presety robocizny** – szablony usług z gotowymi cenami, wybierane w zleceniu
- **Raporty** – wydruk karty pojazdu do TXT
- **Synchronizacja Google Drive** – backup i sync między komputerami
- **System licencji** – 30-dniowy trial, aktywacja kluczem (HMAC + HWID)
- **Wielojęzyczność** – PL/EN z przełącznikiem w headerze
- **Motywy** – Matrix (domyślny), Cyberpunk, Light
- **Użytkownicy** – logowanie, role (admin/manager), ograniczenie dostępu

---

## Wymagania

- **System**: Windows 10+ lub Linux (Ubuntu 22.04+)
- **Python**: 3.10+ (tylko do wersji developerskiej)
- **Dysk**: ~100 MB na aplikację + miejsce na bazę danych

---

## Szybki start (dev)

```bash
# 1. Klonuj repozytorium
git clone <repo-url>
cd warsztat-main

# 2. Utwórz wirtualne środowisko
python -m venv .venv

# 3. Aktywuj
# Windows:
.venv\Scripts\activate
# Linux:
source .venv/bin/activate

# 4. Zainstaluj zależności
pip install -r requirements.txt

# 5. Uruchom
python main.py
```

### Wymagane biblioteki (requirements.txt)

```
customtkinter
google-auth
google-auth-oauthlib
google-api-python-client
Pillow
```

---

## Kompilacja do EXE

Aplikacja jest kompilowana PyInstallerem do jednego pliku EXE – przenośnego, bez instalatora.

### Windows

Wystarczy kliknąć `build_exe.bat` w katalogu głównym projektu. Skrypt:
1. Sprawdza środowisko Python (`.venv`)
2. Instaluje PyInstaller jeśli brak
3. Generuje ikonę z kodu
4. Kompiluje do `dist\MatrixGarage.exe`

```batch
build_exe.bat
```

Wynik: `dist\MatrixGarage.exe` (~37 MB)

### Linux

```bash
bash build_linux.sh
```

Wynik: `dist/MatrixGarage`

### Uwagi do kompilacji

- Kompilacja musi odbyć się **na docelowej platformie** (Windows → Windows, Linux → Linux)
- EXE zawiera wbudowane dane OAuth Google – synchronizacja działa od razu
- Nie potrzeba dodatkowych plików – jeden EXE i gotowe
- Dane użytkownika (baza, licencja, token) zapisują się w APPDATA (~/.local/share)

---

## Użytkowanie

### Klienci

Lewy panel – lista klientów. Możliwości:
- **Dodaj** – przycisk "Nowy klient" (imię, nazwisko, telefon, email, NIP)
- **Edytuj** – kliknij ikonę ołówka przy kliencie
- **Usuń** – kliknij X (miękkie usunięcie – rekord zostaje w bazie z flagą)
- **Szukaj** – wpisz w pole wyszukiwania (szuka po imieniu, nazwisku, telefonie)

Po wybraniu klienta, środkowy panel pokazuje jego pojazdy.

### Pojazdy

Lista pojazdów wybranego klienta. Każdy pojazd ma:
- Numer rejestracyjny, VIN, marka, model
- Status: Przyjęty / W naprawie / Gotowy do odbioru / Wydany
- Datę przyjęcia i aktualizacji
- Przycisk "Szczegóły" – pełna karta pojazdu z dekoderem VIN

**Dekoder VIN** – automatyczny dla Saabów (VIN zaczynające się od YS3, YTN, YK1, YK3, 5S3). Dekoduje:
- Markę, model, rocznik, silnik, moc, paliwo, nadwozie, skrzynię, naped
- Kod silnika, kod skrzyni, fabrykę, serię, turbo

### Zlecenia

Okno zleceń dla wybranego pojazdu:
- **Dodaj zlecenie** – nazwa usługi, koszt robocizny, koszt części, koszt inne, przebieg, notatki
- **Użyj presetu** – przycisk obok pola usługi, wybierasz gotowy preset (nazwa + cena)
- **Dodaj część z magazynu** – lista części na stanie, klikasz aby dodać do zlecenia
- **Dokumenty** – załączniki do zlecenia/pojazdu
- Lista zleceń pokazuje: datę, usługę, koszty, części, notatki
- Każde zlecenie można edytować lub usunąć

### Magazyn części

Panel części (przycisk "Części" w headerze):
- Lista części z filtrowaniem (szukaj po nazwie, kodzie, dostawcy)
- Stany magazynowe z minimalnym stanem (ostrzeżenie przy niskim)
- Ceny zakupu i sprzedaży
- Kategorie, kod producenta, gwarancja
- Dodawanie/edycja/usuwanie części

### Dokumenty

Do każdego pojazdu można dodać dokumenty (instrukcje, zdjęcia, faktury PDF):
- Przeciągnij plik lub wybierz przez Okno pliku
- Dokument zapisuje się lokalnie (kopia w `%APPDATA%\MatrixGarage\dokumenty\`)
- Lista z datą dodania, nazwą i przyciskiem do otwarcia

### Presety robocizny

Przycisk "Presety" w headerze – zarządzanie szablonami usług:
- **Dodaj** – nazwa usługi + koszt robocizny + opis
- **Edytuj** – kliknij przycisk edycji przy prezecie
- **Usuń** – kliknij X
- W oknie zlecenia kliknij "Użyj presetu" → lista do wyboru → automatycznie wypełnia nazwę i koszt

### Raporty

Przycisk "Raport" w headerze – generuje kartę pojazdu do wydruku:
- Wybierz status pojazdu do filtrowania
- Kliknij pojazd → podgląd karty z wszystkimi danymi i zleceniami
- Zapisz do pliku TXT

---

## Synchronizacja Google Drive

### Jak działa

1. Aplikacja łączy się z Google Drive przez OAuth 2.0
2. Tworzy folder `WasztatSync` na Twoim Dysku Google
3. Synchronizuje plik `warsztat.db` (baza danych)

### Logowanie

1. Kliknij "Konto Google" w headerze
2. "Zaloguj przez Google"
3. Otworzy się przeglądarka → zaloguj się na swoje konto Google
4. Zezwól aplikacji MatrixGarage na dostęp
5. Skopiuj kod autoryzacyjny i wklej w okno aplikacji

### Automatyczna synchronizacja

- Sync uruchamia się automatycznie co 5 minut
- Przy starcie aplikacji
- Przy zamknięciu aplikacji

### Scalanie danych (merge)

Przy konflikcie (dwa komputery wprowadziły zmiany):
1. Aplikacja pobiera bazę z Drive do pliku tymczasowego
2. Porównuje rekord po rekordzie (po ID) dla każdej tabeli
3. **Nowe rekordy z Drive** → dodaje do lokalnej bazy
4. **Istniejące rekordy** → zostawia lokalną wersję (nie nadpisuje)
5. Scalona baza jest wysyłana na Drive

Dzięki temu żadne dane nie giną – nowe wpisy z każdego komputera są zachowywane.

---

## System licencji

### Okres próbny

- Automatyczny 30-dniowy trial przy pierwszym uruchomieniu
- Licznik dni pokazuje się w headerze aplikacji
- Po wygaśnięciu triala blokada do czasu aktywacji

### Aktywacja

1. Kliknij "Konto Google" → "Licencja"
2. Wpisz adres email
3. Wpisz klucz licencyjny (format: XXXX-XXXX-XXXX-XXXX)
4. Kliknij "Aktywuj licencję"

### Dla sprzedawcy – generowanie kluczy

Uruchom narzędzie GUI:
```bash
python generuj_klucz_gui.py
```
Wpisz email klienta → kliknij "Generuj klucz" → przekaż klientowi.

Klucz jest powiązany z:
- Adresem email klienta
- Unikalnym identyfikatorem sprzętu (HWID)
- Sekretnym seedem wbudowanym w aplikację

Każda kompilacja może mieć inny SECRET_SEED (w `core/license.py`).

---

## Backup i przywracanie

### System backupu "jak rejestrator"

Przed każdą synchronizacją aplikacja automatycznie:

1. **Backup na Drive** – timestampowana kopia bazy w folderze `WasztatSync/Backups/`
   - Nazwa: `warsztat_YYYY-MM-DD_HH-MM-SS.db`
   - Przechowywane: ostatnie **20 wersji** (najstarsze usuwane)
2. **Backup lokalny** – kopia przed pobraniem z Drive
   - Lokalizacja: `%APPDATA%\MatrixGarage\local_backups\`
   - Przechowywane: ostatnie **5 wersji**

### Przywracanie backupu

1. Kliknij "Konto Google" w headerze
2. "Kopie zapasowe"
3. Pokaże się lista wszystkich backupów na Drive z datą i rozmiarem
4. Kliknij "Przywróć" przy wybranej kopii
5. Potwierdź → baza zostaje zastąpiona wybranym backupem

---

## Struktura plików

### Po kompilacji (u użytkownika)

| Plik | Lokalizacja | Opis |
|---|---|---|
| `MatrixGarage.exe` | katalog instalacji | Główny program (jeden plik) |
| `warsztat.db` | `%APPDATA%\MatrixGarage\` | Baza danych SQLite |
| `license.dat` | `%APPDATA%\MatrixGarage\` | Klucz licencyjny |
| `google_token.json` | `%APPDATA%\MatrixGarage\` | Token autoryzacji Google |
| `.trial` | `%APPDATA%\MatrixGarage\` | Znacznik startu okresu próbnego |
| `local_backups/` | `%APPDATA%\MatrixGarage\` | Lokalne kopie zapasowe bazy |

**Linux**: zamiast `%APPDATA%` → `~/.local/share/MatrixGarage/`

### Struktura katalogu projektu

```
warsztat-main/
├── main.py                  # Punkt wejścia
├── build_exe.bat            # Kompilacja Windows
├── build_linux.sh           # Kompilacja Linux
├── generuj_klucz_gui.py     # Generator kluczy licencyjnych
├── requirements.txt         # Zależności Pythona
├── README.md                # Ta dokumentacja
│
├── core/
│   ├── database.py          # Baza danych SQLite (modele + CRUD)
│   ├── i18n.py              # Tłumaczenia PL/EN
│   ├── icon.py              # Generowanie ikony (pixel art)
│   ├── license.py           # System licencji (HMAC + HWID)
│   └── themes.py            # Motywy kolorystyczne
│
├── ui/
│   └── main_window.py       # GUI (customtkinter) – wszystkie okna
│
├── backup/
│   └── gdrive.py            # Google Drive OAuth + sync + backup
│
├── assets/
│   └── icon.ico             # Wygenerowana ikona
│
└── .venv/                   # Wirtualne środowisko Python
```

---

## Konfiguracja własnego Google API

W aplikacji są wbudowane domyślne dane OAuth. Jeśli chcesz użyć własnych:

1. Wejdź na https://console.cloud.google.com/
2. Utwórz projekt → włącz Google Drive API
3. Credentials → OAuth 2.0 → Desktop App
4. Pobierz JSON lub skopiuj `client_id` i `client_secret`
5. Edytuj `backup/gdrive.py` – znajdź słownik `_CLIENT_CONFIG` i wklej swoje dane
6. Przekompiluj EXE

---

## Technologie

| Technologia | Zastosowanie |
|---|---|
| **Python 3.14** | Język programowania |
| **customtkinter** | GUI – nowoczesny Tkinter z motywami |
| **SQLite** | Baza danych (lokalna, bez serwera) |
| **Google Drive API** | Synchronizacja i backup w chmurze |
| **PyInstaller 6.x** | Kompilacja do EXE |
| **google-auth-oauthlib** | Logowanie przez Google OAuth 2.0 |
| **HMAC + SHA256** | Generowanie kluczy licencyjnych |

---

## Licencja

© 2026 – Wszelkie prawa zastrzeżone.  
Aplikacja komercyjna – dystrybucja wyłącznie z ważnym kluczem licencyjnym.
