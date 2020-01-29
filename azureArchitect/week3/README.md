### Zadanie tydzień 3

#### Jesteś architektem w firmie o europejskim zasięgu i rozpoczynasz w swojej firmie budowanie rozwiązań opartych o Microsoft Azure.

Jako architekt ustaliłeś kilka pryncypiów projektowych, które powinny być respektowane przy każdym wdrożeniu:

- Każdy projekt powinien używać konwencji nazw dla grup i zasobów zgodnych z konwencją firmy.
- Infrastruktura, budowana w Azure pod środowiska czy aplikacje powinna być zawsze budowana z wykorzystaniem Azure Resource Managera i template’ów. Jeśli jest to duże wdrożenie, powinny być użyte tzw. Linked Templates.
- Jeśli to konieczne, należy zbudować własny model ról za pomocą RBAC.
- Docelowo, wszystkie kluczowe ustawienia, tak jak np. nazwy lokalnych administratorów i hasła powinny być pobierane z Azure KeyVault

### Zadania

#### TYDZIEN3.1

*Zbuduj prostą konwencję nazewniczą dla min. takich zasobów jak Grupa Zasobów, VNET, Maszyn Wirtualna, Dysk, Konta składowania danych. Pamiętaj o ograniczeniach w nazywaniu zasobów, które występują w Azure*

| Zasób           | Szablon                                    |
| --------------- | ------------------------------------------ |
| Resource group  | ```rg-<region>-<srodowisko>-<licznik>```         |
| VNET            | ```vnet-<region>-<srodowisko>-<licznik>```       |
| VM              | ```vm-<region>-<srodowisko>-<os>-<licznik>```    |
| Disk            | ```disk-<typ>-<nazwa vm>-<licznik>```            |
| Storage Account | ```st-<region>-<typ><przeznaczenie>-<licznik>``` |

#### TYDZIEN3.2

*Zbuduj prosty ARM Template (możesz wykorzystać już gotowe wzorce z GitHub), który wykorzystuje koncepcję Linked Templates. Template powinien zbudować środowisko złożone z jednej sieci VNET, podzielonej na dwa subnety. W każdy subnecie powinna powstać najprostsza maszyna wirtualna z systemem Ubuntu 18.04 a na każdym subnecie powinny zostać skonfigurowane NSG.*

**Realizacja:**

1. Przygotowano dwa template:

   a) [1-main.json](1-main.json) - tempalate główny tworzący podstawowe elementy infrastruktury oraz wywołujący *linked template*, który tworzy  *Network Security Group*,

   b) [1-linked-nsg.json](1-linked-nsg.json)- podlinkowany template tworzący *Network Security Group*.

   | Zasób                | Nazwa        | Template |
   | -------------------- | ------------ | -------- |
   | networkSecurityGroup | nsgLab03     | linked   |
   | virtualNetwork       | vnetLab03    | main     |
   | subnet               | Subnet1lab03 | main     |
   | subnet               | Subnet2lab03 | main     |
   | publicIPAddresses    | vm01PublicIP | main     |
   | publicIPAddresses    | vm02PublicIP | main     |
   | networkInterface     | vm01NetInt   | main     |
   | networkInterface     | vm02NetInt   | main     |
   | virtualMachine       | vm01         | main     |
   | virtualMachine       | vm02         | main     |

2. Przygotowano plik z parametrami 1-main.parameters.json

3. Utworzono grupę zasobów na potrzeby PoC.

   ```
   az group create --name scAzureArch --location westeurope
   ```

   

4. Przygotowano Storage Account i kontenera w celu przechowania linkowanego template.

   ```
   az storage account create \
       --name armstorerk01\
       --resource-group scAzureArch \
       --location westeurope \
       --sku Standard_LRS \
       --kind StorageV2
   
   az storage container create --name arm-template --account-name armstorerk01
   
   az storage blob upload \
       --container-name arm-template \
       --name 1-linked-nsg.json \
       --file 1-linked-nsg.json \
       --account-name armstorerk01
   ```

   

5. Utworzono SAS token do kontenera i umieszczono w głównym template

   ```
   expiretime=$(date -u -d '120 minutes' +%Y-%m-%dT%H:%MZ)
   
   connection=$(az storage account show-connection-string \
     --resource-group scAzureArch \
     --name armstorerk01 \
     --query connectionString)
     
   token=$(az storage container generate-sas \
     --name arm-template \
     --expiry $expiretime \
     --permissions r \
     --output tsv \
     --connection-string $connection)
     
   url=$(az storage blob url \
     --container-name arm-template \
     --name 1-linked-nsg.json \
     --output tsv \
     --connection-string $connection)
   ```

   

6. Wykonano deployment template

   ```
   az group deployment create --name siec6 --resource-group scAzureArch \ 
   	--template-file "1-main.json" --parameters 1-main.parameters.json.json
   ```

