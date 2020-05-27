---
title: Firewall de aplicação web Azure no Gateway de aplicações - perguntas frequentes
description: Este artigo fornece respostas a perguntas frequentes sobre firewall de aplicação web no Gateway de aplicações
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: article
ms.date: 05/05/2020
ms.author: victorh
ms.openlocfilehash: 3bc71e10167b21b65616dd20f8edb9a1d902527e
ms.sourcegitcommit: cf7caaf1e42f1420e1491e3616cc989d504f0902
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/22/2020
ms.locfileid: "83798356"
---
# <a name="frequently-asked-questions-for-azure-web-application-firewall-on-application-gateway"></a>Perguntas frequentes para firewall de aplicação web Azure no Gateway de aplicações

Este artigo responde a perguntas comuns sobre o Firewall de Aplicação Web Azure (WAF) sobre funcionalidades e funcionalidades do Gateway de Aplicação. 

## <a name="what-is-azure-waf"></a>O que é Azure WAF?

O Azure WAF é uma firewall de aplicação web que ajuda a proteger as suas aplicações web de ameaças comuns, tais como injeção de SQL, scripts transporitais e outras explorações web. Pode definir uma política waf que consiste numa combinação de regras personalizadas e geridas para controlar o acesso às suas aplicações web.

Uma política azure WAF pode ser aplicada a aplicações web hospedadas em Application Gateway ou Azure Front Doors.

## <a name="what-features-does-the-waf-sku-support"></a>Quais as funcionalidades que o WAF SKU suporta?

O WAF SKU suporta todas as funcionalidades disponíveis no SKU Standard.

## <a name="how-do-i-monitor-waf"></a>Como posso monitorizar a WAF?

Monitorize o WAF através da exploração de diagnóstico. Para mais informações, consulte o [registo de diagnóstico e as métricas para o Gateway](../../application-gateway/application-gateway-diagnostics.md)da Aplicação .

## <a name="does-detection-mode-block-traffic"></a>O modo de deteção bloqueia o tráfego?

Não. O modo de deteção apenas regista o tráfego que aciona uma regra WAF.

## <a name="can-i-customize-waf-rules"></a>Posso personalizar as regras da WAF?

Sim. Para mais informações, consulte [Customize WAF rule groups and rules](application-gateway-customize-waf-rules-portal.md).

## <a name="what-rules-are-currently-available-for-waf"></a>Quais as regras atualmente disponíveis para a WAF?

A WAF suporta atualmente O CRS [2.2.9](application-gateway-crs-rulegroups-rules.md#owasp229), [3.0](application-gateway-crs-rulegroups-rules.md#owasp30)e [3.1](application-gateway-crs-rulegroups-rules.md#owasp31). Estas regras fornecem segurança de base contra a maioria das vulnerabilidades do top-10 que o Open Web Application Security Project (OWASP) identifica: 

* Proteção contra injeção de SQL
* Proteção de scripts cross-site
* Proteção contra ataques comuns na Web, tais como injeção de comando, http solicitar contrabando, divisão de resposta HTTP e ataque de inclusão de ficheiros remotos
* Proteção contra violações de protocolo HTTP
* Proteção contra anomalias de protocolo HTTP, como agente de utilizador de anfitrião e cabeçalhos de aceitação em falta
* Prevenção de contra bots, crawlers e scanners
* Deteção de configurações de aplicações comuns (isto é, Apache, IIS, e assim por diante)

Para mais informações, consulte [as vulnerabilidades do Top-10 da OWASP.](https://owasp.org/www-project-top-ten/)

## <a name="does-waf-support-ddos-protection"></a>A WAF suporta a proteção dDoS?

Sim. Pode ativar a proteção DDoS na rede virtual onde o gateway da aplicação está implantado. Esta definição garante que o serviço de proteção Azure DDoS também protege o ip virtual de gateway de aplicação (VIP).


## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre o Firewall de [Aplicação Web Azure](../overview.md).
- Saiba mais sobre [a Porta da Frente Azure.](../../frontdoor/front-door-overview.md)
