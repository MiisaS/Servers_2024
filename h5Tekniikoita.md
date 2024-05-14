# x.) Lue ja tiivistä (tehtävä on vielä kesken salt ongelmien vuoksi)
Luin lähteenä Rikun vuonna 2021 muokkaamaa Github sivua.
https://github.com/rikurikurikuriku/Palvelinten-hallinta/wiki/H5-Aikajana#b-windows-pakettivarasto-ja-vs

Tehtävä aloitettiin käyttämällä linux debiania johon asennettiin sls-tiedosto käsin.
Tämän jälkeen. Hyödynnettiin Teron materiaaleja ja ladattiin saltminion windowsille, josta se päivitettiin Git:in kautta masteriksi.
Jotta tekstin muokkaaminen olisi helpompaa ladattiin visualstudiocode.

Aikajana näytettiin komennolla:
```
cd /etc/; sudo find -printf '%T+ %p\n'|sort|tail
```
Aikajanaan tehtiin portin avaus minioneille. Tämä suoritettiin UFW:n kautta.
Riku loi kaksi SLS-tiedostoa 5050on ja 5050off
Hän ajoi 5050on ja sai onnistuneesti portin avattua.

# Tehtävät
## a.) Salt asennus
Saltin avauswindowsille. Olin itse jo ladannu Saltin koneelleni, joten pääsin heti näyttämään onnistuneen asennuksen.
Asennus näytettiin komennolla: 
```
salt-call --local
```
![Sieppaasaltcallocal](https://github.com/MiisaS/Servers_2024/assets/122888617/eaf6a96a-f4ba-48bb-b2f5-ee928f4fb21d)

## Tiedon keruu
Seuraavaksi keräsimme tietoa grains.item-toiminnolla.
Käytin komentoa: salt-call --local grains.items, joka on toiminut aikaisemmin. Veikkaan että vika on oikeuksissani. Komento ei löydä polkua.
![Sieppaasalteitoimi](https://github.com/MiisaS/Servers_2024/assets/122888617/b5358642-4009-4625-9b23-e1f5fc569f47)

Päädyinkin laittamaan Salt ympäristöni uudestaan pystyyn. Katsoin ohjeita sivustolta:
https://docs.saltproject.io/salt/install-guide/en/latest/topics/start-salt-services.html#start-salt-services
ja Teron ohjeita:
https://terokarvinen.com/2018/salt-quickstart-salt-stack-master-and-slave-on-ubuntu-linux/?fromSearch=salt%20quickstart%20salt%20stack%20master%20and%20slave%20on%20ubuntu%20linux
Tähän mennessä ongelman ratkaisuun on kulut n. 2h ja koin tämän helpoimmaksi keinoksi. Kertaus on myös opintojen äiti:)

Salt minion ja master on luotu ja salt-minion on paused tilassa.

![Sieppaalupaavaltanäyttää](https://github.com/MiisaS/Servers_2024/assets/122888617/b0f6bf77-7dd8-45ef-83f8-5601734ac173)

En saanut käynnistettyä start minionia.
![Sieppaaeimeepäälle](https://github.com/MiisaS/Servers_2024/assets/122888617/0c1c7237-df17-43fa-b766-3b99dae14ba4)


## Ongelman päivitys:
Ongelmaa ei ole saatu ratkottua. Salt on kokeiltu ladata uudestaan ja windows näyttää ladatun salt minionin. Jostain syystä haluttu Programfile ei löydy/aukea. Vaikka käytettäysiin administratoria.
![Uusiyritys](https://github.com/MiisaS/Servers_2024/assets/122888617/029080b3-754c-459c-8eff-9300068d7812)

Tässä kuva ilmoituksesta:
![SieppaaEitääsalttoimi](https://github.com/MiisaS/Servers_2024/assets/122888617/58426e7b-9bee-4501-be46-8fc8e916a351)

Ongelmanratkaisuun on hyödynnetty saltin verkkosivuja ja Teron materiaaleja.

Ongelman ratkaisu löytyi. Koska kumpikaan testaamani kone ei löytäny saltin conf tiedostoja. Yritin manuaalisesti etsiä niitä koneen tiedostoista koska tiesin niiden kuuluvan programdata kansioon. Kansio on windowsilla piilotettu, ja asetuksista pystyin yksin kertaisesti laittamaan myös piilotetut kansiot näkyviin. Piilotetuista kansioista löytyi ProgramData ja etsimäni Saltin configuraatio filet. Sain avattua tiedostolle käyttöoikeudet, koska jostain syystä minulla ei niitä ennestään ollut. 

Lähteet: 
https://docs.saltproject.io/salt/install-guide/en/latest/topics/install-by-operating-system/windows.html#install-windows

https://terokarvinen.com/2024/configuration-management-2024-spring/

https://terokarvinen.com/2018/control-windows-with-salt/

