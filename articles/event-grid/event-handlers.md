---
title: Manipuladores de eventos do Azure Event Grid
description: Descreve os manipuladores de eventos suportados do Azure Event Grid
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 01/21/2019
ms.author: spelluru
ms.openlocfilehash: 915d1284d66438219fc9aba893512e5f6a5b02b3
ms.sourcegitcommit: 009334a842d08b1c83ee183b5830092e067f4374
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/29/2019
ms.locfileid: "66305036"
---
# <a name="event-handlers-in-azure-event-grid"></a>Manipuladores de eventos no Azure Event Grid

Um manipulador de eventos é o local em que o evento é enviado. O manipulador demora alguns mais nenhuma ação ao processar o evento. Vários serviços do Azure são automaticamente configurados para manipular eventos. Também pode utilizar qualquer WebHook para manipular eventos. O WebHook não precisa ser alojado no Azure para manipular eventos. Event Grid suporta apenas pontos finais do WebHook de HTTPS.

Este artigo fornece ligações para conteúdo para cada manipulador de eventos.

## <a name="azure-automation"></a>Automatização do Azure

Utilize a automatização do Azure para processar eventos com runbooks automatizados.

|Cargo  |Descrição  |
|---------|---------|
|[Tutorial: Automatização do Azure com o Event Grid e o Microsoft Teams](ensure-tags-exists-on-new-virtual-machines.md) |Crie uma máquina virtual, que envia um evento. O evento é acionado um runbook da automatização que a máquina virtual de etiquetas e aciona uma mensagem que é enviada para um canal do Microsoft Teams. |

## <a name="azure-functions"></a>Funções do Azure

Utilize as funções do Azure para resposta sem servidor a eventos.

