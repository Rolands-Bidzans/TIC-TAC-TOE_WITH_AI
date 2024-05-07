# Nightwatch laboratorijas darbs

Autors: Haralds Bikše

Studenta numurs: 211RDB120

## Izmantotie rīki un npm paketes

- Node.js: v20.5.1
- NightWatch.js: v3.6.1
- Chromedriver: v124.0.6367.91
- Geckodriver: v0.34.0

## Projekta uzstādīšana

Lai uzstādītu projektu priekš palaišanas, sākumā jāparliecinas, ka terminālī ir atlasīta 
projekta direktorija. 

Talāk jāizpilda sekojošā kommanda termināli:
```shell
$ npm install
```

Šī komande uzstādīs visas projekta vajadzīgās paketes, kas ir norādītas 'package.json' failā.

Tālāk ir svarīgi atvērt `tests/github.js` failu ar kādu teksta reduktoru. Un 39. kā arī 40. rindiņā
pie mainīgajiem `validEmail` un `validPassword` ierakstīt derīgu GitHub lietotāju, lai nodrošinātu, ka testi
tiek izpildīti veiksmīgi.

Vēl šajā failā 3. rindā pie `repoName` var nomainīt testu ietvaros izveidojamās repozitorijas nosaukumu, ja ir velme
citādi to var atstāt ar noklusējamo nosaukumu.

## NightWatch config fails

Failā izveidotā konfigurācija tiek izmantota priekš 'NightWatch.js' testiem.
Noklusējumā ir definēts, ka 'NightWatch.js' izmanto 4444 portu priekš webdrivera.
Kā arī noklusējamais webdrivers ir iestatīts 'Chromedriver', bet ir arī iespēja izmantot
'Geckodriver', ja ir velme.

Toties 'Geckodriver' konfigurēju, lai tas izmanto 4445 portu priekš komunikācijas,
lai būtu iespēja veikt paralēlus testus.

Tādad ja laiž testa scenārijus nenorādot kuru webdriveri vēlas izmantot, tiks automātiksi palaists
'Chromedriver'. Ja vēlas norādīt kommandrindā kuru webdriveri izmantot tad ir pieejamas šādas opcijas:
- ``default`` - 'Chromedriver'
- ``firefox`` - 'Geckodriver'

## Pieejamie testu scenāriji

Visi testi un to scenāriji atrodas `tests` mapē:
- ``tests`` mapes saturs
    - `github.js` - Fails, kas satur visiem scenārījiem vajadzīgu informāciju un datus.
    - `github_incorrect_login.js` - Satur testa scenāriju pieslēgšanai sistēmā ar nepareizu paroli.
    - `github_repo_create_delete.js` - Scenāriju repozitorijas izveidošanai, dzēšanai un tās pārbaudei.
    - `github_topics.js` - Scenārījs, kas pārbauda vai 'Arduino' topic apraksta kopējo rakstzīmju skaits nepāsniedz 500.
    - `github_valid_login.js` - Scenārījs pieslēgšanai sistēmai ar korektu ēpasu un paroli.

Failos pieejamie scenāriji:
- `github_incorrect_login.js`
    - `testIncorrectLogin` - Veic pieslēgšanos GitHub ar nederīgu ēpastu un paroli.
- `github_repo_create_delete.js`
    - `testCreateRepo` - Izveido jaunu repozitoriju.
    - `testDeleteRepo` - Izdzēš izveidoto repozitoriju.
    - `testCheckDeletedRepo` - Pārbauda vai izdzēstā repozitorija vairs nepastāv sākumlapās.
- `github_topics.js`
    - `testGithubTopics` - Atver GitHub sākumlapu un no tās aiziet līdz topic lapai un atver 'Arduino' topic lapu,
un pārbauda vai apraksta rakstzīmju garmums nepārsniedz 500 rakstzīmes.
- `github_valid_login.js`
    - `testValidLogin` - Veic pieslēgšanos GitHub ar derīgu lietotāju.

## Testu palaišana

Šajā nodaļā ir parādīts, kā palaist testus un kādos veidos to var izdarīt.

