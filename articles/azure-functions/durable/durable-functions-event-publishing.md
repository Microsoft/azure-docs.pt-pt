---
title: Funções Duráveis publicação na Grelha de Eventos Azure (pré-visualização)
description: Saiba como configurar a publicação automática da Rede de Eventos Azure para Funções Duráveis.
ms.topic: conceptual
ms.date: 03/14/2019
ms.openlocfilehash: 52ffcd4eb81936ffcfa61580288c60bd59ffb744
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2020
ms.locfileid: "78249767"
---
# <a name="durable-functions-publishing-to-azure-event-grid-preview"></a>Funções Duráveis publicação na Grelha de Eventos Azure (pré-visualização)

Este artigo mostra como configurar Funções Duráveis para publicar eventos de ciclo de vida de orquestração (tais como criados, concluídos e falhados) a um tópico personalizado da grelha de [eventos azure](https://docs.microsoft.com/azure/event-grid/overview).

Seguem-se alguns cenários em que esta funcionalidade é útil:

* **Cenários de DevOps como implementações azuis/verdes**: É melhor saber se alguma tarefa está a decorrer antes de implementar a [estratégia de implantação lado a lado](durable-functions-versioning.md#side-by-side-deployments).

* Suporte avançado de **monitorização e diagnóstico:** Pode acompanhar as informações sobre o estado da orquestração numa loja externa otimizada para consultas, como a Base de Dados Azure SQL ou a Azure Cosmos DB.

* **Atividade de fundo de longa duração**: Se utilizar funções duráveis para uma atividade de fundo de longa duração, esta funcionalidade ajuda-o a conhecer o estado atual.

## <a name="prerequisites"></a>Pré-requisitos

* Instale [microsoft.Azure.WebJobs.Extensions.DurableTask](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DurableTask) no seu projeto Funções Duráveis.
* Instale o [Emulador](../../storage/common/storage-use-emulator.md) de Armazenamento Azure (apenas para windows) ou utilize uma conta de Armazenamento Azure existente.
* Instale [o Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) ou utilize [a Casca de Nuvem Azure](../../cloud-shell/overview.md)

## <a name="create-a-custom-event-grid-topic"></a>Crie um tópico personalizado da Grelha de Eventos

Crie um tópico de Grelha de Eventos para o envio de eventos a partir de Funções Duráveis. As seguintes instruções mostram como criar um tópico utilizando o Azure CLI. Também pode fazê-lo utilizando o [PowerShell](../../event-grid/custom-event-quickstart-powershell.md) ou [utilizando o portal Azure](../../event-grid/custom-event-quickstart-portal.md).

### <a name="create-a-resource-group"></a>Criar um grupo de recursos:

Crie um grupo de recursos com o comando `az group create`. Atualmente, a Azure Event Grid não apoia todas as regiões. Para obter informações sobre quais as regiões apoiadas, consulte a visão geral da Grelha de [Eventos Azure.](../../event-grid/overview.md)

```azurecli
az group create --name eventResourceGroup --location westus2
```

### <a name="create-a-custom-topic"></a>Criar um tópico personalizado

Um tópico da Grelha de Eventos fornece um ponto final definido pelo utilizador para o que publica o seu evento. Substitua `<topic_name>` por um nome exclusivo para o seu tópico. O nome tópico deve ser único porque se torna uma entrada DNS.

```azurecli
az eventgrid topic create --name <topic_name> -l westus2 -g eventResourceGroup
```

## <a name="get-the-endpoint-and-key"></a>Obtenha o ponto final e a chave

Pegue o ponto final do tópico. Substitua `<topic_name>` pelo nome que escolheu.

```azurecli
az eventgrid topic show --name <topic_name> -g eventResourceGroup --query "endpoint" --output tsv
```

Pegue a chave do tópico. Substitua `<topic_name>` pelo nome que escolheu.

```azurecli
az eventgrid topic key list --name <topic_name> -g eventResourceGroup --query "key1" --output tsv
```

Agora pode enviar eventos para o tema.

## <a name="configure-event-grid-publishing"></a>Configure a publicação da Rede de Eventos

No seu projeto DeFunções Duráveis, encontre o ficheiro `host.json`.

Adicione `eventGridTopicEndpoint` e `eventGridKeySettingName` numa propriedade `durableTask`.

```json
{
    "durableTask": {
        "eventGridTopicEndpoint": "https://<topic_name>.westus2-1.eventgrid.azure.net/api/events",
        "eventGridKeySettingName": "EventGridKey"
    }
}
```

As possíveis propriedades de configuração da Grelha de Eventos Azure podem ser encontradas na [documentação host.json](../functions-host-json.md#durabletask). Depois de configurar o ficheiro `host.json`, a sua aplicação de função envia eventos de ciclo de vida para o tópico da Grelha de Eventos. Isto funciona quando executa a sua aplicação de funções tanto localmente como em Azure.

Defina a definição da aplicação para a chave de tópicos na App de Funções e `local.settings.json`. O JSON seguinte é uma amostra do `local.settings.json` para depuração local. Substitua `<topic_key>` com a chave do tópico.  

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

Se estiver a utilizar o [Emulador](../../storage/common/storage-use-emulator.md) de Armazenamento (apenas windows), certifique-se de que está a funcionar. É uma boa ideia dirigir o comando `AzureStorageEmulator.exe clear all` antes de executar.

Se estiver a utilizar uma conta de Armazenamento Azure existente, substitua `UseDevelopmentStorage=true` em `local.settings.json` com a sua cadeia de ligação.

## <a name="create-functions-that-listen-for-events"></a>Criar funções que ouçam eventos

Utilizando o portal Azure, crie outra aplicação de função para ouvir os eventos publicados pela sua aplicação Funções Duráveis. É melhor localizá-lo na mesma região que o tópico da Grelha de Eventos.

### <a name="create-an-event-grid-trigger-function"></a>Criar uma função de gatilho da Grelha de Eventos

Crie uma função para receber os eventos de ciclo de vida. Selecione **Função Personalizada**.

![Selecione uma função personalizada.](./media/durable-functions-event-publishing/functions-portal.png)

Escolha o Gatilho da Grelha de Eventos e selecione um idioma.

![Selecione o Gatilho da Grelha de Eventos.](./media/durable-functions-event-publishing/eventgrid-trigger.png)

Introduza o nome da função e, em seguida, selecione `Create`.

![Crie o Gatilho da Grelha de Eventos.](./media/durable-functions-event-publishing/eventgrid-trigger-creation.png)

É criada uma função com o seguinte código:

# <a name="c-script"></a>[C#Roteiro](#tab/csharp-script)

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

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
module.exports = async function(context, eventGridEvent) {
    context.log(typeof eventGridEvent);
    context.log(eventGridEvent);
}
```

---

Selecione `Add Event Grid Subscription`. Esta operação adiciona uma subscrição da Grelha de Eventos para o tópico da Grelha de Eventos que criou. Para mais informações, consulte [Conceitos em Grelha de Eventos Azure](https://docs.microsoft.com/azure/event-grid/concepts)

![Selecione a ligação de gatilho da grelha de eventos.](./media/durable-functions-event-publishing/eventgrid-trigger-link.png)

Selecione `Event Grid Topics` para **o Tipo tópico**. Selecione o grupo de recursos que criou para o tópico da Grelha de Eventos. Em seguida, selecione a instância do tópico da Grelha de Eventos. Pressione `Create`.

![Crie uma subscrição do Event Grid.](./media/durable-functions-event-publishing/eventsubscription.png)

Agora estápronto para receber eventos de ciclo de vida.

## <a name="run-durable-functions-app-to-send-the-events"></a>Executar aplicativo Funções Duráveis para enviar os eventos

No projeto Funções Duráveis que configuraste anteriormente, começa a depurar a sua máquina local e iniciar uma orquestração. A aplicação publica eventos de ciclo de vida de Funções Duráveis para a Grelha de Eventos. Verifique se a Grelha de Eventos aciona a função de ouvinte que criou verificando os seus registos no portal Azure.

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

## <a name="event-schema"></a>Evento Schema

A lista que se segue explica o esquema de eventos de ciclo de vida:

* **`id`** : Identificador único para o evento Event Grid.
* **`subject`** : Caminho para o assunto do evento. `durable/orchestrator/{orchestrationRuntimeStatus}`. `{orchestrationRuntimeStatus}` serão `Running`, `Completed`, `Failed`e `Terminated`.  
* **`data`:** Parâmetros específicos de funções duráveis.
  * **`hubName`** : Nome [TaskHub.](durable-functions-task-hubs.md)
  * **`functionName`** : Nome de função de orquestrador.
  * **`instanceId`:** Funções Duráveis instânciaId.
  * **`reason`** : Dados adicionais associados ao evento de rastreio. Para mais informações, consulte [Diagnósticos em Funções Duráveis (Funções Azure)](durable-functions-diagnostics.md)
  * **`runtimeStatus`** : Estado de execução da orquestração. Correr, Completar, Falhar, Cancelado.
* **`eventType`** : "orchestratorEvent"
* **`eventTime`** : Hora do evento (UTC).
* **`dataVersion`** : Versão do esquema do evento lifecycle.
* **`metadataVersion`** : Versão dos metadados.
* **`topic`** : Recurso tópico da grelha de eventos.

## <a name="how-to-test-locally"></a>Como testar localmente

Para testar localmente, leia a grelha de eventos de [função Azure Acionar a depuração local](../functions-debug-event-grid-trigger-local.md).

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Aprenda gestão de instâncias em Funções Duráveis](durable-functions-instance-management.md)

> [!div class="nextstepaction"]
> [Aprenda a versonionar em Funções Duráveis](durable-functions-versioning.md)
