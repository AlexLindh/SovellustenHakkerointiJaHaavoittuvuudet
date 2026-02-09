# h5 It's Alive!

Tämä tehtävä raportti on luotu Iso-Anttila Lari & Karvinen Tero "Sovellusten Hakkerointi ja Haavoittuvuudet" -Kurssille.

## Main.cpp

Aloitin ensin kääntämällä lähdekoodin ajettavaksi binääriksi komennolla: `$ g++ main.cpp -o main` ja ajamalla sen `$ ./main`.

Ohjelma kysyi numeroa ja kaikki laitetut numerot palautti arvon 0.

Lähdin tutkimaan binääriä GNU debuggerin avulla, komennolla: `$ gdb ./main`

En saanut listattua lähdekoodia, enkä ymmärtänyt oikein mistään mitään joten loin uuden debugatun version komennolla: `$ g++ main.cpp -g -Wall -Werror -o main-dbg`.

Nyt sain listattua koodin ja pääsin ymmärtämään sitä hieman paremmin.

<img width="617" height="848" alt="kuva" src="https://github.com/user-attachments/assets/96ffc64e-c8ed-4b18-b212-8c77d8442471" />

Kuva 1 Alkuperäinen ohjelmakoodi

Lähdin siten ohjeiden mukaisesti liikkumaan ja tarkistelemaan samalla koodia.

Huomasinkin, että koodissa oleva muuttuja, joka kertoo itsensä aina yhden pienemmällä luvulla, kertoo itsensä myös 0, joten kaikki numerot joita kokeillaan syöttää ohjelmaan tulostaa aina 0.

Lähdin korjaamaan koodia seuraavanlaisesti:

    int main()
    {
        int n(0);                 // Luodaan uusi muuttuja: n, jonka lähtöarvo on 0 
        cin>>n;                   // Tallennetaan käyttäjän syöttämä arvo muuttujaan n 
        long val=factorial(n);    // Luodaan uusi muuttuja long val, jonka arvo saadaan funktiosta factorial 
        cout<<val;                // Tulostetaan funktion saama arvo 
        return 0;                 // Lopettaa koodin 
    }

    long factorial(int n)
    {
        long result(1);
        while(n--)                // Ongelma tässä kohtaa koodia 
        {
            result*=n;
        }
        return result;
    }

Uskoin täten ongelman olevan funktion sisällä mikä laskee muuttujan n arvoa, joten lähdin kysymään tekoälyltä miten tämän voisi korjata.

Sain vastaukseksi, että koodissa oleva kohta: `while(n--)` kertoo ensin result arvon n:n arvolla ja vasta sitten miinustaa yhden.

Vaihdoin täten `n--` -> `--n`, joka ensin vähentää luvusta yhden ja sitten vasta kertoo n:n arvolla. 

Ymmärsin lukiessani, että nytten ei tule ongelmaa jossa kerrotaan luku 0, koska vähennyksen jälkeen koodi itsessään tarkistaa onko n > 0 ja kertoo luvun.

<img width="473" height="239" alt="kuva" src="https://github.com/user-attachments/assets/f7d3a632-778c-48d8-8356-21fb87010da2" />

Kuva 2 Ohjelmakoodin testaus
  
## lab0

Aloitin ensiksi ajamalla ohjelman `./buggy_program` ja huomasin, että ohjelma tulostaa tekstiä.

Loin debugatun version lähdekoodista komennolla: `g++ buggy_program.c -g -Wall -Werror -o buggy-gdb` ja ajoin sen.

