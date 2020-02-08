### Tydzień 6. Zadanie domowe

*Budujesz aplikację w chmurze, Aplikacja uruchomiona na 3 VM, Środowisko TEST, Zatrzymaj maszyny o godzinie 22 uruchom o 7:00*

Na potrzeby zadania zostaną wykorzystane AWS Lambda

##### 1. Utworzenie policy
![Polityka](./ec2-start-stop-policy.json) 


##### 2. Utworzenie roli

Utworzenie roli dla Lambda z poprzednią polityką.

##### 3. Utworzenie AWS Lambda do zatrzymania instancji

![Python stop](./ec2-stop.py)

##### 4. Utworzenie rule w CloudWatch

```
Schedule Cron expression 0 22 * * ? *
```

##### 5. Test wyłączenia 

```
START RequestId: f96fede8-c447-4035-83b1-063370160227 Version: $LATEST
[{u'StoppingInstances': [{u'InstanceId': 'i-058d6d749da215208', u'CurrentState': {u'Code': 64, u'Name': 'stopping'}, u'PreviousState': {u'Code': 16, u'Name': 'running'}}], 'ResponseMetadata': {'RetryAttempts': 0, 'HTTPStatusCode': 200, 'RequestId': 'daaca958-dd45-49d7-91d2-405d336c37ee', 'HTTPHeaders': {'date': 'Sat, 08 Feb 2020 22:07:09 GMT', 'content-length': '579', 'content-type': 'text/xml;charset=UTF-8', 'server': 'AmazonEC2'}}}]
END RequestId: f96fede8-c447-4035-83b1-063370160227
REPORT RequestId: f96fede8-c447-4035-83b1-063370160227	Duration: 960.09 ms	Billed Duration: 1000 ms	Memory Size: 128 MB	Max Memory Used: 98 MB
```



##### 6. Utworzenie AWS Lambda do uruchamiania instancji

![Python stop](./ec2-start.py)

##### 4. Utworzenie rule w CloudWatch

```
Schedule Cron expression 0 7 * * ? *
```

##### 5. Test włączenia

```
START RequestId: d34822a5-506e-4623-89fc-a6e7e196d5d3 Version: $LATEST
[{u'StartingInstances': [{u'InstanceId': 'i-058d6d749da215208', u'CurrentState': {u'Code': 0, u'Name': 'pending'}, u'PreviousState': {u'Code': 80, u'Name': 'stopped'}}], 'ResponseMetadata': {'RetryAttempts': 0, 'HTTPStatusCode': 200, 'RequestId': '960955a9-3b64-4c3c-8262-15eb729b1176', 'HTTPHeaders': {'date': 'Sat, 08 Feb 2020 22:21:27 GMT', 'content-length': '579', 'content-type': 'text/xml;charset=UTF-8', 'server': 'AmazonEC2'}}}]
END RequestId: d34822a5-506e-4623-89fc-a6e7e196d5d3
REPORT RequestId: d34822a5-506e-4623-89fc-a6e7e196d5d3	Duration: 1146.22 ms	Billed Duration: 1200 ms	Memory Size: 128 MB	Max Memory Used: 97 MB	Init Duration: 480.06 ms

```


_______________________________________________



### LAB Creating your first Amazon EC2 Instances

#### Task 1: Launch a Linux instance

```
aws2 ec2 create-security-group \
    --group-name ec2lab-sg \
    --vpc-id vpc-0eb1a4545191580fb
    --description "grupa dla maszyny lab"
```

```
aws2 ec2 authorize-security-group-ingress \
    --group-id sg-029b90c1c6e90fae2\
    --protocol tcp \
    --port 22 \
    --cidr 0.0.0.0/0

```

```
aws2 ec2 authorize-security-group-ingress \
    --group-id sg-029b90c1c6e90fae2\
    --protocol tcp \
    --port 80 \
    --cidr 0.0.0.0/0
```

```
aws2 ec2 create-key-pair --key-name ec2lab-key \
    --query 'KeyMaterial' \
    --output text > ec2lab-key.pem

chmod 400 ec2lab-key.pem
```

```
aws2 ec2 run-instances \
    --image-id ami-071f4ce599deff521 \
    --security-group-ids sg-029b90c1c6e90fae2\
    --instance-type t2.micro \
    --subnet-id subnet-063ccd9d6a565f19a \
    --count 1 \
    --associate-public-ip-address \
    --user-data file://user-data.txt \
    --key-name ec2lab-key
```

```
aws2 ec2 create-tags \
	--resources i-058d6d749da215208 \
    --tags Key=Name,Value=ec2lab
    
aws2 ec2 create-tags \
	--resources i-058d6d749da215208 \
	--tags Key=Env,Value=TEST
```

### Task 2: Connect to your EC2 instance

```
$ ssh -i "ec2lab-key.pem" ec2-user@18.202.233.32
The authenticity of host '18.202.233.32 (18.202.233.32)' can't be established.
ECDSA key fingerprint is SHA256:4GnUBZAH3fQ9wELXhfJK7IGazY5L8VrxWzXkiICx5XA.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added '18.202.233.32' (ECDSA) to the list of known hosts.

       __|  __|_  )
       _|  (     /   Amazon Linux AMI
      ___|\___|___|

https://aws.amazon.com/amazon-linux-ami/2018.03-release-notes/
2 package(s) needed for security, out of 5 available
Run "sudo yum update" to apply all updates.

```

### Task 3: Create a PHP Web Page on Your EC2

```
cd /var/www/html 
sudo nano index.php
```

```php
<?php
      $url = "http://169.254.169.254/latest/meta-data/instance-id";
      $instance_id = file_get_contents($url);
      echo "<h1>Hello World</h1>";
      echo "Instance ID: <b>" . $instance_id . "</b><br/>";
      $url = "http://169.254.169.254/latest/meta-data/placement/availability-zone";
      $zone = file_get_contents($url);
      echo "Zone: <b>" . $zone . "</b><br/>";
?>
```

```
$ curl 18.202.233.32
<h1>Hello World</h1>Instance ID: <b>i-058d6d749da215208</b><br/>Zone: <b>eu-west-1a</b><br/>
```

