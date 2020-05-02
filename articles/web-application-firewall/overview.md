---
title: Introdução ao Firewall de aplicação web azure
description: Este artigo fornece uma visão geral da Firewall de Aplicação Web Azure (WAF)
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 03/18/2020
ms.author: victorh
ms.topic: overview
ms.openlocfilehash: 49024c86d09f5cdd9e8b04d5a49f60021660b0c4
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "79475792"
---
# <a name="what-is-azure-web-application-firewall"></a>O que é a Firewall de Aplicações Web do Azure?

A Firewall de Aplicações Web (WAF) fornece uma proteção centralizada das suas aplicações Web contra exploits e vulnerabilidades comuns. As aplicações web são cada vez mais alvo de ataques maliciosos que exploram vulnerabilidades vulgarmente conhecidas. A injeção SQL e a escrita cruzada estão entre os ataques mais comuns.

![Visão geral da WAF](media/overview/wafoverview.png)

Prevenir tais ataques no código de aplicação é um desafio. Pode requerer uma manutenção rigorosa, remendos e monitorização em várias camadas da topologia da aplicação. Uma firewall centralizada de aplicações web ajuda a tornar a gestão de segurança muito mais simples. Um WAF também dá aos administradores de aplicações uma melhor garantia de proteção contra ameaças e intrusões.

Uma solução WAF pode reagir a uma ameaça de segurança mais rapidamente, remendando centralmente uma vulnerabilidade conhecida, em vez de garantir cada aplicação web individual.

## <a name="supported-service"></a>Serviço apoiado

O WAF pode ser implantado com o serviço Azure Application Gateway, Azure Front Door e Azure Content Delivery Network (CDN) da Microsoft. WaF em Azure CDN está atualmente sob pré-visualização pública.  A WAF tem funcionalidades personalizadas para cada serviço específico. Para obter mais informações sobre as funcionalidades da WAF para cada serviço, consulte a visão geral de cada serviço.

## <a name="next-steps"></a>Passos seguintes

- Para obter mais informações sobre o Firewall da Aplicação Web no Gateway da Aplicação, consulte firewall [de aplicação web no Gateway de aplicações do Azure](./ag/ag-overview.md).
- Para obter mais informações sobre firewall de aplicação web no Serviço de Porta Frontal Azure, consulte firewall de [aplicação web no Serviço de Porta Frontal Azure](./afds/afds-overview.md).
- Para mais informações sobre firewall de aplicação web no serviço Azure CDN, consulte firewall de [aplicação web no serviço CDN Azure](./cdn/cdn-overview.md)
