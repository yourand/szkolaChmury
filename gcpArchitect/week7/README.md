Zadanie domowe nr  7

## 1. Wstęp

Dress4Win jest internetową firmą, która pomaga swoim użytkownikom zorganizować i zarządzać swoją garderobą za pomocą aplikacji internetowej i aplikacji mobilnej. Ich aplikacja rozrosła się z kilku serwerów do nawet kilkuset serwerów i urządzeń w kolokowanym centrum danych. Jednak pojemność ich infrastruktury jest obecnie niewystarczająca do szybkiego rozwoju aplikacji. Ze względu na ten wzrost i chęć firmy do szybszego wprowadzania innowacji, Dress4Win zobowiązuje się do pełnej migracji do publicznej chmury.

W pierwszej fazie migracji do chmury, Dress4Win przenosi swoje środowiska rozwojowe i testowe. Budują również miejsce odzyskiwania danych w przypadku awarii, ponieważ ich obecna infrastruktura znajduje się w jednym miejscu. Nie są oni pewni, które komponenty ich architektury mogą migrować tak jak są i które komponenty muszą zmienić przed migracją.

## 2. Opis środowiska

- Firma posiada jedną główną bazę danych MySQL - jeden serwer dla danych użytkowników, inwentaryzacji, danych statycznych. Serwis ten jest głównym elementem, który ma zostać przeniesiony do środowiska w Google Cloud.

- Dział bezpieczeństwa posiada serwery, które nie są związane bezpośrednio z samą architekturą aplikacji. Serwery te również mają zostać przeniesione do środowiska w Google Cloud:
  -  Jenkins, monitoring, bastion hosts, skanery bezpieczeństwa
  - Serwery składają się z : 8 CPUs oraz 32GB RAM.
- Firma lokalnie posiada serwery NAS, które odpowiadają za przechowywanie obrazów, logów oraz kopii zapasowych. Serwery muszą posiadają możliwość wersjonowania obiektów oraz kontrolowania dostępu na poziomie pojedynczego obiektu. Kilka informacji o aktualnej pojemności w środowisku lokalnym, która musi zostać spełniona w środowisku chmurowym:
  - 100 TB całkowitej pojemności; 35 TB wolnej przestrzeni.
- Dress4Win planuje zbudować miejsce odzyskiwania danych w przypadku awarii, ponieważ ich obecna infrastruktura znajduje się w jednym miejscu.
- Plan działania w przypadku awarii na poziomie samej bazy danych, ponieważ jest to krytyczny element działania aplikacji oraz środowisk w całej firmie, dlatego ten element wymaga dość dużej precyzji.
- Plan, który będzie brał pod uwagę odzyskiwanie danych z rozwiązania dla serwerów NAS w Google Cloud tak, aby firma nie musiała się przejmować, że ich obrazy czy też np. logi z danego dnia nagle znikną - nikt tego nie chce prawda?
- Zarząd planuje ekspansje globalną jeśli chodzi o aplikacje, wiec również jej dane będą udostępniane globalnie w pewnych regionach. Zarząd zauważył, że baza MySQL pod względem architektury staje się wąskim gardłem, kiedy mówimy o skalowalności. Firma jest gotowa zainwestować czas na migrację do pełni zarządzalnego, skalowalnego, relacyjnego serwisu baz danych dla regionalnych i globalnych danych aplikacyjnych, aby ekspansja na rynek zagraniczny nie była przeszkodą.



## 3. Założenia

Realizowany projekt będzie skupiał się w ogólności na przygotowaniu środowiska  na potrzeby składowania kopii zasobów oraz umożliwienia weryfikacji możliwości przeniesienia aplikacji do zasobów chmurowych. Dodatkowo budowane jest miejsce z którego będzie można odzyskać dane w wypadku awarii lokalnego DC.

Mapowanie usług:

| Lp.  | Nazwa                          | On-premises          | Cloud                                 |
| ---- | ------------------------------ | -------------------- | ------------------------------------- |
| 1.   | Baza danych                    | MySQL                | Cloud SQL - MySQL                     |
| 2.   | Serwery działu  bezpieczeństwa | 8 CPUs oraz 32GB RAM | n2-standard-8 (8  vCPU, 32 GB memory) |
| 3.   | Serwery NAS                    | kopie                | Coud Storage                          |
| 4.   | Serwery NAS                    | logi                 | Stackdriver                           |
| 5.   | Serwery NAS                    | obrazy               | Compute Engine -  Images              |



- Główna baz danych firmy zostanie oparta na usłudze Cloud SQL, która bazę danych w chmurze.

