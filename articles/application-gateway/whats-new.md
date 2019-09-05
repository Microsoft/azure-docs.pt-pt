---
title: O que há de novo no gateway de Aplicativo Azure
description: Saiba o que há de novo no gateway de Aplicativo Azure, como as notas de versão mais recentes, problemas conhecidos, correções de bugs, funcionalidade preterida e alterações futuras.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: overview
ms.date: 4/30/2019
ms.author: victorh
ms.openlocfilehash: 6755825aa123b3d7d1eed23291790ff3dc477ac5
ms.sourcegitcommit: 49c4b9c797c09c92632d7cedfec0ac1cf783631b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/05/2019
ms.locfileid: "70382471"
---
# <a name="whats-new-in-azure-application-gateway"></a>O que há de novo no gateway Aplicativo Azure?

Aplicativo Azure gateway é atualizado em uma base contínua. Para se manter atualizado com os desenvolvimentos mais recentes, este artigo fornece informações sobre:

- Versões mais recentes
- Problemas conhecidos
- Correções de erros
- Funcionalidades preteridas

## <a name="new-features"></a>Novos recursos

|Funcionalidade  |Descrição  |Data de adição  |
|---------|---------|---------|
|Mais métricas |Adicionamos as novas métricas a seguir para ajudá-lo a monitorar seu SKU do Application Gateway v2: [Métricas relacionadas a tempo](https://docs.microsoft.com/azure/application-gateway/application-gateway-metrics#timing-metrics), status de resposta de back-end, bytes recebidos, bytes enviados, protocolo TLS de cliente e unidades de computação atuais. Consulte as [métricas com suporte do SKU do gateway de aplicativo v2](https://docs.microsoft.com/azure/application-gateway/application-gateway-metrics#metrics-supported-by-application-gateway-v2-sku). |Agosto de 2019 |
|Regras personalizadas de WAF |Application Gateway WAF_v2 agora dá suporte à criação de regras personalizadas. Consulte [regras personalizadas do gateway de aplicativo](custom-waf-rules-overview.md). |Junho de 2019 |
|Dimensionamento automático, redundância de zona, suporte a VIP estático GA |Disponibilidade geral para SKU v2 que dá suporte ao dimensionamento automático, redundância de zona, desempenho aprimorado, VIPs estáticos, Key Vault, reescrita de cabeçalho. Consulte [documentação de dimensionamento](application-gateway-autoscaling-zone-redundant.md)automático do gateway de aplicativo. |Abril de 2019 |
|Integração do Key Vault |O gateway de aplicativo agora dá suporte à integração com o Key Vault (em visualização pública) para certificados de servidor anexados a ouvintes habilitados para HTTPS. Consulte [terminação SSL com certificados de Key Vault](key-vault-certs.md). |Abril de 2019 |
|CRUD/regravações de cabeçalho     |Agora você pode reescrever cabeçalhos HTTP. Consulte [o tutorial: Crie um gateway de aplicativo e reescreva os](tutorial-http-header-rewrite-powershell.md) cabeçalhos HTTP para obter mais informações.|Dezembro de 2018|
|Lista de configuração e exclusão do WAF     |Adicionamos mais opções para ajudá-lo a configurar seu WAF e a reduzir os falsos positivos. Consulte [limites de tamanho de solicitação de firewall do aplicativo Web e listas de exclusão](application-gateway-waf-configuration.md) para obter mais informações.|Dezembro de 2018|
|Dimensionamento automático, redundância de zona, suporte a VIP estático      |Com a SKU v2, há muitas melhorias, como dimensionamento automático, melhor desempenho e muito mais. Consulte [o que é aplicativo Azure gateway?](overview.md) para obter mais informações.|Setembro de 2018|
|Drenagem de ligação     |O descarregamento de conexão permite que você remova normalmente os membros de seus pools de back-end. Para obter mais informações, consulte [descarga de conexão](overview.md#connection-draining).|Setembro de 2018|
|Páginas de erro personalizadas     |Com páginas de erro personalizadas, você pode criar uma página de erro dentro do formato do restante dos seus sites. Para habilitar isso, consulte [criar páginas de erro personalizadas do gateway de aplicativo](custom-error.md).|Setembro de 2018|
|Aprimoramentos de métricas     |Você pode obter uma exibição melhor do estado do seu gateway de aplicativo com métricas aprimoradas. Para habilitar as métricas no seu gateway de aplicativo, consulte [integridade de back-end, logs de diagnóstico e métricas para o gateway de aplicativo](application-gateway-diagnostics.md).|junho de 2018|

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações sobre Aplicativo Azure gateway, consulte [o que é o gateway de aplicativo Azure?](overview.md)
