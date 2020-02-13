---
title: 'Quickstart: Criar um hub de eventos com grupo de consumidores - Azure Event Hubs'
description: 'Quickstart: Criar um espaço de nome de Hubs de Eventos com um hub de eventos e um grupo de consumidores usando modelos de Gestor de Recursos Azure'
services: event-hubs
documentationcenter: .net
author: spelluru
editor: ''
ms.assetid: 28bb4591-1fd7-444f-a327-4e67e8878798
ms.service: event-hubs
ms.devlang: tbd
ms.topic: quickstart
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 02/11/2020
ms.author: spelluru
ms.openlocfilehash: 88cd29af75239f0ad79eb78b5ff8e106c3b2ee56
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/12/2020
ms.locfileid: "77163080"
---
# <a name="quickstart-create-an-event-hub-by-using-an-azure-resource-manager-template"></a>Quickstart: Criar um hub de eventousando um modelo de Gestor de Recursos Azure

Os Hubs de Eventos do Azure são uma plataforma de fluxo de Macrodados e um serviço de ingestão de eventos capaz de receber e processar milhões de eventos por segundo. Os Hubs de Eventos podem processar e armazenar eventos, dados ou telemetria produzidos por dispositivos e software distribuído. Os dados enviados para um hub de eventos podem ser transformados e armazenados em qualquer fornecedor de análise em tempo real ou adaptadores de armazenamento/criação de batches. Para uma descrição geral detalhada dos Hubs de Eventos, veja [Descrição geral dos Hubs de Eventos](event-hubs-about.md) e [Funcionalidades dos Hubs de Eventos](event-hubs-features.md).

Neste arranque rápido, você cria um hub de eventousando um modelo de Gestor de [Recursos Azure](../azure-resource-manager/management/overview.md). Você implementa um modelo de Gestor de Recursos Azure para criar um espaço de nome de hubs de [eventos](event-hubs-what-is-event-hubs.md)tipo, com um hub de evento. O artigo mostra como definir quais recursos são implementados e como definir os parâmetros que são especificados quando a implementação é executada. Pode utilizar este modelo para as suas próprias implementações ou personalizá-lo para satisfazer as suas necessidades. Para obter informações sobre a criação de modelos, consulte [os modelos de Gestor de Recursos Do Azure.][Authoring Azure Resource Manager templates] Para que a sintaxe e as propriedades da JSON utilizem num modelo, consulte os tipos de [recursos Microsoft.EventHub](/azure/templates/microsoft.eventhub/allversions).

Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="create-an-event-hub"></a>Criar um hub de eventos

Neste arranque rápido, você usa um [modelo de arranque rápido existente:](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-eventhubs-create-namespace-and-eventhub/azuredeploy.json)

[!code-json[create-azure-event-hub-namespace](~/quickstart-templates/101-eventhubs-create-namespace-and-eventhub/azuredeploy.json)]

Para encontrar mais amostras de modelo, consulte [os modelos Azure Quickstart](https://azure.microsoft.com/resources/templates/?term=eventhub&pageNumber=1&sort=Popular).

Para implantar o modelo:

1. Selecione **Experimente** a partir do seguinte bloco de código e, em seguida, siga as instruções para iniciar sessão na concha Azure Cloud.

   ```azurepowershell-interactive
   $projectName = Read-Host -Prompt "Enter a project name that is used for generating resource names"
   $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
   $resourceGroupName = "${projectName}rg"
   $templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-eventhubs-create-namespace-and-eventhub/azuredeploy.json"

   New-AzResourceGroup -Name $resourceGroupName -Location $location
   New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri -projectName $projectName

   Write-Host "Press [ENTER] to continue ..."
   ```

   Leva alguns momentos para criar um centro de eventos.

1. Selecione **Copiar** para copiar o script PowerShell.
1. Clique na consola da concha e, em seguida, selecione **Paste**.

## <a name="verify-the-deployment"></a>Verificar a implementação

Para verificar a implementação, pode abrir o grupo de recursos a partir do [portal Azure,](https://portal.azure.com)ou utilizar o seguinte script Azure PowerShell.  Se a casca cloud ainda estiver aberta, não precisa de copiar/executar a primeira linha (Read-Host).

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter the same project name that you used in the last procedure"
$resourceGroupName = "${projectName}rg"
$namespaceName = "${projectName}ns"

Get-AzEventHub -ResourceGroupName $resourceGroupName -Namespace $namespaceName

Write-Host "Press [ENTER] to continue ..."
```

## <a name="clean-up-resources"></a>Limpar recursos

Quando os recursos do Azure já não forem necessários, limpe os recursos implementados ao eliminar o grupo de recursos. Se a casca cloud ainda estiver aberta, não precisa de copiar/executar a primeira linha (Read-Host).

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter the same project name that you used in the last procedure"
$resourceGroupName = "${projectName}rg"

Remove-AzResourceGroup -ResourceGroupName $resourceGroupName

Write-Host "Press [ENTER] to continue ..."
```

## <a name="next-steps"></a>Passos seguintes

Neste artigo, criou um espaço de nome sem nome do Event Hubs e um hub de eventos no espaço de nome. Para instruções passo a passo para enviar eventos para (ou) receber eventos de um centro de eventos, consulte o **Envio e receba** tutoriais de eventos:

- [.NET Core](get-started-dotnet-standard-send-v2.md)
- [Java](get-started-java-send-v2.md)
- [python](get-started-python-send-v2.md)
- [JavaScript](get-started-java-send-v2.md)
- [Go](event-hubs-go-get-started-send.md)
- [C (apenas enviar)](event-hubs-c-getstarted-send.md)
- [Tempestade Apache (receber apenas)](event-hubs-storm-getstarted-receive.md)


[3]: ./media/event-hubs-quickstart-powershell/sender1.png
[4]: ./media/event-hubs-quickstart-powershell/receiver1.png
[5]: ./media/event-hubs-quickstart-powershell/metrics.png

[Authoring Azure Resource Manager templates]: ../azure-resource-manager/templates/template-syntax.md
[Azure Quickstart Templates]:  https://azure.microsoft.com/documentation/templates/?term=event+hubs
[Using Azure PowerShell with Azure Resource Manager]: ../powershell-azure-resource-manager.md
[Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../xplat-cli-azure-resource-manager.md
[Event hub and consumer group template]: https://github.com/Azure/azure-quickstart-templates/blob/master/201-event-hubs-create-event-hub-and-consumer-group/
