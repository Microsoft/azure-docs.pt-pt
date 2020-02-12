---
title: Planear e gerir custos para a Azure Cosmos DB
description: Aprenda a planear e gerir custos para o Azure Cosmos DB utilizando a análise de custos no portal Azure.
author: SnehaGunda
ms.author: sngun
ms.custom: subject-cost-optimization
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/02/2020
ms.openlocfilehash: cf259dfa0b00393cf0fee259b199527569ba1792
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/12/2020
ms.locfileid: "77152589"
---
# <a name="plan-and-manage-costs-for-azure-cosmos-db"></a>Planear e gerir custos para a Azure Cosmos DB

Este artigo descreve como pode planear e gerir custos para o Azure Cosmos DB. Em primeiro lugar, usa a calculadora de capacidade saca-do-dia do Azure Cosmos para ajudar a planear custos antes de adicionar quaisquer recursos. Em seguida, à medida que adiciona os recursos Azure, pode rever os custos estimados. Depois de começar a usar recursos Da Azure Cosmos DB, use as funcionalidades de gestão de custos para definir orçamentos e monitorizar os custos. Também pode rever os custos previstos e identificar as tendências de gastos para identificar áreas onde possa querer agir.

Entenda que os custos para o Azure Cosmos DB são apenas uma parte dos custos mensais na sua conta Azure. Se estiver a utilizar outros serviços Azure, é cobrado por todos os serviços e recursos do Azure utilizados na sua subscrição Azure, incluindo os serviços de terceiros. Este artigo explica como planear e gerir custos para a Azure Cosmos DB. Depois de conhecer os custos de gestão do Azure Cosmos DB, pode aplicar métodos semelhantes para gerir os custos de todos os serviços Azure utilizados na sua subscrição.

## <a name="prerequisites"></a>Pré-requisitos

A análise de custo dá suporte a diferentes tipos de tipos de conta do Azure. Para ver a lista completa dos tipos de conta suportados, consulte [compreender os dados da Cost Management](../cost-management-billing/costs/understand-cost-mgt-data.md). Para exibir dados de custo, você precisa de pelo menos acesso de leitura para sua conta do Azure. Para obter informações sobre a atribuição de acesso aos dados da Azure Cost Management, consulte [O acesso atribuído aos dados.](../cost-management-billing/costs/assign-access-acm-data.md)

## <a name="review-estimated-costs-with-capacity-calculator"></a>Rever os custos estimados com calculadora de capacidade

