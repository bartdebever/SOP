# OTAP

Binnen dit document wordt de OTAP opstelling uitgelegd met daarbij
de verantwoording van de benodigde tooling.

## Automatische uitrol

Om het testen van de applicatie binnen elke stap van het process makkelijker
te maken worden `Productie` en `Acceptatie` automatisch uitgerold.

De `Test` en `Ontwikkel` omgevingen worden voor nu nog lokaal gedaan.

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
deploy:
  provider: script
  script: bash docker_push
  on:
    branch: master
```

Om de daadwerkelijke Docker push uit te voeren wordt een bash script gebruikt.

```bash
#!/bin/bash
echo "$DOCKER_PASSWORD" | docker login -u "$DOCKER_USERNAME" --password-stdin
docker push fightcore/fightcore
```

Voor de acceptatie branch hoeft alleen het bash script worden aangepast
zodat deze met de `acceptance` tag werkt.

```bash
#!/bin/bash
echo "$DOCKER_PASSWORD" | docker login -u "$DOCKER_USERNAME" --password-stdin
docker push fightcore/fightcore:acceptance
```

[Source](https://docs.travis-ci.com/user/docker/)

Voor de veiligheid van het systeem en omdat het project open source is, zijn
de authenticatie gegevens binnen Travis encrypted opgeslagen.
Dit zorgt ervoor dat er niemand bij kan aangezien Travis alleen de waardes decrypt
voor een build.

### Automatisch van Docker Hub afhalen

[Watchtower](https://github.com/containrrr/watchtower)
zorgt ervoor dat de Docker containers automatisch worden geüpdate
wanneer er een nieuwe versie uit is.
Wanneer de Watchtower Docker Container wordt opgestart, worden alle
aanwezige containers automatisch geüpdate.

## Verantwoording

Watchtower en Docker containers worden gebruikt om de buildstraat simpel te houden.
Door deze toolset toe te passen hoeft er geen SSH of FTP connectie gelegd te
worden met de acceptatie of productie server.
Een buildstraat loopt snel uit de hand wanneer er features worden toegevoegd.
Met deze opstelling kunnen er servers worden toegevoegd en automatisch de updates
krijgen zonder moeilijke configuratie.
