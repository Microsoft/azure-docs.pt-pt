---
title: Instale a Firewall Azure num hub virtual WAN
titleSuffix: Azure Virtual WAN
description: Passos para configurar a Firewall Azure num hub virtual WAN
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 09/22/2020
ms.author: cherylmc
ms.openlocfilehash: 577340e485550e84941a33d82b58aa6ff1c933d3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90983670"
---
# <a name="configure-azure-firewall-in-a-virtual-wan-hub"></a>Configure Azure Firewall em um hub VIRTUAL WAN

Um **hub seguro** é um hub Azure Virtual WAN com Azure Firewall. Este artigo percorre os passos para converter um hub WAN virtual para um hub seguro, instalando o Azure Firewall diretamente a partir das páginas do portal Azure Virtual WAN.

## <a name="before-you-begin"></a>Antes de começar

Os passos deste artigo assumem que já implementou um WAN virtual com um ou mais hubs.

Para criar um novo WAN virtual e um novo hub, utilize os passos nos seguintes artigos:

* [Criar uma WAN Virtual](virtual-wan-site-to-site-portal.md#openvwan)
* [Criar um hub](virtual-wan-site-to-site-portal.md#hub)

## <a name="view-virtual-hubs"></a>Ver centros virtuais

A **página de visão geral** do seu WAN virtual mostra uma lista de centros virtuais e centros seguros. A seguinte figura mostra um WAN virtual sem centros seguros.

[![A screenshot mostra a página de visão geral para um WAN virtual com uma lista de centros virtuais.](./media/howto-firewall/overview.png)](./media/howto-firewall/overview.png#lightbox)

## <a name="convert-to-secured-hub"></a>Converter para centro seguro

1. Na página **'Vista Geral'** para o seu WAN virtual, selecione o hub que pretende converter num hub seguro. Na página do hub virtual, você vê duas opções para colocar Azure Firewall neste hub. Selecione qualquer uma das opções.

   [![A screenshot mostra a página 'Vista Geral' para o seu WAN virtual, onde pode selecionar o Convert para o hub seguro ou o Azure Firewall.](./media/howto-firewall/security.png)](./media/howto-firewall/security.png#lightbox)

1. Depois de selecionar uma das opções, vê o **Converse para proteger a** página do hub. Selecione um hub para converter e, em seguida, selecione **Seguinte: Azure Firewall** na parte inferior da página.

   [![selecionar hub](./media/howto-firewall/select-hub.png)](./media/howto-firewall/select-hub.png#lightbox)
1. Depois de completar o fluxo de trabalho, **selecione Confirmar**.

   [![A screenshot mostra o Converse para proteger o painel do hub com confirmação selecionado.](./media/howto-firewall/confirm.png)](./media/howto-firewall/confirm.png#lightbox)

1. Depois de o hub ter sido convertido para um hub seguro, pode vê-lo na **página** geral do WAN virtual.

   [![ver centro seguro](./media/howto-firewall/secured-hub.png)](./media/howto-firewall/secured-hub.png#lightbox)

## <a name="view-hub-resources"></a>Ver recursos do hub

A partir da **página** geral do WAN, selecione o hub seguro. Na página do hub, você pode ver todos os recursos do hub virtual, incluindo Azure Firewall.

[![ver recursos hub](./media/howto-firewall/view-resources.png)](./media/howto-firewall/view-resources.png#lightbox)

Para visualizar as definições de Azure Firewall a partir do hub seguro, sob **Segurança**, selecione **configurações de hub virtual Secured**.
[![ver configurações do hub](./media/howto-firewall/hub-settings.png)](./media/howto-firewall/hub-settings.png#lightbox)

## <a name="configure-additional-settings"></a>Configurar definições adicionais

Para configurar as definições adicionais do Azure Firewall para o hub virtual, selecione a ligação para **O Azure Firewall Manager**. Para obter informações sobre as políticas de firewall, consulte [o Azure Firewall Manager](../firewall-manager/secure-cloud-network.md#create-a-firewall-policy-and-secure-your-hub).

[![definições adicionais](./media/howto-firewall/additional-settings.png)](./media/howto-firewall/additional-settings.png#lightbox)

Para voltar à página **de visão geral** do hub, pode navegar de volta clicando no caminho, como mostrado pela seta na figura seguinte.

[![voltar à visão geral](./media/howto-firewall/arrow.png)](./media/howto-firewall/arrow.png#lightbox)

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre o VIRTUAL WAN, consulte as [FAQ.](virtual-wan-faq.md)
