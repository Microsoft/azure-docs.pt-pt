---
title: Planear e gerir os custos da Azure Cosmos DB
description: Aprenda a planear e gerir os custos para a Azure Cosmos DB utilizando a análise de custos no portal Azure.
author: SnehaGunda
ms.author: sngun
ms.custom: subject-cost-optimization
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/19/2020
ms.openlocfilehash: 7f0a8fcb841399eb910f5f043cc75ddad037ee30
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88606881"
---
# <a name="plan-and-manage-costs-for-azure-cosmos-db"></a>Planear e gerir os custos da Azure Cosmos DB

Este artigo descreve como pode planear e gerir custos para a Azure Cosmos DB:

- Calcule qual será o seu custo antes de criar quaisquer recursos
- Reveja os custos estimados à medida que começa a utilizar os seus recursos
- Utilize as funcionalidades de gestão de custos para definir orçamentos e monitorizar os custos
- Reveja os custos previstos e identifique as tendências de gastos para revelar áreas onde você pode querer agir

Entenda que os custos para a Azure Cosmos DB são apenas uma parte dos custos mensais na sua conta Azure. Se estiver a utilizar outros serviços Azure, é cobrado por todos os serviços e recursos da Azure utilizados na sua subscrição Azure, incluindo os serviços de terceiros. Este artigo explica como planear e gerir os custos para a Azure Cosmos DB. Depois de estar familiarizado com os custos de gestão da Azure Cosmos DB, pode aplicar métodos semelhantes para gerir custos para todos os serviços Azure utilizados na sua subscrição.

## <a name="prerequisites"></a>Pré-requisitos

A análise de custos suporta diferentes tipos de contas do Azure. Para ver a lista completa dos tipos de contas suportados, veja [Compreender os dados do Cost Management](../cost-management-billing/costs/understand-cost-mgt-data.md). Para ver os dados dos custos, precisa de ter, pelo menos, acesso de leitura na sua conta do Azure. Para obter informações sobre a atribuição de acesso aos dados do Azure Cost Management, veja [Atribuir acesso aos dados](../cost-management-billing/costs/assign-access-acm-data.md).

## <a name="provisioned-throughput-or-serverless"></a>Produção provisida ou sem servidor

A Azure Cosmos DB suporta dois tipos de modos de capacidade: [produção a provisionada](set-throughput.md) e [sem servidor](serverless.md). A forma como é cobrado pelo seu uso DB Azure Cosmos varia muito entre estes dois modos, por isso é importante escolher o que funciona melhor para a sua carga de trabalho. Veja como escolher entre o produto [de produção previsto e](throughput-serverless.md) o artigo sem servidor para obter orientações e recomendações sobre como fazer esta escolha.

## <a name="estimating-provisioned-throughput-costs-with-capacity-calculator"></a>Estimativa dos custos de produção previstos com calculadora de capacidade

