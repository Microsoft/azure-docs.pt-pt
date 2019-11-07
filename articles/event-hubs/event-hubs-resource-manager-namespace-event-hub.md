---
title: 'Início rápido: criar um hub de eventos com grupo de consumidores-hubs de eventos do Azure'
description: 'Início rápido: criar um namespace de hubs de eventos com um hub de eventos e um grupo de consumidores usando modelos de Azure Resource Manager'
services: event-hubs
documentationcenter: .net
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.assetid: 28bb4591-1fd7-444f-a327-4e67e8878798
ms.service: event-hubs
ms.devlang: tbd
ms.topic: quickstart
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 11/05/2019
ms.author: shvija
ms.openlocfilehash: b262f5f4122256a55c005002c4323205a5b564f0
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/07/2019
ms.locfileid: "73717656"
---
# <a name="quickstart-create-an-event-hub-by-using-an-azure-resource-manager-template"></a>Início rápido: criar um hub de eventos usando um modelo de Azure Resource Manager

Os Hubs de Eventos do Azure são uma plataforma de fluxo de Macrodados e um serviço de ingestão de eventos capaz de receber e processar milhões de eventos por segundo. Os Hubs de Eventos podem processar e armazenar eventos, dados ou telemetria produzidos por dispositivos e software distribuído. Os dados enviados para um hub de eventos podem ser transformados e armazenados em qualquer fornecedor de análise em tempo real ou adaptadores de armazenamento/criação de batches. Para uma descrição geral detalhada dos Hubs de Eventos, veja [Descrição geral dos Hubs de Eventos](event-hubs-about.md) e [Funcionalidades dos Hubs de Eventos](event-hubs-features.md).

Neste guia de início rápido, você cria um hub de eventos usando um [modelo de Azure Resource Manager](../azure-resource-manager/resource-group-overview.md). Você implanta um modelo de Azure Resource Manager para criar um namespace do tipo [hubs de eventos](event-hubs-what-is-event-hubs.md), com um hub de eventos. O artigo mostra como definir quais recursos são implantados e como definir parâmetros que são especificados quando a implantação é executada. Pode utilizar este modelo para as suas próprias implementações ou personalizá-lo para satisfazer as suas necessidades. Para obter informações sobre como criar modelos, consulte Criando [modelos de Azure Resource Manager][Authoring Azure Resource Manager templates]. Para obter a sintaxe JSON e as propriedades a serem usadas em um modelo, consulte [tipos de recurso Microsoft. EventHub](/azure/templates/microsoft.eventhub/allversions).

Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="create-an-event-hub"></a>Criar um hub de eventos

Neste guia de início rápido, você usa um [modelo de início rápido existente](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-eventhubs-create-namespace-and-eventhub/azuredeploy.json):

[!code-json[create-azure-event-hub-namespace](~/quickstart-templates/101-eventhubs-create-namespace-and-eventhub/azuredeploy.json)]

Para encontrar mais exemplos de modelo, consulte [modelos de início rápido do Azure](https://azure.microsoft.com/resources/templates/?term=eventhub&pageNumber=1&sort=Popular).

Para implantar o modelo:

1. Selecione **Experimente** no seguinte bloco de código e siga as instruções para entrar no Azure cloud Shell.

   ```azurepowershell-interactive
   $projectName = Read-Host -Prompt "Enter a project name that is used for generating resource names"
   $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
   $resourceGroupName = "${projectName}rg"
   $templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-eventhubs-create-namespace-and-eventhub/azuredeploy.json"

   New-AzResourceGroup -Name $resourceGroupName -Location $location
   New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri -projectName $projectName

   Write-Host "Press [ENTER] to continue ..."
   ```

   Demora alguns minutos para criar um hub de eventos.

1. Selecione **copiar** para copiar o script do PowerShell.
1. Clique com o botão direito do mouse no console do Shell e selecione **colar**.

## <a name="verify-the-deployment"></a>Verificar a implementação

Para verificar a implantação, você pode abrir o grupo de recursos do [portal do Azure](https://portal.azure.com)ou usar o script de Azure PowerShell a seguir.  Se o Cloud Shell ainda estiver aberto, você não precisará copiar/executar a primeira linha (Read-Host).

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter the same project name that you used in the last procedure"
$resourceGroupName = "${projectName}rg"
$namespaceName = "${projectName}ns"

Get-AzEventHub -ResourceGroupName $resourceGroupName -Namespace $namespaceName

Write-Host "Press [ENTER] to continue ..."
```

## <a name="clean-up-resources"></a>Limpar recursos

Quando os recursos do Azure já não forem necessários, limpe os recursos implementados ao eliminar o grupo de recursos. Se o Cloud Shell ainda estiver aberto, você não precisará copiar/executar a primeira linha (Read-Host).

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter the same project name that you used in the last procedure"
$resourceGroupName = "${projectName}rg"

Remove-AzResourceGroup -ResourceGroupName $resourceGroupName

Write-Host "Press [ENTER] to continue ..."
```

## <a name="next-steps"></a>Passos seguintes

Neste artigo, você criou um namespace de hubs de eventos e um hub de eventos no namespace. Para obter instruções detalhadas sobre como enviar eventos para (ou) receber eventos de um hub de eventos, consulte os tutoriais de **eventos de envio e recebimento** :

- [.NET Core](event-hubs-dotnet-standard-getstarted-send.md)
- [.NET Framework](event-hubs-dotnet-framework-getstarted-send.md)
- [Java](event-hubs-java-get-started-send.md)
- [Python](event-hubs-python-get-started-send.md)
- [Node.js](event-hubs-node-get-started-send.md)
- [Go](event-hubs-go-get-started-send.md)
- [C (somente enviar)](event-hubs-c-getstarted-send.md)
- [Apache Storm (somente reecive)](event-hubs-storm-getstarted-receive.md)


[3]: ./media/event-hubs-quickstart-powershell/sender1.png
[4]: ./media/event-hubs-quickstart-powershell/receiver1.png
[5]: ./media/event-hubs-quickstart-powershell/metrics.png

[Authoring Azure Resource Manager templates]: ../azure-resource-manager/resource-group-authoring-templates.md
[Azure Quickstart Templates]:  https://azure.microsoft.com/documentation/templates/?term=event+hubs
[Using Azure PowerShell with Azure Resource Manager]: ../powershell-azure-resource-manager.md
[Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../xplat-cli-azure-resource-manager.md
[Event hub and consumer group template]: https://github.com/Azure/azure-quickstart-templates/blob/master/201-event-hubs-create-event-hub-and-consumer-group/
