# Multi-Site-Enterprise-Network
Acest proiect reprezintă o topologie de rețea enterprise, simulată în Cisco Packet Tracer. Infrastructura interconectează patru locații distincte (Home Office, Branch Office, Central Office și Data Center), implementând concepte de rutare, switching (Layer 2 / Layer 3), redundanță, securitate și infrastructură wireless centralizată (WLC).


<img width="533" height="294" alt="image" src="https://github.com/user-attachments/assets/8e300bcf-ec4f-410f-9bfc-b6bcda0358ae" />


## 🛠️ Tehnologii și Protocoale Implementate

* **Layer 2 & Switching:** VLANs, 802.1Q Trunking, Rapid-PVST+ (Spanning Tree), EtherChannel (LACP & PAgP), Inter-VLAN Routing (ROAS & SVI).
* **Layer 3 & Redundanță:** HSRP (Hot Standby Router Protocol) cu load-balancing prin priorități, IP Helper/DHCP Relay, Rutare Statică.
* **Securitate (Switch & Device):** Port Security (Protect, Restrict, Shutdown, MAC Sticky), SSHv2, autentificare locală, parole criptate (Secret), dezactivare porturi nefolosite (BlackHole VLAN).
* **Wireless (WLAN):** Cisco WLC (Wireless LAN Controller), Lightweight APs, FlexConnect, WPA2-PSK, 802.1x (RADIUS Authentication), Guest Networks.
* **Servicii de Rețea:** DHCP, DNS, HTTP, RADIUS.

---

## 🏢 Arhitectura Rețelei pe Locații

### 1. Home Office (HO)
O rețea SOHO (Small Office / Home Office) izolată, cu acces la internet prin NAT.
* **Echipament:** Wireless Router (HO-R1).
* **Configurații:** DHCP Server intern pentru clienți, Dual-SSID (Rețea internă `Home_PSK` și Rețea vizitatori `Home_Guest_PSK`).
* **Securitate WLAN:** WPA2-Personal (AES).

### 2. Branch Office (BR)
O sucursală conectată la infrastructura centrală, bazată pe o arhitectură de tip Router-on-a-Stick.
* **Layer 2:** Segmentare în VLAN-uri (Office, WiFi, Guest, Mgmt, BlackHole). Toate porturile nefolosite sunt închise (Shutdown) și izolate în VLAN 101.
* **Layer 3:** Sub-interfețe pe router (BR-R1) pentru Inter-VLAN Routing (802.1Q).
* **Securitate:** Port-security configurat pentru maxim 3 adrese MAC dinamice per interfață (Violation: Protect).
* **Wireless:** WLC preconfigurat gestionând AP-uri locale cu profile multiple: PSK pentru Guest și 802.1x pentru angajați.
* **DHCP:** Alocare centralizată a adreselor IP din Data Center folosind `ip helper-address`.

### 3. Central Office (CO)
Sediul central, bazat pe o arhitectură ierarhică robustă (Routere + Multilayer Switches + Access Switches).
* **Redundanță L2 (EtherChannel):** Agregare de link-uri între Core și Access folosind **LACP** (Port-channel 1) și **PAgP** (Port-channel 2).
* **Redundanță L3 (HSRP):** Două routere (CO-R1 și CO-R2) acționează ca gateway-uri redundante. Încărcarea este distribuită (Load Balancing): CO-R1 este Active pentru VLAN 8, 10, 11, iar CO-R2 este Active pentru VLAN 13, 99, 100, folosind preempt.
* **Spanning-Tree:** CO-CSW1 este configurat manual ca Root Bridge (Priority 4096).
* **WLAN:** Management centralizat prin WLC, VLAN-uri dedicate pentru acces WiFi și infrastructură Lightweight AP.
* **Securitate:** Port-security cu funcția MAC Sticky (Violation: Shutdown pe clienți, Restrict pe servere).

### 4. Data Center (DC)
Nucleul rețelei care găzduiește serviciile centrale (DNS, RADIUS, DHCP, WEB).
* **Routing de înaltă performanță:** Rutarea inter-VLAN este realizată direct pe Multilayer Switches (DC-CSW1 și DC-CSW2) folosind interfețe SVI (Switch Virtual Interface).
* **Redundanță Totală:** 
  * **HSRP la nivel de SVI:** CSW1 este Active pentru jumătate din VLAN-uri, CSW2 pentru cealaltă jumătate.
  * **HSRP la nivel de Routere Edge:** DC-R1 și DC-R2 oferă ieșire redundantă spre ISP.
* **EtherChannel:** Link-uri agregate LACP între switch-urile Core.
* **Spanning-Tree Tuning:** DC-CSW1 este Root Primary pentru anumite VLAN-uri (Priority 8192) și Secondary pentru restul, balansând traficul L2 împreună cu DC-CSW2.
* **AAA & Servicii:** Server RADIUS pentru autentificarea clienților wireless 802.1x (pe sucursale), Servere DNS și DHCP centralizate.

---

## 🔑 Acces Echipamente și Autentificare

**Echipamente de Rețea (Routere & Switch-uri CLI)**
* **Username:** `admin`
* **Parolă consolă / vty / SSH:** `Admin123`
* **Enable Secret:** `Admin123`

**Interfețe Web & Controllere**
* **Interfață GUI WLC (Branch & Central Office):** User: `admin` | Parolă: `Admin123`
* **HO-R1 (Wireless Router SOHO):** Parolă administrare: `Admin123`

**Acces Wireless (WLAN)**
* **SSID Angajați (Home_PSK / Office_PSK):** WPA2-PSK: `Cisco123`
* **SSID Guest (Home_Guest_PSK / Office_Guest_PSK):** WPA2-PSK: `Guest123`
* **SSID 802.1x (Office_802.1x):** Autentificare RADIUS (Ex. User: `BR-Employee3` / Parolă: `BR-Employee3`)

**Securitate RADIUS**
* **RADIUS Shared Secret:** `Admin123`
