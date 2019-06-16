---
title: Utilizar rotas personalizadas do Azure para ativar a ativação do KMS com túnel forçado | Documentos da Microsoft
description: Mostra como utilizar rotas personalizadas do Azure para ativar a ativação do KMS quando utilizar o túnel no Azure forçado.
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
ms.openlocfilehash: 6557649eb1b97ad4d88876906737f8249e18b958
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66399793"
---
# <a name="windows-activation-fails-in-forced-tunneling-scenario"></a>Falha de ativação do Windows no cenário de túnel forçado

Este artigo descreve como resolver o problema de ativação do KMS que podem ocorrer quando ativar forçado túnel numa ligação de VPN de site a site ou ExpressRoute cenários.

## <a name="symptom"></a>Sintoma

Ativar [túnel forçado](../../vpn-gateway/vpn-gateway-forced-tunneling-rm.md) no Azure sub-redes da rede virtual para direcionar todo o tráfego vinculado à Internet de volta para a sua rede no local. Neste cenário, as máquinas virtuais (VMs) do Azure que executam o Windows Server 2012 R2 (ou versões posteriores do Windows) com êxito pode ativar Windows. No entanto, as VMs que executam a versão anterior do Windows não ativar o Windows.

## <a name="cause"></a>Causa

As VMs do Windows Azure tem de ligar ao servidor do Azure KMS para ativação do Windows. A ativação exige que o pedido de ativação é proveniente de um endereço IP público do Azure. O cenário de túnel forçado, a ativação falha porque o pedido de ativação é proveniente de sua rede no local em vez de partir de um endereço IP público do Azure.

## <a name="solution"></a>Solução

Para resolver este problema, utilize o tráfego de ativação de rota à rota personalizada do Azure para o servidor do KMS do Azure.

O endereço IP do servidor KMS para a cloud do Azure Global é 23.102.135.246. O nome DNS é kms.core.windows.net. Se usar outras plataformas do Azure, como o Azure Alemanha, tem de utilizar o endereço IP do servidor KMS correspondente. Para obter mais informações, consulte a tabela seguinte:

|Plataforma| DNS DO KMS|IP DE KMS|
|------|-------|-------|
|Azure Global|kms.core.windows.net|23.102.135.246|
|Azure Alemanha|kms.core.cloudapi.de|51.4.143.248|
|Azure US Government|kms.core.usgovcloudapi.net|23.97.0.13|
|Azure China 21Vianet|kms.core.chinacloudapi.cn|42.159.7.249|


Para adicionar a rota personalizada, siga estes passos:

### <a name="for-resource-manager-vms"></a>Para VMs do Resource Manager

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

1. Abra o Azure PowerShell e, em seguida [iniciar sessão na sua subscrição do Azure](https://docs.microsoft.com/powershell/azure/authenticate-azureps).
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
3. Vá para a VM que tem problemas de ativação. Uso [PsPing](https://docs.microsoft.com/sysinternals/downloads/psping) para testar se ele pode contactar o servidor do KMS:

        psping kms.core.windows.net:1688

4. Tente ativar o Windows e veja se o problema está resolvido.

### <a name="for-classic-vms"></a>Para VMs clássicas

1. Abra o Azure PowerShell e, em seguida [iniciar sessão na sua subscrição do Azure](https://docs.microsoft.com/powershell/azure/authenticate-azureps).
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

3. Vá para a VM que tem problemas de ativação. Uso [PsPing](https://docs.microsoft.com/sysinternals/downloads/psping) para testar se ele pode contactar o servidor do KMS:

        psping kms.core.windows.net:1688

4. Tente ativar o Windows e veja se o problema está resolvido.

## <a name="next-steps"></a>Passos Seguintes

- [Chaves de configuração de cliente do KMS](https://docs.microsoft.com/windows-server/get-started/kmsclientkeys
)
- [Revisão e métodos de ativação selecione](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj134256(v=ws.11)
)
