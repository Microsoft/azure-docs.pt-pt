---
title: A azure funciona como manipulador de eventos para eventos Azure Event Grid
description: Descreve como pode usar funções Azure como manipuladores de eventos para eventos de Grade de Eventos.
ms.topic: conceptual
ms.date: 09/18/2020
ms.openlocfilehash: db06962c020eb954bf0c595e5a4019b1df774898
ms.sourcegitcommit: d479ad7ae4b6c2c416049cb0e0221ce15470acf6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/01/2020
ms.locfileid: "91629693"
---
# <a name="azure-function-as-an-event-handler-for-event-grid-events"></a>A azure funciona como manipulador de eventos para eventos de Grade de Eventos

Um manipulador de eventos é o local onde o evento é enviado. O manipulador toma uma ação para processar o evento. Vários serviços Azure são configurados automaticamente para lidar com eventos e **as Funções Azure** é um deles. 

Use **funções Azure** numa arquitetura sem servidor para responder a eventos da Grade de Eventos. Quando utilizar uma função Azure como manipulador, utilize o gatilho 'Grelha de Evento' em vez do gatilho GENÉRICO HTTP. A Grelha de Eventos valida automaticamente os gatilhos da Grelha de Eventos. Com os gatilhos GENÉRICOS HTTP, tem de implementar a [resposta de validação](webhook-event-delivery.md) por si mesmo.

Para obter mais informações, consulte [o gatilho da Grelha de Eventos para as Funções Azure](../azure-functions/functions-bindings-event-grid.md) para uma visão geral da utilização do gatilho da Grelha de Eventos em funções.

## <a name="tutorials"></a>Tutorials (Tutoriais)

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
Pode definir **maxEventsPerBatch** e **preferirBatchSizeInKilobytes** num modelo de Gestor de Recursos Azure. Para obter mais informações, consulte [a referência do modelo de eventos Microsoft.EventGrid](https://docs.microsoft.com/azure/templates/microsoft.eventgrid/eventsubscriptions).

### <a name="azure-cli"></a>CLI do Azure
Pode utilizar o comando [de subscrição de eventos az ou](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription?view=azure-cli-latest#az_eventgrid_event_subscription_create&preserve-view=true) [az eventgrid de subscrição de eventos para](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription?view=azure-cli-latest#az_eventgrid_event_subscription_update&preserve-view=true) configurar definições relacionadas com o lote utilizando os seguintes parâmetros: ou `--max-events-per-batch` `--preferred-batch-size-in-kilobytes` .

### <a name="azure-powershell"></a>Azure PowerShell
Pode utilizar o [cmdion de subscrição new-AzEventGridS ou](https://docs.microsoft.com/powershell/module/az.eventgrid/new-azeventgridsubscription) [Update-AzEventGridSubscription](https://docs.microsoft.com/powershell/module/az.eventgrid/update-azeventgridsubscription) para configurar as definições relacionadas com o lote utilizando os seguintes parâmetros: `-MaxEventsPerBatch` ou `-PreferredBatchSizeInKiloBytes` .

## <a name="next-steps"></a>Passos seguintes
Consulte o artigo [dos manipuladores do Evento](event-handlers.md) para obter uma lista de manipuladores de eventos suportados. 
