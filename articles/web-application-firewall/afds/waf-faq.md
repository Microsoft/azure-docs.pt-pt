---
title: Firewall de aplicação web Azure no Azure Front Door Service - perguntas frequentes
description: Este artigo fornece respostas a perguntas frequentes sobre Firewall de Aplicação Web na Porta frontal Azure
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.devlang: na
ms.topic: article
ms.date: 05/05/2020
ms.author: victorh
ms.openlocfilehash: d129c37c909c630623f8a41c06da9aa80e4e2392
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "82837639"
---
# <a name="frequently-asked-questions-for-azure-web-application-firewall-on-azure-front-door-service"></a>Perguntas frequentes para firewall de aplicação web Azure no serviço de porta frontal Azure

Este artigo responde a perguntas comuns sobre a Azure Web Application Firewall (WAF) sobre as funcionalidades e funcionalidades do Serviço de Porta Frontal Azure. 

## <a name="what-is-azure-waf"></a>O que é Azure WAF?

Azure WAF é uma firewall de aplicação web que ajuda a proteger as suas aplicações web de ameaças comuns, tais como injeção de SQL, scripts de sites cruzados e outras explorações web. Pode definir uma política da WAF que consiste numa combinação de regras personalizadas e geridas para controlar o acesso às suas aplicações web.

Uma política Azure WAF pode ser aplicada a aplicações web hospedadas em Application Gateway ou Azure Front Doors.

## <a name="what-is-waf-on-azure-front-door"></a>O que é WAF na Porta da Frente Azure? 

AZure Front Door é uma rede de aplicações e entrega de conteúdos altamente escalável e distribuída globalmente. O Azure WAF, quando integrado com a Porta Frontal, para os ataques de aplicação de negação de serviço e direcionados para a rede Azure, perto de fontes de ataque antes de entrarem na sua rede virtual, oferece proteção sem sacrificar o desempenho.

## <a name="does-azure-waf-support-https"></a>A Azure WAF suporta HTTPS?

A Porta da Frente oferece descarregamento TLS. A WAF está integrada nativamente com a Porta frontal e pode inspecionar um pedido após a sua desencriptação.

## <a name="does-azure-waf-support-ipv6"></a>A Azure WAF suporta o IPv6?

Sim. Pode configurar a restrição IP para IPv4 e IPv6.

## <a name="how-up-to-date-are-the-managed-rule-sets"></a>Até que ponto estão atualizados os conjuntos de regras geridos?

Fazemos o nosso melhor para acompanhar a mudança da paisagem de ameaças. Uma vez que uma nova regra é atualizada, é adicionada ao Conjunto de Regras Predefinidos com um novo número de versão.

## <a name="what-is-the-propagation-time-if-i-make-a-change-to-my-waf-policy"></a>Qual é o tempo de propagação se eu mudar a minha política da WAF?

A implementação de uma política da WAF globalmente geralmente demora cerca de 5 minutos e muitas vezes completa mais cedo.

## <a name="can-waf-policies-be-different-for-different-regions"></a>As políticas da WAF podem ser diferentes para diferentes regiões?

Quando integrado com porta frontal, o WAF é um recurso global. A mesma configuração aplica-se em todos os locais da Porta frontal.
 
## <a name="how-do-i-limit-access-to-my-back-end-to-be-from-front-door-only"></a>Como limito o acesso à minha parte de trás para ser apenas da Porta da Frente?

Pode configurar a Lista de Controlo de Acesso IP na sua parte traseira para permitir apenas intervalos de endereço IP de saída da Porta Frontal e negar qualquer acesso direto a partir da Internet. As etiquetas de serviço são suportadas para que possa utilizar na sua rede virtual. Além disso, pode verificar se o campo de cabeçalho HTTP do anfitrião X-Forward é válido para a sua aplicação web.

## <a name="which-azure-waf-options-should-i-choose"></a>Que opções azure WAF devo escolher?

Existem duas opções na aplicação das políticas waf em Azure. WAF com Azure Front Door é uma solução de segurança de borda distribuída globalmente. A WAF com Application Gateway é uma solução regional e dedicada. Recomendamos que escolha uma solução com base nos seus requisitos globais de desempenho e segurança. Para obter mais informações, consulte [o load-balance com a suite de entrega de aplicações da Azure.](https://docs.microsoft.com/azure/frontdoor/front-door-lb-with-azure-app-delivery-suite)


## <a name="do-you-support-same-waf-features-in-all-integrated-platforms"></a>Suporta as mesmas funcionalidades da WAF em todas as plataformas integradas?

Atualmente, as regras ModSec CRS 2.2.9, CRS 3.0 e CRS 3.1 só são suportadas com WAF no Gateway de Aplicações. As regras de definição de regras geridas por defeito de limitação de taxas são suportadas apenas com WAF na Porta frontal Azure.

## <a name="is-ddos-protection-integrated-with-front-door"></a>A proteção DDoS está integrada com a Porta da Frente? 

Distribuído globalmente nas bordas da rede Azure, a Porta Frontal Azure pode absorver e isolar geograficamente grandes ataques de grande volume. Pode criar uma política WAF personalizada para bloquear e taxar automaticamente os ataques http(s) que tenham assinaturas conhecidas. Além disso, pode ativar o DDoS Protection Standard no VNet onde as suas extremidades traseiras estão implantadas. Os clientes Azure DDoS Protection Standard recebem benefícios adicionais, incluindo proteção de custos, garantia SLA e acesso a especialistas da DDoS Rapid Response Team para ajuda imediata durante um ataque.

## <a name="why-do-additional-requests-above-the-threshold-configured-for-my-rate-limit-rule-get-passed-to-my-backend-server"></a>Porque é que os pedidos adicionais acima do limiar configurado para a minha regra de limite de taxa são passados para o meu servidor de backend?

Uma regra de limite de taxa pode limitar o tráfego anormalmente elevado a partir de qualquer endereço IP do cliente. Pode configurar um limiar no número de pedidos web permitidos a partir de um endereço IP do cliente durante um período de um minuto ou cinco minutos. Para o controlo da taxa granular, a limitação da taxa pode ser combinada com condições adicionais de correspondência, tais como correspondência de parâmetros HTTP(S). 

Os pedidos do mesmo cliente chegam frequentemente ao mesmo servidor da Porta frontal. Nesse caso, verá que pedidos adicionais acima do limiar são bloqueados imediatamente. 

No entanto, é possível que os pedidos do mesmo cliente possam chegar a um servidor front door diferente que ainda não tenha atualizado o contador de limites de taxa. Por exemplo, o cliente pode abrir uma nova ligação para cada pedido e o limiar é baixo. Neste caso, o primeiro pedido para o novo servidor front door passaria a taxa de verificação limite. Um limiar de limite de taxa é geralmente definido alto para se defender contra ataques de negação de serviço de qualquer endereço IP do cliente. Para um limiar muito baixo, você pode ver pedidos adicionais acima do limiar passar.

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre [o Azure Web Application Firewall](../overview.md).
- Saiba mais sobre [a Porta frontal Azure.](../../frontdoor/front-door-overview.md)
