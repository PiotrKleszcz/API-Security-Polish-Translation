# API9:2023 Niewłaściwe zarządzanie inwentarzem

| Źródła zagrożeń/Wektory ataku | Słaby punkt bezpieczeństwa | Wpływ |
| - | - | - |
| Zależne od API : Możliwość wykorzystania **Łatwa** | Powszechność **Powszechna** : Wykrywalność **Średnia** | Techniczny **Umiarkowany** : Zależny od biznesu |
| Źródła zagrożeń zwykle uzyskują nieautoryzowany dostęp przez stare wersje API lub endpointy, które nadal działają bez poprawek i stosują słabsze wymagania bezpieczeństwa. W niektórych przypadkach dostępne są gotowe exploity. Mogą też uzyskać dostęp do danych wrażliwych za pośrednictwem strony trzeciej, z którą nie ma powodu udostępniać danych. | Nieaktualna dokumentacja utrudnia znajdowanie i/lub usuwanie podatności. Brak inwentarza zasobów i strategii wycofywania z użytku prowadzi do utrzymywania niezałatanych systemów, co skutkuje wyciekiem danych wrażliwych. Często spotyka się niepotrzebnie udostępnione hosty API, co wynika z nowoczesnych koncepcji, takich jak mikrousługi, które ułatwiają wdrażanie aplikacji i czynią je niezależnymi (np. chmura obliczeniowa, K8S). Do wykrycia celów wystarczy proste Google Dorking, enumeracja DNS lub użycie wyspecjalizowanych wyszukiwarek różnych typów serwerów (kamer internetowych, routerów, serwerów itp.) podłączonych do internetu. | Atakujący mogą uzyskać dostęp do danych wrażliwych, a nawet przejąć serwer. Czasami różne wersje/wdrożenia API są połączone z tą samą bazą danych zawierającą rzeczywiste dane. Źródła zagrożeń mogą wykorzystywać przestarzałe endpointy dostępne w starych wersjach API, aby uzyskać dostęp do funkcji administracyjnych lub wykorzystać znane podatności. |

## Czy API jest podatne?

Rozproszony i silnie powiązany charakter API oraz nowoczesnych aplikacji
stwarza nowe wyzwania. Organizacje muszą nie tylko dobrze rozumieć i widzieć
własne API i endpointy API, ale także wiedzieć, w jaki sposób API przechowują
lub udostępniają dane zewnętrznym stronom trzecim.

Utrzymywanie wielu wersji API wymaga od dostawcy API dodatkowych zasobów
do zarządzania i zwiększa powierzchnię ataku.

API ma „<ins>martwe pole w dokumentacji</ins>”, jeśli:

* Przeznaczenie hosta API jest niejasne i brakuje jednoznacznych odpowiedzi
  na następujące pytania
    * W jakim środowisku działa API (np. produkcyjnym, przedprodukcyjnym
      (staging), testowym, deweloperskim)?
    * Kto powinien mieć dostęp sieciowy do API (np. publiczny, wewnętrzny,
      partnerzy)?
    * Która wersja API jest uruchomiona?
* Brakuje dokumentacji lub istniejąca dokumentacja nie jest aktualizowana.
* Brakuje planu wycofania z użytku dla każdej wersji API.
* Brakuje inwentarza hostów lub jest on nieaktualny.

Widoczność i inwentarz przepływów danych wrażliwych odgrywają ważną rolę
w ramach planu reagowania na incydenty, na wypadek gdyby do naruszenia doszło
po stronie strony trzeciej.

API ma „<ins>martwe pole w przepływach danych</ins>”, jeśli:

* Istnieje „przepływ danych wrażliwych”, w ramach którego API udostępnia dane
  wrażliwe stronie trzeciej, oraz
    * Brakuje uzasadnienia biznesowego lub zatwierdzenia tego przepływu
    * Brakuje inwentarza lub widoczności tego przepływu
    * Brakuje dokładnej wiedzy o tym, jakie typy danych wrażliwych są
      udostępniane

## Przykładowe scenariusze ataków

### Scenariusz nr 1

