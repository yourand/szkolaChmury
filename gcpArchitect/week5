> XXXXXXXX
>
> **Projekt **
>
> „Wysoka dostępność aplikacji w chmurze publicznej GCP"
>
> dla
>
> Mountkirk Games
>
> **Opracowany przez:**
>
> XXXXX
>
> XXXXX
>
> XXXXX
>
> Wersja dokumentu: 0.1
>
> Identyfikator projektu: MountkirkGames20191228
>
> Data opracowania: 2019-12-28

Dane Dokumentu

+-----------------+-----------------+-----------------+-----------------+
| Nazwa Projektu: | Wysoka          |                 |                 |
|                 | dostępność      |                 |                 |
|                 | aplikacji w     |                 |                 |
|                 | chmurze         |                 |                 |
|                 | publicznej GCP  |                 |                 |
+-----------------+-----------------+-----------------+-----------------+
| Opracowany      | \_\_\_\_\_\_\_\ | Nr Wersji:      | 0.1             |
| Przez:          | _\_\_\_\_\_\_\_ |                 |                 |
|                 | \_              |                 |                 |
| Rola:           |                 |                 |                 |
|                 | Kierownik       |                 |                 |
|                 | Projektu        |                 |                 |
|                 |                 |                 |                 |
|                 | \_\_\_\_\_\_\_\ |                 |                 |
|                 | _\_\_\_\_\_\_\_ |                 |                 |
|                 | \_\_            |                 |                 |
+-----------------+-----------------+-----------------+-----------------+
| Rola:           | Konsultant      | Data Wersji:    | 2019-12-28      |
|                 | Techniczny      |                 |                 |
| Rola:           |                 |                 |                 |
|                 | \_\_\_\_\_\_\_\ |                 |                 |
|                 | _\_\_\_\_\_\_\_ |                 |                 |
|                 | \_\_\_          |                 |                 |
|                 |                 |                 |                 |
|                 | Konsultant      |                 |                 |
|                 | Techniczny      |                 |                 |
+-----------------+-----------------+-----------------+-----------------+
| Sprawdzony      |                 |                 |                 |
| Przez:          |                 |                 |                 |
+-----------------+-----------------+-----------------+-----------------+

Rozdzielnik

  ---- ------ --------------------
  Od   Data   Telefon/Faks/Email
              
              
              
  ---- ------ --------------------

  ---- ------------- -------- --------------------
  Do   Działanie\*   Termin   Telefon/Faks/Email
                              
                              
                              
                              
                              
                              
                              
  ---- ------------- -------- --------------------

Rodzaje Działania: Zatwierdzenie, Przegląd, Do Wiadomości, Do Akt,
Wymagane Działanie, Uczestnictwo w Zebraniu, Inne (proszę określić,
jakie?)

Historia Zmian

  ----------- ------------- ---------------------- ---------------------- ----------- --------
  Nr Wersji   Data Wersji   Zmiany Wprowadził(a)   Opis Zmian             Recenzent   Status
  0.1         2019-12-28    Robert Kostecki        Utworzenie dokumentu               
                                                                                      
                                                                                      
                                                                                      
                                                                                      
                                                                                      
  ----------- ------------- ---------------------- ---------------------- ----------- --------

