---
title: Implementar funcionalidades em atores de tecido de serviço Azure
description: Descreve como escrever o seu próprio serviço de ator que implementa funcionalidades de nível de serviço da mesma forma que quando herda o StatefulService.
ms.topic: conceptual
ms.date: 03/19/2018
ms.custom: devx-track-csharp
ms.openlocfilehash: d39ec93e0ad03d6c860bae9d0790e860c95457a5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96575572"
---
# <a name="implement-service-level-features-in-your-actor-service"></a>Implementar funcionalidades de nível de serviço no seu serviço de ator

Como descrito em [camadas de serviço,](service-fabric-reliable-actors-platform.md#service-layering)o próprio serviço de ator é um serviço confiável. Pode escrever o seu próprio serviço que deriva `ActorService` de. Também pode implementar funcionalidades de nível de serviço da mesma forma que quando herda um serviço estatal, como:

- Assistência e restauro.
- Funcionalidade partilhada para todos os atores, por exemplo, um disjuntor.
- O procedimento remoto requer o próprio serviço do ator e de cada ator individual.

## <a name="use-the-actor-service"></a>Use o serviço de ator

Os casos de ator têm acesso ao serviço de atores em que estão a correr. Através do serviço de ator, os casos de ator podem obter programáticamente o contexto de serviço. O contexto de serviço tem o ID de partição, nome de serviço, nome de aplicação e outras informações específicas da plataforma Azure Service Fabric.

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

Como todos os Serviços Fidedig rios, o serviço de ator deve ser registado com um tipo de serviço no tempo de execução do Tecido de Serviço. Para que o serviço de ator desemisse os seus casos de ator, o seu tipo de ator também deve estar registado no serviço de ator. O método de registo `ActorRuntime` realiza este trabalho para atores. No caso mais simples, pode registar o seu tipo de ator, e o serviço de ator utiliza as definições predefinidos.

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

Em alternativa, pode utilizar uma lambda fornecida pelo método de registo para construir o serviço do ator. Em seguida, pode configurar o serviço de ator e construir explicitamente os seus casos de ator. Podes injetar dependências ao teu ator através do seu construtor.

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

O serviço do ator implementa `IActorService` (C#) ou `ActorService` (Java), que por sua vez implementa `IService` (C#) ou `Service` (Java). Esta interface é utilizada pela remoting de Serviços Fiáveis, que permite chamadas de procedimento remoto em métodos de serviço. Contém métodos de nível de serviço que podem ser chamados remotamente através da remoting de serviço. Pode usá-lo para [enumerar](service-fabric-reliable-actors-enumerate.md) e [apagar](service-fabric-reliable-actors-delete-actors.md) atores.


## <a name="custom-actor-service"></a>Serviço de ator personalizado

Ao utilizar o registo do ator lambda, pode registar o seu próprio serviço de ator personalizado que deriva de `ActorService` (C#) e `FabricActorService` (Java). Em seguida, pode implementar a sua própria funcionalidade de nível de serviço escrevendo uma classe de serviço que herda `ActorService` (C#) ou `FabricActorService` (Java). Um serviço de ator personalizado herda toda a funcionalidade de execução do ator de `ActorService` (C#) ou `FabricActorService` (Java). Pode ser usado para implementar os seus próprios métodos de serviço.

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

## <a name="implement-actor-backup-and-restore"></a>Implementar backup de ator e restaurar

Um serviço de ator personalizado pode expor um método de apoio aos dados do ator, tirando partido do ouvinte de remoting já presente em `ActorService` . Por exemplo, consulte [backup e restaure os atores.](service-fabric-reliable-actors-backup-and-restore.md)

## <a name="actor-that-uses-a-remoting-v2-interface-compatible-stack"></a>Ator que usa uma pilha V2 (compatível com interface)

A pilha de remoting V2 (compatível com interface, conhecida como V2_1) tem todas as características da pilha de remoing V2. A sua interface é compatível com a pilha V1 de remoting, mas não é compatível com V2 e V1. Para atualizar de V1 para V2_1 sem efeitos na disponibilidade do serviço, siga os passos na secção seguinte.

São necessárias as seguintes alterações para utilizar a pilha de V2_1 de remoting:

1. Adicione o seguinte atributo de montagem nas interfaces do ator.
  
   ```csharp
   [assembly:FabricTransportActorRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2_1,RemotingClientVersion = RemotingClientVersion.V2_1)]
   ```

2. Construa e atualize os projetos de clientes de ator e ator para começar a usar a pilha V2.

### <a name="actor-service-upgrade-to-remoting-v2-interface-compatible-stack-without-affecting-service-availability"></a>Atualização do serviço do ator para a pilha V2 (compatível com interface) sem afetar a disponibilidade do serviço

Esta mudança é uma atualização em dois passos. Siga os passos desta sequência.

1. Adicione o seguinte atributo de montagem nas interfaces do ator. Este atributo inicia dois ouvintes para o serviço de ator, V1 (existente) e o ouvinte V2_1. Atualize o serviço de ator com esta mudança.

   ```csharp
   [assembly:FabricTransportActorRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V1|RemotingListenerVersion.V2_1,RemotingClientVersion = RemotingClientVersion.V2_1)]
   ```

2. Atualize os clientes do ator depois de completar a atualização anterior.
   Este passo garante que o representante do ator usa a pilha de V2_1 de remoing.

3. Este passo é opcional. Mude o atributo anterior para remover o ouvinte V1.

    ```csharp
    [assembly:FabricTransportActorRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2_1,RemotingClientVersion = RemotingClientVersion.V2_1)]
    ```

## <a name="actor-that-uses-the-remoting-v2-stack"></a>Ator que usa a pilha V2 de remoting

Com o pacote NuGet da versão 2.8, os utilizadores podem agora utilizar a stack V2 de remoting, que funciona melhor e fornece funcionalidades como serialização personalizada. O remoting V2 não é compatível com a pilha de remoagem existente (agora chamada de pilha de remoagem V1).

São necessárias as seguintes alterações para utilizar a pilha V2 de remoting.

1. Adicione o seguinte atributo de montagem nas interfaces do ator.

   ```csharp
   [assembly:FabricTransportActorRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2,RemotingClientVersion = RemotingClientVersion.V2)]
   ```

2. Construa e atualize os projetos de clientes de ator e ator para começar a usar a pilha V2.

### <a name="upgrade-the-actor-service-to-the-remoting-v2-stack-without-affecting-service-availability"></a>Atualize o serviço de ator para a pilha V2 de remoting sem afetar a disponibilidade do serviço

Esta mudança é uma atualização em dois passos. Siga os passos desta sequência.

1. Adicione o seguinte atributo de montagem nas interfaces do ator. Este atributo inicia dois ouvintes para o serviço de ator, V1 (existente) e o ouvinte V2. Atualize o serviço de ator com esta mudança.

   ```csharp
   [assembly:FabricTransportActorRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V1|RemotingListenerVersion.V2,RemotingClientVersion = RemotingClientVersion.V2)]
   ```

2. Atualize os clientes do ator depois de completar a atualização anterior.
   Este passo garante que o proxy ator usa a pilha V2 de remoting.

3. Este passo é opcional. Mude o atributo anterior para remover o ouvinte V1.

    ```csharp
    [assembly:FabricTransportActorRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2,RemotingClientVersion = RemotingClientVersion.V2)]
    ```

## <a name="next-steps"></a>Passos seguintes

* [Gestão do estado do ator](service-fabric-reliable-actors-state-management.md)
* [Ciclo de vida do ator e coleção de lixo](service-fabric-reliable-actors-lifecycle.md)
* [Documentação de referência da API dos atores](/previous-versions/azure/dn971626(v=azure.100))
* [.NET código de amostra](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Código de amostra de Java](https://github.com/Azure-Samples/service-fabric-java-getting-started)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-platform/actor-service.png
[2]: ./media/service-fabric-reliable-actors-platform/app-deployment-scripts.png
[3]: ./media/service-fabric-reliable-actors-platform/actor-partition-info.png
[4]: ./media/service-fabric-reliable-actors-platform/actor-replica-role.png
[5]: ./media/service-fabric-reliable-actors-introduction/distribution.png
