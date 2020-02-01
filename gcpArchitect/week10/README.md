### Tydzień 10 zadanie

#### Zadanie 1.

*Bezpieczna komunikacja pomiędzy platformą Google Cloud Platform (GCP) a innymi chmurami lub systemami w lokalu jest powszechną, krytyczną potrzebą. GCP ułatwia tworzenie bezpiecznych sieci prywatnych (VPN) oraz jeżeli pojedynczy tunel nie zapewnia koniecznej przepustowości, GCP może płynnie rozłożyć ruch w wielu tunelach, aby zapewnić dodatkową przepustowość*

1. Generowanie klucza do VPN

   ```kluczVpn="$(openssl rand -base64 24)"```

   

2. Utworzenie VPC.

   **cloud**

   ```
   gcloud compute networks create cloud \
          --subnet-mode=custom
          
   gcloud compute firewall-rules create fwcloud01 \
          --network cloud \
          --allow tcp,udp,icmp \
          --source-ranges 0.0.0.0/0
   gcloud compute firewall-rules create fwcloud02 \
          --network cloud \
          --allow tcp:22,tcp:3389,icmp
   ```

   **onpremis**

   ```
   gcloud compute networks create onprem \
          --subnet-mode=custom    
   
   gcloud compute firewall-rules create fwonprem01 \
          --network onprem \
          --allow tcp,udp,icmp \
          --source-ranges 0.0.0.0/0
   gcloud compute firewall-rules create fwonprem2 \
          --network onprem \
          --allow tcp:22,tcp:3389,icmp
   ```

3. Tworzenie Subnets

   ```
   gcloud compute networks subnets create subnet01 \
       --network=cloud --range 10.0.0.0/16 \
       --region europe-west3
   
   gcloud compute networks subnets create subnet02 \
       --network=onprem --range 192.168.1.0/24 \
       --region europe-west3
   ```

   

4. Tworzenie Gateways

   ```
   gcloud compute vpn-gateways create gw-cloud01 --network cloud --region europe-west3
   Creating VPN Gateway...done.
   NAME        INTERFACE0    INTERFACE1    NETWORK  REGION
   gw-cloud01  35.242.18.66  35.220.19.80  cloud    europe-west3
   
   gcloud compute vpn-gateways create gw-on-prem01 --network onprem --region europe-west3
   Creating VPN Gateway...done.
   NAME          INTERFACE0     INTERFACE1      NETWORK  REGION
   gw-on-prem01  35.242.89.254  35.220.107.166  onprem  europe-west3
   ```

   

5. Tworzenie Routers

   ```
   gcloud compute routers create router-cloud01 \
   	--region europe-west3 \
   	--network cloud \
   	--asn 65001
   
   gcloud compute routers create router-on-prem01 \
   	--region europe-west3 \
   	--network onprem \
   	--asn 65002
   ```

   

6. Tworzenie VPN tunnels

   ```
   gcloud compute vpn-tunnels create tunnel-gw-cloud01-if0 \
       --peer-gcp-gateway gw-on-prem01 \
       --region europe-west3 \
       --ike-version 2 \
       --shared-secret $kluczVpn \
       --router router-cloud01 \
       --vpn-gateway gw-cloud01 \
       --interface 0
   
     gcloud compute vpn-tunnels create tunnel-gw-cloud01-if1 \
       --peer-gcp-gateway gw-on-prem01 \
       --region europe-west3 \
       --ike-version 2 \
       --shared-secret $kluczVpn \
       --router router-cloud01 \
       --vpn-gateway gw-cloud01 \
       --interface 1
   
   gcloud compute vpn-tunnels create tunnel-gw-onprem01-if0 \
      --peer-gcp-gateway gw-cloud01 \
      --region europe-west3 \
      --ike-version 2 \
      --shared-secret $kluczVpn \
      --router router-on-prem01 \
      --vpn-gateway gw-on-prem01 \
      --interface 0
   
   gcloud compute vpn-tunnels create tunnel-gw-onprem01-if1 \
      --peer-gcp-gateway gw-cloud01 \
      --region europe-west3 \
      --ike-version 2 \
      --shared-secret $kluczVpn \
      --router router-on-prem01 \
      --vpn-gateway gw-on-prem01 \
      --interface 1
   ```

   

