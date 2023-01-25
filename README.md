# Etisk hacking

## Kom i gang
1. Start Burp Suite
2. Gå til proxy og trykk Open browser. Da får du en nettleser satt opp med HTTP proxy
3. Gå til url fra nettleservinduet som åpnet seg

Google i andre nettleservinduer for å unngå for mye unødvendig trafikk i Burp Suite

Trafikk inspiseres i HTTP history under proxy eller i sitemap under target
Velg en request og trykk send to repeater for å endre på den og sende på nytt

## Oppgaver

### Postbook
- Trykk deg rundt og finn ut hvordan applikasjonen fungerer
  - Lag bruker
  - Logg inn
  - Opprett en post
- Klarer du å se noen andre sin post?
  - Tips: klikk for å se på en av dine poster og se på requesten. Er det noe du kan endre der?
- Klarer du å endre en annen post?
  - Tips: se på requesten for å endre en post, er det noe du kan endre der?
- Klarer du å lage en post på vegne av en annen bruker?
- Klarer du å endre posten til en administrator?
- Klarer du å slette en post?

### Petshop
- Klarer du å sette prisen til 0kr?
- Finner du en administrator-side der man kan logge inn?
  - Tips: Bruk denne listen i Intruder for å finne mulige innloggingssider
- Klarer du å finne en gyldig innlogging?
  - Tips: Finn en ordliste og bruk Turbo intruder extension for å unngå throttling. Legg inn riktig tid til ordlisten
