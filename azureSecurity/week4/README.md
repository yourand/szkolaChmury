### Tydzień 4 zadanie

#### Ochrona tożsamości oraz zarządzanie dostępem uprzywilejowanym (Azure AD Privileged Identity Management)



###### Utwórz police user risk, którą przyporządkujesz dla wszystkich użytkowników w ramach warunków ryzyka średni. Ponadto zezwól na dostęp, ale po zmianie hasła, wymuś tą police.

![](https://github.com/yourand/szkolaChmury/blob/master/azureSecurity/week4/img/1-user-risk-policy.JPG)



##### Utwórz police sign-in risk, którą przyporządkujesz dla wszystkich użytkowników w ramach warunków ryzyka wysoki. Ponadto zezwól na dostęp, ale wraz z MFS oraz wymuś tą police.

![](https://github.com/yourand/szkolaChmury/blob/master/azureSecurity/week4/img/2-sign-in-risk-policy.JPG)

##### Postaraj się użyć wybranych kont, w których będziesz mógł zalogować się z wielu miejsc geograficznych, a następnie zobacz jakie powstały alerty.

POC: Zalogowanie się z dwóch geograficznie odległych miejsc na jedno konto:

- system wykrywa zagrożenie
  ![](https://github.com/yourand/szkolaChmury/blob/master/azureSecurity/week4/img/3-sign-in-risk-policy-weryfikacja.JPG)


- wymusza użycie drugiego składnika do weryfikacji tożsamości
  ![](https://github.com/yourand/szkolaChmury/blob/master/azureSecurity/week4/img/3-sign-in-risk-policy-weryfikacja2.JPG)


- wymuszona zmiana hasła
  ![](https://github.com/yourand/szkolaChmury/blob/master/azureSecurity/week4/img/3-sign-in-risk-policy-weryfikacja3.JPG)

-logi zdarzenia
![](https://github.com/yourand/szkolaChmury/blob/master/azureSecurity/week4/img/3-sign-in-risk-policy-log.JPG)

#### Usługi Azure AD Privileged Identity Management

#### Ćwiczenie

##### Przegląd i dodanie ról

**AAD

![](https://github.com/yourand/szkolaChmury/blob/master/azureSecurity/week4/img/5-dodaj-role-1.jpg)

**Subskrypcja

![](https://github.com/yourand/szkolaChmury/blob/master/azureSecurity/week4/img/6-dodaj-role-sub.jpg)


##### Administrator Grup na życzenie

- **przygotowanie roli**
  ![](https://github.com/yourand/szkolaChmury/blob/master/azureSecurity/week4/img/7-rola-1.jpg)

- **konfiguracja warunków uzyskania uprawnień z jednym akceptującym**
  ![](https://github.com/yourand/szkolaChmury/blob/master/azureSecurity/week4/img/7-rola-1.jpg)

- **lista dostępnych ról**
  ![](https://github.com/yourand/szkolaChmury/blob/master/azureSecurity/week4/img/8-lista-rol.jpg)

- **wysłanie żądania uprawnień z perspektywy użytkownika**

![](https://github.com/yourand/szkolaChmury/blob/master/azureSecurity/week4/img/9-warunki-uzyskania.jpg)

- **wysłanie żądania**

![](https://github.com/yourand/szkolaChmury/blob/master/azureSecurity/week4/img/10-wyslanie-rzadania.jpg)

- **akceptacja żądania z perspektywy zatwierdzającego**

![](https://github.com/yourand/szkolaChmury/blob/master/azureSecurity/week4/img/11-akceptacja.jpg)

- **aktywacja z perspektywy użytkownika**

![](https://github.com/yourand/szkolaChmury/blob/master/azureSecurity/week4/img/14-aktywne-role.jpg)

- **nabyte uprawnienia z perspektywy użytkownika**

![](https://github.com/yourand/szkolaChmury/blob/master/azureSecurity/week4/img/12-uprawnienia.jpg)
