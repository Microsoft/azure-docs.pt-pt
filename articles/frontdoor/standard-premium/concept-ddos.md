---
title: 'Porta da frente Azure: proteção DDoS'
description: Esta página fornece informações sobre como a Azure Front Door Standard/Premium ajuda a proteger contra ataques de DDoS
services: frontdoor
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.topic: conceptual
ms.date: 02/18/2021
ms.author: jodowns
ms.openlocfilehash: 9c67944717888439c0d6bd84e1615f51dd91fcac
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "101100147"
---
# <a name="ddos-protection-on-azure-front-door-standardpremium-preview"></a>Proteção DDoS em Azure Front Door Standard/Premium (Pré-visualização)

> [!Note]
> Esta documentação destina-se ao Azure Front Door Standard/Premium (Preview). À procura de informações sobre a Porta da Frente Azure? Ver [aqui](../front-door-overview.md).

A Azure Front Door tem várias funcionalidades e características que podem ajudar a prevenir ataques de negação de serviço distribuídos (DDoS). Estas funcionalidades podem impedir que os atacantes cheguem à sua aplicação e afetem a disponibilidade e desempenho da sua aplicação.

> [!IMPORTANT]
> Azure Front Door Standard/Premium (Preview) está atualmente em pré-visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas.
> Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="integration-with-azure-ddos-protection-basic"></a>Integração com Azure DDoS Protection Basic

A porta da frente está protegida pela Azure DDoS Protection Basic. A funcionalidade encontra-se integrada na plataforma Front Door por padrão e sem custos adicionais. A escala e a capacidade total da rede implantada globalmente pela Front Door fornece defesa contra ataques comuns de camadas de rede através de monitorização de tráfego sempre on e mitigação em tempo real. A proteção básica do DDoS também se defende contra as inundações de consulta de dNS mais comuns e frequentes que ocorrem na camada 7 e ataques volutéricos da camada 3 e 4 que visam os pontos finais públicos. Este serviço também tem um histórico comprovado na proteção das empresas e dos serviços de consumo da Microsoft contra ataques em larga escala. Para mais informações, consulte [a Azure DDoS Protection](../../security/fundamentals/ddos-best-practices.md).

## <a name="protocol-blocking"></a>Bloqueio de protocolo

A Porta frontal só aceita o tráfego nos protocolos HTTP e HTTPS, e só processará pedidos válidos com um `Host` cabeçalho conhecido. Este comportamento ajuda a mitigar alguns tipos comuns de ataques DDoS, incluindo ataques volumosos que se espalham por uma série de protocolos e portas, ataques de amplificação de DNS e ataques de envenenamento por TCP.

## <a name="capacity-absorption"></a>Absorção de capacidade

Front Door é um serviço massivamente dimensionado e distribuído globalmente. Temos muitos clientes, incluindo os próprios produtos em nuvem em larga escala da Microsoft, que recebem centenas de milhares de pedidos a cada segundo. A Porta da Frente está localizada na borda da rede do Azure, absorvendo e isolando geograficamente grandes ataques de grande volume. Isto pode evitar que o tráfego malicioso vá mais longe do que a borda da rede Azure.

## <a name="caching"></a>Colocação em cache

[As capacidades de caching da Porta da Frente](concept-caching.md) podem ser usadas para proteger os backends de grandes volumes de tráfego gerados por um ataque. Os recursos em cache serão devolvidos dos nós da porta da frente para que não sejam encaminhados para o seu backend. Mesmo os tempos de validade da cache curtos (segundos ou minutos) em respostas dinâmicas podem reduzir consideravelmente a carga nos serviços de backend. Para obter mais informações sobre conceitos e padrões de cache, consulte [considerações de Cache](/azure/architecture/best-practices/caching) e [padrão cache-aside](/azure/architecture/patterns/cache-aside).

## <a name="web-application-firewall-waf"></a>Firewall de Aplicações Web (WAF)

[A Firewall de Aplicação Web (WAF) da Porta Da Frente](../../web-application-firewall/afds/afds-overview.md) pode ser usada para mitigar muitos tipos diferentes de ataques:

* A utilização do conjunto de regras geridas proporciona proteção contra muitos ataques comuns.
* O tráfego de fora de uma região geográfica definida, ou dentro de uma região definida, pode ser bloqueado ou redirecionado para uma página web estática. Para obter mais informações, consulte [Geo-filtragem.](../../web-application-firewall/afds/waf-front-door-geo-filtering.md)
* Os endereços IP e os intervalos que identifica como maliciosos podem ser bloqueados.
* A limitação da taxa pode ser aplicada para evitar que os endereços IP chamem o seu serviço com demasiada frequência.
* Pode criar [regras WAF personalizadas](../../web-application-firewall/afds/waf-front-door-custom-rules.md) para bloquear e limitar automaticamente os ataques HTTP ou HTTPS que tenham assinaturas conhecidas.

## <a name="for-further-protection"></a>Para mais proteção

Se necessitar de mais proteção, pode ativar [a Norma de Proteção Azure DDoS](../../security/fundamentals/ddos-best-practices.md#ddos-protection-standard) no VNet onde estão implantadas as suas extremidades traseiras. Os clientes DDoS Protection Standard recebem mais benefícios, incluindo:

* Proteção de custos
* Garantia SLA
* Acesso a peritos da Equipa de Resposta Rápida do DDoS para ajuda imediata durante um ataque.

## <a name="next-steps"></a>Passos seguintes

Saiba como [criar um Padrão/Premium da Porta da Frente.](create-front-door-portal.md)
