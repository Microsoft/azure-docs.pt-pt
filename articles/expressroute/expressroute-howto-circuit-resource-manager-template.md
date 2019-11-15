---
title: 'Modelo do Azure ExpressRoute: criar um circuito do ExpressRoute'
description: Criar, provisionar, excluir e desprovisionar um circuito do ExpressRoute.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: article
ms.date: 11/13/2019
ms.author: cherylmc
ms.reviewer: ganesr
ms.openlocfilehash: 25ed38e72f5a21622a87e36ad811ffd66f6a4c90
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/14/2019
ms.locfileid: "74083517"
---
# <a name="create-an-expressroute-circuit-by-using-azure-resource-manager-template"></a>Criar um circuito do ExpressRoute usando Azure Resource Manager modelo

> [!div class="op_single_selector"]
> * [Portal do Azure](expressroute-howto-circuit-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-circuit-arm.md)
> * [CLI do Azure](howto-circuit-cli.md)
> * [Modelo do Azure Resource Manager](expressroute-howto-circuit-resource-manager-template.md)
> * [Vídeo - portal do Azure](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit)
> * [PowerShell (clássico)](expressroute-howto-circuit-classic.md)
>

Saiba como criar um circuito do ExpressRoute implantando um modelo de Azure Resource Manager usando Azure PowerShell. Para obter mais informações sobre como desenvolver modelos do Resource Manager, consulte a [documentação do Resource Manager](/azure/azure-resource-manager/) e a [referência de modelo](/azure/templates/microsoft.network/expressroutecircuits).

## <a name="before-you-begin"></a>Antes de começar

* Reveja os [pré-requisitos](expressroute-prerequisites.md) e [fluxos de trabalho](expressroute-workflows.md) antes de iniciar a configuração.
* Certifique-se de que tem permissões para criar novos recursos de rede. Se não tiver as permissões corretas, contacte o administrador de conta.
* Pode [ver um vídeo](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit) antes de iniciar para compreender melhor os passos.

## <a name="create"></a>Criar e aprovisionar um circuito do ExpressRoute

Os [modelos de início rápido do Azure](https://azure.microsoft.com/resources/templates/) têm uma boa coleção do modelo do Resource Manager. Você usa um dos [modelos existentes](https://azure.microsoft.com/resources/templates/101-expressroute-circuit-create/) para criar um circuito do ExpressRoute.

[!code-json[create-azure-expressroute-circuit](~/quickstart-templates/101-expressroute-circuit-create/azuredeploy.json)]

Para ver mais modelos relacionados, selecione [aqui](https://azure.microsoft.com/resources/templates/?term=expressroute).

Para criar um circuito de ExpressRoute implantando um modelo:

1. Selecione **Experimente** no seguinte bloco de código e siga as instruções para entrar no Azure cloud Shell.

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

   * A **camada de SKU** determina se um circuito de ExpressRoute é [local](expressroute-faqs.md#expressroute-local), Standard ou [Premium](expressroute-faqs.md#expressroute-premium). Você pode especificar *local*, *Standard* ou *Premium*.
   * A **família de SKUs** determina o tipo de cobrança. Pode especificar *Metereddata* de um plano de dados limitados e *Unlimiteddata* para um plano de dados ilimitados. Pode alterar o tipo de faturação de *Metereddata* ao *Unlimiteddata*, mas não é possível alterar o tipo de *Unlimiteddata* para *Metereddata*. Um circuito *local* é apenas *Unlimiteddata* .
   * **Localização de peering** é a localização física onde são peering com a Microsoft.

     > [!IMPORTANT]
     > A localização de Peering indica a [localização física](expressroute-locations.md) onde são peering com a Microsoft. Isto é **não** ligada à propriedade de "Localização", que se refere a geografia onde está localizado o fornecedor de recursos de rede do Azure. Embora não estejam relacionados, é uma boa prática para escolher um fornecedor de recursos de rede geograficamente próximo a localização de Peering do circuito.

    O nome do grupo de recursos é o nome do namespace do barramento de serviço com **RG** anexado.

2. Selecione **cópia** para copiar o script do PowerShell.
3. Clique com o botão direito do mouse no console do Shell e selecione **colar**.

Demora alguns minutos para criar um hub de eventos.

Azure PowerShell é usado para implantar o modelo neste tutorial. Para outros métodos de implantação de modelo, consulte:

* [Usando o portal do Azure](../azure-resource-manager/resource-group-template-deploy-portal.md).
* [Usando CLI do Azure](../azure-resource-manager/resource-group-template-deploy-cli.md).
* [Usando a API REST](../azure-resource-manager/resource-group-template-deploy-rest.md).

## <a name="delete"></a>Desaprovisionar e eliminar um circuito do ExpressRoute

Pode eliminar o seu circuito do ExpressRoute, selecionando o **eliminar** ícone. Tenha em atenção as seguintes informações:

* Tem de desassociar todas as redes virtuais do circuito do ExpressRoute. Se esta operação falhar, verifique se quaisquer redes virtuais são ligadas ao circuito.
* Se for o fornecedor de serviços de circuito do ExpressRoute estado de aprovisionamento **aprovisionamento** ou **aprovisionado** deve trabalhar com o fornecedor de serviços para desaprovisionar o circuito no seu lado. Continuamos a reservar recursos e a faturação é que o fornecedor de serviços seja concluída o circuito de desaprovisionamento e notifica-nos.
* Se o fornecedor de serviços tiver desaprovisionado o circuito (fornecedor de serviços de estado de aprovisionamento está definido como **não aprovisionado**), pode eliminar o circuito. Isto interrompe a faturação do circuito.

Você pode excluir o circuito do ExpressRoute executando o seguinte comando do PowerShell:

```azurepowershell-interactive
$circuitName = Read-Host -Prompt "Enter the same circuit name that you used earlier"
$resourceGroupName = "${circuitName}rg"

Remove-AzExpressRouteCircuit -ResourceGroupName $resourceGroupName -Name $circuitName
```

## <a name="next-steps"></a>Passos seguintes

Depois de criar o seu circuito, continue com os seguintes passos seguintes:

* [Criar e modificar o encaminhamento para o seu circuito do ExpressRoute](expressroute-howto-routing-portal-resource-manager.md)
* [Ligar a rede virtual para o seu circuito do ExpressRoute](expressroute-howto-linkvnet-arm.md)
