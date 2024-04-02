# Ensimmäinen osa x: Tiivistelmät 

## Osa 1: Run salt locally
-Lataa sudo apt-get update
sudo apt-get -y install salt-minion
-pkg.installed pitäisi olla, kokeile komennolla: sudo salt-call --local -l info state.single pkg.installed tree
- Tiedostojen hallinta, koska linuks kaikki on text file esimerkki komennot sudo salt-call --local -l info state.single file.managed /tmp/hellotero
sudo salt-call --local -l info state.single file.managed /tmp/x contents="foo"
sudo salt-call --local -l info state.single file.absent /tmp/x
- Mistä näet onko daemon running: käytä service.running/dead apache2 enable=true/false
- Käyttäjä aktiivisuus user.present/absent x
- Running command sudo salt-call --local -l info state.single cmd.run 'touch /tmp/foo' creates="/tmp/foo"
  Ohjeet löytyy: sudo salt-call --local sys.state_doc
  Lähde: https://terokarvinen.com/2021/salt-run-command-locally/

## Osa 2: Sivun luonti Github
- Luo tai kirjaudu github tilillesi. 
- Luo uusi repository ja siihen readme, public, description, license(free esim. GNU)
- Create new file add file kohdasta.
  Kirjoita ja nimea tiedosto loppuun .md jotta saadaan tiedosto markdowniksi.
Ohjeet kuinka Markdown toimii ja lähde löytyy osoitteesta: https://terokarvinen.com/2023/create-a-web-page-using-github/

## Osa 3: Raportin kirjoittaminen
- Raportti kirjoitetaa pääosin itseään varten jotta löytää itselleen selkeät ohjeet asioiden ymmärtämiseen tai kertaamiseen. Myös muut voivat hyötyä raportistasi.
- Jotta raporttisi olisivat ihan kelvollisia on muistettava muutama seikka, jotka kyseisessä tekstissä on listattu.
- Toistettavuus: tarveksi selkeä ohjeiksi ja luettavaksi uudelleen.
- Täsmällinen: Pidä tarkasti kirjaa vaiheista ja myös yksityiskohdista kuten kellonajoista, käytetyistä tiedostoista, sovellusversioista, työkaluista
- Ota myös virheet ja ongelma tilanteet ylös, kenties joku törmää samaan virheeseen ja hyötyy sinun ratkaisustasi.
- Helppo lukuisuus, hyvä rakenne ja kielioppi.
- Lähteet! Vakiotekstit Tero ja GNU lisenssi.
- Älä tee näin: kopioi ja liitä kuvia joihin sinulla ei ole käyttöoikeuksia, kerro satuja, plagioi.


# Toinen osa: virtuaalikoneisiin liittyvät tehtävät.
a. Hello Windows Salt world! Saltin asennus salt ladattiin osoitteesta https://docs.saltproject.io/salt/install-guide/en/latest/topics/downloads.html#windows
Latauksen jälkeen varmistettiin että Salt on onnistuneesti koneella. 
kuva 1

b. Hello vagrant! Vagrantin kanssa kohtasin vaikeuksia. Aluksi en ollut varma enkä oikein käsittänyt kuinka vagrantin avulla luodaan vartuaalikone. 
Käytin apunani lähdettä https://www.educative.io/courses/guide-to-vagrant-virtual-machines/building-your-first-vm-with-vagrant
kuva 2. Latasin vagrantin Windows koneelleni.
c. Loin sille working directoryn, Initialize vagrant boxin, laitoin virtuaali koneen päälle komennolla vagrant up, 
Annoin koneen hetken mietiskellä jonka jälkeen käytin vagrant ssh komentoa ja sain virtuaali koneen auki. HUomiona haluan mainita avanneeni vahingossa ubuntu version (huomasin jälkeen päin teron ohjeista komennon "$ vagrant init debian/bullseye64 $ vagrant up $ vagrant ssh").
Katso kuvat vaiheesta wikistä(vagrant 1 ja 2).

a. Saltin asennus virtuaali koneeseeni ei sujunut ongelmitta. Johtuen: E: Unable to locate package salt-minion. Mystisesti ongelma ratkaistui kun en tehnyt mitään muutoksia vaan kokeilin uudestaan.
b ja c.) Seuraavaksi kokeillaan salt funktiot: 
1. pkg ulostulo: 
local:
----------
          ID: tree
    Function: pkg.installed
      Result: True
     Comment: The following packages were installed/updated: tree
     Started: 12:01:29.671577
    Duration: 6491.493 ms
     Changes:
              ----------
              tree:
                  ----------
                  new:
                      1.7.0-3
                  old:

Summary for local
------------
Succeeded: 1 (changed=1)
Failed:    0
------------
Total states run:     1

Tällä toiminnolla asennettiin paketti(package: tree)

2. file managed: 
salt-call --local -l info state.single file.managed/tmp/hellotero
[INFO    ] Determining pillar cache

Passed invalid arguments: single() takes at least 2 arguments (1 given).

Usage:

    Execute a single state function with the named kwargs, returns False if
    insufficient data is sent to the command

    By default, the values of the kwargs will be parsed as YAML. So, you can
    specify lists values, or lists of single entry key-value maps, as you
    would in a YAML salt file. Alternatively, JSON format of keyword values
    is also supported.

    CLI Example:

    .. code-block:: bash

        salt '*' state.single pkg.installed name=vim
