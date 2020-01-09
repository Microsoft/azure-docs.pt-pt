---
title: Implementar recursos no Azure Service Fabric atores
description: Descreve como escrever seu próprio serviço de ator que implementa recursos de nível de serviço da mesma maneira que quando você herda o StatefulService.
author: vturecek
ms.topic: conceptual
ms.date: 03/19/2018
ms.author: vturecek
ms.openlocfilehash: 9f5f9e00c374b16026f22d4efdee51ec94d2902a
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75426723"
---
# <a name="implement-service-level-features-in-your-actor-service"></a>Implementar recursos de nível de serviço em seu serviço de ator

Conforme descrito em [camadas de serviço](service-fabric-reliable-actors-platform.md#service-layering), o próprio serviço de ator é um serviço confiável. Você pode escrever seu próprio serviço derivado de `ActorService`. Você também pode implementar recursos de nível de serviço da mesma maneira que ao herdar um serviço com estado, como:

- Backup e restauração de serviço.
- Funcionalidade compartilhada para todos os atores, por exemplo, um disjuntor.
- Chamadas de procedimento remoto no próprio serviço de ator e em cada ator individual.

## <a name="use-the-actor-service"></a>Usar o serviço de ator

As instâncias de ator têm acesso ao serviço de ator no qual estão em execução. Por meio do serviço de ator, as instâncias de ator podem obter programaticamente o contexto de serviço. O contexto de serviço tem a ID da partição, o nome do serviço, o nome do aplicativo e outras informações específicas da plataforma de Service Fabric do Azure.

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

Assim como todos os Reliable Services, o serviço de ator deve ser registrado com um tipo de serviço no tempo de execução de Service Fabric. Para que o serviço de ator execute suas instâncias de ator, o tipo de ator também deve ser registrado com o serviço de ator. O método de registo `ActorRuntime` realiza este trabalho para atores. No caso mais simples, você pode registrar o tipo de ator e o serviço de ator usa as configurações padrão.

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

Como alternativa, você pode usar um lambda fornecido pelo método de registro para construir o serviço de ator por conta própria. Em seguida, você pode configurar o serviço de ator e construir explicitamente suas instâncias de ator. Você pode injetar dependências para seu ator por meio de seu construtor.

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

## <a name="actor-service-methods"></a>Métodos do serviço de ator

O serviço de ator implementa `IActorService`C#() ou `ActorService` (Java), que, por sua vezC#, implementa `IService` () ou `Service` (Java). Essa interface é usada por Reliable Services comunicação remota, que permite chamadas de procedimento remotos em métodos de serviço. Ele contém métodos de nível de serviço que podem ser chamados remotamente por meio da comunicação remota do serviço. Você pode usá-lo para [enumerar](service-fabric-reliable-actors-enumerate.md) e [excluir](service-fabric-reliable-actors-delete-actors.md) atores.


## <a name="custom-actor-service"></a>Serviço de ator personalizado

Usando o lambda de registro do ator, você pode registrar seu próprio serviço de ator personalizado que deriva de `ActorService`C#() e `FabricActorService` (Java). Em seguida, você pode implementar sua própria funcionalidade de nível de serviço escrevendo uma classe de serviço queC#herda `ActorService` () ou `FabricActorService` (Java). Um serviço de ator personalizado herda toda a funcionalidade de tempo de execuçãoC#de ator de `ActorService` () ou `FabricActorService` (Java). Ele pode ser usado para implementar seus próprios métodos de serviço.

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

## <a name="implement-actor-backup-and-restore"></a>Implementar backup e restauração de ator

Um serviço de ator personalizado pode expor um método para fazer backup de dados de ator aproveitando o ouvinte de comunicação remota já presente no `ActorService`. Para obter um exemplo, consulte os [atores de backup e restauração](service-fabric-reliable-actors-backup-and-restore.md).

## <a name="actor-that-uses-a-remoting-v2-interface-compatible-stack"></a>Ator que usa uma pilha v2 de comunicação remota (compatível com interface)

A pilha remota v2 (compatível com a interface, conhecida como V2_1) tem todos os recursos da pilha de comunicação remota v2. Sua interface é compatível com a pilha de comunicação remota v1, mas não é compatível com a versão V2 e v1. Para atualizar de v1 para V2_1 sem efeitos sobre a disponibilidade do serviço, siga as etapas na próxima seção.

As seguintes alterações são necessárias para usar a pilha de V2_1 de comunicação remota:

1. Adicione o seguinte atributo de assembly em interfaces de ator.
  
   ```csharp
   [assembly:FabricTransportActorRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2_1,RemotingClientVersion = RemotingClientVersion.V2_1)]
   ```

2. Crie e atualize projetos de cliente de ator e serviço de ator para começar a usar a pilha v2.

### <a name="actor-service-upgrade-to-remoting-v2-interface-compatible-stack-without-affecting-service-availability"></a>Atualização do serviço de ator para a pilha do Remoting v2 (compatível com a interface) sem afetar a disponibilidade do serviço

Essa alteração é uma atualização em duas etapas. Siga as etapas nesta sequência.

1. Adicione o seguinte atributo de assembly em interfaces de ator. Esse atributo inicia dois ouvintes para o serviço de ator, V1 (existente) e o ouvinte de V2_1. Atualize o serviço de ator com essa alteração.

   ```csharp
   [assembly:FabricTransportActorRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V1|RemotingListenerVersion.V2_1,RemotingClientVersion = RemotingClientVersion.V2_1)]
   ```

2. Atualize os clientes do ator depois de concluir a atualização anterior.
   Essa etapa garante que o proxy de ator use a pilha de V2_1 de comunicação remota.

3. Este passo é opcional. Altere o atributo anterior para remover o ouvinte v1.

    ```csharp
    [assembly:FabricTransportActorRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2_1,RemotingClientVersion = RemotingClientVersion.V2_1)]
    ```

## <a name="actor-that-uses-the-remoting-v2-stack"></a>Ator que usa a pilha v2 de comunicação remota

Com o pacote NuGet da versão 2,8, os usuários agora podem usar a pilha v2 de comunicação remota, que tem um desempenho melhor e fornece recursos como serialização personalizada. O Remoting v2 não é compatível retroativamente com a pilha de comunicação remota existente (agora chamada de pilha de comunicação remota v1).

As seguintes alterações são necessárias para usar a pilha v2 de comunicação remota.

1. Adicione o seguinte atributo de assembly em interfaces de ator.

   ```csharp
   [assembly:FabricTransportActorRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2,RemotingClientVersion = RemotingClientVersion.V2)]
   ```

2. Crie e atualize os projetos do serviço de ator e do cliente de ator para começar a usar a pilha v2.

### <a name="upgrade-the-actor-service-to-the-remoting-v2-stack-without-affecting-service-availability"></a>Atualizar o serviço de ator para a pilha v2 de comunicação remota sem afetar a disponibilidade do serviço

Essa alteração é uma atualização em duas etapas. Siga as etapas nesta sequência.

1. Adicione o seguinte atributo de assembly em interfaces de ator. Esse atributo inicia dois ouvintes para o serviço de ator, V1 (existente) e o ouvinte v2. Atualize o serviço de ator com essa alteração.

   ```csharp
   [assembly:FabricTransportActorRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V1|RemotingListenerVersion.V2,RemotingClientVersion = RemotingClientVersion.V2)]
   ```

2. Atualize os clientes do ator depois de concluir a atualização anterior.
   Essa etapa garante que o proxy de ator use a pilha v2 de comunicação remota.

3. Este passo é opcional. Altere o atributo anterior para remover o ouvinte v1.

    ```csharp
    [assembly:FabricTransportActorRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2,RemotingClientVersion = RemotingClientVersion.V2)]
    ```

## <a name="next-steps"></a>Passos seguintes

* [Gerenciamento de estado do ator](service-fabric-reliable-actors-state-management.md)
* [Ciclo de vida do ator e coleta de lixo](service-fabric-reliable-actors-lifecycle.md)
* [Documentação de referência da API dos atores](https://msdn.microsoft.com/library/azure/dn971626.aspx)
* [Código de exemplo do .NET](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Código de exemplo Java](https://github.com/Azure-Samples/service-fabric-java-getting-started)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-platform/actor-service.png
[2]: ./media/service-fabric-reliable-actors-platform/app-deployment-scripts.png
[3]: ./media/service-fabric-reliable-actors-platform/actor-partition-info.png
[4]: ./media/service-fabric-reliable-actors-platform/actor-replica-role.png
[5]: ./media/service-fabric-reliable-actors-introduction/distribution.png
