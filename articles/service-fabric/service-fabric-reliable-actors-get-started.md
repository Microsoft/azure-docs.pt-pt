---
title: Criar um serviço baseado em atores no Azure Service Fabric | Documentos da Microsoft
description: Saiba como criar, depurar e implantar o seu primeiro serviço de ator com base no c# com o Reliable Actors do Service Fabric.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: ''
ms.assetid: d4aebe72-1551-4062-b1eb-54d83297f139
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/16/2018
ms.author: vturecek
ms.openlocfilehash: 5c21f1c9345fcf1b89afc3e1ee6b7f61ee79ddbe
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58168107"
---
# <a name="getting-started-with-reliable-actors"></a>Introdução ao Reliable Actors
> [!div class="op_single_selector"]
> * [C# no Windows](service-fabric-reliable-actors-get-started.md)
> * [Java em Linux](service-fabric-reliable-actors-get-started-java.md)

Este artigo explica-criar e depurar uma aplicação de Reliable Actor simples no Visual Studio. Para obter mais informações sobre Reliable Actors, consulte [introdução ao Service Fabric Reliable Actors](service-fabric-reliable-actors-introduction.md).

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, certifique-se de que tem o ambiente de desenvolvimento do Service Fabric, incluindo o Visual Studio, configurar no seu computador. Para obter detalhes, consulte [como configurar o ambiente de desenvolvimento](service-fabric-get-started.md).

## <a name="create-a-new-project-in-visual-studio"></a>Criar um novo projeto no Visual Studio

Inicie o Visual Studio 2015 ou posterior como um administrador e, em seguida, crie um novo **aplicação do Service Fabric** projeto:

![Ferramentas do Service Fabric para Visual Studio – novo projeto][1]

Na caixa de diálogo seguinte, escolha **serviço de Ator** sob **.NET Core 2.0** e introduza um nome para o serviço.

![Modelos de projeto do Service Fabric][5]

O projeto criado mostra a estrutura seguinte:

![Estrutura de projeto do Service Fabric][2]

## <a name="examine-the-solution"></a>Examinar a solução

A solução contém três projetos:

* **O projeto de aplicativo (MyApplication)**. Este projeto pacotes todos os serviços em conjunto para implementação. Contém os *Applicationmanifest* e scripts do PowerShell para gerir a aplicação.

* **O projeto de interface (HelloWorld.Interfaces)**. Este projeto contém a definição de interface para o ator. Interfaces de ator podem ser definidos em qualquer projeto com qualquer nome.  A interface define o contrato de ator que é partilhado pela implementação de ator e pelos clientes que chamam o ator.  Porque os projetos de cliente podem dependem dele, normalmente, faz sentido defini-lo num assembly separado da implementação de ator.

* **O projeto de serviço de ator (HelloWorld)**. Este projeto define o serviço do Service Fabric que irá alojar o ator. Contém a implementação de ator, *HelloWorld.cs*. Uma implementação de ator é uma classe que deriva do tipo base `Actor` e implementa as interfaces definidas no *MyActor.Interfaces* projeto. Uma classe de ator também tem de implementar um construtor que aceita uma `ActorService` instância e uma `ActorId` e os passa para a base `Actor` classe.
    
    Este projeto contém também *Program.cs*, que regista as classes de ator com o tempo de execução do Service Fabric utilizando `ActorRuntime.RegisterActorAsync<T>()`. O `HelloWorld` classe já está registrada. Quaisquer implementações de ator adicionais adicionadas ao projeto também devem ser registadas no `Main()` método.

## <a name="customize-the-helloworld-actor"></a>Personalizar o ator HelloWorld

O modelo de projeto define alguns métodos no `IHelloWorld` interface e implementa-as no `HelloWorld` implementação de ator.  Substitua esses métodos para que o serviço de ator devolve uma cadeia de caracteres "Hello World" simples.

Na *HelloWorld.Interfaces* , além de projeto a *IHelloWorld.cs* do ficheiro, substitua a definição de interface da seguinte forma:

```csharp
public interface IHelloWorld : IActor
{
    Task<string> GetHelloWorldAsync();
}
```

Na **Olámundo** , além do projeto **HelloWorld.cs**, substitua a definição de classe inteira da seguinte forma:

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

Prima **Ctrl-Shift-B** compilar o projeto e certifique-se de tudo o que é compilado.

## <a name="add-a-client"></a>Adicionar um cliente

Crie uma aplicação de consola simples para chamar o serviço de ator.

1. Com o botão direito na solução no Explorador de soluções > **Add** > **novo projeto...** .

2. Sob o **.NET Core** tipos de projetos, escolha **aplicação de consola (.NET Core)**.  Nomeie o projeto *ActorClient*.
    
    ![Adicionar a caixa de diálogo novo projeto][6]    
    
    > [!NOTE]
    > Uma aplicação de consola não é o tipo de aplicação que normalmente seria usada como um cliente nos recursos de infraestrutura do serviço, mas faz um exemplo prático para depuração e teste com o cluster do Service Fabric local.

3. A aplicação de consola tem de ser uma aplicação de 64 bits para manter a compatibilidade com o projeto de interface e outras dependências.  No Solution Explorer, clique com botão direito a **ActorClient** projeto e, em seguida, clique em **propriedades**.  Sobre o **crie** separador, defina **destino de plataforma** para **x64**.
    
    ![Criar propriedades][8]

4. O projeto de cliente requer o pacote de NuGet dos reliable actors.  clique em **Ferramentas** > **Gestor de Pacotes NuGet** > **Consola de Gestor de Pacotes**.  Na consola do Gestor de pacotes, introduza o seguinte comando:
    
    ```powershell
    Install-Package Microsoft.ServiceFabric.Actors -IncludePrerelease -ProjectName ActorClient
    ```

    O pacote NuGet e todas as suas dependências estão instaladas no projeto ActorClient.

5. O projeto de cliente também requer uma referência ao projeto de interfaces.  No projeto ActorClient, com o botão direito **dependências** e, em seguida, clique em **Adicionar referência...** .  Selecione **projetos > solução** (se não estiver já selecionada) e, em seguida, assinale a caixa de verificação junto a **HelloWorld.Interfaces**.  Clique em **OK**.
    
    ![Adicionar a caixa de diálogo de referência][7]

6. No projeto ActorClient, substitua todo o conteúdo do *Program.cs* com o código a seguir:
    
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

## <a name="running-and-debugging"></a>Executando e depurando

Prima **F5** para criar, implementar e executar a aplicação localmente no cluster de desenvolvimento do Service Fabric.  Durante o processo de implantação, pode ver o progresso no **saída** janela.

![Janela de saída de depuração Service Fabric][3]

Quando a saída contém o texto, *a aplicação está pronta*, é possível testar o serviço usando o aplicativo ActorClient.  No Explorador de soluções, faça duplo clique no **ActorClient** do projeto, em seguida, clique em **depurar** > **iniciar nova instância**.  A aplicação de linha de comandos deve exibir a saída do serviço de ator.

![Saída da aplicação][9]

> [!TIP]
> O tempo de execução de Atores do Service Fabric emite algumas [eventos e contadores de desempenho relacionados com métodos de ator](service-fabric-reliable-actors-diagnostics.md#actor-method-events-and-performance-counters). Eles são úteis no diagnóstico e monitorização de desempenho.

## <a name="next-steps"></a>Passos Seguintes
Saiba mais sobre [como os Reliable Actors utilizam a plataforma do Service Fabric](service-fabric-reliable-actors-platform.md).


[1]: ./media/service-fabric-reliable-actors-get-started/reliable-actors-newproject.PNG
[2]: ./media/service-fabric-reliable-actors-get-started/reliable-actors-projectstructure.PNG
[3]: ./media/service-fabric-reliable-actors-get-started/debugging-output.PNG
[4]: ./media/service-fabric-reliable-actors-get-started/vs-context-menu.png
[5]: ./media/service-fabric-reliable-actors-get-started/reliable-actors-newproject1.PNG
[6]: ./media/service-fabric-reliable-actors-get-started/new-console-app.png
[7]: ./media/service-fabric-reliable-actors-get-started/add-reference.png
[8]: ./media/service-fabric-reliable-actors-get-started/build-props.png
[9]: ./media/service-fabric-reliable-actors-get-started/app-output.png