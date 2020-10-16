---
title: Polimorfismo no quadro de atores fiáveis
description: Construa hierarquias de interfaces e tipos .NET no quadro de Atores Fidedignos para reutilizar as definições de funcionalidade e API.
author: vturecek
ms.topic: conceptual
ms.date: 11/02/2017
ms.author: vturecek
ms.custom: devx-track-csharp
ms.openlocfilehash: 7552609fc4c85cdd5033e67b1bab7fb58cb6ebf1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89016568"
---
# <a name="polymorphism-in-the-reliable-actors-framework"></a>Polimorfismo no quadro de atores fiáveis
A estrutura de Atores Fidedigtos permite-lhe construir atores usando muitas das mesmas técnicas que usaria no design orientado para objetos. Uma dessas técnicas é o polimorfismo, que permite que tipos e interfaces herdem de pais mais generalizados. A herança no quadro de Atores Fiáveis segue geralmente o modelo .NET com alguns constrangimentos adicionais. No caso de Java/Linux, segue o modelo Java.

## <a name="interfaces"></a>Interfaces
O quadro de Atores Fidedigtos requer que você defina pelo menos uma interface a ser implementada pelo seu tipo de ator. Esta interface é usada para gerar uma classe de procuração que pode ser usada pelos clientes para comunicar com os seus atores. As interfaces podem herdar de outras interfaces, desde que todas as interfaces que são implementadas por um tipo de ator e todos os seus pais, em última análise, derivam de IActor(C#) ou Ator(Java) . IActor(C#) e Ator(Java) são as interfaces base definidas pela plataforma para atores nos quadros .NET e Java, respectivamente. Assim, o exemplo clássico do polimorfismo usando formas pode parecer algo assim:

![Hierarquia de interface para atores de forma][shapes-interface-hierarchy]

## <a name="types"></a>Tipos
Também pode criar uma hierarquia de tipos de atores, que são derivados da classe base ator que é fornecida pela plataforma. No caso de formas, pode ter um tipo base `Shape` (C#) ou `ShapeImpl` (Java):

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

Subtipos de `Shape` (C#) ou `ShapeImpl` (Java) podem sobrepor métodos a partir da base.

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

Note o `ActorService` atributo no tipo de ator. Este atributo diz ao quadro de Ator Fiável que deve criar automaticamente um serviço para hospedar atores deste tipo. Em alguns casos, poderá desejar criar um tipo base que se destina exclusivamente à partilha de funcionalidades com subtipos e nunca será utilizado para instantaneaizar atores concretos. Nesses casos, deve usar a `abstract` palavra-chave para indicar que nunca criará um ator com base nesse tipo.

## <a name="next-steps"></a>Passos seguintes
* Veja [como o quadro de Atores Fidedigtos aproveita a plataforma Service Fabric](service-fabric-reliable-actors-platform.md) para fornecer fiabilidade, escalabilidade e estado consistente.
* Conheça o ciclo de vida do [ator.](service-fabric-reliable-actors-lifecycle.md)

<!-- Image references -->

[shapes-interface-hierarchy]: ./media/service-fabric-reliable-actors-polymorphism/Shapes-Interface-Hierarchy.png
