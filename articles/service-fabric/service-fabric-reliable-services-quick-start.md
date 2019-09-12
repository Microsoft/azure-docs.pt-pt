---
title: Criar seu primeiro aplicativo Service Fabric no C# | Microsoft Docs
description: Introdução à criação de um aplicativo Microsoft Azure Service Fabric com serviços com e sem estado.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: chackdan
editor: ''
ms.assetid: d9b44d75-e905-468e-b867-2190ce97379a
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/10/2019
ms.author: vturecek
ms.openlocfilehash: f3b3d5c3dcea7d190724ae946a27c47b34a26c31
ms.sourcegitcommit: 920ad23613a9504212aac2bfbd24a7c3de15d549
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/15/2019
ms.locfileid: "68225061"
---
# <a name="get-started-with-reliable-services"></a>Introdução ao Reliable Services
> [!div class="op_single_selector"]
> * [C# no Windows](service-fabric-reliable-services-quick-start.md)
> * [Java em Linux](service-fabric-reliable-services-quick-start-java.md)
> 
> 

Um aplicativo de Service Fabric do Azure contém um ou mais serviços que executam seu código. Este guia mostra como criar aplicativos de Service Fabric com e sem estado com [Reliable Services](service-fabric-reliable-services-introduction.md).  

## <a name="basic-concepts"></a>Conceitos básicos
Para começar a usar o Reliable Services, você só precisa entender alguns conceitos básicos:

* **Tipo de serviço**: Esta é a implementação do seu serviço. Ele é definido pela classe que você escreve que se `StatelessService` estende e quaisquer outros códigos ou dependências usados nele, juntamente com um nome e um número de versão.
* **Instância de serviço nomeada**: Para executar o serviço, você cria instâncias nomeadas do seu tipo de serviço, de forma semelhante a criar instâncias de objeto de um tipo de classe. Uma instância de serviço tem um nome na forma de um URI usando a "Fabric:/" esquema, como "Fabric:/MyApp/MyService".
* **Host de serviço**: As instâncias de serviço nomeado que você cria precisam ser executadas dentro de um processo de host. O host de serviço é apenas um processo em que as instâncias do serviço podem ser executadas.
* **Registro de serviço**: O registro faz tudo junto. O tipo de serviço deve ser registrado com o tempo de execução de Service Fabric em um host de serviço para permitir que Service Fabric criem instâncias dele para serem executados.  

## <a name="create-a-stateless-service"></a>Criar um serviço sem estado
Um serviço sem estado é um tipo de serviço que é atualmente a norma em aplicativos em nuvem. Ele é considerado sem estado porque o serviço em si não contém dados que precisam ser armazenados de maneira confiável ou tornarem-se altamente disponíveis. Se uma instância de um serviço sem estado for desligada, todo o seu estado interno será perdido. Nesse tipo de serviço, o estado deve ser persistido em um repositório externo, como tabelas do Azure ou um banco de dados SQL, para que ele se tornará altamente disponível e confiável.

Inicie o Visual Studio 2017 ou o Visual Studio 2019 como administrador e crie um novo projeto de aplicativo Service Fabric chamado *HelloWorld*:

![Use a caixa de diálogo novo projeto para criar um novo aplicativo Service Fabric](media/service-fabric-reliable-services-quick-start/hello-stateless-NewProject.png)

Em seguida, crie um projeto de serviço sem estado usando o **.NET Core 2,0** chamado *HelloWorldStateless*:

![Na segunda caixa de diálogo, crie um projeto de serviço sem estado](media/service-fabric-reliable-services-quick-start/hello-stateless-NewProject2.png)

Sua solução agora contém dois projetos:

* *HelloWorld*. Este é o projeto de *aplicativo* que contém seus *Serviços*. Ele também contém o manifesto do aplicativo que descreve o aplicativo, bem como vários scripts do PowerShell que ajudam você a implantar seu aplicativo.
* *HelloWorldStateless*. Este é o projeto de serviço. Ele contém a implementação do serviço sem estado.

## <a name="implement-the-service"></a>Implementar o serviço
Abra o arquivo **HelloWorldStateless.cs** no projeto de serviço. No Service Fabric, um serviço pode executar qualquer lógica de negócios. A API de serviço fornece dois pontos de entrada para seu código:

* Um método de ponto de entrada aberto, chamado *RunAsync*, no qual você pode começar a executar qualquer carga de trabalho, incluindo cargas de trabalho de computação de longa execução.

```csharp
protected override async Task RunAsync(CancellationToken cancellationToken)
{
    ...
}
```

* Um ponto de entrada de comunicação no qual você pode conectar sua pilha de comunicação de sua escolha, como ASP.NET Core. É aí que você pode começar a receber solicitações de usuários e outros serviços.

```csharp
protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
{
    ...
}
```

Neste tutorial, iremos nos concentrar no método de `RunAsync()` ponto de entrada. É aí que você pode iniciar imediatamente a execução do código.
O modelo de projeto inclui uma implementação de `RunAsync()` exemplo do que incrementa uma contagem sem interrupção.

> [!NOTE]
> Para obter detalhes sobre como trabalhar com uma pilha de comunicação, consulte [Service Fabric serviços de API Web com hospedagem interna OWIN](service-fabric-reliable-services-communication-webapi.md)
> 
> 

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

A plataforma chama esse método quando uma instância de um serviço é colocada e está pronta para ser executada. Para um serviço sem estado, isso simplesmente significa quando a instância do serviço é aberta. Um token de cancelamento é fornecido para coordenar quando sua instância de serviço precisa ser fechada. Em Service Fabric, esse ciclo de abertura/fechamento de uma instância de serviço pode ocorrer muitas vezes durante o tempo de vida do serviço como um todo. Isso pode ocorrer por vários motivos, incluindo:

* O sistema move as instâncias de serviço para o balanceamento de recursos.
* As falhas ocorrem em seu código.
* O aplicativo ou o sistema é atualizado.
* O hardware subjacente passa por uma interrupção.

Essa orquestração é gerenciada pelo sistema para manter seu serviço altamente disponível e equilibrado corretamente.

`RunAsync()`Não deve bloquear de forma síncrona. Sua implementação de RunAsync deve retornar uma tarefa ou Await em operações de execução longa ou de bloqueio para permitir que o tempo de execução continue. Observação no `while(true)` loop no exemplo anterior, um retorno `await Task.Delay()` de tarefa é usado. Se a carga de trabalho precisar ser bloqueada de forma síncrona, você `Task.Run()` deverá agendar uma nova tarefa com em sua `RunAsync` implementação.

O cancelamento de sua carga de trabalho é um esforço cooperativo orquestrado pelo token de cancelamento fornecido. O sistema aguardará que a tarefa seja encerrada (com êxito na conclusão, cancelamento ou falha) antes de se mover. É importante honrar o token de cancelamento, concluir qualquer trabalho e sair `RunAsync()` o mais rápido possível quando o sistema solicita o cancelamento.

Neste exemplo de serviço sem estado, a contagem é armazenada em uma variável local. Mas como esse é um serviço sem estado, o valor armazenado só existe para o ciclo de vida atual de sua instância de serviço. Quando o serviço é movido ou reiniciado, o valor é perdido.

## <a name="create-a-stateful-service"></a>Criar um serviço com estado
Service Fabric introduz um novo tipo de serviço com estado. Um serviço com estado pode manter o estado de forma confiável no próprio serviço, localizado em conjunto com o código que o está usando. O estado se torna altamente disponível por Service Fabric sem a necessidade de persistir o estado para um repositório externo.

Para converter um valor de contador de sem estado para altamente disponível e persistente, mesmo quando o serviço é movido ou reiniciado, você precisa de um serviço com estado.

No mesmo aplicativo *HelloWorld* , você pode adicionar um novo serviço clicando com o botão direito do mouse nas referências de serviços no projeto de aplicativo e selecionando **Adicionar > novo serviço de Service Fabric**.

![Adicionar um serviço ao seu aplicativo Service Fabric](media/service-fabric-reliable-services-quick-start/hello-stateful-NewService.png)

Selecione **.NET Core 2,0-> serviço com estado** e nomeie-o *HelloWorldStateful*. Clique em **OK**.

![Use a caixa de diálogo novo projeto para criar um novo serviço com estado Service Fabric](media/service-fabric-reliable-services-quick-start/hello-stateful-NewProject.png)

Seu aplicativo agora deve ter dois serviços: o serviço sem estado *HelloWorldStateless* e o serviço com estado *HelloWorldStateful*.

Um serviço com estado tem os mesmos pontos de entrada que um serviço sem estado. A principal diferença é a disponibilidade de um *provedor de estado* que pode armazenar o estado de forma confiável. Service Fabric vem com uma implementação de provedor de estado chamada [coleções confiáveis](service-fabric-reliable-services-reliable-collections.md), que permite criar estruturas de dados replicadas por meio do Gerenciador de estado confiável. Um serviço confiável com estado usa esse provedor de estado por padrão.

Abra **HelloWorldStateful.cs** no *HelloWorldStateful*, que contém o seguinte método RunAsync:

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
`RunAsync()`funciona da mesma forma em serviços com e sem estado. No entanto, em um serviço com estado, a plataforma executa trabalho adicional em seu nome antes `RunAsync()`de ser executada. Esse trabalho pode incluir garantir que o Gerenciador de estado confiável e as coleções confiáveis estejam prontos para uso.

### <a name="reliable-collections-and-the-reliable-state-manager"></a>Coleções confiáveis e o Gerenciador de estado confiável
```csharp
var myDictionary = await this.StateManager.GetOrAddAsync<IReliableDictionary<string, long>>("myDictionary");
```

[IReliableDictionary](https://msdn.microsoft.com/library/dn971511.aspx) é uma implementação de dicionário que você pode usar para armazenar o estado de forma confiável no serviço. Com Service Fabric e coleções confiáveis, você pode armazenar dados diretamente em seu serviço sem a necessidade de um repositório persistente externo. As coleções confiáveis tornam seus dados altamente disponíveis. Service Fabric realiza isso criando e gerenciando várias *réplicas* do seu serviço para você. Ele também fornece uma API que abstrai as complexidades de gerenciar essas réplicas e suas transições de estado.

As coleções confiáveis podem armazenar qualquer tipo .NET, incluindo seus tipos personalizados, com algumas limitações:

* Service Fabric torna seu estado altamente disponível ao *replicar* o estado entre os nós e as coleções confiáveis armazenam seus dados no disco local em cada réplica. Isso significa que tudo que é armazenado em coleções confiáveis deve ser *serializável*. Por padrão, as coleções confiáveis [](https://msdn.microsoft.com/library/system.runtime.serialization.datacontractattribute%28v=vs.110%29.aspx) usam DataContract para serialização, portanto, é importante certificar-se de que os tipos têm [suporte do serializador de contrato de dados](https://msdn.microsoft.com/library/ms731923%28v=vs.110%29.aspx) quando você usa o serializador padrão.
* Os objetos são replicados para alta disponibilidade quando você confirma transações em coleções confiáveis. Os objetos armazenados em coleções confiáveis são mantidos na memória local em seu serviço. Isso significa que você tem uma referência local para o objeto.
  
   É importante que você não faça mutação de instâncias locais desses objetos sem executar uma operação de atualização na coleção confiável em uma transação. Isso ocorre porque as alterações em instâncias locais de objetos não serão replicadas automaticamente. Você deve inserir novamente o objeto de volta no dicionário ou usar um dos métodos de *atualização* no dicionário.

O Gerenciador de estado confiável gerencia coleções confiáveis para você. Você pode simplesmente pedir ao Gerenciador de estado confiável uma coleção confiável por nome a qualquer momento e em qualquer lugar em seu serviço. O Gerenciador de estado confiável garante que você obtenha uma referência de volta. Não recomendamos que você salve referências a instâncias de coleções confiáveis em Propriedades ou variáveis de membro de classe. Deve-se tomar cuidado especial para garantir que a referência seja definida para uma instância em todos os momentos no ciclo de vida do serviço. O Gerenciador de estado confiável manipula esse trabalho para você e é otimizado para visitas repetidas.

### <a name="transactional-and-asynchronous-operations"></a>Operações transacionais e assíncronas
```csharp
using (ITransaction tx = this.StateManager.CreateTransaction())
{
    var result = await myDictionary.TryGetValueAsync(tx, "Counter-1");

    await myDictionary.AddOrUpdateAsync(tx, "Counter-1", 0, (k, v) => ++v);

    await tx.CommitAsync();
}
```

As coleções confiáveis têm muitas das mesmas operações que suas `System.Collections.Generic` e `System.Collections.Concurrent` contraparte, exceto LINQ. As operações em coleções confiáveis são assíncronas. Isso ocorre porque as operações de gravação com coleções confiáveis executam operações de e/s para replicar e manter os dados no disco.

As operações de coletaconfiáveis são transacionais, para que você possa manter o estado consistente entre várias coleções confiáveis e operações. Por exemplo, você pode remover uma fila de um item de trabalho de uma fila confiável, executar uma operação nele e salvar o resultado em um dicionário confiável, tudo em uma única transação. Isso é tratado como uma operação atômica e garante que toda a operação seja realizada com sucesso ou que toda a operação seja revertida. Se ocorrer um erro depois que você remover a fila do item, mas antes de salvar o resultado, toda a transação será revertida e o item permanecerá na fila para processamento.

## <a name="run-the-application"></a>Executar a aplicação
Agora retornamos ao aplicativo *HelloWorld* . Agora você pode criar e implantar seus serviços. Quando você pressionar **F5**, seu aplicativo será compilado e implantado em seu cluster local.

Depois que os serviços iniciarem a execução, você poderá exibir os eventos do ETW (rastreamento de eventos gerados para Windows) em uma janela **eventos de diagnóstico** . Observe que os eventos exibidos são do serviço sem estado e do serviço com estado no aplicativo. Você pode pausar o fluxo clicando no botão Pausar. Em seguida, você pode examinar os detalhes de uma mensagem expandindo essa mensagem.

> [!NOTE]
> Antes de executar o aplicativo, verifique se você tem um cluster de desenvolvimento local em execução. Confira o [Guia de introdução](service-fabric-get-started.md) para obter informações sobre como configurar seu ambiente local.
> 
> 

![Exibir eventos de diagnóstico no Visual Studio](media/service-fabric-reliable-services-quick-start/hello-stateful-Output.png)

## <a name="next-steps"></a>Passos Seguintes
[Depurar seu aplicativo Service Fabric no Visual Studio](service-fabric-debugging-your-application.md)

[Introdução: Service Fabric serviços de API Web com hospedagem interna OWIN](service-fabric-reliable-services-communication-webapi.md)

[Saiba mais sobre as coleções confiáveis](service-fabric-reliable-services-reliable-collections.md)

[Implementar uma aplicação](service-fabric-deploy-remove-applications.md)

[Atualização da aplicação](service-fabric-application-upgrade.md)

[Referência do desenvolvedor para Reliable Services](https://msdn.microsoft.com/library/azure/dn706529.aspx)

