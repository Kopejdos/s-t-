sítě a tutoš na ně:
videa a duležité časy:

video 1:
https://owncloud.vspj.cz/index.php/s/azXXjMNTRhYJ25v

Základní příkazy v Terminálu
  1.	pwd – aktuální adresář
  2.	ls – výpis složky
  3.	ls -a – výpis složky se skrytými soubory
  4.	dnf search [NAZEV_BALICKU] – najde všemožné balíčky k instalaci
  5.	ifconfig – seznam síťových adaptérů
  6.	netstat -antup (all, numeric, TCP, UDP, program) – aktivní spojení a porty
  7.	rpm -ql [NÁZEV_BAÍČKU] – vypíše obsah balíčku

Instalace MC (video 2 – 25:00)
  1.	V konzoli napíši „mc“
  2.	Vše odsouhlasit
  3.	Zadat heslo

Statické parametry síťové karty pomocí MC (video 2 – 1:21:22)
  1.	mc -> cd /etc/sysconfig/network-scripts
  2.	ifcfg-eth0 -> edit
  3.	BOOTPROTO=static
  4.	IPV6INIT=no
  5.	IPV6_AUTOCONF=no
  6.	IPV6_DEFROUTE=no
  7.	IPV6_FAILURE_FATAL=no
  8.	Na konec napsat:
    •	IPADDR=192.168.60.XXX (XXX = 200 + číslo počítače)
    •	PREFIX=24 (maska), lze i NETMASK=255.255.255.0
    •	GATEWAY=192.168.60.254 (poslední adresa z rozsahu)
    •	DNS1=192.168.50.165 (DNS server 1)
    •	DNS2=8.8.8.8 (DNS server 2)
  9.	Uložit
  10.	V terminálu -> ifup eth0 – aktualizuje parametry rozhrání


Linux jako router s NATem (video 2 – 1:36:00)
Linux
  1.	Na Linux mašině v Hyper-V -> přidám síťovou kartu a switch nastavím na „internal“
  2.	mc -> cd /etc/sysconfig/network-scripts (v obou oknech)
  3.	Najedu na soubor ifcfg-eth0
  4.	Stisknu „F5“
  5.	Pole „na“: ifcfg-eth1
  6.	ifcfg-eth1 -> edit
    •	NAME=eth1
    •	UUID -> #UUID (zakomentovat)
    •	DEVICE=eth1
    •	IPADDR=10.0.0.1
    •	PREFIX=8
    •	GATEWAY -> #GATEWAY (na druhé síťovce -> zakomentovat)
    •	Všechna DNS -> #DNS (na druhé síťovce -> zakomentovat)
  7.	Uložit
  8.	V terminálu -> ifup eth1 – aktualizuje parametry rozhrání
Windows 1:45:30
  9.	Nastavení adaptéru
    •	IP address: 10.0.0.9 (nebo jiná z rozsahu /8)
    •	Subnet mask 255.0.0.0 (/8)
    •	Default gateway: 10.0.0.1 (adresa interní síťovky eth1 z Linuxu)
    •	DNS: 8.8.8.8 (Googlovská)
  10.	V terminálu -> ifup eth0 – aktualizuje parametry rozhrání
Linux 1:58:40
  11.	mc -> cd /etc
  12.	sysctl.conf -> edit
  13.	net.ipv4.ip_forward = 1 (povolení forwardování na Linuxu)
  14.	Z Windows ještě nepingneme ven do Internetu -> Linux na 192.168.60.XXX dostane paket s cílovou adresou našeho Windows, ale neví kam to poslat (musíme zapnout NAT)
  15.	Zapnutí NAT -> zapíná se ve firewallu
    •	Ze softwaru nainstalovat firewall
    •	Nastavení: Trvalá
    •	Pro eth0 -> public
    •	Maškarádování -> Povolit maškarádu v zóně
    •	Možnosti -> Znovu načíst firewall

SSH a SElinux – vzdálená kontrola počítače (Putty) (video 2 – 2:17:38)
  1.	mc -> cd /etc/ssh
  2.	sshd_config -> edit
  3.	odkomentovat port (a eventuelně ho změnit / změnit další nastavení)
    •	PermitRootLogin -> přístup přímo na root účet přes Putty (yes/no)
  4.	uložit a zpět do terminálu
  5.	sudo firewall-cmd --add-port [CISLO_PORTU]/tcp --permanent 
    (pro smazání místo add -> remove)
  6.	firewall-cmd --reload
  7.	semanage port -a -t ssh_port_t -p tcp [CISLO_PORTU] 
    (pro smazání místo -a -> --delete)
  8.	systemctl restart sshd.service 
  9.	systemctl status sshd.service 
  10.	netstat -antpu 

