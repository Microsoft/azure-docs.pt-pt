---
title: Durable Functions recursos de visualização – Azure Functions
description: Saiba mais sobre os recursos de visualização para Durable Functions.
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: article
ms.date: 09/04/2019
ms.author: azfuncdf
ms.openlocfilehash: 8f2560141eac4e7d9fed267d347990e65bfe9c26
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/12/2019
ms.locfileid: "70933250"
---
# <a name="durable-functions-20-preview-azure-functions"></a>Durable Functions 2,0 Preview (Azure Functions)

*Durable Functions* é uma extensão de [Azure Functions](../functions-overview.md) e [Azure WebJobs](../../app-service/web-sites-create-web-jobs.md) que permite a gravação de funções com estado em um ambiente sem servidor. A extensão gere o estado, os pontos de verificação e os reinícios por si. Se você ainda não estiver familiarizado com Durable Functions, consulte a [documentação de visão geral](durable-functions-overview.md).

O Durable Functions 1. x é um recurso GA (disponível para o público) de Azure Functions, mas também contém vários sub-recursos que estão atualmente em visualização pública. Este artigo descreve os recursos de visualização lançados recentemente e entra em detalhes sobre como eles funcionam e como você pode começar a usá-los.

> [!NOTE]
> Esses recursos de visualização fazem parte de uma versão Durable Functions 2,0, que é atualmente uma **versão de qualidade de visualização** com várias alterações significativas. As compilações do pacote de extensão durável Azure Functions podem ser encontradas em nuget.org com versões na forma de **2.0.0-betaX**. Essas compilações não são destinadas a cargas de trabalho de produção, e as versões subsequentes podem conter alterações significativas adicionais.

## <a name="breaking-changes"></a>Alterações interruptivas

Várias alterações significativas são introduzidas no Durable Functions 2,0. Os aplicativos existentes não devem ser compatíveis com o Durable Functions 2,0 sem alterações de código. Esta seção lista algumas das alterações:

### <a name="hostjson-schema"></a>Esquema host. JSON

O trecho a seguir mostra o novo esquema para host. JSON. As principais alterações a serem consideradas são as novas subseções:

