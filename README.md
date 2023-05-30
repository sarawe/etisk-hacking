# Etisk hacking

## Kom i gang
1. Start Burp Suite
2. Gå til ``` Proxy ``` og trykk ``` Open browser ```. Da får du en nettleser satt opp med HTTP proxy
3. Gå til https://ctf.hacker101.com/auth/login fra nettleservinduet som åpnet seg. Når intercept er på må du trygge ``` Forward ``` for at requestene skal bli sendt.

For å unngå for mye unødvendig trafikk i Burp Suite kan det være lurt å google i et annet nettleservindu.

Trafikk inspiseres i ``` HTTP history ``` under ``` proxy ``` eller i ``` sitemap ``` under ``` target ```. Velg en request, høyreklikk og trykk send to repeater for å endre på den og sende på nytt.

## Oppgaver på Hacker101

### Postbook
1. Trykk deg rundt og finn ut hvordan applikasjonen fungerer
  - Lag en bruker og logg inn
  - Opprett en post
  
2. Klarer du å se noen andre sin post?
    <details>
      <summary>💡 Hint</summary>
    Klikk for å se på en av dine poster og se på requesten. Er det noe du kan endre der?
    </details>
    <details>
      <summary>🚨 Løsning</summary>
    Trykk på en av dine egne poster. Finn requesten under HTTP History. Høyreklikk på requesten og trykk Send to repeater. Gå til Repeater i menyen. Endre id-parameteret i requesten til feks 1 (GET /index.php?page=view.php&id=1) og trykk send. I responsen vil du se en annen person sin post. 
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
      
    Istedenfor å gjette manuelt hvor innloggingssiden ligger kan vi automatisere prosessen ved å la Intruder iterere over en liste med payloads og sende HTTP-kall på nytt med ulik payload hver gang.
    
    Høyreklikk på GET-kallet til forsiden og velg ``` Send to Intruder ```. Legg til to paragraftegn (§§) etter ``` GET / ```. Dette forteller Intruder hvor den skal injisere payloaden vi definerer i neste steg.
    
    Velg deretter fanen ``` Payloads ```. Her velger man hvilke payloads Intruder skal bruke. For denne oppgaven kan vi bruke en liste med typiske stier på nettsider. Lim inn innholdet i [denne fila](common-web-paths.txt) under Payload Options og velg Start attack.
    
    Finner du noen sider som returnerer en 2XX-respons? Hvis ikke kan det hende webserveren vi gjør kall mot skiller mellom store og små bokstaver. Endre payloaden til lowercase ved å velge ``` Add ``` under ``` Payload Processing ```. Deretter ``` Modify case ```, ``` To lower case ``` og OK. Kjør intruder på nytt.

    </details>
    
3. Klarer du å finne en gyldig innlogging?
    <details>
      <summary>💡 Hint</summary>
    
      Siden Intruder har kraftige begrensnigner på hvor mange kall man kan gjøre i sekundet er den ikke spesielt godt egnet til å gjøre noe reell brute-forcing. Heldigvis har noen laget en utvidelse som gir deg kraftigere funksjonalitet. [Følg denne guiden](https://portswigger.net/research/turbo-intruder-embracing-the-billion-request-attack) for å installere og bruke Turbo Intruder til å brute-force brukernavn og passord på innloggingssiden. Disse listene med [vanlige brukernavn](https://github.com/danielmiessler/SecLists/blob/master/Usernames/Names/names.txt) og [passord](https://github.com/danielmiessler/SecLists/blob/master/Passwords/Common-Credentials/10k-most-common.txt) er for eksempel fin å bruke.
  
    Start med å finne et gyldig brukernavn. Deretter kan du brute-force passordet til brukeren. Det kan være lurt å filtrere vekk responser som indikerer at brukernavnet eller passordet er feil i resultattabellen.
  
    </details>
  
  Hvis du er ferdig med alle oppgavene her er det bare å gå løs på flere oppgaver på Hacker101 💪
  
## Nyttige ressurser
  
https://github.com/swisskyrepo/PayloadsAllTheThings

https://github.com/danielmiessler/SecLists

https://github.com/snoopysecurity/awesome-burp-extensions

