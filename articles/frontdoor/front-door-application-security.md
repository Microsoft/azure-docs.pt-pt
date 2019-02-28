---
title: Serviço de porta de entrada do Azure - segurança de camada de aplicativos | Documentos da Microsoft
description: Este artigo ajuda-o a compreender como o serviço de porta de entrada do Azure permite proteger e proteger o seu back-ends de aplicação
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
ms.openlocfilehash: c7b99548e2fe1ad0c1cab39953e28a97e7ebff4b
ms.sourcegitcommit: 1afd2e835dd507259cf7bb798b1b130adbb21840
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/28/2019
ms.locfileid: "56984405"
---
# <a name="application-layer-security-with-front-door"></a>Segurança de camada de aplicação com a porta de entrada
Serviço de porta de entrada do Azure fornece a capacidade de proteção de aplicações web para salvaguardar os seus aplicativos web de ataques de rede e explorações de vulnerabilidades web comuns, como Injeção de SQL ou Cross Site XSS (script). Ativada para http (s) front-ends, segurança de camada de aplicativos da porta de entrada é distribuída globalmente e sempre, ataques maliciosos de parar na rede do Azure de borda, longe do seu back-ends. Com maior segurança e a otimização de desempenho, desde início rápido fornece e experiências na web segura aos seus utilizadores finais.

## <a name="application-protection"></a>Proteção de aplicações
Proteção de aplicações da porta de entrada está configurada em cada ambiente de borda em todo o mundo, de acordo com as aplicações e bloqueia automaticamente o não-tráfego HTTP (s) de chegar aos seus aplicativos web. Nossa arquitetura distribuída do multi-inquilino permite proteção global em escala, sem sacrificar o desempenho. Para cargas de trabalho do HTTP (s), serviço de proteção de aplicações da porta de entrada web fornece um mecanismo de regras avançadas para regras personalizadas, o conjunto de regras pré-configuradas contra ataques comuns, e detalhadas para todos os pedidos de registo que corresponde a uma regra. Ações flexíveis, incluindo permitir, bloquear ou registo apenas são suportadas.

## <a name="custom-access-control-rules"></a>Regras de controlo de acesso personalizado
- **As permissões de IP lista e a lista de bloqueios:** Só pode configurar regras personalizadas para controlar o acesso às suas aplicações web com base na lista de endereços IP do cliente. Endereço IP v4 e IP v6 são suportados
- **Controlo de acesso baseado em geográfica:** Pode configurar regras personalizadas para controlar o acesso às suas aplicações web com base num IP de cliente é a partir de código de país
- **Parâmetros HTTP de filtragem:** Pode configurar regras de acesso personalizado com base na correspondência de parâmetros de pedido de HTTP (s) incluindo cabeçalhos, URL e cadeias de consulta

## <a name="azure-managed-rules"></a>Regras de geridos pelo Azure
- Um conjunto pré-configuradas de regras em relação a vulnerabilidades mais comuns da OWASP superior está ativado por predefinição. Na pré-visualização, o conjunto de regras inclui pedidos sqli e xss, a verificação. Regras adicionais serão adicionadas. Pode optar por começar com uma única ação de registo para validar o trabalho de regras pré-configuradas conforme esperado para as suas aplicações 

## <a name="rate-limiting"></a>Limitação de velocidade
- Uma regra de controlo de taxa é limitar o tráfego elevado anormal de qualquer IP de cliente.  Pode definir um limite no número de solicitações da web permitido por um IP de cliente durante um período de um minuto.

## <a name="centralized-protection-policy"></a>Política de proteção centralizada
- Pode definir várias regras de proteção e adicioná-los a uma política de ordem de prioridade. Regras personalizadas têm maior prioridade que ruleset gerida para permitir exceções. Uma única política está associada à sua aplicação web.  Mesma política de proteção de aplicações web é replicada para todos os servidores de borda em todos os locais, certifique-se a política de segurança consistente em todas as regiões

## <a name="configuration"></a>Configuração
- Durante a pré-visualização, pode utilizar as APIs REST, PowerShell ou CLI para criar e implementar políticas e regras de proteção de aplicações da porta de entrada. Acesso ao portal será suportado antes do serviço está disponível em geral. 


## <a name="monitoring"></a>Monitorização
Porta de entrada fornece a capacidade de monitorizar as aplicações web contra ataques através de métricas em tempo real que estão integradas com o Azure Monitor para acompanhar os alertas e monitorizar tendências facilmente.

## <a name="pricing"></a>Preços
Segurança de camada de aplicativos da porta de entrada é gratuita durante a pré-visualização.


## <a name="next-steps"></a>Passos Seguintes

- Saiba como [criar um Front Door](quickstart-create-front-door.md).
- Saiba [como funciona o Front Door](front-door-routing-architecture.md).
