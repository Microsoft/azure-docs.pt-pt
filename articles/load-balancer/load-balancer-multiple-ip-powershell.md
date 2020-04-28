---
title: Equilíbrio de carga em várias configurações IP - Azure CLI
titleSuffix: Azure Load Balancer
description: Neste artigo, aprenda sobre o equilíbrio de carga através das configurações ip primárias e secundárias usando o Azure CLI.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "74075968"
---
# <a name="load-balancing-on-multiple-ip-configurations-using-powershell"></a>Equilíbrio de carga em várias configurações IP usando PowerShell

> [!div class="op_single_selector"]
> * [Portal](load-balancer-multiple-ip.md)
> * [CLI](load-balancer-multiple-ip-cli.md)
> * [PowerShell](load-balancer-multiple-ip-powershell.md)


Este artigo descreve como utilizar o Azure Load Balancer com vários endereços IP numa interface de rede secundária (NIC). Para este cenário, temos dois VMs a executar o Windows, cada um com um NIC primário e um NIC secundário. Cada um dos NICs secundários tem duas configurações IP. Cada VM acolhe os dois websites contoso.com e fabrikam.com. Cada website está ligado a uma das configurações IP no NIC secundário. Utilizamos o Azure Load Balancer para expor dois endereços IP frontend, um para cada website, para distribuir tráfego para a respetiva configuração IP para o website. Este cenário utiliza o mesmo número de porta em ambas as extremidades dianteiras, bem como ambos os endereços IP do pool de backend.

![Imagem de cenário LB](./media/load-balancer-multiple-ip/lb-multi-ip.PNG)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="steps-to-load-balance-on-multiple-ip-configurations"></a>Passos para carregar o equilíbrio em várias configurações ip

Siga os passos abaixo para alcançar o cenário descrito neste artigo:

1. Instale o Azure PowerShell. Veja [How to install and configure Azure PowerShell (Como instalar e configurar o Azure PowerShell)](/powershell/azure/overview) para obter informações sobre como instalar a versão mais recente do Azure PowerShell, selecionar a subscrição que quer utilizar e iniciar sessão na sua conta do Azure.
2. Criar um grupo de recursos utilizando as seguintes definições:

    ```powershell
    $location = "westcentralus".
    $myResourceGroup = "contosofabrikam"
    ```

    Para mais informações, consulte o Passo 2 de [Criar um Grupo](../virtual-machines/virtual-machines-windows-ps-create.md?toc=%2fazure%2fload-balancer%2ftoc.json)de Recursos .

3. [Crie um Conjunto](../virtual-machines/windows/tutorial-availability-sets.md?toc=%2fazure%2fload-balancer%2ftoc.json) de Disponibilidade para conter os seus VMs. Para este cenário, utilize o seguinte comando:

    ```powershell
    New-AzAvailabilitySet -ResourceGroupName "contosofabrikam" -Name "myAvailset" -Location "West Central US"
    ```

4. Siga as instruções passos 3 a 5 em Criar um artigo [VM do Windows](../virtual-machines/virtual-machines-windows-ps-create.md?toc=%2fazure%2fload-balancer%2ftoc.json) para preparar a criação de um VM com um único NIC. Executar o passo 6.1 e utilizar o seguinte em vez do passo 6.2:

    ```powershell
    $availset = Get-AzAvailabilitySet -ResourceGroupName "contosofabrikam" -Name "myAvailset"
    New-AzVMConfig -VMName "VM1" -VMSize "Standard_DS1_v2" -AvailabilitySetId $availset.Id
    ```

    Em seguida, complete [Criar um Windows VM](../virtual-machines/virtual-machines-windows-ps-create.md?toc=%2fazure%2fload-balancer%2ftoc.json) passos 6.3 a 6.8.

