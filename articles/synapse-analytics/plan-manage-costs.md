---
title: Plano de gestão de custos para a Azure Synapse Analytics
description: Saiba como planear e gerir os custos para o Azure Synapse Analytics utilizando a análise de custos no portal Azure.
author: julieMSFT
ms.author: jrasnick
ms.custom: subject-cost-optimization
ms.service: synapse-analytics
ms.topic: how-to
ms.date: 12/09/2020
ms.openlocfilehash: e62d494989c86b4c0eab9cdbd51b68fc49fffe76
ms.sourcegitcommit: 66b0caafd915544f1c658c131eaf4695daba74c8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/18/2020
ms.locfileid: "97681704"
---
# <a name="plan-and-manage-costs-for-azure-synapse-analytics"></a>Planear e gerir os custos da Azure Synapse Analytics

Este artigo descreve como planear e gerir os custos para a Azure Synapse Analytics. Em primeiro lugar, você usa a calculadora de preços Azure para ajudar a planear os custos da Azure Synapse antes de adicionar quaisquer recursos para o serviço para estimar custos. Em seguida, ao adicionar recursos Azure Synapse, reveja os custos estimados.

Depois de ter começado a usar recursos da Azure Synapse, use funcionalidades de Gestão de Custos para definir orçamentos e monitorizar os custos. Também pode rever os custos previstos e identificar tendências de gastos para identificar áreas onde você pode querer agir. Os custos da Azure Synapse são apenas uma parte dos custos mensais na sua conta Azure. Embora este artigo explique como planear e gerir os custos para a Azure Synapse, você é cobrado por todos os serviços e recursos da Azure usados na sua subscrição Azure, incluindo os serviços de terceiros.

## <a name="prerequisites"></a>Pré-requisitos

