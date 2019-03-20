---
title: 'Transfira scripts de configuração do dispositivo VPN para ligações S2S VPN: O Azure Resource Manager | Documentos da Microsoft'
description: Este artigo explica como transferir os scripts de configuração do dispositivo VPN para ligações S2S VPN com Gateways de VPN do Azure com o Azure Resource Manager.
services: vpn-gateway
author: yushwang
manager: rossort
ms.service: vpn-gateway
ms.topic: article
ms.date: 01/09/2019
ms.author: yushwang
ms.openlocfilehash: f7ee53c10c6597dbf98f8f85fc31fe789137471e
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57996321"
---
# <a name="download-vpn-device-configuration-scripts-for-s2s-vpn-connections"></a>Transfere os scripts de configuração do dispositivo VPN para ligações S2S VPN

Este artigo explica como transferir os scripts de configuração do dispositivo VPN para ligações S2S VPN com Gateways de VPN do Azure com o Azure Resource Manager. O diagrama seguinte mostra o fluxo de trabalho de alto nível.

![download-script](./media/vpn-gateway-download-vpndevicescript/downloaddevicescript.png)

Os seguintes dispositivos tem scripts disponíveis:

[!INCLUDE [scripts](../../includes/vpn-gateway-device-configuration-scripts.md)]

## <a name="about"></a>Sobre scripts de configuração do dispositivo VPN

Uma ligação de VPN em vários locais é composta por um gateway de VPN do Azure, um dispositivo VPN no local e um túnel IPsec S2S VPN ligar os dois. O fluxo de trabalho típica inclui os seguintes passos:

1. Criar e configurar um gateway de VPN do Azure (gateway de rede virtual)
2. Criar e configurar um gateway de rede local do Azure que representa a sua rede no local e o dispositivo VPN
3. Criar e configurar uma ligação de VPN do Azure entre o gateway de VPN do Azure e o gateway de rede local
4. Configurar o dispositivo VPN no local, representado pelo gateway de rede local para estabelecer o túnel S2S VPN real com o gateway de VPN do Azure

Pode concluir os passos 1 a 3 com o Azure [portal](vpn-gateway-howto-site-to-site-resource-manager-portal.md), [PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md), ou [CLI](vpn-gateway-howto-site-to-site-resource-manager-cli.md). A última etapa envolve configurar os dispositivos VPN no local fora do Azure. Esta funcionalidade permite-lhe transferir um script de configuração para o dispositivo VPN com os valores correspondentes do seu gateway de VPN do Azure, rede virtual e prefixos de endereço de rede no local e propriedades de ligação de VPN, etc. já preenchidos. Pode utilizar o script como um ponto de partida, ou aplicar o script diretamente aos dispositivos VPN no local através da consola de configuração.

> [!IMPORTANT]
> * A sintaxe para cada script de configuração de dispositivos VPN é diferente e depende muito os modelos e as versões de firmware. Preste especial atenção às suas informações de modelo e uma versão de dispositivo contra os modelos disponíveis.
> * Alguns valores de parâmetro tem de ser exclusivos no dispositivo e não podem ser determinados sem aceder ao dispositivo. Os scripts de configuração gerado pelo Azure preencher previamente estes valores, mas certifique-se de que os valores fornecidos são válidos no seu dispositivo. Para obter exemplos:
>    * Números de interface
>    * Lista de números de controlo de acesso
>    * Nomes de política ou números, etc.
> * Procurar a palavra-chave, "**SUBSTITUA**", incorporado no script para encontrar os parâmetros, tem de verificar antes de aplicar o script.
> * Alguns modelos incluem um "**limpeza**" secção pode aplicar a remover as configurações. As secções de limpeza são comentadas por predefinição.

## <a name="download-the-configuration-script-from-azure-portal"></a>Transferir o script de configuração a partir do portal do Azure

Crie um gateway de VPN do Azure, o gateway de rede local e um recurso de ligação ligar os dois. A página seguinte orienta-o ao longo dos passos:

* [Criar uma ligação Site a Site no portal do Azure](vpn-gateway-howto-site-to-site-resource-manager-portal.md)

Quando o recurso de ligação é criado, siga as instruções abaixo para transferir os scripts de configuração do dispositivo VPN:

1. Num browser, navegue para o [portal do Azure](https://portal.azure.com) e, se necessário, inicie sessão com a sua conta do Azure
2. Vá para o recurso de ligação que criou. Pode encontrar a lista de todos os recursos de ligação ao clicar em "Todos os serviços", em seguida, "Rede" e "Ligações".

    ![lista de ligação](./media/vpn-gateway-download-vpndevicescript/connectionlist.png)

3. Clique na ligação que pretende configurar.

    ![connection-overview](./media/vpn-gateway-download-vpndevicescript/connectionoverview.png)

4. Clique no link "Transferir a configuração de" como realçada em vermelho na página de descrição geral da ligação; Esta ação abre a página "Configuração de transferência".

    ![download-script-1](./media/vpn-gateway-download-vpndevicescript/downloadscript-1.png)

5. Selecione a versão de família e o firmware do modelo para o dispositivo VPN, em seguida, clique no botão "Download configuration".

    ![download66-script-2](./media/vpn-gateway-download-vpndevicescript/downloadscript-2.PNG)

6. São-lhe pedido para guardar o script transferido (um arquivo de texto) a partir do seu browser.
7. Depois de transferido o script de configuração, abra-o com um editor de texto e procurar a palavra-chave "REPLACE" para identificar e examinar os parâmetros que poderão ter de ser substituído.

    ![Editar script](./media/vpn-gateway-download-vpndevicescript/editscript.png)

## <a name="download-the-configuration-script-using-azure-powershell"></a>Transferir o script de configuração com o Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Também pode transferir o script de configuração com o Azure PowerShell, conforme mostrado no exemplo a seguir:

```azurepowershell-interactive
$RG          = "TestRG1"
$GWName      = "VNet1GW"
$Connection  = "VNet1toSite1"

# List the available VPN device models and versions
Get-AzVirtualNetworkGatewaySupportedVpnDevice -Name $GWName -ResourceGroupName $RG

# Download the configuration script for the connection
Get-AzVirtualNetworkGatewayConnectionVpnDeviceConfigScript -Name $Connection -ResourceGroupName $RG -DeviceVendor Juniper -DeviceFamily Juniper_SRX_GA -FirmwareVersion Juniper_SRX_12.x_GA
```

## <a name="apply-the-configuration-script-to-your-vpn-device"></a>Aplicar o script de configuração para o seu dispositivo VPN

Depois de ter baixado e validar o script de configuração, a próxima etapa é aplicar o script para o seu dispositivo VPN. O procedimento real varia com base nos seus modelos e torna o dispositivo VPN. Para os dispositivos VPN, consulte os manuais de operação ou as páginas de instrução.

## <a name="next-steps"></a>Passos Seguintes

Continuar a configurar seus [ligação Site a Site](vpn-gateway-howto-site-to-site-resource-manager-portal.md).
