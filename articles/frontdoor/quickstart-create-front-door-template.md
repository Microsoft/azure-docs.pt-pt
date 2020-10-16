---
title: 'Quickstart: Criar um serviço de porta frontal Azure utilizando um modelo de Gestor de Recursos Azure (modelo ARM)'
description: Este quickstart descreve como criar um serviço de porta frontal Azure usando o modelo Azure Resource Manager (modelo ARM).
services: front-door
documentationcenter: ''
author: duongau
editor: ''
ms.assetid: ''
ms.service: frontdoor
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/14/2020
ms.author: duau
ms.custom: subject-armqs
ms.openlocfilehash: e7c3f2f50d9ac1fb1731f70f7b442ab4a2e44425
ms.sourcegitcommit: 30505c01d43ef71dac08138a960903c2b53f2499
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/15/2020
ms.locfileid: "92088929"
---
# <a name="quickstart-create-a-front-door-using-an-arm-template"></a>Quickstart: Criar uma porta frontal usando um modelo ARM

Este quickstart descreve como usar um modelo de Gestor de Recursos Azure (Modelo ARM) para criar uma porta frontal para configurar uma alta disponibilidade para um ponto final web.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Se o seu ambiente cumpre os pré-requisitos e se está familiarizado com a utilização de modelos ARM, selecione o botão **Implementar no Azure**. O modelo será aberto no portal do Azure.

[![Implementar no Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-front-door-create-basic%2Fazuredeploy.json)

## <a name="prerequisites"></a>Pré-requisitos

* Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.
* IP ou FQDN de um site ou aplicação web.

## <a name="review-the-template"></a>Rever o modelo

O modelo utilizado neste início rápido pertence aos [Modelos de Início Rápido do Azure](https://azure.microsoft.com/resources/templates/101-front-door-create-basic).

Neste arranque rápido, irá criar uma configuração da Porta frontal com um único backend e um único caminho padrão que corresponda a "/*". 

:::code language="json" source="~/quickstart-templates/101-front-door-create-basic/azuredeploy.json":::

No modelo, está definido um recurso do Azure:

* [**Microsoft.Network/frontDoors**](/azure/templates/microsoft.network/frontDoors)

## <a name="deploy-the-template"></a>Implementar o modelo

1. Selecione **Experimentá-lo** a partir do bloco de código que se segue para abrir a Azure Cloud Shell e, em seguida, siga as instruções para iniciar súm no Azure. 

    ```azurepowershell-interactive
    $projectName = Read-Host -Prompt "Enter a project name that is used for generating resource names"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
    $templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-front-door-create-basic/azuredeploy.json"

    $resourceGroupName = "${projectName}rg"

    New-AzResourceGroup -Name $resourceGroupName -Location "$location"
    New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri

    Read-Host -Prompt "Press [ENTER] to continue ..."
    ```

    Espere até ver o pedido da consola.

1. Selecione **Copiar** do bloco de código anterior para copiar o script PowerShell.

1. Clique com o botão direito da placa de consola da concha e, em seguida, **selecione Pasta**.

1. Insira os valores.

    A implementação do modelo cria uma porta frontal com um único backend. Neste * <span>exemplo, a Microsoft.</span> com* é usado como **backendAddress**.

    O nome do grupo de recursos é o nome do projeto com **rg** anexado.

    > [!NOTE]
    > **frontDoorName** precisa ser um nome globalmente único para que o modelo seja implantado com sucesso. Se a implantação falhar, comece de novo com o Passo 1.

    Leva alguns minutos para implementar o modelo. Quando concluída, a saída é semelhante a:

    :::image type="content" source="./media/quickstart-create-front-door-template/front-door-template-deployment-powershell-output.png" alt-text="Saída de implementação do powerShell do gestor de recursos da porta da frente":::

Azure PowerShell é usado para implementar o modelo. Além do Azure PowerShell, também pode utilizar o portal Azure CLI e a REST API. Para aprender outros métodos de implementação, consulte [os modelos de implementação](../azure-resource-manager/templates/deploy-portal.md).

## <a name="validate-the-deployment"></a>Validar a implementação

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

1. Selecione **grupos** de recursos do painel esquerdo.

1. Selecione o grupo de recursos que criou na secção anterior. O nome do grupo de recursos predefinidos é o nome do projeto com **rg** anexado.

1. Selecione a Porta frontal que criou anteriormente e clique no link **anfitrião Frontend.** O link abrirá um navegador web redirecionando-o para o seu Backend FQDN que definiu durante a criação.

    :::image type="content" source="./media/quickstart-create-front-door-template/front-door-overview.png" alt-text="Saída de implementação do powerShell do gestor de recursos da porta da frente":::

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não precisar do serviço Porta da Frente, elimine o grupo de recursos. Isto remove a Porta da Frente e todos os recursos relacionados.

Para eliminar o grupo de recursos, ligue para o `Remove-AzResourceGroup` cmdlet:

```azurepowershell-interactive
Remove-AzResourceGroup -Name <your resource group name>
```

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, criou um:
* Front Door

Para aprender a adicionar um domínio personalizado à sua Porta da Frente, continue para os tutoriais da Porta da Frente.

> [!div class="nextstepaction"]
> [Tutoriais da porta da frente](front-door-custom-domain.md)
