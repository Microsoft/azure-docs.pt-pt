---
title: Criar subscrição de tópico do Service bus do Azure e regra utilizando o modelo Azure Resource Manager | Documentos da Microsoft
description: Criar um espaço de nomes do Service Bus com o tópico, subscrição e regra com o modelo Azure Resource Manager
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
ms.date: 01/23/2019
ms.author: spelluru
ms.openlocfilehash: 8be34a85cb1a1863ee8a78c7b97af66627612fea
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67444753"
---
# <a name="create-a-service-bus-namespace-with-topic-subscription-and-rule-using-an-azure-resource-manager-template"></a>Criar um espaço de nomes do Service Bus com o tópico, subscrição e regra utilizando um modelo Azure Resource Manager

Este artigo mostra como utilizar um modelo do Azure Resource Manager que cria um espaço de nomes do Service Bus com um tópico, subscrição e regra (filtro). O artigo explica como especificar quais recursos são implementados e como definir os parâmetros que são especificados quando a implementação é executada. Pode utilizar este modelo para as suas próprias implementações ou personalizá-lo para satisfazer as suas necessidades

Para obter mais informações sobre a criação de modelos, consulte [modelos Authoring Azure Resource Manager][Authoring Azure Resource Manager templates].

Para obter mais informações sobre práticas e padrões de convenções de nomenclatura de recursos do Azure, consulte [convenções de nomenclatura para recursos do Azure recomendadas][Recommended naming conventions for Azure resources].

Para o modelo completo, consulte a [espaço de nomes do Service Bus com o tópico, subscrição e regra][Service Bus namespace with topic, subscription, and rule] modelo.

> [!NOTE]
> Os seguintes modelos do Azure Resource Manager estão disponíveis para download e implantação.
> 
> * [Criar um espaço de nomes do Service Bus com a regra de autorização e fila](service-bus-resource-manager-namespace-auth-rule.md)
> * [Criar um espaço de nomes do Service Bus com a fila](service-bus-resource-manager-namespace-queue.md)
> * [Criar um espaço de nomes do Service Bus](service-bus-resource-manager-namespace.md)
> * [Criar um espaço de nomes do Service Bus com o tópico e uma subscrição](service-bus-resource-manager-namespace-topic.md)
> 
> Para verificar os modelos mais recentes, visite o [modelos de início rápido do Azure][Azure Quickstart Templates] Galeria e pesquisa do Service Bus.
> 
> 

## <a name="what-do-you-deploy"></a>O que implanta?

Com este modelo, implementa um espaço de nomes do Service Bus com o tópico, subscrição e regra (filtro).

