# Co dalej dla DevSecOps

Ze względu na znaczenie API w nowoczesnych architekturach aplikacji tworzenie
bezpiecznych API ma kluczowe znaczenie. Bezpieczeństwa nie można zaniedbywać
i powinno ono być częścią całego cyklu życia wytwarzania oprogramowania.
Coroczne skanowanie i testy penetracyjne już nie wystarczają.

Specjaliści DevSecOps powinni włączyć się w prace nad wytwarzaniem
oprogramowania, umożliwiając ciągłe testowanie bezpieczeństwa w całym cyklu
życia wytwarzania oprogramowania. Celem powinno być wzbogacenie potoku
wytwarzania oprogramowania o automatyzację bezpieczeństwa, bez wpływu na
tempo prac.

W razie wątpliwości bądź na bieżąco i sięgnij do [DevSecOps Manifesto][1].

| | |
|-|-|
| **Zrozum model zagrożeń** | Priorytety testowania wynikają z modelu zagrożeń. Jeśli go nie masz, rozważ wykorzystanie [OWASP Application Security Verification Standard (ASVS)][2] oraz [OWASP Testing Guide][3] jako danych wejściowych. Zaangażowanie zespołu programistów pomoże zwiększyć jego świadomość w zakresie bezpieczeństwa. |
| **Zrozum SDLC** | Dołącz do zespołu programistów, aby lepiej zrozumieć cykl życia wytwarzania oprogramowania. Twój wkład w ciągłe testowanie bezpieczeństwa powinien być dopasowany do ludzi, procesów i narzędzi. Wszyscy powinni zgadzać się co do procesu, aby uniknąć zbędnych tarć i oporu. |
| **Strategie testowania** | Ponieważ Twoja praca nie powinna wpływać na tempo wytwarzania oprogramowania, rozważnie wybieraj najlepszą (prostą, najszybszą, najdokładniejszą) technikę weryfikacji wymagań bezpieczeństwa. [OWASP Security Knowledge Framework][4] i [OWASP Application Security Verification Standard][2] mogą być doskonałymi źródłami funkcjonalnych i niefunkcjonalnych wymagań bezpieczeństwa. Istnieją też inne świetne źródła [projektów][5] i [narzędzi][6], takich jak te oferowane przez [społeczność DevSecOps][7]. |
| **Osiągnięcie pokrycia i dokładności** | Jesteś pomostem między zespołami programistów i zespołami operacyjnymi. Aby osiągnąć pokrycie, skup się nie tylko na funkcjonalności, ale także na orkiestracji. Od początku ściśle współpracuj zarówno z zespołem programistów, jak i z zespołem operacyjnym, aby zoptymalizować swój czas i wysiłek. Dąż do stanu, w którym podstawowe aspekty bezpieczeństwa są weryfikowane w sposób ciągły. |
| **Jasno komunikuj ustalenia** | Wnoś wartość przy minimalnych tarciach lub bez nich. Przekazuj ustalenia na czas, w narzędziach, z których korzystają zespoły programistów (a nie w plikach PDF). Dołącz do zespołu programistów, aby zająć się ustaleniami. Wykorzystaj okazję, aby ich edukować: jasno opisz słaby punkt i sposób jego wykorzystania, łącznie ze scenariuszem ataku, który uczyni zagrożenie realnym. |

[1]: https://www.devsecops.org/
[2]: https://owasp.org/www-project-application-security-verification-standard/
[3]: https://owasp.org/www-project-web-security-testing-guide/
[4]: https://owasp.org/www-project-security-knowledge-framework/
[5]: http://devsecops.github.io/
[6]: https://github.com/devsecops/awesome-devsecops
[7]: http://devsecops.org
