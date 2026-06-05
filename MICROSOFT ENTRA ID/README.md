# 🔐 Projekt: Konfiguracja Microsoft Entra ID – Środowisko zewnętrzne (External Tenant)

> **Autor:** SonnyMat 
> **Technologia:** Microsoft Entra ID (dawniej Azure Active Directory)  
> **Środowisko:** Microsoft Entra Admin Center  
> **Status:** Zrealizowany  
> **Data realizacji:** 02–03.06.2026

---

## 📋 Opis projektu

W ramach projektu samodzielnie skonfigurowałam środowisko zarządzania tożsamością i dostępem (IAM) przy użyciu **Microsoft Entra ID**. Projekt obejmował utworzenie nowego dzierżawcy (tenant) typu **External**, zaprojektowanie struktury użytkowników i grup, rejestrację aplikacji, konfigurację metod uwierzytelniania MFA oraz zarządzanie rolami administracyjnymi – zgodnie z zasadą najmniejszych uprawnień (least privilege).

Celem była demonstracja praktycznych umiejętności z zakresu **Identity & Access Management (IAM)** w środowisku chmurowym Microsoft Azure / Entra.

---

## 🗂️ Struktura repozytorium

```
/
├── README.md
└── screenshots/
    ├── 1.png
    ├── 2.png
    ├── 3.png
    └── ... (1–48)
```

---

## 📦 Moduł I – Tworzenie i konfiguracja dzierżawcy (Tenant)

### Krok 1 – Przegląd istniejącego środowiska

Zapoznałam się ze strukturą domyślnego katalogu (`Katalog domyślny`) powiązanego z kontem `sonia121999@gmail.com`.

![Katalog domyślny – przegląd](screenshots/1.png)

| Pole | Wartość |
|---|---|
| Tenant ID | `3797d513-a303-4d3c-abc3-ef7b9153550f` |
| Domena podstawowa | `sonia121999gmail.onmicrosoft.com` |
| Licencja | Microsoft Entra ID Free |
| Użytkownicy | 1 |

Zidentyfikowałam alert dotyczący konieczności migracji do ujednoliconej polityki metod uwierzytelniania przed wrześniem 2025 r.

---

### Krok 2 – Przegląd listy tenantów

![Manage Tenants](screenshots/2.png)

Potwierdziłam, że w środowisku istnieje jeden dzierżawca (Katalog domyślny, typ Workforce).

---

### Krok 3 – Wybór konfiguracji nowego dzierżawcy

![Wybór konfiguracji tenanta](screenshots/6.png)

Na ekranie wyboru wybrałam typ **External** (zamiast Workforce legacy lub Governed Workforce Preview), ponieważ umożliwia on budowanie środowiska B2C/B2B bez wymagania płatnej subskrypcji.

---

### Krok 4 – Uzupełnienie danych i utworzenie dzierżawcy

![Formularz tworzenia tenanta](screenshots/3.png)
![Podsumowanie i walidacja](screenshots/5.png)

| Pole | Wartość |
|---|---|
| Tenant Name | Rododendron Project Entra |
| Domain Name | `rododendronproject.onmicrosoft.com` |
| Country/Region | Poland (Europe) |
| Subskrypcja | Azure subscription 1 |
| Grupa zasobów | Lab1 (East US) |

Walidacja przebiegła pomyślnie (`Validation passed`). Potwierdziłam utworzenie dzierżawcy.

---

### Krok 5 – Nowy tenant aktywny

![Nowy tenant – strona główna](screenshots/4.png)

Po przełączeniu kontekstu widziałam panel **Microsoft Entra External ID** z opcjami integracji aplikacji.

---

## 👤 Moduł II – Zarządzanie użytkownikami (ręczne tworzenie)

### Krok 1 – Stan początkowy: jeden użytkownik

![Lista użytkowników – stan początkowy](screenshots/7.png)

Po przejściu do sekcji **Użytkownicy** w nowym tenancie widniał tylko właściciel konta (Sonia Paszkowiak, tożsamość MicrosoftAccount).

---

### Krok 2 – Tworzenie użytkowników z rolami i właściwościami

Dla każdego użytkownika uzupełniałam dane na zakładkach: Podstawowe informacje, Właściwości, Przypisania.

![Formularz nowego użytkownika – Jan Kowalski](screenshots/8.png)
![Przegląd – Jan Kowalski](screenshots/9.png)
![Przegląd – admin.helpdesk](screenshots/10.png)
![Przegląd – manager.it](screenshots/12.png)
![Przegląd – Anna Nowak](screenshots/13.png)
![Przegląd – Patrycja Pietruszka](screenshots/15.png)
![Przegląd – svc.app](screenshots/14.png)

