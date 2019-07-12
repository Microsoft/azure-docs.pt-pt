---
title: Origens de eventos do Azure Event Grid
description: Descreve as origens de eventos suportados do Azure Event Grid
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 02/12/2019
ms.author: spelluru
ms.openlocfilehash: ad9bb5c135684c4573195298fb8e55a08208f6b6
ms.sourcegitcommit: 1572b615c8f863be4986c23ea2ff7642b02bc605
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/10/2019
ms.locfileid: "67785826"
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

Subscreva eventos do IoT Hub para responder a dispositivo criado, eliminada, conectados, desligado e eventos de telemetria.

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
| [Descrição geral: O Azure Service Bus, a integração do Event Grid](../service-bus-messaging/service-bus-to-event-grid-integration-concept.md) | Descrição geral da integração do Service Bus com o Event Grid. |
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
| [Modelo do Resource Manager: Criar armazenamento de BLOBs e subscrição](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-subscription-and-storage) | Implementa uma conta de armazenamento de Blobs do Azure e subscreve eventos para essa conta de armazenamento. Envia eventos para um WebHook. |
| [Descrição geral: reagir a eventos de armazenamento de BLOBs](../storage/blobs/storage-blob-event-overview.md) | Descrição geral da integração do armazenamento de Blobs com o Event Grid. |
| [Esquema de eventos](event-schema-blob-storage.md) | Mostra os campos nos eventos de armazenamento de Blobs. |

## <a name="maps"></a>Maps
Subscreva eventos de mapas do Azure para responder a eventos do perímetro geográfico. Por exemplo, um aplicativo pode fornecer uma notificação por e-mail sempre que um dispositivo entra ou sai de um perímetro geográfico.

|Cargo  |Descrição  |
|---------|---------|
| [Reagir a eventos do Azure Maps com o Event Grid](../azure-maps/azure-maps-event-grid-integration.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Descrição geral da integração do Azure Maps com o Event Grid. |
| [Tutorial: Configurar um perímetro geográfico](../azure-maps/tutorial-geofence.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Este tutorial explica-lhe os passos de noções básicas para configurar o perímetro geográfico com o Azure Maps. Utilize o Azure Event Grid para transmitir os resultados do perímetro geográfico e configurar uma notificação com base nos resultados do perímetro geográfico. |
| [Esquema de eventos](event-schema-azure-maps.md) | Mostra os campos nos eventos do Azure Maps. |

## <a name="app-configuration"></a>App Configuration
Subscreva eventos de configuração de aplicações do Azure para responder a eventos de modificação de chave-valor.

|Cargo | Descrição |
|---------|---------|
| [Reagir a eventos de configuração de aplicações do Azure com o Event Grid](../azure-app-configuration/concept-app-configuration-event.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Descrição geral da integração de configuração de aplicações do Azure com o Event Grid. |
| [Início rápido: encaminhar eventos de configuração de aplicações do Azure para um ponto final web personalizado com a CLI do Azure](../azure-app-configuration/howto-app-configuration-event.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Mostra como utilizar a CLI do Azure para enviar eventos de configuração de aplicações do Azure para um WebHook. |
| [Esquema de eventos](event-schema-app-configuration.md) | Mostra os campos nos eventos de configuração de aplicações do Azure. |

## <a name="azure-signalr"></a>Azure SignalR
Subscreva eventos de serviço Azure SignalR para responder a eventos de ligação de cliente.

|Cargo | Descrição |
|---------|---------|
| [Reagir a eventos do serviço Azure SignalR ao utilizar o Event Grid](../azure-signalr/signalr-concept-event-grid-integration.md) | Descrição geral da integração do serviço Azure SignalR com o Event Grid. |
| [Como enviar eventos de serviço Azure SignalR para o Event Grid](../azure-signalr/signalr-howto-event-grid-integration.md) | Mostra como enviar eventos do serviço Azure SignalR para uma aplicação através do Event Grid. |
| [Esquema de eventos](event-schema-azure-signalr.md) | Mostra os campos nos eventos do serviço Azure SignalR. |



## <a name="next-steps"></a>Passos seguintes

* Para obter uma introdução ao Event Grid, veja [Sobre o Azure Event Grid](overview.md).
* Para começar rapidamente com o Event Grid, veja [criar e encaminhar eventos personalizados com o Azure Event Grid](custom-event-quickstart.md).
