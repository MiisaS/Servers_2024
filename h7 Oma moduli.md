# Projekti: Kuinka "bännätä" verkkosivu salt minionilta.
Projektin ideana on luoda palvelin, jolta on estetty pääsy tietylle verkkosivulle. Esimerkissä käytetään verkko sivuna youtubea, joka ei sinänsä oli haitallinen verkkosivu.
Esimerkkiä voi kuitenkin hyödyntää melkeimpä mihin vaan verkkosivuun. Vain squidille annettuja konfiguraatioita pitää muuttaa. 
Tämä tekeekin projektista hyödyllisen sillä joskus on hyödyllistä sensuuroida käyttäjiä. Erityisesti työympäristöissä tai lasten kanssa.

Laitteiden tekniset ominaisuudet:
Lenovo yogaslim 7
AMD Radeon
Windows 11
Projekti on tehty käyttäen Oracle VM Virtual Boxia.
Käytetyissä virtuaaliboxeissa käytetään linux ympäristöä, (Debian).

## Vaihe 1: Virtuaali koneitten luonti.
Tehtävä aloitetaa luomalla kaksi virtuaalikonetta. Tajusin että tässä vaiheessa on hyvä hyödyntää kurssin tehtävää 2 soittokotiin. Loin siis 2 virtuaalikonetta käyttäen vagranttia. Vagrant oli ladattu windows 11 koneelleni. 
Käytin ohjeena Teron kirjoittomaa ohjeta: Two Machine Virtual Network with debian 11 and bullseye.
Lähde: https://terokarvinen.com/2021/two-machine-virtual-network-with-debian-11-bullseye-and-vagrant/
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

## Vaihe 2: Salt master ja minion
T001 luotiin master ja T002 minionin. 
Kun oltiin varmistettu koneiden toimivan pystyin aloittamaan salt masterin ja minionin latauksen.
Latasimme aluksi salt masterin t001:


Onnistuneen latauksen jälkeen, Masterin ip osoite otettiin ylös komennolla.
```
hostname -I
```

Latasimme Salt minionin t002:


Onnistuneen latauksen jälkeen lisäsimme masterin Ip osoitteen minionin tietoisuuteen.
Tämän jälkeen uudelleen käynnistimme minionin komennolle:
```
systemctl restart salt-minion.service

```
Tämä ei onnistunut puuteellisten oikeuksien vuoksi:
Syy oli niinkin yksinkertainen kun että 
```
sudo
```
Puuttui.



## Vaihe 3: Squidin asennus
Asensin squidin masterilleni komennolla:

## Vaihe 4: Squidin konfigurointi
Squid konfiguroitiin niin että youtubeen ei voi luoda yhteyttä.
https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/networking_guide/configuring-a-domain-blacklist-in-squid
## Vaihe 5: Konfiguraatioiden siirtäminen salt minionille

## Testaus
