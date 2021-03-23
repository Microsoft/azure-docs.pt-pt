---
title: Configurar túneis forçados para ligações site-to-site
description: Como redirecionar (forçar) todo o tráfego ligado à Internet de volta para o seu local no local.
services: vpn-gateway
titleSuffix: Azure VPN Gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 03/22/2021
ms.author: cherylmc
ms.openlocfilehash: afd1c1d5312a9fbf39b401b0cbb4b9997f27407a
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/23/2021
ms.locfileid: "104869043"
---
# <a name="configure-forced-tunneling"></a>Configurar túnel forçado

O túnel forçado permite redirecionar ou “forçar” todo o tráfego associado à Internet novamente para a localização no local através de um túnel de VPN site a site para inspeção e auditoria. Este é um requisito crítico de segurança para a maioria das políticas de TI da empresa. Se não configurar túneis forçados, o tráfego ligado à Internet a partir dos seus VMs em Azure atravessa sempre a partir da infraestrutura da rede Azure diretamente para a Internet, sem a opção de lhe permitir inspecionar ou auditar o tráfego. O acesso não autorizado à Internet pode potencialmente levar à divulgação de informações ou a outros tipos de falhas de segurança.

Os túneis forçados podem ser configurados utilizando a Azure PowerShell. Não pode ser configurado usando o portal Azure. Este artigo ajuda-o a configurar túneis forçados para redes virtuais criadas utilizando o modelo de implementação do Gestor de Recursos. Se quiser configurar um túnel forçado para o modelo de implantação clássico, veja [o túnel forçado - clássico](vpn-gateway-about-forced-tunneling.md).

## <a name="about-forced-tunneling"></a>Sobre o túnel forçado

O diagrama seguinte ilustra como funciona o túnel forçado.

:::image type="content" source="./media/vpn-gateway-forced-tunneling-rm/forced-tunnel.png" alt-text="O diagrama mostra um túnel forçado.":::

Neste exemplo, a sub-rede Frontend não é forçada a fazer um túnel. As cargas de trabalho na sub-rede Frontend podem continuar a aceitar e responder diretamente aos pedidos dos clientes da Internet. As sub-redes de nível médio e backend são forçadas a fazer um túnel. Quaisquer ligações de saída destas duas sub-redes para a Internet serão forçadas ou redirecionadas para um local através de um dos túneis VPN do Site-to-site (S2S).

Isto permite-lhe restringir e inspecionar o acesso à Internet a partir das suas máquinas virtuais ou serviços em nuvem em Azure, ao mesmo tempo que continua a ativar a sua arquitetura de serviço multi-nível necessária. Se não houver cargas de trabalho viradas para a Internet nas suas redes virtuais, também pode aplicar túneis forçados a todas as redes virtuais.

## <a name="requirements-and-considerations"></a>Requisitos e considerações

