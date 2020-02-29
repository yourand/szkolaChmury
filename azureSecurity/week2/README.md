## Zadanie Tydzień 2

#### 1. Utwórz konta oraz Grupy w ten sposób, aby było dynamiczne członkostwo. Przedstaw przykładową grupę.

###### Przygotowanie zmiennych

```powershell
$userPassword=$(openssl rand -base64 12)
$PasswordProfile = New-Object -TypeName Microsoft.Open.AzureAD.Model.PasswordProfile
$PasswordProfile.Password = $(openssl rand -base64 12)
```
###### Utworzenie użytkowników

```
New-AzureADUser -DisplayName "Week2 01" -PasswordProfile $PasswordProfile `
-UserPrincipalName "week02-01@azure.overseer.pl" `
-AccountEnabled $true -MailNickName "week201" `
-Department "Week02"

New-AzureADUser -DisplayName "Week2 02" -PasswordProfile $PasswordProfile `
-UserPrincipalName "week02-02@azure.overseer.pl" `
-AccountEnabled $true -MailNickName "week202" `
-Department "Week02"
```

###### Wynik
```
PS C:\Users\Robert> Get-AzureADUser -Filter "Department eq 'Week02'"

ObjectId                             DisplayName UserPrincipalName     UserType
--------                             ----------- -----------------     --------
da837b29-998a-42f6-b4ed-c373e2f30001 Week2 01    week02-01@overseer.pl Member
0873de89-9989-4c59-ad38-0844c63548f4 week2 02    week02-02@overseer.pl Member
```

###### Utworzenie grupy

```
New-AzureADMSGroup -DisplayName "Week 02 Users" `
-Description "Dynamic group of week 02 users" `
-MailEnabled $False `
-MailNickName "group" `
-SecurityEnabled $True `
-GroupTypes "DynamicMembership" `
-MembershipRule "(user.department -contains ""Week02"")" `
-MembershipRuleProcessingState "On"

Add-AzureADGroupOwner -ObjectId "482febce-c5d8-4891-9a29-bb8179e7e555" `
-RefObjectId "72a53493-9dd5-4217-8079-1c4258f48402"
```



###### Wynik

```
PS C:\Users\Robert> Get-AzureADGroupMember -ObjectId "482febce-c5d8-4891-9a29-bb8179e7e555"

ObjectId                             DisplayName UserPrincipalName     UserType
--------                             ----------- -----------------     --------
da837b29-998a-42f6-b4ed-c373e2f30001 Week2 01    week02-01@overseer.pl Member
0873de89-9989-4c59-ad38-0844c63548f4 week2 02    week02-02@overseer.pl Member
```



#### 2. Zaproponuj sposób uwierzytelnienia dla aplikacji. Określ w jaki sposób będzie mogła posiadać prawa oraz w jaki sposób może zostać wykorzystana w Azure AD. Przedstaw właściwości tego konta.

Dla uwierzytelnienia aplikacji możemy wykorzystać Service Principal Object, który będzie referencją/endpointem naszej aplikacji.

Możemy to wykorzystać przy weryfikacji poświadczeń dostępowych przez Azure AD między komponentami aplikacji. W celu uzyskania zamierzonej funkcjonalności :

- tworzymy komponent serwera który będzie działał jako endpoint dla żądań.
- service principal dla komponentu serwera naszej aplikacji, który umożliwi uwierzytelni siebie w Azure,
-  przypisujemy uprawnienia do serwera,
- tworzymy komponent kliencki i powiązany service proncipal.
- przypisujemy odpowiednio serwer i aplikację.



#### 3. Przedstaw najprostszą metodę przejrzenia zwartości Azure AD za pomocą API.

Najprostszą metodą przeglądanie zawartości Azure AD jest wykorzystanie  Azure Active Directory Graph API, który jest RESTful serwisem, który pozwala przegląd zawartości za pomocą żądań HTTP.
