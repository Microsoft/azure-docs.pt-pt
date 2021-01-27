---
title: 'Tutorial: Use Azure Virtual WAN para criar uma ligação ponto-a-local ao Azure'
description: Neste tutorial, vai aprender a utilizar uma WAN Virtual do Azure para criar uma ligação VPN ponto a site ao Azure.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: tutorial
ms.date: 11/09/2020
ms.author: cherylmc
ms.openlocfilehash: 1876ab86e6f4c46edc23361dd884d8b32328f36c
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/27/2021
ms.locfileid: "98919074"
---
# <a name="tutorial-create-a-user-vpn-connection-using-azure-virtual-wan"></a>Tutorial: Criar uma ligação VPN do utilizador utilizando a Azure Virtual WAN

Este tutorial mostra como utilizar a WAN Virtual para se ligar aos seus recursos no Azure através de uma ligação VPN IPsec/IKE (IKEv2) ou OpenVPN. Este tipo de ligação requer que o cliente VPN seja configurado no computador cliente. Para obter mais informações sobre a WAN Virtual, veja a [Descrição Geral da WAN Virtual](virtual-wan-about.md).

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar uma WAN Virtual
> * Criar uma configuração P2S
> * Criar um hub virtual
> * Escolha piscinas de endereços de cliente
> * Especificar servidores DNS
> * Gerei o pacote de configuração do perfil do cliente VPN
> * Configurar clientes VPN
> * Ver a WAN Virtual

![Diagrama da WAN Virtual](./media/virtual-wan-about/virtualwanp2s.png)

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [Before beginning](../../includes/virtual-wan-before-include.md)]

## <a name="create-a-virtual-wan"></a><a name="wan"></a>Criar uma WAN Virtual

[!INCLUDE [Create a virtual WAN](../../includes/virtual-wan-create-vwan-include.md)]

## <a name="create-a-p2s-configuration"></a><a name="p2sconfig"></a>Criar uma configuração P2S

Uma configuração ponto-a-local (P2S) define os parâmetros para ligar clientes remotos.

[!INCLUDE [Create P2S configuration](../../includes/virtual-wan-p2s-configuration-include.md)]

## <a name="create-virtual-hub-and-gateway"></a><a name="hub"></a>Criar hub virtual e gateway

[!INCLUDE [Create hub](../../includes/virtual-wan-p2s-hub-include.md)]


## <a name="choose-p2s-client-address-pools"></a><a name="chooseclientpools"></a> Escolha piscinas de endereços de cliente P2S

[!INCLUDE [Choose pools](../../includes/virtual-wan-allocating-p2s-pools.md)]

## <a name="specify-dns-server"></a><a name="dns"></a>Especificar o servidor DNS

Pode configurar esta definição quando criar o hub ou modificá-lo mais tarde. Para modificar, localize o centro virtual. Em **VPN do utilizador (ponto a site)**, selecione **Configurar** e introduza o endereço IP do servidor DNS na caixa de texto **dos Servidores DNS** personalizados(es). Pode especificar até 5 Servidores DNS.

   :::image type="content" source="media/virtual-wan-point-to-site-portal/custom-dns.png" alt-text="DNS personalizado" lightbox="media/virtual-wan-point-to-site-portal/custom-dns-expand.png":::

## <a name="generate-vpn-client-profile-package"></a><a name="download"></a>Gere pacote de perfil de cliente VPN

Gere e descarregue o pacote de perfil de cliente VPN para configurar os seus clientes VPN.

[!INCLUDE [Download profile](../../includes/virtual-wan-p2s-download-profile-include.md)]

## <a name="configure-vpn-clients"></a><a name="configure-client"></a>Configurar clientes VPN

Utilize o pacote de perfil descarregado para configurar os clientes VPN de acesso remoto. O procedimento para cada sistema operativo é diferente. Siga as instruções que se aplicam ao seu sistema.
Uma vez terminada a configuração do seu cliente, pode ligar-se.

[!INCLUDE [Configure clients](../../includes/virtual-wan-p2s-configure-clients-include.md)]

## <a name="view-your-virtual-wan"></a><a name="viewwan"></a>Ver a WAN Virtual

1. Navegue para a WAN virtual.
1. Na página **'Visão Geral',** cada ponto no mapa representa um hub.
1. Na secção **Hubs e conexões,** pode ver o estado do hub, site, região, estado de ligação VPN e bytes dentro e fora.



## <a name="clean-up-resources"></a><a name="cleanup"></a>Limpar os recursos

Quando já não precisar desses recursos, pode utilizar [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) para remover o grupo de recursos e todos os recursos que o mesmo contém. Substitua "myResourceGroup" pelo nome do grupo de recursos e execute o seguinte comando do PowerShell:

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Próximos passos

Em seguida, para saber mais sobre O WAN Virtual, consulte:

> [!div class="nextstepaction"]
> * [FAQ da WAN Virtual](virtual-wan-faq.md)
