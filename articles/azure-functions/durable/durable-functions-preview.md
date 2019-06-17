---
title: Funcionalidades - as funções do Azure de pré-visualização de funções duráveis
description: Saiba mais sobre as funcionalidades de pré-visualização para funções duráveis.
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.date: 04/23/2019
ms.author: azfuncdf
ms.openlocfilehash: 8ceb84ab9e9c41ff6a9cbde62571fb12ae67d790
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65596074"
---
# <a name="durable-functions-20-preview-azure-functions"></a>Durável Functions 2.0 pré-visualização (funções do Azure)

*Funções duráveis* é uma extensão da [as funções do Azure](../functions-overview.md) e [WebJobs do Azure](../../app-service/web-sites-create-web-jobs.md) que permite que escreva funções com monitoração de estado num ambiente sem servidor. A extensão gere o estado, os pontos de verificação e os reinícios por si. Se não já estiver familiarizado com as funções durável, consulte a [documentação de visão geral](durable-functions-overview.md).

Funções duráveis é uma funcionalidade de disponibilidade geral (disponível em geral) das funções do Azure, mas também contém vários subfuncionalidades que estão atualmente em pré-visualização pública. Este artigo descreve as funcionalidades de pré-visualização recém-lançada e entra em detalhes sobre como funcionam e como pode começar a usá-los.

> [!NOTE]
> Estas funcionalidades de pré-visualização fazem parte de uma versão 2.0 de funções duráveis, o que é atualmente um **versão alfa qualidade** com várias alterações de última hora. O Azure funções durável cria o pacote de extensão pode ser encontrado em nuget.org com versões na forma de **2.0.0-alpha**. Essas compilações não são adequadas para quaisquer cargas de trabalho de produção e versões posteriores podem conter alterações de última hora adicionais.

## <a name="breaking-changes"></a>Alterações interruptivas

Várias alterações de última hora são introduzidas no 2.0 de funções durável. As aplicações existentes não são esperadas para ser compatível com 2.0 de funções durável sem alterações de código. Esta secção lista algumas das alterações:

### <a name="dropping-net-framework-support"></a>Remover o suporte ao .NET Framework

Suporte para o .NET Framework (e, portanto, as funções 1.0) foi removido para 2.0 de funções durável. É a principal razão permitir que os contribuidores de não-Windows para facilmente criar e testar as alterações que fizer para funções duráveis do macOS e Linux plataformas. É o motivo secundário para o ajudar a encorajar os desenvolvedores a mover para a versão mais recente do runtime das funções do Azure.

### <a name="hostjson-schema"></a>Esquema de Host. JSON

