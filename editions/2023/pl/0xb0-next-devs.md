# Co dalej dla programistów

Tworzenie i utrzymywanie bezpiecznych aplikacji, a także naprawianie
istniejących aplikacji, może być trudnym zadaniem. W przypadku API jest
podobnie.

Wierzymy, że edukacja i świadomość są kluczowymi czynnikami w tworzeniu
bezpiecznego oprogramowania. Wszystko inne, co jest potrzebne do osiągnięcia
tego celu, zależy od **wprowadzenia i stosowania powtarzalnych procesów
bezpieczeństwa oraz standardowych mechanizmów bezpieczeństwa**.

OWASP udostępnia wiele bezpłatnych i otwartych zasobów, które pomagają
zadbać o bezpieczeństwo. Pełną listę dostępnych projektów znajdziesz na
[stronie projektów OWASP][1].

| | |
|-|-|
| **Edukacja** | [Application Security Wayfinder][2] pozwala zorientować się, jakie projekty są dostępne na poszczególnych etapach/fazach cyklu życia wytwarzania oprogramowania (Software Development LifeCycle, SDLC). Praktyczną naukę/szkolenie możesz zacząć od [OWASP **crAPI** - **C**ompletely **R**idiculous **API**][3] lub [OWASP Juice Shop][4]: oba projekty mają celowo podatne API. [OWASP Vulnerable Web Applications Directory Project][5] zawiera starannie dobraną listę celowo podatnych aplikacji, wśród których znajdziesz kilka innych podatnych API. Możesz też wziąć udział w szkoleniach podczas [konferencji OWASP AppSec][6] lub [dołączyć do lokalnego oddziału][7]. |
| **Wymagania bezpieczeństwa** | Bezpieczeństwo powinno być częścią każdego projektu od samego początku. Definiując wymagania, należy określić, co dla danego projektu oznacza „bezpieczny”. OWASP zaleca stosowanie [OWASP Application Security Verification Standard (ASVS)][8] jako wytycznych przy ustalaniu wymagań bezpieczeństwa. Jeśli zlecasz prace na zewnątrz, rozważ [OWASP Secure Software Contract Annex][9], który należy dostosować do lokalnego prawa i przepisów. |
| **Architektura bezpieczeństwa** | Bezpieczeństwo powinno pozostawać w centrum uwagi na wszystkich etapach projektu. [OWASP Cheat Sheet Series][10] to dobry punkt wyjścia do wskazówek, jak wbudować bezpieczeństwo w projekt na etapie architektury. Wśród wielu innych znajdziesz tam [REST Security Cheat Sheet][11], [REST Assessment Cheat Sheet][12], a także [GraphQL Cheat Sheet][13]. |
| **Standardowe mechanizmy bezpieczeństwa** | Stosowanie standardowych mechanizmów bezpieczeństwa zmniejsza ryzyko wprowadzenia słabych punktów bezpieczeństwa podczas pisania własnej logiki. Choć wiele nowoczesnych frameworków ma obecnie skuteczne, wbudowane standardowe mechanizmy, [OWASP Proactive Controls][14] daje dobry przegląd tego, jakie mechanizmy bezpieczeństwa warto uwzględnić w projekcie. OWASP udostępnia też biblioteki i narzędzia, które mogą okazać się przydatne, np. mechanizmy walidacji. |
| **Bezpieczny cykl życia wytwarzania oprogramowania** | Do usprawnienia procesów tworzenia API możesz wykorzystać [OWASP Software Assurance Maturity Model (SAMM)][15]. Na różnych etapach tworzenia API mogą pomóc także inne projekty OWASP, np. [OWASP Code Review Guide][16]. |

[1]: https://owasp.org/projects/
[2]: https://owasp.org/projects/#owasp-projects-the-sdlc-and-the-security-wayfinder
[3]: https://owasp.org/www-project-crapi/
[4]: https://owasp.org/www-project-juice-shop/
[5]: https://owasp.org/www-project-vulnerable-web-applications-directory/
[6]: https://owasp.org/events/
[7]: https://owasp.org/chapters/
[8]: https://owasp.org/www-project-application-security-verification-standard/
[9]: https://owasp.org/www-community/OWASP_Secure_Software_Contract_Annex
[10]: https://cheatsheetseries.owasp.org/
[11]: https://cheatsheetseries.owasp.org/cheatsheets/REST_Security_Cheat_Sheet.html
[12]: https://cheatsheetseries.owasp.org/cheatsheets/REST_Assessment_Cheat_Sheet.html
[13]: https://cheatsheetseries.owasp.org/cheatsheets/GraphQL_Cheat_Sheet.html
[14]: https://owasp.org/www-project-proactive-controls/
[15]: https://owasp.org/www-project-samm/
[16]: https://owasp.org/www-project-code-review-guide/
