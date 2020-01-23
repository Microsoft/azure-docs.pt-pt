---
title: Durable Functions publicação na grade de eventos do Azure (visualização)
description: Saiba como configurar a publicação automática da grade de eventos do Azure para Durable Functions.
ms.topic: conceptual
ms.date: 03/14/2019
ms.openlocfilehash: 768af2e89d6523f50bd9fcc3d13cc84b711cc6f0
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/23/2020
ms.locfileid: "76547477"
---
# <a name="durable-functions-publishing-to-azure-event-grid-preview"></a>Durable Functions publicação na grade de eventos do Azure (visualização)

Este artigo mostra como configurar Durable Functions para publicar eventos de ciclo de vida de orquestração (como criado, concluído e com falha) em um [tópico personalizado da grade de eventos do Azure](https://docs.microsoft.com/azure/event-grid/overview).

A seguir, alguns cenários em que esse recurso é útil:

* **Cenários de DevOps como implantações azuis/verdes**: Talvez você queira saber se alguma tarefa está em execução antes de implementar a [estratégia de implantação lado a lado](durable-functions-versioning.md#side-by-side-deployments).

* **Monitoramento avançado e suporte a diagnósticos**: você pode manter o controle das informações de status de orquestração em um repositório externo otimizado para consultas, como o banco de dados SQL ou CosmosDB.

* **Atividade de segundo plano de execução longa**: se você usar Durable Functions para uma atividade de segundo plano de execução longa, esse recurso ajudará você a saber o status atual.

[!INCLUDE [v1-note](../../../includes/functions-durable-v1-tutorial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

* Instale [Microsoft. Azure. webjobs. Extensions. DurableTask](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DurableTask) em seu projeto Durable functions.
* Instale o [emulador de armazenamento do Azure](../../storage/common/storage-use-emulator.md).
* Instalar [CLI do Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) ou usar [Azure cloud Shell](../../cloud-shell/overview.md)

## <a name="create-a-custom-event-grid-topic"></a>Criar um tópico de grade de eventos personalizado

Crie um tópico de grade de eventos para enviar eventos de Durable Functions. As instruções a seguir mostram como criar um tópico usando CLI do Azure. Para obter informações sobre como fazer isso usando o PowerShell ou o portal do Azure, consulte os seguintes artigos:

* [EventGrid início rápido: criar evento personalizado-PowerShell](../../event-grid/custom-event-quickstart-powershell.md)
* [Início rápido do EventGrid: criar evento personalizado-portal do Azure](../../event-grid/custom-event-quickstart-portal.md)

### <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um grupo de recursos com o comando `az group create`. Atualmente, a grade de eventos do Azure não dá suporte a todas as regiões. Para obter informações sobre quais regiões têm suporte, consulte [visão geral da grade de eventos do Azure](../../event-grid/overview.md).

```bash
az group create --name eventResourceGroup --location westus2
```

### <a name="create-a-custom-topic"></a>Criar um tópico personalizado

Um tópico da grade de eventos fornece um ponto de extremidade definido pelo usuário no qual você posta seu evento. Substitua `<topic_name>` por um nome exclusivo para o seu tópico. O nome do tópico deve ser exclusivo porque se torna uma entrada DNS.

```bash
az eventgrid topic create --name <topic_name> -l westus2 -g eventResourceGroup
```

## <a name="get-the-endpoint-and-key"></a>Obter o ponto de extremidade e a chave

Obtenha o ponto de extremidade do tópico. Substitua `<topic_name>` pelo nome escolhido.

```bash
az eventgrid topic show --name <topic_name> -g eventResourceGroup --query "endpoint" --output tsv
```

Obtenha a chave do tópico. Substitua `<topic_name>` pelo nome escolhido.

```bash
az eventgrid topic key list --name <topic_name> -g eventResourceGroup --query "key1" --output tsv
```

Agora você pode enviar eventos para o tópico.

## <a name="configure-azure-event-grid-publishing"></a>Configurar publicação da grade de eventos do Azure

No projeto Durable Functions, localize o arquivo `host.json`.

Adicione `eventGridTopicEndpoint` e `eventGridKeySettingName` em uma propriedade `durableTask`.

```json
{
    "durableTask": {
        "eventGridTopicEndpoint": "https://<topic_name>.westus2-1.eventgrid.azure.net/api/events",
        "eventGridKeySettingName": "EventGridKey"
    }
}
```

As propriedades de configuração da grade de eventos do Azure possíveis podem ser encontradas na [documentação do host. JSON](../functions-host-json.md#durabletask). Depois de configurar o arquivo de `host.json`, seu aplicativo de funções envia eventos de ciclo de vida para o tópico da grade de eventos. Isso funciona quando você executa seu aplicativo de funções localmente e no Azure. ' ' '

Defina a configuração do aplicativo para a chave de tópico no Aplicativo de funções e `local.settings.json`. O JSON a seguir é um exemplo de `local.settings.json` para depuração local. Substitua `<topic_key>` pela chave do tópico.  

```json
{
    "IsEncrypted": false,
    "Values": {
        "AzureWebJobsStorage": "UseDevelopmentStorage=true",
        "AzureWebJobsDashboard": "UseDevelopmentStorage=true",
        "EventGridKey": "<topic_key>"
    }
}
```

Verifique se o [emulador de armazenamento](../../storage/common/storage-use-emulator.md) está funcionando. É uma boa ideia executar o comando `AzureStorageEmulator.exe clear all` antes de executar.

## <a name="create-functions-that-listen-for-events"></a>Criar funções que escutam eventos

Crie um Aplicativo de funções. É melhor localizá-lo na mesma região que o tópico da grade de eventos.

### <a name="create-an-event-grid-trigger-function"></a>Criar uma função de gatilho de grade de eventos

Crie uma função para receber os eventos do ciclo de vida. Selecione **função personalizada**.

![Selecione um criar uma função personalizada.](./media/durable-functions-event-publishing/functions-portal.png)

Escolha gatilho de grade de eventos e selecione `C#`.

![Selecione o gatilho de grade de eventos.](./media/durable-functions-event-publishing/eventgrid-trigger.png)

Insira o nome da função e, em seguida, selecione `Create`.

![Crie o gatilho de grade de eventos.](./media/durable-functions-event-publishing/eventgrid-trigger-creation.png)

Uma função com o código a seguir é criada:

#### <a name="precompiled-c"></a>Pré-compiladoC#
```csharp
public static void Run([HttpTrigger] JObject eventGridEvent, ILogger log)
{
    log.LogInformation(eventGridEvent.ToString(Formatting.Indented));
}
```

#### <a name="c-script"></a>C#Prescritiva

```csharp
#r "Newtonsoft.Json"
using Newtonsoft.Json;
using Newtonsoft.Json.Linq;
using Microsoft.Extensions.Logging;

public static void Run(JObject eventGridEvent, ILogger log)
{
    log.LogInformation(eventGridEvent.ToString(Formatting.Indented));
}
```

Selecione `Add Event Grid Subscription`. Esta operação adiciona uma assinatura de grade de eventos para o tópico da grade de eventos que você criou. Para obter mais informações, consulte [conceitos na grade de eventos do Azure](https://docs.microsoft.com/azure/event-grid/concepts)

![Selecione o link disparador de grade de eventos.](./media/durable-functions-event-publishing/eventgrid-trigger-link.png)

Selecione `Event Grid Topics` para **tipo de tópico**. Selecione o grupo de recursos que você criou para o tópico da grade de eventos. Em seguida, selecione a instância do tópico da grade de eventos. Pressione `Create`.

![Crie uma subscrição do Event Grid.](./media/durable-functions-event-publishing/eventsubscription.png)

Agora você está pronto para receber eventos de ciclo de vida.

## <a name="create-durable-functions-to-send-the-events"></a>Criar Durable Functions para enviar os eventos

Em seu projeto de Durable Functions, inicie a depuração no computador local.  O código a seguir é o mesmo que o código de modelo para o Durable Functions. Você já configurou `host.json` e `local.settings.json` no computador local.

### <a name="precompiled-c"></a>Pré-compiladoC#

```csharp
using System.Collections.Generic;
using System.Net.Http;
using System.Threading.Tasks;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.DurableTask;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.Extensions.Logging;

namespace LifeCycleEventSpike
{
    public static class Sample
    {
        [FunctionName("Sample")]
        public static async Task<List<string>> RunOrchestrator(
            [OrchestrationTrigger] IDurableOrchestrationContext context)
        {
            var outputs = new List<string>();

            // Replace "hello" with the name of your Durable Activity Function.
            outputs.Add(await context.CallActivityAsync<string>("Sample_Hello", "Tokyo"));
            outputs.Add(await context.CallActivityAsync<string>("Sample_Hello", "Seattle"));
            outputs.Add(await context.CallActivityAsync<string>("Sample_Hello", "London"));

            // returns ["Hello Tokyo!", "Hello Seattle!", "Hello London!"]
            return outputs;
        }

        [FunctionName("Sample_Hello")]
        public static string SayHello([ActivityTrigger] string name, ILogger log)
        {
            log.LogInformation($"Saying hello to {name}.");
            return $"Hello {name}!";
        }

        [FunctionName("Sample_HttpStart")]
        public static async Task<HttpResponseMessage> HttpStart(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post")] HttpRequestMessage req,
            [DurableClient] IDurableOrchestrationClient starter,
            ILogger log)
        {
            // Function input comes from the request content.
            string instanceId = await starter.StartNewAsync("Sample", null);
            log.LogInformation($"Started orchestration with ID = '{instanceId}'.");
            return starter.CreateCheckStatusResponse(req, instanceId);
        }
    }
}
```

> [!NOTE]
> O código anterior é para Durable Functions 2. x. Para Durable Functions 1. x, você deve usar `DurableOrchestrationContext` em vez de `IDurableOrchestrationContext`, `OrchestrationClient` atributo em vez do atributo `DurableClient`, e deve usar o tipo de parâmetro `DurableOrchestrationClient` em vez de `IDurableOrchestrationClient`. Para obter mais informações sobre as diferenças entre versões, consulte o artigo [Durable Functions versões](durable-functions-versions.md) .

Se você chamar o `Sample_HttpStart` com o postmaster ou seu navegador, a função durável começará a enviar eventos de ciclo de vida. O ponto de extremidade geralmente é `http://localhost:7071/api/Sample_HttpStart` para depuração local.

Consulte os logs da função que você criou na portal do Azure.

```
2019-04-20T09:28:21.041 [Info] Function started (Id=3301c3ef-625f-40ce-ad4c-9ba2916b162d)
2019-04-20T09:28:21.104 [Info] {
    "id": "054fe385-c017-4ce3-b38a-052ac970c39d",
    "subject": "durable/orchestrator/Running",
    "data": {
        "hubName": "DurableFunctionsHub",
        "functionName": "Sample",
        "instanceId": "055d045b1c8a415b94f7671d8df693a6",
        "reason": "",
        "runtimeStatus": "Running"
    },
    "eventType": "orchestratorEvent",
    "eventTime": "2019-04-20T09:28:19.6492068Z",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "topic": "/subscriptions/<your_subscription_id>/resourceGroups/eventResourceGroup/providers/Microsoft.EventGrid/topics/durableTopic"
}

2019-04-20T09:28:21.104 [Info] Function completed (Success, Id=3301c3ef-625f-40ce-ad4c-9ba2916b162d, Duration=65ms)
2019-04-20T09:28:37.098 [Info] Function started (Id=36fadea5-198b-4345-bb8e-2837febb89a2)
2019-04-20T09:28:37.098 [Info] {
    "id": "8cf17246-fa9c-4dad-b32a-5a868104f17b",
    "subject": "durable/orchestrator/Completed",
    "data": {
        "hubName": "DurableFunctionsHub",
        "functionName": "Sample",
        "instanceId": "055d045b1c8a415b94f7671d8df693a6",
        "reason": "",
        "runtimeStatus": "Completed"
    },
    "eventType": "orchestratorEvent",
    "eventTime": "2019-04-20T09:28:36.5061317Z",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "topic": "/subscriptions/<your_subscription_id>/resourceGroups/eventResourceGroup/providers/Microsoft.EventGrid/topics/durableTopic"
}
2019-04-20T09:28:37.098 [Info] Function completed (Success, Id=36fadea5-198b-4345-bb8e-2837febb89a2, Duration=0ms)
```

## <a name="event-schema"></a>Esquema de evento

A lista a seguir explica o esquema de eventos de ciclo de vida:

* **`id`** : identificador exclusivo para o evento da grade de eventos.
* **`subject`** : caminho para o assunto do evento. `durable/orchestrator/{orchestrationRuntimeStatus}`. `{orchestrationRuntimeStatus}` será `Running`, `Completed`, `Failed`e `Terminated`.  
* **`data`** : Durable Functions parâmetros específicos.
  * **`hubName`** : nome do [TaskHub](durable-functions-task-hubs.md) .
  * **`functionName`** : nome da função de orquestrador.
  * **`instanceId`** : Durable Functions InstanceId.
  * **`reason`** : dados adicionais associados ao evento de rastreamento. Para obter mais informações, consulte [Diagnostics in Durable Functions (Azure Functions)](durable-functions-diagnostics.md)
  * **`runtimeStatus`** : status do tempo de execução de orquestração. Em execução, concluído, com falha, cancelado.
* **`eventType`** : "orchestratorEvent"
* **`eventTime`** : hora do evento (UTC).
* **`dataVersion`** : versão do esquema de eventos do ciclo de vida.
* **`metadataVersion`** : versão dos metadados.
* **`topic`** : recurso de tópico da grade de eventos.

## <a name="how-to-test-locally"></a>Como testar localmente

Para testar localmente, leia a [depuração local do gatilho de grade de eventos da função do Azure](../functions-debug-event-grid-trigger-local.md).

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Aprenda o gerenciamento de instâncias no Durable Functions](durable-functions-instance-management.md)

> [!div class="nextstepaction"]
> [Aprenda o controle de versão no Durable Functions](durable-functions-versioning.md)