Spis treści {#spis-treści .Nagwekspisutreci}
===========

[[1.]{.underline} [Opis środowiska.]{.underline} 4](#opis-środowiska.)

[[2.]{.underline} [Wymagania.]{.underline} 4](#wymagania.)

[[3.]{.underline} [Założenia.]{.underline} 4](#założenia.)

[[4.]{.underline} [Monitoring środowiska.]{.underline}
4](#monitoring-środowiska.)

[[5.]{.underline} [Koncepcja rozwiązania.]{.underline}
5](#koncepcja-rozwiązania.)

[[5.1.]{.underline} [Architektura rozwiązania.]{.underline}
5](#architektura-rozwiązania.)

[[5.2.]{.underline} [Zapewnienie wysokiej dostępność.]{.underline}
6](#zapewnienie-wysokiej-dostępność.)

[[5.3.]{.underline} [Skalowanie aplikacji.]{.underline}
8](#skalowanie-aplikacji.)

[[5.4.]{.underline} [Aktualizacja aplikacji.]{.underline}
8](#aktualizacja-aplikacji.)

[[6.]{.underline} [PoC.]{.underline} 9](#poc.)

[[7.]{.underline} [Harmonogram prac.]{.underline}
11](#harmonogram-prac.)

[[8.]{.underline} [Szkolenia.]{.underline} 11](#szkolenia.)

[[9.]{.underline} [Testy odbiorowe.]{.underline} 11](#testy-odbiorowe.)

Spis rysunków
=============

[[Rysunek 1. Logiczny schemat architektury rozwiązania.]{.underline}
5](#_Toc28540980)

[[Rysunek 2. Wysoka dostępność - dwa regiony A-A.]{.underline}
6](#_Toc28540981)

[[Rysunek 3. Rysunek 2. Wysoka dostępność - 2 regiony A-P.]{.underline}
7](#_Toc28540982)

[[Rysunek 4. Wysoka dostępność - cztery regiony A-A.]{.underline}
7](#_Toc28540983)

[[Rysunek 5. Procedura aktualizacji aplikacji.]{.underline}
8](#_Toc28540984)

Opis środowiska.
================

Zarząd MountKirk zdecydował, że wypuszczając swoją nową grę na rynek
skorzystają z gotowego rozwiązania w Google Cloud jakim jest Compute
Engine. firma postanowiła również wprowadzić nową aplikację dla graczy,
którą również chcą oprzeć na tym rozwiązaniu.

Gra okazała się wielkim sukcesem, a aplikacja również przyniosła
oczekiwane rezultaty. Firma nie spodziewała się, aż tak wielkiej ilości
nowych osób z całego świata, którzy będą chcieli skorzystać z nowych
funkcjonalności. W pewnym momencie całe środowisko stało się mało
wydajne i użytkownicy końcowi zaczęli dostrzegać, że platformie
doskwierają jakieś problemy.

Specjaliści starali się zwiększać na bieżąco parametry maszyn
wirtualnych, aby podnieść wydajność, lecz było to mało efektowne oraz
zajmowało masę czasu. Realizowany projekt ma dokonać optymalizacji i
automatyzacji realizowanych zadań.

Wymagania.
==========

MountKirk określił następujące wymagania:

a)  Na ten moment firma nie może zrezygnować z maszyn wirtualnych,
    dlatego nowa architektura musi korzystać z wirtualnych maszyn
    postawionych z niestandardowego obrazu, dostarczonego bezpośrednio
    przez firmę.

b)  Rozwiązanie musi dynamicznie skalować się w górę lub w dół w
    zależności od aktywności w grze - bez większej ingerencji
    specjalistów

c)  Gracze korzystający z funkcjonalności firmy pochodzą z całego
    świata, a w szczególności z Stanów Zjednoczonych oraz Europy.
    Poprzez odpowiednie umiejscowienie rozwiązania MountKirk chce
    zredukować opóźnienie jakie występuje dla osób łączących się z US.

d)  Rozwiązanie musi zapobiegać jakiejkolwiek przerwie w dostarczaniu
    funkcjonalności na wypadek awarii np. regionu Google Cloud.

e)  Rozwiązanie musi umożliwość łatwe i bezpiecznie wdrażanie nowych
    wersji oprogramowania do instancji bez konieczności wpływania na
    całe środowisko.

Założenia.
==========

a)  Biorąc pod uwagę wymagania dotyczące aplikacji odpowiednim
    rozwiązaniem jest użycie managed instance groups (MIGs).

Użycie MIGs zaadresuje takie wymagania jak:

-   Wysoka dostępność,

-   Skalowalność.

-   Automatyzacja aktualizacji.

a)  Dla zapewnienia lepszej dostępności użyte zostaną grupy regionalne.

b)  Niskie opóźnienia dla graczy zostaną uzyskane poprzez umieszczenie
    grup w regionach w Europie oraz USA.

c)  Obsługa awarii regionu zostanie zrealizowana poprzez duplikację
    grupy do zapasowego regionu lub przekierowanie ruchu do istniejącego
    regionu. Wybór scenariusza zależnie od woli klienta.

d)  Bezpieczne wdrażanie nowych wersji zostanie zrealizowane canary
    testing/updates co zapewni możliwość wdrożenia aktualizacji dla
    wybranej grupy maszyn przetestowanie i podjęcie decyzji o wdrożeniu
    aktualizacji na pozostałe maszyny lub jej wycofanie.

Monitoring środowiska.
======================

Przed rozwinięciem architektury w projekt techniczny należy dokonać
analizy istniejącego środowiska ze szczególnym uwzględnieniem:

a)  Obciążenia środowiska.

b)  Ilości aktywnych instancji.

c)  Struktury aplikacji.

<!-- -->

5.  Koncepcja rozwiązania.
    ======================

    1.  Architektura rozwiązania.
        -------------------------

Zaproponowana architektura zakłada wykorzystanie podwójnych regionów, z
których jeden jest w trybie pasywnym i zostanie wykorzystany w przypadku
utraty regionu głównego.

![](media/image1.jpeg){width="6.3in" height="7.333333333333333in"}

[]{#_Toc28540980 .anchor}Rysunek 1. Logiczny schemat architektury
rozwiązania.

Zapewnienie wysokiej dostępność.
--------------------------------

Dostępność aplikacji będzie realizowana na dwóch poziomach:

a)  Mechanizmy MIGs, które zagwarantują poprawną pracę wymaganej ilości
    maszyn wirtualnych:

-   Wykrywanie awarii serwera wirtualnego co powoduje stworzenie nowej
    instancji.

-   Sprawdzanie poprawności pracy aplikacji poprzez okresową weryfikacje
    odpowiedzi zapytania http.

-   Rozdzielenie instancji aplikacji w wybranych regionach.

-   Rozrzucanie obciążenia za pomocą usługi Load Balancera.

b)  Mechanizmy Load Balancer-a, który zapewni dostępność w przypadku
    awarii regionu. Możliwe jest kilka scenariuszy różniące się
    kosztami.

-   HA oparte na dwóch regionach. Obra regiony obsługują klientów, w
    przypadku awarii jednego regionu drugi przejmuje całość ruchu.

> Główne zalety niski koszt oraz szybkość przełączenia klientów.
>
> Głowna wada: wzrost opóźnień dla klientów w regionie, który uległ
> awarii, brak zapasu w przypadku problemów w pozostałym regionie. Na
> poniższym schemacie widzimy symulacje awarii regionu europejskiego.

[]{#_Toc28540981 .anchor}Rysunek 2. Wysoka dostępność - dwa regiony A-A.

-   HA oparte na zdublowanych regionach na kontynentach z czego jeden z
    pary regionów jest w trybie pasywnym z minimalną ilością maszyn i
    zostanie wyskalowany w razie potrzeby. Główne zalety podwójne
    zabezpieczenie w przypadku awarii regionu bez pogorszenia opóźnień,
    zapewni pracę aplikacji nawet przy awarii 3 regionów jednocześnie.

> Główne wady nieznacznie wyższy koszt, wyższa poziom komplikacji
> infrastruktury, możliwe zakłócenie w jakości świadczonych usług zanim
> system zapasowy się wyskaluje.

[]{#_Toc28540982 .anchor}Rysunek 3. Rysunek 2. Wysoka dostępność - 2
regiony A-P.

-   Możliwa jest również architektura hybrydowa, kiedy regiony są
    zdublowane i wszystkie przyjmują ruch. Takie rozwiązanie ograniczy
    zakłócenia w przypadku awarii jednego regionu, co wynika z tego, że
    ruch jest cały czas kierowany do „zapasowego regionu" i będzie
    łatwiej się skalował. W tym scenariuszu wzrasta komplikacja
    wykonywania aktualizacji oprogramowania.

[]{#_Toc28540983 .anchor}Rysunek 4. Wysoka dostępność - cztery regiony
A-A.

Ze względu na konieczność zachowanie wysokiej dostępności oraz łatwości
aktualizacji rekomendowane jest wykorzystanie architektury z pasywnymi
duplikatami.

Skalowanie aplikacji.
---------------------

Skalowanie grupy maszyn dla aplikacji będzie realizowane w sposób
horyzontalny u oparciu o obciążenie CPU. W trakcie stabilizacji systemu
możliwa zmiana metryk oddających specyficzne wymagania aplikacji,

Skalowanie będzie realizowane w ramach grupy w górę i w dół. Parametry
cooldown po inicjalizacji nowej maszyny, oraz satbililizacji przed
skalowaniem w dół zostaną określne na podstawie obserwacji środowiska i
aplikacji.

Aktualizacja aplikacji.
-----------------------

Realizacja aktualizacji planowana jest w dwóch głównych krokach.

a)  Wykonanie canary update w wybranym regionie.

Ten typ aktualizacji pozwala na update wybranej ilości instancji w celu
wykonania testów na środowisku produkcyjnym. Zależnie od wyniku testów
aktualizacje można rozszerzyć na resztę grupy lub ją wycofać.

b)  Weryfikacja poprawności aktualizacji.

c)  Wykonanie rolling update w pozostałych regionach.

W przypadku akceptacji aktualizacji, przeprowadza jest pełna
aktualizacja pozostałych regionów.

Aktualizacja instancji maszyn wirtualnych wymaga wyłączenia
aktualizowanej instancji oraz powołania nowej. Odpowiednia
parametryzacja operacji ograniczy możliwy wpływ na użytkowników systemu.
Parametry pozwalają między innymi, określić akceptowalną ilość
niedostępnych instancji (maxUnavailable) oraz ilość instancji, które
zostaną stworzone z wyprzedzeniem i zastąpią aktualizowane istniejące
instancje (maxSurge). Parametry należy dobrać biorąc pod uwagę średnią
ilość instancji uruchomionych podczas okna aktualizacji oraz istniejące
quot-y na posiadanych zasobach. Odpowiednio dobrane parametry
zminimalizują zakłócenia w pracy aplikacji przy założeniu jej
bezstanowości.

[]{#_Toc28540984 .anchor}Rysunek 5. Procedura aktualizacji aplikacji.

PoC.
====

Dla celów demonstracyjnych utworzona zostanie grupa w regionie
europejskim w celu demonstracji procesu deploymentu, skalowania oraz
aktualizacji środowiska.

a)  Przygotowanie template dla PoC

\#\# Utworzenie maszyny z Joomla (marketplace).

> \#\# Utworzenie template z instancji Joomla
>
> gcloud compute instance-templates create mountkirk-tmpl-1 \\
>
> \--source-instance=joomla-1-vm \\
>
> \--source-instance-zone=europe-west1-d
>
> \#\# Utworzenie maszyny z WordPress (marketplace)
>
> \#\# Utworzenie template z instancji Wordpress.
>
> gcloud compute instance-templates create mountkirk-tmpl-2 \\
>
> \--source-instance=wordpress-1-vm\\
>
> \--source-instance-zone=europe-west1-b
>
> \#\# Wynik
>
> \$ gcloud compute instance-templates list
>
> NAME MACHINE\_TYPE PREEMPTIBLE CREATION\_TIMESTAMP
>
> mountkirk-tmpl-1 g1-small 2019-12-29T09:19:27.514-08:00
>
> mountkirk-tmpl-2 g1-small 2019-12-29T09:16:22.075-08:00

b)  Utworzenie MIGs regionalnego dla EU

gcloud compute instance-groups managed create mountkirk-mig-eu-west1 \\

\--template mountkirk-tmpl-1 \--base-instance-name mountkirk-eu-v1 \\

\--size 3 \--region europe-west1

![](media/image6.png){width="5.263999343832021in"
height="0.42590113735783025in"}

![](media/image7.png){width="5.33599956255468in"
height="0.5793602362204724in"}

![](media/image8.png){width="5.519087926509187in" height="2.08in"}

c)  Ustawienie autoscalowania

gcloud compute instance-groups managed set-autoscaling
mountkirk-mig-eu-west1 \\

\--cool-down-period=90 \\

\--target-cpu-utilization 0.2 \\

\--max-num-replicas 6 \\

\--region europe-west1

![](media/image9.png){width="5.623999343832021in"
height="0.4066732283464567in"}

d)  Obciążenie instancji

sudo stress \--cpu 8 \--timeout 180

![](media/image10.png){width="5.37599956255468in"
height="1.134815179352581in"}

e)  Canary update/testing , podmiana jednej instancji

> gcloud compute instance-groups managed rolling-action start-update
> mountkirk-mig-eu-west1 \\

\--version template=mountkirk-tmpl-1 \\

\--canary-version template=mountkirk-tmpl-2,target-size=30% \\

\--max-surge=3 \--max-unavailable=0 \--region europe-west1

![](media/image11.png){width="5.135999562554681in"
height="0.5655708661417322in"}

f)  Testowanie wersji.

> ![](media/image12.png){width="3.88in" height="2.6538134295713034in"}

g)  Aktualizacja pozostałych maszyn. Utworzone zostają dwie nowe a
    następnie usunięte dwie ze starym oprogramowaniem.

> gcloud compute instance-groups managed rolling-action start-update
> mountkirk-mig-eu-west1 \\

\--version template=mountkirk-tmpl-2 \\

\--max-surge=3 \--max-unavailable=0 \--region europe-west1

![](media/image13.png){width="5.03200021872266in"
height="0.8808223972003499in"}

![](media/image14.png){width="5.03200021872266in"
height="0.5807436570428697in"}

![](media/image15.png){width="4.84in" height="2.5837871828521433in"}

7.  Harmonogram prac.
    =================

8.  Szkolenia.
    ==========

9.  Testy odbiorowe.
    ================
