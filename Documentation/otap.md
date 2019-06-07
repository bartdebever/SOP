# OTAP

Binnen dit document wordt de OTAP opstelling uitgelegd met daarbij
de verantwoording van de benodigde tooling.

## Automatische uitrol

Om het testen van de applicatie binnen elke stap van het process makkelijker
te maken worden `Productie en Acceptatie` automatisch uitgerold.

### Pushen naar Docker Hub

Hierbij wordt er gebruik gemaakt van een `private Docker image`.
Er wordt hier met `tags` gewerkt om zo onderschijt te kunnen maken in wat een
update heeft gekregen.
Eén update die met de acceptatie tag wordt gepushed moet niet op de productie
server komen.

Travis, waarin de buildstraat is verwerkt, ondersteund het maken en pushen van
Docker images met tags. Dit betekend dat er geen externe tooling gebruikt hoeft
te worden om de docker images te maken en op te zetten.

```yml
TRAVIS CODE HIER
```

Voor de veiligheid van het systeem en omdat het project open source is, zijn
de authenticatie gegevens binnen Travis encrypted opgeslagen.
Dit zorgt ervoor dat er niemand bij kan aangezien Travis alleen de waardes decrypt
voor een build.

### Automatisch van Docker Hub afhalen

WATCHTOWER
