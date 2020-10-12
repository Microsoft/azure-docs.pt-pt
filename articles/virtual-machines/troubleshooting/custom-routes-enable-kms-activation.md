---
title: Utilize rotas personalizadas Azure para permitir a ativação do KMS com túneis forçados Microsoft Docs
description: Mostra como utilizar as rotas personalizadas Azure para permitir a ativação kms quando se utiliza um túnel forçado em Azure.
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: top-support-issue, azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 12/20/2018
ms.author: genli
ms.openlocfilehash: 1c2050969e95b521554bba100b688add3a987a80
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86526746"
---
# <a name="windows-activation-fails-in-forced-tunneling-scenario"></a>Ativação do Windows falha em cenário de túnel forçado

Este artigo descreve como resolver o problema de ativação KMS que pode experimentar quando ativar um túnel forçado na ligação local-a-local ou nos cenários ExpressRoute.

## <a name="symptom"></a>Sintoma

Você permite [a escavação forçada](../../vpn-gateway/vpn-gateway-forced-tunneling-rm.md) nas sub-redes de rede virtuais Azure para direcionar todo o tráfego ligado à Internet de volta para a sua rede no local. Neste cenário, as máquinas virtuais Azure (VMs) que executam o Windows não conseguem ativar o Windows.

## <a name="cause"></a>Causa

Os VMs do Azure Windows precisam de se ligar ao servidor Azure KMS para ativação do Windows. A ativação requer que o pedido de ativação provém de um endereço IP público Azure. No cenário de túneis forçados, a ativação falha porque o pedido de ativação provém da sua rede no local em vez de de um endereço IP público Azure.

## <a name="solution"></a>Solução

Para resolver este problema, utilize a rota personalizada Azure para encaminhar o tráfego de ativação para o servidor Azure KMS.

O endereço IP do servidor KMS para a nuvem Azure Global é de 23.102.135.246. O seu nome DNS é kms.core.windows.net. Se utilizar outras plataformas Azure, como a Azure Germany, deve utilizar o endereço IP do servidor KMS correspondente. Para mais informações, consulte a seguinte tabela:

|Plataforma| KMS DNS|KMS IP|
|------|-------|-------|
|Azure Global|kms.core.windows.net|23.102.135.246|
|Azure Alemanha|kms.core.cloudapi.de|51.4.143.248|
|Azure US Government|kms.core.usgovcloudapi.net|23.97.0.13|
|Azure China 21Vianet|kms.core.chinacloudapi.cn|42.159.7.249|


Para adicionar a rota personalizada, siga estes passos:

### <a name="for-resource-manager-vms"></a>Para VMs gestor de recursos

 

> [!NOTE] 
> A ativação utiliza endereços IP públicos e será afetada por uma configuração padrão do Balancer de Carga SKU. Reveja cuidadosamente [as ligações de saída em Azure](../../load-balancer/load-balancer-outbound-connections.md) para saber mais sobre os requisitos.

1. Abra o Azure PowerShell e, em seguida, [inscreva-se na sua subscrição Azure](/powershell/azure/authenticate-azureps).
2. Execute os seguintes comandos:

    ```powershell
    # First, get the virtual network that hosts the VMs that have activation problems. In this case, we get virtual network ArmVNet-DM in Resource Group ArmVNet-DM:

    $vnet = Get-AzVirtualNetwork -ResourceGroupName "ArmVNet-DM" -Name "ArmVNet-DM"

    # Next, create a route table and specify that traffic bound to the KMS IP (23.102.135.246) will go directly out:

    $RouteTable = New-AzRouteTable -Name "ArmVNet-DM-KmsDirectRoute" -ResourceGroupName "ArmVNet-DM" -Location "centralus"

    Add-AzRouteConfig -Name "DirectRouteToKMS" -AddressPrefix 23.102.135.246/32 -NextHopType Internet -RouteTable $RouteTable

    Set-AzRouteTable -RouteTable $RouteTable

    # Next, attach the route table to the subnet that hosts the VMs

    Set-AzVirtualNetworkSubnetConfig -Name "Subnet01" -VirtualNetwork $vnet -AddressPrefix "10.0.0.0/24" -RouteTable $RouteTable

    Set-AzVirtualNetwork -VirtualNetwork $vnet
    ```
3. Vá ao VM que tem problemas de ativação. Utilize [o PsPing](/sysinternals/downloads/psping) para testar se pode chegar ao servidor KMS:

    ```console
    psping kms.core.windows.net:1688
    ```

4. Tente ativar o Windows e ver se o problema está resolvido.

### <a name="for-classic-vms"></a>Para VMs clássicos

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

1. Abra o Azure PowerShell e, em seguida, [inscreva-se na sua subscrição Azure](/powershell/azure/authenticate-azureps).
2. Execute os seguintes comandos:

    ```powershell
    # First, create a new route table:
    New-AzureRouteTable -Name "VNet-DM-KmsRouteGroup" -Label "Route table for KMS" -Location "Central US"

    # Next, get the route table that was created:
    $rt = Get-AzureRouteTable -Name "VNet-DM-KmsRouteTable"

    # Next, create a route:
    Set-AzureRoute -RouteTable $rt -RouteName "AzureKMS" -AddressPrefix "23.102.135.246/32" -NextHopType Internet

    # Apply the KMS route table to the subnet that hosts the problem VMs (in this case, we apply it to the subnet that's named Subnet-1):
    Set-AzureSubnetRouteTable -VirtualNetworkName "VNet-DM" -SubnetName "Subnet-1" 
    -RouteTableName "VNet-DM-KmsRouteTable"
    ```

3. Vá ao VM que tem problemas de ativação. Utilize [o PsPing](/sysinternals/downloads/psping) para testar se pode chegar ao servidor KMS:

    ```console
    psping kms.core.windows.net:1688
    ```

4. Tente ativar o Windows e ver se o problema está resolvido.

## <a name="next-steps"></a>Passos seguintes

- [Teclas de Configuração de Cliente KMS](/windows-server/get-started/kmsclientkeys)
- [Rever e selecionar métodos de ativação](/previous-versions/windows/it-pro/windows-server-2012-r2-and-2012/jj134256(v=ws.11))
