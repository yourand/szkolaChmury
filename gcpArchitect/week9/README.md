## Tydzień 9



#### Zadanie 1.

##### 1. Utwórz dwa osobne projekty, aby móc wytworzyć w nich dwa osobne środowiska sieciowe.

Preq:   

```
gcloud organizations list
gcloud alpha resource-manager folders list --organization=1069146298931
```

Create:

```
gcloud projects create szkola-chmury-pr01 --folder=813244329151
gcloud projects create szkola-chmury-pr02 --folder=813244329151
```

Post:

```
gcloud projects list

$ gcloud projects list
PROJECT_ID          NAME                PROJECT_NUMBER
szkola-chmury-gcpa  szkola-chmury-gcpa  816650243873
szkola-chmury-pr01  szkola-chmury-pr01  195169113896
szkola-chmury-pr02  szkola-chmury-pr02  659991755322
tokyo-dream-262809  tokyo-dream         965255384108
```



##### 2. W każdym z projektów wytwórz sieć wirtualną oraz jedną podsieć.

###### a) utworzenie sieci i maszyn

VPC pr01:

```
gcloud config set project szkola-chmury-pr01

gcloud compute networks create vpc-lab-project01 --subnet-mode=custom

gcloud compute firewall-rules create fire01-lab-project01 \
	--network vpc-lab-project01 \
	--allow tcp,udp,icmp \
	--source-ranges 0.0.0.0/0
	
gcloud compute firewall-rules create fire01-lab-project01 \
	--network vpc-lab-project01 \
    --allow tcp:22,tcp:3389,icmp
```

Subnet pr01:

```
gcloud compute networks subnets create subnet01-lab-project01 \
	--network=vpc-lab-project01 \
    --range=10.1.0.0/16 \
    --region=europe-west3
```

Test vm pr02:

```
gcloud compute instances create vm01-pr01 \
        --zone=europe-west3-a --machine-type=f1-micro \
        --preemptible \
        --subnet=subnet01-lab-project01 \
        --image=debian-9-stretch-v20191210 --image-project=debian-cloud
```



VPC pr02:

```
gcloud config set project szkola-chmury-pr02

gcloud compute networks create vpc-lab-project02 --subnet-mode=custom

gcloud compute firewall-rules create fire01-lab-project02 \
	--network vpc-lab-project02 \
	--allow tcp,udp,icmp \
	--source-ranges 0.0.0.0/0
	
gcloud compute firewall-rules create fire01-lab-project02 \
	--network vpc-lab-project02 \
    --allow tcp:22,tcp:3389,icmp
```



Subnet pr02:

```
gcloud compute networks subnets create subnet01-lab-project02 \
	--network=vpc-lab-project02 \
    --range=10.2.0.0/16 \
    --region=us-central1
```

Test vm pr02:

```
    gcloud compute instances create vm01-pr02 \
        --zone=us-central1-a --machine-type=f1-micro \
        --preemptible \
        --subnet=subnet01-lab-project02 \
        --image=debian-9-stretch-v20191210 --image-project=debian-cloud
```

###### b) utworzenie peering

Peering pr01->pr02:

```
gcloud config set project szkola-chmury-pr01 
gcloud compute networks peerings create peervpc-pr01-pr02 \
        --auto-create-routes \
        --network=vpc-lab-project01 \
        --peer-project szkola-chmury-pr02 \
        --peer-network vpc-lab-project02
```

Peering pr01->pr02:

```
gcloud config set project szkola-chmury-pr02
gcloud compute networks peerings create peervpc-pr02-pr01 \
    --auto-create-routes \
    --network=vpc-lab-project02 \
    --peer-project szkola-chmury-pr01 \
    --peer-network vpc-lab-project01
```

Test:

```
$ gcloud compute networks peerings list --network=vpc-lab-project02
NAME               NETWORK            PEER_PROJECT        PEER_NETWORK       AUTO_CREATE_ROUTES  STATE   STATE_DETAILS
peervpc-pr02-pr01  vpc-lab-project02  szkola-chmury-pr01  vpc-lab-project01  True                ACTIVE  [2020-01-25T05:50:32.427-08:00]: Connected.

```

```
admin_gcp@vm01-pr01:~$ ip a
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host 
       valid_lft forever preferred_lft forever
2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1460 qdisc pfifo_fast state UP group default qlen 1000
    link/ether 42:01:0a:01:00:02 brd ff:ff:ff:ff:ff:ff
    inet 10.1.0.2/32 brd 10.1.0.2 scope global eth0
       valid_lft forever preferred_lft forever
    inet6 fe80::4001:aff:fe01:2/64 scope link 
       valid_lft forever preferred_lft forever
admin_gcp@vm01-pr01:~$ ping -c 1 10.2.0.2
PING 10.2.0.2 (10.2.0.2) 56(84) bytes of data.
64 bytes from 10.2.0.2: icmp_seq=1 ttl=64 time=109 ms
--- 10.2.0.2 ping statistics ---
1 packets transmitted, 1 received, 0% packet loss, time 0ms
rtt min/avg/max/mdev = 109.639/109.639/109.639/0.000 ms
```

