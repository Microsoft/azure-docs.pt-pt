---
title: Criar recursos de ônibus de serviço Azure usando modelos
description: Use modelos de Gestor de Recursos Azure para automatizar a criação de recursos de Service Bus
documentationcenter: .net
author: spelluru
ms.topic: article
ms.tgt_pltfrm: dotnet
ms.date: 06/23/2020
ms.author: spelluru
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: df8a7fde9114f03521f0e57e072f81a867efcf39
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "89075261"
---
# <a name="create-service-bus-resources-using-azure-resource-manager-templates"></a>Criar recursos de ônibus de serviço usando modelos de gestor de recursos Azure

Este artigo descreve como criar e implementar recursos de Service Bus usando modelos de Gestor de Recursos Azure, PowerShell e o fornecedor de recursos de Service Bus.

Os modelos do Gestor de Recursos Azure ajudam-no a definir os recursos para implementar para uma solução e a especificar parâmetros e variáveis que lhe permitem inserir valores para diferentes ambientes. O modelo está escrito em JSON e consiste em expressões que pode usar para construir valores para a sua implantação. Para obter informações detalhadas sobre a escrita de modelos do Gestor de Recursos Azure e uma discussão sobre o formato do modelo, consulte [a estrutura e sintaxe dos modelos do Gestor de Recursos Azure](../azure-resource-manager/templates/template-syntax.md).

> [!NOTE]
> Os exemplos deste artigo mostram como usar o Azure Resource Manager para criar um espaço de nome de Service Bus e entidade de mensagens (fila). Para outros exemplos de modelos, visite a [galeria Azure Quickstart Templates][Azure Quickstart Templates gallery] e procure por **Service Bus**.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="service-bus-resource-manager-templates"></a>Modelos de gestor de recursos de ônibus de serviço

Estes modelos de Gestor de Recursos Azure do Service Bus Estão disponíveis para download e implementação. Clique nos seguintes links para mais detalhes sobre cada um, com links para os modelos no GitHub:

* [Criar um espaço de nomes do Service Bus](service-bus-resource-manager-namespace.md)
* [Criar um espaço de nomes de ônibus de serviço com fila](service-bus-resource-manager-namespace-queue.md)
* [Criar um espaço de nomes de ônibus de serviço com tópico e subscrição](service-bus-resource-manager-namespace-topic.md)
* [Criar um espaço de nome de ônibus de serviço com regra de fila e autorização](service-bus-resource-manager-namespace-auth-rule.md)
* [Criar um espaço de nomes de Service Bus com tópico, subscrição e regra](service-bus-resource-manager-namespace-topic-with-rule.md)

## <a name="deploy-with-powershell"></a>Implementar com o PowerShell

