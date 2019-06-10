# Statische Code Kwaliteit

Binnen dit bestand wordt er uitleg gegeven over het nut van statische
code kwaliteit tools en waarom dit zinvol is in applicaties.

## Implementatie

Binnen het document over de ontwikkelstraat is er beschreven hoe de
ontwikkelstraat met `SonarCloud` is geïntegreerd.
Dit wordt hier niet nogmaals herhaald en er worden geen verdere
referenties gemaakt naar dit document.
Voor de implementatie van SonarCloud in het project zie
[het ontwikkelstraat document](https://github.com/bartdebever/SOP/blob/master/Documentation/buildstraat.md).

## Het nut van statische code kwaliteit

Code kwaliteit is een punt wat bij elk software project besproken moet worden.
Verschillende ontwikkelaars willen verschillende standaarde aanhouden en geven
andere waarde aan statische code kwaliteit.

In de volgende hoofdstukken worden de volgende 4 metrieken besproken:

- Cyclomatische complexiteit
- Block depth
- Techical debt
- Fan-in/fan-out

### Cyclomatische complexiteit

Cyclomatische complexiteit gaat over hoe complex een methode mag zijn.
Hierbij wordt er gekeken naar hoeveel logica er binnen één methode wordt uitgevoerd
en of er geen gebruik gemaakt kan worden van bijvoorbeeld een `early return`.

```c#
void FunctionOne()
{
    if (conditionOne)
    {
        FunctionTwo();
    }
    else
    {
        FunctionThree();
    }

    if (conditionTwo)
    {
        FunctionFour();
    }
    else
    {
        FunctionFive();
    }
}
```

Binnen deze methode wordt logica uitgevoerd en het is lastig om te zeggen wat er
precies gedaan wordt wanneer.
Binnen dit geval zou de eerste en tweede if statement aparte methodes kunnen worden.

```cs
void FunctionOneOne()
{
    if (conditionOne)
    {
        FunctionTwo();
        return;
    }

    FunctionThree();
}

void FunctionOneTwo()
{
    if (conditionTwo)
    {
        FunctionFour();
        return;
    }

    FunctionFive();
}
```

Een andere ontwikkelaar heeft nu een beter overzicht van welke logica er
daadwerkelijk wordt uitgevoerd.
Ook maakt het debuggen makkelijker door een else-statement weg te halen.

### Block depth

Bij block depth gaat het om die diepte van de code blokken.
Wanneer er meerdere checks worden gedaan, zetten beginnende ontwikkelaars vaak
deze if statements in elkaar.

```cs
Item GetItemById(int id)
{
    if (id > 0)
    {
        try
        {
            Item item = _itemService.get(id);
            if (item == null)
            {
                throw new EntityNotFoundException();
            }
            else
            {
                return null;
            }
        }
        catch (DatabaseException exception)
        {
            if (exception.type == ExceptionType.INVALIDTABLE)
            {
                return null;
            }
            else if (exception.type == ExceptionType.WRONGID)
            {
                throw new EntityNotFoundException();
            }
            else
            {
                return null;
            }
        }
    }
    else
    {
        return null;
    }
}
```

Deze methode is lang en heeft nesting van 3 items.
If statement > Try/Else > If statement.
Door het gebruik van `early returns` kan hier een kortere methode van worden gemaakt.

```cs
Item GetItemById(int id)
{
    if (id > 0)
    {
        return null;
    }

    Item item
    try
    {
        item = _itemService.get(id);
    }
    catch (DatabaseException exception)
    {
        return HandleDatabaseException(exception);
    }

    if (item == null)
    {
        throw new EntityNotFoundException();
    }

    return item;
}

HandleDatabaseException(DatabaseException exception)
{
    if (exception.type == ExceptionType.INVALIDTABLE)
    {
        return null;
    }

    if (exception.type == ExceptionType.WRONGID)
    {
        throw new EntityNotFoundException();
    }

    return null;
}
```

Met de opsplitsing van de code en door vroeg te returnen in plaats van een `else`
statement neer te zetten, wordt de code meer leesbaar en onderhoudbaar.
Een blok code krijgt een verantwoordelijkheid en stopt waar dit nodig mogelijk is.

### Technical debt

Technical debt zijn overgebleven TODOs en andere zaken die niet belangrijk
zijn voor het functioneel hebben van de applicatie maar toch gefixed moeten worden.
Hierbij is er te denken aan nieuwe technieken gebruiken om code te vervangen,
snel gemaakte hacks vervangen door code waar overnaar is gedacht.

Technical debt hoopt zich op in de looptijd van een project en is een natuurlijk effect.
`Microsoft` heeft recentelijk aangekondigd om de Windows Command Line opnieuw te maken.
Hierin is de oude C++ code op de schop genomen en er is veel technical debt aanwezig.
Het team loopt nu tegen 100+ regels C++ code aan die in moderne C++ vervangen wordt door 4 regels.
Ook zijn er `TODO Fix this` comments gevonden uit 1970.

Dit moet echter niet genegeerd worden binnen het project.
Technical debt is belangrijk om de applicatie stabiel te houden.
