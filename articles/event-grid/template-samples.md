---
title: Exemplos de modelos do Azure Resource Manager - Event Grid | Microsoft Docs
description: Este artigo fornece uma lista de amostras de modelos do Azure Resource Manager para Azure Event Grid no GitHub.
ms.topic: sample
ms.date: 07/07/2020
ms.openlocfilehash: 910012adf2dc930e6f1a26f1a7fc41f5ed0580c9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "86119060"
---
# <a name="azure-resource-manager-templates-for-event-grid"></a>Modelos do Azure Resource Manager para Event Grid

Para que a sintaxe JSON e as propriedades utilizem num modelo, consulte os [tipos de recursos Microsoft.EventGrid](/azure/templates/microsoft.eventgrid/allversions). A tabela seguinte inclui ligações para modelos do Azure Resource Manager para o Event Grid.

## <a name="event-grid-subscriptions"></a>Assinaturas de Grade de Eventos
- [Tópico personalizado e subscrição com o ponto final webHook](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid) - Implementa um tópico personalizado da Grade de Eventos. Cria uma subscrição para esse tópico personalizado que utiliza um ponto final do WebHook. 
- [Subscrição de tópico personalizado com o ponto final do EventHub](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-event-hubs-handler) - Cria uma subscrição de Grade de Eventos para um tópico personalizado. A subscrição utiliza um Hub de Eventos para o ponto final. 
- [Subscrição de azure ou subscrição do grupo de recursos](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-resource-events-to-webhook) - Subscreve eventos para um grupo de recursos ou subscrição Azure. O grupo de recursos que especificar como destino durante a implementação é a origem de eventos. A subscrição utiliza um WebHook para o ponto final. 
- [Conta de armazenamento blob e subscrição](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-subscription-and-storage) - Implementa uma conta de armazenamento Azure Blob e subscreve eventos para essa conta de armazenamento. 

## <a name="next-steps"></a>Passos seguintes
Consulte as seguintes amostras:

- [Exemplos do PowerShell](powershell-samples.md)
- [Amostras de CLI](cli-samples.md)
