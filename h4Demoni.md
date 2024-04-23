# h4 Demoni
# x.) Lue ja tiivistä
## Salt vagrant
- Infra as Code - Your wishes as a text file

```
$ sudo mkdir -p /srv/salt/hello
$ sudoedit /srv/salt/hello/init.sls
$ cat /srv/salt/hello/init.sls
/tmp/infra-as-code:
  file.managed

$ sudo salt '*' state.apply hello
```
- top.sls - What Slave Runs What states
  Määrittää mitkä states julkaistaa millekkin orjalle (slave)

```
$ sudo salt '*' state.apply hello^C
$ sudoedit /srv/salt/top.sls
$ cat /srv/salt/top.sls
base:
  '*':
    - hello
```
Lähde: https://terokarvinen.com/2023/salt-vagrant/#infra-as-code---your-wishes-as-a-text-file
## Salt overview
Rules of yaml, YAML on yksin kertaistettu keino renderoida monia tiedostoja Salt:issa. 
Yksinkertaiset käyttöohjeet:
- Data on jaettu(structure) key:value pairs.
- Mappings käyttää : ja välilyöntiä.
- Avaimet voivat olla monessa eri rakenteissa.
- Kaikki avaimet ja properties on case-sensitive.
- Tabulaattori ei toimi, käytetään vain space.
- Merkki # aloittaa kommentin.
YAML Simple structure, kolme eri
- Scalars-key, arvo on numero, merkkijono tai boolean arvo.
- Lists- a key, perässä on luettelo arvoista, joista jokainen on omalla rivillään ja alkaa kahdella välilyönnillä ja viivalla.
- Dictionaries - Kokoelma avaimista, niiden mappings and lists.

Lähde: https://docs.saltproject.io/salt/user-guide/en/latest/topics/overview.html#rules-of-yaml
## pkg-file-service, SSH serveri portin vaihtaminen
- Luo SSH state
```
$ cat /srv/salt/sshd.sls
openssh-server:
 pkg.installed
/etc/ssh/sshd_config:
 file.managed:
   - source: salt://sshd_config
sshd:
 service.running:
   - watch:
     - file: /etc/ssh/sshd_config
```
Tällä portti vaihtuu 8888.

- Apply the states to slaves, muutokset käyttöön

```
$ sudo salt '*' state.apply sshd
```
Muista testata!

Lähde: https://terokarvinen.com/2018/04/03/pkg-file-service-control-daemons-with-salt-change-ssh-server-port/?fromSearch=karvinen%20salt%20ssh

## Saltin ohjeet tilafukstioille
```
$ sudo salt-call --local sys.state_doc pkg # johdanto + silmäile pkg.installed, pkg.purged, pkgs
```

- pkg.installed, Paketin asennus
- pkg.purged, varmistaa paketin poiston ja kutsuu komennon pkg.purged tarvittaessa.
- pkgs, asennettavat paketit listassa
```
$ sudo salt-call --local sys.state_doc file # johdanto + silmäile file.managed, file.absent, file.symlink
```

- file.managed, hallitsee tiedostoa (esimerkiksi salt master välillä)
- file.absent, poistaa tiedoston ja varmistaa että sitä ei ole
- file.symlink, luo symbolisen linkin

```
$ sudo salt-call --local sys.state_doc service # johdanto + silmäile service.running, service.dead, enable

```
- service.running, varmistaa että service on päällä
- service.dead, service pois päältä
- service.enabled, käynnistää servicen käynnistyksen yhteydessä.
## Huom! Nämä tehtävät on tehty kokeilun omaisesti windowsilla
## a.) Hello SLS!
Aloitettiin luomalla hakemisto hello ja muokattiin se notepadissä.

![Sieppaakuvavaiheesta1hello](https://github.com/MiisaS/Servers_2024/assets/122888617/4a786953-b468-4cea-b33c-f83e099de282)

Notepadiin kirjoitettiin:

 ![Sieppaakuvanotepadistä](https://github.com/MiisaS/Servers_2024/assets/122888617/812a9f80-df4a-4027-a62a-c15b0131f190)

## b.) Top
Tässä kohtaa luomme top.sls hakemiston, jossa määritämme haluamme tilat ja suoritamme ne.
Loimme tiedoston haluamaamme hakemistoon ja muokkasimme sen sisältöä notepadillä:
```
notepad C:\srv\salt\top.sls

```
Notepadiin kirjoitimme: 
![Sieppaakutsujenluku](https://github.com/MiisaS/Servers_2024/assets/122888617/49659106-1fd9-4743-82d4-5e1c43f7c389)

Tämän jälkeen suoritimme komennon:
```
salt-call --local state.apply
```
![SieppaatopkomentostatesapliledH4](https://github.com/MiisaS/Servers_2024/assets/122888617/d99518a2-131c-4ed5-93c3-524e9186c36c)

Tämä ei ihan toiminut veikkaan Saltissani tai komennossa olleen jotain häikkää. 

## c. Apache easy mode
(Tässä kohtaa on hyödynnetty Chatgpt 4, jotta saatiin suoritettua windowsilla)
Tässä kohtaa asennamme Apachen, korvaamme testisivun ja käynnistämme sen.
```
notepad C:\srv\salt\apache.sls
```
Kirjoitin notepadiin sisällön:
![SieppaaH4notepadApache](https://github.com/MiisaS/Servers_2024/assets/122888617/def1149a-8cfe-4f76-9054-d4dafd53a890)

Tieodsto tallennetiin ja suoritetaan komennolla:
```
salt-call --local state.apply apache
```
Webselaimeen kirjoitetaan localhost. Oletuksena tehtyjen muutoksien jälkeen apachen testisivu on nyt korvattu. Itsellä en saanut yhteyttä localhost sivulle.

## d. SSHouto, uuden portin luominen SSHd kuuluviin.



