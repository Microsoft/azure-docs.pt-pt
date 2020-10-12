---
title: 'Azure VPN Gateway: Configure túnel forçado - Conexões site-to-site: clássico'
description: Saiba como configurar túneis forçados para redes virtuais criadas usando o modelo clássico de implementação.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 08/01/2017
ms.author: cherylmc
ms.openlocfilehash: 5999ef8431ce1e128c3775450eedf13644505918
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91313540"
---
# <a name="configure-forced-tunneling-using-the-classic-deployment-model"></a>Configurar o túnel forçado utilizando o modelo de implementação clássico

O túnel forçado permite redirecionar ou “forçar” todo o tráfego associado à Internet novamente para a localização no local através de um túnel de VPN site a site para inspeção e auditoria. Este é um requisito crítico de segurança para a maioria das políticas de TI da empresa. Sem fazer túneis forçados, o tráfego ligado à Internet a partir dos seus VMs em Azure irá sempre atravessar a partir da infraestrutura de rede Azure diretamente para a Internet, sem a opção de lhe permitir inspecionar ou auditar o tráfego. O acesso não autorizado à Internet pode potencialmente levar à divulgação de informações ou a outros tipos de falhas de segurança.

[!INCLUDE [vpn-gateway-classic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

Este artigo acompanha-o através da configuração de túneis forçados para redes virtuais criadas usando o modelo de implementação clássico. Os túneis forçados podem ser configurados usando o PowerShell, não através do portal. Se pretender configurar um túnel forçado para o modelo de implementação do Gestor de Recursos, selecione o artigo do Gestor de Recursos da seguinte lista de desistências:

> [!div class="op_single_selector"]
> * [PowerShell - Clássico](vpn-gateway-about-forced-tunneling.md)
> * [PowerShell – Resource Manager](vpn-gateway-forced-tunneling-rm.md)
> 
> 

## <a name="requirements-and-considerations"></a>Requisitos e considerações
O túnel forçado em Azure é configurado através de rotas definidas pelo utilizador da rede virtual (UDR). Redirecionar o tráfego para um local no local é expresso como uma Rota Padrão para o gateway Azure VPN. A secção seguinte lista a limitação atual da tabela de encaminhamento e das rotas para uma Rede Virtual Azure:

* Cada sub-rede de rede virtual tem uma tabela de encaminhamento de sistema incorporada. A tabela de encaminhamento do sistema tem os seguintes três grupos de rotas:

  * **Rotas locais de VNet:** Diretamente para o destino VMs na mesma rede virtual.
  * **Rotas no local:** Para o gateway Azure VPN.
  * **Rota predefinida:** Diretamente para a Internet. Os pacotes destinados aos endereços IP privados não abrangidos pelas duas rotas anteriores serão retirados.
* Com a libertação de rotas definidas pelo utilizador, pode criar uma tabela de encaminhamento para adicionar uma rota predefinitiva e, em seguida, associar a tabela de encaminhamento ao(s) sub-redes VNet(s) para permitir a escavação forçada nessas sub-redes.
* É necessário definir um "site predefinido" entre as instalações locais ligadas à rede virtual.
* Os túneis forçados devem ser associados a um VNet que tenha uma porta VPN de encaminhamento dinâmico (não uma porta estática).
* O túnel forçado ExpressRoute não é configurado através deste mecanismo, mas é ativado através da publicidade de uma rota padrão através das sessões de observação do ExpressRoute BGP. Consulte a [Documentação ExpressRoute](https://azure.microsoft.com/documentation/services/expressroute/) para obter mais informações.

## <a name="configuration-overview"></a>Descrição geral da configuração
No exemplo seguinte, a sub-rede Frontend não é forçada a fazer um túnel. As cargas de trabalho na sub-rede Frontend podem continuar a aceitar e responder diretamente aos pedidos dos clientes da Internet. As sub-redes de nível médio e backend são forçadas a fazer um túnel. Quaisquer ligações de saída destas duas sub-redes para a Internet serão forçadas ou redirecionadas para um local no local através de um dos túneis S2S VPN.

Isto permite-lhe restringir e inspecionar o acesso à Internet a partir das suas máquinas virtuais ou serviços em nuvem em Azure, ao mesmo tempo que continua a ativar a sua arquitetura de serviço multi-nível necessária. Também pode aplicar túneis forçados a todas as redes virtuais se não houver cargas de trabalho viradas para a Internet nas suas redes virtuais.

![Túnel forçado](./media/vpn-gateway-about-forced-tunneling/forced-tunnel.png)

## <a name="before-you-begin"></a>Antes de começar
Antes de iniciar a configuração, verifique se tem os seguintes itens:

* Uma subscrição do Azure. Se ainda não tiver uma subscrição do Azure, pode ativar os [Benefícios de subscritor do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou inscrever-se numa [conta gratuita](https://azure.microsoft.com/pricing/free-trial/).
* Uma rede virtual configurada. 
* [!INCLUDE [vpn-gateway-classic-powershell](../../includes/vpn-gateway-powershell-classic-locally.md)]

### <a name="to-sign-in"></a>Para iniciar sinscrevi-lo

1. Abra a sua consola PowerShell com direitos elevados. Para mudar para a gestão de serviços, utilize este comando:

   ```powershell
   azure config mode asm
   ```
2. Ligar à sua conta. Utilize o exemplo seguinte para o ajudar na ligação:

   ```powershell
   Add-AzureAccount
   ```

## <a name="configure-forced-tunneling"></a>Configurar túnel forçado
O procedimento seguinte irá ajudá-lo a especificar o túnel forçado para uma rede virtual. Os passos de configuração correspondem ao ficheiro de configuração da rede VNet.

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

Neste exemplo, a rede virtual 'MultiTier-VNet' tem três sub-redes: sub-redes 'Frontend', 'Midtier' e 'Backend', com quatro ligações de premissas cruzadas: 'DefaultSiteHQ', e três Ramos. 

Os passos definirão o 'DefaultSiteHQ' como a ligação padrão do local para a escavação forçada, e configurarão as subnetas Midtier e Backend para utilizar túneis forçados.

1. Crie uma mesa de encaminhamento. Utilize o seguinte cmdlet para criar a sua tabela de rotas.

   ```powershell
   New-AzureRouteTable –Name "MyRouteTable" –Label "Routing Table for Forced Tunneling" –Location "North Europe"
   ```

2. Adicione uma rota predefinido à tabela de encaminhamento. 

   O exemplo a seguir adiciona uma rota predefinido à tabela de encaminhamento criada no Passo 1. Note que a única rota suportada é o prefixo de destino de "0.0.0.0/0" para a Loja NextHop "VPNGateway".

   ```powershell
   Get-AzureRouteTable -Name "MyRouteTable" | Set-AzureRoute –RouteTable "MyRouteTable" –RouteName "DefaultRoute" –AddressPrefix "0.0.0.0/0" –NextHopType VPNGateway
   ```

3. Associar a tabela de encaminhamento às sub-redes. 

   Depois de criar uma tabela de encaminhamento e adicionar uma rota, use o seguinte exemplo para adicionar ou associar a tabela de rotas a uma sub-rede VNet. O exemplo adiciona a tabela de rotas "MyRouteTable" às sub-redes Midtier e Backend da VNet MultiTier-VNet.

   ```powershell
   Set-AzureSubnetRouteTable -VirtualNetworkName "MultiTier-VNet" -SubnetName "Midtier" -RouteTableName "MyRouteTable"
   Set-AzureSubnetRouteTable -VirtualNetworkName "MultiTier-VNet" -SubnetName "Backend" -RouteTableName "MyRouteTable"
   ```

4. Atribua um local padrão para fazer um túnel forçado. 

   No passo anterior, os scripts cmdlet da amostra criaram a tabela de encaminhamento e associaram a tabela de rotas a duas das sub-redes VNet. O passo restante é selecionar um site local entre as ligações multi-locais da rede virtual como local ou túnel predefinido.

   ```powershell
   $DefaultSite = @("DefaultSiteHQ")
   Set-AzureVNetGatewayDefaultSite –VNetName "MultiTier-VNet" –DefaultSite "DefaultSiteHQ"
   ```

## <a name="additional-powershell-cmdlets"></a>Cmdlets adicionais powerShell
### <a name="to-delete-a-route-table"></a>Para apagar uma tabela de rotas

```powershell
Remove-AzureRouteTable -Name <routeTableName>
```
  
### <a name="to-list-a-route-table"></a>Para listar uma tabela de rotas

```powershell
Get-AzureRouteTable [-Name <routeTableName> [-DetailLevel <detailLevel>]]
```

### <a name="to-delete-a-route-from-a-route-table"></a>Para apagar uma rota de uma tabela de rotas

```powershell
Remove-AzureRouteTable –Name <routeTableName>
```

### <a name="to-remove-a-route-from-a-subnet"></a>Para remover uma rota de uma sub-rede

```powershell
Remove-AzureSubnetRouteTable –VirtualNetworkName <virtualNetworkName> -SubnetName <subnetName>
```

### <a name="to-list-the-route-table-associated-with-a-subnet"></a>Para listar a tabela de rotas associada a uma sub-rede

```powershell
Get-AzureSubnetRouteTable -VirtualNetworkName <virtualNetworkName> -SubnetName <subnetName>
```

### <a name="to-remove-a-default-site-from-a-vnet-vpn-gateway"></a>Para remover um site predefinido de um gateway VNet VPN

```powershell
Remove-AzureVnetGatewayDefaultSite -VNetName <virtualNetworkName>
```
