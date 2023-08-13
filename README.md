1. DMZ Bölgesini konfigüre edin

DMZ Network ID: 195.195.195.0/29

DOMAIN : artemis.com
GW : 195.195.195.1
DMZ SW VLAN 1 : 195.195.195.2
ZERO IP : 195.195.195.3

TASK:
1. Zero server üzerinde ki tüm servisler aktif hala getirin.
2. DMZ SW üzerinde admin kullanıcısı için telnet üzründeki eirşimi sağlayın.
3. ZERO Server'ın bağlı olduğu port üzerinde güvenlik ayarlarını yapın
4. R3 Router üzerinde, DMZ Tarafı için routing işlemini set edin.
5. Tüm testleri yapın.

----------------------------------------------------
ssh bağlantı configurasyon

SSH
---

1. 	hostname R4
2. 	ip domain-name artemis.com
3. 	enable secret Siber5
4. 	username admin pri 15 secret Cisco1
4. 	line vty 0
		transport input ssh
		login local
		exit
7. 	ip ssh version
8. 	ip ssh auşentication-retries 3
9. 	ip ssh time-out 30
10.	crypto key generate rsa
------------------------------------------------------
------------------------------------------------------
DMZ 
----
enable secret Siber5

int vlan 1
	ip add 195.195.195.2 255.255.255.248
	no shut
	exit
ip default-gateway 195.195.195.1
ip domain-name artemis.com
ip name-server 195.195.195.3

line vty 0
	login local
	exit
username admin privilege 15 secret Cisco1
enable secret Siber5


int f0/1
	swi mode access
	swi port-sec
	switchport port-security maximum 5
	switchport port-security mac-address 00d0.ba73.6157 
	switchport port-security violation shutdown 
	exit









R3
-------------------------------
enable secret Siber5

int g6/0
	no shut
	ip add 195.195.195.1 255.255.255.248
	exit

int g3/0
	no shut
	ip add 1.1.1.1 255.255.255.252

line vty 0
	login local
	exit

username admin privilege 15 secret Cisco1

router eigrp 55
 passive-interface GigabitEşernet6/0
 network 195.195.195.0 0.0.0.7
 network 1.1.1.0 0.0.0.3
 no auto-summary
!

ip domain-name artemis.com
ip name-server 195.195.195.3

ip dhcp excluded-address 196.196.196.1 196.196.196.5
ip dhcp excluded-address 196.196.196.126
ip dhcp excluded-address 197.197.197.1
ip dhcp excluded-address 197.197.197.126
ip dhcp excluded-address 198.198.198.1
ip dhcp excluded-address 198.198.198.126
!
ip dhcp pool Kat1
 network 196.196.196.0 255.255.255.128
 default-router 196.196.196.126
 dns-server 195.195.195.3

ip dhcp pool v12
 network 197.197.197.0 255.255.255.128
 default-router 197.197.197.126
 dns-server 195.195.195.3
ip dhcp pool vl22
 network 198.198.198.0 255.255.255.128
 default-router 198.198.198.126
 dns-server 195.195.195.3


2. KAT1
------------------------------
enable secret Siber5


Kat1 Network ID: 	196.196.196.0/25

Kat1 Sw IP:		196.196.196.1
Kat1 Gw: 		196.196.196.126

R3-R4 Network Id	1.1.1.0/30
R3 >> R4 G3/0: 		1.1.1.1
R4 >> R3 G3/0		1.1.1.2

TASK
1. Kat1 çalışanları IP Bilgilerini R3 Router 'dan alacaklar..
2. Hem Kat1 sw, Hem de R4 Router 'ına admin kullanıcısı uzak bağlantı için 
   SSH 'etkinleştir.
3. Kat1 çalışanları için outlook tanımlamalarını yap..

4. Test.. Test .. test...

R4
--------------------------------------------------
enable secret Siber5

interface GigabitEşernet3/0
 ip address 1.1.1.2 255.255.255.252
 no shut
 exit
interface GigabitEşernet6/0
 ip address 196.196.196.126 255.255.255.128
 ip helper-address 1.1.1.1
 no shut
 exit

interface GigabitEşernet2/0
 no shut
 ip address 2.2.2.1 255.255.255.252


router eigrp 55
 network 196.196.196.0 0.0.0.127
 network 1.1.1.0 0.0.0.3
 network 197.197.197.0 0.0.0.127
 network 198.198.198.0 0.0.0.127
 network 2.2.2.0 0.0.0.3
 no auto-summary
 exit

ip domain-name artemis.com
ip name-server 195.195.195.3

user admin pri 15 sec Cisco1
line vty o
	transport input ssh
	login local
	exit
