# H6 Benchmark 
## (tehtävän loppu osiot tehdään loppuun hieman myöhemmin. Johtuen liian myöhään ratkenneesta salt ongelmasta. Aika tulee vastaan:( )

## Lue ja tiivistä
Windows package manager:
Salttia voi hyödyntää windowsin kanssa pakettien hallinnassa. Tämä työkalu muistuttaa linuxin yum ja apt.
Package definition files:
YAML/JINJA2 file ja sls. Tämän rakenteiset sisältävät kaikki tiedot jota salt tarvitsee lataakseen.
Paketteja voi myös poistaa salttia hyödyntäen. 
Komennot joita käytetään on 
```
pkg.install
pkg.installed
```
Jotta salt "löytää" tarvittavat sls. Kloonataan tarvittava reporsity.
```
salt-run winrepo.update_git_repos
```
Päivitetään minionin tiedot:
```
# From the master
salt -G 'os:windows' pkg.refresh_db

# From the minion in masterless mode
salt-call --local pkg.refresh_db
```
Tämän jälkeen saat tiedot pakettien kokonaismäärästä ja kuinka moni latauntui ja kuinka moni epäonnistui. 

Lataukset suoritetaan komennoilla:

```
From the master
salt * pkg.install 'firefox_x64'

From the minion in masterless mode
salt-call --local pkg.install "firefox_x64"
```
Kaikki konfiguraatiot suoritetaan: winrepo_dir_ng

Ladatut tiedostot voidaan poistaa käyttäen:
pkg.remove

Lähde: 
https://docs.saltproject.io/en/latest/topics/windows/windows-package-manager.html

# Paketin lataus windowsiin.
Hyödynsin tässä tehtävässä ongelmantultua Nicklaksen palautusta, koska hän osasi asian ja selitti hyvin tekemänsä vaiheet.
Linkki Nicklaksen Gittiin: https://github.com/NicklasHH/Palvelinten-hallinta/blob/master/h6%20Benchmark/h6%20Benchmark.md

Tässä tehtävässä päästiin kokeilemaan pkg.installed toimintoa. En saanut Salt:tia toimimaan edes h5 tehtävissä, joten nämä ongelmat viivästivät tämänkin tehtävän tekoa ja syy miksi kaikkea en ehtinyt tekemään. Ongelma oli ollut kansioiden oikeuksissa ja kansio oli piilossa (hidden file). Tästä löysin kuitenkin hirveän vähän ongelman ratkasu apua(H5 tehtävässä selitetty enemmän ongelmasta ja sen ratkaisusta).
Aloitimme varmistamalla että salt on ja toimii.
Käytin masterina luomaani debian virtual boxia ja minionina toimi koneeni oma windows.

Aluksi en saanut ladattua paketteja. Tajusin kuitenkin että unohdin aloittaa tehtävän komennolla:
```
salt -G 'os:windows' pkg.refresh_db #toimin minionin kautta joten oikea komento oli
salt-call --local pkg.refresh_db
```
koska luomani ympäristö oli kokonaan uusi ja uudelle koneelle, joten git puuttui.
<img width="661" alt="Näyttökuva 2024-05-05 215059saltjuttua" src="https://github.com/MiisaS/Servers_2024/assets/122888617/47b29447-baa6-4200-8147-8f8949a8c248">
<img width="380" alt="Näyttökuva 2024-05-06 200524gitladattusalt" src="https://github.com/MiisaS/Servers_2024/assets/122888617/bf28021c-9949-4bc1-a106-a7987ce1e7ed">

Gitin latauksen jälken suoritimme komennon pkg.install_db uudestaan. 
Jiihaa se lähti toimimaan.
Kokeilin myös pkg.refreshiä.
![Näyttökuva 2024-05-06 200456](https://github.com/MiisaS/Servers_2024/assets/122888617/4da23fef-39ec-4cc8-9479-e9cb8d2c3117)

Mietin mieleeni tulleen hyödyllisen paketin ja ne olivat git sekä visualstudiocode ja apache2, jostain syystä kaikkia paketteja ei löydy vaikka pkg.refresh_db löytää tarvittavat paketit.
<img width="404" alt="Näyttökuva 2024-05-06 200651ekavääräsitkokeilinsitonnistu (002)" src="https://github.com/MiisaS/Servers_2024/assets/122888617/09034f7b-8036-48e2-afb8-066dff04e1a4">

Tässä vielä muista yrityksistä. Yritin myös ladata winrar ja sen lopputulos erosikin muista.
<img width="366" alt="Näyttökuva 2024-05-06 211239latausyrityksiä,winrarnäyttääeriltä" src="https://github.com/MiisaS/Servers_2024/assets/122888617/2763a546-3107-4a21-a686-268168cb8140">

## Benchmark
Löysin minua kiinnostavaksi 
