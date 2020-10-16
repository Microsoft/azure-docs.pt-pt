---
title: Descarregue scripts de configuração de dispositivos VPN para ligações VPN S2S
description: Este artigo acompanha-o através do download de scripts de configuração de dispositivos VPN para ligações S2S VPN com Gateways Azure VPN usando Azure Resource Manager.
titleSuffix: Azure VPN Gateway
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/02/2020
ms.author: yushwang
ms.openlocfilehash: 70d9a8069979a299ad3031de37c525438ab0159d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89394606"
---
# <a name="download-vpn-device-configuration-scripts-for-s2s-vpn-connections"></a>Descarregue scripts de configuração de dispositivos VPN para ligações VPN S2S

Este artigo acompanha-o através do download de scripts de configuração de dispositivos VPN para ligações S2S VPN com Gateways Azure VPN usando Azure Resource Manager. O diagrama seguinte mostra o fluxo de trabalho de alto nível.

![download-script](./media/vpn-gateway-download-vpndevicescript/downloaddevicescript.png)

Os seguintes dispositivos têm scripts disponíveis:

[!INCLUDE [scripts](../../includes/vpn-gateway-device-configuration-scripts.md)]

## <a name="about-vpn-device-configuration-scripts"></a><a name="about"></a>Sobre scripts de configuração de dispositivos VPN

Uma ligação VPN transversal é constituída por um gateway VPN Azure, um dispositivo VPN no local e um túnel VPN IPsec S2S que liga os dois. O fluxo de trabalho típico inclui os seguintes passos:

1. Criar e configurar um gateway Azure VPN (gateway de rede virtual)
2. Crie e configura um gateway de rede local Azure que represente a sua rede no local e dispositivo VPN
3. Criar e configurar uma ligação Azure VPN entre o gateway Azure VPN e o gateway de rede local
4. Configure o dispositivo VPN no local representado pela porta de entrada da rede local para estabelecer o túnel VPN S2S real com o gateway Azure VPN

Pode completar os passos 1 a 3 utilizando o [portal](vpn-gateway-howto-site-to-site-resource-manager-portal.md)Azure , [PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md)ou [CLI](vpn-gateway-howto-site-to-site-resource-manager-cli.md). O último passo consiste em configurar os dispositivos VPN no local fora de Azure. Esta funcionalidade permite-lhe descarregar um script de configuração para o seu dispositivo VPN com os valores correspondentes do seu gateway Azure VPN, rede virtual e prefixos de endereço de rede no local, e propriedades de conexão VPN, etc. já preenchidos. Pode utilizar o script como ponto de partida ou aplicar o script diretamente nos dispositivos VPN no local através da consola de configuração.

> [!IMPORTANT]
> * A sintaxe para cada script de configuração de dispositivo VPN é diferente, e depende fortemente dos modelos e versões de firmware. Preste especial atenção ao modelo do dispositivo e às informações da versão contra os modelos disponíveis.
> * Alguns valores de parâmetros devem ser únicos no dispositivo e não podem ser determinados sem aceder ao dispositivo. Os scripts de configuração gerados pelo Azure preenchem estes valores, mas é necessário garantir que os valores fornecidos são válidos no seu dispositivo. Por exemplo:
>    * Números de interface
>    * Números da lista de controlo de acesso
>    * Nomes de políticas ou números, etc.
> * Procure a palavra-chave, "**SUBSTITU**", incorporada no script para encontrar os parâmetros que precisa de verificar antes de aplicar o script.
> * Alguns modelos incluem uma secção "**CLEANUP**" que pode aplicar para remover as configurações. As secções de limpeza são comentadas por defeito.

## <a name="download-the-configuration-script-from-azure-portal"></a>Descarregue o script de configuração a partir do portal Azure

Crie um gateway Azure VPN, porta de rede local e um recurso de ligação que ligue os dois. A página que se segue guia-o através dos passos:

* [Criar uma ligação Site a Site no portal do Azure](vpn-gateway-howto-site-to-site-resource-manager-portal.md)

Assim que o recurso de ligação for criado, siga as instruções abaixo para descarregar os scripts de configuração do dispositivo VPN:

1. A partir de um navegador, navegue para o [portal Azure](https://portal.azure.com) e, se necessário, inscreva-se na sua conta Azure
2. Aceda ao recurso de ligação que criou. Pode encontrar a lista de todos os recursos de ligação clicando em "Todos os serviços", em seguida " NETWORKING" e "Conexões".

    ![lista de ligações](./media/vpn-gateway-download-vpndevicescript/connectionlist.png)

3. Clique na ligação que pretende configurar.

    ![visão geral de conexão](./media/vpn-gateway-download-vpndevicescript/connectionoverview.png)

4. Clique no link "Configuração de descarregamento" como realçado a vermelho na página 'Visão geral' 'Ligação'; isto abre a página "Configuração de descarregamento".

    ![download-script-1](./media/vpn-gateway-download-vpndevicescript/downloadscript-1.png)

5. Selecione a versão modelo de família e firmware para o seu dispositivo VPN e, em seguida, clique no botão "Descarregar configuração".

    ![download6-script-2](./media/vpn-gateway-download-vpndevicescript/downloadscript-2.PNG)

6. É-lhe pedido que guarde o script descarregado (um ficheiro de texto) do seu navegador.
7. Uma vez descarregado o script de configuração, abra-o com um editor de texto e procure a palavra-chave "REPLACE" para identificar e examinar os parâmetros que podem ter de ser substituídos.

    ![editar script](./media/vpn-gateway-download-vpndevicescript/editscript.png)

## <a name="download-the-configuration-script-using-azure-powershell"></a>Descarregue o script de configuração usando Azure PowerShell



Também pode descarregar o script de configuração utilizando o Azure PowerShell, como mostra o seguinte exemplo:

```azurepowershell-interactive
$RG          = "TestRG1"
$GWName      = "VNet1GW"
$Connection  = "VNet1toSite1"

# List the available VPN device models and versions
Get-AzVirtualNetworkGatewaySupportedVpnDevice -Name $GWName -ResourceGroupName $RG

# Download the configuration script for the connection
Get-AzVirtualNetworkGatewayConnectionVpnDeviceConfigScript -Name $Connection -ResourceGroupName $RG -DeviceVendor Juniper -DeviceFamily Juniper_SRX_GA -FirmwareVersion Juniper_SRX_12.x_GA
```

## <a name="apply-the-configuration-script-to-your-vpn-device"></a>Aplique o script de configuração no seu dispositivo VPN

Depois de ter descarregado e validado o script de configuração, o próximo passo é aplicar o script no seu dispositivo VPN. O procedimento real varia em com base nas fabricações e modelos do seu dispositivo VPN. Consulte os manuais de funcionamento ou as páginas de instruções para os seus dispositivos VPN.

## <a name="next-steps"></a>Passos seguintes

Continue a configurar a sua [ligação Site-to-Site](vpn-gateway-howto-site-to-site-resource-manager-portal.md).
