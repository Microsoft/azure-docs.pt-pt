---
title: Crie subscrição e regra de tópico de ônibus de serviço usando o modelo Azure
description: Crie um espaço de nome de ônibus de serviço com tópico, subscrição e regra usando o modelo de Gestor de Recursos Azure
author: spelluru
ms.topic: article
ms.tgt_pltfrm: dotnet
ms.date: 06/23/2020
ms.author: spelluru
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 3f3287dd67f89f678a9875ddce93e2d0d26b2209
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "89077629"
---
# <a name="create-a-service-bus-namespace-with-topic-subscription-and-rule-using-an-azure-resource-manager-template"></a>Crie um espaço de nome de ônibus de serviço com tópico, subscrição e regra usando um modelo de Gestor de Recursos Azure

Este artigo mostra como usar um modelo de Gestor de Recursos Azure que cria um espaço de nome de Service Bus com um tópico, subscrição e regra (filtro). O artigo explica como especificar quais os recursos que são implantados e como definir parâmetros especificados quando a implantação é executada. Pode utilizar este modelo para as suas próprias implementações ou personalizá-lo para satisfazer as suas necessidades

Para obter mais informações sobre a criação de modelos, consulte [Authoring Azure Resource Manager templates][Authoring Azure Resource Manager templates] (Criar modelos do Azure Resource Manager).

Para obter mais informações sobre práticas e padrões sobre as convenções de nomeação de recursos Azure, consulte [convenções de nomeação recomendadas para recursos Azure.][Recommended naming conventions for Azure resources]

Para o modelo completo, consulte o [espaço de nomes do Service Bus com tópico, subscrição e][Service Bus namespace with topic, subscription, and rule] modelo de regra.

> [!NOTE]
> Os seguintes modelos do Gestor de Recursos Azure estão disponíveis para download e implementação.
> 
> * [Criar um espaço de nome de ônibus de serviço com regra de fila e autorização](service-bus-resource-manager-namespace-auth-rule.md)
> * [Criar um espaço de nomes de ônibus de serviço com fila](service-bus-resource-manager-namespace-queue.md)
> * [Criar um espaço de nomes do Service Bus](service-bus-resource-manager-namespace.md)
> * [Criar um espaço de nomes de ônibus de serviço com tópico e subscrição](service-bus-resource-manager-namespace-topic.md)
> 
> Para verificar os modelos mais recentes, visite a galeria [Azure Quickstart Templates][Azure Quickstart Templates] e procure por Service Bus.

## <a name="what-do-you-deploy"></a>O que é que implementas?

Com este modelo, você implementa um espaço de nomes de Service Bus com tópico, subscrição e regra (filtro).

[Os tópicos e subscrições](service-bus-queues-topics-subscriptions.md#topics-and-subscriptions) do Service Bus fornecem uma forma de comunicação de um a muitos, num padrão *de publicação/subscrição.* Ao utilizar tópicos e subscrições, os componentes de uma aplicação distribuída não comunicam diretamente entre si, em vez disso trocam mensagens através de tópicos que atuam como intermediários. Uma subscrição de um tópico assemelha-se a uma fila virtual que recebe cópias de mensagens que foram enviadas para o tema. Um filtro na subscrição permite especificar quais as mensagens enviadas para um tópico que devem aparecer dentro de uma subscrição de tópicos específicos.

## <a name="what-are-rules-filters"></a>O que são regras (filtros)?

Em muitos cenários, as mensagens com características específicas devem ser processadas de diferentes formas. Para ativar este processamento personalizado, pode configurar subscrições para encontrar mensagens que tenham propriedades específicas e, em seguida, realizar modificações a essas propriedades. Embora as subscrições do Service Bus vejam todas as mensagens enviadas para o tópico, só é possível copiar um subconjunto dessas mensagens para a fila de subscrição virtual. É realizado com filtros de subscrição. Para saber mais sobre regras (filtros), consulte [Regras e ações.](service-bus-queues-topics-subscriptions.md#rules-and-actions)

Para executar automaticamente a implementação, clique no seguinte botão:

[![Implementar no Azure](./media/service-bus-resource-manager-namespace-topic/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-servicebus-create-topic-subscription-rule%2Fazuredeploy.json)

## <a name="parameters"></a>Parâmetros

Com o Azure Resource Manager, defina parâmetros para valores que pretende especificar quando o modelo é implantado. O modelo inclui uma secção denominada `Parameters` que contém todos os valores de parâmetro. Defina um parâmetro para os valores que variam com base no projeto que está a implementar ou com base no ambiente para o qual está a implementar. Não defina parâmetros para valores que permanecem sempre iguais. Cada valor de parâmetro é utilizado no modelo para definir os recursos que são implementados.

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

O nome da regra (filtro) criada no espaço de nomes do Service Bus.

```json
   "serviceBusRuleName": {
   "type": "string",
  }
```

### <a name="servicebusapiversion"></a>serviceBusApiVersion

A versão API do Service Bus do modelo.

```json
"serviceBusApiVersion": { 
       "type": "string", 
       "defaultValue": "2017-04-01", 
       "metadata": { 
           "description": "Service Bus ApiVersion used by the template" 
       }
```

## <a name="resources-to-deploy"></a>Recursos a implementar

Cria um espaço de nome padrão do Service Bus do tipo **Mensagens,** com tópicos e subscrições e regras.

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

Para sintaxe e propriedades JSON, consulte [espaços de nome,](/azure/templates/microsoft.servicebus/namespaces) [tópicos,](/azure/templates/microsoft.servicebus/namespaces/topics)subscrições e [regras.](/azure/templates/microsoft.servicebus/namespaces/topics/subscriptions) [](/azure/templates/microsoft.servicebus/namespaces/topics/subscriptions/rules)

## <a name="commands-to-run-deployment"></a>Comandos para executar a implementação

[!INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

## <a name="powershell"></a>PowerShell

```powershell-interactive
New-AzureResourceGroupDeployment -Name \<deployment-name\> -ResourceGroupName \<resource-group-name\> -TemplateUri <https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-servicebus-create-topic-subscription-rule/azuredeploy.json>
```

## <a name="azure-cli"></a>CLI do Azure

```azurecli-interactive
azure config mode arm

azure group deployment create \<my-resource-group\> \<my-deployment-name\> --template-uri <https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-servicebus-create-topic-subscription-rule/azuredeploy.json>
```

## <a name="next-steps"></a>Passos seguintes

Saiba como gerir estes recursos visualizando estes artigos:

* [Gerir o Azure Service Bus](service-bus-management-libraries.md)
* [Gerir o Barramento de Serviço com o PowerShell](service-bus-manage-with-ps.md)
* [Gerir recursos de ônibus de serviço com o Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/releases)

[Authoring Azure Resource Manager templates]: ../azure-resource-manager/templates/template-syntax.md
[Azure Quickstart Templates]: https://azure.microsoft.com/documentation/templates/?term=service+bus
[Learn more about Service Bus topics and subscriptions]: service-bus-queues-topics-subscriptions.md
[Using Azure PowerShell with Azure Resource Manager]: ../azure-resource-manager/management/manage-resources-powershell.md
[Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../azure-resource-manager/management/manage-resources-cli.md
[Recommended naming conventions for Azure resources]: /azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging
[Service Bus namespace with topic, subscription, and rule]: https://github.com/Azure/azure-quickstart-templates/blob/master/201-servicebus-create-topic-subscription-rule/
[Service Bus queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
