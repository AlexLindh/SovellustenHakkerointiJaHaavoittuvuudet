# h4 Some Dissassembly Required

Tämä tehtävä raportti on luotu Iso-Anttila Lari & Karvinen Tero "Sovellusten Hakkerointi ja Haavoittuvuudet" -Kurssille.

## Laitteisto

Tehtävän aikana käytin seuraavia laitteita ja ohjelmia:

Windows 11, AMD

Oracle VirtualBox 7.2.2, Debian 13, NAT -network.

## Read/watch/listen and summarize.

Hammond 2022: https://www.youtube.com/watch?v=oTD_ki86c9I

Videolla John käänteismallintaa ja ratkaisee PicoCTF #42 tehtävän 'bbbloat'.

Erilaisia käytettyjä ohjelmia `ltrace`, `strace`, `ghidra`
- ltrace on virheenkorjausohjelma, jota käytetään käyttäjäsovelluksien kutsujen näyttämiseen.
- strace on ohjelma, joka pystyy hakemaan järjestelmäkutsuja.
- ghidra on käänteismallinnustyökalu. Käytetään binäärien kääntämiseen selkokielisiksi.

Ghidra asennetaan githubista ja tiedosto puretaan. Tarvitsee myös javan asennuksen.

Ghidran käyttö:
- Luodaan uusi projekti (CTRL+N)
- Tuodaan uusi tiedosto (I)
- Avataan tiedosto tuplaklikkaamalla
- Analyze -> Yes

Erilaisia käytettyjä ghidran työkaluja:
- Defined Strings, voidaan tarkastella erilaisia muutujia.
- Decompiler, voidaan tarkastella binääriä selkokielellä.
- Rename (L), voidaan muokata funkitoiden nimiä helpotaksemme käänteismallinnusta.


## a) Install Ghidra

Asensin Ghidran ennen tuntia kyseisen ohjeiden mukaisesti (Hooper 2023).

Aloitin Ghidran asentamisen hakemalla githubista uusimman version (NationalSecurityAgency 2026), ja siirtämällä sen /opt/ -hakemistoon.

        $ wget https://github.com/NationalSecurityAgency/ghidra/releases/download/Ghidra_12.0.2_build/ghidra_12.0.2_PUBLIC_20260129.zip
        $ mv ghidra_10.2.3_PUBLIC_20230208.zip /opt/

Tämän jälkeen purin .zip tiedoston ja vaihdoin sen nimen.

        $ sudo unzip ghidra_10.2.3_PUBLIC_20230208.zip

        $ sudo mv ghidra_10.2.3_PUBLIC ghidra

Asensin vielä uusimman version javasta ja ghidra toimi käynnistämällä sen ajamalla ohjelman ./ghidraRun.

## b) rever-C. Reverse engineer the packd binary to C language with Ghidra. Find the main program. Give variables descriptive names. Explain the program's operation. Solve the task from the binary, without the original source code.

Aloitin tehtävien tekemisen lataamalla koneelleni tehtäväpaketin (Karvinen 2026) sivuilta komennolla:

    $ wget https://terokarvinen.com/loota/yctjx7/ezbin-challenges.zip
    $ unzip ezbin-challenges.zip

Avasin seuraavaksi Ghidran ja loin uuden projektin ja avasin packd binäärin ohjelmassa.

Tarkastelin listing ja defined strings sivuja, mutta en saanut selville mitään muuta, kuin sen, että binääri on pakattu UPX ohjelmalla.

<img width="1004" height="81" alt="kuva" src="https://github.com/user-attachments/assets/a260e4ea-81a6-4ed2-896a-f1b0f3c27e29" />

Kuva 1 Packd pakattu käyttäen UPX ohjelmaa

Purin packd binäärin käyttäen UPX ohjelmaa (Alex Lindh 2026) ohjeiden mukaisesti ja avaamalla binäärin uudestaan Ghidralla

Nyt kun packd oli purettu niin pystyin tarkaselemaan binäärejä selkokielisinä ja sainkin salasanan `piilos-AnAnAs` selville.

<img width="1004" height="372" alt="kuva" src="https://github.com/user-attachments/assets/847039aa-e4e0-44b0-bada-a6a2771bdd83" />

