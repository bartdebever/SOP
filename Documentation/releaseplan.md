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