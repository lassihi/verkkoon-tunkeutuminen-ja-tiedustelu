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


