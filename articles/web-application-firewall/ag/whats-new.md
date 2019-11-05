---
title: O que há de novo no firewall do aplicativo Web do Azure
description: Saiba o que há de novo no firewall do aplicativo Web do Azure, como as notas de versão mais recentes, problemas conhecidos, correções de bugs, funcionalidade preterida e alterações futuras.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: overview
ms.date: 10/24/2019
ms.author: victorh
ms.openlocfilehash: 0e0e67bfb893e1bf141182e45ce4a49f5f6880ca
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73502248"
---
# <a name="whats-new-in-azure-web-application-firewall"></a>O que há de novo no firewall do aplicativo Web do Azure?

O Firewall do aplicativo Web do Azure é atualizado em uma base contínua. Para se manter atualizado com os desenvolvimentos mais recentes, este artigo fornece informações sobre:

- As versões mais recentes
- Problemas conhecidos
- Correções de erros
- Funcionalidade preterida

## <a name="new-features"></a>Novas funcionalidades

|Funcionalidade  |Descrição  |Data de adição  |
|---------|---------|---------|
|Conjunto de regras de mitigação de bot (versão prévia)|Você pode habilitar um conjunto de regras de mitigação de bot, junto com o conjunto de regras do CRS escolhido. | Novembro de 2019 |
|Integração do GeoDB (versão prévia)|Agora você pode criar regras personalizadas restringindo o tráfego por país de origem. | Novembro de 2019 |
|Política de WAF por site/por URI (versão prévia)|O WAF-v2 agora dá suporte à aplicação de uma política para ouvintes, bem como a regras com base em caminhos. Consulte [criar política de WAF](create-waf-policy-ag.md). | Novembro de 2019 |
|Regras personalizadas de WAF |O WAF_v2 do gateway de aplicativo agora dá suporte à criação de regras personalizadas. Consulte [regras personalizadas do gateway de aplicativo](custom-waf-rules-overview.md). |Junho de 2019 |
|Lista de configuração e exclusão do WAF     |Adicionamos mais opções para ajudá-lo a configurar seu WAF e a reduzir os falsos positivos. Consulte [limites de tamanho de solicitação de firewall do aplicativo Web e listas de exclusão](application-gateway-waf-configuration.md) para obter mais informações.|Dezembro de 2018|

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre o Firewall do aplicativo Web no gateway de aplicativo, consulte [Firewall do aplicativo Web do Azure no gateway de aplicativo Azure](ag-overview.md).
