# h6 Layer Cake

Tämä tehtävä raportti on luotu Iso-Anttila Lari & Karvinen Tero "Sovellusten Hakkerointi ja Haavoittuvuudet" -Kurssille.

## Laitteisto

Tehtävän aikana käytin seuraavia laitteita ja ohjelmia:

Windows 11, AMD

Oracle VirtualBox 7.2.2, Debian 13, NAT -network.

## Lab0: Tutki tiedostoa h1.jpg, jo opituilla työkaluilla mitä saat selville?

Aloitin wget https://terokarvinen.com/application-hacking/h1.jpg

Aloin tutkimaan kuvaa perustavoilla ja erilaisilla opituilla työkaluille kuten file ja strings.

!KUVA1! file

Filen avulla sain selville, että kuva on JPEG muodossa ja pikselikoko on 1024x1024.

Stringsin avulla sain suurimmaksi osaksi 4-5 merkkisiä jonoja irti kuvasta. Päätin testata myös asettaa stringisille minimipituuden komennolla `$ strings -n 10 h1.jpg`.

!KUVA2! strings

Stringsillä sain selville, että kuvassa on piilotettu ilmeisemmin jotain word tiedostoja tai asetuksia?

## Lab1: Tutki tiedostoa h1.jpg binwalk (Versio 2.x tai 3.x huom toimivat eri tavalla), mitä tietoja löydät nyt tiedostosta. Mitä työkalua käyttäisit tiedostojen erottamiseen?

Aloitin ensin asentamalla binwalkin komennolla: `$ sudo apt-get install binwalk`.

Päätin heti kokeilla binwalkkia kuvaan ja sain tulosteeksi, että mahdollisesti kuvan metadataan on piilotettu pakattuja tiedostoja. Päätin katsoa binwalkin manuaalisivuilta pystynkö, jotenkin purkamaan näitä tiedostoja ja sain selville, että --extract (-e) lisäkomennolla saisin purettua tiedostot.

!KUVA3! binwalk

!KUVA4! purettu

Purettuani huomasin tämän luovan uuden hakemiston ja .zip -tiedoston kyseiseen hakemistoon. Yritin purkaa kyseistä .zip -tiedostoa, mutta se ei ollut mahdollista. Errorin mukaan tiedosto mahdollisesti ei ole oikea .zip -tiedosto.

!KUVA5! unzip+file

Päätin tarkastaa mikä tiedosto kyseinen 494F5.zip -tiedosto oikeasti on ja lopputulemaksi tuli Microsoft Word dokumentti. 

Päätin vaihtaa tiedoston nimen .docx loppuiseksi, että pääsisin avaamaan sen wordissa komennolla: `$ mv 494F5.zip 494F5.docx` ja kokeilin mennä avaamaan sen wordissa.

LibreOffice antoi ensiksi errorin, että tiedosto on korruptoitunut, mutta voisi yrittää korjata tiedoston. Painoin "Kyllä" ja tämä avasi minulle word tiedoston.

!KUVA6! word tiedosto.


## Lab2: FOSS (Free Android OpenSource) Tutustu listaan eri android applikaatioita. Valitse listalla itsellesi mielenkiintoisin applikaatio ja mene sen GitHubiin. Lataa ohjelman APK itsellesi ja käytä seuraavia työkaluja tutustuaksesi miten APK:n voi avata. ZIP, JADX, Bytecode-viewer.

## Lähteet
