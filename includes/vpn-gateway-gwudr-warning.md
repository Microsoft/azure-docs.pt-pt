---
title: incluir ficheiro
description: incluir ficheiro
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 06/04/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 43e381bd26dadbea5ef5bfb002e51465e180a097
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66159351"
---
Não associe uma tabela de rota que inclui uma rota com um destino 0.0.0.0/0 à sub-rede de gateway. Isso impede que o gateway está a funcionar corretamente.