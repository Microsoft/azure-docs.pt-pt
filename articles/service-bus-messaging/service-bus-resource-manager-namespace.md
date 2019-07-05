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
ms.date: 06/21/2019
ms.author: spelluru
ms.openlocfilehash: 4162775153a48dc8ea28e06f7c99f9927b9c602a
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67444761"
---
# <a name="create-a-service-bus-namespace-by-using-an-azure-resource-manager-template"></a>Criar um espaço de nomes do Service Bus com um modelo Azure Resource Manager

Saiba como implementar um modelo do Azure Resource Manager para criar um espaço de nomes do Service Bus. Pode utilizar este modelo para as suas próprias implementações ou personalizá-lo para satisfazer as suas necessidades. Para obter mais informações sobre a criação de modelos, consulte [documentação do Azure Resource Manager](/azure/azure-resource-manager/).

Os seguintes modelos também estão disponíveis para a criação de espaços de nomes do Service Bus:

* [Criar um espaço de nomes do Service Bus com a fila](./service-bus-resource-manager-namespace-queue.md)
* [Criar um espaço de nomes do Service Bus com o tópico e uma subscrição](./service-bus-resource-manager-namespace-topic.md)
* [Criar um espaço de nomes do Service Bus com a regra de autorização e fila](./service-bus-resource-manager-namespace-auth-rule.md)
* [Criar um espaço de nomes do Service Bus com o tópico, subscrição e regra](./service-bus-resource-manager-namespace-topic-with-rule.md)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="create-a-service-bus-namespace"></a>Criar um espaço de nomes do service bus

Neste início rápido, vai utilizar um [modelo Resource Manager existente](https://github.com/Azure/azure-quickstart-templates/blob/master/101-servicebus-create-namespace/azuredeploy.json) partir [modelos de início rápido do Azure](https://azure.microsoft.com/resources/templates/):

[!code-json[create-azure-service-bus-namespace](~/quickstart-templates/101-servicebus-create-namespace/azuredeploy.json)]

Para obter mais exemplos de modelos, veja [modelos de início rápido do Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Servicebus&pageNumber=1&sort=Popular).

Para criar um espaço de nomes do barramento de serviço ao implementar um modelo:

1. Selecione **experimente** do seguinte bloco de código e, em seguida, siga as instruções para iniciar sessão no Azure Cloud shell.

    ```azurepowershell-interactive
    $serviceBusNamespaceName = Read-Host -Prompt "Enter a name for the service bus namespace to be created"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
    $resourceGroupName = "${serviceBusNamespaceName}rg"
    $templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-servicebus-create-namespace/azuredeploy.json"

    New-AzResourceGroup -Name $resourceGroupName -Location $location
    New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri -serviceBusNamespaceName $serviceBusNamespaceName

    Write-Host "Press [ENTER] to continue ..."
    ```

    O nome de grupo de recursos é o nome de espaço de nomes do service bus com **rg** anexado.

2. Selecione **cópia** para copiar o script do PowerShell.
3. O console do shell com o botão direito e, em seguida, selecione **colar**.

Demora alguns minutos para criar um hub de eventos.

## <a name="verify-the-deployment"></a>Verificar a implementação

Para ver o espaço de nomes do barramento de serviço implementado, pode abrir o grupo de recursos a partir do portal do Azure ou utilize o seguinte script do PowerShell do Azure. Se o Cloud shell ainda está aberto, não precisa de cópia/executado as primeira e segunda linhas do seguinte script.

```azurepowershell-interactive
$serviceBusNamespaceName = Read-Host -Prompt "Enter the same service bus namespace name used earlier"
$resourceGroupName = "${serviceBusNamespaceName}rg"

Get-AzServiceBusNamespace -ResourceGroupName $resourceGroupName -Name $serviceBusNamespaceName

Write-Host "Press [ENTER] to continue ..."
```

O Azure PowerShell é utilizado para implementar o modelo neste tutorial. Para outros métodos de implementação do modelo, consulte:

* [Com o portal do Azure](../azure-resource-manager/resource-group-template-deploy-portal.md).
* [Ao utilizar a CLI do Azure](../azure-resource-manager/resource-group-template-deploy-cli.md).
* [Ao utilizar a REST API](../azure-resource-manager/resource-group-template-deploy-rest.md).

## <a name="clean-up-resources"></a>Limpar recursos

Quando os recursos do Azure já não forem necessários, limpe os recursos implementados ao eliminar o grupo de recursos. Se o Cloud shell ainda está aberto, não precisa de cópia/executado as primeira e segunda linhas do seguinte script.

```azurepowershell-interactive
$serviceBusNamespaceName = Read-Host -Prompt "Enter the same service bus namespace name used earlier"
$resourceGroupName = "${serviceBusNamespaceName}rg"

Remove-AzResourceGroup -ResourceGroupName $resourceGroupName

Write-Host "Press [ENTER] to continue ..."
```

## <a name="next-steps"></a>Passos Seguintes

Neste artigo, criou um espaço de nomes do Service Bus. Consulte os outros guias de introdução para saber como criar filas, tópicos/subscrições e utilizá-los:

* [Introdução às filas do Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Introdução aos tópicos do Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)