Lähdin samalla lähestymistavalla kuin edellisessä kohdassa ja oletin, että tulostetut numerot olivat väärässä järjestyksessä tai vuotaa yli niin kuin kommenteissa sanottiin.

    void buggy_funtion(int *arr, int size) {
      for (int i = 0; i <= size; i++=) {    // Huomaa: <= aiheuttaa puskuriylivuodon
        printf("Element %d: %d\n", i, arr[i];
      }
    }

    int main() {
      int numbers[] = {1, 2, 3, 4, 5,};
      buggy_function(numbers, 5);    // Virheellinen koko
      return 0;
    }

Lähdin analysoimaan koodia, josta sain selville ainakin että:

- Funktio tulostaa tekstin "Element (numero1): (numero2), josta numero1 tulee muuttujasta i, joka alkaa 0 ja numero2 tulee taulukosta samasta kohtaa kuin i:n arvo.

Täten se kulkee seuraavanlaisesti:

    Element 0: 1
    Element 1: 2
    ...
    Element 4: 5
    Element 5: 0

Lähdin tutkimaan enemmän koodissa olevasta kommentista, jossa kerrottiin että `<=` aiheuttaa puskuriylivuodon.

Sain selville, että ylivuotoa tapahtuu koska i arvo yrittää lukea taulukosta indexiä 5, jota ei ole olemassa. Täten yhtäsuuruusmerkki on otettava pois.

<img width="1004" height="544" alt="kuva" src="https://github.com/user-attachments/assets/71a70acf-2f9e-4ef6-91db-9c9210bfcecd" />

Kuva 3 Ohjelmakoodin testaus

## lab1

Aloitin ajamalla ihan ensiksi ohjelman josta tuli seuraava syöte takaisin:

    alex@debian:~/h5/lab1$ ./gdb_example1
    Khoor/#zruog1
    Segmentation fault

lähdin tämän jälkeen debuggerilla ohjelman kimppuun!

<img width="756" height="755" alt="kuva" src="https://github.com/user-attachments/assets/961bb645-cae6-4913-b536-034ba481f861" />

Kuva 4 Alkuperäinen ohjelmakoodi

Sain koodista sen verran selville, että siinä tulostetaan teksti, jonka jokaiseen merkkiin on lisätty muuttuja i=3. Päätin testata muuttaa sanan "Hello, world." ASCII numeroiksi ja lisätä jokaiseen 3. Täten sain vastaukseksi Khoor/#zruog1. 

Käydessäni debuggeria läpi huomasin sen ottavan teksin "Hello, world." ja tekevän sen kaikille kirjaimille muutoksen, jonka jälkeen se tulosti kyseisen tekstin. Bad_messagen=NULL kohdassa tätä ei tapahtunut. Ajattelin sen olevan sen takia koska null ei ole mitään, joten ei ole mitään mitä muokata. Päätin laittaa null tekstin heittomerkkien sisään ja testaamalla vielä toisella tekstipätkällä koodia.

<img width="669" height="839" alt="kuva" src="https://github.com/user-attachments/assets/31ba7212-b5f4-47e7-8440-338d7cd2135a" />

Kuva 5 Ohjelmakoodin testaus

Ongelma löytyikin juuri siitä ja heittomerkit korjasivat tilanteet!

## lab2

Lähdin aluksi GNU debuggerilla ratkomaan tehtävää, mutta en löytänyt juuri mistään mitään. Lähdin tämän jälkeen rivi riviltä tutkimaan jos mikään aukeaisi minulle :D

Asetin ensiksi break kohdan mainille sekä funktiolle check_password, jotka sain selville komennolla: `info functions`.

Ainoat asiat mitkä huomasin:

`<main+62>  rep movsl  %ds:(%rsi),%es:(rdi)`: Jonkinlainen tarkistus muuttujan rcx arvolle 20 kertaa, samalla rsi ja rdi arvo nousee neljällä tavulla.

`<main+110>  call  0x555555555060 <_isoc99_scanf@plt>`: Kysyy syötettä salasanalle ja tallentaa sen mahdollisesti %rdi muuttujaan??

`<main+123>  call  0x55555555525a <mAsdf3a>`: Aloittaa uuden funktion, jossa mahdollisesti tarkistetaan ja verrataan syöte(salasana) oikean salasanan kanssa??

`<main+135>  jne  0x555555555119 <main+153>`: Tapahtuu hyppy kohtaan <main+153> salasana syötteen tarkistuksen jälkeen. Mahdollinen if -lauseke joka hyppää seuraavaan jos vertaus ei ole sama.

Tästä tuli mieleen edellisissä ghidra tehtävissä olevan if -lausekkeen kääntäminen ja lähdin tätä kautta kyselemään tekoälyltä, onko mahdollista muokata koodia GNU:n sisällä

Sain ohjeeksi, että voin vaihtaa suoraan tuossa koodin tarkistelussa komennot. Vaihdoin komennon jne -> je, jotta koodi ei hyppäisi if lauseen yli.

Käytetty komento: `set {unsigned char}0x555555555107 = 0x74`: 0x74 vastaa komentoa je

<img width="1004" height="571" alt="kuva" src="https://github.com/user-attachments/assets/480e1cad-9647-406f-9a63-951ed2753ed5" />

Kuva 6 Muokattu koodi jne -> je kohdassa <main+135>

Lähdin liikkumaan nexti komennolla eteenpäin ja pääsin if lauseen sisälle, josta sain lipun tulostettua. 

Kokeilin tämän jälkeen strings työkalua josta osui silmään kohta `anLTj4u8`, jota luulin tehtävän salasanaksi. Kokeiltuani kyseistä salasanaa en tosin saanut lippua.

Uskon tuon olevan tehtävän salasana, jolle pitää vain tehdä jonkinlainen merkkienmuunnos, jota en saanut selville tehtävän tekemisen aikana.

Jatkan vielä mahdollisesti, jos saisin itse salasanan selville käyttäen GNU debuggeria tai ghidraa.

## lähteet

ChatGPT

Cplusplus. s.a. Operators. Luettavissa: https://cplusplus.com/doc/tutorial/operators/. Luettu: 9.2.2026.

Haaga Helia ammattikorkeakoulu. s.a. GNU Debugger (GDB). Sovellusten hakkerointi ja haavoittuvuudet -opintojakson apumateriaalit Moodlessa. Haaga-Helia ammattikorkeakoulu. Luettu: 9.2.2026

Low Level. 17.4.2021. GDB is REALLY easy! Find Bugs in Your Code with Only A Few Commands. Video. Katsottavissa: https://youtu.be/Dq8l1_-QgAc. Katsottu: 9.2.2026

Path Cybersec. 29.3.2021. GDB Tutorial for Reverse Engineers: Breakpoints, Modifying Memory and Printing its Contents. Video. Katsottavissa: https://youtu.be/nLp3hr6Jf2M. Katsottu: 9.2.2026.


