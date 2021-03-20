---
title: 'Modelo Azure ExpressRoute: Criar um circuito ExpressRoute'
description: Aprenda a criar um circuito Azure ExpressRoute implantando um modelo de Gestor de Recursos Azure utilizando a Azure PowerShell.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 11/13/2019
ms.author: duau
ms.openlocfilehash: 2e9b6ddc9da4467590946af12a47f1473a4ea494
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "92202060"
---
# <a name="create-an-expressroute-circuit-by-using-azure-resource-manager-template"></a>Crie um circuito ExpressRoute utilizando o modelo de Gestor de Recursos Azure

> [!div class="op_single_selector"]
> * [Portal do Azure](expressroute-howto-circuit-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-circuit-arm.md)
> * [CLI do Azure](howto-circuit-cli.md)
> * [Modelo Azure Resource Manager](expressroute-howto-circuit-resource-manager-template.md)
> * [Vídeo - Portal Azure](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit)
> * [PowerShell (clássico)](expressroute-howto-circuit-classic.md)
>

Aprenda a criar um circuito ExpressRoute implantando um modelo de Gestor de Recursos Azure utilizando a Azure PowerShell. Para obter mais informações sobre o desenvolvimento de modelos de Gestor de Recursos, consulte [a documentação do Gestor de Recursos](../azure-resource-manager/index.yml) e a referência do [modelo.](/azure/templates/microsoft.network/expressroutecircuits)

## <a name="before-you-begin"></a>Antes de começar

* Reveja os [pré-requisitos](expressroute-prerequisites.md) e [fluxos de trabalho](expressroute-workflows.md) antes de iniciar a configuração.
* Certifique-se de que tem permissões para criar novos recursos de rede. Contacte o administrador da sua conta se não tiver as permissões certas.
* Pode [ver um vídeo](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit) antes de começar para entender melhor os passos.

## <a name="create-and-provision-an-expressroute-circuit"></a><a name="create"></a>Criar e providenciar um circuito ExpressRoute

[Os modelos Azure Quickstart](https://azure.microsoft.com/resources/templates/) têm uma boa coleção de modelo de Gestor de Recursos. Utiliza um dos [modelos existentes](https://azure.microsoft.com/resources/templates/101-expressroute-circuit-create/) para criar um circuito ExpressRoute.

[!code-json[create-azure-expressroute-circuit](~/quickstart-templates/101-expressroute-circuit-create/azuredeploy.json)]

Para ver mais modelos relacionados, selecione [aqui.](https://azure.microsoft.com/resources/templates/?term=expressroute)

Para criar um Circuito ExpressRoute implantando um modelo:

1. Selecione **Experimente-o** a partir do bloco de códigos que se segue e, em seguida, siga as instruções para iniciar seduca na casca da Nuvem Azure.

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

   * **O nível SKU** determina se um circuito ExpressRoute é [Local,](expressroute-faqs.md#expressroute-local)Standard ou [Premium](expressroute-faqs.md#expressroute-premium). Pode especificar *Local,**Standard ou *Premium.* Não é possível alterar o SKU de *Standard/Premium* para *Local.*
   * **A família SKU** determina o tipo de faturação. Pode especificar *os dados medidos* para um plano de dados medido e *dados ilimitados* para um plano de dados ilimitado. Pode alterar o tipo de faturação de *Metereddata* para *Unlimiteddata,* mas não pode alterar o tipo de *dados ilimitados* para *Metereddata*. Um circuito *local* é *apenas dados ilimitados.*
   * **O Local de Observação** é o local físico onde está a espreitar com a Microsoft.

     > [!IMPORTANT]
     > A Localização De Pares indica a [localização física](expressroute-locations.md) onde está a espreitar com a Microsoft. Isto **não** está ligado à propriedade "Localização", que se refere à geografia onde está localizado o Fornecedor de Recursos da Rede Azure. Embora não estejam relacionados, é uma boa prática escolher um Fornecedor de Recursos de Rede geograficamente próximo da Localização De Pares do circuito.

    O nome do grupo de recursos é o nome do espaço de serviço do autocarro com **rg** anexado.

2. Selecione **Copy** para copiar o script PowerShell.
3. Clique com o botão direito na consola da concha e, em seguida, **selecione Pasta**.

Leva alguns momentos para criar um centro de eventos.

Azure PowerShell é usado para implementar o modelo neste tutorial. Para outros métodos de implantação do modelo, consulte:

* [Utilizando o portal Azure.](../azure-resource-manager/templates/deploy-portal.md)
* [Utilizando o Azure CLI](../azure-resource-manager/templates/deploy-cli.md).
* [Utilizando a API REST.](../azure-resource-manager/templates/deploy-rest.md)

## <a name="deprovisioning-and-deleting-an-expressroute-circuit"></a><a name="delete"></a>Deprovisioning and deleting an ExpressRoute circuit (Desaprovisionar e eliminar um circuito do ExpressRoute)

Pode eliminar o seu circuito ExpressRoute selecionando o ícone **de eliminação.** Tenha em atenção as seguintes informações:

* Tem de desassociar todas as redes virtuais do circuito do ExpressRoute. Se esta operação falhar, verifique se alguma rede virtual está ligada ao circuito.
* Se o estado de prestação do serviço de circuito ExpressRoute for **Provisioning** ou **Provisioned,** deve trabalhar com o seu prestador de serviços para desprovisionar o circuito do seu lado. Continuamos a reservar recursos e a cobrar até que o prestador de serviços complete a desprovisionamento do circuito e nos notifique.
* Se o prestador de serviços tiver desprovisionado o circuito (o estado de prestação do prestador de serviços está definido para **Não aprovisionado),** pode apagar o circuito. Isto interrompe a faturação do circuito.

Pode eliminar o seu circuito ExpressRoute executando o seguinte comando PowerShell:

```azurepowershell-interactive
$circuitName = Read-Host -Prompt "Enter the same circuit name that you used earlier"
$resourceGroupName = "${circuitName}rg"

Remove-AzExpressRouteCircuit -ResourceGroupName $resourceGroupName -Name $circuitName
```

## <a name="next-steps"></a>Passos seguintes

Depois de criar o seu circuito, continue com os seguintes passos:

* [Crie e modifique o encaminhamento para o seu circuito ExpressRoute](expressroute-howto-routing-portal-resource-manager.md)
* [Ligue a sua rede virtual ao circuito ExpressRoute](expressroute-howto-linkvnet-arm.md)