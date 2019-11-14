---
title: Balanceamento de carga em várias configurações de IP-CLI do Azure
titleSuffix: Azure Load Balancer
description: Neste artigo, saiba mais sobre o balanceamento de carga nas configurações de IP primário e secundário usando CLI do Azure.
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: allensu
ms.openlocfilehash: 6ac9e362314cc45e6adbdcf1390f70cbe6b05de8
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/14/2019
ms.locfileid: "74075968"
---
# <a name="load-balancing-on-multiple-ip-configurations-using-powershell"></a>Balanceamento de carga em várias configurações de IP usando o PowerShell

> [!div class="op_single_selector"]
> * [Portal](load-balancer-multiple-ip.md)
> * [CLI](load-balancer-multiple-ip-cli.md)
> * [PowerShell](load-balancer-multiple-ip-powershell.md)


Este artigo descreve como usar Azure Load Balancer com vários endereços IP em uma NIC (interface de rede secundária). Para esse cenário, temos duas VMs executando o Windows, cada uma com uma NIC primária e uma secundária. Cada uma das NICs secundárias tem duas configurações de IP. Cada VM hospeda os sites contoso.com e fabrikam.com. Cada site está associado a uma das configurações de IP na NIC secundária. Usamos Azure Load Balancer para expor dois endereços IP de front-end, um para cada site, para distribuir o tráfego para a respectiva configuração de IP para o site. Esse cenário usa o mesmo número de porta em ambos os front-ends, bem como os dois endereços IP do pool de back-end.

![Imagem do cenário LB](./media/load-balancer-multiple-ip/lb-multi-ip.PNG)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="steps-to-load-balance-on-multiple-ip-configurations"></a>Etapas para balancear a carga em várias configurações de IP

Siga as etapas abaixo para obter o cenário descrito neste artigo:

1. Instale o Azure PowerShell. Veja [How to install and configure Azure PowerShell (Como instalar e configurar o Azure PowerShell)](/powershell/azure/overview) para obter informações sobre como instalar a versão mais recente do Azure PowerShell, selecionar a subscrição que quer utilizar e iniciar sessão na sua conta do Azure.
2. Crie um grupo de recursos usando as seguintes configurações:

    ```powershell
    $location = "westcentralus".
    $myResourceGroup = "contosofabrikam"
    ```

    Para obter mais informações, consulte a etapa 2 de [criar um grupo de recursos](../virtual-machines/virtual-machines-windows-ps-create.md?toc=%2fazure%2fload-balancer%2ftoc.json).

3. [Crie um conjunto de disponibilidade](../virtual-machines/windows/tutorial-availability-sets.md?toc=%2fazure%2fload-balancer%2ftoc.json) para conter suas VMs. Para este cenário, use o seguinte comando:

    ```powershell
    New-AzAvailabilitySet -ResourceGroupName "contosofabrikam" -Name "myAvailset" -Location "West Central US"
    ```

4. Siga as instruções das etapas 3 a 5 no artigo [criar uma VM do Windows](../virtual-machines/virtual-machines-windows-ps-create.md?toc=%2fazure%2fload-balancer%2ftoc.json) para preparar a criação de uma VM com uma única NIC. Execute a etapa 6,1 e use o seguinte em vez da etapa 6,2:

    ```powershell
    $availset = Get-AzAvailabilitySet -ResourceGroupName "contosofabrikam" -Name "myAvailset"
    New-AzVMConfig -VMName "VM1" -VMSize "Standard_DS1_v2" -AvailabilitySetId $availset.Id
    ```

    Em seguida, conclua [a criação de etapas de VM do Windows de](../virtual-machines/virtual-machines-windows-ps-create.md?toc=%2fazure%2fload-balancer%2ftoc.json) 6,3 a 6,8.

