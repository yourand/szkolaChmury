### #TYDZIEN5.1 Migracja do usług PaaS
##### Systemy analityki kosztów na projektach.
Systemy rzadko używane stosunkowo rzadko kilka razy w tygodniu, jednak w momencie uruchomienia wymagają/mogą wymagać dużo mocy obliczeniowej.  Działają na zasadzie funkcji napisanych w x++ więc stosunkowo łatwo dokonać adaptacji i umieścić jako np. web app i wdrożyć na Azure App Service.
Wdrożenie na Azure App Service pozwoli:

- Umieszczenie analityki i prezentacji w środowisku prywatnym (dane wewnętrzne).
- Skalowanie pozwoli na  sprostanie chwilowym obciążeniom.
- Bezpieczny dostęp przez integrację z VPN.

##### System rozliczenia czasu pracy i kontroli dostępu.
System zbudowany na zasadzie wielu aplikacji na serwerach linux-owych realizujące pojedyncze operacje w zakresie kontroli urządzeń oraz przetwarzania i zapisu informacji. Występują chwilowe dociążenia modułów analitycznych. System wydaje się możliwy do zaadaptowania w AKS.

- Struktura systemu pozwala na łatwą adaptację do serwisów umieszczonych w kontenerach zarządzanych przez K8S.
- Moduły aplikacji korzystają z zewnętrznej bazy danych poza tym są bezstanowe.
- Chwilowe obciążenia wynikające z zadań analityczne mogą zostać wyskalowane przez mechanizmy k8s.
- Autohealing podów pozwoli na automatyzację restartów modułów, które potrafią się zawiesić.

### #TYDZIEN5.2 Czego nie migrować do PaaS?
- Aplikacje desktopowe, eksploatujące silnie bazę danych, opóźnienia mogą uniemożliwić pracę. W takich systemach byłaby konieczność uruchomienia rozwiązań terminalowych i kolejne problemy z kompatybilnością aplikacji.
- Związane mocno z systemem operacyjnym lub wymagające przestarzałych platform serwerowych do pracy.
- Systemy, których licencjonowanie może spowodować znaczący wzrost kosztów lub spowodować ryzyko prawne.



### #TYDZIEN5.3 Service Fabric – dlaczego jest tak dobry?

#### Lab Service Fabric

##### Uruchomienie
**Service Fabric Explorer**
![Alt text](https://github.com/yourand/szkolaChmury/blob/master/azureArchitect/week5/img/service-fabric-explorer.PNG)

**Voting App**
![Alt text](https://github.com/yourand/szkolaChmury/blob/master/azureArchitect/week5/img/voting-app-1.PNG)

##### Upgrade
**Aktualizacja**
![Alt text](https://github.com/yourand/szkolaChmury/blob/master/azureArchitect/week5/img/voting-app-upgrade.PNG)






*jestem pod wrażeniem jakości onpremisowej wersji :)*

