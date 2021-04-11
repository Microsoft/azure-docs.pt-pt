---
title: Azure Web Application Firewall on Application Gateway - perguntas frequentes
description: Este artigo fornece respostas a perguntas frequentes sobre Firewall de aplicação web no Gateway de aplicações
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: article
ms.date: 05/05/2020
ms.author: victorh
ms.openlocfilehash: 890688dba70a7fa654e97652b3e474b919f9a077
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104585388"
---
# <a name="frequently-asked-questions-for-azure-web-application-firewall-on-application-gateway"></a>Perguntas frequentes para Azure Web Application Firewall no Gateway de aplicações

Este artigo responde a perguntas comuns sobre a Azure Web Application Firewall (WAF) nas funcionalidades e funcionalidades do Gateway de Aplicações. 

## <a name="what-is-azure-waf"></a>O que é Azure WAF?

Azure WAF é uma firewall de aplicação web que ajuda a proteger as suas aplicações web de ameaças comuns, tais como injeção de SQL, scripts de sites cruzados e outras explorações web. Pode definir uma política da WAF que consiste numa combinação de regras personalizadas e geridas para controlar o acesso às suas aplicações web.

Uma política Azure WAF pode ser aplicada a aplicações web hospedadas em Application Gateway ou Azure Front Doors.

## <a name="what-features-does-the-waf-sku-support"></a>Quais as funcionalidades que o WAF SKU suporta?

O WAF SKU suporta todas as funcionalidades disponíveis no SKU Standard.

## <a name="how-do-i-monitor-waf"></a>Como monitorizo a WAF?

Monitorize a WAF através de registo de diagnóstico. Para obter mais informações, consulte [o registo de diagnóstico e as métricas do Gateway de Aplicação.](../../application-gateway/application-gateway-diagnostics.md)

## <a name="does-detection-mode-block-traffic"></a>O modo de deteção bloqueia o tráfego?

N.º O modo de deteção apenas regista o tráfego que aciona uma regra WAF.

## <a name="can-i-customize-waf-rules"></a>Posso personalizar as regras da WAF?

Sim. Para obter mais informações, consulte [personalizar grupos e regras de regras waf](application-gateway-customize-waf-rules-portal.md).

## <a name="what-rules-are-currently-available-for-waf"></a>Quais as regras atualmente disponíveis para a WAF?

A WAF suporta atualmente o CRS [2.2.9](application-gateway-crs-rulegroups-rules.md#owasp229), [3.0](application-gateway-crs-rulegroups-rules.md#owasp30)e [3.1](application-gateway-crs-rulegroups-rules.md#owasp31). Estas regras fornecem segurança de base contra a maioria das vulnerabilidades do top-10 que o Open Web Application Security Project (OWASP) identifica: 

* Proteção contra injeção de SQL
* Proteção de scripts cross-site
* Proteção contra ataques comuns da web, tais como injeção de comando, pedido HTTP contrabando, divisão de resposta HTTP e ataque de inclusão de ficheiros remotos
* Proteção contra violações de protocolo HTTP
* Proteção contra anomalias de protocolo HTTP, como agente de utilizador de anfitrião e cabeçalhos de aceitação em falta
* Prevenção de contra bots, crawlers e scanners
* Deteção de configurações comuns de aplicações (isto é, Apache, IIS, e assim por diante)

Para obter mais informações, consulte [as vulnerabilidades do top-10 da OWASP](https://owasp.org/www-project-top-ten/).

## <a name="what-content-types-does-waf-support"></a>Que tipos de conteúdo suporta o WAF?

O Application Gateway WAF suporta os seguintes tipos de conteúdo para regras geridas:

* application/json
* aplicação/xml
* aplicação/x-www-form-urlencoded
* multiparte/dados de formulários

E para regras personalizadas:

* aplicação/x-www-form-urlencoded
* multiparte/dados de formulários

## <a name="does-waf-support-ddos-protection"></a>A WAF suporta a proteção DDoS?

Sim. Pode ativar a proteção DDoS na rede virtual onde o gateway de aplicações é implantado. Esta definição garante que o serviço de Proteção Azure DDoS também protege o GATEWAY virtual IP (VIP).

### <a name="does-waf-store-customer-data"></a>A WAF armazena os dados dos clientes?

Não, a WAF não armazena os dados dos clientes.

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre [o Azure Web Application Firewall](../overview.md).
- Saiba mais sobre [a Porta frontal Azure.](../../frontdoor/front-door-overview.md)
