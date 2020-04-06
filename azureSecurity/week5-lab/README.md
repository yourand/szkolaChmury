## Labolatorium – IP Forwarding z Mikrotik

1. Tworzenie grupy zasobów.

   ```
   az group create --name scSecRk01 --location centralus
   ```

   

2. Przygotowanie obrazu dysku Mikrotik.

   ```powershell
   Convert-VHD -Path chr-6.46.4.vhdx -DestinationPath chr-6.46.4.vhd -VHDType Fixed
   ```

   

3. Tworzenie konta storage obrazu dysku.

   ```
   az storage account create \
       --name vhdrk01\
       --resource-group scSecRk01 \
       --location centralus \
       --sku Standard_LRS \
       --kind StorageV2
   ```

   ```
   az storage container create \
   	--name vhd-mikrotik \
   	--account-name vhdrk01
   ```

   

4. Przeniesienie vhd do kontenera.

   ```
   az storage blob upload \
       --container-name vhd-mikrotik \
       --name chr-6.46.4.vhd \
       --file chr-6.46.4.vhd \
       --account-name vhdrk01
   ```

   

5.  Tworzenie sieci.

   ```
   az network vnet create \
   	--address-prefixes 10.0.0.0/16 \
   	--name vnet01-mng \
   	--resource-group scSecRk01 \
   	--subnet-name frontend-subnet \
   	--subnet-prefixes 10.0.0.0/24
   ```

   ```
   az network vnet subnet create \
   	--name backend-subnet \
   	--vnet-name vnet01-mng \
   	--resource-group scSecRk01 \
   	--address-prefixes "10.0.1.0/24"
   ```

   ```
   az network vnet subnet create \
   	--name solution-subnet \
   	--vnet-name vnet01-mng \
   	--resource-group scSecRk01 \
   	--address-prefixes "10.0.2.0/24"
   ```

   

6. Utworzenie maszyny z Mikrotik na podstawie skryptu.  

![](https://github.com/yourand/szkolaChmury/blob/master/azureSecurity/week5-lab/img/1-mik-create.jpg)
   

7. Wyklinanie z portalu vm Windows 10 w subnet backend-subnet.

8. Dostanie się do vm Mikrotik.

   ```
   próba 1 ...
   próba 2 ...
   próba 3 ...
   próba 4 ...
   próba 5 ...
   próba 6 ...
   ...
   ...
   próba x
   Jest !!!
   ```

   

9. Wyklinanie z portalu vm ubuntu w subnetsolution-subnet.

10. Utworzenie UDR.

   ```
   az network route-table create \
   	--name forward2mikrotic \
   	--resource-group scSecRk01 \
   	--location centralus
   ```

   ```
   az network route-table route create \
   	--resource-group scSecRk01 \
   	--route-table-name forward2mikrotic \
   	--name INTERNET \
       --next-hop-type VirtualAppliance \
   	--address-prefix 0.0.0.0/0 \
   	--next-hop-ip-address 10.0.1.4
   ```

   ```
   az network vnet subnet update \
   	--resource-group scSecRk01 \
   	--name solution-subnet \
   	--vnet-name vnet01-mng \
   	--route-table INTERNET
   ```

   

11. Konfiguracja Mikrotika.  

   Konfiguracja z winbox:
   IP kart sieciowych  
   Routing  
   Nat  
      
12. Topologia.  

![](https://github.com/yourand/szkolaChmury/blob/master/azureSecurity/week5-lab/img/2-topol.jpg)
    

13. Testy.

![](https://github.com/yourand/szkolaChmury/blob/master/azureSecurity/week5-lab/img/5-lynx-mtr.jpg)





*tworzenie UDR z cmd nie przetestowane*