O procedimento a seguir descreve como usar o PowerShell para implementar um modelo de Gestor de Recursos Azure que cria um espaço de nome de serviço standard tier bus, e uma fila dentro desse espaço de nome. Este exemplo baseia-se no espaço de [nomes Create a Service Bus com](https://github.com/Azure/azure-quickstart-templates/tree/master/201-servicebus-create-queue) o modelo de fila. O fluxo de trabalho aproximado é o seguinte:

1. Instale a PowerShell.
2. Crie o modelo e (opcionalmente) um ficheiro de parâmetro.
3. Na PowerShell, faça login na sua conta Azure.
4. Criar um novo grupo de recursos se não existir.
5. Teste a colocação.
6. Se desejar, desa ajuste o modo de implantação.
7. Implemente o modelo.

Para obter informações completas sobre a implementação de modelos do Gestor de Recursos Azure, consulte [recursos de implementação com modelos do Gestor de Recursos Azure][Deploy resources with Azure Resource Manager templates].

### <a name="install-powershell"></a>Instalar o PowerShell

Instale a Azure PowerShell seguindo as instruções em [Começar com a Azure PowerShell](/powershell/azure/get-started-azureps).

### <a name="create-a-template"></a>Criar um modelo

Clone o repositório ou copie o modelo [de 201-servicebus-create-queue](https://github.com/Azure/azure-quickstart-templates/blob/master/201-servicebus-create-queue/azuredeploy.json) do GitHub:

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

### <a name="create-a-parameters-file-optional"></a>Criar um ficheiro de parâmetros (opcional)

Para utilizar um ficheiro de parâmetros opcionais, copie o ficheiro [de 201-servicebus-create-queue.](https://github.com/Azure/azure-quickstart-templates/blob/master/201-servicebus-create-queue/azuredeploy.parameters.json) Substitua o valor `serviceBusNamespaceName` do espaço de nomes do Service Bus que pretende criar nesta implementação e substitua o valor `serviceBusQueueName` da fila que pretende criar.

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

Para mais informações, consulte o artigo [Parâmetros.](../azure-resource-manager/templates/parameter-files.md)

### <a name="log-in-to-azure-and-set-the-azure-subscription"></a>Faça login no Azure e desemconfie da subscrição do Azure

A partir de um pedido PowerShell, executar o seguinte comando:

```powershell
Connect-AzAccount
```

É-lhe pedido que inicie sessão na sua conta Azure. Depois de iniciar sessão, execute o seguinte comando para visualizar as suas subscrições disponíveis:

```powershell
Get-AzSubscription
```

Este comando devolve uma lista de subscrições disponíveis do Azure. Escolha uma subscrição para a sessão atual executando o seguinte comando. `<YourSubscriptionId>`Substitua-o pela assinatura Azure que pretende utilizar:

```powershell
Set-AzContext -SubscriptionID <YourSubscriptionId>
```

### <a name="set-the-resource-group"></a>Definir o grupo de recursos

Se não tiver um grupo de recursos existente, crie um novo grupo de recursos com o comando **New-AzResourceGroup.** Forneça o nome do grupo de recursos e a localização que pretende utilizar. Por exemplo:

```powershell
New-AzResourceGroup -Name MyDemoRG -Location "West US"
```

Se for bem sucedido, é apresentado um resumo do novo grupo de recursos.

```powershell
ResourceGroupName : MyDemoRG
Location          : westus
ProvisioningState : Succeeded
Tags              :
ResourceId        : /subscriptions/<GUID>/resourceGroups/MyDemoRG
```

### <a name="test-the-deployment"></a>Testar a implementação

Valide a sua implantação executando o `Test-AzResourceGroupDeployment` cmdlet. Ao testar a implementação, forneça parâmetros exatamente como faria ao executar a implementação.

```powershell
Test-AzResourceGroupDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json
```

### <a name="create-the-deployment"></a>Criar a implantação

Para criar a nova implantação, executar o `New-AzResourceGroupDeployment` cmdlet e fornecer os parâmetros necessários quando solicitado. Os parâmetros incluem um nome para a sua implementação, o nome do seu grupo de recursos, e o caminho ou URL para o ficheiro do modelo. Se o parâmetro **Modo** não for especificado, é utilizado o valor predefinido de **Incremental.** Para obter mais informações, consulte [implementações incrementais e completas.](../azure-resource-manager/templates/deployment-modes.md)

O seguinte comando solicita-lhe os três parâmetros na janela PowerShell:

```powershell
New-AzResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json
```

Para especificar um ficheiro de parâmetros, utilize o seguinte comando:

```powershell
New-AzResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json -TemplateParameterFile <path to parameters file>\azuredeploy.parameters.json
```

Também pode utilizar parâmetros de linha quando executar o cmdlet de implantação. O comando é o seguinte:

```powershell
New-AzResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json -parameterName "parameterValue"
```

Para executar uma implementação [completa,](../azure-resource-manager/templates/deployment-modes.md) desa ajuste o parâmetro **de modo** para **completar:**

```powershell
New-AzResourceGroupDeployment -Name MyDemoDeployment -Mode Complete -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json
```

### <a name="verify-the-deployment"></a>Verificar a implementação
Se os recursos forem utilizados com sucesso, é apresentado um resumo da implantação na janela PowerShell:

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
Já viu o fluxo básico de trabalho e comandos para a implementação de um modelo de Gestor de Recursos Azure. Para obter informações mais detalhadas, visite os seguintes links:

* [Visão geral do Gestor de Recursos Azure][Azure Resource Manager overview]
* [Implementar recursos com modelos do Resource Manager e o Azure PowerShell][Deploy resources with Azure Resource Manager templates]
* [Modelos de gestor de recursos Azure](../azure-resource-manager/templates/template-syntax.md)
* [Tipos de recursos Microsoft.ServiceBus](/azure/templates/microsoft.servicebus/allversions)

[Azure Resource Manager overview]: ../azure-resource-manager/management/overview.md
[Deploy resources with Azure Resource Manager templates]: ../azure-resource-manager/templates/deploy-powershell.md
[Azure Quickstart Templates gallery]: https://azure.microsoft.com/documentation/templates/?term=service+bus
