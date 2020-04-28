---
title: 'Azure VPN Gateway: Configurar túneis forçados - Ligações Local-A-Local: clássico'
description: Como redirecionar ou "forçar" todo o tráfego ligado à Internet de volta ao seu local no local.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 08/01/2017
ms.author: cherylmc
ms.openlocfilehash: fe06257127ff352f68fb27d3507cee0229e31498
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77201582"
---
# <a name="configure-forced-tunneling-using-the-classic-deployment-model"></a>Configurar o túnel forçado utilizando o modelo de implementação clássico

O túnel forçado permite redirecionar ou “forçar” todo o tráfego associado à Internet novamente para a localização no local através de um túnel de VPN site a site para inspeção e auditoria. Este é um requisito crítico de segurança para a maioria das políticas de TI da empresa. Sem túneis forçados, o tráfego ligado à Internet dos seus VMs em Azure irá sempre atravessar da infraestrutura da rede Azure diretamente para a Internet, sem a opção de permitir inspecionar ou auditar o tráfego. O acesso não autorizado à Internet pode potencialmente levar à divulgação de informações ou a outros tipos de falhas de segurança.

[!INCLUDE [vpn-gateway-classic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

Este artigo acompanha-o através da configuração de túneis forçados para redes virtuais criadas usando o modelo de implantação clássico. O túnel forçado pode ser configurado utilizando o PowerShell, não através do portal. Se pretender configurar túneis forçados para o modelo de implementação do Gestor de Recursos, selecione o artigo do Gestor de Recursos na lista de dropdown seguinte:

> [!div class="op_single_selector"]
> * [PowerShell - Clássico](vpn-gateway-about-forced-tunneling.md)
> * [PowerShell – Resource Manager](vpn-gateway-forced-tunneling-rm.md)
> 
> 

## <a name="requirements-and-considerations"></a>Requisitos e considerações
A escavação forçada em Azure é configurada através de rotas definidas pelo utilizador da rede virtual (UDR). Redirecionar o tráfego para um local no local é expresso como uma Rota Padrão para o gateway VPN Azure. A secção seguinte enumera a limitação atual da tabela de encaminhamento e rotas para uma Rede Virtual Azure:

* Cada subnet de rede virtual tem uma mesa de encaminhamento do sistema incorporada. A tabela de encaminhamento do sistema tem os seguintes três grupos de rotas:

  * **Rotas VNet locais:** Diretamente para os VMs de destino na mesma rede virtual.
  * **Rotas no local:** Para o portal Azure VPN.
  * **Rota padrão:** Diretamente para a Internet. Os pacotes destinados aos endereços IP privados não abrangidos pelas duas rotas anteriores serão retirados.
* Com o lançamento de rotas definidas pelo utilizador, pode criar uma tabela de encaminhamento para adicionar uma rota predefinida e, em seguida, associar a tabela de encaminhamento à sua subnet VNet para permitir a escavação forçada nessas subredes.
* É necessário definir um "site predefinido" entre os sites locais inter-instalações ligados à rede virtual.
* A escavação forçada deve ser associada a uma VNet que tenha um gateway VPN de encaminhamento dinâmico (não uma porta de entrada estática).
* O túnel forçado ExpressRoute não está configurado através deste mecanismo, mas sim, é ativado pela publicidade de uma rota padrão através das sessões de peering Do ExpressRoute BGP. Consulte a [Documentação ExpressRoute](https://azure.microsoft.com/documentation/services/expressroute/) para mais informações.

## <a name="configuration-overview"></a>Descrição geral da configuração
No exemplo seguinte, a subnet Frontend não é forçada a fazer túneis. As cargas de trabalho na subnet Frontend podem continuar a aceitar e responder diretamente aos pedidos dos clientes da Internet. As subredes de nível médio e backend são forçadas a fazer túneis. Quaisquer ligações de saída destas duas subredes para a Internet serão forçadas ou redirecionadas de volta para um local no local através de um dos túneis VpN S2S.

Isto permite-lhe restringir e inspecionar o acesso à Internet a partir das suas máquinas virtuais ou serviços de nuvem em Azure, ao mesmo tempo que continua a ativar a sua arquitetura de serviço de vários níveis necessária. Também pode aplicar túneis forçados a todas as redes virtuais se não houver cargas de trabalho viradas para a Internet nas suas redes virtuais.

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
O procedimento seguinte irá ajudá-lo a especificar a escavação forçada para uma rede virtual. Os passos de configuração correspondem ao ficheiro de configuração da rede VNet.

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

Neste exemplo, a rede virtual 'MultiTier-VNet' tem três subredes: 'Frontend', 'Midtier' e 'Backend', com quatro ligações de premissas cruzadas: 'DefaultSiteHQ', e três Ramos. 

As etapas definirão o 'PadrãoSiteHQ' como a ligação padrão do local para túneis forçados, e configurarão as subredes Midtier e Backend para utilizar túneis forçados.

1. Crie uma mesa de encaminhamento. Utilize o seguinte cmdlet para criar a sua tabela de rotas.

   ```powershell
   New-AzureRouteTable –Name "MyRouteTable" –Label "Routing Table for Forced Tunneling" –Location "North Europe"
   ```

2. Adicione uma rota padrão para a mesa de encaminhamento. 

   O exemplo seguinte adiciona uma rota padrão para a tabela de encaminhamento criada no Passo 1. Note que a única rota suportada é o prefixo de destino de "0.0.0.0/0" para o "VPNGateway" NextHop.

   ```powershell
   Get-AzureRouteTable -Name "MyRouteTable" | Set-AzureRoute –RouteTable "MyRouteTable" –RouteName "DefaultRoute" –AddressPrefix "0.0.0.0/0" –NextHopType VPNGateway
   ```

3. Associe a mesa de encaminhamento às subredes. 

   Depois de criada uma tabela de encaminhamento e de uma rota adicionada, utilize o seguinte exemplo para adicionar ou associar a tabela de rotas a uma subnet VNet. O exemplo adiciona a tabela de rotas "MyRouteTable" às subredes Midtier e Backend da VNet MultiTier-VNet.

   ```powershell
   Set-AzureSubnetRouteTable -VirtualNetworkName "MultiTier-VNet" -SubnetName "Midtier" -RouteTableName "MyRouteTable"
   Set-AzureSubnetRouteTable -VirtualNetworkName "MultiTier-VNet" -SubnetName "Backend" -RouteTableName "MyRouteTable"
   ```

4. Atribuir um local padrão para túneis forçados. 

   Na etapa anterior, as scripts cmdlet da amostra criaram a tabela de encaminhamento e associaram a tabela de rotas a duas das subredes VNet. O passo restante é selecionar um local local entre as ligações multi-locais da rede virtual como o local padrão ou túnel.

   ```powershell
   $DefaultSite = @("DefaultSiteHQ")
   Set-AzureVNetGatewayDefaultSite –VNetName "MultiTier-VNet" –DefaultSite "DefaultSiteHQ"
   ```

## <a name="additional-powershell-cmdlets"></a>Cmdlets powerShell adicionais
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

### <a name="to-remove-a-route-from-a-subnet"></a>Para remover uma rota de uma subneta

```powershell
Remove-AzureSubnetRouteTable –VirtualNetworkName <virtualNetworkName> -SubnetName <subnetName>
```

### <a name="to-list-the-route-table-associated-with-a-subnet"></a>Para listar a tabela de rotas associada a uma subneta

```powershell
Get-AzureSubnetRouteTable -VirtualNetworkName <virtualNetworkName> -SubnetName <subnetName>
```

### <a name="to-remove-a-default-site-from-a-vnet-vpn-gateway"></a>Para remover um site predefinido de um gateway VNet VPN

```powershell
Remove-AzureVnetGatewayDefaultSite -VNetName <virtualNetworkName>
```
