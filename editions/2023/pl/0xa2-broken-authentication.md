# API2:2023 Niepoprawne uwierzytelnianie

| Źródła zagrożeń/Wektory ataku | Słaby punkt bezpieczeństwa | Wpływ |
| - | - | - |
| Zależne od API : Możliwość wykorzystania **Łatwa** | Powszechność **Częsta** : Wykrywalność **Łatwa** | Techniczny **Poważny** : Zależny od biznesu |
| Mechanizm uwierzytelniania jest łatwym celem dla atakujących, ponieważ jest dostępny dla wszystkich. Choć wykorzystanie niektórych problemów z uwierzytelnianiem może wymagać bardziej zaawansowanych umiejętności technicznych, narzędzia do ich wykorzystania są powszechnie dostępne. | Błędne wyobrażenia inżynierów oprogramowania i bezpieczeństwa na temat granic uwierzytelniania oraz nieodłączna złożoność implementacji sprawiają, że problemy z uwierzytelnianiem są powszechne. Metodyki wykrywania niepoprawnego uwierzytelniania są dostępne i łatwe do opracowania. | Atakujący mogą przejąć pełną kontrolę nad kontami innych użytkowników w systemie, odczytać ich dane osobowe i wykonywać w ich imieniu wrażliwe operacje. Systemy najprawdopodobniej nie będą w stanie odróżnić działań atakujących od działań uprawnionych użytkowników. |

## Czy API jest podatne?

Endpointy i przepływy uwierzytelniania są zasobami, które wymagają ochrony.
Ponadto funkcje „Nie pamiętam hasła / resetowanie hasła” należy traktować tak
samo jak mechanizmy uwierzytelniania.

API jest podatne, jeśli:

* Dopuszcza credential stuffing, w którym atakujący przeprowadza atak siłowy
  z użyciem listy prawidłowych nazw użytkowników i haseł.
* Pozwala atakującym przeprowadzić atak siłowy na to samo konto użytkownika
  bez stosowania mechanizmu captcha/blokowania kont.
* Dopuszcza słabe hasła.
* Przesyła wrażliwe dane uwierzytelniające, takie jak tokeny uwierzytelniające
  i hasła, w adresie URL.
* Pozwala użytkownikom zmienić adres e-mail, bieżące hasło lub wykonać inne
  wrażliwe operacje bez prośby o potwierdzenie hasłem.
* Nie weryfikuje autentyczności tokenów.
* Akceptuje niepodpisane lub słabo podpisane tokeny JWT (`{"alg":"none"}`)
* Nie weryfikuje daty wygaśnięcia tokena JWT.
* Używa haseł w postaci tekstu jawnego, niezaszyfrowanych lub słabo
  haszowanych.
* Używa słabych kluczy szyfrujących.

Ponadto mikrousługa jest podatna, jeśli:

* Inne mikrousługi mogą uzyskać do niej dostęp bez uwierzytelniania
* Używa słabych lub przewidywalnych tokenów do egzekwowania uwierzytelniania

## Przykładowe scenariusze ataków

## Scenariusz nr 1

Aby uwierzytelnić użytkownika, klient musi wysłać żądanie API podobne do
poniższego, zawierające poświadczenia użytkownika:

```
POST /graphql
{
  "query":"mutation {
    login (username:\"<username>\",password:\"<password>\") {
      token
    }
   }"
}
```

Jeśli poświadczenia są prawidłowe, zwracany jest token uwierzytelniający, który
należy przekazywać w kolejnych żądaniach w celu identyfikacji użytkownika.
Próby logowania podlegają restrykcyjnemu ograniczaniu częstotliwości żądań:
dozwolone są tylko trzy żądania na minutę.

Aby przeprowadzić atak siłowy na logowanie do konta ofiary, atakujący
wykorzystują grupowanie zapytań GraphQL (query batching) do obejścia
ograniczenia częstotliwości żądań, co przyspiesza atak:

