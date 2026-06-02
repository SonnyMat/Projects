# 🔐 Microsoft Entra ID – Hands-On Lab Portfolio

> Praktyczne laboratorium tożsamości i dostępu zbudowane od zera na Microsoft Entra ID (Azure AD).  
> Projekt dokumentuje realne umiejętności IAM – od podstawowej konfiguracji tenanta po zaawansowane polityki Zero Trust.

---

## 👤 O projekcie

To repozytorium jest dowodem praktycznej znajomości **Microsoft Entra ID** i ekosystemu tożsamości Microsoft.  
Każdy moduł został zrealizowany w działającym środowisku laboratoryjnym – nie są to notatki teoretyczne, lecz udokumentowane konfiguracje z rzeczywistego tenanta.

Projekt skierowany jest do rekruterów i osób technicznych, które chcą ocenić poziom znajomości tematów z obszaru **Identity & Access Management (IAM)**.

---

## 🗂️ Zawartość projektu

| Moduł | Temat | Poziom |
|---|---|---|
| 01 | Konfiguracja tenanta Entra ID | ⚪ Podstawowy |
| 02 | Zarządzanie użytkownikami i grupami (w tym Dynamic Groups) | ⚪ Podstawowy |
| 03 | Role i administracja – RBAC + Custom Roles | 🔵 Średni |
| 04 | Multi-Factor Authentication (MFA) + SSPR | 🔵 Średni |
| 05 | Conditional Access – polityki Zero Trust | 🔴 Zaawansowany |
| 06 | Rejestracja aplikacji – OAuth2 / OIDC / JWT | 🔴 Zaawansowany |
| 07 | Monitoring – Sign-in Logs, Audit Logs, Log Analytics + KQL | 🔵 Średni |
| 08 | Privileged Identity Management (PIM) – Just-in-Time Access | 🔴 Zaawansowany |

---

## 🛠️ Technologie i usługi

![Microsoft Entra ID](https://img.shields.io/badge/Microsoft%20Entra%20ID-0078D4?style=for-the-badge&logo=microsoftazure&logoColor=white)
![Azure](https://img.shields.io/badge/Azure-0089D6?style=for-the-badge&logo=microsoftazure&logoColor=white)
![Microsoft 365](https://img.shields.io/badge/Microsoft%20365-D83B01?style=for-the-badge&logo=microsoft&logoColor=white)
![Log Analytics](https://img.shields.io/badge/Log%20Analytics-0078D4?style=for-the-badge&logo=microsoftazure&logoColor=white)
![KQL](https://img.shields.io/badge/KQL-black?style=for-the-badge&logo=azuredataexplorer&logoColor=white)

---

## 📌 Kluczowe umiejętności udokumentowane w projekcie

- ✅ Zarządzanie cyklem życia tożsamości (użytkownicy, grupy, bulk import CSV)
- ✅ Konfiguracja Dynamic Groups na podstawie atrybutów użytkowników
- ✅ Tworzenie Custom Roles zgodnie z zasadą Least Privilege
- ✅ Wdrożenie MFA przez Authentication Methods Policy (nowoczesna metoda, nie legacy per-user)
- ✅ Self-Service Password Reset (SSPR) z wieloma metodami weryfikacji
- ✅ Projektowanie polityk Conditional Access (Named Locations, Sign-in Risk, Device Compliance)
- ✅ Rejestracja aplikacji OAuth2/OIDC i analiza JWT tokenów
- ✅ Eksport logów do Log Analytics i zapytania w języku KQL
- ✅ Just-in-Time access przez PIM z wymaganym MFA przy aktywacji roli

---

## 🔍 Struktura repozytorium

```
📁 entra-id-lab/
├── 📁 01-tenant-setup/
│   ├── README.md          # Opis konfiguracji
│   └── screenshots/       # Dowody konfiguracji
├── 📁 02-users-groups/
│   ├── README.md
│   ├── bulk-import.csv    # Przykładowy plik importu
│   └── screenshots/
├── 📁 03-rbac-roles/
├── 📁 04-mfa-sspr/
├── 📁 05-conditional-access/
│   └── policies/          # Opisy polityk CA
├── 📁 06-app-registration/
│   └── jwt-token-test.md  # Wynik testu tokenu JWT
├── 📁 07-monitoring-kql/
│   └── queries.kql        # Przykładowe zapytania KQL
└── 📁 08-pim/
```

---

## 🧪 Środowisko laboratoryjne

Projekt zrealizowany na:
- **Microsoft 365 Developer Tenant** z licencją **Entra ID P2**
- Środowisko izolowane, stworzone wyłącznie do celów edukacyjnych
- Wszystkie konfiguracje testowane w trybie **Report-only** przed włączeniem produkcyjnym

---

## 💡 Wnioski i dobre praktyki

Podczas realizacji projektu szczególnie zwróciłam uwagę na:

- Różnicę między **legacy MFA (per-user)** a nowoczesną **Authentication Methods Policy** – Microsoft rekomenduje migrację do nowej metody
- Znaczenie trybu **Report-only** w Conditional Access przed wdrożeniem – pozwala uniknąć przypadkowego zablokowania użytkowników
- Zasadę **Least Privilege** przy tworzeniu ról – Custom Role z jednym uprawnieniem jest bezpieczniejsza niż przypisanie wbudowanej roli z szerokim zakresem
- Wartość **PIM** w eliminacji stałych przypisań do ról uprzywilejowanych – każda aktywacja zostawia ślad w audit log

---

## 📬 Kontakt

**Sonia Matysiak**  
[![LinkedIn](https://img.shields.io/badge/LinkedIn-0077B5?style=for-the-badge&logo=linkedin&logoColor=white)](https://linkedin.com/in/TwójProfil)
[![Email](https://img.shields.io/badge/Email-D14836?style=for-the-badge&logo=gmail&logoColor=white)](mailto:twoj@email.com)

---

*Projekt w toku – kolejne moduły są systematycznie uzupełniane o screenshoty i szczegółową dokumentację.*
