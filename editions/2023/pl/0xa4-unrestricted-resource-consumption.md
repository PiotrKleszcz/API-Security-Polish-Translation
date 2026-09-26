# API4:2023 Nieograniczona konsumpcja zasobów

| Źródła zagrożeń/Wektory ataku | Słaby punkt bezpieczeństwa | Wpływ |
| - | - | - |
| Zależne od API : Możliwość wykorzystania **Średnia** | Powszechność **Powszechna** : Wykrywalność **Łatwa** | Techniczny **Poważny** : Zależny od biznesu |
| Wykorzystanie wymaga prostych żądań API. Wiele równoczesnych żądań można wysłać z jednego lokalnego komputera lub za pomocą zasobów chmury obliczeniowej. Większość dostępnych zautomatyzowanych narzędzi służy do wywoływania DoS poprzez generowanie dużego ruchu, co wpływa na wydajność obsługi API. | Często spotyka się API, które nie ograniczają interakcji klientów ani konsumpcji zasobów. Problem powinny ujawnić spreparowane żądania API, np. zawierające parametry określające liczbę zwracanych zasobów, połączone z analizą statusu, czasu i długości odpowiedzi. To samo dotyczy operacji grupowych. Choć źródła zagrożeń nie mają wglądu w skutki kosztowe, można je wywnioskować na podstawie modelu biznesowego/cennika dostawców usług (np. dostawcy chmury). | Wykorzystanie może prowadzić do DoS w wyniku zagłodzenia zasobów, ale także do wzrostu kosztów operacyjnych, np. kosztów infrastruktury wynikających z większego zapotrzebowania na CPU, rosnących potrzeb w zakresie przestrzeni dyskowej w chmurze itp. |

## Czy API jest podatne?

Obsługa żądań API wymaga zasobów, takich jak przepustowość sieci, CPU, pamięć
i przestrzeń dyskowa. Czasami wymagane zasoby są udostępniane przez dostawców
usług poprzez integracje API i opłacane za każde żądanie, np. wysyłanie
e-maili/SMS-ów/połączeń telefonicznych, weryfikacja biometryczna itp.

API jest podatne, jeśli brakuje co najmniej jednego z poniższych limitów lub
jest on ustawiony nieodpowiednio (np. zbyt nisko/wysoko):

* Limity czasu wykonania
* Maksymalna ilość pamięci możliwej do przydzielenia
* Maksymalna liczba deskryptorów plików
* Maksymalna liczba procesów
* Maksymalny rozmiar przesyłanego pliku
* Liczba operacji wykonywanych w ramach jednego żądania klienta API (np.
  grupowanie w GraphQL)
* Liczba rekordów na stronę zwracanych w ramach jednej pary żądanie-odpowiedź
* Limit wydatków u zewnętrznych dostawców usług

## Przykładowe scenariusze ataków

### Scenariusz nr 1

Serwis społecznościowy zaimplementował przepływ „Nie pamiętam hasła” oparty na
weryfikacji SMS, który pozwala użytkownikowi otrzymać token jednorazowy SMS-em
w celu zresetowania hasła.

Gdy użytkownik kliknie „Nie pamiętam hasła”, z przeglądarki użytkownika do API
backendu wysyłane jest wywołanie API:

```
POST /initiate_forgot_password

{
  "step": 1,
  "user_number": "6501113434"
}
```

Następnie, w tle, backend wysyła wywołanie API do API strony trzeciej, które
odpowiada za dostarczanie SMS-ów:

```
POST /sms/send_reset_pass_code

Host: willyo.net

{
  "phone_number": "6501113434"
}
```

Zewnętrzny dostawca, Willyo, pobiera opłatę 0,05 USD za każde wywołanie tego
typu.

Atakujący pisze skrypt, który wysyła pierwsze wywołanie API dziesiątki tysięcy
razy. Backend przekazuje żądania dalej i zleca Willyo wysłanie dziesiątek
tysięcy wiadomości SMS, przez co firma w ciągu kilku minut traci tysiące
dolarów.

### Scenariusz nr 2

Endpoint API GraphQL pozwala użytkownikowi przesłać zdjęcie profilowe.

```
POST /graphql

{
  "query": "mutation {
    uploadPic(name: \"pic1\", base64_pic: \"R0FOIEFOR0xJVA…\") {
      url
    }
  }"
}
```

Po zakończeniu przesyłania API generuje na podstawie przesłanego zdjęcia kilka
miniatur w różnych rozmiarach. Ta operacja graficzna zużywa dużo pamięci
serwera.

API stosuje tradycyjną ochronę w postaci ograniczania częstotliwości żądań:
użytkownik nie może odwoływać się do endpointu GraphQL zbyt wiele razy
w krótkim czasie. API sprawdza też rozmiar przesłanego zdjęcia przed
wygenerowaniem miniatur, aby uniknąć przetwarzania zbyt dużych zdjęć.

Atakujący może łatwo obejść te mechanizmy, wykorzystując elastyczność GraphQL:

