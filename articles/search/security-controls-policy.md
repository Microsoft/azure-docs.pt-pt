---
title: Controlos de conformidade regulamentar da política Azure para a pesquisa cognitiva do Azure
description: Lista controlos de conformidade regulamentar da política Azure disponíveis para pesquisa cognitiva Azure. Estas definições políticas incorporadas fornecem abordagens comuns para gerir o cumprimento dos seus recursos Azure.
ms.date: 04/14/2021
ms.topic: sample
author: HeidiSteen
ms.author: heidist
ms.service: search
ms.custom: subject-policy-compliancecontrols
ms.openlocfilehash: 807201d5626bcdbc421d182f06b888c5775dcc48
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107497422"
---
# <a name="azure-policy-regulatory-compliance-controls-for-azure-cognitive-search"></a>Controlos de conformidade regulamentar da política Azure para a pesquisa cognitiva do Azure

Se está a usar a [Azure Policy](../governance/policy/overview.md) para fazer cumprir as recomendações no [Azure Security Benchmark,](../security/benchmarks/introduction.md)então provavelmente já sabe que pode criar políticas para identificar e corrigir serviços não conformes. Estas políticas podem ser personalizadas, ou podem basear-se em definições incorporadas que fornecem critérios de conformidade e soluções adequadas para boas práticas bem compreendidas.

Para a Azure Cognitive Search, existe atualmente uma definição embutida, listada abaixo, que pode usar numa atribuição de política. O incorporado é para registo e monitorização. Ao utilizar esta definição incorporada numa [política que cria,](../governance/policy/assign-policy-portal.md)o sistema irá procurar serviços de pesquisa que não tenham [registo de diagnóstico](search-monitor-logs.md), e depois ativar-se-á em conformidade.

[A Conformidade Regulamentar na Política Azure](../governance/policy/concepts/regulatory-compliance.md) fornece definições de iniciativa criadas e geridas pela Microsoft, conhecidas como _incorporados,_ para os domínios de conformidade e **controlos** de segurança relacionados com **diferentes** padrões de conformidade. Esta página lista os **domínios de conformidade** e os **controlos de segurança** para a Pesquisa Cognitiva Azure. Pode atribuir os incorporados a um controlo de **segurança** individualmente para ajudar a tornar os seus recursos Azure conforme com o padrão específico.

[!INCLUDE [azure-policy-compliancecontrols-introwarning](../../includes/policy/standards/intro-warning.md)]

[!INCLUDE [azure-policy-compliancecontrols-search](../../includes/policy/standards/byrp/microsoft.search.md)]

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre [a Conformidade Regulamentar da Política Azure.](../governance/policy/concepts/regulatory-compliance.md)
- Veja as incorporações no [repositório do GitHub do Azure Policy](https://github.com/Azure/azure-policy).
