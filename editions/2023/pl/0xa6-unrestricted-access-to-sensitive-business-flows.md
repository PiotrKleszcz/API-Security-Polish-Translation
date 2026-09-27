# API6:2023 Nieograniczony dostęp do wrażliwych przepływów biznesowych

| Źródła zagrożeń/Wektory ataku | Słaby punkt bezpieczeństwa | Wpływ |
| - | - | - |
| Zależne od API : Możliwość wykorzystania **Łatwa** | Powszechność **Powszechna** : Wykrywalność **Średnia** | Techniczny **Umiarkowany** : Zależny od biznesu |
| Wykorzystanie zwykle polega na zrozumieniu modelu biznesowego, na którym opiera się API, znalezieniu wrażliwych przepływów biznesowych i zautomatyzowaniu dostępu do nich, co szkodzi firmie. | Do powszechności tego problemu przyczynia się zwykle brak całościowego spojrzenia na API, które pozwoliłoby w pełni uwzględnić wymagania biznesowe. Atakujący ręcznie ustalają, jakie zasoby (np. endpointy) biorą udział w docelowym procesie i jak ze sobą współdziałają. Jeśli mechanizmy ochronne są już wdrożone, atakujący muszą znaleźć sposób na ich obejście. | Zasadniczo nie należy spodziewać się wpływu technicznego. Wykorzystanie może zaszkodzić firmie na różne sposoby, np. uniemożliwić uprawnionym użytkownikom zakup produktu lub doprowadzić do inflacji w wewnętrznej ekonomii gry. |

## Czy API jest podatne?

Tworząc endpoint API, należy zrozumieć, jaki przepływ biznesowy on udostępnia.
Niektóre przepływy biznesowe są bardziej wrażliwe niż inne w tym sensie, że
nadmierny dostęp do nich może zaszkodzić firmie.

Typowe przykłady wrażliwych przepływów biznesowych i związanego z nimi ryzyka
nadmiernego dostępu:

* Przepływ zakupu produktu - atakujący może wykupić od razu cały zapas
  poszukiwanego towaru i odsprzedać go po wyższej cenie (scalping)
* Przepływ tworzenia komentarza/wpisu - atakujący może zalać system spamem
* Dokonywanie rezerwacji - atakujący może zarezerwować wszystkie dostępne
  terminy i uniemożliwić innym użytkownikom korzystanie z systemu

Ryzyko nadmiernego dostępu może się różnić w zależności od branży i firmy.
Na przykład tworzenie wpisów przez skrypt może być uznawane przez jeden serwis
społecznościowy za ryzyko spamu, a przez inny serwis społecznościowy
zachęcane.

Endpoint API jest podatny, jeśli udostępnia wrażliwy przepływ biznesowy bez
odpowiedniego ograniczenia dostępu do niego.

## Przykładowe scenariusze ataków

### Scenariusz nr 1

Firma technologiczna ogłasza, że w Święto Dziękczynienia wprowadzi na rynek
nową konsolę do gier. Popyt na produkt jest bardzo wysoki, a zapas ograniczony.
Atakujący pisze kod, który automatycznie kupuje nowy produkt i finalizuje
transakcję.

W dniu premiery atakujący uruchamia kod rozproszony na różne adresy IP
i lokalizacje. API nie implementuje odpowiedniej ochrony i pozwala
atakującemu wykupić większość zapasu, zanim zrobią to inni, uprawnieni
użytkownicy.

Następnie atakujący sprzedaje produkt na innej platformie po znacznie wyższej
cenie.

### Scenariusz nr 2

Linia lotnicza oferuje zakup biletów online bez opłaty za anulowanie.
Użytkownik o złośliwych zamiarach rezerwuje 90% miejsc w wybranym locie.

Kilka dni przed lotem złośliwy użytkownik anuluje jednocześnie wszystkie
bilety, co zmusza linię lotniczą do obniżenia cen biletów, aby zapełnić
samolot.

W tym momencie użytkownik kupuje dla siebie jeden bilet, znacznie tańszy od
pierwotnego.

### Scenariusz nr 3

Aplikacja do przewozu osób (ride-sharing) oferuje program poleceń: użytkownicy
mogą zapraszać znajomych i otrzymywać środki za każdego znajomego, który
dołączył do aplikacji. Środki te można później wykorzystać jak gotówkę do
zamawiania przejazdów.

Atakujący wykorzystuje ten przepływ, pisząc skrypt automatyzujący proces
rejestracji, w którym każdy nowy użytkownik dodaje środki do portfela
atakującego.

Atakujący może później korzystać z darmowych przejazdów lub sprzedawać konta
z nadmiarowymi środkami za gotówkę.

## Jak zapobiegać

Planowanie ochrony powinno odbywać się na dwóch poziomach:

* Biznesowym - zidentyfikuj przepływy biznesowe, które mogą zaszkodzić
  firmie, jeśli będą nadmiernie wykorzystywane.
* Inżynieryjnym - wybierz odpowiednie mechanizmy ochronne, aby ograniczyć
  ryzyko biznesowe.

    Niektóre mechanizmy ochronne są prostsze, a inne trudniejsze do
    wdrożenia. Do spowalniania zautomatyzowanych zagrożeń stosuje się
    następujące metody:

    * Fingerprinting urządzeń: odmowa obsługi nieoczekiwanych urządzeń
      klienckich (np. przeglądarek headless) zwykle zmusza atakujących do
      stosowania bardziej zaawansowanych, a więc dla nich droższych rozwiązań
    * Wykrywanie człowieka: stosowanie mechanizmu captcha lub bardziej
      zaawansowanych rozwiązań biometrycznych (np. wzorców pisania na
      klawiaturze)
    * Wzorce zachowań niecharakterystyczne dla człowieka: analizuj przepływ
      użytkownika, aby wykryć takie wzorce (np. użytkownik użył funkcji
      „dodaj do koszyka” i „sfinalizuj zakup” w czasie krótszym niż jedna
      sekunda)
    * Rozważ blokowanie adresów IP węzłów wyjściowych sieci Tor i znanych
      serwerów proxy

    Zabezpieczaj i ograniczaj dostęp do API konsumowanych bezpośrednio przez
    maszyny (np. API dla programistów i API B2B). Są one zwykle łatwym celem
    dla atakujących, ponieważ często nie implementują wszystkich wymaganych
    mechanizmów ochronnych.

## Źródła

### OWASP

* [OWASP Automated Threats to Web Applications][1]
* [API10:2019 Insufficient Logging & Monitoring][2]

[1]: https://owasp.org/www-project-automated-threats-to-web-applications/
[2]: https://owasp.org/API-Security/editions/2019/en/0xaa-insufficient-logging-monitoring/