7. Tworzenie Cloud Router interfaces i BGP peers

   ```
   gcloud compute routers  add-interface router-cloud01 \
      --interface-name interface-gw-cloud01-if0  \
      --ip-address 169.254.0.1 \
      --mask-length 30 \
      --vpn-tunnel tunnel-gw-cloud01-if0 \
      --region europe-west3
   
   gcloud compute routers add-bgp-peer router-cloud01 \
      --peer-name peer-tunnel-name-gw1-if0 \
      --interface interface-gw-cloud01-if0 \
      --peer-ip-address 169.254.0.2 \
      --peer-asn 65002 \
      --region europe-west3
   
   gcloud compute routers add-interface router-cloud01 \
      --interface-name interface-gw-cloud01-if1  \
      --ip-address 169.254.1.1 \
      --mask-length 30 \
      --vpn-tunnel tunnel-gw-cloud01-if1 \
      --region europe-west3
   
   gcloud compute routers add-bgp-peer router-cloud01 \
      --peer-name peer-tunnel-name-gw1-if1 \
      --interface interface-gw-cloud01-if1 \
      --peer-ip-address 169.254.1.2 \
      --peer-asn 65002 \
      --region europe-west3
   
   gcloud compute routers describe router-cloud01  \
       --region europe-west3
   
   gcloud compute routers add-interface router-on-prem01 \
      --interface-name interface-tunnel-gw-onprem01-if0 \
      --ip-address 169.254.0.2 \
      --mask-length 30 \
      --vpn-tunnel tunnel-gw-onprem01-if0 \
      --region europe-west3
   
    gcloud compute routers add-bgp-peer router-on-prem01 \
      --peer-name peer-tunnel-gw-onprem01-if0 \
      --interface interface-tunnel-gw-onprem01-if0 \
      --peer-ip-address 169.254.0.1 \
      --peer-asn 65001 \
      --region europe-west3
   
   gcloud compute routers add-interface router-on-prem01 \
      --interface-name interface-tunnel-gw-onprem01-if1 \
      --ip-address 169.254.1.2 \
      --mask-length 30 \
      --vpn-tunnel tunnel-gw-onprem01-if1 \
      --region europe-west3
   
   
    gcloud compute routers add-bgp-peer router-on-prem01 \
      --peer-name peer-tunnel-gw-onprem01-if1 \
      --interface interface-tunnel-gw-onprem01-if1 \
      --peer-ip-address 169.254.1.1 \
      --peer-asn 65001 \
      --region europe-west3
   
   gcloud compute routers describe router-on-prem01   \
       --region europe-west3
   ```

   

8. Weryfikacja działania tuneli

   ```
   gcloud compute vpn-tunnels describe tunnel-gw-onprem01-if0  \
       --region europe-west3 \
       --format='flattened(status,detailedStatus)'
   detailedStatus: Tunnel is up and running.
   status:         ESTABLISHED
   
   gcloud compute vpn-tunnels describe tunnel-gw-onprem01-if1  \
       --region europe-west3 \
       --format='flattened(status,detailedStatus)'
   detailedStatus: Tunnel is up and running.
   status:         ESTABLISHED
   
   gcloud compute vpn-tunnels describe tunnel-gw-cloud01-if0  \
       --region europe-west3 \
       --format='flattened(status,detailedStatus)'
   detailedStatus: Tunnel is up and running.
   status:         ESTABLISHED
   $  gcloud compute vpn-tunnels describe tunnel-gw-cloud01-if1  \
       --region europe-west3 \
       --format='flattened(status,detailedStatus)'
   detailedStatus: Tunnel is up and running.
   status:         ESTABLISHED
   gcloud compute vpn-gateways get-status gw-on-prem01 \
          --region europe-west3
   result:
     vpnConnections:
     - peerGcpGateway: https://www.googleapis.com/compute/v1/projects/szkola-chmury-gcpa/regions/europe-west3/vpnGateways/gw-cloud01
       state:
         state: CONNECTION_REDUNDANCY_MET
       tunnels:
       - localGatewayInterface: 0
         peerGatewayInterface: 0
         tunnelUrl: https://www.googleapis.com/compute/v1/projects/szkola-chmury-gcpa/regions/europe-west3/vpnTunnels/tunnel-gw-onprem01-if0
       - localGatewayInterface: 1
         peerGatewayInterface: 1
         tunnelUrl: https://www.googleapis.com/compute/v1/projects/szkola-chmury-gcpa/regions/europe-west3/vpnTunnels/tunnel-gw-onprem01-if1
   
   ```

   

