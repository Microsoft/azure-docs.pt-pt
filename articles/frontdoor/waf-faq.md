---
title: Firewall de aplicações web do Azure - perguntas mais frequentes
description: Esta página fornece respostas às perguntas mais frequentes sobre o serviço de porta de entrada do Azure
services: frontdoor
documentationcenter: ''
author: KumudD
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/10/2019
ms.author: kumud;tyao
ms.openlocfilehash: 91ee8b6e614cf12deccd1429ad67323c277954a0
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67061493"
---
# <a name="frequently-asked-questions-for-azure-web-application-firewall"></a>Perguntas mais frequentes sobre a firewall de aplicações web do Azure

Este artigo responde a perguntas comuns sobre a funcionalidade e recursos de firewall (WAF) de aplicativos web do Azure. 

## <a name="what-is-azure-waf"></a>O que é o WAF do Azure?

WAF do Azure é um firewall de aplicações web que o ajuda a proteger seus aplicativos web de ameaças comuns, como SQL injeção, scripts entre sites e outros exploradores de web. Pode definir uma política de WAF consistindo numa combinação de regras personalizadas e geridas para controlar o acesso às suas aplicações web.

Pode ser aplicada uma política de WAF do Azure para aplicações web alojadas nos serviços de Gateway de aplicação ou a porta de entrada do Azure.

## <a name="what-is-waf-for-azure-front-door-service"></a>O que é o WAF desde início do serviço do Azure? 

Porta de entrada do Azure é uma aplicação altamente dimensionável e globalmente distribuída e a rede de entrega de conteúdos. WAF do Azure, quando integrada com a porta da frente, deixa de negação de serviço e direcionada a ataques de aplicativo na borda da rede do Azure, próximo de origens de ataque antes de entrarem em sua rede virtual, oferece proteção sem sacrificar o desempenho.

## <a name="does-azure-waf-support-https"></a>O WAF do Azure suporta HTTPS?

Serviço de porta de entrada oferece a descarga de SSL. WAF nativamente está integrada com a porta de entrada e pode inspecionar um pedido depois de estes são desencriptados.

## <a name="does-azure-waf-support-ipv6"></a>O WAF do Azure suporta IPv6?

Sim. Pode configurar restrições de IP para IPv4 e IPv6.

## <a name="how-up-to-date-are-the-managed-rule-sets"></a>Grau de atualização são os conjuntos de regras gerido?

Façamos o melhor para se manter atualizado com a alteração do Panorama de ameaças. Depois de uma nova regra é atualizada, ele é adicionado à regra conjunto predefinidos com um novo número de versão.

## <a name="what-is-the-propagation-time-if-i-make-a-change-to-my-waf-policy"></a>O que é o tempo de propagação, se eu fizer uma alteração à minha diretiva de WAF?

Implementar uma política de WAF globalmente normalmente demora cerca de 5 minutos e, muitas vezes, é concluído mais cedo.

## <a name="can-waf-policies-be-different-for-different-regions"></a>Políticas de WAF podem ser diferentes para diferentes regiões?

Quando integrado com o serviço de porta de entrada, o WAF é um recurso global. Mesma configuração aplica-se em todas as localizações de porta de entrada.
 
## <a name="how-do-i-limit-access-to-my-back-end-to-be-from-front-door-only"></a>Como posso limitar acesso para meu back-end para ser só de porta de entrada?

Só pode configurar a lista de controle de acesso de IP no seu back-end para permitir apenas desde início saídos intervalos de endereços IP e negar qualquer acesso direto a partir da Internet. Etiquetas de serviço são suportadas para utilização na sua rede virtual. Além disso, pode verificar que o campo de cabeçalho HTTP X-reencaminhados-anfitrião é válido para a sua aplicação web.




## <a name="which-azure-waf-options-should-i-choose"></a>Quais as opções de WAF de Azure devo escolher?

Existem duas opções ao aplicar políticas de WAF no Azure. WAF porta da frente com o Azure é uma solução de segurança de borda, globalmente distribuído. WAF do gateway de aplicação é uma solução regional e dedicada. Recomendamos que escolher uma solução com base nos seus requisitos de desempenho e a segurança geral. Para obter mais informações, consulte [balanceamento de carga com o conjunto de entrega de aplicativos do Azure](https://docs.microsoft.com/azure/frontdoor/front-door-lb-with-azure-app-delivery-suite).


## <a name="do-you-support-same-waf-features-in-all-integrated-platforms"></a>Suporta funcionalidades do mesmo WAF em todas as plataformas integradas?

Atualmente, ModSec CRS 2.2.9 e regras de CRS 3.0 só são suportadas com a WAF no Gateway de aplicação. Limitação de velocidade, filtragem geográfica e do Azure geridas predefinido de conjunto de regras regras só são suportadas com WAF na porta da frente do Azure.

## <a name="is-ddos-protection-integrated-with-front-door"></a>Proteção contra DDoS está integrada com a porta da frente? 

Distribuído globalmente extremidades da rede do Azure, a porta de entrada do Azure pode absorver e geograficamente isolar os ataques de grande volume. Pode criar a política personalizada do WAF para bloquear automaticamente e a taxa de ataques de HTTP (s) de limite que conhecem assinaturas. Ainda mais além disso, pode ativar a DDoS Protection padrão na VNet em que o seu back-ends são implementadas. Os clientes de padrão de proteção contra DDoS do Azure recebem benefícios adicionais, incluindo a proteção contra custos, garantia SLA e o acesso aos especialistas da equipa de resposta rápida de DDoS para obter ajuda imediata durante um ataque. 

## <a name="next-steps"></a>Passos Seguintes

- Saiba mais sobre [firewall de aplicações web do Azure](waf-overview.md).
- Saiba mais sobre [porta da frente do Azure](front-door-overview.md).
