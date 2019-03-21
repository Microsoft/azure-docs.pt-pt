---
title: 'Configure o túnel forçado para ligações do Azure Site a Site: O Resource Manager | Documentos da Microsoft'
description: Como redirecionar ou "forçar" todo o tráfego vinculado à Internet para sua localização no local.
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: ''
tags: azure-resource-manager
ms.assetid: cbe58db8-b598-4c9f-ac88-62c865eb8721
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/01/2018
ms.author: cherylmc
ms.openlocfilehash: b4d9a469e46d964055d9459901ebdb9c6d04cf24
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58078351"
---
# <a name="configure-forced-tunneling-using-the-azure-resource-manager-deployment-model"></a>Configurar o túnel forçado utilizando o modelo de implementação Azure Resource Manager

O túnel forçado permite-lhe redirecionamento ou "forçar" todo o tráfego vinculado à Internet de volta para a sua localização no local através de um túnel VPN de Site a Site para inspeção e auditoria. Este é um requisito de críticas de segurança de TI de empresas a maioria das políticas. Sem forçar o tráfego de túnel, vinculado à Internet suas VMS no Azure sempre traverses da infraestrutura de rede do Azure diretamente saída à Internet, sem a opção para que possa inspecionar ou o tráfego de auditoria. Acesso não autorizado da Internet pode potencialmente conduzir a divulgação de informações ou outros tipos de falhas de segurança.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [vpn-gateway-classic-rm](../../includes/vpn-gateway-classic-rm-include.md)] 

Este artigo orienta-o por meio de configurar imposição de túnel para redes virtuais criadas com o modelo de implementação do Resource Manager. O túnel forçado pode ser configurado com o PowerShell, não através do portal. Se quiser configurar o túnel forçado para o modelo de implementação clássica, seleccione o artigo clássico na lista pendente seguinte:

> [!div class="op_single_selector"]
> * [PowerShell – Clássica](vpn-gateway-about-forced-tunneling.md)
> * [PowerShell – Resource Manager](vpn-gateway-forced-tunneling-rm.md)
> 
> 

## <a name="about-forced-tunneling"></a>Aproximadamente túnel forçado

O diagrama seguinte ilustra como forçada funciona encapsulamento. 

![Túnel Forçado](./media/vpn-gateway-forced-tunneling-rm/forced-tunnel.png)

No exemplo acima, o front-end sub-rede não é forçado em túnel. As cargas de trabalho na sub-rede de front-end podem continuar a aceitar e responder aos pedidos dos clientes da Internet diretamente. As sub-redes de escalão médio e de back-end são forçadas encapsulada. Qualquer conexões de saída destas duas sub-redes para a Internet serão forçados ou redirecionados para um site no local através de um dos túneis S2S VPN.

Isto permite-lhe restringir e inspecionar o acesso à Internet das suas máquinas virtuais ou serviços no Azure, de cloud e continuar a ativar a sua arquitetura de várias camadas de serviço necessária. Se não houver nenhum cargas de trabalho de acesso à Internet em suas redes virtuais, também pode aplicar o túnel forçado para as redes virtuais inteiras.

## <a name="requirements-and-considerations"></a>Requisitos e considerações

O túnel forçado no Azure está configurado por meio de rotas definidas pelo utilizador de rede virtual. Redirecionar o tráfego para um site no local é expressa como uma rota predefinida para o gateway de VPN do Azure. Para obter mais informações sobre o encaminhamento definido pelo utilizador e redes virtuais, consulte [rotas definidas pelo utilizador e reencaminhamento IP](../virtual-network/virtual-networks-udr-overview.md).

* Cada sub-rede da rede virtual tem uma tabela de roteamento interno, do sistema. A tabela de encaminhamento do sistema tem os seguintes três grupos de rotas:
  
  * **Rotas de VNet locais:** Diretamente para o destino VMs na mesma rede virtual.
  * **Rotas no local:** Para o gateway de VPN do Azure.
  * **Rota predefinida:** Diretamente à Internet. Pacotes destinados aos endereços IP privados, não abrangidos pelas anteriores duas rotas são ignorados.
