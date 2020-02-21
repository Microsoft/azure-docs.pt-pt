---
title: Manipuladores de eventos da grade de eventos do Azure
description: Descreve os manipuladores de eventos com suporte para a grade de eventos do Azure. Automação Azure, Funções, Hubs de Eventos, Conexões Híbridas, Aplicações Lógicas, Ônibus de Serviço, Armazenamento de Filas, Webhooks.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 01/21/2020
ms.author: spelluru
ms.openlocfilehash: 7ea00d663264e902c1818f7a4684e90eccd97b28
ms.sourcegitcommit: 3c8fbce6989174b6c3cdbb6fea38974b46197ebe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/21/2020
ms.locfileid: "77525809"
---
# <a name="event-handlers-in-azure-event-grid"></a>Manipuladores de eventos na grade de eventos do Azure

Um manipulador de eventos é o local onde o evento é enviado. O manipulador executa algumas outras ações para processar o evento. Vários serviços do Azure são configurados automaticamente para manipular eventos. Você também pode usar qualquer webhook para manipular eventos. O webhook não precisa ser hospedado no Azure para manipular eventos. A grade de eventos só dá suporte a pontos de extremidade de webhook HTTPS.

Este artigo fornece links para o conteúdo de cada manipulador de eventos.

## <a name="azure-automation"></a>Automatização do Azure

Use a automação do Azure para processar eventos com runbooks automatizados.

|Título  |Descrição  |
|---------|---------|
|[Tutorial: Automação Azure com Grelha de Eventos e Equipas da Microsoft](ensure-tags-exists-on-new-virtual-machines.md) |Crie uma máquina virtual, que envie um evento. O evento despoleta um livro de automação que marca a máquina virtual e despoleta uma mensagem que é enviada para um canal da Microsoft Teams. |

## <a name="azure-functions"></a>Funções do Azure

Use Azure Functions para a resposta sem servidor a eventos.

