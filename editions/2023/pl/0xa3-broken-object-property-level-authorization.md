# API3:2023 Niepoprawna autoryzacja na poziomie właściwości obiektu

| Źródła zagrożeń/Wektory ataku | Słaby punkt bezpieczeństwa | Wpływ |
| - | - | - |
| Zależne od API : Możliwość wykorzystania **Łatwa** | Powszechność **Częsta** : Wykrywalność **Łatwa** | Techniczny **Umiarkowany** : Zależny od biznesu |
| API zwykle udostępniają endpointy, które zwracają wszystkie właściwości obiektu. Dotyczy to szczególnie API REST. W przypadku innych protokołów, takich jak GraphQL, określenie, które właściwości mają zostać zwrócone, może wymagać spreparowanych żądań. Identyfikacja dodatkowych właściwości, którymi można manipulować, wymaga więcej wysiłku, ale dostępnych jest kilka zautomatyzowanych narzędzi, które pomagają w tym zadaniu. | Analiza odpowiedzi API wystarcza, aby zidentyfikować informacje wrażliwe w reprezentacjach zwracanych obiektów. Do identyfikacji dodatkowych (ukrytych) właściwości zwykle stosuje się fuzzing. To, czy można je zmienić, sprowadza się do spreparowania żądania API i analizy odpowiedzi. Jeśli docelowa właściwość nie jest zwracana w odpowiedzi API, może być konieczna analiza efektów ubocznych. | Nieautoryzowany dostęp do prywatnych/wrażliwych właściwości obiektów może skutkować ujawnieniem, utratą lub uszkodzeniem danych. W pewnych okolicznościach nieautoryzowany dostęp do właściwości obiektów może prowadzić do eskalacji uprawnień lub częściowego/całkowitego przejęcia konta. |

## Czy API jest podatne?

Gdy użytkownik uzyskuje dostęp do obiektu za pośrednictwem endpointu API,
ważne jest, aby sprawdzić, czy ma on dostęp do konkretnych właściwości obiektu,
do których próbuje się odwołać.

Endpoint API jest podatny, jeśli:

* Endpoint API udostępnia właściwości obiektu, które są uznawane za wrażliwe
  i nie powinny być odczytywane przez użytkownika (wcześniej: „[Nadmierne
  ujawnianie danych (Excessive Data Exposure)][1]”).
* Endpoint API pozwala użytkownikowi zmienić, dodać lub usunąć wartość
  wrażliwej właściwości obiektu, do której użytkownik nie powinien mieć dostępu
  (wcześniej: „[Mass Assignment][2]”).

## Przykładowe scenariusze ataków

### Scenariusz nr 1

Aplikacja randkowa pozwala użytkownikowi zgłaszać innych użytkowników za
niewłaściwe zachowanie. W ramach tego przepływu użytkownik klika przycisk
„zgłoś”, co wywołuje następujące wywołanie API:

```
POST /graphql
{
  "operationName":"reportUser",
  "variables":{
    "userId": 313,
    "reason":["offensive behavior"]
  },
  "query":"mutation reportUser($userId: ID!, $reason: String!) {
    reportUser(userId: $userId, reason: $reason) {
      status
      message
      reportedUser {
        id
        fullName
        recentLocation
      }
    }
  }"
}
```

Endpoint API jest podatny, ponieważ pozwala uwierzytelnionemu użytkownikowi
uzyskać dostęp do wrażliwych właściwości obiektu (zgłoszonego) użytkownika,
takich jak „fullName” i „recentLocation”, do których inni użytkownicy nie
powinni mieć dostępu.

### Scenariusz nr 2

Internetowa platforma handlowa, która pozwala jednemu typowi użytkowników
(„gospodarzom”) wynajmować swoje mieszkania innemu typowi użytkowników
(„gościom”), wymaga od gospodarza zaakceptowania rezerwacji dokonanej przez
gościa przed obciążeniem gościa opłatą za pobyt.

W ramach tego przepływu gospodarz wysyła wywołanie API do
`POST /api/host/approve_booking` z następującym prawidłowym ładunkiem:

```
{
  "approved": true,
  "comment": "Check-in is after 3pm"
}
```

Gospodarz ponownie wysyła prawidłowe żądanie, dodając następujący złośliwy
ładunek:

```
{
  "approved": true,
  "comment": "Check-in is after 3pm",
  "total_stay_price": "$1,000,000"
}
```

Endpoint API jest podatny, ponieważ nie sprawdza, czy gospodarz powinien mieć
dostęp do wewnętrznej właściwości obiektu `total_stay_price`, w związku z czym
gość zostanie obciążony wyższą kwotą, niż powinien.

### Scenariusz nr 3

Serwis społecznościowy oparty na krótkich filmach stosuje restrykcyjne
filtrowanie treści i cenzurę. Nawet jeśli przesłany film zostanie
zablokowany, użytkownik może zmienić jego opis za pomocą następującego żądania
API:

```
PUT /api/video/update_video

{
  "description": "a funny video about cats"
}
```

Sfrustrowany użytkownik może ponownie wysłać prawidłowe żądanie, dodając
następujący złośliwy ładunek:

```
{
  "description": "a funny video about cats",
  "blocked": false
}
```

Endpoint API jest podatny, ponieważ nie sprawdza, czy użytkownik powinien mieć
dostęp do wewnętrznej właściwości obiektu `blocked`, dzięki czemu użytkownik
może zmienić jej wartość z `true` na `false` i odblokować własne zablokowane
treści.

## Jak zapobiegać

* Udostępniając obiekt za pośrednictwem endpointu API, zawsze upewnij się, że
  użytkownik powinien mieć dostęp do udostępnianych właściwości obiektu.
* Unikaj stosowania ogólnych metod, takich jak `to_json()` i `to_string()`.
  Zamiast tego starannie wybieraj konkretne właściwości obiektu, które
  faktycznie chcesz zwrócić.
* Jeśli to możliwe, unikaj funkcji, które automatycznie wiążą dane wejściowe
  klienta ze zmiennymi w kodzie, obiektami wewnętrznymi lub właściwościami
  obiektów („Mass Assignment”).
* Zezwalaj na zmiany wyłącznie tych właściwości obiektu, które powinny być
  aktualizowane przez klienta.
* Zaimplementuj mechanizm walidacji odpowiedzi schematem jako dodatkową warstwę
  zabezpieczeń. W ramach tego mechanizmu zdefiniuj i egzekwuj dane zwracane
  przez wszystkie metody API.
* Ograniczaj zwracane struktury danych do absolutnego minimum, zgodnie
  z wymaganiami biznesowymi/funkcjonalnymi dla danego endpointu.

## Źródła

### OWASP

* [API3:2019 Excessive Data Exposure - OWASP API Security Top 10 2019][1]
* [API6:2019 - Mass Assignment - OWASP API Security Top 10 2019][2]
* [Mass Assignment Cheat Sheet][3]

### Zewnętrzne

* [CWE-213: Exposure of Sensitive Information Due to Incompatible Policies][4]
* [CWE-915: Improperly Controlled Modification of Dynamically-Determined Object Attributes][5]

[1]: https://owasp.org/API-Security/editions/2019/en/0xa3-excessive-data-exposure/
[2]: https://owasp.org/API-Security/editions/2019/en/0xa6-mass-assignment/
[3]: https://cheatsheetseries.owasp.org/cheatsheets/Mass_Assignment_Cheat_Sheet.html
[4]: https://cwe.mitre.org/data/definitions/213.html
[5]: https://cwe.mitre.org/data/definitions/915.html
