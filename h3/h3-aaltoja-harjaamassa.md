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
Avasin Converted_433.92M_2000k.cs8 tiedoston rtl_433:lla. Alla analyysi kokonaisuudessaan.

![image](https://github.com/user-attachments/assets/9e66a5cf-f39e-4061-bea6-71625ecd7c5d)

Analyysistä huomataan, että tiedosto sisältää kolmen laitemallin (model) signaaleja, KlikAanKlikUit-Switch, Proove-Security ja Nexa-Security. Näiden signaalit on vastaanotettu aina samaan aikaan. Signaalit toistuvat aikaleiman perusteella aina noin 0,08 sekuntin välein. Lisäksi kaikilla Malleilla on sama id/House Code -tunniste "8785315". 

Koska en aiheesta juurikaan tiedä, niin lähdin etsimään lisää tietoa. Kohdan x) artikkelista Decode 433.92 MHz weather station data, löytyy linkki rtl_433 decoder tietokantaan (https://triq.org/explorer/). Tänne syötin hakusanaksi "Klik" ja löysin kaksi decoderia.

![image](https://github.com/user-attachments/assets/4eff2d16-6045-4fc6-bad0-80fd41361dad)

Näiden lähdekoodia ja rtl_433-tulostetta vertailemalla päättelin käytetyt decoderit:
* KlikAanKlikUit-Switch: https://github.com/merbanan/rtl_433/blob/master/src/devices/newkaku.c#L87 eli "newkaku"
* Proove-Security ja Nexa-Security: https://github.com/merbanan/rtl_433/blob/master/src/devices/newkaku.c#L87 eli "proove"

Löytämäni perusteella näyttäisi olevan kyse siitä, että KlikAanKlikUit, Nexa ja Proove -merkin laitteet käyttävät kaikki samanlaista signaalia, joten niiden tunnistaminen toisistaan ei juurikaan ole mahdollista. Analysoimassani sieppauksessa on siis todennäköisesti alun perin käytetty vain yhden edellä mainitun merkin laitetta, mutta koska rtl_433 ei pysty erottamaan niitä, niin se näyttää signaalin purettuna jokaisen merkin mukaan, joka selittäisi myös identtiset aikaleimat.

## d) Too compex 16? Olet nauhoittanut näytteen 'urh' -ohjelmalla .complex16s-muodossa. Muunna näyte rtl_433-yhteensopivaan muotoon ja analysoi se. Näyte Recorded-HackRF-20250411_183354-433_92MHz-2MSps-2MHz.complex16s
Latasin tiedoston ja kotitehtävien vinkkien avulla vaihdoin nimen rtl_433-yhteensopivaksi.

    mv Recorded-HackRF-20250411_183354-433_92MHz-2MSps-2MHz.complex16s Recorded-HackRF-433.92M_1000k.cs8

Yritin analysoida tiedoston, mutta mitään ei tapahtununt.

![image](https://github.com/user-attachments/assets/8b6776b3-f70f-4d7c-901f-c33f2c035ec7)

Tajusin tässä kohtaa alkuperäisessä teidostossa olevan "2MSps" kuvaavan näytteenottotaajuutta, joten vaihdoin nimeä.

    mv Recorded-HackRF-433.92M_1000k.cs8 Recorded-HackRF-433.92M_2000k.cs8

Analysoin tiedoston.

![image](https://github.com/user-attachments/assets/6d0b71f1-8f2e-499e-8bc2-82cfc9691f79)

Analyysin perusteella näyte on sama kuin tehtävässä c)

## e) Ultimate. Asenna URH, the Ultimate Radio Hacker.

Tehvävän vinkkien mukaan asensin python sovellusten latausta varten pipx:n, `sudo apt-get install pipx`

Yritin asentaa urh:n.

![image](https://github.com/user-attachments/assets/594659e8-d5a4-4b12-95f1-2e71ead6fde9)

Sain virheen ja kävin tarkastamassa tarkemman virheilmoituksen.

![image](https://github.com/user-attachments/assets/ba299d57-5914-4d8c-aadd-05ce9366bead)

Seurasin tämän ohjeita Cythonin asennukseen.

![image](https://github.com/user-attachments/assets/13d6a92c-83cf-4d42-82fb-c3c3bdc0a1f4)

Seurasin taas ohjeita.

![image](https://github.com/user-attachments/assets/38f6cb77-5999-46e9-ad33-cf1989e55ed2)

Yritin urh:n asennusta uudelleen.

![image](https://github.com/user-attachments/assets/13d63bf2-1336-4c04-b42d-ef983cffd303)

Sain saman virheen, vaikka olin juuri asentanut Cythonin.

Sama ongelma oli muilla tullut vastaan liian uuden Python version vuoksi (https://github.com/jopohl/urh/issues/1064). Päätin asentaa Urh:n suoraan GitHubin koodista.

Sain sa
## Lähteet
https://www.youtube.com/watch?v=sbqMqb6FVMY&t=199s

https://www.onetransistor.eu/2022/01/decode-433mhz-ask-signal.html

http://websdr3.sdrutah.org:8903/index1a.html

https://shortwaveschedule.com/index.php?freq=15575

https://fi.wikipedia.org/wiki/Korean_Broadcasting_System

https://github.com/merbanan/rtl_433/blob/master/src/devices/newkaku.c#L87