Se planeia utilizar o Azure Cosmos DB no modo de produção a provisionado, utilize a [calculadora de capacidade de DB Azure Cosmos](https://cosmos.azure.com/capacitycalculator/) para estimar os custos antes de criar os recursos numa conta Azure Cosmos. A calculadora de capacidade é usada para obter uma estimativa do rendimento e custo necessários da sua carga de trabalho. Configurar as suas bases de dados e contentores Azure Cosmos com a quantidade certa de produção provisida, ou [Unidades de Pedido (RU/s)](request-units.md), pois a sua carga de trabalho é essencial para otimizar o custo e o desempenho. Tem de inserir detalhes como o tipo API, o número de regiões, o tamanho do item, os pedidos de leitura/escrita por segundo, os dados totais armazenados para obter uma estimativa de custo. Para saber mais sobre a calculadora de capacidade, consulte o artigo [de estimativa.](estimate-ru-with-capacity-planner.md)

A imagem que se segue mostra a estimativa de produção e custos utilizando a calculadora de capacidade:

:::image type="content" source="./media/plan-manage-costs/capacity-calculator-cost-estimate.png" alt-text="Estimativa de custos na calculadora de capacidade da Azure Cosmos DB":::

## <a name="estimating-serverless-costs"></a>Estimar custos sem servidor

Se planeia utilizar o Azure Cosmos DB em modo sem servidor, tem de estimar quantas [Unidades](request-units.md) de Pedido e GB de armazenamento poderá consumir mensalmente. Pode estimar o montante exigido das Unidades de Pedido, avaliando o número de operações de base de dados que seriam emitidas num mês, e multiplicar o seu valor pelo custo RU correspondente. As seguintes listas de tabelas estimam os encargos ru estimados para operações comuns de base de dados:

| Operação | Custo estimado | Notas |
| --- | --- | --- |
| Criar um item | 5 RUs | Custo médio de um item de 1 KB com menos de 5 propriedades para indexar |
| Atualizar um item | 10 RUs | Custo médio de um item de 1 KB com menos de 5 propriedades para indexar |
| Leia um item individual pela sua chave de ID e partição (leitura de ponto) | 1 RU | Custo médio de um item KB de 1 KB |
| Eliminar um item | 5 RUs | |
| Executar uma consulta | 10 RUs | Custo médio para uma consulta que tira o máximo partido da [indexação](index-overview.md) e devolve 100 resultados ou menos |

> [!IMPORTANT] 
> Preste atenção às notas da tabela acima. Para uma estimativa mais precisa dos custos reais das suas operações, pode utilizar o [Emulador Azure Cosmos](local-emulator.md) e [medir o custo exato da RU das suas operações.](find-request-unit-charge.md) Embora o Emulador Azure Cosmos não suporte sem servidor, reporta uma taxa RU padrão para operações de base de dados e pode ser usado para esta estimativa.

Uma vez calculado o número total de Unidades de Pedido e GB de armazenamento que provavelmente consumirá ao longo de um mês, a seguinte fórmula devolverá a sua estimativa **de custos: ([Número de unidades de pedido] / 1.000.000 * $0,25) + ([GB de armazenamento] * $0,25)**.

> [!NOTE]
> Os custos apresentados no exemplo anterior são apenas para fins de demonstração. Consulte [a página de preços](https://azure.microsoft.com/pricing/details/cosmos-db/) para obter as últimas informações sobre preços.

## <a name="review-estimated-costs-from-the-azure-portal"></a>Reveja os custos estimados do portal Azure

À medida que começa a usar os recursos DB da Azure Cosmos a partir do portal Azure, você pode ver os custos estimados. Utilize as seguintes medidas para rever a estimativa de custos:

1. Inscreva-se no portal Azure e navegue para a sua conta Azure Cosmos.
1. Vá à secção **Geral.**
1. Verifique a tabela **de custos** na parte inferior. Este gráfico mostra uma estimativa do seu custo atual durante um período de tempo configurável:
1. Criar um novo recipiente, como um recipiente de gráficos.
1. Insira a produção necessária para a sua carga de trabalho, como 400 RU/s. Depois de introduzir o valor de produção, pode ver a estimativa de preços como mostrado na imagem seguinte:

   :::image type="content" source="./media/plan-manage-costs/cost-estimate-portal.png" alt-text="Estimativa de custos na calculadora de capacidade da Azure Cosmos DB":::

## <a name="use-budgets-and-cost-alerts"></a>Utilizar orçamentos e alertas de custo

Pode criar [orçamentos](../cost-management/tutorial-acm-create-budgets.md) para gerir custos e criar alertas que notifiquem automaticamente as partes interessadas das anomalias nos gastos e do risco em fazer gastos excessivos. Os alertas são baseados nos gastos em comparação com os orçamentos e os limites de custos. Orçamentos e alertas são criados para subscrições e grupos de recursos Azure, por isso são úteis como parte de uma estratégia global de monitorização de custos. No entanto, podem ter uma funcionalidade limitada para gerir os custos individuais do serviço Azure, como o custo da Azure Cosmos DB, porque são projetados para controlar os custos a um nível mais elevado.

Se a sua subscrição Azure tiver um limite de gastos, o Azure impede-o de gastar acima do seu valor de crédito. À medida que cria e utiliza recursos Azure, os seus créditos são usados. Quando atingir o seu limite de crédito, os recursos que implementou são desativados durante o resto desse período de faturação. Não podes alterar o teu limite de crédito, mas podes removê-lo. Para obter mais informações sobre os limites de gastos, consulte [o limite de gastos do Azure.](../billing/billing-spending-limit.md)

## <a name="monitor-costs"></a>Monitorizar os custos

À medida que usa recursos com a Azure Cosmos DB, incorre em custos. Os custos da unidade de utilização do recurso variam por intervalos de tempo (segundos, minutos, horas e dias) ou por utilização da unidade de pedido. Assim que a utilização do Azure Cosmos DB começa, os custos são incorridos e você pode vê-los no painel de análise de [custos](../cost-management/quick-acm-cost-analysis.md) no portal Azure.

Quando utiliza a análise de custos, pode ver os custos do Azure Cosmos DB em gráficos e tabelas para diferentes intervalos de tempo. Alguns exemplos são de dia, corrente, mês anterior e ano. Também é possível ver os custos com os orçamentos e os custos previstos. Mudar para vistas mais longas ao longo do tempo pode ajudá-lo a identificar tendências de gastos e ver onde poderia ter ocorrido gastos excessivos. Se criou orçamentos, também pode facilmente ver onde excederam. Para ver os custos da Azure Cosmos DB na análise de custos:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

1. Abra a janela **De Gestão de Custos + Faturação,** selecione **a gestão** de custos do menu e, em seguida, selecione **análise de custos**. Em seguida, pode alterar o âmbito de uma subscrição específica a partir do **dropdown** scope.

1. Por padrão, o custo de todos os serviços é mostrado na primeira tabela de donuts. Selecione a área na tabela com o nome "Azure Cosmos DB".

1. Para reduzir os custos para um único serviço como Azure Cosmos DB, selecione **adicionar filtro** e, em seguida, selecione o nome **de Serviço**. Então, escolha **Azure Cosmos DB** da lista. Aqui está um exemplo mostrando custos apenas para Azure Cosmos DB:
 
   :::image type="content" source="./media/plan-manage-costs/cost-analysis-pane.png" alt-text="Estimativa de custos na calculadora de capacidade da Azure Cosmos DB":::

No exemplo anterior, vê-se o custo atual da Azure Cosmos DB para o mês de fevereiro. Os gráficos também contêm custos DB Azure Cosmos por localização e por grupo de recursos.

## <a name="next-steps"></a>Passos seguintes

Veja os seguintes artigos para saber mais sobre como os preços funcionam na Azure Cosmos DB:

* [Modelo de preços no Azure Cosmos DB](how-pricing-works.md)
* [Otimizar o débito aprovisionado no Azure Cosmos DB](optimize-cost-throughput.md)
* [Otimizar o custo das consultas no Azure Cosmos DB](optimize-cost-queries.md)
* [Otimizar o custo de armazenamento em Azure Cosmos DB](optimize-cost-storage.md)