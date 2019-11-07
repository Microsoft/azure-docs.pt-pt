---
title: Manipuladores de eventos da grade de eventos do Azure
description: Descreve os manipuladores de eventos com suporte para a grade de eventos do Azure
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: spelluru
ms.openlocfilehash: 21a66b7389df64a776cdecb45c41de56d7d258e4
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/05/2019
ms.locfileid: "73606369"
---
# <a name="event-handlers-in-azure-event-grid"></a>Manipuladores de eventos na grade de eventos do Azure

Um manipulador de eventos é o local onde o evento é enviado. O manipulador executa algumas outras ações para processar o evento. Vários serviços do Azure são configurados automaticamente para manipular eventos. Você também pode usar qualquer webhook para manipular eventos. O webhook não precisa ser hospedado no Azure para manipular eventos. A grade de eventos só dá suporte a pontos de extremidade de webhook HTTPS.

Este artigo fornece links para o conteúdo de cada manipulador de eventos.

## <a name="azure-automation"></a>Automatização do Azure

Use a automação do Azure para processar eventos com runbooks automatizados.

|Título  |Descrição  |
|---------|---------|
|[Tutorial: automação do Azure com grade de eventos e Microsoft Teams](ensure-tags-exists-on-new-virtual-machines.md) |Crie uma máquina virtual, que envia um evento. O evento dispara um runbook de automação que marca a máquina virtual e dispara uma mensagem que é enviada para um canal do Microsoft Teams. |

## <a name="azure-functions"></a>Funções do Azure

Use Azure Functions para a resposta sem servidor a eventos.

