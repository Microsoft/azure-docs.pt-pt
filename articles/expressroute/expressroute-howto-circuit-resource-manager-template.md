---
title: 'Crie um circuito do ExpressRoute - modelo do Resource Manager: Azure | Microsoft Docs'
description: Criar, aprovisionar, eliminar e desaprovisionar um circuito do ExpressRoute.
services: expressroute;azure-resource-manager
author: cherylmc
ms.service: expressroute
ms.topic: article
ms.date: 07/05/2019
ms.author: cherylmc;ganesr
ms.openlocfilehash: bf56145d0a8cd3b01d0d74fcaf3348c1916cee5a
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/08/2019
ms.locfileid: "67659687"
---
# <a name="create-an-expressroute-circuit-by-using-azure-resource-manager-template"></a>Criar um circuito do ExpressRoute com o modelo Azure Resource Manager

> [!div class="op_single_selector"]
> * [Azure portal](expressroute-howto-circuit-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-circuit-arm.md)
> * [CLI do Azure](howto-circuit-cli.md)
> * [Modelo do Azure Resource Manager](expressroute-howto-circuit-resource-manager-template.md)
> * [Vídeo - portal do Azure](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit)
> * [PowerShell (clássico)](expressroute-howto-circuit-classic.md)
>

Saiba como criar um circuito do ExpressRoute ao implementar um modelo Azure Resource Manager com o Azure PowerShell. Para obter mais informações sobre como desenvolver modelos do Resource Manager, consulte [documentação do Resource Manager](/azure/azure-resource-manager/) e o [referência de modelo](/azure/templates/microsoft.network/expressroutecircuits).

## <a name="before-you-begin"></a>Antes de começar

* Reveja os [pré-requisitos](expressroute-prerequisites.md) e [fluxos de trabalho](expressroute-workflows.md) antes de iniciar a configuração.
* Certifique-se de que tem permissões para criar novos recursos de rede. Se não tiver as permissões corretas, contacte o administrador de conta.
* Pode [ver um vídeo](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit) antes de iniciar para compreender melhor os passos.

## <a name="create"></a>Criar e aprovisionar um circuito do ExpressRoute

[Modelos de início rápido do Azure](https://azure.microsoft.com/resources/templates/) tem uma coleção de boa de modelo do Resource Manager. Utilizar um da [modelos existentes](https://azure.microsoft.com/resources/templates/101-expressroute-circuit-create/) para criar um circuito do ExpressRoute.

[!code-json[create-azure-expressroute-circuit](~/quickstart-templates/101-expressroute-circuit-create/azuredeploy.json)]

Para ver mais modelos relacionados, selecione [aqui](https://azure.microsoft.com/resources/templates/?term=expressroute).

Para criar um circuito do ExpressRoute ao implementar um modelo:

1. Selecione **experimente** do seguinte bloco de código e, em seguida, siga as instruções para iniciar sessão no Azure Cloud shell.

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

   * **Escalão** determina se um padrão de ExpressRoute ou de um suplemento premium do ExpressRoute está ativado. Pode especificar **padrão** para obter o SKU standard ou **Premium** para o suplemento premium.

   * **Localização de peering** é a localização física onde são peering com a Microsoft.

     > [!IMPORTANT]
     > A localização de Peering indica a [localização física](expressroute-locations.md) onde são peering com a Microsoft. Isto é **não** ligada à propriedade de "Localização", que se refere a geografia onde está localizado o fornecedor de recursos de rede do Azure. Embora não estejam relacionados, é uma boa prática para escolher um fornecedor de recursos de rede geograficamente próximo a localização de Peering do circuito.

    O nome de grupo de recursos é o nome de espaço de nomes do service bus com **rg** anexado.

2. Selecione **cópia** para copiar o script do PowerShell.
3. O console do shell com o botão direito e, em seguida, selecione **colar**.

Demora alguns minutos para criar um hub de eventos.

O Azure PowerShell é utilizado para implementar o modelo neste tutorial. Para outros métodos de implementação do modelo, consulte:

* [Com o portal do Azure](../azure-resource-manager/resource-group-template-deploy-portal.md).
* [Ao utilizar a CLI do Azure](../azure-resource-manager/resource-group-template-deploy-cli.md).
* [Ao utilizar a REST API](../azure-resource-manager/resource-group-template-deploy-rest.md).

## <a name="delete"></a>Desaprovisionar e eliminar um circuito do ExpressRoute

Pode eliminar o seu circuito do ExpressRoute, selecionando o **eliminar** ícone. Tenha em atenção as seguintes informações:

* Tem de desassociar todas as redes virtuais do circuito do ExpressRoute. Se esta operação falhar, verifique se quaisquer redes virtuais são ligadas ao circuito.
* Se for o fornecedor de serviços de circuito do ExpressRoute estado de aprovisionamento **aprovisionamento** ou **aprovisionado** deve trabalhar com o fornecedor de serviços para desaprovisionar o circuito no seu lado. Continuamos a reservar recursos e a faturação é que o fornecedor de serviços seja concluída o circuito de desaprovisionamento e notifica-nos.
* Se o fornecedor de serviços tiver desaprovisionado o circuito (fornecedor de serviços de estado de aprovisionamento está definido como **não aprovisionado**), pode eliminar o circuito. Isto interrompe a faturação do circuito.

Pode eliminar o seu circuito do ExpressRoute, executando o seguinte comando do PowerShell:

```azurepowershell-interactive
$circuitName = Read-Host -Prompt "Enter the same circuit name that you used earlier"
$resourceGroupName = "${circuitName}rg"

Remove-AzExpressRouteCircuit -ResourceGroupName $resourceGroupName -Name $circuitName
```

## <a name="next-steps"></a>Passos Seguintes

Depois de criar o seu circuito, continue com os seguintes passos seguintes:

* [Criar e modificar o encaminhamento para o seu circuito do ExpressRoute](expressroute-howto-routing-portal-resource-manager.md)
* [Ligar a rede virtual para o seu circuito do ExpressRoute](expressroute-howto-linkvnet-arm.md)