Kuva 2 Purettu binääri ja salasana selkokielisenä

## c) If backwards. Modify the passtr program's binary (without the original source code) so that it accepts all passwords except the correct one. Demonstrate with tests that the program works.

Aloitin tehtävän samalla tavalla kuin packd, eli avasin Ghidralla uuden projektin ja laitoin passtr sinne tutkittavaksi.

<img width="1004" height="338" alt="kuva" src="https://github.com/user-attachments/assets/86d7aede-f974-4f78-9cfd-b11c455400b6" />

Kuva 3 Alkuperäinen binääri

Menin suoraan if lausekkeeseen ja huomasin, sen olevan JNZ muodossa eli `Jump if not zero`. Vaihdoin tämän päikseen JZ muotoon eli `Jump if zero`. Eli selkokielellä alkuperäisessä koodissa jos oikean salasanan ja käyttäjän syötetyn argumentin ero on 0 (salasana ja argumentti samat) annetaan FLAG käyttäjälle. Tässä vaihdettiin, että jos oikean salasanan ja argumentit ero on 1 (eli ovat erit) palautetaan FLAG ja oikealla salasanalla ja argumentilla (Difference == 0) ei sitä saada.

<img width="709" height="164" alt="kuva" src="https://github.com/user-attachments/assets/e688fd6f-aa4a-4c91-adfa-e5f24634addc" />

Kuva 4 JNZ muoto 

Seuraavaksi exporttasin muokatun binäärin ja testasin toimintoa.

<img width="1004" height="370" alt="kuva" src="https://github.com/user-attachments/assets/5026ce70-ea28-47a5-9fab-e6ec057ffed4" />

Kuva 5 Ohjelman testaus

Nyt väärällä salasanalla pääsee ohjelmassa eteenpäin ja oikealla salasanalla ei.

## d) Download the Nora crackme binaries.

Latasin tehtävät githubista (NoraCodes 2023) git clone komennolla.

## e) Solve Nora crackme01 binary.

Aloitin tehtävät lukemalla README.md läpi ja iskinkin heti tehtävien pariin. Ensin `$ make` -komennolla loin skriptit ja loin sitten Ghidraan uudet projektit ja avasin binäärit siellä.

<img width="1004" height="360" alt="kuva" src="https://github.com/user-attachments/assets/7a28a4f1-ed91-4747-95c9-216491ebc526" />

Kuva 6 Crackme01 binääri

Binäärissä luki selkokielellä salasana "password1", joten päätin testata sitä terminaalissa.

<img width="816" height="173" alt="kuva" src="https://github.com/user-attachments/assets/2858284f-1322-4aa4-9a48-4935ae65a641" />

Kuva 7 Salasanojen testaus

Salasana toimi ja testasin vielä, että jokin muu salasana ei toiminut.

## e.2) Solve Nora crackme01e binary.

Aloitin samalla tavalla kuin edellisessä kohdassa.

<img width="1004" height="419" alt="kuva" src="https://github.com/user-attachments/assets/5ab7c14c-8f63-491c-9d47-7f5046a503da" />

Kuva 8 Crackme01e binääri

Salasana oli taas selkokielisenä ja päätin heti kokeilla salasanaa "slm!pass.k" terminaalissa.

<img width="913" height="356" alt="kuva" src="https://github.com/user-attachments/assets/b88bd0fb-2dce-4f96-a219-39f73acdd3c4" />

Kuva 9 Salasana yritykset

Huomasin, että huutomerkin kanssa minulle tulee eri argumentti, kuin se yrittäisi ajaa jotain uutta muuta koodia, joten laitoin salasanan hipsukoiden sisään ja pääsin tällä läpi.

## f) Nora crackme02. Name the main program's variables from the reverse-engineered binary and explain the program's operation. Solve the binary.

Aloitin samalla tavalla tämän tehtävän kuin 2 edellistä.