```
POST /graphql
[
  {"query":"mutation{login(username:\"victim\",password:\"password\"){token}}"},
  {"query":"mutation{login(username:\"victim\",password:\"123456\"){token}}"},
  {"query":"mutation{login(username:\"victim\",password:\"qwerty\"){token}}"},
  ...
  {"query":"mutation{login(username:\"victim\",password:\"123\"){token}}"},
]
```

## Scenariusz nr 2

Aby zaktualizować adres e-mail powiązany z kontem użytkownika, klienci powinni
wysłać żądanie API podobne do poniższego:

```
PUT /account
Authorization: Bearer <token>

{ "email": "<new_email_address>" }
```

Ponieważ API nie wymaga od użytkowników potwierdzenia tożsamości przez podanie
bieżącego hasła, atakujący, którym uda się wykraść token uwierzytelniający,
mogą przejąć konto ofiary, uruchamiając proces resetowania hasła po zmianie
adresu e-mail przypisanego do konta ofiary.

## Jak zapobiegać

* Upewnij się, że znasz wszystkie możliwe przepływy uwierzytelniania w API
  (mobilne/webowe/linki głębokie implementujące uwierzytelnianie jednym
  kliknięciem itp.). Zapytaj swoich inżynierów, które przepływy zostały
  pominięte.
* Zapoznaj się ze swoimi mechanizmami uwierzytelniania. Upewnij się, że
  rozumiesz, czym są i jak są używane. OAuth nie jest uwierzytelnianiem, tak
  samo jak klucze API.
* Nie wymyślaj koła na nowo w zakresie uwierzytelniania, generowania tokenów
  ani przechowywania haseł. Korzystaj ze standardów.
* Endpointy odzyskiwania poświadczeń/resetowania hasła należy traktować tak
  samo jak endpointy logowania pod względem ochrony przed atakami siłowymi,
  ograniczania częstotliwości żądań i blokowania kont.
* Wymagaj ponownego uwierzytelnienia przy wrażliwych operacjach (np. zmianie
  adresu e-mail właściciela konta lub numeru telefonu do 2FA).
* Korzystaj z [OWASP Authentication Cheatsheet][1].
* Tam, gdzie to możliwe, zaimplementuj uwierzytelnianie wieloskładnikowe.
* Zaimplementuj mechanizmy chroniące przed atakami siłowymi, aby ograniczyć
  credential stuffing, ataki słownikowe i ataki siłowe na endpointy
  uwierzytelniania. Mechanizm ten powinien być bardziej restrykcyjny niż
  standardowe mechanizmy ograniczania częstotliwości żądań w API.
* Zaimplementuj mechanizmy [blokowania kont][2]/captcha, aby zapobiegać atakom
  siłowym na konkretnych użytkowników. Zaimplementuj sprawdzanie słabych haseł.
* Kluczy API nie należy używać do uwierzytelniania użytkowników. Powinny być
  używane wyłącznie do autoryzacji [klientów API][3].

## Źródła

### OWASP

* [Authentication Cheat Sheet][1]
* [Key Management Cheat Sheet][4]
* [Credential Stuffing][5]

### Zewnętrzne

* [CWE-204: Observable Response Discrepancy][6]
* [CWE-307: Improper Restriction of Excessive Authentication Attempts][7]

[1]: https://cheatsheetseries.owasp.org/cheatsheets/Authentication_Cheat_Sheet.html
[2]: https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/04-Authentication_Testing/03-Testing_for_Weak_Lock_Out_Mechanism(OTG-AUTHN-003)
[3]: https://cloud.google.com/endpoints/docs/openapi/when-why-api-key
[4]: https://cheatsheetseries.owasp.org/cheatsheets/Key_Management_Cheat_Sheet.html
[5]: https://owasp.org/www-community/attacks/Credential_stuffing
[6]: https://cwe.mitre.org/data/definitions/204.html
[7]: https://cwe.mitre.org/data/definitions/307.html
