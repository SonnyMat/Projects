# 🔐 Okta IAM – Projekt Laboratoryjny

> **Cel projektu:** Zademonstrowanie praktycznych umiejętności w obszarze Identity and Access Management (IAM) przy użyciu platformy **Okta Integrator Free Plan**. Projekt obejmuje konfigurację środowiska od podstaw: tworzenie użytkowników, zarządzanie grupami, konfigurację MFA, integracje SSO (SAML 2.0, OIDC) oraz polityki bezpieczeństwa.

---

## 📋 Spis treści

1. [Środowisko i narzędzia](#środowisko-i-narzędzia)
2. [Branding organizacji](#1-branding-organizacji)
3. [Konfiguracja Authenticators (MFA)](#2-konfiguracja-authenticators-mfa)
4. [Tworzenie użytkowników](#3-tworzenie-użytkowników)
5. [Zarządzanie grupami](#4-zarządzanie-grupami)
6. [Group Rules – automatyczne członkostwo](#5-group-rules--automatyczne-członkostwo)
7. [Polityki uwierzytelniania](#6-polityki-uwierzytelniania-authentication-policies)
8. [Aplikacja SAML 2.0](#7-aplikacja-saml-20)
9. [Aplikacja OIDC / OAuth 2.0](#8-aplikacja-oidc--oauth-20)
10. [Raporty i System Log](#9-raporty-i-system-log)
11. [Podsumowanie umiejętności](#podsumowanie-umiejętności)

---

## Środowisko i narzędzia

| Element | Wartość |
|---|---|
| Platforma | Okta Integrator Free Plan |
| Typ konta | Bezpłatne konto deweloperskie |
| Zakres | Identity & Access Management (IAM) |
| Protokoły | SAML 2.0, OIDC, OAuth 2.0 |
| Liczba użytkowników | 6 (limit planu: 10) |

---

## 1. Branding organizacji

**Screenshoty:** `1.png`, `2.png`

![Admin Console](1.png)
![Brands – Rododendron Lab](2.png)

### Co zostało zrobione
Skonfigurowałam branding organizacji w Okta, tworząc dedykowaną markę **"Rododendron Lab"**. Okta pozwala na pełną personalizację strony logowania – logo, kolory i komunikaty powitalne.

### Dlaczego to ważne w IAM
Personalizacja strony logowania zwiększa rozpoznawalność systemu przez użytkowników i zmniejsza ryzyko ataków phishingowych – użytkownicy wiedzą jak powinna wyglądać ich strona logowania i mogą wykryć podróbkę.

---

## 2. Konfiguracja Authenticators (MFA)

**Screenshoty:** `3.png`, `18.png`, `19.png`, `20.png`

![Authenticators – stan początkowy](3.png)
![Katalog dostępnych authenticatorów](19.png)
![Dodawanie Google Authenticator](20.png)
![Authenticators – stan końcowy z Google Authenticator](18.png)

### Co zostało zrobione
Skonfigurowałam metody uwierzytelniania wieloskładnikowego (MFA):

| Metoda | Typ czynnika | Status | Charakterystyka |
|---|---|---|---|
| **Email** | Possession | ✅ Active | Kod OTP wysyłany na email |
| **Okta Verify** | Possession + Biometric | ✅ Active | Push, TOTP, FastPass – phishing resistant |
| **Google Authenticator** | Possession | ✅ Active | Kod TOTP via Google Authenticator app |
| **Password** | Knowledge | ✅ Active | Hasło użytkownika |

### Dlaczego to ważne w IAM
MFA blokuje ponad 99% ataków na konta. Okta Verify z FastPass oferuje uwierzytelnianie **phishing-resistant** – najwyższy dostępny standard bezpieczeństwa bez kart sprzętowych.

---

## 3. Tworzenie użytkowników

**Screenshoty:** `4.png`, `5.png`

![Formularz tworzenia użytkownika](4.png)
![Lista wszystkich użytkowników](5.png)

### Co zostało zrobione
Ręcznie utworzyłam 5 użytkowników testowych z wypełnionymi atrybutami profilu:

| Użytkownik | Dział | Status |
|---|---|---|
| Jan Kowalski | IT | Password expired |
| Anna Nowak | IT | Password expired |
| Marek Zielinski | Security | Password expired |
| Natalia Dabrowska | HR | Password expired |
| Karolina Wojcik | IT | Password expired |

Każdy użytkownik ma ustawioną flagę **"User must change password on first login"**.

### Dlaczego to ważne w IAM
Zarządzanie cyklem życia użytkownika (Identity Lifecycle Management) to fundament IAM. Atrybut `department` jest kluczowy – na jego podstawie działają automatyczne reguły przydziału do grup.

```
Joiner  → nowy pracownik  → tworzenie konta + nadanie uprawnień
Mover   → zmiana działu   → modyfikacja uprawnień
Leaver  → odejście        → dezaktywacja + odebranie dostępów
```

---

## 4. Zarządzanie grupami

**Screenshoty:** `6.png`, `7.png`, `8.png`, `9.png`, `10.png`, `11.png`, `12.png`

![Wczesna lista grup](6.png)
![Przypisywanie użytkowników do IT-Team](7.png)
![IT-Team – 2 członków](8.png)
![HR-Team – Natalia Dabrowska](9.png)
![Security-Team – Marek Zielinski](10.png)
![All-Employees – wszyscy pracownicy](11.png)
![Pełna lista wszystkich grup](12.png)

### Co zostało zrobione
Utworzyłam strukturę grup Security odpowiadającą działom organizacji:

| Grupa | Opis | Członkowie |
|---|---|---|
| **IT-Team** | Pracownicy działu IT | 4 |
| **HR-Team** | Pracownicy działu HR | 1 |
| **Security-Team** | Pracownicy działu Security | 1 |
| **All-Employees** | Wszyscy pracownicy | 5 |
| **Everyone** | Systemowa | 6 |
| **Okta Administrators** | Administratorzy | - |

### Dlaczego to ważne w IAM
Grupy są podstawową jednostką zarządzania dostępem w modelu **RBAC (Role-Based Access Control)**. Uprawnienia przypisuje się do grup, nie do pojedynczych użytkowników – co drastycznie upraszcza zarządzanie przy dużej skali.

---

## 5. Group Rules – automatyczne członkostwo

**Screenshoty:** `13.png`, `14.png`, `15.png`, `16.png`, `17.png`

![Pusta lista reguł – punkt startowy](13.png)
![Tworzenie reguły Auto-IT-Department](14.png)
![Aktywacja reguły](15.png)
![Pierwsza aktywna reguła](16.png)
![Wszystkie 3 reguły aktywne](17.png)

### Co zostało zrobione
Skonfigurowałam **3 automatyczne reguły członkostwa** przy użyciu **Okta Expression Language**:

```javascript
user.department == "IT"       →  IT-Team
user.department == "HR"       →  HR-Team
user.department == "Security" →  Security-Team
```

Wszystkie reguły mają status **Active**.

### Dlaczego to ważne w IAM
Group Rules to odpowiednik Dynamic Groups z Microsoft Entra ID. Automatyzują scenariusz **Mover**:

```
Admin zmienia: department = "IT"
         ↓
Okta automatycznie:
  ✅ Usuwa z HR-Team
  ✅ Dodaje do IT-Team
  ✅ Aktualizuje dostęp do aplikacji
```

---

## 6. Polityki uwierzytelniania (Authentication Policies)

**Screenshoty:** `24.png`, `25.png`, `26.png`, `27.png`, `28.png`, `53.png`

![Menu Authentication Policies](24.png)
![Lista App sign-in policies](25.png)
![Istniejąca reguła Catch-all](26.png)
![Tworzenie reguły – warunki IF](27.png)
![Tworzenie reguły – warunki THEN](28.png)
![Aktywna reguła Require MFA – ENABLED](53.png)

### Co zostało zrobione
Skonfigurowałam politykę **App Sign-On Policy** z regułą MFA:

| Parametr | Wartość |
|---|---|
| Nazwa reguły | `Require MFA for All Users` |
| Zakres | Any user / Any request |
| Wymaganie | Any 2 factor types |
| Possession constraints | Phishing resistant + Require user interaction |
| Re-authentication | Every 1 hour |
| Status | **ENABLED** ✅ |

### Dlaczego to ważne w IAM
To praktyczna implementacja zasady **Zero Trust** – "nigdy nie ufaj, zawsze weryfikuj". Każde logowanie wymaga dwóch niezależnych czynników, niezależnie od lokalizacji czy urządzenia.

---

## 7. Aplikacja SAML 2.0

**Screenshoty:** `29.png`, `30.png`, `31.png`, `32.png`, `34.png`, `35.png`, `36.png`, `37.png`, `38.png`, `39.png`, `40.png`, `21.png`

![Lista aplikacji](29.png)
![Wybór protokołu SAML 2.0](30.png)
![General Settings – nazwa aplikacji](31.png)
![SAML Settings – SSO URL i Audience URI](32.png)
![Advanced Settings – algorytmy podpisywania](34.png)
![Aplikacja Active](35.png)
![Metadata URL](36.png)
![Profile Attribute Statements](37.png)
![SAML Signing Certificate SHA-2](38.png)
![Assignments – widok przed przypisaniem](39.png)
![Wybór grupy All-Employees](40.png)
![Potwierdzenie – 1 group assigned successfully](21.png)

### Co zostało zrobione
Skonfigurowałam pełną integrację SAML 2.0 dla aplikacji **TestApp-SAML-Lab**:

| Parametr | Wartość |
|---|---|
| SSO URL | `https://localhost/saml/callback` |
| Audience URI | `https://localhost/saml` |
| Name ID Format | EmailAddress |
| Signature Algorithm | **RSA-SHA256** |
| Digest Algorithm | SHA256 |
| Certyfikat | SHA-2, Active, ważny do 2036 |
| Przypisanie | Grupa **All-Employees** |
| Status | **Active** ✅ |

**Attribute Statements:** `email`, `firstName`, `lastName`

### Dlaczego to ważne w IAM
SAML 2.0 to najszerzej stosowany protokół SSO w enterprise. Okta pełni rolę **Identity Provider (IdP)** – wystawia podpisany token XML. Użytkownik loguje się raz i automatycznie uzyskuje dostęp do wszystkich zintegrowanych aplikacji.

```
Użytkownik → Aplikacja (SP) → Okta (IdP) → SAML Assertion → Dostęp ✅
```

---

## 8. Aplikacja OIDC / OAuth 2.0

**Screenshoty:** `47.png`, `49.png`, `50.png`, `51.png`, `52.png`

![Lista aplikacji z TestApp-SAML-Lab](47.png)
![Wybór protokołu OIDC + Web Application](49.png)
![General Settings – Authorization Code + Redirect URIs](50.png)
![Assignments – Limit access to IT-Team](51.png)
![TestApp-OIDC-Lab Active – Client ID i Client Secret](52.png)

### Co zostało zrobione
Zarejestrowałam aplikację **TestApp-OIDC-Lab** używającą OIDC/OAuth 2.0:

| Parametr | Wartość |
|---|---|
| Protokół | OIDC – OpenID Connect |
| App Type | Web Application |
| Grant Type | **Authorization Code** |
| Client ID | `0oa13zk5pfvPSXgLj698` |
| Client Secret | Active (ukryty) |
| Sign-in redirect URI | `https://localhost/callback` |
| Sign-out redirect URI | `https://localhost/logout` |
| Przypisanie | Grupa **IT-Team** (ograniczony dostęp) |
| Status | **Active** ✅ |

### Dlaczego to ważne w IAM

| | SAML 2.0 | OIDC/OAuth 2.0 |
|---|---|---|
| Format tokenu | XML | JSON/JWT |
| Zastosowanie | Enterprise legacy apps | Nowoczesne apps, mobile, API |
| Rok powstania | 2005 | 2014 |

Znajomość obu protokołów jest kluczowa – różne organizacje używają różnych standardów w zależności od wieku i typu aplikacji.

---

## 9. Raporty i System Log

**Screenshoty:** `22.png`, `23.png`, `42.png`, `44.png`, `45.png`, `46.png`

![System Log – przegląd ogólny 349 zdarzeń](22.png)
![System Log – filtr user.lifecycle.create](23.png)
![MFA Enrollment by User – wykresy](44.png)
![MFA Enrollment – szczegółowa tabela użytkowników](46.png)
![Eksport CSV otwarty w Excelu](45.png)

### 9.1 System Log (Audit Trail)

Przeanalizowałam **System Log** z **349 zdarzeń** z całego okresu projektu:

| Typ zdarzenia | Co rejestruje |
|---|---|
| `user.lifecycle.create` | Tworzenie kont użytkowników |
| `user.session.start` | Logowania do systemu |
| `app.oauth2.token.grant` | Wydawanie tokenów OAuth 2.0 |
| `policy.evaluate_sign_on` | Ewaluacja polityk dostępu |
| `user.authentication.sso` | Logowania SSO do aplikacji |

### 9.2 MFA Enrollment by User

| Metryka | Wartość | Znaczenie |
|---|---|---|
| Total Users | 6 | Wszyscy użytkownicy tenanta |
| MFA Ready | 1 (17%) | Tylko admin ma MFA zarejestrowane |
| Bez MFA | 5 (83%) | **Krytyczne ryzyko – wymagają onboardingu!** |

Szczegółowa tabela pokazuje każdego użytkownika z przypisanymi grupami, liczbą authenticatorów i datą aktywacji.

### 9.3 Eksport do CSV

Wyeksportowałam raport MFA do pliku CSV i otworzyłam w Excelu. Plik zawiera kompletne dane: `user.id`, `user.fullName`, `user.status`, `authenticators.type`, `groups.name`, `user.created`, `user.activated`.

### Dlaczego to ważne w IAM
Logi audytu są wymagane przez regulacje: **GDPR, ISO 27001, SOX, NIS2**. Eksport do CSV umożliwia integrację z systemami SIEM (Splunk, Microsoft Sentinel). Raport MFA identyfikuje użytkowników bez ochrony – **priorytetowe zadanie dla IAM Administratora**.

---

## Podsumowanie umiejętności

| Obszar | Umiejętności |
|---|---|
| **Zarządzanie tożsamościami** | Tworzenie użytkowników, atrybuty profilu, cykl życia (Joiner/Mover/Leaver), stany konta |
| **Grupy i RBAC** | Grupy manualne, Group Rules z Okta Expression Language, przypisanie aplikacji do grup |
| **MFA** | Okta Verify (FastPass), Google Authenticator, Email OTP, typy czynników |
| **Polityki dostępu** | App Sign-On Policies, Zero Trust, phishing-resistant authentication |
| **Federacja i SSO** | SAML 2.0 (end-to-end), OIDC/OAuth 2.0 (Authorization Code), Attribute Statements |
| **Audyt i compliance** | System Log (349 zdarzeń), MFA Enrollment Report, eksport CSV |

---

## Technologie i protokoły

![Okta](https://img.shields.io/badge/Okta-007DC1?style=for-the-badge&logo=okta&logoColor=white)
![SAML](https://img.shields.io/badge/SAML_2.0-FF6B35?style=for-the-badge)
![OAuth](https://img.shields.io/badge/OAuth_2.0-3C3C3D?style=for-the-badge)
![OIDC](https://img.shields.io/badge/OpenID_Connect-F78C40?style=for-the-badge)

---

*Projekt wykonany jako część portfolio IAM. Środowisko: Okta Integrator Free Plan (bezpłatne konto deweloperskie).*
