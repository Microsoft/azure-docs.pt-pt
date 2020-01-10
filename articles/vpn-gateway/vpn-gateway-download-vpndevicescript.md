---
title: Baixar scripts de configuração de dispositivo VPN para conexões VPN S2S
description: Este artigo explica como baixar scripts de configuração de dispositivo VPN para conexões VPN S2S com gateways de VPN do Azure usando o Azure Resource Manager.
titleSuffix: Azure VPN Gateway
services: vpn-gateway
author: yushwang
manager: rossort
ms.service: vpn-gateway
ms.topic: article
ms.date: 01/09/2019
ms.author: yushwang
ms.openlocfilehash: 4bce6a05938af9b8726b1b52ccb65d22d7492cfe
ms.sourcegitcommit: 5b073caafebaf80dc1774b66483136ac342f7808
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/09/2020
ms.locfileid: "75778454"
---
# <a name="download-vpn-device-configuration-scripts-for-s2s-vpn-connections"></a>Baixar scripts de configuração de dispositivo VPN para conexões VPN S2S

Este artigo explica como baixar scripts de configuração de dispositivo VPN para conexões VPN S2S com gateways de VPN do Azure usando o Azure Resource Manager. O diagrama a seguir mostra o fluxo de trabalho de alto nível.

![Download-script](./media/vpn-gateway-download-vpndevicescript/downloaddevicescript.png)

Os seguintes dispositivos têm scripts disponíveis:

[!INCLUDE [scripts](../../includes/vpn-gateway-device-configuration-scripts.md)]

## <a name="about"></a>Sobre os scripts de configuração do dispositivo VPN

Uma conexão VPN entre locais consiste em um gateway de VPN do Azure, um dispositivo VPN local e um túnel VPN S2S IPsec conectando os dois. O fluxo de trabalho típico inclui as seguintes etapas:

1. Criar e configurar um gateway de VPN do Azure (gateway de rede virtual)
2. Criar e configurar um gateway de rede local do Azure que representa a rede e o dispositivo de VPN locais
3. Criar e configurar uma conexão VPN do Azure entre o gateway de VPN do Azure e o gateway de rede local
4. Configurar o dispositivo VPN local representado pelo gateway de rede local para estabelecer o túnel VPN S2S real com o gateway de VPN do Azure

Você pode concluir as etapas 1 a 3 usando o [portal](vpn-gateway-howto-site-to-site-resource-manager-portal.md)do Azure, o [PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md)ou a [CLI](vpn-gateway-howto-site-to-site-resource-manager-cli.md). A última etapa envolve a configuração de dispositivos VPN locais fora do Azure. Esse recurso permite que você baixe um script de configuração para seu dispositivo VPN com os valores correspondentes de seu gateway de VPN do Azure, rede virtual e prefixos de endereço de rede local e propriedades de conexão VPN, etc. já preenchidos. Você pode usar o script como um ponto de partida ou aplicar o script diretamente aos dispositivos VPN locais por meio do console de configuração.

> [!IMPORTANT]
> * A sintaxe para cada script de configuração de dispositivo VPN é diferente e depende muito do modelo e das versões de firmware. Preste atenção especial ao modelo do dispositivo e às informações de versão em relação aos modelos disponíveis.
> * Alguns valores de parâmetro devem ser exclusivos no dispositivo e não podem ser determinados sem acessar o dispositivo. Os scripts de configuração gerados pelo Azure preenchem previamente esses valores, mas você precisa garantir que os valores fornecidos sejam válidos em seu dispositivo. Para obter exemplos:
>    * Números de interface
>    * Números da lista de controle de acesso
>    * Nomes ou números de política, etc.
> * Procure a palavra-chave "**replace**", inserida no script para localizar os parâmetros que você precisa verificar antes de aplicar o script.
> * Alguns modelos incluem uma seção de "**limpeza**" que você pode aplicar para remover as configurações. As seções de limpeza são comentadas por padrão.

## <a name="download-the-configuration-script-from-azure-portal"></a>Baixe o script de configuração do portal do Azure

Crie um gateway de VPN do Azure, um gateway de rede local e um recurso de conexão conectando os dois. A página a seguir orienta você pelas etapas:

* [Criar uma conexão site a site no portal do Azure](vpn-gateway-howto-site-to-site-resource-manager-portal.md)

Depois que o recurso de conexão for criado, siga as instruções abaixo para baixar os scripts de configuração do dispositivo VPN:

1. Em um navegador, navegue até a [portal do Azure](https://portal.azure.com) e, se necessário, entre com sua conta do Azure
2. Vá para o recurso de conexão que você criou. Você pode encontrar a lista de todos os recursos de conexão clicando em "todos os serviços", em "rede" e "conexões".

    ![lista de conexões](./media/vpn-gateway-download-vpndevicescript/connectionlist.png)

3. Clique na conexão que você deseja configurar.

    ![conexão-visão geral](./media/vpn-gateway-download-vpndevicescript/connectionoverview.png)

4. Clique no link "configuração de download" como realçado em vermelho na página Visão geral da conexão; Isso abre a página "configuração de download".

    ![download-script-1](./media/vpn-gateway-download-vpndevicescript/downloadscript-1.png)

5. Selecione a família de modelos e a versão de firmware para seu dispositivo VPN e clique no botão "configuração de download".

    ![download66-script-2](./media/vpn-gateway-download-vpndevicescript/downloadscript-2.PNG)

6. Você será solicitado a salvar o script baixado (um arquivo de texto) do seu navegador.
7. Depois de baixar o script de configuração, abra-o com um editor de texto e procure a palavra-chave "REPLACE" para identificar e examinar os parâmetros que talvez precisem ser substituídos.

    ![Editar script](./media/vpn-gateway-download-vpndevicescript/editscript.png)

## <a name="download-the-configuration-script-using-azure-powershell"></a>Baixar o script de configuração usando Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Você também pode baixar o script de configuração usando Azure PowerShell, conforme mostrado no exemplo a seguir:

```azurepowershell-interactive
$RG          = "TestRG1"
$GWName      = "VNet1GW"
$Connection  = "VNet1toSite1"

# List the available VPN device models and versions
Get-AzVirtualNetworkGatewaySupportedVpnDevice -Name $GWName -ResourceGroupName $RG

# Download the configuration script for the connection
Get-AzVirtualNetworkGatewayConnectionVpnDeviceConfigScript -Name $Connection -ResourceGroupName $RG -DeviceVendor Juniper -DeviceFamily Juniper_SRX_GA -FirmwareVersion Juniper_SRX_12.x_GA
```

## <a name="apply-the-configuration-script-to-your-vpn-device"></a>Aplicar o script de configuração ao seu dispositivo VPN

Depois de baixar e validar o script de configuração, a próxima etapa é aplicar o script ao seu dispositivo VPN. O procedimento real varia de acordo com os modelos e as marcas de seu dispositivo VPN. Consulte os manuais de operação ou as páginas de instruções para seus dispositivos VPN.

## <a name="next-steps"></a>Passos seguintes

Continue Configurando sua [conexão site a site](vpn-gateway-howto-site-to-site-resource-manager-portal.md).
