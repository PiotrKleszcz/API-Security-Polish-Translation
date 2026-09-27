# API5:2023 Niepoprawna autoryzacja na poziomie funkcji

| Źródła zagrożeń/Wektory ataku | Słaby punkt bezpieczeństwa | Wpływ |
| - | - | - |
| Zależne od API : Możliwość wykorzystania **Łatwa** | Powszechność **Częsta** : Wykrywalność **Łatwa** | Techniczny **Poważny** : Zależny od biznesu |
| Wykorzystanie wymaga od atakującego wysłania prawidłowych wywołań API do endpointu API, do którego jako użytkownik anonimowy lub zwykły, nieuprzywilejowany użytkownik nie powinien mieć dostępu. Udostępnione endpointy będą łatwe do wykorzystania. | Kontrole autoryzacji dla funkcji lub zasobu są zwykle zarządzane na poziomie konfiguracji lub kodu. Implementacja właściwych kontroli może być skomplikowanym zadaniem, ponieważ nowoczesne aplikacje mogą zawierać wiele typów ról, grup i złożone hierarchie użytkowników (np. podużytkowników lub użytkowników z więcej niż jedną rolą). Takie błędy łatwiej wykryć w API, ponieważ API mają bardziej uporządkowaną strukturę, a dostęp do różnych funkcji jest bardziej przewidywalny. | Takie błędy pozwalają atakującym uzyskać dostęp do nieautoryzowanych funkcjonalności. Funkcje administracyjne są głównym celem tego typu ataków, a ich wykorzystanie może prowadzić do ujawnienia, utraty lub uszkodzenia danych. Ostatecznie może to prowadzić do zakłócenia działania usługi. |

## Czy API jest podatne?

Najlepszym sposobem na wykrycie problemów z niepoprawną autoryzacją na poziomie
funkcji jest przeprowadzenie dogłębnej analizy mechanizmu autoryzacji
z uwzględnieniem hierarchii użytkowników oraz różnych ról lub grup
w aplikacji, a także zadanie następujących pytań:

* Czy zwykły użytkownik może uzyskać dostęp do endpointów administracyjnych?
* Czy użytkownik może wykonać wrażliwe działania (np. utworzenie, modyfikację
  lub usunięcie), do których nie powinien mieć dostępu, po prostu zmieniając
  metodę HTTP (np. z `GET` na `DELETE`)?
* Czy użytkownik z grupy X może uzyskać dostęp do funkcji, która powinna być
  udostępniona wyłącznie użytkownikom z grupy Y, po prostu odgadując URL
  endpointu i parametry (np. `/api/v1/users/export_all`)?

Nie zakładaj, że endpoint API jest zwykły lub administracyjny wyłącznie na
podstawie ścieżki URL.

Choć programiści mogą udostępniać większość endpointów administracyjnych pod
określoną ścieżką względną, np. `/api/admins`, bardzo często spotyka się
endpointy administracyjne pod innymi ścieżkami względnymi, razem ze zwykłymi
endpointami, np. `/api/users`.

## Przykładowe scenariusze ataków

### Scenariusz nr 1

Podczas procesu rejestracji w aplikacji, do której mogą dołączyć wyłącznie
zaproszeni użytkownicy, aplikacja mobilna wysyła wywołanie API do
`GET /api/invites/{invite_guid}`. Odpowiedź zawiera obiekt JSON ze
szczegółami zaproszenia, w tym rolą i adresem e-mail użytkownika.

Atakujący powiela żądanie i zmienia metodę HTTP oraz endpoint na
`POST /api/invites/new`. Z tego endpointu powinni korzystać wyłącznie
administratorzy za pośrednictwem konsoli administracyjnej. Endpoint nie
implementuje kontroli autoryzacji na poziomie funkcji.

Atakujący wykorzystuje ten problem i wysyła nowe zaproszenie z uprawnieniami
administratora:

```
POST /api/invites/new

{
  "email": "attacker@somehost.com",
  "role":"admin"
}
```

Następnie atakujący wykorzystuje złośliwie spreparowane zaproszenie, aby
utworzyć dla siebie konto administratora i uzyskać pełny dostęp do systemu.

### Scenariusz nr 2

API zawiera endpoint, który powinien być udostępniony wyłącznie
administratorom: `GET /api/admin/v1/users/all`. Ten endpoint zwraca dane
wszystkich użytkowników aplikacji i nie implementuje kontroli autoryzacji na
poziomie funkcji. Atakujący, który poznał strukturę API, trafnie zgaduje adres
i uzyskuje dostęp do tego endpointu, który ujawnia wrażliwe dane użytkowników
aplikacji.

## Jak zapobiegać

Aplikacja powinna mieć spójny i łatwy do przeanalizowania moduł autoryzacji,
wywoływany ze wszystkich funkcji biznesowych. Często taką ochronę zapewnia co
najmniej jeden komponent zewnętrzny względem kodu aplikacji.

* Mechanizmy egzekwowania autoryzacji powinny domyślnie odmawiać wszelkiego
  dostępu i wymagać jawnego przyznania uprawnień określonym rolom do każdej
  funkcji.
* Przeglądaj endpointy API pod kątem błędów autoryzacji na poziomie funkcji,
  uwzględniając logikę biznesową aplikacji i hierarchię grup.
* Upewnij się, że wszystkie kontrolery administracyjne dziedziczą po
  abstrakcyjnym kontrolerze administracyjnym, który implementuje kontrole
  autoryzacji na podstawie grupy/roli użytkownika.
* Upewnij się, że funkcje administracyjne w zwykłym kontrolerze implementują
  kontrole autoryzacji na podstawie grupy i roli użytkownika.

## Źródła

### OWASP

* [Forced Browsing][1]
* "A7: Missing Function Level Access Control", [OWASP Top 10 2013][2]
* [Access Control][3]

### Zewnętrzne

* [CWE-285: Improper Authorization][4]

[1]: https://owasp.org/www-community/attacks/Forced_browsing
[2]: https://github.com/OWASP/Top10/raw/master/2013/OWASP%20Top%2010%20-%202013.pdf
[3]: https://owasp.org/www-community/Access_Control
[4]: https://cwe.mitre.org/data/definitions/285.html
