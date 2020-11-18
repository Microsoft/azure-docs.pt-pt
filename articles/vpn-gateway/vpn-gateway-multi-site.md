---
title: 'Ligue um VNet a vários sites usando VPN Gateway: Classic'
description: Ligue vários sites locais no local a uma rede virtual clássica usando um Gateway VPN.
services: vpn-gateway
titleSuffix: Azure VPN Gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/03/2020
ms.author: yushwang
ms.openlocfilehash: 168bb9e06c73ec27ec1304813023889c9549b8e6
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/17/2020
ms.locfileid: "94660700"
---
# <a name="add-a-site-to-site-connection-to-a-vnet-with-an-existing-vpn-gateway-connection-classic"></a>Adicione uma ligação Site-a-Local a um VNet com uma ligação de gateway VPN existente (clássico)

[!INCLUDE [deployment models](../../includes/vpn-gateway-classic-deployment-model-include.md)]

> [!div class="op_single_selector"]
> * [Portal do Azure](vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md)
> * [PowerShell (clássico)](vpn-gateway-multi-site.md)
>
>

Este artigo acompanha-o através da utilização do PowerShell para adicionar ligações Site-to-Site (S2S) a um gateway VPN que tem uma ligação existente. Este tipo de ligação é frequentemente referido como uma configuração "multi-site". Os passos deste artigo aplicam-se às redes virtuais criadas utilizando o modelo clássico de implementação (também conhecido como Gestão de Serviços). Estes passos não se aplicam às configurações de conexão coexistindo ExpressRoute/Site-to-Site.

### <a name="deployment-models-and-methods"></a>Métodos e modelos de implementação