[Subscrições e tópicos do Service Bus](service-bus-queues-topics-subscriptions.md#topics-and-subscriptions) oferecem uma forma de um-para-muitos de comunicação, num *publicação/subscrição* padrão. Quando utilizam tópicos e subscrições, os componentes de uma aplicação distribuída não comunicam diretamente entre si, em vez disso, trocam mensagens através de tópico que serve de intermediário. Uma subscrição para um tópico é semelhante a uma fila virtual que recebe cópias das mensagens que foram enviadas para o tópico. Um filtro de subscrição permite-lhe especificar quais as mensagens enviadas para um tópico deve aparecer dentro de uma subscrição de tópico específico.

## <a name="what-are-rules-filters"></a>O que são regras (filtros)?

Em muitos cenários, as mensagens com características específicas devem ser processadas de formas diferentes. Para ativar este processamento personalizado, pode configurar subscrições para localizar mensagens que têm as propriedades específicas e, em seguida, executam modificações nas propriedades. Apesar de subscrições do Service Bus ver todas as mensagens enviadas para o tópico, só poderá copiar um subconjunto dessas mensagens na fila virtual da subscrição. Isso pode ser realizado com filtros de subscrição. Para saber mais sobre regras (filtros), veja [regras e ações](service-bus-queues-topics-subscriptions.md#rules-and-actions).

Para executar automaticamente a implementação, clique no seguinte botão:

[![Implementar no Azure](./media/service-bus-resource-manager-namespace-topic/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-servicebus-create-topic-subscription-rule%2Fazuredeploy.json)

## <a name="parameters"></a>Parâmetros

Com o Azure Resource Manager, defina parâmetros para valores que pretende especificar quando o modelo é implementado. O modelo inclui uma secção denominada `Parameters` que contém todos os valores de parâmetro. Defina um parâmetro para esses valores que variam com base no projeto que está a implementar ou com base no ambiente que esteja a implementar. Não defina parâmetros para valores que permanecem sempre iguais. Cada valor de parâmetro é utilizado no modelo para definir os recursos que são implementados.

O modelo define os seguintes parâmetros:

### <a name="servicebusnamespacename"></a>serviceBusNamespaceName
O nome do espaço de nomes do Service Bus para criar.

```json
"serviceBusNamespaceName": {
"type": "string"
}
```

### <a name="servicebustopicname"></a>serviceBusTopicName
O nome do tópico criado no espaço de nomes do Service Bus.

```json
"serviceBusTopicName": {
"type": "string"
}
```

### <a name="servicebussubscriptionname"></a>serviceBusSubscriptionName
O nome da subscrição criada no espaço de nomes do Service Bus.

```json
"serviceBusSubscriptionName": {
"type": "string"
}
```
### <a name="servicebusrulename"></a>serviceBusRuleName
O nome do rule(filter) criado no espaço de nomes do Service Bus.

```json
   "serviceBusRuleName": {
   "type": "string",
  }
```
### <a name="servicebusapiversion"></a>serviceBusApiVersion
A versão de API do Service Bus do modelo.

```json
"serviceBusApiVersion": { 
       "type": "string", 
       "defaultValue": "2017-04-01", 
       "metadata": { 
           "description": "Service Bus ApiVersion used by the template" 
       }
```
## <a name="resources-to-deploy"></a>Recursos a implementar
Cria um espaço de nomes do Service Bus padrão do tipo **Messaging**, com o tópico e subscrição e regras.

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

Para a sintaxe JSON e propriedades, consulte [espaços de nomes](/azure/templates/microsoft.servicebus/namespaces), [tópicos](/azure/templates/microsoft.servicebus/namespaces/topics), [subscrições](/azure/templates/microsoft.servicebus/namespaces/topics/subscriptions), e [regras](/azure/templates/microsoft.servicebus/namespaces/topics/subscriptions/rules).

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

## <a name="next-steps"></a>Passos Seguintes
Consulte o tópico seguinte que mostra como criar um filtro de subscrição: [Criar um espaço de nomes do Service Bus com o tópico, subscrição e regra utilizando um modelo Azure Resource Manager](service-bus-resource-manager-namespace-topic-with-rule.md)

Saiba como gerir estes recursos ao visualizar estes artigos:

* [Gerir o Azure Service Bus](service-bus-management-libraries.md)
* [Gerir o Service Bus com o PowerShell](service-bus-manage-with-ps.md)
* [Gerir recursos do Service Bus com o Explorador do Service Bus](https://github.com/paolosalvatori/ServiceBusExplorer/releases)

[Authoring Azure Resource Manager templates]: ../azure-resource-manager/resource-group-authoring-templates.md
[Azure Quickstart Templates]: https://azure.microsoft.com/documentation/templates/?term=service+bus
[Learn more about Service Bus topics and subscriptions]: service-bus-queues-topics-subscriptions.md
[Using Azure PowerShell with Azure Resource Manager]: ../azure-resource-manager/powershell-azure-resource-manager.md
[Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../azure-resource-manager/xplat-cli-azure-resource-manager.md
[Recommended naming conventions for Azure resources]: ../guidance/guidance-naming-conventions.md
[Service Bus namespace with topic, subscription, and rule]: https://github.com/Azure/azure-quickstart-templates/blob/master/201-servicebus-create-topic-subscription-rule/
[Service Bus queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md

