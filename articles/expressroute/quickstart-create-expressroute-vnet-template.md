---
title: Crie um circuito ExpressRoute utilizando um modelo de Gestor de Recursos Azure (modelo ARM)
description: Aprenda a criar um circuito ExpressRoute utilizando o modelo Azure Resource Manager (modelo ARM).
services: expressroute
author: duongau
mnager: kumud
ms.service: expressroute
ms.topic: quickstart
ms.custom: subject-armsq
ms.date: 08/31/2020
ms.author: duau
ms.openlocfilehash: f1dbb9623ddc87f9940fd97b05abbee113fd71c4
ms.sourcegitcommit: 43558caf1f3917f0c535ae0bf7ce7fe4723391f9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/11/2020
ms.locfileid: "90016559"
---
# <a name="quickstart-create-an-expressroute-circuit-with-private-peering-using-an-arm-template"></a>Quickstart: Criar um circuito ExpressRoute com um esprevamento privado usando um modelo ARM

Este quickstart descreve como usar um modelo de Gestor de Recursos Azure (Modelo ARM) para criar um circuito ExpressRoute com um esprevamento privado.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Se o seu ambiente cumpre os pré-requisitos e se está familiarizado com a utilização de modelos ARM, selecione o botão **Implementar no Azure**. O modelo será aberto no portal do Azure.

[![Implementar no Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-expressroute-private-peering-vnet%2Fazuredeploy.json)

## <a name="prerequisites"></a>Pré-requisitos

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="review-the-template"></a>Rever o modelo

O modelo utilizado neste início rápido pertence aos [Modelos de Início Rápido do Azure](https://azure.microsoft.com/resources/templates/101-expressroute-private-peering-vnet).

Neste arranque rápido, irá criar um circuito ExpressRoute com *a Equinix* como prestador de serviços. O circuito utilizará um *Premium SKU,* com uma largura de banda de *50 Mbps,* e a localização de observação de *Washington DC.* O espreitamento privado será ativado com uma sub-rede primária e secundária de *192.168.10.16/30* e *192.168.10.20/30,* respectivamente. Será também criada uma rede virtual juntamente com um *gateway HighPerformance ExpressRoute*.

:::code language="json" source="~/quickstart-templates/101-expressroute-private-peering-vnet/azuredeploy.json" range="001-351" highlight="183-219":::

Vários recursos Azure foram definidos no modelo:

* [**Microsoft.Network/expressRouteCircuits**](/azure/templates/microsoft.network/expressRouteCircuits)
* [**Microsoft.Network/expressRouteCircuits/peerings**](/azure/templates/microsoft.network/expressRouteCircuits/peerings) (Usado para ativar o espreitamento privado no circuito)
* [**Microsoft.Network/networkSecurityGroups**](/azure/templates/microsoft.network/networkSecurityGroups) (o grupo de segurança da rede é aplicado às sub-redes da rede virtual)
* [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualNetworks) 
* [**Microsoft.Network/publicIPAddresses**](/azure/templates/microsoft.network/publicIPAddresses) (O IP público é utilizado pelo gateway ExpressRoute)
* [**Microsoft.Network/virtualNetworkGateways**](/azure/templates/microsoft.network/virtualNetworkGateways) (o gateway ExpressRoute é utilizado para ligar o VNet ao circuito)

Para encontrar mais modelos relacionados com ExpressRoute, consulte [Azure Quickstart Templates](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Network&pageNumber=1&sort=Popular).

## <a name="deploy-the-template"></a>Implementar o modelo

1. Selecione **Experimentá-lo** a partir do bloco de código que se segue para abrir a Azure Cloud Shell e, em seguida, siga as instruções para iniciar súm no Azure. 

    ```azurepowershell-interactive
    $projectName = Read-Host -Prompt "Enter a project name that is used for generating resource names"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
    $templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-expressroute-private-peering-vnet/azuredeploy.json"

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

    :::image type="content" source="./media/quickstart-create-expressroute-vnet/expressroute-powershell-output.png" alt-text="Saída de implementação do powershell do gestor de recursos ExpressRoute":::

Azure PowerShell é usado para implementar o modelo. Além do Azure PowerShell, também pode utilizar o portal Azure CLI e a REST API. Para aprender outros métodos de implementação, consulte [os modelos de implementação](../azure-resource-manager/templates/deploy-portal.md).

## <a name="validate-the-deployment"></a>Validar a implementação

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

1. Selecione **grupos** de recursos do painel esquerdo.

1. Selecione o grupo de recursos que criou na secção anterior. O nome do grupo de recursos predefinidos é o nome do projeto com **rg** anexado.

1. O grupo de recursos deve conter os seguintes recursos vistos aqui:

     :::image type="content" source="./media/quickstart-create-expressroute-vnet/expressroute-resource-group.png" alt-text="Grupo de recursos de implantação ExpressRoute":::

1. Selecione o circuito ExpressRoute **er-ck01** para verificar se o estado do circuito está **Ativado,** o estado do fornecedor não é **aprovisionado** e o estoque privado tem o estatuto de **Provisionado**.

    :::image type="content" source="./media/quickstart-create-expressroute-vnet/expressroute-circuit.png" alt-text="Circuito de implantação ExpressRoute":::

> [!NOTE]
> Terá de ligar ao fornecedor para concluir o processo de provisionamento antes de poder ligar a rede virtual ao circuito.

## <a name="clean-up-resources"></a>Limpar os recursos

Quando já não precisar dos recursos que criou com o circuito ExpressRoute, elimine o grupo de recursos. Isto remove o circuito ExpressRoute e todos os recursos relacionados.

Para eliminar o grupo de recursos, ligue para o `Remove-AzResourceGroup` cmdlet:

```azurepowershell-interactive
Remove-AzResourceGroup -Name <your resource group name>
```

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, criou um:
* Circuito do ExpressRoute
* Rede Virtual
* Gateway de VPN
* IP público
* grupos de segurança de rede

Para saber mais sobre a ligação de uma rede virtual a um circuito, continue para os tutoriais ExpressRoute.

> [!div class="nextstepaction"]
> [Tutoriais ExpressRoute](expressroute-howto-linkvnet-portal-resource-manager.md)

* Para obter mais informações sobre o fluxo de trabalho do ExpressRoute, veja [Fluxos de trabalho do ExpressRoute](expressroute-workflows.md).
* Para obter mais informações sobre peering do circuito, veja [Circuitos ExpressRoute e domínios de encaminhamento](expressroute-circuit-peerings.md).
* Para obter mais informações sobre como trabalhar com redes virtuais, veja [Descrição geral da rede virtual](../virtual-network/virtual-networks-overview.md).
