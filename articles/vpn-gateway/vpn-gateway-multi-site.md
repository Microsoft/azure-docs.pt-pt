---
title: 'Ligue um VNet a vários sites usando VPN Gateway: Classic'
description: Ligue vários sites locais no local a uma rede virtual clássica usando um VPN Gateway.
services: vpn-gateway
titleSuffix: Azure VPN Gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: article
ms.date: 02/11/2020
ms.author: yushwang
ms.openlocfilehash: a95cd6ea85a16b0e0bf5f67f5dfc20d57f11463b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77198105"
---
# <a name="add-a-site-to-site-connection-to-a-vnet-with-an-existing-vpn-gateway-connection-classic"></a>Adicione uma ligação Site-a-Site a um VNet com uma conexão de gateway VPN existente (clássica)

[!INCLUDE [deployment models](../../includes/vpn-gateway-classic-deployment-model-include.md)]

> [!div class="op_single_selector"]
> * [Portal do Azure](vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md)
> * [PowerShell (clássico)](vpn-gateway-multi-site.md)
>
>

Este artigo percorre-o através da utilização do PowerShell para adicionar ligações Site-to-Site (S2S) a um gateway VPN que tem uma ligação existente. Este tipo de ligação é frequentemente referido como uma configuração "multi-site". Os passos neste artigo aplicam-se às redes virtuais criadas utilizando o modelo clássico de implementação (também conhecido como Gestão de Serviços). Estes passos não se aplicam às configurações de ligação coexistente expressRoute/Site-to-Site.

### <a name="deployment-models-and-methods"></a>Métodos e modelos de implementação

