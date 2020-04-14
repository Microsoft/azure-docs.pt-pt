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
ms.openlocfilehash: 97fde67c3ac7649418ed0239a2c7aa4f1a4b3f96
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/13/2020
ms.locfileid: "81275277"
---
As rotas definidas pelo utilizador com um destino 0.0.0.0/0 e NSGs na GatewaySubnet **não são suportadas**. Gateways criados com esta configuração serão bloqueados da criação. Os gateways requerem acesso aos controladores de gestão para funcionarem corretamente. [A propagação](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#border-gateway-protocol) da rota BGP deve ser definida como "Ativada" na GatewaySubnet para garantir a disponibilidade do gateway. Se isto estiver programado para desativar, o portal não funcionará.