![image](https://github.com/user-attachments/assets/95fb9fac-7147-4501-b0c2-5d42fbd16f5c)


video 2:
https://owncloud.vspj.cz/index.php/s/9OhndKJaZWmTFdO

Linux jako DHCP server (video 3 – 18:20)
Linux
  1.	dnf search dhcp -> vyhledání balíčků dhcp
  2.	dnf install dhcp-server -> instalace DHCP serveru
  3.	vše odsouhlasit
  4.	mc -> cd /etc/dhcp
  5.	Do tohoto adresáře vložíme konfigurák z moodle
    •	wget https://alpha.kts.vspj.cz/~apribyl/dhcpd.txt
    •	mv dhcpd.txt dhcpd.conf -> přejmenování souboru
    •	obsah souboru:
authoritative;

default-lease-time 86400;

max-lease-time 259200;

option subnet-mask 255.0.0.0;

option domain-name-servers 192.168.50.165, 192.168.50.166;

option domain-name "vspj.cz";

subnet 10.0.0.0 netmask 255.0.0.0 {

range 10.0.0.100 10.0.0.200;

option broadcast-address

10.255.255.255;

option routers 10.0.0.1; }
![image](https://github.com/user-attachments/assets/c4009128-f9db-46e6-b76e-8ce84d0531d2)

  7.	systemctl start dhcpd -> nastartování DHCP serveru
  8.	firewall -> povolení DHCP
    •	Nastavení: Trvalá
    •	Pro eth1 -> public
    •	V seznamu vyberu dhcp
    •	Možnosti -> Znovu načíst firewall
Windows
  9.	Nastavení síťovky
  10.	Přepnout vše na automatickou konfiguraci
Linux
  11.	Seznam obsloužených PC (pokud bude třeba)
    •	cd /var/lib/dhcpd -> dhcpd.leases
  12.	Statická alokace 
    •	mc -> cd /etc/dhcp
    •	DOVNITŘ konkrétního subnetu:

![image](https://github.com/user-attachments/assets/2dbb1bed-126f-4630-b828-0cb8c2532be3)

Linux jako DNS (video 3 – 41:00)
  1.	Bude rekurzivní (nikam dotaz nepřeposílá, ale sám zjistí od kořenových serverů)
  2.	Bude primární autoritativní pro doménu (komukoliv bude na dotaz na danou doménu odpovídat)
  3.	dnf install bind
  4.	vše odsouhlasit
  5.	mc -> cd /etc
  6.	named.conf -> edit
    •	listen-on port 53 { any; };
    •	//listen-on-v6 port 53 { ::1; }; -> nepoužíváme, zakomentujeme
    •	allow-query { any; };
    •	recursion yes;
    •	allow-recursion {localhost; 10.0.0.0/8;}; -> rekurzi povolujeme jenom z naší sítě, jinak by hrozilo nebezpečí DNS zesilujícího útoku, v příkazu jsou všude středníky, žádné dvojtečky
  7.	systemctl start named
  8.	firewall -> povolení DNS
    •	Nastavení: Trvalá
    •	public
    •	V seznamu vyberu dns
    •	Možnosti -> Znovu načíst firewall

  9.	Přenastavení našeho DHCP na naší DNS
    •	mc -> cd /etc/dhcp
    •	dhcpd.conf -> edit
    •	option domain-name-servers 10.0.0.1;
    •	Uložit
  10.	Zónový soubor pro danou doménu
    •	mc -> cd /etc
    •	named.conf -> edit
    •	zkopírujeme zone “.” IN { … }
    •	vložíme pod to a přepíšeme:
    •	zone „myvspj.cz“ IN {
    •		type master;
    •		file „myvspj.cz“;
    •	}
  uložíme
    •	cd /var/named
    •	wget https://alpha.kts.vspj.cz/~apribyl/myvspj.cz.txt
    •	mv myvspj.cz.txt myvspj.cz -> přejmenování souboru
    •	Pokud na Windows nejde DNS zkusit ruční konfiguraci DNS (10.0.0.1) -> funguje? Pak je nutné restartovat dhcpd na Linuxu a obnovit nastavení síťovky na Windows.

Mailový klient pro doménu (Postfix a IMAP) (video 3 – 1:36:40)
Postfix
  1.	dnf install postfix
  2.	vše odsouhlasit
  3.	mc -> cd /etc/postfix
  4.	main.cf -> edit
    •	myhostname = mail.myvspj.cz (řádek 94)
    •	mydomain = myvspj.cz (řádek 102)
    •	myorigin = $mydomain (odkomentovat, řádek 118)
    •	inet_interfaces = all (odkomentovat, řádek 132)
    •	inet_interfaces = localhost (ZAkomentovat, řádek 135)
    •	inet_protocols = ipv4 (řádek 138)
    •	mydestination (ZAkomentovat, řádek 183)
    •	mydestination (odkomentovat, řádek 185 a 186)
    •	mynetworks = 10.0.0.0/8, 127.0.0.0/8 (řádek 283)
    •	home_mailbox = Maildir/ (odkomentovat, řádek 438)



  5.	Aliasy
    •	mc -> cd /etc
    •	aliases -> edit
    •	Poslední řádek odkomentovat a přepsat (root: eit) -> rootovská pošta se bude přesměrovávat na účet eit
    •	Uložit
    •	Musíme soubor transformovat do tvaru, který čte postfix
    •	postalias aliases -> vytvoří se soubor aliases.db, ten používá Postfix
  6.	systemctl start postfix
  7.	Test mailu z příkazové řádky
    •	dnf install mailx
    •	vše odsouhlasit
    •	echo „[TEXT_MAILU]“ |mail -s „[PŘEDMĚT_MAILU]“ [UCET_PRIJEMCE] -> poslání mailu
    •	echo „Toto je text mailu“ |mail -s „Toto je předmět mailu“ eit
  8.	mc -> cd /home/eit/Maildir -> zobrazení emailové schránky
  IMAP
  9.	dnf install dovecot
  10.	odsouhlasit vše
  11.	mc -> cd /etc/dovecot
  12.	dovecot.conf -> edit
    •	protocols = imap pop3 lmtp submission (odkomentujeme, řádek 24)
    •	listen = * (řádek 30)
    •	login_greeting = [POZDRAV_PŘI_PŘIHLÁŠENÍ] (změnit podle potřeby, řádek 42)
    •	login_trusted_networks = 10.0.0.0/8 127.0.0.0/8 (řádek 48)
  13.	systemctl start dovecot
  14.	firewall -> povolení Dovecot
    •	Nastavení: Trvalá
    •	public
    •	V seznamu vyberu imap, imaps, pop3, pop3s, smtp, smtp-submission
    •	Možnosti -> Znovu načíst firewall
 Windows
  15.	Nainstalujeme Thunderbird
  16.	Jméno: libovolné
  17.	E-mailová adresa: eit@myvspj.cz
  18.	Heslo: heslo účtu eit
  19.	Schválíme bezpečnostní výjimku
  20.	Odesílání pošty -> změna nastavení
    •	Pravým tlačítkem na účet
    •	Nastavení
    •	Server odchozí pošty (SMTP)
    •	Upravit
    •	Port 25
    •	Zabezpečení spojení: Žádné
    •	Způsob autentizace: Bez autentizace

Server Apache (video 3 – 2:23:08)
  1.	dnf install httpd
  2.	odsouhlasit vše
  3.	mc -> cd /etc/httpd/conf
  4.	httpd.conf -> edit (pokud bude potřeba editovat, ve výchozím stavu není potřeba)
  5.	systemctl start httpd
  6.	firewall -> povolení Apache
    •	Nastavení: Trvalá
    •	public
    •	V seznamu vyberu http
    •	Možnosti -> Znovu načíst firewall
  7.	Vlastní výchozí stránka
    •	mc -> cd /var/www/html
    •	touch index.html -> něco tam napsat
  8.	Přidání podpory PHP
    •	dnf install php
    •	vše odsouhlasit
    •	mc -> cd /var/www/html
    •	touch index.php -> napsat tam kód php, třeba (<?php phpinfo(); ?>)
    •	uložit
    •	Musíme restartovat server Apache, aby zjistil, že podporuje PHP -> systemctl restart httpd
    •	A smažeme index.html, protože ten má přednost před index.php
  9.	Uživatelské weby
    •	mc -> cd /etc/httpd/conf.d
    •	userdir.conf -> edit
    •	UserDir disabled (zakomentujeme, řádek 17)
    •	UserDir public_html (odkomentujeme, řádek 24)
    •	Uložit
    •	systemctl restart httpd
    •	mc -> „F7“ -> „public_html“ -> Vytvořím si v domovském účtu adresář s názvem public_html
    •	cd /public_html
    •	touch index.html
    •	uložit
    •	Adresa dané uživatelské webovky je 10.0.0.1/ ~eit/ -> nefunguje, kvůli špatně nastaveným oprávněním, takže:
    •	mc -> cd /home
    •	na složku „eit“
    •	soubor -> změna práv
    •	execute/search by others
    •	Nastaveno, pořád se ale nic neděje. Za to může SELinux. Vyskočí bublina SELinux varování: (možná, pokud ne, tak prostě postupuj dále)
    •	Otevřu ji pomocí „Ukázat“
    •	Zobrazím tlačítkem „Detaily“
    •	Najdu řádek setsebool -P httpd_enable_homedirs 1 a zkopíruji ho
    •	V terminálu příkaz spustím
  10.	Virtuální weby (podweby web1.myvspj.cz, atd…)
    •	Podle cvičení 13 na moodle https://moodle.vspj.cz/mod/page/view.php?id=110684
