---
title: 'Azure VPN Gateway: Configurar túneis forçados - Ligações Local-A-Local: clássico'
description: Como redirecionar ou "forçar" todo o tráfego vinculado à Internet para sua localização no local.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 08/01/2017
ms.author: cherylmc
ms.openlocfilehash: fe06257127ff352f68fb27d3507cee0229e31498
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/13/2020
ms.locfileid: "77201582"
---
# <a name="configure-forced-tunneling-using-the-classic-deployment-model"></a>Configurar o túnel forçado utilizando o modelo de implementação clássico

O túnel forçado permite redirecionar ou “forçar” todo o tráfego associado à Internet novamente para a localização no local através de um túnel de VPN site a site para inspeção e auditoria. Este é um requisito de críticas de segurança de TI de empresas a maioria das políticas. Sem o túnel forçado, tráfego da Internet das suas VMs no Azure atravessará sempre da infraestrutura de rede do Azure diretamente saída à Internet, sem a opção para que possa inspecionar ou o tráfego de auditoria. Acesso não autorizado da Internet pode potencialmente conduzir a divulgação de informações ou outros tipos de falhas de segurança.

[!INCLUDE [vpn-gateway-classic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

Este artigo orienta-o por meio de configurar imposição de túnel para redes virtuais criadas com o modelo de implementação clássica. O túnel forçado pode ser configurado com o PowerShell, não através do portal. Se pretender configurar túneis forçados para o modelo de implementação do Gestor de Recursos, selecione o artigo do Gestor de Recursos na lista de dropdown seguinte:

> [!div class="op_single_selector"]
> * [PowerShell – Clássica](vpn-gateway-about-forced-tunneling.md)
> * [PowerShell – Resource Manager](vpn-gateway-forced-tunneling-rm.md)
> 
> 

## <a name="requirements-and-considerations"></a>Requisitos e considerações
O túnel forçado no Azure está configurado por meio de rotas definidas pelo utilizador da rede virtual (UDR). Redirecionar o tráfego para um site no local é expressa como uma rota predefinida para o gateway de VPN do Azure. A secção seguinte mostra a limitação atual da tabela de encaminhamento e rotas de uma rede Virtual do Azure:

* Cada sub-rede da rede virtual tem uma tabela de roteamento interno, do sistema. A tabela de encaminhamento do sistema tem os seguintes três grupos de rotas:

  * **Rotas VNet locais:** Diretamente para os VMs de destino na mesma rede virtual.
  * **Rotas no local:** Para o portal Azure VPN.
  * **Rota padrão:** Diretamente para a Internet. Pacotes destinados aos endereços IP privados, não abrangidos pelas rotas de duas anteriores serão ignorados.
* Com o lançamento das rotas definidas pelo utilizador, pode criar uma tabela de encaminhamento para adicionar uma rota predefinida e, em seguida, associar a tabela de encaminhamento para as sub-redes de VNet para ativar o protocolo de túnel forçado nessas sub-redes.
* Tem de definir um "site predefinido" entre os sites locais em vários locais ligado à rede virtual.
* O túnel forçado tem de ser associado a uma VNet com um gateway de VPN encaminhamento dinâmico (não um gateway estático).
* Túnel forçado do ExpressRoute não está configurado por intermédio deste mecanismo, mas em vez disso, está ativado por uma rota predefinida por meio de sessões de peering de BGP de ExpressRoute de publicidade. Consulte a [Documentação ExpressRoute](https://azure.microsoft.com/documentation/services/expressroute/) para mais informações.

## <a name="configuration-overview"></a>Descrição geral da configuração
No exemplo a seguir, o front-end sub-rede não é forçado em túnel. As cargas de trabalho na sub-rede de front-end podem continuar a aceitar e responder aos pedidos dos clientes da Internet diretamente. As sub-redes de escalão médio e de back-end são forçadas encapsulada. Qualquer conexões de saída destas duas sub-redes para a Internet serão forçados ou redirecionados para um site no local através de um dos túneis S2S VPN.

Isto permite-lhe restringir e inspecionar o acesso à Internet das suas máquinas virtuais ou serviços no Azure, de cloud e continuar a ativar a sua arquitetura de várias camadas de serviço necessária. Também pode aplicar o túnel forçado para as redes virtuais todas se não houver nenhum cargas de trabalho de acesso à Internet em suas redes virtuais.

![Túnel Forçado](./media/vpn-gateway-about-forced-tunneling/forced-tunnel.png)

## <a name="before-you-begin"></a>Antes de começar
Antes de iniciar a configuração, verifique se tem os seguintes itens:

* Uma subscrição do Azure. Se ainda não tiver uma subscrição do Azure, pode ativar os [Benefícios de subscritor do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou inscrever-se numa [conta gratuita](https://azure.microsoft.com/pricing/free-trial/).
* Uma rede virtual configurada. 
* [!INCLUDE [vpn-gateway-classic-powershell](../../includes/vpn-gateway-powershell-classic-locally.md)]

### <a name="to-sign-in"></a>Para assinar

1. Abra a consola PowerShell com direitos elevados. Para mudar para a gestão do serviço, utilize este comando:

   ```powershell
   azure config mode asm
   ```
2. Ligar à sua conta. Utilize o exemplo seguinte para o ajudar na ligação:

   ```powershell
   Add-AzureAccount
   ```

## <a name="configure-forced-tunneling"></a>Configurar túnel forçado
O procedimento seguinte irá ajudá-lo a especificar o protocolo de túnel forçado para uma rede virtual. Os passos de configuração correspondem para o ficheiro de configuração de rede de VNet.

```xml
<VirtualNetworkSite name="MultiTier-VNet" Location="North Europe">
     <AddressSpace>
      <AddressPrefix>10.1.0.0/16</AddressPrefix>
        </AddressSpace>
        <Subnets>
          <Subnet name="Frontend">
            <AddressPrefix>10.1.0.0/24</AddressPrefix>
          </Subnet>
          <Subnet name="Midtier">
            <AddressPrefix>10.1.1.0/24</AddressPrefix>
          </Subnet>
          <Subnet name="Backend">
            <AddressPrefix>10.1.2.0/23</AddressPrefix>
          </Subnet>
          <Subnet name="GatewaySubnet">
            <AddressPrefix>10.1.200.0/28</AddressPrefix>
          </Subnet>
        </Subnets>
        <Gateway>
          <ConnectionsToLocalNetwork>
            <LocalNetworkSiteRef name="DefaultSiteHQ">
              <Connection type="IPsec" />
            </LocalNetworkSiteRef>
            <LocalNetworkSiteRef name="Branch1">
              <Connection type="IPsec" />
            </LocalNetworkSiteRef>
            <LocalNetworkSiteRef name="Branch2">
              <Connection type="IPsec" />
            </LocalNetworkSiteRef>
            <LocalNetworkSiteRef name="Branch3">
              <Connection type="IPsec" />
            </LocalNetworkSiteRef>
        </Gateway>
      </VirtualNetworkSite>
    </VirtualNetworkSite>
```

Neste exemplo, a rede virtual de várias camadas-VNet tem três sub-redes: "Front-end", "Midtier" e "Back-end" sub-redes, com ligações de quatro em vários locais: "DefaultSiteHQ" e três ramos. 

Os passos irão definir o DefaultSiteHQ como a ligação de site predefinido para o túnel forçado e configurar o Midtier e sub-redes de back-end para utilizar o túnel forçado.

1. Crie uma tabela de encaminhamento. Utilize o cmdlet seguinte para criar a sua tabela de rotas.

   ```powershell
   New-AzureRouteTable –Name "MyRouteTable" –Label "Routing Table for Forced Tunneling" –Location "North Europe"
   ```

2. Adicione uma rota padrão para a tabela de roteamento. 

   O exemplo seguinte adiciona uma rota predefinida para a tabela de roteamento criada no passo 1. Tenha em atenção que o único caminho suportado é o prefixo de destino de "0.0.0.0/0" para o NextHop "VPNGateway".

   ```powershell
   Get-AzureRouteTable -Name "MyRouteTable" | Set-AzureRoute –RouteTable "MyRouteTable" –RouteName "DefaultRoute" –AddressPrefix "0.0.0.0/0" –NextHopType VPNGateway
   ```

3. Associe a tabela de roteamento às sub-redes. 

   Depois de uma tabela de encaminhamento é criada e adicionada uma rota, utilize o exemplo a seguir para adicionar ou associar a tabela de rotas a uma sub-rede de VNet. O exemplo adiciona a tabela de rotas "MyRouteTable" para as sub-redes de várias camadas VNet VNet Midtier e back-end.

   ```powershell
   Set-AzureSubnetRouteTable -VirtualNetworkName "MultiTier-VNet" -SubnetName "Midtier" -RouteTableName "MyRouteTable"
   Set-AzureSubnetRouteTable -VirtualNetworkName "MultiTier-VNet" -SubnetName "Backend" -RouteTableName "MyRouteTable"
   ```

4. Atribua um site padrão para o túnel forçado. 

   No passo anterior, os scripts de cmdlet de exemplo criou a tabela de encaminhamento e a tabela de rotas em duas sub-redes a VNet associada. A última etapa é selecionar um site local entre as ligações a múltiplos sites de rede virtual como o site predefinido ou o túnel.

   ```powershell
   $DefaultSite = @("DefaultSiteHQ")
   Set-AzureVNetGatewayDefaultSite –VNetName "MultiTier-VNet" –DefaultSite "DefaultSiteHQ"
   ```

## <a name="additional-powershell-cmdlets"></a>Cmdlets do PowerShell adicionais
### <a name="to-delete-a-route-table"></a>Para eliminar uma tabela de rotas

```powershell
Remove-AzureRouteTable -Name <routeTableName>
```
  
### <a name="to-list-a-route-table"></a>Para listar uma tabela de rotas

```powershell
Get-AzureRouteTable [-Name <routeTableName> [-DetailLevel <detailLevel>]]
```

### <a name="to-delete-a-route-from-a-route-table"></a>Para eliminar uma rota de uma tabela de rotas

```powershell
Remove-AzureRouteTable –Name <routeTableName>
```

### <a name="to-remove-a-route-from-a-subnet"></a>Para remover uma rota de sub-rede

```powershell
Remove-AzureSubnetRouteTable –VirtualNetworkName <virtualNetworkName> -SubnetName <subnetName>
```

### <a name="to-list-the-route-table-associated-with-a-subnet"></a>Para listar a tabela de rotas associada com uma sub-rede

```powershell
Get-AzureSubnetRouteTable -VirtualNetworkName <virtualNetworkName> -SubnetName <subnetName>
```

### <a name="to-remove-a-default-site-from-a-vnet-vpn-gateway"></a>Para remover um site padrão de um gateway de VPN de VNet

```powershell
Remove-AzureVnetGatewayDefaultSite -VNetName <virtualNetworkName>
```
