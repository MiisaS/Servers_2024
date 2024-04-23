# h4 Demoni
## x.) Lue ja tiivistä
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


 

