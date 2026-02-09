# h5 It's Alive!

Tämä tehtävä raportti on luotu Iso-Anttila Lari & Karvinen Tero "Sovellusten Hakkerointi ja Haavoittuvuudet" -Kurssille.

## Main.cpp

Aloitin ensin kääntämällä lähdekoodin ajettavaksi binääriksi komennolla: `$ g++ main.cpp -o main` ja ajamalla sen `$ ./main`.

Ohjelma kysyi numeroa ja kaikki laitetut numerot palautti arvon 0.

Lähdin tutkimaan binääriä GNU debuggerin avulla, komennolla: `$ gdb ./main`

En saanut listattua lähdekoodia, enkä ymmärtänyt oikein mistään mitään joten loin uuden debugatun version komennolla: `$ g++ main.cpp -g -Wall -Werror -o main-dbg`.

Nyt sain listattua koodin ja pääsin ymmärtämään sitä hieman paremmin.

!KUVA1!

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

!KUVA 2!
  
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

!KUVA3!

## lab1

## lab2

## lähteet

https://cplusplus.com/doc/tutorial/operators/
