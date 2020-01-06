## Praca domowa week 3

##### env
```
region="eu-west-1"
```

## Projekt

#### Zlozenia
Budowana jest aplikacja wspolp[arcujaca z s3 bucket.  
Aplikacja dziala na wirtualnej maszynie i musi miec mozliwosc zapisywanie na bucket S3.  

#### Wymagania aplikacji pracujacej na vm
1. Mozliwosc zapisu  do s3 bucket.  
2. Mozliwosc wylistowania zawarosci w celu okreslenia zawartosci bucket.
3. Brak mozliwosci usuniecia obiektu.


#### Procedura
1. Utworzenie policy umozliwiajacej wyswietlenie zawartosci bucket i umieszczenie tam pliku. 1-allowBucketOp.json.
2. Utworzenie roli IAM dla serwisu EC2 z wczesniej utworzona polityka.
3. Dodanie nowej roli do maszyny wirtualnej.



## PoC

#### Procedura. Korekta procedury z uwagi na wykonanie zdania w CLI.
1. Utworzenie trust policy.
2. Utworzenie roli z trust policy 2-ec2RoleTrustPolicy.json.
3. Utworzenie policy 1-allowBucketOp.json.
4. Przypisanioe policy do roli.
5. Utworzenie profilu instancji
6. Dodanie roli do profilu.
7. Dodanie profilu do instancji.



#### Utworzenie Bucket
```
aws2 s3api create-bucket --bucket hw-week3 --region $region \
    --create-bucket-configuration LocationConstraint=$region
```
#### Utworzenie polityki
```
aws2 iam create-policy --policy-name rk-S3ListPutGet \
    --policy-document file://allowBucketOp.json
```

#### Utworznie roli EC2
```
aws2 iam create-role --role-name rk-role-S3ListPutGet \
    --assume-role-policy-document file://2-ec2RoleTrustPolicy.json
```
#### Polaczenie polityki z rola

```
aws2 iam attach-role-policy --policy-arn arn:aws:iam::007353919774:policy/rk-S3ListPutGet \
    --role-name rk-role-S3ListPutGet
```

#### Utworzenie profilu instancji
```
aws2 iam create-instance-profile --instance-profile-name rk-profile01-s3

```
#### dodanie roli do profilu
```
aws2 iam add-role-to-instance-profile --instance-profile-name rk-profile01-s3 --role-name rk-role-S3ListPutGet
```

#### Utworzenie maszyny
```

aws2 ec2 create-key-pair --key-name ssh-key --query 'KeyMaterial' --output text > ssh-key.pem
aws2 ec2 run-instances --image-id ami-0bbc25e23a7640b9b --instance-type t2.micro --key-name ssh-key 
chmod 400 ssh-key.pem

```

#### dodanie profilu do instancji
```
aws2 ec2 describe-instances
aws2 ec2 associate-iam-instance-profile --instance-id i-0a61dff800af09721 --iam-instance-profile Name="rk-profile01-s3"
```

#### Testy
##### Testy z podpietym profilem
```
[ec2-user@ip-172-31-44-168 ~]$ aws s3 ls
An error occurred (AccessDenied) when calling the ListBuckets operation: Access Denied
[ec2-user@ip-172-31-44-168 ~]$ aws s3 ls s3://hw-week3
2020-01-06 14:05:36       1561 zadanie3.md
[ec2-user@ip-172-31-44-168 ~]$ aws s3 rm s3://hw-week3/zadanie3.md
delete failed: s3://hw-week3/zadanie3.md An error occurred (AccessDenied) when calling the DeleteObject operation: Access Denied
[ec2-user@ip-172-31-44-168 ~]$ aws s3 cp olo.txt  s3://hw-week3/
upload: ./olo.txt to s3://hw-week3/olo.txt                        
[ec2-user@ip-172-31-44-168 ~]$ aws s3 ls s3://hw-week3
2020-01-06 14:21:13         13 olo.txt
2020-01-06 14:05:36       1561 zadanie3.md

```

##### usuniecie profilu 
```
aws2 ec2 describe-iam-instance-profile-associations
aws2 ec2 disassociate-iam-instance-profile --association-id iip-assoc-0baa8259b386ac4c1
```

##### Testy bez profilu
```
[ec2-user@ip-172-31-44-168 ~]$ aws s3 ls s3://hw-week3
Unable to locate credentials. You can configure credentials by running "aws configure".
[ec2-user@ip-172-31-44-168 ~]$ aws s3 cp olo.txt  s3://hw-week3/
upload failed: ./olo.txt to s3://hw-week3/olo.txt Unable to locate credentials
```