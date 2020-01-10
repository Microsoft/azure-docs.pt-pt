---
title: Configurar o túnel forçado para conexões site a site
description: Como redirecionar ou "forçar" todo o tráfego vinculado à Internet para sua localização no local.
services: vpn-gateway
titleSuffix: Azure VPN Gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 02/01/2018
ms.author: cherylmc
ms.openlocfilehash: c0b32bfba61f1c6f3f00c5189f611d84069dd9da
ms.sourcegitcommit: 5b073caafebaf80dc1774b66483136ac342f7808
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/09/2020
ms.locfileid: "75779676"
---
# <a name="configure-forced-tunneling-using-the-azure-resource-manager-deployment-model"></a>Configurar o túnel forçado utilizando o modelo de implementação Azure Resource Manager

O túnel forçado permite redirecionar ou “forçar” todo o tráfego associado à Internet novamente para a localização no local através de um túnel de VPN site a site para inspeção e auditoria. Este é um requisito de críticas de segurança de TI de empresas a maioria das políticas. Sem o túnel forçado, o tráfego de entrada na Internet de suas VMs no Azure sempre percorre a infraestrutura de rede do Azure diretamente para a Internet, sem a opção de permitir que você inspecione ou audite o tráfego. Acesso não autorizado da Internet pode potencialmente conduzir a divulgação de informações ou outros tipos de falhas de segurança.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [vpn-gateway-classic-rm](../../includes/vpn-gateway-classic-rm-include.md)] 

Este artigo orienta você pela configuração do túnel forçado para redes virtuais criadas usando o modelo de implantação do Resource Manager. O túnel forçado pode ser configurado com o PowerShell, não através do portal. Se você quiser configurar o túnel forçado para o modelo de implantação clássico, selecione o artigo clássico na lista suspensa a seguir:

> [!div class="op_single_selector"]
> * [PowerShell – Clássica](vpn-gateway-about-forced-tunneling.md)
> * [PowerShell – Resource Manager](vpn-gateway-forced-tunneling-rm.md)
> 
> 

## <a name="about-forced-tunneling"></a>Sobre o túnel forçado

O diagrama a seguir ilustra como funciona o túnel forçado. 

![Túnel Forçado](./media/vpn-gateway-forced-tunneling-rm/forced-tunnel.png)

No exemplo acima, a sub-rede frontend não é encapsulada em túnel forçado. As cargas de trabalho na sub-rede de front-end podem continuar a aceitar e responder aos pedidos dos clientes da Internet diretamente. As sub-redes de escalão médio e de back-end são forçadas encapsulada. Qualquer conexões de saída destas duas sub-redes para a Internet serão forçados ou redirecionados para um site no local através de um dos túneis S2S VPN.

Isto permite-lhe restringir e inspecionar o acesso à Internet das suas máquinas virtuais ou serviços no Azure, de cloud e continuar a ativar a sua arquitetura de várias camadas de serviço necessária. Se não houver nenhuma carga de trabalho voltada para a Internet em suas redes virtuais, você também poderá aplicar o túnel forçado a todas as redes virtuais.

## <a name="requirements-and-considerations"></a>Requisitos e considerações

O túnel forçado no Azure é configurado por meio de rotas definidas pelo usuário de rede virtual. Redirecionar o tráfego para um site no local é expressa como uma rota predefinida para o gateway de VPN do Azure. Para obter mais informações sobre roteamento definido pelo usuário e redes virtuais, consulte [rotas definidas pelo usuário e encaminhamento de IP](../virtual-network/virtual-networks-udr-overview.md).

* Cada sub-rede da rede virtual tem uma tabela de roteamento interno, do sistema. A tabela de encaminhamento do sistema tem os seguintes três grupos de rotas:
  
  * **As rotas locais de VNet:** diretamente para o destino de VMs na mesma rede virtual.
  * **As rotas locais:** para o VPN gateway do Azure.
  * **Rota predefinida:** diretamente à Internet. Os pacotes destinados aos endereços IP privados não cobertos pelas duas rotas anteriores são removidos.
