---
title: Novidades no Portal de Aplicações Azure
description: Saiba quais são as novidades com o Portal de Aplicações Azure, como as mais recentes notas de lançamento, questões conhecidas, correções de bugs, funcionalidade depreciada e próximas alterações.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: overview
ms.date: 03/24/2020
ms.author: victorh
ms.openlocfilehash: 70014b5ab37a07e01eaa2db3d729b7d8af520842
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "81311765"
---
# <a name="whats-new-in-azure-application-gateway"></a>Quais as novidades no Portal de Aplicação Azure?

O Portal de Aplicações Azure é atualizado de forma contínua. Para se manter atualizado com os desenvolvimentos mais recentes, este artigo fornece-lhe informações sobre:

- Os últimos lançamentos
- Problemas conhecidos
- Correções de erros
- Funcionalidade preterida

## <a name="new-features"></a>Novas funcionalidades

|Funcionalidade  |Descrição  |Data adicionada  |
|---------|---------|---------|
| Rotas definidas pelo utilizador (UDR) em v2 (Pré-visualização) |As rotas definidas pelo utilizador são agora suportadas em alguns cenários no Gateway v2 SKUs. Para mais informações, consulte a visão geral da [configuração do Gateway](configuration-overview.md#user-defined-routes-supported-on-the-application-gateway-subnet)de aplicação . |Março de 2020 |
|Alterações de cookies de afinidade |Quando a afinidade baseada em cookies está ativada, o Application Gateway injeta outro cookie idêntico chamado *ApplicationGatewayAffinityCORS* para além do cookie ApplicationGatewayAffinity existente. *ApplicationGatewayAffinityCORS* tem mais dois atributos adicionados a ele (*SameSite=None; Seguro*) de modo a que as sessões pegajosas sejam mantidas mesmo para pedidos de origem cruzada. Consulte [a afinidade baseada em Cookies de Gateway](configuration-overview.md#cookie-based-affinity) de aplicação para obter mais informações. |Fevereiro de 2020 |
|Melhorias da sonda |Com as melhorias de sonda personalizadas no Application Gateway v2 SKU, simplificamos a configuração da [sonda,](https://docs.microsoft.com/azure/application-gateway/application-gateway-create-probe-portal#create-probe-for-application-gateway-v2-sku)facilitamos [os testes de saúde a pedido](https://docs.microsoft.com/azure/application-gateway/application-gateway-create-probe-portal#test-backend-health-with-the-probe) e adicionamos mais [informações](https://docs.microsoft.com/azure/application-gateway/application-gateway-backend-health-troubleshooting#error-messages) de diagnóstico para ajudá-lo a resolver problemas de saúde.  |Outubro de 2019 |
|Mais métricas |Adicionámos as seguintes novas métricas para o ajudar a monitorizar o seu Gateway de aplicação v2 SKU: [métricas relacionadas com](https://docs.microsoft.com/azure/application-gateway/application-gateway-metrics#timing-metrics)o tempo, estado de resposta backend, Bytes recebidos, Bytes enviados, protocolo TLS cliente e unidades de computação atual. Consulte [métricas suportadas pelo Gateway V2 SKU](https://docs.microsoft.com/azure/application-gateway/application-gateway-metrics#metrics-supported-by-application-gateway-v2-sku). |Agosto de 2019 |
|Regras personalizadas de WAF |O Gateway de aplicações WAF_v2 agora suporta a criação de regras personalizadas. Consulte [as regras personalizadas do Application Gateway](custom-waf-rules-overview.md). |Junho de 2019 |
|Autoscalcificação, redundância de zona, suporte VIP estático GA |Disponibilidade geral para v2 SKU, que suporta autoescalação, redundância de zona, melhoramento desempenho, VIPs estáticos, Cofre chave, reescrita do cabeçalho. Consulte [a documentação autoscalcificante do Gateway de aplicação.](application-gateway-autoscaling-zone-redundant.md) |Abril de 2019 |
|Integração do Cofre Chave |O Application Gateway suporta agora a integração com o Key Vault (em pré-visualização pública) para certificados de servidor que estão anexados aos ouvintes ativados por HTTPS. Consulte [a rescisão de TLS com certificados key vault](key-vault-certs.md). |Abril de 2019 |
|Cabeçalho CRUD/Reescreve     |Agora pode reescrever os cabeçalhos http. Consulte [tutorial: Crie um gateway de aplicação e reescreva os cabeçalhos HTTP](tutorial-http-header-rewrite-powershell.md) para mais informações.|Dezembro de 2018|
|Lista de configuração e exclusão de WAF     |Adicionámos mais opções para o ajudar a configurar o seu WAF e a reduzir falsos positivos. Para mais informações, consulte os limites de tamanho do pedido de firewall da [aplicação Web e as listas](application-gateway-waf-configuration.md)de exclusão .|Dezembro de 2018|
|Autoscalcificação, redundância de zona, suporte VIP estático      |Com o V2 SKU, existem muitas melhorias como autoscaling, melhor desempenho, e muito mais. Veja o que é o Portal de [Aplicação Azure?](overview.md)|Setembro de 2018|
|Drenagem de ligação     |A drenagem da ligação permite-lhe remover graciosamente os membros das suas piscinas de backend. Para mais informações, consulte a [drenagem da Ligação](features.md#connection-draining).|Setembro de 2018|
|Páginas de erro personalizadas     |Com páginas de erro personalizadas, pode criar uma página de erro dentro do formato dos restantes websites. Para o ativar, consulte criar páginas de [erro personalizadas do Gateway](custom-error.md)de aplicação .|Setembro de 2018|
|Melhorias de Métricas     |Você pode obter uma melhor visão do estado do seu Gateway de Aplicação com métricas melhoradas. Para ativar métricas no gateway da aplicação, consulte [a saúde de back-end, registos de diagnóstico e métricas para Gateway](application-gateway-diagnostics.md)de Aplicação .|junho de 2018|

## <a name="next-steps"></a>Passos seguintes

Para mais informações sobre o Portal de Aplicação Azure, consulte [o portal de aplicação Azure?](overview.md)
