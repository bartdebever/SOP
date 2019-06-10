# Docker Opzet

- [Docker Opzet](#docker-opzet)
  - [Frontend docker file](#frontend-docker-file)
  - [Backend docker file](#backend-docker-file)
  - [Configuratie](#configuratie)
  - [Documentatie](#documentatie)

## Frontend docker file

Voor de frontend wordt er Angular gebruikt.
Dit draait binnen het Node.js ecosysteem.
Hierdoor is er gekozen om Node.js 10 te installeren.
Versie 10 is de laatste `Long Term Support` versie van Node.js

```dockerfile
# Create image based on the official Node.js 10 LTS image from dockerhub
FROM node:10

# Create a directory where our app will be placed
RUN mkdir -p /app

# Set this new directory as the working directory.
WORKDIR /app

# Copy dependency file to install it separately
COPY package*.json /app/
RUN npm install

# Copy the app itself.
COPY . /app/

# Expose the port npm will run this application on.
EXPOSE 4200

# Serve the app
CMD ["npm", "start"]
```

## Backend docker file

Om te beginnen moet er een dockerfile gemaakt worden om de
output klaar te zetten.
Microsoft heeft een
[officiële tutorial](https://docs.docker.com/compose/aspnet-mssql-compose/)
voor het maken van
een image met een SQL server en ASP Net Core.

```dockerfile
# Get the latest LTS of the ASP Net Core image.
FROM microsoft/aspnetcore-build:lts

# Copy all files into the working directory.
COPY . /app

# Set the working directory.
WORKDIR /app

# Run the restore and build commands
RUN ["dotnet", "restore"]
RUN ["dotnet", "build"]

# Document which port should be exposed.
EXPOSE 80/tcp

# Run the commands to start the app.
RUN chmod +x ./entrypoint.sh
CMD /bin/bash ./entrypoint.sh
```

Nu moet er een entrypoint script gemaakt worden zodat
er op een makkelijke manier ervoor gezorgd kan worden dat de output juist wordt uitgevoerd.

```bash
#!/bin/bash

set -e
run_cmd="dotnet run --server.urls http://*:80"

until dotnet ef database update; do
>&2 echo "SQL Server is starting up"
sleep 1
done

>&2 echo "SQL Server is up - executing command"
exec $run_cmd
```

Dit zorgt ervoor dat de database naar de juiste migration wordt geüpdate en de API wordt uitgevoerd.

## Configuratie

Omdat de API niet alleen voor intern gebruik is moet de API en
de Frontend apart bereikbaar zijn.
Dit zorgt ervoor dat de docker instatie met `localhost` naar de
frontend en backend.

Dit maakt de configuratie dat er alleen één API container en
één frontend container hoeven te worden opgestart.

De SQL server is niet inbegrepen bij deze configuratie.
Test servers kunnen worden opgezet met een in memory database
omdat deze geen persistentie nodig hebben.
Acceptatie en Productie krijgen een eigen dedicated database per server.
Hoe de structuur van deze database connectie in elkaar zit is
nog niet duidelijk.

## Documentatie

De backend krijgt automatisch documentatie door het gebruik van Swagger UI.
Hierbij worden de bestaande endpoints en modellen gedocumenteerd.
Dit zou er voor moeten zorgen dat de externe ontwikkelaars een
sandbox hebben waar ze in rond kunnen spelen met de API.

De frontend wordt apart gedocumenteerd met van de backend.
Hierbij kunnen programma's zoals `compodoc` gebruikt worden.
Dit wordt niet in de configuratie opgenomen omdat deze documentatie
minder tot geen waarde heeft voor de normale gebruiker of
externe developer.