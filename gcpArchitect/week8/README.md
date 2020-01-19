## Zadanie 1

Uruchamiasz aplikację w Google App Engine, która obsługuje ruch produkcyjny. Chcesz wdrożyć ryzykowną, ale konieczną zmianę w aplikacji. Może ona zniszczyć Twoją usługę, jeśli nie będzie prawidłowo zakodowana. Podczas tworzenia aplikacji zdajesz sobie sprawę, że możesz ją prawidłowo przetestować tylko z rzeczywistym ruchem użytkowników.



#### 1.  Stworzenie aplikacji i umieszczenie jej na App Engine.

Na potrzeby testu wykorzystano aplikację w python wyświetlająca komunikat na stronie.



```
gcloud app deploy --version prod-01
```



```
$ curl szkola-chmury-agcp.appspot.com
Krytyczna aplikacja wersja 1.0
```



#### 2. Wdróż na środowisko drugą wersję aplikacji 

Wdrożenie drugiej wersji aplikacji bez przekierowywania ruchu na nowa wersję.

```
gcloud app deploy --no-promote --version prod-02
```



```
$ gcloud app versions list
SERVICE  VERSION.ID  TRAFFIC_SPLIT  LAST_DEPLOYED              SERVING_STATUS
default  prod-01     1.00           2020-01-18T23:42:58+01:00  SERVING
default  prod-02     0.00           2020-01-18T23:54:03+01:00  SERVING

```



#### 3. Użyj funkcji traffic spliting, aby wysłać do nowej wersji aplikacji niewielki procent użytkowników.

Przekierowanie 20% ruchu na nową wersję.

```
gcloud app services set-traffic default --splits prod-01=0.8,prod-02=0.2 \
	--split-by cookie
```



```
$ gcloud app versions list
SERVICE  VERSION.ID  TRAFFIC_SPLIT  LAST_DEPLOYED              SERVING_STATUS
default  prod-01     0.80           2020-01-18T23:42:58+01:00  SERVING
default  prod-02     0.20           2020-01-18T23:54:03+01:00  SERVING
```

```
Krytyczna aplikacja wersja 1.0
[robert@gcloud-kali:2-python (⎈|AKSTestCluster12-2:default)]
$ curl szkola-chmury-agcp.appspot.com
Krytyczna aplikacja wersja 1.0
[robert@gcloud-kali:2-python (⎈|AKSTestCluster12-2:default)]
$ curl szkola-chmury-agcp.appspot.com
Krytyczna aplikacja wersja 1.0
[robert@gcloud-kali:2-python (⎈|AKSTestCluster12-2:default)]
$ curl szkola-chmury-agcp.appspot.com
Krytyczna aplikacja wersja 2.0
```
## Zadanie 2

Zarząd pewnej firmy zdecydował się na przeniesienie swojej aplikacji do środowiska w Google Cloud. Zdecydowali się umieścić swoją aplikacje na środowisku w App Engine. Środowisko wymaga integracji z bazą danych MySQL z których aplikacja pobiera dane.



#### 1. Utwórz instację App Engine tym razem w wersji Flexible.



Na potrzeby zadania wykorzystano gotową aplikacje CloudSql w dotnet. Deployment zrealizowano za pomocą Visual Studio.

Tip: Warto dodać wpis w app.yaml:

```manual_scaling:
manual_scaling:
  instances: 1
```



#### 2. Na swoje środowisko wdróż aplikacje.

# Uzupelnij



#### 3. Powołaj usługę Cloud SQL,

Dla próby terraformem, bo czemu nie :)

```
terraform init
terraform plan
terraform apply -auto-approve
```





#### 4. Pytanie.

Posiadasz aplikację App Engine Standard Environment, która używa SQL w chmurze dla backendu bazy danych. W godzinach szczytu użytkowania, liczba zapytań do Cloud SQL skutkuje spadkiem wydajności. W jaki sposób można najlepiej pomóc w ograniczeniu wąskich gardeł wydajnościowych dla typowych zapytań?

- Przełączyć środowisko na App Engine Flexible Environment
- **Ustawić Memcache App Engine na dedicated service level i zwiększyć pojemność pamięci podręcznej, aby sprostać szczytowemu obciążeniu zapytań.**
- Zwiększyć pamięć swojej bazy danych SQL w chmurze
- Ustawić App Engine's Memcache na poziom shared Service level.



Najlepszym wydaj się być ustawienie Memcache App Engine na dedicated service level, w celu przyspieszenia odpowiedzi bazy przez cache-ownie często używanych danych. Dedykowany memcache pozwoli na dostosowanie do wymagań aplikacji aby ograniczyć spadki wydajności.