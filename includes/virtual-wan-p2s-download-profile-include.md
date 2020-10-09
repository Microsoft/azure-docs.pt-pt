---
title: ficheiro de inclusão
description: ficheiro de inclusão
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 10/06/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: e1a9256cc9a015a5d8286de5e5bd7b61ed915a3b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91812750"
---
1. Na página para o seu WAN virtual, selecione **configurações VPN do utilizador**.
1. No topo da página, selecione **Download user VPN config**. Ao descarregar a configuração de nível WAN, obtém um perfil VPN de utilizador baseado no Gestor de Tráfego incorporado. Para obter mais informações sobre perfis globais ou um perfil baseado em hub, consulte [os perfis do Hub.](https://docs.microsoft.com/azure/virtual-wan/global-hub-profile) Os cenários de failover são simplificados com o perfil global.

   Se por alguma razão um hub não estiver disponível, a gestão de tráfego incorporada fornecida pelo serviço garante conectividade (através de um hub diferente) aos recursos do Azure para os utilizadores ponto-a-local. Pode sempre descarregar uma configuração VPN específica do hub navegando para o hub. Em **VPN do Utilizador (ponto a site)**, descarregue o perfil **VPN do utilizador** do hub virtual.
1. Uma vez que o ficheiro tenha terminado de criar, selecione o link para o descarregar.
1. Utilize o ficheiro de perfil para configurar os clientes VPN.
