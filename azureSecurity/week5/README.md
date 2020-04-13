## Tydzień 5 – Praca Domowa

#### Tworzenie środowiska

##### Zbudowanie prostej struktury sieci Hub & Spoke

- Grupa zasobów 

  ```
  az group create --name rghw5 --location centralus
  ```

  

- Deployment arm template

  ```
  az group deployment create --name net01 --resource-group rghw5 \ 
  	--template-file "networking.json"
  ```

  

- 

- 
