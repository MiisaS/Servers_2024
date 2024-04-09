## H2 Soittokotiin
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
sudo salt-call --local state.apply hello
...
Succeeded: 1
sudo salt-call --local state.apply hello
...
Succeeded: 1

## Tärkeät perus funktiot
pkg
file
service
user
cmd (only if the four others can't be used)