### Viena konkrēta testa palaišana:

Lai palaistu vienu konkrētu testu ir jāizpilda sekojošā kommanda projekta direktorijā:

```shell
$ npm test -- -t tests\github_topics.js --testcase "testGithubTopics"
```
- ``tests\github_topics.js`` - Fails kurā atrodas izpildāmais tests
- ``--testcase "testGithubTopics"`` - Arguments ar kuru tiek norādīts kuru scenāriju no faila izpildīt.

### Visu testu palaišana:

Lai palaistu visus izveidotos testu scenārījus jāizpilda sekojošā kommanda projekta direktorijā:

```shell
$ npm test -- -t tests
```
- ``tests`` - Mape, kas satur izpildāmos testu skriptus.

### Testu palaišana konkrētā pārlūkā:

Testiem var specificēt izmantojamo pārlūku ar sekojošo kommandu:

```shell
$ npm test -- -t tests\github_incorrect_login.js --testcase "testIncorrectLogin" -e firefox
```

- ``tests\github_incorrect_login.js`` - Fails kurā atrodas izpildāmais tests
- ``--testcase "testIncorrectLogin"`` - Arguments ar kuru tiek norādīts kuru scenāriju no faila izpildīt.
- ``-e firefox`` - Arguments, kas norāda kādu pārluku/webdriver izmantot norādītajams scenārijam.

Pieejamo pārluku opcijas var apskatīt šajā nodaļā: [NightWatch config fails](#nightwatch-config-fails)

Toties, ja ``firefox``, jeb 'Geckodriver' opcija nestrādā, tad var nākties ieslēgt 'Geckodriver' manuāli no
cita termināļa, lai panāktu tā izmantošanu. [Manas problēmas ar 'Geckodriver'](#manas-problēmas-ar-geckodriver)

### Testu palaišana paralēli:

Lai palaistu divus vai vairākus testus paralēli to var izdarīt ar šo kommandu:

```shell
$ npm test -- -t tests\github_topics.js --testcase "testGithubTopics" -e firefox,default
```

- ``tests\github_topics.js`` - Fails kurā atrodas izpildāmais tests
- ``--testcase "testGithubTopics"`` - Arguments ar kuru tiek norādīts kuru scenāriju no faila izpildīt.
- ``-e firefox,default`` - Arguments, kas norāda kādus pārluku/webdriver izmantot norādītajams scenārijam 
(Norādot vēl papildus pārlukus var palielināt izpildāmo paralēlo testu skaitu).

Tātad ja gribētu izpildīt 4 paralēlus testus ar 'Chromedirver' to varētu izdarīt ar šo kommandu:

```shell
$ npm test -- -t tests\github_topics.js --testcase "testGithubTopics" -e default,default,default,default
```

# Manas problēmas ar 'Geckodriver'

Kā iepriekšs minēts ar firefox driveri šajā projektā man radās problēmas. Neesmu ticis skaidrībā,
kādu iemeslu pēc tas negribēja slēgties iegšā ar izveidoto 'NightWatch.js' konfigurāciju.

Tāpēc nācās atrasts alternatīvu veidu, kā panākt 'Geckodriver' izmantošanu. To man izdevās panākt atverot
paildus termināli un inicializējot 'Geckodriver' instanci manuāli norādot tam, lai izmanto 4445 portu.

Lai šo dabūtu gatavu no projekta direktorijas terminālī atvēru direktoriju kurā atrodas 'Geckodriver' izpildāmais
kommand rindas fails.

```shell
$ cd ./node_modules
$ cd ./.bin
```

Tiklīdz atrados ``/node_modules/.bin`` direktorijā izpildiju sekojošo kommandu, lai palaistu 'Geckodriver' (Priekš windows):

```shell
$ .\geckodriver.cmd --port 4445
```

Un pēc tā palaišanas varēju atgriezties pie sava primārā termināļa un izpildīt testēšanas kommandu,
kas paredzēja 'Geckodriver' izmantošanu.

Vienīgā problēma ar šo pieeju ir tāda, ka veico paralēlas testa izpildes var izmantot tikai vienu
firefox instances testus.
