---
title: Criar recursos do Azure Service Bus com modelos do Resource Manager | Documentos da Microsoft
description: Utilizar modelos Azure Resource Manager para automatizar a criação de recursos do Service Bus
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
ms.openlocfilehash: 4fa9026405789a6a90bbb9213cc54346aa8374c8
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57845407"
---
# <a name="create-service-bus-resources-using-azure-resource-manager-templates"></a>Criar recursos do Service Bus com modelos Azure Resource Manager

Este artigo descreve como criar e implementar recursos do Service Bus com modelos do Azure Resource Manager, PowerShell e o fornecedor de recursos do Service Bus.

Os modelos do Resource Manager do Azure ajudam a definir os recursos para implementar uma solução de e para especificar os parâmetros e variáveis que permitem-lhe introduzir os valores para diferentes ambientes. O modelo é escrito em JSON e consiste em expressões que pode utilizar para construir valores para a sua implementação. Para obter informações detalhadas sobre como escrever modelos Azure Resource Manager e uma discussão sobre o formato de modelo, consulte [estrutura e a sintaxe de modelos Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md).

> [!NOTE]
> Os exemplos neste artigo mostram como utilizar o Azure Resource Manager para criar um espaço de nomes do Service Bus e a entidade de mensagens (fila). Para outros exemplos de modelo, visite o [Galeria de modelos de início rápido do Azure] [ Azure Quickstart Templates gallery] e procure **do Service Bus**.
>
>

## <a name="service-bus-resource-manager-templates"></a>Modelos do Resource Manager do Service Bus

Estes modelos do Gestor de recursos do Service Bus do Azure estão disponíveis para download e implantação. Clique nas ligações seguintes para obter detalhes sobre cada uma delas, com links para os modelos no GitHub:

* [Criar um espaço de nomes do Service Bus](service-bus-resource-manager-namespace.md)
* [Criar um espaço de nomes do Service Bus com a fila](service-bus-resource-manager-namespace-queue.md)
* [Criar um espaço de nomes do Service Bus com o tópico e uma subscrição](service-bus-resource-manager-namespace-topic.md)
* [Criar um espaço de nomes do Service Bus com a regra de autorização e fila](service-bus-resource-manager-namespace-auth-rule.md)
* [Criar um espaço de nomes do Service Bus com o tópico, subscrição e regra](service-bus-resource-manager-namespace-topic-with-rule.md)

## <a name="deploy-with-powershell"></a>Implementar com o PowerShell

