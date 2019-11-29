---
title: Criar assinatura e regra de tópico do barramento de serviço do Azure usando o modelo Azure Resource Manager | Microsoft Docs
description: Criar um namespace do barramento de serviço com tópico, assinatura e regra usando Azure Resource Manager modelo
services: service-bus-messaging
documentationcenter: .net
author: spelluru
manager: timlt
editor: ''
ms.assetid: 9e0aaf58-0214-4bca-bd00-d29c08f9b1bc
ms.service: service-bus-messaging
ms.devlang: tbd
ms.topic: article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 11/27/2019
ms.author: spelluru
ms.openlocfilehash: dc61fb8e066c6189b5607a6e1cd479cb812466f7
ms.sourcegitcommit: c31dbf646682c0f9d731f8df8cfd43d36a041f85
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/27/2019
ms.locfileid: "74561556"
---
# <a name="create-a-service-bus-namespace-with-topic-subscription-and-rule-using-an-azure-resource-manager-template"></a>Criar um namespace do barramento de serviço com tópico, assinatura e regra usando um modelo de Azure Resource Manager

Este artigo mostra como usar um modelo de Azure Resource Manager que cria um namespace do barramento de serviço com um tópico, uma assinatura e uma regra (filtro). O artigo explica como especificar quais recursos são implantados e como definir parâmetros que são especificados quando a implantação é executada. Pode utilizar este modelo para as suas próprias implementações ou personalizá-lo para satisfazer as suas necessidades

Para obter mais informações sobre como criar modelos, consulte Criando [modelos de Azure Resource Manager][Authoring Azure Resource Manager templates].

Para obter mais informações sobre práticas e padrões em convenções de nomenclatura de recursos do Azure, consulte [convenções de nomenclatura recomendadas para recursos do Azure][Recommended naming conventions for Azure resources].

Para obter o modelo completo, consulte o modelo de [namespace do barramento de serviço com tópico, assinatura e regra][Service Bus namespace with topic, subscription, and rule] .

> [!NOTE]
> Os modelos de Azure Resource Manager a seguir estão disponíveis para download e implantação.
> 
> * [Criar um namespace do barramento de serviço com a regra de fila e autorização](service-bus-resource-manager-namespace-auth-rule.md)
> * [Criar um namespace do barramento de serviço com fila](service-bus-resource-manager-namespace-queue.md)
> * [Criar um namespace do barramento de serviço](service-bus-resource-manager-namespace.md)
> * [Criar um namespace do barramento de serviço com tópico e assinatura](service-bus-resource-manager-namespace-topic.md)
> 
> Para verificar os modelos mais recentes, visite a Galeria de [modelos de início rápido do Azure][Azure Quickstart Templates] e pesquise por barramento de serviço.
> 
> 

## <a name="what-do-you-deploy"></a>O que você implanta?

Com este modelo, você implanta um namespace do barramento de serviço com tópico, assinatura e regra (filtro).

