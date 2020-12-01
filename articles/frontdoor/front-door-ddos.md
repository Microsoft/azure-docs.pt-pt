---
title: Porta frontal Azure - Proteção DDoS
description: Esta página fornece informações sobre como a Porta frontal Azure ajuda a proteger contra ataques de DDoS
services: frontdoor
documentationcenter: ''
author: johndowns
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/28/2020
ms.author: jodowns
ms.openlocfilehash: 24a8a559d72eae4dab0b6e740b34890f1d7ff114
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/30/2020
ms.locfileid: "96350788"
---
# <a name="ddos-protection-on-front-door"></a>Proteção DDoS na porta da frente

A Azure Front Door tem várias funcionalidades e características que podem ajudar a prevenir ataques de negação de serviço distribuídos (DDoS). Estas funcionalidades podem impedir que os atacantes cheguem à sua aplicação e afetem a disponibilidade e desempenho da sua aplicação.

## <a name="integration-with-azure-ddos-protection-basic"></a>Integração com Azure DDoS Protection Basic

A porta da frente está protegida pela Azure DDoS Protection Basic. Está integrado na plataforma Front Door por padrão e sem custos adicionais. A escala e a capacidade total da rede implantada globalmente pela Front Door fornece defesa contra ataques comuns de camadas de rede através de monitorização de tráfego sempre on e mitigação em tempo real. A proteção básica do DDoS também se defende contra as inundações de consulta de dNS mais comuns e frequentes que ocorrem na camada 7 e ataques volutéricos da camada 3 e 4 que visam os pontos finais públicos. Este serviço também tem um histórico comprovado na proteção das empresas e dos serviços de consumo da Microsoft contra ataques em larga escala. Para mais informações, consulte [a Azure DDoS Protection](../security/fundamentals/ddos-best-practices.md).

## <a name="protocol-blocking"></a>Bloqueio de protocolo

A Porta frontal só aceita o tráfego nos protocolos HTTP e HTTPS, e só processará pedidos válidos com um `Host` cabeçalho conhecido. Este comportamento ajuda a mitigar alguns tipos comuns de ataques DDoS, incluindo ataques volumosos que se espalham por uma série de protocolos e portas, ataques de amplificação de DNS e ataques de envenenamento por TCP.

## <a name="capacity-absorption"></a>Absorção de capacidade

Front Door é um serviço massivamente dimensionado e distribuído globalmente. Temos muitos clientes, incluindo os próprios produtos em nuvem em larga escala da Microsoft, que recebem centenas de milhares de pedidos a cada segundo. A Porta frontal está localizada na borda da rede do Azure, absorvendo e isolando geograficamente grandes ataques de grande volume. Isto pode evitar que o tráfego malicioso vá mais longe do que a borda da rede Azure.

## <a name="caching"></a>Colocação em cache

[As capacidades de caching da Porta da Frente](./front-door-caching.md) podem ser usadas para proteger os backends de grandes volumes de tráfego gerados por um ataque. Os recursos em cache serão devolvidos dos nós da porta da frente para que não sejam encaminhados para o seu backend. Mesmo os tempos de validade da cache curtos (segundos ou minutos) em respostas dinâmicas podem reduzir consideravelmente a carga nos serviços de backend. Para obter mais informações sobre conceitos e padrões de cache, consulte [considerações de Cache](/azure/architecture/best-practices/caching) e [padrão cache-aside](/azure/architecture/patterns/cache-aside).

## <a name="web-application-firewall-waf"></a>Firewall de Aplicações Web (WAF)

[A Firewall de Aplicação Web (WAF) da Porta Frontal](../web-application-firewall/afds/afds-overview.md) pode ser usada para mitigar vários tipos de ataques:

* A utilização do conjunto de regras geridas proporciona proteção contra uma série de ataques comuns.
* O tráfego de fora de uma região geográfica definida, ou dentro de uma região definida, pode ser bloqueado ou redirecionado para uma página web estática. Para obter mais informações, consulte [Geo-filtragem.](../web-application-firewall/afds/waf-front-door-geo-filtering.md)
* Os endereços IP e os intervalos que identifica como maliciosos podem ser bloqueados.
* A limitação da taxa pode ser aplicada para evitar que os endereços IP chamem o seu serviço com demasiada frequência.
* Pode criar [regras WAF personalizadas](../web-application-firewall/afds/waf-front-door-custom-rules.md) para bloquear e limitar automaticamente os ataques HTTP ou HTTPS que tenham assinaturas conhecidas.

## <a name="for-further-protection"></a>Para mais proteção

Se necessitar de mais proteção, pode ativar [a Norma de Proteção Azure DDoS](../security/fundamentals/ddos-best-practices.md#ddos-protection-standard) no VNet onde estão implantadas as suas extremidades traseiras. Os clientes DDoS Protection Standard recebem benefícios adicionais, incluindo proteção de custos, garantia SLA e acesso a especialistas da Equipa de Resposta Rápida DDoS para ajuda imediata durante um ataque.

## <a name="next-steps"></a>Passos seguintes

- Saiba como configurar um [perfil WAF na Porta da Frente](front-door-waf.md). 
- Saiba como [criar um Front Door](quickstart-create-front-door.md).
- Saiba [como funciona o Front Door](front-door-routing-architecture.md).