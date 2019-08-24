---
title: Criar uma regra de autorização do barramento de serviço usando o modelo Azure Resource Manager | Microsoft Docs
description: Criar uma regra de autorização do barramento de serviço para namespace e fila usando Azure Resource Manager modelo
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
ms.openlocfilehash: 8769979fe3e5107e4ca788f65ff1e721b266776b
ms.sourcegitcommit: dcf3e03ef228fcbdaf0c83ae1ec2ba996a4b1892
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/23/2019
ms.locfileid: "70013067"
---
# <a name="create-a-service-bus-authorization-rule-for-namespace-and-queue-using-an-azure-resource-manager-template"></a>Criar uma regra de autorização do barramento de serviço para namespace e fila usando um modelo de Azure Resource Manager

Este artigo mostra como usar um modelo de Azure Resource Manager que cria uma [regra de autorização](service-bus-authentication-and-authorization.md#shared-access-signature) para um namespace e uma fila do barramento de serviço. O artigo explica como especificar quais recursos são implantados e como definir parâmetros que são especificados quando a implantação é executada. Pode utilizar este modelo para as suas próprias implementações ou personalizá-lo para satisfazer as suas necessidades.

Para obter mais informações sobre como criar modelos, consulte [criação de modelos de Azure Resource Manager][Authoring Azure Resource Manager templates].

Para obter o modelo completo, consulte o [modelo de regra de autorização do barramento de serviço][Service Bus auth rule template] no github.

> [!NOTE]
> Os modelos de Azure Resource Manager a seguir estão disponíveis para download e implantação.
> 
> * [Criar um namespace do barramento de serviço](service-bus-resource-manager-namespace.md)
> * [Criar um namespace do barramento de serviço com fila](service-bus-resource-manager-namespace-queue.md)
> * [Criar um namespace do barramento de serviço com tópico e assinatura](service-bus-resource-manager-namespace-topic.md)
> * [Criar um namespace do barramento de serviço com tópico, assinatura e regra](service-bus-resource-manager-namespace-topic-with-rule.md)
> 
> Para verificar os modelos mais recentes, visite a Galeria de [modelos de início rápido do Azure][Azure Quickstart Templates] e pesquise por **barramento de serviço**.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="what-will-you-deploy"></a>O que irá implementar?

Com esse modelo, você implanta uma regra de autorização do barramento de serviço para um namespace e uma entidade de mensagens (nesse caso, uma fila).

Este modelo usa [SAS (assinatura de acesso compartilhado)](service-bus-sas.md) para autenticação. A SAS permite que os aplicativos se autentiquem no barramento de serviço usando uma chave de acesso configurada no namespace ou na entidade de mensagens (fila ou tópico) à qual direitos específicos estão associados. Você pode usar essa chave para gerar um token SAS que os clientes podem, por sua vez, usar para autenticar no barramento de serviço.

Para executar automaticamente a implementação, clique no seguinte botão:

[![Implementar no Azure](./media/service-bus-resource-manager-namespace-auth-rule/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F301-servicebus-create-authrule-namespace-and-queue%2Fazuredeploy.json)

## <a name="parameters"></a>Parâmetros

Com o Azure Resource Manager, define parâmetros para os valores que pretende especificar quando o modelo é implementado. O modelo inclui uma seção chamada `Parameters` que contém todos os valores de parâmetro. Você deve definir um parâmetro para esses valores que irão variar com base no projeto que você está implantando ou com base no ambiente no qual você está implantando. Não defina parâmetros para valores que sempre permanecerão os mesmos. Cada valor de parâmetro é utilizado no modelo para definir os recursos que são implementados.

O modelo define os seguintes parâmetros.

### <a name="servicebusnamespacename"></a>serviceBusNamespaceName
O nome do namespace do barramento de serviço a ser criado.

```json
"serviceBusNamespaceName": {
"type": "string"
}
```

### <a name="namespaceauthorizationrulename"></a>namespaceAuthorizationRuleName
O nome da regra de autorização para o namespace.

```json
"namespaceAuthorizationRuleName ": {
"type": "string"
}
```

### <a name="servicebusqueuename"></a>serviceBusQueueName
O nome da fila no namespace do barramento de serviço.

```json
"serviceBusQueueName": {
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
Cria um namespace padrão do barramento de serviço do tipo **mensagens**e uma regra de autorização do barramento de serviço para o namespace e a entidade.

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

Para sintaxe e propriedades JSON, consulte [namespaces](/azure/templates/microsoft.servicebus/namespaces), [filas](/azure/templates/microsoft.servicebus/namespaces/queues)e [AuthorizationRules](/azure/templates/microsoft.servicebus/namespaces/authorizationrules).

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
Agora que você criou e implantou recursos usando Azure Resource Manager, saiba como gerenciar esses recursos exibindo estes artigos:

* [Gerir o Service Bus com o PowerShell](service-bus-powershell-how-to-provision.md)
* [Gerenciar recursos do barramento de serviço com o Gerenciador do barramento de serviço](https://github.com/paolosalvatori/ServiceBusExplorer/releases)
* [Autenticação e autorização do barramento de serviço](service-bus-authentication-and-authorization.md)

[Authoring Azure Resource Manager templates]: ../azure-resource-manager/resource-group-authoring-templates.md
[Azure Quickstart Templates]: https://azure.microsoft.com/documentation/templates/?term=service+bus
[Using Azure PowerShell with Azure Resource Manager]: ../azure-resource-manager/powershell-azure-resource-manager.md
[Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../azure-resource-manager/xplat-cli-azure-resource-manager.md
[Service Bus auth rule template]: https://github.com/Azure/azure-quickstart-templates/blob/master/301-servicebus-create-authrule-namespace-and-queue/
