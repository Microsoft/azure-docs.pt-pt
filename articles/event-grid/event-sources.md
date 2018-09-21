---
title: Origens de eventos do Azure Event Grid
description: Descreve as origens de eventos suportados do Azure Event Grid
services: event-grid
author: tfitzmac
ms.service: event-grid
ms.topic: conceptual
ms.date: 09/19/2018
ms.author: tomfitz
ms.openlocfilehash: adef9d2f2d859c62d3b3b3a542536698fa668f9a
ms.sourcegitcommit: 8b694bf803806b2f237494cd3b69f13751de9926
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/20/2018
ms.locfileid: "46498305"
---
# <a name="event-sources-in-azure-event-grid"></a>Origens de eventos no Azure Event Grid

Uma origem de evento é onde acontece o evento. Vários serviços do Azure são automaticamente configurados para enviar eventos. Também pode criar aplicações personalizadas que enviam eventos. Aplicativos personalizados não precisam de ser alojado no Azure para utilizar o Event Grid para distribuição de eventos.

Este artigo fornece ligações para conteúdo para cada origem de evento.

## <a name="azure-subscriptions"></a>Subscrições do Azure

Subscreva eventos de subscrições do Azure para responder a alterações em recursos numa subscrição do Azure.

|Cargo |Descrição  |
|---------|---------|
| [Tutorial: Automatização do Azure com o Event Grid e o Microsoft Teams](ensure-tags-exists-on-new-virtual-machines.md) |Crie uma máquina virtual, que envia um evento. O evento é acionado um runbook da automatização que a máquina virtual de etiquetas e aciona uma mensagem que é enviada para um canal do Microsoft Teams. |
| [Como: assinar eventos através do portal](subscribe-through-portal.md) | Utilize o portal para subscrever eventos para uma subscrição do Azure. |
| [CLI do Azure: subscrever eventos para uma subscrição do Azure](./scripts/event-grid-cli-azure-subscription.md) |Script de exemplo que cria uma subscrição do Event Grid para uma subscrição do Azure e envia eventos para um WebHook. |
| [PowerShell: subscrever eventos para uma subscrição do Azure](./scripts/event-grid-powershell-azure-subscription.md)| Script de exemplo que cria uma subscrição do Event Grid para uma subscrição do Azure e envia eventos para um WebHook. |
| [Esquema de eventos](event-schema-subscriptions.md) | Mostra os campos nos eventos de subscrição do Azure. |

## <a name="container-registry"></a>Registo de Contentor

Subscreva eventos de registo de contentor para responder a alterações em imagens.

