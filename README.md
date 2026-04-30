# checklista
Czy logika końcówki operuje na bazie danych?
- Pamiętać o implementacji logiki w sposób asynchroniczny.

Czy końcówka przyjmuje parametry zapytania?
- Zaimplementować ich obsługę z uwagą na to, że zapytanie powinno się normalnie wykonać, nawet jeżeli użytkownik nie przekaże pod nie żadnej wartości.

Czy końcówka realizuje operacje typu PUT/POST i wymaga przesłania danych w ramach ciała żądania?
- Pamiętać o walidacji danych - sprawdzenie długości stringów, określenie wymaganych elementów, sprawdzenie wstępnych wymagań logiki biznesowej. 
https://weblogs.asp.net/ricardoperes/net-8-data-annotations-validation/

Czy końcówka operuje na jakichś wartościach z URL'a/ciała żądania, które odwołują się do jakiegoś stanu naszej aplikacji?
- Wziąć pod uwagę to, że typowy użytkownik aplikacji nie wie, jaki jest jej stan. Zanim użyjemy takich danych w naszym zapytaniu, należy sprawdzić, czy nasza aplikacja w ogóle o nich wie (czy je posiada). Jeżeli nie to zwrócić do klienta stosowną informację (404).

Czy końcówka wymaga sprawdzenia rozbudowanych wymagań biznesowych?
- Zaimplementować obsługę. Jeżeli wymaania nie są spełnione, zwrócić stosowną informację do klinta (409).

Czy końcówka GET osbsługuje pobranie danych z wielu tabeli?
- Odpowiednio pobrać wszystkie wartości z uwagą na to, że zwykły JOIN (Inner) zwraca tylko cześć wspólną wyniku (brane są pod uwagę tylko elementy, które mają jakieś relacje) - wymóg podziału zapytania na części lub użycia innych rodzajów JOIN'a (Left, Right).

Czy końcówka realizuje operacje typu GET na całej grupie zasobów (np. /students)?
- W przypadku powodzenia wykonania zapytania zwrócić kod 200 z listą obiektów w ciele odpowiedzi.
- W przypadku braku obiektów tego typu w stanie aplikacji, zwrócić pustą listę.

Czy końcówka realizuje operacje typu GET na pojedynczym obiekcie danego zasobu (np. /students/1)?
- W przypadku powodzenia operacji zwrócić kod 200 z żądanym obiektem.
- W przypadku niepowodzenia operacji zwrócić odpowiedni błąd.

Czy końcówka realizuje operacje typu POST?
- W przypadku powodzenia operacji zwrócić kod 201.

Czy końcówa realizuje operacje typy PUT/DELETE?
- W przypadku powodzenia operacji zwrócić kod 204.

Czy końcówa realizuje operacje typu POST/PUT/DELETE, w trakcie której wywoływanych jest wiele operacji edytujących stan aplikacji (np. baza danych)?
- Pamiętać o założeniu i obsłudze transakcji.

Czy logika biznesowa/zapytań do bazy zapisana jest bezpośrednio w kontrolerze?
- Wydzielić logikę do oddzielnych warstw. Odwołać się do nich poprzez wbudowany system zarządzania zależnościami (Kontener DI).
