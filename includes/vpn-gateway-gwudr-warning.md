---
title: ficheiro de inclusão
description: ficheiro de inclusão
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 09/28/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 97fde67c3ac7649418ed0239a2c7aa4f1a4b3f96
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "81275277"
---
As rotas definidas pelo utilizador com um destino 0.0.0.0/0 e NSGs no GatewaySubnet **não são suportadas**. Gateways criados com esta configuração serão bloqueados da criação. Gateways requerem acesso aos controladores de gestão para funcionar corretamente. [A propagação da rota BGP](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#border-gateway-protocol) deve ser definida como "Ativada" na GatewaySubnet para garantir a disponibilidade do gateway. Se isto estiver definido para desativar, o gateway não funcionará.
