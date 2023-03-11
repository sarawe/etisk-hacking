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
    
      Siden Intruder har kraftige begrensnigner på hvor mange kall man kan gjøre i sekundet er den ikke spesielt godt egnet til å gjøre noe reell brute-forcing. Heldigvis har noen laget en utvidelse som gir deg kraftigere funksjonalitet. [Følg denne guiden](https://portswigger.net/research/turbo-intruder-embracing-the-billion-request-attack) for å installere og bruke Turbo Intruder til å brute-force brukernavn og passord på innloggingssiden. [Denne listen med vanlige brukernavn og passord](https://github.com/danielmiessler/SecLists/blob/master/Passwords/Leaked-Databases/rockyou-75.txt) er for eksempel fin å bruke.
  
    Start med å finne et gyldig brukernavn. Deretter kan du brute-force passordet til brukeren. Det kan være lurt å filtrere vekk responser som indikerer at brukernavnet eller passordet er feil i resultattabellen.
  
    </details>
  
  Hvis du er ferdig med alle oppgavene her er det bare å gå løs på flere oppgaver på Hacker101 💪
  
## Nyttige ressurser
  
https://github.com/swisskyrepo/PayloadsAllTheThings

https://github.com/danielmiessler/SecLists

https://github.com/snoopysecurity/awesome-burp-extensions



# Sikker utvikling
Her vil du se noen eksempler på usikker kode som vi kan gjøre sikrere. Prøv gjerne å skrive om komponentene selv eller tenke på ulike løsninger før du ser på løsningsforslaget.


## Cookies
Dette er en usikker cookie. Hvordan kan vi gjøre den sikrere?
```
app.get('/', (req,res)=> {
    // Set cookie options
    let options = {
        maxAge: 1000 * 60 * 60 * 24,
    }
    // Convert user details to Base64
    let buff = new Buffer(userDetails);
    let base64data = buff.toString('base64');

    // Set cookie
    res.cookie('user', base64data, options)
    res.send('')
})
```
<details>

<summary>🚨 Løsningsforslag </summary>
Cookien over har tre problemer:

  - Den har veldig lang levetid
  - Den inneholder brukerdata (som enkelt kan konverteres tilbake fra base64)
  - Den har ikke satt flaggene ``` httpOnly ``` og ``` secure ```

Her ser dere et eksempel på en sikrere versjon av den samme cookien hvor vi setter flaggene, setter kortere levetid, og bruker et session token som vi kan koble til brukeren i backend istedenfor å legge ved brukerdata:

```
app.get('/', (req,res)=>{
    // Set cookie options
    let options = {
        maxAge: 1000 * 60 * 15, // 15 minute timeout     
        httpOnly: true,
        secure: true,
    }
    // Set cookie
    res.cookie('user', sessionToken, options)
    res.send('')
})
```
</details>



## Gjenoppretting av passord
Bollebakeriet.no har følgende metode for gjenoppretting av passord. Hvis brukeren trykker på knappen for glemt passord genereres et token som sendes i en lenke på e-post til brukeren. URL-en for å gjenopprette passord blir dermed noe ala ``` https://bollebakeriet.no/glemt-passord?token=51ee1d86fce7df4c7376032a897f5134 ```

Koden for gjenoppretting ser slik ut:

```
app.post('/glemt-passord', (req, res) => {
  username = req.query.username
  token = crypto.createHash('md5').update(username).digest("hex")
  allow_password_reset(username)
  send_password_reset_email(username, token)
  res.send('Check your email!')
})
```
Hvordan kan vi gjøre den sikrere?

<details>

<summary>🚨 Løsningsforslag </summary>
Tokenet som generes er ikke random siden det kun er en hash av brukernavnet. Dermed kan man resette andres passord hvis man vet eller gjetter brukernavnet deres.


Her ser dere et eksempel på en sikrere versjon av den samme koden hvor vi bruker en [NPM-pakke for UUID](https://www.npmjs.com/package/uuid) for å generere et tilfeldig token.

```
app.post('/forgot-password', (req, res) => {
  username = req.query.username
  token = uuidv4(); 
  allow_password_reset(username)
  get_user(username).set_user_reset_token(token)
  send_password_reset_email(username, token)
  res.send('Check your email!')
})
```
</details>


## Hente karakter
Studentweb har følgende endepunkt for å hente en karakter hvis man er logget inn som student.

```
class Grade:
    def on_get(self, req, resp):
        grade = lookup_grade(req.params["subjectID"], req.params["studentID"])
        resp.media = grade

app = falcon.App()
app.add_route("/grades", Grade())
```
Dette endepunktet er usikkert, ser du hvorfor? Hvordan kan vi gjøre det sikrere?

<details>

<summary>🚨 Løsningsforslag </summary>
Endepunktet over har ingen sjekk på om det er en gyldig sesjon, så man vil kunne hente andre studenters karakterer ved å sende inn forskjellige ID-er. 

Her har vi lagt til en sjekk på at ID-en tilhører brukerens sesjon så man kun kan se sin egen karakter.

```
class Grade:
    def on_get(self, req, resp):
        if get_student_id(session) != req.params['studentID']:
            resp.media = "Access Denied"
            return False 
        grade = lookup_grade(req.params['subjectID'], req.params['studentID'])
        resp.media = grade
```
</details>


## Innlogging
Koden under utfører en innlogging av en bruker. Ved riktig kombinasjon av brukernavn og passord logges brukeren inn, mens ved feil passord sendes brukeren tilbake til innloggingssiden. Ser du noen problemer med denne innloggingen?

```
@app.route("/login", ["GET", "POST"])
def login():
   # User authentication
   form = LoginForm()
   if form.validate_on_submit():
       user = User.query.filter_by(username=form.username.data).first()

       # User entered correct password?
       if user is None or not user.check_password(form.password.data):
           flash("Invalid username or password")
           return redirect(url_for("login"))

       login_user(user)
       return redirect(url_for("user_page"))

   return render_template("login.html", form = form)
```

<details>
<summary>💡 Hint </summary>
Siden vi ikke begrenser antall mulige feilede innloggingsforsøk er denne koden sårbar for brute-forcing. Vet du om noen måter vi kan forhindre brute-forcing på?
</details>

<details>

<summary>🚨 Løsningsforslag </summary>
For å forhindre brute-forcing kan vi for eksempel gjøre en eller flere av følgende tiltak:

  - Legge til CAPTCHA ved innlogging. Det vil ikke begrense antall forsøk, men stoppe automatiserte angrep med scripts.
  - Legge på et delay hvor hvert forsøk for å forsinke automatiserte angrep.
  - Rate-limiting på server-nivå. Cloudflare, AWS etc har ofte innebygde mekanismer for å inspisere requester og begrense antall requester per IP-adresse eller per nettside innenfor et gitt tidsintervall.

#### Eksempel med øktende delay for hvert feilede forsøk

```
WAIT_TIME_PER_LOGIN = 5

@app.route("/login", ["GET", "POST"])
def login():
   # User authentication
   form = LoginForm()
   if form.validate_on_submit():
       user = User.query.filter_by(username=form.username.data).first()

       last_login_atmpt = user.last_login_attempt
       consec_failed_logins = user.no_failed_logins
       user_timeout = user.user_timeout

       time_between = (datetime.datetime.now() - last_login_atmpt).total_seconds()

       user.last_login_attempt = datetime.datetime.now()

       if time_between < user_timeout:
           flash("Please wait {user_timeout} seconds before attempting to login again.")

           # Wait 5 extra seconds for each incorrect login
           user.user_timeout = consec_failed_logins * WAIT_TIME_PER_LOGIN
           user.no_failed_logins += 1
           db.session.update(user)
           db.session.commit()
           return redirect(url_for("login"))  
           
      # User entered correct password?
       if user is None or not user.check_password(form.password.data):
           flash("Invalid username or password")
           user.no_failed_logins += 1

           db.session.update(user)
           db.session.commit()
           return redirect(url_for("login"))

       user.no_failed_logins = 0
       user.user_timeout = 0
       db.session.update(user)
       db.session.commit()
       login_user(user)
       return redirect(url_for("user_page"))
```

</details>


## Printing av brukernavn
Koden under er en del av onboardingen av nye kunder. Den tar imot navnet til brukeren, konverterer det til store bokstaver og viser en melding til brukeren. Ser du hvorfor denne koden er usikker og hvordan vi kan gjøre den sikrere?

```
app.get('/customerOnboarding', (req, res) => {
  const name = req.query.name;
  const uppercaseName = eval('"' + name + '"' + '.toUpperCase()');
  res.send('Hi there, ' + uppercaseName);
});
```

<details>
<summary>🚨 Løsningsforslag </summary>
Koden over har ingen validering av brukerinput og bruker den usikre eval()-funksjonen.

For å gjøre koden sikrere kan vi for eksempel validere input ved å sette en makslengde og kun tillate store og små bokstaver med en regex. eval() bør ikke brukes fordi den evaluerer inputen og kjører eventuelt innhold, så vi kan heller forenkle koden og kun bruke toUpperCase.

```
validPattern = re.compile(r"[A-Za-z]{1,100}")

app.get('/customerOnboarding', (req, res) => {
  if (re.fullmatch(validPattern, req.query.name) {
    const name = req.query.name;
    const uppercaseName = name.toUpperCase()');
    res.send('Hi there, ' + uppercaseName);
  }
});
```
</details>
