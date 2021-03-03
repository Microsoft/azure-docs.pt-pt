---
title: Plano de gestão de custos para Azure Logic Apps
description: Saiba como planear e gerir custos para apps Azure Logic utilizando a análise de custos no portal Azure
ms.service: logic-apps
ms.reviewer: estfan, logicappspm, azla
ms.topic: how-to
ms.custom: subject-cost-optimization
ms.date: 01/29/2021
ms.openlocfilehash: 44351497ed58c8d49404c094f6800b52186edabb
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101709636"
---
# <a name="plan-and-manage-costs-for-azure-logic-apps"></a>Planear e gerir custos para apps Azure Logic

Este artigo ajuda-o a planear e gerir custos para a Azure Logic Apps. Antes de criar ou adicionar quaisquer recursos usando este serviço, estimure os seus custos usando a calculadora de preços Azure. Depois de começar a utilizar os recursos de Aplicações Lógicas, pode definir orçamentos e monitorizar os custos utilizando [a Azure Cost Management.](../cost-management-billing/cost-management-billing-overview.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) Para identificar áreas onde você pode querer agir, você também pode rever os custos previstos e monitorizar as tendências de gastos.

Tenha em mente que os custos para as Aplicações Lógicas são apenas parte dos custos mensais na sua conta Azure. Embora este artigo explique como estimar e gerir custos para as Aplicações Lógicas, você está cobrado para todos os serviços e recursos Azure que são usados na sua subscrição Azure, incluindo quaisquer serviços de terceiros. Depois de estar familiarizado com os custos de gestão das Aplicações Lógicas, pode aplicar métodos semelhantes para gerir custos para todos os serviços Azure utilizados na sua subscrição.

## <a name="prerequisites"></a>Pré-requisitos

<!--Note for Azure service writer: This section covers prerequisites for the Cost Management's Cost Analysis feature. Add other prerequisites needed for your service after the Cost Management prerequisites. -->

[A Azure Cost Management](../cost-management-billing/cost-management-billing-overview.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) suporta a maioria dos tipos de conta Azure. Para ver todos os tipos de conta suportados, consulte [os dados de Gestão de Custos](../cost-management-billing/costs/understand-cost-mgt-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn). Para ver os dados dos custos, precisa de ter, pelo menos, acesso de leitura na sua conta do Azure.

