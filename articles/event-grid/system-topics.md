---
title: Tópicos do sistema na Grelha de Eventos Azure
description: Descreve tópicos do sistema na Grelha de Eventos Azure.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 06/02/2020
ms.author: spelluru
ms.openlocfilehash: c34ada2c7437d8415b52c68fb66103ec3aa81d95
ms.sourcegitcommit: ba8df8424d73c8c4ac43602678dae4273af8b336
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/05/2020
ms.locfileid: "84456934"
---
# <a name="system-topics-in-azure-event-grid"></a>Tópicos do sistema na Grelha de Eventos Azure
Os tópicos do sistema são os tópicos criados para os serviços Azure, como o Azure Storage e o Azure Event Hubs. Pode criar tópicos de sistema utilizando o modelo Azure portal, PowerShell, CLI ou Azure Resource Manager.  

> [!NOTE]
> Esta funcionalidade não está atualmente ativada para a nuvem do Governo Azure. 

## <a name="create-system-topics"></a>Criar tópicos de sistema
Pode criar um tópico de sistema de duas formas: 

- Crie um tópico de sistema para um recurso Azure e, em seguida, crie uma subscrição de eventos para esse tópico do sistema.
- Crie uma subscrição de eventos num recurso Azure, que cria internamente um tópico de sistema para si.

Quando utiliza a primeira abordagem, o tópico do sistema não é automaticamente eliminado quando a última subscrição do evento para esse tópico do sistema é eliminada. Quando utiliza a segunda abordagem, o tópico do sistema é automaticamente eliminado quando a última subscrição do evento é eliminada. 

Para obter instruções detalhadas sobre a criação de tópicos do sistema utilizando o portal Azure, PowerShell ou CLI, consulte os seguintes artigos:

- [Criar, visualizar e gerir tópicos do sistema utilizando o portal Azure](create-view-manage-system-topics.md).
- [Criar, ver e gerir tópicos do sistema de grade de eventos utilizando o Azure CLI](create-view-manage-system-topics-cli.md)
- [Crie tópicos de sistema de grelha de eventos utilizando modelos de Gestor de Recursos Azure](create-view-manage-system-topics-arm.md)

## <a name="system-topic-name"></a>Nome tópico do sistema
O Event Grid não criou tópicos de sistema para fontes Azure (Azure Storage, Azure Event Hubs, etc.) que foram criados antes de Mar, 15, 2020. Se criou uma subscrição para um evento criado por fontes Azure entre 3/15/2020 e 6/2/2020, o serviço Desemiste criou automaticamente um tópico de sistema com um **nome gerado aleatoriamente.** Depois de 6/2/2020, pode especificar um nome para o tópico do sistema enquanto cria uma subscrição de eventos para uma fonte Azure. 

## <a name="location-and-resource-group"></a>Localização e grupo de recursos
Para fontes de eventos Azure que se encontram numa região/localização específica, o tópico do sistema é criado no mesmo local que a fonte do evento Azure. Por exemplo, se criar uma subscrição de eventos para um armazenamento de blob Azure no Leste dos EUA, o tópico do sistema é criado no Leste dos EUA. Para fontes globais de eventos Azure, como subscrições Azure, grupos de recursos ou Azure Maps, a Grade de Eventos cria o tópico do sistema na localização **global.** 

Em geral, o tópico do sistema é criado no mesmo grupo de recursos em que a fonte de eventos Azure está. Para subscrições de eventos criadas no âmbito de subscrição do Azure, o tópico do sistema é criado sob o grupo de recursos **Default-EventGrid**. Se o grupo de recursos não existir, a Azure Event Grid cria-o antes de criar o tópico do sistema. 

## <a name="next-steps"></a>Próximos passos
Consulte os seguintes artigos: 

- [Criar, visualizar e gerir tópicos do sistema utilizando o portal Azure](create-view-manage-system-topics.md).
- [Criar, ver e gerir tópicos do sistema de grade de eventos utilizando o Azure CLI](create-view-manage-system-topics-cli.md)
- [Crie tópicos de sistema de grelha de eventos utilizando modelos de Gestor de Recursos Azure](create-view-manage-system-topics-arm.md)
