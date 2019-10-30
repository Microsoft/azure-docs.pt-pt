---
title: Origens de eventos da grade de eventos do Azure
description: Descreve as fontes de eventos com suporte para a grade de eventos do Azure
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 02/12/2019
ms.author: spelluru
ms.openlocfilehash: 5e8dc6629cfbbc3e42aeeb4dc94f33d13867ca9f
ms.sourcegitcommit: d47a30e54c5c9e65255f7ef3f7194a07931c27df
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73024799"
---
# <a name="event-sources-in-azure-event-grid"></a>Fontes de eventos na grade de eventos do Azure

Uma origem do evento é onde o evento acontece. Vários serviços do Azure são configurados automaticamente para enviar eventos. Você também pode criar aplicativos personalizados que enviam eventos. Os aplicativos personalizados não precisam ser hospedados no Azure para usar a grade de eventos para distribuição de eventos.

Este artigo fornece links para o conteúdo de cada origem de evento.

## <a name="azure-subscriptions"></a>Subscrições do Azure

Assine os eventos de assinaturas do Azure para responder às alterações nos recursos em uma assinatura do Azure.

|Título |Descrição  |
|---------|---------|
| [Tutorial: automação do Azure com grade de eventos e Microsoft Teams](ensure-tags-exists-on-new-virtual-machines.md) |Crie uma máquina virtual, que envia um evento. O evento dispara um runbook de automação que marca a máquina virtual e dispara uma mensagem que é enviada para um canal do Microsoft Teams. |
| [Como assinar eventos por meio do portal](subscribe-through-portal.md) | Use o portal para assinar eventos para uma assinatura do Azure. |
| [CLI do Azure: assinar eventos para uma assinatura do Azure](./scripts/event-grid-cli-azure-subscription.md) |Script de exemplo que cria uma assinatura de grade de eventos para uma assinatura do Azure e envia eventos para um webhook. |
| [PowerShell: assinar eventos para uma assinatura do Azure](./scripts/event-grid-powershell-azure-subscription.md)| Script de exemplo que cria uma assinatura de grade de eventos para uma assinatura do Azure e envia eventos para um webhook. |
| [Esquema de evento](event-schema-subscriptions.md) | Mostra os campos nos eventos de assinatura do Azure. |

## <a name="container-registry"></a>Container Registry

Assine os eventos de registro de contêiner para responder às alterações nas imagens.

