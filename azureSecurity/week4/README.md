


### Tydzień 4 zadanie

##### Ochrona tożsamości oraz zarządzanie dostępem uprzywilejowanym (Azure AD Privileged Identity Management)



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

