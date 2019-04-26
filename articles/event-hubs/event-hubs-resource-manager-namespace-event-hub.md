---
title: Criar um hub de eventos com o grupo de consumidores - Event Hubs do Azure | Documentos da Microsoft
description: Criar um espaço de nomes de Hubs de eventos com um hub de eventos e um grupo de consumidores através de modelos do Azure Resource Manager
services: event-hubs
documentationcenter: .net
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.assetid: 28bb4591-1fd7-444f-a327-4e67e8878798
ms.service: event-hubs
ms.devlang: tbd
ms.topic: article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 10/16/2018
ms.author: shvija
ms.openlocfilehash: d5dc65dc225d11a996d9b9d3c329151a17321fb6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60343499"
---
# <a name="quickstart-create-an-event-hub-using-azure-resource-manager-template"></a>Início rápido: Criar um hub de eventos com o modelo Azure Resource Manager
Os Hubs de Eventos do Azure são uma plataforma de fluxo de Macrodados e um serviço de ingestão de eventos capaz de receber e processar milhões de eventos por segundo. Os Hubs de Eventos podem processar e armazenar eventos, dados ou telemetria produzidos por dispositivos e software distribuído. Os dados enviados para um hub de eventos podem ser transformados e armazenados em qualquer fornecedor de análise em tempo real ou adaptadores de armazenamento/criação de batches. Para uma descrição geral detalhada dos Hubs de Eventos, veja [Descrição geral dos Hubs de Eventos](event-hubs-about.md) e [Funcionalidades dos Hubs de Eventos](event-hubs-features.md).

Neste início rápido, vai criar um hub de eventos com um modelo Azure Resource Manager. Utilize um modelo Azure Resource Manager para criar um espaço de nomes do tipo [os Hubs de eventos](event-hubs-what-is-event-hubs.md), com um hub de eventos e um grupo de consumidores. O artigo mostra como definir quais recursos são implementados e como definir os parâmetros que são especificados quando a implementação é executada. Pode utilizar este modelo para as suas próprias implementações ou personalizá-lo para satisfazer as suas necessidades. Para obter informações sobre a criação de modelos, consulte [modelos Authoring Azure Resource Manager][Authoring Azure Resource Manager templates]. Para a sintaxe JSON e propriedades para utilizar num modelo, consulte [tipos de recursos do eventhub](/azure/templates/microsoft.eventhub/allversions).

> [!NOTE]
> Para o modelo completo, consulte a [modelo de grupo de hub e o consumidor de eventos] [ Event Hub and consumer group template] no GitHub. Este modelo criado um grupo de consumidores, além de um espaço de nomes do hub de eventos e um hub de eventos. Para verificar os modelos mais recentes, visite a galeria [Modelos de Início Rápido do Azure][Azure Quickstart Templates] e procure Hubs de Eventos.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Para concluir este início rápido, precisa de uma subscrição do Azure. Se não tiver uma, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

Se quiser usar **do Azure PowerShell** para implementar o modelo do Resource Manager [instalar o Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps).

Se quiser usar **CLI do Azure** para implementar o modelo do Resource Manager [instalar a CLI do Azure]( /cli/azure/install-azure-cli).

## <a name="create-the-resource-manager-template-json"></a>Criar o modelo do Resource Manager JSON
Crie um ficheiro JSON com o nome MyEventHub.json com o seguinte conteúdo e guarde-o para uma pasta (por exemplo: C:\EventHubsQuickstarts\ResourceManagerTemplate).

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "eventhub-namespace-name": {
            "type": "String"
        },
        "eventhub_name": {
            "type": "String"
        }
    },
    "resources": [
        {
            "type": "Microsoft.EventHub/namespaces",
            "sku": {
                "name": "Standard",
                "tier": "Standard",
                "capacity": 1
            },
            "name": "[parameters('eventhub-namespace-name')]",
            "apiVersion": "2017-04-01",
            "location": "East US",
            "tags": {},
            "scale": null,
            "properties": {
                "isAutoInflateEnabled": false,
                "maximumThroughputUnits": 0
            },
            "dependsOn": []
        },
        {
            "type": "Microsoft.EventHub/namespaces/eventhubs",
            "name": "[concat(parameters('eventhub-namespace-name'), '/', parameters('eventhub_name'))]",
            "apiVersion": "2017-04-01",
            "location": "East US",
            "scale": null,
            "properties": {
                "messageRetentionInDays": 7,
                "partitionCount": 1,
                "status": "Active"
            },
            "dependsOn": [
                "[resourceId('Microsoft.EventHub/namespaces', parameters('eventhub-namespace-name'))]"
            ]
        }
    ]
}
```

## <a name="create-the-parameters-json"></a>Criar os parâmetros JSON
Crie um ficheiro JSON denominado MyEventHub Parameters. JSON que contém os parâmetros para o modelo Azure Resource Manager. 

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
        "eventhub-namespace-name": {
            "value": "<specify a name for the event hub namespace>"
        },
        "eventhub_name": {
            "value": "<Specify a name for the event hub in the namespace>"
        }
  }
}
```