crypto key generate rsa
ip ssh time 30
ip ssh aut 3
ip ssh ver 2

interface g7/0
	no shut
	no ip add
	exit
int g7/0.21
	enc dot 21
	ip add 197.197.197.126 255.255.255.128
	ip helper-address 1.1.1.1
	exit
int g7/0.22
	enc dot 22
	ip add 198.198.198.126 255.255.255.128
	ip helper-address 1.1.1.1
	exit








Kat1 Switch
----------------

enable secret Siber5

int vlan 1
	ip add 196.196.196.1 255.255.255.128
	exit

ip default-gateway 196.196.196.126
ip domain-name artemis.com
ip name-server 195.195.195.3

user admin pri 15 sec Cisco1
line vty o
	transport input ssh
	login local
	exit
crypto key generate rsa
ip ssh time 30
ip ssh aut 3
ip ssh ver 2


-------------------------------------------------
--------------------------------------------------

TASK
Kat2 Çalışanları..


1. Bu bölgede çalışanlar 2 ayrı departman.. IK ve Ar-Ge çalışanları..

	IK Çalışanları Sw üzerinde Fa0/1-Fa0/14 'e kadar..
	Ar-Ge Çalışanları ise Sw üzerinde Fa0/15-Fa0/24 'e kadar komumlanmıştır. 
	Bu iki departmanda çalışanların bilgisayarları farklı Vlan 'larda olmaları istenmektedir.

IK 	Vlan 21 >> Network ID: 197.197.197.0/25 >> Vlan 21 IP: 197.197.197.1 Vlan21 GW: 197.197.197.126
Ar-ge 	Vlan 22 >> Network ID: 198.198.198.0/25 >> Vlan 21 IP: 198.198.198.1 Vlan21 GW: 198.198.198.126

2. Bu vlanlar 'da bulunan tüm bilgisayarlar IP bilgilerini DHCP Server olarak konumlanan R3 'üzerinden alacaklardır.
3. Hem IK hem de Ar-Ge çalışanlarının outlook ayarlarını yapınız..
4. Kat2 Sw 'e de uzaktan erişim için SSH bağlantısını sağlayın..

STATIC VLAN

ister static ister dinamik vlanlar oluşturun, bu vlanlar flash ta vian.dat dosyasında tutuulur.. 

- vlanlar oluşturulur.
- hangi interface 'leri vlanda istiyorsanız o interface 'ler vlan'a taşınır.. (Acccess)
- farklı vianlar kesinlike trunk alınmış interface 'ler üzerinden sağlanır. 
- vlanlar arasında paket taşınırken encapsulation yapılır. bunun için ISL veya 802.1q protokolleri kullanılır. 
- Kat2 sw 'e admin kullanıcı SSH üzerinden erişim sağlasın..




Vlan oluşturma

1. metod. (config modda)

	vlan 21
		name IK
		exit

2. metod (enable)

	vlan database
		vlan 22 name Ar-Ge
		exit >>> "database çıkarken vlan oluşur (apply)

------------

interface taşıma (access)

fa0/1-fa0/14 	>> vlan 21
fa0/15-fa0/24 	>> vlan 22

	
Kat2 sw
-------

hotname Kat2

ip domain-name artemis.com
ip name-server 195.195.195.3

vlan 21
	name IK
	exit
vlan 22
	name Ar-Ge
	exit

int range fa0/1-14
	swi access vlan 21
	exit
int range fa0/15-24
	swi access vlan 22
	exit

int g0/1
	swi mode trunk
	exit
int vlan  21
	ip add 197.197.197.1 255.255.255.128
	exit
inter vlan 22
	ip add 198.198.198.1 255.255.255.128

---------------------------------

TASK:

KAT3 çalışanların diğer tüm networklerle erişimlerini sağlayın.. Bu çalışanları 2 farklı Vlan yapısında konumlandı.

FINANS 		Vlan 31 >> Network ID: 199.199.199.0/25 	>> kat3-Sw1 Vlan 31 IP: 199.199.199.1 		Vlan31 GW: 199.199.199.126
PAZARLAMA 	Vlan 32 >> Network ID: 199.199.199.128/25 	>> kat3-Sw1 Vlan 32 IP: 199.199.199.129 	Vlan32 GW: 199.199.199.254

FINANS 		Vlan 31 >> Network ID: 199.199.199.0/25 	>> kat3-Sw2 Vlan 31 IP: 199.199.199.2 		Vlan31 GW: 199.199.199.126
PAZARLAMA 	Vlan 32 >> Network ID: 199.199.199.128/25 	>> kat3-Sw2 Vlan 32 IP: 199.199.199.130 	Vlan32 GW: 199.199.199.254