* `"storageProvider"`(e a `"azureStorage"` subseção) para configuração específica de armazenamento
* `"tracking"`para rastreamento e configuração de log
* `"notifications"`(e a `"eventGrid"` subseção) para configuração de notificação da grade de eventos

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
      "tracking": {
        "traceInputsAndOutputs": <bool?>,
        "traceReplayEvents": <bool?>,
      },
      "notifications": {
        "eventGrid": {
          "topicEndpoint": <string?>,
          "keySettingName": <string?>,
          "publishRetryCount": <string?>,
          "publishRetryInterval": <hh:mm:ss?>,
          "publishRetryHttpStatus": <int[]?>,
          "publishEventTypes": <string[]?>,
        }
      },
      "maxConcurrentActivityFunctions": <int?>,
      "maxConcurrentOrchestratorFunctions": <int?>,
      "extendedSessionsEnabled": <bool?>,
      "extendedSessionIdleTimeoutInSeconds": <int?>,
      "customLifeCycleNotificationHelperType": <string?>
  }
}
```

Como Durable Functions 2,0 continua a estabilizar, mais alterações serão introduzidas na `durableTask` seção host. JSON. Para obter mais informações sobre essas alterações, consulte [este problema do GitHub](https://github.com/Azure/azure-functions-durable-extension/issues/641).

### <a name="public-interface-changes"></a>Alterações de interface pública

Os vários objetos "Context" com suporte pelo Durable Functions tinham classes base abstratas destinadas a uso em testes de unidade. Como parte do Durable Functions 2,0, essas classes base abstratas foram substituídas por interfaces. O código de função que usa os tipos concretos diretamente não é afetado.

A tabela a seguir representa as principais alterações:

| Tipo antigo | Novo tipo |
|----------|----------|
| DurableOrchestrationClientBase | IDurableOrchestrationClient |
| DurableOrchestrationContextBase | IDurableOrchestrationContext |
| DurableActivityContextBase | IDurableActivityContext |
| OrchestrationClientAttribute | DurableClientAttribute |

No caso em que uma classe base abstrata continha métodos virtuais, esses métodos virtuais foram substituídos por métodos de extensão `DurableContextExtensions`definidos em.

## <a name="entity-functions"></a>Funções de entidade

A partir do Durable Functions v 2.0.0-Alpha, apresentamos um novo conceito de [funções de entidade](durable-functions-entities.md) .

As funções de entidade definem operações para ler e atualizar pequenas partes de estado, conhecidas como *entidades duráveis*. Como as funções de orquestrador, as funções de entidade são funções com um tipo de gatilho especial, *gatilho de entidade*. Ao contrário das funções de orquestrador, as funções de entidade não têm nenhuma restrição de código específica. As funções de entidade também gerenciam o estado explicitamente, em vez de representar implicitamente o estado por meio do fluxo de controle.

Com base nos comentários iniciais do usuário, posteriormente Adicionamos suporte para um modelo de programação baseado em classe para entidades no Durable Functions v 2.0.0-beta1.

Para saber mais, consulte o artigo [funções de entidade](durable-functions-entities.md) .

## <a name="durable-http"></a>HTTP durável

A partir do Durable Functions v 2.0.0-beta2, apresentamos um novo recurso de [http durável](durable-functions-http-features.md) que permite:

* Chamar APIs HTTP diretamente de funções de orquestração (com algumas limitações documentadas)
* Implementa sondagem de status HTTP 202 do lado do cliente automático
* Suporte interno para [identidades gerenciadas do Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)

Para saber mais, consulte o artigo [recursos http](durable-functions-http-features.md#consuming-http-apis) .

## <a name="alternate-storage-providers"></a>Provedores de armazenamento alternativos

O Framework de tarefa durável dá suporte a vários provedores de armazenamento hoje, incluindo o [armazenamento do Azure](https://github.com/Azure/durabletask/tree/master/src/DurableTask.AzureStorage), o [barramento de serviço do Azure](https://github.com/Azure/durabletask/tree/master/src/DurableTask.ServiceBus), um [emulador na memória](https://github.com/Azure/durabletask/tree/master/src/DurableTask.Emulator)e um provedor de [Redis](https://github.com/Azure/durabletask/tree/redis/src/DurableTask.Redis) experimental. No entanto, até agora, a extensão de tarefa durável para Azure Functions só tem suporte no provedor de armazenamento do Azure. A partir do Durable Functions 2,0, o suporte para provedores de armazenamento alternativo está sendo adicionado, começando com o provedor Redis.

> [!NOTE]
> Durable Functions 2,0 dá suporte apenas a provedores compatíveis com .NET Standard 2,0.

### <a name="emulator"></a>Emulator

O provedor [DurableTask. Emulator](https://www.nuget.org/packages/Microsoft.Azure.DurableTask.Emulator/) é uma memória local, um provedor de armazenamento não durável adequado para cenários de teste local. Ele pode ser configurado usando o seguinte esquema de **host. JSON** mínimo:

```json
{
  "version": "2.0",
  "extensions": {
    "durableTask": {
      "hubName": <string>,
      "storageProvider": {
        "emulator": { "enabled": true }
      }
    }
  }
}
```

### <a name="redis-experimental"></a>Redis (experimental)

O provedor [DurableTask. Redis](https://www.nuget.org/packages/Microsoft.Azure.DurableTask.Redis/) persiste todo o estado de orquestração para um cluster Redis configurado.

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

O `connectionStringName` deve referenciar o nome de uma configuração de aplicativo ou variável de ambiente. Essa configuração de aplicativo ou a variável de ambiente deve conter um valor de cadeia de conexão Redis na forma de *Server: Port*. Por exemplo, `localhost:6379` para se conectar a um cluster Redis local.

> [!NOTE]
> O provedor Redis é experimental no momento e só dá suporte a aplicativos de funções em execução em um único nó. Não há garantia de que o provedor Redis será disponibilizado para o público geral e poderá ser removido em uma versão futura.
