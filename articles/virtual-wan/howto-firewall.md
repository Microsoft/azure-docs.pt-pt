---
title: Instale firewall azure num centro virtual WAN
titleSuffix: Azure Virtual WAN
description: Passos para configurar firewall Azure em um centro virtual WAN
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 05/21/2020
ms.author: cherylmc
ms.openlocfilehash: 01e703b0e1062e38095358565260addbd07351f1
ms.sourcegitcommit: cf7caaf1e42f1420e1491e3616cc989d504f0902
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/22/2020
ms.locfileid: "83801563"
---
# <a name="configure-azure-firewall-in-a-virtual-wan-hub"></a>Configure Firewall Azure em um centro virtual WAN

Um **hub seguro** é um hub Azure Virtual WAN com Firewall Azure. Este artigo percorre-o através dos degraus para converter um hub virtual WAN para um hub seguro, instalando o Azure Firewall diretamente a partir das páginas do portal Azure Virtual WAN.

## <a name="before-you-begin"></a>Antes de começar

Os passos neste artigo assumem que já implementou um WAN virtual com um ou mais hubs.

Para criar um novo WAN virtual e um novo hub, use os passos nos seguintes artigos:

* [Criar uma WAN Virtual](virtual-wan-site-to-site-portal.md#openvwan)
* [Criar um hub](virtual-wan-site-to-site-portal.md#hub)

## <a name="view-virtual-hubs"></a>Ver centros virtuais

A página **de visão geral** para o seu WAN virtual mostra uma lista de centros virtuais e centros protegidos. A figura que se segue mostra um WAN virtual sem centros seguros.

[![visão geral](./media/howto-firewall/overview.png)](./media/howto-firewall/overview.png#lightbox)

## <a name="convert-to-secured-hub"></a>Converter em centro seguro

1. Na página **'Overview'** para o seu WAN virtual, selecione o hub que pretende converter para um hub seguro. Na página do hub virtual, você vê duas opções para implantar o Azure Firewall neste hub. Selecione qualquer uma das opções.

   [![segurança](./media/howto-firewall/security.png)](./media/howto-firewall/security.png#lightbox)

1. Depois de selecionar uma das opções, consulte o Converter para garantir a página **do hub.** Selecione um hub para converter e, em seguida, **selecione Seguinte: Firewall Azure** na parte inferior da página.

   [![selecione hub](./media/howto-firewall/select-hub.png)](./media/howto-firewall/select-hub.png#lightbox)
1. Depois de completar o fluxo de trabalho, selecione **Confirmar**.

   [![confirmar](./media/howto-firewall/confirm.png)](./media/howto-firewall/confirm.png#lightbox)

1. Depois de o hub ter sido convertido para um hub seguro, pode vê-lo na página virtual WAN **Overview.**

   [![ver centro seguro](./media/howto-firewall/secured-hub.png)](./media/howto-firewall/secured-hub.png#lightbox)

## <a name="view-hub-resources"></a>Ver recursos do hub

A partir da página virtual WAN **Overview,** selecione o hub seguro. Na página do hub, você pode ver todos os recursos do hub virtual, incluindo O Firewall Azure.

[![ver recursos hub](./media/howto-firewall/view-resources.png)](./media/howto-firewall/view-resources.png#lightbox)

Para visualizar as definições de Firewall Azure a partir do centro protegido, em **'Segurança',** selecione **definições de hub virtual protegidos**.
[![ver configurações do hub](./media/howto-firewall/hub-settings.png)](./media/howto-firewall/hub-settings.png#lightbox)

## <a name="configure-additional-settings"></a>Configurar definições adicionais

Para configurar definições adicionais de Firewall Azure para o hub virtual, selecione o link para **O Gestor de Firewall Azure**. Para obter informações sobre as políticas de firewall, consulte [O Gestor de Firewall azure](../firewall-manager/secure-cloud-network.md#create-a-firewall-policy-and-secure-your-hub).

[![configurações adicionais](./media/howto-firewall/additional-settings.png)](./media/howto-firewall/additional-settings.png#lightbox)

Para voltar à página de **visão geral** do hub, pode navegar de volta clicando no caminho, como mostra a seta na figura seguinte.

[![voltar à visão geral](./media/howto-firewall/arrow.png)](./media/howto-firewall/arrow.png#lightbox)

## <a name="next-steps"></a>Passos seguintes

Para mais informações sobre o WAN Virtual, consulte as [FAQ](virtual-wan-faq.md).
