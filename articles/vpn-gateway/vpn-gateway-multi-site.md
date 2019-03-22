---
title: 'Ligar uma rede virtual para vários sites com o Gateway de VPN e o PowerShell: Clássico | Documentos da Microsoft'
description: Ligar vários sites no local a uma rede virtual clássica a utilizar um Gateway de VPN.
services: vpn-gateway
documentationcenter: na
author: yushwang
manager: rossort
editor: ''
tags: azure-service-management
ms.assetid: b043df6e-f1e8-4a4d-8467-c06079e2c093
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/14/2018
ms.author: yushwang
ms.openlocfilehash: 77f8b7094c96e507eef1d360a26240627bc0e350
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57994013"
---
# <a name="add-a-site-to-site-connection-to-a-vnet-with-an-existing-vpn-gateway-connection-classic"></a>Adicionar uma ligação Site a Site a uma VNet com uma ligação de gateway VPN existente (clássica)

[!INCLUDE [deployment models](../../includes/vpn-gateway-classic-deployment-model-include.md)]

> [!div class="op_single_selector"]
> * [Portal do Azure](vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md)
> * [PowerShell (clássico)](vpn-gateway-multi-site.md)
>
>

Este artigo orienta-o através da utilização do PowerShell para adicionar as ligações Site a Site (S2S) a um gateway VPN que tenha uma ligação existente. Este tipo de ligação é frequentemente referido como uma configuração de "multilocal". Os passos neste artigo aplicam-se às redes virtuais criadas com o modelo de implementação clássica (também conhecido como gerenciamento de serviço). Estes passos não são aplicáveis a configurações de ligação coexistentes do ExpressRoute/Site para Site.

### <a name="deployment-models-and-methods"></a>Métodos e modelos de implementação

