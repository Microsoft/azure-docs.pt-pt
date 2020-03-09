---
title: Fontes do evento Azure Event Grid
description: Assinaturas Azure, Registo de Contentores, tópicos personalizados, Hubs de Eventos, Hub IoT, Cofre chave, Serviços de Media, grupos de recursos, Ônibus de Serviço, Armazenamento, Mapas, Configuração de Aplicações, Sinal R, Machine Learning.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 02/12/2019
ms.author: spelluru
ms.openlocfilehash: d4a426ea1432d0266b7ae9344afefe8ddac1d030
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78359276"
---
# <a name="event-sources-in-azure-event-grid"></a>Fontes do evento na Grelha de Eventos Azure

Uma fonte de evento é onde o evento acontece. Vários serviços Azure são configurados automaticamente para enviar eventos. Também pode criar aplicações personalizadas que enviam eventos. As aplicações personalizadas não precisam de ser hospedadas em Azure para utilizar a Grelha de Eventos para distribuição de eventos.

Este artigo fornece links para conteúdos para cada evento Fonte.

## <a name="azure-subscriptions"></a>Assinaturas Azure

Subscreva os eventos da Azure Subscriptions para responder às mudanças de recursos através de uma subscrição do Azure.

|título |Descrição  |
|---------|---------|
| [Tutorial: Automação Azure com Grelha de Eventos e Equipas da Microsoft](ensure-tags-exists-on-new-virtual-machines.md) |Crie uma máquina virtual, que envie um evento. O evento despoleta um livro de automação que marca a máquina virtual e despoleta uma mensagem que é enviada para um canal da Microsoft Teams. |
| [Como: subscrever eventos através do portal](subscribe-through-portal.md) | Utilize o portal para subscrever eventos para uma subscrição do Azure. |
| [Azure CLI: subscreva eventos para uma subscrição do Azure](./scripts/event-grid-cli-azure-subscription.md) |Sample script que cria uma subscrição de Event Grid para uma subscrição Azure e envia eventos para um WebHook. |
| [PowerShell: subscreva eventos para uma subscrição do Azure](./scripts/event-grid-powershell-azure-subscription.md)| Sample script que cria uma subscrição de Event Grid para uma subscrição Azure e envia eventos para um WebHook. |
| [Esquema do evento](event-schema-subscriptions.md) | Mostra campos em eventos de subscrição do Azure. |

## <a name="container-registry"></a>Registo de Contentores

Subscreva os eventos do Registo de Contentores para responder às alterações nas imagens.

