---
title: incluir ficheiro
description: incluir ficheiro
services: vpn-gateway
author: WenJason
ms.service: vpn-gateway
ms.topic: include
origin.date: 06/04/2018
ms.date: 12/24/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 43e381bd26dadbea5ef5bfb002e51465e180a097
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "64859117"
---
Não associe uma tabela de rota que inclui uma rota com um destino 0.0.0.0/0 à sub-rede de gateway. Isso impede que o gateway está a funcionar corretamente.
