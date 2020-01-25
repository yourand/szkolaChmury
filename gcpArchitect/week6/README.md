### Zadanie domowe nr 6.

 ##### 1. Założenia.

Firma chce mieć możliwość udostępniania swoich zbiorów danych zewnętrznym dostawcom, w celach sprawdzenia poprawności działania ich rozwiązań. W tym celu należy przygotować mechanizm migracji danych do chmury, którymi jest zbiór zdjęć.

##### 2. Wymagania.

a)    Przenoszenie plików graficznych ze środowiska on-premises do chmury.

b)   Bezpieczne udostępnienie przeniesionych zasobów firmie trzeciej.

c)    Ze względów prawnych dane muszą być przechowywane w Europie.

d)   W celu uniknięcia konsekwencji awarii regionu dane musze być przechowywane w dwóch geolokalizacjach.

e)   Zgromadzone dane po 60 dniach będą przenoszone do tańszego storage Nearline.

f)    Zgromadzone dane po 90 dniach powinny zostać usunięte.

g)    10 dni od usunięcia, pliki będzie można jeszcze odtworzyć.

h)   Metoda dostępu do plików dla zewnętrznej firmy, umożliwiająca dostęp  do plików bez konieczności zakładania dedykowanych kont.

##### 3. Architektura.

W celu spełnienia wymagań, zostanie utworzony dedykowany bucket na Cloud Storage oraz konto serwisowe z uprawnieniami do odczytu danych z dedykowanego bucketu. Ze względu na wymogi dotyczące lokalizacji w Europie, wysokiej dostępności i ograniczeniu kosztów storage będzie umieszczone w dual-regionie. Dane będą kopiowane z lokalizacji klienta do Cloud Storage a stamtąd udostępniane firmie trzeciej. Zgodnie z wymogiem aby nie zakładać dedykowanych kont dla pracowników firmy trzeciej, dane będą udostępnione za pomocą signed urls.

 

![img](https://github.com/yourand/szkolaChmury/blob/master/gcpArchitect/week6/img/arch.png?raw=true)

 

 

##### 4. PoC.

a)    Utworzenie bucket – bucket utworzono w dual-regionie w celu optymalizacji kosztów. Jedyny dual region w Europie to EUR4 (*EUROPE-NORTH1 and EUROPE-WEST4. Additionally, object metadata may be stored in EUROPE-WEST1*):

```
gsutil mb -c standard -l EUR4 gs://rk-home-week6
```



 

b)   Pobranie pliku.

```wget https://storage.googleapis.com/testdatachm/sampledata/imagedata.tar.gz```

c)    Wypakowanie zdjęć.

```tar -xvf imagedata.tar.gz```

 

d)   Utworzenie dedykowanego konta serwisowego:

```
gcloud iam service-accounts create bucketowy-podgladacz \
	--display-name="Podgladacz bucketow"

kontosv="bucketowy-podgladacz@szkola-chmury-agcp.iam.gserviceaccount.com"
```

 e)   Wygenerowanie kluczy dla konta serwisowego oraz nadanie uprawnień:

```
gcloud iam service-accounts keys create priv-key.json \
	--iam-account $kontosv

gsutil iam ch $kontosv:objectViewer gs://rk-home-week6/
```

f)    Ustawienie wersjonowania na potrzeby soft-delete:

```gsutil versioning set on gs://rk-home-week6```

 g)    Przygotowanie polityki lifecycles policy1.json:

```
{
    "lifecycle":
    {
        "rule": 
    [
        {
            "action": {
                "type": "SetStorageClass",
                "storageClass": "NEARLINE"
            },

            "condition": {
                "age": 60,
                "matchesStorageClass": ["MULTI_REGIONAL", "STANDARD", "DURABLE_REDUCED_AVAILABILITY"]
            }
        },

        {
            "action": {
                "type": "Delete"
            },
            "condition": {
                  "age": 90,
                  "isLive": true
            }
        },

        {
            "action": {
                "type": "Delete"
            },
            "condition": {
                  "age": 10,
                  "isLive": false
            }
        }
                    
    ]
    }
}

```



 h)   Przekopiowanie plików z serwera lokalnego do bucket w chmurze:

```gsutil -m cp -r * gs://rk-home-week6/```

 i)    Aplikacja polityki:

```gsutil lifecycle set policy1.json gs://rk-home-week6```

 

j)    Wygenerowanie signed url na potrzeby bezpiecznego udostępnienia plików do firmy trzeciej.

```
gsutil signurl -d 7d priv-key.json gs://rk-home-week6/fungs/*
gsutil signurl -d 7d priv-key.json gs://rk-home-week6/garden/*
gsutil signurl -d 7d priv-key.json gs://rk-home-week6/homeplants/*
gsutil signurl -d 7d priv-key.json gs://rk-home-week6/rocks/*
```

 

##### 5.  Podsumowanie.

Przygotowany PoC wykazał, że zaprojektowana metoda jest mało efektywna. Wygenerowanie signed url dla każdego pliku oraz przesłanie ich firmie trzeciej w celu dostępu do plików praktycznie uniemożliwi ich wykorzystanie w szerszym zakresie. 

Aby zachować wymogi oraz umożliwić prace z plikami rekomendowane jest umieszczenie na bucket jednego pliku w formie skompresowanego archiwum oraz umożliwienie jego pobrania przez firmę trzecią. Pobrane archiwum zostanie rozpakowane po stronie odbiorcy danych.