Se utilizar as Funções do Azure como o processador, utilize o acionador do Event Grid em vez dos acionadores HTTP genéricos. O Event Grid valida automaticamente os acionadores de função do Event Grid. Com os acionadores HTTP genéricos, tem de implementar a [resposta de validação](security-authentication.md#webhook-event-delivery).

|Título  |Descrição  |
|---------|---------|
| [Gatilho de grade de eventos para Azure Functions](../azure-functions/functions-bindings-event-grid.md) | Visão geral do uso do gatilho de grade de eventos no functions. |
| [Tutorial: automatizar o redimensionamento de imagens carregadas usando a grade de eventos](resize-images-on-storage-blob-upload-event.md) | Os usuários carregam imagens por meio do aplicativo Web para a conta de armazenamento. Quando um blob de armazenamento é criado, a grade de eventos envia um evento para o aplicativo de funções, que redimensiona a imagem carregada. |
| [Tutorial: transmitir Big Data para um data warehouse](event-grid-event-hubs-integration.md) | Quando os hubs de eventos criam um arquivo de captura, a grade de eventos envia um evento para um aplicativo de funções. O aplicativo recupera o arquivo de captura e migra os dados para um data warehouse. |
| [Tutorial: exemplos de integração do barramento de serviço do Azure para a grade de eventos do Azure](../service-bus-messaging/service-bus-to-event-grid-integration-example.md?toc=%2fazure%2fevent-grid%2ftoc.json) | A grade de eventos envia mensagens do tópico do barramento de serviço para o aplicativo de funções e o aplicativo lógico. |

## <a name="event-hubs"></a>Event Hubs

Use os hubs de eventos quando sua solução receber eventos mais rapidamente do que pode processar os eventos. Seu aplicativo processa os eventos dos hubs de eventos no próprio agendamento. Você pode dimensionar o processamento de eventos para lidar com os eventos de entrada.

Os hubs de eventos podem atuar como um manipulador de eventos ou origem de evento. O artigo a seguir mostra como usar os hubs de eventos como um manipulador.

|Título  |Descrição  |
|---------|---------|
| [Início rápido: rotear eventos personalizados para hubs de eventos do Azure com CLI do Azure e grade de eventos](custom-event-to-eventhub.md) | Envia um evento personalizado para um hub de eventos para processamento por um aplicativo. |
| [Modelo do Resource Manager: ponto de extremidade do tópico personalizado e dos hubs de eventos](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-event-hubs-handler)| Um modelo do Resource Manager que cria uma assinatura para um tópico personalizado. Ele envia eventos para um hub de eventos do Azure. |

Para obter exemplos de hubs de eventos como uma fonte, consulte [origem dos hubs de eventos](event-sources.md#event-hubs).

## <a name="hybrid-connections"></a>Ligações Híbridas

Use o Conexões Híbridas de retransmissão do Azure para enviar eventos para aplicativos que estão em uma rede corporativa e não têm um ponto de extremidade publicamente acessível.

|Título  |Descrição  |
|---------|---------|
| [Tutorial: enviar eventos para conexão híbrida](custom-event-to-hybrid-connection.md) | Envia um evento personalizado para uma conexão híbrida existente para processamento por um aplicativo de ouvinte. |

## <a name="logic-apps"></a>Aplicações Lógicas

Use aplicativos lógicos para automatizar processos de negócios para responder a eventos.

|Título  |Descrição  |
|---------|---------|
| [Tutorial: monitorar alterações de máquina virtual com a grade de eventos do Azure e aplicativos lógicos](monitor-virtual-machine-changes-event-grid-logic-app.md) | Um aplicativo lógico monitora as alterações em uma máquina virtual e envia emails sobre essas alterações. |
| [Tutorial: enviar notificações por email sobre eventos do Hub IoT do Azure usando aplicativos lógicos](publish-iot-hub-events-to-logic-apps.md) | Um aplicativo lógico envia um email de notificação toda vez que um dispositivo é adicionado ao Hub IoT. |
| [Tutorial: exemplos de integração do barramento de serviço do Azure para a grade de eventos do Azure](../service-bus-messaging/service-bus-to-event-grid-integration-example.md?toc=%2fazure%2fevent-grid%2ftoc.json) | A grade de eventos envia mensagens do tópico do barramento de serviço para o aplicativo de funções e o aplicativo lógico. |

## <a name="service-bus-queue"></a>Fila do Service Bus 
Você pode rotear eventos na grade de eventos diretamente para filas do barramento de serviço para uso em buffer ou comando & cenários de controle em aplicativos empresariais.

### <a name="using-cli-to-add-a-service-bus-handler"></a>Usando a CLI para adicionar um manipulador de barramento de serviço

Por CLI do Azure, o exemplo a seguir assina e conecta um tópico da grade de eventos a uma fila do barramento de serviço:

```azurecli-interactive
# If you haven't already installed the extension, do it now.
# This extension is required for preview features.
az extension add --name eventgrid

az eventgrid event-subscription create \
    --name <my-event-subscription> \
    --source-resource-id /subscriptions/{SubID}/resourceGroups/{RG}/providers/Microsoft.EventGrid/topics/topic1 \
    --endpoint-type servicebusqueue \
    --endpoint /subscriptions/{SubID}/resourceGroups/TestRG/providers/Microsoft.ServiceBus/namespaces/ns1/queues/queue1
```

## <a name="queue-storage"></a>Armazenamento de Filas

Use o armazenamento de filas para receber eventos que precisam ser obtidos. Você pode usar o armazenamento de fila quando tiver um processo de longa execução que leva muito tempo para responder. Ao enviar eventos para o armazenamento de fila, o aplicativo pode efetuar pull e processar eventos em sua própria agenda.

|Título  |Descrição  |
|---------|---------|
| [Início rápido: rotear eventos personalizados para o armazenamento de filas do Azure com CLI do Azure e a grade de eventos](custom-event-to-queue-storage.md) | Descreve como enviar eventos personalizados para um armazenamento de fila. |

## <a name="webhooks"></a>WebHooks

Use WebHooks para pontos de extremidade personalizáveis que respondem a eventos.

|Título  |Descrição  |
|---------|---------|
| Início rápido: criar e rotear eventos personalizados com- [CLI do Azure](custom-event-quickstart.md), [PowerShell](custom-event-quickstart-powershell.md)e [portal](custom-event-quickstart-portal.md). | Mostra como enviar eventos personalizados para um webhook. |
| Início rápido: rotear eventos de armazenamento de BLOBs para um ponto de extremidade da Web personalizado com- [CLI do Azure](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json), [PowerShell](../storage/blobs/storage-blob-event-quickstart-powershell.md?toc=%2fazure%2fevent-grid%2ftoc.json)e [portal](blob-event-quickstart-portal.md). | Mostra como enviar eventos de armazenamento de BLOBs para um webhook. |
| [Início rápido: enviar eventos de registro de contêiner](../container-registry/container-registry-event-grid-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Mostra como usar CLI do Azure para enviar eventos de registro de contêiner. |
| [Visão geral: receber eventos para um ponto de extremidade HTTP](receive-events.md) | Descreve como validar um ponto de extremidade HTTP para receber eventos de uma assinatura de evento e receber e desserializar eventos. |

## <a name="next-steps"></a>Passos seguintes

* Para obter uma introdução ao Event Grid, veja [Sobre o Azure Event Grid](overview.md).
* Para começar a usar a grade de eventos rapidamente, consulte [criar e rotear eventos personalizados com a grade de eventos do Azure](custom-event-quickstart.md).