## <a name="use-azure-powershell-to-deploy-the-template"></a>Utilizar o Azure PowerShell para implementar o modelo

### <a name="sign-in-to-azure"></a>Iniciar sessão no Azure
1. Inicie o Azure PowerShell

2. Execute o seguinte comando para iniciar sessão no Azure:

   ```azurepowershell
   Login-AzAccount
   ```
3. Se tiver emita os seguintes comandos para definir o contexto de subscrição atual:

   ```azurepowershell
   Select-AzSubscription -SubscriptionName "<YourSubscriptionName>" 
   ```

### <a name="provision-resources"></a>Recursos de aprovisionamento
Para implementar/aprovisionar os recursos com o Azure PowerShell, mude para a pasta de C:\EventHubsQuickStart\ARM\, execute os seguintes comandos:

> [!IMPORTANT]
> Especifique um nome para o grupo de recursos do Azure como um valor para $resourceGroupName antes de executar os comandos. 

```azurepowershell
$resourceGroupName = "<Specify a name for the Azure resource group>"

# Create an Azure resource group
New-AzResourceGroup $resourceGroupName -location 'East US'

# Deploy the Resource Manager template. Specify the names of deployment itself, resource group, JSON file for the template, JSON file for parameters
New-AzResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName $resourceGroupName -TemplateFile MyEventHub.json -TemplateParameterFile MyEventHub-Parameters.json
```

## <a name="use-azure-cli-to-deploy-the-template"></a>Utilizar a CLI do Azure para implementar o modelo

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Os passos seguintes não são necessários se estiver a executar comandos no Cloud Shell. Se estiver a executar a CLI localmente, siga os seguintes passos para iniciar sessão no Azure e definir a sua subscrição atual:

Execute o seguinte comando para iniciar sessão no Azure:

```azurecli
az login
```

Defina o contexto da subscrição atual. Substitua `MyAzureSub` pelo nome da subscrição do Azure que quer utilizar:

```azurecli
az account set --subscription <Name of your Azure subscription>
``` 

### <a name="provision-resources"></a>Recursos de aprovisionamento
Para implementar os recursos com a CLI do Azure, mude para a pasta de C:\EventHubsQuickStart\ARM\ e execute os seguintes comandos:

> [!IMPORTANT]
> Especifique um nome para o grupo de recursos do Azure no grupo az criar comando. .

```azurecli
# Create an Azure resource group
az group create --name <YourResourceGroupName> --location eastus

# # Deploy the Resource Manager template. Specify the names of resource group, deployment, JSON file for the template, JSON file for parameters
az group deployment create --name <Specify a name for the deployment> --resource-group <YourResourceGroupName> --template-file MyEventHub.json --parameters @MyEventHub-Parameters.json
```

Parabéns! Utilizou o modelo Azure Resource Manager para criar um espaço de nomes de Hubs de eventos e um hub de eventos dentro desse namespace.

## <a name="next-steps"></a>Passos Seguintes

Neste artigo, criou o espaço de nomes dos Hubs de Eventos e utilizou exemplos de aplicações para enviar e receber eventos do hub de eventos. Para obter instruções passo a passo enviar eventos para (ou) receber eventos de um hub de eventos, consulte a **enviar e receber eventos** tutoriais: 

- [.NET Core](event-hubs-dotnet-standard-getstarted-send.md)
- [.NET Framework](event-hubs-dotnet-framework-getstarted-send.md)
- [Java](event-hubs-java-get-started-send.md)
- [Python](event-hubs-python-get-started-send.md)
- [Node.js](event-hubs-node-get-started-send.md)
- [Go](event-hubs-go-get-started-send.md)
- [C (apenas para o envio)](event-hubs-c-getstarted-send.md)
- [O Apache Storm (reecive apenas)](event-hubs-storm-getstarted-receive.md)


[3]: ./media/event-hubs-quickstart-powershell/sender1.png
[4]: ./media/event-hubs-quickstart-powershell/receiver1.png
[5]: ./media/event-hubs-quickstart-powershell/metrics.png

[Authoring Azure Resource Manager templates]: ../azure-resource-manager/resource-group-authoring-templates.md
[Azure Quickstart Templates]:  https://azure.microsoft.com/documentation/templates/?term=event+hubs
[Using Azure PowerShell with Azure Resource Manager]: ../powershell-azure-resource-manager.md
[Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../xplat-cli-azure-resource-manager.md
[Event hub and consumer group template]: https://github.com/Azure/azure-quickstart-templates/blob/master/201-event-hubs-create-event-hub-and-consumer-group/
