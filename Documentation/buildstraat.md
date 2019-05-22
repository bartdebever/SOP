# De ontwikkelstraat

Binnen dit document wordt de ontwikkelstraat uitgelegd en enige keuzes verantwoord.

## Gekozen technologieën

Er is gekozen om met `Travis CI` en `SonarCloud` te werken.
Beide deze tools zijn open source en gratis voor een open source project.
Het zijn moderne tools en hebben integratie met GitHub.

Voordat er met deze technieken is gewerkt was er geen ervaring hiermee.

## Ontwikkelstraat opzet

Binnen deze ontwikkelstraat worden 2 onderdelen verwerkt, de frontend en backend.
Er komen dus ook 2 verschillende runners:

- Node runner voor de Angular Frontend met TSLint.
- C# (.NET Core 2.2) runner met SonarCloud.

### Basis

Binnen Travis kunnen er in meerdere niveaus gewerkt worden.
Omdat er een stel instellingen zijn die zowel voor Angular als C# gelden worden
deze in de basis instellingen opgenomen.

```yml
sudo: required
git:
  depth: false
```

`sudo` is hier nodig omdat er packages op de server worden geïnstalleerd,
dit is bijvoorbeeld voor .NET Core 2.2 en NPM te bemachtigen.
`Git depth` voor SonarCloud om zo specifieke bugs met deze integratie te voorkomen.

### Angular

Binnen het Angular project zijn er 3 doelen opgesteld:

- Build het project.
- Voer de testen uit.
- Bekijk de code kwaliteit met TSLint.

De applicatie wordt op beide node 8, 10 en de laatste stabiele versie gebuild.
Het volgende code block is de gebruikt `.yml` configuratie voor Travis.

```yml
    - language: node_js
      node_js:
        - "8"
        - "10"
        - "node"
        # "node" grabs the latest stable release
      before_script:
        - cd Frontend
        - npm install -g @angular/cli
        - npm install
      script:
        - ng build --prod
```

De Angular CLI wordt eerst geïnstalleerd om enige oneenigheden in de
dependencies te voorkomen.
Hierna wordt met `npm install` de rest van de dependencies geïnstalleerd.

`ng build --prod` zorgt dat het project wordt gebuild en hierbij worden beide
de tests en TSLint nagekeken.
Als één van deze faalt, faalt de build.

### ASP.NET Core

Voor het ASP.NET Core project zijn er 3 doelen:

- Build het project.
- Voer de testen uit.
- Bekijk de code kwaliteit met SonarCloud.

Hierbij wordt de applicatie getest op .NET Core 2.2.
Dit is een recente versie en hier werkt het ontwikkelteam ook op.
De volgende `yml` zijn de gebruikte instellingen voor Travis:

```yml
    - language: csharp
      mono: none
      dotnet: 2.2
      dist: xenial

      install:
        - cd Backend
        - dotnet tool install --global dotnet-sonarscanner
        - dotnet restore

      before_script:
      # Script to expose the dotnet CLI tools.
        - export PATH="$PATH:$HOME/.dotnet/tools"
      script:
      # Run restore to restore the NuGet packages
        - dotnet restore
        # SonarCloud Stuff
        # Begin the scanner with the keys and props for SonarCloud.
        - dotnet sonarscanner begin /key:"FightCore_FightCore" /name:"FightCore" /version:"1" /d:sonar.login="$SONAR_TOKEN" /d:sonar.host.url=https://sonarcloud.io /o:fightcore
        - dotnet build
        - dotnet sonarscanner end /d:sonar.login="$SONAR_TOKEN"
        # SonarCloud Stuff ends, run tests.
        - dotnet test
      global:
      # Some global settings to make the experience better
        - DOTNET_SKIP_FIRST_TIME_EXPERIENCE=true
        - DOTNET_CLI_TELEMETRY_OPTOUT=1
      cache:
        directories:
        # Cache the NuGet and SonarCloud folders.
          - "$HOME/.nuget/packages"
          - "$HOME/.local/share/NuGet/Cache"
          - "$HOME/.sonar/cache"
```

`Xenial` wordt als distro gebruikt omdat er met de standaard distro een bug is
waardoor .NET Core 2.2 niet geïnstalleerd kan worden.
Dit maakt binnen de ontwikkelstraat niet uit aangezien hier niks op released gaat worden.

Voor veiligheid wordt de `$SONAR_TOKEN` variabelen binnen Travis gebruikt.
Deze wordt veilig opgeslagen zodat niet legitieme gebruikers een invalide scans kunnen uitvoeren.

Verder worden er cache mappen aangegeven zodat wanneer iemand meerdere builds
acther elkaar uitvoert, deze builds snel verlopen.

Binnen deze chain aan commandos worden de het project gebuild en de code kwaliteit
geanalyseerd en de testen uitgevoerd.

## Email en notificatie

Binnen de opdracht van Fontys staat aangegeven dat er emails worden verstuurd
naar de relevante personen per SonarCloud probleem.
Deze functionaliteit zit al in zowel Travis als SonarCloud gebouwd.

Een gebruiker kan bij SonarCloud aangeven of hij/zij een email wil krijgen
wanneer een nieuw probleem aan hem/haar is toegekend.
SonarCloud kent tevens automatisch problemen aan degene die de fout heeft gemaakt
volgens de commit history.

Travis CI geeft een email aan het team wanneer een build is gefaald en wanneer deze weer werkt.