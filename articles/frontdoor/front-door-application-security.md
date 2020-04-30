---
title: Porta da Frente Azure - Segurança da camada de aplicação / Microsoft Docs
description: Este artigo ajuda-o a entender como a Porta Frontal Azure permite proteger e proteger os backends da sua aplicação
services: frontdoor
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/10/2018
ms.author: sharadag
ms.openlocfilehash: e458926930c1b95d48886559551878fc6c9d0673
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79471800"
---
# <a name="application-layer-security-with-front-door"></a>Segurança da camada de aplicação com porta da frente
O Azure Front Door fornece capacidade de proteção de aplicações web para proteger as suas aplicações web de ataques de rede e vulnerabilidades comuns da web explorações como A Injeção SQL ou Scripting Cross Site (XSS). Habilitada para http(s) front-ends, a segurança da camada de aplicação da Front Door é distribuída globalmente e sempre em, impedindo ataques maliciosos na borda da rede do Azure, longe dos seus backends. Com uma maior otimização de segurança e desempenho, a Porta Frontal oferece experiências web rápidas e seguras aos seus utilizadores finais.

## <a name="application-protection"></a>Proteção de aplicações
A proteção de aplicações da Front Door está configurada em cada ambiente de borda em todo o mundo, em consonância com as aplicações, e bloqueia automaticamente o tráfego não-http(s) de chegar às suas aplicações web. A nossa arquitetura distribuída multi-inquilino permite a proteção global em escala sem sacrificar o desempenho. Para http(s) cargas de trabalho, o serviço de proteção de aplicações web da Front Door fornece um motor de regras ricas para regras personalizadas, regras pré-configuradas contra ataques comuns, e registo detalhado para todos os pedidos que correspondam a uma regra. As ações flexíveis, incluindo permitir, bloquear ou registar apenas, são suportadas.

## <a name="custom-access-control-rules"></a>Regras de controlo de acesso personalizado
- **IP permitir lista e lista de blocos:** Pode configurar regras personalizadas para controlar o acesso às suas aplicações web com base na lista de endereços IP do cliente. Tanto ip v4 como IP v6 são suportados
- **Controlo de acesso baseado em geográficos:** Pode configurar regras personalizadas para controlar o acesso às suas aplicações web com base no código do país de que um cliente IP é
- **Filtragem de parâmetros HTTP:** Pode configurar regras de acesso personalizadas com base em parâmetros de pedido correspondentes http(s), incluindo cabeçalhos, URL e cordas de consulta

## <a name="azure-managed-rules"></a>Regras geridas pelo Azure
- Um conjunto de regras reconfiguradas contra vulnerabilidades owasp de topo comum é ativado por padrão. Na pré-visualização, o conjunto de regras inclui verificação de pedidos de sqli e xss. Serão adicionadas regras adicionais. Pode optar por iniciar apenas a ação de log para validar regras pré-configuradas funciona como esperado para as suas aplicações 

## <a name="rate-limiting"></a>Rate limiting (Limitação de taxa)
- Uma regra de controlo de tarifas é limitar o tráfego anormal de qualquer CLIENTE IP.  Pode estabelecer um limiar no número de pedidos web permitidos por um IP cliente durante um minuto de duração.

## <a name="centralized-protection-policy"></a>Política centralizada de proteção
- Pode definir várias regras de proteção e adicioná-las a uma Política em ordem prioritária. As regras personalizadas têm uma prioridade maior do que as regras geridas para permitir exceções. Uma única política está associada à sua aplicação web.  A mesma política de proteção de aplicações web é replicada para todos os servidores de borda em todos os locais, garantir uma política de segurança consistente em todas as regiões

## <a name="configuration"></a>Configuração
- Durante a pré-visualização, pode utilizar APIs REST, PowerShell ou CLI para criar e implementar as regras e políticas de proteção de aplicações da Porta Frontal. O acesso ao portal será suportado antes de o serviço estar geralmente disponível. 


## <a name="monitoring"></a>Monitorização
A Porta Frontal fornece a capacidade de monitorizar aplicações web contra ataques usando métricas em tempo real que estão integradas com o Azure Monitor para rastrear alertas e monitorizar facilmente as tendências.

## <a name="pricing"></a>Preços
A segurança da camada de aplicação da Porta Frontal é gratuita durante a pré-visualização.


## <a name="next-steps"></a>Passos seguintes

- Saiba como [criar um Front Door](quickstart-create-front-door.md).
- Saiba [como funciona o Front Door](front-door-routing-architecture.md).
