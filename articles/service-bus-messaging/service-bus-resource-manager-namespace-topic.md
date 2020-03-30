---
title: Crie o tópico do espaço de nome do ônibus de serviço Azure usando um modelo
description: 'Quickstart: Criar um espaço de nome de ônibus de serviço com tópico e subscrição usando o modelo de Gestor de Recursos Azure'
services: service-bus-messaging
documentationcenter: .net
author: spelluru
manager: timlt
editor: ''
ms.assetid: d3d55200-5c60-4b5f-822d-59974cafff0e
ms.service: service-bus-messaging
ms.devlang: tbd
ms.topic: quickstart
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 01/16/2020
ms.author: spelluru
ms.openlocfilehash: c94b670a33f7640d2d6f428287b3ba0fab766bc5
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2020
ms.locfileid: "80384879"
---
# <a name="quickstart-create-a-service-bus-namespace-with-topic-and-subscription-using-an-azure-resource-manager-template"></a>Quickstart: Criar um espaço de nome de ônibus de serviço com tópico e subscrição usando um modelo de Gestor de Recursos Azure

Este artigo mostra como usar um modelo de Gestor de Recursos Azure que cria um espaço de nome de ônibus de serviço e um tópico e subscrição dentro desse espaço de nome. O artigo explica como especificar quais os recursos que são implantados e como definir parâmetros que são especificados quando a implementação é executada. Pode utilizar este modelo para as suas próprias implementações ou personalizá-lo para satisfazer as suas necessidades

Para obter mais informações sobre a criação de modelos, consulte [Authoring Azure Resource Manager templates][Authoring Azure Resource Manager templates] (Criar modelos do Azure Resource Manager).

Para obter o modelo completo, consulte o espaço de nome do Ônibus de serviço com tópico e modelo [de subscrição.][Service Bus namespace with topic and subscription]

> [!NOTE]
> Os seguintes modelos do Gestor de Recursos Azure estão disponíveis para download e implementação.
> 
> * [Criar um espaço de nomes do Service Bus](service-bus-resource-manager-namespace.md)
> * [Crie um espaço de nome de ônibus de serviço com fila](service-bus-resource-manager-namespace-queue.md)
> * [Criar um espaço de nome de ônibus de serviço com a regra de fila e autorização](service-bus-resource-manager-namespace-auth-rule.md)
> * [Criar um espaço de nome de ônibus de serviço com tópico, subscrição e regra](service-bus-resource-manager-namespace-topic-with-rule.md)
> 
> Para verificar os modelos mais recentes, visite a galeria [de modelos Azure Quickstart][Azure Quickstart Templates] e procure o **Ônibus de Serviço.**

## <a name="what-do-you-deploy"></a>O que é que vais acionar?

Com este modelo, você implementa um espaço de nome de ônibus de serviço com tópico e subscrição.

