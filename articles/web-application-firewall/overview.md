---
title: Introdução ao firewall do aplicativo Web do Azure
description: Este artigo fornece uma visão geral do WAF (firewall do aplicativo Web) do Azure
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 10/04/2019
ms.author: victorh
ms.topic: overview
ms.openlocfilehash: 7b43a6bdac254493da8693b55158e15746e76dc3
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73502045"
---
# <a name="what-is-azure-web-application-firewall"></a>O que é a Firewall de Aplicações Web do Azure?

O WAF (firewall do aplicativo Web) fornece proteção centralizada de seus aplicativos Web contra explorações e vulnerabilidades comuns. Os aplicativos Web são cada vez mais direcionados a ataques mal-intencionados que exploram vulnerabilidades comumente conhecidas. A injeção de SQL e o script entre sites estão entre os ataques mais comuns.

![Visão geral do WAF](media/overview/wafoverview.png)

A prevenção de tais ataques no código do aplicativo é desafiadora. Ele pode exigir manutenção rigorosa, aplicação de patches e monitoramento em várias camadas da topologia do aplicativo. Um firewall de aplicativo Web centralizado ajuda a tornar o gerenciamento de segurança muito mais simples. Um WAF também oferece aos administradores de aplicativos melhor garantia de proteção contra ameaças e invasões.

Uma solução WAF pode reagir a uma ameaça de segurança mais rapidamente, corrigindo centralmente uma vulnerabilidade conhecida, em vez de proteger cada aplicativo Web individual.

O WAF pode ser implantado com Aplicativo Azure gateway e o serviço de porta frontal do Azure. Atualmente, o WAF tem recursos que são personalizados para cada serviço específico. Para obter mais informações sobre os recursos do WAF para cada serviço, consulte a visão geral de cada serviço.

## <a name="next-steps"></a>Passos seguintes

- Para obter mais informações sobre o Firewall do aplicativo Web no gateway de aplicativo, consulte [Firewall do aplicativo Web no gateway de aplicativo Azure](./ag/ag-overview.md).
- Para obter mais informações sobre o Firewall do aplicativo Web no serviço de porta de recepção do Azure, consulte [Firewall do aplicativo Web no serviço de porta frontal do Azure](./afds/afds-overview.md).