| Użytkownik | UPN | Stanowisko | Dział | Rola Entra |
|---|---|---|---|---|
| Jan Kowalski | jan.kowalski@rododendronproject... | Support Specialist | IT | Helpdesk Administrator |
| admin.helpdesk | admin.helpdesk@rododendronproject... | – | – | Helpdesk Administrator |
| manager.it | manager.it@rododendronproject... | – | IT | – |
| Anna Nowak | anna.nowak@rododendronproject... | User | – | – |
| Patrycja Pietruszka | patrycjapietruszka@rododendronproject... | HR | HR | Groups Administrator |
| svc.app | svc.app@rododendronproject... | – | – | – (konto usługowe) |

Konto `svc.app` zostało zaprojektowane jako **service account** – przeznaczone do uwierzytelniania aplikacji.

---

### Krok 3 – Lista 7 użytkowników

![Finalna lista 7 użytkowników](screenshots/11.png)

---

## 📥 Moduł III – Import zbiorczy i zaproszenia gości

### Krok 1 – Bulk Import przez plik CSV

Zamiast tworzyć kolejnych użytkowników ręcznie, skorzystałam z funkcji **Operacje zbiorcze → Utwórz użytkowników**. Pobrałam szablon CSV, wypełniłam go danymi i przesłałam.

![Przesyłanie pliku CSV](screenshots/16.png)

W widoku **Operacje zbiorcze** widoczna jest historia operacji:

![Historia operacji zbiorczych](screenshots/17.png)

| Plik | Data | Status |
|---|---|---|
| CreateUsersTemplate_filled.csv | 3.06.2026, 09:02 | ❌ Niepowodzenie |
| CreateUsersTemplate_filled.csv | 3.06.2026, 09:02 | ❌ Niepowodzenie |
| CreateUsersTemplate_new.csv | 3.06.2026, 09:19 | ✅ Powodzenie |

Pierwsze dwie próby zakończyły się błędem (błąd w formacie CSV). Po poprawieniu pliku operacja zakończyła się sukcesem – to realistyczny scenariusz diagnostyki w pracy administratora.

---

### Krok 2 – Lista 13 użytkowników po imporcie

![Lista 13 użytkowników po bulk import](screenshots/19.png)

Nowi użytkownicy dodani przez import: Agnieszka Wrobel, Karolina Wojcik, Marek Zielinski, Michal Kaminski, Natalia Dabrowska, Tomasz Lewandowski.

---

### Krok 3 – Zaproszenie użytkownika zewnętrznego (Guest Invite)

Skorzystałam z funkcji **Zaproś użytkownika zewnętrznego**, aby dodać osobę spoza organizacji jako gościa (B2B).

![Formularz zaproszenia gościa](screenshots/20.png)

| Pole | Wartość |
|---|---|
| E-mail | soniamatys1@gmail.com |
| Typ użytkownika | Gość |
| Imię / Nazwisko | Anna Nowak |
| Lokalizacja | Polska |
| Wiadomość | „Zapraszam." |

---

### Krok 4 – E-mail z zaproszeniem

![E-mail z zaproszeniem](screenshots/21.png)

Zaproszony użytkownik otrzymał wiadomość od `MSSecurity-noreply@microsoft.com` z linkiem `Accept invitation`.

---

### Krok 5 – Konto gościa po akceptacji

![Konto gościa na liście](screenshots/22.png)

Po akceptacji zaproszenia konto pojawiło się z typem **Gość**, tożsamością **MicrosoftAccount** i typem utworzenia **Zaproszenie**.

---

## 👥 Moduł IV – Grupy zabezpieczeń

### Krok 1 – Tworzenie grupy SEC-IT-Admins

![Formularz nowej grupy](screenshots/23.png)

| Pole | Wartość |
|---|---|
| Typ grupy | Zabezpieczenia |
| Nazwa | SEC-IT-Admins |
| Opis | Security IT Administration |
| Typ członkostwa | Przypisane |

---

### Krok 2 – Wszystkie grupy (4 grupy)

![Lista wszystkich grup](screenshots/44.png)

Ostatecznie w tenancie powstały 4 grupy zabezpieczeń:

| Nazwa grupy | Przeznaczenie |
|---|---|
| SEC-Finance-Team | Zespół finansów |
| SEC-HR-Team | Zespół HR |
| SEC-IT-Admins | Administratorzy IT |
| SEC-IT-Team | Zespół IT |

---

### Krok 3 – Szczegóły grupy SEC-IT-Admins

![Szczegóły grupy SEC-IT-Admins](screenshots/45.png)