Utilize a calculadora de [capacidade saca-do-dia do Azure Cosmos](https://cosmos.azure.com/capacitycalculator/) para estimar os custos antes de criar os recursos numa conta Azure Cosmos. A calculadora de capacidade é usada para obter uma estimativa da entrada e custo necessários da sua carga de trabalho. Configurar as suas bases de dados e contentores Azure Cosmos com a quantidade certa de entrada aprovisionada, ou Unidades de [Pedido (RU/s),](request-units.md)para a sua carga de trabalho é essencial para otimizar o custo e o desempenho. Tem de inserir detalhes como o tipo DePi, número de regiões, tamanho do item, pedidos de leitura/escrita por segundo, dados totais armazenados para obter uma estimativa de custos. Para saber mais sobre a calculadora de capacidade, consulte o artigo de [estimativa.](estimate-ru-with-capacity-planner.md)

A imagem seguinte mostra a estimativa de saída e de custo utilizando a calculadora de capacidade:

![Estimativa de custos na calculadora de capacidade do Azure Cosmos DB](./media/plan-manage-costs/capacity-calculator-cost-estimate.png)

## <a name="review-estimated-costs-from-the-azure-portal"></a>Rever os custos estimados do portal Azure

Ao criar recursos Da Azure Cosmos DB a partir do portal Azure, pode ver os custos estimados. Utilize os seguintes passos para rever a estimativa de custos:

1. Assine no portal Azure e navegue para a sua conta Azure Cosmos.
1. Vá ao Explorador de **Dados.**
1. Crie um novo recipiente, como um recipiente de gráficos.
1. Insere a entrada necessária para a sua carga de trabalho, como 400 RU/s. Depois de inserir o valor de entrada, pode ver a estimativa de preços como mostrado na seguinte imagem:

   ![Estimativa de custos no portal Azure](./media/plan-manage-costs/cost-estimate-portal.png)

Se a sua subscrição Azure tiver um limite de gastos, o Azure impede-o de gastar acima do seu valor de crédito. À medida que cria e utiliza recursos Azure, os seus créditos são usados. Quando atingir o seu limite de crédito, os recursos que implementou são desativados para o resto do período de faturação. Não pode alterar o seu limite de crédito, mas pode removê-lo. Para obter mais informações sobre limites de gastos, consulte o [limite de gastos do Azure.](../billing/billing-spending-limit.md)

## <a name="use-budgets-and-cost-alerts"></a>Utilizar orçamentos e alertas de custo

Pode criar [orçamentos](../cost-management/tutorial-acm-create-budgets.md) para gerir custos e criar alertas que notificam automaticamente as partes interessadas de gastar anomalias e riscos de gastos excessivos. Os alertas são baseados nos gastos em comparação com os orçamentos e os limites de custos. Orçamentos e alertas são criados para subscrições do Azure e grupos de recursos, por isso são úteis como parte de uma estratégia global de monitorização de custos. No entanto, podem ter uma funcionalidade limitada para gerir custos individuais de serviço Azure, como o custo do Azure Cosmos DB, porque são projetados para rastrear custos a um nível mais elevado.

## <a name="monitor-costs"></a>Monitorizar os custos

Ao utilizar recursos com a Azure Cosmos DB, incorre em custos. Os custos da unidade de utilização dos recursos variam por intervalos de tempo (segundos, minutos, horas e dias) ou por pedido de utilização da unidade. Assim que o uso do Azure Cosmos DB começa, os custos são incorridos e você pode vê-los no painel de análise de [custos](../cost-management/quick-acm-cost-analysis.md) no portal Azure.

Quando utilizar a análise de custos, pode ver os custos do Azure Cosmos DB em gráficos e tabelas para diferentes intervalos de tempo. Alguns exemplos são de dia, corrente, mês anterior e ano. Pode também ver os custos com orçamentos e os custos previstos. Mudar para vistas mais longas ao longo do tempo pode ajudá-lo a identificar as tendências de gastos e ver onde poderia ter ocorrido gastos excessivos. Se criou orçamentos, também pode facilmente ver onde foram ultrapassados. Para ver os custos da DB da Azure Cosmos na análise de custos:

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).

1. Abra a janela **Cost Management + Billing,** selecione Gestão de **Custos** do menu e, em seguida, selecione **análise de custos**. Em seguida, pode alterar a margem de manobra para uma subscrição específica a partir do **dropdown** scope.

1. Por padrão, o custo para todos os serviços é mostrado na primeira tabela de donuts. Selecione a área na tabela com a etiqueta "Azure Cosmos DB".

1. Para reduzir os custos para um único serviço, como o Azure Cosmos DB, selecione **Adicionar filtro** e, em seguida, selecione **o nome de serviço**. Então, escolha **Azure Cosmos DB** da lista. Aqui está um exemplo mostrando custos apenas para Azure Cosmos DB:
 
   ![Monitorizar os custos com painel de análise de custos](./media/plan-manage-costs/cost-analysis-pane.png)

No exemplo anterior, vê-se o custo atual do Azure Cosmos DB para o mês de fevereiro. Os gráficos também contêm custos de DB Azure Cosmos por localização e por grupo de recursos.

## <a name="next-steps"></a>Passos seguintes

Veja os seguintes artigos para saber mais sobre como funcionam os preços em Azure Cosmos DB:

* [Modelo de preços no Azure Cosmos DB](how-pricing-works.md)
* [Otimizar o débito aprovisionado no Azure Cosmos DB](optimize-cost-throughput.md)
* [Otimizar o custo das consultas no Azure Cosmos DB](optimize-cost-queries.md)
* [Otimizar o custo de armazenamento em Azure Cosmos DB](optimize-cost-storage.md)