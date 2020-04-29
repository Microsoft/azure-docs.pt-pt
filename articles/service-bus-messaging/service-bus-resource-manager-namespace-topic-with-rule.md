---
title: Crie subscrição e regra do tópico do ônibus de serviço usando o modelo Azure
description: Crie um espaço de nome de ônibus de serviço com tópico, subscrição e regra usando o modelo de Gestor de Recursos Azure
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
ms.openlocfilehash: 6cbaf447dfcf06ae11f2282d7d847978297af8b8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80384896"
---
# <a name="create-a-service-bus-namespace-with-topic-subscription-and-rule-using-an-azure-resource-manager-template"></a>Crie um espaço de nome de ônibus de serviço com tópico, subscrição e regra usando um modelo de Gestor de Recursos Azure

Este artigo mostra como usar um modelo de Gestor de Recursos Azure que cria um espaço de nome de ônibus de serviço com um tópico, subscrição e regra (filtro). O artigo explica como especificar quais os recursos que são implantados e como definir parâmetros que são especificados quando a implementação é executada. Pode utilizar este modelo para as suas próprias implementações ou personalizá-lo para satisfazer as suas necessidades

Para obter mais informações sobre a criação de modelos, consulte [Authoring Azure Resource Manager templates][Authoring Azure Resource Manager templates] (Criar modelos do Azure Resource Manager).

Para obter mais informações sobre práticas e padrões sobre as convenções de nomeação de recursos Azure, consulte [convenções recomendadas de nomeação para os recursos azure.][Recommended naming conventions for Azure resources]

Para obter o modelo completo, consulte o espaço de nome do [Ônibus de serviço com tópico, subscrição e][Service Bus namespace with topic, subscription, and rule] modelo de regra.

> [!NOTE]
> Os seguintes modelos do Gestor de Recursos Azure estão disponíveis para download e implementação.
> 
> * [Criar um espaço de nome de ônibus de serviço com a regra de fila e autorização](service-bus-resource-manager-namespace-auth-rule.md)
> * [Crie um espaço de nome de ônibus de serviço com fila](service-bus-resource-manager-namespace-queue.md)
> * [Criar um espaço de nomes do Service Bus](service-bus-resource-manager-namespace.md)
> * [Crie um espaço de nome de ônibus de serviço com tópico e subscrição](service-bus-resource-manager-namespace-topic.md)
> 
> Para verificar os modelos mais recentes, visite a galeria [De modelos Azure Quickstart][Azure Quickstart Templates] e procure o Ônibus de Serviço.

## <a name="what-do-you-deploy"></a>O que é que vais acionar?

Com este modelo, você implementa um espaço de nome de ônibus de serviço com tópico, subscrição e regra (filtro).

[Os tópicos e subscrições](service-bus-queues-topics-subscriptions.md#topics-and-subscriptions) do Service Bus fornecem uma forma de comunicação de um a muitos, num padrão de *publicação/subscrição.* Quando utilizam tópicos e subscrições, os componentes de uma aplicação distribuída não comunicam diretamente entre si, em vez disso trocam mensagens através de tópicos que atuam como intermediários. Uma subscrição de um tópico assemelha-se a uma fila virtual que recebe cópias de mensagens que foram enviadas para o tema. Um filtro na subscrição permite especificar quais as mensagens enviadas para um tópico dentro de uma subscrição específica do tópico.

## <a name="what-are-rules-filters"></a>O que são regras (filtros)?

Em muitos cenários, as mensagens com características específicas devem ser processadas de diferentes formas. Para ativar este processamento personalizado, pode configurar subscrições para encontrar mensagens que tenham propriedades específicas e, em seguida, realizar modificações nessas propriedades. Embora as subscrições do Service Bus vejam todas as mensagens enviadas para o tópico, só pode copiar um subconjunto dessas mensagens para a fila de subscrição virtual. É realizado usando filtros de subscrição. Para saber mais sobre regras (filtros), consulte [Regras e ações.](service-bus-queues-topics-subscriptions.md#rules-and-actions)

Para executar automaticamente a implementação, clique no seguinte botão:

[![Implementar no Azure](./media/service-bus-resource-manager-namespace-topic/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-servicebus-create-topic-subscription-rule%2Fazuredeploy.json)

## <a name="parameters"></a>Parâmetros

Com o Gestor de Recursos Azure, defina os parâmetros para os valores que pretende especificar quando o modelo é implantado. O modelo inclui uma secção denominada `Parameters` que contém todos os valores de parâmetro. Defina um parâmetro para os valores que variam em função do projeto que está a implementar ou com base no ambiente para o qual está a implementar. Não defina parâmetros para valores que permanecem sempre iguais. Cada valor de parâmetro é utilizado no modelo para definir os recursos que são implementados.

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

### <a name="servicebusrulename"></a>serviceBusRuleName

O nome da regra (filtro) criada no espaço de nome do Autocarro de Serviço.

```json
   "serviceBusRuleName": {
   "type": "string",
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

Cria um espaço de nome de ônibus de serviço padrão de tipo **Mensagens,** com tópico e subscrição e regras.

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

Para sintaxe jSON e propriedades, consulte espaços de [nomes,](/azure/templates/microsoft.servicebus/namespaces) [tópicos,](/azure/templates/microsoft.servicebus/namespaces/topics) [subscrições](/azure/templates/microsoft.servicebus/namespaces/topics/subscriptions)e [regras.](/azure/templates/microsoft.servicebus/namespaces/topics/subscriptions/rules)

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

Aprenda a gerir estes recursos visualizando estes artigos:

* [Gerir o ônibus de serviço azure](service-bus-management-libraries.md)
* [Gerir o Barramento de Serviço com o PowerShell](service-bus-manage-with-ps.md)
* [Gerir os recursos de ônibus de serviço com o Explorador de Ônibus de Serviço](https://github.com/paolosalvatori/ServiceBusExplorer/releases)

[Authoring Azure Resource Manager templates]: ../azure-resource-manager/templates/template-syntax.md
[Azure Quickstart Templates]: https://azure.microsoft.com/documentation/templates/?term=service+bus
[Learn more about Service Bus topics and subscriptions]: service-bus-queues-topics-subscriptions.md
[Using Azure PowerShell with Azure Resource Manager]: ../azure-resource-manager/powershell-azure-resource-manager.md
[Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../azure-resource-manager/xplat-cli-azure-resource-manager.md
[Recommended naming conventions for Azure resources]: /azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging
[Service Bus namespace with topic, subscription, and rule]: https://github.com/Azure/azure-quickstart-templates/blob/master/201-servicebus-create-topic-subscription-rule/
[Service Bus queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
