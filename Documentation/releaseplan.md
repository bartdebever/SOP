# Release Plan

Door Bart de Bever

## Inleiding

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
Een ontwikkelaar draait lokaal de `Kestrel` (of `IIS`) server voor de backend en
de `Angular` frontend met gebruik van het `ng serve` commando.

De afhankelijkheden van het software project worden lokaal gedraaid.
Hierbij moet er gedacht worden aan bijvoorbeeld SQL Server, Elasticsearch, RabbitMQ, etc.
Er wordt aangeraden voor de ontwikkelaars om gebruik te maken van Docker waar mogelijk
om zo de werkomgeving schoon te houden.

### T: Test

Voor de testers wordt een aparte server omgeving opgezet.
Een tester komt dus van buiten af op de applicatie en hoeft geen tooling te hebben
geïnstalleerd om de applicatie te besturen.
Het kan zo zijn dat de tester eerst een VPN verbinding moet leggen naar de server
voordat er toegang is naar de applicatie.

De test omgeving wordt zo ver mogelijk gelijk getrokken naar productie.
Hierbij kan het wel zo zijn dat er minder server resources beschikbaar zijn
dan op productie. Dit is voor de onderneming gewenst om de kosten van omgevingen
te verminderen.

### A: Acceptatie

De acceptatie server wordt door de klant gebruikt om te testen of de versie van de
applicatie klaar is om doorgezet te worden naar productie.
Het is van belang dat deze server gelijk staat aan de productie server.
De klant moet geen verschil merken tussen de implementatie zoals deze nu is
en wanneer de applicatie in productie wordt genomen.

Net zoals bij test, wordt de server van buiten af benaderd en kan er een VPN
verbinding nodig zijn om naar de applicatie te komen.

### P: Productie

De productie server is vanaf buiten beschikbaar en heeft de nieuwste geaccepteerde
versie van de applicatie draaiend. Voor de productie server hoeft geen VPN verbinding
worden aangelegd.

Om de performance van de omgeving optimaal te maken wordt hier waar mogelijk de
code `geminimized`. Dit wordt vaak toegepast in front end elementen zoals `JavaScript`
en `css` waarbij de code zo klein mogelijk wordt gemaakt.
Hierdoor is de bestandsgrote kleiner en kan de pagina sneller laden.
Bij de back end is dit niet een relevant concept.

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

Op de **master** staat de huidige productie code. Dit betekend dat wanneer er een
probleem in de productie is, deze hierop gebaseerd kan worden.

Een **develop** branch is voor een onderdeel van het project. Dit kan zowel de
opsplitsing zijn tussen front end en back end of tussen overkoepelende functies
van de applicatie.
Neem Spotify als voorbeeld. Er zou een `develop/player`, `develop/friends` en
`develop/browser` kunnen zijn. Er is ook één generale develop genaamt `dev` (
een branch mag niet dezelfde naam hebben als een map).
In deze branch worden de verschillende develop branches samen gebracht voor een
release.

Het idee van meerdere develop branches is dat er aan meerdere onderdelen tegelijk
gewerkt zonder dat deze nog in de `master` worden getrokken.

Een **feature** branch wordt gebruikt om deze overkoepelende functies verder
uit te bereiden. Als er terug gekeken wordt naar het Spotify voorbeeld,
de volgende  *features* zouden onder de `develop/player` kunnen hangen.
`feature/play`, `feature/shuffle`, `feature/repeat`, `feature/volume`.

Als laatste is er de **hotfix**, deze branches worden gebruikt om kritieke
aanpassingen te doen aan de code. Wanneer er een probleem wordt gemeld waarbij
er een kritiek proces vast loopt kan een *hotfix* worden toegepast op de `master`
branch.

Om een archief te houden van oudere versies van de applicatie wordt er een
`archive` map aangemaakt onder de Git branches.
Wanneer er een nieuwe versie wordt uitgerold naar `master`, wordt er eerst de
huidige `master` code verplaatst naar `archive/VERSIENUMMER`.