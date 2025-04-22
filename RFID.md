# NFC JA RFID
> Kotitehtävä:
> 1. Tarkastele käytössäsi olevia RFID tuotteita, mieti miten hyvin olet suojautunut RFID urkinnalta?
> 2. Tutustu APDU komentojen rakenteeseen (voit käyttää tekoälyä tutustumiseen)
> 3. Tutki ja kerro minkä mielenkiintoisen RFID hakkerointi uutiset löysit. (Vinkki, useimmat liittyvät henkilökortteihin)

## 1. Tarkastele käytössäsi olevia RFID tuotteita, mieti miten hyvin olet suojautunut RFID urkinnalta?
Avaimenperässäni roikkuu kolme RFID-tuetta: iLOQ-avain, kulkulätkä kuntosalille ja lätkä taloyhtiön roskiksille. Jos totta puhutaan, niin en ole ollenkaan miettynet näiden suojaamista urkinnalta, sillä luotan avaimen suojaukseen, enkä välitä tarpeeksi muista lätkistä. Lompakostani löytyy useampi RFID ominaisuuksilla varustettu kortti. Lompakko on kuitenkin mallia, joka ainakin tuoteselosteen perusteella estää henkilökorttien ja pankkikorttien lukemisen lompakon sisältä. Näiden lisäksi käytän työpaikalla RFID kulkukorttia liikkumiseen ja kaupoissa puhelinta lähimaksuihin. Käytän todennäköisesti vielä muitakin RFID tuotteita aktiivisesti, mutta ne eivät ole aivan yhtä ilmeisiä. Näiden suojaamista en ole kummemmin ajatellut, mutta pyrin pitämään tavarat aina omassa hallussani.

## 2. Tutustu APDU komentojen rakenteeseen (voit käyttää tekoälyä tutustumiseen)
APDU komennot ovat kortinlukijoiden käyttämiä komentoja korteille. Komentojen rakenne määritellään standardissa ISO/IEC 7816-4.

APDU komento voi sisältää 0-65535 tavua dataa. Komennon ensimmäiset 4 tavua ovat aina samat:
* Tavu 1: CLA: Ilmoittaa komennon luokan, esimerkiksi toimialojen välinen tai valmistajakohtainen.
* Tavu 2: INS: Ilmoittaa komennon, esimerkiksi "valitse" tai "kirjoita data".
* Tavu 3 ja 4: P1 ja P2: Parametreja, jotka ohjeistavat komennon suorittamisessa.

Loput komennon tavuista sisältävät datan pituuden (Lc), itse datan (command data) ja odotetun vastauksen pituuden (Le). Näille kentille ei ole määritetty vakituista määrää tavuja, vaan tavujen määrä vaihtelee hieman komennosta riippuen.

Lähde: https://en.wikipedia.org/wiki/Smart_card_application_protocol_data_unit

## 3. Tutki ja kerro minkä mielenkiintoisen RFID hakkerointi uutiset löysit. (Vinkki, useimmat liittyvät henkilökortteihin)
Löysin usean tuoreen uutisen, jotka kaikki varoittavat liikkeellä olevasta huijauksesta. Huijauksessa hyökkääjä yrittää saada uhrin lataamaan Android puhelimelleen haittaohjelman ja syöttämään haittaohjelmaan pankkikorttin PIN-koodin. Tässä hyökkääjän onnistuttua, haittaohjelma pystyy lukemaan puhelinta lähellä olevan pankkikortin tiedot ja siirtämään siltä rahaa NFC:n avulla.

   https://euroweeklynews.com/2025/04/22/new-nfc-phone-scam-survival-guide-fbi-spanish-tax-office-and-bank-warnings/ \
   https://www.bankinfosecurity.com/hackers-hijack-nfc-for-instant-payment-fraud-a-28060 \
   https://www.blackenterprise.com/new-smartphone-scam-targets-bank-accounts-via-nfc/ \
   https://therecord.media/new-payment-card-scam-involves-malware-tap
