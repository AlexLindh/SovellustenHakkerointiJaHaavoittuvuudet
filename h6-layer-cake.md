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

Aloitin ensin tutkimalla erilaisia sovelluksia ja valitsin kohdesovelluksekseni "Atmos Weather" -sovelluksen. Asensin sovelluksen .apk:n tekijän github sivujen releases osiosta komennolla:

    $ wget https://github.com/atticuscornett/AtmosWeather/releases/download/v3.0.4/app-release.apk

Loin tämän jälkeen jokaiselle työkalulle uuden hakemiston minne asentaisin ne.

<img width="514" height="173" alt="kuva" src="https://github.com/user-attachments/assets/d621eed2-5df2-4a9d-8b95-e86a20efebb2" />

Kuva 7 Asennettu .apk ja hakemistot

### Zip

Aloitin zipillä ja kuten normaalistikkin unzippaus käytin seuraavaa komentoa:

    $ unzip ../app-release.apk

<img width="1004" height="106" alt="kuva" src="https://github.com/user-attachments/assets/ed01ec0a-dfd8-48cc-878a-e0ce21cc28ed" />

Kuva 8 Purettu .apk tiedosto käyttäen unzip komentoa

### Jadx

Aloitin ensin asentamalla JADX:in hakemalla niiden omilta github sivuilta ja komennolla:

    $ wget https://github.com/skylot/jadx/releases/download/v1.5.4/jadx-1.5.4.zip

Purin tiedoston ja testasin, että ohjelma toimii.

<img width="1004" height="614" alt="kuva" src="https://github.com/user-attachments/assets/8a74539b-3438-4df7-891f-a37629534b72" />

Kuva 9 Tyhjä JADX graafinen käyttöliittymä

Aloitin uuden projektin ja avasin aikaisemmin ladatun .apk tiedoston tällä.

<img width="666" height="758" alt="kuva" src="https://github.com/user-attachments/assets/49f0418f-80ad-4885-9d09-a99802e87f8a" />

Kuva 10 JADX käyttöliittymä .apk tiedostolle

Seuraavaksi testasin myös purkamista käyttäen JADX työkalua seuraavalla komennolla.

    $ jadx -d purku ../app-release.apk

`jadx -d`: Komento ja -d purkaa tiedoston.

`purku`: Luo hakemiston nimeltä "purku" ja vie puretun tiedoston sinne.

`../app-release.apk`: Polku missä purettava tiedosto on.

<img width="1004" height="419" alt="kuva" src="https://github.com/user-attachments/assets/14831a63-363c-4509-8ab5-84a21662b1cc" />

Kuva 11 Purettu .apk -tiedosto käyttäen jadx:ia

### Bytecode-viewer

Aloitin asentamalla Bytecode-viewerin githubista komennolla:

    $ wget https://github.com/Konloch/bytecode-viewer/releases/download/v2.13.2/Bytecode-Viewer-2.13.2.jar

Käynnistin sovelluksen komennolla:

    $ java -jar Bytecode-Viewer-2.13.2.jar

<img width="1000" height="708" alt="kuva" src="https://github.com/user-attachments/assets/77f198a7-40e9-4948-9c4f-1105fd7232d1" />

Kuva 12 Tyhjä bytecode-viewer graafinen käyttöliittymä

Loin uuden projektin Bytecode-viewerissä ja toin aikaisemmin lataamani .apk -tiedoston tähän.

<img width="1004" height="333" alt="kuva" src="https://github.com/user-attachments/assets/3bf43d4e-6dbe-4171-b051-b18148039b4d" />

Kuva 13 Avattu .apk -tiedosto käyttäen bytecode-vieweriä

## Hardware hacking

### Different tasks:

### decrypt firmware image

Normaali binwalk ei antanut minulle mitään tuosta kuvatiedostosta, joten päätin yrittää tehtävässä asenenettua työkalua ja sain tulosteeksi seuraavan:

<img width="1106" height="520" alt="kuva" src="https://github.com/user-attachments/assets/1f024901-3f68-4893-bef4-0af8877f6adb" />

Kuva 14 Key + IV

### Analyse the image file

### extract rootfs from the dump file

Sain purettua dumppi -tiedoston ja saamaan sieltä rootfs hakemistot/tiedostot seuraavilla komennoilla:

    $ binwalk -e dump-tapo-c200v3-1.4.2.bin
    $ cd _dump-tapo-c200v3-1.4.2.bin.extracted
    $ cd squashfs-root

<img width="1004" height="490" alt="kuva" src="https://github.com/user-attachments/assets/8c1a2f3c-2045-4351-ac3b-3dd6283b2a83" />

Kuva 15 Purettu dumppitiedosto ja sen sisältö

### extract rootfs from the image file

Aloitin ensin binwalkilla, mutta se ei löytänyt mitään. Mutta kun olin aikaisemmin tehtävässä käyttänyt bin/tp-link-decrypt -työkalua, joka oli luonut uuden tiedoston .bin tiedostosta nimeltä .bin.dec.

Sain binwalkilla tämän tiedston purettua ja avattua.

<img width="1004" height="578" alt="kuva" src="https://github.com/user-attachments/assets/5bb1aa7b-2a6d-4400-8298-23c7286a3f52" />

Kuva 16 Purettu kuvatiedosto

Puretun kuvatiedoston squashfs-root/ -hakemistossa oli samanlainen sisältö kuin dumpissa.

### search available applications

### analyse and try to open root password

## Lähteet

Atticuscornett. 2025. AtmosWeather. Luettavissa: https://github.com/atticuscornett/AtmosWeather/. Luettu 24.2.2026.

Haaga Helia ammattikorkeakoulu. s.a. Hardware hacking. Sovellusten hakkerointi ja haavoittuvuudet -opintojakson apumateriaalit Moodlessa. Haaga-Helia ammattikorkeakoulu. Luettu: 23.2.2026.

Haaga Helia ammattikorkeakoulu. s.a. Sulautetu järjestelmät. Sovellusten hakkerointi ja haavoittuvuudet -opintojakson apumateriaalit Moodlessa. Haaga-Helia ammattikorkeakoulu. Luettu: 23.2.2026.

Konloch. 2026. bytecode-viewer. Luettavissa: https://github.com/Konloch/bytecode-viewer/. Luettu 24.2.2026.

Robbins. 2025. tp-link-decrypt. Luettavissa: https://github.com/robbins/tp-link-decrypt. Luettu 23.2.2026.

Skylot. 2026. jadx. Luettavissa: https://github.com/skylot/jadx/. Luettu 24.2.2026.

Tero Karvinen. 2026. Application hacking - 2026 Spring. Luettavissa: https://terokarvinen.com/application-hacking/#laksyt. Luettu: 23.2.2026.

