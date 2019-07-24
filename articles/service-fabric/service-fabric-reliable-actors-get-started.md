---
title: Criar um serviço baseado em ator no Azure Service Fabric | Microsoft Docs
description: Saiba como criar, depurar e implantar seu primeiro serviço baseado em C# ator usando o Service Fabric Reliable Actors.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: chackdan
editor: ''
ms.assetid: d4aebe72-1551-4062-b1eb-54d83297f139
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/10/2019
ms.author: vturecek
ms.openlocfilehash: d870690416f96a2e1c24e6de16bdc8faa060f6bd
ms.sourcegitcommit: 920ad23613a9504212aac2bfbd24a7c3de15d549
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/15/2019
ms.locfileid: "68225184"
---
# <a name="getting-started-with-reliable-actors"></a>Introdução ao Reliable Actors
> [!div class="op_single_selector"]
> * [C# no Windows](service-fabric-reliable-actors-get-started.md)
> * [Java em Linux](service-fabric-reliable-actors-get-started-java.md)

Este artigo orienta a criação e a depuração de um aplicativo simples de ator confiável no Visual Studio. Para obter mais informações sobre Reliable Actors, consulte [introdução ao Service Fabric Reliable Actors](service-fabric-reliable-actors-introduction.md).

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, verifique se você tem o ambiente de desenvolvimento Service Fabric, incluindo o Visual Studio, configurado no seu computador. Para obter detalhes, consulte [como configurar o ambiente de desenvolvimento](service-fabric-get-started.md).

## <a name="create-a-new-project-in-visual-studio"></a>Criar um novo projeto no Visual Studio

Inicie o Visual Studio 2019 ou posterior como administrador e, em seguida, crie um novo projeto de **aplicativo Service Fabric** :

![Ferramentas de Service Fabric para Visual Studio – novo projeto][1]

Na próxima caixa de diálogo, escolha **serviço de ator** em **.NET Core 2,0** e insira um nome para o serviço.

![Modelos de projeto Service Fabric][5]

O projeto criado mostra a seguinte estrutura:

![Estrutura do projeto Service Fabric][2]

## <a name="examine-the-solution"></a>Examinar a solução

A solução contém três projetos:

* **O projeto de aplicativo (MyApplication)** . Este projeto empacota todos os serviços juntos para implantação. Ele contém os scripts *ApplicationManifest. xml* e PowerShell para gerenciar o aplicativo.

* **O projeto de interface (HelloWorld. Interfaces)** . Este projeto contém a definição de interface para o ator. As interfaces de ator podem ser definidas em qualquer projeto com qualquer nome.  A interface define o contrato de ator que é compartilhado pela implementação do ator e os clientes que chamam o ator.  Como os projetos de cliente podem depender dele, normalmente faz sentido defini-lo em um assembly separado da implementação do ator.

* **O projeto de serviço de ator (HelloWorld)** . Esse projeto define o serviço de Service Fabric que vai hospedar o ator. Ele contém a implementação do ator, *HelloWorld.cs*. Uma implementação de ator é uma classe derivada do tipo `Actor` base e implementa as interfaces definidas no projeto MyActor *. interfaces* . Uma classe de ator também deve implementar um construtor que aceite `ActorService` uma instância e `ActorId` um e as passe para a `Actor` classe base.
    
    Esse projeto também contém *Program.cs*, que registra as classes de ator com o tempo `ActorRuntime.RegisterActorAsync<T>()`de execução Service Fabric usando. A `HelloWorld` classe já está registrada. Todas as implementações de ator adicionais adicionadas ao projeto também devem ser `Main()` registradas no método.

## <a name="customize-the-helloworld-actor"></a>Personalizar o ator HelloWorld

O modelo de projeto define alguns métodos na `IHelloWorld` interface e os implementa na implementação `HelloWorld` do ator.  Substitua esses métodos para que o serviço de ator retorne uma cadeia de caracteres "Olá, Mundo" simples.

No projeto *HelloWorld. interfaces* , no arquivo *IHelloWorld.cs* , substitua a definição da interface da seguinte maneira:

```csharp
public interface IHelloWorld : IActor
{
    Task<string> GetHelloWorldAsync();
}
```

No projeto **HelloWorld** , no **HelloWorld.cs**, substitua a definição de classe inteira da seguinte maneira:

```csharp
[StatePersistence(StatePersistence.Persisted)]
internal class HelloWorld : Actor, IHelloWorld
{
    public HelloWorld(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public Task<string> GetHelloWorldAsync()
    {
        return Task.FromResult("Hello from my reliable actor!");
    }
}
```

Pressione **Ctrl-Shift-B** para compilar o projeto e verificar se tudo está compilado.

## <a name="add-a-client"></a>Adicionar um cliente

Crie um aplicativo de console simples para chamar o serviço de ator.

1. Clique com o botão direito do mouse na solução em Gerenciador de soluções > **Adicionar** > **novo projeto...** .

2. Nos tipos de projeto do **.NET Core** , escolha **aplicativo de console (.NET Core)** .  Nomeie o projeto *ActorClient*.
    
    ![Caixa de diálogo Adicionar novo projeto][6]    
    
    > [!NOTE]
    > Um aplicativo de console não é o tipo de aplicativo que você normalmente usaria como um cliente no Service Fabric, mas ele cria um exemplo conveniente para depuração e teste usando o cluster de Service Fabric local.

3. O aplicativo de console deve ser um aplicativo de 64 bits para manter a compatibilidade com o projeto de interface e outras dependências.  Em Gerenciador de Soluções, clique com o botão direito do mouse no projeto **ActorClient** e clique em **Propriedades**.  Na guia **Compilar** , defina o **destino da plataforma** como **x64**.
    
    ![Propriedades da compilação][8]

4. O projeto cliente requer o pacote NuGet dos Reliable Actors.  clique em **Ferramentas** > **Gestor de Pacotes NuGet** > **Consola de Gestor de Pacotes**.  No console do Gerenciador de pacotes, digite o seguinte comando:
    
    ```powershell
    Install-Package Microsoft.ServiceFabric.Actors -IncludePrerelease -ProjectName ActorClient
    ```

    O pacote NuGet e todas as suas dependências são instalados no projeto ActorClient.

5. O projeto cliente também requer uma referência ao projeto interfaces.  No projeto ActorClient, clique com o botão  direito do mouse em dependências e clique em **Adicionar referência..** ..  Selecione **projetos > solução** (se ainda não estiver selecionada) e, em seguida, marque a caixa de seleção ao lado de **HelloWorld. interfaces**.  Clique em **OK**.
    
    ![Caixa de diálogo Adicionar referência][7]

6. No projeto ActorClient, substitua todo o conteúdo de *Program.cs* pelo código a seguir:
    
    ```csharp
    using System;
    using System.Threading.Tasks;
    using Microsoft.ServiceFabric.Actors;
    using Microsoft.ServiceFabric.Actors.Client;
    using HelloWorld.Interfaces;
    
    namespace ActorClient
    {
        class Program
        {
            static void Main(string[] args)
            {
                IHelloWorld actor = ActorProxy.Create<IHelloWorld>(ActorId.CreateRandom(), new Uri("fabric:/MyApplication/HelloWorldActorService"));
                Task<string> retval = actor.GetHelloWorldAsync();
                Console.Write(retval.Result);
                Console.ReadLine();
            }
        }
    }
    ```

## <a name="running-and-debugging"></a>Executando e Depurando

Pressione **F5** para compilar, implantar e executar o aplicativo localmente no cluster de desenvolvimento Service Fabric.  Durante o processo de implantação, você pode ver o progresso na janela de **saída** .

![Janela de saída de depuração Service Fabric][3]

Quando a saída contém o texto, *o aplicativo está pronto*, é possível testar o serviço usando o aplicativo ActorClient.  Em Gerenciador de soluções, clique com o botão direito do mouse no projeto **ActorClient** e clique em **depurar** > **Iniciar nova instância**.  O aplicativo de linha de comando deve exibir a saída do serviço de ator.

![Saída do aplicativo][9]

> [!TIP]
> O tempo de execução de Service Fabric atores emite alguns [eventos e contadores de desempenho relacionados aos métodos de ator](service-fabric-reliable-actors-diagnostics.md#actor-method-events-and-performance-counters). Eles são úteis no diagnóstico e no monitoramento de desempenho.

## <a name="next-steps"></a>Passos Seguintes
Saiba mais sobre [como Reliable Actors usar a plataforma Service Fabric](service-fabric-reliable-actors-platform.md).


[1]: ./media/service-fabric-reliable-actors-get-started/reliable-actors-newproject.PNG
[2]: ./media/service-fabric-reliable-actors-get-started/reliable-actors-projectstructure.PNG
[3]: ./media/service-fabric-reliable-actors-get-started/debugging-output.PNG
[4]: ./media/service-fabric-reliable-actors-get-started/vs-context-menu.png
[5]: ./media/service-fabric-reliable-actors-get-started/reliable-actors-newproject1.PNG
[6]: ./media/service-fabric-reliable-actors-get-started/new-console-app.png
[7]: ./media/service-fabric-reliable-actors-get-started/add-reference.png
[8]: ./media/service-fabric-reliable-actors-get-started/build-props.png
[9]: ./media/service-fabric-reliable-actors-get-started/app-output.png