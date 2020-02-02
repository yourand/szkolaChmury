### Zadnie Tydzień 4

*Opis sytuacji:*
*Jesteś architektem w dużej firmie (20 000 pracowników, ponad 5000 maszyn wirtualnych, ponad 200 maszyn fizycznych pod środowiskiem wirtualnym), która podjęła decyzję o migracji do Chmury. W firmie jest dość znaczna ilość systemów „legacy” i pierwszy kroki migracji to będzie migracja typu Lift&Shift, gdzie głównie wykorzystasz środowiska maszyn wirtualnych, ale postarasz się zautomatyzować cały proces powoływania i zarządzania środowiskami.*

####	#TYDZIEN4.1 

*Na bazie swoich doświadczeń i podstawowej wiedzy o maszynach wirtualnych oraz Scale Set zaproponuj kilka kroków, które byś zrealizował w ramach takiej migracji. Przy każdym kroku napisz maksymalnie 2-3 zdania, jakie czynności w takim kroku byś zrealizował.*
*Pamiętaj o ograniczeniach w chmurze, limitach oraz ograniczeniach co do tego, jakie parametry wirtualnych maszyn, możesz uzyskać w Azure.*

1. Audyt/inwentaryzacja.

   Szczegółowa inwentaryzacja zasobów, które mają podlegać migracji. Inwentaryzacja powinna obejmować:

   - Sprzęt,
   - systemy operacyjne,
   - aplikacje,
   - wymagania mocy obliczeniowej.

2. Mapowanie zależności wewnętrznych i zewnętrznych.

   Mapowanie zależności miedzy migrowanymi zasobami. W tym kroku określimy, które systemy współpracują między sobą lub korzystają ze wspólnych zasobów (migrowanych lub nie).

3. Mapowanie zasobów.

   Mapowanie fizycznych rozwiązań do usług w chmurze. W naszym przypadku mapujemy używane maszyny na ich odpowiedniki. Tutaj również oceniamy czy wymagana moc obliczeniowa pokrywa się z wymogami (aby uniknąć sytuacji znanej z onprem, że wykorzystywane urządzenia mają duży zapas mocy nigdy nie wykorzystywanej). Na tym etapie można również zaproponować wykorzystanie rozwiązań, które zapewnią odpowiednie skalowanie grup maszyn co może pozwolić na optymalizację kosztową.

   Określamy sposób zarządzania tożsamością, monitorowania, zapewnienia dostępności oraz backupów.

4.  Weryfikacja licencji.

   Zbadanie posiadanych licencji pod względem ich warunków. Należy tutaj ocenić czy posiadane licencje umożliwiają pracę w chmurze publicznej oraz czy w związku ze zmianą platformy licencje pokryją zasoby. Może okazać się, że konieczny jest zakup lub dzierżawa dodatkowych licencji lub też migracja pewnych komponentów może być nieopłacalna.

5.  Planowanie sieci. Połączeń z on-premis.

   Zaplanowanie struktury sieci, adresacji, dodatkowych urządzeń, która zapewni wymagania migrowanemu systemowi. Planujemy tutaj również sposób połączenia z naszą infrastrukturą on-premis lub/i infrastrukturą w chmurze.

6. Governance.

   Planowanie systemu polityk, uprawnień, podziału na subskrypcje, zarządzanie.

7.  Planowanie migracji.

   W oparciu o zebrane informacje planujemy kroki migracji w szczególności biorąc pod uwagę zależności między systemami i co za tym idzie budujemy hierarchię potrzebnych zasobów.

   Weryfikacja limitów ograniczających dostępne zasoby oraz dostosowanie się do nich lub ich zmiana.

8. Przygotowanie skryptów i planów szczegółowych.

9. Migracja.

10. Optymalizacja techniczna i kosztowa.

#### #TYDZIEN4.2 Virtual Machine Scale Set
*VMSS nie są często używane w projektach. W ramach zadania nr. 2 napisz mi proszę do jakich warstw aplikacji użyłbyś Scale Set a następnie spróbuj za pomocą Azure CLI zbudować swój prosty Scale Set.*

1. ENV

   ```
   az group create --name labweek4 --location westeurope
   myResourceGroup=labweek4
   myScaleSet=rkVMSS
   myPassword="$(openssl rand -base64 24)"
   myVirtualNetwork=vnet
   mySubnet=subnet01
   ```

   

2. Sieć

   ```
   az network vnet create \
     --name $myVirtualNetwork \
     --resource-group $myResourceGroup \
     --subnet-name $mySubnet
   
   az network nsg create \
       -g $myResourceGroup \
       -n allow-http-s
   
   az network nsg rule create \
     --name Allow_HTTP_HTTPS \
     --nsg-name allow-http-s \
     --priority 101 \
     --resource-group $myResourceGroup \
     --access Allow \
     --protocol Tcp \
     --direction Inbound \
     --destination-port-ranges 80 443
   
   az network vnet subnet update \
       -g $myResourceGroup \
       -n $mySubnet \
       --vnet-name $myVirtualNetwork \
       --network-security-group allow-http-s
   ```

   

3. VMSS

   ```
   az vmss create \
     --resource-group $myResourceGroup \
     --name $myScaleSet \
     --image UbuntuLTS \
     --vm-sku Standard_B1ms \
     --custom-data init.yml \
     --instance-count 2 \
     --vnet-name $myVirtualNetwork \
     --subnet $mySubnet \
     --upgrade-policy-mode automatic \
     --admin-username rkadmin \
     --admin-password $myPassword
   ```

   

4. LB rule

   ```
   az network lb rule create \
     --resource-group $myResourceGroup \
     --name LoadBalancerRuleWeb \
     --lb-name rkVMSSLB \
     --backend-pool-name rkVMSSLBBEPool \
     --backend-port 80 \
     --frontend-ip-name loadBalancerFrontEnd \
     --frontend-port 80 \
     --protocol tcp
   ```

   

5. Test

   ```
   curl 40.68.201.83
   
   <!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">
   <html xmlns="http://www.w3.org/1999/xhtml">
     <!--
       Modified from the Debian original for Ubuntu
   /----------------/
   ```