[Os tópicos e subscrições](service-bus-queues-topics-subscriptions.md#topics-and-subscriptions) do Service Bus fornecem uma forma de comunicação de um a muitos, num padrão de *publicação/subscrição.*

Para executar automaticamente a implementação, clique no seguinte botão:

[![Desdobre para Azure](./media/service-bus-resource-manager-namespace-topic/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-servicebus-create-topic-and-subscription%2Fazuredeploy.json)

## <a name="parameters"></a>Parâmetros

Com o Azure Resource Manager, define parâmetros para os valores que pretende especificar quando o modelo é implementado. O modelo inclui `Parameters` uma secção chamada que contém todos os valores do parâmetro. Defina um parâmetro para os valores que variam em função do projeto que está a implementar ou com base no ambiente para o qual está a implementar. Não defina parâmetros para valores que permanecem sempre iguais. Cada valor de parâmetro é utilizado no modelo para definir os recursos que são implementados.

O modelo define os seguintes parâmetros:

### <a name="servicebusnamespacename"></a>serviceBusNamespaceName

O nome do espaço de nome do Ônibus de serviço para criar.

```json
"serviceBusNamespaceName": {
"type": "string"
}
```

### <a name="servicebustopicname"></a>serviceBusTopicName

O nome do tema criado no espaço de nome do Bus de Serviço.

```json
"serviceBusTopicName": {
"type": "string"
}
```

### <a name="servicebussubscriptionname"></a>serviceBusSubscriptionName

O nome da subscrição criada no espaço de nome do Bus de Serviço.

```json
"serviceBusSubscriptionName": {
"type": "string"
}
```

### <a name="servicebusapiversion"></a>serviçoBusApiVersion

A versão API do bus de serviço do modelo.

```json
"serviceBusApiVersion": { 
       "type": "string", 
       "defaultValue": "2017-04-01", 
       "metadata": { 
           "description": "Service Bus ApiVersion used by the template" 
       }
```

## <a name="resources-to-deploy"></a>Recursos a implementar

Cria um espaço de nome de ônibus de serviço padrão de tipo **Mensagens,** com tópico e subscrição.

```json
"resources": [{
        "apiVersion": "[variables('sbVersion')]",
        "name": "[parameters('serviceBusNamespaceName')]",
        "type": "Microsoft.ServiceBus/Namespaces",
        "location": "[variables('location')]",
        "kind": "Messaging",
        "sku": {
            "name": "Standard",
        },
        "resources": [{
            "apiVersion": "[variables('sbVersion')]",
            "name": "[parameters('serviceBusTopicName')]",
            "type": "Topics",
            "dependsOn": [
                "[concat('Microsoft.ServiceBus/namespaces/', parameters('serviceBusNamespaceName'))]"
            ],
            "properties": {
                "path": "[parameters('serviceBusTopicName')]",
            },
            "resources": [{
                "apiVersion": "[variables('sbVersion')]",
                "name": "[parameters('serviceBusSubscriptionName')]",
                "type": "Subscriptions",
                "dependsOn": [
                    "[parameters('serviceBusTopicName')]"
                ],
                "properties": {}
            }]
        }]
    }]
```

Para sintaxe jSON e propriedades, consulte espaços de [nome,](/azure/templates/microsoft.servicebus/namespaces) [tópicos](/azure/templates/microsoft.servicebus/namespaces/topics)e [subscrições.](/azure/templates/microsoft.servicebus/namespaces/topics/subscriptions)

## <a name="commands-to-run-deployment"></a>Comandos para executar a implementação

[!INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

## <a name="powershell"></a>PowerShell

```powershell-interactive
New-AzureResourceGroupDeployment -Name \<deployment-name\> -ResourceGroupName \<resource-group-name\> -TemplateUri <https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-servicebus-create-topic-and-subscription/azuredeploy.json>
```

## <a name="azure-cli"></a>CLI do Azure

```azurecli-interactive
az group deployment create \<my-resource-group\> --name \<my-deployment-name\> --template-uri <https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-servicebus-create-topic-and-subscription/azuredeploy.json>
```

## <a name="next-steps"></a>Passos seguintes

Agora que criou e implantou recursos usando o Azure Resource Manager, aprenda a gerir estes recursos visualizando estes artigos:

* [Gerir o Barramento de Serviço com o PowerShell](service-bus-manage-with-ps.md)
* [Gerir os recursos de ônibus de serviço com o Explorador de Ônibus de Serviço](https://github.com/paolosalvatori/ServiceBusExplorer/releases)

[Authoring Azure Resource Manager templates]: ../azure-resource-manager/templates/template-syntax.md
[Azure Quickstart Templates]: https://azure.microsoft.com/documentation/templates/?term=service+bus
[Learn more about Service Bus topics and subscriptions]: service-bus-queues-topics-subscriptions.md
[Using Azure PowerShell with Azure Resource Manager]: ../azure-resource-manager/powershell-azure-resource-manager.md
[Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../azure-resource-manager/xplat-cli-azure-resource-manager.md
[Service Bus namespace with topic and subscription]: https://github.com/Azure/azure-quickstart-templates/blob/master/201-servicebus-create-topic-and-subscription/
