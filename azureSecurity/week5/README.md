## Tydzień 5 – Praca Domowa

#### Tworzenie środowiska

##### Zbudowanie prostej struktury sieci Hub & Spoke

- Adresacja

  | Vnet         | Subnet         | Adresacja   |
  | ------------ | -------------- | ----------- |
  | vnet-hub01   |                | 10.0.0.0/16 |
  |              | sub-gateway01  | 10.0.0.0/24 |
  |              | sub-firewall01 | 10.0.1.0/24 |
  |              | sub-shared01   | 10.0.2.0/24 |
  |              | sub-mgm01      | 10.0.3.0/24 |
  | vnet-spoke01 |                | 10.1.0.0/16 |
  |              | sub-app01      | 10.1.0.0/24 |
  |              | sub-db01       | 10.1.1.0/24 |

- Grupa zasobów 

  ```
  az group create --name rghw5 --location centralus
  ```

  

- Deployment arm template

  ```
  az group deployment create --name net01 --resource-group rghw5 \ 
  	--template-file "networking.json"
  ```

- Topologia sieci
  ![](https://github.com/yourand/szkolaChmury/blob/master/azureSecurity/week5/img/1-topologia-hub-spoke.JPG)

- 

##### Instalacja serwera aplikacyjnego i bazy danych

- Instalacja serwerów i aplikacji
- Topologia
  ![](https://github.com/yourand/szkolaChmury/blob/master/azureSecurity/week5/img/2-topologia-net-serwery.JPG)

#### Zadania

##### 1. Test peeringu oraz domyślnego routingu

  ![](https://github.com/yourand/szkolaChmury/blob/master/azureSecurity/week5/img/3-test-peering.JPG)

##### 2. Przygotowanie odpowiednich reguł NSG

a) Możliwy ruch z maszyny z serwerem aplikacyjnym po porcie baz danych do serwera bazodanowego a z sieci Hub oraz sieci Spoke tylko ruch po portach zarządczych (np. 22 dla SSH czy 3389 dla Windows). Ruch z innych sieci czy Internetu po tych portach nie był możliwy.  

Widok NSG  
  ![](https://github.com/yourand/szkolaChmury/blob/master/azureSecurity/week5/img/6-nsg-view.JPG)  
Weryfikacja dostępu do MySQL z sieci Hub oraz z sieci Spoke  
  ![](https://github.com/yourand/szkolaChmury/blob/master/azureSecurity/week5/img/4-allowMysqlAsg.JPG)  
Weryfikacja dostepu SSH z internetu do serwera MySQL  
  ![](https://github.com/yourand/szkolaChmury/blob/master/azureSecurity/week5/img/5-deny-ssh-internet.JPG)  
  
b) Wykorzystując Service Tag zapewnij, że osiągniesz następujące konfiguracje:

- Ruch wychodzący z maszyny bazodanowej do Internetu nie będzie możliwy, ruch do adresów Azure w ramach całego Azure nie będzie możliwy

  ![](https://github.com/yourand/szkolaChmury/blob/master/azureSecurity/week5/img/7-allow-azure.JPG)

- Ruch wychodzący z maszyny serwera aplikacyjnego do Internetu nie będzie możliwy, ruch do adresów Azure w ramach całego Azure będzie możliwy

  ![](https://github.com/yourand/szkolaChmury/blob/master/azureSecurity/week5/img/8-deny-internet.JPG)

- Ruch wychodzący z maszyny zarządzającej do adresów Internetowych będzie możliwy, ruch do adresów Azure nie będzie możliwy

  ![](https://github.com/yourand/szkolaChmury/blob/master/azureSecurity/week5/img/9-deny-azure.JPG)

##### 3. Włącz Network Watcher i zbierz dane

  ![](https://github.com/yourand/szkolaChmury/blob/master/azureSecurity/week5/img/10-traffic.JPG)  
  
  ![](https://github.com/yourand/szkolaChmury/blob/master/azureSecurity/week5/img/11-traffic-vm.JPG)


Po kilku dniach dniach zbierania danych o trafficu w kontekście security:

- Nie należy zostawiać vm z publicznym IP, klasy adresowa skanowane są automatycznie w poszukiwaniu podatności, głownie z kierunku Chin.
- Ruch do Internetu z vnet-tów jest swobodny nie nadzorowany, warto przekierować ruch przez jeden punkt, który będzie nadzorowany.

  ![](https://github.com/yourand/szkolaChmury/blob/master/azureSecurity/week5/img/12-traffic-trend-malic.JPG)


##### 4. Projekt Twojej sieci w Azure
  ![](https://github.com/yourand/szkolaChmury/blob/master/azureSecurity/week5/img/hub02.jpg)  
  
