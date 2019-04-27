---
title: Polimorfismo no framework Reliable Actors | Documentos da Microsoft
description: Crie hierarquias de .NET interfaces e tipos na estrutura dos Reliable Actors reutilizar a funcionalidade e as definições da API.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: chackdan
editor: vturecek
ms.assetid: ef0eeff6-32b7-410d-ac69-87cba8b8fd46
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/02/2017
ms.author: vturecek
ms.openlocfilehash: c14b3006184f7bd6dcd1eb67be11bd0214957d72
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60725500"
---
# <a name="polymorphism-in-the-reliable-actors-framework"></a>Polimorfismo no framework Reliable Actors
A estrutura de Reliable Actors permite-lhe criar atores utilizando muitas das técnicas que usaria no design orientado a objeto. Uma dessas técnicas é que o polimorfismo, que permite que os tipos e interfaces para herdar de mais generalizada pais. Herança do Framework de Reliable Actors em geral, segue o modelo de .NET com algumas restrições adicionais. Em caso de Java/Linux, ele segue o modelo de Java.

## <a name="interfaces"></a>Interfaces
A estrutura de Reliable Actors exige que defina pelo menos uma interface para ser implementada por seu tipo de ator. Essa interface é usada para gerar uma classe de proxy que pode ser utilizada pelos clientes para comunicar com seus atores. Interfaces podem herdar de outras interfaces, desde que cada interface implementada por um tipo de ator e todos os seus pais, por fim, derivar de IActor (C#) ou Actor(Java). IActor (C#) e Actor(Java) são as interfaces de bases definida de plataforma para atores em estruturas .NET e Java, respectivamente. Portanto, o exemplo de polimorfismo clássico com formas poderia uma aparência semelhante a esta:

![Hierarquia de interface para os atores de forma][shapes-interface-hierarchy]

## <a name="types"></a>Tipos
Também pode criar uma hierarquia de tipos de ator, que são derivadas da classe base do Ator que é fornecida pela plataforma. No caso de formas, poderá ter uma base `Shape`(C#) ou `ShapeImpl`tipo de (Java):

```csharp
public abstract class Shape : Actor, IShape
{
    public abstract Task<int> GetVerticeCount();

    public abstract Task<double> GetAreaAsync();
}
```
```Java
public abstract class ShapeImpl extends FabricActor implements Shape
{
    public abstract CompletableFuture<int> getVerticeCount();

    public abstract CompletableFuture<double> getAreaAsync();
}
```

Subtypes dos `Shape`(C#) ou `ShapeImpl`(Java) pode substituir os métodos da base.

```csharp
[ActorService(Name = "Circle")]
[StatePersistence(StatePersistence.Persisted)]
public class Circle : Shape, ICircle
{
    public override Task<int> GetVerticeCount()
    {
        return Task.FromResult(0);
    }

    public override async Task<double> GetAreaAsync()
    {
        CircleState state = await this.StateManager.GetStateAsync<CircleState>("circle");

        return Math.PI *
            state.Radius *
            state.Radius;
    }
}
```
```Java
@ActorServiceAttribute(name = "Circle")
@StatePersistenceAttribute(statePersistence = StatePersistence.Persisted)
public class Circle extends ShapeImpl implements Circle
{
    @Override
    public CompletableFuture<Integer> getVerticeCount()
    {
        return CompletableFuture.completedFuture(0);
    }

    @Override
    public CompletableFuture<Double> getAreaAsync()
    {
        return (this.stateManager().getStateAsync<CircleState>("circle").thenApply(state->{
          return Math.PI * state.radius * state.radius;
        }));
    }
}
```

Tenha em atenção o `ActorService` atributo no tipo de ator. Este atributo informa a estrutura de Reliable Actor que deve criar automaticamente um serviço para o alojamento de atores desse tipo. Em alguns casos, pode pretender criar um tipo de base que destina-se exclusivamente para compartilhar a funcionalidade com subtipos e nunca será utilizado para criar uma instância de atores concretos. Nesses casos, deve usar o `abstract` palavra-chave para indicar que nunca criará um ator com base nesse tipo.

## <a name="next-steps"></a>Passos Seguintes
* Ver [como o framework de Reliable Actors tira partido da plataforma do Service Fabric](service-fabric-reliable-actors-platform.md) para fornecer a fiabilidade, escalabilidade e o estado consistente.
* Saiba mais sobre o [ciclo de vida de ator](service-fabric-reliable-actors-lifecycle.md).

<!-- Image references -->

[shapes-interface-hierarchy]: ./media/service-fabric-reliable-actors-polymorphism/Shapes-Interface-Hierarchy.png
