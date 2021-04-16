---
title: Crie o espaço de nome do ônibus de serviço Azure e a fila usando o modelo Azure
description: 'Quickstart: Crie um espaço de nome de ônibus de serviço e uma fila usando o modelo de Gestor de Recursos Azure'
documentationcenter: .net
author: spelluru
ms.author: spelluru
ms.date: 06/23/2020
ms.topic: quickstart
ms.tgt_pltfrm: dotnet
ms.custom:
- subject-armqs
- mode-arm
ms.openlocfilehash: 6577abf1900a166087ff63c331d7c0390fab1342
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107535030"
---
# <a name="quickstart-create-a-service-bus-namespace-and-a-queue-using-an-arm-template"></a>Quickstart: Crie um espaço de nome de ônibus de serviço e uma fila usando um modelo ARM

Este artigo mostra como usar um modelo de Gestor de Recursos Azure (modelo ARM) que cria um espaço de nome de Service Bus e uma fila dentro desse espaço de nome. O artigo explica como especificar quais os recursos que são implantados e como definir parâmetros especificados quando a implantação é executada. Pode utilizar este modelo para as suas próprias implementações ou personalizá-lo para satisfazer as suas necessidades.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Se o seu ambiente cumpre os pré-requisitos e se está familiarizado com a utilização de modelos ARM, selecione o botão **Implementar no Azure**. O modelo será aberto no portal do Azure.

[![Implementar no Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-servicebus-create-queue%2Fazuredeploy.json)

## <a name="prerequisites"></a>Pré-requisitos

Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="review-the-template"></a>Rever o modelo

O modelo utilizado neste início rápido pertence aos [Modelos de Início Rápido do Azure](https://azure.microsoft.com/resources/templates/201-servicebus-create-queue).

:::code language="json" source="~/quickstart-templates/201-servicebus-create-queue/azuredeploy.json":::

Os recursos definidos no modelo incluem:

- [**Microsoft.ServiceBus/namespaces**](/azure/templates/microsoft.servicebus/namespaces)
- [**Microsoft.ServiceBus/namespaces/filas**](/azure/templates/microsoft.servicebus/namespaces/queues)

> [!NOTE]
> Os seguintes modelos ARM estão disponíveis para download e implementação.
>
> * [Criar um espaço de nome de ônibus de serviço com regra de fila e autorização](service-bus-resource-manager-namespace-auth-rule.md)
> * [Criar um espaço de nomes de ônibus de serviço com tópico e subscrição](service-bus-resource-manager-namespace-topic.md)
> * [Criar um espaço de nomes do Service Bus](service-bus-resource-manager-namespace.md)
> * [Criar um espaço de nomes de Service Bus com tópico, subscrição e regra](service-bus-resource-manager-namespace-topic-with-rule.md)

Você pode encontrar mais modelo de [Azure Quickstart Templates](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Servicebus&pageNumber=1&sort=Popular)

## <a name="deploy-the-template"></a>Implementar o modelo

Com este modelo, você implementa um espaço de nome de Service Bus com uma fila.

[As filas de ônibus](service-bus-queues-topics-subscriptions.md#queues) de serviço oferecem entrega de mensagens First In, First out (FIFO) a um ou mais consumidores concorrentes.

Para executar a implementação automaticamente, clique no seguinte botão: Crie um novo grupo de recursos para a implementação para que possa ser facilmente limpada mais tarde.

[![Implementar no Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-servicebus-create-queue%2Fazuredeploy.json)

## <a name="validate-the-deployment"></a>Validar a implementação

1. Selecione **Notificações** no topo para ver o estado da implementação. Espere até que o destacamento tenha sucesso. Em seguida, selecione **Ir ao grupo de recursos** na mensagem de notificação para navegar na página para o grupo de recursos que contém o espaço de nomes do Service Bus. 

    ![Notificação da implantação](./media/service-bus-resource-manager-namespace-queue/notification.png)
2. Confirme que vê o seu espaço de nome de Service Bus na lista de recursos. 

    ![Grupo de recursos - espaço de nome](./media/service-bus-resource-manager-namespace-queue/resource-group-namespace.png)
3. Selecione o espaço de nomes da lista para ver a página **Service Bus Namespace.** 

## <a name="clean-up-resources"></a>Limpar os recursos

1. No portal Azure, navegue na página do **grupo De recursos** para o seu grupo de recursos.
2. Selecione **Eliminar grupo de recursos**, na barra de ferramentas. 
3. Digite o nome do grupo de recursos e selecione **Delete**. 

    ![Grupo de recursos - excluir](./media/service-bus-resource-manager-namespace-queue/resource-group-delete.png)

## <a name="next-steps"></a>Passos seguintes

Veja o seguinte tópico que mostra como criar uma regra de autorização para o espaço/fila de nomes:

[Crie uma regra de autorização de ônibus de serviço para espaço de nome e fila usando um modelo ARM](service-bus-resource-manager-namespace-auth-rule.md)

Saiba como gerir estes recursos visualizando estes artigos:

* [Gerir o Barramento de Serviço com o PowerShell](service-bus-manage-with-ps.md)
* [Gerir recursos de ônibus de serviço com o Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/releases)

[Authoring Azure Resource Manager templates]: ../azure-resource-manager/templates/template-syntax.md
[Service Bus namespace and queue template]: https://github.com/Azure/azure-quickstart-templates/blob/master/201-servicebus-create-queue/
[Azure Quickstart Templates]: https://azure.microsoft.com/documentation/templates/?term=service+bus
[Learn more about Service Bus queues]: service-bus-queues-topics-subscriptions.md
[Using Azure PowerShell with Azure Resource Manager]: ../azure-resource-manager/management/manage-resources-powershell.md
[Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../azure-resource-manager/management/manage-resources-cli.md
