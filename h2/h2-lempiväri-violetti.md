# Harjoitus 2: Lempiväri: Violetti
Kurssi: https://terokarvinen.com/verkkoon-tunkeutuminen-ja-tiedustelu/ \
Tehtävänanto: https://terokarvinen.com/verkkoon-tunkeutuminen-ja-tiedustelu/#h2-lempivari-violetti

## x) Lue ja vastaa lyhyesti kysymyksiin.
Selitä tuskan pyramidin idea 1-2 virkeellä. Bianco 2013: [Pyramid of Pain](https://detect-respond.blogspot.com/2013/03/the-pyramid-of-pain.html).
* Tuskan pyramidin idea on kuvata kuinka paljon haittaa hyökkääjälle aiheutuu, kun puolustaja havaitsee tai estää jonkin tähän liittyvän tunnisteen.
* Mitä ylemmäs pyramidissa mennään, niin sitä haitallisempi tunnisteen estäminen hyökkääjälle.

Selitä timattimallin (Diamond Model) idea 1-2 virkkeellä. Caltagirone et al 2013: [Diamond Model](https://www.threatintel.academy/wp-content/uploads/2020/07/diamond-model.pdf).
* Timanttimalli on tunkeutumisten analysoinnissa käytetty tapa jakaa tunkeutuminen neljään ominaisuuteen, hyökkääjään, kyvykkyyteen, infrastruktuuriin ja kohteeseen.
* Nimi timanttimalli tulee siitä, että ominaisuudet ovat sijoitettu timantin kulmiin ja yhdistetty toisiinsa kuvaten niiden välisiä suhteita.

## a) Apache log. Asenna Apache-weppipalvelin paikalliselle virtuaalikoneellesi. Surffaa palvelimellesi salaamattomalla HTTP-yhteydellä, http://localhost . Etsi omaa sivulataustasi vastaava lokirivi. Analysoi yksi tällainen lokirivi, eli selitä sen kaikki kohdat.
Päivitin paketit ja asensin apachen.

![image](https://github.com/user-attachments/assets/a605972b-80dc-469a-95f4-bc796b90a490)

Apache olikin jo asennettuna etukäteen, joten käynnistin sen.

![image](https://github.com/user-attachments/assets/cbdd9977-b2fc-47a1-a945-e440eadd7039)

Tarkastin, että Apache oli toiminnassa selaamalla osoitteeseen http://localhost

![image](https://github.com/user-attachments/assets/da946b7f-5c07-467f-a123-5e278ace9f9d)

Näkyi Apachen oletussivu, joten se oli käynnissä. Seuraavaksi siirryin katsomaan sivun latauksesta syntyineitä lokeja, jotka lötyvät sijainnista `/var/log/apache2/access.log`.

![image](https://github.com/user-attachments/assets/d80001a6-3215-491c-9847-5f0932dbdc31)

Otin tiedostosta analysoitavaksi ensimmäisen sivun latauksesta syntyneen lokirivin.

![image](https://github.com/user-attachments/assets/a0ff2510-13dc-4a8d-aa6c-5d2fda227745)

Olin selittänyt Apachen lokit Linux palvelimet tehtävässä h3 (https://github.com/lassihi/linux-course/blob/main/h3/h3-hello-web-server.md).

"Vasemmalta oikealle lokitietoja tarkastelemalla ilmoitetaan ensiksi IP-osoite, josta pyyntö tulee. Seuraavat kaksi viivaa liittyvät käyttäjän tunnistamiseen. Pyydetyt tiedot olivat julkisia, joten tunnistusta ei tehty. Seuraava tieto lokissa kertoo pyynnön ajankohdan, jonka jälkeen ilmoitetaan itse pyyntö. Pyynnöstä selviää pyynnön tyyppi, esim. "GET", pyydetyt resurssit ja käytetty protokolla. Tämän jälkeen ilmoitetaan statuskoodi, jonka palvelin lähettää käyttäjälle, jota seuraa vastauksen pituus tavuina. Seuraavaksi ilmoitetaan osoite, josta pyyntö ohjattiin luomaan. Viimeisenä ilmoitetaan tiedot pyynnön luoneesta selaimesta."

Tämän perusteella lokirivin analyysi:
* `127.0.0.1`: Pyyntö tullut loopback-osoitteesta. 
* `- -`: Ei autentikointia.
* `[07/Apr/2025:19:36:51 +0300]`: Tarkka aika jolloin pyyntö saapunut, sekä aikavyöhyke. 
* `GET / HTTP/1.1`: Pyynnön tyyppi "GET", pyydetyt resurssit "/" ja protokolla "HTTP/1.1". 
* `200`: Palvelin vastannut pyyntöön HTTP status koodilla "200 OK". 
* `3383`: Lähetetyn vastauksen pituus 3383 tavua.
* `-`: Pyyntöä ei ohjattu toiselta sivulta.
* `Mozilla/5.0 (X11; Linuz x86_64; rv:128.0) Gecko/20100101 Firefox/128.0`: Selaimen ilmoittama selainagentti.

## b) Nmapped. Porttiskannaa oma weppipalvelimesi käyttäen localhost-osoitetta ja 'nmap -A' päällä. Selitä tulokset.

Skannasin localhost ositteen, `nmap -A locahost`.

![image](https://github.com/user-attachments/assets/ffa0f154-6d9b-4c9d-b872-1ff66cfbc50b)

Nmap manuaalisivujen mukaan optio `-A` ottaa käytöön OS-skannauksen, versiontunnistuksen, script-skannauksen ja tracerouten.

Tulosten perusteella nmap havaitsi avonaisen portin 80/tcp. Yhteensä portteja skannattiin 1000. Nmap tunnisti portissa olevaksi palveluksi "Apache httpd 2.4.63 ((Debian))" ja samalla haki palvelimen ilmoittaman "server" headerin ja sivun title elementin tiedot. 

Laitteen tyypiksi nmap ilmoitti "general purpose", jonka alle kuuluu esim. yleisimmät Windows ja Linux -käyttöjärjestelmät (lähde: nmap.org). Skannatun laitteen käyttöjärjestelmäksi nmap tunnisti "Linux 2.6.32, Linux 5.0 - 6.2", eli Linux ytimen longterm versio 2.6.32 ja stable 5.0-6.2. Linux ytimen kategoriosita lisää: https://www.kernel.org/category/releases.html.

## c) Skriptit. Mitkä skriptit olivat automaattisesti päällä, kun käytit "-A" parametria?

Skripteinä olivat käytössä "http-server-header" ja "http-title". Näiden ansionsta tulosteesta ilmeni palvelimen "server" header ja html title-elementin sisältö.

## d) Jäljet lokissa. Etsi weppipalvelimen lokeista jäljet porttiskannauksesta (NSE eli Nmap Scripting Engine -skripteistä skannauksessa). Löydätkö sanan "nmap" isolla tai pienellä? Selitä osumat. Millaisilla hauilla tai säännöillä voisit tunnistaa porttiskannauksen jostain muusta lokista, jos se on niin laaja, että et pysty lukemaan itse kaikkia rivejä?

Kävin tarkastamassa apachen `access.log` tiedoston skannauksen jälkeen ja alla sen jättämät jäljet.

![image](https://github.com/user-attachments/assets/4bd780d2-5070-43eb-9fc6-01c00c9b974a)

Sana Nmap löytyy suurimmasta osasta pyynnöistä, user-agent -kohdassa. Linkki joka user user-agent -kohdassa ilmoitetaan (https://nmap.org/book/nse.html) ohjaa sivulle, jossa kerrotaan lisätietoja Nmap Scripting Enginestä. Voidaan olettaa, että käytetyt nmap skriptit tuottavat nämä lokirivit. Lokien perusteella skriptit kokeilevat useiden eri resurssien hakemista eri metodeilla.

Tämän lisäksi sana nmap löytyy pyynnöstä "GET /nmaplowercheck..... HTTP/1.1", josta lisää kohdassa i).

Helpoin tapa tunnistaa porttiskannaus lokeista on putkittaa ne grepille ja käyttää optiota `-i` yhdessä hakusanan kanssa, jolloin näytetään kaikki hakua vastaavat tulokset riippumatta hakusanan kirjoitusasusta. Esimerkiksi `cat /var/log/apache2/access.log | grep -i "nmap"` apachen lokien tarkastamiseen. Tämä ei kuitenkaan toimi, jos nmap on muutettu olemaan käyttämättä kyseistä merkkijonoa.

## e) Wire sharking. Sieppaa verkkoliikenne porttiskannatessa Wiresharkilla.

Kännistin sieppauksen Wiresharkilla käyttäen lo-verkkoliitäntää. Tämän jälkeen ajoin uudestaan komennon `nmap -A localhost`. Skannauksen jälkeen pysäytin sieppauksen. 

Nopeasti katsottuna sieppaus sisältää 2345 pakettia, joista ensimmäiset 2000 ovat porttiskannauksesta johtuvia. Loput paketit ovat porttiin 80 kohdistuneesta skannauksesta johtuvia.

Tutkittuani sovellukesssa filttereitä sain luotua haun "any http contains 'nmap'". Haun luominen onnistui kohdasta "Analyze" -> "Display Filter Expressions...", valitsemalla oikeat tiedot ja klikkaamalla "OK".

![image](https://github.com/user-attachments/assets/efce760e-d8d6-4b2b-a668-b3550c11c50f)

Haun kriteerit täsmäsivät 25 pakettiin.

![image](https://github.com/user-attachments/assets/bab62622-23ef-4c60-b32c-5d9bb34be086)

Paketit ovat numeroiden perusteella skannauksen loppupäästä. Osoitteista on mahdotonta päätellä ovatko paketit nmapin vai apachen lähettämiä, mutta jokaisen paketin info osiosta huomataan, että ne kaikki sisältävät jonkinlaisen HTTP-metodin, eli ovat nmapin lähettämiä. Huomasin pakettien myös vastaavan suhteellisen tarkasti kohdassa d) tutkimiani Apachen lokeja.

Huomasin jälkikäteen, että vinkeissä mainittiin filter "frame contains 'nmap'". Kokeilin tätä ja se suodatti samat tulokset kuin omani.

## f) Net grep. Sieppaa verkkoliikenne 'ngrep' komennolla ja näytä kohdat, joissa on sana "nmap".
Ngrep ei ole itselleni entuudestaan tuttu, joten jouduin hieman tutkimaan sen man-sivuja. Sain tehtyä seuraavan komennon `sudo ngrep -iC -d lo "nmap"`.

Komennon kohta:
* `sudo ngrep`: ajaa ngrepin pääkäyttäjänä
* `-iC`: ei välitä isoista tai pienistä kirjaimista, värittää sanan tulosteessa
* `-d lo`: käyttää loopback-adapteria
* `"nmap"`: hakee sanalla nmap

Ajoin komennon ja avasin toisen välilehden, jolla suoritin porttiskannauksen `nmap -A localhost`. Ngrep tulokset näkyvät alla.

![image](https://github.com/user-attachments/assets/1a7e1441-f9cc-4f7d-8400-7c03be255605)

## g) Agentti. Vaihda nmap:n user-agent niin, että se näyttää tavalliselta weppiselaimelta.
User agentin saa vaihdettua yksittäisissä skannauksissa lisäämällä komentoon option `--script-args http.useragent="Mozilla/5.0..."`

Halusin löytää mistä tämän saa vaihdettua pysyvämmin, joten siirryin hakemistoon `/usr/share/nmap/`, jossa nmapin skriptit ja niiden kirjastot sijaitsevat. Hain hakemistosta ja sen alihakemistoista nmapin user agent merkkijonon.

![image](https://github.com/user-attachments/assets/9a7084bc-02f9-487d-a70e-90833eb8b514)

Kävin tulokset läpi ja http.lua tiedoston sisältä löysin kohdan, jossa määritetään user agent.

![image](https://github.com/user-attachments/assets/e650884d-4e51-4aa6-b14a-dc8bcb1b9a9e)

Koodissa http.useragent oli kommentoitu ulos, joten riitti vain kyseiselle riville user agentin vaihtaminen. Avasin tiedoston uudelleen sudoeditillä, jotta voin tehdä siihen muutoksia ja päivitin agentin.

![image](https://github.com/user-attachments/assets/544c068d-7846-4572-9543-9f2b100228d8)

Lopuksi tallensin tiedoston.

## h) Pienemmät jäljet. Porttiskannaa weppipalvelimesi uudelleen localhost-osoitteella. Tarkastele sekä Apachen lokia että siepattua verkkoliikennettä.
Porttiskannasin localhost-osoitteen portin 80, `nmap -A -p 80 localhost`.

Avasin tämän jälkeen Apachen lokin 30 viimeistä riviä.

![image](https://github.com/user-attachments/assets/89db9a27-c3a7-4adb-afe4-ed4731427800)

Tällä kertaa user agent oli se jonka lisäsin http.lua tiedostoon. Merkkijono "nmap" kuitenkin esiintyi vielä kohdassa " GET /nmaplowercheck1744135301".

## i) Hieman vaikeampi: LoWeR ChEcK. Poista skritiskannauksesta viimeinenkin "nmap" -teksti.
Artikkelin https://infosecwriteups.com/evading-detection-while-using-nmap-69633df091f3 mukaan nmap käyttää "/nmaplowercheck..." tai muuta vastaavaa tarkastamaan, että palvelin käsittelee 404 vastaukset oiken.

Lähdin etsimään tätä hakemistosta `/usr/share/nmap`.

![image](https://github.com/user-attachments/assets/d01376b6-3ce2-4643-b693-d7e549a9d37b)

Se löytyi myös tiedostosta http.lua.

![image](https://github.com/user-attachments/assets/e69f57eb-6913-40a0-a1ba-ec5aff01c9ca)

Muutin hieman merkkijonoja niin, että ne näyttävät lokeissa vähemmän epäilyttäviltä, mutta kuitenkin säilyttävät toiminnot.

![image](https://github.com/user-attachments/assets/26e05edd-f7d5-4c03-acbd-87e8a2aa0167)

Tallensin tiedoston ja tein uuden skannauksen `nmap -A -p 80 localhost`. Sieppasin samalla paketit Wiresharkilla. Sieppauksen jälkeen hain "nmap" merkkijonon sisältävät paketit.

![image](https://github.com/user-attachments/assets/37dd0808-ac51-4d0c-82e5-c5482d8c2f74)

Tarkastin myös apachen lokit.

![image](https://github.com/user-attachments/assets/f9358ef9-49d9-46bf-9337-f679e6f30728)

Merkkijonoa "nmap" ei näkynyt kummassakaan, eli olin onnistunut tekemään nmapista hieman näkymättömämmän.

## Lähteet

Iso-Anttila, Karvinen 2025: Verkkoon tunkeutuminen ja tiedustelu: https://terokarvinen.com/verkkoon-tunkeutuminen-ja-tiedustelu/

Bianco 2013: Pyramid of Pain: https://detect-respond.blogspot.com/2013/03/the-pyramid-of-pain.html

Caltagirone et al 2013: Diamond Model: https://www.threatintel.academy/wp-content/uploads/2020/07/diamond-model.pdf

Hirvonen 2025: Harjoitus 3: Hello Web Server: https://github.com/lassihi/linux-course/blob/main/h3/h3-hello-web-server.md

Nmap man -sivut: `man nmap`

Nmap: Device types: https://nmap.org/book/osdetect-device-types.html

Ngrep man -sivut: `man ngrep`

Van Der Staak 2023: Evading Detection while using nmap: https://infosecwriteups.com/evading-detection-while-using-nmap-69633df091f3

