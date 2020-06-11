---
title: Novidades no Azure Application Gateway
description: Saiba quais as novidades com o Azure Application Gateway, como as últimas notas de lançamento, problemas conhecidos, correções de bugs, funcionalidades preprecadas e próximas alterações.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: overview
ms.date: 06/10/2020
ms.author: victorh
ms.openlocfilehash: dd6ec2057ede076511b567c013fc1b6fa12d8281
ms.sourcegitcommit: eeba08c8eaa1d724635dcf3a5e931993c848c633
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/10/2020
ms.locfileid: "84669737"
---
# <a name="whats-new-in-azure-application-gateway"></a>Quais as novidades no Azure Application Gateway?

O Azure Application Gateway é atualizado de forma contínua. Para se manter atualizado com os desenvolvimentos mais recentes, este artigo fornece-lhe informações sobre:

- Os últimos lançamentos
- Problemas conhecidos
- Correções de erros
- Funcionalidade preterida

## <a name="new-features"></a>Novas funcionalidades

|Funcionalidade  |Descrição  |Data adicionada  |
|---------|---------|---------|
| Controlador de entrada de entrada de aplicação (AGIC) Addon AKS (Pré-visualização) |O Controlador de Entrada de Gateway de Aplicação pode agora ser implantado como um addon AKS nativo numa linha através do Azure CLI. Ser um addon AKS permite que a AGIC se torne um serviço totalmente gerido, enquanto ainda está em execução no cluster AKS do cliente. Para obter mais informações, consulte [as diferenças adicionais da AGIC](ingress-controller-overview.md#difference-between-helm-deployment-and-aks-add-on). |junho de 2020 |
| Rotas definidas pelo utilizador (UDR) em v2 (Pré-visualização) |As rotas definidas pelo utilizador são agora suportadas em alguns cenários em Application Gateway v2 SKUs. Para obter mais informações, consulte a [visão geral da configuração do Gateway de Aplicação](configuration-overview.md#user-defined-routes-supported-on-the-application-gateway-subnet). |Março de 2020 |
|Alterações no cookie de afinidade |Quando a afinidade baseada em cookies está ativada, o Application Gateway injeta outro cookie idêntico chamado *ApplicationGatewayAffinityCORS,* além do cookie applicationGatewayAffinity existente. *ApplicationGatewayAffinityCORS* tem mais dois atributos adicionados a ele *(SameSite=Nenhum; Secure*), de modo a que as sessões pegajosas sejam mantidas mesmo para pedidos de origem cruzada. Consulte [a afinidade baseada em Cookies de Gateway para](configuration-overview.md#cookie-based-affinity) obter mais informações. |Fevereiro de 2020 |
|Melhorias da sonda |Com as melhorias personalizadas da sonda em Application Gateway v2 SKU, temos [configuração](https://docs.microsoft.com/azure/application-gateway/application-gateway-create-probe-portal#create-probe-for-application-gateway-v2-sku)de sonda simplificada, facilitamos [os testes de saúde de backend a pedido](https://docs.microsoft.com/azure/application-gateway/application-gateway-create-probe-portal#test-backend-health-with-the-probe) e adicionamos mais [informações de diagnóstico](https://docs.microsoft.com/azure/application-gateway/application-gateway-backend-health-troubleshooting#error-messages) para ajudá-lo a resolver problemas de saúde.  |Outubro de 2019 |
|Mais métricas |Adicionámos as seguintes novas métricas para o ajudar a monitorizar o seu Application Gateway v2 SKU: [métricas relacionadas com o tempo,](https://docs.microsoft.com/azure/application-gateway/application-gateway-metrics#timing-metrics)estado de resposta de backend, Bytes recebidos, Bytes enviados, protocolo TLS do Cliente e unidades de computação corrente. Ver [métricas suportadas pelo Gateway V2 SKU](https://docs.microsoft.com/azure/application-gateway/application-gateway-metrics#metrics-supported-by-application-gateway-v2-sku). |Agosto de 2019 |
|Regras personalizadas de WAF |O WAF_v2 de Aplicação Gateway suporta agora a criação de regras personalizadas. Consulte [as regras personalizadas do Application Gateway](custom-waf-rules-overview.md). |Junho de 2019 |
|Autoscalagem, redundância de zona, suporte VIP estático |Disponibilidade geral para v2 SKU, que suporta autoscaling, redundância de zona, melhor desempenho, VIPs estáticos, Cofre chave, reescrita do cabeçalho. Consulte a [documentação de auto-cálculo do Application Gateway](application-gateway-autoscaling-zone-redundant.md). |Abril de 2019 |
|Integração do Cofre Chave |O Application Gateway suporta agora a integração com o Key Vault (na pré-visualização pública) para certificados de servidor que estão anexados aos ouvintes com ativação HTTPS. Consulte [a rescisão de TLS com certificados Key Vault](key-vault-certs.md). |Abril de 2019 |
|Cabeçalho CRUD/Reescritas     |Agora pode reescrever os cabeçalhos HTTP. Consulte [Tutorial: Crie um gateway de aplicação e reescreva os cabeçalhos HTTP](tutorial-http-header-rewrite-powershell.md) para mais informações.|Dezembro de 2018|
|Lista de configuração e exclusão de WAF     |Adicionámos mais opções para o ajudar a configurar o seu WAF e a reduzir falsos positivos. Para obter mais informações, consulte os limites de tamanho do pedido de [firewall da web e as listas de exclusão.](application-gateway-waf-configuration.md)|Dezembro de 2018|
|Autoscalagem, redundância de zona, suporte VIP estático      |Com o V2 SKU, existem muitas melhorias como Autoscaling, melhor desempenho, e muito mais. Veja [o que é Azure Application Gateway?](overview.md)|Setembro de 2018|
|Drenagem de ligação     |A drenagem de ligação permite-lhe remover graciosamente os membros das suas piscinas de backend. Para obter mais informações, consulte [a drenagem de ligação.](features.md#connection-draining)|Setembro de 2018|
|Páginas de erro personalizadas     |Com páginas de erro personalizadas, pode criar uma página de erro dentro do formato do resto dos seus websites. Para ativar isto, consulte [as páginas de erro personalizadas Do Portal da Aplicação.](custom-error.md)|Setembro de 2018|
|Melhorias métricas     |Pode ter uma melhor visão do estado do seu Gateway de aplicação com métricas melhoradas. Para ativar métricas no seu Gateway de aplicação, consulte [saúde back-end, registos de diagnóstico e métricas para o Gateway de Aplicações](application-gateway-diagnostics.md).|Junho de 2018|

## <a name="next-steps"></a>Próximos passos

Para mais informações sobre o Gateway de Aplicações Azure, veja [o que é O Gateway de Aplicação Azure?](overview.md)
