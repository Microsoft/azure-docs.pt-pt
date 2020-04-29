---
title: Configurar túneis forçados para ligações local-a-local
description: Como redirecionar ou "forçar" todo o tráfego ligado à Internet de volta ao seu local no local.
services: vpn-gateway
titleSuffix: Azure VPN Gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 02/01/2018
ms.author: cherylmc
ms.openlocfilehash: fc35654403bbe1375d4188476b11fd0453f74345
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79244631"
---
# <a name="configure-forced-tunneling-using-the-azure-resource-manager-deployment-model"></a>Configurar o túnel forçado utilizando o modelo de implementação Azure Resource Manager

O túnel forçado permite redirecionar ou “forçar” todo o tráfego associado à Internet novamente para a localização no local através de um túnel de VPN site a site para inspeção e auditoria. Este é um requisito crítico de segurança para a maioria das políticas de TI da empresa. Sem túneis forçados, o tráfego ligado à Internet dos seus VMs em Azure percorre sempre desde a infraestrutura da rede Azure diretamente para a Internet, sem a opção de permitir inspecionar ou auditar o tráfego. O acesso não autorizado à Internet pode potencialmente levar à divulgação de informações ou a outros tipos de falhas de segurança.



[!INCLUDE [vpn-gateway-classic-rm](../../includes/vpn-gateway-classic-rm-include.md)] 

Este artigo acompanha-o através da configuração de túneis forçados para redes virtuais criadas utilizando o modelo de implementação do Gestor de Recursos. O túnel forçado pode ser configurado utilizando o PowerShell, não através do portal. Se quiser configurar túneis forçados para o modelo de implantação clássico, selecione artigo clássico da seguinte lista de dropdown:

> [!div class="op_single_selector"]
> * [PowerShell - Clássico](vpn-gateway-about-forced-tunneling.md)
> * [PowerShell – Resource Manager](vpn-gateway-forced-tunneling-rm.md)
> 
> 

## <a name="about-forced-tunneling"></a>Sobre túneis forçados

O diagrama que se segue ilustra como funciona o túnel forçado. 

![Túnel Forçado](./media/vpn-gateway-forced-tunneling-rm/forced-tunnel.png)

No exemplo acima, a subnet Frontend não é forçada a fazer túneis. As cargas de trabalho na subnet Frontend podem continuar a aceitar e responder diretamente aos pedidos dos clientes da Internet. As subredes de nível médio e backend são forçadas a fazer túneis. Quaisquer ligações de saída destas duas subredes para a Internet serão forçadas ou redirecionadas de volta para um local no local através de um dos túneis VpN S2S.

Isto permite-lhe restringir e inspecionar o acesso à Internet a partir das suas máquinas virtuais ou serviços de nuvem em Azure, ao mesmo tempo que continua a ativar a sua arquitetura de serviço de vários níveis necessária. Se não houver cargas de trabalho viradas para a Internet nas suas redes virtuais, também pode aplicar túneis forçados a todas as redes virtuais.

## <a name="requirements-and-considerations"></a>Requisitos e considerações

O túnel forçado em Azure é configurado através de rotas definidas pelo utilizador da rede virtual. Redirecionar o tráfego para um local no local é expresso como uma Rota Padrão para o gateway VPN Azure. Para obter mais informações sobre o encaminhamento definido pelo utilizador e as redes virtuais, consulte as rotas definidas pelo Utilizador e o [reencaminhamento de IP](../virtual-network/virtual-networks-udr-overview.md).

* Cada subnet de rede virtual tem uma mesa de encaminhamento do sistema incorporada. A tabela de encaminhamento do sistema tem os seguintes três grupos de rotas:
  
  * **Rotas VNet locais:** Diretamente para os VMs de destino na mesma rede virtual.
  * **Rotas no local:** Para o portal Azure VPN.
  * **Rota padrão:** Diretamente para a Internet. Os pacotes destinados aos endereços IP privados não abrangidos pelas duas rotas anteriores são retirados.
