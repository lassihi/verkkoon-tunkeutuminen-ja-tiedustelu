# Harjoitus 1: Sniff
Kurssi: https://terokarvinen.com/verkkoon-tunkeutuminen-ja-tiedustelu/ \
Tehtävänanto: https://terokarvinen.com/verkkoon-tunkeutuminen-ja-tiedustelu/#h1-sniff

## x) Lue ja tiivistä
Karvinen 2025: [Wireshark - Getting Started](https://terokarvinen.com/wireshark-getting-started/)
* WireShark on johtava verkkoanalysaattori ja -snifferi.
* Asennus `sudo apt-get install wireshark`, käyttäjän lisäys wireshark ryhmään `sudo adduser tero wireshark`.
* Valitse verkkoliitäntä jonka liikennettä haluat seurat. Tallenna vain se liikenne jota haluat tarkastella, jonka jälkeen lopeta tallenna. Jälkikäteen on helpompi tarkestalla teidostoa, jossa on pienempi määrä liikennettä.
* Hakufilttereitä:
  * dns
  * tls
  * http
  * tcp.port == 443
  * ip.addr == 192.168.122.7

Karvinen 2025: [Network Interface Names on Linux](https://terokarvinen.com/network-interface-linux/)
* Uudet Linux verkkoliitäntänimet ovat peräisin systemd nimistä.
* Etuliite (wl, en, lo) kertoo liitännän tyypin.
* Etuliitteen jälkeiset numerot kertovat verkkokortin sijainnin (p=position, s=slot) tai mac-osoitteen

## a) Linux. Asenna Debian tai Kali Linux virtuaalikoneeseen.
Asensin Kalin virtualboxilla, sillä useampi Debian virtuaalikone löytyy jo. Kalin esiasennetut työkalut myös vähentävät niiden asentelujen vaivan esim. Debianissa.

## b) Ei voi kalastaa. Osoita, että pystyt katkaisemaan ja palauttamaan virtuaalikoneen Internet-yhteyden.
Internet-yhteyden katkaisemiseksi klikkasin oikeasta ylälaidasta RJ45-portin kuvaketta ja "Disconnect".

![image](https://github.com/user-attachments/assets/77d9ee2b-939c-462f-8246-5b6d0d53cb66)

Testasin voinko pingata Googlen DNS-palvelinta 8.8.8.8 ja omaa palvelintani. 

![image](https://github.com/user-attachments/assets/49868ea3-b9b1-4d66-888c-ebeab5990fee)

Sain kummastakin ilmoitukset virheestä, joten voidaan olettaa, että yhteys on kytketty pois päältä.

Kytkin samasta valikosta yhteyden takaisin päälle ja tein saman testin.

![image](https://github.com/user-attachments/assets/3640d293-9836-48f8-a956-cdcbbc29f73d)

Tällä kertaa ping onnistui, joten virtuaalikone on yhteydessä internettiin.

## c) Wireshark. Asenna Wireshark. Sieppaa liikennettä Wiresharkilla. (Vain omaa liikennettäsi. Voit käyttää tähän esimerkiksi virtuaalikonetta).
Kalissa Wireshark tuli esiasennettuna, joten käynnistin sen komennolla `wireshark`.

![image](https://github.com/user-attachments/assets/75f82ed3-8e24-4df6-97e3-14913380628f)

Valitsin verkkoliitännäksi eth0. Avasin selaimen ja Googlen etusivun, jonka jälkeen lopetin sieppauksen Wireshark ikkunan vasemman yläreunan punaisesta neliöstä. Kuvakaappaus sieppauksen loppupäästä:

![image](https://github.com/user-attachments/assets/2f399c5a-76bf-4574-93c5-3b1fcf738423)

## d) Oikeesti TCP/IP. Osoita TCP/IP-mallin neljä kerrosta yhdestä siepatusta paketista. Voit selityksen tueksi laatikoida ne ruutukaappauksesta.
Yllä olevan kuvan paketti on huono esimerkki, sillä se on TCP ACK vastaus yhteyden päätökseen, joka ei sisällä sovelluskerroksen dataa. Valitsin siis ensimmäisen tätä edeltävän paketin, josta ilmenee jokainen TCP/IP kerros.

![image](https://github.com/user-attachments/assets/939ff795-a587-40a1-b48e-e6db22f45d6a)

Wireshark-ikkunan alareunassa on valitun paketin sisältö mukavasti jaettuna TCP/IP kerroksiin. 

![image](https://github.com/user-attachments/assets/6c11b5b7-cffb-4ee8-8bf2-5a38fc3866c4)

Link-kerros on kuvattu punaisella ääriviivalla, verkkokerros vihreällä, siirtokerros sinisellä ja sovelluskerros keltaisella. Kunkin kerroksen voi tunnistaa esimerkiksi käytetyllä protokollalla, joka on ensimmäisenä rivillä.

## e) Mitäs tuli surffattua? Avaa surfing-secure.pcap. Tutustu siihen pintapuolisesti ja kuvaile, millainen kaappaus on kyseessä....
Tiedossa on yhteensä 283 pakettia, jotka ovat pääasiassa ovat TCP, TLS, DNS paketteja. Myös muutama QUIC ja ARP paketti mäyttäisi olevan. Yläreunasta "Statistics" -> "Endpoints" näyttää, että koneita on seitsemän.

![image](https://github.com/user-attachments/assets/eb67f62d-1234-4487-8e8f-9b722e6efddf)

"Statistics" -> "Capture File Properties" näyttää, että sieppaus kesti 7,536 sekuntia ja paketteja on lähetettiin 122445 tavun verran.

![image](https://github.com/user-attachments/assets/ec8e082f-99f4-4af0-830b-583b5ad38fc5)

## f) Vapaaehtoinen, vaikea: Mitä selainta käyttäjä käyttää?...
Kaikki DNS-kyselyt näyttäisivät tulevan osoitteesta 192.168.122.7, joten tämä on todennäköisesti käyttäjä jonka selain halutaan tunnistaa. Suodatin wiresharkin näyttämään vain tämän lähettämiä paketteja haulla ip.src == 192.168.122.7. Tieto selaimesta lienee ainakin sovelluskerroksen datassa, mutta tämä on TLS-salattua, joka tekee sen selvittämisesta paljon haastavampaa. Tutkin hieman eri paketteja ja TLS-handshake Client Hello viesteissä näytti olevan eniten hyödyllistä dataa. Päädyin tutkimaan josko cipher suiten tai hash algorytmien avulla voisi tunnistaa selaimen, mutta huonolla menestyksellä.

![image](https://github.com/user-attachments/assets/f470d561-3a9c-49f2-8507-9000728b49fe)

Tämän tutkimiseen oli hetki ehtinyt kulua, joten päätin katsoa kuinka Giang sai selaimen selville omassa raportissaan https://github.com/gianglex/Courses/blob/main/Verkkoon-Tunkeutuminen-ja-Tiedustelu/h1-sniff.md. Selaimen pystyi tunnistamaan samassa Client Hello viestissä olevan JA3 hashin avulla. 

![image](https://github.com/user-attachments/assets/86a67e6c-fc17-464c-8c8e-0f6357f41093)

Kun hashin arvon haki JA3 tietokannasta, niin selain osoittautui Firefoxiksi.

## g) Minkä merkkinen verkkokortti käyttäjällä on?
Osa laitevalmistajista on rekisteröinyt omille verkkokorteilleen tietyn MAC-osoitteen alkupään, jolloin merkki on mahdollista tunnistaa MAC-osoitteesta. Kopioin osoitteessa 192.168.122.7 sijaitsevan laitteen MAC-osoitteen.

![image](https://github.com/user-attachments/assets/445bdb88-3923-48b6-9943-43aa00b755b6)

Syötin osoitteen sivustolle https://macaddress.io ja sain seuraavan tuloksen.

![image](https://github.com/user-attachments/assets/5642d1f2-66bd-4d6a-914d-ebcedf419956)

Tulosten perusteella osoite ei kuulu millekään valmistajalle. Sivuston mukaan wireshark kuitenkin tunnistaa valmistajan olevan todennäköisesti Realtek.

## h) Millä weppipalvelimella käyttäjä on surffaillut?
Sieppauksen DNS-kyselyiden ja IP-osoitteiden perusteella:
* www.google.com (A 216.58.210.164)
* terokarvinen.com (A 139.162.131.217)
  * commentero.terokarvinen.com (CNAME terokarvinen.com, A 139.162.131.217)
* gc.zgo.at (CNAME goatcounter.netlify.com, A 3.75.10.80)
* terokarvinen.goatcounter.com (A 135.181.139.209)

## i) Analyysi. Sieppaa pieni määrä omaa liikennettäsi. Analysoi se, eli selitä mahdollisimman perusteellisesti, mitä tapahtuu...
Ajoin komennon `sudo apt-get update` ja sieppasin samalla liikenteen. Alla komennosta seurannut ensimmäinen TCP-yhteys, jota analysoidaan.

![image](https://github.com/user-attachments/assets/e56c682d-7686-4e89-a6b1-4265e291b89c)

Yhteys oman virtuaalikoneen (10.0.2.15:50330) ja http.kali.org palvelimen (18.211.24.19:80) välillä. Yhteyden kesto noin 0,5 s. Siirtokerroksen protokollana TCP ja sovelluskerroksen HTTP.
1. paketti (No. 14): Virtuaalikone aloittaa TCP-kättelyn palvelimen kanssa. Määrittää samalla esim. ikkunan koon (kuinka paljon dataa lähetetään ennen vahvistusta sen vastaanotosta) ja segmentin koon (kuinka paljon dataa maksimissaan yhdessä paketissa).
2. paketti: Palvelin vastaa kättelyyn ja vahvistaa saaneensa koneen lähettämän paketin.
3. paketti: Kone vahvistaa saaneensa palvelimen paketin. TCP-yhteys virallisesti alkaa.
4. paketti: Kone pyytää palvelimelta resurssit osoittesta /kali/dists/kali-rolling/InRelease käyttäen HTTP GET metodia.
5. paketti: Palvelin vahvistaa saaneensa koneen pyynnön.
6. paketti: Palvelin vastaa koneen pyyntöön [302 Found](https://developer.mozilla.org/en-US/docs/Web/HTTP/Reference/Status/302) status koodilla (eli resurssit ovat siirretty uuteen osoitteeseen hetkellisesti). Resurssien uusi osoite löytyy paketin "Location:" kentästä.
   ![image](https://github.com/user-attachments/assets/cb3f0d38-3222-40c7-b9fb-c641a4f763ac)
7. paketti: Kone vahvistaa saaneensa palvelimen vastauksen.

## Lähteet
Iso-Anttila, Karvinen 2025: Verkkoon tunkeutuminen ja tiedustelu: https://terokarvinen.com/verkkoon-tunkeutuminen-ja-tiedustelu/

Karvinen 2025: Wireshark - Getting Started: https://terokarvinen.com/wireshark-getting-started/

Karvinen 2025: Network Interface Names on Linux: https://terokarvinen.com/network-interface-linux/

Giang 2025: h1-sniff: https://github.com/gianglex/Courses/blob/main/Verkkoon-Tunkeutuminen-ja-Tiedustelu/h1-sniff.md

Mozilla Corporation 2025: HTTP response status codes: https://developer.mozilla.org/en-US/docs/Web/HTTP/Reference/Status/302
