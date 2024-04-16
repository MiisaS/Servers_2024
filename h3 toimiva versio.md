# Toimiva versio
## Chagon and Straub 2014
## Book
Pro git kirjasta löydät laajat ohjeet git:in käyttöön kirja sisältää osiot:
- Getting started, joka kertoo perus ohjeita Git:stä kuten uuden luomisen ja tietoja, jota aloittelija voi tarvita
- Git Basics, Tässä osiossa kerrotaan enemmän repositoryistä.
- Git branching, Ohjeet brancheista ja miten niitä käytetään sekä hallitaan.
- Git on the Servers, Sisältää tiedot git:in käyttämisestä servereillä. Sisältää tidot mm. Ssh, deamon ja http.
- Distributed Git
- Github, ohjeet githubin käyttöön kuten setuppiin ja projekteiden luomiseen/hallintaan.
- Git tools
- Customizing git, kuinka saat gitistä enemmän omiin tarpeisiisi vastaavan.
- Git Internals, kuten transfer protocol ja data recovery
- A1, Osio Git in Other: krtoo enemmän ympäristöistä kuten visualstudion käyttämisestä Gitin kanssa ja erillaisista kielistä esim. powershell ja bash.
- A2, Applications: Command-line, lidgit2, JGit, go-git ja Dulwich
- A3, Git commands, kertoo laajasti hyödylliset Git commands.

- Lähde: https://git-scm.com/book/en/v2
## Getting Started - What is Git

Tiivistetty tieto "pläjäys" Gitin käyttäjälle.
Sisältää osiot: 
- What is Git?
- Snapchots
- Nearly Every Operations is Local
- Git has Integrity
- Git Generally Only Adds Data
- The three states: midfied, staged ja committed

## add, push and pull

- git add, komennolla saat uudet tai muutetut tiedostot lisättyä Gittiin. Tiedot tallennetaan comennolla git commit.
Lähde: https://git-scm.com/docs/git-add
- git push, komennolla saat päivitettyä ja lähetettyä tiedot valittuun reporsityyn. Kyseinen komento päivittää etärepositoryn vastaamaan paikallista repositoryäsi.
Lähde: https://git-scm.com/docs/git-push
- git pull, kyseinen komento noutaa (Fetch) etänä tekemäsi muutokset ja yhdistää ne tämän hetkiseen branch:iin. Jos nykyinen Branch on erillään, kommenoilla --rebase ja -- no rebase, saa 
 sen sovitettua yhteen.
Lähde: https://git-scm.com/docs/git-pullt

## Varaston terokarvinen/suolax/ historia
Viimesimpinä muutoksina on viime viikolla tehdyt 
Koska koneeni antoi virhekommennon:
```
'less' is not recognized as an internal or external command,
operable program or batch file.
```
Etsin commit välilehden verkosta: 

![Sieppaateroncommits](https://github.com/MiisaS/Servers_2024/assets/122888617/0fd52bf7-6993-4475-9276-3ec9385cfddb)

Viimeisimmät muutokset oli esimerkiksi viime tunnilla tekemämme lataukset. 

## a.) Online, Uuden varaston luominen.
Aluksi luotiin uusi varasto GitHubiin. 

![Sieppaakesätulee](https://github.com/MiisaS/Servers_2024/assets/122888617/4f52f612-ca19-4d10-81ac-560d672c8b6a)

## b.) Dolly, Etämuutos 
Siirsin varaston koneelleni:
```
git clone https://github.com/MiisaS/Teht-v-3

```
ja siirryin kyseiseen repositeryyn:

```
cd Teht-v-3
```
![Sieppaacloonaust3](https://github.com/MiisaS/Servers_2024/assets/122888617/bb4f9e89-4a14-4141-92b5-0061a9565ece)

Muutokseksi lisäsin tekstiä README Fileen. 

```
notepad README.md
```
![Sieppaatestimuokkausnotereadme](https://github.com/MiisaS/Servers_2024/assets/122888617/d8e3a95b-66a5-4a26-9bd9-59abb49e86ed)

Tallensin tiedoston ja siirsin muutokset.
```
git add README.md
git commit -m "Muutos readme file"
git push origin

```

Push komennon jälkeen, pyydettiin minulta authenticationia jonka suoritin Webissä.

![Sieppaaonnistunutsiirto](https://github.com/MiisaS/Servers_2024/assets/122888617/377f66ef-192f-4ead-b8d9-46df2b04ffa9)

Muutokset siirtyivät onnistuneesti. 
![Sieppaasiirtyivätgittiin](https://github.com/MiisaS/Servers_2024/assets/122888617/817f197e-4384-459a-b785-46da25b2cc16)

## c.) Doh! Tyhmä muutos
Muutokseksi loin tekstitiedoston ja muokkasin sitä:
```
echo. > tyhma.txt
notepad tyhma.txt

```
Tämän jälkeen katsottiin status ja huomattiin että tiedosto on untrackted. 
Muutokset peruttiin komennolla:
```
git reset --hard
```
![Sieppaaresetointijamuutostyhma](https://github.com/MiisaS/Servers_2024/assets/122888617/c07dba82-7c49-4b84-bc63-0a32dacd7b6b)

## Tukki, Logit ja omien tietojen tarkastus

Aloitetaan avaamalla logi. 

```
git log
```
Näin nähdään tehdyt muutokset.
![Sieppaalogit](https://github.com/MiisaS/Servers_2024/assets/122888617/357012dc-b43a-414d-a5ad-6160ffd4b859)

Seuraavaksi tarkistetaan nimi ja sähköposti. 
```
git config user.name
git config user.email
```
![Sieppaakäyttäjäjasähköp](https://github.com/MiisaS/Servers_2024/assets/122888617/904d0174-38d9-4db3-b6f0-596bda67ed7f)

Minun ei tarvinnut tehdä muutoksia tietoihini mutta halutessa muutokset tietoihin voi tehdä komennolla:

```
git config --global user.name "päivitettynimi"
```
Myös sähköposti muutetaan samalla komennolla korvataan vain user.name, user.email ja hakasulkuihin kirjoitetaan uusi sähköpostisi.

## Suolattu rakki, Salt
Tätä en oikein tajunnut tai saanut toimimaan. En löytänyt salt hakemistoani.

Lähteet: 
https://terokarvinen.com/2022/palvelinten-hallinta-2022p2/
https://git-scm.com/book/en/v2
https://git-scm.com/docs/git-add
https://git-scm.com/docs/git-push
https://git-scm.com/docs/git-pullt

