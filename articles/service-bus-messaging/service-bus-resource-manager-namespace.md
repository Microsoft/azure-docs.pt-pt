---
title: Crie um espaço de nome do ônibus de serviço Azure usando o modelo
description: Use o modelo do Gestor de Recursos Azure para criar um espaço de nome de mensagens de ônibus de serviço
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
ms.date: 06/21/2019
ms.author: spelluru
ms.openlocfilehash: 5febdd63ab6f854ca3244f8449f6f715a75e735f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76264480"
---
# <a name="create-a-service-bus-namespace-by-using-an-azure-resource-manager-template"></a>Crie um espaço de nome de ônibus de serviço usando um modelo de Gestor de Recursos Azure

Aprenda a implementar um modelo de Gestor de Recursos Azure para criar um espaço de nome de ônibus de serviço. Pode utilizar este modelo para as suas próprias implementações ou personalizá-lo para satisfazer as suas necessidades. Para obter mais informações sobre a criação de modelos, consulte a [documentação do Gestor de Recursos do Azure.](/azure/azure-resource-manager/)

Os seguintes modelos também estão disponíveis para criar espaços de nome sinuoso de ônibus de serviço:

* [Crie um espaço de nome de ônibus de serviço com fila](./service-bus-resource-manager-namespace-queue.md)
* [Crie um espaço de nome de ônibus de serviço com tópico e subscrição](./service-bus-resource-manager-namespace-topic.md)
* [Criar um espaço de nome de ônibus de serviço com a regra de fila e autorização](./service-bus-resource-manager-namespace-auth-rule.md)
* [Criar um espaço de nome de ônibus de serviço com tópico, subscrição e regra](./service-bus-resource-manager-namespace-topic-with-rule.md)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Se não tiver uma subscrição Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="create-a-service-bus-namespace"></a>Criar um espaço de nome de ônibus de serviço

Neste arranque rápido, você usa um [modelo de Gestor](https://github.com/Azure/azure-quickstart-templates/blob/master/101-servicebus-create-namespace/azuredeploy.json) de Recursos existente a partir de [modelos de quickstart Azure:](https://azure.microsoft.com/resources/templates/)

[!code-json[create-azure-service-bus-namespace](~/quickstart-templates/101-servicebus-create-namespace/azuredeploy.json)]

Para encontrar mais amostras de modelo, consulte [os modelos Azure Quickstart](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Servicebus&pageNumber=1&sort=Popular).

Para criar um espaço de nome de autocarro de serviço, implementando um modelo:

1. Selecione **Experimente** a partir do seguinte bloco de código e, em seguida, siga as instruções para iniciar sessão na concha Azure Cloud.

    ```azurepowershell-interactive
    $serviceBusNamespaceName = Read-Host -Prompt "Enter a name for the service bus namespace to be created"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
    $resourceGroupName = "${serviceBusNamespaceName}rg"
    $templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-servicebus-create-namespace/azuredeploy.json"

    New-AzResourceGroup -Name $resourceGroupName -Location $location
    New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri -serviceBusNamespaceName $serviceBusNamespaceName

    Write-Host "Press [ENTER] to continue ..."
    ```

    O nome do grupo de recursos é o nome do espaço de nome do ônibus de serviço com **rg** anexado.

2. Selecione **Copiar** para copiar o script PowerShell.
3. Clique na consola da concha e, em seguida, selecione **Paste**.

Leva alguns momentos para criar um centro de eventos.

## <a name="verify-the-deployment"></a>Verificar a implementação

Para ver o espaço de nome de ônibus de serviço implantado, pode abrir o grupo de recursos a partir do portal Azure ou utilizar o seguinte script Azure PowerShell. Se a casca cloud ainda estiver aberta, não precisa de copiar/executar as primeira e segunda linhas do seguinte script.

```azurepowershell-interactive
$serviceBusNamespaceName = Read-Host -Prompt "Enter the same service bus namespace name used earlier"
$resourceGroupName = "${serviceBusNamespaceName}rg"

Get-AzServiceBusNamespace -ResourceGroupName $resourceGroupName -Name $serviceBusNamespaceName

Write-Host "Press [ENTER] to continue ..."
```

O Azure PowerShell é usado para implantar o modelo neste tutorial. Para outros métodos de implementação do modelo, consulte:

* [Utilizando o portal Azure.](../azure-resource-manager/templates/deploy-portal.md)
* [Utilizando o Azure CLI](../azure-resource-manager/templates/deploy-cli.md).
* [Utilizando a Rest API](../azure-resource-manager/templates/deploy-rest.md).

## <a name="clean-up-resources"></a>Limpar recursos

Quando os recursos do Azure já não forem necessários, limpe os recursos implementados ao eliminar o grupo de recursos. Se a casca cloud ainda estiver aberta, não precisa de copiar/executar as primeira e segunda linhas do seguinte script.

```azurepowershell-interactive
$serviceBusNamespaceName = Read-Host -Prompt "Enter the same service bus namespace name used earlier"
$resourceGroupName = "${serviceBusNamespaceName}rg"

Remove-AzResourceGroup -ResourceGroupName $resourceGroupName

Write-Host "Press [ENTER] to continue ..."
```

## <a name="next-steps"></a>Passos seguintes

Neste artigo, criou um espaço de nome service Bus. Veja os outros quickstarts para aprender a criar filas, tópicos/subscrições e usá-los:

* [Começar com as filas de ônibus de serviço](service-bus-dotnet-get-started-with-queues.md)
* [Introdução aos tópicos do Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)