[!INCLUDE [vpn-gateway-classic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

Atualizamos esta tabela à medida que novos artigos e ferramentas adicionais ficam disponíveis para esta configuração. Quando um artigo está disponível, ligamos diretamente a partir desta mesa.

[!INCLUDE [vpn-gateway-table-multi-site](../../includes/vpn-gateway-table-multisite-include.md)]

## <a name="about-connecting"></a>Sobre a ligação

Pode ligar vários sites no local a uma única rede virtual. Isto é especialmente atrativo para a construção de soluções híbridas em nuvem. Criar uma ligação multi-site ao seu portal de rede virtual Azure é semelhante à criação de outras ligações Site-to-Site. Na verdade, você pode usar um gateway Azure VPN existente, desde que o gateway seja dinâmico (baseado em rota).

Se já tem um portal estático ligado à sua rede virtual, pode alterar o tipo de gateway para dinâmico sem precisar de reconstruir a rede virtual de forma a acomodar vários sites. Antes de alterar o tipo de encaminhamento, certifique-se de que o seu gateway VPN no local suporta configurações VPN baseadas em rotas.

![diagrama de vários locais](./media/vpn-gateway-multi-site/multisite.png "multi-site")

## <a name="points-to-consider"></a>Pontos a considerar

**Não poderá utilizar o portal para fazer alterações nesta rede virtual.** Tem de fazer alterações no ficheiro de configuração da rede em vez de utilizar o portal. Se fizer alterações no portal, elas irão substituir as definições de referência multi-site para esta rede virtual.

Deve sentir-se confortável usando o ficheiro de configuração da rede quando tiver concluído o procedimento multi-site. No entanto, se tiver várias pessoas a trabalhar na configuração da sua rede, terá de se certificar de que todos sabem desta limitação. Isto não significa que não possas usar o portal. Pode usá-lo para tudo o resto, exceto fazer alterações de configuração nesta rede virtual em particular.

## <a name="before-you-begin"></a>Antes de começar

Antes de iniciar a configuração, verifique se tem o seguinte:

* Hardware VPN compatível para cada local. Consulte [os dispositivos VPN para conectividade](vpn-gateway-about-vpn-devices.md) de rede virtual para verificar se o dispositivo que pretende utilizar é algo que se sabe ser compatível.
* Um endereço IP iPv4 virado para o exterior para cada dispositivo VPN. O endereço IP não pode ser localizado atrás de um NAT. Isto é obrigatório.
* Alguém que seja competente em configurar o seu hardware VPN. Terá de ter uma forte compreensão de como configurar o seu dispositivo VPN, ou trabalhar com alguém que o faça.
* O endereço IP varia que pretende utilizar para a sua rede virtual (se ainda não criou um).
* O endereço IP varia para cada um dos sites de rede locais a que irá ligar. Terá de se certificar de que o endereço IP varia para cada um dos sites de rede locais a que pretende ligar não se sobrepõe. Caso contrário, o portal ou a API REST rejeitarão a configuração que está a ser carregada.<br>Por exemplo, se tiver dois sites de rede locais que contenham a gama de endereços IP 10.2.3.0/24 e tiver um pacote com um endereço de destino 10.2.3.3, o Azure não saberia para que site pretende enviar o pacote porque as gamas de endereços estão sobrepostas. Para evitar problemas de encaminhamento, o Azure não lhe permite carregar um ficheiro de configuração que tem gamas sobrepostas.

### <a name="working-with-azure-powershell"></a>Trabalhar com a Azure PowerShell

[!INCLUDE [vpn-gateway-classic-powershell](../../includes/vpn-gateway-powershell-classic-locally.md)]

## <a name="1-create-a-site-to-site-vpn"></a>1. Criar uma VPN site-to-site
Se já tem uma VPN site-to-site com um gateway de encaminhamento dinâmico, ótimo! Pode proceder à [Exportação das definições](#export)de configuração da rede virtual . Caso contrário, faça o seguinte:

### <a name="if-you-already-have-a-site-to-site-virtual-network-but-it-has-a-static-policy-based-routing-gateway"></a>Se já tem uma rede virtual site-to-site, mas tem um portal de encaminhamento estático (baseado em políticas):
1. Mude o seu tipo de gateway para o encaminhamento dinâmico. Uma VPN multi-site requer um gateway de encaminhamento dinâmico (também conhecido como rota). Para alterar o seu tipo de gateway, terá primeiro de eliminar o gateway existente e, em seguida, criar um novo.
2. Configure o seu novo portal e crie o seu túnel VPN. Para obter instruções, para obter instruções, consulte [Especifique o tipo SKU e VPN](vpn-gateway-howto-site-to-site-classic-portal.md#sku). Certifique-se de especificar o Tipo de Encaminhamento como 'Dinâmico'.

### <a name="if-you-dont-have-a-site-to-site-virtual-network"></a>Se não tiver uma rede virtual site-to-site:
1. Crie a sua rede virtual Site-to-Site utilizando estas instruções: [Criar uma Rede Virtual com uma ligação VPN site-to-site](vpn-gateway-site-to-site-create.md).  
2. Configure um gateway de encaminhamento dinâmico utilizando estas instruções: [Configure um Gateway VPN](vpn-gateway-configure-vpn-gateway-mp.md). Certifique-se de selecionar **o encaminhamento dinâmico** para o seu tipo de gateway.

## <a name="2-export-the-network-configuration-file"></a><a name="export"></a>2. Exportar o ficheiro de configuração da rede

Abra a consola PowerShell com direitos elevados. Para mudar para a gestão do serviço, utilize este comando:

```powershell
azure config mode asm
```

Ligar à sua conta. Utilize o exemplo seguinte para o ajudar na ligação:

```powershell
Add-AzureAccount
```

Exporte o seu ficheiro de configuração de rede Azure executando o seguinte comando. Pode alterar a localização do ficheiro para exportar para um local diferente, se necessário.

```powershell
Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml
```

## <a name="3-open-the-network-configuration-file"></a>3. Abra o ficheiro de configuração da rede
Abra o ficheiro de configuração da rede que descarregou no último passo. Use qualquer editor xml que quiser. O ficheiro deve ser semelhante ao seguinte:

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

## <a name="4-add-multiple-site-references"></a>4. Adicionar múltiplas referências ao site
Quando adicionar ou remover informações de referência do site, irá efetuar alterações de configuração nas Ligações ToLocalNetwork/LocalNetworkSiteRef. Adicionar um novo local de referência desencadeia o Azure para criar um novo túnel. No exemplo abaixo, a configuração da rede é para uma ligação de um único local. Guarde o ficheiro depois de ter terminado de fazer as alterações.

```xml
  <Gateway>
    <ConnectionsToLocalNetwork>
      <LocalNetworkSiteRef name="Site1"><Connection type="IPsec" /></LocalNetworkSiteRef>
    </ConnectionsToLocalNetwork>
  </Gateway>
```

Para adicionar referências adicionais ao site (criar uma configuração multi-site), basta adicionar linhas adicionais "LocalNetworkSiteRef", como mostra o exemplo abaixo:

```xml
  <Gateway>
    <ConnectionsToLocalNetwork>
      <LocalNetworkSiteRef name="Site1"><Connection type="IPsec" /></LocalNetworkSiteRef>
      <LocalNetworkSiteRef name="Site2"><Connection type="IPsec" /></LocalNetworkSiteRef>
    </ConnectionsToLocalNetwork>
  </Gateway>
```

## <a name="5-import-the-network-configuration-file"></a>5. Importar o ficheiro de configuração da rede
Importar o ficheiro de configuração da rede. Quando importar este ficheiro com as alterações, os novos túneis serão adicionados. Os túneis usarão o portal dinâmico que criou anteriormente. Pode usar a PowerShell para importar o ficheiro.

## <a name="6-download-keys"></a>6. Baixar chaves
Uma vez adicionados os seus novos túneis, utilize o cmdlet PowerShell 'Get-AzureVNetGatewayKey' para obter as teclas pré-partilhadas IPsec/IKE para cada túnel.

Por exemplo:

```powershell
Get-AzureVNetGatewayKey –VNetName "VNet1" –LocalNetworkSiteName "Site1"
Get-AzureVNetGatewayKey –VNetName "VNet1" –LocalNetworkSiteName "Site2"
```

Se preferir, também pode utilizar a *API de Chave Partilhada* Get Virtual Network Gateway para obter as chaves pré-partilhadas.

## <a name="7-verify-your-connections"></a>7. Verifique as suas ligações
Verifique o estado do túnel multi-local. Depois de descarregar as chaves para cada túnel, deverá verificar as ligações. Utilize 'Get-AzureVnetConnection' para obter uma lista de túneis de rede virtuais, como mostra o exemplo abaixo. VNet1 é o nome do VNet.

```powershell
Get-AzureVnetConnection -VNetName VNET1
```

Retorno do exemplo:

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

Para saber mais sobre os Gateways VPN, consulte [sobre os Gateways VPN](vpn-gateway-about-vpngateways.md).
