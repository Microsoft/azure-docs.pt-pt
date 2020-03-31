---
title: Crie o espaço de nome do ônibus de serviço Azure e a fila usando o modelo Azure
description: 'Quickstart: Crie um espaço de nome de ônibus de serviço e uma fila usando o modelo de Gestor de Recursos Azure'
services: service-bus-messaging
documentationcenter: .net
author: spelluru
manager: timlt
editor: ''
ms.assetid: a6bfb5fd-7b98-4588-8aa1-9d5f91b599b6
ms.service: service-bus-messaging
ms.devlang: tbd
ms.topic: quickstart
ms.custom: subject-armqs
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 12/20/2019
ms.author: spelluru
ms.openlocfilehash: 8605dae05b7f5270513b645367248090006c04a8
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2020
ms.locfileid: "80384913"
---
# <a name="quickstart-create-a-service-bus-namespace-and-a-queue-using-an-azure-resource-manager-template"></a>Quickstart: Crie um espaço de nome de ônibus de serviço e uma fila usando um modelo de Gestor de Recursos Azure

Este artigo mostra como usar um modelo de Gestor de Recursos Azure que cria um espaço de nome de ônibus de serviço e uma fila dentro desse espaço de nome. O artigo explica como especificar quais os recursos que são implantados e como definir parâmetros que são especificados quando a implementação é executada. Pode utilizar este modelo para as suas próprias implementações ou personalizá-lo para satisfazer as suas necessidades.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Se não tiver uma subscrição Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Nenhuma

## <a name="create-a-service-bus-namespace-and-a-queue"></a>Crie um espaço de nome de ônibus de serviço e uma fila

### <a name="review-the-template"></a>Reveja o modelo

O modelo utilizado neste quickstart é de [modelos Azure Quickstart](https://azure.microsoft.com/resources/templates/201-servicebus-create-queue).

:::code language="json" source="~/quickstart-templates/201-servicebus-create-queue/azuredeploy.json" range="1-75" highlight="31-63":::

Os recursos definidos no modelo incluem:

- [**Microsoft.ServiceBus/espaços de nome**](/azure/templates/microsoft.servicebus/namespaces)
- [**Microsoft.ServiceBus/espaços de nome/filas**](/azure/templates/microsoft.servicebus/namespaces/queues)

> [!NOTE]
> Os seguintes modelos do Gestor de Recursos Azure estão disponíveis para download e implementação.
>
> * [Criar um espaço de nome de ônibus de serviço com a regra de fila e autorização](service-bus-resource-manager-namespace-auth-rule.md)
> * [Crie um espaço de nome de ônibus de serviço com tópico e subscrição](service-bus-resource-manager-namespace-topic.md)
> * [Criar um espaço de nomes do Service Bus](service-bus-resource-manager-namespace.md)
> * [Criar um espaço de nome de ônibus de serviço com tópico, subscrição e regra](service-bus-resource-manager-namespace-topic-with-rule.md)

Você pode encontrar mais modelo a partir de [modelos Azure Quickstart](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Servicebus&pageNumber=1&sort=Popular)

### <a name="deploy-the-template"></a>Implementar o modelo

Com este modelo, você implementa um espaço de nome de ônibus de serviço com uma fila.

[As filas de ônibus](service-bus-queues-topics-subscriptions.md#queues) de serviço oferecem a entrega de mensagens First In, First out (FIFO) a um ou mais consumidores concorrentes.

Para executar automaticamente a implementação, clique no seguinte botão:

[![Desdobre para Azure](./media/service-bus-resource-manager-namespace-queue/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-servicebus-create-queue%2Fazuredeploy.json)

## <a name="next-steps"></a>Passos seguintes

Consulte o seguinte tópico que mostra como criar uma regra de autorização para o espaço de nome/fila:

[Crie uma regra de autorização de ônibus de serviço para espaço de nome e fila usando um modelo de Gestor de Recursos Azure](service-bus-resource-manager-namespace-auth-rule.md)

Aprenda a gerir estes recursos visualizando estes artigos:

* [Gerir o Barramento de Serviço com o PowerShell](service-bus-manage-with-ps.md)
* [Gerir os recursos de ônibus de serviço com o Explorador de Ônibus de Serviço](https://github.com/paolosalvatori/ServiceBusExplorer/releases)

[Authoring Azure Resource Manager templates]: ../azure-resource-manager/templates/template-syntax.md
[Service Bus namespace and queue template]: https://github.com/Azure/azure-quickstart-templates/blob/master/201-servicebus-create-queue/
[Azure Quickstart Templates]: https://azure.microsoft.com/documentation/templates/?term=service+bus
[Learn more about Service Bus queues]: service-bus-queues-topics-subscriptions.md
[Using Azure PowerShell with Azure Resource Manager]: ../azure-resource-manager/powershell-azure-resource-manager.md
[Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../azure-resource-manager/xplat-cli-azure-resource-manager.md