|Título |Descrição  |
|---------|---------|
| [Início rápido: enviar eventos de registro de contêiner](../container-registry/container-registry-event-grid-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Mostra como usar CLI do Azure para enviar eventos de registro de contêiner. |
| [Esquema de evento](event-schema-container-registry.md) | Mostra os campos em eventos de registro de contêiner. |

## <a name="custom-topics"></a>Tópicos personalizados

Assine tópicos personalizados para responder a eventos do aplicativo.

|Título  |Descrição  |
|---------|---------|
| [Início rápido: criar e rotear eventos personalizados com CLI do Azure](custom-event-quickstart.md) | Mostra como usar CLI do Azure para enviar eventos personalizados. |
| [Início rápido: criar e rotear eventos personalizados com Azure PowerShell](custom-event-quickstart-powershell.md) | Mostra como usar Azure PowerShell para enviar eventos personalizados. |
| [Início rápido: criar e rotear eventos personalizados com o portal do Azure](custom-event-quickstart-portal.md) | Mostra como usar o portal para enviar eventos personalizados. |
| [Início rápido: rotear eventos personalizados para o armazenamento de filas do Azure](custom-event-to-queue-storage.md) | Descreve como enviar eventos personalizados para um armazenamento de fila. |
| [Como: postar para o tópico personalizado](post-to-custom-topic.md) | Mostra como postar um evento em um tópico personalizado. |
| [CLI do Azure: criar tópico personalizado da grade de eventos](./scripts/event-grid-cli-create-custom-topic.md)|Script de exemplo que cria um tópico personalizado. O script recupera o ponto de extremidade e uma chave.|
| [CLI do Azure: assinar eventos para um tópico personalizado](./scripts/event-grid-cli-subscribe-custom-topic.md)|Script de exemplo que cria uma assinatura para um tópico personalizado. Ele envia eventos para um webhook.|
| [PowerShell: criar tópico personalizado da grade de eventos](./scripts/event-grid-powershell-create-custom-topic.md)|Script de exemplo que cria um tópico personalizado. O script recupera o ponto de extremidade e uma chave.|
| [PowerShell: assinar eventos para um tópico personalizado](./scripts/event-grid-powershell-subscribe-custom-topic.md)|Script de exemplo que cria uma assinatura para um tópico personalizado. Ele envia eventos para um webhook.|
| [Modelo do Resource Manager: tópico personalizado e ponto de extremidade do webhook](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid) | Um modelo do Resource Manager que cria um tópico e uma assinatura personalizados para esse tópico personalizado. Ele envia eventos para um webhook. |
|
| [Modelo do Resource Manager: ponto de extremidade do tópico personalizado e dos hubs de eventos](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-event-hubs-handler)| Um modelo do Resource Manager que cria uma assinatura para um tópico personalizado. Ele envia eventos para um hub de eventos do Azure. |
| [Esquema de evento](event-schema.md) | Mostra os campos em eventos personalizados. |

## <a name="event-hubs"></a>Hubs de Eventos

Assine eventos de hubs de eventos para responder a eventos de captura de arquivo. Os hubs de eventos podem atuar como um manipulador de eventos ou origem de evento. Os artigos a seguir mostram como usar os hubs de eventos como uma origem.

|Título  |Descrição  |
|---------|---------|
| [Tutorial: transmitir Big Data para um data warehouse](event-grid-event-hubs-integration.md) | Quando os hubs de eventos criam um arquivo de captura, a grade de eventos envia um evento para um aplicativo de funções. O aplicativo recupera o arquivo de captura e migra os dados para um data warehouse. |
| [Esquema de evento](event-schema-event-hubs.md) | Mostra os campos em eventos de hubs de eventos. |

Para obter exemplos de hubs de eventos como um manipulador, consulte [manipulador de hubs de eventos](event-handlers.md#event-hubs).

## <a name="iot-hub"></a>Hub IoT

Assine eventos do Hub IoT para responder a eventos criados, excluídos, conectados, desconectados e de telemetria do dispositivo.

|Título  |Descrição  |
|---------|---------|
| [Enviar notificações por email sobre eventos do Hub IoT do Azure usando aplicativos lógicos](publish-iot-hub-events-to-logic-apps.md) | Um aplicativo lógico envia um email de notificação toda vez que um dispositivo é adicionado ao Hub IoT. |
| [Reagir aos eventos do Hub IoT usando a grade de eventos para disparar ações](../iot-hub/iot-hub-event-grid.md) | Visão geral da integração do Hub IoT com a grade de eventos. |
| [Esquema de evento](event-schema-iot-hub.md) | Mostra os campos nos eventos do Hub IoT. |
| [Solicitar o dispositivo conectado e eventos desconectados do dispositivo](../iot-hub/iot-hub-how-to-order-connection-state-events.md) | Mostra como ordenar eventos de estado de conexão do dispositivo. |

## <a name="key-vault-preview"></a>Key Vault (versão prévia)

A integração do Key Vault com a grade de eventos está atualmente em visualização. 

Assine Key Vault eventos a serem notificados quando um segredo está prestes a expirar, um segredo expira ou um segredo tem uma nova versão disponível. 

|Título  |Descrição  |
|---------|---------|
| [Monitorando Key Vault eventos com a grade de eventos do Azure](../key-vault/event-grid-overview.md) | Visão geral da integração de Key Vault com a grade de eventos. |
| [Tutorial: criar e monitorar eventos de Key Vault com a grade de eventos](../key-vault/event-grid-tutorial.md) | Saiba como configurar notificações de grade de eventos para Key Vault. |
| [Esquema de evento](event-schema-key-vault.md) | Mostra os campos em eventos de Key Vault. |

## <a name="media-services"></a>Serviços de Multimédia

Assine os eventos dos serviços de mídia para responder aos eventos de estado do trabalho.

|Título  |Descrição  |
|---------|---------|
| [Visão geral: reagindo aos eventos dos serviços de mídia](../media-services/latest/reacting-to-media-services-events.md) | Visão geral da integração dos serviços de mídia com a grade de eventos. |
| [Tutorial: rotear eventos dos serviços de mídia do Azure para um ponto de extremidade da Web personalizado usando a CLI](../media-services/latest/job-state-events-cli-how-to.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Mostra como enviar eventos dos serviços de mídia. |
| [Esquema de evento](../media-services/latest/media-services-event-schemas.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Mostra os campos nos eventos dos serviços de mídia. |

## <a name="resource-groups"></a>Grupos de recursos

Assine eventos de grupo de recursos para responder a alterações nos recursos em um grupo de recursos.

|Título  |Descrição  |
|---------|---------|
| [Tutorial: monitorar alterações de máquina virtual com a grade de eventos do Azure e aplicativos lógicos](monitor-virtual-machine-changes-event-grid-logic-app.md) | Um aplicativo lógico monitora as alterações em uma máquina virtual e envia emails sobre essas alterações. |
| [CLI do Azure: assinar eventos para um grupo de recursos](./scripts/event-grid-cli-resource-group.md)| Script de exemplo que assina eventos para um grupo de recursos. Ele envia eventos para um webhook. |
| [CLI do Azure: assinar eventos para um grupo de recursos e filtrar um recurso](./scripts/event-grid-cli-resource-group-filter.md) | Script de exemplo que assina eventos para um grupo de recursos e filtra eventos para um recurso. |
| [PowerShell: assinar eventos para um grupo de recursos](./scripts/event-grid-powershell-resource-group.md) | Script de exemplo que assina eventos para um grupo de recursos. Ele envia eventos para um webhook. |
| [PowerShell: assinar eventos para um grupo de recursos e filtrar um recurso](./scripts/event-grid-powershell-resource-group-filter.md) | Script de exemplo que assina eventos para um grupo de recursos e filtra eventos para um recurso. |
| [Modelo do Resource Manager: assinatura de recurso](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-resource-events-to-webhook) | Assina eventos para uma assinatura do Azure ou grupo de recursos. Ele envia eventos para um webhook. |
| [Esquema de Eventos](event-schema-resource-groups.md) | Mostra os campos em eventos de grupo de recursos. |

## <a name="service-bus"></a>Service Bus

Assinar eventos do barramento de serviço para responder a mensagens sem um ouvinte ativo.

|Título  |Descrição  |
|---------|---------|
| [Tutorial: exemplos de integração do barramento de serviço do Azure para a grade de eventos do Azure](../service-bus-messaging/service-bus-to-event-grid-integration-example.md?toc=%2fazure%2fevent-grid%2ftoc.json) | A grade de eventos envia mensagens do tópico do barramento de serviço para o aplicativo de funções e o aplicativo lógico. |
| [Visão geral: barramento de serviço do Azure para integração da grade de eventos](../service-bus-messaging/service-bus-to-event-grid-integration-concept.md) | Visão geral da integração do barramento de serviço com a grade de eventos. |
| [Esquema de evento](event-schema-service-bus.md) | Mostra os campos nos eventos do barramento de serviço. |

## <a name="storage"></a>Armazenamento

Assinar eventos de armazenamento de BLOBs para responder a eventos de blob criados e excluídos.

|Título  |Descrição  |
|---------|---------|
| [Início rápido: rotear eventos de armazenamento de BLOBs para um ponto de extremidade da Web personalizado com CLI do Azure](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Mostra como usar CLI do Azure para enviar eventos de armazenamento de BLOBs para um webhook. |
| [Início rápido: rotear eventos de armazenamento de BLOBs para um ponto de extremidade Web personalizado com o PowerShell](../storage/blobs/storage-blob-event-quickstart-powershell.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Mostra como usar Azure PowerShell para enviar eventos de armazenamento de BLOBs para um webhook. |
| [Início rápido: criar e rotear eventos de armazenamento de BLOBs com o portal do Azure](blob-event-quickstart-portal.md) | Mostra como usar o portal para enviar eventos de armazenamento de BLOBs para um webhook. |
| [CLI do Azure: assinar eventos para uma conta de armazenamento de BLOBs](./scripts/event-grid-cli-blob.md) | Script de exemplo que assina o evento para uma conta de armazenamento de BLOBs. Ele envia o evento para um webhook. |
| [PowerShell: assinar eventos para uma conta de armazenamento de BLOBs](./scripts/event-grid-powershell-blob.md) | Script de exemplo que assina o evento para uma conta de armazenamento de BLOBs. Ele envia o evento para um webhook. |
| [Modelo do Resource Manager: criar assinatura e armazenamento de BLOBs](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-subscription-and-storage) | Implementa uma conta de armazenamento de Blobs do Azure e subscreve eventos para essa conta de armazenamento. Ele envia eventos para um webhook. |
| [Visão geral: reagindo a eventos de armazenamento de BLOBs](../storage/blobs/storage-blob-event-overview.md) | Visão geral da integração do armazenamento de BLOBs com a grade de eventos. |
| [Esquema de evento](event-schema-blob-storage.md) | Mostra os campos em eventos de armazenamento de BLOBs. |

## <a name="maps"></a>Mapas
Assinar eventos do Azure Maps para responder a eventos de limite geográfico. Por exemplo, um aplicativo pode entregar uma notificação por email toda vez que um dispositivo entra ou sai de uma cerca geográfica.

|Título  |Descrição  |
|---------|---------|
| [Reagir aos eventos do Azure Maps usando a grade de eventos](../azure-maps/azure-maps-event-grid-integration.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Visão geral da integração do Azure Maps com a grade de eventos. |
| [Tutorial: configurar uma cerca geográfica](../azure-maps/tutorial-geofence.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Este tutorial orienta você pelas etapas básicas para configurar a cerca geográfica usando o Azure Maps. Você usa a grade de eventos do Azure para transmitir os resultados de cerca geográfica e configurar uma notificação com base nos resultados de cerca geográfica. |
| [Esquema de evento](event-schema-azure-maps.md) | Mostra os campos nos eventos do Azure Maps. |

## <a name="app-configuration"></a>Configuração da Aplicação
Assine Azure App eventos de configuração para responder a eventos de modificação de chave-valor.

|Título | Descrição |
|---------|---------|
| [Reagir a Azure App eventos de configuração usando a grade de eventos](../azure-app-configuration/concept-app-configuration-event.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Visão geral da integração da configuração de Azure App com a grade de eventos. |
| [Início rápido: rotear Azure App eventos de configuração para um ponto de extremidade da Web personalizado com CLI do Azure](../azure-app-configuration/howto-app-configuration-event.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Mostra como usar CLI do Azure para enviar Azure App eventos de configuração para um webhook. |
| [Esquema de evento](event-schema-app-configuration.md) | Mostra os campos em Azure App eventos de configuração. |

## <a name="azure-signalr"></a>Azure SignalR
Assine os eventos do serviço de Signaler do Azure para responder aos eventos de conexão do cliente.

|Título | Descrição |
|---------|---------|
| [Reagir aos eventos do serviço de Signaler do Azure usando a grade de eventos](../azure-signalr/signalr-concept-event-grid-integration.md) | Visão geral da integração do serviço de Signaler do Azure com a grade de eventos. |
| [Como enviar eventos do serviço de Signaler do Azure para a grade de eventos](../azure-signalr/signalr-howto-event-grid-integration.md) | Mostra como enviar eventos do serviço de Signaler do Azure para um aplicativo por meio da grade de eventos. |
| [Esquema de evento](event-schema-azure-signalr.md) | Mostra os campos nos eventos do serviço de Signaler do Azure. |



## <a name="next-steps"></a>Passos seguintes

* Para obter uma introdução ao Event Grid, veja [Sobre o Azure Event Grid](overview.md).
* Para começar a usar a grade de eventos rapidamente, consulte [criar e rotear eventos personalizados com a grade de eventos do Azure](custom-event-quickstart.md).
