---
title: Polimorfismo no quadro de Atores Fiáveis
description: Construir hierarquias de interfaces e tipos .NET no quadro De Atores Fiáveis para reutilizar funcionalidades e definições de API.
author: vturecek
ms.topic: conceptual
ms.date: 11/02/2017
ms.author: vturecek
ms.openlocfilehash: 4e485463f41cdfbadeb166ecbb3a86d4a32c1589
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75348930"
---
# <a name="polymorphism-in-the-reliable-actors-framework"></a>Polimorfismo no quadro de Atores Fiáveis
A estrutura de Atores Fiáveis permite-lhe construir atores usando muitas das mesmas técnicas que usaria no design orientado para objetos. Uma dessas técnicas é o polimorfismo, que permite que tipos e interfaces herdem de pais mais generalizados. A herança no quadro "Reliable Actors" segue geralmente o modelo .NET com alguns constrangimentos adicionais. No caso de Java/Linux, segue o modelo Java.

## <a name="interfaces"></a>Interfaces
A estrutura de Atores Fiáveis requer que defina pelo menos uma interface a ser implementada pelo seu tipo de ator. Esta interface é usada para gerar uma classe de procuração que pode ser usada pelos clientes para comunicar com os seus atores. As interfaces podem herdar de outras interfaces desde que todas as interfaces implementadas por um tipo de ator e todos os seus pais derivam, em última análise, do IActor(C#) ou do Ator (Java). IActor(C#) e Ator (Java) são as interfaces base definidas pela plataforma para os atores nas estruturas .NET e Java, respectivamente. Assim, o exemplo clássico do polimorfismo usando formas pode parecer algo assim:

![Hierarquia da interface para atores de forma][shapes-interface-hierarchy]

## <a name="types"></a>Tipos
Você também pode criar uma hierarquia de tipos de atores, que são derivados da classe base ator que é fornecido pela plataforma. No caso de formas, pode `Shape`ter um tipo `ShapeImpl`de base (C#) ou (Java):

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

Subtipos `Shape`de (C#) ou `ShapeImpl`(Java) podem substituir métodos a partir da base.

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

Reparem `ActorService` no atributo do tipo ator. Este atributo diz ao quadro do Ator Fiável que deve criar automaticamente um serviço para hospedar atores deste tipo. Em alguns casos, poderá querer criar um tipo base que se destina exclusivamente à partilha de funcionalidades com subtipos e que nunca será utilizado para instantaneamente atores de betão. Nesses casos, deve `abstract` usar a palavra-chave para indicar que nunca criará um ator baseado nesse tipo.

## <a name="next-steps"></a>Passos seguintes
* Veja [como a estrutura de Atores Fiáveis aproveita a plataforma Service Fabric](service-fabric-reliable-actors-platform.md) para fornecer fiabilidade, escalabilidade e estado consistente.
* Conheça o ciclo de vida do [ator.](service-fabric-reliable-actors-lifecycle.md)

<!-- Image references -->

[shapes-interface-hierarchy]: ./media/service-fabric-reliable-actors-polymorphism/Shapes-Interface-Hierarchy.png
