---
title: 'Adicione várias ligações VPN gateway Site-to-Site a um VNet: Azure portal'
description: Adicione ligações S2S multi-locais a um gateway VPN que tem uma ligação existente
services: vpn-gateway
titleSuffix: Azure VPN Gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 10/27/2020
ms.author: cherylmc
ms.openlocfilehash: 92d39b0d39511571701fd092f641cb8ca3ae42c7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "92890173"
---
# <a name="add-additional-s2s-connections-to-a-vnet-azure-portal"></a>Adicione ligações S2S adicionais a um VNet: Portal Azure

> [!div class="op_single_selector"]
> * [Portal do Azure](vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md)
> * [PowerShell (clássico)](vpn-gateway-multi-site.md)
>

Este artigo ajuda-o a adicionar ligações adicionais site-to-site (S2S) a um gateway VPN que tem uma ligação existente. Esta arquitetura é frequentemente referida como uma configuração "multi-site". Pode adicionar uma ligação S2S a um VNet que já tem uma ligação S2S, ligação ponto-a-local ou ligação VNet-to-VNet. Existem algumas limitações na adição de ligações. Consulte a secção [Pré-Requisitos](#before) neste artigo para verificar antes de iniciar a sua configuração.

Este artigo aplica-se aos VNets do Gestor de Recursos que têm uma porta VPN RouteBased. Estes passos não se aplicam às novas configurações de conexão coexistindo ExpressRoute/Site-to-Site. No entanto, se estiver apenas a adicionar uma nova ligação VPN a uma configuração coexistidora já existente, pode utilizar estes passos. Consulte [as ligações coexistindo ExpressRoute/S2S](../expressroute/expressroute-howto-coexist-resource-manager.md) para obter informações sobre ligações coexistindo.

## <a name="prerequisites"></a><a name="before"></a>Pré-requisitos

Verifique os seguintes itens:

* Não está a configurar uma nova configuração expressRoute e VPN Gateway coexistindo.
* Tem uma rede virtual que foi criada utilizando o modelo de implementação do Gestor de Recursos com uma ligação existente.
* O portal de rede virtual para o seu VNet é RouteBased. Se tiver uma porta VPN VPN de 44 de política, deve eliminar o gateway de rede virtual e criar um novo gateway VPN como RouteBased.
* Nenhum dos intervalos de endereços se sobrepõe a nenhum dos VNets a que este VNet está a ligar.
* Tem um dispositivo VPN compatível e alguém que seja capaz de o configurar. Veja [Acerca dos Dispositivos VPN](vpn-gateway-about-vpn-devices.md). Se não estiver familiarizado com a configuração do dispositivo VPN ou com os intervalos de endereços IP localizados na configuração da rede no local, tem de se coordenar com alguém que consiga fornecer esses detalhes.
* Tem um endereço IP público virado para o exterior para o seu dispositivo VPN.

## <a name="configure-a-connection"></a><a name="configure"></a>Configure uma ligação

1. Num browser, navegue para o [Portal do Azure](https://portal.azure.com) e, se necessário, inicie sessão com a sua conta do Azure.
1. Selecione **Todos os recursos** e localize o seu portal de rede **virtual** a partir da lista de recursos e selecione-o.
1. Na página **de gateway de rede Virtual,** selecione **Connections**.

   :::image type="content" source="./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/connections.png" alt-text="Ligações de gateway VPN":::
1. Na página **'Ligações',** selecione **+Adicionar**.
1. Isto abre a página **de ligação Add.**

   :::image type="content" source="./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/add-connection.png" alt-text="Adicionar página de conexão":::
1. Na página **de ligação Adicionar,** preencha os seguintes campos:

   * **Nome:** O nome que pretende dar ao site a que está a criar a ligação.
   * **Tipo de ligação:** Selecione **Site-to-site (IPsec)**.

## <a name="add-a-local-network-gateway"></a><a name="local"></a>Adicione um portal de rede local

1. Para o campo **de gateway de rede local,** selecione Escolha uma porta de rede **_local_*_. Isto abre a*** página _ Escolha a página de gateway de rede local.
1. Selecione **+ Crie novo** para abrir a página de gateway de rede **local.**

   :::image type="content" source="./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/create-local-network-gateway.png" alt-text="Criar página de gateway de rede local":::
1. Na página de gateway de **rede local,** preencha os seguintes campos:

   * **Nome:** O nome que pretende dar ao recurso de gateway de rede local.
   * **Ponto final:** O endereço IP público do dispositivo VPN no site a que pretende ligar, ou o FQDN do ponto final.
   * **Espaço de endereço:** O espaço de endereço que deseja ser encaminhado para o novo site da rede local.
1. Selecione **OK** na página **de gateway de rede local** para guardar as alterações.

## <a name="add-the-shared-key"></a><a name="part3"></a>Adicione a chave partilhada

1. Depois de criar o gateway de rede local, volte à página **de ligação Add.**
1. Complete os campos restantes. Para a **tecla Partilhada (PSK),** pode obter a chave partilhada do seu dispositivo VPN ou fazer uma aqui em cima e, em seguida, configurar o seu dispositivo VPN para usar a mesma chave partilhada. O importante é que as chaves são exatamente iguais.

## <a name="create-the-connection"></a><a name="create"></a>Criar a ligação

1. Na parte inferior da página, selecione **OK** para criar a ligação. A ligação começa a criar imediatamente.
1. Uma vez concluída a ligação, pode vê-la e verificar.

## <a name="view-and-verify-the-vpn-connection"></a><a name="verify"></a>Ver e verificar a ligação VPN

[!INCLUDE [Verify the connection](../../includes/vpn-gateway-verify-connection-portal-include.md)]

## <a name="next-steps"></a>Passos seguintes

Assim que a ligação estiver concluída, pode adicionar máquinas virtuais às redes virtuais. Para obter mais informações, consulte [os caminhos de aprendizagem das máquinas virtuais.](/learn/paths/deploy-a-website-with-azure-virtual-machines/)
