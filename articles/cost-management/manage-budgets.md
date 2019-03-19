---
title: Gira orçamentos de Cloudyn no Azure | Documentos da Microsoft
description: Este artigo ajuda-o a criar rapidamente os orçamentos e começar a geri-los no Cloudyn.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 03/14/2018
ms.topic: conceptual
ms.service: cost-management
manager: vitavor
ms.custom: seodec18
ms.openlocfilehash: e6afa7eb91daec95c44d76007ea793fca90b61bb
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58009110"
---
# <a name="manage-azure-budgets-with-cloudyn"></a>Gira orçamentos do Azure no Cloudyn

Definição de segurança orçamentos e com base no orçamento alerta ajuda a melhorar a governação de cloud e a responsabilidade. Este artigo ajuda-o a criar rapidamente os orçamentos e começar a geri-los no Cloudyn.

Quando tiver uma conta de empresa ou MSP, pode utilizar a sua estrutura de entidades de custo hierárquica para atribuir as quotas mensais de orçamento para diferentes unidades de negócios, departamentos ou qualquer outra entidade de custo. Quando tiver uma conta de Premium, pode utilizar a funcionalidade de gestão do orçamento, que, em seguida, é aplicada a suas atribuições de despesas de integralmente na nuvem. Todos os orçamentos manualmente são atribuídos.

Com base nos orçamentos atribuídos, pode definir alertas de limiar com base na percentagem de seu orçamento de que é consumido e definir a gravidade de cada limite.

Os relatórios de orçamento mostram o orçamento atribuído. Os utilizadores podem ver quando seus gastos é através de, em ou semelhante com o respetivo consumo ao longo do tempo. Quando seleciona **Mostrar/ocultar campos** na parte superior de um relatório de orçamento, pode ver o custo, o orçamento, o custo acumulado ou o orçamento total.

O Azure Cost Management disponibiliza uma funcionalidade semelhante à Cloudyn. O Azure Cost Management é uma solução de gestão de custos nativa do Azure. Ajuda-o a analisar os custos, a criar e a gerir orçamentos, a exportar dados, bem como a analisar e a agir de acordo com as recomendações de otimização para economizar dinheiro. Para obter mais informações sobre os orçamentos no Cost Management, consulte [criar e gerir orçamentos](tutorial-acm-create-budgets.md).

## <a name="create-budgets"></a>Criar orçamentos

Quando cria um orçamento, pode defini-la para o ano fiscal e aplica-se para uma entidade específica.

Para criar um orçamento e atribuí-lo a uma entidade:

1. Navegue para **custos** &gt; **gestão de custos** &gt; **orçamento**.
2. Na página de gestão do orçamento, sob **entidades**, selecione a entidade em que pretende criar o orçamento.
3. No ano de orçamento, selecione o ano em que pretende criar o orçamento.
4. Para cada mês, defina um valor de orçamento. Quando tiver terminado, clique em **guardar**.
Neste exemplo, o orçamento mensal para Junho de 2018 é definido como $135,000. O orçamento total para o ano é US $1,615,000.00.
![Criar uma página de orçamento onde define um orçamento de todos os meses](./media/manage-budgets/set-budget.png)


Para importar um ficheiro para o orçamento anual:

1. Sob **ações**, selecione **exportar** para transferir um modelo CSV em branco para utilizar como a base para o orçamento.
2. Preencha o ficheiro CSV com suas entradas de orçamento e guarde-o localmente.
3. Sob **ações**, selecione **importação**.
4. Selecione o seu ficheiro guardado e, em seguida, clique em **OK**.

Para exportar seu orçamento concluído como um ficheiro CSV, em **ações**, selecione **exportar** para transferir o ficheiro.

## <a name="view-budget-in-reports"></a>Orçamento de modo de exibição nos relatórios

Quando concluída, o orçamento é mostrado na maioria dos relatórios de custos em **custos** &gt; **análise de custo** e no vs custo. Relatório de orçamento ao longo do tempo. Também pode agendar relatórios baseados em limiares de orçamento usando **ações**.

Eis um exemplo do relatório de análise de custos. Mostra o orçamento total e o custo por tipos de carga de trabalho e a utilização desde o início do ano.

![Relatório de análise de custos de exemplo com orçamento](./media/manage-budgets/cost-analysis-budget-example.png)

Neste exemplo, suponha que a data atual é de 22 de Junho. O custo de Junho de 2018 é US $71,611.28 em comparação com o orçamento mensal de US $135,000. O custo é muito menor do que o orçamento mensal porque ainda existem oito dias dos gastos antes do final do mês.

Outra forma de ver o relatório é examinar o custo acumulado vs seu orçamento. Para ver os custos acumulados, em **Mostrar/ocultar campos**, selecione **custo acumulado** e **orçamento Total**. Eis um exemplo que mostra o custo acumulado desde o início do ano.

![Exemplo acumulados custo e orçamento total mostrada os custo vs. Relatório de orçamento ao longo do tempo](./media/manage-budgets/accumulated-budget.png)

Algum tempo no futuro o custo acumulado pode exceder seu orçamento. Vemos que mais facilmente se alterar a vista de gráfico para o _linha_ tipo.

![Mostrada um gráfico de linhas no custo por relatório de meses de orçamento](./media/manage-budgets/budget-line.png)

## <a name="create-budget-alerts-for-a-filter"></a>Criar alertas de orçamento para um filtro

No exemplo anterior, pode ver que o custo acumulado abordado do orçamento. Pode criar alertas de orçamento automática para que receberá uma notificação quando gastos abordagens ou excede o seu orçamento. Basicamente, o alerta é um relatório agendado com um limiar. Métricas de limiar de alerta de orçamento incluem:

- Restante custo versus Orçamento – para especificar um limiar de valor de moeda
- Percentagem de custo versus Orçamento – para especificar um limiar de valor de percentagem

Vamos examinar um exemplo.

No vs custo. Orçamento ao longo do relatório do tempo, clique em **ações** e, em seguida, selecione **agendar relatório**. No separador do limiar, selecione uma métrica de limiar. Por exemplo, **orçamento de vs de percentagem de custo**. Selecione um tipo de alerta e introduza um valor de percentagem do orçamento. Se quiser ser notificado de uma só vez, selecione **número de alertas consecutivos** e, em seguida, escreva _1_. Clique em **Guardar**.

![Criar um orçamento alertar relativamente ao salvar ou agendar esta caixa de relatório](./media/manage-budgets/budget-alert.png)

## <a name="next-steps"></a>Passos Seguintes

- Se ainda não tiver concluído o primeiro tutorial da Cloudyn, leia-a em [rever a utilização e custos](tutorial-review-usage.md).
- Saiba mais sobre o [relatórios disponíveis no Cloudyn](use-reports.md).
