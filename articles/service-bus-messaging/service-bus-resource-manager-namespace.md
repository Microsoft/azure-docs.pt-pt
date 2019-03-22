---
title: Criar o espaço de nomes mensagens do Service Bus com o modelo Azure Resource Manager | Documentos da Microsoft
description: Utilizar o modelo Azure Resource Manager para criar um espaço de nomes de mensagens do Service Bus
services: service-bus-messaging
documentationcenter: .net
author: spelluru
manager: timlt
editor: ''
ms.assetid: dc0d6482-6344-4cef-8644-d4573639f5e4
ms.service: service-bus-messaging
ms.devlang: tbd
ms.topic: article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 01/23/2019
ms.author: spelluru
ms.openlocfilehash: a79565661ae11e70364d64503b3b11bdeabeabdb
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57899762"
---
# <a name="create-a-service-bus-namespace-using-an-azure-resource-manager-template"></a>Criar um espaço de nomes do Service Bus com um modelo Azure Resource Manager
Neste início rápido, vai criar um modelo do Azure Resource Manager que cria um espaço de nomes do Service Bus do tipo **Messaging** com um **padrão** SKU. O artigo também define os parâmetros que são especificados para a execução da implantação. Pode utilizar este modelo para as suas próprias implementações ou personalizá-lo para satisfazer as suas necessidades. Para obter mais informações sobre a criação de modelos, consulte [Authoring Azure Resource Manager templates][Authoring Azure Resource Manager templates] (Criar modelos do Azure Resource Manager). Para o modelo completo, consulte a [modelo de espaço de nomes do Service Bus] [ Service Bus namespace template] no GitHub.

> [!NOTE]
> Os seguintes modelos do Azure Resource Manager estão disponíveis para download e implantação. 
> 
> * [Criar um espaço de nomes do Service Bus com a fila](service-bus-resource-manager-namespace-queue.md)
> * [Criar um espaço de nomes do Service Bus com o tópico e uma subscrição](service-bus-resource-manager-namespace-topic.md)
> * [Criar um espaço de nomes do Service Bus com a regra de autorização e fila](service-bus-resource-manager-namespace-auth-rule.md)
> * [Criar um espaço de nomes do Service Bus com o tópico, subscrição e regra](service-bus-resource-manager-namespace-topic-with-rule.md)
> 
> Para verificar os modelos mais recentes, visite o [modelos de início rápido do Azure] [ Azure Quickstart Templates] Galeria e pesquisa do Service Bus.

## <a name="quick-deployment"></a>Implementação rápida
Para executar o exemplo sem escrever qualquer JSON e executar o comando do PowerShell/CLI, selecione o botão seguinte:

