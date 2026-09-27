# Metodologia i dane

## Przegląd

Przy aktualizacji tej listy zespół OWASP API Security zastosował tę samą
metodologię, co w przypadku udanej i szeroko przyjętej listy z 2019 roku,
uzupełnioną o trzymiesięczne [publiczne wezwanie do przekazywania danych][1].
Niestety wezwanie to nie przyniosło danych, które umożliwiłyby miarodajną
analizę statystyczną najczęstszych problemów bezpieczeństwa API.

Jednak dzięki dojrzalszej branży bezpieczeństwa API, zdolnej do przekazywania
bezpośrednich opinii i spostrzeżeń, proces aktualizacji kontynuowano, stosując
tę samą metodologię co wcześniej.

Na tym etapie uważamy, że mamy dobry, perspektywiczny dokument podnoszący
świadomość na kolejne trzy lub cztery lata, bardziej skoncentrowany na
problemach specyficznych dla nowoczesnych API. Celem tego projektu nie jest
zastąpienie innych list Top 10, lecz omówienie istniejących i nadchodzących
najważniejszych ryzyk bezpieczeństwa API, które naszym zdaniem branża powinna
znać i na które powinna zwracać szczególną uwagę.

## Metodologia

W pierwszej fazie zebrano, przeanalizowano i skategoryzowano publicznie
dostępne dane o incydentach bezpieczeństwa API. Dane te pochodziły z platform
bug bounty i publicznie dostępnych raportów. Uwzględniono wyłącznie problemy
zgłoszone w latach 2019–2022. Dane te pozwoliły zespołowi zorientować się,
w jakim kierunku powinna ewoluować poprzednia lista Top 10, a także pomogły
przeciwdziałać ewentualnym zniekształceniom w przekazanych danych.

Publiczne [wezwanie do przekazywania danych][1] trwało od 1 września do
30 listopada 2022 r. Równolegle zespół projektu rozpoczął dyskusję na temat
tego, co zmieniło się od 2019 roku. Dyskusja obejmowała wpływ pierwszej listy,
opinie otrzymane od społeczności oraz nowe trendy w bezpieczeństwie API.

Zespół projektu zorganizował spotkania ze specjalistami od istotnych zagrożeń
bezpieczeństwa API, aby dowiedzieć się, jaki wpływ mają one na ofiary i jak
można je ograniczać.

W wyniku tych prac powstał wstępny projekt listy dziesięciu najbardziej
krytycznych, zdaniem zespołu, ryzyk bezpieczeństwa API. Do przeprowadzenia
analizy ryzyka wykorzystano [OWASP Risk Rating Methodology][2]. Oceny
powszechności ustalono w drodze konsensusu członków zespołu projektu, na
podstawie ich doświadczenia w tej dziedzinie. Rozważania na ten temat
znajdziesz w sekcji [Ryzyka bezpieczeństwa API][3].

Wstępny projekt został następnie przekazany do przeglądu praktykom
bezpieczeństwa z odpowiednim doświadczeniem w obszarze bezpieczeństwa API.
Ich uwagi zostały przeanalizowane, omówione i, tam gdzie miało to
zastosowanie, uwzględnione w dokumencie. Powstały dokument został
[opublikowany jako wersja kandydująca (Release Candidate)][4] do
[otwartej dyskusji][5]. Do ostatecznej wersji dokumentu włączono kilka
[propozycji zgłoszonych przez społeczność][6].

Lista współtwórców znajduje się w sekcji [Podziękowania][7].

## Ryzyka specyficzne dla API

Lista została opracowana z myślą o ryzykach bezpieczeństwa, które są bardziej
specyficzne dla API.

Nie oznacza to, że w aplikacjach opartych na API nie występują inne, ogólne
ryzyka bezpieczeństwa aplikacji. Na przykład nie uwzględniliśmy ryzyk takich
jak „Podatne i przestarzałe komponenty” (Vulnerable and Outdated Components)
czy „Wstrzyknięcia” (Injection), choć można je spotkać w aplikacjach opartych
na API. Są to ryzyka ogólne: w API nie zachowują się inaczej, a ich
wykorzystanie nie przebiega inaczej.

Naszym celem jest zwiększenie świadomości na temat ryzyk bezpieczeństwa, które
w przypadku API zasługują na szczególną uwagę.

[1]: https://owasp.org/www-project-api-security/announcements/cfd/2022/
[2]: https://www.owasp.org/index.php/OWASP_Risk_Rating_Methodology
[3]: ./0x10-api-security-risks.md
[4]: https://owasp.org/www-project-api-security/announcements/2023/02/api-top10-2023rc
[5]: https://github.com/OWASP/API-Security/issues?q=is%3Aissue+label%3A2023RC
[6]: https://github.com/OWASP/API-Security/pulls?q=is%3Apr+label%3A2023RC
[7]: ./0xd1-acknowledgments.md