3. Deamon running: 
salt-call --local -l info state.single service.running apache2 enable=true
[INFO    ] Determining pillar cache
[INFO    ] Determining pillar cache
[INFO    ] Loading fresh modules for state activity
[INFO    ] Running state [apache2] at time 12:08:46.235503
[INFO    ] Executing state service.running for apache2
[INFO    ] Executing command ['systemctl', 'status', 'apache2.service', '-n', '0'] in directory '/home/vagrant'
[ERROR   ] The named service apache2 is not available
[INFO    ] Completed state [apache2] at time 12:08:46.250287
local:
----------
          ID: apache2
    Function: service.running
      Result: False
     Comment: The named service apache2 is not available
     Started: 12:08:46.235503
    Duration: 14.784 ms
     Changes:

Summary for local
------------
Succeeded: 0
Failed:    1
------------
Total states run:     1

Tämä ei siis onnistunu kokeilen vielä uudestaan, mutta pohdintana enable=true laittaa päälle enable=false sulkee.

4. Käyttäjä
 salt-call --local -l info state.single user.present käyttäjä
[INFO    ] Determining pillar cache
[INFO    ] Determining pillar cache
[INFO    ] Loading fresh modules for state activity
[ERROR   ] An un-handled exception was caught by salt's global exception handler:
UnicodeEncodeError: 'ascii' codec can't encode character u'\xe4' in position 1: ordinal not in range(128)
Traceback (most recent call last):
  File "/usr/bin/salt-call", line 11, in <module>
    salt_call()
  File "/usr/lib/python2.7/dist-packages/salt/scripts.py", line 335, in salt_call
    client.run()
  File "/usr/lib/python2.7/dist-packages/salt/cli/call.py", line 53, in run
    caller.run()
  File "/usr/lib/python2.7/dist-packages/salt/cli/caller.py", line 133, in run
    ret = self.call()
  File "/usr/lib/python2.7/dist-packages/salt/cli/caller.py", line 196, in call
    ret['return'] = func(*args, **kwargs)
  File "/usr/lib/python2.7/dist-packages/salt/modules/state.py", line 1169, in single
    st_.call(kwargs)}
  File "/usr/lib/python2.7/dist-packages/salt/state.py", line 1540, in call
    log.info('Running state [{0}] at time {1}'.format(low['name'], start_time.time().isoformat()))
UnicodeEncodeError: 'ascii' codec can't encode character u'\xe4' in position 1: ordinal not in range(128)
Traceback (most recent call last):
  File "/usr/bin/salt-call", line 11, in <module>
    salt_call()
  File "/usr/lib/python2.7/dist-packages/salt/scripts.py", line 335, in salt_call
    client.run()
  File "/usr/lib/python2.7/dist-packages/salt/cli/call.py", line 53, in run
    caller.run()
  File "/usr/lib/python2.7/dist-packages/salt/cli/caller.py", line 133, in run
    ret = self.call()
  File "/usr/lib/python2.7/dist-packages/salt/cli/caller.py", line 196, in call
    ret['return'] = func(*args, **kwargs)
  File "/usr/lib/python2.7/dist-packages/salt/modules/state.py", line 1169, in single
    st_.call(kwargs)}
  File "/usr/lib/python2.7/dist-packages/salt/state.py", line 1540, in call
    log.info('Running state [{0}] at time {1}'.format(low['name'], start_time.time().isoformat()))
UnicodeEncodeError: 'ascii' codec can't encode character u'\xe4' in position 1: ordinal not in range(128

Mielestäni minun pitäis "luoda" käyttäjä kyseiselle virtuaalikoneelle, en nimittäin tehnyt slelaista missään vaiheessa.

5. cmd

   sudo salt-call --local -l info state.single cmd.run 'touch /tmp/foo' creates="/tmp/foo"
[INFO    ] Determining pillar cache
[INFO    ] Determining pillar cache
[INFO    ] Loading fresh modules for state activity
[INFO    ] Running state [touch /tmp/foo] at time 12:15:45.312125
[INFO    ] Executing state cmd.run for touch /tmp/foo
[INFO    ] Executing command 'touch /tmp/foo' in directory '/home/vagrant'
[INFO    ] {'pid': 4637, 'retcode': 0, 'stderr': '', 'stdout': ''}
[INFO    ] Completed state [touch /tmp/foo] at time 12:15:45.322527
local:
----------
          ID: touch /tmp/foo
    Function: cmd.run
      Result: True
     Comment: Command "touch /tmp/foo" run
     Started: 12:15:45.312125
    Duration: 10.402 ms
     Changes:
              ----------
              pid:
                  4637
              retcode:
                  0
              stderr:
              stdout:

Summary for local
------------
Succeeded: 1 (changed=1)
Failed:    0
------------
Total states run:     1

d. Salt grains komento output: 
 sudo salt-call --local grains.items osfinger virtual
[INFO    ] Determining pillar cache

Passed invalid arguments: items() takes at most 1 argument (2 given).

Usage:

    Return all of the minion's grains

    CLI Example:

    .. code-block:: bash

        salt '*' grains.items

    Sanitized CLI Example:

    .. code-block:: bash

        salt '*' grains.items sanitize=True
En osannut aluksi käyttää komentoa mutta mielenkiinotista oli huomata julkaisupäivä joka oli 2006, ipv4:  ja ipv6 tiedot    
ipv4:
        - 10.0.2.15
        - 127.0.0.1
ipv6:
        - ::1
        - fe80::be:82ff:fe6b:cc1d
Koen että nämä ovat hyödyllisiä tietoja löytää ja tietää.  






  
