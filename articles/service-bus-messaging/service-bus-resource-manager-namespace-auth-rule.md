---
title: Crie uma regra de autorização de ônibus de serviço usando um modelo Azure
description: Crie uma regra de autorização de ônibus de serviço para espaço de nome e fila usando o modelo de Gestor de Recursos Azure
services: service-bus-messaging
documentationcenter: .net
author: axisc
manager: timlt
editor: spelluru
ms.assetid: 7f1443a0-5fa8-4d90-8637-1a977ef0b1f0
ms.service: service-bus-messaging
ms.devlang: tbd
ms.topic: article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 12/20/2019
ms.author: aschhab
ms.openlocfilehash: 1bfb2d2d946a85c1d051315fb29a5a63f7a00871
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2020
ms.locfileid: "80384930"
---
# <a name="create-a-service-bus-authorization-rule-for-namespace-and-queue-using-an-azure-resource-manager-template"></a>Crie uma regra de autorização de ônibus de serviço para espaço de nome e fila usando um modelo de Gestor de Recursos Azure

Este artigo mostra como usar um modelo de Gestor de Recursos Azure que cria uma regra de [autorização](service-bus-authentication-and-authorization.md#shared-access-signature) para um espaço de nome e fila de ônibus de serviço. O artigo explica como especificar quais os recursos que são implantados e como definir parâmetros que são especificados quando a implementação é executada. Pode utilizar este modelo para as suas próprias implementações ou personalizá-lo para satisfazer as suas necessidades.

Para mais informações sobre a criação de modelos, consulte [os modelos de Gestor de Recursos Do Azure.][Authoring Azure Resource Manager templates]

Para obter o modelo completo, consulte o modelo de regra de autorização de [ônibus de serviço][Service Bus auth rule template] no GitHub.

> [!NOTE]
> Os seguintes modelos do Gestor de Recursos Azure estão disponíveis para download e implementação.
> 
> * [Criar um espaço de nomes do Service Bus](service-bus-resource-manager-namespace.md)
> * [Crie um espaço de nome de ônibus de serviço com fila](service-bus-resource-manager-namespace-queue.md)
> * [Crie um espaço de nome de ônibus de serviço com tópico e subscrição](service-bus-resource-manager-namespace-topic.md)
> * [Criar um espaço de nome de ônibus de serviço com tópico, subscrição e regra](service-bus-resource-manager-namespace-topic-with-rule.md)
> 
> Para verificar os modelos mais recentes, visite a galeria [de modelos Azure Quickstart][Azure Quickstart Templates] e procure o **Ônibus de Serviço.**

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="what-will-you-deploy"></a>O que irá implementar?

Com este modelo, você implementa uma regra de autorização de ônibus de serviço para um espaço de nome e entidade de mensagens (neste caso, uma fila).

Este modelo utiliza [a Assinatura de Acesso Partilhado (SAS)](service-bus-sas.md) para autenticação. A SAS permite que as aplicações se autentiquem ao Service Bus utilizando uma chave de acesso configurada no espaço de nome, ou na entidade de mensagens (fila ou tópico) com a qual os direitos específicos estão associados. Em seguida, pode utilizar esta chave para gerar um símbolo SAS que os clientes podem, por sua vez, usar para autenticar o Service Bus.

Para executar automaticamente a implementação, clique no seguinte botão:

[![Desdobre para Azure](./media/service-bus-resource-manager-namespace-auth-rule/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F301-servicebus-create-authrule-namespace-and-queue%2Fazuredeploy.json)

## <a name="parameters"></a>Parâmetros

Com o Azure Resource Manager, define parâmetros para os valores que pretende especificar quando o modelo é implementado. O modelo inclui `Parameters` uma secção chamada que contém todos os valores do parâmetro. Deve definir um parâmetro para os valores que variarão em função do projeto que está a implementar ou com base no ambiente para o qual está a implementar. Não defina parâmetros para valores que se mantenham sempre os mesmos. Cada valor de parâmetro é utilizado no modelo para definir os recursos que são implementados.

O modelo define os seguintes parâmetros.

### <a name="servicebusnamespacename"></a>serviceBusNamespaceName

O nome do espaço de nome do Ônibus de serviço para criar.

```json
"serviceBusNamespaceName": {
"type": "string"
}
```

### <a name="namespaceauthorizationrulename"></a>nome spaceAuthorizationRuleName

O nome da regra de autorização para o espaço de nome.

```json
"namespaceAuthorizationRuleName ": {
"type": "string"
}
```

### <a name="servicebusqueuename"></a>serviceBusQueueName

O nome da fila no espaço de nome do Autocarro de Serviço.

```json
"serviceBusQueueName": {
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

Cria um espaço de nome de ônibus de serviço padrão de tipo **Mensagens,** e uma regra de autorização de ônibus de serviço para espaço de nome e entidade.

```json
"resources": [
        {
            "apiVersion": "[variables('sbVersion')]",
            "name": "[parameters('serviceBusNamespaceName')]",
            "type": "Microsoft.ServiceBus/namespaces",
            "location": "[variables('location')]",
            "kind": "Messaging",
            "sku": {
                "name": "Standard",
            },
            "resources": [
                {
                    "apiVersion": "[variables('sbVersion')]",
                    "name": "[parameters('serviceBusQueueName')]",
                    "type": "Queues",
                    "dependsOn": [
                        "[concat('Microsoft.ServiceBus/namespaces/', parameters('serviceBusNamespaceName'))]"
                    ],
                    "properties": {
                        "path": "[parameters('serviceBusQueueName')]"
                    },
                    "resources": [
                        {
                            "apiVersion": "[variables('sbVersion')]",
                            "name": "[parameters('queueAuthorizationRuleName')]",
                            "type": "authorizationRules",
                            "dependsOn": [
                                "[parameters('serviceBusQueueName')]"
                            ],
                            "properties": {
                                "Rights": ["Listen"]
                            }
                        }
                    ]
                }
            ]
        }, {
            "apiVersion": "[variables('sbVersion')]",
            "name": "[variables('namespaceAuthRuleName')]",
            "type": "Microsoft.ServiceBus/namespaces/authorizationRules",
            "dependsOn": ["[concat('Microsoft.ServiceBus/namespaces/', parameters('serviceBusNamespaceName'))]"],
            "location": "[resourceGroup().location]",
            "properties": {
                "Rights": ["Send"]
            }
        }
    ]
```

Para sintaxe jSON e propriedades, consulte espaços de [nome,](/azure/templates/microsoft.servicebus/namespaces) [filas,](/azure/templates/microsoft.servicebus/namespaces/queues)e [Regras de Autorização.](/azure/templates/microsoft.servicebus/namespaces/authorizationrules)

## <a name="commands-to-run-deployment"></a>Comandos para executar a implementação

[!INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

### <a name="powershell"></a>PowerShell

```powershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName \<resource-group-name\> -TemplateFile <https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/301-servicebus-create-authrule-namespace-and-queue/azuredeploy.json>
```

## <a name="azure-cli"></a>CLI do Azure

```azurecli-interactive
azure config mode arm

azure group deployment create \<my-resource-group\> \<my-deployment-name\> --template-uri <https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/301-servicebus-create-authrule-namespace-and-queue/azuredeploy.json>
```

## <a name="next-steps"></a>Passos seguintes

Agora que criou e implantou recursos usando o Azure Resource Manager, aprenda a gerir estes recursos visualizando estes artigos:

* [Gerir o Barramento de Serviço com o PowerShell](service-bus-powershell-how-to-provision.md)
* [Gerir os recursos de ônibus de serviço com o Explorador de Ônibus de Serviço](https://github.com/paolosalvatori/ServiceBusExplorer/releases)
* [Autenticação e autorização do Service Bus](service-bus-authentication-and-authorization.md)

[Authoring Azure Resource Manager templates]: ../azure-resource-manager/templates/template-syntax.md
[Azure Quickstart Templates]: https://azure.microsoft.com/documentation/templates/?term=service+bus
[Using Azure PowerShell with Azure Resource Manager]: ../azure-resource-manager/powershell-azure-resource-manager.md
[Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../azure-resource-manager/xplat-cli-azure-resource-manager.md
[Service Bus auth rule template]: https://github.com/Azure/azure-quickstart-templates/blob/master/301-servicebus-create-authrule-namespace-and-queue/
