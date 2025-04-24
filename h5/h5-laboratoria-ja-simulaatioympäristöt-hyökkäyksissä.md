# Harjoitus 5: Laboratorio ja simulaatioympäristöt hyökkäyksissä

## 01-Network-Security-Lab

### Task 1: ICMP Spoofing Attack

Mininetissä käynnistin h1:n ja h2:n terminaalit.

    mininet> xterm h1
    mininet> xterm h2

Käynnistin h1:lla sniff_icmp.py skriptin.

    python sniff_icmp.py

Aloitin h2:lla h1:n pingaamisen.

![image](https://github.com/user-attachments/assets/63914966-e6c6-48cc-96ec-879d53508beb)

h1:lla näkyy icmp-paketit.

![image](https://github.com/user-attachments/assets/18bd0973-4345-4ccc-b26f-c184d1fecf32)

spoof_icmp.py;

