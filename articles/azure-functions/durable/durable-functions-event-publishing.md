---
title: Funções Duradouras publicadas na Grelha de Eventos Azure (pré-visualização)
description: Saiba como configurar a publicação automática da Azure Event Grid para funções duradouras.
ms.topic: conceptual
ms.date: 04/25/2020
ms.openlocfilehash: 44df100a5c794abf918a09dea0f94d30ddf916d3
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2021
ms.locfileid: "102175962"
---
# <a name="durable-functions-publishing-to-azure-event-grid-preview"></a>Funções Duradouras publicadas na Grelha de Eventos Azure (pré-visualização)

Este artigo mostra como configurar funções duráveis para publicar eventos de ciclo de vida de orquestração (como criados, concluídos e falhados) a um tópico de [grelha de eventos Azure.](../../event-grid/overview.md)

Seguem-se alguns cenários em que esta funcionalidade é útil:

* **Cenários de devOps como implementações azuis/verdes**: É melhor saber se alguma tarefa está a ser executada antes de implementar a [estratégia de implementação lado](durable-functions-versioning.md#side-by-side-deployments)a lado .

* **Suporte avançado de monitorização e diagnóstico**: Pode acompanhar as informações sobre o estado da orquestração numa loja externa otimizada para consultas, como a Base de Dados Azure SQL ou a Azure Cosmos DB.

* **Atividade de fundo de longa duração**: Se utilizar Funções Duráveis para uma atividade de fundo de longa duração, esta funcionalidade ajuda-o a conhecer o estado atual.

## <a name="prerequisites"></a>Pré-requisitos

* Instale [Microsoft.Azure.WebJobs.Extensions.DurableTask](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DurableTask) no seu projeto Funções Duradouras.
* Instale [o Emulador de Armazenamento Azure](../../storage/common/storage-use-emulator.md) (apenas para Windows) ou utilize uma conta de Armazenamento Azure existente.
* Instale [o Azure CLI](/cli/azure/) ou use [a Azure Cloud Shell](../../cloud-shell/overview.md)

## <a name="create-a-custom-event-grid-topic"></a>Crie um tópico personalizado da Grelha de Eventos

Crie um tópico de Grelha de Eventos para o envio de eventos a partir de Funções Duradouras. As seguintes instruções mostram como criar um tópico utilizando o Azure CLI. Também pode criar o tópico [utilizando o PowerShell](../../event-grid/custom-event-quickstart-powershell.md) ou [utilizando o portal Azure](../../event-grid/custom-event-quickstart-portal.md).

### <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um grupo de recursos com o comando `az group create`. Atualmente, a Azure Event Grid não suporta todas as regiões. Para obter informações sobre quais as regiões apoiadas, consulte a [visão geral da Grelha de Eventos Azure](../../event-grid/overview.md).

```azurecli
az group create --name eventResourceGroup --location westus2
```

### <a name="create-a-custom-topic"></a>Criar um tópico personalizado

Um tópico de Grade de Eventos fornece um ponto final definido pelo utilizador para o quais publica o seu evento. Substitua `<topic_name>` por um nome exclusivo para o seu tópico. O nome tópico deve ser único porque se torna uma entrada de DNS.

```azurecli
az eventgrid topic create --name <topic_name> -l westus2 -g eventResourceGroup
```

## <a name="get-the-endpoint-and-key"></a>Obtenha o ponto final e a chave

Pegue o ponto final do tópico. `<topic_name>`Substitua-o pelo nome que escolheu.

```azurecli
az eventgrid topic show --name <topic_name> -g eventResourceGroup --query "endpoint" --output tsv
```

Pegue a chave do tópico. `<topic_name>`Substitua-o pelo nome que escolheu.

```azurecli
az eventgrid topic key list --name <topic_name> -g eventResourceGroup --query "key1" --output tsv
```

Agora pode enviar eventos para o tema.

## <a name="configure-event-grid-publishing"></a>Configure a publicação da Grelha de Eventos

No seu projeto Funções Duradouras, encontre o `host.json` ficheiro.

### <a name="durable-functions-1x"></a>Funções duradouras 1.x

Adicione `eventGridTopicEndpoint` e em uma `eventGridKeySettingName` `durableTask` propriedade.

```json
{
  "durableTask": {
    "eventGridTopicEndpoint": "https://<topic_name>.westus2-1.eventgrid.azure.net/api/events",
    "eventGridKeySettingName": "EventGridKey"
  }
}
```

### <a name="durable-functions-2x"></a>Funções duradouras 2.x

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

As possíveis propriedades de configuração da Grelha de Eventos Azure podem ser encontradas no [host.jsna documentação.](../functions-host-json.md#durabletask) Depois de configurar o ficheiro, a `host.json` sua aplicação de função envia eventos de ciclo de vida para o tópico 'Grade de Eventos'. Esta ação começa quando executar a sua aplicação de função tanto localmente como em Azure.

Defina a definição de aplicativo para a tecla de tópico na App de Função e `local.settings.json` . O seguinte JSON é uma amostra do `local.settings.json` para depurar local. `<topic_key>`Substitua-a pela tecla de tópico.  

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

Se estiver a utilizar o [Emulador de Armazenamento](../../storage/common/storage-use-emulator.md) (apenas para Windows), certifique-se de que está a funcionar. É uma boa ideia comandar o `AzureStorageEmulator.exe clear all` comando antes de executar.

Se estiver a utilizar uma conta de Armazenamento Azure existente, `UseDevelopmentStorage=true` substitua-a `local.settings.json` pela sua cadeia de ligação.

## <a name="create-functions-that-listen-for-events"></a>Criar funções que ouvem eventos

Utilizando o portal Azure, crie outra aplicação de função para ouvir os eventos publicados pela sua aplicação Funções Duráveis. É melhor localizá-lo na mesma região que o tema Da Grelha de Eventos.

### <a name="create-an-event-grid-trigger-function"></a>Criar uma função de gatilho de grade de evento

1. Na sua aplicação de funções, selecione **Funções** e, em seguida, selecione **+ Adicionar** 

   :::image type="content" source="./media/durable-functions-event-publishing/function-add-function.png" alt-text="Adicione uma função no portal Azure." border="true":::

1. Procure por **Grade de Eventos** e, em seguida, selecione o modelo **de gatilho da grelha de eventos Azure.** 

    :::image type="content" source="./media/durable-functions-event-publishing/function-select-event-grid-trigger.png" alt-text="Selecione o modelo de gatilho da grelha de evento no portal Azure." border="true":::

1. Nomeie o novo gatilho e, em seguida, **selecione Criar Função**.

    :::image type="content" source="./media/durable-functions-event-publishing/function-name-event-grid-trigger.png" alt-text="Nomeie o gatilho da grelha de eventos no portal Azure." border="true":::


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

### <a name="add-an-event-grid-subscription"></a>Adicionar uma subscrição de Grade de Eventos

Agora pode adicionar uma subscrição de Grade de Eventos para o tópico 'Grade de Eventos' que criou. Para mais informações, consulte [Conceitos na Grelha de Eventos Azure.](../../event-grid/concepts.md)

1. Na sua nova função, selecione **Integração** e, em seguida, selecione **Event Grid Trigger (eventGridEvent)**. 

    :::image type="content" source="./media/durable-functions-event-publishing/eventgrid-trigger-link.png" alt-text="Selecione a ligação de gatilho da grelha de evento." border="true":::

1. Selecione **Criar Descrição da grelha de eventos**.

    :::image type="content" source="./media/durable-functions-event-publishing/create-event-grid-subscription.png" alt-text="Crie a subscrição da Grade de Eventos." border="true":::

1. Nomeie a subscrição do seu evento e selecione o tipo de tópico **de tópicos de grelha de evento.** 

1. Selecione uma subscrição. Em seguida, selecione o grupo de recursos e o recurso que criou para o tópico 'Grade de Eventos'. 

1. Selecione **Criar**.

    :::image type="content" source="./media/durable-functions-event-publishing/event-grid-subscription-details.png" alt-text="Crie uma subscrição de Grade de Eventos." border="true":::

Agora estás pronto para receber eventos de ciclo de vida.

## <a name="run-durable-functions-app-to-send-the-events"></a>Executar app Funções Duradouras para enviar os eventos

No projeto Funções Duradouras que configuraste anteriormente, começa a depurar a máquina local e inicia uma orquestração. A aplicação publica eventos de ciclo de vida Durable Functions para a Grade de Eventos. Verifique se a Grade de Eventos ativa a função de ouvinte criada verificando os seus registos no portal Azure.

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

## <a name="event-schema"></a>Schema do evento

A seguinte lista explica o esquema de eventos do ciclo de vida:

* **`id`**: Identificador exclusivo para o evento Event Grid.
* **`subject`**: Caminho para o tema do evento. `durable/orchestrator/{orchestrationRuntimeStatus}`. `{orchestrationRuntimeStatus}` será, `Running` `Completed` , e `Failed` `Terminated` .  
* **`data`**: Funções duradouras Parâmetros específicos.
  * **`hubName`**: [Nome TaskHub.](durable-functions-task-hubs.md)
  * **`functionName`**: Nome da função do orquestrador.
  * **`instanceId`**: Funções Duradouras instanceId.
  * **`reason`**: Dados adicionais associados ao evento de rastreio. Para obter mais informações, consulte [Diagnósticos em Funções Duradouras (Funções Azure)](durable-functions-diagnostics.md)
  * **`runtimeStatus`**: Estado do tempo de execução da orquestração. Correr, completar, falhar, cancelar.
* **`eventType`**: "OrquestradorEvent"
* **`eventTime`**: Hora do evento (UTC).
* **`dataVersion`**: Versão do esquema do evento do ciclo de vida.
* **`metadataVersion`**: Versão dos metadados.
* **`topic`**: Recurso tópico de grelha de evento.

## <a name="how-to-test-locally"></a>Como testar localmente

Para testar localmente, leia [Azure Function Event Event Grid Trigger Local Debugging](../functions-debug-event-grid-trigger-local.md)local .

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Aprenda a gestão de instâncias em Funções Duradouras](durable-functions-instance-management.md)

> [!div class="nextstepaction"]
> [Aprenda a ver versão em Funções Duradouras](durable-functions-versioning.md)
