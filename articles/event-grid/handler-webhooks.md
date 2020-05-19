---
title: Webhooks como manipuladores de eventos para eventos da Grelha de Eventos Azure
description: Descreve como pode usar webhooks como manipuladores de eventos para eventos da Rede de Eventos Azure. Os livros de execução da Azure Automation e as aplicações lógicas são suportadas como manipuladores de eventos através de webhooks.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 05/11/2020
ms.author: spelluru
ms.openlocfilehash: ba67b1cd93bc1c713648f799090e0b2cd77cff1b
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83598620"
---
# <a name="webhooks-automation-runbooks-logic-apps-as-event-handlers-for-azure-event-grid-events"></a>Webhooks, Livros de Automação, Aplicações Lógicas como manipuladores de eventos para eventos da Rede de Eventos Azure
Um manipulador de eventos é o local onde o evento é enviado. O manipulador toma mais medidas para processar o evento. Vários serviços Azure são configurados automaticamente para lidar com eventos. Também pode utilizar qualquer WebHook para lidar com eventos. O WebHook não precisa de ser hospedado em Azure para lidar com eventos. A Grelha de Eventos suporta apenas pontos finais HTTPS Webhook.

> [!NOTE]
> Os livros de execução da Azure Automation e as aplicações lógicas são suportadas como manipuladores de eventos através de webhooks. 

## <a name="webhooks"></a>Webhooks
Consulte os seguintes artigos para uma visão geral e exemplos de utilização de webhooks como manipuladores de eventos. 

|Título  |Descrição  |
|---------|---------|
| Quickstart: criar e encaminhar eventos personalizados com - [Azure CLI,](custom-event-quickstart.md) [PowerShell](custom-event-quickstart-powershell.md), e [portal](custom-event-quickstart-portal.md). | Mostra como enviar eventos personalizados para um WebHook. |
| Quickstart: encaminhe os eventos de armazenamento blob para um ponto final web personalizado com - [Azure CLI,](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json) [PowerShell](../storage/blobs/storage-blob-event-quickstart-powershell.md?toc=%2fazure%2fevent-grid%2ftoc.json), e [portal](blob-event-quickstart-portal.md). | Mostra como enviar eventos de armazenamento de blob para um WebHook. |
| [Quickstart: envie eventos de registo de contentores](../container-registry/container-registry-event-grid-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Mostra como usar o Azure CLI para enviar eventos de registo de contentores. |
| [Visão geral: receba eventos para um ponto final http](receive-events.md) | Descreve como validar um ponto final http para receber eventos de uma Subscrição de Eventos, e receber e desserializar eventos. |


## <a name="azure-automation"></a>Automatização do Azure
Pode processar eventos utilizando livros de execução Da Automatização Azure. O processamento de eventos utilizando livros automáticos é suportado através de webhooks. Cria um webhook para o livro de execução e depois utiliza-se o manipulador de webhook. Consulte o seguinte tutorial, por exemplo: 

|Título  |Descrição  |
|---------|---------|
|[Tutorial: Automação Azure com Grelha de Eventos e Equipas da Microsoft](ensure-tags-exists-on-new-virtual-machines.md) |Crie uma máquina virtual, que envie um evento. O evento despoleta um livro de automação que marca a máquina virtual e despoleta uma mensagem que é enviada para um canal da Microsoft Teams. |


## <a name="logic-apps"></a>Logic Apps
Use **aplicações lógicas** para implementar processos de negócio para processar eventos da Grelha de Eventos. Não se cria um webhook explicitamente neste cenário. O webhook é criado automaticamente para si quando configura a aplicação lógica para lidar com eventos a partir da Rede de Eventos. Consulte os seguintes tutoriais, por exemplo: 

|Título  |Descrição  |
|---------|---------|
| [Tutorial: Monitorize alterações de máquinas virtuais com grelha de eventos azure e aplicações lógicas](monitor-virtual-machine-changes-event-grid-logic-app.md) | Uma aplicação lógica monitoriza alterações a uma máquina virtual e envia e-mails sobre essas alterações. |
| [Tutorial: Envie notificações por e-mail sobre eventos do Hub Azure IoT usando aplicações lógicas](publish-iot-hub-events-to-logic-apps.md) | Uma aplicação lógica envia um e-mail de notificação sempre que um dispositivo é adicionado ao seu hub IoT. |
| [Tutorial: Responda aos eventos de ônibus de serviço azure recebidos via Azure Event Grid usando funções Azure e aplicações lógicas azure](../service-bus-messaging/service-bus-to-event-grid-integration-example.md?toc=%2fazure%2fevent-grid%2ftoc.json) | A Rede de Eventos envia mensagens do tópico do Bus de Serviço para a aplicação de função e lógica. |

## <a name="next-steps"></a>Passos seguintes
Consulte o artigo de manipuladores de [eventos](event-handlers.md) para obter uma lista de manipuladores de eventos suportados. 
