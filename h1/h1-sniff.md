# Harjoitus 1: Sniff
Kurssi: https://terokarvinen.com/verkkoon-tunkeutuminen-ja-tiedustelu/ \
Tehtävänanto: https://terokarvinen.com/verkkoon-tunkeutuminen-ja-tiedustelu/#h1-sniff

## x) Lue ja tiivistä
Karvinen 2025: [Wireshark - Getting Started](https://terokarvinen.com/wireshark-getting-started/)
* WireShark on johtava verkkoanalysaattori ja -snifferi.
* Asennus `sudo apt-get install wireshark`, käyttäjän lisäys wireshark ryhmään `sudo adduser lassi wireshark`.
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

Wireshark-ikkunan alareunassa on valitun paketin sisältö mukavasti jaettu TCP/IP kerroksiin. 

![image](https://github.com/user-attachments/assets/6c11b5b7-cffb-4ee8-8bf2-5a38fc3866c4)

Link-kerros on kuvattu punaisella ääriviivalla, verkkokerros vihreällä, siirtokerros sinisellä ja sovelluskerros keltaisella. Kukin kerros käy ilmi esimerkiksi käytetyllä protokollalla, joka on ensimmäisenä rivillä.

## e) Mitäs tuli surffattua? Avaa surfing-secure.pcap. Tutustu siihen pintapuolisesti ja kuvaile, millainen kaappaus on kyseessä....
Tiedossa on yhteensä 283 pakettia, jotka ovat pääasiassa ovat TCP, TLS, DNS paketteja. Myös muutama QUIC ja ARP paketti mäyttäisi olevan. Yläreunasta "Statistics" -> "Endpoints" näyttää, että koneita on seitsemän.

![image](https://github.com/user-attachments/assets/eb67f62d-1234-4487-8e8f-9b722e6efddf)

"Statistics" -> "Capture File Properties" näyttää, että sieppaus kesti 7,536 sekuntia ja paketteja on lähetettiin 122445 tavun verran.

![image](https://github.com/user-attachments/assets/ec8e082f-99f4-4af0-830b-583b5ad38fc5)

f) Vapaaehtoinen, vaikea: Mitä selainta käyttäjä käyttää?...
DNS-kyselyt tulivat pääasiassa osoitteesta 192.168.122.7, joten tämä on todennäköisesti käyttäjä jonka selain halutaan tunnistaa. 