Se utilizar as Funções do Azure como o processador, utilize o acionador do Event Grid em vez dos acionadores HTTP genéricos. O Event Grid valida automaticamente os acionadores de função do Event Grid. Com os acionadores HTTP genéricos, tem de implementar a [resposta de validação](security-authentication.md#webhook-event-delivery).

|Título  |Descrição  |
|---------|---------|
| [Quickstart: Lidar com eventos com função](custom-event-to-function.md) | Envia um evento personalizado para uma função para processamento. |
| [Gatilho da Grelha de Eventos para Funções Azure](../azure-functions/functions-bindings-event-grid.md) | Visão geral do uso do gatilho de grade de eventos no functions. |
| [Tutorial: automatizar imagens carregadas usando a Grelha de Eventos](resize-images-on-storage-blob-upload-event.md) | Os usuários carregam imagens por meio do aplicativo Web para a conta de armazenamento. Quando um blob de armazenamento é criado, a grade de eventos envia um evento para o aplicativo de funções, que redimensiona a imagem carregada. |
| [Tutorial: transmitir big data em um armazém de dados](event-grid-event-hubs-integration.md) | Quando o Event Hubs cria um ficheiro Capture, a Rede de Eventos envia um evento para uma aplicação de função. A aplicação recupera o ficheiro Capture e migra dados para um armazém de dados. |
| [Tutorial: Ônibus de serviço azure para exemplos de integração da Rede de Eventos Azure](../service-bus-messaging/service-bus-to-event-grid-integration-example.md?toc=%2fazure%2fevent-grid%2ftoc.json) | A Rede de Eventos envia mensagens do tópico do Bus de Serviço para a aplicação de função e lógica. |

## <a name="event-hubs"></a>Hubs de Eventos

Use os hubs de eventos quando sua solução receber eventos mais rapidamente do que pode processar os eventos. Seu aplicativo processa os eventos dos hubs de eventos no próprio agendamento. Você pode dimensionar o processamento de eventos para lidar com os eventos de entrada.

Os Hubs de Eventos podem funcionar como uma fonte de eventoou manipulador de eventos. O artigo a seguir mostra como usar os hubs de eventos como um manipulador.

|Título  |Descrição  |
|---------|---------|
| [Quickstart: encaminha eventos personalizados para Hubs de Eventos Azure com Azure CLI e Grid de Eventos](custom-event-to-eventhub.md) | Envia um evento personalizado para um hub de eventos para processamento por um aplicativo. |
| [Modelo de Gestor de Recursos: tópico personalizado e ponto final do Event Hubs](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-event-hubs-handler)| Um modelo de Gestor de Recursos que cria uma subscrição para um tópico personalizado. Envia eventos para um Azure Event Hubs. |

Por exemplo, de Centros de Eventos como fonte, consulte a [fonte do Event Hubs](event-sources.md#event-hubs).

## <a name="hybrid-connections"></a>Ligações Híbridas

Use o Conexões Híbridas de retransmissão do Azure para enviar eventos para aplicativos que estão em uma rede corporativa e não têm um ponto de extremidade publicamente acessível.

|Título  |Descrição  |
|---------|---------|
| [Tutorial: enviar eventos para conexão híbrida](custom-event-to-hybrid-connection.md) | Envia um evento personalizado para uma conexão híbrida existente para processamento por um aplicativo de ouvinte. |

## <a name="logic-apps"></a>Aplicações Lógicas

Use aplicativos lógicos para automatizar processos de negócios para responder a eventos.

|Título  |Descrição  |
|---------|---------|
| [Tutorial: monitorizar alterações de máquinas virtuais com grelha de eventos azure e aplicações lógicas](monitor-virtual-machine-changes-event-grid-logic-app.md) | Uma aplicação lógica monitoriza alterações a uma máquina virtual e envia e-mails sobre essas alterações. |
| [Tutorial: envie notificações por e-mail sobre eventos do Hub Azure IoT usando aplicações lógicas](publish-iot-hub-events-to-logic-apps.md) | Um aplicativo lógico envia um email de notificação toda vez que um dispositivo é adicionado ao Hub IoT. |
| [Tutorial: Ônibus de serviço azure para exemplos de integração da Rede de Eventos Azure](../service-bus-messaging/service-bus-to-event-grid-integration-example.md?toc=%2fazure%2fevent-grid%2ftoc.json) | A Rede de Eventos envia mensagens do tópico do Bus de Serviço para a aplicação de função e lógica. |

## <a name="service-bus"></a>Service Bus

### <a name="service-bus-queues"></a>Filas do Service Bus

Você pode rotear eventos na grade de eventos diretamente para filas do barramento de serviço para uso em buffer ou comando & cenários de controle em aplicativos empresariais.

Na portal do Azure, ao criar uma assinatura de evento, selecione "fila do barramento de serviço" como tipo de ponto de extremidade e clique em "selecionar um ponto de extremidade" para escolher uma fila do barramento de serviço.

#### <a name="using-cli-to-add-a-service-bus-queue-handler"></a>Usando a CLI para adicionar um manipulador de fila do barramento de serviço

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

### <a name="service-bus-topics"></a>Tópicos de ônibus de serviço

Você pode rotear eventos na grade de eventos diretamente nos tópicos do barramento de serviço para manipular eventos do sistema do Azure com tópicos de barramento de serviço ou para cenários de & de mensagens de controle de comando.

Na portal do Azure, ao criar uma assinatura de evento, selecione "tópico do barramento de serviço" como tipo de ponto de extremidade e clique em "selecionar e ponto de extremidade" para escolher um tópico do barramento de serviço.

#### <a name="using-cli-to-add-a-service-bus-topic-handler"></a>Usando a CLI para adicionar um manipulador de tópico do barramento de serviço

Por CLI do Azure, o exemplo a seguir assina e conecta um tópico da grade de eventos a uma fila do barramento de serviço:

```azurecli-interactive
# If you haven't already installed the extension, do it now.
# This extension is required for preview features.
az extension add --name eventgrid

az eventgrid event-subscription create \
    --name <my-event-subscription> \
    --source-resource-id /subscriptions/{SubID}/resourceGroups/{RG}/providers/Microsoft.EventGrid/topics/topic1 \
    --endpoint-type servicebustopic \
    --endpoint /subscriptions/{SubID}/resourceGroups/TestRG/providers/Microsoft.ServiceBus/namespaces/ns1/topics/topic1
```

## <a name="queue-storage"></a>Armazenamento de filas

Use o armazenamento de filas para receber eventos que precisam ser obtidos. Você pode usar o armazenamento de fila quando tiver um processo de longa execução que leva muito tempo para responder. Ao enviar eventos para o armazenamento de fila, o aplicativo pode efetuar pull e processar eventos em sua própria agenda.

|Título  |Descrição  |
|---------|---------|
| [Quickstart: enverede eventos personalizados para armazenamento de fila Azure com Azure CLI e Grade de Eventos](custom-event-to-queue-storage.md) | Descreve como enviar eventos personalizados para um armazenamento de fila. |

## <a name="webhooks"></a>WebHooks

Use WebHooks para pontos de extremidade personalizáveis que respondem a eventos.

|Título  |Descrição  |
|---------|---------|
| Quickstart: criar e encaminhar eventos personalizados com - [Azure CLI,](custom-event-quickstart.md) [PowerShell](custom-event-quickstart-powershell.md), e [portal](custom-event-quickstart-portal.md). | Mostra como enviar eventos personalizados para um webhook. |
| Quickstart: encaminhe os eventos de armazenamento blob para um ponto final web personalizado com - [Azure CLI,](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json) [PowerShell](../storage/blobs/storage-blob-event-quickstart-powershell.md?toc=%2fazure%2fevent-grid%2ftoc.json), e [portal](blob-event-quickstart-portal.md). | Mostra como enviar eventos de armazenamento de BLOBs para um webhook. |
| [Quickstart: envie eventos de registo de contentores](../container-registry/container-registry-event-grid-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Mostra como usar o Azure CLI para enviar eventos de registo de contentores. |
| [Visão geral: receba eventos para um ponto final http](receive-events.md) | Descreve como validar um ponto de extremidade HTTP para receber eventos de uma assinatura de evento e receber e desserializar eventos. |

## <a name="next-steps"></a>Passos seguintes

* Para obter uma introdução ao Event Grid, veja [Sobre o Azure Event Grid](overview.md).
* Para começar rapidamente a usar a Grelha de Eventos, consulte [create e encaminhe eventos personalizados com a Grelha de Eventos Azure](custom-event-quickstart.md).