Grupa zawiera **2 użytkowników**, źródło: Chmura, typ członkostwa: Przypisane. Data utworzenia: 3.06.2026.

---

## 🖥️ Moduł V – Rejestracja aplikacji (App Registration)

### Krok 1 – Formularz rejestracji

![Formularz rejestracji aplikacji – pusty](screenshots/35.png)
![Formularz rejestracji – TestApp-Lab01](screenshots/40.png)

| Pole | Wartość |
|---|---|
| Nazwa | TestApp-Lab01 |
| Obsługiwane typy kont | Tylko dla jednej dzierżawy – Rododendron Project Entra |
| Redirect URI | `https://localhost/callback` (Internet) |

---

### Krok 2 – Zarejestrowana aplikacja

![Przegląd zarejestrowanej aplikacji](screenshots/41.png)

| Pole | Wartość |
|---|---|
| Application (client) ID | `09e1eeb5-8570-4def-afe5-18182c5057a3` |
| Object ID | `6c7764d1-6b78-4c66-89a3-0ca981d2ba50` |
| Directory (tenant) ID | `a6931aef-e432-4c3e-9e6e-9174943269a8` |
| Stan | ✅ Aktywowano |

---

### Krok 3 – Certyfikaty i klucze tajne (Client Secrets)

![Certyfikaty – brak wpisów tajnych](screenshots/42.png)
![Certyfikaty – wpis tajny LabSecret](screenshots/43.png)

Utworzyłam wpis tajny klienta o nazwie **LabSecret** z datą wygaśnięcia 30.11.2026. Wartość klucza tajnego jest widoczna tylko bezpośrednio po utworzeniu – na screenshocie jest zanonimizowana. To ważna praktyka bezpieczeństwa: klucz należy skopiować i zabezpieczyć natychmiast po wygenerowaniu.

---

## 🔒 Moduł VI – Uwierzytelnianie MFA i SSPR

### Krok 1 – Przegląd katalogów i MFA Phase 2

![Directories + subscriptions](screenshots/25.png)

W portalu Azure zobaczyłam oba katalogi oraz powiadomienie o **Mandatory Azure MFA Phase 2** – od października 2025 r. MFA wymagane dla Azure CLI, PowerShell, IaC, MSAL.

---

### Krok 2 – Polityki metod uwierzytelniania

![Authentication Methods Policies – katalog domyślny](screenshots/26.png)
![Authentication Methods Policies – Rododendron](screenshots/39.png)

| Metoda | Włączona |
|---|---|
| Passkey (FIDO2) | ✅ Tak |
| Microsoft Authenticator | ✅ Tak |
| SMS | ✅ Tak (włączone przeze mnie) |
| Temporary Access Pass | ✅ Tak |
| Software OATH tokens | ✅ Tak |
| Email OTP | ✅ Tak |
| Hardware OATH tokens | ❌ Nie |
| Voice call | ❌ Nie |
| Certificate-based auth | ❌ Nie |

---

### Krok 3 – Włączenie SMS

![Konfiguracja SMS](screenshots/27.png)

Włączyłam metodę **SMS** dla wszystkich użytkowników z rejestracją opcjonalną. SMS może być używany zarówno jako MFA jak i w Self-Service Password Reset (SSPR).

---

### Krok 4 – Microsoft Authenticator – tryby uwierzytelniania

![Microsoft Authenticator settings](screenshots/28.png)

Sprawdziłam dostępne tryby: **Any**, **Passwordless**, **Push**. Pozostawiłam ustawienie *Any*, dając użytkownikom elastyczność wyboru.

---

### Krok 5 – Samoobsługowe resetowanie hasła (SSPR)

![SSPR – włączone dla wszystkich](screenshots/38.png)

Włączyłam **SSPR** dla **wszystkich** użytkowników. Dzięki temu użytkownicy mogą samodzielnie resetować hasła bez angażowania administratora.

---

### Krok 6 – Dostęp warunkowy (Conditional Access)

![Conditional Access Overview](screenshots/29.png)

Zapoznałam się z możliwościami Dostępu warunkowego. Przykładowe scenariusze:
- logowanie spoza sieci firmowej → wymagane MFA
- grupa Managers → wymagane urządzenie zgodne z Intune

Tworzenie własnych polityk wymaga licencji **Entra ID Premium**.

---

## 🎭 Moduł VII – Role i administratorzy (RBAC)

### Krok 1 – Przegląd wbudowanych ról

![Lista ról administracyjnych](screenshots/30.png)

Przejrzałam pełną listę wbudowanych ról Entra ID. Moja aktualna rola: **Administrator globalny**.

