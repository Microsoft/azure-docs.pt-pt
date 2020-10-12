---
title: Equilíbrio de carga em várias configurações IP - Azure CLI
titleSuffix: Azure Load Balancer
description: Neste artigo, aprenda sobre o equilíbrio de carga através das configurações ip primárias e secundárias utilizando O Azure CLI.
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: how-to
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: allensu
ms.openlocfilehash: e8fedad40c18818932bf37dfe93c1b236357c30b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87001610"
---
# <a name="load-balancing-on-multiple-ip-configurations-using-powershell"></a>Equilibrar a carga em várias configurações IP utilizando PowerShell

> [!div class="op_single_selector"]
> * [Portal](load-balancer-multiple-ip.md)
> * [CLI](load-balancer-multiple-ip-cli.md)
> * [PowerShell](load-balancer-multiple-ip-powershell.md)


Este artigo descreve como utilizar o Azure Load Balancer com vários endereços IP numa interface de rede secundária (NIC). Para este cenário, temos dois VMs a executar o Windows, cada um com um NIC primário e secundário. Cada um dos NICs secundários tem duas configurações IP. Cada VM acolhe ambos os websites contoso.com e fabrikam.com. Cada website está ligado a uma das configurações ip no NIC secundário. Utilizamos o Azure Load Balancer para expor dois endereços IP frontend, um para cada website, para distribuir tráfego para a respetiva configuração IP para o website. Este cenário utiliza o mesmo número de porta em ambos os frontends, bem como ambos os endereços IP do pool backend.

![Imagem de cenário LB](./media/load-balancer-multiple-ip/lb-multi-ip.PNG)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="steps-to-load-balance-on-multiple-ip-configurations"></a>Passos para carregar o equilíbrio em várias configurações de IP

Siga os passos abaixo para alcançar o cenário delineado neste artigo:

1. Instale o Azure PowerShell. Veja [How to install and configure Azure PowerShell (Como instalar e configurar o Azure PowerShell)](/powershell/azure/) para obter informações sobre como instalar a versão mais recente do Azure PowerShell, selecionar a subscrição que quer utilizar e iniciar sessão na sua conta do Azure.
2. Criar um grupo de recursos utilizando as seguintes definições:

    ```powershell
    $location = "westcentralus".
    $myResourceGroup = "contosofabrikam"
    ```

    Para mais informações, consulte o passo 2 da [Create a Resource Group](../virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm.md?toc=%2fazure%2fload-balancer%2ftoc.json).

3. [Crie um Conjunto de Disponibilidade](../virtual-machines/windows/tutorial-availability-sets.md?toc=%2fazure%2fload-balancer%2ftoc.json) para conter os seus VMs. Para este cenário, utilize o seguinte comando:

    ```powershell
    New-AzAvailabilitySet -ResourceGroupName "contosofabrikam" -Name "myAvailset" -Location "West Central US"
    ```

4. Siga os passos 3 a 5 em Criar um artigo [do Windows VM](../virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm.md?toc=%2fazure%2fload-balancer%2ftoc.json) para preparar a criação de um VM com um único NIC. Executar o passo 6.1 e utilizar o seguinte em vez do passo 6.2:

    ```powershell
    $availset = Get-AzAvailabilitySet -ResourceGroupName "contosofabrikam" -Name "myAvailset"
    New-AzVMConfig -VMName "VM1" -VMSize "Standard_DS1_v2" -AvailabilitySetId $availset.Id
    ```

    Em [seguida,](../virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm.md?toc=%2fazure%2fload-balancer%2ftoc.json) complete Criar um Windows VM passos 6.3 a 6.8.

5. Adicione uma segunda configuração IP a cada um dos VMs. Siga as instruções em [Atribuir vários endereços IP a artigos de máquinas virtuais.](../virtual-network/virtual-network-multiple-ip-addresses-powershell.md#add) Utilize as seguintes definições de configuração:

    ```powershell
    $NicName = "VM1-NIC2"
    $RgName = "contosofabrikam"
    $NicLocation = "West Central US"
    $IPConfigName4 = "VM1-ipconfig2"
    $Subnet1 = Get-AzVirtualNetworkSubnetConfig -Name "mySubnet" -VirtualNetwork $myVnet
    ```

    Não é necessário associar as configurações de IP secundárias a IPs públicos para efeitos deste tutorial. Editar o comando para remover a parte pública da associação IP.

6. Complete os passos 4 a 6 deste artigo novamente para VM2. Certifique-se de que substitui o nome VM para VM2 ao fazê-lo. Note que não precisa de criar uma rede virtual para o segundo VM. Pode ou não criar uma nova sub-rede com base no seu caso de utilização.

7. Crie dois endereços IP públicos e guarde-os nas variáveis apropriadas, conforme mostrado:

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

11. Adicione a segunda configuração de endereço de backend e frontend IP ao seu equilibrador de carga recém-criado:

    ```powershell
    $mylb = Get-AzLoadBalancer -Name "mylb" -ResourceGroupName $myResourceGroup | Add-AzLoadBalancerBackendAddressPoolConfig -Name fabrikampool | Set-AzLoadBalancer

    $mylb | Add-AzLoadBalancerFrontendIpConfig -Name fabrikamfe -PublicIpAddress $publicIP2 | Set-AzLoadBalancer
    
    Add-AzLoadBalancerRuleConfig -Name HTTP -LoadBalancer $mylb -FrontendIpConfiguration $frontendIP2 -BackendAddressPool $beaddresspool2 -Probe $healthProbe -Protocol Tcp -FrontendPort 80 -BackendPort 80 | Set-AzLoadBalancer
    ```

12. Os comandos abaixo obtêm os NICs e, em seguida, adicionam ambas as configurações IP de cada NIC secundário ao conjunto de endereços de backend do equilibrador de carga:

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

13. Por fim, tem de configurar registos de recursos DNS para indicar o respetivo endereço IP frontal do Balanceador de Carga. Pode hospedar os seus domínios em Azure DNS. Para obter mais informações sobre a utilização do Azure DNS com balanceador de carga, consulte [utilizar o Azure DNS com outros serviços Azure](../dns/dns-for-azure-services.md).

## <a name="next-steps"></a>Passos seguintes
- Saiba mais sobre como combinar serviços de equilíbrio de carga em Azure em [Utilizar serviços de equilíbrio de carga em Azure](../traffic-manager/traffic-manager-load-balancing-azure.md).
- Saiba como pode utilizar diferentes tipos de registos em Azure para gerir e resolver problemas nos [registos do Azure Monitor para o Azure Load Balancer](../load-balancer/load-balancer-monitor-log.md).
