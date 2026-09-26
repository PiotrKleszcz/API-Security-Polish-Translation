# Informacje o wersji

To druga edycja OWASP API Security Top 10, opublikowana dokładnie cztery lata
po pierwszym wydaniu. W świecie API (i ich bezpieczeństwa) wiele się zmieniło.
Ruch API rósł w szybkim tempie, niektóre protokoły API zyskały znacznie większą
popularność, pojawiło się wielu nowych dostawców i rozwiązań z zakresu
bezpieczeństwa API, a atakujący oczywiście rozwinęli nowe umiejętności
i techniki kompromitowania API. Nadszedł czas, aby zaktualizować listę
dziesięciu najbardziej krytycznych ryzyk bezpieczeństwa API.

Wraz z dojrzewaniem branży bezpieczeństwa API po raz pierwszy ogłoszono
[publiczne wezwanie do przekazywania danych][1]. Niestety nie przekazano żadnych
danych, dlatego nową listę opracowaliśmy na podstawie doświadczenia zespołu
projektu, starannego przeglądu przeprowadzonego przez specjalistów od
bezpieczeństwa API oraz opinii społeczności na temat wersji kandydującej
(release candidate). W [sekcji Metodologia i dane][2] znajdziesz więcej
szczegółów na temat tego, jak powstała ta wersja. Więcej informacji
o poszczególnych ryzykach bezpieczeństwa znajdziesz w [sekcji Ryzyka
bezpieczeństwa API][3].

OWASP API Security Top 10 2023 to dokument, który z wyprzedzeniem podnosi
świadomość w szybko rozwijającej się branży. Nie zastępuje on innych list
Top 10. W tej edycji:

* Połączyliśmy kategorie „Nadmierne ujawnianie danych” (Excessive Data
  Exposure) i Mass Assignment, koncentrując się na ich wspólnej przyczynie
  źródłowej: błędach walidacji autoryzacji na poziomie właściwości obiektu.
* Położyliśmy większy nacisk na konsumpcję zasobów, zamiast skupiać się na
  tempie ich wyczerpywania.
* Utworzyliśmy nową kategorię „Nieograniczony dostęp do wrażliwych przepływów
  biznesowych”, aby uwzględnić nowe zagrożenia, w tym większość tych, którym
  można przeciwdziałać za pomocą ograniczania częstotliwości żądań.
* Dodaliśmy kategorię „Niebezpieczna konsumpcja API”, aby odnieść się do
  zjawiska, które zaczęliśmy obserwować: atakujący zaczęli szukać usług
  zintegrowanych z celem ataku, aby je skompromitować, zamiast atakować
  bezpośrednio API swojego celu. To właściwy moment, aby zacząć podnosić
  świadomość na temat tego rosnącego ryzyka.

API odgrywają coraz ważniejszą rolę we współczesnej architekturze mikrousług,
aplikacjach jednostronicowych (Single Page Applications, SPA), aplikacjach
mobilnych, IoT itp. OWASP API Security Top 10 to niezbędne przedsięwzięcie
służące podnoszeniu świadomości na temat współczesnych problemów
bezpieczeństwa API.

Ta aktualizacja była możliwa wyłącznie dzięki ogromnemu wysiłkowi wielu
wolontariuszy wymienionych w sekcji [Podziękowania][4].

Dziękujemy!

[1]: https://owasp.org/www-project-api-security/announcements/cfd/2022/
[2]: ./0xd0-about-data.md
[3]: ./0x10-api-security-risks.md
[4]: ./0xd1-acknowledgments.md
