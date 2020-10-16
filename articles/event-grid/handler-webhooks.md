---
title: Webhooks como manipuladores de eventos para eventos Azure Event Grid
description: Descreve como pode usar webhooks como manipuladores de eventos para eventos Azure Event Grid. Os runbooks e aplicações lógicas da Azure Automation são suportados como manipuladores de eventos através de webhooks.
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: aa20c75789a18b93f787134dffe165e60ff1ab6f
ms.sourcegitcommit: fbb620e0c47f49a8cf0a568ba704edefd0e30f81
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91875806"
---
# <a name="webhooks-automation-runbooks-logic-apps-as-event-handlers-for-azure-event-grid-events"></a>Webhooks, livros de automação, Aplicações Lógicas como manipuladores de eventos para eventos Azure Event Grid
Um manipulador de eventos é o local onde o evento é enviado. O manipulador toma mais algumas medidas para processar o evento. Vários serviços Azure são configurados automaticamente para lidar com eventos. Também pode utilizar qualquer WebHook para lidar com eventos. O WebHook não precisa de ser hospedado no Azure para lidar com eventos. O Event Grid suporta apenas pontos finais HTTPS Webhook.

> [!NOTE]
> - Os runbooks e aplicações lógicas da Azure Automation são suportados como manipuladores de eventos através de webhooks. 
> - Mesmo que possa utilizar **o Webhook** como **tipo ponto final** para configurar uma função Azure como manipulador de eventos, utilize a **Função Azure** como tipo ponto final. Para mais informações, consulte [a função Azure como manipulador de eventos](handler-functions.md).

## <a name="webhooks"></a>Webhooks
Consulte os seguintes artigos para obter uma visão geral e exemplos de utilização de webhooks como manipuladores de eventos. 

|Título  |Descrição  |
|---------|---------|
| Quickstart: criar e encaminhar eventos personalizados com - [Azure CLI,](custom-event-quickstart.md) [PowerShell,](custom-event-quickstart-powershell.md)e [portal](custom-event-quickstart-portal.md). | Mostra como enviar eventos personalizados para um WebHook. |
| Quickstart: encaminhe os eventos de armazenamento blob para um ponto final personalizado com - [Azure CLI,](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json) [PowerShell](../storage/blobs/storage-blob-event-quickstart-powershell.md?toc=%2fazure%2fevent-grid%2ftoc.json), e [portal](blob-event-quickstart-portal.md). | Mostra como enviar eventos de armazenamento de bolhas para um WebHook. |
| [Quickstart: enviar eventos de registo de contentores](../container-registry/container-registry-event-grid-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Mostra como usar o Azure CLI para enviar eventos de registo de contentores. |
| [Visão geral: receber eventos para um ponto final HTTP](receive-events.md) | Descreve como validar um ponto final HTTP para receber eventos de uma Subscrição de Eventos e receber e desseializar eventos. |


## <a name="azure-automation"></a>Automatização do Azure
Pode processar eventos utilizando os runbooks da Azure Automation. O processamento de eventos utilizando runbooks automatizados é suportado através de webhooks. Cria-se um webhook para o livro de formulários e, em seguida, utiliza-se o manipulador webhook. Veja o seguinte tutorial para um exemplo: 

|Título  |Descrição  |
|---------|---------|
|[Tutorial: Azure Automation com Grade de Eventos e Equipas microsoft](ensure-tags-exists-on-new-virtual-machines.md) |Crie uma máquina virtual, que envia um evento. O evento aciona um runbook Automation que marca a máquina virtual e despoleta uma mensagem que é enviada para um canal microsoft Teams. |


## <a name="logic-apps"></a>Aplicações Lógicas
Utilize **aplicações lógicas** para implementar processos de negócio para processar eventos da Grade de Eventos. Não se cria um webhook explicitamente neste cenário. O webhook é criado automaticamente para si quando configura a aplicação lógica para lidar com eventos a partir de Event Grid. Veja os seguintes tutoriais por exemplo: 

|Título  |Descrição  |
|---------|---------|
| [Tutorial: Monitorize alterações na máquina virtual com a Azure Event Grid e as Aplicações Lógicas](monitor-virtual-machine-changes-event-grid-logic-app.md) | Uma aplicação lógica monitoriza as alterações numa máquina virtual e envia e-mails sobre essas alterações. |
| [Tutorial: Enviar notificações de email sobre eventos do Azure IoT Hub usando Apps Lógicas](publish-iot-hub-events-to-logic-apps.md) | Uma aplicação lógica envia um e-mail de notificação sempre que um dispositivo é adicionado ao seu hub IoT. |
| [Tutorial: Responder aos eventos de autocarro do Azure Service recebidos através da Azure Event Grid utilizando funções Azure e Apps Azure Logic](../service-bus-messaging/service-bus-to-event-grid-integration-example.md?toc=%2fazure%2fevent-grid%2ftoc.json) | A Grade de Eventos envia mensagens do tópico Service Bus para a aplicação de funções e aplicação lógica. |

## <a name="rest-example-for-put"></a>Exemplo DE REPOUSO (para PUT)

```json
{
    "properties": 
    {
        "destination": 
        {
            "endpointType": "WebHook",
            "properties": 
            {
                "endpointUrl": "<WEB HOOK URL>",
                "maxEventsPerBatch": 1,
                "preferredBatchSizeInKilobytes": 64
            }
        },
        "eventDeliverySchema": "EventGridSchema"
    }
}
```

## <a name="next-steps"></a>Passos seguintes
Consulte o artigo [dos manipuladores do Evento](event-handlers.md) para obter uma lista de manipuladores de eventos suportados. 
