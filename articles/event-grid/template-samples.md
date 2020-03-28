---
title: Exemplos de modelos do Azure Resource Manager - Event Grid | Microsoft Docs
description: Este artigo fornece uma lista de amostras de modelo de gestor de recursos Azure para a Grelha de Eventos Azure no GitHub.
services: event-grid
author: spelluru
manager: timlt
ms.service: event-grid
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: na
ms.date: 01/23/2020
ms.author: spelluru
ms.openlocfilehash: 38d8db0bcc504760595fe51b63072f63e785577a
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "76720627"
---
# <a name="azure-resource-manager-templates-for-event-grid"></a>Modelos do Azure Resource Manager para Event Grid

Para que a sintaxe e as propriedades da JSON utilizem num modelo, consulte os tipos de [recursos Microsoft.EventGrid](/azure/templates/microsoft.eventgrid/allversions). A tabela seguinte inclui ligações para modelos do Azure Resource Manager para o Event Grid.

| | |
|-|-|
|**Assinaturas da Grelha de Eventos**||
| [Tópico personalizado e subscrição com o ponto final do WebHook](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid)| Implementa um tópico personalizado do Event Grid. Cria uma subscrição para esse tópico personalizado que utiliza um ponto final do WebHook. |
| [Subscrição do tópico personalizado com ponto final do EventHub](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-event-hubs-handler)| Cria uma subscrição do Event Grid para um tópico personalizado. A subscrição utiliza um Hub de Eventos para o ponto final. |
| [Subscrição do Azure ou subscrição do grupo de recursos](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-resource-events-to-webhook)| Subscreve eventos de um grupo de recursos ou subscrição do Azure. O grupo de recursos que especificar como destino durante a implementação é a origem de eventos. A subscrição utiliza um WebHook para o ponto final. |
| [Conta de armazenamento de blobs e subscrição](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-subscription-and-storage)| Implementa uma conta de armazenamento de Blobs do Azure e subscreve eventos para essa conta de armazenamento. |
| | |
