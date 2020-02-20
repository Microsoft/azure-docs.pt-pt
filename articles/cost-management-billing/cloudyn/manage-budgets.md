---
title: Gerir os orçamentos da Cloudyn no Azure
description: Este artigo ajuda-o a criar rapidamente orçamentos e a começar a geri-los na Cloudyn.
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 01/24/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.reviewer: vitavor
ms.custom: seodec18
ms.openlocfilehash: 1436c9b3b612a231760b6cdb04b5166fb45d7962
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/13/2020
ms.locfileid: "77201140"
---
# <a name="manage-azure-budgets-with-cloudyn"></a>Gerir orçamentos do Azure com a Cloudyn

A configuração dos orçamentos e dos alertas baseados em orçamentos ajudam a melhorar a governação e a responsabilidade na cloud. Este artigo ajuda-o a criar rapidamente orçamentos e a começar a geri-los na Cloudyn.

Se tiver uma conta Enterprise ou MSP, poderá utilizar a estrutura de entidades de custos hierárquica para atribuir quotas orçamentais mensais a diferentes unidades de negócio, departamentos ou qualquer outra entidade de custos. Se tiver uma conta Premium, poderá utilizar a funcionalidade de gestão de orçamentos, que será aplicada a todos os gastos da cloud. Todos os orçamentos são atribuídos manualmente.

Com base nos orçamentos atribuídos, pode estabelecer alertas de limiar com base na percentagem do orçamento consumido e definir a gravidade de cada limiar.

Os relatórios orçamentais mostram o orçamento atribuído. Os utilizadores podem ver quando as suas despesas estão acima, abaixo ou iguais em comparação com o consumo ao longo do tempo. Quando selecionar a opção **Mostrar/Ocultar Campos** na parte superior do relatório orçamental, pode ver o custo, o orçamento, o custo acumulado ou o orçamento total.

O Azure Cost Management disponibiliza uma funcionalidade semelhante à Cloudyn. O Azure Cost Management é uma solução de gestão de custos nativa do Azure. Ajuda-o a analisar os custos, a criar e a gerir orçamentos, a exportar dados, bem como a analisar e a agir de acordo com as recomendações de otimização para economizar dinheiro. Para obter mais informações sobre orçamentos no Cost Management, veja [Criar e gerir orçamentos](../costs/tutorial-acm-create-budgets.md).

## <a name="create-budgets"></a>Criar orçamentos

Quando cria um orçamento, define-o para o seu ano fiscal e é aplicado a uma entidade específica.

Para criar um orçamento e atribuí-lo a uma entidade:

1. Navegue para **Custos** &gt; **Cost Management** &gt; **Orçamento**.
2. Na página Gestão de Orçamentos, em **Entidades**, selecione a entidade onde quer criar o orçamento.
3. No ano do orçamento, selecione o ano em que quer criar o orçamento.
4. Para cada mês, defina um valor orçamental. Quando tiver terminado, clique em **Guardar**.
Neste exemplo, o orçamento mensal para junho de 2018 está fixado em 135 000 $. O orçamento total do ano é de 1 615 000,00 $.
![Página Criar um orçamento, onde define um orçamento para cada mês](./media/manage-budgets/set-budget.png)


Para importar um ficheiro para o orçamento anual:

1. Em **Ações**, selecione **Exportar** para transferir um modelo CSV vazio para utilizar como base para o orçamento.
2. Preencha o ficheiro CSV com as suas entradas de orçamento e guarde-o localmente.
3. Em **Ações**, selecione **Importar**.
4. Selecione o ficheiro guardado e, em seguida, clique em **OK**.

Para exportar o orçamento concluído como um ficheiro CSV, em **Ações**, selecione **Exportar** para transferir o ficheiro.

## <a name="view-budget-in-reports"></a>Ver o orçamento nos relatórios

Quando estiver concluído, o orçamento é apresentado na maioria dos relatórios de Custos, em **Custos** &gt; **Análise de Custos** e no relatório Custos vs. Orçamento ao Longo do Tempo. Também pode agendar relatórios com base em limiares do orçamento com **Ações**.

Segue-se um exemplo do relatório Análise de Custos. Mostra o orçamento total e o custo por carga de trabalho e tipos de utilização desde o início do ano.

![Exemplo de relatório Análise de Custos com orçamento](./media/manage-budgets/cost-analysis-budget-example.png)

Neste exemplo, assuma que a data atual é 22 de junho. O custo para junho de 2018 é de 71 611,28 $ em comparação com o orçamento mensal de 135 000 $. O custo é muito inferior ao orçamento mensal, porque ainda faltam oito dias de despesas até ao final do mês.

Outra forma de ver o relatório é olhar para o custo acumulado vs. o seu orçamento. Para ver os custos acumulados, em **Mostrar/Ocultar Campos**, selecione **Custos Acumulados** e **Orçamento Total**. Segue-se um exemplo que mostra o custo acumulado desde o início do ano.

![Exemplo de custo acumulado e orçamento total apresentados no relatório Custos vs. Orçamento ao Longo do Tempo](./media/manage-budgets/accumulated-budget.png)

Em algum momento no futuro, o custo acumulado pode ultrapassar o orçamento. Poderá ver isso mais facilmente se alterar a vista do gráfico para o tipo _Linhas_.

![Orçamento apresentado num gráfico de linhas no relatório Custo por Meses](./media/manage-budgets/budget-line.png)

## <a name="create-budget-alerts-for-a-filter"></a>Criar alertas de orçamento para um filtro

No exemplo anterior, pode ver que o custo acumulado se aproximou do orçamento. Pode criar alertas de orçamento automáticos para ser notificado quando as despesas se aproximarem ou ultrapassarem o orçamento. Basicamente, o alerta é um relatório agendado com um limiar. As métricas de limiar de alertas de orçamento incluem:

- Custo restante vs. orçamento, para especificar um limiar de valor de moeda
- Percentagem de custos vs. orçamento, para especificar um limiar de valor de percentagem

Vejamos um exemplo.

No relatório Custos vs. Orçamento ao Longo do Tempo, clique em **Ações** e, em seguida, selecione **Agendar relatório**. No separador Limiar, selecione uma métrica de limiar. Por exemplo, **Percentagem de custos vs. orçamento**. Selecione um tipo de alerta e introduza um valor de percentagem do orçamento. Se quiser ser notificado apenas uma vez, selecione **Número de alertas consecutivos** e, em seguida, escreva _1_. Clique em **Guardar**.

![Criar um alerta de orçamento na caixa Guardar ou Agendar este relatório](./media/manage-budgets/budget-alert.png)

## <a name="next-steps"></a>Passos seguintes

- Se ainda não tiver concluído o primeiro tutorial da Cloudyn, leia-o em [Rever a utilização e os custos](tutorial-review-usage.md).
- Saiba mais sobre os [relatórios disponíveis na Cloudyn](use-reports.md).