A análise de custos na Gestão de Custos suporta a maioria dos tipos de conta Azure, mas nem todos. Para ver a lista completa dos tipos de contas suportados, veja [Compreender os dados do Cost Management](https://docs.microsoft.com/azure/cost-management-billing/costs/understand-cost-mgt-data?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn). Para visualizar dados de custos, precisa pelo menos ler o acesso a uma conta Azure. Para obter informações sobre a atribuição de acesso aos dados do Azure Cost Management, veja [Atribuir acesso aos dados](https://docs.microsoft.com/azure/cost-management/assign-access-acm-data?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

## <a name="estimate-costs-before-using-azure-synapse-analytics"></a>Estimativa de custos antes de usar Azure Synapse Analytics

Utilize a [calculadora de preços Azure](https://azure.microsoft.com/pricing/calculator/) para estimar os custos antes de adicionar a Azure Synapse Analytics.

A Azure Synapse tem vários recursos que têm taxas diferentes como visto na estimativa de custos abaixo. 

![Exemplo que mostra o custo estimado na calculadora de preços do Azure](./media/plan-manage-costs/cost-estimate.png)

## <a name="understand-the-full-billing-model-for-azure-synapse-analytics"></a>Compreenda o modelo completo de faturação para a Azure Synapse Analytics

O Azure Synapse funciona na infraestrutura Azure que acumula custos juntamente com a Azure Synapse quando implementa o novo recurso. É importante entender que infraestruturas adicionais podem acumular custos. Tens de gerir esse custo quando fazes alterações aos recursos implantados. 

### <a name="costs-that-typically-accrue-with-azure-synapse-analytics"></a>Custos que normalmente se acumulam com a Azure Synapse Analytics

Quando cria recursos para o Azure Synapse, também são criados recursos para outros serviços Azure. Estas incluem:

- Data Lake Storage Gen2

 ### <a name="costs-might-accrue-after-resource-deletion"></a>Os custos podem acumular-se após a supressão de recursos

Depois de eliminar os recursos da Azure Synapse, os seguintes recursos poderão continuar a existir. Continuam a acumular custos até os eliminares.

- Data Lake Storage Gen2

### <a name="using-monetary-credit-with-azure-synapse"></a>Utilização de Crédito Monetário com Azure Synapse 

Você pode pagar os encargos da Azure Synapse com o seu crédito de compromisso monetário EA. No entanto, não pode usar o crédito de compromisso monetário da EA para pagar os encargos de produtos e serviços de terceiros, incluindo os do Azure Marketplace.

## <a name="review-estimated-costs-in-the-azure-portal"></a>Rever os custos estimados no portal do Azure

À medida que cria recursos para a Azure Synapse Analytics, vê custos estimados. Um espaço de trabalho tem uma piscina SQL sem servidor criada com o espaço de trabalho. A piscina SQL sem servidor não incorrerá em cargas até que faça consultas. Outros recursos, como piscinas SQL dedicadas e piscinas Apache Spark sem servidor, terão de ser criados dentro do espaço de trabalho.

Para criar um <ResourceName> e ver o preço estimado:

1. Navegue para o serviço no portal Azure.
2. Criar o recurso.
3. Reveja o preço estimado indicado no resumo.
4. Termine de criar o recurso.

![Exemplo mostrando custos estimados ao criar um recurso](./media/plan-manage-costs/create-workspace-cost.png)


Se a sua subscrição Azure tiver um limite de gastos, o Azure impede-o de gastar acima do seu valor de crédito. À medida que cria e utiliza recursos Azure, os seus créditos são usados. Quando se atinge o seu limite de crédito, os recursos que implementou são desativados durante o resto desse período de faturação. Não podes alterar o teu limite de crédito, mas podes removê-lo. Para obter mais informações sobre os limites de gastos, consulte [o limite de gastos do Azure.](https://docs.microsoft.com/azure/billing/billing-spending-limit)

## <a name="monitor-costs"></a>Monitorizar os custos

Ao usar os recursos da Azure Synapse, incorre em custos. Os custos da unidade de utilização de recursos Azure variam em intervalos de tempo (segundos, minutos, horas e dias) ou por utilização unitária (bytes, megabytes, e assim por diante.) Assim que começar a utilizar recursos em Azure Synapse, os custos são incorridos e você pode ver os custos na [análise de custos.](https://docs.microsoft.com/azure/cost-management/quick-acm-cost-analysis?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)

Quando utiliza a análise de custos, vê os custos do Azure Synapse Analytics em gráficos e tabelas para diferentes intervalos de tempo. Alguns exemplos são de dia, mês e mês anterior, e ano. Vê-se também os custos contra os orçamentos e os custos previstos. Mudar para vistas mais longas ao longo do tempo pode ajudá-lo a identificar tendências de gastos. E vê onde pode ter ocorrido gastos excessivos. Se criou orçamentos, também pode ver facilmente onde são ultrapassados.

Para visualizar os custos da Azure Synapse na análise de custos:

1. Inicie sessão no portal do Azure.
2. Abra o âmbito, quer a subscrição quer o grupo de recursos, no portal Azure e selecione **a análise de Custos** no menu. Por exemplo, vá a **Subscrições,** selecione uma subscrição da lista e, em seguida, selecione  **análise de custos** no menu. Selecione **Scope** para mudar para um âmbito diferente na análise de custos.
3. Por padrão, o custo dos serviços é mostrado na primeira tabela de donuts. Selecione a área na tabela marcada Azure Synapse.

Os custos mensais reais são mostrados quando inicialmente abre a análise de custos. Aqui está um exemplo mostrando todos os custos mensais de uso.

![Exemplo mostrando custos acumulados para uma subscrição](./media/plan-manage-costs/actual-monthly-costs.png)

- Para reduzir os custos para um único serviço, como a Azure Synapse, selecione **adicionar filtro** e, em seguida, selecione o nome **de Serviço**. Em seguida, selecione **Azure Synapse Analytics**.

Aqui está um exemplo mostrando custos apenas para Azure Synapse.

![Exemplo mostrando custos acumulados para o ServiceName](./media/plan-manage-costs/filtered-monthly-costs.png)

No exemplo anterior, vê-se o custo atual do serviço. Os custos das regiões de Azure (localizações) e do Azure Synapse por grupo de recursos também são mostrados. A partir daqui, você pode explorar os custos por conta própria.

## <a name="create-budgets"></a>Criar orçamentos

Pode criar [orçamentos](https://docs.microsoft.com/azure/cost-management/tutorial-acm-create-budgets?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) para gerir custos e criar [alertas](https://docs.microsoft.com/azure/cost-management/cost-mgt-alerts-monitor-usage-spending?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) que notifiquem automaticamente as partes interessadas das anomalias nos gastos e do risco em fazer gastos excessivos. Os alertas são baseados nos gastos em comparação com os orçamentos e os limites de custos. Orçamentos e alertas são criados para subscrições e grupos de recursos Azure, por isso são úteis como parte de uma estratégia global de monitorização de custos. 

Os orçamentos podem ser criados com filtros para recursos ou serviços específicos em Azure se quiser mais granularidade presente na sua monitorização. Os filtros ajudam a garantir que não crie acidentalmente novos recursos que lhe custem dinheiro adicional. Para saber mais sobre as opções do filtro quando criar um orçamento, consulte [opções de Grupo e filtro.](https://docs.microsoft.com/azure/cost-management-billing/costs/group-filter?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)

## <a name="export-cost-data"></a>Exportar dados de custos

Também pode [exportar os seus dados de custos](https://docs.microsoft.com/azure/cost-management-billing/costs/tutorial-export-acm-data?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) para uma conta de armazenamento. Isto é útil quando você precisa ou outros para fazer análise adicional de dados para custos. Por exemplo, uma equipa de finanças pode analisar os dados usando o Excel ou o Power BI. Pode exportar os seus custos num horário diário, semanal ou mensal e definir um intervalo de datas personalizado. Exportar dados de custos é a forma recomendada de recuperar conjuntos de dados de custos.


## <a name="other-ways-to-manage-and-reduce-costs-for-azure-synapse"></a>Outras formas de gerir e reduzir custos para a Azure Synapse 

### <a name="serverless-sql-pool"></a>Conjunto de SQL sem servidor

Para saber mais sobre os custos para a piscina SQL sem servidor consulte [gestão de custos para piscina SQL sem servidor em Azure Synapse Analytics](./sql/data-processed.md)

### <a name="dedicated-sql-pool"></a>Conjunto de SQL dedicado

Pode controlar os custos de uma piscina SQL dedicada, pausando o recurso quando não é utilizado. Por exemplo, se não utilizar a base de dados durante a noite e aos fins de semana, pode fazer uma pausa durante esses horários e retomá-la durante o dia. Para mais informações consulte [Pause e retomar o cálculo na piscina SQL dedicada através do portal Azure](./sql-data-warehouse/pause-and-resume-compute-portal.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)

<!-- ### Serverless Apache Spark pool -->

### <a name="data-integration---pipelines-and-data-flows"></a>Integração de dados - oleodutos e fluxos de dados 

Para saber mais sobre o custo de integração de dados ver [Plano e gerir custos para a Azure Data Factory](../data-factory/plan-manage-costs.md)

## <a name="next-steps"></a>Passos seguintes

- Saiba [como otimizar o seu investimento em nuvem com a Azure Cost Management.](https://docs.microsoft.com/azure/cost-management-billing/costs/cost-mgt-best-practices?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)
- Saiba mais sobre gestão de custos com [a análise de custos.](https://docs.microsoft.com/azure/cost-management-billing/costs/quick-acm-cost-analysis?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)
- Saiba como [evitar custos inesperados.](https://docs.microsoft.com/azure/cost-management-billing/manage/getting-started?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)
- Faça o curso de aprendizagem guiada [de Gestão](https://docs.microsoft.com/learn/paths/control-spending-manage-bills?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) de Custos.
- Saiba mais sobre planeamento e gestão de custos para [a Azure Machine Learning](../machine-learning/concept-plan-manage-cost.md)