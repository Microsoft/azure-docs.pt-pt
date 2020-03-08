---
title: Introdução ao Firewall de aplicação web azure
description: Este artigo fornece uma visão geral da Firewall de Aplicação Web Azure (WAF)
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 03/06/2020
ms.author: victorh
ms.topic: overview
ms.openlocfilehash: 44bc8db5d8ada0378f8f9d0911ed398ba491d289
ms.sourcegitcommit: f5e4d0466b417fa511b942fd3bd206aeae0055bc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2020
ms.locfileid: "78851183"
---
# <a name="what-is-azure-web-application-firewall"></a>O que é a Firewall de Aplicações Web do Azure?

A Firewall de Aplicações Web (WAF) fornece uma proteção centralizada das suas aplicações Web contra exploits e vulnerabilidades comuns. As aplicações web são cada vez mais alvo de ataques maliciosos que exploram vulnerabilidades vulgarmente conhecidas. A injeção SQL e a escrita cruzada estão entre os ataques mais comuns.

![Visão geral da WAF](media/overview/wafoverview.png)

Prevenir tais ataques no código de aplicação é um desafio. Pode requerer uma manutenção rigorosa, remendos e monitorização em várias camadas da topologia da aplicação. Uma firewall centralizada de aplicações web ajuda a tornar a gestão de segurança muito mais simples. Um WAF também dá aos administradores de aplicações uma melhor garantia de proteção contra ameaças e intrusões.

Uma solução WAF pode reagir a uma ameaça de segurança mais rapidamente, remendando centralmente uma vulnerabilidade conhecida, em vez de garantir cada aplicação web individual.

## <a name="supported-services"></a>Serviços suportados

O WAF pode ser implantado com o Portal de [Aplicações Azure](../application-gateway/overview.md) e o [Serviço de Porta Frontal Azure.](../frontdoor/front-door-overview.md) Ambos os serviços são equilibradores de carga Layer-7 (HTTP/S), mas application Gateway é um serviço regional e front door é um serviço global. A WAF tem funcionalidades personalizadas para cada serviço específico.

Para mais informações, consulte a visão geral da WAF para cada serviço.

## <a name="next-steps"></a>Passos seguintes

- Para obter mais informações sobre o Firewall da Aplicação Web no Gateway da Aplicação, consulte firewall [de aplicação web no Gateway de aplicações do Azure](./ag/ag-overview.md).
- Para obter mais informações sobre firewall de aplicação web no Serviço de Porta Frontal Azure, consulte firewall de [aplicação web no Serviço de Porta Frontal Azure](./afds/afds-overview.md).