[![Implementar no Azure](./media/service-bus-resource-manager-namespace/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-servicebus-create-namespace%2Fazuredeploy.json)

Para criar e implementar o modelo manualmente, percorra as secções seguintes neste artigo.

## <a name="prerequisites"></a>Pré-requisitos
Para concluir este início rápido, precisa de uma subscrição do Azure. Se não tiver uma, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

Se quiser usar **do Azure PowerShell** para implementar o modelo do Resource Manager [instalar o Azure PowerShell](https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps?view=azurermps-5.7.0).

Se quiser usar **CLI do Azure** para implementar o modelo do Resource Manager [instalar a CLI do Azure]( /cli/azure/install-azure-cli).

## <a name="create-the-resource-manager-template-json"></a>Criar o modelo do Resource Manager JSON 
Crie um ficheiro JSON com o nome **MyServiceBusNamespace.json** com o seguinte conteúdo: 

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "serviceBusNamespaceName": {
            "type": "string",
            "metadata": {
                "description": "Name of the Service Bus namespace"
            }
        },
        "serviceBusSku": {
            "type": "string",
            "allowedValues": [
                "Basic",
                "Standard",
                "Premium"
            ],
            "defaultValue": "Standard",
            "metadata": {
                "description": "The messaging tier for service Bus namespace"
            }
        },
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]",
            "metadata": {
                "description": "Location for all resources."
            }
        }
    },
    "resources": [
        {
            "apiVersion": "2017-04-01",
            "name": "[parameters('serviceBusNamespaceName')]",
            "type": "Microsoft.ServiceBus/namespaces",
            "location": "[parameters('location')]",
            "sku": {
                "name": "[parameters('serviceBusSku')]"
            }
        }
    ]
}
```

Este modelo cria um espaço de nomes do Service Bus standard. Para a sintaxe JSON e propriedades, consulte [espaços de nomes](/azure/templates/microsoft.servicebus/namespaces) referência de modelo.

## <a name="create-the-parameters-json"></a>Criar os parâmetros JSON
O modelo que criou no passo anterior tem uma seção chamada `Parameters`. Definir parâmetros para esses valores que variam com base no projeto estiver a implementar ou com base no ambiente de destino. Esse modelo define os seguintes parâmetros: **serviceBusNamespaceName**, **serviceBusSku**, e **localização**. Para saber mais sobre os SKUs do Service Bus, veja [SKUs de barramento de serviço](https://azure.microsoft.com/pricing/details/service-bus/) para criar.

Crie um ficheiro JSON com o nome **Parameters. JSON MyServiceBusNamespace** com o seguinte conteúdo: 

> [!NOTE] 
> Especifique um nome para o espaço de nomes do Service Bus. 


```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "serviceBusNamespaceName": {
      "value": "<Specify a name for the Service Bus namespace>"
    },
    "serviceBusSku": {
      "value": "Standard"
    },
    "location": {
        "value": "East US"
    }
  }
}
```


## <a name="use-azure-powershell-to-deploy-the-template"></a>Utilizar o Azure PowerShell para implementar o modelo

### <a name="sign-in-to-azure"></a>Iniciar sessão no Azure
1. Inicie o Azure PowerShell

2. Execute o seguinte comando para iniciar sessão no Azure:

   ```azurepowershell
   Login-AzureRmAccount
   ```
3. Se tiver emita os seguintes comandos para definir o contexto de subscrição atual:

   ```azurepowershell
   Select-AzureRmSubscription -SubscriptionName "<YourSubscriptionName>" 
   ```

### <a name="deploy-resources"></a>Implementar recursos
Para implementar os recursos com o Azure PowerShell, mude para a pasta onde guardou os ficheiros JSON e execute os seguintes comandos:

> [!IMPORTANT]
> Especifique um nome para o grupo de recursos do Azure como um valor para $resourceGroupName antes de executar os comandos. 

1. Declarar uma variável para o nome do grupo de recursos e especifique um valor para o mesmo. 

    ```azurepowershell
    $resourceGroupName = "<Specify a name for the Azure resource group>"
    ```
2. Criar um grupo de recursos do Azure.

    ```azurepowershell
    New-AzureRmResourceGroup $resourceGroupName -location 'East US'
    ```
3. Implemente o modelo do Resource Manager. Especifique os nomes de implantação propriamente dita, o grupo de recursos, o ficheiro JSON para o modelo, o ficheiro JSON para os parâmetros

    ```azurepowershell
    New-AzureRmResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName $resourceGroupName -TemplateFile MyServiceBusNamespace.json -TemplateParameterFile MyServiceBusNamespace-Parameters.json
    ```

## <a name="use-azure-cli-to-deploy-the-template"></a>Utilizar a CLI do Azure para implementar o modelo

### <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

1. Execute o seguinte comando para iniciar sessão no Azure:

    ```azurecli
    az login
    ```
2. Defina o contexto da subscrição atual. Substitua `MyAzureSub` pelo nome da subscrição do Azure que quer utilizar:

    ```azurecli
    az account set --subscription <Name of your Azure subscription>
    ``` 

### <a name="deploy-resources"></a>Implementar recursos
Para implementar os recursos com a CLI do Azure, mude para a pasta com ficheiros JSON e execute os seguintes comandos:

> [!IMPORTANT]
> Especifique um nome para o grupo de recursos do Azure no grupo az criar comando. .

1. Criar um grupo de recursos do Azure. 
    ```azurecli
    az group create --name <YourResourceGroupName> --location eastus
    ```

2. Implemente o modelo do Resource Manager. Especifique os nomes de grupo de recursos, implementação, ficheiro JSON para o modelo, ficheiro JSON para os parâmetros.

    ```azurecli
    az group deployment create --name <Specify a name for the deployment> --resource-group <YourResourceGroupName> --template-file MyServiceBusNamespace.json --parameters @MyServiceBusNamespace-Parameters.json
    ```

## <a name="next-steps"></a>Passos Seguintes
Neste artigo, criou um espaço de nomes do Service Bus. Consulte os outros guias de introdução para saber como criar filas, tópicos/subscrições e utilizá-los: 

- [Introdução às filas do Service Bus](service-bus-dotnet-get-started-with-queues.md)
- [Introdução aos tópicos do Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)

[Authoring Azure Resource Manager templates]: ../azure-resource-manager/resource-group-authoring-templates.md
[Service Bus namespace template]: https://github.com/Azure/azure-quickstart-templates/blob/master/101-servicebus-create-namespace/
[Azure Quickstart Templates]: https://azure.microsoft.com/documentation/templates/?term=service+bus
[Service Bus pricing and billing]: service-bus-pricing-billing.md
[Using Azure PowerShell with Azure Resource Manager]: ../azure-resource-manager/powershell-azure-resource-manager.md
[Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../azure-resource-manager/xplat-cli-azure-resource-manager.md