- Serwery działu bezpieczeństwa zostaną przeniesione na instancje wirtualne typu n2-standard-8.

- Serwery NAS służące do przechowywanie danych w formie plikowej zostaną przeniesione do następujących usług:
  - Pliki kopii bezpieczeństwa zostaną umieszczone w buckets Cloud Storage.
  
  - Logi będą zbierane przez usługę Stackdriver – operacja będzie wymagała przygotowania odpowiednich konektorów.
  
  - (chodziło o maszyny wirtualne?) - Obrazy będą przechowywane w prywatnym repozytorium obrazów w usłudze Compute Engine.
  
  - (chodziło o pliki graficzne?) - Obrazy będą zostaną umieszczone w buckets Cloud Storage.
  
  -  Cloud Storage będzie multi-regionalny i będzie skonfigurowane w sposób umożliwiający wersjonowanie plików.
  
  - Cloud Storage będzie posiadał polityki automatyzujące migrację archiwalnych danych do odpowiednich klas storage.
  
    | Klasa  Storage    | Okres  przechowywania w klasie |
    | ----------------- | ------------------------------ |
    | Standard  Storage | bieżące                        |
    | Nearline  Storage | > 30 dni                       |
    | Coldline  Storage | > 90 dni                       |
    | Archive  Storage  | > 365 dni                      |
  
    **** 
  
- **Za dostępność bazy danych w przypadku awarii będą odpowiadały mechanizmy HA w ramach usługi Cloud SQL.**

- Odzyskiwanie danych maszyn wirtualnych zostanie oparte na mechanizmach systemów operacyjnych maszyn wirtualnych.

- W celu zwiększenia skalowalności bazy danych zaproponowane zostanie wykorzystanie mechanizmu replik dostępnego dla usługi MySQL w Cloud SQL. Repliki dostępne są jedynie w tym samym regionie co baza danych,  ogranicza ich wykorzystanie przy obniżaniu opóźnień w globalnych rozwiązaniach. Wprawdzie możliwe jest zbudowanie repliki w innym regionie wykorzystując  replikę zewnętrzną jednak musimy się liczy z obniżeniem wydajności. W przypadku chęci dostosowania aplikacji i zmiany bazy danych możliwe jest wykorzystanie usługi Cloud Spanner.

- Proces migracji z klasycznej bazy MySQL do zaproponowanego środowiska.

## 4. Architektura

### 4.1. Logiczny podział zasobów organizacji

Logiczny podział organizacji w na działy funkcjonalne obrazuje zakres niniejszego projektu i będzie rozwijanym szablonem dla dalszych kroków.

![Alt text](https://github.com/yourand/szkolaChmury/blob/master/gcpArchitect/week7/img/log-org.png)



### 4.1.  Architektura horyzontalna logiczna

Ze względu na ograniczony zakres posiadanych danych zaproponowano rozwiązanie z dużym stopniem ogólności. Zostanie uszczegółowione po przeprowadzeniu analizy. Budowana infrastruktura zostanie umieszczona w projektach funkcjonalnych zapewniających separację ról w organizacji oraz przejrzyste zarządzanie.



![Alt text](https://github.com/yourand/szkolaChmury/blob/master/gcpArchitect/week7/img/log-proj.png)

### 4.2.  Architektura sieciowa

*Czekamy ... czekamy ... na transfer wiedzy .... :)*

### 4.3. Baza danych

Na potrzeby projektu zostanie wykorzystana baza danych MySQL  generacji 2  w wersji 5.7 z Cloud SQL.

Podstawowe parametry bazy danych:

| Nazwa                       | Wartość          |
| --------------------------- | ---------------- |
| Region                      | europe-west3     |
| Zone                        | enabled          |
| Database  version           | MySQL 5.7        |
| Private IP                  | enabled          |
| Machine type  (Tier)        | db-n1-highmem-64 |
| Storage type                | SSD              |
| Storage  capacity           | 10GB             |
| Automatic  storage increase | On               |
| Auto backups                | On               |

Dostęp do bazy będzie możliwy tylko za pomocą adresacji prywatnej.



### 4.4. Wysoka dostępność bazy danych

 Wysoka dostępność zostanie zrealizowana za pomocą wbudowanego mechanizmu HA, który działa regionalnie i jest dostępny w wybranych regionach.

Wysoką dostępność zapewnia synchroniczna kopia bazy danych realizowane w ramach regionu oraz dwóch stref.



![](D:\Users\Robert\OneDrive - overseer.pl\50 - Szkolenia\11 - GCP Architekt\2 - tygodnie\7 - tydzien7\img\db-ha.png)

### 5. Procedury przywracania po awarii.

### 5.1. Baza danych
