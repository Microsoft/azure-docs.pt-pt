---
title: 'Conectar uma VNet a vários sites usando o gateway de VPN: clássico'
description: Conecte vários sites locais a uma rede virtual clássica usando um gateway de VPN.
services: vpn-gateway
titleSuffix: Azure VPN Gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: article
ms.date: 01/10/2020
ms.author: yushwang
ms.openlocfilehash: 5bedf5bd6d061d74201dbac3f1f99ed0d4c381aa
ms.sourcegitcommit: 3eb0cc8091c8e4ae4d537051c3265b92427537fe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/11/2020
ms.locfileid: "75902444"
---
# <a name="add-a-site-to-site-connection-to-a-vnet-with-an-existing-vpn-gateway-connection-classic"></a>Adicionar uma conexão site a site a uma VNet com uma conexão de gateway de VPN existente (clássica)

[!INCLUDE [deployment models](../../includes/vpn-gateway-classic-deployment-model-include.md)]

> [!div class="op_single_selector"]
> * [Portal do Azure](vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md)
> * [PowerShell (clássico)](vpn-gateway-multi-site.md)
>
>

Este artigo orienta você pelo uso do PowerShell para adicionar conexões site a site (S2S) a um gateway de VPN que tem uma conexão existente. Esse tipo de conexão é geralmente chamado de configuração de "multissite". As etapas neste artigo se aplicam a redes virtuais criadas usando o modelo de implantação clássico (também conhecido como gerenciamento de serviços). Essas etapas não se aplicam a configurações de conexão coexistentes do ExpressRoute/site a site.

### <a name="deployment-models-and-methods"></a>Métodos e modelos de implementação

