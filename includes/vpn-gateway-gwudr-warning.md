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
ms.openlocfilehash: 0e12ad66c635632b29b70000b6e227ddcbb5357b
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/01/2020
ms.locfileid: "80501799"
---
As rotas definidas pelo utilizador com um destino 0.0.0.0/0 e NSGs na GatewaySubnet **não são suportadas**. Gateways criados com esta configuração serão bloqueados da criação. Os gateways requerem acesso aos controladores de gestão para funcionarem corretamente. [A propagação](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#border-gateway-protocol]) da rota BGP deve ser definida como "Ativada" na GatewaySubnet para garantir a disponibilidade do gateway. Se isto estiver programado para desativar, o portal não funcionará.