O fragmento seguinte mostra o novo esquema para o Host. JSON. A principal alteração ter em consideração é a nova `"storageProvider"` secção e o `"azureStorage"` secção abaixo dela. Esta alteração foi efetuada para suportar [alternativo fornecedores de armazenamento](durable-functions-preview.md#alternate-storage-providers).

```json
{
  "version": "2.0",
  "extensions": {
    "durableTask": {
      "hubName": <string>,
      "storageProvider": {
        "azureStorage": {
          "connectionStringName": <string>,
          "controlQueueBatchSize": <int?>,
          "partitionCount": <int?>,
          "controlQueueVisibilityTimeout": <hh:mm:ss?>,
          "workItemQueueVisibilityTimeout": <hh:mm:ss?>,
          "trackingStoreConnectionStringName": <string?>,
          "trackingStoreNamePrefix": <string?>,
          "maxQueuePollingInterval": <hh:mm:ss?>
        }
      },
      "maxConcurrentActivityFunctions": <int?>,
      "maxConcurrentOrchestratorFunctions": <int?>,
      "traceInputAndOutputs": <bool?>,
      "eventGridTopicEndpoint": <string?>,
      "eventGridKeySettingName": <string?>,
      "eventGridPublishRetryCount": <string?>,
      "eventGridPublishRetryInterval": <hh:mm:ss?>,
      "eventGridPublishRetryHttpStatus": <int[]?>,
      "eventgridPublishEventTypes": <string[]?>,
      "customLifeCycleNotificationHelperType"
      "extendedSessionsEnabled": <bool?>,
      "extendedSessionIdleTimeoutInSeconds": <int?>,
      "logReplayEvents": <bool?>
  }
}
```

Como durável Functions 2.0 continua a estabilizar, mais alterações irão ser apresentadas para o `durableTask` secção Host. JSON. Para obter mais informações sobre estas alterações, consulte [este problema do GitHub](https://github.com/Azure/azure-functions-durable-extension/issues/641).

### <a name="public-interface-changes"></a>Alterações de interface pública

Os vários objetos de "contexto" suportados pelas funções duráveis tinham as classes base abstratas que pretende utilizar no teste de unidade. Como parte do 2.0 de funções durável, estas classes base abstratas foram substituídas com interfaces. Código de função que usa os tipos concretos diretamente não são afetados.

A tabela seguinte representa as alterações principais:

| Antigo tipo | Novo tipo |
|----------|----------|
| DurableOrchestrationClientBase | IDurableOrchestrationClient |
| DurableOrchestrationContextBase | IDurableOrchestrationContext |
| DurableActivityContextBase | IDurableActivityContext |

No caso em que uma classe base abstrata continha métodos virtuais, esses métodos virtuais foram substituídos por métodos de extensão definidos em `DurableContextExtensions`.

## <a name="entity-functions"></a>Funções de entidade

Funções de entidade definem operações de leitura e atualizando pequenas partes do Estado, conhecido como *entidades duráveis*. Como funções do orchestrator, as funções de entidade são as funções com um tipo de Acionador especial *acionador de entidade*. Ao contrário das funções do orchestrator, as funções de entidade não tem quaisquer restrições de código específica. Funções de entidade também gerir o estado explicitamente em vez de implicitamente que representa o estado através do fluxo de controle.

O código a seguir é um exemplo de uma função de entidade simples que define um *contador* entidade. A função define três operações, `add`, `subtract`, e `reset`, cada de atualização que um número inteiro, `currentValue`.

```csharp
[FunctionName("Counter")]
public static async Task Counter(
    [EntityTrigger] IDurableEntityContext ctx)
{
    int currentValue = ctx.GetState<int>();
    int operand = ctx.GetInput<int>();

    switch (ctx.OperationName)
    {
        case "add":
            currentValue += operand;
            break;
        case "subtract":
            currentValue -= operand;
            break;
        case "reset":
            await SendResetNotificationAsync();
            currentValue = 0;
            break;
    }

    ctx.SetState(currentValue);
}
```

Entidade *instâncias* são acedidas através de um identificador exclusivo, o *ID de entidade*. Um ID de entidade é simplesmente um par de cadeias de caracteres que identifica exclusivamente uma instância de entidade. Ele consiste em:

1. uma **nome da entidade**: um nome que identifica o tipo de entidade (por exemplo, "Contador")
2. uma **chave de entidade**: uma cadeia que identifica de forma exclusiva a entidade entre todas as outras entidades, o mesmo nome (por exemplo, um GUID)

Por exemplo, um *contador* a função de entidade pode ser utilizada para manter a classificação num jogo online. Cada instância do jogo tem um ID de entidade exclusivo, tal como `@Counter@Game1`, `@Counter@Game2`e assim por diante.

### <a name="comparison-with-virtual-actors"></a>Comparação com virtual atores

A estrutura de entidades durável é bastante influenciada pela [modelo de ator](https://en.wikipedia.org/wiki/Actor_model). Se já estiver familiarizado com actors, em seguida, os conceitos por trás de entidades duráveis devem ser familiares para. Em particular, são semelhantes às entidades duráveis [atores virtual](https://research.microsoft.com/en-us/projects/orleans/) de várias maneiras:

* Entidades duráveis são endereçáveis através de um *ID de entidade*.
* Operações de entidade durável em série, executadas uma por vez, para evitar condições de corrida.
* Entidades duráveis são criadas automaticamente quando são chamados ou sinalizados.
* Ao não executar operações, duráveis entidades são silenciosamente baixadas da memória.

Existem algumas diferenças importantes, no entanto, que são a pena observar:

* Entidades duráveis são modeladas como funções puras. Esse design é diferente da maioria das estruturas e orientado a objetos que representam os atores com suporte de idioma específico para classes, propriedades e métodos.
* Entidades duráveis priorizar *durabilidade* sobre *latência*e, portanto, pode não ser apropriado para aplicativos com requisitos de latência rigorosa.
* As mensagens enviadas entre entidades são entregues por ordem e confiável.
* Entidades duráveis podem ser utilizadas em conjunto com orquestrações duráveis e podem servir como bloqueios distribuídos, o que são descritos posteriormente neste artigo.
* Padrões de solicitação/resposta nas entidades estão limitados a orquestrações. Para a comunicação de entidade-para-entidade, são permitidas apenas as mensagens unidirecionais (também conhecido como "sinalização"), como no modelo de ator original. Esse comportamento evita deadlocks distribuídos.

### <a name="durable-entity-apis"></a>APIs de entidade durável

Suporte de entidade envolve várias APIs. Para um, existe uma nova API para a definição de funções de entidade, conforme mostrado acima, que especifica o que deve acontecer quando uma operação ser invocada numa entidade. Além disso, as APIs existentes para clientes e orquestrações foram atualizadas com a nova funcionalidade para interação com entidades.

### <a name="implementing-entity-operations"></a>Implementar operações de entidade

A execução de uma operação numa entidade pode chamar esses membros do objeto de contexto (`IDurableEntityContext` no .NET):

* **OperationName**: obtém o nome da operação.
* **GetInput\<T >** : obtém a entrada para a operação.
* **GetState\<T >** : obtém o estado atual da entidade.
* **SetState**: atualiza o estado da entidade.
* **SignalEntity**: envia uma mensagem unidirecional para uma entidade.
* **Self**: obtém o ID da entidade.
* **Devolver**: retorna um valor para o cliente ou uma orquestração que chama a operação.
* **IsNewlyConstructed**: devolve `true` se a entidade não existia antes da operação.
* **DestructOnExit**: elimina a entidade depois de concluir a operação.

As operações são menos restritas que orquestrações:

* Operações podem chamar a e/s externos, através de APIs síncronas ou assíncronas (Recomendamos que utilize apenas aqueles assíncronas).
* As operações podem ser determinística. Por exemplo, é seguro chamar `DateTime.UtcNow`, `Guid.NewGuid()` ou `new Random()`.

### <a name="accessing-entities-from-clients"></a>Acesso a entidades de clientes

Entidades duráveis podem ser invocadas a partir de funções comuns através da `orchestrationClient` enlace (`IDurableOrchestrationClient` no .NET). São suportados os seguintes métodos:

* **ReadEntityStateAsync\<T >** : lê o estado de uma entidade.
* **SignalEntityAsync**: envia uma mensagem unidirecional para uma entidade e aguarda para que elas sejam colocados em fila.

Esses métodos priorizar o desempenho ao longo de consistência: `ReadEntityStateAsync` pode devolver um valor obsoleto, e `SignalEntityAsync` pode retornar antes da operação foi concluída. Por outro lado, a chamada de entidades de orquestrações (conforme descrito a seguir) é vivamente consistente.

### <a name="accessing-entities-from-orchestrations"></a>Acesso a entidades de orquestrações

Orquestrações podem aceder a entidades usando o objeto de contexto. Podem escolher entre comunicação unidirecional (disparar e esquecer) e a comunicação bidirecional (solicitação e resposta). Os métodos correspondentes são

* **SignalEntity**: envia uma mensagem unidirecional para uma entidade.
* **CallEntityAsync**: envia uma mensagem para uma entidade e aguarda uma resposta a indicar que a operação foi concluída.
* **CallEntityAsync\<T >** : envia uma mensagem para uma entidade e aguarda uma resposta que contém um resultado do tipo T.

Ao utilizar a comunicação bidirecional, todas as exceções lançadas durante a execução da operação também são transmitidas de volta para a orquestração de chamada e relançadas. Por outro lado, quando utilizar disparar e esquecer, exceções não são respeitadas.

### <a name="locking-entities-from-orchestrations"></a>Entidades de bloqueios de orquestrações

Orquestrações podem bloquear as entidades. Esta capacidade fornece uma forma simples de evitar corridas indesejadas usando *seções críticas*.

O objeto context fornece os seguintes métodos:

* **LockAsync**: adquire bloqueios numa ou mais entidades.
* **IsLocked**: retorna true se atualmente numa seção crítica, false caso contrário.

A seção crítica ends e todos os bloqueios são lançados, quando termina de orquestração. No .NET, `LockAsync` retorna um `IDisposable` que termina a seção crítica quando descartado, que pode ser utilizada em conjunto com um `using` cláusula para obter uma representação sintática de seção crítica.

Por exemplo, considere uma orquestração que precisa para testar se os dois jogadores estão disponíveis e, em seguida, atribuí-los a um jogo. Esta tarefa pode ser implementada usando uma seção crítica da seguinte forma:

```csharp
[FunctionName("Orchestrator")]
public static async Task RunOrchestrator(
    [OrchestrationTrigger] IDurableOrchestrationContext ctx)
{
    EntityId player1 = /* ... */;
    EntityId player2 = /* ... */;

    using (await ctx.LockAsync(player1, player2))
    {
        bool available1 = await ctx.CallEntityAsync<bool>(player1, "is-available");
        bool available2 = await ctx.CallEntityAsync<bool>(player2, "is-available");

        if (available1 && available2)
        {
            Guid gameId = ctx.NewGuid();

            await ctx.CallEntityAsync(player1, "assign-game", gameId);
            await ctx.CallEntityAsync(player2, "assign-game", gameId);
        }
    }
}
```

Dentro da seção crítica, as duas entidades de player estejam bloqueadas, que significa que não está a executar quaisquer operações diferentes dos que são chamados a partir da seção crítica). Este comportamento impede corridas com operações em conflito, tais como os jogadores que está sendo atribuídos a outro jogos ou assinatura desativado.

Podemos impõe várias restrições das seções críticas como pode ser utilizado. Estas restrições servem para evitar deadlocks e reentrância.

* Não não possível aninhar seções críticas.
* Seções críticas não é possível criar suborchestrations.
* Seções críticas podem chamar apenas as entidades que têm de ser bloqueados.
* Seções críticas não é possível chamar a mesma entidade usando várias chamadas paralelas.
* Seções críticas podem sinalizar apenas as entidades que não têm a ser bloqueados.

## <a name="alternate-storage-providers"></a>Fornecedores de armazenamento alternativo

A estrutura de tarefa durável suporta vários fornecedores de armazenamento hoje em dia, incluindo [armazenamento do Azure](https://github.com/Azure/durabletask/tree/master/src/DurableTask.AzureStorage), [do Azure Service Bus](https://github.com/Azure/durabletask/tree/master/src/DurableTask.ServiceBus), uma [emulador na memória](https://github.com/Azure/durabletask/tree/master/src/DurableTask.Emulator)e um experimentais [Redis](https://github.com/Azure/durabletask/tree/redis/src/DurableTask.Redis) fornecedor. No entanto, até agora, a extensão de tarefas durável para as funções do Azure só suportado o fornecedor de armazenamento do Azure. A partir do 2.0 de funções durável, suporte para fornecedores de armazenamento alternativo está a ser adicionada, começando com o fornecedor de Redis.

> [!NOTE]
> 2\.0 de funções durável só suporta os fornecedores de 2.0 compatível com .NET Standard. No momento da escrita, o fornecedor do Azure Service Bus não suporta o .NET Standard 2.0 e, portanto, não está disponível como um fornecedor de armazenamento alternativo.

### <a name="emulator"></a>Emulador

O [DurableTask.Emulator](https://www.nuget.org/packages/Microsoft.Azure.DurableTask.Emulator/) fornecedor é um local da memória, o fornecedor de armazenamento não duradoura adequado a cenários de testes locais. Pode ser configurada utilizando os seguintes mínimos **Host. JSON** esquema:

```json
{
  "version": "2.0",
  "extensions": {
    "durableTask": {
      "hubName": <string>,
      "storageProvider": {
        "emulator": { }
      }
    }
  }
}
```

### <a name="redis-experimental"></a>(Experimental) de redis

O [DurableTask.Redis](https://www.nuget.org/packages/Microsoft.Azure.DurableTask.Redis/) fornecedor persistir todos os Estados de orquestração para um cluster de Redis configurado.

```json
{
  "version": "2.0",
  "extensions": {
    "durableTask": {
      "hubName": <string>,
      "storageProvider": {
        "redis": {
          "connectionStringName": <string>,
        }
      }
    }
  }
}
```

O `connectionStringName` tem de referenciar o nome de uma variável de ambiente ou definição de aplicação. Essa variável de ambiente ou definição de aplicação deve conter um valor de cadeia de ligação de Redis na forma de *porta do servidor:* . Por exemplo, `localhost:6379` para ligar a um cluster de Redis local.

> [!NOTE]
> O fornecedor de Redis é atualmente experimental e só suporta aplicações de função em execução num único nó.
