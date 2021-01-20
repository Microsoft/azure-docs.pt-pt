---
title: Planear e gerir os custos da Azure Cosmos DB
description: Aprenda a planear e gerir os custos para a Azure Cosmos DB utilizando a análise de custos no portal Azure.
author: SnehaGunda
ms.author: sngun
ms.custom: subject-cost-optimization
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/19/2020
ms.openlocfilehash: 2bea2324817986654de6689a2be15d0cbf999b38
ms.sourcegitcommit: fc401c220eaa40f6b3c8344db84b801aa9ff7185
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/20/2021
ms.locfileid: "98602142"
---
# <a name="plan-and-manage-costs-for-azure-cosmos-db"></a>Planear e gerir os custos da Azure Cosmos DB
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Este artigo descreve como pode planear e gerir custos para a Azure Cosmos DB. Em primeiro lugar, utiliza a calculadora de capacidade de DB da Azure Cosmos para estimar o seu custo de carga de trabalho antes de criar quaisquer recursos. Mais tarde, pode rever o custo estimado e começar a criar os seus recursos.

Depois de ter começado a usar os recursos DB da Azure Cosmos, use as funcionalidades de Gestão de Custos para definir orçamentos e monitorizar os custos. Também pode rever os custos previstos e identificar tendências de gastos para identificar áreas onde você pode querer agir. Os custos da Azure Cosmos DB são apenas uma parte dos custos mensais na sua conta Azure. Embora este artigo explique como planear e gerir os custos para a Azure Cosmos DB, você é cobrado por todos os serviços e recursos da Azure usados na sua assinatura Azure, incluindo os serviços de terceiros.

## <a name="prerequisites"></a>Pré-requisitos

### <a name="provisioned-throughput-or-serverless"></a>Produção provisida ou sem servidor

A Azure Cosmos DB suporta dois tipos de modos de capacidade: [produção a provisionada](set-throughput.md) e [sem servidor](serverless.md). A forma como é cobrado pelo seu uso DB Azure Cosmos varia muito entre estes dois modos, por isso é importante escolher o que funciona melhor para a sua carga de trabalho. Veja como escolher entre o produto [de produção previsto e](throughput-serverless.md) o artigo sem servidor para obter orientações e recomendações sobre como fazer esta escolha.

### <a name="cost-analysis"></a>Análise de custos

