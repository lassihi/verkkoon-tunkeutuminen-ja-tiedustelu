# Harjoitus 5: Laboratorio- ja simulaatioympäristöt hyökkäyksissä
Kurssi: https://terokarvinen.com/verkkoon-tunkeutuminen-ja-tiedustelu/

## Tehtävänanto:

a) Tutustu seuraavaan työkaluun

  https://github.com/kgretzky/evilginx2
  
  Vastaa seuraaviin kysymyksiin:
  * Asensitko työkalun, jos asensit niin kirjoita miten sen teit.
  * Mitä teit työkalun kanssa?
  * Onnistuitko huijaamaan liikennettä

b) Sinulla on käytössäsi mininet ympäristö. Luo ympäristö, jossa voit tehdä TCP SYN-Flood hyökkäyksen.

  Kirjoita miten loit mininet ympäristön ja miten toteutit hyökkäyksen.

## a) Tutustu työkaluun https://github.com/kgretzky/evilginx2

Evilginx2 on MITM-työkalu kirjautumistunnusten ja evästeiden kalasteluun. Se toimii käänteisproxyna välittäen liikennettä uhrin ja kohdesivuston välillä. (https://github.com/kgretzky/evilginx2).

Siis käytännössä:
1. Uhri klikkaa kalasteluviestissä olevaa kohdesivustoa esittävää linkkiä, joka todellisuudessa ohjaa Evilginx-palvelimelle.
2. Evilginx hakee kohdesivuston ja se esittää sen uhrille reaaliajassa, jolloin uhri luulee olevansa suoraan yhteydessä kohdesivustoon.
3. Käyttäjä kirjautuu sisään kohdesivustolle. Liikenne kulkee Evilginxin kautta, joka kerää kirjautumistiedot ja istunnon evästeet, ja esittää ne hyökkääjälle.

### Asensitko työkalun, jos asensit niin kirjoita miten sen teit.

Aloitin lukemalla työkalun dokumentaation ja seurasin sen asennusohjeita (https://help.evilginx.com/community)

Latasin työkalun githubista.

    git clone https://github.com/kgretzky/evilginx2.git

Siirryin evilginx2 hakemistoon.

    cd evilginx2

Loin evilginxin go:n avulla (go oli entuudestaan ladattuna koneella).

    make

Siirryin hakemistoon `evilginx2/build` ja käynnistin evilginxin.

    cd build
    sudo ./evilginx

<img width="739" alt="image" src="https://github.com/user-attachments/assets/48aa171e-2635-4548-b174-619ae097b8ad" />

Evilginxiltä puuttui phishlets hakemiston sijainti. Tämä korjaantui, kun ajoin dokumentaation ohjeiden mukaisen komennon evilginx2 hakemistossa.

<img width="735" alt="image" src="https://github.com/user-attachments/assets/5180301b-fd7c-4ac9-b3c7-03d0a9fb6903" />

* Optio `-p` määrittää phishlets-hakemiston.
  * Phishletit ovat YAML konfiguraatiotiedostoja, joilla määritetään evilginx toimimaan kuten kohdesivusto. 
* Optio `-t` määrittää redirectors-hakemiston.
  * Redirectorit ovat kalastelulinkkien laskeutumissivuja.
  * Ei pakollinen.
 
### Mitä teit työkalun kanssa?

Tavoitteenani oli ajaa evilginxia paikallisesti, niin että se sijaitsisi localhost-osoitteesta, jolloin vahingossakaan en saisi kalastettua ketään muuta kuin itseäni.

Löysin githubista valmiiksi luotuja phishletteja suosituille verkkosivuille (https://github.com/simplerhacking/Evilginx3-Phishlets).

Lisäsin phishletit evilginx-hakemistoon.

<img width="573" alt="image" src="https://github.com/user-attachments/assets/6817a02f-486d-4632-8410-22dd5290aaa9" />

Käynnistin evilginxin käyttäen uusia phishletteja. Evilginxin ajaminen paikallisesti vaatii `--developer` option.

<img width="832" alt="image" src="https://github.com/user-attachments/assets/d8f1ded3-906c-496a-a04f-97183e8e6d63" />

Kaikki hakemiston phishletit eivät ladanneet, mutta se ei tässä testissä haittaa.

Määritin seuraavaksi evilginxille satunnaisen domainin ja paikallisen IP-osoitteen.

<img width="526" alt="image" src="https://github.com/user-attachments/assets/27312510-0ea8-46fb-bd11-eea944d4d7cf" />

Päätin käyttää phishlettia Microsoft2024 testissäni, joten määritin sille evilginxin domainin.

<img width="417" alt="image" src="https://github.com/user-attachments/assets/a754a8fd-2656-4e15-9c69-6195f44a39ff" />

Koska ajan evilginxiä localhost-osoitteessa, niin jouduin määrittämään tietokoneen asetuksista valitsemani domainin localhost-osoitteeseen. 
Evilginx osaa auttaa tässä luomalla asetustiedostoon lisättävät rivit phishletin perusteella.

<img width="273" alt="image" src="https://github.com/user-attachments/assets/d06f5eba-3054-4462-986a-208ac770ca54" />

Avasin asetusteidoston ja lisäsin evilginxin luomat rivit sinne.

    sudoedit /etc/hosts

<img width="319" alt="image" src="https://github.com/user-attachments/assets/cf9e8ad8-52fc-4a01-b4a0-f719494ea61f" />

Tallennettuani teidoston, käynnistin phishletin Microsoft2024 evilginxissä.

<img width="741" alt="image" src="https://github.com/user-attachments/assets/bcab4b5f-b685-4654-9a14-0624d2d534ac" />

Loin tämän jälkeen luren, eli uhrille lähetettävän lopullisen linkin.

<img width="261" alt="image" src="https://github.com/user-attachments/assets/7a9ba584-3f99-4343-820a-05552b198073" />

Paikallisesti evilginxin ajaminen vaatii vielä evilginxin root CA:n lisäämisen selaimelle, salatun yhteyden muodostamiseksi. 
Tätä ei tarvitse tehdä jos ajaa evilginxiä normaalisti julkisessa IP osoitteessa, sillä se osaa hakea automaattisesti sertifikaatin Let's Encryptiltä.

    sudo cp /root/.evilginx/crt/ca.crt /usr/local/share/ca-certificates/evilginx.crt
    sudo update-ca-certificates

Nyt evilginx on vihdoin valmiina, joten avasin luren url-osoitteen selaimessa. 

Avautunut sivusto:

<img width="520" alt="image" src="https://github.com/user-attachments/assets/d4c07e77-5b8f-4d04-9fc8-3fb281c0bbe3" />

Vertauksena oikea Microsoft sivusto:

<img width="534" alt="image" src="https://github.com/user-attachments/assets/c1776e6e-4157-412c-b3fb-cc348ff02b0a" />

Sivustot ovat identtisiä, joten ainoa tapa ulkoapäin tunnistaa phishing-sivusto on katsoa url-osoitetta.

Jatkoin omalla sähköpostillani, jotta pahimmassa tapauksessa vain oma tilini menee sekaisin.

<img width="519" alt="image" src="https://github.com/user-attachments/assets/b1b34b4b-5ab4-4910-a652-ffecdcf70209" />

Yritin kirjautua sisään salaisella salasanallani.

<img width="519" alt="image" src="https://github.com/user-attachments/assets/15416dcb-b0c7-48b7-b3d3-adc645aa0d4d" />

Syötettyäni salasanan, evilginxiin tulostui käyttämäni käyttäjätunnus ja salasana.

<img width="342" alt="image" src="https://github.com/user-attachments/assets/0bb40f3b-7991-49df-98d2-b7748628a771" />

Jos kirjautuminen olisi onnistunut, niin Microsoft2024 phishlet-tiedoston perusteella evilginx olisi kaapannut myös käyttäjän todennukseen käytettävän evästeen.

<img width="512" alt="image" src="https://github.com/user-attachments/assets/d2b7f9f4-5c10-4af9-aabc-23600d1fbf70" />

### Onnistuitko huijaamaan liikennettä

Onnistuin luomaan paikallisesti huijaussivuston, joka matkii Microsoftin kirjautumista ja kerää kirjautumistiedot. Käyttämilläni asetuksilla huijaussivusto toimii vain omalla koneellani.

Oikean julkisen huijaussivuston luominen olisi vielä omaa esimerkkiäni yksinkertaisempi, sillä voisi skipata paikallisten domainien määrittämisen (kuitenkin pitää domain olla osoittamassa evilgixin IP osoitteeseen) ja varmenteenmyöntäjän lisäämisen selaimelle.

## b) Sinulla on käytössäsi mininet ympäristö. Luo ympäristö, jossa voit tehdä TCP SYN-Flood hyökkäyksen. Kirjoita miten loit mininet ympäristön ja miten toteutit hyökkäyksen.

Alotin käynnistämällä mininet virtuaalikoneen. Käynnistyksen jälkeen käynnistin sen verkko-ohjaimen.

<img width="251" alt="image" src="https://github.com/user-attachments/assets/c1dba248-d480-44cb-96e0-a541ff005ba2" />

Asensin brian404:n SYN-flood skriptin GitHubista (https://github.com/brian404/SYN-flood).

<img width="442" alt="image" src="https://github.com/user-attachments/assets/d4170271-29ea-4f61-8d1f-f3b45f42a003" />

Kopioin sen mininet virtuaalikoneelle.

<img width="430" alt="image" src="https://github.com/user-attachments/assets/a683421b-89fa-4f59-9900-2a544f21ec58" />

Yhdistin mininet virtuaalikoneeseen.

<img width="727" alt="image" src="https://github.com/user-attachments/assets/bd1dd9d8-aca7-422c-9a6b-876749f43ab8" />

Käynnistin kolmen koneen mininet verkon.

<img width="545" alt="image" src="https://github.com/user-attachments/assets/a8abe8c1-56f2-4cdd-a61b-d4c2e2ad48a0" />

Avasin h1:n terminaalin uuteen ikkunaan.

<img width="120" alt="image" src="https://github.com/user-attachments/assets/576e6c0c-c644-49a3-9c9d-47dbe068befd" />

Käynnistin tuntiharjituksen 01-Network-Security-Lab mukana tulleen tcp-palvelinta mukailevan python skriptin.

<img width="239" alt="image" src="https://github.com/user-attachments/assets/d5763e82-ff2f-4564-a60b-b786037d4a92" />

Avasin lisäksi toisen h1:n terminaalin, jossa käynnistin wiresharkin sieppaamaan liikennettä liitännästä h1-eth0.

<img width="531" alt="image" src="https://github.com/user-attachments/assets/cc507222-fefe-44c6-8bd9-74a66f6a8321" />

Avasin mininetistä h2 ja h3 nodejen terminaalit.

<img width="130" alt="image" src="https://github.com/user-attachments/assets/6399c417-fcea-47b1-9799-b3cf5f677674" />

H2:n terminaalissa ajoin tcp_client.py skriptin yhteyden testaamiseksi ilman TCP SYN-hyökkäystä.

<img width="243" alt="image" src="https://github.com/user-attachments/assets/d59053a8-0851-44ce-9853-c52029ad0052" />

Ajettuani skriptin h2 sai välittömästi vastauksen h1:n palvelimelta.

Seuraavaksi käynnistin SYN flood hyökkäyksen h3:lla käyttäen lataamaani skriptiä.

    python3 syn.py --port 12345 --method socket 10.0.0.1

(kohdeportti 12345, sillä se on `tcp-server.py` ja `tcp-client.py` skriptien käyttämä portti)

<img width="220" alt="image" src="https://github.com/user-attachments/assets/9bdbced4-838d-468d-8efd-0b2fcf9bd9a4" />

H1:n wiresharkissa alkoi heti tapahtua komennon jälkeen. 

Sen mukaan h1:n (10.0.0.1) ja h3:n (10.0.0.3) välillä liikennettä kulki noin 10000 pakettia sekunnissa.

<img width="755" alt="image" src="https://github.com/user-attachments/assets/d1cfe5d3-f882-43b7-bc3e-614c416f5ec0" />

Ajoin h2:lla `tcp_client.py` skriptin, mutta se sai välittömästi yhteyden h1:n palvelimeen. 

Yritin tämän jälkeen samaa hyökkäystä uudessa ympäristössä neljällä hyökkääjällä, mutta lopputulos oli sama, tcp_client sai välittömästi yhteyden tcp_serveriin. En siis saanut aikaan h1:n palvelunestohyökkäystä, vaikka yhteensä paketteja h1-eth0 liitännässä liikkui wiresharkin mukaan noin 20000-30000 joka sekuntti. Ainoa asia mikä alkoi takkuilemaan oli mininet-virutaalikone, joten ainakin jonkinnäköisen palvelunestohyökkäyksen sain aikaan :D.

Uskon, että en saanut näkyviä vaikutuksia aikaan tcp_clientin ja tcp_serverin välillä, sillä mininet ei tarpeeksi hyvin simuloi aitoa ympäristöä, jossa jokainen laite pyörii omilla resursseillaan. Voin kuitenkin olla väärässä ja liikennettä olisi tarvittu enemmän, tai testimenetelmäni oli ylipäätään virheellinen.

## Lähteet

kgretzky 2024: Evilginx 3.0: https://github.com/kgretzky/evilginx2

Breakpoint Software Developement 2025: Introduction: https://help.evilginx.com/community

simplerhacking 2024: Updated Evilginx3 Phishlets: https://github.com/simplerhacking/Evilginx3-Phishlets

brian404 2024: SYN-flood: https://github.com/brian404/SYN-flood