[!INCLUDE [vpn-gateway-classic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

Atualizamos esta tabela como novos artigos e ferramentas adicionais ficam disponíveis para esta configuração. Quando estiver disponível um artigo, vamos ligar diretamente a ele desta tabela.

[!INCLUDE [vpn-gateway-table-multi-site](../../includes/vpn-gateway-table-multisite-include.md)]

## <a name="about-connecting"></a>Sobre a ligação

Pode ligar vários sites no local para uma única rede virtual. Isso é especialmente atraente para a criação de soluções na cloud híbrida. Criar uma ligação de múltiplos site para o gateway de rede virtual do Azure é similar à criação de outras ligações de Site a Site. Na verdade, pode utilizar um gateway de VPN do Azure existente, desde que o gateway é dinâmico (baseado na rota).

Se já tiver um gateway estático ligado à sua rede virtual, pode alterar o tipo de gateway para dinâmico sem a necessidade de recriar a rede virtual a fim de acomodar vários sites. Antes de alterar o tipo de encaminhamento, certifique-se de que o gateway de VPN no local suporta configurações de VPN baseado na rota.

![diagrama multilocal](./media/vpn-gateway-multi-site/multisite.png "múltiplos sites")

## <a name="points-to-consider"></a>Pontos a considerar

**Não será possível utilizar o portal para fazer alterações a esta rede virtual.** Terá de efetuar alterações ao arquivo de configuração de rede em vez de utilizar o portal. Se fizer alterações no portal, eles irá substituir as definições de referência de múltiplos sites para esta rede virtual.

Deve se sentir confortável com o ficheiro de configuração de rede no momento em que concluiu o procedimento de múltiplos site. No entanto, se tiver várias pessoas trabalhando em sua configuração de rede, terá de certificar-se de que todo mundo sabe sobre esta limitação. Isso não significa que não é possível utilizar o portal de todo. Pode usá-lo para todo o resto, exceto alterações de configuração para esta rede virtual específico.

## <a name="before-you-begin"></a>Antes de começar

Antes de iniciar a configuração, certifique-se de que tem o seguinte:

* Hardware VPN compatível para cada localização no local. Verifique [sobre dispositivos VPN para conectividade de rede Virtual](vpn-gateway-about-vpn-devices.md) para verificar se o dispositivo que pretende utilizar é algo que é conhecido como não compatível.
* Um com acesso exterior IPv4 endereço IP público para cada dispositivo VPN. O endereço IP não pode estar localizado atrás de um NAT. Este é o requisito.
* Terá de instalar a versão mais recente dos cmdlets do Azure PowerShell. Certifique-se de que instalar a versão de gestão de serviço (SM), além da versão do Resource Manager. Ver [como instalar e configurar o Azure PowerShell](/powershell/azure/overview) para obter mais informações.
* Alguém que é o especialista no configurar seu hardware VPN. Terá de ter uma forte compreensão sobre como configurar o dispositivo VPN ou trabalhar com alguém que faz.
* Os intervalos de endereços IP que pretende utilizar para a rede virtual (se ainda não tiver criado uma).
* Os intervalos de endereços IP para cada um dos sites de rede local que vai ser-lhe ligar a. Terá de certificar-se de que o endereço IP intervalos para cada um dos sites de rede local que pretende ligar para não se sobrepõem. Caso contrário, o portal ou a API REST rejeitará a configuração a ser carregada.<br>Por exemplo, se tiver dois sites de rede local que ambos contêm o IP endereço intervalo 10.2.3.0/24 e tiver um pacote com um endereço de destino 10.2.3.3, o Azure não saberia qual site que pretende enviar o pacote para uma vez que os intervalos de endereços de sobreposição. Para evitar problemas de encaminhamento, o Azure não permite-lhe carregar um ficheiro de configuração que tem intervalos sobrepostos.

## <a name="1-create-a-site-to-site-vpn"></a>1. Criar uma Rede de VPNs
Se já tiver uma VPN de Site a Site com um gateway de encaminhamento dinâmico, muito bem! Pode avançar para [exportar as definições de configuração de rede virtual](#export). Caso contrário, faça o seguinte:

### <a name="if-you-already-have-a-site-to-site-virtual-network-but-it-has-a-static-policy-based-routing-gateway"></a>Se já tiver uma rede virtual Site a Site, mas tem um gateway de encaminhamento estático (baseado em políticas):
1. Altere o tipo de gateway de encaminhamento dinâmico. Uma VPN multilocal requer um gateway de encaminhamento dinâmico (também conhecido como baseado na rota). Para alterar o tipo de gateway, precisará primeiro elimine o gateway existente, em seguida, crie um novo.
2. Configurar o novo gateway e criar o túnel VPN. Para obter instruções, para obter instruções, consulte [especifique o tipo SKU e a VPN](vpn-gateway-howto-site-to-site-classic-portal.md#sku). Certifique-se de que especificar o tipo de encaminhamento de mensagens em fila 'Dynamic' como.

### <a name="if-you-dont-have-a-site-to-site-virtual-network"></a>Se não tiver uma rede virtual Site a Site:
1. Crie a rede virtual do Site a Site a utilizar estas instruções: [Criar uma rede Virtual com uma ligação VPN de Site a Site](vpn-gateway-site-to-site-create.md).  
2. Configure um gateway de encaminhamento dinâmico utilizando estas instruções: [Configurar um Gateway de VPN](vpn-gateway-configure-vpn-gateway-mp.md). Verifique se seleciona **encaminhamento dinâmico** para o seu tipo de gateway.

## <a name="export"></a>2. Exportar o ficheiro de configuração de rede
Exporte o ficheiro de configuração de rede do Azure ao executar o comando seguinte. Pode alterar a localização do ficheiro a exportar para uma localização diferente, se necessário.

```powershell
Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml
```

## <a name="3-open-the-network-configuration-file"></a>3. Abra o ficheiro de configuração de rede
Abra o ficheiro de configuração de rede que transferiu no último passo. Utilize o editor de xml que desejar. O ficheiro deve ter um aspeto semelhante ao seguinte:

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

## <a name="4-add-multiple-site-references"></a>4. Adicionar várias referências de site
Quando adicionar ou remover informações de referência do site, terá de efetuar alterações de configuração para ConnectionsToLocalNetwork/LocalNetworkSiteRef. Adicionar um novo acionadores de referência do local site do Azure para criar um túnel de novo. No exemplo abaixo, a configuração de rede é de uma ligação de site único. Guarde o ficheiro depois de terminar de realizar as suas alterações.

```xml
  <Gateway>
    <ConnectionsToLocalNetwork>
      <LocalNetworkSiteRef name="Site1"><Connection type="IPsec" /></LocalNetworkSiteRef>
    </ConnectionsToLocalNetwork>
  </Gateway>
```

Para adicionar referências de site adicionais (criar uma configuração de múltiplos site), basta adicionar linhas de "LocalNetworkSiteRef" adicionais, conforme mostrado no exemplo abaixo:

```xml
  <Gateway>
    <ConnectionsToLocalNetwork>
      <LocalNetworkSiteRef name="Site1"><Connection type="IPsec" /></LocalNetworkSiteRef>
      <LocalNetworkSiteRef name="Site2"><Connection type="IPsec" /></LocalNetworkSiteRef>
    </ConnectionsToLocalNetwork>
  </Gateway>
```

## <a name="5-import-the-network-configuration-file"></a>5. Importar o ficheiro de configuração de rede
Importe o ficheiro de configuração de rede. Quando importar este ficheiro com as alterações, os túneis novo serão adicionados. Os túneis irão utilizar o gateway dinâmico que criou anteriormente. Pode utilizar o PowerShell para importar o ficheiro.

## <a name="6-download-keys"></a>6. Transferir as chaves
Assim que tiverem sido adicionados a seu novo túneis, utilize o cmdlet do PowerShell "Get-AzureVNetGatewayKey" para obter as chaves pré-partilhadas de IPsec/IKE para cada túnel.

Por exemplo:

```powershell
Get-AzureVNetGatewayKey –VNetName "VNet1" –LocalNetworkSiteName "Site1"
Get-AzureVNetGatewayKey –VNetName "VNet1" –LocalNetworkSiteName "Site2"
```

Se preferir, também pode utilizar o *obter Virtual rede Gateway chave partilhada* API REST para obter as chaves pré-partilhadas.

## <a name="7-verify-your-connections"></a>7. Verifique as suas ligações
Verificar o estado de túnel de múltiplos sites. Depois de transferir as chaves para cada túnel, desejará verificar ligações. Utilize "Get-AzureVnetConnection" para obter uma lista de túneis de rede virtual, conforme mostrado no exemplo abaixo. VNet1 é o nome da VNet.

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

## <a name="next-steps"></a>Passos Seguintes

Para saber mais sobre os Gateways de VPN, veja [acerca dos VPN Gateways](vpn-gateway-about-vpngateways.md).
