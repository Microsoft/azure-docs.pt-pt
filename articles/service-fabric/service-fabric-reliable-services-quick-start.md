---
title: 'Crie a sua primeira aplicação de Tecido de Serviço em C #'
description: Introdução à criação de uma aplicação Microsoft Azure Service Fabric com serviços apátridas e imponentes.
ms.topic: conceptual
ms.date: 07/10/2019
ms.custom: sfrev, devx-track-csharp
ms.openlocfilehash: 45341c98a40cbcabfa8b96f2016f02f1755fe2b3
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/26/2021
ms.locfileid: "98791532"
---
# <a name="get-started-with-reliable-services"></a>Introdução ao Reliable Services

> [!div class="op_single_selector"]
> * [C# no Windows](service-fabric-reliable-services-quick-start.md)
> * [Java em Linux](service-fabric-reliable-services-quick-start-java.md)

Uma aplicação Azure Service Fabric contém um ou mais serviços que executam o seu código. Este guia mostra-lhe como criar aplicações de tecido de serviço apátridas e imponentes com [Serviços Fiáveis.](service-fabric-reliable-services-introduction.md)  

## <a name="basic-concepts"></a>Conceitos básicos

Para começar com os Serviços Fiáveis, basta compreender alguns conceitos básicos:

* **Tipo de serviço**: Esta é a sua implementação de serviço. É definido pela classe que escreve que se estende `StatelessService` e qualquer outro código ou dependências neles utilizados, juntamente com um nome e um número de versão.
* **Caso de serviço nomeado**: Para executar o seu serviço, cria instâncias nomeadas do seu tipo de serviço, tal como cria instâncias de objetos de tipo classe. Uma instância de serviço tem um nome na forma de um URI usando o "tecido:/" esquema, como "tecido:/MyApp/MyService".
* **Anfitrião de serviço**: As instâncias de serviço nomeadas que cria precisam de ser executadas dentro de um processo de anfitrião. O anfitrião do serviço é apenas um processo em que os casos do seu serviço podem ser executados.
* **Registo de** serviço : As inscrições reúnem tudo. O tipo de serviço deve ser registado no tempo de funcionação do Tecido de Serviço num anfitrião de serviço para permitir que o Service Fabric crie instâncias para executar.  

## <a name="create-a-stateless-service"></a>Criar um serviço apátrida

Um serviço apátrida é um tipo de serviço que é atualmente a norma em aplicações em nuvem. Considera-se apátrida porque o próprio serviço não contém dados que precisam de ser armazenados de forma fiável ou altamente disponíveis. Se um caso de serviço apátrida encerrar, todo o seu estado interno está perdido. Neste tipo de serviço, o estado deve ser persistido a uma loja externa, como tabelas Azure ou Base de Dados SQL, para que seja disponibilizado e fiável.

Lançar Visual Studio 2017 ou Visual Studio 2019 como administrador, e criar um novo projeto de aplicação de Service Fabric chamado *HelloWorld*:

![Use a caixa de diálogo New Project para criar uma nova aplicação de Tecido de Serviço](media/service-fabric-reliable-services-quick-start/hello-stateless-NewProject.png)

Em seguida, crie um projeto de serviço apátrida utilizando **.NET Core 2.0** chamado *HelloWorldStateless*:

![Na segunda caixa de diálogo, crie um projeto de serviço apátrida](media/service-fabric-reliable-services-quick-start/hello-stateless-NewProject2.png)

A sua solução contém agora dois projetos:

* *OláWorld*. Este é o projeto *de candidatura* que contém os seus *serviços.* Também contém o manifesto de aplicação que descreve a aplicação, bem como uma série de scripts PowerShell que o ajudam a implementar a sua aplicação.
* *HelloWorldStateless*. Este é o projeto de serviço. Contém a implementação do serviço apátrida.

## <a name="implement-the-service"></a>Implementar o serviço

Abra o ficheiro **HelloWorldStateless.cs** no projeto de serviço. Na Service Fabric, um serviço pode executar qualquer lógica de negócio. A API de serviço fornece dois pontos de entrada para o seu código:

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
O modelo do projeto inclui uma implementação de amostra `RunAsync()` que incrementa uma contagem de rolos.

> [!NOTE]
> Para mais detalhes sobre como trabalhar com uma pilha de comunicações, consulte [a comunicação de serviço com ASP.NET Core](service-fabric-reliable-services-communication-aspnetcore.md)

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

A plataforma chama este método quando uma instância de um serviço é colocada e pronta para ser executada. Para um serviço apátrida, isto significa simplesmente quando a instância de serviço é aberta. É fornecido um sinal de cancelamento para coordenar quando a sua instância de serviço precisa de ser encerrada. No Tecido de Serviço, este ciclo aberto/fechado de uma instância de serviço pode ocorrer muitas vezes ao longo da vida útil do serviço como um todo. Isto pode acontecer por várias razões, incluindo:

* O sistema move as suas instâncias de serviço para o equilíbrio de recursos.
* Falhas ocorrem no seu código.
* A aplicação ou sistema está atualizado.
* O hardware subjacente experimenta uma paragem.

Esta orquestração é gerida pelo sistema para manter o seu serviço altamente disponível e devidamente equilibrado.

`RunAsync()` não deve bloquear sincronizadamente. A sua implementação do RunAsync deve devolver uma Tarefa ou aguardar quaisquer operações de longo prazo ou bloqueio para permitir que o tempo de execução continue. Note-se `while(true)` no loop no exemplo anterior, é utilizado um retorno de `await Task.Delay()` tarefas. Se a sua carga de trabalho tiver de bloquear sincronizadamente, deverá agendar uma nova Tarefa `Task.Run()` na sua `RunAsync` implementação.

O cancelamento da sua carga de trabalho é um esforço de cooperação orquestrado pelo token de cancelamento fornecido. O sistema aguardará o fim da sua tarefa (com sucesso de conclusão, cancelamento ou falha) antes de seguir em frente. É importante honrar o token de cancelamento, terminar qualquer trabalho e sair `RunAsync()` o mais rápido possível quando o sistema solicita cancelamento.

Neste exemplo de serviço apátrida, a contagem é armazenada numa variável local. Mas como este é um serviço apátrida, o valor que é armazenado existe apenas para o ciclo de vida atual da sua instância de serviço. Quando o serviço se move ou reinicia, o valor perde-se.

## <a name="create-a-stateful-service"></a>Criar um serviço imponente

A Service Fabric introduz um novo tipo de serviço que é imponente. Um serviço estatal pode manter o estado de forma fiável dentro do próprio serviço, co-localizado com o código que o está a usar. O Estado é altamente disponibilizado pela Service Fabric sem a necessidade de persistir em estado de uma loja externa.

Para converter um valor de contra-valor de apátrida para altamente disponível e persistente, mesmo quando o serviço se move ou reinicia, precisa de um serviço estatal.

Na mesma aplicação *HelloWorld,* pode adicionar um novo serviço clicando no direito nas referências de Serviços no projeto de aplicação e selecionando **Add -> New Service Fabric Service**.

![Adicione um serviço à sua aplicação Service Fabric](media/service-fabric-reliable-services-quick-start/hello-stateful-NewService.png)

Selecione **.NET Core 2.0 -> Stateful Service** e nomeie-o *HelloWorldStateful*. Clique em **OK**.

![Use a caixa de diálogo New Project para criar um novo serviço stateful do Service Fabric](media/service-fabric-reliable-services-quick-start/hello-stateful-NewProject.png)

A sua aplicação deverá agora ter dois serviços: o serviço apátrida *HelloWorldStateless* e o serviço stateful *HelloWorldStateful*.

Um serviço estatal tem os mesmos pontos de entrada que um serviço apátrida. A principal diferença é a disponibilidade de um *fornecedor estatal* que pode armazenar o Estado de forma fiável. O Service Fabric vem com uma implementação de fornecedor estatal chamada [Reliable Collections,](service-fabric-reliable-services-reliable-collections.md)que permite criar estruturas de dados replicadas através do Gestor estatal fiável. Um Serviço Fidedigna imponente utiliza este fornecedor estatal por defeito.

Abra **HelloWorldStateful.cs** em *HelloWorldStateful,* que contém o seguinte método RunAsync:

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

`RunAsync()` opera da mesma forma em serviços apátridas e apátridas. No entanto, num serviço imponente, a plataforma realiza trabalhos adicionais em seu nome antes de executar `RunAsync()` . Este trabalho pode incluir garantir que o Gestor de Estado Fiável e as Coleções Fiáveis estão prontas a usar.

### <a name="reliable-collections-and-the-reliable-state-manager"></a>Coleções fiáveis e o Gestor de Estado Fiável

```csharp
var myDictionary = await this.StateManager.GetOrAddAsync<IReliableDictionary<string, long>>("myDictionary");
```

[IReliableDictionary](/dotnet/api/microsoft.servicefabric.data.collections.ireliabledictionary-2#microsoft_servicefabric_data_collections_ireliabledictionary_2) é uma implementação de dicionário que você pode usar para armazenar de forma fiável o estado no serviço. Com o Service Fabric e as Reliable Collections, pode armazenar dados diretamente no seu serviço sem a necessidade de uma loja externa persistente. As Coleções Fiáveis tornam os seus dados altamente disponíveis. O Service Fabric realiza-o criando e gerindo *múltiplas réplicas* do seu serviço para si. Também fornece uma API que retira as complexidades da gestão dessas réplicas e as suas transições estatais.

As Coleções Fiáveis podem armazenar qualquer tipo .NET, incluindo os seus tipos personalizados, com algumas ressalvas:

* O Service Fabric torna o seu estado altamente disponível *replicando* o estado através dos nós, e as Coleções Fiáveis armazenam os seus dados em disco local em cada réplica. Isto significa que tudo o que está armazenado em Coleções Fiáveis deve ser *serializável.* Por predefinição, as Coleções Fiáveis utilizam [o DataContract](/dotnet/api/system.runtime.serialization.datacontractattribute) para serialização, por isso é importante certificar-se de que os seus tipos são [suportados pelo Serializer do Contrato de Dados](/dotnet/framework/wcf/feature-details/types-supported-by-the-data-contract-serializer) quando utiliza o serializador predefinido.
* Os objetos são replicados para uma elevada disponibilidade quando efetivos transações em Coleções Fiáveis. Os objetos armazenados em Coleções Fiáveis são guardados na memória local no seu serviço. Isto significa que tem uma referência local ao objeto.
  
   É importante que não mutação de instâncias locais desses objetos sem realizar uma operação de atualização sobre a coleção fiável numa transação. Isto porque as alterações nas instâncias locais de objetos não serão replicadas automaticamente. Deve voltar a inserir o objeto no dicionário ou utilizar um dos métodos de *atualização* do dicionário.

O Gestor estatal fiável gere coleções fidedignas para si. Pode simplesmente pedir ao Gestor de Estado Fiável uma recolha fiável pelo nome a qualquer momento e em qualquer lugar do seu serviço. O Gestor de Estado Fidedigna garante que recebe uma referência de volta. Não recomendamos que guarde referências a casos de recolha fiáveis em variáveis ou propriedades de membros da classe. Deve-se ter especial cuidado para garantir que a referência seja sempre definida num caso no ciclo de vida do serviço. O Gestor de Estado Confiável trata deste trabalho para si, e está otimizado para visitas repetidas.

### <a name="transactional-and-asynchronous-operations"></a>Operações transacionais e assíncroas

```csharp
using (ITransaction tx = this.StateManager.CreateTransaction())
{
    var result = await myDictionary.TryGetValueAsync(tx, "Counter-1");

    await myDictionary.AddOrUpdateAsync(tx, "Counter-1", 0, (k, v) => ++v);

    await tx.CommitAsync();
}
```

As Coleções Fiáveis têm muitas das mesmas operações que os seus `System.Collections.Generic` e `System.Collections.Concurrent` homólogos, com exceção da Consulta Integrada de Línguas (LINQ). As operações em Coleções Fiáveis são assíncronos. Isto porque as operações de escrita com Coleções Fiáveis executam operações de E/S para replicar e persistir dados em disco.

As operações de Cobrança Fiável são *transacionais,* para que possa manter o estado consistente em várias Coleções e operações fiáveis. Por exemplo, pode descacionar um item de trabalho de uma Fila Fiável, efetuar uma operação nele, e guardar o resultado num Dicionário Fiável, tudo dentro de uma única transação. Isto é tratado como uma operação atómica, e garante que toda a operação terá sucesso ou toda a operação irá recuar. Se ocorrer um erro depois de descodionar o item, mas antes de guardar o resultado, toda a transação é recorrida e o item permanece na fila para processamento.

## <a name="run-the-application"></a>Executar a aplicação
Voltamos agora à aplicação *HelloWorld.* Agora pode construir e implementar os seus serviços. Quando premir **F5,** a sua aplicação será construída e implantada no seu cluster local.

Após o início dos serviços, pode visualizar os eventos gerados de rastreio de eventos para windows (ETW) numa janela **de Eventos de Diagnóstico.** Note que os eventos exibidos são do serviço apátrida e do serviço estatal na aplicação. Pode fazer uma pausa no fluxo clicando no botão **Pausa.** Em seguida, pode examinar os detalhes de uma mensagem expandindo essa mensagem.

> [!NOTE]
> Antes de executar a aplicação, certifique-se de que tem um cluster de desenvolvimento local em funcionamento. Consulte o [guia de arranque](service-fabric-get-started.md) para obter informações sobre a configuração do seu ambiente local.
> 
> 

![Ver eventos de diagnóstico em estúdio visual](media/service-fabric-reliable-services-quick-start/hello-stateful-Output.png)

## <a name="next-steps"></a>Próximos passos
[Depurar a sua aplicação de Tecido de Serviço no Estúdio Visual](service-fabric-debugging-your-application.md)

[Começar: Serviços de API Web de Tecido de Serviço com auto-hospedagem OWIN](./service-fabric-reliable-services-communication-aspnetcore.md)

[Saiba mais sobre Coleções Fiáveis](service-fabric-reliable-services-reliable-collections.md)

[Implementar uma aplicação](service-fabric-deploy-remove-applications.md)

[Atualização da aplicação](service-fabric-application-upgrade.md)

[Referência do programador para serviços fiáveis](/previous-versions/azure/dn706529(v=azure.100))
