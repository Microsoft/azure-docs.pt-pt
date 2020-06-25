---
title: Crie o espaço de nome do ônibus de serviço Azure e a fila usando o modelo Azure
description: 'Quickstart: Crie um espaço de nome de ônibus de serviço e uma fila usando o modelo de Gestor de Recursos Azure'
documentationcenter: .net
author: spelluru
ms.topic: quickstart
ms.custom: subject-armqs
ms.tgt_pltfrm: dotnet
ms.date: 06/23/2020
ms.author: spelluru
ms.openlocfilehash: e382d39e10e1907c9892f03a3da64945f3049fee
ms.sourcegitcommit: 61d92af1d24510c0cc80afb1aebdc46180997c69
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/24/2020
ms.locfileid: "85337022"
---
# <a name="quickstart-create-a-service-bus-namespace-and-a-queue-using-an-azure-resource-manager-template"></a>Quickstart: Crie um espaço de nome de ônibus de serviço e uma fila usando um modelo de gestor de recursos Azure

Este artigo mostra como usar um modelo de Gestor de Recursos Azure que cria um espaço de nome de Service Bus e uma fila dentro desse espaço de nome. O artigo explica como especificar quais os recursos que são implantados e como definir parâmetros especificados quando a implantação é executada. Pode utilizar este modelo para as suas próprias implementações ou personalizá-lo para satisfazer as suas necessidades.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Nenhuma

## <a name="create-a-service-bus-namespace-and-a-queue"></a>Crie um espaço de nomes de ônibus de serviço e uma fila

### <a name="review-the-template"></a>Rever o modelo

O modelo utilizado neste início rápido pertence aos [modelos de Início Rápido do Azure](https://azure.microsoft.com/resources/templates/201-servicebus-create-queue).

:::code language="json" source="~/quickstart-templates/201-servicebus-create-queue/azuredeploy.json" range="1-75" highlight="31-63":::

Os recursos definidos no modelo incluem:

- [**Microsoft.ServiceBus/namespaces**](/azure/templates/microsoft.servicebus/namespaces)
- [**Microsoft.ServiceBus/namespaces/filas**](/azure/templates/microsoft.servicebus/namespaces/queues)

> [!NOTE]
> Os seguintes modelos do Gestor de Recursos Azure estão disponíveis para download e implementação.
>
> * [Criar um espaço de nome de ônibus de serviço com regra de fila e autorização](service-bus-resource-manager-namespace-auth-rule.md)
> * [Criar um espaço de nomes de ônibus de serviço com tópico e subscrição](service-bus-resource-manager-namespace-topic.md)
> * [Criar um espaço de nomes do Service Bus](service-bus-resource-manager-namespace.md)
> * [Criar um espaço de nomes de Service Bus com tópico, subscrição e regra](service-bus-resource-manager-namespace-topic-with-rule.md)

Você pode encontrar mais modelo de [modelos Azure Quickstart](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Servicebus&pageNumber=1&sort=Popular)

### <a name="deploy-the-template"></a>Implementar o modelo

Com este modelo, você implementa um espaço de nome de Service Bus com uma fila.

[As filas de ônibus](service-bus-queues-topics-subscriptions.md#queues) de serviço oferecem entrega de mensagens First In, First out (FIFO) a um ou mais consumidores concorrentes.

Para executar a implementação automaticamente, clique no seguinte botão: Crie um novo grupo de recursos para a implementação para que possa ser facilmente limpada mais tarde.

[![Implementar no Azure](./media/service-bus-resource-manager-namespace-queue/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-servicebus-create-queue%2Fazuredeploy.json)

## <a name="verify-the-deployment"></a>Verificar a implementação

1. Selecione **Notificações** no topo para ver o estado da implementação. Espere até que o destacamento tenha sucesso. Em seguida, selecione **Ir ao grupo de recursos** na mensagem de notificação para navegar na página para o grupo de recursos que contém o espaço de nomes do Service Bus. 

    ![Notificação da implantação](./media/service-bus-resource-manager-namespace-queue/notification.png)
2. Confirme que vê o seu espaço de nome de Service Bus na lista de recursos. 

    ![Grupo de recursos - espaço de nome](./media/service-bus-resource-manager-namespace-queue/resource-group-namespace.png)
3. Selecione o espaço de nomes da lista para ver a página **Service Bus Namespace.** 

## <a name="cleanup-resources"></a>Recursos de limpeza

1. No portal Azure, navegue na página do **grupo De recursos** para o seu grupo de recursos.
2. Selecione **Eliminar grupo de recursos**, na barra de ferramentas. 
3. Digite o nome do grupo de recursos e selecione **Delete**. 

    ![Grupo de recursos - excluir](./media/service-bus-resource-manager-namespace-queue/resource-group-delete.png)

## <a name="next-steps"></a>Passos seguintes

Veja o seguinte tópico que mostra como criar uma regra de autorização para o espaço/fila de nomes:

[Crie uma regra de autorização de ônibus de serviço para espaço de nome e fila usando um modelo de Gestor de Recursos Azure](service-bus-resource-manager-namespace-auth-rule.md)

Saiba como gerir estes recursos visualizando estes artigos:

* [Gerir o Barramento de Serviço com o PowerShell](service-bus-manage-with-ps.md)
* [Gerir recursos de ônibus de serviço com o Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/releases)

[Authoring Azure Resource Manager templates]: ../azure-resource-manager/templates/template-syntax.md
[Service Bus namespace and queue template]: https://github.com/Azure/azure-quickstart-templates/blob/master/201-servicebus-create-queue/
[Azure Quickstart Templates]: https://azure.microsoft.com/documentation/templates/?term=service+bus
[Learn more about Service Bus queues]: service-bus-queues-topics-subscriptions.md
[Using Azure PowerShell with Azure Resource Manager]: ../azure-resource-manager/powershell-azure-resource-manager.md
[Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../azure-resource-manager/xplat-cli-azure-resource-manager.md