Avasin binäärin ja lähdin tutkimaan sitä

    undefined8 main(int param_1, long param_2)
    
    {
      char cVar1;
      char cVar2;
      undefined8 uVar3;
      long lVar4;

      if (param_1 == 2) {
        cVar2 = 'p';
        lVar4 = 0;
        do {
          cVar1 = *(char *)(*(long *)(param_2 + 8) + lVar4);
          if (cVar1 == '\0') break;
          if (cVar2 + -1 != (int)cVar1) {
            printf("No, %s is not correct.\n");
            return 1;
          }
          cVar2 = "password1"[lVar4 + 1];
          lVar4 = lVar4 + 1;
        } while (cVar2 != '\0');
        printf/"Yes, %s is correct!\n");
        uVar3 = 0;
      }
      else {
        puts("Need exactly one argument.");
      }
      return uVar3;
    }

Hieman luettuani koodia aloin muuttelemaan eri muuttujia eri nimisiksi, jos se avaisi hieman miten koodi toimii. Koodista tulikin seuraavanlainen.
    
    undefined8 main(int argumentCount, long param_2)
    
    {
      char CorrectPasswd;
      undefined8 uVar3;
      long lVar4;
      char WrittenPasswd;

      if (argumentCount == 2) { // 4. Muutos
        CorrectPasswd = 'p';
        lVar4 = 0;
        do {
          WrittenPasswd = *(char *)(*(long *)(param_2 + 8) + lVar4);
          if (WrittenPasswd == '\0') break; // 3. Muutos.
          if (CorrectPasswd + -1 != (int)WrittenPasswd) { // 2. Muutos
            printf("No, %s is not correct.\n");
            return 1;
          }
          CorrectPasswd = "password1"[lVar4 + 1]; // 1. Muutos
          lVar4 = lVar4 + 1;
        } while (CorrectPasswd != '\0');
        printf/"Yes, %s is correct!\n");
        uVar3 = 0;
      }
      else {
        puts("Need exactly one argument.");
      }
      return uVar3;
    }

Muutos nro1: `Oikea salasana on "password1", johon on lisätty jotakin`

Muutos nro2: `Jos "oikea salasana + -1" ei ole sama kuin käyttäjän kirjoittama yritys tulee epäonnistut kirjautuminen.`

Muutos nro3: `Jos käyttäjän kirjoittama salasana on täysin sama kuin '/0' looppi loppuu.`

Muutos nro4: `Jos käyttäjän syöttämä määrä argumentteja on kaksi jatketaan seuraaviin kohtiin, jos ei ole tasan kaksi, pyytää koodi juuri yhtä argumenttiä ajettavan argumentin perään.` eli terminaalissa seuraavasti:

    $ ./crackme02.64 heissulivei

./crackme02.64: `ajettava skripti & 1. argumentti`

heissulivei: `käyttäjän kirjoittama salasanayritys & 2. argumentti`

Lähdin liikkelle muutoksessa nro3 olevasta pointista. Jos antaisin salasanaksi "/0" eli en mitään pääsisinkö suoraan läpi.

<img width="723" height="117" alt="kuva" src="https://github.com/user-attachments/assets/b4943846-e651-48ff-926a-115d91d453a7" />

Kuva 10 "Oikea" salasana

Pienellä tuurillä päätin kokeilla tuota, mutta ilmeisesti kelpasi! :D

## Lähteet

Alex Lindh. 2026. h3 No Strings Attached. Luettavissa: https://github.com/AlexLindh/SovellustenHakkerointiJaHaavoittuvuudet/blob/main/h3-no-strings-attached.md. Luettu 3.2.2026.

Hooper J. 2023. How to install ghidra. Luettavissa: https://medium.com/@ecojumper30/how-to-install-ghidra-f6592ab002bb. Luettu: 3.2.2026.

John Hammond. 27.4.2022. GHIDRA for Reverse Engineering (PicoCTF 2022 #42 'bbbloat'). Katsottavissa: https://www.youtube.com/watch?v=oTD_ki86c9I. Katsottu: 3.2.2026.

NationalSecurityAgency. 2026. ghidra releases. Luettavissa: https://github.com/NationalSecurityAgency/ghidra/releases. Luettu: 3.2.2026.

NoraCodes. 2023. crackmes. Luettavissa: https://github.com/NoraCodes/crackmes. Luettu: 3.2.2026.

Tero Karvinen. 2026. Application hacking - 2026 Spring. Luettavissa: https://terokarvinen.com/application-hacking/#laksyt. Luettu: 3.2.2026.


