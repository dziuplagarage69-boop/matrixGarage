# MATRIX GARAGE v2.0

System zarządzania warsztatem samochodowym. Aplikacja desktopowa (Windows/Linux) z synchronizacją przez Google Drive.

## Funkcje

- **Klienci** – dodawanie, edycja, wyszukiwanie
- **Pojazdy** – VIN, rejestracja, marka/model, dekoder VIN dla Saabów
- **Zlecenia** – usługi + koszty (robocizna, części, inne)
- **Części / Magazyn** – stany magazynowe, ceny zakupu/sprzedaży, kategorie
- **Dokumenty** – załączniki do pojazdów/zleceń
- **Raporty** – wydruk karty pojazdu
- **Presety robocizny** – szablony usług z gotowymi cenami
- **Synchronizacja Google Drive** – backup i sync między komputerami
- **System licencji** – 30-dniowy trial, aktywacja kluczem
- **Wielojęzyczność** – PL/EN
- **Motywy** – Matrix, Cyberpunk, Light

## Wymagania

- Python 3.10+
- Windows 10+ lub Linux (Ubuntu 22.04+)

## Szybki start (dev)

```bash
python -m venv .venv
.venv\Scripts\activate     # Windows
# source .venv/bin/activate # Linux

pip install -r requirements.txt
python main.py
```

## Kompilacja do EXE

### Windows
```
build_exe.bat
```
EXE w `dist\MatrixGarage.exe`

### Linux
```bash
bash build_linux.sh
```
EXE w `dist/MatrixGarage`

## Google Drive Sync

Aby synchronizacja działała, aplikacja ma wbudowane dane OAuth.  
Każdy użytkownik loguje się na **własne konto Google** – token zapisuje się lokalnie.

Dane są przechowywane w folderze `WasztatSync` na Google Drive.  
Przed każdą synchronizacją tworzona jest kopia zapasowa (max 20 wersji).

## System licencji

- 30-dniowy okres próbny
- Aktywacja kluczem (email + klucz licencyjny)
- Klucz generowany przez sprzedawcę (narzędzie `generuj_klucz_gui.py`)
- Plik licencji w `%APPDATA%\MatrixGarage\license.dat`

## Backup

- Przed każdym sync – kopia bazy na Drive (`WasztatSync/Backups/`, max 20)
- Lokalny backup przed pobraniem (`local_backups/`, max 5)
- Przywracanie: menu Konto Google → Kopie zapasowe

## Struktura plików

| Plik | Lokalizacja | Opis |
|---|---|---|
| `MatrixGarage.exe` | katalog instalacji | Główny program |
| `warsztat.db` | `%APPDATA%\MatrixGarage\` | Baza danych |
| `license.dat` | `%APPDATA%\MatrixGarage\` | Klucz licencyjny |
| `google_token.json` | `%APPDATA%\MatrixGarage\` | Token Google |
| `local_backups/` | `%APPDATA%\MatrixGarage\` | Lokalne kopie zapasowe |

Na Linux zamiast `%APPDATA%` → `~/.local/share/MatrixGarage/`

## Technologie

- Python + customtkinter (GUI)
- SQLite (baza danych)
- Google Drive API (synchronizacja)
- PyInstaller (kompilacja do EXE)
