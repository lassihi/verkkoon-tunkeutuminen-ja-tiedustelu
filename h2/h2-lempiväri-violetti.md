# Harjoitus 2: Lempiväri: Violetti
Kurssi: https://terokarvinen.com/verkkoon-tunkeutuminen-ja-tiedustelu/ \
Tehtävänanto: https://terokarvinen.com/verkkoon-tunkeutuminen-ja-tiedustelu/#h2-lempivari-violetti

## x) Lue ja vastaa lyhyesti kysymyksiin.
Selitä tuskan pyramidin idea 1-2 virkeellä. Bianco 2013: [Pyramid of Pain](https://detect-respond.blogspot.com/2013/03/the-pyramid-of-pain.html).
* Tuskan pyramidin idea on kuvata kuinka paljon haittaa hyökkääjälle aiheutuu, kun puolustaja havaitsee jonkin estää jonkin tähän liittyvän tunnisteen. Mitä ylemmäs pyramidissa mennään, niin sitä haitallisempi sen estäminen hyökkääjälle.

Selitä timattimallin (Diamond Model) idea 1-2 virkkeellä. Caltagirone et al 2013: [Diamond Model](https://www.threatintel.academy/wp-content/uploads/2020/07/diamond-model.pdf).

## a) Apache log. Asenna Apache-weppipalvelin paikalliselle virtuaalikoneellesi. Surffaa palvelimellesi salaamattomalla HTTP-yhteydellä, http://localhost . Etsi omaa sivulataustasi vastaava lokirivi. Analysoi yksi tällainen lokirivi, eli selitä sen kaikki kohdat.
Päivitin paketit ja asensin apachen.

![image](https://github.com/user-attachments/assets/a605972b-80dc-469a-95f4-bc796b90a490)

Apache olikin jo asennettuna etukäteen, joten käynnistin sen.

![image](https://github.com/user-attachments/assets/cbdd9977-b2fc-47a1-a945-e440eadd7039)

Tarkastin, että Apache oli toiminnassa selaamalla osoitteeseen http://localhost

![image](https://github.com/user-attachments/assets/da946b7f-5c07-467f-a123-5e278ace9f9d)

Näkyi Apachen oletussivu, joten se oli käynnissä. Seuraavaksi siirryin katsomaan latauksestani syntyineitä lokeja, jotka lötyvät sijainnista `/var/log/apache2/access.log`.

![image](https://github.com/user-attachments/assets/d80001a6-3215-491c-9847-5f0932dbdc31)

Otin tiedostosta analysoitavaksi ensimmäisen sivun latauksesta syntyneen lokirivin.

![image](https://github.com/user-attachments/assets/a0ff2510-13dc-4a8d-aa6c-5d2fda227745)

Olin selittänyt Apachen lokit Linux palvelimet tehtävässä (https://github.com/lassihi/linux-course/blob/main/h3/h3-hello-web-server.md), joka näkyy alla.

"Vasemmalta oikealle lokitietoja tarkastelemalla ilmoitetaan ensiksi IP-osoite, josta pyyntö tulee. Seuraavat kaksi viivaa liittyvät käyttäjän tunnistamiseen. Pyydetyt tiedot olivat julkisia, joten tunnistusta ei tehty. Seuraava tieto lokissa kertoo pyynnön ajankohdan, jonka jälkeen ilmoitetaan itse pyyntö. Pyynnöstä selviää pyynnön tyyppi, esim. "GET", pyydetyt resurssit ja käytetty protokolla. Tämän jälkeen ilmoitetaan statuskoodi, jonka palvelin lähettää käyttäjälle, jota seuraa vastauksen pituus tavuina. Seuraavaksi ilmoitetaan osoite, josta pyyntö ohjattiin luomaan. Viimeisenä ilmoitetaan tiedot pyynnön luoneesta selaimesta."

Lokirivin analyysi:
* `127.0.0.1`: Pyyntö tullut loopback-osoitteesta. 
* `- -`: Ei autentikointia.
* `[07/Apr/2025:19:36:51 +0300]`: Tarkka aika jolloin pyyntö saapunut, sekä aikavyöhyke. 
* `GET / HTTP/1.1`: Pyynnön tyyppi "GET", pyydetyt resurssit "/" ja protokolla "HTTP/1.1". 
* `200`: Palvelin vastannut pyyntöön HTTP status koodilla "200 OK". 
* `3383`: Lähetetyn vastauksen pituus 3383 tavua.
* `-`: Pyyntöä ei ohjattu toiselta sivulta.
* `Mozilla/5.0 (X11; Linuz x86_64; rv:128.0) Gecko/20100101 Firefox/128.0`: Selaimen ilmoittama selainagentti.

## b) Nmapped. Porttiskannaa oma weppipalvelimesi käyttäen localhost-osoitetta ja 'nmap -A' päällä. Selitä tulokset. (Pelkkä http-portti 80/tcp riittää)

Skannasin localhost ositteen, `nmap -A locahost`.

![image](https://github.com/user-attachments/assets/ffa0f154-6d9b-4c9d-b872-1ff66cfbc50b)

Nmap manuaalisivujen mukaan optio `-A` ottaa käytöön OS-skannauksen, versiontunnistuksen, script-skannauksen ja tracerouten.

Tulosten perusteella nmap havaitsi avonaisen portin 80/tcp. Yhteensä portteja skannattiin 1000. Nmap tunnisti portissa olevaksi palveluksi "Apache httpd 2.4.63 ((Debian))" ja samalla haki palvelimen ilmoittaman "server" headerin ja sivun title elementin tiedot. 

Laitteen tyypiksi nmap ilmoitti "general purpose", jonka alle kuuluu esim. yleisimmät Windows ja Linux -käyttöjärjestelmät (lähde: Nmap org: Device Types). Skannatun laitteen käyttöjärjestelmäksi nmap tunnisti "Linux 2.6.32, Linux 5.0 - 6.2", eli Linux ytimen longterm versio 2.6.32 ja stable 5.0-6.2 (stable). Linux ytimen kategoriosita lisää: https://www.kernel.org/category/releases.html.

## c) Skriptit. Mitkä skriptit olivat automaattisesti päällä, kun käytit "-A" parametria?

Skripteinä olivat käytössä "http-server-header" ja "http-title". Näiden ansionsta tulosteesta ilmeni palvelimen "server" header ja html title-elementin sisältö.

## d) Jäljet lokissa. Etsi weppipalvelimen lokeista jäljet porttiskannauksesta (NSE eli Nmap Scripting Engine -skripteistä skannauksessa). Löydätkö sanan "nmap" isolla tai pienellä? Selitä osumat. Millaisilla hauilla tai säännöillä voisit tunnistaa porttiskannauksen jostain muusta lokista, jos se on niin laaja, että et pysty lukemaan itse kaikkia rivejä?

Kävin tarkastamassa apachen `access.log` tiedoston skannauksen jälkeen ja alla sen jättämät jäljet.

![image](https://github.com/user-attachments/assets/4bd780d2-5070-43eb-9fc6-01c00c9b974a)

Sana Nmap löytyy lokeista suurimmasta osasta pyynnöistä, user-agent -kohdassa. Linkki, joka user user-agent -kohdassa ilmoitetaan (https://nmap.org/book/nse.html) ohjaa sivulle, jossa kerrotaani lisätietoja Nmap Scripting Enginestä. Voidaan olettaa, että käytetyt nmap skriptit tuottavat nämä lokirivit. Lokien perusteella skriptit kokeilevat useiden eri resurssien hakemista eri metodeilla.

Tämän lisäksi sana nmap löytyy pyynnöstä "GET /nmaplowercheck..... HTTP/1.1", josta lisää kohdassa i).

Helpoin tapa tunnistaa porttiskannaus lokeista on putkittaa ne grepille ja käyttää optiota `-i` yhdessä hakusanan kanssa, jolloin näytetään kaikki hakua vastaavat tulokset riippumatta hakusanan kirjoitusasusta. Esimerkiksi `cat /var/log/apache2/access.log | grep -i "nmap"`. Tämä ei kuitenkaan toimi, jos nmap on muutettu olemaan käyttämättä kyseistä merkkijonoa.

## e) Wire sharking. Sieppaa verkkoliikenne porttiskannatessa Wiresharkilla. Huomaa, että localhost käyttää "Loopback adapter" eli "lo". Tallenna pcap. Etsi kohdat, joilla on sana "nmap" ja kommentoi niitä.


## Lähteet

https://detect-respond.blogspot.com/2013/03/the-pyramid-of-pain.html

https://www.threatintel.academy/wp-content/uploads/2020/07/diamond-model.pdf

https://nmap.org/book/osdetect-device-types.html

