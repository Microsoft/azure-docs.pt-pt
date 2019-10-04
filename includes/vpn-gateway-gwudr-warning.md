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
ms.openlocfilehash: a852807ab685e85b76d26e5b39c99a32f645bbd7
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/03/2019
ms.locfileid: "71838168"
---
**Não há suporte para**rotas definidas pelo usuário com um destino 0.0.0.0/0 e NSGs em GatewaySubnet. Os gateways criados com essa configuração serão bloqueados da criação. Os gateways exigem acesso aos controladores de gerenciamento para funcionar corretamente.