[!INCLUDE [vpn-gateway-classic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

Atualizamos esta tabela conforme novos artigos e ferramentas adicionais ficam disponíveis para essa configuração. Quando um artigo está disponível, nós vinculamos diretamente a ele a partir desta tabela.

[!INCLUDE [vpn-gateway-table-multi-site](../../includes/vpn-gateway-table-multisite-include.md)]

## <a name="about-connecting"></a>Sobre a conexão

Você pode conectar vários sites locais a uma única rede virtual. Isso é especialmente atraente para a criação de soluções de nuvem híbrida. A criação de uma conexão multissite para o gateway de rede virtual do Azure é semelhante à criação de outras conexões site a site. Na verdade, você pode usar um gateway de VPN do Azure existente, desde que o gateway seja dinâmico (baseado em rota).

Se você já tiver um gateway estático conectado à sua rede virtual, poderá alterar o tipo de gateway para dinâmico sem a necessidade de recriar a rede virtual para acomodar vários sites. Antes de alterar o tipo de roteamento, verifique se o gateway de VPN local oferece suporte a configurações de VPN baseadas em rota.

![diagrama de vários sites](./media/vpn-gateway-multi-site/multisite.png "multissite")

## <a name="points-to-consider"></a>Pontos a considerar

**Você não poderá usar o portal para fazer alterações nessa rede virtual.** Você precisa fazer alterações no arquivo de configuração de rede em vez de usar o Portal. Se você fizer alterações no portal, elas substituirão as configurações de referência de vários sites para essa rede virtual.

Você deve se sentir confortável ao usar o arquivo de configuração de rede no momento em que concluiu o procedimento de vários sites. No entanto, se você tiver várias pessoas trabalhando em sua configuração de rede, precisará garantir que todos saibam dessa limitação. Isso não significa que você não pode usar o Portal. Você pode usá-lo para todo o resto, exceto fazer alterações de configuração nessa rede virtual específica.

## <a name="before-you-begin"></a>Antes de começar

Antes de começar a configuração, verifique se você tem o seguinte:

* Hardware de VPN compatível para cada local. Verifique se há [dispositivos VPN para conectividade de rede virtual](vpn-gateway-about-vpn-devices.md) para verificar se o dispositivo que você deseja usar é algo que é conhecido como compatível.
* Um endereço IP IPv4 público voltado externamente para cada dispositivo VPN. O endereço IP não pode ser localizado atrás de um NAT. Isso é requisito.
* Terá de instalar a versão mais recente dos cmdlets do Azure PowerShell. Certifique-se de instalar a versão do gerenciamento de serviços (SM) Além da versão do Resource Manager. Consulte [como instalar e configurar Azure PowerShell](/powershell/azure/overview) para obter mais informações.
* Alguém que é proficiente na configuração de seu hardware de VPN. Você precisará ter um forte entendimento de como configurar seu dispositivo VPN ou trabalhar com alguém que o faz.
* Os intervalos de endereços IP que você deseja usar para sua rede virtual (se você ainda não tiver criado um).
* Os intervalos de endereços IP para cada um dos sites de rede local aos quais você estará se conectando. Você precisará certificar-se de que os intervalos de endereços IP para cada um dos sites de rede local aos quais você deseja se conectar não se sobreponham. Caso contrário, o portal ou a API REST rejeitará a configuração que está sendo carregada.<br>Por exemplo, se você tiver dois sites de rede local que contenham o intervalo de endereços IP 10.2.3.0/24 e tiver um pacote com um endereço de destino 10.2.3.3, o Azure não saberá a qual site você deseja enviar o pacote, pois os intervalos de endereços estão sobrepostos. Para evitar problemas de roteamento, o Azure não permite que você carregue um arquivo de configuração que tenha intervalos sobrepostos.

## <a name="1-create-a-site-to-site-vpn"></a>1. criar uma VPN site a site
Se você já tiver uma VPN site a site com um gateway de roteamento dinâmico, ótimo! Você pode continuar a [exportar as definições de configuração de rede virtual](#export). Caso contrário, faça o seguinte:

### <a name="if-you-already-have-a-site-to-site-virtual-network-but-it-has-a-static-policy-based-routing-gateway"></a>Se você já tiver uma rede virtual site a site, mas ela tiver um gateway de roteamento estático (baseado em políticas):
1. Altere o tipo de gateway para roteamento dinâmico. Uma VPN de vários sites requer um gateway de roteamento dinâmico (também conhecido como baseado em rota). Para alterar o tipo de gateway, primeiro você precisará excluir o gateway existente e, em seguida, criar um novo.
2. Configure seu novo gateway e crie seu túnel de VPN. Para obter instruções, consulte [especificar o tipo de SKU e VPN](vpn-gateway-howto-site-to-site-classic-portal.md#sku). Certifique-se de especificar o tipo de roteamento como ' dinâmico '.

### <a name="if-you-dont-have-a-site-to-site-virtual-network"></a>Se você não tiver uma rede virtual site a site:
1. Crie sua rede virtual site a site usando estas instruções: [criar uma rede virtual com uma conexão VPN site a site](vpn-gateway-site-to-site-create.md).  
2. Configure um gateway de roteamento dinâmico usando estas instruções: [configurar um gateway de VPN](vpn-gateway-configure-vpn-gateway-mp.md). Certifique-se de selecionar **Roteamento dinâmico** para o tipo de gateway.

## <a name="export"></a>2. exportar o arquivo de configuração de rede
Exporte o arquivo de configuração de rede do Azure executando o comando a seguir. Você pode alterar o local do arquivo para exportar para um local diferente, se necessário.

```powershell
Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml
```

## <a name="3-open-the-network-configuration-file"></a>3. abrir o arquivo de configuração de rede
Abra o arquivo de configuração de rede que você baixou na última etapa. Use qualquer editor de XML que desejar. O arquivo deve ser semelhante ao seguinte:

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

## <a name="4-add-multiple-site-references"></a>4. adicionar referências a vários sites
Ao adicionar ou remover informações de referência do site, você fará alterações de configuração no ConnectionsToLocalNetwork/LocalNetworkSiteRef. A adição de uma nova referência de site local dispara o Azure para criar um novo túnel. No exemplo a seguir, a configuração de rede é para uma conexão de site único. Salve o arquivo depois de terminar de fazer suas alterações.

```xml
  <Gateway>
    <ConnectionsToLocalNetwork>
      <LocalNetworkSiteRef name="Site1"><Connection type="IPsec" /></LocalNetworkSiteRef>
    </ConnectionsToLocalNetwork>
  </Gateway>
```

Para adicionar referências de site adicionais (criar uma configuração multissite), basta adicionar linhas "LocalNetworkSiteRef" adicionais, conforme mostrado no exemplo abaixo:

```xml
  <Gateway>
    <ConnectionsToLocalNetwork>
      <LocalNetworkSiteRef name="Site1"><Connection type="IPsec" /></LocalNetworkSiteRef>
      <LocalNetworkSiteRef name="Site2"><Connection type="IPsec" /></LocalNetworkSiteRef>
    </ConnectionsToLocalNetwork>
  </Gateway>
```

## <a name="5-import-the-network-configuration-file"></a>5. importar o arquivo de configuração de rede
Importe o arquivo de configuração de rede. Quando você importar esse arquivo com as alterações, os novos túneis serão adicionados. Os túneis usarão o gateway dinâmico que você criou anteriormente. Você pode usar o PowerShell para importar o arquivo.

## <a name="6-download-keys"></a>6. chaves de download
Depois que os novos túneis forem adicionados, use o cmdlet ' Get-AzureVNetGatewayKey ' do PowerShell para obter as chaves pré-compartilhadas de IPsec/IKE para cada túnel.

Por exemplo:

```powershell
Get-AzureVNetGatewayKey –VNetName "VNet1" –LocalNetworkSiteName "Site1"
Get-AzureVNetGatewayKey –VNetName "VNet1" –LocalNetworkSiteName "Site2"
```

Se preferir, você também pode usar a API REST *obter chave compartilhada do gateway de rede virtual* para obter as chaves pré-compartilhadas.

## <a name="7-verify-your-connections"></a>7. Verifique suas conexões
Verifique o status do túnel de vários sites. Depois de baixar as chaves para cada túnel, você desejará verificar as conexões. Use ' Get-AzureVnetConnection ' para obter uma lista de túneis de rede virtual, conforme mostrado no exemplo abaixo. VNet1 é o nome da VNet.

```powershell
Get-AzureVnetConnection -VNetName VNET1
```

Exemplo de retorno:

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

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre gateways de VPN, consulte [sobre gateways de VPN](vpn-gateway-about-vpngateways.md).
