#### Zadanie 1.

**Tworzenie połączenia hybrydowego**

- Utwórz nowe Azure AD, które będzie wykorzystywe do tworzenia naszych połączeń hybrydowych. Możesz śmiało zrobić to z portalu! 😉
- Utwórz wirtualną maszynę, która będzie DC. Możesz też skorzytać z **Azure Quickstart Templates - active-directory-new-domain**.
- Zainstaluj AD connect a następnie połącz z Twoim nowo utworzonym Azure AD.
- W ramach SSO użyj opcji **Pass-through Authentication** oraz dokonaj konfiguracji tak, aby została wykorzystana opcja **Password Writeback**

##### Realizacja

- instalacja serwera 2019 i konfiguracja Active Directory
- Instalacja i konfiguracja Azure AD Connect
- dodanie polityki GPO

```
User Configuration\Administrative Templates\Windows Components\Internet Explorer\Internet Control Panel\Security Page

https://autologon.microsoftazuread-sso.com  value 1
https://aadg.windows.net.nsatc.net value 1
```

##### Testy

- logowanie do office 365 migrowanym użytkownikiem

  

**Uwierzytelnianie wieloskładnikowe w Azure AD (Multi-Factor Authentication)**

- Na samym początku włącz trial Azure AD w wersji P2.
- Włącz MFA dla swojej organizacji oraz skonfiguruj opcje potwierdzanie swojej tożsamości poprzez **SMS**, jeśli chcesz możesz też skonfigurować potwierdzenie poprzez aplikację **authenticator**.
- Określ zaufane podsieci w Twojej organizacji, dla których nie będziesz musiał wykorzystywać MFA.
- Utwórz grupy z członkostwem dynamicznym, zobacz jaki mechanizm będzie wykorzystywany w tym zadaniu z perspektywy użytkowników.

##### Realizacja

- włącznie trial P2,
- włączenie MFA,
- określenie podsieci lokalnych,
- Dodanie nazwy departamentu "IT" do użytkowników onpremis, stworzenie grupy dynamicznej.

##### Testy



Założenie MFA z dozwolonym IP prywatnym

Wymaga sms

Założenie MFA z dozwolonym adresem IP publicznym 

Nie wymaga drugiego składnika

**Dostęp warunkowy przy logowaniu do Azure AD.**

- Uruchom politykę (policy) umożliwiając dostęp do wybranej aplikacji nadając te prawa wybranej grupie lub określając wybranych użytkowników
- Dodaj do utworzonej polityki warunek, który umożliwi dostęp, gdy zagrożenie dla **Sign-in Risk** występuje jako średnie i tylko z lokalizacji jaką jest Polska. Dodatkowo nadaj możliwość dostępu z urządzeń typu iOS
- Nadaj możliwość dostępu, ale z wymogiem logowania za pomocą MFA.

##### Realizacja

- stworzenie "Named Location"  wskazującą na Polskę.
- Utworzenie polityki Conditional Access
