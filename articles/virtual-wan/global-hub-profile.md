---
title: Descarregue perfis VPN globais ou baseados em hubs da Azure Virtual WAN | Microsoft Docs
description: Saiba como o Azure Virtual WAN oferece dois tipos de conectividade para utilizadores remotos e como descarregar um perfil.
services: virtual-wan
author: kumudD
ms.service: virtual-wan
ms.topic: how-to
ms.date: 09/22/2020
ms.author: alzam
ms.openlocfilehash: 6d5c4ba0f1f55119d1ec38296e67ae3e90c52650
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "91313727"
---
# <a name="download-a-global-or-hub-based-profile-for-user-vpn-clients"></a>Faça o download de um perfil global ou baseado em hub para clientes VPN de utilizador

O Azure Virtual WAN oferece dois tipos de conectividade para utilizadores remotos: Global e Baseado em Hub. Use as seguintes secções para saber e fazer o download de um perfil. 

> [!IMPORTANT]
> A autenticação RADIUS suporta apenas o perfil baseado no Hub.

## <a name="global-profile"></a>Perfil global

O perfil aponta para um equilibrador de carga que inclui todos os hubs VPN do utilizador ativo. O utilizador é direcionado para o hub mais próximo da localização geográfica do utilizador. Este tipo de conectividade é útil quando os utilizadores viajam para diferentes locais frequentemente. Para descarregar o perfil **global:**

1. Navegue para a WAN virtual.
2. Clique **na configuração VPN do utilizador**.
3. Realce a configuração para a qual pretende descarregar o perfil.
4. Clique **em Baixar o perfil VPN do utilizador WAN virtual.**

   ![Perfil global](./media/global-hub-profile/global1.png)

## <a name="hub-based-profile"></a>Perfil baseado em hub

O perfil aponta para um único centro. O utilizador só pode ligar-se ao hub em particular utilizando este perfil. Para descarregar o perfil **baseado no hub:**

1. Navegue para a WAN virtual.
2. Clique no **Hub** na página 'Vista Geral'.

    ![Perfil do centro 1](./media/global-hub-profile/hub1.png)
3. Clique **em VPN do utilizador (ponto a site)**.
4. Clique **em Baixar o perfil VPN do utilizador do utilizador do hub virtual**.

   ![Perfil do centro 2](./media/global-hub-profile/hub2.png)
5. Verifique **os EAPTLS.**
6. Clique **em Gerar e descarregar o perfil**.

   ![Perfil do centro 3](./media/global-hub-profile/download.png)

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre a WAN Virtual, veja a página [Virtual WAN Overview](virtual-wan-about.md) (Descrição Geral da WAN Virtual).
