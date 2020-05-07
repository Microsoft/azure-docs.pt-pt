---
title: Firewall de aplicação web Azure no Serviço de Porta Frontal Azure - perguntas frequentes
description: Este artigo fornece respostas a perguntas frequentes sobre firewall de aplicação web na porta da frente de Azure
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.devlang: na
ms.topic: article
ms.date: 05/05/2020
ms.author: victorh
ms.openlocfilehash: d129c37c909c630623f8a41c06da9aa80e4e2392
ms.sourcegitcommit: 11572a869ef8dbec8e7c721bc7744e2859b79962
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/05/2020
ms.locfileid: "82837639"
---
# <a name="frequently-asked-questions-for-azure-web-application-firewall-on-azure-front-door-service"></a>Perguntas frequentes para firewall de aplicação web Azure no serviço de porta frontal Azure

Este artigo responde a perguntas comuns sobre o Azure Web Application Firewall (WAF) sobre as funcionalidades e funcionalidades do Serviço de Porta Frontal Do Azure. 

## <a name="what-is-azure-waf"></a>O que é Azure WAF?

O Azure WAF é uma firewall de aplicação web que ajuda a proteger as suas aplicações web de ameaças comuns, tais como injeção de SQL, scripts transporitais e outras explorações web. Pode definir uma política waf que consiste numa combinação de regras personalizadas e geridas para controlar o acesso às suas aplicações web.

Uma política azure WAF pode ser aplicada a aplicações web hospedadas em Application Gateway ou Azure Front Doors.

## <a name="what-is-waf-on-azure-front-door"></a>O que é WAF na porta da frente de Azure? 

A Porta Frontal Azure é uma rede de aplicação e entrega de conteúdos altamente escalável e distribuída globalmente. O Azure WAF, quando integrado com a Porta Da Frente, para os ataques de negação de serviço e aplicações direcionadas na borda da rede Azure, perto de fontes de ataque antes de entrarem na sua rede virtual, oferece proteção sem sacrificar o desempenho.

## <a name="does-azure-waf-support-https"></a>A Azure WAF suporta HTTPS?

A Porta da Frente oferece descarga stLS. A WAF está integrada de forma nativa com a Porta da Frente e pode inspecionar um pedido depois de ser desencriptado.

## <a name="does-azure-waf-support-ipv6"></a>A Azure WAF suporta o IPv6?

Sim. Pode configurar a restrição IP para IPv4 e IPv6.

## <a name="how-up-to-date-are-the-managed-rule-sets"></a>Quão atualizadas são as regras geridas?

Fazemos o nosso melhor para acompanhar a mudança da paisagem de ameaças. Uma vez atualizada uma nova regra, é adicionada ao Conjunto de Regras Padrão com um novo número de versão.

## <a name="what-is-the-propagation-time-if-i-make-a-change-to-my-waf-policy"></a>Qual é o tempo de propagação se eu fizer uma alteração na minha política de WAF?

A implementação de uma política de WAF globalmente geralmente leva cerca de 5 minutos e muitas vezes completa mais cedo.

## <a name="can-waf-policies-be-different-for-different-regions"></a>As políticas de WAF podem ser diferentes para diferentes regiões?

Quando integrado com a Porta da Frente, a WAF é um recurso global. A mesma configuração aplica-se em todos os locais da Porta Da Frente.
 
## <a name="how-do-i-limit-access-to-my-back-end-to-be-from-front-door-only"></a>Como posso limitar o acesso ao meu back-end para ser apenas da Porta da Frente?

Pode configurar a Lista de Controlo de Acesso IP na parte de trás para permitir apenas intervalos de endereçoip de saída da Porta Frontal e negar qualquer acesso direto a partir da Internet. As etiquetas de serviço são suportadas para que possa utilizar na sua rede virtual. Além disso, pode verificar se o campo de cabeçalho si-forward-host HTTP é válido para a sua aplicação web.

## <a name="which-azure-waf-options-should-i-choose"></a>Que opções azure WAF devo escolher?

Existem duas opções na aplicação das políticas waf em Azure. WAF com Porta Frontal Azure é uma solução de segurança de borda distribuída globalmente. WaF com Application Gateway é uma solução regional e dedicada. Recomendamos que escolha uma solução com base nos seus requisitos globais de desempenho e segurança. Para mais informações, consulte o equilíbrio de carga com a [suíte de entrega de aplicações do Azure.](https://docs.microsoft.com/azure/frontdoor/front-door-lb-with-azure-app-delivery-suite)


## <a name="do-you-support-same-waf-features-in-all-integrated-platforms"></a>Suporta as mesmas funcionalidades WAF em todas as plataformas integradas?

Atualmente, modSec CRS 2.2.9, CRS 3.0 e CRS 3.1 são apenas suportados com WAF no Gateway de Aplicação. As regras de definição de regras de definição de regras de definição de regras de definição de regras de padrão geridas pelo Azure são suportadas apenas com WAF na Porta frontal azure.

## <a name="is-ddos-protection-integrated-with-front-door"></a>A proteção DDoS está integrada com a Porta da Frente? 

Distribuído globalmente nas bordas da rede Azure, a Porta Frontal Azure pode absorver e isolar geograficamente ataques de grande volume. Pode criar uma política de WAF personalizada para bloquear automaticamente e limitar os ataques http(s) que tenham conhecido as assinaturas. Além disso, pode ativar o Padrão de Proteção DDoS no VNet onde as suas extremidades traseiras estão implantadas. Os clientes da Azure DDoS Protection Standard recebem benefícios adicionais, incluindo proteção de custos, garantia SLA e acesso a especialistas da DDoS Rapid Response Team para ajuda imediata durante um ataque.

## <a name="why-do-additional-requests-above-the-threshold-configured-for-my-rate-limit-rule-get-passed-to-my-backend-server"></a>Porque é que pedidos adicionais acima do limiar configurado para a minha regra limite de tarifas são passados para o meu servidor de backend?

Uma regra limite de tarifa pode limitar o tráfego anormalmente elevado de qualquer endereço IP do cliente. Pode configurar um limiar sobre o número de pedidos web permitidos a partir de um endereço IP do cliente durante um minuto ou cinco minutos de duração. Para o controlo da taxa granular, a limitação da taxa pode ser combinada com condições adicionais de correspondência, tais como a correspondência do parâmetro HTTP(S). 

Os pedidos do mesmo cliente chegam frequentemente ao mesmo servidor da Porta Da Frente. Nesse caso, verá pedidos adicionais acima do limiar serem bloqueados imediatamente. 

No entanto, é possível que os pedidos do mesmo cliente possam chegar a um servidor da Porta Frontal diferente que ainda não tenha atualizado o contador de limites de tarifas. Por exemplo, o cliente pode abrir uma nova ligação para cada pedido e o limiar é baixo. Neste caso, o primeiro pedido para o novo servidor front door passaria a verificação do limite de tarifa. Um limiar de limite de taxa é geralmente estabelecido alto para se defender contra a negação de ataques de serviço de qualquer endereço IP do cliente. Para um limiar muito baixo, pode ver pedidos adicionais acima do limiar passarem.

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre o Firewall de [Aplicação Web Azure](../overview.md).
- Saiba mais sobre [a Porta da Frente Azure.](../../frontdoor/front-door-overview.md)