* Este procedimento utiliza as rotas definidas pelo utilizador (UDR) para criar uma tabela de encaminhamento para adicionar uma rota predefinida e, em seguida, associar a tabela de encaminhamento para as sub-redes de VNet para ativar o protocolo de túnel forçado nessas sub-redes.
* O túnel forçado tem de ser associado a uma VNet que tenha um gateway VPN baseado na rota. Tem de definir um "site predefinido" entre os sites locais em vários locais ligado à rede virtual. Além disso, o dispositivo VPN no local deve ser configurado usando 0.0.0.0/0 como seletores de tráfego. 
* Túnel forçado do ExpressRoute não está configurado por intermédio deste mecanismo, mas em vez disso, está ativado por uma rota predefinida por meio de sessões de peering de BGP de ExpressRoute de publicidade. Para obter mais informações, consulte a [documentação do ExpressRoute](https://azure.microsoft.com/documentation/services/expressroute/).

## <a name="configuration-overview"></a>Descrição geral da configuração

O procedimento seguinte ajuda-o a criar um grupo de recursos e uma VNet. Em seguida, irá criar um gateway de VPN e configurar o túnel forçado. Neste procedimento, a rede virtual de várias camadas-VNet tem três sub-redes: "Front-end", "Midtier" e "Back-end", com quatro ligações entre locais: "DefaultSiteHQ" e três ramos.

Os passos do procedimento definir o 'DefaultSiteHQ"da ligação de site predefinido para o túnel forçado e configurar o Midtier e sub-redes"Back-end"para utilizar o túnel forçado.

## <a name="before"></a>Antes de começar

Instale a versão mais recente dos cmdlets do PowerShell do Azure Resource Manager. Veja [Como instalar e configurar o Azure PowerShell](/powershell/azure/overview) para obter mais informações sobre como instalar os cmdlets PowerShell.

> [!IMPORTANT]
> É necessário instalar a versão mais recente dos cmdlets do PowerShell. Caso contrário, poderá receber erros de validação ao executar alguns dos cmdlets.
>
>

### <a name="to-log-in"></a>Para iniciar sessão

[!INCLUDE [To log in](../../includes/vpn-gateway-ps-login-include.md)]

## <a name="configure-forced-tunneling"></a>Configurar túnel forçado

> [!NOTE]
> Pode ver avisos dizendo "o tipo de objeto de saída deste cmdlet irá ser modificado numa futura versão". Este comportamento está previsto e pode ignorar com segurança estes avisos.
>
>


1. Crie um grupo de recursos.

   ```powershell
   New-AzResourceGroup -Name 'ForcedTunneling' -Location 'North Europe'
   ```
2. Criar uma rede virtual e especificar sub-redes.

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
5. Associe a tabela de rotas às sub-redes Midtier e de back-end.

   ```powershell
   $vnet = Get-AzVirtualNetwork -Name "MultiTier-Vnet" -ResourceGroupName "ForcedTunneling"
   Set-AzVirtualNetworkSubnetConfig -Name "MidTier" -VirtualNetwork $vnet -AddressPrefix "10.1.1.0/24" -RouteTable $rt
   Set-AzVirtualNetworkSubnetConfig -Name "Backend" -VirtualNetwork $vnet -AddressPrefix "10.1.2.0/24" -RouteTable $rt
   Set-AzVirtualNetwork -VirtualNetwork $vnet
   ```
6. Crie o gateway de rede virtual. Este passo demora algum tempo a concluir, às vezes, 45 minutos ou mais, uma vez que estiver a criar e configurar o gateway. Se vir erros ValidateSet sobre o valor de GatewaySKU, confirme que tem instalado o [a versão mais recente dos cmdlets do PowerShell](#before). A versão mais recente dos cmdlets do PowerShell contém novos valores validados para os SKUs de Gateway mais recente.

   ```powershell
   $pip = New-AzPublicIpAddress -Name "GatewayIP" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -AllocationMethod Dynamic
   $gwsubnet = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet
   $ipconfig = New-AzVirtualNetworkGatewayIpConfig -Name "gwIpConfig" -SubnetId $gwsubnet.Id -PublicIpAddressId $pip.Id
   New-AzVirtualNetworkGateway -Name "Gateway1" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -IpConfigurations $ipconfig -GatewayType Vpn -VpnType RouteBased -GatewaySku VpnGw1 -EnableBgp $false
   ```
7. Atribua um site padrão para o gateway de rede virtual. O **- GatewayDefaultSite** é o parâmetro de cmdlet que permite que a configuração de roteamento forçada funcionem, por isso, tenha cuidado para configurar esta definição corretamente. 

   ```powershell
   $LocalGateway = Get-AzLocalNetworkGateway -Name "DefaultSiteHQ" -ResourceGroupName "ForcedTunneling"
   $VirtualGateway = Get-AzVirtualNetworkGateway -Name "Gateway1" -ResourceGroupName "ForcedTunneling"
   Set-AzVirtualNetworkGatewayDefaultSite -GatewayDefaultSite $LocalGateway -VirtualNetworkGateway $VirtualGateway
   ```
8. Estabelece as ligações de VPN de Site a Site.

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
