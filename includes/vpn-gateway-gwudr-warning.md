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
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "95559403"
---
As rotas definidas pelo utilizador com um destino 0.0.0.0/0 e NSGs no GatewaySubnet **não são suportadas**. Gateways criados com esta configuração serão bloqueados da criação. Gateways requerem acesso aos controladores de gestão para funcionar corretamente. [A propagação da rota BGP](../articles/virtual-network/virtual-networks-udr-overview.md#border-gateway-protocol) deve ser definida como "Ativada" na GatewaySubnet para garantir a disponibilidade do gateway. Se for definida como Desativada, o gateway não funcionará.