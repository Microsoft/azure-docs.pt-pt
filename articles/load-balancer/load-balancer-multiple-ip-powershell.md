---
title: Balanceamento de carga em várias configurações de IP - CLI do Azure
titlesuffix: Azure Load Balancer
description: Balanceamento de carga entre configurações de IP primárias e secundárias.
services: load-balancer
documentationcenter: na
author: anavinahar
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: annahar
ms.openlocfilehash: 1b130dff9d290dcce566570fd015d227d44a1310
ms.sourcegitcommit: cdf0e37450044f65c33e07aeb6d115819a2bb822
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/01/2019
ms.locfileid: "57191906"
---
# <a name="load-balancing-on-multiple-ip-configurations-using-powershell"></a>Balanceamento de carga em várias configurações de IP com o PowerShell

> [!div class="op_single_selector"]
> * [Portal](load-balancer-multiple-ip.md)
> * [CLI](load-balancer-multiple-ip-cli.md)
> * [PowerShell](load-balancer-multiple-ip-powershell.md)


Este artigo descreve como utilizar o Balanceador de carga do Azure com vários endereços IP numa interface de rede secundária (NIC). Para este cenário, temos duas VMs com o Windows, cada qual com um site primário e um NIC secundário. Cada uma os NICs secundários tem duas configurações de IP. Cada VM que aloja o Web sites contoso.com e fabrikam.com. Cada Web site está vinculado a uma das configurações de IP na NIC secundário. Usamos o Balanceador de carga do Azure para expor dois endereços IP de front-end, um para cada site, para distribuir o tráfego para a respetiva configuração de IP para o Web site. Este cenário utiliza o mesmo número de porta em ambos os front-ends, bem como os dois endereços IP do conjunto de back-end.

![Imagem de cenário LB](./media/load-balancer-multiple-ip/lb-multi-ip.PNG)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="steps-to-load-balance-on-multiple-ip-configurations"></a>Passos para carregar saldo em várias configurações de IP

Siga os passos abaixo para obter o cenário descrito neste artigo:

1. Instale o Azure PowerShell. Veja [How to install and configure Azure PowerShell (Como instalar e configurar o Azure PowerShell)](/powershell/azure/overview) para obter informações sobre como instalar a versão mais recente do Azure PowerShell, selecionar a subscrição que quer utilizar e iniciar sessão na sua conta do Azure.
2. Crie um grupo de recursos com as seguintes definições:

    ```powershell
    $location = "westcentralus".
    $myResourceGroup = "contosofabrikam"
    ```

    Para obter mais informações, consulte o passo 2 do [criar um grupo de recursos](../virtual-machines/virtual-machines-windows-ps-create.md?toc=%2fazure%2fload-balancer%2ftoc.json).

3. [Criar um conjunto de disponibilidade](../virtual-machines/windows/tutorial-availability-sets.md?toc=%2fazure%2fload-balancer%2ftoc.json) para conter as suas VMs. Para este cenário, utilize o seguinte comando:

    ```powershell
    New-AzAvailabilitySet -ResourceGroupName "contosofabrikam" -Name "myAvailset" -Location "West Central US"
    ```

4. Siga as instruções passos 3 a 5 no [criar uma VM do Windows](../virtual-machines/virtual-machines-windows-ps-create.md?toc=%2fazure%2fload-balancer%2ftoc.json) artigo para preparar a criação de uma VM com um único NIC. Executar passo 6.1 e utilize o seguinte procedimento em vez do 6.2:

    ```powershell
    $availset = Get-AzAvailabilitySet -ResourceGroupName "contosofabrikam" -Name "myAvailset"
    New-AzVMConfig -VMName "VM1" -VMSize "Standard_DS1_v2" -AvailabilitySetId $availset.Id
    ```

    Em seguida, conclua [criar uma VM do Windows](../virtual-machines/virtual-machines-windows-ps-create.md?toc=%2fazure%2fload-balancer%2ftoc.json) 6.3 por meio de 6.8 os passos.

