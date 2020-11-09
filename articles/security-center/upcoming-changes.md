---
title: Mudanças importantes que chegam ao Centro de Segurança Azure
description: Próximas alterações ao Azure Security Center que poderá ter de estar atento e para as quais poderá ter de planear
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/09/2020
ms.author: memildin
ms.openlocfilehash: df863372cbf7abfb6fee145b7d13bb00d8deb074
ms.sourcegitcommit: 8a1ba1ebc76635b643b6634cc64e137f74a1e4da
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/09/2020
ms.locfileid: "94380170"
---
# <a name="important-upcoming-changes-to-azure-security-center"></a>Mudanças importantes para o Centro de Segurança Azure

> [!IMPORTANT]
> As informações desta página dizem respeito a produtos ou funcionalidades pré-lançamento, que podem ser substancialmente modificadas antes de serem lançadas comercialmente, se é que alguma vez. A Microsoft não compromete ou garante, expressa ou implícita, no que diz respeito às informações fornecidas aqui.

Nesta página, você vai aprender sobre as mudanças que estão planejadas para o Centro de Segurança. Descreve modificações planeadas no produto que podem ter impacto em coisas como a sua pontuação segura ou fluxos de trabalho.

Se procura as últimas notas de lançamento, vai encontrá-las no [What's new in Azure Security Center.](release-notes.md)


## <a name="planned-changes"></a>Alterações planeadas

### <a name="system-updates-should-be-installed-on-your-machines-recommendation-getting-sub-recommendations"></a>Recomendações "Atualizações do sistema devem ser instaladas nas suas máquinas" recebendo sub-recomendações

#### <a name="summary"></a>Resumo

| Aspeto | Detalhes |
|---------|---------|
|Data do anúncio | 9 de novembro de 2020  |
|Data para esta alteração  |  Meados de novembro de 2020 |
|Impacto     | Durante a transição da versão atual desta recomendação para a sua substituição, a sua pontuação segura pode mudar. |
|  |  |

Estamos a lançar uma versão melhorada das atualizações do Sistema que deve ser instalada na recomendação **das suas máquinas.** A nova versão *substituirá* a versão atual no controlo de segurança das atualizações do sistema aplicada e traz as seguintes melhorias:

- Sub-recomendações para cada atualização em falta
- Uma experiência redesenhada nas páginas do Centro de Segurança Azure do portal Azure
- Dados enriquecidos para a recomendação do Azure Resource Graph

#### <a name="transition-period"></a>Período de transição

Haverá um período de transição de 36 horas (aproximadamente). Para minimizar qualquer potencial perturbação, agendámos a atualização para um fim de semana. Durante a transição, as suas pontuações de segurança podem ser afetadas.

#### <a name="redesigned-portal-experience"></a>Experiência de portal redesenhada

A página de detalhes da recomendação para **atualizações do Sistema deve ser instalada nas suas máquinas** inclui a lista de resultados, tal como mostrado abaixo. Ao selecionar uma única descoberta, o painel de detalhes abre-se com um link para a informação de remediação e uma lista de recursos afetados.

:::image type="content" source="./media/upcoming-changes/system-updates-should-be-installed-subassessment.png" alt-text="Abertura de uma das sub-recomendações na experiência do portal para a recomendação atualizada":::


#### <a name="richer-data-from-azure-resource-graph"></a>Dados mais ricos do Azure Resource Graph

Azure Resource Graph é um serviço em Azure que é projetado para fornecer uma exploração eficiente de recursos. Você pode usar ARG para consultar em escala através de um determinado conjunto de subscrições para que você possa efetivamente governar o seu ambiente. 

Para o Azure Security Center, pode utilizar o ARG e a [Língua De Consulta de Kusto (KQL)](https://docs.microsoft.com/azure/data-explorer/kusto/query/) para consultar uma ampla gama de dados de postura de segurança.

Se consultar a versão atual das atualizações do **Sistema deve ser instalada nas suas máquinas** , a única informação disponível na ARG é que a recomendação precisa de ser remediada numa máquina. Quando a versão atualizada for lançada, a seguinte consulta irá retornar cada atualização do sistema em falta agrupadas por máquina.

```kusto
securityresources
| where type =~ "microsoft.security/assessments/subassessments"
| where extract(@"(?i)providers/Microsoft.Security/assessments/([^/]*)", 1, id) == "4ab6e3c5-74dd-8b35-9ab9-f61b30875b27"
| where properties.status.code == "Unhealthy"
```

## <a name="next-steps"></a>Próximos passos

Para todas as alterações recentes ao produto, veja [o que há de novo no Azure Security Center?](release-notes.md)