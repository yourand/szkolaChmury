### Zadanie tydzień 4

#### 1. Schemat

![Alt text](https://github.com/yourand/szkolaChmury/blob/master/awsArchitectAssociate/week4/img/schemat01.png)


#### 1. VPC

```
aws2 ec2 create-vpc --cidr-block 10.1.0.0/16
aws2 ec2 create-tags --resources vpc-08cf2e289b2c8ec93 --tags Key=Name,Value=HomeWork04

```

#### 2. Public Subnets

Utworzenie subnet Public01 10.1.1.0/24

```
aws2 ec2 create-subnet \
    --vpc-id vpc-08cf2e289b2c8ec93 \
    --availability-zone eu-west-1a \
    --cidr-block 10.1.1.0/24

aws2 ec2 create-tags --resources subnet-0a8c311c1e696fe9d \
    --tags Key=Name,Value=Public01

```


Utworzenie subnet Public02 10.1.2.0/24

```
aws2 ec2 create-subnet \
    --vpc-id vpc-08cf2e289b2c8ec93 \
    --availability-zone eu-west-1b \
    --cidr-block 10.1.2.0/24

aws2 ec2 create-tags --resources subnet-0164f2b1ae47d4b2e \
    --tags Key=Name,Value=Public02

```

#### 3. Private Subnets

Utworzenie subnet Private01 10.1.3.0/24

```
aws2 ec2 create-subnet \
    --vpc-id vpc-08cf2e289b2c8ec93 \
    --availability-zone eu-west-1a \
    --cidr-block 10.1.3.0/24

aws2 ec2 create-tags --resources subnet-0081f258d37261cac \
    --tags Key=Name,Value=Private01

```

Utworzenie subnet Private02 10.1.2.0/24

```
aws2 ec2 create-subnet \
    --vpc-id vpc-08cf2e289b2c8ec93 \
    --availability-zone eu-west-1b \
    --cidr-block 10.1.4.0/24

aws2 ec2 create-tags --resources subnet-08689551c0b507387 \
    --tags Key=Name,Value=Private02
```

#### 4. Internet Public obie strony

**Utworzenie tablicy routingu**

```
aws2 ec2 create-route-table --vpc-id vpc-08cf2e289b2c8ec93

aws2 ec2 create-tags --resources rtb-0a9fab451e04e8c02 \
    --tags Key=Name,Value=RT-HomeWork04-Public
```

**Przypisanie subnetów publicznych do nowej tablicy routingu**

```
aws2 ec2 associate-route-table --route-table-id rtb-0a9fab451e04e8c02 --subnet-id subnet-0a8c311c1e696fe9d
aws2 ec2 associate-route-table --route-table-id rtb-0a9fab451e04e8c02 --subnet-id subnet-0164f2b1ae47d4b2e
```

**Utworzenie Internet Gateway**

```
aws2 ec2 create-internet-gateway

 aws2 ec2 create-tags --resources igw-0013e0097718fd3e5 \
    --tags Key=Name,Value=RT-HomeWork04-InternetGateway
```

**Przypięcie IG do VPC i dodanie routingu**

```
aws2 ec2 attach-internet-gateway --internet-gateway-id igw-0013e0097718fd3e5\
	--vpc-id vpc-08cf2e289b2c8ec93

aws2 ec2 create-route --route-table-id rtb-0a9fab451e04e8c02\
	--destination-cidr-block 0.0.0.0/0 \
    --gateway-id igw-0013e0097718fd3e5
```



#### 5. Internet Private tylko wychodzący

*NA SUCHO*

```
aws2 ec2 allocate-address
aws2 ec2 create-nat-gateway --subnet-id subnet-0a8c311c1e696fe9d --allocation-id eipalloc-*

aws2 ec2 create-route-table --vpc-id vpc-08cf2e289b2c8ec93
rtb-0bd0fa6237d94ab76

aws2 ec2 create-tags --resources rtb-0bd0fa6237d94ab76 \
    --tags Key=Name,Value=RT-HomeWork04-Priv

aws2 ec2 associate-route-table --route-table-id rtb-0bd0fa6237d94ab76\
	--subnet-id subnet-0081f258d37261cac
aws2 ec2 associate-route-table --route-table-id rtb-0bd0fa6237d94ab76\
	--subnet-id subnet-08689551c0b507387
```



#### 6. Private<->Public tylko 3306

```
aws2 ec2 create-security-group --group-name SgSql --description "SG SQL"\
	--vpc-id vpc-08cf2e289b2c8ec93

aws2 ec2 create-tags --resources sg-098d397382946071a \
    --tags Key=Name,Value=SG-HomeWork04-sql


aws2 ec2 authorize-security-group-ingress --group-id sg-098d397382946071a\
	--protocol tcp\
    --port 3306 \
    --cidr 10.1.0.0/16
```



