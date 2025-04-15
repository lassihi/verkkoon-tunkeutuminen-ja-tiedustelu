# Harjoitus 3: Aaltoja harjaamassa
Kurssi: https://terokarvinen.com/verkkoon-tunkeutuminen-ja-tiedustelu/ \
Tehtävänanto: https://terokarvinen.com/verkkoon-tunkeutuminen-ja-tiedustelu/#h3-aaltoja-harjaamassa

## x) Lue ja tiivistä
Hubacek 2019: [Universal Radio Hacker SDR Tutorial on 433 MHz radio plugs](https://www.youtube.com/watch?v=sbqMqb6FVMY&t=199s) (Video, alkaen 3:19 ja päättyen 7:40. Yhteensä noin 4 min.)
* Laitteen käyttämä taajuus voidaan tarkastaa Spectrum Analyzerin tuottamasta vesiputouksesta.
* Modulaation valinnan jälkeen Autodetect parameters -ominaisuus tunnistaa signaalin ominaisuudet, kuten bitin pituuden.
* Demoduloinnin jälkeen signaalia mahdollista muuttaa heksadesimaaliksi.

Cornelius 2022: [Decode 433.92 MHz weather station data](https://www.onetransistor.eu/2022/01/decode-433mhz-ask-signal.html)
* Käytetty rlt_433-ohjelmaa 433,92 MHz nauhoitteen automaattiseen analysointiin.
* URH:lla signaalin sieppaus ja analisointi.

Vapaaehtoinen, vaikeahko: Lohner 2019: [Decoding ASK/OOK_PPM Signals with URH and rtl_433](https://github.karllohner.com/SDR/Decoding/Example_2019-01-24/)

## a) WebSDR. Etäkäytä WebSDR-ohjelmaradiota, joka on kaukana sinusta ja kuuntele radioliikennettä. Radioliikenne tulee siepata niin, että radiovastaanotin on joko eri maassa tai vähintään 400 km paikasta, jossa teet tätä tehtävää. Käytä esimerkkinä julkista, suurelle yleisölle tarkoitettua viestiä, esimerkiksi yleisradiolähetystä. Kerro löytämäsi taajuus, aallonpituus ja modulaatio. Kuvaile askeleet ja ota ruutukaappaus.

Kuuntelin Corinnessa, Utahin osavaltiossa Yhdysvalloissa sijaitsevaa WebSDR-radiota (http://websdr3.sdrutah.org:8903/index1a.html), jonka löysin http://websdr.org sivulta. Äänen kytkin päälle keltaisesta "Chrome audio start" -napista.

![image](https://github.com/user-attachments/assets/175c1fa9-621b-4cb1-a000-8b968bfc39d8)

Taajuudella 15575 kHz kuului radiokanava, jossa keskusteltiin noin puolen tunnin sisällä pääasiassa Etelä-Korean uutisista englanniksi. Valitsin tämän taajuuden, sillä sen lähetys kuului huomattavasti selkeämmin kuin suurin osa muista taajuuksista.

![image](https://github.com/user-attachments/assets/beeb350a-e8ae-4d69-bc63-217970afca3d)

Käytin AM-modulaatiota ja noin 10 kHz kaistanleveyttä. 

![image](https://github.com/user-attachments/assets/33c10ded-190c-4c90-aa77-199fecfbe377)

Googlasin lähetyksen taajuuden ja sivusto shortwaveschedule.com antoi tulokseksi KBS Word Radio. KBS (Korean Broadcasting System) on Etelä-Korean yleisradioyhtiö (https://fi.wikipedia.org/wiki/Korean_Broadcasting_System).

![image](https://github.com/user-attachments/assets/3831ec75-80dd-4881-b774-7a68eebeae10)

## b) rtl_433. Asenna rtl_433 automaattista analyysia varten. Kokeile, että voit ajaa sitä. './rtl_433' vastaa "rtl_433 version 25.02 branch..."
Käynnistin virtuaalikoneen ja päivitin paketit, `sudo apt-get update`.

Kalille rtl_433-sovelluksen asennus onnistui helposti paketinhallinnasta, `sudo apt-get install rtl-433`.

Varmistin, että sovellus on asennettuna ajamalla sen.

![image](https://github.com/user-attachments/assets/508b7cc1-4f10-40d9-b847-2ee3eb5d4dd8)

Versionumero tulostui, joten asennus onnistui.

## c) Automaattinen analyysi. Mitä tässä näytteessä tapahtuu? Mitä tunnisteita (id yms) löydät? Converted_433.92M_2000k.cs8. Analysoi näyte 'rtl_433' ohjelmalla.

## Lähteet
https://www.youtube.com/watch?v=sbqMqb6FVMY&t=199s

https://www.onetransistor.eu/2022/01/decode-433mhz-ask-signal.html

http://websdr3.sdrutah.org:8903/index1a.html

https://shortwaveschedule.com/index.php?freq=15575

https://fi.wikipedia.org/wiki/Korean_Broadcasting_System
