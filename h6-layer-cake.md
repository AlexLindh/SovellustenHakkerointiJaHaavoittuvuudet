# h6 Layer Cake

Tämä tehtävä raportti on luotu Iso-Anttila Lari & Karvinen Tero "Sovellusten Hakkerointi ja Haavoittuvuudet" -Kurssille.

## Laitteisto

Tehtävän aikana käytin seuraavia laitteita ja ohjelmia:

Windows 11, AMD

Oracle VirtualBox 7.2.2, Debian 13, NAT -network.

## Lab0: Tutki tiedostoa h1.jpg, jo opituilla työkaluilla mitä saat selville?

Aloitin lataamalla kohdetiedoston komennolla: `$ wget https://terokarvinen.com/application-hacking/h1.jpg`

Aloin tutkimaan kuvaa perustavoilla ja erilaisilla opituilla työkaluille kuten file ja strings.

<img width="1004" height="101" alt="kuva" src="https://github.com/user-attachments/assets/5c52a588-fe38-41f6-988a-341ad847bacd" />

Kuva 1 Kuvan tutkimista file -komennolla

Filen avulla sain selville, että kuva on JPEG muodossa ja pikselikoko on 1024x1024.

Stringsin avulla sain suurimmaksi osaksi 4-5 merkkisiä jonoja irti kuvasta. Päätin testata myös asettaa stringisille minimipituuden komennolla `$ strings -n 10 h1.jpg`.

<img width="827" height="859" alt="kuva" src="https://github.com/user-attachments/assets/14a63a3e-36e0-4468-8be4-b1fbfbbce063" />

Kuva 2 Kuvan tutkimista strings -komenolla

Stringsillä sain selville, että kuvassa on piilotettu ilmeisemmin jotain word tiedostoja tai asetuksia?

## Lab1: Tutki tiedostoa h1.jpg binwalk (Versio 2.x tai 3.x huom toimivat eri tavalla), mitä tietoja löydät nyt tiedostosta. Mitä työkalua käyttäisit tiedostojen erottamiseen?

Aloitin ensin asentamalla binwalkin komennolla: `$ sudo apt-get install binwalk`.

Päätin heti kokeilla binwalkkia kuvaan ja sain tulosteeksi, että mahdollisesti kuvan metadataan on piilotettu pakattuja tiedostoja. Päätin katsoa binwalkin manuaalisivuilta pystynkö, jotenkin purkamaan näitä tiedostoja ja sain selville, että --extract (-e) lisäkomennolla saisin purettua tiedostot.

<img width="1004" height="318" alt="kuva" src="https://github.com/user-attachments/assets/88b83623-99c3-467c-b160-c5f4290f4ce0" />

Kuva 3 Kuvan tutkimista binwalk -komennolla

<img width="477" height="803" alt="kuva" src="https://github.com/user-attachments/assets/7477c355-77eb-4219-bf26-3163f9981932" />

Kuva 4 Purettu kuvatiedosto

Purettuani huomasin tämän luovan uuden hakemiston ja .zip -tiedoston kyseiseen hakemistoon. Yritin purkaa kyseistä .zip -tiedostoa, mutta se ei ollut mahdollista. Errorin mukaan tiedosto mahdollisesti ei ole oikea .zip -tiedosto.

<img width="775" height="252" alt="kuva" src="https://github.com/user-attachments/assets/8da3174d-85f7-49ce-8066-31c64aef0013" />

Kuva 5 Puretun tiedoston tutkimista

Päätin tarkastaa mikä tiedosto kyseinen 494F5.zip -tiedosto oikeasti on ja lopputulemaksi tuli Microsoft Word dokumentti. 

Päätin vaihtaa tiedoston nimen .docx loppuiseksi, että pääsisin avaamaan sen wordissa komennolla: `$ mv 494F5.zip 494F5.docx` ja kokeilin mennä avaamaan sen wordissa.

LibreOffice antoi ensiksi errorin, että tiedosto on korruptoitunut, mutta voisi yrittää korjata tiedoston. Painoin "Kyllä" ja tämä avasi minulle word tiedoston.

<img width="1004" height="748" alt="kuva" src="https://github.com/user-attachments/assets/11d3b3bb-729f-4b9d-bbdf-8051380d02e0" />

Kuva 6 Piilossa ollut word tiedosto


## Lab2: FOSS (Free Android OpenSource) Tutustu listaan eri android applikaatioita. Valitse listalla itsellesi mielenkiintoisin applikaatio ja mene sen GitHubiin. Lataa ohjelman APK itsellesi ja käytä seuraavia työkaluja tutustuaksesi miten APK:n voi avata. ZIP, JADX, Bytecode-viewer.

## Hardware hacking

### Different tasks:

### decrypt firmware image

Normaali binwalk ei antanut minulle mitään tuosta kuvatiedostosta, joten päätin yrittää tehtävässä asenenettua työkalua ja sain tulosteeksi seuraavan:

<img width="1106" height="520" alt="kuva" src="https://github.com/user-attachments/assets/1f024901-3f68-4893-bef4-0af8877f6adb" />

Kuva ? Key + IV

### Analyse the image file

### extract rootfs from the dump file

### extract rootfs from the image file

### search available applications

### analyse and try to open root password

## Lähteet

Haaga Helia ammattikorkeakoulu. s.a. Sulautetu järjestelmät. Sovellusten hakkerointi ja haavoittuvuudet -opintojakson apumateriaalit Moodlessa. Haaga-Helia ammattikorkeakoulu. Luettu:

Tero Karvinen. 2026. Application hacking - 2026 Spring. Luettavissa: https://terokarvinen.com/application-hacking/#laksyt. Luettu: 
