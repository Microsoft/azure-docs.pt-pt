---
title: Funções Duráveis publicação na Grelha de Eventos Azure (pré-visualização)
description: Saiba como configurar a publicação automática da Rede de Eventos Azure para Funções Duráveis.
ms.topic: conceptual
ms.date: 04/25/2020
ms.openlocfilehash: c0106f3754e0cdcbf1f295fbe3f1b5def8dc3ca1
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/12/2020
ms.locfileid: "83124293"
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

Crie um tópico de Grelha de Eventos para o envio de eventos a partir de Funções Duráveis. As seguintes instruções mostram como criar um tópico utilizando o Azure CLI. Também pode criar o tópico utilizando o [PowerShell](../../event-grid/custom-event-quickstart-powershell.md) ou [utilizando o portal Azure](../../event-grid/custom-event-quickstart-portal.md).

### <a name="create-a-resource-group"></a>Criar um grupo de recursos

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

No seu projeto DeFunções Duráveis, encontre o `host.json` ficheiro.

### <a name="durable-functions-1x"></a>Funções Duráveis 1.x

Adicione `eventGridTopicEndpoint` e em uma `eventGridKeySettingName` `durableTask` propriedade.

```json
{
  "durableTask": {
    "eventGridTopicEndpoint": "https://<topic_name>.westus2-1.eventgrid.azure.net/api/events",
    "eventGridKeySettingName": "EventGridKey"
  }
}
```

### <a name="durable-functions-2x"></a>Funções Duráveis 2.x

Adicione uma `notifications` secção à propriedade do `durableTask` ficheiro, substituindo `<topic_name>` pelo nome que escolheu. Se as `durableTask` propriedades ou propriedades não `extensions` existirem, crie-as como este exemplo:

```json
{
  "version": "2.0",
  "extensions": {
    "durableTask": {
      "notifications": {
        "eventGrid": {
          "topicEndpoint": "https://<topic_name>.westus2-1.eventgrid.azure.net/api/events",
          "keySettingName": "EventGridKey"
        }
      }
    }
  }
}
```

As possíveis propriedades de configuração da Grelha de Eventos Azure podem ser encontradas na [documentação host.json](../functions-host-json.md#durabletask). Depois de configurar o ficheiro, a sua aplicação de função envia eventos de ciclo de vida para o tópico da Grelha de `host.json` Eventos. Esta ação começa quando executa a sua app de funções tanto localmente como em Azure.

Defina a definição da aplicação para a chave de tópicos na App de Funções e `local.settings.json` . O JSON seguinte é uma amostra da `local.settings.json` depuração local. `<topic_key>`Substitua-a com a tecla de tópico.  

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

Se estiver a utilizar o [Emulador](../../storage/common/storage-use-emulator.md) de Armazenamento (apenas windows), certifique-se de que está a funcionar. É uma boa ideia dirigir o comando antes de `AzureStorageEmulator.exe clear all` executar.

Se estiver a utilizar uma conta de Armazenamento Azure existente, substitua-a com a sua cadeia de `UseDevelopmentStorage=true` `local.settings.json` ligação.

## <a name="create-functions-that-listen-for-events"></a>Criar funções que ouçam eventos

Utilizando o portal Azure, crie outra aplicação de função para ouvir os eventos publicados pela sua aplicação Funções Duráveis. É melhor localizá-lo na mesma região que o tópico da Grelha de Eventos.

### <a name="create-an-event-grid-trigger-function"></a>Criar uma função de gatilho da Grelha de Eventos

1. Na sua aplicação de funções, selecione **Funções,** e depois selecione **+ Adicionar** 

   :::image type="content" source="./media/durable-functions-event-publishing/function-add-function.png" alt-text="Adicione uma função no portal Azure." border="true":::

1. Procure a **Grelha de Eventos**e, em seguida, selecione o modelo de gatilho da Grelha de **Eventos Azure.** 

    :::image type="content" source="./media/durable-functions-event-publishing/function-select-event-grid-trigger.png" alt-text="Selecione o modelo de gatilho da grelha de eventos no portal Azure." border="true":::

1. Nomeie o novo gatilho e, em seguida, selecione **Criar Função**.

    :::image type="content" source="./media/durable-functions-event-publishing/function-name-event-grid-trigger.png" alt-text="Nomeie o gatilho da grelha do evento no portal Azure." border="true":::


    É criada uma função com o seguinte código:

    # <a name="c-script"></a>[C# Script](#tab/csharp-script)

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

### <a name="add-an-event-grid-subscription"></a>Adicione uma subscrição da Grelha de Eventos

Agora pode adicionar uma subscrição de Event Grid para o tópico da Grelha de Eventos que criou. Para mais informações, consulte [Concepts in Azure Event Grid](https://docs.microsoft.com/azure/event-grid/concepts).

1. Na sua nova função, selecione **Integração** e, em seguida, selecione **Event Grid Trigger (eventGridEvent)**. 

    :::image type="content" source="./media/durable-functions-event-publishing/eventgrid-trigger-link.png" alt-text="Selecione a ligação de gatilho da grelha de eventos." border="true":::

1. **Selecione Criar descrição da grelha**de eventos .

    :::image type="content" source="./media/durable-functions-event-publishing/create-event-grid-subscription.png" alt-text="Crie a subscrição da Grelha de Eventos." border="true":::

1. Nomeie a subscrição do seu evento e selecione o tipo de tópicos da Grelha de **Eventos.** 

1. Selecione uma subscrição. Em seguida, selecione o grupo de recursos e o recurso que criou para o tópico da Grelha de Eventos. 

1. Selecione **Criar**.

    :::image type="content" source="./media/durable-functions-event-publishing/event-grid-subscription-details.png" alt-text="Crie uma subscrição da Grelha de Eventos." border="true":::

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

## <a name="event-schema"></a>Esquema de Eventos

A lista que se segue explica o esquema de eventos de ciclo de vida:

* **`id`**: Identificador único para o evento Event Grid.
* **`subject`**: Caminho para o assunto do evento. `durable/orchestrator/{orchestrationRuntimeStatus}`. `{orchestrationRuntimeStatus}`será, `Running` `Completed` `Failed` e `Terminated` .  
* **`data`**: Funções Duráveis Parâmetros específicos.
  * **`hubName`**: [Nome TaskHub.](durable-functions-task-hubs.md)
  * **`functionName`**: Nome de função orquestrador.
  * **`instanceId`**: Funções Duráveis instânciaId.
  * **`reason`**: Dados adicionais associados ao evento de rastreio. Para mais informações, consulte [Diagnósticos em Funções Duráveis (Funções Azure)](durable-functions-diagnostics.md)
  * **`runtimeStatus`**: Estado de execução da orquestração. Correr, Completar, Falhar, Cancelado.
* **`eventType`**: "orchestratorEvent"
* **`eventTime`**: Hora do evento (UTC).
* **`dataVersion`**: Versão do esquema do evento lifecycle.
* **`metadataVersion`**: Versão dos metadados.
* **`topic`**: Recurso tópico da grelha de eventos.

## <a name="how-to-test-locally"></a>Como testar localmente

Para testar localmente, leia a grelha de eventos de [função Azure Acionar a depuração local](../functions-debug-event-grid-trigger-local.md).

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Aprenda gestão de instâncias em Funções Duráveis](durable-functions-instance-management.md)

> [!div class="nextstepaction"]
> [Aprenda a versonionar em Funções Duráveis](durable-functions-versioning.md)
