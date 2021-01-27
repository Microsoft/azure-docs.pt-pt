---
title: 'Tutorial - Criar e gerir um Portal VPN Gateway: Azure'
description: Siga este tutorial para aprender a criar, implementar e gerir um Gateway Azure VPN usando o portal
author: cherylmc
ms.author: cherylmc
ms.service: vpn-gateway
ms.topic: tutorial
ms.date: 12/01/2020
ms.openlocfilehash: 820482a268af038737557c517ccda086cd65d943
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/27/2021
ms.locfileid: "98880563"
---
# <a name="tutorial-create-and-manage-a-vpn-gateway-using-azure-portal"></a>Tutorial: Criar e gerir uma porta de entrada VPN usando o portal Azure

Os gateways de VPN do Azure fornecem conectividade em vários locais entre as instalações do cliente e o Azure. Este tutorial abrange itens básicos da implementação do gateway de VPN do Azure, tais como criar e gerir um gateway de VPN. Também pode criar um portal utilizando [Azure CLI](create-routebased-vpn-gateway-cli.md) ou [Azure PowerShell](create-routebased-vpn-gateway-powershell.md).

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar uma rede virtual
> * Criar um gateway de VPN
> * Ver o endereço IP público gateway
> * Redimensione uma porta VPN (redimensione sKU)
> * Repor um Gateway de VPN

O diagrama seguinte mostra a rede virtual e o gateway de VPN criados como parte deste tutorial.

:::image type="content" source="./media/tutorial-create-gateway-portal/gateway-diagram.png" alt-text="Diagrama de gateway VNet e VPN":::

## <a name="prerequisites"></a>Pré-requisitos

Uma conta Azure com uma subscrição ativa. Se não tiver um, [crie um de graça.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)

## <a name="create-a-virtual-network"></a><a name="CreatVNet"></a>Criar uma rede virtual

Criar um VNet utilizando os seguintes valores:

* **Grupo de recursos:** TestRG1
* **Nome:** VNet1
* **Região:** (EUA) Leste dos EUA
* **Espaço de endereço IPv4:** 10.1.0.0/16
* **Nome da sub-rede:** FrontEnd
* **Espaço de endereço da sub-rede:** 10.1.0.0/24

[!INCLUDE [Create a virtual network](../../includes/vpn-gateway-basic-vnet-rm-portal-include.md)]

## <a name="create-a-vpn-gateway"></a><a name="VNetGateway"></a>Criar um gateway de VPN

Neste passo, vai criar o gateway de rede virtual da VNet. Criar um gateway, muitas vezes, pode demorar 45 minutos ou mais, dependendo do SKU de gateway selecionado.

Criar um gateway de rede virtual utilizando os seguintes valores:

* **Nome:** VNet1GW
* **Região:** Leste dos EUA
* **Tipo de gateway:** VPN
* **Tipo de VPN:** baseado na rota
* **SKU:** VpnGw1
* **Geração:** Geração1
* **Rede virtual:** VNet1
* **Intervalo de endereço da sub-rede Gateway:** 10.1.255.0/27
* **Endereço IP público:** Criar novo
* **Nome do endereço IP público:** VNet1GWpip
* **Ativar o modo ativo:** Deficientes
* **Configure BGP:** Deficientes

[!INCLUDE [Create a vpn gateway](../../includes/vpn-gateway-add-gw-rm-portal-include.md)]

[!INCLUDE [NSG warning](../../includes/vpn-gateway-no-nsg-include.md)]

## <a name="view-the-public-ip-address"></a><a name="view"></a>Ver o endereço IP público

Pode ver o endereço IP público gateway na página **'Vista Geral'** para o seu gateway.

:::image type="content" source="./media/tutorial-create-gateway-portal/address.png" alt-text="Página de descrição geral":::

Para ver informações adicionais sobre o objeto de endereço IP público, clique no link de endereço nome/IP ao lado **do endereço IP Público.**

## <a name="resize-a-gateway-sku"></a><a name="resize"></a>Redimensione um gateway SKU

Existem regras específicas relativas ao redimensionamento vs. alterando um gateway SKU. Nesta secção, vamos redimensionar o SKU. Para obter mais informações, consulte [as definições gateway - redimensionamento e alteração de SKUs](vpn-gateway-about-vpn-gateway-settings.md#resizechange).

[!INCLUDE [resize a gateway](../../includes/vpn-gateway-resize-gw-portal-include.md)]

## <a name="reset-a-gateway"></a><a name="reset"></a>Redefinir uma porta de entrada

[!INCLUDE [reset a gateway](../../includes/vpn-gateway-reset-gw-portal-include.md)]

## <a name="clean-up-resources"></a>Limpar os recursos

Se não continuar a utilizar esta aplicação ou ir ao próximo tutorial, elimine estes recursos utilizando os seguintes passos:

1. Introduza o nome do seu grupo de recursos na caixa **de Pesquisa** no topo do portal e selecione-o a partir dos resultados da pesquisa.

1. Selecione **Eliminar grupo de recursos**.

1. Introduza o seu grupo de recursos para **TYPE THE RESOURCE GROUP NAME** e selecione **Delete**.

## <a name="next-steps"></a>Próximos passos

Uma vez que tenha uma porta VPN, pode configurar ligações. Os artigos abaixo irão ajudá-lo a criar algumas das configurações mais comuns:

> [!div class="nextstepaction"]
> [Ligações VPN site-to-site](./tutorial-site-to-site-portal.md)

> [!div class="nextstepaction"]
> [Ligações VPN ponto-a-local](vpn-gateway-howto-point-to-site-resource-manager-portal.md)