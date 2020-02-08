---
title: Crie seu primeiro aplicativo Service Fabric noC#
description: Introdução à criação de uma aplicação Microsoft Azure Service Fabric com serviços apátridas e apátridas.
ms.topic: conceptual
ms.date: 07/10/2019
ms.custom: sfrev
ms.openlocfilehash: 15dd9bf6ac19bdac7bc8b50fc70e0b3b0a4e9a83
ms.sourcegitcommit: cfbea479cc065c6343e10c8b5f09424e9809092e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/08/2020
ms.locfileid: "77083744"
---
# <a name="get-started-with-reliable-services"></a>Introdução ao Reliable Services

> [!div class="op_single_selector"]
> * [C# no Windows](service-fabric-reliable-services-quick-start.md)
> * [Java em Linux](service-fabric-reliable-services-quick-start-java.md)

Um aplicativo de Service Fabric do Azure contém um ou mais serviços que executam seu código. Este guia mostra-lhe como criar aplicações apátridas e apátridas de Tecido de Serviço com [Serviços Fiáveis.](service-fabric-reliable-services-introduction.md)  

## <a name="basic-concepts"></a>Conceitos básicos

Para começar com serviços fiáveis, basta compreender alguns conceitos básicos:

* **Tipo**de serviço : Esta é a sua implementação de serviço. É definido pela classe que escreve que se estende `StatelessService` e qualquer outro código ou dependências nele utilizados, juntamente com um nome e um número de versão.
* **Caso de serviço nomeado**: Para executar o seu serviço, cria instâncias nomeadas do seu tipo de serviço, tal como cria situações de objetos de tipo de classe. Uma instância de serviço tem um nome na forma de um URI usando a "Fabric:/" esquema, como "Fabric:/MyApp/MyService".
* **Anfitrião de serviço**: As instâncias de serviço nomeadas que cria precisam de ser executadas dentro de um processo de hospedar. O anfitrião do serviço é apenas um processo onde as instâncias do seu serviço podem ser executadas.
* **Inscrição de serviço:** O registo reúne tudo. O tipo de serviço deve ser registado com o tempo de funcionar do Tecido de Serviço num hospedeiro de serviço para permitir que o Tecido de Serviço crie instâncias do mesmo.  

## <a name="create-a-stateless-service"></a>Criar um serviço apátrida

Um serviço sem estado é um tipo de serviço que é atualmente a norma em aplicativos em nuvem. Ele é considerado sem estado porque o serviço em si não contém dados que precisam ser armazenados de maneira confiável ou tornarem-se altamente disponíveis. Se uma instância de um serviço sem estado for desligada, todo o seu estado interno será perdido. Nesse tipo de serviço, o estado deve ser persistido em um repositório externo, como tabelas do Azure ou um banco de dados SQL, para que ele se tornará altamente disponível e confiável.

Lançar o Visual Studio 2017 ou o Visual Studio 2019 como administrador, e criar um novo projeto de aplicação Service Fabric chamado *HelloWorld*:

![Use a caixa de diálogo novo projeto para criar um novo aplicativo Service Fabric](media/service-fabric-reliable-services-quick-start/hello-stateless-NewProject.png)

Em seguida, crie um projeto de serviço apátrida utilizando **.NET Core 2.0** chamado *HelloWorldStateless*:

![Na segunda caixa de diálogo, crie um projeto de serviço sem estado](media/service-fabric-reliable-services-quick-start/hello-stateless-NewProject2.png)

Sua solução agora contém dois projetos:

* *HelloWorld.* Este é o projeto de *candidatura* que contém os seus *serviços.* Ele também contém o manifesto do aplicativo que descreve o aplicativo, bem como vários scripts do PowerShell que ajudam você a implantar seu aplicativo.
* *HelloWorldStateless.* Este é o projeto de serviço. Ele contém a implementação do serviço sem estado.

## <a name="implement-the-service"></a>Implementar o serviço

Abra o ficheiro **HelloWorldStateless.cs** no projeto de serviço. No Service Fabric, um serviço pode executar qualquer lógica de negócios. O serviço API fornece dois pontos de entrada para o seu código:

* Um método de ponto de entrada aberto, chamado *RunAsync,* onde pode começar a executar quaisquer cargas de trabalho, incluindo cargas de trabalho de computação de longa duração.

```csharp
protected override async Task RunAsync(CancellationToken cancellationToken)
{
    ...
}
```

* Um ponto de entrada de comunicação no qual você pode conectar sua pilha de comunicação de sua escolha, como ASP.NET Core. É aqui que pode começar a receber pedidos de utilizadores e outros serviços.

```csharp
protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
{
    ...
}
```

Neste tutorial, vamos focar-nos no método `RunAsync()` ponto de entrada. É aqui que pode começar imediatamente a executar o seu código.
O modelo do projeto inclui uma implementação de amostra de `RunAsync()` que incrementa uma contagem de rolos.

> [!NOTE]
> Para mais detalhes sobre como trabalhar com uma pilha de comunicações, consulte [a comunicação do Serviço com ASP.NET Core](service-fabric-reliable-services-communication-aspnetcore.md)

### <a name="runasync"></a>RunAsync

```csharp
protected override async Task RunAsync(CancellationToken cancellationToken)
{
    // TODO: Replace the following sample code with your own logic
    //       or remove this RunAsync override if it's not needed in your service.

    long iterations = 0;

    while (true)
    {
        cancellationToken.ThrowIfCancellationRequested();

        ServiceEventSource.Current.ServiceMessage(this.Context, "Working-{0}", ++iterations);

        await Task.Delay(TimeSpan.FromSeconds(1), cancellationToken);
    }
}
```

A plataforma chama a este método quando uma instância de um serviço é colocada e pronta a ser executada. Para um serviço sem estado, isso simplesmente significa quando a instância do serviço é aberta. É fornecido um símbolo de cancelamento para coordenar quando a sua instância de serviço precisa de ser fechada. No Tecido de Serviço, este ciclo aberto/próximo de uma instância de serviço pode ocorrer muitas vezes ao longo da vida do serviço como um todo. Isto pode acontecer por várias razões, incluindo:

* O sistema move as suas instâncias de serviço para o equilíbrio de recursos.
* As falhas ocorrem no seu código.
* A aplicação ou sistema é atualizado.
* O hardware subjacente experimenta uma paragem.

Essa orquestração é gerenciada pelo sistema para manter seu serviço altamente disponível e equilibrado corretamente.

`RunAsync()` não deve bloquear sincronizadamente. Sua implementação de RunAsync deve retornar uma tarefa ou Await em operações de execução longa ou de bloqueio para permitir que o tempo de execução continue. Nota no ciclo `while(true)` no exemplo anterior, é utilizado um `await Task.Delay()` de retorno de tarefas. Se a sua carga de trabalho tiver de bloquear sincronizadamente, deverá agendar uma nova Tarefa com `Task.Run()` na sua `RunAsync` implementação.

O cancelamento da sua carga de trabalho é um esforço de cooperação orquestrado pelo símbolo de cancelamento fornecido. O sistema aguardará que a tarefa seja encerrada (com êxito na conclusão, cancelamento ou falha) antes de se mover. É importante honrar o sinal de cancelamento, terminar qualquer trabalho e sair `RunAsync()` o mais rápido possível quando o sistema solicitar o cancelamento.

Neste exemplo de serviço apátrida, a contagem é armazenada numa variável local. Mas como este é um serviço apátrida, o valor armazenado só existe para o ciclo de vida atual da sua instância de serviço. Quando o serviço se move ou reinicia, o valor perde-se.

## <a name="create-a-stateful-service"></a>Criar um serviço imponente

A Service Fabric introduz um novo tipo de serviço que é imponente. Um serviço imponente pode manter o estado de forma fiável dentro do próprio serviço, co-localizado com o código que o está a usar. O Estado é altamente disponibilizado pela Service Fabric sem a necessidade de persistir em estado para uma loja externa.

Para converter um contra-valor de apátrida para altamente disponível e persistente, mesmo quando o serviço se move ou reinicia, precisa de um serviço imponente.

Na mesma aplicação *HelloWorld,* pode adicionar um novo serviço clicando direito nas referências dos Serviços no projeto de aplicação e selecionando **Add -> New Service Service .**

![Adicione um serviço à sua aplicação Service Fabric](media/service-fabric-reliable-services-quick-start/hello-stateful-NewService.png)

Selecione **.NET Core 2.0 -> Stateful Service** e nomeie-o *HelloWorldStateful*. Clique em **OK**.

![Use a caixa de diálogo novo projeto para criar um novo serviço com estado Service Fabric](media/service-fabric-reliable-services-quick-start/hello-stateful-NewProject.png)

A sua aplicação deverá agora ter dois serviços: o serviço apátrida *HelloWorldStateless* e o serviço imponente *HelloWorldStateful.*

Um serviço imponente tem os mesmos pontos de entrada que um serviço apátrida. A principal diferença é a disponibilidade de um *provedor do Estado* que possa armazenar o Estado de forma fiável. O Service Fabric vem com uma implementação de fornecedor estatal chamada [Reliable Collections,](service-fabric-reliable-services-reliable-collections.md)que permite criar estruturas de dados replicadas através do Gestor de Estado Fiável. Um serviço fiável e audato utiliza este fornecedor estatal por defeito.

Aberto **HelloWorldStateful.cs** no *HelloWorldStateful,* que contém o seguinte método RunAsync:

```csharp
protected override async Task RunAsync(CancellationToken cancellationToken)
{
    // TODO: Replace the following sample code with your own logic
    //       or remove this RunAsync override if it's not needed in your service.

    var myDictionary = await this.StateManager.GetOrAddAsync<IReliableDictionary<string, long>>("myDictionary");

    while (true)
    {
        cancellationToken.ThrowIfCancellationRequested();

        using (var tx = this.StateManager.CreateTransaction())
        {
            var result = await myDictionary.TryGetValueAsync(tx, "Counter");

            ServiceEventSource.Current.ServiceMessage(this.Context, "Current Counter Value: {0}",
                result.HasValue ? result.Value.ToString() : "Value does not exist.");

            await myDictionary.AddOrUpdateAsync(tx, "Counter", 0, (key, value) => ++value);

            // If an exception is thrown before calling CommitAsync, the transaction aborts, all changes are
            // discarded, and nothing is saved to the secondary replicas.
            await tx.CommitAsync();
        }

        await Task.Delay(TimeSpan.FromSeconds(1), cancellationToken);
    }
```

### <a name="runasync"></a>RunAsync

`RunAsync()` opera de forma semelhante em serviços apátridas e apátridas. No entanto, num serviço imponente, a plataforma realiza trabalhoadicional em seu nome antes de executar `RunAsync()`. Este trabalho pode incluir garantir que o Gestor de Estado Fiável e Coleções Fiáveis estão prontos a ser utilizados.

### <a name="reliable-collections-and-the-reliable-state-manager"></a>Coleções fiáveis e o Gestor de Estado Confiável

```csharp
var myDictionary = await this.StateManager.GetOrAddAsync<IReliableDictionary<string, long>>("myDictionary");
```

[IReliableDictionary](https://msdn.microsoft.com/library/dn971511.aspx) é uma implementação de dicionário que pode usar para armazenar o estado de forma fiável no serviço. Com Service Fabric e coleções confiáveis, você pode armazenar dados diretamente em seu serviço sem a necessidade de um repositório persistente externo. As coleções confiáveis tornam seus dados altamente disponíveis. O Service Fabric consegue isso criando e gerindo *múltiplas réplicas* do seu serviço para si. Também fornece uma API que abstrata as complexidades da gestão dessas réplicas e as suas transições estatais.

As coleções confiáveis podem armazenar qualquer tipo .NET, incluindo seus tipos personalizados, com algumas limitações:

* O Service Fabric disponibiliza o seu estado *replicando* o estado através de nós, e as Coleções Fiáveis armazenam os seus dados em disco local em cada réplica. Isto significa que tudo o que está armazenado em Coleções Fiáveis deve ser *ilizável.* Por predefinição, as Coleções Fiáveis utilizam [dataContract](https://msdn.microsoft.com/library/system.runtime.serialization.datacontractattribute%28v=vs.110%29.aspx) para serialização, por isso é importante certificar-se de que os seus tipos são [suportados pelo Serializer](https://msdn.microsoft.com/library/ms731923%28v=vs.110%29.aspx) data contract quando utiliza o serializador predefinido.
* Os objetos são replicados para alta disponibilidade quando você confirma transações em coleções confiáveis. Os objetos armazenados em coleções confiáveis são mantidos na memória local em seu serviço. Isto significa que tem uma referência local ao objeto.
  
   É importante que não sofram mutações locais desses objetos sem realizar uma operação de atualização sobre a recolha fiável numa transação. Isto porque as alterações às instâncias locais dos objetos não serão replicadas automaticamente. Deve voltar a inserir o objeto no dicionário ou utilizar um dos métodos de *atualização* do dicionário.

O Gerenciador de estado confiável gerencia coleções confiáveis para você. Você pode simplesmente pedir ao Gerenciador de estado confiável uma coleção confiável por nome a qualquer momento e em qualquer lugar em seu serviço. O Gestor de Estado confiável garante que obtém uma referência de volta. Não recomendamos que você salve referências a instâncias de coleções confiáveis em Propriedades ou variáveis de membro de classe. Há que ter especial cuidado para garantir que a referência seja definida em todos os momentos do ciclo de vida de serviço. O Gestor de Estado confiável lida com este trabalho para si, e está otimizado para visitas repetidas.

### <a name="transactional-and-asynchronous-operations"></a>Operações transacionais e assíncronas

```csharp
using (ITransaction tx = this.StateManager.CreateTransaction())
{
    var result = await myDictionary.TryGetValueAsync(tx, "Counter-1");

    await myDictionary.AddOrUpdateAsync(tx, "Counter-1", 0, (k, v) => ++v);

    await tx.CommitAsync();
}
```

As Coleções Fiáveis têm muitas das mesmas operações que os seus homólogos `System.Collections.Generic` e `System.Collections.Concurrent`, com exceção da Consulta Integrada de Línguas (LINQ). As operações em coleções confiáveis são assíncronas. Isto porque as operações de escrita com Coleções Fiáveis realizam operações de I/S para replicar e persistir dados em disco.

As operações de Recolha Fiável são *transacionais,* para que possa manter o estado consistente em várias Coleções e operações fiáveis. Por exemplo, você pode remover uma fila de um item de trabalho de uma fila confiável, executar uma operação nele e salvar o resultado em um dicionário confiável, tudo em uma única transação. Isto é tratado como uma operação atómica, e garante que ou toda a operação terá sucesso ou toda a operação irá reverter. Se ocorrer um erro após a desfilar o item, mas antes de guardar o resultado, toda a transação é relançada e o item permanece na fila para processamento.

## <a name="run-the-application"></a>Executar a aplicação
Voltamos agora à aplicação *HelloWorld.* Agora você pode criar e implantar seus serviços. Quando premir **F5,** a sua aplicação será construída e implantada para o seu cluster local.

Depois de os serviços começarem a funcionar, pode ver os eventos gerados de Rastreio de Eventos para Windows (ETW) numa janela de Eventos de **Diagnóstico.** Observe que os eventos exibidos são do serviço sem estado e do serviço com estado no aplicativo. Pode interromper o fluxo clicando no botão **Pausa.** Em seguida, você pode examinar os detalhes de uma mensagem expandindo essa mensagem.

> [!NOTE]
> Antes de executar o aplicativo, verifique se você tem um cluster de desenvolvimento local em execução. Consulte o [guia de partida](service-fabric-get-started.md) para obter informações sobre a criação do seu ambiente local.
> 
> 

![Exibir eventos de diagnóstico no Visual Studio](media/service-fabric-reliable-services-quick-start/hello-stateful-Output.png)

## <a name="next-steps"></a>Passos seguintes
[Desinvejo a sua aplicação de Tecido de Serviço no Estúdio Visual](service-fabric-debugging-your-application.md)

[Começar: Serviço de Serviço Fabric Web API serviços com auto-hospedagem OWIN](service-fabric-reliable-services-communication-webapi.md)

[Saiba mais sobre Coleções Fiáveis](service-fabric-reliable-services-reliable-collections.md)

[Implementar uma aplicação](service-fabric-deploy-remove-applications.md)

[Atualização da aplicação](service-fabric-application-upgrade.md)

[Referência do desenvolvedor para Serviços Fiáveis](https://msdn.microsoft.com/library/azure/dn706529.aspx)