* Este procedimento utiliza rotas definidas pelo utilizador (UDR) para criar uma tabela de encaminhamento para adicionar uma rota predefinida e, em seguida, associar a tabela de encaminhamento à sua subnet VNet para permitir a escavação forçada nessas subredes.
* O túnel forçado deve ser associado a uma VNet que tenha um gateway VPN baseado em rota. É necessário definir um "site predefinido" entre os sites locais inter-instalações ligados à rede virtual. Além disso, o dispositivo VPN no local deve ser configurado utilizando 0.0.0.0/0 como selecionadores de tráfego. 
* O túnel forçado ExpressRoute não está configurado através deste mecanismo, mas sim, é ativado pela publicidade de uma rota padrão através das sessões de peering Do ExpressRoute BGP. Para mais informações, consulte a [Documentação ExpressRoute](https://azure.microsoft.com/documentation/services/expressroute/).

## <a name="configuration-overview"></a>Descrição geral da configuração

O seguinte procedimento ajuda-o a criar um grupo de recursos e um VNet. Em seguida, criará um gateway VPN e configurará túneis forçados. Neste procedimento, a rede virtual 'MultiTier-VNet' tem três subredes: 'Frontend', 'Midtier' e 'Backend', com quatro ligações transversais: 'DefaultSiteHQ', e três Branches.

As etapas de procedimento estabelecem o 'PadrãoSiteHQ' como a ligação padrão do local para a escavação forçada e configuram as subredes 'Midtier' e 'Backend' para utilizar túneis forçados.

## <a name="before-you-begin"></a><a name="before"></a>Antes de começar

Instale a versão mais recente dos cmdlets do PowerShell do Azure Resource Manager. Veja [Como instalar e configurar o Azure PowerShell](/powershell/azure/overview) para obter mais informações sobre como instalar os cmdlets PowerShell.

> [!IMPORTANT]
> É necessária a instalação da versão mais recente dos cmdlets PowerShell. Caso contrário, poderá receber erros de validação ao executar alguns dos cmdlets.
>
>

### <a name="to-log-in"></a>Para fazer login

[!INCLUDE [To log in](../../includes/vpn-gateway-cloud-shell-ps-login.md)]

## <a name="configure-forced-tunneling"></a>Configurar túnel forçado

> [!NOTE]
> Pode ver avisos que digam "O tipo de objeto de saída deste cmdlet será modificado numa futura libertação". Este comportamento é esperado e você pode ignorar com segurança estes avisos.
>
>


1. Crie um grupo de recursos.

   ```powershell
   New-AzResourceGroup -Name 'ForcedTunneling' -Location 'North Europe'
   ```
2. Crie uma rede virtual e especifique as subredes.

   ```powershell 
   $s1 = New-AzVirtualNetworkSubnetConfig -Name "Frontend" -AddressPrefix "10.1.0.0/24"
   $s2 = New-AzVirtualNetworkSubnetConfig -Name "Midtier" -AddressPrefix "10.1.1.0/24"
   $s3 = New-AzVirtualNetworkSubnetConfig -Name "Backend" -AddressPrefix "10.1.2.0/24"
   $s4 = New-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -AddressPrefix "10.1.200.0/28"
   $vnet = New-AzVirtualNetwork -Name "MultiTier-VNet" -Location "North Europe" -ResourceGroupName "ForcedTunneling" -AddressPrefix "10.1.0.0/16" -Subnet $s1,$s2,$s3,$s4
   ```
3. Crie os portões da rede local.

   ```powershell
   $lng1 = New-AzLocalNetworkGateway -Name "DefaultSiteHQ" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -GatewayIpAddress "111.111.111.111" -AddressPrefix "192.168.1.0/24"
   $lng2 = New-AzLocalNetworkGateway -Name "Branch1" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -GatewayIpAddress "111.111.111.112" -AddressPrefix "192.168.2.0/24"
   $lng3 = New-AzLocalNetworkGateway -Name "Branch2" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -GatewayIpAddress "111.111.111.113" -AddressPrefix "192.168.3.0/24"
   $lng4 = New-AzLocalNetworkGateway -Name "Branch3" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -GatewayIpAddress "111.111.111.114" -AddressPrefix "192.168.4.0/24"
   ```
4. Crie a tabela de rotas e a regra da rota.

   ```powershell
   New-AzRouteTable –Name "MyRouteTable" -ResourceGroupName "ForcedTunneling" –Location "North Europe"
   $rt = Get-AzRouteTable –Name "MyRouteTable" -ResourceGroupName "ForcedTunneling" 
   Add-AzRouteConfig -Name "DefaultRoute" -AddressPrefix "0.0.0.0/0" -NextHopType VirtualNetworkGateway -RouteTable $rt
   Set-AzRouteTable -RouteTable $rt
   ```
5. Associe a tabela de rotas às subnets Midtier e Backend.

   ```powershell
   $vnet = Get-AzVirtualNetwork -Name "MultiTier-Vnet" -ResourceGroupName "ForcedTunneling"
   Set-AzVirtualNetworkSubnetConfig -Name "MidTier" -VirtualNetwork $vnet -AddressPrefix "10.1.1.0/24" -RouteTable $rt
   Set-AzVirtualNetworkSubnetConfig -Name "Backend" -VirtualNetwork $vnet -AddressPrefix "10.1.2.0/24" -RouteTable $rt
   Set-AzVirtualNetwork -VirtualNetwork $vnet
   ```
6. Crie o portal da rede virtual. Este passo leva algum tempo para completar, às vezes 45 minutos ou mais, porque está a criar e configurar o portal. Se vir erros do ValidateSet em relação ao valor GatewaySKU, verifique se instalou a [versão mais recente dos cmdlets PowerShell](#before). A versão mais recente dos cmdlets PowerShell contém os novos valores validados para as mais recentes Gateway SKUs.

   ```powershell
   $pip = New-AzPublicIpAddress -Name "GatewayIP" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -AllocationMethod Dynamic
   $gwsubnet = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet
   $ipconfig = New-AzVirtualNetworkGatewayIpConfig -Name "gwIpConfig" -SubnetId $gwsubnet.Id -PublicIpAddressId $pip.Id
   New-AzVirtualNetworkGateway -Name "Gateway1" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -IpConfigurations $ipconfig -GatewayType Vpn -VpnType RouteBased -GatewaySku VpnGw1 -EnableBgp $false
   ```
7. Atribuir um site predefinido ao gateway da rede virtual. O **-GatewayDefaultSite** é o parâmetro cmdlet que permite que a configuração forçada de encaminhamento funcione, por isso, tome o cuidado de configurar esta definição corretamente. 

   ```powershell
   $LocalGateway = Get-AzLocalNetworkGateway -Name "DefaultSiteHQ" -ResourceGroupName "ForcedTunneling"
   $VirtualGateway = Get-AzVirtualNetworkGateway -Name "Gateway1" -ResourceGroupName "ForcedTunneling"
   Set-AzVirtualNetworkGatewayDefaultSite -GatewayDefaultSite $LocalGateway -VirtualNetworkGateway $VirtualGateway
   ```
8. Estabeleça as ligações VPN site-to-site.

   ```powershell
   $gateway = Get-AzVirtualNetworkGateway -Name "Gateway1" -ResourceGroupName "ForcedTunneling"
   $lng1 = Get-AzLocalNetworkGateway -Name "DefaultSiteHQ" -ResourceGroupName "ForcedTunneling" 
   $lng2 = Get-AzLocalNetworkGateway -Name "Branch1" -ResourceGroupName "ForcedTunneling" 
   $lng3 = Get-AzLocalNetworkGateway -Name "Branch2" -ResourceGroupName "ForcedTunneling" 
   $lng4 = Get-AzLocalNetworkGateway -Name "Branch3" -ResourceGroupName "ForcedTunneling" 
    
   New-AzVirtualNetworkGatewayConnection -Name "Connection1" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -VirtualNetworkGateway1 $gateway -LocalNetworkGateway2 $lng1 -ConnectionType IPsec -SharedKey "preSharedKey"
   New-AzVirtualNetworkGatewayConnection -Name "Connection2" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -VirtualNetworkGateway1 $gateway -LocalNetworkGateway2 $lng2 -ConnectionType IPsec -SharedKey "preSharedKey"
   New-AzVirtualNetworkGatewayConnection -Name "Connection3" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -VirtualNetworkGateway1 $gateway -LocalNetworkGateway2 $lng3 -ConnectionType IPsec -SharedKey "preSharedKey"
   New-AzVirtualNetworkGatewayConnection -Name "Connection4" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -VirtualNetworkGateway1 $gateway -LocalNetworkGateway2 $lng4 -ConnectionType IPsec -SharedKey "preSharedKey"
    
   Get-AzVirtualNetworkGatewayConnection -Name "Connection1" -ResourceGroupName "ForcedTunneling"
   ```
