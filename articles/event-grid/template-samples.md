---
title: Exemplos de modelos do Azure Resource Manager - Event Grid | Microsoft Docs
description: Exemplos de modelos do Azure Resource Manager para Event Grid
services: event-grid
author: tfitzmac
manager: timlt
ms.service: event-grid
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: na
ms.date: 01/04/2019
ms.author: tomfitz
ms.openlocfilehash: 788d23c7bddd90c1e12a118742c651eb9759529c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60822713"
---
# <a name="azure-resource-manager-templates-for-event-grid"></a>Modelos do Azure Resource Manager para Event Grid

Para a sintaxe JSON e propriedades para utilizar num modelo, consulte [tipos de recursos de Microsoft.EventGrid](/azure/templates/microsoft.eventgrid/allversions). A tabela seguinte inclui ligações para modelos do Azure Resource Manager para o Event Grid.

| | |
|-|-|
|**Subscrições do Event Grid**||
| [Tópico personalizado e subscrição com o ponto final do WebHook](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid)| Implementa um tópico personalizado do Event Grid. Cria uma subscrição para esse tópico personalizado que utiliza um ponto final do WebHook. |
| [Subscrição do tópico personalizado com ponto final do EventHub](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-event-hubs-handler)| Cria uma subscrição do Event Grid para um tópico personalizado. A subscrição utiliza um Hub de Eventos para o ponto final. |
| [Subscrição do Azure ou subscrição do grupo de recursos](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-resource-events-to-webhook)| Subscreve eventos de um grupo de recursos ou subscrição do Azure. O grupo de recursos que especificar como destino durante a implementação é a origem de eventos. A subscrição utiliza um WebHook para o ponto final. |
| [Conta de armazenamento de blobs e subscrição](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-subscription-and-storage)| Implementa uma conta de armazenamento de Blobs do Azure e subscreve eventos para essa conta de armazenamento. |
| | |
