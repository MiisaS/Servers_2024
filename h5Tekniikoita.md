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
Käytin komentoa: salt-call --local grains.items, joka on toiminut aikaisemmin. Veikkaan että vika on oikeuksissani.