9. Testy

   **Utworzenie VM**

   ```
   gcloud compute instances create vm01-cloud \
           --zone=europe-west3-a --machine-type=f1-micro \
           --preemptible \
           --subnet=subnet01 \
           --image=debian-9-stretch-v20191210 \
           --image-project=debian-cloud
   
   gcloud compute instances create vm01-onprem \
           --zone=europe-west3-a --machine-type=f1-micro \
           --preemptible \
           --subnet=subnet02 \
           --image=debian-9-stretch-v20191210 \
           --image-project=debian-cloud
   ```

   **Weryfikacja połączenia**

   ```
   admin_gcp@vm01-cloud:~$ ping -c 1 192.168.1.2
   PING 192.168.1.2 (192.168.1.2) 56(84) bytes of data.
   64 bytes from 192.168.1.2: icmp_seq=1 ttl=62 time=3.82 ms
   
   --- 192.168.1.2 ping statistics ---
   1 packets transmitted, 1 received, 0% packet loss, time 0ms
   rtt min/avg/max/mdev = 3.828/3.828/3.828/0.000 ms
   
   admin_gcp@vm01-onprem:~$ ping -c 1 10.0.0.2
   PING 10.0.0.2 (10.0.0.2) 56(84) bytes of data.
   64 bytes from 10.0.0.2: icmp_seq=1 ttl=62 time=3.99 ms
   
   --- 10.0.0.2 ping statistics ---
   1 packets transmitted, 1 received, 0% packet loss, time 0ms
   rtt min/avg/max/mdev = 3.994/3.994/3.994/0.000 ms
   ```

   **Instalacja IPREF**

   ```
   sudo apt install iperf
   ```

   **Testy wydajności**

   ```
   iperf -c 192.168.1.2 -t 20 -i 2
   ------------------------------------------------------------
   Client connecting to 192.168.1.2, TCP port 5001
   TCP window size: 45.0 KByte (default)
   ------------------------------------------------------------
   [  3] local 10.0.0.2 port 33024 connected with 192.168.1.2 port 5001
   [ ID] Interval       Transfer     Bandwidth
   [  3]  0.0- 2.0 sec   228 MBytes   954 Mbits/sec
   [  3]  2.0- 4.0 sec   236 MBytes   988 Mbits/sec
   [  3]  4.0- 6.0 sec   237 MBytes   994 Mbits/sec
   [  3]  6.0- 8.0 sec   236 MBytes   989 Mbits/sec
   [  3]  8.0-10.0 sec   236 MBytes   988 Mbits/sec
   [  3] 10.0-12.0 sec   236 MBytes   989 Mbits/sec
   [  3] 12.0-14.0 sec   235 MBytes   987 Mbits/sec
   [  3] 14.0-16.0 sec   236 MBytes   991 Mbits/sec
   [  3] 16.0-18.0 sec   234 MBytes   980 Mbits/sec
   [  3] 18.0-20.0 sec   227 MBytes   951 Mbits/sec
   [  3]  0.0-20.0 sec  2.28 GBytes   981 Mbits/sec
   
   iperf -c 10.0.0.2 -t 20 -i 2
   ------------------------------------------------------------
   Client connecting to 10.0.0.2, TCP port 5001
   TCP window size: 45.0 KByte (default)
   ------------------------------------------------------------
   [  3] local 192.168.1.2 port 43438 connected with 10.0.0.2 port 5001
   [ ID] Interval       Transfer     Bandwidth
   [  3]  0.0- 2.0 sec   228 MBytes   958 Mbits/sec
   [  3]  2.0- 4.0 sec   236 MBytes   990 Mbits/sec
   [  3]  4.0- 6.0 sec   236 MBytes   990 Mbits/sec
   [  3]  6.0- 8.0 sec   235 MBytes   985 Mbits/sec
   [  3]  8.0-10.0 sec   232 MBytes   973 Mbits/sec
   [  3] 10.0-12.0 sec   229 MBytes   962 Mbits/sec
   [  3] 12.0-14.0 sec   235 MBytes   986 Mbits/sec
   [  3] 14.0-16.0 sec   236 MBytes   992 Mbits/sec
   [  3] 16.0-18.0 sec   235 MBytes   986 Mbits/sec
   [  3] 18.0-20.0 sec   237 MBytes   994 Mbits/sec
   [  3]  0.0-20.0 sec  2.29 GBytes   981 Mbits/sec
   ```

#### Zadanie 2.

*Twoim drugim zadaniem będzie odpowiednie zbudowanie połączenia do aplikacji znajdującej się on-premises. Poniżej znajdziesz główne wymagania, które musisz spełnić*

*Przez aplikacje będzie przepływać około 70 GB danych dziennie z dużym natężeniem przez co jakakolwiek przerwa połączenia nie jest akceptowalna.*

*Dopasuj odpowiednie rozwiązanie, aby połączenie pomiędzy środowiskiem w Google Cloud, a środowiskiem lokalnym było dodatkowo szyfrowane oraz zawierało odpowiednie przepustowości, aby spełnić dzienny limit.*

*Jaki komponent dodałbyś do schematu ,aby przełączanie pomiędzy źródłami przesyłu danych było w pełni automatyczne podczas awarii?*

### Uzupelnic
