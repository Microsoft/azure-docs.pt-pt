---
title: Criar a sua primeira aplicação do Service Fabric em C# | Documentos da Microsoft
description: Introdução à criação de um aplicativo do Microsoft Azure Service Fabric com os serviços com e sem estado.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: ''
ms.assetid: d9b44d75-e905-468e-b867-2190ce97379a
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/16/2018
ms.author: vturecek
ms.openlocfilehash: 26520e3ff8f5bb97866015d3148b4c8b8813b319
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58168575"
---
# <a name="get-started-with-reliable-services"></a>Introdução ao Reliable Services
> [!div class="op_single_selector"]
> * [C# no Windows](service-fabric-reliable-services-quick-start.md)
> * [Java em Linux](service-fabric-reliable-services-quick-start-java.md)
> 
> 

Uma aplicação do Azure Service Fabric contém um ou mais serviços com o seu código. Este guia mostra-lhe como criar aplicações do Service Fabric com e sem monitoração de estado com [Reliable Services](service-fabric-reliable-services-introduction.md).  

## <a name="basic-concepts"></a>Conceitos básicos
Para começar a utilizar com o Reliable Services, só precisa de compreender alguns conceitos básicos:

* **Tipo de serviço**: Esta é a sua implementação de serviço. Ele é definido pela classe que escreve que expande `StatelessService` e qualquer outro código ou dependências utilizadas nele, juntamente com um nome e um número de versão.
* **Instância de serviço com o nome**: Para executar seu serviço, vai criar instâncias nomeadas do tipo de serviço, bem como criar instâncias de objeto de um tipo de classe. Uma instância de serviço tem um nome na forma de um URI usando o "recursos de infraestrutura: /" esquema, tal como "fabric: / MyApp/MyService".
* **Anfitrião do serviço**: As instâncias de serviço com nome que cria tem de ser executado dentro de um processo de host. O anfitrião do serviço é apenas um processo em que as instâncias do seu serviço podem executar.
* **Registo do serviço**: Registo reúne tudo. O tipo de serviço tem de ser registado com o runtime do Service Fabric num host de serviço para permitir que o Service Fabric criar instâncias do mesmo para executar.  

## <a name="create-a-stateless-service"></a>Criar um serviço sem estado
Um serviço sem estado é um tipo de serviço, que está a norma nas aplicações na cloud. Ele é considerado sem monitoração de estado porque o serviço em si não contém dados que precisam de ser armazenados de forma fiável ou elevada disponibilidade. Se uma instância de um serviço sem estado é encerrado, todo seu estado interno é perdido. Este tipo de serviço, estado de persistência para um armazenamento externo, como as tabelas do Azure ou uma base de dados SQL, para o mesmo que se tornarão altamente disponíveis e fiáveis.

Inicie o Visual Studio 2015 ou Visual Studio 2017, como administrador e crie um novo projeto de aplicação do Service Fabric com o nome *HelloWorld*:

![Utilize a caixa de diálogo novo projeto para criar uma nova aplicação de Service Fabric](media/service-fabric-reliable-services-quick-start/hello-stateless-NewProject.png)

Em seguida, crie um projeto de serviço sem estado com **.NET Core 2.0** com o nome *HelloWorldStateless*:

![Na segunda caixa de diálogo, crie um projeto de serviço sem estado](media/service-fabric-reliable-services-quick-start/hello-stateless-NewProject2.png)

Agora, a sua solução contém dois projetos:

* *HelloWorld*. Este é o *aplicativo* projeto que contém seu *serviços*. Também contém o manifesto do aplicativo que descreve a aplicação, bem como uma série de scripts do PowerShell que ajudam a implementar a sua aplicação.
* *HelloWorldStateless*. Este é o projeto de serviço. Ela contém a implementação de serviço sem estado.

## <a name="implement-the-service"></a>Implementar o serviço
Abra o **HelloWorldStateless.cs** arquivo no projeto de serviço. No Service Fabric, um serviço pode executar qualquer lógica de negócios. A API de serviço fornece dois pontos de entrada para o código:

* Um método de ponto de entrada aberta, denominado *RunAsync*, onde pode começar a executar quaisquer cargas de trabalho, incluindo cargas de trabalho de computação de execução longa.

```csharp
protected override async Task RunAsync(CancellationToken cancellationToken)
{
    ...
}
```

* Um ponto de entrada de comunicação em que pode conectar sua pilha de comunicações de escolha, tal como o ASP.NET Core. Isso é onde pode começar a receber pedidos de utilizadores e outros serviços.

```csharp
protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
{
    ...
}
```

Neste tutorial, nos concentraremos no `RunAsync()` método de ponto de entrada. Isso é onde pode começar a execução do seu código.
O modelo de projeto inclui uma implementação de exemplo de `RunAsync()` que incrementa uma contagem sem interrupção.

> [!NOTE]
> Para obter detalhes sobre como trabalhar com uma pilha de comunicações, consulte [serviços de API de Web do Service Fabric com o OWIN hospedagem interna](service-fabric-reliable-services-communication-webapi.md)
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

A plataforma chama esse método quando uma instância de um serviço é colocá-la e pronto para ser executado. Para um serviço sem estado, que simplesmente significa que quando a instância do serviço é aberta. É fornecido um token de cancelamento para coordenar quando sua instância de serviço deve ser fechada. No Service Fabric, esse ciclo aberto/fechamento de uma instância de serviço pode ocorrer, como um todo, muitas vezes ao longo do tempo de vida do serviço. Isto pode acontecer por várias razões, incluindo:

* O sistema move as instâncias de serviço para balanceamento de recurso.
* As falhas ocorrem no seu código.
* O aplicativo ou o sistema está atualizado.
* O hardware subjacente sofre um período de indisponibilidade.

Esta orquestração é gerenciada pelo sistema para manter o seu serviço de elevada disponibilidade e corretamente equilibrada.

`RunAsync()` não deve bloquear sincronicamente. Sua implementação de RunAsync deve retornar uma tarefa ou await no quaisquer operações de longa execução ou bloqueios para permitir que o tempo de execução continuar. Tenha em atenção na `while(true)` loop no exemplo anterior, um que retornam Task `await Task.Delay()` é utilizado. Se a sua carga de trabalho deve bloquear de forma síncrona, deve agendar uma nova tarefa com `Task.Run()` no seu `RunAsync` implementação.

O cancelamento da sua carga de trabalho é um esforço cooperativo orquestrado pelo token de cancelamento fornecido. O sistema esperará terminar (por conclusão com êxito, cancelamento ou falha) antes de passar sua tarefa. É importante honrar o token de cancelamento, concluir qualquer trabalho e sair `RunAsync()` mais rápido possível quando o sistema pede cancelamento.

Neste exemplo de serviço sem estado, a contagem é armazenada numa variável local. Mas como esse é um serviço sem estado, o valor que é armazenado existe apenas para o ciclo de vida da sua instância de serviço atual. Quando o serviço muda ou for reiniciado, o valor é perdido.

## <a name="create-a-stateful-service"></a>Criar um serviço com estado
Service Fabric introduz um novo tipo de serviço de monitorização de estado. Um serviço com estado pode manter o estado de forma fiável dentro do serviço em si, localizado conjuntamente com o código que está a utilizar. Estado fica elevada disponibilidade ao Service Fabric sem a necessidade de persistir o estado de um armazenamento externo.

Para converter um valor de contador de sem monitoração de estado para elevada disponibilidade e persistente, mesmo quando move ou reinicia o serviço, terá de um serviço com estado.

Da mesma *Olámundo* aplicação, pode adicionar um novo serviço clicar nos serviços de referências do projeto de aplicativo e selecionando **adicionar -> novo serviço do Service Fabric**.

![Adicionar um serviço a sua aplicação do Service Fabric](media/service-fabric-reliable-services-quick-start/hello-stateful-NewService.png)

Selecione **.NET Core 2.0 -> serviço com estado** e o nomeio *HelloWorldStateful*. Clique em **OK**.

![Utilize a caixa de diálogo novo projeto para criar um novo serviço com monitorização de estado do Service Fabric](media/service-fabric-reliable-services-quick-start/hello-stateful-NewProject.png)

A aplicação deve agora ter dois serviços: o serviço sem estado *HelloWorldStateless* e o serviço com estado *HelloWorldStateful*.

Um serviço com estado tem os mesmos pontos de entrada como um serviço sem estado. A principal diferença é a disponibilidade de um *fornecedor de estado* que pode armazenar o estado de forma fiável. Service Fabric é fornecido com uma implementação de fornecedor de estado chamada [Reliable Collections](service-fabric-reliable-services-reliable-collections.md), que lhe permite criar replicados estruturas de dados através do Gestor de estado fiável. Um serviço fiável com estado usa esse provedor de estado por predefinição.

Open **HelloWorldStateful.cs** na *HelloWorldStateful*, que contém o seguinte método RunAsync:

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
`RunAsync()` funciona da mesma forma nos serviços de com e sem estado. No entanto, num serviço com estado, a plataforma executa trabalho adicional em seu nome antes de ser executada `RunAsync()`. Este trabalho pode incluir a garantir que o Reliable State Manager e das Reliable Collections são pronto a utilizar.

### <a name="reliable-collections-and-the-reliable-state-manager"></a>Coleções fiáveis e o Gestor de estado fiável
```csharp
var myDictionary = await this.StateManager.GetOrAddAsync<IReliableDictionary<string, long>>("myDictionary");
```

[IReliableDictionary](https://msdn.microsoft.com/library/dn971511.aspx) é uma implementação de dicionário que pode utilizar para armazenar de forma fiável Estado no serviço. Com o Service Fabric e coleções fiáveis, pode armazenar dados diretamente no seu serviço sem a necessidade de um armazenamento persistente externo. As coleções fiáveis tornam os dados de elevada disponibilidade. Service Fabric faz isso ao criar e gerir múltiplos *réplicas* do seu serviço para. Ele também fornece uma API que abstrai as complexidades de gerenciamento essas réplicas e de suas transições de estado.

As coleções fiáveis podem armazenar qualquer tipo de .NET, incluindo seus tipos personalizados, com algumas limitações:

* O Service Fabric garante o seu estado altamente disponível por *replicar* estado por nós e as coleções fiáveis armazenar os dados no disco local em cada réplica. Isso significa que tudo o que são armazenados em coleções fiáveis tem de ser *serializável*. Por predefinição, utilizar a Reliable Collections [DataContract](https://msdn.microsoft.com/library/system.runtime.serialization.datacontractattribute%28v=vs.110%29.aspx) para a serialização, por isso, é importante certificar-se de que seus tipos são [suportados pelo serializador de contrato de dados](https://msdn.microsoft.com/library/ms731923%28v=vs.110%29.aspx) quando utiliza o serializador padrão.
* Objetos são replicados para elevada disponibilidade ao consolidar transações no Reliable Collections. Objetos armazenados na Reliable Collections são mantidos na memória local no seu serviço. Isso significa que tem uma referência local para o objeto.
  
   É importante que modifica as instâncias locais desses objetos sem efetuar uma operação de atualização na coleção fiável numa transação. Isto acontece porque as alterações para as instâncias locais de objetos não serão replicadas automaticamente. Tem de inserir novamente o objeto de volta no dicionário ou utilize um da *atualizar* métodos no dicionário.

O Reliable State Manager gerencia a Reliable Collections para. Pode simplesmente solicitar a Reliable State Manager reliable collection por nome em qualquer altura e em qualquer lugar no seu serviço. O Gestor de estado confiável garante que obtém uma referência. Não recomendamos que guarde referências a instâncias de coleção fiável em membro de classe variáveis ou propriedades. Especial deve ter cuidado para garantir que a referência é definida para uma instância durante todo o tempo do ciclo de vida do serviço. O Reliable State Manager processa esse trabalho para e está otimizado para visitas repetidas.

### <a name="transactional-and-asynchronous-operations"></a>Operações transacionais e assíncronas
```csharp
using (ITransaction tx = this.StateManager.CreateTransaction())
{
    var result = await myDictionary.TryGetValueAsync(tx, "Counter-1");

    await myDictionary.AddOrUpdateAsync(tx, "Counter-1", 0, (k, v) => ++v);

    await tx.CommitAsync();
}
```

As coleções fiáveis tem muitas das mesmas operações que seus `System.Collections.Generic` e `System.Collections.Concurrent` contrapartes, exceto o LINQ. Operações em coleções fiáveis são assíncronas. Isto acontece porque as operações de escrita com as Reliable Collections executam operações de e/s para replicar e manter os dados no disco.

As operações de coleção fiável *transacional*, para que possa manter estado consistente em várias coleções fiáveis e operações. Por exemplo, pode remover da fila um item de trabalho de uma fila fiável, realizar uma operação no mesmo e guardar o resultado num dicionário fiável, tudo numa única transação. Isso é tratado como uma operação Atómica, e esta ação garante que toda a operação será efetuada com êxito ou toda a operação será revertida. Se ocorrer um erro depois de remover da fila do item, mas antes de guardar o resultado, toda a transação é revertida e o item permanecerá na fila para processamento.

## <a name="run-the-application"></a>Executar a aplicação
Vamos agora voltar para o *HelloWorld* aplicação. Agora pode criar e implementar os seus serviços. Quando pressiona **F5**, seu aplicativo será criado e implementado no seu cluster local.

Depois dos serviços de iniciar a execução, pode ver os eventos de rastreio de eventos para Windows (ETW) gerado numa **eventos de diagnóstico** janela. Tenha em atenção que os eventos apresentados são do serviço sem estado e o serviço com estado no aplicativo. Pode interromper o fluxo ao clicar o **colocar em pausa** botão. Em seguida, pode examinar os detalhes de uma mensagem ao expandir essa mensagem.

> [!NOTE]
> Antes de executar a aplicação, certifique-se de que tem um cluster de desenvolvimento local em execução. Veja a [guia de introdução](service-fabric-get-started.md) para obter informações sobre como configurar o ambiente local.
> 
> 

![Ver eventos de diagnóstico no Visual Studio](media/service-fabric-reliable-services-quick-start/hello-stateful-Output.png)

## <a name="next-steps"></a>Passos Seguintes
[Depurar a sua aplicação do Service Fabric no Visual Studio](service-fabric-debugging-your-application.md)

[Começar a utilizar: Serviços de API de Web do Service Fabric com o OWIN hospedagem interna](service-fabric-reliable-services-communication-webapi.md)

[Saiba mais sobre o Reliable Collections](service-fabric-reliable-services-reliable-collections.md)

[Implementar uma aplicação](service-fabric-deploy-remove-applications.md)

[Atualização da aplicação](service-fabric-application-upgrade.md)

[Referência do desenvolvedor do Reliable Services](https://msdn.microsoft.com/library/azure/dn706529.aspx)