A análise de custos na Gestão de Custos suporta a maioria dos tipos de conta Azure, mas nem todos. Para ver a lista completa dos tipos de contas suportados, veja [Compreender os dados do Cost Management](../cost-management-billing/costs/understand-cost-mgt-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn). Para visualizar dados de custos, precisa pelo menos ler o acesso a uma conta Azure. Para obter informações sobre a atribuição de acesso aos dados do Azure Cost Management, veja [Atribuir acesso aos dados](../cost-management-billing/costs/assign-access-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

## <a name="estimating-provisioned-throughput-costs-before-using-azure-cosmos-db"></a>Estimando os custos de produção previstos antes de utilizar o Azure Cosmos DB

Se planeia utilizar o Azure Cosmos DB no modo de produção a provisionado, utilize a [calculadora de capacidade de DB Azure Cosmos](https://cosmos.azure.com/capacitycalculator/) para estimar os custos antes de criar os recursos numa conta Azure Cosmos. A calculadora de capacidade é usada para obter uma estimativa do rendimento e custo necessários da sua carga de trabalho. Configurar as suas bases de dados e contentores Azure Cosmos com a quantidade certa de produção provisida, ou [Unidades de Pedido (RU/s)](request-units.md), pois a sua carga de trabalho é essencial para otimizar o custo e o desempenho. Tem de inserir detalhes como o tipo API, o número de regiões, o tamanho do item, os pedidos de leitura/escrita por segundo, os dados totais armazenados para obter uma estimativa de custo. Para saber mais sobre a calculadora de capacidade, consulte o artigo [de estimativa.](estimate-ru-with-capacity-planner.md)

A imagem que se segue mostra a estimativa de produção e custos utilizando a calculadora de capacidade:

:::image type="content" source="./media/plan-manage-costs/capacity-calculator-cost-estimate.png" alt-text="Estimativa de custos na calculadora de capacidade da Azure Cosmos DB":::

## <a name="estimating-serverless-costs-before-using-azure-cosmos-db"></a><a id="estimating-serverless-costs"></a> Estimar custos sem servidor antes de usar o Azure Cosmos DB

Se planeia utilizar o Azure Cosmos DB em modo sem servidor, tem de estimar quantas [Unidades](request-units.md) de Pedido e GB de armazenamento poderá consumir mensalmente. Pode estimar o montante exigido das Unidades de Pedido, avaliando o número de operações de base de dados que seriam emitidas num mês, e multiplicar o seu valor pelo custo RU correspondente. As seguintes listas de tabelas estimam os encargos ru estimados para operações comuns de base de dados:

| Operação | Custo estimado | Notas |
| --- | --- | --- |
| Criar um item | 5 RUs | Custo médio de um item de 1 KB com menos de 5 propriedades para indexar |
| Atualizar um item | 10 RUs | Custo médio de um item de 1 KB com menos de 5 propriedades para indexar |
| Leia um item individual pela sua chave de ID e partição (leitura de ponto) | 1 RU | Custo médio de um item KB de 1 KB |
| Eliminar um item | 5 RUs | |
| Executar uma consulta | 10 RUs | Custo médio para uma consulta que tira o máximo partido da [indexação](index-overview.md) e devolve 100 resultados ou menos |

> [!IMPORTANT] 
> Preste atenção às notas da tabela acima. Para uma estimativa mais precisa dos custos reais das suas operações, pode utilizar o [Emulador Azure Cosmos DB](local-emulator.md) e [medir o custo exato da RU das suas operações.](find-request-unit-charge.md) Embora o Emulador DB Azure Cosmos não suporte sem servidor, reporta uma taxa RU padrão para operações de base de dados e pode ser usado para esta estimativa.

Uma vez calculado o número total de Unidades de Pedido e GB de armazenamento que provavelmente consumirá ao longo de um mês, a seguinte fórmula devolverá a sua estimativa **de custos: ([Número de unidades de pedido] / 1.000.000 * $0,25) + ([GB de armazenamento] * $0,25)**.

> [!NOTE]
> Os custos apresentados no exemplo anterior são apenas para fins de demonstração. Consulte [a página de preços](https://azure.microsoft.com/pricing/details/cosmos-db/) para obter as últimas informações sobre preços.

## <a name="review-estimated-costs-in-the-azure-portal"></a>Rever os custos estimados no portal do Azure

À medida que começa a usar os recursos DB da Azure Cosmos a partir do portal Azure, você pode ver os custos estimados. Utilize as seguintes medidas para rever a estimativa de custos:

1. Inscreva-se no portal Azure e navegue para a sua conta Azure Cosmos.
1. Vá à secção **Geral.**
1. Verifique a tabela **de custos** na parte inferior. Este gráfico mostra uma estimativa do seu custo atual durante um período de tempo configurável:
1. Criar um novo recipiente, como um recipiente de gráficos.
1. Insira a produção necessária para a sua carga de trabalho, como 400 RU/s. Depois de introduzir o valor de produção, pode ver a estimativa de preços como mostrado na imagem seguinte:

   :::image type="content" source="./media/plan-manage-costs/cost-estimate-portal.png" alt-text="Estimativa de custos no portal Azure":::

Se a sua subscrição Azure tiver um limite de gastos, o Azure impede-o de gastar acima do seu valor de crédito. À medida que cria e utiliza recursos Azure, os seus créditos são usados. Quando se atinge o seu limite de crédito, os recursos que implementou são desativados durante o resto desse período de faturação. Não podes alterar o teu limite de crédito, mas podes removê-lo. Para obter mais informações sobre os limites de gastos, consulte [o limite de gastos do Azure.](../cost-management-billing/manage/spending-limit.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)

Você pode pagar por taxas de DB Azure Cosmos com o seu crédito Azure Prepayment (anteriormente chamado de compromisso monetário). No entanto, não pode usar o crédito Azure Prepayment para pagar os custos de produtos e serviços de terceiros, incluindo os do Azure Marketplace.

## <a name="monitor-costs"></a>Monitorizar os custos

À medida que usa recursos com a Azure Cosmos DB, incorre em custos. Os custos da unidade de utilização do recurso variam por intervalos de tempo (segundos, minutos, horas e dias) ou por utilização da unidade de pedido. Assim que a utilização do Azure Cosmos DB começa, os custos são incorridos e você pode vê-los no painel de análise de [custos](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) no portal Azure.

Quando utiliza a análise de custos, pode ver os custos do Azure Cosmos DB em gráficos e tabelas para diferentes intervalos de tempo. Alguns exemplos são de dia, corrente, mês anterior e ano. Também é possível ver os custos com os orçamentos e os custos previstos. Mudar para vistas mais longas ao longo do tempo pode ajudá-lo a identificar tendências de gastos e ver onde poderia ter ocorrido gastos excessivos. Se criou orçamentos, também pode facilmente ver onde excederam. 

Para ver os custos da Azure Cosmos DB na análise de custos:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

1. Abra o âmbito no portal Azure e selecione **análise de custos** no menu. Por exemplo, vá a **Subscrições,** selecione uma subscrição da lista e, em seguida, selecione  **análise de custos** no menu. Selecione **Scope** para mudar para um âmbito diferente na análise de custos.

1. Por padrão, o custo de todos os serviços é mostrado na primeira tabela de donuts. Selecione a área na tabela com o nome "Azure Cosmos DB".

1. Para reduzir os custos para um único serviço como Azure Cosmos DB, selecione **adicionar filtro** e, em seguida, selecione o nome **de Serviço**. Então, escolha **Azure Cosmos DB** da lista. Aqui está um exemplo mostrando custos apenas para Azure Cosmos DB:
 
   :::image type="content" source="./media/plan-manage-costs/cost-analysis-pane.png" alt-text="Monitorizar os custos com o painel de análise de custos":::

No exemplo anterior, vê-se o custo atual da Azure Cosmos DB para o mês de fevereiro. Os gráficos também contêm custos DB Azure Cosmos por localização e por grupo de recursos.

## <a name="create-budgets"></a>Criar orçamentos

Pode criar [orçamentos](../cost-management-billing/costs/tutorial-acm-create-budgets.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) para gerir custos e criar [alertas](../cost-management-billing/costs/cost-mgt-alerts-monitor-usage-spending.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) que notifiquem automaticamente as partes interessadas das anomalias nos gastos e do risco em fazer gastos excessivos. Os alertas são baseados nos gastos em comparação com os orçamentos e os limites de custos. Orçamentos e alertas são criados para subscrições e grupos de recursos Azure, por isso são úteis como parte de uma estratégia global de monitorização de custos. 

Os orçamentos podem ser criados com filtros para recursos ou serviços específicos em Azure se quiser mais granularidade presente na sua monitorização. Os filtros ajudam a garantir que não crie acidentalmente novos recursos que lhe custem dinheiro adicional. Para saber mais sobre as opções do filtro quando criar um orçamento, consulte [opções de Grupo e filtro](../cost-management-billing/costs/group-filter.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

## <a name="export-cost-data"></a>Exportar dados de custos

Também pode [exportar os seus dados de custos](../cost-management-billing/costs/tutorial-export-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) para uma conta de armazenamento. Isto é útil quando você precisa ou outros para fazer análise adicional de dados para custos. Por exemplo, uma equipa de finanças pode analisar os dados usando o Excel ou o Power BI. Pode exportar os seus custos num horário diário, semanal ou mensal e definir um intervalo de datas personalizado. Exportar dados de custos é a forma recomendada de recuperar conjuntos de dados de custos.

## <a name="next-steps"></a>Passos seguintes

Veja os seguintes artigos para saber mais sobre como os preços funcionam na Azure Cosmos DB:

* [Modelo de preços no Azure Cosmos DB](how-pricing-works.md)
* [Otimizar o débito aprovisionado no Azure Cosmos DB](optimize-cost-throughput.md)
* [Otimizar o custo das consultas no Azure Cosmos DB](./optimize-cost-reads-writes.md)
* [Otimizar o custo de armazenamento em Azure Cosmos DB](optimize-cost-storage.md)
* Saiba [como otimizar o seu investimento em nuvem com a Azure Cost Management.](../cost-management-billing/costs/cost-mgt-best-practices.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)
* Saiba mais sobre gestão de custos com [a análise de custos.](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)
* Saiba como [evitar custos inesperados.](../cost-management-billing/cost-management-billing-overview.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)
* Faça o curso de aprendizagem guiada [de Gestão](/learn/paths/control-spending-manage-bills?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) de Custos.