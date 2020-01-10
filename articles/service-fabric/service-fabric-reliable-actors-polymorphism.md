---
title: Polimorfismo na estrutura de Reliable Actors
description: Crie hierarquias de interfaces e tipos .NET no Reliable Actors Framework para reutilizar a funcionalidade e as definições de API.
author: vturecek
ms.topic: conceptual
ms.date: 11/02/2017
ms.author: vturecek
ms.openlocfilehash: 4e485463f41cdfbadeb166ecbb3a86d4a32c1589
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75348930"
---
# <a name="polymorphism-in-the-reliable-actors-framework"></a>Polimorfismo na estrutura de Reliable Actors
O Reliable Actors Framework permite que você crie atores usando muitas das mesmas técnicas que usaria no design orientado a objeto. Uma dessas técnicas é o polimorfismo, que permite que tipos e interfaces herdem de pais mais generalizados. A herança na estrutura de Reliable Actors geralmente segue o modelo .NET com algumas restrições adicionais. No caso do Java/Linux, ele segue o modelo Java.

## <a name="interfaces"></a>Interfaces
O Reliable Actors Framework exige que você defina pelo menos uma interface a ser implementada pelo seu tipo de ator. Essa interface é usada para gerar uma classe proxy que pode ser usada por clientes para se comunicar com seus atores. As interfaces podem herdar de outras interfaces, contanto que cada interface implementada por um tipo de ator e todos os seus pais derivemC#, por fim, de IActor () ou ator (Java). IActor (C#) e actor (Java) são as interfaces base definidas pela plataforma para atores nas estruturas .net e Java, respectivamente. Assim, o exemplo de polimorfismo clássico usando formas pode ser semelhante ao seguinte:

![Hierarquia de interface para atores de forma][shapes-interface-hierarchy]

## <a name="types"></a>Tipos
Você também pode criar uma hierarquia de tipos de ator, que são derivados da classe de ator base fornecida pela plataforma. No caso de formas, você pode ter um tipo de `Shape`(C#) ou `ShapeImpl`(Java) de base:

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

Os subtipos de `Shape`C#() ou `ShapeImpl`(Java) podem substituir os métodos da base.

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

Observe o atributo `ActorService` no tipo de ator. Esse atributo informa à estrutura de ator confiável que ele deve criar automaticamente um serviço para hospedar atores desse tipo. Em alguns casos, talvez você queira criar um tipo base destinado exclusivamente ao compartilhamento de funcionalidade com subtipos e nunca será usado para criar uma instância de atores concretos. Nesses casos, você deve usar a palavra-chave `abstract` para indicar que você nunca criará um ator com base nesse tipo.

## <a name="next-steps"></a>Passos seguintes
* Veja [como a estrutura de Reliable Actors aproveita a plataforma Service Fabric](service-fabric-reliable-actors-platform.md) para fornecer confiabilidade, escalabilidade e estado consistente.
* Saiba mais sobre o [ciclo de vida do ator](service-fabric-reliable-actors-lifecycle.md).

<!-- Image references -->

[shapes-interface-hierarchy]: ./media/service-fabric-reliable-actors-polymorphism/Shapes-Interface-Hierarchy.png
