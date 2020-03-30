---
title: Criar um serviço baseado em ator no Azure Service Fabric
description: Aprenda a criar, depurar e implementar o seu primeiro serviço baseado em atores em C# usando Atores Fiáveis de Tecido de Serviço.
author: vturecek
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: vturecek
ms.openlocfilehash: a6e4fb48653572139463738c82de632ff7d55074
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75466249"
---
# <a name="getting-started-with-reliable-actors"></a>Começar com atores confiáveis
> [!div class="op_single_selector"]
> * [C# no Windows](service-fabric-reliable-actors-get-started.md)
> * [Java em Linux](service-fabric-reliable-actors-get-started-java.md)

Este artigo passa pela criação e depuração de uma simples aplicação de Ator Fiável no Estúdio Visual. Para obter mais informações sobre atores fiáveis, consulte [Introdução a Atores Fiáveis](service-fabric-reliable-actors-introduction.md)de Tecido de Serviço.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, certifique-se de que tem o ambiente de desenvolvimento do Tecido de Serviço, incluindo o Estúdio Visual, configurado na sua máquina. Para mais detalhes, veja [como configurar o ambiente](service-fabric-get-started.md)de desenvolvimento.

## <a name="create-a-new-project-in-visual-studio"></a>Criar um novo projeto no Estúdio Visual

Lançar o Visual Studio 2019 ou mais tarde como administrador, e depois criar um novo projeto de **Aplicação de Tecidos de Serviço:**

![Ferramentas de Tecido de Serviço para Estúdio Visual - novo projeto][1]

Na caixa de diálogo seguinte, escolha **o Ator Service** em **.NET Core 2.0** e introduza um nome para o serviço.

![Modelos de projeto de tecido de serviço][5]

O projeto criado mostra a seguinte estrutura:

![Estrutura de projeto de tecido de serviço][2]

## <a name="examine-the-solution"></a>Examinar a solução

A solução contém três projetos:

* **O projeto de candidatura (MyApplication)**. Este projeto embala todos os serviços em conjunto para implantação. Contém os scripts *ApplicationManifest.xml* e PowerShell para a gestão da aplicação.

* **O projeto de interface (HelloWorld.Interfaces)**. Este projeto contém a definição de interface para o ator. As interfaces do ator podem ser definidas em qualquer projeto com qualquer nome.  A interface define o contrato do ator que é partilhado pela implementação do ator e pelos clientes que chamam o ator.  Como os projetos de clientes podem depender disso, normalmente faz sentido defini-lo numa montagem separada da implementação do ator.

* O projeto de serviço de **ator (HelloWorld)**. Este projeto define o serviço de Tecido de Serviço que vai acolher o ator. Contém a implementação do ator, *HelloWorld.cs.* Uma implementação de ator é uma `Actor` classe que deriva do tipo base e implementa as interfaces definidas no projeto *MyActor.Interfaces.* Uma classe de ator também deve implementar `ActorService` um `ActorId` construtor que aceite `Actor` uma instância e um e passá-los para a classe base.
    
    Este projeto também contém *Program.cs,* que regista aulas de `ActorRuntime.RegisterActorAsync<T>()`ator com o tempo de execução do Tecido de Serviço utilizando. A `HelloWorld` aula já está registada. Quaisquer implementações adicionais de ator adicionadas `Main()` ao projeto também devem ser registadas no método.

## <a name="customize-the-helloworld-actor"></a>Personalize o ator helloWorld

O modelo de projeto define `IHelloWorld` alguns métodos `HelloWorld` na interface e implementa-os na implementação do ator.  Substitua esses métodos para que o serviço de ator derredelhe uma simples corda "Hello World".

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

Pressione **o CTRL-Shift-B** para construir o projeto e certificar-se de que tudo se compila.

## <a name="add-a-client"></a>Adicione um cliente

Crie uma aplicação simples de consola para chamar o serviço de ator.

1. Clique à direita na solução no Solution Explorer > **Adicionar** > **Novo Projeto...**.

2. Sob os tipos de projeto **.NET Core,** escolha **App consola (.NET Core)**.  Nomeie o projeto *ActorClient*.
    
    ![Adicionar novo diálogo de projeto][6]    
    
    > [!NOTE]
    > Uma aplicação de consola não é o tipo de aplicação que normalmente usaria como cliente em Service Fabric, mas é um exemplo conveniente para depuração e teste usando o cluster de Tecido de Serviço local.

3. A aplicação da consola deve ser uma aplicação de 64 bits para manter a compatibilidade com o projeto da interface e outras dependências.  No Solution Explorer, clique no projeto **ActorClient** e, em seguida, clique em **Propriedades**.  No separador **Build,** defina o **alvo da plataforma** para **x64**.
    
    ![Construir propriedades][8]

4. O projeto do cliente requer o pacote nuGet de atores confiáveis.  Clique em **ferramentas** > **nuget pacote manager** > **consola**.  Na consola do gestor de pacotes, insira o seguinte comando:
    
    ```powershell
    Install-Package Microsoft.ServiceFabric.Actors -IncludePrerelease -ProjectName ActorClient
    ```

    O pacote NuGet e todas as suas dependências estão instalados no projeto ActorClient.

5. O projeto do cliente também requer uma referência ao projeto interfaces.  No projeto ActorClient, clique direito **Dependências** e, em seguida, clique em **Adicionar referência...**.  Selecione **Projetos > Solução** (se ainda não selecionado) e, em seguida, marque a caixa de verificação ao lado da **HelloWorld.Interfaces**.  Clique em **OK**.
    
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

## <a name="running-and-debugging"></a>Corrida e depuração

Pressione **f5** para construir, implementar e executar a aplicação localmente no cluster de desenvolvimento de Tecido de Serviço.  Durante o processo de implantação, pode ver o progresso na janela **de saída.**

![Janela de saída de depuração de tecido de serviço][3]

Quando a saída contém o texto, *a aplicação está pronta,* é possível testar o serviço utilizando a aplicação ActorClient.  No Solution Explorer, clique à direita no projeto **ActorClient** e, em seguida, clique em **Debug** > **Start nova instância**.  A aplicação da linha de comando deve exibir a saída do serviço do ator.

![Saída de candidatura][9]

> [!TIP]
> O tempo de execução de Service Fabric Actors emite [alguns eventos e contadores de desempenho relacionados com métodos de ator.](service-fabric-reliable-actors-diagnostics.md#actor-method-events-and-performance-counters) São úteis em diagnósticos e monitorização de desempenho.

## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre [como os Atores Fiáveis usam a plataforma Service Fabric](service-fabric-reliable-actors-platform.md).


[1]: ./media/service-fabric-reliable-actors-get-started/reliable-actors-newproject.PNG
[2]: ./media/service-fabric-reliable-actors-get-started/reliable-actors-projectstructure.PNG
[3]: ./media/service-fabric-reliable-actors-get-started/debugging-output.PNG
[4]: ./media/service-fabric-reliable-actors-get-started/vs-context-menu.png
[5]: ./media/service-fabric-reliable-actors-get-started/reliable-actors-newproject1.PNG
[6]: ./media/service-fabric-reliable-actors-get-started/new-console-app.png
[7]: ./media/service-fabric-reliable-actors-get-started/add-reference.png
[8]: ./media/service-fabric-reliable-actors-get-started/build-props.png
[9]: ./media/service-fabric-reliable-actors-get-started/app-output.png