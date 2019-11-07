---
title: 'Início rápido: criar fila e namespace do barramento de serviço do Azure usando o modelo de Azure Resource Manager'
description: 'Início rápido: criar um namespace e uma fila do barramento de serviço usando Azure Resource Manager modelo'
services: service-bus-messaging
documentationcenter: .net
author: spelluru
manager: timlt
editor: ''
ms.assetid: a6bfb5fd-7b98-4588-8aa1-9d5f91b599b6
ms.service: service-bus-messaging
ms.devlang: tbd
ms.topic: quickstart
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 11/05/2019
ms.author: spelluru
ms.openlocfilehash: 40aeff225cfa6d499ebdfae6736e0684b2a27244
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/07/2019
ms.locfileid: "73718792"
---
# <a name="quickstart-create-a-service-bus-namespace-and-a-queue-using-an-azure-resource-manager-template"></a>Início rápido: criar um namespace e uma fila do barramento de serviço usando um modelo de Azure Resource Manager

Este artigo mostra como usar um modelo de Azure Resource Manager que cria um namespace do barramento de serviço e uma fila dentro desse namespace. O artigo explica como especificar quais recursos são implantados e como definir parâmetros que são especificados quando a implantação é executada. Pode utilizar este modelo para as suas próprias implementações ou personalizá-lo para satisfazer as suas necessidades.

Para obter mais informações sobre como criar modelos, consulte [criação de modelos de Azure Resource Manager][Authoring Azure Resource Manager templates].

Para obter o modelo completo, consulte o [modelo de namespace e fila do barramento de serviço][Service Bus namespace and queue template] no github.

> [!NOTE]
> Os modelos de Azure Resource Manager a seguir estão disponíveis para download e implantação.
> 
> * [Criar um namespace do barramento de serviço com a regra de fila e autorização](service-bus-resource-manager-namespace-auth-rule.md)
> * [Criar um namespace do barramento de serviço com tópico e assinatura](service-bus-resource-manager-namespace-topic.md)
> * [Criar um namespace do barramento de serviço](service-bus-resource-manager-namespace.md)
> * [Criar um namespace do barramento de serviço com tópico, assinatura e regra](service-bus-resource-manager-namespace-topic-with-rule.md)
> 
> Para verificar os modelos mais recentes, visite a Galeria de [modelos de início rápido do Azure][Azure Quickstart Templates] e pesquise por **barramento de serviço**.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="what-will-you-deploy"></a>O que irá implementar?

Com esse modelo, você implanta um namespace do barramento de serviço com uma fila.

As [filas do barramento de serviço](service-bus-queues-topics-subscriptions.md#queues) oferecem entrega de mensagem PEPS (primeiro a entrar, primeiro a sair) para um ou mais consumidores concorrentes.

Para executar automaticamente a implementação, clique no seguinte botão:

[![Implementar no Azure](./media/service-bus-resource-manager-namespace-queue/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-servicebus-create-queue%2Fazuredeploy.json)

## <a name="parameters"></a>Parâmetros

Com o Azure Resource Manager, define parâmetros para os valores que pretende especificar quando o modelo é implementado. O modelo inclui uma seção chamada `Parameters` que contém todos os valores de parâmetro. Você deve definir um parâmetro para esses valores que irão variar com base no projeto que você está implantando ou com base no ambiente no qual você está implantando. Não defina parâmetros para valores que sempre permanecerão os mesmos. Cada valor de parâmetro é utilizado no modelo para definir os recursos que são implementados.

O modelo define os seguintes parâmetros.

### <a name="servicebusnamespacename"></a>serviceBusNamespaceName
O nome do namespace do barramento de serviço a ser criado.

```json
"serviceBusNamespaceName": {
"type": "string",
"metadata": { 
    "description": "Name of the Service Bus namespace" 
    }
}
```

### <a name="servicebusqueuename"></a>serviceBusQueueName
O nome da fila criada no namespace do barramento de serviço.

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
Cria um namespace padrão do barramento de serviço do tipo **mensagens**, com uma fila.

```json
{
    "resources": [{
        "apiVersion": "2017-04-01",
        "name": "[parameters('serviceBusNamespaceName')]",
        "type": "Microsoft.ServiceBus/namespaces",
        "location": "[parameters('location')]",
        "sku": {
            "name": "Standard"
        },
        "properties": {},
        "resources": [{
            "apiVersion": "[variables('sbVersion')]",
            "name": "[parameters('serviceBusQueueName')]",
            "type": "Queues",
            "dependsOn": [
                "[concat('Microsoft.ServiceBus/namespaces/', parameters('serviceBusNamespaceName'))]"
            ],
            "properties": {
                "path": "[parameters('serviceBusQueueName')]"
            }
        }]
    }]
}
```

Para sintaxe e propriedades JSON, consulte [namespaces](/azure/templates/microsoft.servicebus/namespaces) e [filas](/azure/templates/microsoft.servicebus/namespaces/queues).

## <a name="commands-to-run-deployment"></a>Comandos para executar a implementação
[!INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

## <a name="powershell"></a>PowerShell

```powershell
New-AzResourceGroupDeployment -ResourceGroupName \<resource-group-name\> -TemplateFile <https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-servicebus-create-queue/azuredeploy.json>
```

## <a name="azure-cli"></a>CLI do Azure

```azurecli
azure config mode arm

azure group deployment create \<my-resource-group\> \<my-deployment-name\> --template-uri <https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-servicebus-create-queue/azuredeploy.json>
```

## <a name="next-steps"></a>Passos seguintes
Consulte o tópico a seguir que mostra como criar uma regra de autorização para o namespace/fila: [criar uma regra de autorização do barramento de serviço para namespace e fila usando um modelo de Azure Resource Manager](service-bus-resource-manager-namespace-auth-rule.md)

Saiba como gerenciar esses recursos exibindo estes artigos:

* [Gerir o Service Bus com o PowerShell](service-bus-manage-with-ps.md)
* [Gerenciar recursos do barramento de serviço com o Gerenciador do barramento de serviço](https://github.com/paolosalvatori/ServiceBusExplorer/releases)

[Authoring Azure Resource Manager templates]: ../azure-resource-manager/resource-group-authoring-templates.md
[Service Bus namespace and queue template]: https://github.com/Azure/azure-quickstart-templates/blob/master/201-servicebus-create-queue/
[Azure Quickstart Templates]: https://azure.microsoft.com/documentation/templates/?term=service+bus
[Learn more about Service Bus queues]: service-bus-queues-topics-subscriptions.md
[Using Azure PowerShell with Azure Resource Manager]: ../azure-resource-manager/powershell-azure-resource-manager.md
[Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../azure-resource-manager/xplat-cli-azure-resource-manager.md
