---
title: 'Tutorial: Criar um equilibrador de carga cross-region usando Azure PowerShell'
titleSuffix: Azure Load Balancer
description: Começa com este tutorial implantando um Equilibrador de Carga Azure cross-region usando a Azure PowerShell.
author: asudbring
ms.author: allensu
ms.service: load-balancer
ms.topic: tutorial
ms.date: 02/10/2021
ms.openlocfilehash: 88e400cea764be84521c003a681aa74885dc29ce
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101721349"
---
# <a name="tutorial-create-a-cross-region-azure-load-balancer-using-azure-powershell"></a>Tutorial: Criar um balanceador de carga Azure de região transversal usando Azure PowerShell

Um equilibrador de carga transversal garante que um serviço está disponível globalmente em várias regiões de Azure. Se uma região falhar, o tráfego é encaminhado para o próximo e mais próximo e saudável equilibrador regional de carga.  

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar balanceador de carga entre regiões.
> * Crie uma regra de balanceador de carga.
> * Crie uma piscina de backend contendo dois equilibradores de carga regionais.
> * Teste o equilibrador de carga.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

- Uma subscrição do Azure.
- Dois  balançadores de carga sku Azure standard com piscinas de backend implantadas em duas regiões Azure diferentes.
    - Para obter informações sobre a criação de um balanceador de carga padrão regional e máquinas virtuais para piscinas de backend, consulte [Quickstart: Criar um equilibrador de carga público para carregar VMs de equilíbrio utilizando Azure PowerShell](quickstart-load-balancer-standard-public-powershell.md).
        - Apeia o nome dos equilibradores de carga e das máquinas virtuais de cada região com um **-R1** e **-R2**. 
- Azure PowerShell instalado localmente ou Azure Cloud Shell.


Se optar por instalar e utilizar o PowerShell localmente, este artigo requer a versão 5.4.1 ou posterior do módulo Azure PowerShell. Execute `Get-Module -ListAvailable Az` para localizar a versão instalada. Se precisar de atualizar, veja [Install Azure PowerShell module (Instalar o módulo do Azure PowerShell)](/powershell/azure/install-Az-ps). Se estiver a executar o PowerShell localmente, também precisa de correr `Connect-AzAccount` para criar uma ligação com o Azure.

## <a name="create-cross-region-load-balancer"></a>Criar balanceador de carga entre regiões


### <a name="create-a-resource-group"></a>Criar um grupo de recursos

Um grupo de recursos do Azure é um contentor lógico no qual os recursos do Azure são implementados e geridos.

Criar um grupo de recursos com [o New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup).


```azurepowershell-interactive
$rg = @{
    Name = 'MyResourceGroupLB-CR'
    Location = 'westus'
}
New-AzResourceGroup @rg

```

### <a name="create-cross-region-load-balancer-resources"></a>Criar recursos equilibradores de carga entre regiões

Nesta secção, você vai criar os recursos necessários para o balanceador de carga cross-region.

Um IP público padrão global é usado para o frontend do balanceador de carga cross-region.

* Utilize [o New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) para criar o endereço IP público.

* Crie uma configuração IP frontal com [New-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/new-azloadbalancerfrontendipconfig).

* Crie uma piscina de endereços back-end com [New-AzLoadBalancerBackendAddressPoolConfig](/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig).

* Crie uma regra do balançador de carga com [Add-AzLoadBalancerRuleConfig](/powershell/module/az.network/add-azloadbalancerruleconfig).

* Crie um balanceador de carga transversal com [New-AzLoadBalancer](/powershell/module/az.network/new-azloadbalancer).

```azurepowershell-interactive
## Create global IP address for load balancer ##
$ip = @{
    Name = 'myPublicIP-CR'
    ResourceGroupName = 'MyResourceGroupLB-CR'
    Location = 'westus'
    Sku = 'Standard'
    Tier = 'Global'
    AllocationMethod = 'Static'
}
$publicIP = New-AzPublicIpAddress @ip

## Create frontend configuration ##
$fe = @{
    Name = 'myFrontEnd-CR'
    PublicIpAddress = $publicIP
}
$feip = New-AzLoadBalancerFrontendIpConfig @fe

## Create back-end address pool ##
$be = @{
    Name = 'myBackEndPool-CR'
}
$bepool = New-AzLoadBalancerBackendAddressPoolConfig @be

## Create the load balancer rule ##
$rul = @{
    Name = 'myHTTPRule-CR'
    Protocol = 'tcp'
    FrontendPort = '80'
    BackendPort = '80'
    FrontendIpConfiguration = $feip
    BackendAddressPool = $bepool
}
$rule = New-AzLoadBalancerRuleConfig @rul

## Create cross-region load balancer resource ##
$lbp = @{
    ResourceGroupName = 'myResourceGroupLB-CR'
    Name = 'myLoadBalancer-CR'
    Location = 'westus'
    Sku = 'Standard'
    Tier = 'Global'
    FrontendIpConfiguration = $feip
    BackendAddressPool = $bepool
    LoadBalancingRule = $rule
}
$lb = New-AzLoadBalancer @lbp
```

