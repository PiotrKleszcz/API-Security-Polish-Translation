# API7:2023 Server Side Request Forgery

| Źródła zagrożeń/Wektory ataku | Słaby punkt bezpieczeństwa | Wpływ |
| - | - | - |
| Zależne od API : Możliwość wykorzystania **Łatwa** | Powszechność **Częsta** : Wykrywalność **Łatwa** | Techniczny **Umiarkowany** : Zależny od biznesu |
| Wykorzystanie wymaga od atakującego znalezienia endpointu API, który odwołuje się do URI dostarczonego przez klienta. Zasadniczo podstawowy SSRF (gdy odpowiedź jest zwracana atakującemu) jest łatwiejszy do wykorzystania niż blind SSRF, w którym atakujący nie otrzymuje informacji zwrotnej o tym, czy atak się powiódł. | Nowoczesne koncepcje tworzenia aplikacji zachęcają programistów do odwoływania się do URI dostarczanych przez klienta. Brak walidacji takich URI lub jej niewłaściwe wykonanie to częste problemy. Do wykrycia problemu potrzebna jest analiza zwykłych żądań i odpowiedzi API. Gdy odpowiedź nie jest zwracana (blind SSRF), wykrycie podatności wymaga więcej wysiłku i kreatywności. | Udane wykorzystanie może prowadzić do enumeracji usług wewnętrznych (np. skanowania portów), ujawnienia informacji, obejścia zapór sieciowych lub innych mechanizmów bezpieczeństwa. W niektórych przypadkach może prowadzić do DoS lub wykorzystania serwera jako proxy do ukrywania złośliwych działań. |

## Czy API jest podatne?

Błędy typu Server-Side Request Forgery (SSRF) występują, gdy API pobiera zdalny
zasób bez walidacji adresu URL dostarczonego przez użytkownika. Umożliwia to
atakującemu zmuszenie aplikacji do wysłania spreparowanego żądania do
nieoczekiwanego miejsca docelowego, nawet jeśli jest ono chronione przez zaporę
sieciową lub VPN.

Nowoczesne koncepcje tworzenia aplikacji sprawiają, że SSRF jest częstszy
i bardziej niebezpieczny.

Częstszy - następujące koncepcje zachęcają programistów do odwoływania się do
zasobów zewnętrznych na podstawie danych wejściowych od użytkownika: webhooki,
pobieranie plików z adresów URL, niestandardowe SSO i podglądy adresów URL.

Bardziej niebezpieczny - nowoczesne technologie, takie jak usługi dostawców
chmury, Kubernetes i Docker, udostępniają kanały zarządzania i sterowania
przez HTTP pod przewidywalnymi, dobrze znanymi ścieżkami. Kanały te są łatwym
celem ataku SSRF.

Ze względu na silne powiązania sieciowe nowoczesnych aplikacji trudniej jest
również ograniczyć ruch wychodzący z aplikacji.

Ryzyka SSRF nie zawsze da się całkowicie wyeliminować. Wybierając mechanizm
ochronny, należy wziąć pod uwagę ryzyka i potrzeby biznesowe.

## Przykładowe scenariusze ataków

### Scenariusz nr 1

Serwis społecznościowy pozwala użytkownikom przesyłać zdjęcia profilowe.
Użytkownik może przesłać plik obrazu ze swojego komputera lub podać adres URL
obrazu. Wybranie drugiej opcji wywoła następujące wywołanie API:

```
POST /api/profile/upload_picture

{
  "picture_url": "http://example.com/profile_pic.jpg"
}
```

Atakujący może wysłać złośliwy adres URL i za pomocą tego endpointu API
rozpocząć skanowanie portów w sieci wewnętrznej.

```
{
  "picture_url": "localhost:8080"
}
```

Na podstawie czasu odpowiedzi atakujący może ustalić, czy dany port jest
otwarty.

### Scenariusz nr 2

Produkt bezpieczeństwa generuje zdarzenia, gdy wykryje anomalie w sieci.
Niektóre zespoły wolą przeglądać zdarzenia w szerszym, bardziej ogólnym
systemie monitorowania, takim jak SIEM (Security Information and Event
Management). W tym celu produkt zapewnia integrację z innymi systemami za
pomocą webhooków.

W ramach tworzenia nowego webhooka wysyłana jest mutacja GraphQL zawierająca
adres URL API systemu SIEM.

```
POST /graphql

[
  {
    "variables": {},
    "query": "mutation {
      createNotificationChannel(input: {
        channelName: \"ch_piney\",
        notificationChannelConfig: {
          customWebhookChannelConfigs: [
            {
              url: \"http://www.siem-system.com/create_new_event\",
              send_test_req: true
            }
          ]
    	  }
  	  }){
    	channelId
  	}
	}"
  }
]

```

Podczas procesu tworzenia backend API wysyła żądanie testowe na podany adres
URL webhooka i przedstawia użytkownikowi odpowiedź.

Atakujący może wykorzystać ten przepływ i sprawić, że API zażąda wrażliwego
zasobu, takiego jak wewnętrzna usługa metadanych chmury, która ujawnia
poświadczenia:

```
POST /graphql

[
  {
    "variables": {},
    "query": "mutation {
      createNotificationChannel(input: {
        channelName: \"ch_piney\",
        notificationChannelConfig: {
          customWebhookChannelConfigs: [
            {
              url: \"http://169.254.169.254/latest/meta-data/iam/security-credentials/ec2-default-ssm\",
              send_test_req: true
            }
          ]
        }
      }) {
        channelId
      }
    }
  }
]
```

Ponieważ aplikacja wyświetla odpowiedź na żądanie testowe, atakujący może
zobaczyć poświadczenia środowiska chmurowego.

## Jak zapobiegać

* Odizoluj w sieci mechanizm pobierania zasobów: zwykle takie funkcje służą do
  pobierania zasobów zdalnych, a nie wewnętrznych.
* Tam, gdzie to możliwe, stosuj listy dozwolonych:
    * Źródeł zdalnych, z których użytkownicy mają pobierać zasoby (np. Google
      Drive, Gravatar itp.)
    * Schematów URL i portów
    * Akceptowanych typów mediów dla danej funkcjonalności
* Wyłącz przekierowania HTTP.
* Używaj dobrze przetestowanego i utrzymywanego parsera URL, aby uniknąć
  problemów wynikających z niespójności w parsowaniu adresów URL.
* Waliduj i sanityzuj wszystkie dane wejściowe dostarczane przez klienta.
* Nie wysyłaj klientom surowych odpowiedzi.

## Źródła

### OWASP

* [Server Side Request Forgery][1]
* [Server-Side Request Forgery Prevention Cheat Sheet][2]

### Zewnętrzne

* [CWE-918: Server-Side Request Forgery (SSRF)][3]
* [URL confusion vulnerabilities in the wild: Exploring parser inconsistencies,
   Snyk][4]

[1]: https://owasp.org/www-community/attacks/Server_Side_Request_Forgery
[2]: https://cheatsheetseries.owasp.org/cheatsheets/Server_Side_Request_Forgery_Prevention_Cheat_Sheet.html
[3]: https://cwe.mitre.org/data/definitions/918.html
[4]: https://snyk.io/blog/url-confusion-vulnerabilities/
