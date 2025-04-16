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

## a) WebSDR. Etäkäytä WebSDR-ohjelmaradiota, joka on kaukana sinusta ja kuuntele radioliikennettä. Radioliikenne tulee siepata niin, että radiovastaanotin on joko eri maassa tai vähintään 400 km paikasta, jossa teet tätä tehtävää. Käytä esimerkkinä julkista, suurelle yleisölle tarkoitettua viestiä, esimerkiksi yleisradiolähetystä. Kerro löytämäsi taajuus, aallonpituus ja modulaatio. Kuvaile askeleet ja ota ruutukaappaus.

Kuuntelin Corinnessa, Utahin osavaltiossa Yhdysvalloissa sijaitsevaa "Northern Utah WebSDR - Server #3" WebSDR-radiota (http://websdr3.sdrutah.org:8903/index1a.html), jonka löysin http://websdr.org sivulta. Äänen kytkin päälle keltaisesta "Chrome audio start" -napista.

![image](https://github.com/user-attachments/assets/175c1fa9-621b-4cb1-a000-8b968bfc39d8)

Taajuudella 15575 kHz kuului radiokanava, jossa keskusteltiin noin puolen tunnin sisällä pääasiassa Etelä-Korean uutisista englanniksi. Valitsin tämän taajuuden, sillä sen lähetys kuului huomattavasti selkeämmin kuin suurin osa muista taajuuksista.

![image](https://github.com/user-attachments/assets/beeb350a-e8ae-4d69-bc63-217970afca3d)

Käytin amplitudi modulaatiota (AM) ja noin 10 kHz kaistanleveyttä. 

![image](https://github.com/user-attachments/assets/33c10ded-190c-4c90-aa77-199fecfbe377)

Googlasin lähetyksen taajuuden ja sivusto shortwaveschedule.com antoi tulokseksi KBS Word Radio. KBS (Korean Broadcasting System) on Etelä-Korean yleisradioyhtiö (https://fi.wikipedia.org/wiki/Korean_Broadcasting_System).

![image](https://github.com/user-attachments/assets/3831ec75-80dd-4881-b774-7a68eebeae10)

## b) rtl_433. Asenna rtl_433 automaattista analyysia varten. Kokeile, että voit ajaa sitä. './rtl_433' vastaa "rtl_433 version 25.02 branch..."
Käynnistin Kali virtuaalikoneen ja päivitin paketit, `sudo apt-get update`.

Kalille rtl_433-sovelluksen asennus onnistui helposti paketinhallinnasta, `sudo apt-get install rtl-433`.

Varmistin, että sovellus on asennettuna ajamalla sen.

![image](https://github.com/user-attachments/assets/508b7cc1-4f10-40d9-b847-2ee3eb5d4dd8)

Versionumero tulostui, joten asennus onnistui.

## c) Automaattinen analyysi. Mitä tässä näytteessä tapahtuu? Mitä tunnisteita (id yms) löydät? Converted_433.92M_2000k.cs8. Analysoi näyte 'rtl_433' ohjelmalla.
Avasin Converted_433.92M_2000k.cs8 tiedoston rtl_433:lla. Alla analyysi kokonaisuudessaan.

![image](https://github.com/user-attachments/assets/9e66a5cf-f39e-4061-bea6-71625ecd7c5d)

Analyysistä huomataan, että tiedosto sisältää kolmen laitemallin (model) signaaleja, KlikAanKlikUit-Switch, Proove-Security ja Nexa-Security. Näiden signaalit on vastaanotettu aina samaan aikaan. Signaalit toistuvat aikaleiman perusteella aina noin 0,08 sekuntin välein. Lisäksi kaikilla Malleilla on sama id/House Code -tunniste "8785315". 

Koska en aiheesta juurikaan tiedä, niin lähdin etsimään lisää tietoa. Kohdan x) artikkelista [Decode 433.92 MHz weather station data](https://www.onetransistor.eu/2022/01/decode-433mhz-ask-signal.html), löytyy linkki rtl_433 decoder tietokantaan (https://triq.org/explorer/). Tänne syötin hakusanaksi "Klik" ja löysin kaksi decoderia.

![image](https://github.com/user-attachments/assets/4eff2d16-6045-4fc6-bad0-80fd41361dad)

Näiden lähdekoodia ja rtl_433-tulostetta vertailemalla päättelin analyysissä käytetyt decoderit:
* KlikAanKlikUit-Switch: https://github.com/merbanan/rtl_433/blob/master/src/devices/newkaku.c eli "newkaku"
* Proove-Security ja Nexa-Security: https://github.com/merbanan/rtl_433/blob/master/src/devices/proove.c eli "proove"

Löytämäni perusteella näyttäisi olevan kyse siitä, että KlikAanKlikUit, Nexa ja Proove -merkin laitteet käyttävät kaikki samanlaista signaalia, joten niiden tunnistaminen toisistaan ei juurikaan ole mahdollista. Analysoimassani sieppauksessa on siis todennäköisesti alun perin käytetty vain yhden edellä mainitun merkin laitetta, mutta koska rtl_433 ei pysty erottamaan niitä, niin se näyttää signaalin purettuna jokaisen merkin mukaan, joka selittäisi myös identtiset aikaleimat.

Harjoituksen kohtien f) ja g) tehtvänannossa mainitaan käytetyn laitteen olevan Nexa merkisen pistorasian kaukosäädin, joten puretaan "Nexa-Security" mallin analyysi hieman tarkemmin. 

Nexan kyttämän decoderin "[proove](https://github.com/merbanan/rtl_433/blob/master/src/devices/proove.c)" lähdekoodin kommenttien perusteella signaali koostuu yhteensä 32-38 bitistä. Ensimmäiset 26 bittiä ovat lähettäjän yksilöllinen koodi, jonka vastaanotin oppii tunnistamaan. Seuraavat 6 bittiä määrittävät erilaisia tunnisteita (channel, state, yms.). Viimeiset 6 ovat vapaaehtoisia ja määrittävät mahdollisia himmennykseen (dim) liittyviä arvoja. 

* "time": aika sieppauksen alusta, jolloin signaali havaittu
* "model": laitteen malli, jonka rtl_433 päättelee sen signaalista
* "House Code": id (mahdollisesti bitit 0-26)
* "Channel": kanava, jolla signaali lähetetty (signaalin 29. ja 30. bitti)
* "State": tila, johon pistorasio asetetaan (signaalin 28. bitti)
* "Unit": pistorasia, jolle signaali tarkoitettu (signaalin 31. ja 32. bitti)
* "Group": 0=off, 1=on, todennäköisesti säätelee kohdistuuko signaali yhteen vai useampaan laitteeseen (signaalin 27. bitti)


## d) Too compex 16? Olet nauhoittanut näytteen 'urh' -ohjelmalla .complex16s-muodossa. Muunna näyte rtl_433-yhteensopivaan muotoon ja analysoi se. Näyte Recorded-HackRF-20250411_183354-433_92MHz-2MSps-2MHz.complex16s
Latasin tiedoston ja kotitehtävien vinkkien avulla vaihdoin nimen rtl_433-yhteensopivaksi.

    mv Recorded-HackRF-20250411_183354-433_92MHz-2MSps-2MHz.complex16s Recorded-HackRF-433.92M_1000k.cs8

Yritin analysoida tiedoston, mutta mitään ei tapahtununt.

![image](https://github.com/user-attachments/assets/8b6776b3-f70f-4d7c-901f-c33f2c035ec7)

Tajusin tässä kohtaa alkuperäisessä teidostossa olevan "2MSps" kuvaavan näytteenottotaajuutta, joten vaihdoin nimeä.

    mv Recorded-HackRF-433.92M_1000k.cs8 Recorded-HackRF-433.92M_2000k.cs8

Analysoin tiedoston.

![image](https://github.com/user-attachments/assets/6d0b71f1-8f2e-499e-8bc2-82cfc9691f79)

Analyysin perusteella näyte on sama kuin tehtävässä c).

## e) Ultimate. Asenna URH, the Ultimate Radio Hacker.

Tehvävän vinkkien mukaan asensin python sovellusten lataukseen tarkoitetun pipx:n, `sudo apt-get install pipx`

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

Sama ongelma oli muilla tullut vastaan liian uuden Python version vuoksi (https://github.com/jopohl/urh/issues/1064), joten päätin asentaa sovelluksen suoraan lähdekoodista. Ennen urh:n asennusta asensin tähän vaadittavat työkalut, `sudo apt-get install python3-setuptools`.

Asennuksen ohjeet löytyivät urh:n GitHub-ohjeista (https://github.com/jopohl/urh).

    git clone https://github.com/jopohl/urh/      #urh GitHub repon kloonaus
    cd urh                                        #urh hakemistoon siirtyminen
    sudo python setup.py install                  #urh:n asennus

Aennuksen jälkeen urh käynnistyi komennolla `urh`.

![image](https://github.com/user-attachments/assets/69c04c18-f1d5-4e78-bb14-d135d8b7695d)

## Tarkastele näytettä 1-on-on-on-HackRF-20250412_113805-433_912MHz-2MSps-2MHz.complex16s. Siinä Nexan pistorasian kaukosäätimen valon 1 ON -nappia on painettu kolmesti. Käytä Ultimate Radio Hacker 'urh' -ohjelmaa.

Asensin näytteen virtuaalikoneelle.

Avasin urh:n ja oikeasta yläkulmasta valitsin "File" -> "Open..." ja juuri lataamani näytteen.

### f) Yleiskuva. Kuvaile näytettä yleisesti: kuinka pitkä, millä taajuudella, milloin nauhoitettu? Miltä näyte silmämääräisesti näyttää?

![image](https://github.com/user-attachments/assets/ea7f834e-477d-452c-ba15-1a71fdcc05a9)

Yleiskuvan perusteella näyttää siltä, että näytteessä on lähetetty suuri määrä signaaleja kolmeen otteeseen. Tiedoston nimen perusteella signaalin taajuus olisi 433.912 MHz ja näytteenottotaajuus 2MSps. "1: Complex Signal" -tekstin vieressä oli infonappi, jota painaessa sain hieman lisätietoja.

![image](https://github.com/user-attachments/assets/2bc4e553-1bca-410e-a0e3-2413a81a1e03)

Ikkunassa näkyi tiedoston nimi, sijainti, koko, aika jolloin sen latasin, näytteiden määrä ja kesto. Oletuksena ikkunassa näytteenottotaajuus oli 1 MSps, mutta pystyin sen vaihtamaan tiedoston nimessä ilmoitettuun 2MSps.

![image](https://github.com/user-attachments/assets/9e15fdea-6d23-45fe-a648-6664474213b8)

Tämän vaihdettuani näytteen kestoksi ilmoitettiin 2.75 sekuntia.

### g) Bittistä. Demoduloi signaali niin, että saat raakabittejä. Mikä on oikea modulaatio? Miten pitkä yksi raakabitti on ajassa? Kuvaile tätä aikaa vertaamalla sitä johonkin. (Monissa singaaleissa on line encoding, eli lopullisia bittejä varten näitä "raakabittejä" on vielä käsiteltävä)

Zoomasin aivan signaalin alkuun, jotta amplitudia, taajuutta ja suuntaa olisi helppo tutkia.
![image](https://github.com/user-attachments/assets/0fed565c-2438-4d7b-9c4f-aa76f0078a96)

Huomasin, että signaalin amplitudissa on selvästi huomattavin vaihtelu ja se vastasi artikkelin [Decode 433.92 MHz weather station data](https://www.onetransistor.eu/2022/01/decode-433mhz-ask-signal.html) signaalia, jonka moduloinnissa käytettiin ASK:ta (Amplitude Shift Keying). Valitsin modulaatioksi siis ASK ja klikkasin "Autodetect parameters". Tämän jälkeen bitit ruudun alareunassa päivittyivät. Valitsin ensimmäisen bitin "1".

![image](https://github.com/user-attachments/assets/45df2eb3-ddc9-4d8f-a121-d2bf4e31e6e9)

Bittiä vastaavan signaalin kestoksi urh ilmoitti 261.00 µs (mikrosekuntia, eli sekunnin miljoonasosaa). 

Pyysin ChatGPT 4o-versiota uudessa keskustelussa vertaamaan 261 mikrosekuntia johonkin.

![image](https://github.com/user-attachments/assets/8ad45759-8e6a-427b-8e85-6c43d0ca0f64)

## Lähteet
Iso-Anttila, Karvinen 2025: Verkkoon tunkeutuminen ja tiedustelu: https://terokarvinen.com/verkkoon-tunkeutuminen-ja-tiedustelu/

Hubacek 2019: Universal Radio Hacker SDR Tutorial on 433 MHz radio plugs: https://www.youtube.com/watch?v=sbqMqb6FVMY&t=199s

Cornelius 2022: Decode 433.92 MHz weather station data: https://www.onetransistor.eu/2022/01/decode-433mhz-ask-signal.html

Northern Utah WebSDR - Server #3: http://websdr3.sdrutah.org:8903/index1a.html

ShortwaveSchedule.com 2025: frequency 15575: https://shortwaveschedule.com/index.php?freq=15575

Wikipedia 2024: Korean Broadcasting System: https://fi.wikipedia.org/wiki/Korean_Broadcasting_System

Triq.org 2024: Decoder & Sample Explorer: https://triq.org/explorer/

Merbanan: newkaku.c https://github.com/merbanan/rtl_433/blob/master/src/devices/newkaku.c

Merbanan: proove.c https://github.com/merbanan/rtl_433/blob/master/src/devices/proove.c

tomcass240 2023: You need Cython to build URH's extensions!: https://github.com/jopohl/urh/issues/1064

ChatGPT 4o: Prompt "Compare 261 microseconds to something"
