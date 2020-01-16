---
title: Criar recursos do barramento de serviço do Azure usando modelos do Resource Manager | Microsoft Docs
description: Usar modelos de Azure Resource Manager para automatizar a criação de recursos do barramento de serviço
services: service-bus-messaging
documentationcenter: .net
author: spelluru
manager: timlt
editor: ''
ms.assetid: 24f6a207-0fa4-49cf-8a58-963f9e2fd655
ms.service: service-bus-messaging
ms.devlang: tbd
ms.topic: article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 09/11/2018
ms.author: spelluru
ms.openlocfilehash: d473cf2a79fb86028797205ee7885b51169b10bf
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/15/2020
ms.locfileid: "75978240"
---
# <a name="create-service-bus-resources-using-azure-resource-manager-templates"></a>Criar recursos do barramento de serviço usando modelos de Azure Resource Manager

Este artigo descreve como criar e implantar recursos do barramento de serviço usando modelos de Azure Resource Manager, o PowerShell e o provedor de recursos do barramento de serviço.

Os modelos de Azure Resource Manager ajudam a definir os recursos a serem implantados para uma solução e a especificar parâmetros e variáveis que permitem que você insira valores para ambientes diferentes. O modelo é escrito em JSON e consiste em expressões que você pode usar para construir valores para sua implantação. Para obter informações detalhadas sobre como escrever modelos de Azure Resource Manager e uma discussão sobre o formato de modelo, consulte [estrutura e sintaxe de modelos de Azure Resource Manager](../azure-resource-manager/templates/template-syntax.md).

> [!NOTE]
> Os exemplos neste artigo mostram como usar Azure Resource Manager para criar um namespace do barramento de serviço e uma entidade de mensagens (fila). Para obter outros exemplos de modelo, visite a [Galeria de modelos de início rápido do Azure][Azure Quickstart Templates gallery] e pesquise por **barramento de serviço**.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="service-bus-resource-manager-templates"></a>Modelos do Resource Manager do barramento de serviço

Esses modelos de Azure Resource Manager do barramento de serviço estão disponíveis para download e implantação. Clique nos links a seguir para obter detalhes sobre cada um deles, com links para os modelos no GitHub:

* [Criar um namespace do barramento de serviço](service-bus-resource-manager-namespace.md)
* [Criar um namespace do barramento de serviço com fila](service-bus-resource-manager-namespace-queue.md)
* [Criar um namespace do barramento de serviço com tópico e assinatura](service-bus-resource-manager-namespace-topic.md)
* [Criar um namespace do barramento de serviço com a regra de fila e autorização](service-bus-resource-manager-namespace-auth-rule.md)
* [Criar um namespace do barramento de serviço com tópico, assinatura e regra](service-bus-resource-manager-namespace-topic-with-rule.md)

## <a name="deploy-with-powershell"></a>Implementar com o Powershell