7. Wynik

   ```
   az resource list --resource-group scAzureArch -o table
   Name                                            ResourceGroup    Location    Type                                     Status
   ----------------------------------------------  ---------------  ----------  ---------------------------------------  --------
   vm01_OsDisk_1_ac8aa75c88bd48539f5899450bc17b4a  SCAZUREARCH      westeurope  Microsoft.Compute/disks
   vm02_disk1_8060479078114ab5beea6dbe83aac903     SCAZUREARCH      westeurope  Microsoft.Compute/disks
   vm01                                            scAzureArch      westeurope  Microsoft.Compute/virtualMachines
   vm02                                            scAzureArch      westeurope    Microsoft.KeyVault/vaults
   vm01NetInt                                      scAzureArch      westeurope  Microsoft.Network/networkInterfaces
   vm02NetInt                                      scAzureArch      westeurope  Microsoft.Network/networkInterfaces
   nsgLab03                                        scAzureArch      westeurope  Microsoft.Network/networkSecurityGroups     
   vm01PublicIP                                    scAzureArch      westeurope  Microsoft.Network/publicIPAddresses
   vm02PublicIP                                    scAzureArch      westeurope  Microsoft.Network/publicIPAddresses
   vnetLab03                                       scAzureArch      westeurope  Microsoft.Network/virtualNetworks
   armstorerk01                                    scAzureArch      westeurope  Microsoft.Storage/storageAccounts
   ```

   

#### TYDZIEN3.3

*„Zbuduj najprostrzą właśną rolę RBAC, która pozwala użytkownikowi uruchomić maszynę, zatrzymać ją i zgłosić zgłoszenie do supportu przez Portal Azure”*

```
{
	"Name": "VM starter",
    "Id": null,
    "IsCustom": true,
    "Description": "Wlacz/wylacz vm, napisz do supportu",
    "Actions": [
      "Microsoft.Compute/virtualMachines/start/action",
      "Microsoft.Compute/virtualMachines/powerOff/action",
      "Microsoft.Support/supportTickets/write"
    ],
    "NotActions": [],
    "AssignableScopes": [
      "/subscriptions/<sub_id>"
    ]
  }
```



#### TYDZIEN3.4

*Spróbuj na koniec zmodyfikować template tak, by nazwa użytkownika i hasło do każdej maszyny z pkt. 2 było pobierane z KeyVault.*

**Realizacja:**

1. Zgodnie z wymogiem tworzenia zasobów przez arm template przygotowano przygotowano [2-keyvault.json](2-keyvault.json) oraz [2-keyvault.parameters.json](2-keyvault.parameters.json). Przygotowane template tworzą *Key Vault* oraz secrety nazwa użytkownika oraz hasło.

   ```
   az group deployment create --name siec7 \
   	--resource-group scAzureArch \
   	--template-file 2-keyvault.json \
   	--parameters 2-keyvault.parameters.json
   ```
   
2. Dokonano modyfikacji pliku z parametrami template main 2-main.parameters.json przekierowując wskazanie na nazwę użytkownika i hasło do Key Vault.

   

3. Wykonano deployment template

   ```
   az group deployment create --name siec8 --resource-group scAzureArch --template-file 1-main.json --parameters 2-main.parameters.json
   ```

4. Wynik.

   ```
   az resource list --resource-group scAzureArch -o table
   Name                                         ResourceGroup    Location    Type                                     Status-------------------------------------------  ---------------  ----------  ---------------------------------------  --------
   vm01_disk1_6e4c30ab93fc4a6bbe9020faf1139096  SCAZUREARCH      westeurope  Microsoft.Compute/disks
   vm02_disk1_b2efbf4599534e319c1c645f741299be  SCAZUREARCH      westeurope  Microsoft.Compute/disks
   vm01                                         scAzureArch      westeurope  Microsoft.Compute/virtualMachines
   vm02                                         scAzureArch      westeurope  Microsoft.Compute/virtualMachines
   rk01-key-vault                               scAzureArch      westeurope  Microsoft.KeyVault/vaults
   vm01NetInt                                   scAzureArch      westeurope  Microsoft.Network/networkInterfaces
   vm02NetInt                                   scAzureArch      westeurope  Microsoft.Network/networkInterfaces
   nsgLab03                                     scAzureArch      westeurope  Microsoft.Network/networkSecurityGroups        
   vm01PublicIP                                 scAzureArch      westeurope  Microsoft.Network/publicIPAddresses
   vm02PublicIP                                 scAzureArch      westeurope  Microsoft.Network/publicIPAddresses
   vnetLab03                                    scAzureArch      westeurope  Microsoft.Network/virtualNetworks
   armstorerk01                                 scAzureArch      westeurope  Microsoft.Storage/storageAccounts
   ```

5. W ramach testu zalogowano się do maszyn podanymi credentialsami.