5. Adicione uma segunda configuração IP a cada um dos VMs. Siga as instruções em [atribuir vários endereços IP ao artigo de máquinas virtuais.](../virtual-network/virtual-network-multiple-ip-addresses-powershell.md#add) Utilize as seguintes definições de configuração:

    ```powershell
    $NicName = "VM1-NIC2"
    $RgName = "contosofabrikam"
    $NicLocation = "West Central US"
    $IPConfigName4 = "VM1-ipconfig2"
    $Subnet1 = Get-AzVirtualNetworkSubnetConfig -Name "mySubnet" -VirtualNetwork $myVnet
    ```

    Não é necessário associar as configurações secundárias de IP com iPs públicos para efeitos deste tutorial. Editar o comando para remover a parte da associação IP pública.

6. Complete os passos 4 a 6 deste artigo novamente para VM2. Certifique-se de que substitui o nome VM por VM2 ao fazê-lo. Note que não precisa de criar uma rede virtual para o segundo VM. Pode ou não criar uma nova sub-rede com base no seu caso de utilização.

7. Crie dois endereços IP públicos e guarde-os nas variáveis apropriadas, como mostrado:

    ```powershell
    $publicIP1 = New-AzPublicIpAddress -Name PublicIp1 -ResourceGroupName contosofabrikam -Location 'West Central US' -AllocationMethod Dynamic -DomainNameLabel contoso
    $publicIP2 = New-AzPublicIpAddress -Name PublicIp2 -ResourceGroupName contosofabrikam -Location 'West Central US' -AllocationMethod Dynamic -DomainNameLabel fabrikam

    $publicIP1 = Get-AzPublicIpAddress -Name PublicIp1 -ResourceGroupName contosofabrikam
    $publicIP2 = Get-AzPublicIpAddress -Name PublicIp2 -ResourceGroupName contosofabrikam
    ```

8. Criar duas configurações IP frontend:

    ```powershell
    $frontendIP1 = New-AzLoadBalancerFrontendIpConfig -Name contosofe -PublicIpAddress $publicIP1
    $frontendIP2 = New-AzLoadBalancerFrontendIpConfig -Name fabrikamfe -PublicIpAddress $publicIP2
    ```

9. Crie as suas piscinas de endereços de backend, uma sonda e as suas regras de equilíbrio de carga:

    ```powershell
    $beaddresspool1 = New-AzLoadBalancerBackendAddressPoolConfig -Name contosopool
    $beaddresspool2 = New-AzLoadBalancerBackendAddressPoolConfig -Name fabrikampool

    $healthProbe = New-AzLoadBalancerProbeConfig -Name HTTP -RequestPath 'index.html' -Protocol http -Port 80 -IntervalInSeconds 15 -ProbeCount 2

    $lbrule1 = New-AzLoadBalancerRuleConfig -Name HTTPc -FrontendIpConfiguration $frontendIP1 -BackendAddressPool $beaddresspool1 -Probe $healthprobe -Protocol Tcp -FrontendPort 80 -BackendPort 80
    $lbrule2 = New-AzLoadBalancerRuleConfig -Name HTTPf -FrontendIpConfiguration $frontendIP2 -BackendAddressPool $beaddresspool2 -Probe $healthprobe -Protocol Tcp -FrontendPort 80 -BackendPort 80
    ```

10. Assim que tiver estes recursos criados, crie o seu equilibrador de carga:

    ```powershell
    $mylb = New-AzLoadBalancer -ResourceGroupName contosofabrikam -Name mylb -Location 'West Central US' -FrontendIpConfiguration $frontendIP1 -LoadBalancingRule $lbrule -BackendAddressPool $beAddressPool -Probe $healthProbe
    ```

11. Adicione a segunda piscina de endereços de backend e a configuração IP frontal ao seu equilibrador de carga recém-criado:

    ```powershell
    $mylb = Get-AzLoadBalancer -Name "mylb" -ResourceGroupName $myResourceGroup | Add-AzLoadBalancerBackendAddressPoolConfig -Name fabrikampool | Set-AzLoadBalancer

    $mylb | Add-AzLoadBalancerFrontendIpConfig -Name fabrikamfe -PublicIpAddress $publicIP2 | Set-AzLoadBalancer
    
    Add-AzLoadBalancerRuleConfig -Name HTTP -LoadBalancer $mylb -FrontendIpConfiguration $frontendIP2 -BackendAddressPool $beaddresspool2 -Probe $healthProbe -Protocol Tcp -FrontendPort 80 -BackendPort 80 | Set-AzLoadBalancer
    ```

12. Os comandos abaixo obtêm os NICs e, em seguida, adicionar ambas as configurações IP de cada NIC secundário ao conjunto de endereços de backend do equilibrista de carga:

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

13. Por fim, tem de configurar os registos de recursos DNS para apontar para o respetivo endereço IP frontal do Balancer de Carga. Pode acolher os seus domínios em Azure DNS. Para obter mais informações sobre a utilização de DNS Azure com Balancer de Carga, consulte [Utilizar o Azure DNS com outros serviços Azure](../dns/dns-for-azure-services.md).

## <a name="next-steps"></a>Passos seguintes
- Saiba mais sobre como combinar serviços de equilíbrio de carga em Azure em [Serviços de equilíbrio de carga em Azure](../traffic-manager/traffic-manager-load-balancing-azure.md).
- Saiba como pode utilizar diferentes tipos de registos em Azure para gerir e resolver problemas em [registos de carregadores Azure Monitor para O Equilíbrio de Carga Seleções Azure](../load-balancer/load-balancer-monitor-log.md).