```
POST /graphql

[
  {"query": "mutation {uploadPic(name: \"pic1\", base64_pic: \"R0FOIEFOR0xJVA…\") {url}}"},
  {"query": "mutation {uploadPic(name: \"pic2\", base64_pic: \"R0FOIEFOR0xJVA…\") {url}}"},
  ...
  {"query": "mutation {uploadPic(name: \"pic999\", base64_pic: \"R0FOIEFOR0xJVA…\") {url}}"},
}
```

Ponieważ API nie ogranicza liczby prób wykonania operacji `uploadPic`, takie
wywołanie doprowadzi do wyczerpania pamięci serwera i odmowy usługi.

### Scenariusz nr 3

Dostawca usług pozwala klientom pobierać za pomocą swojego API pliki
o dowolnie dużym rozmiarze. Pliki te są przechowywane w obiektowej pamięci
masowej w chmurze i nie zmieniają się zbyt często. Dostawca korzysta z usługi
pamięci podręcznej (cache), aby zapewnić lepszą wydajność obsługi i utrzymać
niskie zużycie przepustowości. Usługa pamięci podręcznej przechowuje wyłącznie
pliki o rozmiarze do 15 GB.

Gdy jeden z plików zostaje zaktualizowany, jego rozmiar wzrasta do 18 GB.
Wszyscy klienci usługi natychmiast zaczynają pobierać nową wersję. Ponieważ nie
skonfigurowano alertów dotyczących kosztów zużycia ani maksymalnego limitu
kosztów dla usługi chmurowej, kolejny miesięczny rachunek wzrasta ze średnio
13 USD do 8 tys. USD.

## Jak zapobiegać

* Stosuj rozwiązanie, które ułatwia ograniczanie [pamięci][1], [CPU][2],
  [liczby ponownych uruchomień][3], [deskryptorów plików i procesów][4], np.
  kontenery lub kod bezserwerowy (np. funkcje Lambda).
* Zdefiniuj i egzekwuj maksymalny rozmiar danych dla wszystkich parametrów
  wejściowych i ładunków, np. maksymalną długość ciągów znaków, maksymalną
  liczbę elementów w tablicach oraz maksymalny rozmiar przesyłanego pliku
  (niezależnie od tego, czy jest on przechowywany lokalnie, czy w chmurze).
* Zaimplementuj limit częstotliwości, z jaką klient może wchodzić w interakcje
  z API w określonym przedziale czasu (ograniczanie częstotliwości żądań).
* Ograniczanie częstotliwości żądań należy precyzyjnie dostroić do potrzeb
  biznesowych. Niektóre endpointy API mogą wymagać bardziej restrykcyjnych
  zasad.
* Ograniczaj lub dław (throttling) liczbę i częstotliwość wykonywania
  pojedynczej operacji przez jednego klienta/użytkownika API (np. weryfikacji
  OTP lub żądania odzyskania hasła bez odwiedzenia jednorazowego adresu URL).
* Dodaj właściwą walidację po stronie serwera dla parametrów ciągu zapytania
  i treści żądania, zwłaszcza parametru określającego liczbę rekordów
  zwracanych w odpowiedzi.
* Skonfiguruj limity wydatków dla wszystkich dostawców usług/integracji API.
  Jeśli ustawienie limitów wydatków nie jest możliwe, należy zamiast tego
  skonfigurować alerty rozliczeniowe.

## Źródła

### OWASP

* ["Availability" - Web Service Security Cheat Sheet][5]
* ["DoS Prevention" - GraphQL Cheat Sheet][6]
* ["Mitigating Batching Attacks" - GraphQL Cheat Sheet][7]

### Zewnętrzne

* [CWE-770: Allocation of Resources Without Limits or Throttling][8]
* [CWE-400: Uncontrolled Resource Consumption][9]
* [CWE-799: Improper Control of Interaction Frequency][10]
* "Rate Limiting (Throttling)" - [Security Strategies for Microservices-based
  Application Systems][11], NIST

[1]: https://docs.docker.com/config/containers/resource_constraints/#memory
[2]: https://docs.docker.com/config/containers/resource_constraints/#cpu
[3]: https://docs.docker.com/engine/reference/commandline/run/#restart
[4]: https://docs.docker.com/engine/reference/commandline/run/#ulimit
[5]: https://cheatsheetseries.owasp.org/cheatsheets/Web_Service_Security_Cheat_Sheet.html#availability
[6]: https://cheatsheetseries.owasp.org/cheatsheets/GraphQL_Cheat_Sheet.html#dos-prevention
[7]: https://cheatsheetseries.owasp.org/cheatsheets/GraphQL_Cheat_Sheet.html#mitigating-batching-attacks
[8]: https://cwe.mitre.org/data/definitions/770.html
[9]: https://cwe.mitre.org/data/definitions/400.html
[10]: https://cwe.mitre.org/data/definitions/799.html
[11]: https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-204.pdf