## <a name="configure-backend-pool"></a>Piscina de backend configurada

Nesta secção, você adicionará dois equilibradores de carga padrão regionais ao pool de backend do equilibrador de carga cross-region.

> [!IMPORTANT]
> Para completar estes passos, certifique-se de que dois equilibradores de carga regionais com pools de backend foram implantados na sua subscrição.  Para obter mais informações, consulte **[Quickstart: Criar um equilibrador de carga pública para carregar VMs de equilíbrio utilizando Azure PowerShell](quickstart-load-balancer-standard-public-powershell.md)**.

* Utilize [o Get-AzLoadBalancer](/powershell/module/az.network/get-azloadbalancer) e [o Get-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/get-azloadbalancerfrontendipconfig) para armazenar a informação regional do balançador de carga em variáveis.

* Utilize [o New-AzLoadBalancerBackendAddressConfig](/powershell/module/az.network/new-azloadbalancerbackendaddressconfig) para criar a configuração do pool de endereços de backend para o balançador de carga.

* Utilize [Set-AzLoadBalancerBackendAddressPool](/powershell/module/az.network/new-azloadbalancerbackendaddresspool) para adicionar o frontend regional do balançador de carga ao pool de backend cross-region.

```azurepowershell-interactive
## Place the region one load balancer configuration in a variable ##
$region1 = @{
    Name = 'myLoadBalancer-R1'
    ResourceGroupName = 'CreatePubLBQS-rg-r1'
}
$R1 = Get-AzLoadBalancer @region1

## Place the region two load balancer configuration in a variable ##
$region2 = @{
    Name = 'myLoadBalancer-R2'
    ResourceGroupName = 'CreatePubLBQS-rg-r2'
}
$R2 = Get-AzLoadBalancer @region2

## Place the region one load balancer front-end configuration in a variable ##
$region1fe = @{
    Name = 'MyFrontEnd-R1'
    LoadBalancer = $R1
}
$R1FE = Get-AzLoadBalancerFrontendIpConfig @region1fe

## Place the region two load balancer front-end configuration in a variable ##
$region2fe = @{
    Name = 'MyFrontEnd-R2'
    LoadBalancer = $R2
}
$R2FE = Get-AzLoadBalancerFrontendIpConfig @region2fe

## Create the cross-region backend address pool configuration for region 1 ##
$region1ap = @{
    Name = 'MyBackendPoolConfig-R1'
    LoadBalancerFrontendIPConfigurationId = $R1FE.Id
}
$beaddressconfigR1 = New-AzLoadBalancerBackendAddressConfig @region1ap

## Create the cross-region backend address pool configuration for region 2 ##
$region2ap = @{
    Name = 'MyBackendPoolConfig-R2'
    LoadBalancerFrontendIPConfigurationId = $R2FE.Id
}
$beaddressconfigR2 = New-AzLoadBalancerBackendAddressConfig @region2ap

## Apply the backend address pool configuration for the cross-region load balancer ##
$bepoolcr = @{
    ResourceGroupName = 'myResourceGroupLB-CR'
    LoadBalancerName = 'myLoadBalancer-CR'
    Name = 'myBackEndPool-CR'
    LoadBalancerBackendAddress = $beaddressconfigR1,$beaddressconfigR2
}
Set-AzLoadBalancerBackendAddressPool @bepoolcr

```

## <a name="test-the-load-balancer"></a>Testar o balanceador de carga

Nesta secção, você vai testar o equilibrador de carga cross-region. Você vai ligar ao endereço IP público em um navegador web.  Você vai parar as máquinas virtuais em uma das piscinas regionais de backend balancer de carga e observar o failover.

1. Utilize [o Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) para obter o endereço IP público do equilibrista de carga:

```azurepowershell-interactive
$ip = @{
    Name = 'myPublicIP-CR'
    ResourceGroupName = 'myResourceGroupLB-CR'
}  
Get-AzPublicIPAddress @ip | select IpAddress

```
2. Copie o endereço IP público e cole-o na barra de endereço do browser. A página predefinida do servidor Web do IIS é apresentada no browser.

3. Pare as máquinas virtuais na piscina de backend de um dos esquilibradores de carga regionais.

4. Refresque o navegador web e observe o failover da ligação ao outro equilibrador regional de carga.

## <a name="clean-up-resources"></a>Limpar os recursos

Quando já não é necessário, pode utilizar o comando [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) para remover o grupo de recursos, o equilibrador de carga e os recursos restantes.

```azurepowershell-interactive
Remove-AzResourceGroup -Name 'myResourceGroupLB-CR'
```

## <a name="next-steps"></a>Passos seguintes

Neste tutorial:

* Criei um endereço IP global.
* Criou um equilibrador de carga entre regiões.
* Criou uma regra de equilíbrio de carga.
* Adicionou equilibradores de carga regionais ao pool de backend do equilibrador de carga entre regiões.
* Testei o equilibrador de carga.


Avance para o próximo artigo para aprender a...
> [!div class="nextstepaction"]
> [VMs do equilibrador de carga em todas as zonas de disponibilidade](tutorial-load-balancer-standard-public-zone-redundant-portal.md)