O procedimento a seguir descreve como usar o PowerShell para implantar um modelo de Azure Resource Manager que cria um namespace de barramento de serviço de camada padrão e uma fila dentro desse namespace. Este exemplo é baseado no modelo [criar um namespace do barramento de serviço com fila](https://github.com/Azure/azure-quickstart-templates/tree/master/201-servicebus-create-queue) . O fluxo de trabalho aproximado é o seguinte:

1. Instale o PowerShell.
2. Crie o modelo e (opcionalmente) um arquivo de parâmetro.
3. No PowerShell, faça logon em sua conta do Azure.
4. Crie um novo grupo de recursos se ele não existir.
5. Teste a implantação.
6. Se desejar, defina o modo de implantação.
7. Implante o modelo.

Para obter informações completas sobre a implantação de modelos de Azure Resource Manager, consulte [implantar recursos com modelos de Azure Resource Manager][Deploy resources with Azure Resource Manager templates].

### <a name="install-powershell"></a>Instalar PowerShell

Instale Azure PowerShell seguindo as instruções em [introdução ao Azure PowerShell](/powershell/azure/get-started-azureps).

### <a name="create-a-template"></a>Criar um modelo

Clone o repositório ou copie o modelo [201-ServiceBus-CREATE-QUEUE](https://github.com/Azure/azure-quickstart-templates/blob/master/201-servicebus-create-queue/azuredeploy.json) do github:

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
    "serviceBusQueueName": {
      "type": "string",
      "metadata": {
        "description": "Name of the Queue"
      }
    }
  },
  "variables": {
    "defaultSASKeyName": "RootManageSharedAccessKey",
    "authRuleResourceId": "[resourceId('Microsoft.ServiceBus/namespaces/authorizationRules', parameters('serviceBusNamespaceName'), variables('defaultSASKeyName'))]",
    "sbVersion": "2017-04-01"
  },
  "resources": [
    {
      "apiVersion": "2017-04-01",
      "name": "[parameters('serviceBusNamespaceName')]",
      "type": "Microsoft.ServiceBus/Namespaces",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Standard"
      },
      "properties": {},
      "resources": [
        {
          "apiVersion": "2017-04-01",
          "name": "[parameters('serviceBusQueueName')]",
          "type": "Queues",
          "dependsOn": [
            "[concat('Microsoft.ServiceBus/namespaces/', parameters('serviceBusNamespaceName'))]"
          ],
          "properties": {
            "lockDuration": "PT5M",
            "maxSizeInMegabytes": "1024",
            "requiresDuplicateDetection": "false",
            "requiresSession": "false",
            "defaultMessageTimeToLive": "P10675199DT2H48M5.4775807S",
            "deadLetteringOnMessageExpiration": "false",
            "duplicateDetectionHistoryTimeWindow": "PT10M",
            "maxDeliveryCount": "10",
            "autoDeleteOnIdle": "P10675199DT2H48M5.4775807S",
            "enablePartitioning": "false",
            "enableExpress": "false"
          }
        }
      ]
    }
  ],
  "outputs": {
    "NamespaceConnectionString": {
      "type": "string",
      "value": "[listkeys(variables('authRuleResourceId'), variables('sbVersion')).primaryConnectionString]"
    },
    "SharedAccessPolicyPrimaryKey": {
      "type": "string",
      "value": "[listkeys(variables('authRuleResourceId'), variables('sbVersion')).primaryKey]"
    }
  }
}
```

### <a name="create-a-parameters-file-optional"></a>Criar um arquivo de parâmetros (opcional)

Para usar um arquivo de parâmetros opcional, copie o arquivo [201-ServiceBus-CREATE-QUEUE](https://github.com/Azure/azure-quickstart-templates/blob/master/201-servicebus-create-queue/azuredeploy.parameters.json) . Substitua o valor de `serviceBusNamespaceName` pelo nome do namespace do barramento de serviço que você deseja criar nessa implantação e substitua o valor de `serviceBusQueueName` pelo nome da fila que você deseja criar.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "serviceBusNamespaceName": {
            "value": "<myNamespaceName>"
        },
        "serviceBusQueueName": {
            "value": "<myQueueName>"
        },
        "serviceBusApiVersion": {
            "value": "2017-04-01"
        }
    }
}
```

Para obter mais informações, consulte o artigo [parâmetros](../azure-resource-manager/templates/parameter-files.md) .

### <a name="log-in-to-azure-and-set-the-azure-subscription"></a>Faça logon no Azure e defina a assinatura do Azure

Em um prompt do PowerShell, execute o seguinte comando:

```powershell
Connect-AzAccount
```

Você será solicitado a fazer logon em sua conta do Azure. Depois de fazer logon, execute o seguinte comando para exibir suas assinaturas disponíveis:

```powershell
Get-AzSubscription
```

Esse comando retorna uma lista de assinaturas do Azure disponíveis. Escolha uma assinatura para a sessão atual executando o comando a seguir. Substitua `<YourSubscriptionId>` pelo GUID da assinatura do Azure que você deseja usar:

```powershell
Set-AzContext -SubscriptionID <YourSubscriptionId>
```

### <a name="set-the-resource-group"></a>Definir o grupo de recursos

Se você não tiver um grupo de recursos existente, crie um novo grupo de recursos com o comando **New-AzResourceGroup** . Forneça o nome do grupo de recursos e o local que você deseja usar. Por exemplo:

```powershell
New-AzResourceGroup -Name MyDemoRG -Location "West US"
```

Se for bem-sucedido, será exibido um resumo do novo grupo de recursos.

```powershell
ResourceGroupName : MyDemoRG
Location          : westus
ProvisioningState : Succeeded
Tags              :
ResourceId        : /subscriptions/<GUID>/resourceGroups/MyDemoRG
```

### <a name="test-the-deployment"></a>Testar a implementação

Valide sua implantação executando o cmdlet `Test-AzResourceGroupDeployment`. Ao testar a implantação, forneça parâmetros exatamente como você faria ao executar a implantação.

```powershell
Test-AzResourceGroupDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json
```

### <a name="create-the-deployment"></a>Criar a implantação

Para criar a nova implantação, execute o cmdlet `New-AzResourceGroupDeployment` e forneça os parâmetros necessários quando solicitado. Os parâmetros incluem um nome para sua implantação, o nome do seu grupo de recursos e o caminho ou a URL para o arquivo de modelo. Se o parâmetro **Mode** não for especificado, o valor padrão de **incremental** será usado. Para obter mais informações, consulte [implantações incrementais e completas](../azure-resource-manager/templates/deployment-modes.md).

O comando a seguir solicita os três parâmetros na janela do PowerShell:

```powershell
New-AzResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json
```

Para especificar um arquivo de parâmetros em vez disso, use o seguinte comando:

```powershell
New-AzResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json -TemplateParameterFile <path to parameters file>\azuredeploy.parameters.json
```

Você também pode usar parâmetros embutidos ao executar o cmdlet de implantação. O comando é o seguinte:

```powershell
New-AzResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json -parameterName "parameterValue"
```

Para executar uma implantação [completa](../azure-resource-manager/templates/deployment-modes.md) , defina o parâmetro de **modo** como **concluído**:

```powershell
New-AzResourceGroupDeployment -Name MyDemoDeployment -Mode Complete -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json
```

### <a name="verify-the-deployment"></a>Verificar a implementação
Se os recursos forem implantados com êxito, um resumo da implantação será exibido na janela do PowerShell:

```powershell
DeploymentName    : MyDemoDeployment
ResourceGroupName : MyDemoRG
ProvisioningState : Succeeded
Timestamp         : 4/19/2017 10:38:30 PM
Mode              : Incremental
TemplateLink      :
Parameters        :
                    Name             Type                       Value
                    ===============  =========================  ==========
                    serviceBusNamespaceName  String             <namespaceName>
                    serviceBusQueueName  String                 <queueName>
                    serviceBusApiVersion  String                2017-04-01

```

## <a name="next-steps"></a>Passos seguintes
Agora você viu o fluxo de trabalho básico e os comandos para implantar um modelo de Azure Resource Manager. Para obter informações mais detalhadas, visite os links a seguir:

* [Descrição geral do Azure Resource Manager][Azure Resource Manager overview]
* [Implementar recursos com modelos do Resource Manager e o Azure PowerShell][Deploy resources with Azure Resource Manager templates]
* [Criar modelos do Azure Resource Manager](../azure-resource-manager/templates/template-syntax.md)
* [Tipos de recurso Microsoft. ServiceBus](/azure/templates/microsoft.servicebus/allversions)

[Azure Resource Manager overview]: ../azure-resource-manager/management/overview.md
[Deploy resources with Azure Resource Manager templates]: ../azure-resource-manager/templates/deploy-powershell.md
[Azure Quickstart Templates gallery]: https://azure.microsoft.com/documentation/templates/?term=service+bus
