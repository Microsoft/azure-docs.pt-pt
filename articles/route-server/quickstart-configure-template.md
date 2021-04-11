---
title: 'Quickstart: Criar um Servidor de Rota Azure utilizando um modelo de Gestor de Recursos Azure (modelo ARM)'
description: Este quickstart mostra-lhe como criar um Servidor de Rota Azure utilizando o modelo Azure Resource Manager (modelo ARM).
services: route-server
author: duongau
ms.service: route-server
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 04/05/2021
ms.author: duau
ms.openlocfilehash: 6f56b9fb1f6a1f5a1fe0811617fb20412c52fd72
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/06/2021
ms.locfileid: "106452217"
---
# <a name="quickstart-create-an-azure-route-server-using-an-arm-template"></a>Quickstart: Criar um servidor de rota Azure usando um modelo ARM

Este quickstart descreve como usar um modelo de Gestor de Recursos Azure (Modelo ARM) para implantar um Servidor de Rota Azure numa rede virtual nova ou existente.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Se o seu ambiente cumpre os pré-requisitos e se está familiarizado com a utilização de modelos ARM, selecione o botão **Implementar no Azure**. O modelo será aberto no portal do Azure.

[![Implementar no Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-route-server%2Fazuredeploy.json)

## <a name="prerequisites"></a>Pré-requisitos

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="review-the-template"></a>Rever o modelo

O modelo utilizado neste início rápido pertence aos [Modelos de Início Rápido do Azure](https://azure.microsoft.com/resources/templates/101-route-server).

Neste arranque rápido, irá colocar um Servidor de Rota Azure numa rede virtual nova ou existente. Será criada uma sub-rede dedicada para `RouteServerSubnet` acolher o Servidor de Rota. O Servidor de Rota também será configurado com o PEER ASN e Peer IP para estabelecer um perspível BGP.

:::code language="json" source="~/quickstart-templates/101-route-server/azuredeploy.json" range="001-145" highlight="105-142":::

Vários recursos Azure foram definidos no modelo:

* [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualNetworks)
* [**Microsoft.Network/virtualNetworks/subnets (duas**](/azure/templates/microsoft.network/virtualNetworks/subnets) sub-redes, uma com o `routeserversubnet` nome)
* [**Microsoft.Network/virtualHubs**](/azure.templates/microsoft.network/virtualhubs) (implementação do Servidor de Rota)
* [**Microsoft.Network/virtualHubs/ipConfigurations**](/azure.templates/microsoft.network/virtualhubs/ipConfigurations)
* [**Microsoft.Network/virtualHubs/bgpConnections**](/azure.templates/microsoft.network/virtualhubs/bgpConnections) (configuração PEER ASN e Peer IP)


Para encontrar mais modelos relacionados com ExpressRoute, consulte [Azure Quickstart Templates](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Network&pageNumber=1&sort=Popular).

## <a name="deploy-the-template"></a>Implementar o modelo

1. Selecione **Experimentá-lo** a partir do bloco de código que se segue para abrir a Azure Cloud Shell e, em seguida, siga as instruções para iniciar súm no Azure.

    ```azurepowershell-interactive
    $projectName = Read-Host -Prompt "Enter a project name that is used for generating resource names"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
    $templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-route-server/azuredeploy.json"

    $resourceGroupName = "${projectName}rg"

    New-AzResourceGroup -Name $resourceGroupName -Location "$location"
    New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri

    Read-Host -Prompt "Press [ENTER] to continue ..."
    ```

    Espere até ver o pedido da consola.

1. Selecione **Copiar** do bloco de código anterior para copiar o script PowerShell.

1. Clique com o botão direito da placa de consola da concha e, em seguida, **selecione Pasta**.

1. Insira os valores.

    O nome do grupo de recursos é o nome do projeto com **rg** anexado.

    Leva cerca de 20 minutos para implantar o modelo. Quando concluída, a saída é semelhante a:

    :::image type="content" source="./media/quickstart-configure-template/powershell-output.png" alt-text="Saída de implementação do gestor de recursos do servidor de rota PowerShell.":::

Azure PowerShell é usado para implementar o modelo. Além do Azure PowerShell, também pode utilizar o portal Azure CLI e a REST API. Para aprender outros métodos de implementação, consulte [os modelos de implementação](../azure-resource-manager/templates/deploy-portal.md).

## <a name="validate-the-deployment"></a>Validar a implementação

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

1. Selecione **grupos** de recursos do painel esquerdo.

1. Selecione o grupo de recursos que criou na secção anterior. O nome do grupo de recursos predefinidos é o nome do projeto com **rg** anexado.

1. O grupo de recursos deve conter apenas a rede virtual:

     :::image type="content" source="./media/quickstart-configure-template/resource-group.png" alt-text="Grupo de recursos de implementação do Servidor de Rota com rede virtual.":::

1. Aceda a https://aka.ms/routeserver.

1. Selecione o route server nomeado **routeserver** para verificar se a implementação foi bem sucedida.

    :::image type="content" source="./media/quickstart-configure-template/deployment.png" alt-text="Screenshot da página geral do Route Server.":::

## <a name="clean-up-resources"></a>Limpar os recursos

Quando já não precisar dos recursos que criou com o Servidor de Rota, elimine o grupo de recursos. Isto remove o Servidor de Rota e todos os recursos relacionados.

Para eliminar o grupo de recursos, ligue para o `Remove-AzResourceGroup` cmdlet:

```azurepowershell-interactive
Remove-AzResourceGroup -Name <your resource group name>
```

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, criou um:

* Servidor de Rota
* Rede Virtual
* Sub-rede

Depois de criar o Azure Route Server, continue a aprender como o Azure Route Server interage com o ExpressRoute e o VPN Gateways: 

> [!div class="nextstepaction"]
> [Suporte Azure ExpressRoute e Azure VPN](expressroute-vpn-support.md)
