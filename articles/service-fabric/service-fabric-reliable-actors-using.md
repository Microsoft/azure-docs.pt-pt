---
title: Implementar recursos em atores do Azure Service Fabric | Documentos da Microsoft
description: Descreve como escrever seu próprio serviço de ator que implementa os recursos de nível de serviço da mesma forma que, quando herda StatefulService.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: amanbha
ms.assetid: 45839a7f-0536-46f1-ae2b-8ba3556407fb
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/19/2018
ms.author: vturecek
ms.openlocfilehash: 5ab967cbd630447132300b22da5c5deb31fd50e9
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57852370"
---
# <a name="implement-service-level-features-in-your-actor-service"></a>Implementar recursos de nível de serviço no seu serviço de ator

Conforme descrito em [disposição em camadas de serviço](service-fabric-reliable-actors-platform.md#service-layering), o próprio serviço de ator é um serviço fiável. Pode escrever seu próprio serviço, que deriva de `ActorService`. Também pode implementar recursos de nível de serviço da mesma forma como quando herdar um serviço com estado, tais como:

- Serviço backup e restauração.
- Partilhado funcionalidade para todos os atores, por exemplo, um disjuntor automático.
- Chamadas de procedimento remoto no próprio serviço de ator e em cada ator individual.

## <a name="use-the-actor-service"></a>Utilizar o serviço de ator

Instâncias de ator tem acesso para o serviço de ator em que estão a executar. Através do serviço de ator, instâncias de ator por meio de programação podem obter o contexto de serviço. O contexto de serviço tem o ID de partição, o nome do serviço, o nome da aplicação e outras informações específicas da plataforma do Azure Service Fabric.

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

Como todos os serviços confiáveis, o serviço de ator tem de estar registado com um tipo de serviço no runtime do Service Fabric. Para o serviço de ator executar as instâncias de ator, o tipo de ator tem de estar registado também com o serviço de ator. O método de registo `ActorRuntime` realiza este trabalho para atores. No caso mais simples, pode registrar o tipo de ator e o serviço de ator, em seguida, utiliza as predefinições.

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

Em alternativa, pode utilizar um lambda fornecido pelo método de registo para construir o serviço de ator por conta própria. Em seguida, pode configurar o serviço de ator e construir explicitamente as instâncias de ator. Pode injetar dependências para o ator por meio de seu construtor.

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

## <a name="actor-service-methods"></a>Métodos de serviço de ator

A implementa do serviço de ator `IActorService` (c#) ou `ActorService` (Java), que por sua vez implementa `IService` (c#) ou `Service` (Java). Essa interface é utilizada por comunicação remota do Reliable Services, que permite chamadas de procedimento remoto em métodos de serviço. Ele contém métodos de nível de serviço que podem ser chamados remotamente por meio de comunicação remota do serviço. Pode usá-lo para [enumerar](service-fabric-reliable-actors-enumerate.md) e [eliminar](service-fabric-reliable-actors-delete-actors.md) atores.


## <a name="custom-actor-service"></a>Serviço de ator personalizado

Ao utilizar o lambda de registo de ator, pode registrar seu próprio serviço de ator personalizado que deriva `ActorService` (c#) e `FabricActorService` (Java). Em seguida, pode implementar sua própria funcionalidade de nível de serviço ao escrever uma classe de serviço que herda `ActorService` (c#) ou `FabricActorService` (Java). Um serviço de ator personalizado herda todas as funcionalidades do runtime de ator partir `ActorService` (c#) ou `FabricActorService` (Java). Ele pode ser usado para implementar seus próprios métodos de serviço.

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

## <a name="implement-actor-backup-and-restore"></a>Implementar a cópia de segurança de ator e restaurar

Um serviço de ator personalizado pode expor um método para fazer cópias de segurança de dados de ator, aproveitando o serviço de escuta de comunicação remota já está presente no `ActorService`. Por exemplo, veja [cópia de segurança e restaurar atores](service-fabric-reliable-actors-backup-and-restore.md).

## <a name="actor-that-uses-a-remoting-v2-interface-compatible-stack"></a>Ator que utiliza uma pilha de (compatível com a interface) de comunicação remota V2

A comunicação remota V2 (interface compatível, conhecido como V2_1) pilha possui todos os recursos da pilha de comunicação remota V2. Sua interface é compatível com a pilha de V1 de comunicação remota, mas não é compatível com versões anteriores com V2 e V1. Para atualizar da V1 V2_1 com nenhuma efeitos sobre a disponibilidade do serviço, siga os passos na secção seguinte.

As seguintes alterações são necessários para usar a pilha de V2_1 de comunicação remota:

1. Adicione o seguinte atributo de assembly em interfaces de ator.
  
   ```csharp
   [assembly:FabricTransportActorRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2_1,RemotingClientVersion = RemotingClientVersion.V2_1)]
   ```

2. Criar e atualizar o serviço de ator e projetos de cliente de ator para começar a utilizar a pilha de V2.

### <a name="actor-service-upgrade-to-remoting-v2-interface-compatible-stack-without-affecting-service-availability"></a>Atualização do serviço de ator a pilha de (compatível com a interface) de comunicação remota V2 sem afetar a disponibilidade do serviço

Esta alteração é uma atualização de dois passos. Siga os passos nesta sequência.

1. Adicione o seguinte atributo de assembly em interfaces de ator. Este atributo é iniciado dois serviços de escuta para o serviço de ator, V1 (existente) e o serviço de escuta V2_1. Atualize o serviço de ator com esta alteração.

   ```csharp
   [assembly:FabricTransportActorRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V1|RemotingListenerVersion.V2_1,RemotingClientVersion = RemotingClientVersion.V2_1)]
   ```

2. Atualize os clientes de ator depois de concluir a atualização anterior.
   Este passo certifica-se de que o proxy de ator utiliza a pilha de comunicação remota V2_1.

3. Este passo é opcional. Altere o atributo anterior para remover o serviço de escuta do V1.

    ```csharp
    [assembly:FabricTransportActorRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2_1,RemotingClientVersion = RemotingClientVersion.V2_1)]
    ```

## <a name="actor-that-uses-the-remoting-v2-stack"></a>Ator que utiliza a pilha de comunicação remota V2

Com o pacote de NuGet versão 2.8, os utilizadores agora podem utilizar a pilha de V2 de comunicação remota, que tem um desempenho melhor e fornece funcionalidades como a serialização personalizada. Gestão remota V2 não é retrocompatível com a pilha de comunicação remota existente (agora chamada de pilha de comunicação remota do V1).

As seguintes alterações são necessárias para usar a pilha de V2 de comunicação remota.

1. Adicione o seguinte atributo de assembly em interfaces de ator.

   ```csharp
   [assembly:FabricTransportActorRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2,RemotingClientVersion = RemotingClientVersion.V2)]
   ```

2. Criar e atualizar o serviço de ator e projetos de cliente de ator para começar a utilizar a pilha de V2.

### <a name="upgrade-the-actor-service-to-the-remoting-v2-stack-without-affecting-service-availability"></a>Atualizar o serviço de ator para a pilha de comunicação remota V2 sem afetar a disponibilidade do serviço

Esta alteração é uma atualização de dois passos. Siga os passos nesta sequência.

1. Adicione o seguinte atributo de assembly em interfaces de ator. Este atributo é iniciado dois serviços de escuta para o serviço de ator, V1 (existente) e o serviço de escuta do V2. Atualize o serviço de ator com esta alteração.

   ```csharp
   [assembly:FabricTransportActorRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V1|RemotingListenerVersion.V2,RemotingClientVersion = RemotingClientVersion.V2)]
   ```

2. Atualize os clientes de ator depois de concluir a atualização anterior.
   Este passo certifica-se de que o proxy de ator utiliza a pilha de comunicação remota V2.

3. Este passo é opcional. Altere o atributo anterior para remover o serviço de escuta do V1.

    ```csharp
    [assembly:FabricTransportActorRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2,RemotingClientVersion = RemotingClientVersion.V2)]
    ```

## <a name="next-steps"></a>Passos Seguintes

* [Gerenciamento de estado do ator](service-fabric-reliable-actors-state-management.md)
* [Coleção de ciclo de vida e libertação da memória de ator](service-fabric-reliable-actors-lifecycle.md)
* [Documentação de referência da API de atores](https://msdn.microsoft.com/library/azure/dn971626.aspx)
* [Código de exemplo do .NET](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Código de exemplo de Java](https://github.com/Azure-Samples/service-fabric-java-getting-started)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-platform/actor-service.png
[2]: ./media/service-fabric-reliable-actors-platform/app-deployment-scripts.png
[3]: ./media/service-fabric-reliable-actors-platform/actor-partition-info.png
[4]: ./media/service-fabric-reliable-actors-platform/actor-replica-role.png
[5]: ./media/service-fabric-reliable-actors-introduction/distribution.png
