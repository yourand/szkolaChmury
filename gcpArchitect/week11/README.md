#### Tydzień11

##### Stwórz regułę zapory sieciowej, która umożliwi ruch HTTP do backendu.

```
gcloud compute firewall-rules create default-allow-http \
    --direction=INGRESS \
    --priority=1000 \
    --network=default \
    --action=ALLOW \
    --rules=tcp:80 \
    --source-ranges=0.0.0.0/0 \
    --target-tags=http-server
```



##### Stwórz regułę zapory, aby zezwolić na wysyłanie sond - Health Check

```
gcloud compute firewall-rules create default-allow-health-check \
    --direction=INGRESS \
    --priority=1010 \
    --network=default \
    --action=ALLOW \
    --rules=tcp \
    --source-ranges=130.211.0.0/22,35.191.0.0/16 \
    --target-tags=http-server
```



##### Utwórz konfigurację szablonu dla instancji - Instance Template

```
gcloud compute instance-templates create web-server \
    --machine-type=f1-micro\
    --image-project=debian-cloud \
    --image=debian-9-stretch-v20200210 \
    --metadata-from-file=startup-script=apach.sh \
    --tags=http-server \
    --preemptible
```

##### Stwórz dwie grupy zarządzanych instancji np. w us-east1 i drugą w europe-west1

```
gcloud compute health-checks create tcp health-check-tcp \
    --global

gcloud compute instance-groups managed create web-server-europe-west1 \
    --template web-server \
    --size=1 \
    --initial-delay=300 \
    --health-check health-check-tcp \
    --region europe-west1

gcloud compute instance-groups managed set-autoscaling web-server-europe-west1 \
    --cool-down-period=90 \
    --target-cpu-utilization 0.9 \
    --min-num-replicas 1 \
    --max-num-replicas 2 \
    --region europe-west1

gcloud compute instance-groups managed create web-server-us-east1 \
    --template web-server \
    --size=1 \
    --initial-delay=300 \
    --health-check health-check-tcp \
    --region us-east1

gcloud compute instance-groups managed set-autoscaling web-server-us-east1 \
    --cool-down-period=90 \
    --target-cpu-utilization 0.9 \
    --min-num-replicas 1 \
    --max-num-replicas 2 \
    --region us-east1
```

##### Konfiguracja HTTP Load Balancera - Frontend i Backend

```
gcloud compute backend-services create backend-web-server \
    --protocol HTTP \
    --health-checks health-check-tcp \
    --global

gcloud compute backend-services add-backend backend-web-server \
    --balancing-mode=UTILIZATION \
    --max-utilization=0.8 \
    --capacity-scaler=1 \
    --instance-group=web-server-europe-west1 \
    --instance-group-region=europe-west1 \
    --global

gcloud compute backend-services add-backend backend-web-server \
    --balancing-mode=UTILIZATION \
    --max-utilization=0.8 \
    --capacity-scaler=1 \
    --instance-group=web-server-us-east1 \
    --instance-group-region=us-east1 \
    --global

gcloud compute url-maps create lb-web-server \
    --default-service backend-web-server

gcloud compute target-http-proxies create http-proxy --url-map lb-web-server

gcloud compute forwarding-rules create http-forwarding-rule \
    --global \
    --ports 80 \
    --target-http-proxy http-proxy
```

##### Test LB

```
HTTP Load Balancing Lab
Client IP
Your IP address : 130.211.0.236
Hostname
Server Hostname: web-server-europe-west1-56hr
Server Location
Region and Zone: europe-west1-c

HTTP Load Balancing Lab
Client IP
Your IP address : 35.191.0.155
Hostname
Server Hostname: web-server-us-east1-h5hg
Server Location
Region and Zone: us-east1-c

```

#### Cloud Armour

```
gcloud compute security-policies create ban-ip-policy \
    --description "ban ip atakujacego"

gcloud compute security-policies rules create 1000 \
    --security-policy ban-ip-policy \
    --description "deny traffic from 34.76.213.56/32" \
    --src-ip-ranges "34.76.213.56/32" \
    --action "deny-502"

gcloud compute backend-services update backend-web-server \
    --security-policy ban-ip-policy
```



##### Test Cloud Armour

```
dmin_gcp@vm01:~$ curl 34.107.158.229/
<h1>HTTP Load Balancing Lab</h1><h2>Client IP</h2>Your IP address : 130.211.2.99<h2>Hostname</h2>Server Hostname: web-server-europe-west1-56hr<h2>Server Location</h2>Region and Zone: europe-west1-c

admin_gcp@vm01:~$ curl 34.107.158.229
<!doctype html><meta charset="utf-8"><meta name=viewport content="width=device-width, initial-scale=1"><title>502</title>502 Bad Gatewayadmin_gcp@vm01:~$ curl 34.107.158.229
```

