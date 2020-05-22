---
title: Novidades na Firewall de Aplicação Web do Azure
description: Saiba quais são as novidades com o Azure Web Application Firewall, como as mais recentes notas de lançamento, problemas conhecidos, correções de bugs, funcionalidade depreciada e próximas alterações.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: overview
ms.date: 10/24/2019
ms.author: victorh
ms.openlocfilehash: 11212c931e38b1c6319c7c3554155d9d17a1080c
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83745151"
---
# <a name="whats-new-in-azure-web-application-firewall"></a>Quais as novidades no Firewall de Aplicação Web do Azure?

O Firewall de aplicação web azure é atualizado de forma contínua. Para se manter atualizado com os desenvolvimentos mais recentes, este artigo fornece-lhe informações sobre:

- Os últimos lançamentos
- Problemas conhecidos
- Correções de erros
- Funcionalidade preterida

## <a name="new-features"></a>Novas funcionalidades

|Funcionalidade  |Descrição  |Data adicionada  |
|---------|---------|---------|
|Regras de Mitigação do Bot (pré-visualização)|Pode ativar uma regra de Mitigação de Bots, juntamente com o conjunto de regras de CRS que escolher. | Novembro de 2019 |
|Integração GeoDB (pré-visualização)|Agora pode criar regras personalizadas que restringem o tráfego por país/região de origem. | Novembro de 2019 |
|Política WAF por site/per-URI (pré-visualização)|A WAF-v2 apoia agora a aplicação de uma política aos ouvintes, bem como regras baseadas em caminhos. Ver [Criar a política waf](create-waf-policy-ag.md). | Novembro de 2019 |
|Regras personalizadas de WAF |O Gateway de aplicações WAF_v2 agora suporta a criação de regras personalizadas. Consulte [as regras personalizadas do Application Gateway](custom-waf-rules-overview.md). |Junho de 2019 |
|Lista de configuração e exclusão de WAF     |Adicionámos mais opções para o ajudar a configurar o seu WAF e a reduzir falsos positivos. Consulte os limites de tamanho do pedido de firewall de [aplicação Web e listas de exclusão](application-gateway-waf-configuration.md) para mais informações.|Dezembro de 2018|

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre o Firewall da Aplicação Web no Gateway da Aplicação, consulte o Firewall de [aplicação web do Azure no Portal da Aplicação Azure](ag-overview.md).