Para obter informações sobre a atribuição de acesso aos dados do Azure Cost Management, veja [Atribuir acesso aos dados](../cost-management-billing/costs/assign-access-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

<!--Note for Azure service writer: If you have other prerequisites for your service, add them here -->

<a name="understand-billing-model"></a>

## <a name="understand-the-billing-model"></a>Compreenda o modelo de faturação

A Azure Logic Apps funciona na infraestrutura Azure que [acumula custos](https://azure.microsoft.com/pricing/details/logic-apps/) quando implementa novos recursos. Certifique-se de que compreende o [modelo de faturação do serviço De aplicações lógicas juntamente com os recursos Azure relacionados](logic-apps-pricing.md), e gere custos devido a estas dependências quando faz alterações aos recursos implantados.

<a name="typical-costs"></a>

### <a name="costs-that-typically-accrue-with-azure-logic-apps"></a>Custos que normalmente acumulam com Azure Logic Apps

O serviço De aplicações lógicas aplica diferentes modelos de preços, com base nos recursos que cria e utiliza:

* Os recursos de aplicações lógicas que cria e funciona no serviço Multi-inquilino Logic Apps usam um [modelo de preços de consumo.](../logic-apps/logic-apps-pricing.md#consumption-pricing)

* Os recursos de aplicações lógicas que cria e funciona num [ambiente de serviço de integração (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) utilizam um modelo de preços [fixos.](../logic-apps/logic-apps-pricing.md#fixed-pricing)

Aqui estão outros recursos que incorrem em custos quando os cria para uso com aplicações lógicas:

* Uma [conta de integração](../logic-apps/logic-apps-pricing.md#integration-accounts) é um recurso separado que cria e liga a aplicações lógicas para a construção de integrações B2B. As contas de integração utilizam um [modelo de preços fixos](../logic-apps/logic-apps-pricing.md#integration-accounts) em que a taxa se baseia no tipo ou *nível* da conta de integração que utiliza.

* Um [ISE](../logic-apps/logic-apps-pricing.md#fixed-pricing) é um recurso separado que cria como um local de implementação para aplicações lógicas que precisam de acesso direto aos recursos numa rede virtual. As ISEs utilizam um [modelo de preços fixos](../logic-apps/logic-apps-pricing.md#fixed-pricing) onde a taxa se baseia no ISE SKU que cria e outras definições.

* Um [conector personalizado](../logic-apps/logic-apps-pricing.md#consumption-pricing) é um recurso separado que cria para uma API REST que não tem conector pré-construído para utilizar nas suas aplicações lógicas. As execuções personalizadas do conector utilizam um [modelo de preços de consumo,](../logic-apps/logic-apps-pricing.md#consumption-pricing) exceto quando as utiliza num ISE.

* No serviço multi-inquilinos Logic Apps, [a retenção de dados e o consumo de armazenamento](../logic-apps/logic-apps-pricing.md#data-retention) acumulam custos utilizando um modelo de preços [fixos.](../logic-apps/logic-apps-pricing.md#fixed-pricing) Por exemplo, as entradas e saídas do histórico de execução são mantidas no armazenamento dos bastidores, o que difere dos recursos de armazenamento que você cria, gere e tem acesso independentemente a partir da sua aplicação lógica.

  Num ISE, a retenção de dados e o consumo de armazenamento não incorrem em custos.

<a name="costs-after-resource-deletion"></a>

### <a name="costs-might-accrue-after-resource-deletion"></a>Os custos podem acumular-se após a supressão de recursos

<!--Note to Azure service writer: You might need to sync with your product team to identify resources that continue to exist after those ones for your service are deleted. If you're certain that no resources can exist after those for your service are deleted, then omit this section. -->

Depois de eliminar uma aplicação lógica, o serviço De Aplicações Lógicas não criará ou executará novos casos de fluxo de trabalho. No entanto, todas as corridas em curso e pendentes continuam até ao fim. Dependendo do número destas execuções, este processo pode levar algum tempo. Para obter mais informações, consulte [Gerir aplicações lógicas.](manage-logic-apps-with-azure-portal.md#delete-logic-apps)

Se tiver estes recursos depois de eliminar uma aplicação lógica, estes recursos continuam a existir e a acumular custos até os eliminar:

* Recursos Azure que você cria e gere independentemente a partir da app lógica que se conecta a esses recursos, por exemplo, aplicações de função Azure, centros de eventos, redes de eventos, e assim por diante

* Contas de integração

* Ambientes de serviços de integração (ISEs)

  Se [eliminar um ISE,](ise-manage-integration-service-environment.md#delete-ise)a rede virtual Azure associada, sub-redes e outros recursos relacionados continuam a existir. Depois de eliminar o ISE, poderá ter de esperar até um número específico de horas antes de tentar apagar a rede virtual ou sub-redes.

### <a name="using-monetary-credit-with-azure-logic-apps"></a>Utilização de Crédito Monetário com Apps LógicaS Azure

Você pode pagar por Azure Logic Apps cobra com o seu crédito de compromisso monetário EA. No entanto, não pode usar o crédito de compromisso monetário da EA para pagar os encargos com produtos e serviços de terceiros, incluindo os do Azure Marketplace.

<a name="estimate-costs"></a>

## <a name="estimate-costs"></a>Cálculo de custos

Antes de criar recursos com a Azure Logic Apps, calcule os seus custos utilizando a [calculadora de preços Azure](https://azure.microsoft.com/pricing/calculator/). Para mais informações, [reveja o modelo de preços para Azure Logic Apps](../logic-apps/logic-apps-pricing.md).

1. Na página da [calculadora de preços Azure,](https://azure.microsoft.com/pricing/calculator/)a partir do menu esquerdo, selecione **Integration**  >  **Azure Logic Apps**.

   ![Screenshot que mostra a calculadora de preços Azure com "Azure Logic Apps" selecionada.](./media/plan-manage-costs/add-azure-logic-apps-pricing-calculator.png)

1. Percorra a página até ver a calculadora de preços Azure Logic Apps. Nas várias secções para recursos Azure que estão diretamente relacionadas com as Aplicações Lógicas Azure, insira o número de recursos que pretende utilizar e o número de intervalos sobre os quais poderá utilizar esses recursos.

   Esta imagem mostra uma estimativa de custos por exemplo utilizando a calculadora:

   ![Exemplo que mostra o custo estimado na calculadora de preços do Azure](./media/plan-manage-costs/example-logic-apps-pricing-calculator.png)

1. Para atualizar as suas estimativas de custos à medida que cria e utiliza novos recursos relacionados, volte a esta calculadora e atualize esses recursos aqui.

<a name="create-budgets-alerts"></a>

## <a name="create-budgets-and-alerts"></a>Criar orçamentos e alertas

Para ajudá-lo a gerir proativamente os custos da sua conta ou subscrição Azure, pode criar [orçamentos](../cost-management-billing/costs/tutorial-acm-create-budgets.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) e [alertas](../cost-management-billing/costs/cost-mgt-alerts-monitor-usage-spending.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) utilizando o serviço e capacidades [de Gestão de Custos E Faturação Azure.](../cost-management-billing/cost-management-billing-overview.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)  Orçamentos e alertas são criados para subscrições e grupos de recursos Azure, por isso são úteis como parte de uma estratégia global de monitorização de custos.

Com base nas despesas comparadas com os limiares orçamentais e de custos, os alertas notificam automaticamente as partes interessadas sobre anomalias de gastos e riscos de gastos excessivos. Se quiser mais granularidade na sua monitorização, também pode criar orçamentos que utilizem filtros para recursos ou serviços específicos em Azure. Os filtros ajudam a garantir que não criam acidentalmente novos recursos que lhe custam dinheiro extra. Para obter mais informações sobre as opções do filtro, consulte as [opções de Grupo e filtro](../cost-management-billing/costs/group-filter.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

<a name="monitor-costs"></a>

## <a name="monitor-costs"></a>Monitorizar os custos

Os custos da unidade de utilização do recurso variam em intervalos de tempo, tais como segundos, minutos, horas e dias, ou por utilização de unidades, tais como bytes, megabytes, e assim por diante. Alguns exemplos são de dia, mês e mês anterior, e ano. Mudar para vistas mais longas ao longo do tempo pode ajudá-lo a identificar tendências de gastos. Quando utiliza as funcionalidades de análise de custos, pode ver os custos como gráficos e tabelas ao longo de vários intervalos de tempo. Se criou orçamentos e previsões de custos, também pode facilmente descobrir onde os orçamentos são excedidos e os gastos excessivos podem ter ocorrido.

Depois de começar a incorrer em custos para recursos que criam ou começam a usar em Azure, pode rever e monitorizar estes custos desta forma:

* [Monitorize execuções de aplicativos lógicos e consumo de armazenamento](#monitor-billing-metrics) utilizando o Azure Monitor

* Executar [análise de custos](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) utilizando [a Azure Cost Management e Billing](../cost-management-billing/cost-management-billing-overview.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)

<a name="monitor-billing-metrics"></a>

### <a name="monitor-logic-app-executions-and-storage-consumption"></a>Monitorizar execuções de aplicativos de lógica e consumo de armazenamento

Utilizando o Azure Monitor, pode ver estas métricas para uma aplicação lógica específica:

* Execuções de ação a executáveis
* Execuções de gatilhos a cobrar
* Utilização de faturação para execuções de operações nativas
* Utilização de faturação para execuções padrão de conector
* Utilização de faturação para consumo de armazenamento
* Total de execuções faturantes

<a name="execution-storage-metrics"></a>

#### <a name="view-execution-and-storage-consumption-metrics"></a>Ver métricas de execução e armazenamento

1. No portal Azure, encontre e abra a sua aplicação lógica. No menu da sua aplicação lógica, em **Monitorização,** selecione **Metrics**.

1. No painel do lado direito, em **"Chart Title",** na barra métrica, abra a lista **métrica** e selecione a métrica que pretende.

   > [!NOTE]
   > O consumo de armazenamento é medido como o número de unidades de armazenamento (GB) que a sua aplicação lógica utiliza e é faturada. As execuções que utilizam menos de 500 MB no armazenamento podem não aparecer na vista de monitorização, mas ainda são faturadas.

   ![Screenshot que mostra o painel métrica com a lista "Métrica" aberta.](./media/logic-apps-pricing/select-metric.png)

1. No canto superior direito do painel, selecione o período de tempo que deseja.

1. Para visualizar outros dados de consumo de armazenamento, especificamente a entrada de ação e tamanhos de saída no histórico de execução da sua aplicação lógica, [siga estes passos](#view-input-output-sizes).

<a name="view-input-output-sizes"></a>

#### <a name="view-action-input-and-output-sizes-in-run-history"></a>Ver tamanhos de entrada de ação e saída na história da execução

1. No portal Azure, encontre e abra a sua aplicação lógica.

1. No menu da sua aplicação lógica, selecione **Overview**.

1. No painel do lado direito, na **história de Runs,** selecione a execução que tem as entradas e saídas que pretende visualizar.

1. No **âmbito da aplicação Logic,** selecione **'Detalhes de execução'.**

1. Na **aplicação Logic execute detalhes** de detalhes, na tabela de ações, que lista o estado e a duração de cada ação, selecione a ação que pretende ver.

1. No painel de ação da **aplicação Logic,** encontre os tamanhos para as entradas e saídas dessa ação. No **link entradas** e **saídas,** encontre os links para essas entradas e saídas.

   > [!NOTE]
   > Para loops, apenas as ações de alto nível mostram tamanhos para as suas entradas e saídas. Para ações dentro de loops aninhados, entradas e saídas mostram tamanho zero e sem ligações.

<a name="run-cost-analysis"></a>

### <a name="run-cost-analysis-by-using-azure-cost-management-and-billing"></a>Executar análise de custos utilizando a Azure Cost Management e Billing

Para rever os custos do serviço De aplicações lógicas com base num âmbito específico, por exemplo, uma subscrição Azure, pode utilizar as capacidades de análise de [custos](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) em [Azure Cost Management e Billing.](../cost-management-billing/cost-management-billing-overview.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)

1. No portal Azure, abra o âmbito que pretende, como a sua subscrição Azure. A partir do menu esquerdo, em **Gestão de Custos,** selecione **Análise de Custos**.

   Quando abre pela primeira vez o painel de análise de custos, o gráfico superior mostra os custos reais e previstos de utilização em todos os serviços da subscrição para o mês em curso.

   ![Screenshot que mostra o portal Azure e o painel de análise de custos com exemplo para custos reais e previstos numa subscrição.](./media/plan-manage-costs/cost-analysis-total-actual-forecasted-costs.png)

   > [!TIP]
   > Para alterar os âmbitos, no painel de **análise de custos,** a partir da barra de filtros, selecione o filtro **Scope.** No painel **de âmbito Select,** mude para o âmbito que deseja.

   Por baixo, os gráficos de donuts mostram os custos atuais dos serviços Azure, pela região de Azure (localização) e pelo grupo de recursos.

   ![Screenshot que mostra o portal Azure e painel de análise de custos com gráficos de donuts de exemplo para serviços, regiões e grupos de recursos.](./media/plan-manage-costs/cost-analysis-donut-charts.png)

1. Para filtrar o gráfico para uma área específica, como um serviço ou recurso, na barra de filtros, **selecione Adicionar filtro**.

1. A partir da lista do lado esquerdo, selecione o tipo de filtro, por exemplo, **nome de serviço**. A partir da lista do lado direito, selecione o filtro, por exemplo, **aplicações lógicas**. Quando terminar, selecione a marca verde de verificação.

   ![Screenshot que mostra portal Azure e painel de análise de custos com seleções de filtros.](./media/plan-manage-costs/cost-analysis-add-service-name-filter.png)

   Por exemplo, aqui está o resultado para o serviço De Aplicações Lógicas:

   ![Screenshot que mostra portal Azure e painel de análise de custos com resultados filtrados em "apps lógicas".](./media/plan-manage-costs/cost-analysis-total-actual-costs-service.png)

### <a name="export-cost-data"></a>Exportar dados de custos

Quando precisa de fazer mais análises de dados sobre os custos, pode [exportar dados de custos](../cost-management-billing/costs/tutorial-export-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) para uma conta de armazenamento. Por exemplo, uma equipa de finanças pode analisar estes dados usando o Excel ou o Power BI. Você pode exportar seus custos em um horário diário, semanal ou mensal, e definir um intervalo de data personalizado. Exportar dados de custos é a forma recomendada de recuperar conjuntos de dados de custos.

## <a name="other-ways-to-manage-and-reduce-costs"></a>Outras formas de gerir e reduzir custos

<!-- Note to Azure service writer: This section is optional. Other than using the Cost Management methods above, your service probably has other specific ways to minimize costs. For example, you might have best practice advice or specific ways to reduce costs that are specific to your service. If so, try to add that guidance here or at least summarize key points. Try to be as prescriptive as possible. If you have more comprehensive content, add links to your other published articles or sections here.

Add a statement that discusses any recommended settings for your service that might help keep the charges minimal if a service isn't being actively used by the customer. For example: Will turning off a VM help to get no charges for the specific VM resource?

Otherwise, if no other cost-saving recommendations or best practices exist to reduce costs, cut this section.
-->

Para ajudá-lo a reduzir custos nos seus aps lógicos e recursos relacionados, experimente estas opções:

* Se possível, utilize [gatilhos e ações incorporados,](../connectors/apis-list.md#built-in)que custam menos para executar por execução do que [os gatilhos e ações geridos](../connectors/apis-list.md#managed-connectors)do conector .

  Por exemplo, poderá reduzir custos ao aceder a outros recursos utilizando a [ação HTTP](../connectors/connectors-native-http.md) ou chamando uma função que criou utilizando o [serviço Azure Functions](../azure-functions/functions-overview.md) e utilizando a [ação Azure Functions incorporada](../logic-apps/logic-apps-azure-functions.md). No entanto, a utilização de Funções Azure também incorre em custos, por isso certifique-se de que compara as suas opções.

* [Especifique condições precisas](logic-apps-workflow-actions-triggers.md#trigger-conditions) do gatilho para executar um fluxo de trabalho.

  Por exemplo, pode especificar que um gatilho dispara apenas quando o site alvo retorna um erro interno do servidor. Na definição JSON do gatilho, utilize a `conditions` propriedade para especificar uma condição que faz referência ao código de estado do gatilho.

* Se um gatilho tiver uma versão de sondagem e uma versão webhook, experimente a versão webhook, que aguarda que o evento especificado aconteça antes de disparar, em vez de verificar regularmente o evento.

* Ligue para a sua aplicação lógica através de outro serviço para que o gatilho dispare apenas quando o fluxo de trabalho deve funcionar.

  Por exemplo, pode ligar para a sua aplicação lógica a partir de uma função que cria e executou utilizando o serviço Azure Functions. Por exemplo, consulte [aplicações lógicas Call ou Trigger utilizando as Funções Azure e o Azure Service Bus](logic-apps-scenario-function-sb-trigger.md).

* [Desativar aplicações lógicas](manage-logic-apps-with-azure-portal.md#disable-or-enable-logic-apps) que não têm de ser executadas constantemente ou [eliminar aplicações lógicas](manage-logic-apps-with-azure-portal.md#delete-logic-apps) de que já não precisa. Se possível, desative quaisquer outros recursos que não necessite constantemente.

## <a name="next-steps"></a>Passos seguintes

* [Otimizar o investimento na cloud com o Azure Cost Management](../cost-management-billing/costs/cost-mgt-best-practices.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)
* [Gerir custos usando a análise de custos](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)
* [Evitar custos inesperados](../cost-management-billing/understand/analyze-unexpected-charges.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)
* Faça o curso de gestão de [custos](/learn/paths/control-spending-manage-bills?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) guiado