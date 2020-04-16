---
title: Manipuladores de eventos azure Event Grid
description: Descreve os manipuladores de eventos suportados para a Grelha de Eventos Azure. Automação Azure, Funções, Hubs de Eventos, Conexões Híbridas, Aplicações Lógicas, Ônibus de Serviço, Armazenamento de Filas, Webhooks.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 01/21/2020
ms.author: spelluru
ms.openlocfilehash: 73118823aec9b8d4bd872986b13f19496240c0b9
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/15/2020
ms.locfileid: "81393446"
---
# <a name="event-handlers-in-azure-event-grid"></a>Manipuladores de eventos na Grelha de Eventos Azure

Um manipulador de eventos é o local onde o evento é enviado. O manipulador toma mais medidas para processar o evento. Vários serviços Azure são configurados automaticamente para lidar com eventos. Também pode utilizar qualquer WebHook para lidar com eventos. O WebHook não precisa de ser hospedado em Azure para lidar com eventos. A Grelha de Eventos suporta apenas pontos finais HTTPS WebHook.

Este artigo fornece links para conteúdo para cada manipulador de eventos.

## <a name="azure-automation"></a>Automatização do Azure

Utilize a Automação Azure para processar eventos com livros automáticos.

|Título  |Descrição  |
|---------|---------|
|[Tutorial: Automação Azure com Grelha de Eventos e Equipas da Microsoft](ensure-tags-exists-on-new-virtual-machines.md) |Crie uma máquina virtual, que envie um evento. O evento despoleta um livro de automação que marca a máquina virtual e despoleta uma mensagem que é enviada para um canal da Microsoft Teams. |

## <a name="azure-functions"></a>Funções do Azure

Utilize funções Azure para uma resposta sem servidor estoirar os eventos.

