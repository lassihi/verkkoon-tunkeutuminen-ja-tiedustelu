# Harjoitus 5: Laboratorio ja simulaatioympäristöt hyökkäyksissä

## 01-Network-Security-Lab

### Task 1: ICMP Spoofing Attack

ICMP sniff:

Mininetissä käynnistin h1:n ja h2:n terminaalit.

    mininet> xterm h1
    mininet> xterm h2

Käynnistin h1:lla sniff_icmp.py skriptin.

    python sniff_icmp.py

Aloitin h2:lla h1:n pingaamisen.

![image](https://github.com/user-attachments/assets/63914966-e6c6-48cc-96ec-879d53508beb)

h1:lla näkyy icmp-paketit.

![image](https://github.com/user-attachments/assets/18bd0973-4345-4ccc-b26f-c184d1fecf32)

MITM attack using arp poisoning:

h1, h2 ja h3 terminaalit käyntiin,

    mininet> xterm h1
    mininet> xterm h2
    mininet> xterm h3

h1 simuloimaan udp-palvelinta.

![image](https://github.com/user-attachments/assets/8da87b57-7fb7-4546-bc37-d2f908994a87)

h2 simuloimaan udp-asiakasohjelmaa.

![image](https://github.com/user-attachments/assets/ea109efa-ff94-444d-92a8-c2a7e3541a62)

