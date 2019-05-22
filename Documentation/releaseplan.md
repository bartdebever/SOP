# Release Plan

Door Bart de Bever

## Inleiding

Dit is het release plan van de FightCore applicatie.
FightCore is een blog post applicatie voor de populaire *Super Smash Bros.*
videogame serie.
FightCore werkt met eenback end geschreven in C# .NET Core en een Angular 7
frontend.

- [Release Plan](#release-plan)
  - [Inleiding](#inleiding)
  - [OTAP](#otap)
    - [O: Ontwikkel](#o-ontwikkel)
    - [T: Test](#t-test)
    - [A: Acceptatie](#a-acceptatie)
    - [P: Productie](#p-productie)
  - [Versiebeheer](#versiebeheer)
  - [Git](#git)
    - [Git Flow](#git-flow)
  - [Versie nummering](#versie-nummering)
  - [Deliverables](#deliverables)
  - [Release management](#release-management)
  - [Release process](#release-process)

## OTAP

OTAP (ook wel DTAP genoemd in het Engels) is een term gebruikt bij het beschrijven
van het releasen van verschillende versies van een software project.
De verschillende letters refereren naar de beschikbare omgevingen

### O: Ontwikkel

De ontwikkelomgeving is wat ontwikkelaars gebruiken om tijdens het ontwikkelen
naar de uitkomst van de code te kijken.
Dit wordt mestal lokaal gedaan waarna dat mogelijk is.
De software ontwikkelaar draait dan op zijn eigen machine een server met daarop
de nieuw geschreven code.

Bij dit project gaat dit op deze manier verlopen.
Een ontwikkelaar draait lokaal de `Kestrel` (of `IIS`) server voor de back end en
de `Angular` front end met gebruik van het `ng serve` commando.

De afhankelijkheden van het software project worden lokaal gedraaid.
Hierbij moet er gedacht worden aan bijvoorbeeld SQL Server, Elasticsearch, RabbitMQ, etc.
Er wordt aangeraden voor de ontwikkelaars om gebruik te maken van Docker waar mogelijk
om zo de werkomgeving schoon te houden.

### T: Test

Voor de testers wordt een aparte omgeving opgezet.
Een tester komt dus van buiten af op de applicatie en hoeft geen tooling te hebben
geïnstalleerd om de applicatie te besturen.
Het kan zo zijn dat de tester eerst een VPN verbinding moet leggen naar de server
voordat er toegang is naar de applicatie.

De test omgeving wordt zo ver mogelijk gelijk getrokken naar productie.
Hierbij kan het wel zo zijn dat er minder server resources beschikbaar zijn
dan op productie. Dit is voor de onderneming gewenst om de kosten van omgevingen
te verminderen.

### A: Acceptatie

De acceptatie omgeving wordt door de klant gebruikt om te testen of de versie van de
applicatie klaar is om doorgezet te worden naar productie.
Het is van belang dat deze omgeving gelijk staat aan de productie server.
De klant moet geen verschil merken tussen de implementatie zoals deze nu is
en wanneer de applicatie in productie wordt genomen.

Net zoals bij test, wordt de omgeving van buiten af benaderd en kan er een VPN
verbinding nodig zijn om naar de applicatie te komen.

### P: Productie

De productie omgeving is vanaf buiten beschikbaar en heeft de nieuwste geaccepteerde
versie van de applicatie draaiend. Voor de productie omgeving hoeft geen VPN verbinding
worden aangelegd.

Om de performance van de omgeving optimaal te maken wordt hier waar mogelijk de
code `geminimized`. Dit wordt vaak toegepast in frontend elementen zoals `JavaScript`
en `css` waarbij de code zo klein mogelijk wordt gemaakt.
Hierdoor is de bestandsgrote kleiner en kan de pagina sneller laden.
Bij de backend is dit niet een relevant concept.

## Versiebeheer

Binnen dit hoofdstuk wordt er uitgelegd hoe de versiebeheer van het project wordt
geregeld. Dit gaat over zowel de keuze van tooling naar de inrichting van deze tooling.

## Git

Voor versiebeheer wordt het populaire `Git` systeem gebruikt.
In het kort, Git slaat alleen de veranderingen tussen de laatste versie van de
software (de laatste `commit`) en de huidige situatie op.
Git doet dit op een efficiënte manier waardoor de bestandsgrote van een Git repository
klein blijft.

Er zijn verschillende website die een Git server voor opslag aanbieden.
`BitBucket`, `GitLab` en `GitHub` zijn voorbeelden van de meest bekende websites.
Er is binnen dit project gekozen om met `GitHub` te werken door de populariteit
van de website en de compatibiliteit met software van andere partijen.

### Git Flow

Een standaard implementatie om Git versiebeheer aan te pakken is via een Git Flow.
De meeste Git Flow implementaties gebruiken als basis 4 branches:

- Master
- Develop
- Feature
- Hotfix

Op de `master` staat de huidige productie code. Dit betekend dat wanneer er een
probleem in de productie is, deze hierop gebaseerd kan worden.

Een `develop` branch is voor een onderdeel van het project. Dit kan zowel de
opsplitsing zijn tussen front end en back end of tussen overkoepelende functies
van de applicatie.
Neem Spotify als voorbeeld. Er zou een `develop/player`
`develop/friends` en
`develop/browser` kunnen zijn. Er is ook één generale develop genaamt `dev` (
een branch mag niet dezelfde naam hebben als een map).
In deze branch worden de verschillende develop branches samen gebracht voor een
release.

Het idee van meerdere develop branches is dat er aan meerdere onderdelen tegelijk
gewerkt zonder dat deze nog in de `master` worden getrokken.

Een `feature` branch wordt gebruikt om deze overkoepelende functies verder
uit te bereiden. Als er terug gekeken wordt naar het Spotify voorbeeld,
de volgende  `features`  zouden onder de `develop/player` kunnen hangen.
`feature/play`, `feature/shuffle`, `feature/repeat`, `feature/volume`.

Als laatste is er de `hotfix`, deze branches worden gebruikt om kritieke
aanpassingen te doen aan de code. Wanneer er een probleem wordt gemeld waarbij
er een kritiek proces vast loopt kan een `hotfix` worden toegepast op de `master`
branch.

Om een archief te houden van oudere versies van de applicatie wordt er een
`archive` map aangemaakt onder de Git branches.
Wanneer er een nieuwe versie wordt uitgerold naar `master`, wordt er eerst de
huidige `master` code verplaatst naar `archive/VERSIENUMMER`.

## Versie nummering

Versies worden op via het `Semantic versioning` systeem gedaan.
Hierbij een quote over hoe dit systeem werkt van de officiële website.

> Given a version number MAJOR.MINOR.PATCH, increment the:
>
>1. MAJOR version when you make incompatible API changes
>2. MINOR version when you add functionality in a >backwards-compatible manner, and
>3. PATCH version when you make backwards-compatible bug fixes.
>
> Additional labels for pre-release and build metadata are
> available as extensions to the MAJOR.MINOR.PATCH format.

Hierbij wordt versie `1.0.0` als de eerste publieke release gezien.
Het is aan de ontwikkelaar die de verandering doorzet naar de `master`
om er voor te zorgen dat het versie nummer juist wordt opgehoogd.
Er wordt met Semantic versies gewerkt maar dit wordt niet hard
afgedwongen.

---
Opmerking: Het kan zo zijn dat er verouderde versies van de backend online blijven
staan. Dit komt door het backwards-compatible houden van de backend. Hierdoor
blijven de applicaties die andere ontwikkelaars hebben geschreven intact.

---

## Deliverables

Bij een release van het software product worden de volgende (bij) producten geleverd:

- De backend API met een eigen developer portal.
- De frontend apart gehost van de back-end.
- Eventuele documentatie over het product
- Zogeheten "patch notes" waarin genoteerd staat wat er is aangepast in deze versie.

De documentatie en patch notes worden als aankondiging geplaatst op de frontend.

## Release management

Binnen dit project wordt een rolling release cycle gebruikt.
Wanneer een **develop** branch af is wordt deze in de **master** gemerged.
Zodra dit succesvol is gedaan, wordt er zo snel mogelijk een nieuwe release gemaakt.

Deze release wordt aangekondigd in de vorm van een blog post en
verdere referentie naar die blog post binnen social media.
Bij grotere feature releases worden de stakeholders aangesproken
binnen de daarvoor bedoelde communicatie stromen.
De geschreven berichten worden voor de release geschreven door degene die de
merge uitvoert.
Dit bericht wordt in de pull request meegenomen en door de reviewer nagekeken.

## Release process

Een release wordt gedaan door een lid van het ontwikkel team met de
rol configuratie manager.
Voor de eerste release worden er in het `markdown` formaat instructies geschreven.
Deze instructies worden meegenomen in de code en volgen hetzelfde proces
als de source code zou doen.
Wanneer een configuratie manager een release moet gaan doen zou hij/zij
alleen deze instructies hoeven te volgen.

Binnen deze instructies staat het bouwen van de frontend, backend en eventuele dependencies.
Er wordt ook aangegeven hoe de oude software versie verplaatst moet worden naar het archief.