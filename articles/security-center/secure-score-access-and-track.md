---
title: Rastreando a sua pontuação segura no Azure Security Center
description: Saiba mais sobre as múltiplas formas de aceder e rastrear a sua pontuação segura no Azure Security Center.
author: memildin
ms.author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 02/25/2021
ms.openlocfilehash: 5efc48d348e9cfceab590bcfba8c621e7721376f
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2021
ms.locfileid: "102107966"
---
# <a name="access-and-track-your-secure-score"></a>Aceda e acompanhe a sua pontuação segura

Pode encontrar a sua pontuação máxima, bem como a sua pontuação por subscrição, através do portal Azure ou programaticamente como descrito nas seguintes secções:

> [!TIP]
> Para obter uma explicação detalhada de como as suas pontuações são [calculadas, consulte Cálculos - compreendendo a sua pontuação](secure-score-security-controls.md#calculations---understanding-your-score).

## <a name="get-your-secure-score-from-the-portal"></a>Obtenha a sua pontuação segura a partir do portal

O Centro de Segurança exibe a sua pontuação de forma proeminente no portal: é o primeiro azulejo principal da página geral do Centro de Segurança. Selecionando este azulejo, leva-o à página de pontuação segura dedicada, onde verá a pontuação desabatada por subscrição. Selecione uma única subscrição para ver a lista detalhada de recomendações prioritárias e o impacto potencial que a sua reparação terá na pontuação da subscrição. 

Para recapitular, a sua pontuação segura é mostrada nos seguintes locais nas páginas do portal do Security Center.

- Num azulejo da **visão geral** do Centro de Segurança (painel principal):

    :::image type="content" source="./media/secure-score-security-controls/score-on-main-dashboard.png" alt-text="A pontuação segura no painel do Centro de Segurança":::

- Na página de **pontuação Secure** dedicada pode ver a pontuação segura para a sua subscrição e os seus grupos de gestão:

    :::image type="content" source="./media/secure-score-security-controls/score-on-dedicated-dashboard.png" alt-text="A pontuação segura para subscrições na página de pontuação segura do Security Center":::

    :::image type="content" source="./media/secure-score-security-controls/secure-score-management-groups.png" alt-text="A pontuação segura para grupos de gestão na página de pontuação segura do Security Center":::

    > [!NOTE]
    > Quaisquer grupos de gestão para os quais não tenha permissões suficientes, mostrarão a sua pontuação como "Restrito". 

- No topo da página de **Recomendações:**

    :::image type="content" source="./media/secure-score-security-controls/score-on-recommendations-page.png" alt-text="A pontuação segura na página de recomendações do Centro de Segurança":::

## <a name="get-your-secure-score-from-the-rest-api"></a>Obtenha a sua pontuação segura na API REST

Pode aceder à sua pontuação através da pontuação segura API. Os métodos API fornecem a flexibilidade para consultar os dados e construir o seu próprio mecanismo de reporte das suas pontuações seguras ao longo do tempo. Por exemplo, pode utilizar a [API de Pontuações Seguras](/rest/api/securitycenter/securescores) para obter a pontuação de uma subscrição específica. Além disso, pode utilizar a [API de Controlos de Pontuação Segura](/rest/api/securitycenter/securescorecontrols) para listar os controlos de segurança e a pontuação atual das suas subscrições.

![Recuperação de uma única pontuação segura através da API](media/secure-score-security-controls/single-secure-score-via-api.png)

Por exemplo, ferramentas construídas em cima da pontuação segura API, consulte [a área de pontuação segura da nossa comunidade GitHub.](https://github.com/Azure/Azure-Security-Center/tree/master/Secure%20Score) 

## <a name="get-your-secure-score-from-azure-resource-graph"></a>Obtenha a sua pontuação segura a partir do Azure Resource Graph

O Azure Resource Graph fornece acesso instantâneo à informação de recursos em todos os seus ambientes em nuvem com capacidades robustas de filtragem, agrupamento e triagem. É uma forma rápida e eficiente de consultar informações através de subscrições Azure programáticas ou dentro do portal Azure. [Saiba mais sobre o Azure Resource Graph](../governance/resource-graph/index.yml).

Para aceder à pontuação segura para várias subscrições com gráfico de recurso Azure:

1. A partir do portal Azure, abra **o Azure Resource Graph Explorer**.

    :::image type="content" source="./media/security-center-identity-access/opening-resource-graph-explorer.png" alt-text="Página de recomendação do Azure Resource Graph Explorer**" :::

1. Insira a sua consulta Kusto (utilizando os exemplos abaixo para obter orientação).

    - Esta consulta devolve o ID de subscrição, a pontuação atual em pontos e em percentagem, e a pontuação máxima para a subscrição. 

        ```kusto
        SecurityResources 
        | where type == 'microsoft.security/securescores' 
        | extend current = properties.score.current, max = todouble(properties.score.max)
        | project subscriptionId, current, max, percentage = ((current / max)*100)
        ```

    - Esta consulta devolve o estado de todos os controlos de segurança. Para cada controlo, obterá o número de recursos insalubres, a pontuação atual e a pontuação máxima. 

        ```kusto
        SecurityResources 
        | where type == 'microsoft.security/securescores/securescorecontrols'
        | extend SecureControl = properties.displayName, unhealthy = properties.unhealthyResourceCount, currentscore = properties.score.current, maxscore = properties.score.max
        | project SecureControl , unhealthy, currentscore, maxscore
        ```

1. Selecione **Consulta de execução**.


## <a name="tracking-your-secure-score-over-time"></a>Rastreando a sua pontuação segura ao longo do tempo

### <a name="secure-score-over-time-report-in-workbooks-page"></a>Relatório de pontuação segura ao longo do tempo na página de livros

A página de livros do Security Center inclui um relatório pronto para rastrear visualmente as pontuações das suas subscrições, controlos de segurança e muito mais. Saiba mais na [Create rich, relatórios interativos de dados do Security Center](custom-dashboards-azure-workbooks.md).

:::image type="content" source="media/custom-dashboards-azure-workbooks/secure-score-over-time-snip.png" alt-text="Uma secção da pontuação segura ao longo do tempo reporte da galeria de livros do Azure Security Center":::

### <a name="power-bi-pro-dashboards"></a>Painéis power BI Pro

Se for um utilizador de Power BI com uma conta Pro, pode utilizar o painel **DeSema** para rastrear a sua pontuação segura ao longo do tempo e investigar quaisquer alterações.

> [!TIP]
> Você pode encontrar este dashboard, bem como outras ferramentas para trabalhar programaticamente com pontuação segura, na área dedicada da comunidade do Centro de Segurança Azure no GitHub: https://github.com/Azure/Azure-Security-Center/tree/master/Secure%20Score

O painel contém os seguintes dois relatórios para ajudá-lo a analisar o seu estado de segurança:

- **Resumo de Recursos** - fornece dados resumidos sobre a saúde dos seus recursos.
- **Secure Score Summary** - fornece dados resumidos sobre o progresso da sua pontuação. Utilize o gráfico "Obter pontuação segura ao longo do tempo por subscrição" para ver as alterações na pontuação. Se notar uma alteração dramática na sua pontuação, verifique a tabela "alterações detetadas que podem afetar a sua pontuação segura" para eventuais alterações que possam ter causado a alteração. Este quadro apresenta recursos eliminados, recursos recém-utilizados ou recursos que o seu estatuto de segurança mudou para uma das recomendações.

:::image type="content" source="./media/secure-score-security-controls/power-bi-secure-score-dashboard.png" alt-text="O painel opcional Secure Score Over Time Power BI para rastrear a sua pontuação segura ao longo do tempo e investigar alterações":::


## <a name="next-steps"></a>Passos seguintes

Este artigo descreveu como aceder e rastrear a sua pontuação segura. Para obter material relacionado, consulte os seguintes artigos:

- [Conheça os diferentes elementos de uma recomendação](security-center-recommendations.md)
- [Saiba como remediar recomendações](security-center-remediate-recommendations.md)
- [Veja as ferramentas baseadas no GitHub para trabalhar programáticamente com pontuação segura](https://github.com/Azure/Azure-Security-Center/tree/master/Secure%20Score)