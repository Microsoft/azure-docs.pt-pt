---
title: Baixe os perfis VPN globais ou baseados em hub Azure Virtual WAN [ Microsoft Docs
description: Saiba mais sobre a conectividade automática de ramo-a-filial virtual WAN, regiões disponíveis e parceiros.
services: virtual-wan
author: anzaman
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 4/20/2020
ms.author: alzam
ms.openlocfilehash: b63bb861f4df087f852bb1bf599d32100d063f7e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81733190"
---
# <a name="download-a-global-or-hub-based-profile-for-user-vpn-clients"></a>Descarregue um perfil global ou baseado em hub para clientes VPN de utilizador

O Azure Virtual WAN oferece dois tipos de conectividade para utilizadores remotos: Global e Hub. Utilize as seguintes secções para saber e baixar um perfil. 

> [!IMPORTANT]
> A autenticação RADIUS suporta apenas o perfil baseado no Hub.

## <a name="global-profile"></a>Perfil global

O perfil aponta para um equilibrador de carga que inclui todos os hubs VPN do Utilizador ativos. O utilizador é direcionado para o centro que está mais próximo da localização geográfica do utilizador. Este tipo de conectividade é útil quando os utilizadores viajam frequentemente para diferentes locais. Para descarregar o perfil **global:**

1. Navegue para a WAN virtual.
2. Clique na **configuração VPN do utilizador**.
3. Realce a configuração para a qual pretende descarregar o perfil.
4. Clique em **Baixar o perfil VPN do utilizador virtual WAN**.

   ![Perfil global](./media/global-hub-profile/global1.png)

## <a name="hub-based-profile"></a>Perfil baseado no hub

O perfil aponta para um único centro. O utilizador só pode ligar-se ao hub específico utilizando este perfil. Para descarregar o perfil **baseado no hub:**

1. Navegue para a WAN virtual.
2. Clique no **Hub** na página 'Overview'.

    ![Perfil hub 1](./media/global-hub-profile/hub1.png)
3. Clique em **VPN do utilizador (Ponto para o site)**.
4. Clique em **baixar o perfil VPN do utilizador do Hub virtual**.

   ![Perfil hub 2](./media/global-hub-profile/hub2.png)
5. Verifique os **EAPTLs**.
6. Clique em **Gerar e transferir perfil.**

   ![Perfil hub 3](./media/global-hub-profile/download.png)

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre a WAN Virtual, veja a página [Virtual WAN Overview](virtual-wan-about.md) (Descrição Geral da WAN Virtual).