Serwis społecznościowy zaimplementował mechanizm ograniczania częstotliwości
żądań, który uniemożliwia atakującym odgadywanie metodą siłową tokenów
resetowania hasła. Mechanizm ten nie został zaimplementowany w samym kodzie
API, lecz w osobnym komponencie między klientem a oficjalnym API
(`api.socialnetwork.owasp.org`). Badacz znalazł host API w wersji beta
(`beta.api.socialnetwork.owasp.org`), na którym działa to samo API, łącznie
z mechanizmem resetowania hasła, ale bez mechanizmu ograniczania częstotliwości
żądań. Badacz był w stanie zresetować hasło dowolnego użytkownika, odgadując
metodą siłową 6-cyfrowy token.

### Scenariusz nr 2

Serwis społecznościowy pozwala twórcom niezależnych aplikacji na integrację
z nim. W ramach tego procesu od użytkownika końcowego wymagana jest zgoda, aby
serwis społecznościowy mógł udostępnić dane osobowe użytkownika niezależnej
aplikacji.

Przepływ danych między serwisem społecznościowym a niezależnymi aplikacjami
nie jest wystarczająco ograniczony ani monitorowany, co pozwala niezależnym
aplikacjom uzyskać dostęp nie tylko do danych użytkownika, ale także do
prywatnych danych wszystkich jego znajomych.

Firma konsultingowa tworzy złośliwą aplikację i uzyskuje zgodę
270 000 użytkowników. Z powodu tego błędu firma konsultingowa uzyskuje dostęp
do prywatnych danych 50 000 000 użytkowników. Następnie firma konsultingowa
sprzedaje te dane w złośliwych celach.

## Jak zapobiegać

* Zinwentaryzuj wszystkie <ins>hosty API</ins> i udokumentuj ważne aspekty
  każdego z nich, koncentrując się na środowisku API (np. produkcyjnym,
  przedprodukcyjnym (staging), testowym, deweloperskim), na tym, kto powinien
  mieć dostęp sieciowy do hosta (np. publiczny, wewnętrzny, partnerzy), oraz
  na wersji API.
* Zinwentaryzuj <ins>usługi zintegrowane</ins> i udokumentuj ważne aspekty,
  takie jak ich rola w systemie, jakie dane są wymieniane (przepływ danych) oraz
  ich wrażliwość.
* Udokumentuj wszystkie aspekty API, takie jak uwierzytelnianie, błędy,
  przekierowania, ograniczanie częstotliwości żądań, polityka cross-origin
  resource sharing (CORS) i endpointy, łącznie z ich parametrami, żądaniami
  i odpowiedziami.
* Generuj dokumentację automatycznie, stosując otwarte standardy. Uwzględnij
  budowanie dokumentacji w swoim potoku CI/CD.
* Udostępniaj dokumentację API wyłącznie osobom uprawnionym do korzystania
  z API.
* Stosuj zewnętrzne środki ochrony, takie jak rozwiązania przeznaczone
  specjalnie do zabezpieczania API, dla wszystkich udostępnionych wersji API,
  a nie tylko dla bieżącej wersji produkcyjnej.
* Unikaj używania danych produkcyjnych we wdrożeniach API innych niż
  produkcyjne. Jeśli nie da się tego uniknąć, takie endpointy powinny być
  zabezpieczone tak samo jak produkcyjne.
* Gdy nowsze wersje API zawierają usprawnienia bezpieczeństwa, przeprowadź
  analizę ryzyka, aby określić działania ochronne wymagane dla starszych
  wersji. Na przykład, czy można przenieść usprawnienia do starszych wersji
  (backport) bez naruszania zgodności API, czy też trzeba szybko wycofać
  starszą wersję i zmusić wszystkich klientów do przejścia na najnowszą.

## Źródła

### OWASP

* [REST Security Cheat Sheet][2]

### Zewnętrzne

* [CWE-1059: Incomplete Documentation][1]
* "Inventory Management" - [Security Strategies for Microservices-based
  Application Systems][3], NIST

[1]: https://cwe.mitre.org/data/definitions/1059.html
[2]: https://cheatsheetseries.owasp.org/cheatsheets/REST_Security_Cheat_Sheet.html
[3]: https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-204.pdf
