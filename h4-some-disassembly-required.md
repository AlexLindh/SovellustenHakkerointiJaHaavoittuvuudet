# h4 Some Dissassembly Required

todo;

lisätä kuvat läppäriltä ja tehdä b) uudestaan jotta saan loppukuvat

## Laitteisto (KESKEN)

## Read/watch/listen and summarize. (KESKEN)

## a) Install Ghidra (KESKEN)

## b) (KESKEN) rever-C. Reverse engineer the packd binary to C language with Ghidra. Find the main program. Give variables descriptive names. Explain the program's operation. Solve the task from the binary, without the original source code.

Aloitin tehtävien tekemisen lataamalla koneelleni tehtäväpaketin (Karvisen) sivuilta komennolla:

    $ wget https://terokarvinen.com/loota/yctjx7/ezbin-challenges.zip
    $ unzip ezbin-challenges.zip

Avasin seuraavaksi Ghidran ja loin uuden projektin ja avasin packd binäärin ohjelmassa.

Tarkastelin listing ja defined strings sivuja, mutta en saanut selville mitään muuta, kuin sen, että binääri on pakattu UPX ohjelmalla.

!KUVA1 packd pakattu UPX ohjelmalla!

Purin packd binäärin käyttäen UPX ohjelmaa (asennusohjeet: oma) ja avaamalla binäärin uudestaan Ghidralla

Nyt kun packd oli purettu niin pystyin tarkaselemaan binäärejä selkokielisinä ja sainkin salasanan `piilos-AnAnAs` selville.

!KUVA2 ghidra ja salasana!

Seuraavaksi muutos ja testaus


## c) If backwards. Modify the passtr program's binary (without the original source code) so that it accepts all passwords except the correct one. Demonstrate with tests that the program works.

Aloitin tehtävän samalla tavalla kuin packd, eli avasin Ghidralla uuden projektin ja laitoin passtr sinne tutkittavaksi.

!KUVA3 alkuperäinen ohjelmakoodi!

Menin suoraan if lausekkeeseen ja huomasin, sen olevan JNZ muodossa eli `Jump if not zero`. Vaihdoin tämän päikseen JZ muotoon eli `Jump if zero`. Eli selkokielellä alkuperäisessä koodissa jos oikean salasanan ja käyttäjän syötetyn argumentin ero on 0 (salasana ja argumentti samat) annetaan FLAG käyttäjälle. Tässä vaihdettiin, että jos oikean salasanan ja argumentit ero on 1 (eli ovat erit) palautetaan FLAG ja oikealla salasanalla ja argumentilla (Difference == 0) ei sitä saada.

!KUVA4. JNZ -> JZ

Seuraavaksi exporttasin muokatun binäärin ja testasin toimintoa.

!KUVA5. Testaus ja onnistuminen

## d) Download the Nora crackme binaries.

git cloonasin tehtävät

## e) Solve Nora crackme01 binary.

Aloitin tehtävät lukemalla README.md läpi ja iskinkin heti tehtävien pariin. Ensin `$ make` -komennolla loin skriptit ja loin sitten Ghidraan uudet projektit ja avasin binäärit siellä.

!KUVA6 crackme01 binääri 

Binäärissä luki selkokielellä salasana "password1", joten päätin testata sitä terminaalissa.

!KUVA7 oikea salasana ja väärä salasana

Salasana toimi ja testasin vielä, että jokin muu salasana ei toiminut.

## e.2) Solve Nora crackme01e binary.

Aloitin samalla tavalla kuin edellisessä kohdassa.

!KUVA8 Binääri ja salasana selkokielellä

Päätin heti kokeilla salasanaa "slm!pass.k"terminaalissa.

!KUVA9 salasana yritykset

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

!KUVA10 Onnistunut "salasana"

Pienellä tuurillä päätin kokeilla tuota, mutta ilmeisesti kelpasi! :D

## Lähteet (KESKEN)

https://terokarvinen.com/application-hacking/#laksyt

https://www.youtube.com/watch?v=oTD_ki86c9I

github.com/NoraCodes/crackmes
