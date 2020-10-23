---
title: A azure funciona como manipulador de eventos para eventos Azure Event Grid
description: Descreve como pode usar funções Azure como manipuladores de eventos para eventos de Grade de Eventos.
ms.topic: conceptual
ms.date: 09/18/2020
ms.openlocfilehash: 9e04fd3e04dab7a50940c2a4a799a56d447fbb6e
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/17/2020
ms.locfileid: "92145764"
---
# <a name="azure-function-as-an-event-handler-for-event-grid-events"></a>A azure funciona como manipulador de eventos para eventos de Grade de Eventos

Um manipulador de eventos é o local onde o evento é enviado. O manipulador toma uma ação para processar o evento. Vários serviços Azure são configurados automaticamente para lidar com eventos e **as Funções Azure** é um deles. 


Para utilizar uma função Azure como manipulador para eventos, siga uma destas abordagens: 

-   Use o [gatilho da grelha de eventos](../azure-functions/functions-bindings-event-grid-trigger.md).  Especificar **a função Azure** como o **tipo ponto final**. Em seguida, especifique a aplicação de função Azure e a função que irá lidar com eventos. 
-   Utilize [o gatilho HTTP](../azure-functions/functions-bindings-http-webhook.md).  Especifique o **Web Hook** como o **tipo de ponto final.** Em seguida, especifique o URL para a função Azure que irá lidar com eventos. 

Recomendamos que utilize a primeira abordagem (detonador de Grade de Eventos), uma vez que tem as seguintes vantagens sobre a segunda abordagem:
-   A Grelha de Eventos valida automaticamente os gatilhos da Grelha de Eventos. Com os gatilhos GENÉRICOS HTTP, tem de implementar a [resposta de validação](webhook-event-delivery.md) por si mesmo.
-   A Grelha de Eventos ajusta automaticamente a taxa a que os eventos são entregues a uma função desencadeada por um evento de Grade de Eventos com base na taxa percebida a que a função pode processar eventos. Esta funcionalidade de correspondência de taxa evita erros de entrega que decorrem da incapacidade de uma função para processar eventos, uma vez que a taxa de processamento de eventos da função pode variar ao longo do tempo. Para melhorar a eficiência a uma produção elevada, ative o lote na subscrição do evento. Para obter mais informações, consulte [Ativar o lote .](#enable-batching)

    > [!NOTE]
    > Atualmente, não é possível utilizar um gatilho de Grade de Eventos para uma aplicação Azure Functions quando o evento é entregue no esquema do **CloudEvents.** Em vez disso, utilize um gatilho HTTP.

## <a name="tutorials"></a>Tutoriais

|Título  |Descrição  |
|---------|---------|
| [Quickstart: Lidar com eventos com função](custom-event-to-function.md) | Envia um evento personalizado para uma função de processamento. |
| [Tutorial: automatizar redimensionamento de imagens carregadas usando a Grade de Eventos](resize-images-on-storage-blob-upload-event.md) | Os utilizadores fazem o upload das imagens através da web app para a conta de armazenamento. Quando uma bolha de armazenamento é criada, o Event Grid envia um evento para a aplicação de função, que redimensiona a imagem carregada. |
| [Tutorial: transmitir big data para um armazém de dados](event-grid-event-hubs-integration.md) | Quando o Event Hubs cria um ficheiro Capture, o Event Grid envia um evento para uma aplicação de função. A aplicação recupera o ficheiro Captura e migra dados para um armazém de dados. |
| [Tutorial: Azure Service Bus para Azure Event Grid exemplos de integração](../service-bus-messaging/service-bus-to-event-grid-integration-example.md?toc=%2fazure%2fevent-grid%2ftoc.json) | A Grade de Eventos envia mensagens do tópico Service Bus para uma aplicação de função e uma aplicação lógica. |

## <a name="rest-example-for-put"></a>Exemplo DE REPOUSO (para PUT)

```json
{
    "properties": 
    {
        "destination": 
        {
            "endpointType": "AzureFunction",
            "properties": 
            {
                "resourceId": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.Web/sites/<FUNCTION APP NAME>/functions/<FUNCTION NAME>",
                "maxEventsPerBatch": 10,
                "preferredBatchSizeInKilobytes": 6400
            }
        },
        "eventDeliverySchema": "EventGridSchema"
    }
}
```

## <a name="enable-batching"></a>Ativar o loteamento
Para uma produção mais elevada, ative o loteamento na subscrição. Se estiver a utilizar o portal Azure, pode definir o máximo de eventos por lote e o tamanho do lote preferido em bytes de quilo no momento de criar uma subscrição ou após a criação. 

Pode configurar as definições do lote utilizando o modelo do portal Azure, PowerShell, CLI ou Gestor de Recursos. 

### <a name="azure-portal"></a>Portal do Azure
No momento em que criar uma subscrição na UI, na página De Subscrição de **Eventos Criar,** mudar para o separador **Funcionalidades Avançadas** e definir valores para **eventos Max por lote** e tamanho do lote preferido em **quilobytes**. 
    
:::image type="content" source="./media/custom-event-to-function/enable-batching.png" alt-text="Ativar o lote no momento de criar uma subscrição":::

Pode atualizar estes valores para uma subscrição existente no separador **Funcionalidades** da página **Tópico de Grelha de Eventos.** 

:::image type="content" source="./media/custom-event-to-function/features-batch-settings.png" alt-text="Ativar o lote no momento de criar uma subscrição":::

### <a name="azure-resource-manager-template"></a>Modelo Azure Resource Manager
Pode definir **maxEventsPerBatch** e **preferirBatchSizeInKilobytes** num modelo de Gestor de Recursos Azure. Para obter mais informações, consulte [a referência do modelo de eventos Microsoft.EventGrid](/azure/templates/microsoft.eventgrid/eventsubscriptions).

### <a name="azure-cli"></a>CLI do Azure
Pode utilizar o comando [de subscrição de eventos az ou](/cli/azure/eventgrid/event-subscription?view=azure-cli-latest#az_eventgrid_event_subscription_create&preserve-view=true) [az eventgrid de subscrição de eventos para](/cli/azure/eventgrid/event-subscription?view=azure-cli-latest#az_eventgrid_event_subscription_update&preserve-view=true) configurar definições relacionadas com o lote utilizando os seguintes parâmetros: ou `--max-events-per-batch` `--preferred-batch-size-in-kilobytes` .

### <a name="azure-powershell"></a>Azure PowerShell
Pode utilizar o [cmdion de subscrição new-AzEventGridS ou](/powershell/module/az.eventgrid/new-azeventgridsubscription) [Update-AzEventGridSubscription](/powershell/module/az.eventgrid/update-azeventgridsubscription) para configurar as definições relacionadas com o lote utilizando os seguintes parâmetros: `-MaxEventsPerBatch` ou `-PreferredBatchSizeInKiloBytes` .

## <a name="next-steps"></a>Passos seguintes
Consulte o artigo [dos manipuladores do Evento](event-handlers.md) para obter uma lista de manipuladores de eventos suportados.