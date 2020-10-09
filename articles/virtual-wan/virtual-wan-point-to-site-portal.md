---
title: Utilizar a WAN Virtual do Azure para criar uma ligação ponto a site ao Azure | Microsoft Docs
description: Neste tutorial, vai aprender a utilizar uma WAN Virtual do Azure para criar uma ligação VPN ponto a site ao Azure.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: tutorial
ms.date: 10/06/2020
ms.author: cherylmc
ms.openlocfilehash: 84f8563a6b03f10f4cbc647426c350d9fac52780
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91812689"
---
# <a name="tutorial-create-a-user-vpn-connection-using-azure-virtual-wan"></a>Tutorial: Criar uma ligação VPN do utilizador utilizando a Azure Virtual WAN

Este tutorial mostra como utilizar a WAN Virtual para se ligar aos seus recursos no Azure através de uma ligação VPN IPsec/IKE (IKEv2) ou OpenVPN. Este tipo de ligação exige a configuração de um cliente no computador cliente. Para mais informações sobre o VIRTUAL WAN, consulte a [Visão Geral do WAN Virtual](virtual-wan-about.md)

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar uma WAN
> * Criar uma configuração P2S
> * Criar um hub
> * Especificar servidores DNS
> * Faça o download de um perfil de cliente VPN
> * Ver a WAN Virtual

![Diagrama da WAN Virtual](./media/virtual-wan-about/virtualwanp2s.png)

## <a name="before-you-begin"></a>Antes de começar

[!INCLUDE [Before beginning](../../includes/virtual-wan-before-include.md)]

## <a name="create-a-virtual-wan"></a><a name="wan"></a>Criar uma WAN Virtual

[!INCLUDE [Create a virtual WAN](../../includes/virtual-wan-create-vwan-include.md)]

## <a name="create-a-p2s-configuration"></a><a name="p2sconfig"></a>Criar uma configuração P2S

Uma configuração ponto-a-local (P2S) define os parâmetros para ligar clientes remotos.

[!INCLUDE [Create client profiles](../../includes/virtual-wan-p2s-configuration-include.md)]

## <a name="create-hub-with-point-to-site-gateway"></a><a name="hub"></a>Criar hub com gateway ponto-a-local

[!INCLUDE [Create hub](../../includes/virtual-wan-p2s-hub-include.md)]

## <a name="specify-dns-server"></a><a name="dns"></a>Especificar o servidor DNS

As portas VPN do utilizador Virtual WAN permitem especificar até 5 Servidores DNS. Pode configurar isto durante o processo de criação do hub ou modificá-lo mais tarde. Para tal, localize o centro virtual. Em **VPN do Utilizador (ponto a site)**, selecione **configuração** e introduza o endereço IP do servidor DNS na caixa de texto **dos Servidores DNS** personalizados(es).

   :::image type="content" source="media/virtual-wan-point-to-site-portal/custom-dns.png" alt-text="DNS personalizado" lightbox="media/virtual-wan-point-to-site-portal/custom-dns-expand.png":::

## <a name="download-vpn-profile"></a><a name="download"></a>Transferir perfil VPN

Utilize o perfil VPN para configurar os seus clientes.

[!INCLUDE [Download profile](../../includes/virtual-wan-p2s-download-profile-include.md)]

### <a name="configure-user-vpn-clients"></a>Configure clientes VPN do utilizador

Utilize o perfil transferido para configurar os clientes de acesso remoto. O procedimento para cada sistema operativo é diferente, siga as instruções que se aplicam ao seu sistema.

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

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre a WAN Virtual, veja a página [Virtual WAN Overview](virtual-wan-about.md) (Descrição Geral da WAN Virtual).