Os [Tópicos e as assinaturas do barramento de serviço](service-bus-queues-topics-subscriptions.md#topics-and-subscriptions) fornecem uma forma de comunicação de um para muitos, em um padrão de *publicação/assinatura* . Ao usar tópicos e assinaturas, os componentes de um aplicativo distribuído não se comunicam diretamente entre si, em vez disso, eles trocam mensagens por meio de um tópico que atua como um intermediário. Uma assinatura de um tópico é semelhante a uma fila virtual que recebe cópias de mensagens que foram enviadas para o tópico. Um filtro na assinatura permite que você especifique quais mensagens enviadas a um tópico devem aparecer dentro de uma assinatura de tópico específica.

## <a name="what-are-rules-filters"></a>O que são regras (filtros)?

Em muitos cenários, as mensagens que têm características específicas devem ser processadas de maneiras diferentes. Para habilitar esse processamento personalizado, você pode configurar assinaturas para localizar mensagens que têm propriedades específicas e, em seguida, executar modificações nessas propriedades. Embora as assinaturas do barramento de serviço Vejam todas as mensagens enviadas ao tópico, você só pode copiar um subconjunto dessas mensagens para a fila de assinatura virtual. Ele é realizado usando filtros de assinatura. Para saber mais sobre regras (filtros), consulte [regras e ações](service-bus-queues-topics-subscriptions.md#rules-and-actions).

Para executar automaticamente a implementação, clique no seguinte botão:

[![Implementar no Azure](./media/service-bus-resource-manager-namespace-topic/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-servicebus-create-topic-subscription-rule%2Fazuredeploy.json)

## <a name="parameters"></a>Parâmetros

Com Azure Resource Manager, defina parâmetros para os valores que você deseja especificar quando o modelo é implantado. O modelo inclui uma secção denominada `Parameters` que contém todos os valores de parâmetro. Defina um parâmetro para os valores que variam de acordo com o projeto que você está implantando ou com base no ambiente no qual você está implantando. Não defina parâmetros para valores que permanecem sempre iguais. Cada valor de parâmetro é utilizado no modelo para definir os recursos que são implementados.

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
### <a name="servicebusrulename"></a>serviceBusRuleName
O nome da regra (filtro) criada no namespace do barramento de serviço.

```json
   "serviceBusRuleName": {
   "type": "string",
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
Cria um namespace padrão do barramento de serviço do tipo **mensagens**, com tópico e assinatura e regras.

```json
 "resources": [{
        "apiVersion": "[variables('sbVersion')]",
        "name": "[parameters('serviceBusNamespaceName')]",
        "type": "Microsoft.ServiceBus/Namespaces",
        "location": "[variables('location')]",
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
                "path": "[parameters('serviceBusTopicName')]"
            },
            "resources": [{
                "apiVersion": "[variables('sbVersion')]",
                "name": "[parameters('serviceBusSubscriptionName')]",
                "type": "Subscriptions",
                "dependsOn": [
                    "[parameters('serviceBusTopicName')]"
                ],
                "properties": {},
                "resources": [{
                    "apiVersion": "[variables('sbVersion')]",
                    "name": "[parameters('serviceBusRuleName')]",
                    "type": "Rules",
                    "dependsOn": [
                        "[parameters('serviceBusSubscriptionName')]"
                    ],
                    "properties": {
                        "filterType": "SqlFilter",
                        "sqlFilter": {
                            "sqlExpression": "StoreName = 'Store1'",
                            "requiresPreprocessing": "false"
                        },
                        "action": {
                            "sqlExpression": "set FilterTag = 'true'"
                        }
                    }
                }]
            }]
        }]
    }]
```

Para sintaxe e propriedades JSON, consulte [namespaces](/azure/templates/microsoft.servicebus/namespaces), [Tópicos](/azure/templates/microsoft.servicebus/namespaces/topics), [assinaturas](/azure/templates/microsoft.servicebus/namespaces/topics/subscriptions)e [regras](/azure/templates/microsoft.servicebus/namespaces/topics/subscriptions/rules).

## <a name="commands-to-run-deployment"></a>Comandos para executar a implementação
[!INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

## <a name="powershell"></a>PowerShell
```powershell
New-AzureResourceGroupDeployment -Name \<deployment-name\> -ResourceGroupName \<resource-group-name\> -TemplateUri <https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-servicebus-create-topic-subscription-rule/azuredeploy.json>
```

## <a name="azure-cli"></a>CLI do Azure
```azurecli
azure config mode arm

azure group deployment create \<my-resource-group\> \<my-deployment-name\> --template-uri <https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-servicebus-create-topic-subscription-rule/azuredeploy.json>
```

## <a name="next-steps"></a>Passos seguintes
Saiba como gerenciar esses recursos exibindo estes artigos:

* [Gerenciar o barramento de serviço do Azure](service-bus-management-libraries.md)
* [Gerir o Service Bus com o PowerShell](service-bus-manage-with-ps.md)
* [Gerenciar recursos do barramento de serviço com o Gerenciador do barramento de serviço](https://github.com/paolosalvatori/ServiceBusExplorer/releases)

[Authoring Azure Resource Manager templates]: ../azure-resource-manager/resource-group-authoring-templates.md
[Azure Quickstart Templates]: https://azure.microsoft.com/documentation/templates/?term=service+bus
[Learn more about Service Bus topics and subscriptions]: service-bus-queues-topics-subscriptions.md
[Using Azure PowerShell with Azure Resource Manager]: ../azure-resource-manager/powershell-azure-resource-manager.md
[Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../azure-resource-manager/xplat-cli-azure-resource-manager.md
[Recommended naming conventions for Azure resources]: /azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging
[Service Bus namespace with topic, subscription, and rule]: https://github.com/Azure/azure-quickstart-templates/blob/master/201-servicebus-create-topic-subscription-rule/
[Service Bus queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md

