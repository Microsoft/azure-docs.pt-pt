---
title: Descarregue scripts de configuração de dispositivo VPN para ligações VPN S2S
description: Este artigo acompanha-o através do download de scripts de configuração de dispositivos VPN para ligações S2S VPN com Gateways VPN Azure utilizando o Gestor de Recursos Azure.
titleSuffix: Azure VPN Gateway
services: vpn-gateway
author: yushwang
manager: rossort
ms.service: vpn-gateway
ms.topic: article
ms.date: 01/09/2019
ms.author: yushwang
ms.openlocfilehash: f905e27f48a0bf9181625bbba07549a13d9420cb
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77162141"
---
# <a name="download-vpn-device-configuration-scripts-for-s2s-vpn-connections"></a>Descarregue scripts de configuração de dispositivo VPN para ligações VPN S2S

Este artigo acompanha-o através do download de scripts de configuração de dispositivos VPN para ligações S2S VPN com Gateways VPN Azure utilizando o Gestor de Recursos Azure. O diagrama seguinte mostra o fluxo de trabalho de alto nível.

![download-script](./media/vpn-gateway-download-vpndevicescript/downloaddevicescript.png)

Os seguintes dispositivos têm scripts disponíveis:

[!INCLUDE [scripts](../../includes/vpn-gateway-device-configuration-scripts.md)]

## <a name="about-vpn-device-configuration-scripts"></a><a name="about"></a>Sobre scripts de configuração de dispositivo VPN

Uma ligação VPN transversal consiste num gateway Azure VPN, num dispositivo VPN no local e num túnel VPN IPsec S2S que liga os dois. O fluxo de trabalho típico inclui os seguintes passos:

1. Criar e configurar um gateway Azure VPN (gateway de rede virtual)
2. Crie e configure um portal de rede local Azure que represente a sua rede no local e o dispositivo VPN
3. Criar e configurar uma ligação VPN Azure entre o gateway Azure VPN e o gateway da rede local
4. Configure o dispositivo VPN no local representado pela porta de entrada da rede local para estabelecer o verdadeiro túnel VpN S2S com o gateway VPN Azure

Pode completar os passos 1 a 3 utilizando o [portal](vpn-gateway-howto-site-to-site-resource-manager-portal.md)Azure, [PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md), ou [CLI](vpn-gateway-howto-site-to-site-resource-manager-cli.md). O último passo passa por configurar os dispositivos VPN no local fora do Azure. Esta funcionalidade permite-lhe descarregar um script de configuração para o seu dispositivo VPN com os valores correspondentes do seu gateway VpN, rede virtual e prefixos de endereçode rede no local, e propriedades de ligação VPN, etc. já preenchidas. Pode utilizar o script como ponto de partida ou aplicar o script diretamente nos seus dispositivos VPN no local através da consola de configuração.

> [!IMPORTANT]
> * A sintaxe para cada script de configuração do dispositivo VPN é diferente, e fortemente dependente dos modelos e versões firmware. Preste especial atenção ao modelo do seu dispositivo e informações de versão contra os modelos disponíveis.
> * Alguns valores de parâmetros devem ser únicos no dispositivo e não podem ser determinados sem aceder ao dispositivo. Os scripts de configuração gerados pelo Azure preenchem estes valores, mas é necessário garantir que os valores fornecidos são válidos no seu dispositivo. Por exemplo:
>    * Números de interface
>    * Números da lista de controlo de acesso
>    * Nomes de políticas ou números, etc.
> * Procure a palavra-chave, "**SUBSTITU**", incorporada no script para encontrar os parâmetros que precisa verificar antes de aplicar o script.
> * Alguns modelos incluem uma secção "**CLEANUP**" que pode aplicar para remover as configurações. As secções de limpeza são comentadas por defeito.

## <a name="download-the-configuration-script-from-azure-portal"></a>Descarregue o script de configuração do portal Azure

Crie um gateway Azure VPN, gateway de rede local e um recurso de ligação que ligue os dois. A página seguinte guia-o através dos passos:

* [Criar uma ligação Site a Site no portal do Azure](vpn-gateway-howto-site-to-site-resource-manager-portal.md)

Assim que o recurso de ligação for criado, siga as instruções abaixo para descarregar os scripts de configuração do dispositivo VPN:

1. A partir de um browser, navegue para o [portal Azure](https://portal.azure.com) e, se necessário, inscreva-se na sua conta Azure
2. Vá ao recurso de ligação que criou. Pode encontrar a lista de todos os recursos de ligação clicando em "Todos os serviços", depois "NETWORKING" e "Connections".

    ![lista de ligação](./media/vpn-gateway-download-vpndevicescript/connectionlist.png)

3. Clique na ligação que pretende configurar.

    ![visão geral da conexão](./media/vpn-gateway-download-vpndevicescript/connectionoverview.png)

4. Clique no link "Configuração de descarregamento" conforme realçado a vermelho na página de visão geral da Ligação; isto abre a página "Configuração de descarregamento".

    ![download-script-1](./media/vpn-gateway-download-vpndevicescript/downloadscript-1.png)

5. Selecione a versão modelo familiar e firmware para o seu dispositivo VPN e, em seguida, clique no botão "Configuração de descarregamento".

    ![download66-script-2](./media/vpn-gateway-download-vpndevicescript/downloadscript-2.PNG)

6. É solicitado que guarde o script descarregado (um ficheiro de texto) do seu navegador.
7. Assim que descarregar o script de configuração, abra-o com um editor de texto e procure a palavra-chave "SUBSTITU" para identificar e examinar os parâmetros que possam ter de ser substituídos.

    ![editar script](./media/vpn-gateway-download-vpndevicescript/editscript.png)

## <a name="download-the-configuration-script-using-azure-powershell"></a>Descarregue o script de configuração usando o Azure PowerShell



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

Depois de ter descarregado e validado o script de configuração, o próximo passo é aplicar o script no seu dispositivo VPN. O procedimento real varia em função das suas faças e modelos do seu dispositivo VPN. Consulte os manuais de funcionamento ou as páginas de instruções dos seus dispositivos VPN.

## <a name="next-steps"></a>Passos seguintes

Continue a configurar a [ligação Site-a-Site](vpn-gateway-howto-site-to-site-resource-manager-portal.md).