Se utilizar as Funções do Azure como o processador, utilize o acionador do Event Grid em vez dos acionadores HTTP genéricos. O Event Grid valida automaticamente os acionadores de função do Event Grid. Com os acionadores HTTP genéricos, tem de implementar a [resposta de validação](security-authentication.md#webhook-event-delivery).

|Cargo  |Descrição  |
|---------|---------|
| [Acionador do Event Grid para as funções do Azure](../azure-functions/functions-bindings-event-grid.md) | Descrição geral da utilização do acionador do Event Grid nas funções de mensagens em fila. |
| [Tutorial: automatizar o redimensionamento de imagens carregadas com o Event Grid](resize-images-on-storage-blob-upload-event.md) | Os utilizadores carregar imagens através da aplicação web para a conta de armazenamento. Quando um blob de armazenamento é criado, o Event Grid envia um evento para a aplicação de função, que redimensiona a imagem carregada. |
| [Tutorial: transmitir grandes quantidades de dados para um armazém de dados](event-grid-event-hubs-integration.md) | Quando os Hubs de eventos cria um ficheiro de captura, o Event Grid envia um evento para uma aplicação de funções. A aplicação obtém o arquivo de captura e migra os dados para um armazém de dados. |
| [Tutorial: O Azure Service Bus para exemplos de integração do Azure Event Grid](../service-bus-messaging/service-bus-to-event-grid-integration-example.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Grelha de eventos envia mensagens de tópico do Service Bus para a função de aplicação e a aplicação lógica. |

## <a name="event-hubs"></a>Hubs de Eventos

Utilize os Hubs de eventos quando a sua solução obtém os eventos mais rapidamente do que consegue processar os eventos. Seu aplicativo processa os eventos dos Hubs de eventos para ele próprio agendamento. Pode dimensionar o processamento para manipular os eventos de entrada de eventos.

Os Hubs de eventos podem agir como uma origem de evento ou de manipulador de eventos. O artigo seguinte mostra como utilizar os Hubs de eventos como um manipulador.

|Cargo  |Descrição  |
|---------|---------|
| [Início rápido: encaminhar eventos personalizados para os Hubs de eventos do Azure com a CLI do Azure e o Event Grid](custom-event-to-eventhub.md) | Envia um evento personalizado para um hub de eventos para processamento por um aplicativo. |
| [Modelo do Resource Manager: um tópico personalizado e um ponto final de Hubs de eventos](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-event-hubs-handler)| Um modelo do Resource Manager que cria uma subscrição para um tópico personalizado. Envia eventos para um Event Hubs do Azure. |

Para obter exemplos de Hubs de eventos como uma origem, consulte [origem dos Hubs de eventos](event-sources.md#event-hubs).

## <a name="hybrid-connections"></a>Ligações Híbridas

Utilize ligações híbridas do reencaminhamento do Azure para enviar eventos para aplicativos que estejam numa rede empresarial e não tem um ponto final acessível publicamente.

|Cargo  |Descrição  |
|---------|---------|
| [Tutorial: enviar eventos para a ligação híbrida](custom-event-to-hybrid-connection.md) | Envia um evento personalizado para uma ligação híbrida existente para processamento por um aplicativo de serviço de escuta. |

## <a name="logic-apps"></a>Aplicações Lógicas

Utilize o Logic Apps para automatizar processos de negócios para responder a eventos.

|Cargo  |Descrição  |
|---------|---------|
| [Tutorial: monitorizar as alterações de máquina virtual com o Azure Event Grid e Logic Apps](monitor-virtual-machine-changes-event-grid-logic-app.md) | Uma aplicação lógica monitoriza alterações a uma máquina virtual e envia e-mails sobre essas alterações. |
| [Tutorial: enviar notificações por e-mail sobre eventos do IoT Hub do Azure com o Logic Apps](publish-iot-hub-events-to-logic-apps.md) | Uma aplicação lógica envia um e-mail de notificação sempre que um dispositivo é adicionado ao seu hub IoT. |
| [Tutorial: O Azure Service Bus para exemplos de integração do Azure Event Grid](../service-bus-messaging/service-bus-to-event-grid-integration-example.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Grelha de eventos envia mensagens de tópico do Service Bus para a função de aplicação e a aplicação lógica. |

## <a name="service-bus-queue-preview"></a>Fila do Service Bus (pré-visualização)

Utilize o Service Bus como um manipulador de eventos para encaminhar os eventos no Event Grid diretamente para filas do Service Bus para utilização em cenários de colocação em memória intermédia ou comando e controlo nas aplicações empresariais. A pré-visualização não funciona com tópicos do Service Bus e sessões, mas funciona com todos os escalões de filas do Service Bus.

Tenha em conta, ao Service Bus como um manipulador está em pré-visualização pública, tem de instalar a extensão CLI ou o PowerShell quando a utilização dos mesmos para criar subscrições de eventos.

### <a name="using-cli"></a>Com a CLI

Para a CLI do Azure, o exemplo seguinte subscreve uma liga-se e o tópico do Event Grid para uma fila do Service Bus:

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

## <a name="queue-storage"></a>Armazenamento de filas

Utilize o armazenamento de filas para receber eventos que têm de ser obtidas. Poderá utilizar o armazenamento de filas quando tiver um processo de execução longa que demora muito tempo a responder. Ao enviar eventos para o armazenamento de filas, a aplicação pode extrair e processar eventos em sua própria agenda.

|Cargo  |Descrição  |
|---------|---------|
| [Início rápido: encaminhar eventos personalizados para o armazenamento de filas do Azure com a CLI do Azure e o Event Grid](custom-event-to-queue-storage.md) | Descreve como enviar eventos personalizados para um armazenamento de filas. |

## <a name="webhooks"></a>WebHooks

Utilize webhooks para pontos finais personalizáveis que respondem a eventos.

|Cargo  |Descrição  |
|---------|---------|
| Início rápido: criar e encaminhar eventos personalizados com - [CLI do Azure](custom-event-quickstart.md), [PowerShell](custom-event-quickstart-powershell.md), e [portal](custom-event-quickstart-portal.md). | Mostra como enviar eventos personalizados para um WebHook. |
| Início rápido: encaminhar eventos de armazenamento de BLOBs para um ponto final web personalizado com - [CLI do Azure](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json), [PowerShell](../storage/blobs/storage-blob-event-quickstart-powershell.md?toc=%2fazure%2fevent-grid%2ftoc.json), e [portal](blob-event-quickstart-portal.md). | Mostra como enviar eventos de armazenamento de BLOBs para um WebHook. |
| [Início rápido: enviar eventos de registo de contentor](../container-registry/container-registry-event-grid-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Mostra como utilizar a CLI do Azure para enviar eventos de registo de contentor. |
| [Descrição geral: receber eventos para um ponto de final HTTP](receive-events.md) | Descreve como validar um ponto de final HTTP para receber eventos a partir de uma subscrição de evento e receber e anular a serialização de eventos. |

## <a name="next-steps"></a>Passos Seguintes

* Para obter uma introdução ao Event Grid, veja [Sobre o Azure Event Grid](overview.md).
* Para começar rapidamente com o Event Grid, veja [criar e encaminhar eventos personalizados com o Azure Event Grid](custom-event-quickstart.md).