Se utilizar as Funções do Azure como o processador, utilize o acionador do Event Grid em vez dos acionadores HTTP genéricos. O Event Grid valida automaticamente os acionadores de função do Event Grid. Com os acionadores HTTP genéricos, tem de implementar a [resposta de validação](security-authentication.md#webhook-event-delivery).

|Título  |Descrição  |
|---------|---------|
| [Quickstart: Lidar com eventos com função](custom-event-to-function.md) | Envia um evento personalizado para uma função de processamento. |
| [Gatilho da Grelha de Eventos para Funções Azure](../azure-functions/functions-bindings-event-grid.md) | Visão geral da utilização do gatilho da grelha de eventos em funções. |
| [Tutorial: automatizar imagens carregadas usando a Grelha de Eventos](resize-images-on-storage-blob-upload-event.md) | Os utilizadores fazem o upload de imagens através da aplicação web para a conta de armazenamento. Quando uma bolha de armazenamento é criada, a Event Grid envia um evento para a aplicação de funções, que redimensiona a imagem carregada. |
| [Tutorial: transmitir big data em um armazém de dados](event-grid-event-hubs-integration.md) | Quando o Event Hubs cria um ficheiro Capture, a Rede de Eventos envia um evento para uma aplicação de função. A aplicação recupera o ficheiro Capture e migra dados para um armazém de dados. |
| [Tutorial: Ônibus de serviço azure para exemplos de integração da Rede de Eventos Azure](../service-bus-messaging/service-bus-to-event-grid-integration-example.md?toc=%2fazure%2fevent-grid%2ftoc.json) | A Rede de Eventos envia mensagens do tópico do Bus de Serviço para a aplicação de função e lógica. |

## <a name="event-hubs"></a>Hubs de Eventos

Use Os Centros de Eventos quando a sua solução obtém eventos mais rápido do que pode processar os eventos. A sua aplicação processa os eventos a partir de Event Hubs na sua própria agenda. Pode escalar o processamento do seu evento para lidar com os eventos que estão a chegar.

Os Hubs de Eventos podem funcionar como uma fonte de eventoou manipulador de eventos. O seguinte artigo mostra como usar os Centros de Eventos como manipulador.

|Título  |Descrição  |
|---------|---------|
| [Quickstart: encaminha eventos personalizados para Hubs de Eventos Azure com Azure CLI e Grid de Eventos](custom-event-to-eventhub.md) | Envia um evento personalizado para um centro de eventos para processamento por uma aplicação. |
| [Modelo de Gestor de Recursos: tópico personalizado e ponto final do Event Hubs](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-event-hubs-handler)| Um modelo de Gestor de Recursos que cria uma subscrição para um tópico personalizado. Envia eventos para um Azure Event Hubs. |

## <a name="hybrid-connections"></a>Ligações Híbridas

Utilize ligações híbridas Azure Relay para enviar eventos para aplicações que estejam dentro de uma rede empresarial e não tenham um ponto final acessível ao público.

|Título  |Descrição  |
|---------|---------|
| [Tutorial: enviar eventos para conexão híbrida](custom-event-to-hybrid-connection.md) | Envia um evento personalizado para uma conexão híbrida existente para processamento por uma aplicação de ouvintes. |

## <a name="logic-apps"></a>Aplicações Lógicas

Use aplicações lógicas para automatizar processos de negócio para responder a eventos.

|Título  |Descrição  |
|---------|---------|
| [Tutorial: monitorizar alterações de máquinas virtuais com grelha de eventos azure e aplicações lógicas](monitor-virtual-machine-changes-event-grid-logic-app.md) | Uma aplicação lógica monitoriza alterações a uma máquina virtual e envia e-mails sobre essas alterações. |
| [Tutorial: envie notificações por e-mail sobre eventos do Hub Azure IoT usando aplicações lógicas](publish-iot-hub-events-to-logic-apps.md) | Uma aplicação lógica envia um e-mail de notificação sempre que um dispositivo é adicionado ao seu hub IoT. |
| [Tutorial: Ônibus de serviço azure para exemplos de integração da Rede de Eventos Azure](../service-bus-messaging/service-bus-to-event-grid-integration-example.md?toc=%2fazure%2fevent-grid%2ftoc.json) | A Rede de Eventos envia mensagens do tópico do Bus de Serviço para a aplicação de função e lógica. |

## <a name="service-bus"></a>Service Bus

### <a name="service-bus-queues"></a>Filas do Service Bus

Pode encaminhar eventos em Event Grid diretamente para as filas de ônibus de serviço para utilização em cenários de tampão ou comando & de controlo em aplicações empresariais.

No portal Azure, ao criar uma subscrição de evento, selecione "Service Bus Queue" como tipo de ponto final e, em seguida, clique em "selecionar um ponto final" para escolher uma fila de ônibus de serviço.

#### <a name="using-cli-to-add-a-service-bus-queue-handler"></a>Usando o CLI para adicionar um manipulador de fila de ônibus de serviço

Para o Azure CLI, o seguinte exemplo subscreve e liga um tópico da grelha de eventos a uma fila de ônibus de serviço:

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

Você pode direcionar eventos em Event Grid diretamente para tópicos de ônibus de serviço de forma a lidar com eventos do sistema Azure com tópicos de ônibus de serviço, ou para comandar & controlar cenários de mensagens.

No portal Azure, ao criar uma subscrição de evento, selecione "Service Bus Topic" como tipo de ponto final e, em seguida, clique em "selecionar e ponto final" para escolher um tópico de Ônibus de serviço.

#### <a name="using-cli-to-add-a-service-bus-topic-handler"></a>Usando o CLI para adicionar um manipulador de tópicos de ônibus de serviço

Para o Azure CLI, o seguinte exemplo subscreve e liga um tópico da grelha de eventos a uma fila de ônibus de serviço:

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

Utilize o armazenamento da fila para receber eventos que precisam de ser puxados. Pode utilizar o armazenamento da fila quando tiver um processo de longa duração que demora muito tempo a responder. Ao enviar eventos para o armazenamento de fila, a aplicação pode puxar e processar eventos na sua própria agenda.

|Título  |Descrição  |
|---------|---------|
| [Quickstart: enverede eventos personalizados para armazenamento de fila Azure com Azure CLI e Grade de Eventos](custom-event-to-queue-storage.md) | Descreve como enviar eventos personalizados para um armazenamento de fila. |

## <a name="webhooks"></a>WebHooks

Utilize webhooks para pontos finais personalizáveis que respondam a eventos.

|Título  |Descrição  |
|---------|---------|
| Quickstart: criar e encaminhar eventos personalizados com - [Azure CLI,](custom-event-quickstart.md) [PowerShell](custom-event-quickstart-powershell.md), e [portal](custom-event-quickstart-portal.md). | Mostra como enviar eventos personalizados para um WebHook. |
| Quickstart: encaminhe os eventos de armazenamento blob para um ponto final web personalizado com - [Azure CLI,](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json) [PowerShell](../storage/blobs/storage-blob-event-quickstart-powershell.md?toc=%2fazure%2fevent-grid%2ftoc.json), e [portal](blob-event-quickstart-portal.md). | Mostra como enviar eventos de armazenamento de blob para um WebHook. |
| [Quickstart: envie eventos de registo de contentores](../container-registry/container-registry-event-grid-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Mostra como usar o Azure CLI para enviar eventos de registo de contentores. |
| [Visão geral: receba eventos para um ponto final http](receive-events.md) | Descreve como validar um ponto final http para receber eventos de uma Subscrição de Eventos, e receber e desserializar eventos. |

## <a name="next-steps"></a>Passos seguintes

* Para obter uma introdução ao Event Grid, veja [Sobre o Azure Event Grid](overview.md).
* Para começar rapidamente a usar a Grelha de Eventos, consulte [create e encaminhe eventos personalizados com a Grelha de Eventos Azure](custom-event-quickstart.md).
