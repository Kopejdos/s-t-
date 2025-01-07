sítě a tutoš na ně:

videa a duležité časy:
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


https://owncloud.vspj.cz/index.php/s/9OhndKJaZWmTFdO
