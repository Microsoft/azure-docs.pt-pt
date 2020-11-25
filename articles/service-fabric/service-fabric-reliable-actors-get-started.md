---
title: Criar um serviço baseado em atores na Azure Service Fabric
description: Aprenda a criar, depurar e implementar o seu primeiro serviço baseado em atores em C# usando Service Fabric Reliable Actors.
author: vturecek
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: vturecek
ms.custom: devx-track-csharp
ms.openlocfilehash: f775608c6c349c93a43ef4aef26ad50707cd39b7
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96023623"
---
# <a name="getting-started-with-reliable-actors"></a>Começar com atores fiáveis
> [!div class="op_single_selector"]
> * [C# no Windows](service-fabric-reliable-actors-get-started.md)
> * [Java em Linux](./service-fabric-create-your-first-linux-application-with-java.md)

Este artigo percorre a criação e depuragem de uma simples aplicação de Ator Fiável no Estúdio Visual. Para obter mais informações sobre atores fiáveis, consulte [Introdução ao Tecido de Serviço Fiáveis Atores.](service-fabric-reliable-actors-introduction.md)

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, certifique-se de que tem o ambiente de desenvolvimento do Service Fabric, incluindo o Visual Studio, montado na sua máquina. Para mais detalhes, [consulte como configurar o ambiente de desenvolvimento.](service-fabric-get-started.md)

## <a name="create-a-new-project-in-visual-studio"></a>Criar um novo projeto no Visual Studio

Lançar Visual Studio 2019 ou mais tarde como administrador, e depois criar um novo projeto **de Aplicação de Tecido de Serviço:**

![Ferramentas de tecido de serviço para Visual Studio - novo projeto][1]

Na caixa de diálogo seguinte, escolha **o Serviço de Ator** em **.NET Core 2.0** e introduza um nome para o serviço.

![Modelos de projeto de tecido de serviço][5]

O projeto criado mostra a seguinte estrutura:

![Estrutura de projeto de tecido de serviço][2]

## <a name="examine-the-solution"></a>Examinar a solução

A solução contém três projetos:

* **O projeto de aplicação (MyApplication)**. Este projeto embala todos os serviços em conjunto para implantação. Contém os *scriptsApplicationManifest.xml* e PowerShell para gerir a aplicação.

* **O projeto de interface (HelloWorld.Interfaces)**. Este projeto contém a definição de interface para o ator. As interfaces dos atores podem ser definidas em qualquer projeto com qualquer nome.  A interface define o contrato de ator que é partilhado pela implementação do ator e pelos clientes que chamam o ator.  Como os projetos de clientes podem depender disso, normalmente faz sentido defini-lo numa assembléia que é separada da implementação do ator.

* **O projeto de serviço de ator (HelloWorld)**. Este projeto define o serviço Service Fabric que vai acolher o ator. Contém a implementação do ator, *HelloWorld.cs*. Uma implementação do ator é uma classe que deriva do tipo base `Actor` e implementa as interfaces definidas no projeto *MyActor.Interfaces.* Uma classe de ator também deve implementar um construtor que aceite um `ActorService` caso e um e `ActorId` passá-lo para a `Actor` classe base.
    
    Este projeto também contém *Program.cs,* que regista aulas de ator com o tempo de execução do Service Fabric `ActorRuntime.RegisterActorAsync<T>()` utilizando. A `HelloWorld` turma já está registada. Quaisquer implementações adicionais de atores adicionadas ao projeto também devem ser registadas no `Main()` método.

## <a name="customize-the-helloworld-actor"></a>Personalize o ator HelloWorld

O modelo do projeto define alguns métodos na `IHelloWorld` interface e implementa-os na implementação do `HelloWorld` ator.  Substitua estes métodos de modo a que o serviço do ator devolva uma simples corda "Hello World".

No projeto *HelloWorld.Interfaces,* no ficheiro *IHelloWorld.cs,* substitua a definição de interface da seguinte forma:

```csharp
public interface IHelloWorld : IActor
{
    Task<string> GetHelloWorldAsync();
}
```

No projeto **HelloWorld,** em **HelloWorld.cs,** substitua toda a definição de classe da seguinte forma:

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

Pressione **ctrl-Shift-B** para construir o projeto e certifique-se de que tudo se compila.

## <a name="add-a-client"></a>Adicionar um cliente

Crie uma aplicação de consola simples para ligar para o serviço de ator.

1. Clique com o botão direito na solução no Solution Explorer > **Add**  >  **New Project...**.

2. Nos tipos de projeto **.NET Core,** escolha **a App consola (.NET Core)**.  Nomeie o projeto *ActorClient*.
    
    ![Adicione o diálogo do novo projeto][6]    
    
    > [!NOTE]
    > Uma aplicação de consola não é o tipo de aplicação que normalmente usaria como cliente em Service Fabric, mas constitui um exemplo conveniente para depurar e testar usando o cluster local de Tecido de Serviço.

3. A aplicação da consola deve ser uma aplicação de 64 bits para manter a compatibilidade com o projeto de interface e outras dependências.  No Solution Explorer, clique com o botão direito no projeto **ActorClient** e, em seguida, clique em **Propriedades**.  No separador **Build,** desaponte o **alvo da plataforma** para **x64**.
    
    ![Construir propriedades][8]

4. O projeto do cliente requer o pacote de atores confiáveis NuGet.  Clique em **Ferramentas**  >  **NuGet Package Manager** Package Manager  >  **Consola**.  Na Consola Gestor de Pacotes, insira o seguinte comando:
    
    ```powershell
    Install-Package Microsoft.ServiceFabric.Actors -IncludePrerelease -ProjectName ActorClient
    ```

    O pacote NuGet e todas as suas dependências estão instalados no projeto ActorClient.

5. O projeto do cliente também requer uma referência ao projeto interfaces.  No projeto ActorClient, clique à direita **Em Dependencies** e, em seguida, clique em **Adicionar Referência do Projeto...**.  Selecione **Projetos > Solução** (se ainda não selecionada) e, em seguida, marque a caixa de verificação ao lado **da HelloWorld.Interfaces**.  Clique em **OK**.
    
    ![Adicionar diálogo de referência][7]

6. No projeto ActorClient, substitua todo o conteúdo da *Program.cs* pelo seguinte código:
    
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

## <a name="running-and-debugging"></a>Correr e depurar

Pressione **f5** para construir, implementar e executar a aplicação localmente no cluster de desenvolvimento do Tecido de Serviço.  Durante o processo de implantação, pode ver o progresso na janela **de saída.**

![Janela de depuragem de tecido de serviço][3]

Quando a saída contém o texto, *a aplicação está pronta,* é possível testar o serviço utilizando a aplicação ActorClient.  No Solution Explorer, clique à direita no projeto **ActorClient** e, em seguida, clique em **Debug**  >  **Iniciar nova instância**.  A aplicação da linha de comando deve exibir a saída do serviço de ator.

![Saída da aplicação][9]

> [!TIP]
> O tempo de execução do Service Fabric Actors emite alguns [eventos e contadores de desempenho relacionados com os métodos do ator.](service-fabric-reliable-actors-diagnostics.md#actor-method-events-and-performance-counters) São úteis em diagnósticos e monitorização de desempenho.

## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre [como os Atores Fiáveis usam a plataforma Service Fabric.](service-fabric-reliable-actors-platform.md)


[1]: ./media/service-fabric-reliable-actors-get-started/reliable-actors-newproject.PNG
[2]: ./media/service-fabric-reliable-actors-get-started/reliable-actors-projectstructure.PNG
[3]: ./media/service-fabric-reliable-actors-get-started/debugging-output.PNG
[4]: ./media/service-fabric-reliable-actors-get-started/vs-context-menu.png
[5]: ./media/service-fabric-reliable-actors-get-started/reliable-actors-newproject1.PNG
[6]: ./media/service-fabric-reliable-actors-get-started/new-console-app.png
[7]: ./media/service-fabric-reliable-actors-get-started/add-reference.png
[8]: ./media/service-fabric-reliable-actors-get-started/build-props.png
[9]: ./media/service-fabric-reliable-actors-get-started/app-output.png
