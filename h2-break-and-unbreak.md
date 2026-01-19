# h2 Break & Unbreak

Tämä tehtävä raportti on luotu Iso-Anttila Lari & Karvinen Tero "Sovellusten Hakkerointi ja Haavoittuvuudet" -Kurssille.

### Laiteisto

Tehtävän aikana käytin seuraavia laitteita ja ohjelmia:

Windows 11, AMD

Oracle VirtualBox 7.2.2, Debian 13, NAT -network.


## X) Lue ja tiivistä (kesken)

OWASP: OWASP Top 10 https://owasp.org/Top10/2021/A01_2021-Broken_Access_Control/index.html

Karvinen 2023: https://terokarvinen.com/2023/fuzz-urls-find-hidden-directories/

PortSwigger: https://portswigger.net/web-security/access-control

Karvinen 2006: https://terokarvinen.com/2006/raportin-kirjoittaminen-4/

## a) Break into 010-staff-only

Aloitin asentamalla virtuaalikoneelleni ensin tehtäväpaketit Karvisen verkkosivuilta (https://terokarvinen.com/hack-n-fix/)

    $ wget https://terokarvinen.com/hack-n-fix/teros-challenges.zip
    $ unzip teros-challenges.zip

Seuraavaksi asensin python3 flaskin, sqlalchemyn ja siirryin & käynnistin ensimmäisen haasteen

    $ sudo apt-get -y install python3-flash python3-flask-sqlalchemy
    $ cd challenges/010-staff-only/
    $ python3 staff-only.py

Avasin  Firefoxin ja sieltä kohdesivun http://127.0.0.1:5000

Lähdin lähestymään tehtävää tunnilla käynneillä opeillani, eli SQL injektiolla.

Heti aluksi huomasin, että PIN alueelle, ei pystynyt kirjoittamaan muuta kuin numeroita, joten kävin tutkimassa ja peukaloimassa inspect elementillä kyseistä kohdetta.

<img width="667" height="784" alt="kuva" src="https://github.com/user-attachments/assets/560385eb-4120-4558-82ef-51ace44a1fc2" />

Kuva 1 Firefox inspect element

Huomasin, että input type oli number, joten otin sen vain kokonaan pois ja kirjoitin PIN -kenttään seuraavan SQL injektio -hyökkäyskoodin

    ' OR 1=1; -- 
`'` : "hipsukka" sulkee voimassa olevan SQL kyselyn

`OR 1=1` : Lisätään uusi ehto, joka on aina totta

`; --` : Lopettaa kyselyn ja kommentti poissulkee kaikki jälkeenpäin olevat arvot

Lopullinen koodi olisi siis: 

        SELECT password FROM pins WHERE pin='' OR 1=1; -- ';

Joten täten kyselyn pitäisi palauttaa salasana koska ehto: `pin='' OR 1=1` on TRUE

<img width="673" height="802" alt="kuva" src="https://github.com/user-attachments/assets/d21f8551-d27a-4a0a-b309-9e2ce6ca8dd1" />

Kuva 2 SQL -injektio

Tämä palautti minulle vain salasanan "foo", eikä haluttua salasanaa, joten lähdin tutkimaan asiaa. Huomasin Teron (https://terokarvinen.com/hack-n-fix/) vinkeissä puhuvan LIMIT ehdosta ja perehdyin siihen hieman tarkemmin.

Luin hieman LIMIT komennosta (https://www.w3schools.com/mysql/mysql_limit.asp) ja aloin testailemaan verkkosivuilla kyseistä komentoa.

Koska nettisivu pystyi palauttamaan vain 1 rivin kerralla ei LIMIT yksinään tehnyt mitään, koska ei ollut väliä haenko 1 vai 100 riviä.

Huomasin, että rivien lähtökohtaa voi muutta joten kokeilin parilla koodilla:

    LIMIT x,y
    LIMIT x OFFSET y

Yritettyäni vähäsen aikaa huomasin, että komennolla `' OR 1=1 LIMIT 2,1; -- ` sain palautettua oikean salasanan.

Lähdin myös mielenkiinnosta yrittämään tuota toista komentoa, mutta yrittäessäni `' OR 1=1 LIMIT 2 OFFSET 1; -- ` sain eri vastauksen ja väärän salasanan.

Pienen tutkinnan ja miettimisen avulla sain selville, että LIMIT kertoo kuinka monta riviä palautetaan ja OFFSET kuinka monta riviä skipataan.

Ensimmäisessä koodissa: `LIMIT 2,1` kerrotaan ensin kuinka monen rivin yli hypätään ja kuinka monta palautetaan.

Joten toisella yrityksellä `LIMIT 1 OFFSET 2` palautti minulle myös oikean salasanan.

<img width="1004" height="686" alt="kuva" src="https://github.com/user-attachments/assets/61380c50-f1fe-4263-9013-35c2b6661324" />

Kuva 3 Lopputulos

## b) Fix the vulnerability in 010-staff-only

Olin aikaisemmilla kursseilla kuullut, että SQL injektioilta pystyy suojautumaan käyttämällä parametrisiä kyselyitä. Lähdinkin tätä kautta tarkistelemaan lähdekoodia.

Sillä oma osaaminen ohjelmoinnissa ja koodissa on hieman ruosteessa lähdin googlettelemaan miten parametrisiä kyselyitä käytetään ja kirjoitetaan pythonissa. Kysyin myös tekoälyltä apuja tässä tehtävässä

<img width="617" height="156" alt="kuva" src="https://github.com/user-attachments/assets/804d761c-5077-4f6d-8cda-826743f9be29" />

Kuva 4 Alkuperäinen ohjelmakoodi

Sain selville, että `pin='"+pin+"'` osuus koodissa lukee käyttäjän kirjoittaman tekstin ja lisää sen suoriltaan tähän koodin, tämän takia myös SQL injektio on mahdollista.

Pienen etsimisen jälkeen löysin, että pin koodi voidaan tallettaa erilliseen muuttujaan, joka estää suoran hyökkäyksen.

<img width="623" height="164" alt="kuva" src="https://github.com/user-attachments/assets/fb04836b-b3e0-427b-8a55-aaa2cc84e4bb" />

Kuva 5 Muutettu ohjelmakoodi

Tämän muutoksen jälkeen testasin uudestaan saada salasanaa, mutta se epäonnistui. Näin koodi tuli korjattua.

<img width="766" height="781" alt="kuva" src="https://github.com/user-attachments/assets/f4f71c94-2d0a-4322-93e5-1854aea3ceb5" />

Kuva 6 Korjattu verkkosivu

## c) Solve dirfuzt-1

Aloitin ensin tekemällä dirfuzt-0 tehtävän Karvisen sivujen ohjeiden mukaisesti. (https://terokarvinen.com/2023/fuzz-urls-find-hidden-directories/)

Aloitin dirfuzt-1 -tehtävän ajamalla ffuf työkalun kyseisellä sivulle komennolla:

        $ ffuf -w common.txt -u http://127.0.0.2:8000

Vastauksia tuli yllättävästä todella monta ja tarkistelin mitä samaa suurimmalla osalla sivuista olisi. Huomasin, että suurimman osan koko oli 154 joten filteröin tämän pois seuraavalla komennolla:

        $ ffuf -w common.txt -u http://127.0.0.2:8000 -fs 154

Tämän jälkeen jäi vain versiohallinta sivut .git alta ja Admin Page josta kävin hakemassa liput!

<img width="1004" height="573" alt="kuva" src="https://github.com/user-attachments/assets/48c4b41d-9fd2-463b-976f-3b7d1cc15d59" />

Kuva 7 ./git/logs & /wp-admin -verkkosivujen flagit

## d) Break into 020-your-eyes-only

Aloitin tehtävänannon Karvisen sivun ohjeiden mukaisesti, kunnes sain kohde verkkosivun pystyyn. (https://terokarvinen.com/hack-n-fix/)

Aloitin sivun tutkimisen ja päädyin lopputulokseen, että yritän kirjautua Admin dashboard sivulle käyttämällä SQL injektiota.

Pienen tovin jälkeen en edennyt minnekkään, joten testasin ffuf komentoa

        $ ffuf -w common.txt -u http://127.0.0.1:8000/FUZZ

Palautuksena sain vaIn yhden sivun, nimeltä: `admin-console`.

Kokeilin kyseistä sivua, mutta pyysi samaa kirjautumista.

Pienen pohtimisen jälkeen päädyin rekisteröitymään sivulle uutena käyttäjänä:

    testperson
    tester321

Pääsin täten tarkastelemaan "Your personal data" -sivua ja huomasin, että admin dashboard antaa vain: 403 Forbidden koodia.

Tämän jälkeen päätin testata uudestaan `admin-console` -sivua, jonka ffuf minulle antoi ja huomasin, että Admin dashboard nappula vie väärälle sivulle (admin-dashboard) jota ei varmaankaan ole edes olemassa.

Vaihettuani urlin oikeaksi pääsin sisälle admin consoleen.

<img width="572" height="475" alt="kuva" src="https://github.com/user-attachments/assets/21fd44f6-c661-4418-9346-ca148df576d0" />

Kuva 8 Admin Console

## e) Fix the vulnerability in 020-your-eyes-only

Uskoin, että ongelma on selvästi jossakin kohtaa ohjelmakoodia, mikä tarkistaa saako käyttäjä tarkistella verkkosivuja, sillä normaalilla käyttäjällä pääsi tarkastelemaan Admin paneelia. Lähdinkin tätä kautta etsimään, missä vika voisi olla. Hetken aikaa löytämättäni tarkistin ohjeiden apusivulta, että pitääpi etsiä tiedostoa nimeltä `views.py`

Lähdin etsimään ensiksi `accounts/` -hakemistosta, koska oletin käyttäjien oikeuksien olevan kyseisessä hakemistossa, josta löysin kyseisen tiedoston. Pienen tutkinnan jälkeen päädyin, että tämä tiedosto ei ole oikea, joten jatkoin etsimistä.

Seuraavaksi löysin itseni `hats/` -hakemistosta, jossa oli saman niminen tiedosto, mitä etsin. Avasin tiedoston lähdekoodin ja aloin tarkastelemaan koodia.

Pienen tutkinnan jälkeen huomasin, että tiedostossa on verkkosivulla olevien sivujen osat: MyDataView, AdminDashboardView & AdminShowAllView.

Huomasin, että AdminDashboardView:n kohdalla oli lisätty koodi: `and self-request.user.is:staff`, jota ei ollut lisätty AdminShowAllView:n kohdalla.

<img width="917" height="455" alt="kuva" src="https://github.com/user-attachments/assets/19562276-0157-4d7e-956d-d6f3975f15cf" />

Kuva 9 hats/views.py -ohjelmakoodi, johon lisätty aikaisemmin mainittu rivi

Päätin lisätä kyseisen koodinpätkän myös alemmalle riville ja testata, pääsisinkö enään samalla tavalla Admin Consoleen.

<img width="1004" height="550" alt="kuva" src="https://github.com/user-attachments/assets/a83764de-8234-4c32-a133-4138de619f86" />

Kuva 10 Korjattu haavoittuvuus

Nyt en päässyt enää samalla tavalla verkkosivun admin consoleen, joten uskon korjanneeni vian.

## Lähteet: (kesken)

Karvinen 2006. Raportin kirjoittaminen. Luettavissa: https://terokarvinen.com/2006/raportin-kirjoittaminen-4/ Luettu: 19.1.2026.

Karvinen 2023. Find Hidden Web Directories - Fuzz URLs with ffuf. Luettavissa: https://terokarvinen.com/2023/fuzz-urls-find-hidden-directories/ Luettu: 19.1.2026.

Karvinen 2024. Hack'n Fix. Luettavissa: https://terokarvinen.com/hack-n-fix/ Luettu: 19.1.2026.

OWASP 2021. A01:2021 - Broken access control. Luettavissa: https://owasp.org/Top10/2021/A01_2021-Broken_Access_Control/index.html Luettu: 19.1.2026

PortSwigger s.a. Access control vulnerabilities and privilege escalation. Luettavissa: https://portswigger.net/web-security/access-control Luettu: 19.1.2026

W3Schools s.a. MySQL LIMIT Clause. Luettavissa: https://www.w3schools.com/mysql/mysql_limit.asp Luettu: 19.1.2026.
