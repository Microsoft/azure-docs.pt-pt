---
title: incluir ficheiro
description: incluir ficheiro
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 10/06/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 317d75dae83e8529bda25897b77824d8cd36e72e
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/24/2020
ms.locfileid: "95562669"
---
1. Na página para o seu WAN virtual, selecione **configurações VPN do utilizador**.
1. No topo da página, selecione **Download user VPN config**. Ao descarregar a configuração de nível WAN, obtém um perfil VPN de utilizador baseado no Gestor de Tráfego incorporado. Para obter mais informações sobre perfis globais ou um perfil baseado em hub, consulte [os perfis do Hub.](../articles/virtual-wan/global-hub-profile.md) Os cenários de failover são simplificados com o perfil global.

   Se por alguma razão um hub não estiver disponível, a gestão de tráfego incorporada fornecida pelo serviço garante conectividade (através de um hub diferente) aos recursos do Azure para os utilizadores ponto-a-local. Pode sempre descarregar uma configuração VPN específica do hub navegando para o hub. Em **VPN do Utilizador (ponto a site)**, descarregue o perfil **VPN do utilizador** do hub virtual.
1. Uma vez que o ficheiro tenha terminado de criar, selecione o link para o descarregar.
1. Utilize o ficheiro de perfil para configurar os clientes VPN.