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


git rm --cached nazwa_pliku.cs

git add .
git commit -m "Twoja wiadomość co zrobiłeś"
git push

https://lab.itsol.dev/published/p/185afb34-1688-4054-ad82-5ab4cf6567e6

https://lab.itsol.dev/published/e/ef415a0f-8d73-4e87-a5b8-f4e907968b0c?classId=44d500ce-c352-4f2a-b551-2d51843df9e4&assignmentId=e3d4017f-f149-4baf-b44d-cf8d9b1d2fd4&submissionId=35c7bdc3-4b2e-3358-a41f-6ab4a6654359


{
  "Logging": {
    "LogLevel": {
      "Default": "Information",
      "Microsoft.AspNetCore": "Warning"
    }
  },
  "AllowedHosts": "*",
  "ConnectionStrings": {
    "DefaultConnection": "Server=localhost,1433;Database=ClinicAdoNet;User Id=sa;Password=YourStrong@Passw0rd;TrustServerCertificate=True"
  }
}



Po utworzeniu projektu w Riderze musisz zestawić połączenie na dwóch poziomach: w kodzie swojej aplikacji (aby endpointy mogły pobierać dane) oraz w samym środowisku Rider (aby IDE podpowiadało Ci nazwy tabel i sprawdzało błędy w SQL-u).

Oto jak to zrobić krok po kroku.
1. Połączenie w kodzie aplikacji (ADO.NET)

Krok 1: Dodanie paczki NuGet
Upewnij się, że masz zainstalowaną bibliotekę do obsługi SQL Server. W terminalu na dole ekranu wpisz:
Bash

dotnet add package Microsoft.Data.SqlClient

Krok 2: Konfiguracja appsettings.json
Otwórz plik appsettings.json i dodaj swój Connection String. To on mówi aplikacji, gdzie leży baza i jak się do niej zalogować:
JSON

{
  "ConnectionStrings": {
    "DefaultConnection": "Server=localhost,1433;Database=ClinicAdoNet;User Id=sa;Password=TwojeHaslo123;TrustServerCertificate=True"
  },
  "Logging": {
    "LogLevel": {
      "Default": "Information"
    }
  }
}

Krok 3: Pobranie adresu w kontrolerze
W swoim kontrolerze musisz pobrać ten adres z konfiguracji za pomocą IConfiguration. Robi się to w konstruktorze:
C#

using Microsoft.AspNetCore.Mvc;
using Microsoft.Data.SqlClient;

[ApiController]
[Route("api/[controller]")]
public class AppointmentsController : ControllerBase
{
    private readonly string _connectionString;

    public AppointmentsController(IConfiguration configuration)
    {
        // Pobieramy connection string o nazwie "DefaultConnection"
        _connectionString = configuration.GetConnectionString("DefaultConnection") 
                            ?? throw new Exception("Brak connection stringa!");
    }
}

Krok 4: Użycie połączenia w endpoincie
Teraz w każdej metodzie (np. GET czy POST) otwierasz połączenie, używając przygotowanego adresu:
C#

[HttpGet]
public async Task<IActionResult> GetSomething()
{
    await using var connection = new SqlConnection(_connectionString);
    await using var command = new SqlCommand("SELECT * FROM Patients", connection);
    
    await connection.OpenAsync(); // Otwarcie drzwi do bazy danych
    
    await using var reader = await command.ExecuteReaderAsync();
    // ... czytanie danych ...
    
    return Ok();
}

2. Połączenie samego Ridera z bazą (Dla podpowiedzi SQL)

Podpięcie bazy bezpośrednio pod Ridera to ogromne ułatwienie, bo IDE będzie analizować Twoje zapytania (np. SELECT * FROM ...) umieszczone w stringach i podświetlać błędy, jeśli pomylisz nazwę kolumny.

Krok 1: Znajdź po prawej stronie ekranu pionową zakładkę Database (ikona bębna) i kliknij ją.
Krok 2: Kliknij ikonę plusa (+) w lewym górnym rogu tego okienka.
Krok 3: Wybierz Data Source, a następnie Microsoft SQL Server.
Krok 4: W nowym oknie wpisz dane logowania do swojego lokalnego serwera:

    User: np. sa

    Password: Twoje hasło do bazy
    Krok 5: Zanim zapiszesz, przejdź do zakładki Schemas w tym samym okienku i zaznacz swoją bazę (np. ClinicAdoNet), aby Rider wiedział, z których tabelek ma brać podpowiedzi.
    Krok 6: Kliknij Test Connection (na dole). Jeśli pokaże się zielony ptaszek, kliknij OK.

Od teraz, gdy w kodzie C# zaczniesz pisać zapytanie wewnątrz new SqlCommand("...", connection), Rider rozpozna, że to SQL i zacznie podpowiadać Ci nazwy z Twojej bazy.