* Este procedimento usa UDR (rotas definidas pelo usuário) para criar uma tabela de roteamento para adicionar uma rota padrão e, em seguida, associar a tabela de roteamento às sub-redes de rede virtual para habilitar o túnel forçado nessas sub-redes.
* O túnel forçado deve ser associado a uma VNet que tenha um gateway de VPN baseado em rota. Tem de definir um "site predefinido" entre os sites locais em vários locais ligado à rede virtual. Além disso, o dispositivo VPN local deve ser configurado usando 0.0.0.0/0 como seletores de tráfego. 
* Túnel forçado do ExpressRoute não está configurado por intermédio deste mecanismo, mas em vez disso, está ativado por uma rota predefinida por meio de sessões de peering de BGP de ExpressRoute de publicidade. Para obter mais informações, consulte a [documentação do ExpressRoute](https://azure.microsoft.com/documentation/services/expressroute/).

## <a name="configuration-overview"></a>Descrição geral da configuração

O procedimento a seguir ajuda a criar um grupo de recursos e uma VNet. Em seguida, você criará um gateway de VPN e configurará o túnel forçado. Neste procedimento, a rede virtual ' multicamada-VNet ' tem três sub-redes: ' frontend ', ' Midtier ' e ' backend ', com quatro conexões entre locais: ' DefaultSiteHQ ' e três ramificações.

As etapas de procedimento definem o ' DefaultSiteHQ ' como a conexão de site padrão para túnel forçado e configura as sub-redes ' Midtier ' e ' backend ' para usar o túnel forçado.

## <a name="before"></a>Antes de começar

Instale a versão mais recente dos cmdlets do PowerShell do Azure Resource Manager. Veja [Como instalar e configurar o Azure PowerShell](/powershell/azure/overview) para obter mais informações sobre como instalar os cmdlets PowerShell.

> [!IMPORTANT]
> É necessário instalar a versão mais recente dos cmdlets do PowerShell. Caso contrário, você poderá receber erros de validação ao executar alguns dos cmdlets.
>
>

### <a name="to-log-in"></a>Para fazer logon

[!INCLUDE [To log in](../../includes/vpn-gateway-ps-login-include.md)]

## <a name="configure-forced-tunneling"></a>Configurar túnel forçado

> [!NOTE]
> Você pode ver avisos dizendo "o tipo de objeto de saída deste cmdlet será modificado em uma versão futura". Esse é o comportamento esperado e você pode ignorar esses avisos com segurança.
>
>


1. Crie um grupo de recursos.

   ```powershell
   New-AzResourceGroup -Name 'ForcedTunneling' -Location 'North Europe'
   ```
2. Crie uma rede virtual e especifique sub-redes.

   ```powershell 
   $s1 = New-AzVirtualNetworkSubnetConfig -Name "Frontend" -AddressPrefix "10.1.0.0/24"
   $s2 = New-AzVirtualNetworkSubnetConfig -Name "Midtier" -AddressPrefix "10.1.1.0/24"
   $s3 = New-AzVirtualNetworkSubnetConfig -Name "Backend" -AddressPrefix "10.1.2.0/24"
   $s4 = New-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -AddressPrefix "10.1.200.0/28"
   $vnet = New-AzVirtualNetwork -Name "MultiTier-VNet" -Location "North Europe" -ResourceGroupName "ForcedTunneling" -AddressPrefix "10.1.0.0/16" -Subnet $s1,$s2,$s3,$s4
   ```
3. Crie os gateways de rede local.

   ```powershell
   $lng1 = New-AzLocalNetworkGateway -Name "DefaultSiteHQ" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -GatewayIpAddress "111.111.111.111" -AddressPrefix "192.168.1.0/24"
   $lng2 = New-AzLocalNetworkGateway -Name "Branch1" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -GatewayIpAddress "111.111.111.112" -AddressPrefix "192.168.2.0/24"
   $lng3 = New-AzLocalNetworkGateway -Name "Branch2" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -GatewayIpAddress "111.111.111.113" -AddressPrefix "192.168.3.0/24"
   $lng4 = New-AzLocalNetworkGateway -Name "Branch3" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -GatewayIpAddress "111.111.111.114" -AddressPrefix "192.168.4.0/24"
   ```
4. Crie a tabela de rotas e a regra de rota.

   ```powershell
   New-AzRouteTable –Name "MyRouteTable" -ResourceGroupName "ForcedTunneling" –Location "North Europe"
   $rt = Get-AzRouteTable –Name "MyRouteTable" -ResourceGroupName "ForcedTunneling" 
   Add-AzRouteConfig -Name "DefaultRoute" -AddressPrefix "0.0.0.0/0" -NextHopType VirtualNetworkGateway -RouteTable $rt
   Set-AzRouteTable -RouteTable $rt
   ```
5. Associe a tabela de rotas às sub-redes Midtier e backend.

   ```powershell
   $vnet = Get-AzVirtualNetwork -Name "MultiTier-Vnet" -ResourceGroupName "ForcedTunneling"
   Set-AzVirtualNetworkSubnetConfig -Name "MidTier" -VirtualNetwork $vnet -AddressPrefix "10.1.1.0/24" -RouteTable $rt
   Set-AzVirtualNetworkSubnetConfig -Name "Backend" -VirtualNetwork $vnet -AddressPrefix "10.1.2.0/24" -RouteTable $rt
   Set-AzVirtualNetwork -VirtualNetwork $vnet
   ```
6. Crie o gateway de rede virtual. Essa etapa leva algum tempo para ser concluída, às vezes 45 minutos ou mais, porque você está criando e Configurando o gateway. Se você vir erros Validset em relação ao valor GatewaySKU, verifique se você instalou a [versão mais recente dos cmdlets do PowerShell](#before). A versão mais recente dos cmdlets do PowerShell contém os novos valores validados para as SKUs de gateway mais recentes.

   ```powershell
   $pip = New-AzPublicIpAddress -Name "GatewayIP" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -AllocationMethod Dynamic
   $gwsubnet = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet
   $ipconfig = New-AzVirtualNetworkGatewayIpConfig -Name "gwIpConfig" -SubnetId $gwsubnet.Id -PublicIpAddressId $pip.Id
   New-AzVirtualNetworkGateway -Name "Gateway1" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -IpConfigurations $ipconfig -GatewayType Vpn -VpnType RouteBased -GatewaySku VpnGw1 -EnableBgp $false
   ```
7. Atribua um site padrão ao gateway de rede virtual. O **-GatewayDefaultSite** é o parâmetro de cmdlet que permite que a configuração de roteamento forçado funcione, portanto, tome cuidado para definir essa configuração corretamente. 

   ```powershell
   $LocalGateway = Get-AzLocalNetworkGateway -Name "DefaultSiteHQ" -ResourceGroupName "ForcedTunneling"
   $VirtualGateway = Get-AzVirtualNetworkGateway -Name "Gateway1" -ResourceGroupName "ForcedTunneling"
   Set-AzVirtualNetworkGatewayDefaultSite -GatewayDefaultSite $LocalGateway -VirtualNetworkGateway $VirtualGateway
   ```
8. Estabeleça as conexões VPN site a site.

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
