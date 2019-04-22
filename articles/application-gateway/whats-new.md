---
title: O que há de novo no Gateway de aplicação do Azure
description: Saiba o que há de novo com o Gateway de aplicação do Azure, como as notas de versão mais recente, problemas, correções de erros conhecidos de funcionalidade preterida e futuras alterações.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: overview
ms.date: 4/1/2019
ms.author: victorh
ms.openlocfilehash: f686c8ac53db2d128cf5bb20f252c547348e5ac7
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/18/2019
ms.locfileid: "58863105"
---
# <a name="whats-new-in-azure-application-gateway"></a>O que há de novo no Gateway de aplicação do Azure?

O Gateway de aplicação do Azure é atualizado de forma contínua. Para se manter atualizado com os desenvolvimentos mais recentes, este artigo fornece informações sobre:

- Versões mais recentes
- Problemas conhecidos
- Correções de erros
- Funcionalidades preteridas

## <a name="new-features"></a>Novos recursos

|Funcionalidade  |Descrição  |Data de adição  |
|---------|---------|---------|
|Cabeçalho CRUD/Reescritas     |Agora pode reescrever cabeçalhos HTTP. Consulte [Tutorial: Criar um gateway de aplicação e volte a escrever cabeçalhos HTTP](tutorial-http-header-rewrite-powershell.md) para obter mais informações.|Dezembro de 2018|
|Lista de configuração e exclusão de WAF     |Adicionámos mais opções para ajudar a configurar o WAF e reduzir os falsos positivos. Ver [limites de tamanho de pedido de firewall de aplicação Web e listas de exclusão](application-gateway-waf-configuration.md) para obter mais informações.|Dezembro de 2018|
|Dimensionamento automático, redundância de zona, a pré-visualização do suporte de VIP estática     |Com o SKU de v2, existem muitas melhorias, como o dimensionamento automático, desempenho melhorado e muito mais. Ver [o que é o Gateway de aplicação do Azure?](overview.md#autoscaling-public-preview) para obter mais informações.|Setembro de 2018|
|Drenagem de ligação     |Drenagem de ligação, simplesmente remover membros de seus conjuntos de back-end. Para obter mais informações, consulte [drenagem de ligação](overview.md#connection-draining).|Setembro de 2018|
|Páginas de erro personalizadas     |Com páginas de erro personalizadas, pode criar uma página de erro no formato do resto dos seus Web sites. Para ativar esta opção, veja [páginas de erro personalizadas de criar o Gateway de aplicação](custom-error.md).|Setembro de 2018|
|Aprimoramentos de métricas     |Pode obter uma melhor vista do Estado do seu Gateway de aplicação com a métrica aprimorada. Para ativar as métricas sobre o Gateway de aplicação, consulte [estado de funcionamento do Back-end, registos de diagnóstico e métricas para o Gateway de aplicação](application-gateway-diagnostics.md).|junho de 2018|

## <a name="known-issues"></a>Problemas conhecidos

- [Problemas conhecidos no SKU do v2](application-gateway-autoscaling-zone-redundant.md#known-issues-and-limitations)

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações sobre o Gateway de aplicação do Azure, consulte [o que é o Gateway de aplicação do Azure?](overview.md)