5. Adicione uma segunda configuração de IP a cada uma das VMs. Siga as instruções no artigo [atribuir vários endereços IP a máquinas virtuais](../virtual-network/virtual-network-multiple-ip-addresses-powershell.md#add) . Use as seguintes definições de configuração:

    ```powershell
    $NicName = "VM1-NIC2"
    $RgName = "contosofabrikam"
    $NicLocation = "West Central US"
    $IPConfigName4 = "VM1-ipconfig2"
    $Subnet1 = Get-AzVirtualNetworkSubnetConfig -Name "mySubnet" -VirtualNetwork $myVnet
    ```

    Você não precisa associar as configurações de IP secundários a IPs públicos para fins deste tutorial. Edite o comando para remover a parte da Associação de IP público.

6. Conclua as etapas 4 a 6 deste artigo novamente para VM2. Certifique-se de substituir o nome da VM para VM2 ao fazer isso. Observe que você não precisa criar uma rede virtual para a segunda VM. Você pode ou não criar uma nova sub-rede com base no seu caso de uso.

7. Crie dois endereços IP públicos e armazene-os nas variáveis apropriadas, conforme mostrado:

    ```powershell
    $publicIP1 = New-AzPublicIpAddress -Name PublicIp1 -ResourceGroupName contosofabrikam -Location 'West Central US' -AllocationMethod Dynamic -DomainNameLabel contoso
    $publicIP2 = New-AzPublicIpAddress -Name PublicIp2 -ResourceGroupName contosofabrikam -Location 'West Central US' -AllocationMethod Dynamic -DomainNameLabel fabrikam

    $publicIP1 = Get-AzPublicIpAddress -Name PublicIp1 -ResourceGroupName contosofabrikam
    $publicIP2 = Get-AzPublicIpAddress -Name PublicIp2 -ResourceGroupName contosofabrikam
    ```

8. Crie duas configurações de IP de front-end:

    ```powershell
    $frontendIP1 = New-AzLoadBalancerFrontendIpConfig -Name contosofe -PublicIpAddress $publicIP1
    $frontendIP2 = New-AzLoadBalancerFrontendIpConfig -Name fabrikamfe -PublicIpAddress $publicIP2
    ```

9. Crie seus pools de endereços de back-end, uma investigação e suas regras de balanceamento de carga:

    ```powershell
    $beaddresspool1 = New-AzLoadBalancerBackendAddressPoolConfig -Name contosopool
    $beaddresspool2 = New-AzLoadBalancerBackendAddressPoolConfig -Name fabrikampool

    $healthProbe = New-AzLoadBalancerProbeConfig -Name HTTP -RequestPath 'index.html' -Protocol http -Port 80 -IntervalInSeconds 15 -ProbeCount 2

    $lbrule1 = New-AzLoadBalancerRuleConfig -Name HTTPc -FrontendIpConfiguration $frontendIP1 -BackendAddressPool $beaddresspool1 -Probe $healthprobe -Protocol Tcp -FrontendPort 80 -BackendPort 80
    $lbrule2 = New-AzLoadBalancerRuleConfig -Name HTTPf -FrontendIpConfiguration $frontendIP2 -BackendAddressPool $beaddresspool2 -Probe $healthprobe -Protocol Tcp -FrontendPort 80 -BackendPort 80
    ```

10. Depois de criar esses recursos, crie o balanceador de carga:

    ```powershell
    $mylb = New-AzLoadBalancer -ResourceGroupName contosofabrikam -Name mylb -Location 'West Central US' -FrontendIpConfiguration $frontendIP1 -LoadBalancingRule $lbrule -BackendAddressPool $beAddressPool -Probe $healthProbe
    ```

11. Adicione o segundo pool de endereços de back-end e a configuração de IP de front-end ao seu balanceador de carga criado recentemente:

    ```powershell
    $mylb = Get-AzLoadBalancer -Name "mylb" -ResourceGroupName $myResourceGroup | Add-AzLoadBalancerBackendAddressPoolConfig -Name fabrikampool | Set-AzLoadBalancer

    $mylb | Add-AzLoadBalancerFrontendIpConfig -Name fabrikamfe -PublicIpAddress $publicIP2 | Set-AzLoadBalancer
    
    Add-AzLoadBalancerRuleConfig -Name HTTP -LoadBalancer $mylb -FrontendIpConfiguration $frontendIP2 -BackendAddressPool $beaddresspool2 -Probe $healthProbe -Protocol Tcp -FrontendPort 80 -BackendPort 80 | Set-AzLoadBalancer
    ```

12. Os comandos a seguir obtêm as NICs e, em seguida, adicionam ambas as configurações de IP de cada NIC secundária ao pool de endereços de back-end do balanceador de carga:

    ```powershell
    $nic1 = Get-AzNetworkInterface -Name "VM1-NIC2" -ResourceGroupName "MyResourcegroup";
    $nic2 = Get-AzNetworkInterface -Name "VM2-NIC2" -ResourceGroupName "MyResourcegroup";

    $nic1.IpConfigurations[0].LoadBalancerBackendAddressPools.Add($mylb.BackendAddressPools[0]);
    $nic1.IpConfigurations[1].LoadBalancerBackendAddressPools.Add($mylb.BackendAddressPools[1]);
    $nic2.IpConfigurations[0].LoadBalancerBackendAddressPools.Add($mylb.BackendAddressPools[0]);
    $nic2.IpConfigurations[1].LoadBalancerBackendAddressPools.Add($mylb.BackendAddressPools[1]);

    $mylb = $mylb | Set-AzLoadBalancer

    $nic1 | Set-AzNetworkInterface
    $nic2 | Set-AzNetworkInterface
    ```

13. Por fim, você deve configurar registros de recursos de DNS para apontar para o respectivo endereço IP de front-end da Load Balancer. Você pode hospedar seus domínios no DNS do Azure. Para obter mais informações sobre como usar o DNS do Azure com o Load Balancer, consulte [usando o DNS do Azure com outros serviços do Azure](../dns/dns-for-azure-services.md).

## <a name="next-steps"></a>Passos seguintes
- Saiba mais sobre como combinar serviços de balanceamento de carga no Azure [usando serviços de balanceamento de carga no Azure](../traffic-manager/traffic-manager-load-balancing-azure.md).
- Saiba como você pode usar diferentes tipos de logs no Azure para gerenciar e solucionar problemas do balanceador de carga nos [logs de Azure monitor para Azure Load Balancer](../load-balancer/load-balancer-monitor-log.md).
