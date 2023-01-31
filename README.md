# Etisk hacking

## Kom i gang
1. Start Burp Suite
2. Gå til ``` Proxy ``` og trykk ``` Open browser ```. Da får du en nettleser satt opp med HTTP proxy
3. Gå til https://ctf.hacker101.com/auth/login (?) fra nettleservinduet som åpnet seg

For å unngå for mye unødvendig trafikk i Burp Suite kan det være lurt å google i et annet nettleservindu

Trafikk inspiseres i ``` HTTP history ``` under ``` proxy ``` eller i ``` sitemap ``` under ``` target ```
Velg en request og trykk send to repeater for å endre på den og sende på nytt

## Oppgaver på Hacker101

### Postbook
1. Trykk deg rundt og finn ut hvordan applikasjonen fungerer
  - Lag bruker
  - Logg inn
  - Opprett en post
  
2. Klarer du å se noen andre sin post?
    <details>
      <summary>💡 Hint</summary>
    Klikk for å se på en av dine poster og se på requesten. Er det noe du kan endre der?
    </details>
 
3. Klarer du å endre en annen post?
    <details>
      <summary>💡 Hint</summary>
    Se på requesten for å endre en post, er det noe du kan endre der?
    </details>
4. Klarer du å lage en post på vegne av en annen bruker?
5. Klarer du å endre posten til en administrator?
6. Klarer du å slette en post?

### Petshop
1. Klarer du å sette prisen til 0kr?
2. Finner du en administrator-side der man kan logge inn?
    <details>
      <summary>💡 Hint</summary>
      
    Istedenfor å gjette manuelt hvor innloggingssiden ligger kan man automatisere prosessen ved å la Intruder iterere over en liste med payloads og sende HTTP-kall på nytt med ulik payload hver gang.
    
    Høyreklikk på GET-kallet til forsiden og velg Send to Intruder. Marker stien HTTP-kallet går mot, i dette tilfellet /, og trykk Add §. Dette forteller Intruder hvor den skal injisere payloaden vi definerer i neste steg.
    
    Velg deretter fanen Payloads. Her velger man hvilke payloads Intruder skal bruke. For denne oppgaven kan vi bruke en liste med typiske stier på nettsider. Lim inn innholdet i [denne fila]() under Payload Options og velg Start attack.
    
    Finner du noen sider som returnerer en 2XX-respons?

    </details>
    
3. Klarer du å finne en gyldig innlogging?
    <details>
      <summary>💡 Hint</summary>
    Finn en ordliste og bruk Turbo intruder extension for å unngå throttling. Legg inn riktig tid til ordlisten
    </details>
  
<details>
  <summary>💡 Hint</summary>

</details>