[!INCLUDE [vpn-gateway-classic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

Atualizamos esta tabela à medida que novos artigos e ferramentas adicionais ficam disponíveis para esta configuração. Quando um artigo está disponível, ligamo-lo diretamente a partir desta tabela.

[!INCLUDE [vpn-gateway-table-multi-site](../../includes/vpn-gateway-table-multisite-include.md)]

## <a name="about-connecting"></a>Sobre a ligação

Pode ligar vários sites no local a uma única rede virtual. Isto é especialmente atraente para a construção de soluções híbridas em nuvem. Criar uma ligação multi-site ao seu gateway de rede virtual Azure é semelhante à criação de outras ligações Site-to-Site. Na verdade, você pode usar um gateway Azure VPN existente, desde que o gateway seja dinâmico (baseado em rota).

Se já tiver um gateway estático ligado à sua rede virtual, pode alterar o tipo gateway para dinâmico sem precisar de reconstruir a rede virtual para acomodar vários locais. Antes de alterar o tipo de encaminhamento, certifique-se de que o seu gateway VPN no local suporta configurações VPN baseadas em rotas.

![diagrama de vários locais](./media/vpn-gateway-multi-site/multisite.png "multi-site")

## <a name="points-to-consider"></a>Pontos a considerar

**Não poderá utilizar o portal para fazer alterações nesta rede virtual.** É necessário fazer alterações no ficheiro de configuração da rede em vez de utilizar o portal. Se fizer alterações no portal, substituirão as definições de referência em vários locais para esta rede virtual.

Deve sentir-se confortável usando o ficheiro de configuração da rede quando tiver concluído o procedimento multi-site. No entanto, se tiver várias pessoas a trabalhar na sua configuração de rede, terá de se certificar de que todos sabem desta limitação. Isto não significa que não possas usar o portal. Pode usá-lo para tudo o resto, exceto fazer alterações de configuração nesta rede virtual em particular.

## <a name="before-you-begin"></a>Before you begin

Antes de iniciar a configuração, verifique se tem o seguinte:

* Hardware VPN compatível para cada localização no local. Consulte [os dispositivos VPN para a conectividade da rede virtual](vpn-gateway-about-vpn-devices.md) para verificar se o dispositivo que pretende utilizar é algo que se sabe ser compatível.
* Um endereço IP IPv4 público virado para o exterior para cada dispositivo VPN. O endereço IP não pode ser localizado atrás de um NAT. Isto é um requisito.
* Alguém que seja competente a configurar o seu hardware VPN. Terá de ter uma forte compreensão de como configurar o seu dispositivo VPN, ou trabalhar com alguém que o faça.
* O endereço IP varia que pretende utilizar para a sua rede virtual (se ainda não criou um).
* O endereço IP varia para cada um dos sites de rede locais a que se vai ligar. Tem de se certificar de que o endereço IP varia para cada um dos sites de rede locais que pretende ligar para não se sobrepor. Caso contrário, o portal ou a API REST rejeitarão a configuração que está a ser carregada.<br>Por exemplo, se tiver dois sites de rede locais que contêm o intervalo de endereço IP 10.2.3.0/24 e tiver uma encomenda com um endereço de destino 10.2.3.3,3, Azure não saberia para que site pretende enviar a encomenda porque as gamas de endereços estão sobrepostas. Para evitar problemas de encaminhamento, o Azure não permite o upload de um ficheiro de configuração que tenha intervalos de sobreposição.

### <a name="working-with-azure-powershell"></a>Trabalhar com a Azure PowerShell

[!INCLUDE [vpn-gateway-classic-powershell](../../includes/vpn-gateway-powershell-classic-locally.md)]

## <a name="1-create-a-site-to-site-vpn"></a>1. Criar uma VPN site-to-site
Se já tem uma VPN site-to-site com uma porta de encaminhamento dinâmica, ótimo! Pode proceder à [exportação das definições de configuração da rede virtual](#export). Caso contrário, faça o seguinte:

### <a name="if-you-already-have-a-site-to-site-virtual-network-but-it-has-a-static-policy-based-routing-gateway"></a>Se já tem uma rede virtual site-to-site, mas tem um gateway de encaminhamento estático (baseado em políticas):
1. Mude o tipo de gateway para o encaminhamento dinâmico. Uma VPN multi-site requer um gateway de encaminhamento dinâmico (também conhecido como baseado em rota). Para alterar o tipo de gateway, terá primeiro de apagar o gateway existente e, em seguida, criar um novo.
2. Configure o seu novo portal e crie o seu túnel VPN. Para obter instruções, consulte [o tipo SKU e VPN](vpn-gateway-howto-site-to-site-classic-portal.md#sku). Certifique-se de que especifica o Tipo de Encaminhamento como 'Dinâmico'.

### <a name="if-you-dont-have-a-site-to-site-virtual-network"></a>Se não tiver uma rede virtual site-to-site:
1. Crie a sua rede virtual Site-to-Site utilizando estas instruções: [Criar uma Rede Virtual com uma Ligação VPN Site-to-Site](./vpn-gateway-howto-site-to-site-classic-portal.md).  
2. Configure um gateway de encaminhamento dinâmico utilizando estas instruções: [Configure um Gateway VPN](./vpn-gateway-howto-site-to-site-classic-portal.md). Certifique-se de que seleciona **o encaminhamento dinâmico** para o tipo gateway.

## <a name="2-export-the-network-configuration-file"></a><a name="export"></a>2. Exportar o ficheiro de configuração da rede

Abra a sua consola PowerShell com direitos elevados. Para mudar para a gestão de serviços, utilize este comando:

```powershell
azure config mode asm
```

Ligar à sua conta. Utilize o exemplo seguinte para o ajudar na ligação:

```powershell
Add-AzureAccount
```

Exporte o seu ficheiro de configuração da rede Azure executando o seguinte comando. Pode alterar a localização do ficheiro para exportar para um local diferente, se necessário.

```powershell
Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml
```

## <a name="3-open-the-network-configuration-file"></a>3. Abra o ficheiro de configuração da rede
Abra o ficheiro de configuração de rede que descarregou no último passo. Use qualquer editor de xml que você goste. O ficheiro deve ser semelhante ao seguinte:

```xml
<NetworkConfiguration xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/ServiceHosting/2011/07/NetworkConfiguration">
  <VirtualNetworkConfiguration>
    <LocalNetworkSites>
      <LocalNetworkSite name="Site1">
        <AddressSpace>
          <AddressPrefix>10.0.0.0/16</AddressPrefix>
          <AddressPrefix>10.1.0.0/16</AddressPrefix>
        </AddressSpace>
        <VPNGatewayAddress>131.2.3.4</VPNGatewayAddress>
      </LocalNetworkSite>
      <LocalNetworkSite name="Site2">
        <AddressSpace>
          <AddressPrefix>10.2.0.0/16</AddressPrefix>
          <AddressPrefix>10.3.0.0/16</AddressPrefix>
        </AddressSpace>
        <VPNGatewayAddress>131.4.5.6</VPNGatewayAddress>
      </LocalNetworkSite>
    </LocalNetworkSites>
    <VirtualNetworkSites>
      <VirtualNetworkSite name="VNet1" AffinityGroup="USWest">
        <AddressSpace>
          <AddressPrefix>10.20.0.0/16</AddressPrefix>
          <AddressPrefix>10.21.0.0/16</AddressPrefix>
        </AddressSpace>
        <Subnets>
          <Subnet name="FE">
            <AddressPrefix>10.20.0.0/24</AddressPrefix>
          </Subnet>
          <Subnet name="BE">
            <AddressPrefix>10.20.1.0/24</AddressPrefix>
          </Subnet>
          <Subnet name="GatewaySubnet">
            <AddressPrefix>10.20.2.0/29</AddressPrefix>
          </Subnet>
        </Subnets>
        <Gateway>
          <ConnectionsToLocalNetwork>
            <LocalNetworkSiteRef name="Site1">
              <Connection type="IPsec" />
            </LocalNetworkSiteRef>
          </ConnectionsToLocalNetwork>
        </Gateway>
      </VirtualNetworkSite>
    </VirtualNetworkSites>
  </VirtualNetworkConfiguration>
</NetworkConfiguration>
```

## <a name="4-add-multiple-site-references"></a>4. Adicionar várias referências ao site
Quando adicionar ou remover informações de referência do site, irá escoar alterações de configuração para as LigaçõesToLocalNetwork/LocalNetworkSiteRef. A adição de uma nova referência local do site desencadeia o Azure para criar um novo túnel. No exemplo abaixo, a configuração da rede destina-se a uma ligação de um único local. Guarde o ficheiro assim que terminar de esporá-lo.

```xml
  <Gateway>
    <ConnectionsToLocalNetwork>
      <LocalNetworkSiteRef name="Site1"><Connection type="IPsec" /></LocalNetworkSiteRef>
    </ConnectionsToLocalNetwork>
  </Gateway>
```

Para adicionar referências adicionais do site (criar uma configuração de vários locais), basta adicionar linhas adicionais "LocalNetworkSiteRef", como mostra o exemplo abaixo:

```xml
  <Gateway>
    <ConnectionsToLocalNetwork>
      <LocalNetworkSiteRef name="Site1"><Connection type="IPsec" /></LocalNetworkSiteRef>
      <LocalNetworkSiteRef name="Site2"><Connection type="IPsec" /></LocalNetworkSiteRef>
    </ConnectionsToLocalNetwork>
  </Gateway>
```

## <a name="5-import-the-network-configuration-file"></a>5. Importe o ficheiro de configuração da rede
Importe o ficheiro de configuração da rede. Quando importar este ficheiro com as alterações, os novos túneis serão adicionados. Os túneis usarão o portal dinâmico que criaste anteriormente. Pode usar o PowerShell para importar o ficheiro.

## <a name="6-download-keys"></a>6. Baixar chaves
Uma vez adicionados os seus novos túneis, utilize o cmdlet PowerShell 'Get-AzureVNetGatewayKey' para obter as chaves pré-partilhadas IPsec/IKE para cada túnel.

Por exemplo:

```powershell
Get-AzureVNetGatewayKey –VNetName "VNet1" –LocalNetworkSiteName "Site1"
Get-AzureVNetGatewayKey –VNetName "VNet1" –LocalNetworkSiteName "Site2"
```

Se preferir, também pode utilizar a API da *Chave Partilhada get Virtual Gateway* para obter as teclas pré-partilhadas.

## <a name="7-verify-your-connections"></a>7. Verifique as suas ligações
Verifique o estado do túnel em vários locais. Depois de descarregar as chaves de cada túnel, deverá verificar as ligações. Utilize o 'Get-AzureVnetConnection' para obter uma lista de túneis de rede virtuais, como mostra o exemplo abaixo. VNet1 é o nome do VNet.

```powershell
Get-AzureVnetConnection -VNetName VNET1
```

Devolução de exemplo:

```
    ConnectivityState         : Connected
    EgressBytesTransferred    : 661530
    IngressBytesTransferred   : 519207
    LastConnectionEstablished : 5/2/2014 2:51:40 PM
    LastEventID               : 23401
    LastEventMessage          : The connectivity state for the local network site 'Site1' changed from Not Connected to Connected.
    LastEventTimeStamp        : 5/2/2014 2:51:40 PM
    LocalNetworkSiteName      : Site1
    OperationDescription      : Get-AzureVNetConnection
    OperationId               : 7f68a8e6-51e9-9db4-88c2-16b8067fed7f
    OperationStatus           : Succeeded

    ConnectivityState         : Connected
    EgressBytesTransferred    : 789398
    IngressBytesTransferred   : 143908
    LastConnectionEstablished : 5/2/2014 3:20:40 PM
    LastEventID               : 23401
    LastEventMessage          : The connectivity state for the local network site 'Site2' changed from Not Connected to Connected.
    LastEventTimeStamp        : 5/2/2014 2:51:40 PM
    LocalNetworkSiteName      : Site2
    OperationDescription      : Get-AzureVNetConnection
    OperationId               : 7893b329-51e9-9db4-88c2-16b8067fed7f
    OperationStatus           : Succeeded
```

## <a name="next-steps"></a>Próximos passos

Para saber mais sobre gateways VPN, consulte [Sobre gateways VPN.](vpn-gateway-about-vpngateways.md)