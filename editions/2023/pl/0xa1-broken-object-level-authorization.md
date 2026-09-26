# API1:2023 Niepoprawna autoryzacja na poziomie obiektu

| Źródła zagrożeń/Wektory ataku | Słaby punkt bezpieczeństwa | Wpływ |
| - | - | - |
| Zależne od API : Możliwość wykorzystania **Łatwa** | Powszechność **Powszechna** : Wykrywalność **Łatwa** | Techniczny **Umiarkowany** : Zależny od biznesu |
| Atakujący mogą wykorzystać endpointy API podatne na niepoprawną autoryzację na poziomie obiektu, manipulując identyfikatorem obiektu przesyłanym w żądaniu. Identyfikatory obiektów mogą przyjmować dowolną postać: od kolejnych liczb całkowitych, przez UUID, po ogólne ciągi znaków. Niezależnie od typu danych łatwo je zidentyfikować w celu żądania (parametry ścieżki lub ciągu zapytania), w nagłówkach żądania, a nawet w ładunku żądania. | Ten problem jest niezwykle powszechny w aplikacjach opartych na API, ponieważ komponent serwerowy zwykle nie śledzi w pełni stanu klienta, lecz w większym stopniu polega na parametrach przesyłanych przez klienta, takich jak identyfikatory obiektów, aby zdecydować, do których obiektów przyznać dostęp. Odpowiedź serwera zazwyczaj wystarcza, aby ustalić, czy żądanie zakończyło się powodzeniem. | Nieautoryzowany dostęp do obiektów innych użytkowników może skutkować ujawnieniem danych nieuprawnionym stronom, utratą danych lub manipulacją danymi. W pewnych okolicznościach nieautoryzowany dostęp do obiektów może również prowadzić do całkowitego przejęcia konta. |

## Czy API jest podatne?

Autoryzacja na poziomie obiektu to mechanizm kontroli dostępu, zwykle
implementowany na poziomie kodu, który sprawdza, czy użytkownik ma dostęp
wyłącznie do tych obiektów, do których powinien mieć uprawnienia.

Każdy endpoint API, który otrzymuje identyfikator obiektu i wykonuje na nim
jakąkolwiek operację, powinien implementować kontrole autoryzacji na poziomie
obiektu. Kontrole te powinny sprawdzać, czy zalogowany użytkownik ma
uprawnienia do wykonania żądanej operacji na żądanym obiekcie.

Błędy w działaniu tego mechanizmu zwykle prowadzą do nieautoryzowanego
ujawnienia, modyfikacji lub zniszczenia wszystkich danych.

Porównanie identyfikatora użytkownika z bieżącej sesji (np. poprzez
wyodrębnienie go z tokena JWT) z podatnym parametrem identyfikatora nie jest
wystarczającym rozwiązaniem problemu niepoprawnej autoryzacji na poziomie
obiektu (Broken Object Level Authorization, BOLA). Takie podejście może
rozwiązać jedynie niewielką część przypadków.

W przypadku BOLA użytkownik z założenia ma dostęp do podatnego endpointu/funkcji
API. Naruszenie następuje na poziomie obiektu, poprzez manipulację
identyfikatorem. Jeśli atakującemu uda się uzyskać dostęp do endpointu/funkcji
API, do których nie powinien mieć dostępu, jest to przypadek [niepoprawnej
autoryzacji na poziomie funkcji][5] (BFLA), a nie BOLA.

## Przykładowe scenariusze ataków

### Scenariusz nr 1

Platforma e-commerce dla sklepów internetowych udostępnia stronę z listą
i wykresami przychodów prowadzonych na niej sklepów. Analizując żądania
przeglądarki, atakujący może zidentyfikować endpointy API wykorzystywane jako
źródło danych dla tych wykresów oraz ich wzorzec:
`/shops/{shopName}/revenue_data.json`. Korzystając z innego endpointu API,
atakujący może pobrać listę nazw wszystkich sklepów prowadzonych na platformie.
Za pomocą prostego skryptu, który podmienia nazwy z listy w miejscu
`{shopName}` w adresie URL, atakujący uzyskuje dostęp do danych sprzedażowych
tysięcy sklepów internetowych.

