# h2 Break & Unbreak

Tämä tehtävä raportti on luotu Iso-Anttila Lari & Karvinen Tero "Sovellusten Hakkerointi ja Haavoittuvuudet" -Kurssille.

### Laiteisto

Tehtävän aikana käytin seuraavia laitteita ja ohjelmia:

Windows 11, AMD

Oracle VirtualBox 7.2.2, Debian 13, NAT -network.


## X) Lue ja tiivistä

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

!KUVA1

Huomasin, että input type oli number, joten otin sen vain kokonaan pois ja kirjoitin PIN -kenttään seuraavan SQL injektio -hyökkäyskoodin

    ' OR 1=1; -- 
`'` : "hipsukka" sulkee voimassa olevan SQL kyselyn

`OR 1=1` : Lisätään uusi ehto, joka on aina totta

`; --` : Lopettaa kyselyn ja kommentti poissulkee kaikki jälkeenpäin olevat arvot

Lopullinen koodi olisi siis: 

        SELECT password FROM pins WHERE pin='' OR 1=1; -- ';

Joten täten kyselyn pitäisi palauttaa salasana koska ehto: `pin='' OR 1=1` on TRUE

!KUVA2

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

!KUVA3

## b) Fix the vulnerability in 010-staff-only

Olin aikaisemmilla kursseilla kuullut, että SQL injektioilta pystyy suojautumaan käyttämällä parametrisiä kyselyitä. Lähdinkin tätä kautta tarkistelemaan lähdekoodia.

Sillä oma osaaminen ohjelmoinnissa ja koodissa on hieman ruosteessa lähdin googlettelemaan miten parametrisiä kyselyitä käytetään ja kirjoitetaan pythonissa. Kysyin myös tekoälyltä apuja tässä tehtävässä

!KUVA alk koodi

Sain selville, että `pin='"+pin+"'` osuus koodissa lukee käyttäjän kirjoittaman tekstin ja lisää sen suoriltaan tähän koodin, tämän takia myös SQL injektio on mahdollista.

Pienen etsimisen jälkeen löysin, että pin koodi voidaan tallettaa erilliseen muuttujaan, joka estää suoran hyökkäyksen.

!KUVA muutettu koodi

Tämän muutoksen jälkeen testasin uudestaan saada salasanaa, mutta se epäonnistui. Näin koodi tuli korjattua.

!KUVA lopputulos

## c) Solve dirfuzt-1

## d) Break into 020-your-eyes-only

## e) Fix the vulnerability in 020-your-eyes-only

## Lähteet:

https://terokarvinen.com/hack-n-fix/

https://owasp.org/Top10/2021/A01_2021-Broken_Access_Control/index.html

https://terokarvinen.com/2023/fuzz-urls-find-hidden-directories/

https://portswigger.net/web-security/access-control

https://terokarvinen.com/2006/raportin-kirjoittaminen-4/

https://www.w3schools.com/mysql/mysql_limit.asp

https://terokarvinen.com/2023/fuzz-urls-find-hidden-directories/