---

### Krok 2 – Wybór roli Administrator użytkowników

![Wybór roli Administrator użytkowników](screenshots/31.png)

Znalazłam rolę **Administrator użytkowników** – może zarządzać użytkownikami i grupami, resetować hasła dla administratorów z ograniczonymi uprawnieniami.

---

### Krok 3 – Dodanie przypisania

![Puste przypisania roli](screenshots/32.png)
![Panel dodawania przypisania](screenshots/34.png)
![Potwierdzone przypisanie – Jan Kowalski](screenshots/33.png)

Wybrałam użytkownika **Jan Kowalski** i potwierdziłam przypisanie roli.

| Nazwa | UserName | Typ | Zakres |
|---|---|---|---|
| Jan Kowalski | jan.kowalski@rododendronproject.onmicrosoft.com | User | Katalog |

---

## 📊 Moduł VIII – Monitorowanie (Audit Logs i Sign-in Logs)

### Krok 1 – Dzienniki inspekcji (Audit Logs)

![Dzienniki inspekcji – operacje grupowe](screenshots/36.png)
![Dzienniki inspekcji – szczegóły wpisu](screenshots/37.png)
![Dzienniki inspekcji – operacje aplikacji i grup](screenshots/46.png)
![Dzienniki inspekcji – pełna lista](screenshots/47.png)

W **Dziennikach inspekcji** widoczne są wszystkie operacje wykonywane w tenancie: tworzenie grup (`Add group`), dodawanie członków (`Add member to group`), operacje zarządzania aplikacjami (`Add application`, `Add service principal`), zdarzenia B2C Authentication oraz zarządzanie rolami (`Add member to role`).

Każdy wpis zawiera szczegóły: datę, typ działania, kategorię, status, adres IP inicjatora i identyfikator korelacji.

---

### Krok 2 – Zdarzenia logowania (Sign-in Logs)

![Zdarzenia logowania](screenshots/48.png)

W **Zdarzeniach logowania** widoczne są interaktywne logowania użytkowników, m.in.:

| Użytkownik | Aplikacja | Stan | Zasób |
|---|---|---|---|
| sonia121999@gmail.com | Azure Portal | ✅ Powodzenie | Azure Resource Manager |
| soniamatys1@gmail.com | My Apps | ❌ Niepowodzenie | Microsoft Graph |
| soniamatys1@gmail.com | Microsoft Invitation Acc... | ❌ Niepowodzenie | – |

Niepowodzenia dla `soniamatys1@gmail.com` związane są z próbą akceptacji zaproszenia – typowe zdarzenia w procesie onboardingu gościa B2B.

---

## 🎯 Podsumowanie – co zademonstrowano

| Obszar | Zademonstrowane umiejętności |
|---|---|
| **Tenant Management** | Tworzenie tenanta External ID, wybór typu, konfiguracja domeny i subskrypcji |
| **User Management** | Ręczne tworzenie użytkowników z właściwościami i rolami, bulk import CSV, diagnostyka błędów importu |
| **B2B Collaboration** | Zaproszenie użytkownika zewnętrznego (Guest), weryfikacja e-mail z zaproszeniem |
| **Group Management** | Tworzenie grup zabezpieczeń, przypisywanie członków |
| **App Registration** | Rejestracja aplikacji (OAuth2/OIDC), konfiguracja redirect URI, tworzenie client secret |
| **MFA / Authentication** | Konfiguracja metod uwierzytelniania, włączenie SMS, Microsoft Authenticator, SSPR |
| **RBAC / Role Management** | Przegląd ról wbudowanych, przypisanie roli użytkownikowi (least privilege) |
| **Monitoring** | Analiza Audit Logs i Sign-in Logs, interpretacja zdarzeń i błędów |
| **Security Best Practices** | Zasada least privilege, service account, anonimizacja client secret, SSPR |

---

## 🔧 Użyte narzędzia i technologie

- **Microsoft Entra Admin Center** (`entra.microsoft.com`)
- **Microsoft Entra ID** (dawniej Azure Active Directory)
- **Microsoft Entra External ID** (B2C/B2B)
- **Azure Portal** (`portal.azure.com`)
- **Azure Subscription** + Resource Groups
- **CSV Bulk Import** (szablony użytkowników)
- **OAuth 2.0 / OpenID Connect** (rejestracja aplikacji)

---

## 📌 Uwagi

> Projekt zrealizowany w środowisku laboratoryjnym w celach edukacyjnych i demonstracyjnych. Wszystkie screenshoty dokumentują realne kroki konfiguracji wykonane 2–3 czerwca 2026 r.
