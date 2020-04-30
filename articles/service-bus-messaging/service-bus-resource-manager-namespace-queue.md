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
ms.date: 03/30/2020
ms.author: spelluru
ms.openlocfilehash: b08253104eeb61f6bb09fde507473d235a996494
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "80422639"
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

Para executar a implementação automaticamente, clique no seguinte botão: Crie um novo grupo de recursos para a implementação para que possa facilmente limpar mais tarde.

[![Implementar no Azure](./media/service-bus-resource-manager-namespace-queue/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-servicebus-create-queue%2Fazuredeploy.json)

## <a name="verify-the-deployment"></a>Verificar a implementação

1. Selecione **Notificações** na parte superior para ver o estado da implementação. Espere até que o destacamento tenha sucesso. Em seguida, selecione **Ir para o grupo de recursos** na mensagem de notificação para navegar para a página para o grupo de recursos que contém o espaço de nome do Ônibus de serviço. 

    ![Notificação da implantação](./media/service-bus-resource-manager-namespace-queue/notification.png)
2. Confirme que vê o seu espaço de nome service Bus na lista de recursos. 

    ![Grupo de recursos - espaço de nome](./media/service-bus-resource-manager-namespace-queue/resource-group-namespace.png)
3. Selecione o espaço de nome da lista para ver a página **Service Bus Namespace.** 

## <a name="cleanup-resources"></a>Recursos de limpeza

1. No portal Azure, navegue para a página do **grupo Recursos** para o seu grupo de recursos.
2. Selecione **Eliminar grupo de recursos** na barra de ferramentas. 
3. Digite o nome do grupo de recursos e selecione **Eliminar**. 

    ![Grupo de recursos - excluir](./media/service-bus-resource-manager-namespace-queue/resource-group-delete.png)

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
