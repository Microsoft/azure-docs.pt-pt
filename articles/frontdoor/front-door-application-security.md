---
title: Porta frontal Azure - segurança da camada de aplicação | Microsoft Docs
description: Este artigo ajuda-o a entender como a Porta Frontal Azure permite proteger e proteger os backends da sua aplicação
services: frontdoor
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/10/2018
ms.author: duau
ms.openlocfilehash: 4ee50b4c7da27df3630c1b4d263f076da44189bc
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "89399944"
---
# <a name="application-layer-security-with-front-door"></a>Segurança da camada de aplicação com porta da frente
O Azure Front Door fornece capacidade de proteção de aplicações web para proteger as suas aplicações web de ataques de rede e explorações de vulnerabilidades web comuns como Aqual ou Cross Site Scripting (XSS). Ativado para as extremidades dianteiras http(s), a segurança da camada de aplicação da Front Door é distribuída globalmente e sempre em frente, impedindo ataques maliciosos na borda da rede do Azure, longe dos seus backends. Com uma otimização adicional de segurança e desempenho, a Porta Frontal oferece experiências web rápidas e seguras aos seus utilizadores finais.

## <a name="application-protection"></a>Proteção de aplicações
A proteção de aplicações da Porta Frontal está configurada em cada ambiente de borda em todo o mundo, em linha com aplicações, e bloqueia automaticamente o tráfego não http(s) de chegar às suas aplicações web. A nossa arquitetura distribuída multi-arrendatário permite a proteção global em escala sem sacrificar o desempenho. Para cargas de trabalho http(s), o serviço de proteção de aplicações web da Front Door fornece um motor de regras ricos para regras personalizadas, regras pré-configuradas contra ataques comuns e registos detalhados para todos os pedidos que correspondam a uma regra. As ações flexíveis, incluindo permitir, bloquear ou registar apenas são suportadas.

## <a name="custom-access-control-rules"></a>Regras de controlo de acesso personalizado
- **Lista de autorizações IP e lista de blocos:** Pode configurar regras personalizadas para controlar o acesso às suas aplicações web com base na lista de endereços IP do cliente. Tanto IP v4 como IP v6 são suportados
- **Controlo de acesso baseado em geográfico:** Você pode configurar regras personalizadas para controlar o acesso às suas aplicações web com base no código de país de um ip do cliente é de
- **Filtragem de parâmetros HTTP:** Pode configurar regras de acesso personalizado com base em parâmetros de pedido de http(s) correspondentes, incluindo cabeçalhos, URL e cadeias de consulta

## <a name="azure-managed-rules"></a>Regras geridas pelo Azure
- Um conjunto pré-configurado de regras contra vulnerabilidades oWASP comuns é ativado por padrão. Na pré-visualização, o conjunto de regras inclui pedidos de sqli e xss. Serão adicionadas regras adicionais. Pode optar por começar com apenas ação de log para validar regras pré-configuradas funcionam como esperado para as suas aplicações 

## <a name="rate-limiting"></a>Rate limiting (Limitação de taxa)
- Uma regra de controlo de tarifas é limitar o tráfego anormal de qualquer IP do cliente.  Pode definir um limiar no número de pedidos web permitidos por um IP do cliente durante um minuto de duração.

## <a name="centralized-protection-policy"></a>Política de proteção centralizada
- Pode definir várias regras de proteção e adicioná-las a uma Política por ordem prioritária. As regras personalizadas têm maior prioridade do que as regras geridas para permitir exceções. Uma única política está associada à sua aplicação web.  A mesma política de proteção de aplicações web é replicada em todos os servidores de borda em todos os locais, garantir uma política de segurança consistente em todas as regiões

## <a name="configuration"></a>Configuração
- Durante a pré-visualização, poderá utilizar APIs de REST, PowerShell ou CLI para criar e implementar as regras e políticas de proteção de aplicações da Porta Frontal. O acesso ao portal será suportado antes de o serviço estar geralmente disponível. 


## <a name="monitoring"></a>Monitorização
A Porta Frontal fornece a capacidade de monitorizar aplicações web contra ataques usando métricas em tempo real que são integradas com o Azure Monitor para rastrear alertas e monitorizar facilmente as tendências.

## <a name="pricing"></a>Preços
A segurança da camada de aplicação da porta da frente é gratuita durante a pré-visualização.


## <a name="next-steps"></a>Passos seguintes

- Saiba como [criar um Front Door](quickstart-create-front-door.md).
- Saiba [como funciona o Front Door](front-door-routing-architecture.md).
