---
title: Durável de funções de publicação para o Azure Event Grid (pré-visualização)
description: Saiba como configurar a publicação automática do Azure Event Grid para funções duráveis.
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 03/14/2019
ms.author: glenga
ms.openlocfilehash: c07a42349fbd81a46b1b7cd9bcad1978f891a6b2
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58136366"
---
# <a name="durable-functions-publishing-to-azure-event-grid-preview"></a>Durável de funções de publicação para o Azure Event Grid (pré-visualização)

Este artigo mostra como configurar funções duráveis para publicar eventos de ciclo de vida de orquestração (como criados, concluída ou falhada) para um personalizado [tópico do Azure Event Grid](https://docs.microsoft.com/azure/event-grid/overview).

Seguem-se alguns cenários em que esta funcionalidade é útil:

* **Cenários de DevOps como implementações de azul/verde**: Pode querer saber se todas as tarefas estão em execução antes de implementar o [estratégia de implementação lado a lado](durable-functions-versioning.md#side-by-side-deployments).

* **Suporte de monitorização e diagnóstico avançado**: Pode manter o controle de informações de estado de orquestração num repositório externo otimizado para consultas, tais como a base de dados SQL ou cosmos DB.

* **Atividade de segundo plano de execução longa**: Se usar funções duráveis para uma atividade de segundo plano de execução longa, esta funcionalidade ajuda-o a saber o estado atual.

## <a name="prerequisites"></a>Pré-requisitos

* Instale [Microsoft.Azure.WebJobs.Extensions.DurableTask](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DurableTask) 1.3.0-rc ou posterior no seu projeto de funções duráveis.
* Instale [emulador de armazenamento do Azure](https://docs.microsoft.com/azure/storage/common/storage-use-emulator).
* Instale [CLI do Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) ou utilize [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview)

## <a name="create-a-custom-event-grid-topic"></a>Criar um tópico do Event grid de evento personalizado

Crie um tópico do event grid para enviar eventos de funções duráveis. As instruções seguintes mostram como criar um tópico com CLI do Azure. Para obter informações sobre como fazê-lo ao utilizar o PowerShell ou o portal do Azure, consulte os artigos seguintes:

* [Inícios rápidos de EventGrid: Criar evento personalizado - PowerShell](https://docs.microsoft.com/azure/event-grid/custom-event-quickstart-powershell)
* [Inícios rápidos de EventGrid: Criar evento personalizado - portal do Azure](https://docs.microsoft.com/azure/event-grid/custom-event-quickstart-portal)

### <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um grupo de recursos com o comando `az group create`. Atualmente, o Azure Event Grid não suporta todas as regiões. Para obter informações sobre as regiões suportadas, consulte a [descrição geral do Azure Event Grid](https://docs.microsoft.com/azure/event-grid/overview).

```bash
az group create --name eventResourceGroup --location westus2
```

### <a name="create-a-custom-topic"></a>Criar um tópico personalizado

Um tópico do event grid fornece um ponto de final definido pelo utilizador, publica o seu evento. Substitua `<topic_name>` por um nome exclusivo para o seu tópico. O nome do tópico deve ser exclusivo, porque torna-se uma entrada DNS.

```bash
az eventgrid topic create --name <topic_name> -l westus2 -g eventResourceGroup
```

## <a name="get-the-endpoint-and-key"></a>Obter o ponto final e a chave

Obter o ponto final do tópico. Substitua `<topic_name>` com o nome que escolheu.

```bash
az eventgrid topic show --name <topic_name> -g eventResourceGroup --query "endpoint" --output tsv
```

Obter a chave de tópico. Substitua `<topic_name>` com o nome que escolheu.

```bash
az eventgrid topic key list --name <topic_name> -g eventResourceGroup --query "key1" --output tsv
```

Agora pode enviar eventos para o tópico.

## <a name="configure-azure-event-grid-publishing"></a>Configurar a publicação do Azure Event Grid

No seu projeto de funções duráveis, encontre o `host.json` ficheiro.

Adicione `eventGridTopicEndpoint` e `eventGridKeySettingName` num `durableTask` propriedade.

```json
{
    "durableTask": {
        "eventGridTopicEndpoint": "https://<topic_name>.westus2-1.eventgrid.azure.net/api/events",
        "eventGridKeySettingName": "EventGridKey"
    }
}
```

As propriedades de configuração do Azure Event Grid possíveis podem ser encontradas na [documentação de Host. JSON](../functions-host-json.md#durabletask). Depois de configurar o `host.json` ficheiro, a sua aplicação function app envia eventos de ciclo de vida para o tópico do event grid. Isso funciona quando executar a aplicação de função tanto localmente como no Azure. ' '

Definir a definição de aplicação para a chave de tópico na Function App e `local.setting.json`. O JSON seguinte é um exemplo do `local.settings.json` para depuração local. Substitua `<topic_key>` com a chave de tópico.  

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

Certifique-se de que [emulador de armazenamento](https://docs.microsoft.com/azure/storage/common/storage-use-emulator) está a funcionar. É uma boa idéia para executar o `AzureStorageEmulator.exe clear all` comando antes de executar.

## <a name="create-functions-that-listen-for-events"></a>Crie funções que escutam eventos

Crie uma aplicação de funções. É melhor para localizá-la na mesma região que o tópico do event grid.

### <a name="create-an-event-grid-trigger-function"></a>Criar uma função de Acionador do event grid

Crie uma função para receber os eventos de ciclo de vida. Selecione **função personalizada**.

![Selecione uma criação de uma função personalizada.](./media/durable-functions-event-publishing/functions-portal.png)

Selecione o acionador do Event Grid e selecione `C#`.

![Selecione o acionador do Event Grid.](./media/durable-functions-event-publishing/eventgrid-trigger.png)

Introduza o nome da função e, em seguida, selecione `Create`.

![Crie o acionador do Event Grid.](./media/durable-functions-event-publishing/eventgrid-trigger-creation.png)

É criada uma função com o código a seguir:

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

Selecione `Add Event Grid Subscription`. Esta operação adiciona uma subscrição do event grid para o tópico do event grid que criou. Para obter mais informações, consulte [conceitos no Azure Event Grid](https://docs.microsoft.com/azure/event-grid/concepts)

![Selecione a ligação de Acionador do Event Grid.](./media/durable-functions-event-publishing/eventgrid-trigger-link.png)

Selecione `Event Grid Topics` para **tipo de tópico**. Selecione o grupo de recursos que criou para o tópico do event grid. Em seguida, selecione a instância do tópico do event grid. Prima `Create`.

![Crie uma subscrição do Event Grid.](./media/durable-functions-event-publishing/eventsubscription.png)

Agora, está pronto para receber eventos de ciclo de vida.

## <a name="create-durable-functions-to-send-the-events"></a>Criar funções duráveis para enviar os eventos

No seu projeto de funções duráveis, inicie a depuração no seu computador local.  O código a seguir é o mesmo que o código de modelo para as funções durável. Já configurado `host.json` e `local.settings.json` no seu computador local.

```csharp
using System.Collections.Generic;
using System.Net.Http;
using System.Threading.Tasks;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.Extensions.Logging;

namespace LifeCycleEventSpike
{
    public static class Sample
    {
        [FunctionName("Sample")]
        public static async Task<List<string>> RunOrchestrator(
            [OrchestrationTrigger] DurableOrchestrationContext context)
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
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post")]HttpRequestMessage req,
            [OrchestrationClient]DurableOrchestrationClient starter,
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

Se chamar o `Sample_HttpStart` com o Postman ou do seu browser, função durável começa a enviar eventos de ciclo de vida. O ponto final é normalmente `http://localhost:7071/api/Sample_HttpStart` para depuração local.

Consulte os registos da função que criou no portal do Azure.

```
2018-04-20T09:28:21.041 [Info] Function started (Id=3301c3ef-625f-40ce-ad4c-9ba2916b162d)
2018-04-20T09:28:21.104 [Info] {
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
    "eventTime": "2018-04-20T09:28:19.6492068Z",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "topic": "/subscriptions/<your_subscription_id>/resourceGroups/eventResourceGroup/providers/Microsoft.EventGrid/topics/durableTopic"
}

2018-04-20T09:28:21.104 [Info] Function completed (Success, Id=3301c3ef-625f-40ce-ad4c-9ba2916b162d, Duration=65ms)
2018-04-20T09:28:37.098 [Info] Function started (Id=36fadea5-198b-4345-bb8e-2837febb89a2)
2018-04-20T09:28:37.098 [Info] {
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
    "eventTime": "2018-04-20T09:28:36.5061317Z",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "topic": "/subscriptions/<your_subscription_id>/resourceGroups/eventResourceGroup/providers/Microsoft.EventGrid/topics/durableTopic"
}
2018-04-20T09:28:37.098 [Info] Function completed (Success, Id=36fadea5-198b-4345-bb8e-2837febb89a2, Duration=0ms)
```

## <a name="event-schema"></a>Esquema de Eventos

A lista a seguir explica o esquema de eventos do ciclo de vida:

* **`id`**: Identificador exclusivo para o evento de grelha de eventos.
* **`subject`**: Caminho para o assunto de evento. `durable/orchestrator/{orchestrationRuntimeStatus}`. `{orchestrationRuntimeStatus}` será `Running`, `Completed`, `Failed`, e `Terminated`.  
* **`data`**: Parâmetros específicos de funções duráveis.
  * **`hubName`**: [TaskHub](durable-functions-task-hubs.md) nome.
  * **`functionName`**: Nome da função de orquestrador.
  * **`instanceId`**: InstanceId de funções durável.
  * **`reason`**: Dados adicionais associados com o evento de controlo. Para obter mais informações, consulte [diagnósticos nas funções durável (funções do Azure)](durable-functions-diagnostics.md)
  * **`runtimeStatus`**: Estado do tempo de execução de orquestração. Em execução, concluída, falha, foi cancelada.
* **`eventType`**: "orchestratorEvent"
* **`eventTime`**: Hora do evento (UTC).
* **`dataVersion`**: Versão do esquema de eventos de ciclo de vida.
* **`metadataVersion`**:  Versão de metadados.
* **`topic`**: Recurso de tópico do Event grid.

## <a name="how-to-test-locally"></a>Como testar localmente

Para testar localmente, utilize [ngrok](../functions-bindings-event-grid.md#local-testing-with-ngrok).

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Saiba mais o gerenciamento de instância nas funções duráveis](durable-functions-instance-management.md)

> [!div class="nextstepaction"]
> [Saiba o controle de versão em funções duráveis](durable-functions-versioning.md)
