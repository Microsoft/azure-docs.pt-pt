---
title: incluir ficheiro
description: incluir ficheiro
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 09/28/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: cf9d4c3fd96df83361e7d9aa89ba702d37265ec6
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/24/2020
ms.locfileid: "95559403"
---
As rotas definidas pelo utilizador com um destino 0.0.0.0/0 e NSGs no GatewaySubnet **não são suportadas**. Gateways criados com esta configuração serão bloqueados da criação. Gateways requerem acesso aos controladores de gestão para funcionar corretamente. [A propagação da rota BGP](../articles/virtual-network/virtual-networks-udr-overview.md#border-gateway-protocol) deve ser definida como "Ativada" na GatewaySubnet para garantir a disponibilidade do gateway. Se isto estiver definido para desativar, o gateway não funcionará.