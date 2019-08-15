---
title: Usar rotas personalizadas do Azure para habilitar a ativação do KMS com túnel forçado | Microsoft Docs
description: Mostra como usar as rotas personalizadas do Azure para habilitar a ativação do KMS ao usar o túnel forçado no Azure.
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: genlin
manager: cshepard
editor: ''
tags: top-support-issue, azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: troubleshooting
ms.date: 12/20/2018
ms.author: genli
ms.openlocfilehash: 2877fae66584ec24fb6e62b20d66ded36157b824
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/13/2019
ms.locfileid: "68990339"
---
# <a name="windows-activation-fails-in-forced-tunneling-scenario"></a>A ativação do Windows falha no cenário de túnel forçado

Este artigo descreve como resolver o problema de ativação do KMS que você pode enfrentar ao habilitar o túnel forçado em cenários de conexão VPN site a site ou de ExpressRoute.

## <a name="symptom"></a>Sintoma

Habilite o [túnel forçado](../../vpn-gateway/vpn-gateway-forced-tunneling-rm.md) em sub-redes da rede virtual do Azure para direcionar todo o tráfego de entrada da Internet para sua rede local. Nesse cenário, as máquinas virtuais (VMs) do Azure que executam o Windows Server 2012 R2 (ou versões posteriores do Windows) podem ativar com êxito o Windows. No entanto, as VMs que executam uma versão anterior do Windows falham ao ativar o Windows.

## <a name="cause"></a>Causa

As VMs do Windows do Azure precisam se conectar ao servidor KMS do Azure para ativação do Windows. A ativação requer que a solicitação de ativação venha de um endereço IP público do Azure. No cenário de túnel forçado, a ativação falha porque a solicitação de ativação vem da sua rede local, em vez de um endereço IP público do Azure.

## <a name="solution"></a>Solução

Para resolver esse problema, use a rota personalizada do Azure para rotear o tráfego de ativação para o servidor KMS do Azure.

O endereço IP do servidor KMS para a nuvem global do Azure é 23.102.135.246. Seu nome DNS é kms.core.windows.net. Se você usar outras plataformas do Azure, como o Azure Alemanha, deverá usar o endereço IP do servidor KMS correspondente. Para obter mais informações, consulte a tabela a seguir:

|Plataforma| DNS KMS|IP KMS|
|------|-------|-------|
|Global do Azure|kms.core.windows.net|23.102.135.246|
|Azure Alemanha|kms.core.cloudapi.de|51.4.143.248|
|Azure US Government|kms.core.usgovcloudapi.net|23.97.0.13|
|Azure China 21Vianet|kms.core.chinacloudapi.cn|42.159.7.249|


Para adicionar a rota personalizada, siga estas etapas:

### <a name="for-resource-manager-vms"></a>Para VMs do Gerenciador de recursos

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

> [!NOTE] 
> A ativação usa endereços IP públicos e será afetada por uma configuração de Load Balancer de SKU padrão. Examine cuidadosamente as [conexões de saída no Azure](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections) para saber mais sobre os requisitos.

1. Abra Azure PowerShell e, em seguida, [entre em sua assinatura do Azure](https://docs.microsoft.com/powershell/azure/authenticate-azureps).
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
3. Vá para a VM que tem problemas de ativação. Use [PsPing](https://docs.microsoft.com/sysinternals/downloads/psping) para testar se ele pode alcançar o servidor kms:

        psping kms.core.windows.net:1688

4. Tente ativar o Windows e veja se o problema foi resolvido.

### <a name="for-classic-vms"></a>Para VMs clássicas

1. Abra Azure PowerShell e, em seguida, [entre em sua assinatura do Azure](https://docs.microsoft.com/powershell/azure/authenticate-azureps).
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

3. Vá para a VM que tem problemas de ativação. Use [PsPing](https://docs.microsoft.com/sysinternals/downloads/psping) para testar se ele pode alcançar o servidor kms:

        psping kms.core.windows.net:1688

4. Tente ativar o Windows e veja se o problema foi resolvido.

## <a name="next-steps"></a>Passos seguintes

- [Chaves de instalação do cliente KMS](https://docs.microsoft.com/windows-server/get-started/kmsclientkeys
)
- [Examinar e selecionar métodos de ativação](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj134256(v=ws.11)
)
