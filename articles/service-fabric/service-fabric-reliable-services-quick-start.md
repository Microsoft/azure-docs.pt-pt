---
title: 'Crie a sua primeira aplicação de Tecido de Serviço em C #'
description: Introdução à criação de uma aplicação Microsoft Azure Service Fabric com serviços apátridas e apátridas.
ms.topic: conceptual
ms.date: 07/10/2019
ms.custom: sfrev
ms.openlocfilehash: 15dd9bf6ac19bdac7bc8b50fc70e0b3b0a4e9a83
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77083744"
---
# <a name="get-started-with-reliable-services"></a>Introdução ao Reliable Services

> [!div class="op_single_selector"]
> * [C# no Windows](service-fabric-reliable-services-quick-start.md)
> * [Java em Linux](service-fabric-reliable-services-quick-start-java.md)

Uma aplicação Azure Service Fabric contém um ou mais serviços que executam o seu código. Este guia mostra-lhe como criar aplicações apátridas e apátridas de Tecido de Serviço com [Serviços Fiáveis.](service-fabric-reliable-services-introduction.md)  

## <a name="basic-concepts"></a>Conceitos básicos

Para começar com serviços fiáveis, basta compreender alguns conceitos básicos:

* **Tipo**de serviço : Esta é a sua implementação de serviço. É definido pela classe que escreve `StatelessService` que se estende e qualquer outro código ou dependências nele utilizados, juntamente com um nome e um número de versão.
* **Caso de serviço nomeado**: Para executar o seu serviço, cria instâncias nomeadas do seu tipo de serviço, tal como cria situações de objetos de tipo de classe. Uma instância de serviço tem um nome na forma de um URI usando o "tecido:/" esquema, como "tecido:/MyApp/MyService".
* **Anfitrião de serviço**: As instâncias de serviço nomeadas que cria precisam de ser executadas dentro de um processo de hospedar. O anfitrião do serviço é apenas um processo onde as instâncias do seu serviço podem ser executadas.
* **Inscrição de serviço:** O registo reúne tudo. O tipo de serviço deve ser registado com o tempo de funcionar do Tecido de Serviço num hospedeiro de serviço para permitir que o Tecido de Serviço crie instâncias do mesmo.  

## <a name="create-a-stateless-service"></a>Criar um serviço apátrida

Um serviço apátrida é um tipo de serviço que é atualmente a norma em aplicações em nuvem. É considerado apátrida porque o próprio serviço não contém dados que precisam de ser armazenados de forma fiável ou altamente disponíveis. Se um caso de um serviço apátrida encerrar, todo o seu estado interno está perdido. Neste tipo de serviço, o Estado deve ser pernoitado a uma loja externa, como as Tabelas Azure ou uma base de dados SQL, para que seja altamente disponível e fiável.

Lançar o Visual Studio 2017 ou o Visual Studio 2019 como administrador, e criar um novo projeto de aplicação Service Fabric chamado *HelloWorld*:

![Utilize a caixa de diálogo new Project para criar uma nova aplicação Service Fabric](media/service-fabric-reliable-services-quick-start/hello-stateless-NewProject.png)

Em seguida, crie um projeto de serviço apátrida utilizando **.NET Core 2.0** chamado *HelloWorldStateless*:

![Na segunda caixa de diálogo, crie um projeto de serviço apátrida](media/service-fabric-reliable-services-quick-start/hello-stateless-NewProject2.png)

A sua solução agora contém dois projetos:

* *HelloWorld.* Este é o projeto de *candidatura* que contém os seus *serviços.* Também contém o manifesto de aplicação que descreve a aplicação, bem como uma série de scripts PowerShell que o ajudam a implementar a sua aplicação.
* *HelloWorldStateless.* Este é o projeto de serviço. Contém a implementação do serviço apátrida.

## <a name="implement-the-service"></a>Implementar o serviço

Abra o ficheiro **HelloWorldStateless.cs** no projeto de serviço. Na Service Fabric, um serviço pode executar qualquer lógica de negócio. O serviço API fornece dois pontos de entrada para o seu código:

* Um método de ponto de entrada aberto, chamado *RunAsync,* onde pode começar a executar quaisquer cargas de trabalho, incluindo cargas de trabalho de computação de longa duração.

```csharp
protected override async Task RunAsync(CancellationToken cancellationToken)
{
    ...
}
```

* Um ponto de entrada de comunicação onde pode ligar a sua pilha de comunicação de eleição, como ASP.NET Core. É aqui que pode começar a receber pedidos de utilizadores e outros serviços.

```csharp
protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
{
    ...
}
```

Neste tutorial, vamos focar-nos no método do `RunAsync()` ponto de entrada. É aqui que pode começar imediatamente a executar o seu código.
O modelo do projeto `RunAsync()` inclui uma implementação de amostras que incrementa uma contagem de rolos.

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

A plataforma chama a este método quando uma instância de um serviço é colocada e pronta a ser executada. Para um serviço apátrida, isso significa simplesmente quando a instância de serviço é aberta. É fornecido um símbolo de cancelamento para coordenar quando a sua instância de serviço precisa de ser fechada. No Tecido de Serviço, este ciclo aberto/próximo de uma instância de serviço pode ocorrer muitas vezes ao longo da vida do serviço como um todo. Isto pode acontecer por várias razões, incluindo:

* O sistema move as suas instâncias de serviço para o equilíbrio de recursos.
* As falhas ocorrem no seu código.
* A aplicação ou sistema é atualizado.
* O hardware subjacente experimenta uma paragem.

Esta orquestração é gerida pelo sistema para manter o seu serviço altamente disponível e devidamente equilibrado.

`RunAsync()`não deve bloquear sincronizadamente. A sua implementação do RunAsync deve devolver uma Tarefa ou aguardar por quaisquer operações de longo prazo ou de bloqueio para permitir que o tempo de funcionamento continue. Nota no `while(true)` loop no exemplo anterior, `await Task.Delay()` é utilizado um retorno de tarefas. Se a sua carga de trabalho tiver de bloquear sincronizadamente, deverá agendar uma nova Tarefa `Task.Run()` com a sua `RunAsync` implementação.

O cancelamento da sua carga de trabalho é um esforço de cooperação orquestrado pelo símbolo de cancelamento fornecido. O sistema aguardará que a sua tarefa termine (por conclusão, cancelamento ou falha com sucesso) antes de seguir em frente. É importante honrar o sinal de cancelamento, terminar `RunAsync()` qualquer trabalho e sair o mais rápido possível quando o sistema solicitar o cancelamento.

Neste exemplo de serviço apátrida, a contagem é armazenada numa variável local. Mas como este é um serviço apátrida, o valor armazenado só existe para o ciclo de vida atual da sua instância de serviço. Quando o serviço se move ou reinicia, o valor perde-se.

## <a name="create-a-stateful-service"></a>Criar um serviço imponente

A Service Fabric introduz um novo tipo de serviço que é imponente. Um serviço imponente pode manter o estado de forma fiável dentro do próprio serviço, co-localizado com o código que o está a usar. O Estado é altamente disponibilizado pela Service Fabric sem a necessidade de persistir em estado para uma loja externa.

Para converter um contra-valor de apátrida para altamente disponível e persistente, mesmo quando o serviço se move ou reinicia, precisa de um serviço imponente.

Na mesma aplicação *HelloWorld,* pode adicionar um novo serviço clicando direito nas referências dos Serviços no projeto de aplicação e selecionando **Add -> New Service Service**.

![Adicione um serviço à sua aplicação Service Fabric](media/service-fabric-reliable-services-quick-start/hello-stateful-NewService.png)

Selecione **.NET Core 2.0 -> Stateful Service** e nomeie-o *HelloWorldStateful*. Clique em **OK**.

![Use a caixa de diálogo new project para criar um novo serviço de serviço Tecido](media/service-fabric-reliable-services-quick-start/hello-stateful-NewProject.png)

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

`RunAsync()`opera da mesma forma em serviços apátridas e apátridas. No entanto, num serviço imponente, a plataforma realiza trabalhoadicional `RunAsync()`em seu nome antes de ser executada. Este trabalho pode incluir garantir que o Gestor de Estado Fiável e Coleções Fiáveis estão prontos a ser utilizados.

### <a name="reliable-collections-and-the-reliable-state-manager"></a>Coleções fiáveis e o Gestor de Estado Confiável

```csharp
var myDictionary = await this.StateManager.GetOrAddAsync<IReliableDictionary<string, long>>("myDictionary");
```

[IReliableDictionary](https://msdn.microsoft.com/library/dn971511.aspx) é uma implementação de dicionário que pode usar para armazenar o estado de forma fiável no serviço. Com tecido de serviço e Coleções Fiáveis, pode armazenar dados diretamente no seu serviço sem a necessidade de uma loja externa persistente. As Coleções Fiáveis disponibilizam os seus dados. O Service Fabric consegue isso criando e gerindo *múltiplas réplicas* do seu serviço para si. Também fornece uma API que abstrata as complexidades da gestão dessas réplicas e as suas transições estatais.

As Coleções Fiáveis podem armazenar qualquer tipo .NET, incluindo os seus tipos personalizados, com um par de ressalvas:

* O Service Fabric disponibiliza o seu estado *replicando* o estado através de nós, e as Coleções Fiáveis armazenam os seus dados em disco local em cada réplica. Isto significa que tudo o que está armazenado em Coleções Fiáveis deve ser *ilizável.* Por predefinição, as Coleções Fiáveis utilizam [dataContract](https://msdn.microsoft.com/library/system.runtime.serialization.datacontractattribute%28v=vs.110%29.aspx) para serialização, por isso é importante certificar-se de que os seus tipos são [suportados pelo Serializer](https://msdn.microsoft.com/library/ms731923%28v=vs.110%29.aspx) data contract quando utiliza o serializador predefinido.
* Os objetos são replicados para alta disponibilidade quando você comete transações em Coleções Fiáveis. Os objetos armazenados em Coleções Fiáveis são guardados na memória local ao seu serviço. Isto significa que tem uma referência local ao objeto.
  
   É importante que não sofram mutações locais desses objetos sem realizar uma operação de atualização sobre a recolha fiável numa transação. Isto porque as alterações às instâncias locais dos objetos não serão replicadas automaticamente. Deve voltar a inserir o objeto no dicionário ou utilizar um dos métodos de *atualização* do dicionário.

O Gestor de Estado Confiável gere coleções fiáveis para si. Você pode simplesmente pedir ao Gestor de Estado fiável uma coleção fiável pelo nome a qualquer momento e em qualquer lugar ao seu serviço. O Gestor de Estado confiável garante que obtém uma referência de volta. Não recomendamos que guarde referências a casos de recolha fiáveis em variáveis ou propriedades de membros da classe. Há que ter especial cuidado para garantir que a referência seja definida em todos os momentos do ciclo de vida de serviço. O Gestor de Estado confiável lida com este trabalho para si, e está otimizado para visitas repetidas.

### <a name="transactional-and-asynchronous-operations"></a>Operações transacionais e assíncronas

```csharp
using (ITransaction tx = this.StateManager.CreateTransaction())
{
    var result = await myDictionary.TryGetValueAsync(tx, "Counter-1");

    await myDictionary.AddOrUpdateAsync(tx, "Counter-1", 0, (k, v) => ++v);

    await tx.CommitAsync();
}
```

As Coleções Fiáveis têm muitas `System.Collections.Generic` `System.Collections.Concurrent` das mesmas operações que as suas e as suas congéneres, com exceção da Consulta Integrada de Línguas (LINQ). As operações em Coleções Fiáveis são assíncronas. Isto porque as operações de escrita com Coleções Fiáveis realizam operações de I/S para replicar e persistir dados em disco.

As operações de Recolha Fiável são *transacionais,* para que possa manter o estado consistente em várias Coleções e operações fiáveis. Por exemplo, pode desviar um item de trabalho de uma fila fiável, executar uma operação nele e guardar o resultado num Dicionário Fiável, tudo dentro de uma única transação. Isto é tratado como uma operação atómica, e garante que ou toda a operação terá sucesso ou toda a operação irá reverter. Se ocorrer um erro após a desfilar o item, mas antes de guardar o resultado, toda a transação é relançada e o item permanece na fila para processamento.

## <a name="run-the-application"></a>Executar a aplicação
Voltamos agora à aplicação *HelloWorld.* Agora pode construir e implementar os seus serviços. Quando premir **F5,** a sua aplicação será construída e implantada para o seu cluster local.

Depois de os serviços começarem a funcionar, pode ver os eventos gerados de Rastreio de Eventos para Windows (ETW) numa janela de Eventos de **Diagnóstico.** Note que os eventos apresentados são do serviço apátrida e do serviço imponente na aplicação. Pode interromper o fluxo clicando no botão **Pausa.** Pode então examinar os detalhes de uma mensagem expandindo essa mensagem.

> [!NOTE]
> Antes de executar a aplicação, certifique-se de que tem um cluster de desenvolvimento local em execução. Consulte o [guia de partida](service-fabric-get-started.md) para obter informações sobre a criação do seu ambiente local.
> 
> 

![Ver eventos de diagnóstico em estúdio visual](media/service-fabric-reliable-services-quick-start/hello-stateful-Output.png)

## <a name="next-steps"></a>Passos seguintes
[Desinvejo a sua aplicação de Tecido de Serviço no Estúdio Visual](service-fabric-debugging-your-application.md)

[Começar: Serviço de Serviço Fabric Web API serviços com auto-hospedagem OWIN](service-fabric-reliable-services-communication-webapi.md)

[Saiba mais sobre Coleções Fiáveis](service-fabric-reliable-services-reliable-collections.md)

[Implementar uma aplicação](service-fabric-deploy-remove-applications.md)

[Atualização de aplicações](service-fabric-application-upgrade.md)

[Referência do desenvolvedor para Serviços Fiáveis](https://msdn.microsoft.com/library/azure/dn706529.aspx)

