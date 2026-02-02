# h3 No Strings Attached

Tämä tehtävä raportti on luotu Iso-Anttila Lari & Karvinen Tero "Sovellusten Hakkerointi ja Haavoittuvuudet" -Kurssille.

## Laitteisto

Tehtävän aikana käytin seuraavia laitteita ja ohjelmia:

Windows 11, AMD

Oracle VirtualBox 7.2.2, Debian 13, NAT -network.

## a) Strings

Download ezbin-challenges.zip. Run 'passtr'. Find the correct password using 'strings'. Also find the flag. (Preferably without looking at the source, if you can.)

Aloitin lataamalla ja purkamalla tehtävät:

    $ wget https://terokarvinen.com/loota/yctjx7/ezbin-challenges.zip

    $ unzip ezbin-challenges.zip

Siirryin täten ensimmäisen tehtävän pariin:

    $ cd challenges/passtr

Ajoin `./passtr` -skriptin ja se kysyi minulta salasanaa.

<img width="636" height="192" alt="kuva" src="https://github.com/user-attachments/assets/502ea55d-b51c-4702-93b6-38ab7879b986" />

Kuva 1 ./passtr -skripti ja epäonnistunut salasana

Seuraavaksi luin README tiedoston ja päätin kokeilla `Strings` -komentoa.

<img width="1004" height="574" alt="kuva" src="https://github.com/user-attachments/assets/56ca3665-5082-4491-9b79-aa4625f0f3e4" />

Kuva 2 Binääriä selkokielisenä 

Huomasin binäärissä olevan teksti: sala-hakkeri-321 ja päätin kokeilla tätä salasanaa.

<img width="1004" height="145" alt="kuva" src="https://github.com/user-attachments/assets/a5340dde-50b4-4bdb-a1eb-c9e4a8d5d8eb" />

Kuva 3 Oikea salasana ja lippu saatu

## b) Fix the code

Make a new version of the passtr.c program where the password doesn't appear directly as-is in the binary. Demonstrate with a test that the password doesn't appear. (Obfuscation is sufficient.)

Koska oma osaamiseni ohjelmoinnissa on hieman ruosteessa lähdin kysymään AI:lta erilaisia tapoja obfuskoida koodia promptilla:

    "different types of obfuscation for a program code"

Sainkin paljon erilaisia vastauksia joista XOR encryptaus ja itse salasanan pilkkominen tuntui parhaalta vaihtoehdolta.

Lähdinkin kokeilemaan XOR encyptausta, koska tässä ei olisi missään kohtaan kirjoitetu salasanaa selkokielisenä lähdekoodiin. Ajattelin, että tämä olisi parempi tapa salata salasana, kuin kirjoittaa se selkokielisenä pilkottuna muutamaan eri paikkaan.

Luin hieman XOR:sta ja kyselin myös apuja tekoälyltä promptilla: `Give me an exmaple of a XOR encyption in C program code`

<img width="1004" height="544" alt="kuva" src="https://github.com/user-attachments/assets/b71b5609-ff61-4c15-96dd-c46b92fd894c" />

Kuva 4 Ohjelmakoodi uudelleenkirjoitettu

Lisäsin Karvisen alkuperäiseen koodiin rivit:

`char encrypt[] = { 0x38, 0x31, ... 0x6a, 0x00 };`: Luodaan uusi merkkijono, johon tallennetaan salasana hexana.

`for (int i = 0; encrypt[i]; i++) encrypt[i] ^= 0x5b;`: Ottaa edellisestä taulukosta merkin kerrallaan ja purkaa sen XOR avaimella 0x5b.

`if (strcmp(password, encrypt) == 0)`: Vertaa käyttäjän syöttämää salasanaa ja encrypt -muuttujassa olevaa salasanaa.

Tämän jälkeen tallensin uuden koodin ja ajoin komennon `strings passtr` uudestaan.

<img width="1004" height="822" alt="kuva" src="https://github.com/user-attachments/assets/7a10b269-4f00-48ab-8467-87f3bab5f22f" />

Kuva 5 Salasana ei ole näkyvissä

## c) Packd

Run 'packd' from the package ezbin-challenges.zip. What is the password? What is the flag? (This task is slightly more challenging. Write down the approaches you tried and hypotheses you came up with. Hopefully you'll reach the goal yourself, but if not, the walkthrough will be revealed in class...)

Aloitin tehtävän tekemisen samalla tavalla kuin edellisenkin.

Siirryin hakemistoon `challenges/packd` ja ajoin skriptin `./packd`

Ohjelma pyysi salasanaa, mutta tätä minulla ei ollut.

Seuraavaksi kokeilinkin `Strings` -komentoa samalla tavalla kuin aikaisemmin, mutta salasanaa ei löytynyt. Stringsiä katsellessa huomasin kumminkin, että ohjelmakoodi oli pakattu käyttäen UPX ohjelmaa. Täten kävin lukemasta mikä se on ja miten sitä käytetään

Löysin itseni UPX:n virallisilta sivuilta ja päädyin asentamaan ohjelman:

    $ wget https://github.com/upx/upx/releases/download/v5.1.0/upx-5.1.0-amd64_linux.tar.xz

    $ tar -xf upx-5.1.0-amd64_linux.tar.xz

Seuraavaksi tarkistelin UPX help sivuja ja ohjeita miten tätä käytetään.

<img width="1004" height="563" alt="kuva" src="https://github.com/user-attachments/assets/0bd45756-31cf-4941-ba82-19b8355ff0b6" />

Kuva 6 UPX skripti

Ajamalla pelkän `./upx` -skriptin huomasin että `-d` parametrillä saan decomprassattua tiedoston, joten ajoinkin seuraavat komennon:

    $ ./upx -d packd

    $ strings packd

Purkamalla tiedoston ja ajamalla stringsin uudestaan sainkin salasanan selville!

<img width="1004" height="562" alt="kuva" src="https://github.com/user-attachments/assets/eb36a5d2-11d2-487b-95b8-7b1f238b279d" />

Kuva 7 Salasana selkokielisenä

Kokeilinkin vielä salasanaa, että se on varmasti oikein.

<img width="1004" height="159" alt="kuva" src="https://github.com/user-attachments/assets/497cd73d-7265-4564-9a56-61335d15e0cb" />

Kuva 8 Oikea salasana ja lippu

## Lähteet

Application hacking 2026. h3 No Strings Attached (Tero). Luettavissa: https://terokarvinen.com/application-hacking/#laksyt. Luettu 26.1.2026.

UPX team 2024. UPX the Ultimate Packer for eXecutables. Luettavissa: https://upx.github.io/. Luettu: 26.1.2026.

Malforge Academy 2025. XOR Obfuscation: Hiding Shellcode from Basic AV. Luettavissa: https://sid4hack.medium.com/xor-obfuscation-hiding-shellcode-from-basic-av-65940817d35a. Luettu: 26.1.2026.
