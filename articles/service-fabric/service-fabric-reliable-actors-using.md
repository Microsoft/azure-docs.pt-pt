---
title: Implementar funcionalidades em atores de Tecido de Serviço Azure
description: Descreve como escrever o seu próprio serviço de ator que implementa funcionalidades de nível de serviço da mesma forma que quando herda o StatefulService.
author: vturecek
ms.topic: conceptual
ms.date: 03/19/2018
ms.author: vturecek
ms.openlocfilehash: 9f5f9e00c374b16026f22d4efdee51ec94d2902a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79502281"
---
# <a name="implement-service-level-features-in-your-actor-service"></a>Implemente funcionalidades de nível de serviço no seu serviço de ator

Como descrito em camadas de [serviço,](service-fabric-reliable-actors-platform.md#service-layering)o próprio serviço do ator é um serviço confiável. Pode escrever o seu próprio `ActorService`serviço que deriva de . Também pode implementar funcionalidades de nível de serviço da mesma forma que quando herda um serviço estatal, tais como:

- Apoio de serviço e restauro.
- Funcionalidade partilhada para todos os atores, por exemplo, um disjuntor.
- O procedimento remoto apela ao próprio serviço do ator e a cada ator individual.

## <a name="use-the-actor-service"></a>Use o serviço de ator

Os casos de actortêm acesso ao serviço de ator estiveram a funcionar. Através do serviço de ator, as instâncias do ator podem obter programáticamente o contexto de serviço. O contexto de serviço tem o ID de partição, nome de serviço, nome da aplicação e outras informações específicas da plataforma Azure Service Fabric.

```csharp
Task MyActorMethod()
{
    Guid partitionId = this.ActorService.Context.PartitionId;
    string serviceTypeName = this.ActorService.Context.ServiceTypeName;
    Uri serviceInstanceName = this.ActorService.Context.ServiceName;
    string applicationInstanceName = this.ActorService.Context.CodePackageActivationContext.ApplicationName;
}
```
```Java
CompletableFuture<?> MyActorMethod()
{
    UUID partitionId = this.getActorService().getServiceContext().getPartitionId();
    String serviceTypeName = this.getActorService().getServiceContext().getServiceTypeName();
    URI serviceInstanceName = this.getActorService().getServiceContext().getServiceName();
    String applicationInstanceName = this.getActorService().getServiceContext().getCodePackageActivationContext().getApplicationName();
}
```

Como todos os Serviços Fiáveis, o serviço de ator deve ser registado com um tipo de serviço no tempo de execução do Tecido de Serviço. Para que o serviço de ator escorra as suas instâncias de ator, o seu tipo de ator também deve estar registado no serviço de ator. O método de registo `ActorRuntime` realiza este trabalho para atores. No caso mais simples, pode registar o seu tipo de ator, e o serviço do ator utiliza as definições padrão.

```csharp
static class Program
{
    private static void Main()
    {
        ActorRuntime.RegisterActorAsync<MyActor>().GetAwaiter().GetResult();

        Thread.Sleep(Timeout.Infinite);
    }
}
```

Em alternativa, pode utilizar uma lambda fornecida pelo método de registo para construir o serviço do ator. Em seguida, pode configurar o serviço de ator e construir explicitamente as suas instâncias de ator. Podes injetar dependências ao teu ator através do seu construtor.

```csharp
static class Program
{
    private static void Main()
    {
        ActorRuntime.RegisterActorAsync<MyActor>(
            (context, actorType) => new ActorService(context, actorType, () => new MyActor()))
            .GetAwaiter().GetResult();

        Thread.Sleep(Timeout.Infinite);
    }
}
```
```Java
static class Program
{
    private static void Main()
    {
      ActorRuntime.registerActorAsync(
              MyActor.class,
              (context, actorTypeInfo) -> new FabricActorService(context, actorTypeInfo),
              timeout);

        Thread.sleep(Long.MAX_VALUE);
    }
}
```

## <a name="actor-service-methods"></a>Métodos de serviço do ator

O serviço do `IActorService` ator implementa `ActorService` (C#) ou `IService` (Java), que `Service` por sua vez implementa (C#) ou (Java). Esta interface é utilizada pela Remoing Reliable Services, que permite que o procedimento remoto apele aos métodos de serviço. Contém métodos de nível de serviço que podem ser chamados remotamente através de remodeção de serviço. Pode usá-lo para [enumerar](service-fabric-reliable-actors-enumerate.md) e [eliminar](service-fabric-reliable-actors-delete-actors.md) atores.


## <a name="custom-actor-service"></a>Serviço de ator personalizado

Ao utilizar o registo do ator lambda, pode registar `ActorService` o seu próprio `FabricActorService` serviço de ator personalizado que deriva de (C#) e (Java). Em seguida, pode implementar a sua própria funcionalidade de `ActorService` nível de `FabricActorService` serviço escrevendo uma classe de serviço que herda (C#) ou (Java). Um serviço de ator personalizado herda toda a funcionalidade de tempo de execução do ator de `ActorService` (C#) ou `FabricActorService` (Java). Pode ser usado para implementar os seus próprios métodos de serviço.

```csharp
class MyActorService : ActorService
{
    public MyActorService(StatefulServiceContext context, ActorTypeInformation typeInfo, Func<ActorBase> newActor)
        : base(context, typeInfo, newActor)
    { }
}
```
```Java
class MyActorService extends FabricActorService
{
    public MyActorService(StatefulServiceContext context, ActorTypeInformation typeInfo, BiFunction<FabricActorService, ActorId, ActorBase> newActor)
    {
         super(context, typeInfo, newActor);
    }
}
```

```csharp
static class Program
{
    private static void Main()
    {
        ActorRuntime.RegisterActorAsync<MyActor>(
            (context, actorType) => new MyActorService(context, actorType, () => new MyActor()))
            .GetAwaiter().GetResult();

        Thread.Sleep(Timeout.Infinite);
    }
}
```
```Java
public class Program
{
    public static void main(String[] args)
    {
        ActorRuntime.registerActorAsync(
                MyActor.class,
                (context, actorTypeInfo) -> new FabricActorService(context, actorTypeInfo),
                timeout);
        Thread.sleep(Long.MAX_VALUE);
    }
}
```

## <a name="implement-actor-backup-and-restore"></a>Implementar backup e restauro do ator

Um serviço de ator personalizado pode expor um método para fazer o back `ActorService`up dados do ator, tirando partido do ouvinte remoting já presente em . Por exemplo, consulte [Backup e restaure os atores](service-fabric-reliable-actors-backup-and-restore.md).

## <a name="actor-that-uses-a-remoting-v2-interface-compatible-stack"></a>Ator que usa uma pilha V2 remoting (compatível com interface)

A pilha V2 remoting (compatível com interface, conhecida como V2_1) tem todas as características da pilha de removing V2. A sua interface é compatível com a pilha V1 remoting, mas não é compatível com V2 e V1. Para fazer upgrade de V1 para V2_1 sem efeitos na disponibilidade do serviço, siga os passos na secção seguinte.

São necessárias as seguintes alterações para utilizar a pilha de V2_1 remoting:

1. Adicione o seguinte atributo de montagem nas interfaces do ator.
  
   ```csharp
   [assembly:FabricTransportActorRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2_1,RemotingClientVersion = RemotingClientVersion.V2_1)]
   ```

2. Construa e atualize o serviço de ator e os projetos de cliente ator para começar a usar a pilha V2.

### <a name="actor-service-upgrade-to-remoting-v2-interface-compatible-stack-without-affecting-service-availability"></a>Upgrade de serviço do ator para remoting V2 (interface compatível) pilha sem afetar a disponibilidade do serviço

Esta mudança é uma atualização em duas etapas. Siga os passos nesta sequência.

1. Adicione o seguinte atributo de montagem nas interfaces do ator. Este atributo começa com dois ouvintes para o serviço de ator, V1 (existente) e o ouvinte V2_1. Atualize o serviço de ator com esta mudança.

   ```csharp
   [assembly:FabricTransportActorRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V1|RemotingListenerVersion.V2_1,RemotingClientVersion = RemotingClientVersion.V2_1)]
   ```

2. Atualize os clientes do ator depois de completar a atualização anterior.
   Este passo garante que o representante do ator usa a pilha de V2_1 remoting.

3. Este passo é opcional. Altere o atributo anterior para remover o ouvinte V1.

    ```csharp
    [assembly:FabricTransportActorRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2_1,RemotingClientVersion = RemotingClientVersion.V2_1)]
    ```

## <a name="actor-that-uses-the-remoting-v2-stack"></a>Ator que usa a pilha V2 remoting

Com o pacote NuGet da versão 2.8, os utilizadores podem agora utilizar a pilha V2 remoting, que funciona melhor e fornece funcionalidades como serialização personalizada. Remoting V2 não é retrocompatível com a pilha de remoing existente (agora chamada de pilha de remo V1).

São necessárias as seguintes alterações para utilizar a pilha V2 em remo.

1. Adicione o seguinte atributo de montagem nas interfaces do ator.

   ```csharp
   [assembly:FabricTransportActorRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2,RemotingClientVersion = RemotingClientVersion.V2)]
   ```

2. Construa e atualize o serviço de ator e os projetos de cliente ator para começar a usar a pilha V2.

### <a name="upgrade-the-actor-service-to-the-remoting-v2-stack-without-affecting-service-availability"></a>Atualize o serviço de ator para a pilha V2 remoting sem afetar a disponibilidade do serviço

Esta mudança é uma atualização em duas etapas. Siga os passos nesta sequência.

1. Adicione o seguinte atributo de montagem nas interfaces do ator. Este atributo começa com dois ouvintes para o serviço de ator, V1 (existente) e o ouvinte V2. Atualize o serviço de ator com esta mudança.

   ```csharp
   [assembly:FabricTransportActorRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V1|RemotingListenerVersion.V2,RemotingClientVersion = RemotingClientVersion.V2)]
   ```

2. Atualize os clientes do ator depois de completar a atualização anterior.
   Este passo garante que o representante do ator usa a pilha V2 remoting.

3. Este passo é opcional. Altere o atributo anterior para remover o ouvinte V1.

    ```csharp
    [assembly:FabricTransportActorRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2,RemotingClientVersion = RemotingClientVersion.V2)]
    ```

## <a name="next-steps"></a>Passos seguintes

* [Gestão do Estado do ator](service-fabric-reliable-actors-state-management.md)
* [Ciclo de vida do ator e recolha de lixo](service-fabric-reliable-actors-lifecycle.md)
* [Documentação de referência da API dos atores](https://msdn.microsoft.com/library/azure/dn971626.aspx)
* [Código de amostra .NET](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Código da amostra java](https://github.com/Azure-Samples/service-fabric-java-getting-started)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-platform/actor-service.png
[2]: ./media/service-fabric-reliable-actors-platform/app-deployment-scripts.png
[3]: ./media/service-fabric-reliable-actors-platform/actor-partition-info.png
[4]: ./media/service-fabric-reliable-actors-platform/actor-replica-role.png
[5]: ./media/service-fabric-reliable-actors-introduction/distribution.png
