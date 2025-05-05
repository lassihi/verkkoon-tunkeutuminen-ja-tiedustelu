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
2. Evilginx hakee kohdesivuston ja se esittää sen käyttäjälle reaaliajassa, jolloin käyttäjä luulee olevansa suoraan yhteydessä kohdesivustoon.
3. Käyttäjä kirjautuu sisään kohdesivustolle. Liikenne kulkee Evilginxin kautta, joka kerää kirjautumistiedot ja istunnon evästeet, ja esittää ne hyökkääjälle.

### Asensitko työkalun, jos asensit niin kirjoita miten sen teit.

Aloitin lukemalla työkalun dokumentaation ja seurasin sen asennusohjeita (https://help.evilginx.com/community)

Latasin työkalun githubista.

    git clone https://github.com/kgretzky/evilginx2.git

Siirryin evilginx2 hakemistoon.

    cd evilginx2

Loin binäärin go:n avulla (go oli entuudestaan ladattuna koneella).

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

Määritin seuraavaksi evilginxille domainin ja IP-osoitteen.

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
Tätä ei tarvitse tehdä jos ajaa evilginxiä julkisessa IP osoitteessa, sillä se osaa hakea automaattisesti Let's Encryptin varmenteen.

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

Oikean julkisen huijaussivuston luominen olisi vielä omaa esimerkkiäni yksinkertaisempi, sillä voisi skipata paikallisten domainien määrittämisen ja varmenteenmyöntäjän lisäämisen selaimelle.

## b) Sinulla on käytössäsi mininet ympäristö. Luo ympäristö, jossa voit tehdä TCP SYN-Flood hyökkäyksen. Kirjoita miten loit mininet ympäristön ja miten toteutit hyökkäyksen.
