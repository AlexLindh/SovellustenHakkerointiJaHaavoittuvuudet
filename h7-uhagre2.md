# h6 Uhagre2

Tämä tehtävä raportti on luotu Iso-Anttila Lari & Karvinen Tero "Sovellusten Hakkerointi ja Haavoittuvuudet" -Kurssille.

## Laitteisto

Tehtävän aikana käytin seuraavia laitteita ja ohjelmia:

Windows 11, AMD

Oracle VirtualBox 7.2.2, Debian 13, NAT -network.

## x) Read/watch/listen & summarize

1.1 Terminology

- Viestien salaustekniikkaa kutsutaan kryptokrafiaksi
- Salaustekstien purkamista kutsutaan kryptoanalyysiksi
- Kryptokrafia ja -analyysi kuuluvat kryptologian alle
- Kryptokrafiassa salauksen lisäksi odotetaan täyttyvän myös todentaminen, eheys ja kiistämättömyys
- Kryptografinen salausmenetelmä (cipher) on matemaattinen funktio, jota käytetään halutun viestin salaukseen ja purkamiseen

1.4 Simple XOR

- XOR on ekslusiivinen -or funktio joka toimii seuraavanlaisesti:

  0 + 0 = 0

  0 + 1 = 1

  1 + 0 = 1
  
  1 + 1 = 0

  Eli verrattavien bittien arvo täytyy olla eri, jotta tulos voi olla tosi

1.7 Large numbers

Kirjan aikana verrataan paljon suuriin lukuihin, joita ei välttämättä pysty suoraan helposti käsittää. Tekijä oli luonutkin taulukon missä käsitteli suuria lukuja, jotta pystyisimme helpommin ymmärtämään kuinka suurista luvuista on oikeasti kyse. Listaan taulukkoon suraavasti muutaman näistä:

|  Tapaus  |  Numero | 
| --- | --- | 
|  Aika seuraavaan jääkauteen  |  14 000 vuotta (2^14)  |
|  Mahdollisuus voittaa päävoitto lotossa (usa) |  1 : 4 000 000 (2^22)  |
|  Universumin ikä  |  10^10 vuotta (2^34)  |
|  Atomien määrä maapallolla  |  10^51 (2^170)  |
|  Atomien määrä universumissa  |  10^77 (2^265)  | 
|  Aika, jolloin kaikke aine muuttuu mustiksi aukoiksi  |  10^10^76 vuotta |


Python basics for Hackers

- Koodin kirjoittaminen pienissä osissa helpottaa koodin luontia ja pienentää virheilmoituksien tapahtuessa kohdealuetta
- REPL erilaisia kattavia ominaisuuksia:

    - `Nuolinäppäimillä`: pääset selaamaan historiaa
    - `CTRL + R pääset`: hakemaan historiasta komentoja
    - `TAB`: ennustava täyttö
    - `F5`: Tulostaa python skriptin tulosteen

- Englanninkielisessä kirjallisuudessa yleisimmät kirjaimet: ETAOIN SHRDLU
- Suomenkielisessä kirjallisuudessa yleisimmät kirjaimet: AINTE SLOUK
- Hyödyllisiä kirjastoja mitä käyttää:

    - requests - web -sivujen lataaminen
    - binascii - muutaa hex tekstiksi (b2a_hex)
    - base64 - ASCII -suojaus binäärille

## CryptoPals Set 1 -challenges

## a) 1. Convert hex to base64

Tehtävänanto luettavissa: https://cryptopals.com/sets/1/challenges/1

Tehtävässä pyydettiin luomaan skripti, joka pyytää hex syötettä ja muuttaa sen base64 muotoon.

<img width="1004" height="403" alt="kuva" src="https://github.com/user-attachments/assets/2431d8bf-5c09-4dc0-b84a-9d1c351bc7d3" />

Kuva 1 skripti ja lopputulos

`hex = input()`: Luo muuttujan nimeltä "hex", joka saa arvon käyttäjän syötteestä.

`tavut = bytes.fromhex(hex)`: Luo muuttujan nimeltä "tavut", joka saa arvon "hex" -merkkijonon tavukäännöksestä.

`b64 = base64.b64encode(tavut)`: Luo muuttujan "b64", joka saa base64 koodatun arvon "tavut" -tavujonosta.

## b) 2. Fixed XOR

Tehtävänanto luettavissa: https://cryptopals.com/sets/1/challenges/2

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

Tehtävänanto luettavissa: https://cryptopals.com/sets/1/challenges/3

