---
title: incluir ficheiro
description: incluir ficheiro
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 02/05/2021
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: eea8cb61ce98b2394abff6995e5cc89f00a7cf46
ms.sourcegitcommit: 59cfed657839f41c36ccdf7dc2bee4535c920dd4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/06/2021
ms.locfileid: "99628896"
---
1. Na página para o seu WAN virtual, selecione **configurações VPN do utilizador**.
1. Na página de **configurações VPN do utilizador,** selecione uma configuração e, em seguida, selecione **Descarregue o perfil VPN do utilizador virtual WAN**. Ao descarregar a configuração de nível WAN, obtém um perfil VPN de utilizador baseado no Gestor de Tráfego incorporado. Para obter mais informações sobre perfis globais ou um perfil baseado em hub, consulte [os perfis do Hub.](../articles/virtual-wan/global-hub-profile.md) Os cenários de failover são simplificados com o perfil global.

   
   Se por alguma razão um hub não estiver disponível, a gestão de tráfego incorporada fornecida pelo serviço garante conectividade (através de um hub diferente) aos recursos do Azure para os utilizadores ponto-a-local. Pode sempre descarregar uma configuração VPN específica do hub navegando para o hub. Em **VPN do Utilizador (ponto a site)**, descarregue o perfil **VPN do utilizador** do hub virtual.
1. Na página **de perfil do utilizador VPN do utilizador virtual Download,** selecione o tipo de **autenticação** e, em seguida, selecione **Gerar e descarregar o perfil**. O pacote de perfil gerará e um ficheiro zip contendo as definições de configuração será descarregado.
