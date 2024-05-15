# Projekti: Kuinka "bännätä" verkkosivu salt minionilta.(viimeistelen vielä "esitys kuntoon")
Projektin ideana on luoda palvelin, jolta on estetty pääsy tietylle verkkosivulle. Esimerkissä käytetään verkko sivuna youtubea, joka ei sinänsä oli haitallinen verkkosivu.
Esimerkkiä voi kuitenkin hyödyntää melkeimpä mihin vaan verkkosivuun. Vain squidille annettuja konfiguraatioita pitää muuttaa. 
Tämä tekeekin projektista hyödyllisen sillä joskus on hyödyllistä sensuuroida tiettyjä verkkosivuja käyttäjiä
ltä. Erityisesti työympäristöissä tai lasten kanssa.

### Laitteiden tekniset ominaisuudet:
* Lenovo yogaslim 7
* AMD Radeon
* Windows 11
* Projekti on tehty käyttäen Oracle VM Virtual Boxia.
* Käytetyissä virtuaaliboxeissa käytetään linux ympäristöä, (Debian).
* Huomitoitavaa on se että ohjeet on http verkkosivuille eikä https. Ohjeissa käsitellään myös mainosten blockaus.
### Lisenssi: GNU General Public License v3.0

## Vaihe 1: Virtuaali koneitten luonti.
Tehtävä aloitetaa luomalla kaksi virtuaalikonetta. Tajusin että tässä vaiheessa on hyvä hyödyntää kurssin tehtävää 2 [soittokotiin](. Loin siis 2 virtuaalikonetta käyttäen vagranttia. Vagrant oli ladattu windows 11 koneelleni. 
Käytin ohjeena Teron kirjoittomaa ohjeta: [Two Machine Virtual Network with debian 11 and bullseye](https://terokarvinen.com/2021/two-machine-virtual-network-with-debian-11-bullseye-and-vagrant/).

<img width="494" alt="Näyttökuva 2024-05-11 205206miisaprojekti1" src="https://github.com/MiisaS/Servers_2024/assets/122888617/0107e5d6-bc4b-4005-81ad-cdc3ea54d8d3">

Luotiin aluksi kansio, muokattiin tiedosto notepadillä. Tiedostoon lisätiin teron kirjoittama konfiguraatio:
```
$tscript = <<TSCRIPT
set -o verbose
apt-get update
apt-get -y install tree
echo "Done - set up test environment - https://terokarvinen.com/search/?q=vagrant"
TSCRIPT

Vagrant.configure("2") do |config|
	config.vm.synced_folder ".", "/vagrant", disabled: true
	config.vm.synced_folder "shared/", "/home/vagrant/shared", create: true
	config.vm.provision "shell", inline: $tscript
	config.vm.box = "debian/bullseye64"

	config.vm.define "t001" do |t001|
		t001.vm.hostname = "t001"
		t001.vm.network "private_network", ip: "192.168.88.101"
	end

	config.vm.define "t002", primary: true do |t002|
		t002.vm.hostname = "t002"
		t002.vm.network "private_network", ip: "192.168.88.102"
	end
	
end
```
Voimme huomata että koneet ovat muodostuneet onnistuneest.

<img width="517" alt="Näyttökuva 2024-05-11 205244miisaprojekti2" src="https://github.com/MiisaS/Servers_2024/assets/122888617/87c75355-f57d-45cf-9150-fd0b14f89a8f">

<img width="286" alt="Näyttökuva 2024-05-11 205332miisaprojekti3koneetmuodostuivat" src="https://github.com/MiisaS/Servers_2024/assets/122888617/139d6d6d-8f44-4307-b72a-71e0515caabc">


## Vaihe 2: Salt master ja minion
T001 luotiin master ja T002 minionin. 
Kun oltiin varmistettu koneiden toimivan pystyin aloittamaan salt masterin ja minionin latauksen.
Latasimme aluksi salt masterin t001:

<img width="438" alt="Näyttökuva 2024-05-11 210302miisaprojekti4" src="https://github.com/MiisaS/Servers_2024/assets/122888617/5b7ceef4-0d01-4b26-83e0-0dec16a6a57a">


Onnistuneen latauksen jälkeen, Masterin ip osoite otettiin ylös komennolla.
```
hostname -I
```
Otimme yhteyden t002
Latasimme Salt minionin t002:


<img width="427" alt="Näyttökuva 2024-05-11 210500miisaprojekti5" src="https://github.com/MiisaS/Servers_2024/assets/122888617/d833aedc-3db1-4f48-a98e-0851ae44330f">


Onnistuneen latauksen jälkeen lisäsimme masterin Ip osoitteen minionin tietoisuuteen.
Tämän jälkeen uudelleen käynnistimme minionin komennolle:
```
systemctl restart salt-minion.service

```

<img width="454" alt="Näyttökuva 2024-05-11 211118miisaprojekti5" src="https://github.com/MiisaS/Servers_2024/assets/122888617/fb2870db-10b1-464c-baca-5843dccb8f83">

Tämä ei onnistunut puuteellisten oikeuksien vuoksi:
Syy oli niinkin yksinkertainen kun että sudo oli unohtunut oikea komento oli:
```
sudo systemctl restart salt-minion.service
```

Tämänjälkeen kirjauduin t001 ja hyväksyin avaimen.

<img width="386" alt="Näyttökuva 2024-05-11 213228miisaprojekti6" src="https://github.com/MiisaS/Servers_2024/assets/122888617/dbf7bad7-7138-45a7-97d9-74e9c4beedff">



## Vaihe 3: Squidin asennus
Kokeiltiin miltä yhteyden ottaminen youtubeen näyttää ennenkuin rupeamme estämään yhteyksiä. Hyödynnimme tässä squiddia. Squiddia proxya.
```
Squid proxy is commonly used for several purposes, including caching, load balancing, filtering traffic from websites, and for security purposes.
```
Lainattu sivustolta: [Manageengine](https://www.manageengine.com/products/firewall/tech-topics/what-is-squid-proxy.html)

<img width="299" alt="Näyttökuva 2024-05-11 214719miisaprojekti7" src="https://github.com/MiisaS/Servers_2024/assets/122888617/666938a2-3037-4fc1-a758-5025efe5e286">

Asensin squidin masterilleni komennolla:
```
sudo apt install squid
```

<img width="602" alt="Näyttökuva 2024-05-11 223306squidasennettumiisa8" src="https://github.com/MiisaS/Servers_2024/assets/122888617/87ea26a3-cff2-4b39-b87c-eb46098313d0">

Kokeilin vielä yhteyttä youtubeen wget komentoa käyttäen.
<img width="653" alt="Näyttökuva 2024-05-11 225452yht kokeileminensquidmiisa9" src="https://github.com/MiisaS/Servers_2024/assets/122888617/a412d372-0c7e-48e6-98d1-ca11835f5148">
Asensin squidin myös minionille. Asennuksessa oli tarkoitus käyttää.
```
pkg.install
```
Mutta asensinkin sen suoraan minionilla samoin kun masterille.
## Vaihe 4: Squidin konfigurointi
Squid konfiguroitiin niin että valitulle sivulle/sivuille ei voi luoda yhteyttä. Tässä esimerkissä käytimme youtubea. 
Käytimme tässä Squidin blacklist toimintoa ja konfiguroinnissa käytettiin ohjeena redhatin ohjetta.
Linkki ohjeeseen [tässä](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/networking_guide/configuring-a-domain-blacklist-in-squid)
Konfigurointi aloitettiin avaamalla squidin konfiguraatio tiedosto.
```
/etc/squid/squid.conf
```
Tiedostoon kirjoitin
```
acl domain_blacklist dstdomain "/etc/squid/domain_blacklist.txt"
http_access deny all domain_blacklist
```
Tämän jälkeen loin tiedoston domain_blacklist.txt. Käytin aluksi väärää komentoa.
```
mkdir /etc/squid/domain_blacklist.txt
```
Komento loi siis hakemiston eikä teksti tiedostoa. Huomasin tämän yrittäessäni päästä muokkaamaan domain_blacklist.txt sisältöä. 
Pääsin siis poistämään luoneeni kansion. Käytin siinä komentoa.
```
rm -r /etc/squid/domain_blacklist.txt
```
Aloitin uudestaan avaamalla
```
cd /etc/squid/
nano domain_blacklist.txt
```
Tiedostoon muokattiin sisällöksi osoitteet joihin ei haluttu että yhteyttä saadaan eli tässä esimerkissä youtube. 
```
.youtube.com
youtube.net
```
!<img width="554" alt="Näyttökuva 2024-05-12 201052lisättiinblacklistitmiisa12" src="https://github.com/MiisaS/Servers_2024/assets/122888617/c4938dfa-4929-44a2-b452-92195c305ca1">

Tämän jälkeen tiedosto tallennettiin ja Squid käynnistettiin uudelleen.
```
sudo systemctl restart squid
```
Kun squid oli uudelleen käynnistetty kokeiltiin yhteyttä uudelleen. ja TADAA yhteyttä ei voitu enää muodostaa. Voitiin siis todeta konfiguroinnin toimineen.
!<img width="523" alt="Näyttökuva 2024-05-12 201443voidaantodetaettäeitoimimiisa14" src="https://github.com/MiisaS/Servers_2024/assets/122888617/3350727e-6170-4504-ae18-66a9fccf11b6">


## Vaihe 5: Konfiguraatioiden siirtäminen salt minionille
Seuraavaksi lähdimme siirtämään tehtyjä konfiguraatioita salt minionille. Saman voisi tehdä myös usealle minionille jos haluaisi. Tiedostojen jakaminen masterilta minionille on helppoa.
Käytin apuna saltstack:sta löytämääni ohjetta.[Linkki](https://serverfault.com/questions/946639/how-to-copy-file-from-master-to-minions-on-salt-stack)

Aloitimme tarkastamalla, että masteri saa yhteyden minionille. 
```
ping -c 1 192.168.88.102
```
Koska squid oli jo ladattu, voimme aloittaa tiedostojen siirtämisen. Käytettiin komentoa
```
sudo salt-cp 't002' /etc/squid/squid.conf /etc/squid/squid.conf
```
Siirto onnistui.
https://serverfault.com/questions/946639/how-to-copy-file-from-master-to-minions-on-salt-stack
!<img width="629" alt="Näyttökuva 2024-05-12 201227oikeasiirto002blacklistmiisa13" src="https://github.com/MiisaS/Servers_2024/assets/122888617/b98672f9-54ad-4713-93fb-430b38e3a0f4">
!<img width="650" alt="Näyttökuva 2024-05-12 194723siirtoonnistuimiisa10uus" src="https://github.com/MiisaS/Servers_2024/assets/122888617/e531cd8b-9185-402d-9745-20199c2a5e79">

## Vaihe 6: Minion
Aloitettiin tarkastamalla onko conf ja blacklist tiedostot varmasti siirtyneet.
Otimme ssh yhteyden minionille.
```
vagrant ssh t002
```
Ja aloitimme tarkistamaan löytyykö tarvittavat tiedostot. Tarkistimme ensin konfiguraatio tiedoston.

!<img width="560" alt="Näyttökuva 2024-05-12 210610siirtynytoikein" src="https://github.com/MiisaS/Servers_2024/assets/122888617/ca788beb-ef61-4ccb-b1ac-177adde7023a">

Tämän jälkeen tarkistin että osoite tiedosto oli myöskin siirtynyt.

!<img width="512" alt="Näyttökuva 2024-05-12 223627varmistusblacklistt002miisaprojekt" src="https://github.com/MiisaS/Servers_2024/assets/122888617/d51fa36e-28e3-444d-a1e2-a712b5226945">

Kun kaikki oli siirtynyt pystyimme lähteä kokeilemaan miten käy kun yritämme hdistää youtubeen minionilta. 

!<img width="611" alt="Näyttökuva 2024-05-12 215135entiiämitätapahtuumiisaprojekti888" src="https://github.com/MiisaS/Servers_2024/assets/122888617/013d43dd-8b63-48ed-bf3f-580d311c3b6d">

## Vaihe 7: Mainosten blokkaus joka on myöskin toinen tapa blokata tietty osoite.
Aloitimme luomalla tiedoston johon lisäsimme bännättävät osoitteet. Tähän voit valita itse haluamasi tai hyödyntää githubista löytyvää valmista osoitelistaa. Esimerkiksi tämä lista antoi laajan valikoiman osoitteita, joita voisi olla hyödyllistä bännätä. Osoitteet olivat myös ajankohtaisia ja niitä päivitettiin aktiivisesti.



https://github.com/blocklistproject/Lists

Valitsin itselleni vain muutaman esimerkin vuoksi. Kokonaisten listojen laittaminen olisi vienyt liikaa aikaa.
Lisäsin [tästä](https://blocklistproject.github.io/Lists/ads.txt) listasta muutaman. 

<img width="509" alt="Näyttökuva 2024-05-13 211935lisäsinmuutamanmiisaprojekti984384" 
src="https://github.com/MiisaS/Servers_2024/assets/122888617/45ff9bc9-4822-40bf-b11f-500dae8a535a">

![Näyttökuva 2024-05-13 212807](https://github.com/MiisaS/Servers_2024/assets/122888617/0ca8b829-1cd2-40ca-840e-e74dffa2a157)

Jostain syystä en saanut luotua oikeeseen hakemistoon .sls tiedostoani, ja aikani ei riittänyt syvempään ongelman ratkaisuun. Loin siis adblocker.sls tiedoston käsin. 

[Tämän](https://stackoverflow.com/questions/60873775/no-matching-sls-found-for-httpd-in-env-base) lähteen perusteella srv/salt/ on oikea paikka luoda, joten en ole varma mistä kiikastaa. Kokeilin vielä suorittaa konfiguraatiot omassa testiympäristössäni. 

Loin tiedoston adblocker.sls
```
sudo nano adblocker.sls
```

Lisäsin tiedostoon githubin listasta samat kuin aikaisemmin t001 luodulle tiedostolle.
Suoritin scriptin komennolle:
```
sudo chmod +X adblocker.sls
```


 <img width="373" alt="Näyttökuva 2024-05-13 220409suoritettiinscriptimuntestissä" src="https://github.com/MiisaS/Servers_2024/assets/122888617/b88e854b-3bad-414b-a5c2-2a28aa4679dd">

## Vaihe 8: Projektin kehitys
Koska projekti suoritettiin linux ympäristössä jossa ei ollut graafista työpöytää, oli selitys lähinnä teoreettinen sillä harva hyötyy tässä näytetyistä muutoksista kotona. Jos ei muuta niin youtubeen ei saada enää configuraatioiden jälkeen yhteyttä. 

Adblocker oli extra osio jota halusin myös kokeilla ja koen sen lähes kaikille hyödylliseksi. Siksi uskalsinkin suorittaa kyseiset omalle testiympäristölleni. 



### Lähteet
* https://terokarvinen.com/2021/two-machine-virtual-network-with-debian-11-bullseye-and-vagrant/
* https://nordvpn.com/fi/blog/use-hosts-file-block-ads-malware/
* https://www.manageengine.com/products/firewall/tech-topics/what-is-squid-proxy.html
* https://stackoverflow.com/questions/60873775/no-matching-sls-found-for-httpd-in-env-base
* https://www.makeuseof.com/how-to-remove-software-repositories-on-ubuntu/
* https://community.spiceworks.com/t/squid-proxy-domain-blocking-issue-www/446186
* https://terokarvinen.com/2024/configuration-management-2024-spring/
* https://stackoverflow.com/questions/60873775/no-matching-sls-found-for-httpd-in-env-base
* 