O túnel forçado em Azure é configurado usando rotas personalizadas de rede virtual definidas pelo utilizador. Redirecionar o tráfego para um local no local é expresso como uma Rota Padrão para o gateway Azure VPN. Para obter mais informações sobre o encaminhamento definido pelo utilizador e redes virtuais, consulte [as rotas definidas pelo utilizador personalizado](../virtual-network/virtual-networks-udr-overview.md#user-defined).

* Cada sub-rede de rede virtual tem uma tabela de encaminhamento de sistema incorporada. A tabela de encaminhamento do sistema tem os seguintes três grupos de rotas:
  
  * **Rotas locais de VNet:** Diretamente para o destino VMs na mesma rede virtual.
  * **Rotas no local:** Para o gateway Azure VPN.
  * **Rota predefinida:** Diretamente para a Internet. Os pacotes destinados aos endereços IP privados não abrangidos pelas duas rotas anteriores são retirados.
* Este procedimento utiliza rotas definidas pelo utilizador (UDR) para criar uma tabela de encaminhamento para adicionar uma rota predefinido e, em seguida, associar a tabela de encaminhamento ao(s) de sub-redes VNet para permitir a escavação forçada nessas sub-redes.
* Os túneis forçados devem ser associados a um VNet que tenha uma porta VPN baseada em rotas. É necessário definir um "site predefinido" entre as instalações locais ligadas à rede virtual. Além disso, o dispositivo VPN no local deve ser configurado utilizando 0.0.0.0/0 como selecionadores de trânsito. 
* O túnel forçado ExpressRoute não é configurado através deste mecanismo, mas é ativado através da publicidade de uma rota padrão através das sessões de observação do ExpressRoute BGP. Para mais informações, consulte a [Documentação ExpressRoute.](https://azure.microsoft.com/documentation/services/expressroute/)
* Quando se tem tanto o Gateway VPN como o ExpressRoute Gateway implantados no mesmo VNet, as rotas definidas pelo utilizador (UDR) deixarão de ser necessárias, uma vez que o ExpressRoute Gateway anunciará o "site predefinido" configurado em VNet.

## <a name="configuration-overview"></a>Descrição geral da configuração

O procedimento a seguir ajuda a criar um grupo de recursos e um VNet. Em seguida, criará uma porta de entrada VPN e configurará um túnel forçado. Neste procedimento, a rede virtual 'MultiTier-VNet' tem três sub-redes: 'Frontend', 'Midtier' e 'Backend', com quatro ligações de premissas cruzadas: 'DefaultSiteHQ', e três Ramos.

As etapas de procedimento definem o 'DefaultSiteHQ' como a ligação padrão do local para a escavação forçada e configuram as subesí redes 'Midtier' e 'Backend' para utilizar túneis forçados.

## <a name="before-you-begin"></a><a name="before"></a>Antes de começar

Instale a versão mais recente dos cmdlets do PowerShell do Azure Resource Manager. Veja [Como instalar e configurar o Azure PowerShell](/powershell/azure/) para obter mais informações sobre como instalar os cmdlets PowerShell.

> [!IMPORTANT]
> É necessária a instalação da versão mais recente dos cmdlets PowerShell. Caso contrário, poderá receber erros de validação ao executar alguns dos cmdlets.
>
>

### <a name="to-sign-in"></a>Para iniciar sinscrevi-lo

[!INCLUDE [Sign in](../../includes/vpn-gateway-cloud-shell-ps-login.md)]

## <a name="configure-forced-tunneling"></a>Configurar túnel forçado

> [!NOTE]
> Pode ver avisos dizendo "O tipo de objeto de saída deste cmdlet será modificado numa versão futura". Este comportamento é esperado e pode ignorar com segurança estes avisos.
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
3. Crie as portas de rede locais.

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
5. Associe a tabela de rotas às sub-redes Midtier e Backend.

   ```powershell
   $vnet = Get-AzVirtualNetwork -Name "MultiTier-Vnet" -ResourceGroupName "ForcedTunneling"
   Set-AzVirtualNetworkSubnetConfig -Name "MidTier" -VirtualNetwork $vnet -AddressPrefix "10.1.1.0/24" -RouteTable $rt
   Set-AzVirtualNetworkSubnetConfig -Name "Backend" -VirtualNetwork $vnet -AddressPrefix "10.1.2.0/24" -RouteTable $rt
   Set-AzVirtualNetwork -VirtualNetwork $vnet
   ```
6. Crie o portal de rede virtual. Este passo leva algum tempo para ser concluído, às vezes 45 minutos ou mais, porque você está criando e configurando o portal. Se vir erros do ValidateSet relativos ao valor GatewaySKU, verifique se instalou a [versão mais recente dos cmdlets PowerShell](#before). A versão mais recente dos cmdlets PowerShell contém os novos valores validados para os mais recentes SKUs gateway.

   ```powershell
   $pip = New-AzPublicIpAddress -Name "GatewayIP" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -AllocationMethod Dynamic
   $gwsubnet = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet
   $ipconfig = New-AzVirtualNetworkGatewayIpConfig -Name "gwIpConfig" -SubnetId $gwsubnet.Id -PublicIpAddressId $pip.Id
   New-AzVirtualNetworkGateway -Name "Gateway1" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -IpConfigurations $ipconfig -GatewayType Vpn -VpnType RouteBased -GatewaySku VpnGw1 -EnableBgp $false
   ```
7. Atribua um site predefinido ao portal de rede virtual. O **-GatewayDefaultSite** é o parâmetro cmdlet que permite que a configuração de encaminhamento forçado funcione, por isso tenha cuidado para configurar corretamente esta definição. 

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
