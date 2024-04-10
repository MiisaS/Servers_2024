## H2 Soittokotiin
Palautettiin ajoissa keskeneräisenä ongelmien vuoksi. Ongelmat ratkesivat ja versiota on päivitetty. 
# x.)
# Two machine virtual network with Debian 11 Bullseye and vagrant

## Step 1: Luo ja käynnista kaksi virtuaalikonetta. Asenna vagrant käyttäen komentoja:
Sudo apt-get update
sudo apt-get install vagrant virtualbox

## Step 2: Vegrant file 
Luo uusi directory ja tallenna Vagrantfile
mkdir twohost/; cd twohost/
nano Vagrantfile
Sisällöksi tiedostoon:
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
## Step 3: 2 Hostia
ssh login: vagrant ssh t001 (takas pääsee exit)
Kummatkin koneet voivat keskustella keskenään(ping):
vagrant ssh t001
vagrant@t001$ ping -c 1 192.168.88.102
vagrant@t001$ ping -c 1 8.8.8.8 # Google nameserver
vagrant@t001$ exit

vagrant ssh t002
vagrant@t002$ ping -c 1 192.168.88.101
vagrant@t002$ exit

## Step 4: Destroy
Voit helposti tuhota luomasi virtuaalikoneet: 
vagrant destroy # all files on both virtual machines are destroyed
Uus aukeaa: vagrant up

Troubleshooting: Jos ei toimi
t001.vm.network "private_network", ip: "192.168.60.101"
 ...
t002.vm.network "private_network", ip: "192.168.60.102"

Lähde: https://terokarvinen.com/2021/two-machine-virtual-network-with-debian-11-bullseye-and-vagrant/
# Salt quickstart Salt stack master and slave on ubuntu Linux

Saltin avulla voi hallita tuhansia koneita. Tästä löydät ohjeet saltin lataamiseen. Slaves nimellä kutsutaan kontrolloitavissa olevia koneita.

## Step 1: Install Master and slave
Master:
master$ sudo apt-get update
master$ sudo apt-get -y install salt-master
master$ hostname -I
10.0.0.88
Slave:
slave$ sudo apt-get update
slave$ sudo apt-get -y install salt-minion

Orjan on tietävä herransa:
slave$ sudoedit /etc/salt/minion
master: 10.0.0.88
id: esimerkki

Jotta uudet asetukset astuvat voimaan täytyy uudelleen käynnistää slave.
slave$ sudo systemctl restart salt-minion.service

## Accept slave key to master
master$ sudo salt-key -A
Unaccepted Keys:
tero
Proceed? [n/Y]
Key for minion esimerkki accepted.

## Try it out
master$ sudo salt '*' cmd.run 'whoami'
Esimerkki:
 root
Tämän jälkeen sinun pitäisi saada vastaus Slave:lta

## Mitä seuraavaksi
Kokeile esimerkiksi näitä komentoja:
master$ sudo salt '*' cmd.run 'hostname -I'
master$ sudo salt '*' grains.items|less
master$ sudo salt '*' grains.items
master$ sudo salt '*' grains.item virtual
master$ sudo salt '*' pkg.install httpie
master$ sudo salt '*' sys.doc|less

Lähde https://terokarvinen.com/2018/salt-quickstart-salt-stack-master-and-slave-on-ubuntu-linux/?fromSearch=salt%20quickstart%20salt%20stack%20master%20and%20slave%20on%20ubuntu%20linux

# Hello Salt Infra-as-code
Kaikki alkaa hello worldillä.
## Lataa salt
sudo apt-get update
sudo apt-get -y install salt-minion
micro editor:
sudo apt-get -y install micro
export EDITOR=micro

## Luo kansio Hello
sudo mkdir -p /srv/salt/hello/
cd /srv/salt/hello/

Komennon /srv/salt/ kansio tulee jaetuksi kaikille orjille. 

## Write infra as code
Varmista että olet samassa kansiossa kuin hello:
sudoedit init.sls
Tiedosto aukeaa micro editorissa. Tässä esimerkki idempotent Saltin kiellellä: /tmp/helloesimerkki:
  file.managed
## Run
sudo salt-call --local state.apply hello

Toivottusti saisit output jossa tietoa mitä juuri teit. 
Jotta voidaan varmistaa että Salt teki toivomamme asiat:
ls /tmp/esimerkki
/tmp/esimerkki

## Varmistus komento 
```
sudo salt-call --local state.apply hello
...
Succeeded: 1
sudo salt-call --local state.apply hello
...
Succeeded: 1
```

