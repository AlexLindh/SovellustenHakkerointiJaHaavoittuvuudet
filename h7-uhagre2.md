# h6 Uhagre2

Tämä tehtävä raportti on luotu Iso-Anttila Lari & Karvinen Tero "Sovellusten Hakkerointi ja Haavoittuvuudet" -Kurssille.

## Laitteisto

Tehtävän aikana käytin seuraavia laitteita ja ohjelmia:

Windows 11, AMD

Oracle VirtualBox 7.2.2, Debian 13, NAT -network.

## x) Read/watch/listen & summarize

1.1 Terminology

1.4 Simple XOR

1.7 Large numbers

Python basics for Hackers

## CryptoPals Set 1 -challenges

## a) 1. Convert hex to base64

Tehtävässä pyydettiin luomaan skripti, joka pyytää hex syötettä ja muuttaa sen base64 muotoon.

<img width="1004" height="403" alt="kuva" src="https://github.com/user-attachments/assets/2431d8bf-5c09-4dc0-b84a-9d1c351bc7d3" />

Kuva 1 skripti ja lopputulos

`hex = input()`: Luo muuttujan nimeltä "hex", joka saa arvon käyttäjän syötteestä.

`tavut = bytes.fromhex(hex)`: Luo muuttujan nimeltä "tavut", joka saa arvon "hex" -merkkijonon tavukäännöksestä.

`b64 = base64.b64encode(tavut)`: Luo muuttujan "b64", joka saa base64 koodatun arvon "tavut" -tavujonosta.

## b) 2. Fixed XOR

Toisessa tehtävässä pyydettiin luomaan skripti, joka pyytää kahta samanpituista hex -merkkijonoa ja tulostaa näiden XOR yhdistelmän.

<img width="902" height="709" alt="kuva" src="https://github.com/user-attachments/assets/92aff714-a8de-43b6-8b60-c65d2d568706" />

Kuva 2 XOR ohjelmakoodi

Skriptin ensimmäinen osa toimii samalla tavalla kuin ensimmäisessä tehtävässä. Pyydetään ensin kaksi syötettä ja tehdään niille tavukäännös.

Seuraavaan osioon olin saanut apua tekoälyltä promptilla: "How to compare two different hex inputs and do a XOR comparision"

Sain apuja, että pystyisin muodostamaan uuden zip objektin, jonka tavujonoja voitaisiin verrata keskenään

`xor_vertaus = bytes(a ^ b for a, b in zip(tavut1, tavut2)`: Luo muuttujan "xor_vertaus", joka saa arvon suorittaessaan XOR vertailun jokaisen tavun kohdalla verrattavista tavujonoista.

`xor = xor_vertaus.hex()`: Luo muuttujan "xor", joka saa arvon "xor_vertaus" -tavujonon muunnoksesta heksadesimaaliksi.

<img width="980" height="445" alt="kuva" src="https://github.com/user-attachments/assets/53f7dc4a-90b0-486d-818c-1c53c33f412b" />

Kuva 3 Skriptin lopputulos

## c) 3. Single-byte XOR cipher

Tehtävässä oli hex -merkkijono, jonka kaikki tavut oltiin XORrattu yhdellä tuntemattomalla merkillä, joka pitäisi löytää käyttämällä skriptiä, joka tekee vertauksen kaikilla merkeillä ja laskee todennäköisyyden ja tulostaa parhaan mahdollisen vastauksen. Todennäköisyys laskettaisiin kirjaimilla, mitä käytetään yleisimmin englanninkielisissä teksteissä.

Sain luotua seuraavanlaisen skriptinpätkän kommenteilla:

    # Verrattava hex -merkkijono
    hex = "1b37373331363f78151b7f2b783431333d78397828372d363c78373e783a393b3736"

    # Tavukäännös hex -> bytes
    translate = bytes.fromhex(hex)

    # Muuttuja missä yleisimmät kirjaimet englanninkielisessä kirjallisuudessa
    common = "ETAOIN SHRDLUetaoinshrdlu"

    # Funktio joka laskee pisteet sen perusteella, kuinka monta yleistä kirjainta se pitää sisällään
    def scoring(text):
        return sum(c in common for c in text)

    # Lista, johon kerätään XOR vertauksien tulokset
    results = []

    # Funktio, joka käy läpi kaikki mahdolliset XOR -avaimet
    for key in range(256):
        plaintext = bytes([b ^ key for b in translate])
        s = scoring(plaintext)
        results.append((s, key, plaintext))

    results.sort(reverse=True)

    # Tulostetaan parhaat 5 tulosta
    for s, key, plaintext in results[5:]:
        print("Score: {s}, Key: {Key(hex)}, Text: {plaintext.decode}")

<img width="984" height="233" alt="kuva" src="https://github.com/user-attachments/assets/6edaca99-ea36-4a35-9ce6-920f74668cf0" />

Kuva 4 Skriptin lopputulos

Tehtävän voittajana toimi tänään MCpekoni.

## d) 4. Detect single-character XOR

## Lähteet

https://terokarvinen.com/python-for-hackers/

https://www.oreilly.com/library/view/applied-cryptography-protocols/9781119096726/08_chap01.html#chap01-sec001

https://terokarvinen.com/application-hacking/#laksyt

https://cryptopals.com/sets/1

https://cryptopals.com/sets/1/challenges/1

https://cryptopals.com/sets/1/challenges/2

https://cryptopals.com/sets/1/challenges/3

https://cryptopals.com/sets/1/challenges/4
