#### Zadanie, tydzień 2

#tydzien2.1

#tydzien2.2



##### 1. CQRS

Rozdzielenie operacji zapisu związanej z obsługą transakcji lub ładowaniem danych od kontrahentów od procesu przeglądania zawartości portalu przez użytkowników. Dodatkowo umożliwi optymalizację zapytań do danych.

Usługa: **Azure SQL** - relacyjna baza danych dla command oraz jej replica dla query.



##### 2. Load Balancing

Rozdzielenie ruchu na poziomie geograficznym oraz na poziomie przekierowania ruchu do wybranego serwisu aplikacji zależnie od dostępności.

Usługa: **Azure Front Door** - usługa balansująca zapewniająca globalne równoważenia obciążeń.



##### 3. Competing Consumers

Pozwoli na optymalizację obciążenia aplikacji asynchronicznym dostarczaniem danych do specjalizowanych serwisów obsługowych. Wzorzec sprawdzi się również w sytuacjach nagłego wzrostu transakcji w systemie, które zostaną umieszczone w kolejce co da czas na dostarczenie odpowiedniej ilości zasobów bez utraty transakcji.

Usługa: **Service Bus** – usługa ma zaimplementowane funkcjonalności , które można wykorzystać przy tworzeniu kolejki