## Tärkeät perus funktiot
pkg
file
service
user
cmd (only if the four others can't be used)

# a.) Kahden virtuaalikoneen luonti samaan verkkoon. 
## Huom! Ongelmat alkavat jo tässä ensimmäisesä vaiheessa, joten loput puuttuvat lisään ne ongelmien ratkettua. (Omat googlailut ja tietotaitoni eivät riittäneet. :( ) Ongelma ratkaistu 9.4.2024 klo.21.18. Ongelman ratkaisussa ja tehtävän alkuosiossa kesti yhteensä n. 12h.  
Aloitin käynnistämällä virtuaalikoneeni ja luomalla tiedoston.
![Sieppaatwohostfile](https://github.com/MiisaS/Servers_2024/assets/122888617/75ac1b77-5c8d-4a57-9eba-a3fdac77240f)

Tiedoston sisältö:
![Sieppaatiedosto](https://github.com/MiisaS/Servers_2024/assets/122888617/8805b9d7-7aae-4816-afbf-079aa54bfa1c)
Komennot eivät toimineet:
![Sieppaaongelmat](https://github.com/MiisaS/Servers_2024/assets/122888617/b1b12ae6-4bb3-487e-9ec2-125650bd389b)
ja tiedosto näytti mielestäni vain tekstiltä, joten muistelin aikasemmin tekemissäni scripteissä käyttäneeni .sh tiedostomuotoa. Kokeilin siis sitä

Tämän jälkeen tiedoston sisältö näytti enemmän oikealta, mutta kohtasin uuden ongelman:
![Sieppaaongelma2](https://github.com/MiisaS/Servers_2024/assets/122888617/26e29f69-6d6d-445f-8e31-37faf5aada9a)

Kokeilin uudestaan käyttäen Bash:
![Sieppaabashillatoimi](https://github.com/MiisaS/Servers_2024/assets/122888617/81c451c8-c412-486f-b856-3410ea8a74f8)

```
Vagrantfile.sh: line 6: =: command not found
Vagrantfile.sh: line 14: syntax error near unexpected token `"2"'
Vagrantfile.sh: line 14: `Vagrant.configure("2") do |config|'
```

Sain tälläiset lähdin katsomaan missä vika. Huomasin, että tiedoston ei tarvitsekkaan olla .sh mutta sain silti samat virheet.
Kokeilin tehdä muutamia muutoksia tiedostoon un huomasin esimerkiksi puuttuneet hipsut, sekä poistin $ merkin kokeilun omaiseksi eikä virhe ilmoitukset poistuneet. 
Tässä tiedostoni sisältö:

![Sieppaamuutettu script](https://github.com/MiisaS/Servers_2024/assets/122888617/b082e7b0-adac-48cb-a005-ca9104802e38)
Huomasin yhden oppilaista muokanneen notepad tiedostoa ja tajusin sen ehkä olevan oikea keino kun käytän windowsia. Kokeilin uudestaan ja suljin Vagrantin, päivitin Vagrantfile:n notepadissa. Tämän jälkeen käynnistin uudelleen Vagrant up. 
Katsoin käynnissä olevat: Vagrant status komennolla.


... Päivitän versiota ongelman ratketessa.

Kokeilin vielä verkosta löytämääni ohjetta joka on hyvin saman kaltainen kylläkin kuin Teron. 
Linkki käyttämääni ohjeeseen: https://medium.com/@srghimire061/how-to-create-multiple-virtual-machines-using-vagrant-tool-d4b074d5bdcc 

Tässä mitä muokkasin notepadille: 

![Sieppaatestiversio2](https://github.com/MiisaS/Servers_2024/assets/122888617/c3a9e333-092f-46ef-a4fa-659806cdd9d1)

Ja sitten kokeilin vagrant up ja katsoin vielä vagrant statuksesta onko mitään tapahtunut eikä ollut.

Kokeilin vielä kerran luoda kaiken alusta luoda kahemistot ja file:n. Enkä saanut toimimaan.
Jotain on kuitenkin tehty oikein sillä kun laittoi: Vagrant up tuli tulostukseksi tämä: 

![Sieppaaviimeinenkokeilu](https://github.com/MiisaS/Servers_2024/assets/122888617/8af7e98c-299d-4785-a77c-5c0cda3e2937)

Ongelman ratkaisuksi selvisi tiedoston päällekkäisyys saman kaltainen kuin tässä tilanteessa:
https://stackoverflow.com/questions/33294269/unable-to-create-new-virtual-machine-unsing-vagrant
Loin koneet:

![Sieppaakaksikonetta](https://github.com/MiisaS/Servers_2024/assets/122888617/63ee2c6c-4e6c-4edd-8527-a4589f72a917)

Kokeilin yhteydet:

![Sieppaapingt001](https://github.com/MiisaS/Servers_2024/assets/122888617/d532b807-64bf-4912-8055-332253ce66c9)
![Sieppaaping002](https://github.com/MiisaS/Servers_2024/assets/122888617/ed4d3543-eedd-4dc8-86c7-94236465c943)

## B.) Salt Master-Slave, Kulunut aika n. 15min
Tein t001 masterin:
![Sieppaamasteriksi](https://github.com/MiisaS/Servers_2024/assets/122888617/c4c220b2-9520-436c-ba88-9230c8fc91cb)
Tein t002 minionin:
![Sieppaasaltminion](https://github.com/MiisaS/Servers_2024/assets/122888617/97a7ca2c-a90a-41ae-b736-d2aa58b04e72)
Muokkasin tiedoston, jotta minion tunnistaisi herransa.
![Sieppaaminionilleherra](https://github.com/MiisaS/Servers_2024/assets/122888617/d9fa84f8-6f8e-43cd-92f4-c39418a38c07)
Avainta ei aluksi löytynyt, Sain ilmoituksen:
```
$ sudo salt-key -A
$ The key glob '*' does not match any unaccepted keys.
```
Tarkistin masterin IP osoitteen komennolla, joka olisi kannattanut tehdä jo ennen, koska virheeni oli laittamassani ip-osoitteessa.
```
hostname -I
```
![Sieppaavarmistukset](https://github.com/MiisaS/Servers_2024/assets/122888617/fc026cb0-c7cb-4dd0-b8df-d86195320c79)

## C.) Shell commands with slave, Kulunut aika n. 10min
Käytin komentoa
```
sudo salt 'miisa' cmd.run 'ls -l'

```

![Sieppaahakemistollashellkomento](https://github.com/MiisaS/Servers_2024/assets/122888617/ab246c9c-0c88-48fd-9ef9-72751d4d4038)

## D.) Idempontti, Kulunut aika n. 30min
Kokeilin eka idemponttia jossa luon saman tiedoston kaksi kertaa.
![Sieppaatestitiedosto](https://github.com/MiisaS/Servers_2024/assets/122888617/11f0ab77-6ba6-44eb-80e1-dbc527c4c4e1)
Sitten kokeilin ladata httpie kaksi kertaa
```
sudo salt '*' pkg.install httpie

```

![Sieppaatodistetaanettäeiuutta](https://github.com/MiisaS/Servers_2024/assets/122888617/ba1a3008-a251-4344-a7a5-8ae2cae1e23d)

## E Grains.Item, Kulunut aika n. 10min
Käytin eka komentoa jolla saadaan kaikki minionin tekniset tiedot
```
sudo salt 'miisa' grains.items
```
Tämän jälkeen poimin muutamat tiedot tällä kertaa os ja ipv4, ipv6.

![Sieppaajyviä](https://github.com/MiisaS/Servers_2024/assets/122888617/368a4bd5-af8e-4da1-a7e3-4b4f07123816)

## f.) Hello IaC 
Aloitin tehtävän asentamalla micro-editorin. 
```
sudo apt-get -y install micro
export EDITOR=micro
```
Loin kansion ja tiedoston moi

```
$ sudo mkdir -p /srv/salt/moi/
$ cd /srv/salt/moi/
```
Kun olin siirtynyt oikeaan kansioon suoritin komennon:

```
sudoedit init.sls
```
Kirjoitin idempontin tiedostoon, en osannut aluksi sulkea editoria mutta se tapahtuikin Ctrl+Q (Ctrl+s) tallentaa tiedoston.
Tämä ei kuitenkaan toiminut kun yritin ajaa komennon.
```
sudo salt-call --local state.apply moi
```
![Sieppaaeitääoikeetoiminu](https://github.com/MiisaS/Servers_2024/assets/122888617/bec1bdcf-93c1-4d6b-954d-9b5129e7ed7c)



Lähteet: 

https://terokarvinen.com/2021/two-machine-virtual-network-with-debian-11-bullseye-and-vagrant/

https://terokarvinen.com/2018/salt-quickstart-salt-stack-master-and-slave-on-ubuntu-linux/?
fromSearch=salt%20quickstart%20salt%20stack%20master%20and%20slave%20on%20ubuntu%20linux

https://terokarvinen.com/2024/hello-salt-infra-as-code/

https://www.cyberciti.biz/faq/run-execute-sh-shell-script/

https://medium.com/@srghimire061/how-to-create-multiple-virtual-machines-using-vagrant-tool-d4b074d5bdcc

https://github.com/bhd471/Palvelinten-hallinta/blob/main/h2-Soitto-kotiin.md