Vlan 31 ınterfaces >>	Kat3-Sw1: Fa0/2-14	Kat3-Sw2: Fa0/2-14
Vlan 32 ınterfaces >>	Kat3-Sw1: Fa0/15-2 4	Kat3-Sw2: Fa0/15-24

Vlan yapısı VTP kullanılarak yapılacaktır. 
Tüm çalışanların bilgisayarları ip bilgilerini R3 Router üzerinden alacaklardır. 
ılk etapta Rputing işlemine Kat3 çalışanları için R4, R2 ve Kat3-ML üzerinden kongigure yapınız..
Bu her iki cihazda uzaktan erişim içim SSH bağlantısını aktif hale getirin.. 

R4 - R2 Network 2.2.2.0/30		R4 g2/0 ip: 2.2.2.1	R2 g3/0 	ip: 2.2.2.2
R2 - Kat3-ML Network 3.3.3.0/30		R2 G6/0 ip: 3.3.3.1	Kat3-ML G0/1 	ip: 3.3.3.2


R2
---------------------
interface GigabitEşernet3/0
 ip address 2.2.2.2 255.255.255.252

interface GigabitEşernet6/0
 ip address 3.3.3.1 255.255.255.252
 

router eigrp 55
 network 3.3.3.0 0.0.0.3
 network 2.2.2.0 0.0.0.3
 auto-summary



Kat3-ML
----------------

interface GigabitEşernet0/1
 no switchport
 ip address 3.3.3.2 255.255.255.252

ip routing
router eigrp 55
 network 3.3.3.0 0.0.0.3
 no auto-summary

	Açıklama:
	vtp (vlan trunking protocol)
	vtp server, vtp client

VTP configuration
--------------------
vtp domain artemis.com
vtp mode server
vtp version 2
vtp password Cisco1

vlan 31
	name Finans
	exit
Vlan 32
	Name Pazarlama



Kat3-Sw1
----------------

hostname Kat3-Sw1

int f0/1
	swi mode trunk
	switchport trunk allowed vlan all


vtp domain artemis.com
vtp mode client
vtp version 2
vtp password Cisco1


------------------------------------------------------------------------------------
------------------------------------------------------------------------------------
ÇOK ÖZEL NOT (L3 VE L2) 

VLAN, VTP

l3 'TE YAPILANLAR:
-----------------

vtp mode server
 

vlan 31
	name Finans
vlan 32
	name Pazarlama

interface vlan 31
 ip add 199.199.199.126 255.255.255.128
 ip helper 1.1.1.1	
inter vlan 32
 ip add 199.199.199.254 255.255.255.128
 p helper 1.1.1.1


interface range fa0/1-2
	switchport trunk encapsulation dot1q
	switchport mode trunk
	switchport trunk allowed vlan all

int g0/1
	no switchport
	ip add 3.3.3.2 255.255.255.252

ip routing
route eigrp 55
	no au
	net 3.3.3.0 0.0.0.3
	net 199.199.199.0 0.0.0.127
	net 199.199.199.128 0.0.0.127
	passive-interface vlan 31
	passive-interface vlan 32


SW1 L2
-----------

vtp mode client
vtp domain artemis.com
vtp version 2
vtp pass Cisco1

int range fa0/2-14
	swi acc vlan 31

int range fa0/15-24
	swi acc vlan 32

int vlan 31
 ip add 199.199.199.1 255.255.255.128

int vlan 32
 ip add 199.199.199.129 255.255.255.128

int fa0/1
 switchport mode trunk

ip default-gateway 199.199.199.126

-------------------------------------------
------------------------------------------------
R3-R1 Network 4.4.4.0/30

R3 >> R1 G2/0: 4.4.4.1
R1 >> R3 G3/0: 4.4.4.2

R3-R2 Network 5.5.5.0/30

R3 >> R2 G4/0: 5.1
R2 >> R3 G4/0: 5.2

R1-R0 Network 6.6.6.0/30

R1 >>R0 S1/0: 6.1
R0 >>R1 S0/0: 6.2



R0-R2 Network 7.7.7.0/30

R0 >> R2 S1/0: 7.1
R2 >> R0 S1/0: 7.2

R4-R1 Network 8.8.8.0/30

R4 >> R1 G4/0: 8.1
R1 >> R4 G4/0: 8.2

R2-R1 Network 9.9.9.0/30

R2 >> R1 G2/0: 9.1
R1 >> R2 G2/0: 9.2

------------------------
------------------------


212.154.01010100.00000000
255.255.11000000.00000000
----------------------------
212.154.64.0






















 