|título |Descrição  |
|---------|---------|
| [Quickstart: envie eventos de registo de contentores](../container-registry/container-registry-event-grid-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Mostra como usar o Azure CLI para enviar eventos de registo de contentores. |
| [Esquema do evento](event-schema-container-registry.md) | Mostra campos em eventos de registo de contentores. |

## <a name="custom-topics"></a>Tópicos personalizados

Subscreva tópicos personalizados para responder a eventos de candidatura.

|título  |Descrição  |
|---------|---------|
| [Quickstart: criar e encaminhar eventos personalizados com o Azure CLI](custom-event-quickstart.md) | Mostra como usar o Azure CLI para enviar eventos personalizados. |
| [Quickstart: criar e encaminhar eventos personalizados com a Azure PowerShell](custom-event-quickstart-powershell.md) | Mostra como usar o Azure PowerShell para enviar eventos personalizados. |
| [Quickstart: criar e encaminhar eventos personalizados com o portal Azure](custom-event-quickstart-portal.md) | Mostra como usar o portal para enviar eventos personalizados. |
| [Quickstart: encaminha eventos personalizados para o armazenamento da Fila Azure](custom-event-to-queue-storage.md) | Descreve como enviar eventos personalizados para um armazenamento de fila. |
| [Como: post arnde ao tópico personalizado](post-to-custom-topic.md) | Mostra como publicar um evento num tópico personalizado. |
| [Azure CLI: crie tópico personalizado da Grelha de Eventos](./scripts/event-grid-cli-create-custom-topic.md)|O script da amostra que cria um tópico personalizado. O guião recupera o ponto final e uma chave.|
| [Azure CLI: subscreva eventos para um tópico personalizado](./scripts/event-grid-cli-subscribe-custom-topic.md)|Sample script que cria uma subscrição para um tópico personalizado. Envia eventos para um WebHook.|
| [PowerShell: criar tópico personalizado da Grelha de Eventos](./scripts/event-grid-powershell-create-custom-topic.md)|O script da amostra que cria um tópico personalizado. O guião recupera o ponto final e uma chave.|
| [PowerShell: subscreva eventos para um tópico personalizado](./scripts/event-grid-powershell-subscribe-custom-topic.md)|Sample script que cria uma subscrição para um tópico personalizado. Envia eventos para um WebHook.|
| [Modelo de Gestor de Recursos: tópico personalizado e ponto final do WebHook](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid) | Um modelo de Gestor de Recursos que cria um tópico personalizado e subscrição para esse tópico personalizado. Envia eventos para um WebHook. |
|
| [Modelo de Gestor de Recursos: tópico personalizado e ponto final do Event Hubs](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-event-hubs-handler)| Um modelo de Gestor de Recursos que cria uma subscrição para um tópico personalizado. Envia eventos para um Azure Event Hubs. |
| [Esquema do evento](event-schema.md) | Mostra campos em eventos personalizados. |

## <a name="event-hubs"></a>Hubs de eventos

Subscreva os eventos do Event Hubs para responder a eventos de ficheiros De captura. Os Hubs de Eventos podem funcionar como uma fonte de eventoou manipulador de eventos. Os seguintes artigos mostram como usar os Centros de Eventos como fonte.

|título  |Descrição  |
|---------|---------|
| [Tutorial: transmitir big data em um armazém de dados](event-grid-event-hubs-integration.md) | Quando o Event Hubs cria um ficheiro Capture, a Rede de Eventos envia um evento para uma aplicação de função. A aplicação recupera o ficheiro Capture e migra dados para um armazém de dados. |
| [Esquema do evento](event-schema-event-hubs.md) | Mostra campos em eventos do Event Hubs. |

Por exemplo, de Event Hubs como manipulador, consulte o manipulador de Centros de [Eventos](event-handlers.md#event-hubs).

## <a name="iot-hub"></a>IoT Hub

Subscreva os eventos IoT Hub para responder a eventos criados, apagados, conectados, desligados e de telemetria.

|título  |Descrição  |
|---------|---------|
| [Envie notificações por e-mail sobre eventos do Hub Azure IoT usando aplicações lógicas](publish-iot-hub-events-to-logic-apps.md) | Uma aplicação lógica envia um e-mail de notificação sempre que um dispositivo é adicionado ao seu IoT Hub. |
| [Reagir aos eventos do IoT Hub usando a Grelha de Eventos para desencadear ações](../iot-hub/iot-hub-event-grid.md) | Visão geral da integração do IoT Hub com a Grelha de Eventos. |
| [Esquema do evento](event-schema-iot-hub.md) | Mostra campos em eventos do IoT Hub. |
| [Encomendar dispositivo conectado e dispositivo eventos desligados](../iot-hub/iot-hub-how-to-order-connection-state-events.md) | Mostra como encomendar eventos estatais de ligação do dispositivo. |

## <a name="key-vault-preview"></a>Cofre de chaves (pré-visualização)

A integração do Cofre Chave com a Grelha de Eventos está atualmente em pré-visualização. 

Subscreva os eventos key vault para ser notificado quando um segredo está prestes a expirar, um segredo expira, ou um segredo tem uma nova versão disponível. 

|título  |Descrição  |
|---------|---------|
| [Monitorização de eventos chave vault com grelha de eventos Azure](../key-vault/event-grid-overview.md) | Visão geral da integração do Cofre chave com grelha de eventos. |
| [Tutorial: Criar e monitorizar eventos chave vault com grelha de eventos](../key-vault/event-grid-tutorial.md) | Saiba como configurar notificações da Grelha de Eventos para o Cofre chave. |
| [Esquema do evento](event-schema-key-vault.md) | Mostra campos em eventos do Cofre Chave. |

## <a name="media-services"></a>Serviços de Media

Subscreva os eventos da Media Services para responder a eventos do estado do trabalho.

|título  |Descrição  |
|---------|---------|
| [Resumo: reagir a eventos de Serviços de Media](../media-services/latest/reacting-to-media-services-events.md) | Visão geral da integração dos Serviços de Media com A Grelha de Eventos. |
| [Tutorial: encaminha os eventos da Azure Media Services para um ponto final personalizado usando o CLI](../media-services/latest/job-state-events-cli-how-to.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Mostra como enviar eventos dos Serviços de Media. |
| [Esquema do evento](../media-services/latest/media-services-event-schemas.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Mostra campos em eventos de Serviços de Media. |

## <a name="resource-groups"></a>Grupos de recursos

Subscreva eventos de grupo de recursos para responder a mudanças de recursos em um grupo de recursos.

|título  |Descrição  |
|---------|---------|
| [Tutorial: monitorizar alterações de máquinas virtuais com grelha de eventos azure e aplicações lógicas](monitor-virtual-machine-changes-event-grid-logic-app.md) | Uma aplicação lógica monitoriza alterações a uma máquina virtual e envia e-mails sobre essas alterações. |
| [Azure CLI: subscreva eventos para um grupo de recursos](./scripts/event-grid-cli-resource-group.md)| Sample script que subscreve eventos para um grupo de recursos. Envia eventos para um WebHook. |
| [Azure CLI: subscreva eventos para um grupo de recursos e filtre para um recurso](./scripts/event-grid-cli-resource-group-filter.md) | Sample script que subscreve eventos para um grupo de recursos e filtra eventos para um recurso. |
| [PowerShell: subscreva eventos para um grupo de recursos](./scripts/event-grid-powershell-resource-group.md) | Sample script que subscreve eventos para um grupo de recursos. Envia eventos para um WebHook. |
| [PowerShell: subscreva eventos para um grupo de recursos e filtre para um recurso](./scripts/event-grid-powershell-resource-group-filter.md) | Sample script que subscreve eventos para um grupo de recursos e filtra eventos para um recurso. |
| [Modelo de Gestor de Recursos: subscrição de recursos](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-resource-events-to-webhook) | Subscreve eventos para uma subscrição azure ou grupo de recursos. Envia eventos para um WebHook. |
| [Evento Schema](event-schema-resource-groups.md) | Mostra campos em eventos de grupo de recursos. |

## <a name="service-bus"></a>Ônibus de serviço

Subscreva os eventos da Service Bus para responder a mensagens sem um ouvinte ativo.

|título  |Descrição  |
|---------|---------|
| [Tutorial: Ônibus de serviço azure para exemplos de integração da Rede de Eventos Azure](../service-bus-messaging/service-bus-to-event-grid-integration-example.md?toc=%2fazure%2fevent-grid%2ftoc.json) | A Rede de Eventos envia mensagens do tópico do Bus de Serviço para a aplicação de função e lógica. |
| [Visão geral: Ônibus de serviço Azure para integração da Rede de Eventos](../service-bus-messaging/service-bus-to-event-grid-integration-concept.md) | Visão geral da integração do ônibus de serviço com grelha de eventos. |
| [Esquema do evento](event-schema-service-bus.md) | Mostra campos em eventos de ônibus de serviço. |

## <a name="storage"></a>armazenamento

Subscreva os eventos blob Storage para responder a eventos blob criados e apagados.

>[!NOTE]
> Apenas contas de armazenamento do tipo **StorageV2 (finalidade geral v2)** e integração de eventos de suporte **blobStorage.** **O armazenamento (finalidade geral v1)** *não* suporta a integração com a Grelha de Eventos.

|título  |Descrição  |
|---------|---------|
| [Quickstart: encaminhe eventos de armazenamento blob para um ponto final web personalizado com Azure CLI](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Mostra como usar o Azure CLI para enviar eventos de armazenamento blob para um WebHook. |
| [Quickstart: encaminhe eventos de armazenamento blob para um ponto final personalizado com powerShell](../storage/blobs/storage-blob-event-quickstart-powershell.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Mostra como usar o Azure PowerShell para enviar eventos de armazenamento de blob para um WebHook. |
| [Quickstart: criar e encaminhar eventos de armazenamento Blob com o portal Azure](blob-event-quickstart-portal.md) | Mostra como usar o portal para enviar eventos de armazenamento blob para um WebHook. |
| [Azure CLI: subscreva eventos para uma conta de armazenamento Blob](./scripts/event-grid-cli-blob.md) | Prove script que subscreve evento para uma conta de armazenamento Blob. Envia o evento para um WebHook. |
| [PowerShell: subscreva eventos para uma conta de armazenamento Blob](./scripts/event-grid-powershell-blob.md) | Prove script que subscreve evento para uma conta de armazenamento Blob. Envia o evento para um WebHook. |
| [Modelo de Gestor de Recursos: Criar armazenamento e subscrição blob](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-subscription-and-storage) | Implementa uma conta de armazenamento Azure Blob e subscreve eventos para essa conta de armazenamento. Envia eventos para um WebHook. |
| [Visão geral: reagir a eventos de armazenamento blob](../storage/blobs/storage-blob-event-overview.md) | Visão geral da integração do armazenamento blob com a Grelha de Eventos. |
| [Esquema do evento](event-schema-blob-storage.md) | Mostra campos em eventos de armazenamento blob. |

## <a name="maps"></a>Mapas
Subscreva os eventos do Azure Maps para responder a eventos geovedantes. Por exemplo, uma aplicação poderia entregar uma notificação por e-mail sempre que um dispositivo entrasse ou saísse de uma geovedação.

|título  |Descrição  |
|---------|---------|
| [Reaja aos eventos do Azure Maps usando a Grelha de Eventos](../azure-maps/azure-maps-event-grid-integration.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Visão geral da integração do Azure Maps com a Grelha de Eventos. |
| [Tutorial: Criar uma geovete](../azure-maps/tutorial-geofence.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Este tutorial acompanha-o através dos passos básicos para configurar geovese usando o Azure Maps. Utilize a Rede de Eventos Azure para transmitir os resultados da geovedação e configurar uma notificação com base nos resultados da geovedação. |
| [Esquema do evento](event-schema-azure-maps.md) | Mostra campos em eventos do Azure Maps. |

## <a name="app-configuration"></a>Configuração de aplicativos
Subscreva os eventos de configuração de aplicações do Azure para responder a eventos de modificação de valor-chave.

|título | Descrição |
|---------|---------|
| [Reaja aos eventos de configuração de aplicações do Azure utilizando a Grelha de Eventos](../azure-app-configuration/concept-app-configuration-event.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Visão geral da integração da configuração da app Azure com grelha de eventos. |
| [Quickstart: encaminha eventos de configuração de aplicações azure para um ponto final personalizado com O ClI Azure](../azure-app-configuration/howto-app-configuration-event.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Mostra como usar o Azure CLI para enviar eventos de configuração de aplicações Azure para um WebHook. |
| [Esquema do evento](event-schema-app-configuration.md) | Mostra campos em eventos de configuração de aplicações azure. |

## <a name="azure-signalr"></a>Sinal de Azure
Subscreva os eventos do Serviço De Sinalização Azure para responder a eventos de ligação ao cliente.

|título | Descrição |
|---------|---------|
| [Reaja aos eventos do Serviço De Sinalização Azure utilizando a Grelha de Eventos](../azure-signalr/signalr-concept-event-grid-integration.md) | Visão geral da integração do Serviço de Sinalização Azure com grelha de eventos. |
| [Como enviar eventos do Serviço De Sinalização Azure para a Grelha de Eventos](../azure-signalr/signalr-howto-event-grid-integration.md) | Mostra como enviar eventos do Serviço De Sinalização Azure para uma aplicação através da Rede de Eventos. |
| [Esquema do evento](event-schema-azure-signalr.md) | Mostra campos em eventos do Serviço De Sinalização Azure. |

## <a name="azure-machine-learning"></a>Aprendizagem automática azure

Subscreva os eventos do espaço de trabalho Azure Machine Learning para responder ao registo do modelo.

| título | Descrição |
| ----- | ----- |
| [Consumir eventos de aprendizagem automática azure](../machine-learning/concept-event-grid-integration.md) | Visão geral da integração de Machine Learning Azure com Grelha de Eventos. |
| [Esquema de evento de azure event para azure machine learning](event-schema-machine-learning.md) | Mostra campos nos eventos de Aprendizagem automática azure. |

## <a name="next-steps"></a>Próximos passos

* Para uma introdução à Grelha de Eventos, consulte sobre a [Grelha de Eventos.](overview.md)
* Para começar rapidamente a usar a Grelha de Eventos, consulte [create e encaminhe eventos personalizados com a Grelha de Eventos Azure](custom-event-quickstart.md).