```
admin_gcp@vm01-pr02:~$ ip a
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host 
       valid_lft forever preferred_lft forever
2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1460 qdisc pfifo_fast state UP group default qlen 1000
    link/ether 42:01:0a:02:00:02 brd ff:ff:ff:ff:ff:ff
    inet 10.2.0.2/32 brd 10.2.0.2 scope global eth0
       valid_lft forever preferred_lft forever
    inet6 fe80::4001:aff:fe02:2/64 scope link 
       valid_lft forever preferred_lft forever
admin_gcp@vm01-pr02:~$ ping -c 1 10.1.0.2
PING 10.1.0.2 (10.1.0.2) 56(84) bytes of data.
64 bytes from 10.1.0.2: icmp_seq=1 ttl=64 time=106 ms

--- 10.1.0.2 ping statistics ---
1 packets transmitted, 1 received, 0% packet loss, time 0ms
rtt min/avg/max/mdev = 106.293/106.293/106.293/0.000 ms
```
Architektura:

![Alt text](https://github.com/yourand/szkolaChmury/blob/master/gcpArchitect/week9/img/peering.png)

##### 3. Jak zbudujesz połączenie pomiędzy sieciami tak, aby umożliwić dotarcie z Projektu B do świata zewnętrznego wychodząc przez urządzenie wirtualne w Projekcie A?

Modyfikacja routingu.

#### Zadanie 2. Przygotowaliśmy pewną architekturę, której schemat jest już gotowy, lecz brakuje realizacji.

![Alt text](https://github.com/yourand/szkolaChmury/blob/master/gcpArchitect/week9/img/schemat.png)



##### 1. Utwórz trzy osobne projekty.

Projekty:

```
gcloud projects create szkola-chmury-pr-a --folder=813244329151
gcloud projects create szkola-chmury-pr-b --folder=813244329151
gcloud projects create szkola-chmury-pr-c --folder=813244329151
```

Podpięcie kont bilingowych:

```
gcloud beta billing projects link szkola-chmury-pr-a \
  --billing-account ${GCP_billing_account}
gcloud beta billing projects link szkola-chmury-pr-b \
  --billing-account ${GCP_billing_account}
gcloud beta billing projects link szkola-chmury-pr-c \
  --billing-account ${GCP_billing_account}
```

VPC:

A

```
gcloud config set project szkola-chmury-pr-a

gcloud compute networks create vpc-lab-project-a-01 --subnet-mode=custom

gcloud compute firewall-rules create fire01-lab-project-a-01 \
	--network vpc-lab-project-a-01 \
	--allow tcp,udp,icmp \
	--source-ranges 0.0.0.0/0
	
gcloud compute firewall-rules create fire02-lab-project-a-01 \
	--network vpc-lab-project-a-01 \
    --allow tcp:22,tcp:3389,icmp

gcloud compute networks subnets create subnet01-lab-project-a-01 \
	--network=vpc-lab-project-a-01 \
    --range=10.128.0.0/20 \
    --region=us-central1
 
gcloud compute networks subnets create subnet02-lab-project-a-01 \
	--network=vpc-lab-project-a-01 \
    --range=10.132.0.0/20 \
    --region=europe-west1

```

B

```
gcloud config set project szkola-chmury-pr-b

gcloud compute networks create vpc-lab-project-b-01 --subnet-mode=custom

gcloud compute firewall-rules create fire01-lab-project-b-01 \
	--network vpc-lab-project-b-01 \
	--allow tcp,udp,icmp \
	--source-ranges 0.0.0.0/0
	
gcloud compute firewall-rules create fire02-lab-project-b-01 \
	--network vpc-lab-project-b-01 \
    --allow tcp:22,tcp:3389,icmp

```

C

```
gcloud config set project szkola-chmury-pr-c

gcloud compute networks create vpc-lab-project-c-01 --subnet-mode=custom

gcloud compute firewall-rules create fire02-lab-project-c-01 \
	--network vpc-lab-project-c-01 \
	--allow tcp,udp,icmp \
	--source-ranges 0.0.0.0/0
	
gcloud compute firewall-rules create fire01-lab-project-c-01 \
	--network vpc-lab-project-c-01 \
    --allow tcp:22,tcp:3389,icmp

gcloud compute networks subnets create managementsubnet-europe \
	--network=vpc-lab-project-c-01 \
    --range=10.130.0.0/20 \
    --region=europe-west1
    
gcloud compute networks subnets create subnet01-lab-project-b-01 \
	--network=vpc-lab-project-c-01 \
	--range=172.20.0.0/20 \
	--region=us-central1
```

Shared VPC:

```
gcloud beta compute shared-vpc enable szkola-chmury-pr-c

gcloud compute shared-vpc associated-projects add szkola-chmury-pr-b \
	--host-project szkola-chmury-pr-c

gcloud projects add-iam-policy-binding szkola-chmury-pr-c \
	--member "user:admin.gcp@overseer.eu" \
	--role "roles/compute.networkUser"
```

Utworzenie maszyny w C:

```
gcloud compute instances create vm01-pr0-c \
        --zone=europe-west1-b --machine-type=f1-micro \
        --preemptible \
        --subnet=managementsubnet-europe \
        --image=debian-9-stretch-v20191210 --image-project=debian-cloud
```

