# Harjoitus 5: Laboratorio ja simulaatioympäristöt hyökkäyksissä

## Tuntitehtävät

### Network-Security-Lab (mininet)

#### ICMP Spoofing attack:

Mininetissä käynnistin h1:n ja h2:n terminaalit.

    mininet> xterm h1
    mininet> xterm h2

Käynnistin h1:lla sniff_icmp.py skriptin.

    python sniff_icmp.py

Aloitin h2:lla h1:n pingaamisen.

![image](https://github.com/user-attachments/assets/63914966-e6c6-48cc-96ec-879d53508beb)

h1:lla näkyy icmp-paketit.

![image](https://github.com/user-attachments/assets/18bd0973-4345-4ccc-b26f-c184d1fecf32)

#### MITM attack using arp poisoning:

h1, h2 ja h3 terminaalit käyntiin,

    mininet> xterm h1
    mininet> xterm h2
    mininet> xterm h3

h3:lla käynnistin liikenteen sieppauksen ja arp myrkytyksen.

    tcpdump –i h3-eth0 –w arp-p.pcap & wireshark
    python3 arp_poison.py

h1 simuloimaan udp-palvelinta.

![image](https://github.com/user-attachments/assets/8da87b57-7fb7-4546-bc37-d2f908994a87)

h2 simuloimaan udp-asiakasohjelmaa.

![image](https://github.com/user-attachments/assets/ea109efa-ff94-444d-92a8-c2a7e3541a62)

Wiresharkilla nähdään mac osoitteita tutkimalla, että liikenne h1:n ja h2:n välillä on kulkenut h3:n kautta.

![image](https://github.com/user-attachments/assets/a3ddde3f-cd31-408d-a420-638b4ac184ac)

![image](https://github.com/user-attachments/assets/bb7e0346-42f7-4b6d-8c25-ec52fbe7a7fb)

### ARP Poisoning, redirect ssh connection to another host

Mininetissä pingasin kaikkia nodeja.

    mininet> pingall
    *** Ping: testing ping reachability
    h1 -> h2 h3 
    h2 -> h1 h3 
    h3 -> h1 h2 
    *** Results: 0% dropped (6/6 received)

Käynnistin h1 terminaalin.

    mininet> xterm h1

Tarkastin h1:n arp taulusta h2:n ja h3:n mac osoitteet.

![image](https://github.com/user-attachments/assets/8c692cc6-933a-4947-9aee-85739477e309)

Käynnistin h3 terminaalin.

    mininet> xterm h3

h3:lla ajoin komennot.

* `sysctl -w net.ipv4.ip_forward=1`
  * Sallii pakettien välityksen verkkoliitäntöjen välillä
* `iptables -t nat -A PREROUTING -p tcp --dport 22 -j REDIRECT --to-port 22`
  * Reitittää porttiin 22 saapuvat tcp paketit porttiin 22
* `tcpdump –i h3-eth0 –w arp-p.pcap & wireshark & sshesame -port 22 -listen_address 0.0.0.0`
  * Käynnistää h3-eth0 liitäntään saapuvan verkkoliikenteen sieppauksen
  * Käynnistää ssh honeypotin, joka kerää lokeja tapahtumista
 
Avasiin uuden h3 terminaalin.

    mininet> xterm h3

Uudessa terminaalissa käynnistin arp myrkytys scriptin.

    python3 arp_poison.py

h1:lla otin ssh yhteyden h2:een.

![image](https://github.com/user-attachments/assets/815454f9-3857-4c8a-99e4-130bbf01befb)

SSH:lla oli tiedossa h2:n avain, joten se tunnisti arp myrkytyksen.

h1:lla Poistin h2:n avaimen.

![image](https://github.com/user-attachments/assets/d81a6c85-021c-4ab3-a43a-1c89e6526804)

h1:lla Otin SSH yhteyden h2:een.

![image](https://github.com/user-attachments/assets/1d4e5a43-f077-4712-81ac-27411cc6cd51)

h1 luulee, että se on nyt yhteydessä h2:een, vaikka tosiasiassa se on yhdistetty h3:n sshesame honeypottiin.

Arp myrkytys wiresharkissa:

![image](https://github.com/user-attachments/assets/fecef80f-f942-4dd1-be06-ce0d2bc23434)

sshesamen lokit:

![image](https://github.com/user-attachments/assets/e46aec8f-074d-4b44-b0e0-27d841c21e57)