5. Adicione uma segunda configuração de IP para cada uma das VMs. Siga as instruções em [atribuir vários endereços IP para máquinas virtuais](../virtual-network/virtual-network-multiple-ip-addresses-powershell.md#add) artigo. Utilize as seguintes definições de configuração:

    ```powershell
    $NicName = "VM1-NIC2"
    $RgName = "contosofabrikam"
    $NicLocation = "West Central US"
    $IPConfigName4 = "VM1-ipconfig2"
    $Subnet1 = Get-AzVirtualNetworkSubnetConfig -Name "mySubnet" -VirtualNetwork $myVnet
    ```

    Não é necessário associar as configurações de IP secundárias IPs públicos no âmbito deste tutorial. Edite o comando para remover a parte de associação de IP pública.

6. Conclua os passos 4 a 6 deste artigo novamente para a VM2. Certifique-se de que substitua o nome da VM para a VM2, ao fazer isso. Tenha em atenção que não é necessário criar uma rede virtual para a segunda VM. Pode ou não pode criar uma nova sub-rede, com base no seu caso de utilização.

7. Criar dois endereços IP públicos e armazená-las nas variáveis apropriadas, conforme mostrado:

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

9. Crie os conjuntos de endereços de back-end, uma sonda e suas regras de balanceamento de carga:

    ```powershell
    $beaddresspool1 = New-AzLoadBalancerBackendAddressPoolConfig -Name contosopool
    $beaddresspool2 = New-AzLoadBalancerBackendAddressPoolConfig -Name fabrikampool

    $healthProbe = New-AzLoadBalancerProbeConfig -Name HTTP -RequestPath 'index.html' -Protocol http -Port 80 -IntervalInSeconds 15 -ProbeCount 2

    $lbrule1 = New-AzLoadBalancerRuleConfig -Name HTTPc -FrontendIpConfiguration $frontendIP1 -BackendAddressPool $beaddresspool1 -Probe $healthprobe -Protocol Tcp -FrontendPort 80 -BackendPort 80
    $lbrule2 = New-AzLoadBalancerRuleConfig -Name HTTPf -FrontendIpConfiguration $frontendIP2 -BackendAddressPool $beaddresspool2 -Probe $healthprobe -Protocol Tcp -FrontendPort 80 -BackendPort 80
    ```

10. Depois de ter estes recursos serem criados, crie o Balanceador de carga:

    ```powershell
    $mylb = New-AzLoadBalancer -ResourceGroupName contosofabrikam -Name mylb -Location 'West Central US' -FrontendIpConfiguration $frontendIP1 -LoadBalancingRule $lbrule -BackendAddressPool $beAddressPool -Probe $healthProbe
    ```

11. Adicione o segundo back-end endereço conjunto e front-end configuração de IP para o Balanceador de carga criado recentemente:

    ```powershell
    $mylb = Get-AzLoadBalancer -Name "mylb" -ResourceGroupName $myResourceGroup | Add-AzLoadBalancerBackendAddressPoolConfig -Name fabrikampool | Set-AzLoadBalancer

    $mylb | Add-AzLoadBalancerFrontendIpConfig -Name fabrikamfe -PublicIpAddress $publicIP2 | Set-AzLoadBalancer
    
    Add-AzLoadBalancerRuleConfig -Name HTTP -LoadBalancer $mylb -FrontendIpConfiguration $frontendIP2 -BackendAddressPool $beaddresspool2 -Probe $healthProbe -Protocol Tcp -FrontendPort 80 -BackendPort 80 | Set-AzLoadBalancer
    ```

12. Os comandos abaixo obtém os NICs e, em seguida, adicione as duas configurações de IP de cada NIC secundário para o conjunto de endereços de back-end de Balanceador de carga:

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

13. Por fim, tem de configurar os registos de recursos DNS para apontar para o endereço IP de front-end respectivos do Balanceador de carga. Pode alojar os seus domínios no DNS do Azure. Para obter mais informações sobre como utilizar o DNS do Azure com o Balanceador de carga, veja [utilizando o Azure DNS com outros serviços do Azure](../dns/dns-for-azure-services.md).

## <a name="next-steps"></a>Passos Seguintes
- Saiba mais sobre como combinar serviços no Azure em de balanceamento de carga [com os serviços de balanceamento de carga no Azure](../traffic-manager/traffic-manager-load-balancing-azure.md).
- Saiba como utilizar diferentes tipos de registos no Azure para gerir e resolver problemas relacionados com o Balanceador de carga [registos do Azure Monitor para o Azure Load Balancer](../load-balancer/load-balancer-monitor-log.md).