Tehtävässä oli hex -merkkijono, jonka kaikki tavut oltiin XORrattu yhdellä tuntemattomalla merkillä, joka pitäisi löytää käyttämällä skriptiä, joka tekee vertauksen kaikilla merkeillä ja laskee todennäköisyyden ja tulostaa parhaan mahdollisen vastauksen. Todennäköisyys laskettaisiin kirjaimilla, mitä käytetään yleisimmin englanninkielisessä kirjallisuudessa.

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

    # Tulostetaan parhaat 5 tulosta, jossa mukana pisteet, käytetty avain ja teksti
    for s, key, plaintext in results[5:]:
        print("Score: {s}, Key: {Key(hex)}, Text: {plaintext.decode}")

<img width="984" height="233" alt="kuva" src="https://github.com/user-attachments/assets/6edaca99-ea36-4a35-9ce6-920f74668cf0" />

Kuva 4 Skriptin lopputulos

Tehtävän voittajana toimi tänään MCpekoni merkillä 0x58 eli "X".

## d) 4. Detect single-character XOR

Tehtävänanto luettavissa: https://cryptopals.com/sets/1/challenges/4

Tehtävä oli samanlainen, kuin edellinen, mutta erona tässä oli vain, että alkuperäinen merkkijono on myös tuntematon, joka löytyisi ladattavasta listasta, jossa on mukana 60 erilaista hex -merkkijonoa. Aloitin lataamalla kyseisen listan `$ wget https://cryptopals.com/static/challenge-data/4.txt`

Kysyin tehtävässä hieman apua tekoälyltä, promptilla: "Kuinka tehdä xor vertailu hex -merkkijonoihin, jotka löytyvät ulkoisesta tekstitiedosta"

Vastauksena sain suhtkoht yksinkertaisen vastauksen, että samalla tavalla kuin edellinen luomani skripti, mutta ilman absoluuttista hexadesimaalimerkkijonoa vaan se hakee tiedostosta hex jonot ja tekee vertailun. Sain muokattua edellistä skriptiä seuraavalla tavalla:

    # Muuttuja missä yleisimmät kirjaimet englanninkielisessä kirjallisuudessa
    common = "ETAOIN SHRDLUetaoinshrdlu"

    # Funktio joka laskee pisteet sen perusteella, kuinka monta yleistä kirjainta se pitää sisällään
    def scoring(text: bytes) -> int:
        return sum(c in common for c in text)

    # Muuttujien lähtökohdat, joihin tallennetaan skriptin aikana parhaat tulokset
    best_score = -1
    best_key = None
    best_plaintext = None
    best_line = None

    # avaa tiedoston "4.txt" ja käy sen läpi rivi kerrallaan muuntaen kaikki hexadesimaalimerkkijonot tavujonoiksi
    with open ("4.txt", "r") as f:
        for line in f:
            hex = line.strip()

            if not hex:
                continue

            translate = bytes.fromhex(hex)

            # Funktio joka käy läpi kaikki mahdolliset avaimet
            for key in range(256):
                plaintext = bytes([b ^ key for in b in translate])
                s = scoring(plaintext)

                # Jos jokin avain saa enemmän pisteitä niin se tallennetaan parhaaksi avaimeksi, kunnes kaikki merkit käyty läpi
                if s > best_score:
                    best_score = s
                    best_key = key
                    best_plaintext = plaintext
                    best_line = hex

    # Tulostaa parhaan tuloksen edellisen skriptin perusteella
    print("Paras rivi: ", best_line)
    print("Käytetty avain: ", hex(best_key))
    print("Purettu teksti: ", best_plaintext.decode)

<img width="1004" height="179" alt="kuva" src="https://github.com/user-attachments/assets/d40a59a6-cbbc-4b46-8787-4e2bd731e06b" />

Kuva 5 Skriptin lopputulos

## Lähteet

CryptoPals. s.a. Crypto Challenge Set 1. Luettavissa: https://cryptopals.com/sets/1. Luettu: 

karvinen. 2024. Getting Started with Cryptopals using Python. Luettavissa: https://terokarvinen.com/getting-started-python-cryptopals/. Luettu: 

Karvinen. 2024. Python Basics for Hackers. Luettavissa: https://terokarvinen.com/python-for-hackers/. Luettu:

Karvinen. 2026. Application hacking - 2026 Spring. Luettavissa: https://terokarvinen.com/application-hacking/#laksyt. Luettu:

Schneier, B. 2015: Applied Cryptography. O'Reilly. E-kirja. Luettavissa: https://www.oreilly.com/library/view/applied-cryptography-protocols/9781119096726/08_chap01.html#chap01-sec001. Luettu: 
