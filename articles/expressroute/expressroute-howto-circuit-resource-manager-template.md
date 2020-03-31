---
title: 'Modelo Azure ExpressRoute: Crie um circuito ExpressRoute'
description: Criar, fornecer, excluir e desabastecer um circuito ExpressRoute.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: article
ms.date: 11/13/2019
ms.author: cherylmc
ms.reviewer: ganesr
ms.openlocfilehash: 78da84a462566cca1a2800174849159ace8dd6dc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75981142"
---
# <a name="create-an-expressroute-circuit-by-using-azure-resource-manager-template"></a>Crie um circuito ExpressRoute usando o modelo de Gestor de Recursos Azure

> [!div class="op_single_selector"]
> * [Portal Azure](expressroute-howto-circuit-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-circuit-arm.md)
> * [Azure CLI](howto-circuit-cli.md)
> * [Modelo Azure Resource Manager](expressroute-howto-circuit-resource-manager-template.md)
> * [Vídeo - Portal Azure](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit)
> * [PowerShell (clássico)](expressroute-howto-circuit-classic.md)
>

Aprenda a criar um circuito ExpressRoute implantando um modelo de Gestor de Recursos Azure utilizando o Azure PowerShell. Para obter mais informações sobre o desenvolvimento de modelos de Gestor de Recursos, consulte a [documentação do Gestor de Recursos](/azure/azure-resource-manager/) e a referência do [modelo](/azure/templates/microsoft.network/expressroutecircuits).

## <a name="before-you-begin"></a>Antes de começar

* Reveja os [pré-requisitos](expressroute-prerequisites.md) e [os fluxos de trabalho](expressroute-workflows.md) antes de iniciar a configuração.
* Certifique-se de que tem permissões para criar novos recursos de networking. Contacte o administrador da sua conta se não tiver as permissões certas.
* Pode [ver um vídeo](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit) antes de começar para entender melhor os passos.

## <a name="create-and-provision-an-expressroute-circuit"></a><a name="create"></a>Criar e fornecer um circuito ExpressRoute

[Os modelos Azure Quickstart](https://azure.microsoft.com/resources/templates/) têm uma boa coleção de modelo de Gestor de Recursos. Usa um dos [modelos existentes](https://azure.microsoft.com/resources/templates/101-expressroute-circuit-create/) para criar um circuito ExpressRoute.

[!code-json[create-azure-expressroute-circuit](~/quickstart-templates/101-expressroute-circuit-create/azuredeploy.json)]

Para ver modelos mais relacionados, selecione [aqui](https://azure.microsoft.com/resources/templates/?term=expressroute).

Para criar um Circuito ExpressRoute implantando um modelo:

1. Selecione **Experimente** a partir do seguinte bloco de código e, em seguida, siga as instruções para iniciar sessão na concha Azure Cloud.

    ```azurepowershell-interactive
    $circuitName = Read-Host -Prompt "Enter a circuit name"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
    $resourceGroupName = "${circuitName}rg"
    $templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-expressroute-circuit-create/azuredeploy.json"

    $serviceProviderName = "Equinix"
    $peeringLocation = "Silicon Valley"
    $bandwidthInMbps = 500
    $sku_tier = "Premium"
    $sku_family = "MeteredData"

    New-AzResourceGroup -Name $resourceGroupName -Location $location
    New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri -circuitName $circuitName -serviceProviderName $serviceProviderName -peeringLocation $peeringLocation -bandwidthInMbps $bandwidthInMbps -sku_tier $sku_tier -sku_family $sku_family

    Write-Host "Press [ENTER] to continue ..."
    ```

   * **O nível SKU** determina se um circuito ExpressRoute é [Local,](expressroute-faqs.md#expressroute-local)Standard ou [Premium.](expressroute-faqs.md#expressroute-premium) Pode especificar *Local,* *Standard* ou *Premium.*
   * **A família SKU** determina o tipo de faturação. Pode especificar *os dados medidos* para um plano de dados medido e *dados ilimitados* para um plano de dados ilimitado. Pode alterar o tipo de faturação de *Dados Medidos* para *Dados Ilimitados,* mas não pode alterar o tipo de Dados *Ilimitados* para *Dados Medidos*. Um circuito *local* é *apenas Ilimitado.*
   * **Localização de observação** é o local físico onde você está olhando com a Microsoft.

     > [!IMPORTANT]
     > O Local de Observação indica a [localização física](expressroute-locations.md) onde está a espreitar com a Microsoft. Isto **não** está ligado à propriedade "Localização", que se refere à geografia onde está localizado o Fornecedor de Recursos da Rede Azure. Embora não estejam relacionados, é uma boa prática escolher um Fornecedor de Recursos de Rede geograficamente próximo da Localização de Peering do circuito.

    O nome do grupo de recursos é o nome do espaço de nome do ônibus de serviço com **rg** anexado.

2. Selecione **Copiar** para copiar o script PowerShell.
3. Clique na consola da concha e, em seguida, selecione **Paste**.

Leva alguns momentos para criar um centro de eventos.

O Azure PowerShell é usado para implantar o modelo neste tutorial. Para outros métodos de implementação do modelo, consulte:

* [Utilizando o portal Azure.](../azure-resource-manager/templates/deploy-portal.md)
* [Utilizando o Azure CLI](../azure-resource-manager/templates/deploy-cli.md).
* [Utilizando a Rest API](../azure-resource-manager/templates/deploy-rest.md).

## <a name="deprovisioning-and-deleting-an-expressroute-circuit"></a><a name="delete"></a>Deprovisioning and deleting an ExpressRoute circuit (Desaprovisionar e eliminar um circuito do ExpressRoute)

Pode eliminar o circuito ExpressRoute selecionando o ícone **de exclusão.** Tenha em atenção as seguintes informações:

* Tem de desassociar todas as redes virtuais do circuito do ExpressRoute. Se esta operação falhar, verifique se alguma rede virtual está ligada ao circuito.
* Se o estado de provisionamento do prestador de serviços do circuito ExpressRoute estiver **provisionamento** ou **provisionado,** deve trabalhar com o seu prestador de serviços para desprovisionar o circuito do seu lado. Continuamos a reservar recursos e a cobrar até que o prestador de serviços complete a desprovisionamento do circuito e nos notifique.
* Se o prestador de serviços tiver desprovisionado o circuito (o estado de provisionamento do prestador de serviços está definido para **Não provisionado),** pode eliminar o circuito. Isto interrompe a faturação do circuito.

Pode eliminar o circuito ExpressRoute executando o seguinte comando PowerShell:

```azurepowershell-interactive
$circuitName = Read-Host -Prompt "Enter the same circuit name that you used earlier"
$resourceGroupName = "${circuitName}rg"

Remove-AzExpressRouteCircuit -ResourceGroupName $resourceGroupName -Name $circuitName
```

## <a name="next-steps"></a>Passos seguintes

Depois de criar o seu circuito, continue com os seguintes passos:

* [Crie e modifique o encaminhamento para o seu circuito ExpressRoute](expressroute-howto-routing-portal-resource-manager.md)
* [Ligue a sua rede virtual ao circuito ExpressRoute](expressroute-howto-linkvnet-arm.md)
