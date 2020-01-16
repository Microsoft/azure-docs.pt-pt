---
title: Criar o namespace de mensagens do barramento de serviço usando o modelo Azure Resource Manager | Microsoft Docs
description: Usar Azure Resource Manager modelo para criar um namespace de mensagens do barramento de serviço
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
ms.openlocfilehash: 8280bb057edea5697b8fa3248ddf755376f62c49
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/15/2020
ms.locfileid: "75978257"
---
# <a name="create-a-service-bus-namespace-by-using-an-azure-resource-manager-template"></a>Criar um namespace do barramento de serviço usando um modelo de Azure Resource Manager

Saiba como implantar um modelo de Azure Resource Manager para criar um namespace do barramento de serviço. Pode utilizar este modelo para as suas próprias implementações ou personalizá-lo para satisfazer as suas necessidades. Para obter mais informações sobre como criar modelos, consulte a [documentação do Azure Resource Manager](/azure/azure-resource-manager/).

Os modelos a seguir também estão disponíveis para a criação de namespaces do barramento de serviço:

* [Criar um namespace do barramento de serviço com fila](./service-bus-resource-manager-namespace-queue.md)
* [Criar um namespace do barramento de serviço com tópico e assinatura](./service-bus-resource-manager-namespace-topic.md)
* [Criar um namespace do barramento de serviço com a regra de fila e autorização](./service-bus-resource-manager-namespace-auth-rule.md)
* [Criar um namespace do barramento de serviço com tópico, assinatura e regra](./service-bus-resource-manager-namespace-topic-with-rule.md)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="create-a-service-bus-namespace"></a>Criar um namespace do barramento de serviço

Neste guia de início rápido, você usa um [modelo existente do Resource Manager](https://github.com/Azure/azure-quickstart-templates/blob/master/101-servicebus-create-namespace/azuredeploy.json) nos [modelos de início rápido do Azure](https://azure.microsoft.com/resources/templates/):

[!code-json[create-azure-service-bus-namespace](~/quickstart-templates/101-servicebus-create-namespace/azuredeploy.json)]

Para encontrar mais exemplos de modelo, consulte [modelos de início rápido do Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Servicebus&pageNumber=1&sort=Popular).

Para criar um namespace do barramento de serviço implantando um modelo:

1. Selecione **Experimente** no seguinte bloco de código e siga as instruções para entrar no Azure cloud Shell.

    ```azurepowershell-interactive
    $serviceBusNamespaceName = Read-Host -Prompt "Enter a name for the service bus namespace to be created"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
    $resourceGroupName = "${serviceBusNamespaceName}rg"
    $templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-servicebus-create-namespace/azuredeploy.json"

    New-AzResourceGroup -Name $resourceGroupName -Location $location
    New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri -serviceBusNamespaceName $serviceBusNamespaceName

    Write-Host "Press [ENTER] to continue ..."
    ```

    O nome do grupo de recursos é o nome do namespace do barramento de serviço com **RG** anexado.

2. Selecione **cópia** para copiar o script do PowerShell.
3. Clique com o botão direito do mouse no console do Shell e selecione **colar**.

Demora alguns minutos para criar um hub de eventos.

## <a name="verify-the-deployment"></a>Verificar a implementação

Para ver o namespace do barramento de serviço implantado, você pode abrir o grupo de recursos no portal do Azure ou usar o script de Azure PowerShell a seguir. Se o Cloud Shell ainda estiver aberto, você não precisará copiar/executar a primeira e segunda linhas do script a seguir.

```azurepowershell-interactive
$serviceBusNamespaceName = Read-Host -Prompt "Enter the same service bus namespace name used earlier"
$resourceGroupName = "${serviceBusNamespaceName}rg"

Get-AzServiceBusNamespace -ResourceGroupName $resourceGroupName -Name $serviceBusNamespaceName

Write-Host "Press [ENTER] to continue ..."
```

Azure PowerShell é usado para implantar o modelo neste tutorial. Para outros métodos de implantação de modelo, consulte:

* [Usando o portal do Azure](../azure-resource-manager/templates/deploy-portal.md).
* [Usando CLI do Azure](../azure-resource-manager/templates/deploy-cli.md).
* [Usando a API REST](../azure-resource-manager/templates/deploy-rest.md).

## <a name="clean-up-resources"></a>Limpar recursos

Quando os recursos do Azure já não forem necessários, limpe os recursos implementados ao eliminar o grupo de recursos. Se o Cloud Shell ainda estiver aberto, você não precisará copiar/executar a primeira e segunda linhas do script a seguir.

```azurepowershell-interactive
$serviceBusNamespaceName = Read-Host -Prompt "Enter the same service bus namespace name used earlier"
$resourceGroupName = "${serviceBusNamespaceName}rg"

Remove-AzResourceGroup -ResourceGroupName $resourceGroupName

Write-Host "Press [ENTER] to continue ..."
```

## <a name="next-steps"></a>Passos seguintes

Neste artigo, você criou um namespace do barramento de serviço. Consulte os outros guias de início rápido para saber como criar filas, tópicos/assinaturas e usá-los:

* [Introdução às filas do Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Introdução aos tópicos do barramento de serviço](service-bus-dotnet-how-to-use-topics-subscriptions.md)
