---
title: Introdução ao Firewall de Aplicação Web Azure
description: Este artigo fornece uma visão geral do Azure Web Application Firewall (WAF)
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 03/18/2020
ms.author: victorh
ms.topic: overview
ms.openlocfilehash: 49024c86d09f5cdd9e8b04d5a49f60021660b0c4
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "79475792"
---
# <a name="what-is-azure-web-application-firewall"></a>O que é a Firewall de Aplicações Web do Azure?

A Firewall de Aplicações Web (WAF) fornece uma proteção centralizada das suas aplicações Web contra exploits e vulnerabilidades comuns. As aplicações web são cada vez mais direcionadas por ataques maliciosos que exploram vulnerabilidades vulgarmente conhecidas. A injeção de SQL e a scripting cross-site estão entre os ataques mais comuns.

![Visão geral da WAF](media/overview/wafoverview.png)

Prevenir tais ataques no código de aplicação é um desafio. Pode exigir uma manutenção rigorosa, remendação e monitorização em várias camadas da topologia da aplicação. Uma firewall de aplicação web centralizada ajuda a tornar a gestão de segurança muito mais simples. Um WAF também dá aos administradores de aplicações uma melhor garantia de proteção contra ameaças e intrusões.

Uma solução WAF pode reagir a uma ameaça de segurança mais rapidamente, remendando centralmente uma vulnerabilidade conhecida, em vez de garantir cada aplicação web individual.

## <a name="supported-service"></a>Serviço apoiado

A WAF pode ser implantada com o serviço Azure Application Gateway, Azure Front Door e Azure Content Delivery Network (CDN) da Microsoft. A WAF on Azure CDN está atualmente em pré-visualização pública.  A WAF tem funcionalidades personalizadas para cada serviço específico. Para obter mais informações sobre as funcionalidades DAA para cada serviço, consulte a visão geral de cada serviço.

## <a name="next-steps"></a>Passos seguintes

- Para obter mais informações sobre firewall de aplicação web no Gateway de aplicações, consulte [firewall de aplicação web no Gateway de aplicações Azure](./ag/ag-overview.md).
- Para obter mais informações sobre firewall de aplicação web no serviço de porta frontal Azure, consulte [firewall de aplicação web no serviço de porta frontal Azure](./afds/afds-overview.md).
- Para obter mais informações sobre firewall de aplicações web no serviço Azure CDN, consulte [firewall de aplicação web no Serviço Azure CDN](./cdn/cdn-overview.md)