|Cargo |Descrição  |
|---------|---------|
| [Início rápido: enviar eventos de registo de contentor](../container-registry/container-registry-event-grid-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Mostra como utilizar a CLI do Azure para enviar eventos de registo de contentor. |
| [Esquema de eventos](event-schema-container-registry.md) | Mostra os campos nos eventos de registo de contentor. |

## <a name="custom-topics"></a>Tópicos personalizados

Subscreva tópicos personalizados para responder a eventos do aplicativo.

|Cargo  |Descrição  |
|---------|---------|
| [Início rápido: criar e encaminhar eventos personalizados com a CLI do Azure](custom-event-quickstart.md) | Mostra como utilizar a CLI do Azure para enviar eventos personalizados. |
| [Início rápido: criar e encaminhar eventos personalizados com o Azure PowerShell](custom-event-quickstart-powershell.md) | Mostra como utilizar o Azure PowerShell para enviar eventos personalizados. |
| [Início rápido: criar e encaminhar eventos personalizados com o portal do Azure](custom-event-quickstart-portal.md) | Mostra como utilizar o portal para enviar eventos personalizados. |
| [Início rápido: encaminhar eventos personalizados para o armazenamento de filas do Azure](custom-event-to-queue-storage.md) | Descreve como enviar eventos personalizados para um armazenamento de filas. |
| [Como: publicar em tópico personalizado](post-to-custom-topic.md) | Mostra como publicar um evento para um tópico personalizado. |
| [CLI do Azure: criar o tópico personalizado do Event Grid](./scripts/event-grid-cli-create-custom-topic.md)|Script de exemplo que cria um tópico personalizado. O script obtém o ponto de extremidade e uma chave.|
| [CLI do Azure: subscrever eventos para um tópico personalizado](./scripts/event-grid-cli-subscribe-custom-topic.md)|Script de exemplo que cria uma subscrição para um tópico personalizado. Envia eventos para um WebHook.|
| [PowerShell: criar o tópico personalizado do Event Grid](./scripts/event-grid-powershell-create-custom-topic.md)|Script de exemplo que cria um tópico personalizado. O script obtém o ponto de extremidade e uma chave.|
| [PowerShell: subscrever eventos para um tópico personalizado](./scripts/event-grid-powershell-subscribe-custom-topic.md)|Script de exemplo que cria uma subscrição para um tópico personalizado. Envia eventos para um WebHook.|
| [Modelo do Resource Manager: um tópico personalizado e o ponto final do WebHook](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid) | Um modelo do Resource Manager que cria um tópico personalizado e uma subscrição para esse tópico personalizado. Envia eventos para um WebHook. |
|
| [Modelo do Resource Manager: um tópico personalizado e um ponto final de Hubs de eventos](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-event-hubs-handler)| Um modelo do Resource Manager que cria uma subscrição para um tópico personalizado. Envia eventos para um Event Hubs do Azure. |
| [Esquema de eventos](event-schema.md) | Mostra campos de eventos personalizados. |

## <a name="event-hubs"></a>Hubs de Eventos

Subscreva eventos de Hubs de eventos para responder a capturar eventos de ficheiro. Os Hubs de eventos podem agir como uma origem de evento ou de manipulador de eventos. Os artigos seguintes mostram como utilizar os Hubs de eventos como uma origem.

|Cargo  |Descrição  |
|---------|---------|
| [Tutorial: transmitir grandes quantidades de dados para um armazém de dados](event-grid-event-hubs-integration.md) | Quando os Hubs de eventos cria um ficheiro de captura, o Event Grid envia um evento para uma aplicação de funções. A aplicação obtém o arquivo de captura e migra os dados para um armazém de dados. |
| [Esquema de eventos](event-schema-event-hubs.md) | Mostra os campos nos eventos de Hubs de eventos. |

Para obter exemplos dos Hubs de eventos como um manipulador de mensagens em fila, consulte [manipulador de Hubs de eventos](event-handlers.md#event-hubs).

## <a name="iot-hub"></a>IoT Hub

Subscreva eventos do IoT Hub para responder a dispositivo criado, eventos de eliminado, conectados ou desconectados.

|Cargo  |Descrição  |
|---------|---------|
| [Enviar notificações por e-mail sobre eventos do IoT Hub do Azure com o Logic Apps](publish-iot-hub-events-to-logic-apps.md) | Uma aplicação lógica envia um e-mail de notificação sempre que um dispositivo é adicionado ao seu IoT Hub. |
| [Reagir a eventos do IoT Hub com o Event Grid para realizar ações](../iot-hub/iot-hub-event-grid.md) | Descrição geral da integração do IoT Hub com o Event Grid. |
| [Esquema de eventos](event-schema-iot-hub.md) | Mostra campos do IoT Hub de eventos. |
| [Ordenar dispositivo ligado e eventos de desligado do dispositivo](../iot-hub/iot-hub-how-to-order-connection-state-events.md) | Mostra como encomendar eventos de estado de ligação do dispositivo. |

## <a name="media-services"></a>Serviços de Multimédia

Subscreva eventos de serviços de multimédia para responder a eventos de estado de tarefa.

|Cargo  |Descrição  |
|---------|---------|
| [Descrição geral: reagir a eventos de serviços de multimédia](../media-services/latest/reacting-to-media-services-events.md) | Descrição geral da integração dos serviços de multimédia com o Event Grid. |
| [Tutorial: encaminhar eventos de serviços de multimédia do Azure para um ponto final web personalizado com a CLI](../media-services/latest/job-state-events-cli-how-to.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Mostra como enviar eventos a partir dos serviços de multimédia. |
| [Esquema de eventos](../media-services/latest/media-services-event-schemas.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Mostra os campos nos eventos de serviços de multimédia. |

## <a name="resource-groups"></a>Grupos de recursos

Subscreva eventos para responder a alterações em recursos num grupo de recursos do grupo de recursos.

|Cargo  |Descrição  |
|---------|---------|
| [Tutorial: monitorizar as alterações de máquina virtual com o Azure Event Grid e Logic Apps](monitor-virtual-machine-changes-event-grid-logic-app.md) | Uma aplicação lógica monitoriza alterações a uma máquina virtual e envia e-mails sobre essas alterações. |
| [CLI do Azure: subscrever eventos para um grupo de recursos](./scripts/event-grid-cli-resource-group.md)| Script de exemplo que subscreve eventos para um grupo de recursos. Envia eventos para um WebHook. |
| [CLI do Azure: subscrever eventos para um grupo de recursos e o filtro para um recurso](./scripts/event-grid-cli-resource-group-filter.md) | Script de exemplo que subscreve eventos para um grupo de recursos e filtra eventos para um recurso. |
| [PowerShell: subscrever eventos para um grupo de recursos](./scripts/event-grid-powershell-resource-group.md) | Script de exemplo que subscreve eventos para um grupo de recursos. Envia eventos para um WebHook. |
| [PowerShell: subscrever eventos para um grupo de recursos e o filtro para um recurso](./scripts/event-grid-powershell-resource-group-filter.md) | Script de exemplo que subscreve eventos para um grupo de recursos e filtra eventos para um recurso. |
| [Modelo do Resource Manager: subscrição de recursos](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-resource-events-to-webhook) | Subscreve eventos para um grupo de subscrição ou ao recurso do Azure. Envia eventos para um WebHook. |
| [Esquema de Eventos](event-schema-resource-groups.md) | Mostra campos no recurso de eventos do grupo. |

## <a name="service-bus"></a>Service Bus

Subscreva eventos do Service Bus para responder a mensagens sem um serviço de escuta ativo.

|Cargo  |Descrição  |
|---------|---------|
| [Tutorial: O Azure Service Bus para exemplos de integração do Azure Event Grid](../service-bus-messaging/service-bus-to-event-grid-integration-example.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Grelha de eventos envia mensagens de tópico do Service Bus para a função de aplicação e a aplicação lógica. |
| [Descrição geral: Azure Service Bus para integração do Event Grid](../service-bus-messaging/service-bus-to-event-grid-integration-concept.md) | Descrição geral da integração do Service Bus com o Event Grid. |
| [Esquema de eventos](event-schema-service-bus.md) | Mostra campos de eventos do Service Bus. |

## <a name="storage"></a>Armazenamento

Subscreva eventos de armazenamento de BLOBs para responder a eventos criados e eliminados de Blobs.

|Cargo  |Descrição  |
|---------|---------|
| [Início rápido: encaminhar eventos de armazenamento de BLOBs para um ponto final web personalizado com a CLI do Azure](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Mostra como utilizar a CLI do Azure para enviar eventos de armazenamento de BLOBs para um WebHook. |
| [Início rápido: encaminhar eventos de armazenamento de BLOBs para um ponto final web personalizado com o PowerShell](../storage/blobs/storage-blob-event-quickstart-powershell.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Mostra como utilizar o Azure PowerShell para enviar eventos de armazenamento de BLOBs para um WebHook. |
| [Início rápido: criar e encaminhar eventos de armazenamento de Blobs com o portal do Azure](blob-event-quickstart-portal.md) | Mostra como utilizar o portal para enviar eventos de armazenamento de BLOBs para um WebHook. |
| [CLI do Azure: subscrever eventos para uma conta de armazenamento de BLOBs](./scripts/event-grid-cli-blob.md) | Script de exemplo que subscreve eventos para uma conta de armazenamento de Blobs. Envia o evento para um WebHook. |
| [PowerShell: subscrever eventos para uma conta de armazenamento de BLOBs](./scripts/event-grid-powershell-blob.md) | Script de exemplo que subscreve eventos para uma conta de armazenamento de Blobs. Envia o evento para um WebHook. |
| [Modelo do Resource Manager: criar o armazenamento de BLOBs e subscrição](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-subscription-and-storage) | Implementa uma conta de armazenamento de Blobs do Azure e subscreve eventos para essa conta de armazenamento. Envia eventos para um WebHook. |
| [Descrição geral: reagir a eventos de armazenamento de BLOBs](../storage/blobs/storage-blob-event-overview.md) | Descrição geral da integração do armazenamento de Blobs com o Event Grid. |
| [Esquema de eventos](event-schema-blob-storage.md) | Mostra os campos nos eventos de armazenamento de Blobs. |

## <a name="next-steps"></a>Passos Seguintes

* Para obter uma introdução ao Event Grid, veja [Sobre o Azure Event Grid](overview.md).
* Para começar rapidamente com o Event Grid, veja [criar e encaminhar eventos personalizados com o Azure Event Grid](custom-event-quickstart.md).
