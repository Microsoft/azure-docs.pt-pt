---
title: Criar uma regra de autorização do Service Bus com o modelo Azure Resource Manager | Documentos da Microsoft
description: Criar uma regra de autorização do Service Bus para o espaço de nomes e a fila usando o modelo Azure Resource Manager
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
ms.date: 01/23/2019
ms.author: aschhab
ms.openlocfilehash: f2c82c8ff353889f06dfc1c2ff5c3f316013c54b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66171249"
---
# <a name="create-a-service-bus-authorization-rule-for-namespace-and-queue-using-an-azure-resource-manager-template"></a>Criar uma regra de autorização do Service Bus para o espaço de nomes e a fila usando um modelo Azure Resource Manager

Este artigo mostra como utilizar um modelo Azure Resource Manager que cria um [regra de autorização](service-bus-authentication-and-authorization.md#shared-access-signature-authentication) para um espaço de nomes do Service Bus e a fila. O artigo explica como especificar quais recursos são implementados e como definir os parâmetros que são especificados quando a implementação é executada. Pode utilizar este modelo para as suas próprias implementações ou personalizá-lo para satisfazer as suas necessidades.

Para obter mais informações sobre a criação de modelos, veja [modelos Authoring Azure Resource Manager][Authoring Azure Resource Manager templates].

Para o modelo completo, consulte a [modelo de regra de autorização do Service Bus] [ Service Bus auth rule template] no GitHub.

> [!NOTE]
> Os seguintes modelos do Azure Resource Manager estão disponíveis para download e implantação.
> 
> * [Criar um espaço de nomes do Service Bus](service-bus-resource-manager-namespace.md)
> * [Criar um espaço de nomes do Service Bus com a fila](service-bus-resource-manager-namespace-queue.md)
> * [Criar um espaço de nomes do Service Bus com o tópico e uma subscrição](service-bus-resource-manager-namespace-topic.md)
> * [Criar um espaço de nomes do Service Bus com o tópico, subscrição e regra](service-bus-resource-manager-namespace-topic-with-rule.md)
> 
> Para verificar os modelos mais recentes, visite o [modelos de início rápido do Azure] [ Azure Quickstart Templates] Galeria e procure **do Service Bus**.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="what-will-you-deploy"></a>O que irá implementar?

Com este modelo implementa uma regra de autorização do Service Bus para um espaço de nomes e a entidade de mensagens (neste caso, uma fila).

Este modelo utiliza [assinatura de acesso partilhado (SAS)](service-bus-sas.md) para autenticação. SAS permite que os aplicativos autenticar para o Service Bus com uma chave de acesso configurada no espaço de nomes ou sobre a entidade de mensagens (fila ou tópico) com as quais direitos específicos estão associados. Em seguida, pode utilizar esta chave para gerar um token SAS que os clientes por sua vez podem utilizar para autenticar para o Service Bus.

Para executar automaticamente a implementação, clique no seguinte botão:

[![Implementar no Azure](./media/service-bus-resource-manager-namespace-auth-rule/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F301-servicebus-create-authrule-namespace-and-queue%2Fazuredeploy.json)

## <a name="parameters"></a>Parâmetros

Com o Azure Resource Manager, define parâmetros para os valores que pretende especificar quando o modelo é implementado. O modelo inclui uma seção chamada `Parameters` que contém todos os valores de parâmetro. Deve definir um parâmetro para esses valores que variam com base no projeto que está a implementar ou com base no ambiente que esteja a implementar. Não defina parâmetros para valores que sempre continuará o mesmo. Cada valor de parâmetro é utilizado no modelo para definir os recursos que são implementados.

O modelo define os seguintes parâmetros.

### <a name="servicebusnamespacename"></a>serviceBusNamespaceName
O nome do espaço de nomes do Service Bus para criar.

```json
"serviceBusNamespaceName": {
"type": "string"
}
```

### <a name="namespaceauthorizationrulename"></a>namespaceAuthorizationRuleName
O nome da regra de autorização para o espaço de nomes.

```json
"namespaceAuthorizationRuleName ": {
"type": "string"
}
```

### <a name="servicebusqueuename"></a>serviceBusQueueName
O nome da fila no espaço de nomes do Service Bus.

```json
"serviceBusQueueName": {
"type": "string"
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
Cria um espaço de nomes do Service Bus padrão do tipo **Messaging**e uma regra de autorização do Service Bus para o espaço de nomes e a entidade.

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

Para a sintaxe JSON e propriedades, consulte [espaços de nomes](/azure/templates/microsoft.servicebus/namespaces), [filas](/azure/templates/microsoft.servicebus/namespaces/queues), e [AuthorizationRules](/azure/templates/microsoft.servicebus/namespaces/authorizationrules).

## <a name="commands-to-run-deployment"></a>Comandos para executar a implementação
[!INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

### <a name="powershell"></a>PowerShell
```powershell
New-AzResourceGroupDeployment -ResourceGroupName \<resource-group-name\> -TemplateFile <https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/301-servicebus-create-authrule-namespace-and-queue/azuredeploy.json>
```

## <a name="azure-cli"></a>CLI do Azure
```azurecli
azure config mode arm

azure group deployment create \<my-resource-group\> \<my-deployment-name\> --template-uri <https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/301-servicebus-create-authrule-namespace-and-queue/azuredeploy.json>
```

## <a name="next-steps"></a>Passos Seguintes
Agora que criou e implantou recursos com o Azure Resource Manager, saiba como gerir estes recursos ao visualizar estes artigos:

* [Gerir o Service Bus com o PowerShell](service-bus-powershell-how-to-provision.md)
* [Gerir recursos do Service Bus com o Explorador do Service Bus](https://github.com/paolosalvatori/ServiceBusExplorer/releases)
* [Barramento de serviço de autenticação e autorização](service-bus-authentication-and-authorization.md)

[Authoring Azure Resource Manager templates]: ../azure-resource-manager/resource-group-authoring-templates.md
[Azure Quickstart Templates]: https://azure.microsoft.com/documentation/templates/?term=service+bus
[Using Azure PowerShell with Azure Resource Manager]: ../azure-resource-manager/powershell-azure-resource-manager.md
[Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../azure-resource-manager/xplat-cli-azure-resource-manager.md
[Service Bus auth rule template]: https://github.com/Azure/azure-quickstart-templates/blob/master/301-servicebus-create-authrule-namespace-and-queue/