### Scenariusz nr 2

Producent samochodów umożliwił zdalne sterowanie swoimi pojazdami za pomocą
mobilnego API służącego do komunikacji z telefonem komórkowym kierowcy. API
pozwala kierowcy zdalnie uruchamiać i zatrzymywać silnik oraz blokować
i odblokowywać drzwi. W ramach tego przepływu użytkownik przesyła do API numer
identyfikacyjny pojazdu (Vehicle Identification Number, VIN).
API nie weryfikuje, czy numer VIN odpowiada pojazdowi należącemu do
zalogowanego użytkownika, co prowadzi do podatności BOLA. Atakujący może
uzyskać dostęp do pojazdów, które do niego nie należą.

### Scenariusz nr 3

Usługa przechowywania dokumentów online pozwala użytkownikom przeglądać,
edytować, przechowywać i usuwać swoje dokumenty. Gdy dokument użytkownika jest
usuwany, do API wysyłana jest mutacja GraphQL z identyfikatorem dokumentu.

```
POST /graphql
{
  "operationName":"deleteReports",
  "variables":{
    "reportKeys":["<DOCUMENT_ID>"]
  },
  "query":"mutation deleteReports($siteId: ID!, $reportKeys: [String]!) {
    {
      deleteReports(reportKeys: $reportKeys)
    }
  }"
}
```

Ponieważ dokument o podanym identyfikatorze jest usuwany bez żadnych dodatkowych
kontroli uprawnień, użytkownik może usunąć dokument innego użytkownika.

## Jak zapobiegać

* Zaimplementuj właściwy mechanizm autoryzacji, oparty na politykach
  użytkowników i ich hierarchii.
* Używaj mechanizmu autoryzacji do sprawdzania, czy zalogowany użytkownik ma
  prawo wykonać żądaną operację na rekordzie, w każdej funkcji, która
  wykorzystuje dane wejściowe od klienta do uzyskania dostępu do rekordu
  w bazie danych.
* Preferuj stosowanie losowych i nieprzewidywalnych wartości GUID jako
  identyfikatorów rekordów.
* Pisz testy oceniające podatność mechanizmu autoryzacji. Nie wdrażaj zmian,
  które powodują niepowodzenie testów.

 **Uwaga**

* Stosowanie identyfikatorów GUID/UUID zamiast przewidywalnych identyfikatorów pomaga ograniczyć ataki polegające na enumeracji obiektów. Jednak gdy prawidłowy identyfikator zostanie ujawniony – przez inny endpoint, nadmierne ujawnianie danych, wpisy w logach lub inną podatność – należy traktować go jako informację publiczną.

* Decyzje autoryzacyjne nigdy nie mogą opierać się na tajności ani nieprzewidywalności identyfikatorów obiektów. Każde żądanie musi niezależnie weryfikować, czy uwierzytelniony użytkownik jest uprawniony do dostępu do żądanego obiektu.

## Źródła

### OWASP

* [Authorization Cheat Sheet][1]
* [Authorization Testing Automation Cheat Sheet][2]

### Zewnętrzne

* [CWE-285: Improper Authorization][3]
* [CWE-639: Authorization Bypass Through User-Controlled Key][4]

[1]: https://cheatsheetseries.owasp.org/cheatsheets/Authorization_Cheat_Sheet.html
[2]: https://cheatsheetseries.owasp.org/cheatsheets/Authorization_Testing_Automation_Cheat_Sheet.html
[3]: https://cwe.mitre.org/data/definitions/285.html
[4]: https://cwe.mitre.org/data/definitions/639.html
[5]: ./0xa5-broken-function-level-authorization.md
