---
title: Tópicos do sistema na Grelha de Eventos Azure
description: Descreve tópicos do sistema na Grelha de Eventos Azure.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 03/16/2020
ms.author: spelluru
ms.openlocfilehash: 35025bf1592f1293b9326d643f76322b4af590c8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79501203"
---
# <a name="system-topics-in-azure-event-grid"></a>Tópicos do sistema na Grelha de Eventos Azure
O serviço Azure Event Grid cria tópicos do sistema quando cria uma subscrição de primeiro evento para uma fonte de evento seletiva. Atualmente, a Event Grid não cria tópicos de sistema para fontes de tópicos que foram criados antes de Mar, 15, 2020. Para todas as fontes de tópico que criou nesta ou após esta data, a Rede de Eventos cria automaticamente tópicos do sistema. Este artigo descreve tópicos do sistema na Grelha de **Eventos** Azure.

> [!NOTE]
> Esta funcionalidade não está atualmente ativada para a nuvem do Governo Azure. 

## <a name="overview"></a>Descrição geral
Quando cria a subscrição do primeiro evento para uma fonte de evento seleção do Azure, como a conta De armazenamento do Azure, o processo de provisionamento para a subscrição cria um recurso adicional do tipo **Microsoft.EventGrid/systemTopics**. Quando a última subscrição do evento à fonte do evento Azure é eliminada, o tópico do sistema é automaticamente eliminado.

O tópico do sistema não é aplicável a cenários tópicos personalizados, isto é, tópicos de Grelha de Eventos e domínios da Grelha de Eventos. 

## <a name="location"></a>Localização
Para fontes de eventos Azure que se encontram numa região/localização específica, o tópico do sistema é criado no mesmo local que a fonte do evento Azure. Por exemplo, se criar uma subscrição de evento para um armazenamento de blob Azure no Leste dos EUA, o tópico do sistema é criado nos EUA Orientais. Para fontes globais de eventos Azure, como subscrições azure, grupos de recursos ou Mapas Azure, a Event Grid cria o tópico do sistema na localização **global.** 

## <a name="resource-group"></a>Grupo de recursos 
Em geral, o tema do sistema é criado no mesmo grupo de recursos em que se encontra a fonte do evento Azure. Para subscrições de eventos criadas no âmbito de subscrição do Azure, o tópico do sistema é criado sob o grupo de recursos **Default-EventGrid**. Se o grupo de recursos não existir, a Azure Event Grid cria-o antes de criar o tópico do sistema. 

Quando tentar eliminar o grupo de recursos com a conta de armazenamento, verá o tópico do sistema na lista de recursos afetados.  

![Eliminar grupo de recursos](./media/system-topics/delete-resource-group.png)

## <a name="next-steps"></a>Passos seguintes
Consulte os seguintes artigos: 

- [Tópicos personalizados](event-sources.md#custom-topics)
- [Domínios](event-domains.md)