# Ryzyka bezpieczeństwa API

Do przeprowadzenia analizy ryzyka wykorzystano [OWASP Risk Rating Methodology][1].

Poniższa tabela podsumowuje terminologię związaną z punktową oceną ryzyka.

| Źródła zagrożeń | Możliwość wykorzystania | Powszechność słabego punktu | Wykrywalność słabego punktu | Wpływ techniczny | Wpływ biznesowy |
| :-: | :-: | :-: | :-: | :-: | :-: |
| Zależne od API | Łatwa: **3** | Powszechna **3** | Łatwa **3** | Poważny **3** | Zależny od biznesu |
| Zależne od API | Średnia: **2** | Częsta **2** | Średnia **2** | Umiarkowany **2** | Zależny od biznesu |
| Zależne od API | Trudna: **1** | Trudna **1** | Trudna **1** | Niewielki **1** | Zależny od biznesu |

**Uwaga**: To podejście nie uwzględnia prawdopodobieństwa związanego ze źródłem
zagrożenia. Nie uwzględnia również żadnych szczegółów technicznych związanych
z konkretną aplikacją. Każdy z tych czynników może znacząco wpłynąć na ogólne
prawdopodobieństwo, że atakujący znajdzie i wykorzysta określoną podatność.
Ta ocena nie uwzględnia rzeczywistego wpływu na działalność biznesową. Każda
organizacja musi sama zdecydować, jakie ryzyko bezpieczeństwa związane
z aplikacjami i API jest gotowa zaakceptować, biorąc pod uwagę swoją kulturę,
branżę i otoczenie regulacyjne. Celem OWASP API Security Top 10 nie jest
wyręczanie organizacji w tej analizie ryzyka. Ponieważ ta edycja nie opiera się
na danych, powszechność wynika z konsensusu członków zespołu.

## Źródła

### OWASP

* [OWASP Risk Rating Methodology][1]
* [Artykuł na temat modelowania zagrożeń/ryzyka][2]

### Zewnętrzne

* [ISO 31000: Risk Management Std][3]
* [ISO 27001: ISMS][4]
* [NIST Cyber Framework (US)][5]
* [ASD Strategic Mitigations (AU)][6]
* [NIST CVSS 3.0][7]
* [Microsoft Threat Modeling Tool][8]

[1]: https://owasp.org/www-project-risk-assessment-framework/
[2]: https://owasp.org/www-community/Threat_Modeling
[3]: https://www.iso.org/iso-31000-risk-management.html
[4]: https://www.iso.org/isoiec-27001-information-security.html
[5]: https://www.nist.gov/cyberframework
[6]: https://www.asd.gov.au/infosec/mitigationstrategies.htm
[7]: https://nvd.nist.gov/vuln-metrics/cvss/v3-calculator
[8]: https://www.microsoft.com/en-us/download/details.aspx?id=49168
