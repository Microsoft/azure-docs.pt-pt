---
title: Tópicos do sistema na Grelha de Eventos Azure
description: Descreve tópicos do sistema na Grelha de Eventos Azure.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 06/02/2020
ms.author: spelluru
ms.openlocfilehash: 67746ebd8a16eb02b8f02d238b0e3c0125989189
ms.sourcegitcommit: 69156ae3c1e22cc570dda7f7234145c8226cc162
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/03/2020
ms.locfileid: "84308273"
---
# <a name="system-topics-in-azure-event-grid"></a>Tópicos do sistema na Grelha de Eventos Azure
O serviço Azure Event Grid cria tópicos de sistema quando cria uma primeira subscrição de eventos para uma fonte de eventos Azure. Atualmente, a Event Grid não cria tópicos de sistema para fontes de tópicos que foram criados antes do Mar, 15 de 2020. Para todas as fontes de tópicos que criou nesta data ou depois desta data, o Event Grid cria automaticamente tópicos do sistema. Este artigo descreve **tópicos do sistema** na Azure Event Grid.

> [!NOTE]
> Esta funcionalidade não está atualmente ativada para a nuvem do Governo Azure. 

## <a name="overview"></a>Descrição geral
Quando cria a subscrição do primeiro evento para uma fonte de eventoS Azure, como a conta Azure Storage, o processo de provisionamento para a subscrição cria um recurso adicional do tipo **Microsoft.EventGrid/systemTopics**. Quando a última subscrição do evento à fonte do evento Azure for eliminada, o tópico do sistema é automaticamente eliminado.

O tópico do sistema não é aplicável a cenários de tópicos personalizados, ou seja, tópicos de Grade de Eventos e domínios de Grade de Eventos. 

## <a name="name"></a>Name 
Anteriormente, quando criou uma subscrição para um evento criado por fontes Azure, o serviço Desematado criou automaticamente um tópico de sistema com um **nome gerado aleatoriamente.** Agora, pode especificar um nome para o tópico do sistema enquanto cria o tópico no portal Azure. Pode utilizar este recurso tópico do sistema para descobrir métricas e registos de diagnóstico.

## <a name="location"></a>Localização
Para fontes de eventos Azure que se encontram numa região/localização específica, o tópico do sistema é criado no mesmo local que a fonte do evento Azure. Por exemplo, se criar uma subscrição de eventos para um armazenamento de blob Azure no Leste dos EUA, o tópico do sistema é criado no Leste dos EUA. Para fontes globais de eventos Azure, como subscrições Azure, grupos de recursos ou Azure Maps, a Grade de Eventos cria o tópico do sistema na localização **global.** 

## <a name="resource-group"></a>Grupo de recursos 
Em geral, o tópico do sistema é criado no mesmo grupo de recursos em que a fonte de eventos Azure está. Para subscrições de eventos criadas no âmbito de subscrição do Azure, o tópico do sistema é criado sob o grupo de recursos **Default-EventGrid**. Se o grupo de recursos não existir, a Azure Event Grid cria-o antes de criar o tópico do sistema. 

Quando tentar eliminar o grupo de recursos com a conta de armazenamento, verá o tópico do sistema na lista de recursos afetados.  

![Eliminar grupo de recursos](./media/system-topics/delete-resource-group.png)


## <a name="next-steps"></a>Passos seguintes
Consulte o seguinte artigo: [Criar, ver e gerir tópicos do sistema](create-view-manage-system-topics.md).