O procedimento seguinte descreve como utilizar o PowerShell para implementar um modelo do Azure Resource Manager que cria um espaço de nomes do Service Bus do escalão Standard e uma fila dentro desse namespace. Este exemplo se baseia a [criar um espaço de nomes do Service Bus com a fila](https://github.com/Azure/azure-quickstart-templates/tree/master/201-servicebus-create-queue) modelo. O fluxo de trabalho aproximado é o seguinte:

1. Instale o PowerShell.
2. Crie o modelo e (opcionalmente) um ficheiro de parâmetros.
3. No PowerShell, inicie sessão na sua conta do Azure.
4. Se não existir, crie um novo grupo de recursos.
5. Teste a implantação.
6. Se assim o desejar, defina o modo de implementação.
7. Implemente o modelo.

Para obter informações completas sobre a implementação de modelos Azure Resource Manager, consulte [implementar recursos com modelos Azure Resource Manager][Deploy resources with Azure Resource Manager templates].

### <a name="install-powershell"></a>Instalar o PowerShell

Instalar o Azure PowerShell, seguindo as instruções em [introdução ao Azure PowerShell](/powershell/azure/get-started-azureps).

### <a name="create-a-template"></a>Criar um modelo

Clonar o repositório ou copiar o [201-servicebus-criar-fila](https://github.com/Azure/azure-quickstart-templates/blob/master/201-servicebus-create-queue/azuredeploy.json) modelo a partir do GitHub:

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

Para utilizar um ficheiro de parâmetros opcionais, copie os [201-servicebus-criar-fila](https://github.com/Azure/azure-quickstart-templates/blob/master/201-servicebus-create-queue/azuredeploy.parameters.json) ficheiro. Substitua o valor de `serviceBusNamespaceName` com o nome do espaço de nomes do Service Bus que pretende criar nesta implementação e substitua o valor do `serviceBusQueueName` com o nome da fila que pretende criar.

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

Para obter mais informações, consulte a [parâmetros](../azure-resource-manager/resource-group-template-deploy.md#parameter-files) artigo.

### <a name="log-in-to-azure-and-set-the-azure-subscription"></a>Inicie sessão no Azure e definir a subscrição do Azure

A partir de uma linha de comandos do PowerShell, execute o seguinte comando:

```powershell
Connect-AzureRmAccount
```

São-lhe pedido para iniciar sessão na sua conta do Azure. Após fazer logon, execute o seguinte comando para ver as subscrições disponíveis:

```powershell
Get-AzureRMSubscription
```

Este comando devolve uma lista de subscrições do Azure disponíveis. Escolha uma subscrição para a sessão atual ao executar o comando seguinte. Substitua `<YourSubscriptionId>` com o GUID da subscrição do Azure que pretende utilizar:

```powershell
Set-AzureRmContext -SubscriptionID <YourSubscriptionId>
```

### <a name="set-the-resource-group"></a>Definir o grupo de recursos

Se não tiver um recurso existente de grupo, criar um novo grupo de recursos com o **New-AzureRmResourceGroup** comando. Forneça o nome do grupo de recursos e localização que pretende utilizar. Por exemplo:

```powershell
New-AzureRmResourceGroup -Name MyDemoRG -Location "West US"
```

Se tiver êxito, é apresentado um resumo do novo grupo de recursos.

```powershell
ResourceGroupName : MyDemoRG
Location          : westus
ProvisioningState : Succeeded
Tags              :
ResourceId        : /subscriptions/<GUID>/resourceGroups/MyDemoRG
```

### <a name="test-the-deployment"></a>Testar a implementação

Validar a sua implementação ao executar o `Test-AzureRmResourceGroupDeployment` cmdlet. Ao testar a implementação, fornece os parâmetros exatamente como faria quando executar a implantação.

```powershell
Test-AzureRmResourceGroupDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json
```

### <a name="create-the-deployment"></a>Criar a implementação

Para criar a nova implementação, execute o `New-AzureRmResourceGroupDeployment` cmdlet e forneça os parâmetros necessários, quando lhe for pedido. Os parâmetros incluem um nome para a sua implementação, o nome do seu grupo de recursos e o caminho ou URL para o ficheiro de modelo. Se o **modo** parâmetro não for especificado, o valor predefinido de **Incremental** é utilizado. Para obter mais informações, consulte [Incremental e completas implementações](../azure-resource-manager/deployment-modes.md).

O seguinte comando pede-lhe os três parâmetros na janela do PowerShell:

```powershell
New-AzureRmResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json
```

Para especificar um ficheiro de parâmetros em vez disso, utilize o seguinte comando:

```powershell
New-AzureRmResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json -TemplateParameterFile <path to parameters file>\azuredeploy.parameters.json
```

Também pode utilizar parâmetros inline ao executar o cmdlet de implementação. O comando é o seguinte:

```powershell
New-AzureRmResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json -parameterName "parameterValue"
```

Para executar uma [concluída](../azure-resource-manager/deployment-modes.md) implantação, defina o **modo** parâmetro **concluída**:

```powershell
New-AzureRmResourceGroupDeployment -Name MyDemoDeployment -Mode Complete -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json
```

### <a name="verify-the-deployment"></a>Verificar a implementação
Se os recursos são implementados com êxito, é apresentado um resumo da implementação na janela do PowerShell:

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

## <a name="next-steps"></a>Passos Seguintes
Agora já viu os comandos para implementar um modelo Azure Resource Manager e o fluxo de trabalho básico. Para obter informações mais detalhadas, visite as seguintes ligações:

* [Descrição geral do Azure Resource Manager][Azure Resource Manager overview]
* [Implementar recursos com modelos do Resource Manager e o Azure PowerShell][Deploy resources with Azure Resource Manager templates]
* [Criar modelos do Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md)
* [Tipos de recursos do servicebus](/azure/templates/microsoft.servicebus/allversions)

[Azure Resource Manager overview]: ../azure-resource-manager/resource-group-overview.md
[Deploy resources with Azure Resource Manager templates]: ../azure-resource-manager/resource-group-template-deploy.md
[Azure Quickstart Templates gallery]: https://azure.microsoft.com/documentation/templates/?term=service+bus
