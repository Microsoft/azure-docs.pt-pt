---
title: Crie um espaço de nome de ônibus de serviço Azure usando o modelo
description: Use o modelo de Gestor de Recursos Azure para criar um espaço de nome de mensagens de ônibus de serviço
documentationcenter: .net
author: spelluru
ms.topic: article
ms.tgt_pltfrm: dotnet
ms.date: 06/23/2020
ms.author: spelluru
ms.openlocfilehash: 1b7aafca331170100ce99c084a11c96c97df7781
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "88067397"
---
# <a name="create-a-service-bus-namespace-by-using-an-azure-resource-manager-template"></a>Crie um espaço de nome de ônibus de serviço usando um modelo de gestor de recursos Azure

Saiba como implementar um modelo de Gestor de Recursos Azure para criar um espaço de nomes de Service Bus. Pode utilizar este modelo para as suas próprias implementações ou personalizá-lo para satisfazer as suas necessidades. Para obter mais informações sobre a criação de modelos, consulte [a documentação do Gestor de Recursos Azure](../azure-resource-manager/index.yml).

Os seguintes modelos também estão disponíveis para criar espaços de nomes de Service Bus:

* [Criar um espaço de nomes de ônibus de serviço com fila](./service-bus-resource-manager-namespace-queue.md)
* [Criar um espaço de nomes de ônibus de serviço com tópico e subscrição](./service-bus-resource-manager-namespace-topic.md)
* [Criar um espaço de nome de ônibus de serviço com regra de fila e autorização](./service-bus-resource-manager-namespace-auth-rule.md)
* [Criar um espaço de nomes de Service Bus com tópico, subscrição e regra](./service-bus-resource-manager-namespace-topic-with-rule.md)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="create-a-service-bus-namespace"></a>Criar um espaço de nome de ônibus de serviço

Neste arranque rápido, você usa um [modelo de gestor de recursos existente](https://github.com/Azure/azure-quickstart-templates/blob/master/101-servicebus-create-namespace/azuredeploy.json) a partir de [modelos Azure Quickstart](https://azure.microsoft.com/resources/templates/):

[!code-json[create-azure-service-bus-namespace](~/quickstart-templates/101-servicebus-create-namespace/azuredeploy.json)]

Para encontrar mais amostras de modelo, consulte [os modelos Azure Quickstart](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Servicebus&pageNumber=1&sort=Popular).

Para criar um espaço de nome de autocarro de serviço, implementando um modelo:

1. Selecione **Experimente-o** a partir do bloco de códigos que se segue e, em seguida, siga as instruções para iniciar seduca na casca da Nuvem Azure.

    ```azurepowershell-interactive
    $serviceBusNamespaceName = Read-Host -Prompt "Enter a name for the service bus namespace to be created"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
    $resourceGroupName = "${serviceBusNamespaceName}rg"
    $templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-servicebus-create-namespace/azuredeploy.json"

    New-AzResourceGroup -Name $resourceGroupName -Location $location
    New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri -serviceBusNamespaceName $serviceBusNamespaceName

    Write-Host "Press [ENTER] to continue ..."
    ```

    O nome do grupo de recursos é o nome do espaço de serviço do autocarro com **rg** anexado.

2. Selecione **Copy** para copiar o script PowerShell.
3. Clique com o botão direito na consola da concha e, em seguida, **selecione Pasta**.

Leva alguns momentos para criar um centro de eventos.

## <a name="verify-the-deployment"></a>Verificar a implementação

Para ver o espaço de nome do ônibus de serviço implantado, pode abrir o grupo de recursos a partir do portal Azure ou utilizar o seguinte script Azure PowerShell. Se a camada cloud ainda estiver aberta, não precisa copiar/executar a primeira e segunda linhas do seguinte script.

```azurepowershell-interactive
$serviceBusNamespaceName = Read-Host -Prompt "Enter the same service bus namespace name used earlier"
$resourceGroupName = "${serviceBusNamespaceName}rg"

Get-AzServiceBusNamespace -ResourceGroupName $resourceGroupName -Name $serviceBusNamespaceName

Write-Host "Press [ENTER] to continue ..."
```

Azure PowerShell é usado para implementar o modelo neste tutorial. Para outros métodos de implantação do modelo, consulte:

* [Utilizando o portal Azure.](../azure-resource-manager/templates/deploy-portal.md)
* [Utilizando o Azure CLI](../azure-resource-manager/templates/deploy-cli.md).
* [Utilizando a API REST.](../azure-resource-manager/templates/deploy-rest.md)

## <a name="clean-up-resources"></a>Limpar os recursos

Quando os recursos do Azure já não forem necessários, limpe os recursos implementados ao eliminar o grupo de recursos. Se a camada cloud ainda estiver aberta, não precisa copiar/executar a primeira e segunda linhas do seguinte script.

```azurepowershell-interactive
$serviceBusNamespaceName = Read-Host -Prompt "Enter the same service bus namespace name used earlier"
$resourceGroupName = "${serviceBusNamespaceName}rg"

Remove-AzResourceGroup -ResourceGroupName $resourceGroupName

Write-Host "Press [ENTER] to continue ..."
```

## <a name="next-steps"></a>Passos seguintes

Neste artigo, criou um espaço de nomes de Service Bus. Veja os outros quickstarts para aprender a criar filas, tópicos/subscrições e use-as:

* [Introdução às filas do Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Introdução aos tópicos do Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)