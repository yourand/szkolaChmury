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
