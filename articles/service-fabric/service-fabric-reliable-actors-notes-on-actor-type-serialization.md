---
title: Betrouwbare actoren opmerkingen bij de actor type serialisatie | Microsoft Docs
description: Basisvereisten voor het definiëren van serialiseerbaar klassen die kunnen worden gebruikt voor het definiëren van Service Fabric Reliable Actors statussen en interfaces wordt beschreven
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: ''
ms.assetid: 6e50e4dc-969a-4a1c-b36c-b292d964c7e3
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/02/2017
ms.author: vturecek
ms.openlocfilehash: 210f47b4b052286900781f97077af4d0a0b9c968
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/23/2018
---
# <a name="notes-on-service-fabric-reliable-actors-type-serialization"></a>Opmerkingen bij de Service Fabric Reliable Actors type serialisatie
De argumenten van alle methoden resultaattypen van de taken worden geretourneerd door elke methode in een interface actor en objecten die zijn opgeslagen in een actor statusbeheer moet [gegevenscontract serialiseerbaar](/dotnet/framework/wcf/feature-details/types-supported-by-the-data-contract-serializer). Dit geldt ook voor de argumenten van de methoden die zijn gedefinieerd in [actor-gebeurtenisinterfaces](service-fabric-reliable-actors-events.md). (Actor gebeurtenis interfacemethoden altijd void retourneren.)

## <a name="custom-data-types"></a>Aangepaste gegevenstypen
In dit voorbeeld definieert de volgende actor-interface een methode die een aangepast gegevenstype aangeroepen retourneert `VoicemailBox`:

```csharp
public interface IVoiceMailBoxActor : IActor
{
    Task<VoicemailBox> GetMailBoxAsync();
}
```

```Java
public interface VoiceMailBoxActor extends Actor
{
    CompletableFuture<VoicemailBox> getMailBoxAsync();
}
```

De interface wordt geïmplementeerd door een actor die gebruikmaakt van de status manager voor het opslaan van een `VoicemailBox` object:

```csharp
[StatePersistence(StatePersistence.Persisted)]
public class VoiceMailBoxActor : Actor, IVoicemailBoxActor
{
    public VoiceMailBoxActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public Task<VoicemailBox> GetMailboxAsync()
    {
        return this.StateManager.GetStateAsync<VoicemailBox>("Mailbox");
    }
}

```

```Java
@StatePersistenceAttribute(statePersistence = StatePersistence.Persisted)
public class VoiceMailBoxActorImpl extends FabricActor implements VoicemailBoxActor
{
    public VoiceMailBoxActorImpl(ActorService actorService, ActorId actorId)
    {
         super(actorService, actorId);
    }

    public CompletableFuture<VoicemailBox> getMailBoxAsync()
    {
         return this.stateManager().getStateAsync("Mailbox");
    }
}

```

In dit voorbeeld wordt de `VoicemailBox` object wordt geserialiseerd wanneer:

* Het object wordt tussen een actor-exemplaar en een aanroeper verzonden.
* Het object is opgeslagen in de status manager waar het op schijf opgeslagen en gerepliceerd naar andere knooppunten.

Het framework betrouwbare Actor maakt gebruik van DataContract-serialisatie. Daarom de aangepaste objecten en hun leden moeten worden gemarkeerd met de **DataContract** en **DataMember** kenmerken, respectievelijk.

```csharp
[DataContract]
public class Voicemail
{
    [DataMember]
    public Guid Id { get; set; }

    [DataMember]
    public string Message { get; set; }

    [DataMember]
    public DateTime ReceivedAt { get; set; }
}
```
```Java
public class Voicemail implements Serializable
{
    private static final long serialVersionUID = 42L;

    private UUID id;                    //getUUID() and setUUID()

    private String message;             //getMessage() and setMessage()

    private GregorianCalendar receivedAt; //getReceivedAt() and setReceivedAt()
}
```


```csharp
[DataContract]
public class VoicemailBox
{
    public VoicemailBox()
    {
        this.MessageList = new List<Voicemail>();
    }

    [DataMember]
    public List<Voicemail> MessageList { get; set; }

    [DataMember]
    public string Greeting { get; set; }
}
```
```Java
public class VoicemailBox implements Serializable
{
    static final long serialVersionUID = 42L;
    
    public VoicemailBox()
    {
        this.messageList = new ArrayList<Voicemail>();
    }

    private List<Voicemail> messageList;   //getMessageList() and setMessageList()

    private String greeting;               //getGreeting() and setGreeting()
}
```


## <a name="next-steps"></a>Volgende stappen
* [Acteur lifecycle en garbage collection](service-fabric-reliable-actors-lifecycle.md)
* [Acteur timers en herinneringen](service-fabric-reliable-actors-timers-reminders.md)
* [Actor-gebeurtenissen](service-fabric-reliable-actors-events.md)
* [Acteur herintreding](service-fabric-reliable-actors-reentrancy.md)
* [Acteur polymorfisme en objectgeoriënteerde ontwerppatronen](service-fabric-reliable-actors-polymorphism.md)
* [Acteur diagnostische gegevens en prestatiebewaking](service-fabric-reliable-actors-diagnostics.md)
