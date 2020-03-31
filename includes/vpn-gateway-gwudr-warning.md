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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "71838168"
---
As rotas definidas pelo utilizador com um destino 0.0.0.0/0 e NSGs na GatewaySubnet **não são suportadas**. Gateways criados com esta configuração serão bloqueados da criação. Os gateways requerem acesso aos controladores de gestão para funcionarem corretamente.
