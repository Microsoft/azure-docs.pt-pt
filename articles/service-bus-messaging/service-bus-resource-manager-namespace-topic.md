---
title: 'Início rápido: criar assinatura de tópico de namespace do barramento de serviço do Azure usando Azure Resource Manager modelo'
description: 'Início rápido: criar um namespace do barramento de serviço com tópico e assinatura usando Azure Resource Manager modelo'
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
ms.date: 11/05/2019
ms.author: spelluru
ms.openlocfilehash: a43b945ce11736b16e8e11edbe988a3c48f62d51
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/07/2019
ms.locfileid: "73718776"
---
# <a name="quickstart-create-a-service-bus-namespace-with-topic-and-subscription-using-an-azure-resource-manager-template"></a>Início rápido: criar um namespace do barramento de serviço com tópico e assinatura usando um modelo de Azure Resource Manager

Este artigo mostra como usar um modelo de Azure Resource Manager que cria um namespace do barramento de serviço e um tópico e uma assinatura dentro desse namespace. O artigo explica como especificar quais recursos são implantados e como definir parâmetros que são especificados quando a implantação é executada. Pode utilizar este modelo para as suas próprias implementações ou personalizá-lo para satisfazer as suas necessidades

Para obter mais informações sobre como criar modelos, consulte Criando [modelos de Azure Resource Manager][Authoring Azure Resource Manager templates].

Para obter o modelo completo, consulte o modelo [namespace do barramento de serviço com tópico e assinatura][Service Bus namespace with topic and subscription] .

> [!NOTE]
> Os modelos de Azure Resource Manager a seguir estão disponíveis para download e implantação.
> 
> * [Criar um namespace do barramento de serviço](service-bus-resource-manager-namespace.md)
> * [Criar um namespace do barramento de serviço com fila](service-bus-resource-manager-namespace-queue.md)
> * [Criar um namespace do barramento de serviço com a regra de fila e autorização](service-bus-resource-manager-namespace-auth-rule.md)
> * [Criar um namespace do barramento de serviço com tópico, assinatura e regra](service-bus-resource-manager-namespace-topic-with-rule.md)
> 
> Para verificar os modelos mais recentes, visite a Galeria de [modelos de início rápido do Azure][Azure Quickstart Templates] e pesquise por **barramento de serviço**.
> 
> 

## <a name="what-do-you-deploy"></a>O que você implanta?

Com esse modelo, você implanta um namespace do barramento de serviço com tópico e assinatura.

Os [Tópicos e as assinaturas do barramento de serviço](service-bus-queues-topics-subscriptions.md#topics-and-subscriptions) fornecem uma forma de comunicação de um para muitos, em um padrão de *publicação/assinatura* .

Para executar automaticamente a implementação, clique no seguinte botão:

[![Implementar no Azure](./media/service-bus-resource-manager-namespace-topic/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-servicebus-create-topic-and-subscription%2Fazuredeploy.json)

## <a name="parameters"></a>Parâmetros

Com o Azure Resource Manager, define parâmetros para os valores que pretende especificar quando o modelo é implementado. O modelo inclui uma seção chamada `Parameters` que contém todos os valores de parâmetro. Defina um parâmetro para os valores que variam de acordo com o projeto que você está implantando ou com base no ambiente no qual você está implantando. Não defina parâmetros para valores que permanecem sempre iguais. Cada valor de parâmetro é utilizado no modelo para definir os recursos que são implementados.

O modelo define os seguintes parâmetros:

### <a name="servicebusnamespacename"></a>serviceBusNamespaceName
O nome do namespace do barramento de serviço a ser criado.

```json
"serviceBusNamespaceName": {
"type": "string"
}
```

### <a name="servicebustopicname"></a>serviceBusTopicName
O nome do tópico criado no namespace do barramento de serviço.

```json
"serviceBusTopicName": {
"type": "string"
}
```

### <a name="servicebussubscriptionname"></a>serviceBusSubscriptionName
O nome da assinatura criada no namespace do barramento de serviço.

```json
"serviceBusSubscriptionName": {
"type": "string"
}
```

### <a name="servicebusapiversion"></a>serviceBusApiVersion
A versão da API do barramento de serviço do modelo.

```json
"serviceBusApiVersion": { 
       "type": "string", 
       "defaultValue": "2017-04-01", 
       "metadata": { 
           "description": "Service Bus ApiVersion used by the template" 
       }
```
## <a name="resources-to-deploy"></a>Recursos a implementar
Cria um namespace padrão do barramento de serviço do tipo **mensagens**, com o tópico e a assinatura.

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

Para sintaxe e propriedades JSON, consulte [namespaces](/azure/templates/microsoft.servicebus/namespaces), [Tópicos](/azure/templates/microsoft.servicebus/namespaces/topics)e [assinaturas](/azure/templates/microsoft.servicebus/namespaces/topics/subscriptions).

## <a name="commands-to-run-deployment"></a>Comandos para executar a implementação
[!INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

## <a name="powershell"></a>PowerShell
```powershell
New-AzureResourceGroupDeployment -Name \<deployment-name\> -ResourceGroupName \<resource-group-name\> -TemplateUri <https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-servicebus-create-topic-and-subscription/azuredeploy.json>
```

## <a name="azure-cli"></a>CLI do Azure
```azurecli
azure config mode arm

azure group deployment create \<my-resource-group\> \<my-deployment-name\> --template-uri <https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-servicebus-create-topic-and-subscription/azuredeploy.json>
```

## <a name="next-steps"></a>Passos seguintes
Agora que você criou e implantou recursos usando Azure Resource Manager, saiba como gerenciar esses recursos exibindo estes artigos:

* [Gerir o Service Bus com o PowerShell](service-bus-manage-with-ps.md)
* [Gerenciar recursos do barramento de serviço com o Gerenciador do barramento de serviço](https://github.com/paolosalvatori/ServiceBusExplorer/releases)

[Authoring Azure Resource Manager templates]: ../azure-resource-manager/resource-group-authoring-templates.md
[Azure Quickstart Templates]: https://azure.microsoft.com/documentation/templates/?term=service+bus
[Learn more about Service Bus topics and subscriptions]: service-bus-queues-topics-subscriptions.md
[Using Azure PowerShell with Azure Resource Manager]: ../azure-resource-manager/powershell-azure-resource-manager.md
[Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../azure-resource-manager/xplat-cli-azure-resource-manager.md
[Service Bus namespace with topic and subscription]: https://github.com/Azure/azure-quickstart-templates/blob/master/201-servicebus-create-topic-and-subscription/
