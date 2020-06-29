---
title: Monitorize RU/s normalizados para um recipiente Azure Cosmos ou uma conta
description: Saiba como monitorizar o uso normalizado da unidade de pedido de uma operação em Azure Cosmos DB. Os proprietários de uma conta DB da Azure Cosmos podem entender quais as operações que estão a consumir mais unidades de pedido.
ms.service: cosmos-db
ms.topic: how-to
author: kanshiG
ms.author: govindk
ms.date: 06/25/2020
ms.openlocfilehash: 8709389208ba1320685b1834b20893f08ef33ed7
ms.sourcegitcommit: 1d9f7368fa3dadedcc133e175e5a4ede003a8413
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/27/2020
ms.locfileid: "85482909"
---
# <a name="how-to-monitor-normalized-rus-for-an-azure-cosmos-container-or-an-account"></a>Como monitorizar RU/s normalizados para um contentor Azure Cosmos ou uma conta

O Azure Monitor for Azure Cosmos DB fornece uma visão métrica para monitorizar a sua conta e criar dashboards. As métricas DB do Azure Cosmos são recolhidas por padrão, esta funcionalidade não requer que você ative ou configure nada explicitamente.

A métrica **de consumo RU normalizada** é usada para ver quão bem saturadas são as réplicas no que diz respeito ao consumo de unidades de pedido em todas as gamas de chaves de partição. Azure Cosmos DB distribui a produção igualmente em todas as divisórias físicas. Esta métrica fornece uma visão por segundo da utilização máxima de produção dentro de um conjunto de réplicas. Utilize esta métrica para calcular a utilização do RU/s através de divisórias para um determinado recipiente. Ao utilizar esta métrica, se vir uma elevada percentagem de utilização das unidades de pedido, deverá aumentar a produção para atender às necessidades da sua carga de trabalho.

## <a name="what-to-expect-and-do-when-normalized-rus-is-higher"></a>O que esperar e fazer quando RU/s normalizado é maior

Quando o consumo de RU/s normalizado atinge os 100%, o cliente recebe erros de limitação de taxa. O cliente deve respeitar o tempo de espera e tentar novamente. Se houver um pequeno pico que atinja 100% de utilização, significa que a produção da réplica atingiu o seu limite máximo de desempenho. Por exemplo, uma única operação, como um procedimento armazenado que consome todos os RU/s numa réplica, conduzirá a um curto pico no consumo normalizado de RU/s. Nesses casos, não haverá erros de limitação de taxa imediata se a taxa de pedido for baixa. Isto porque, a Azure Cosmos DB permite que os pedidos cobram mais do que os RU/s previstos para o pedido específico e outros pedidos dentro desse período são limitados.

As métricas do Monitor Azure ajudam-no a encontrar as operações por código de estado utilizando a métrica **Total De Pedidos.** Posteriormente, pode filtrar estes pedidos pelo código de estado 429 e dividi-los por **Operação Tipo**.

Para encontrar os pedidos que são limitados, a maneira recomendada é obter esta informação através de registos de diagnóstico.

Se houver um pico contínuo de consumo de RU/s 100% normalizado ou perto de 100%, recomenda-se aumentar a produção. Pode descobrir quais as operações pesadas e o seu pico de utilização utilizando as métricas do monitor Azure e os registos do monitor Azure.

A métrica **de consumo de RU normalizada** também é usada para ver qual a gama de chaves de partição mais quente em termos de utilização; dando-lhe assim o enviesamento de produção para uma gama de chaves de partição. Mais tarde, pode acompanhar o registo **partitionKeyRUConsuconsumption** nos registos do Azure Monitor para obter informações sobre quais as chaves lógicas de partição que estão quentes em termos de utilização.

## <a name="view-the-normalized-request-unit-consumption-metric"></a>Ver a métrica de consumo de unidade de pedido normalizado

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

2. Selecione **Monitor** da barra de navegação à esquerda e selecione **Métricas**.

   :::image type="content" source="./media/monitor-normalized-request-units/monitor-metrics-blade.png" alt-text="Painel de métricas no Monitor Azure":::

3. A partir do painel **métrica >** **Selecione um recurso** > escolha a **subscrição**necessária e o **grupo de recursos**. Para o **tipo de recurso**, selecione contas **DB Azure Cosmos**, escolha uma das suas contas Azure Cosmos existentes e selecione **Apply**.

   :::image type="content" source="./media/monitor-normalized-request-units/select-cosmos-db-account.png" alt-text="Escolha uma conta Azure Cosmos para ver métricas":::

4. Em seguida, pode selecionar uma métrica da lista de métricas disponíveis. Pode selecionar métricas específicas para solicitar unidades, armazenamento, latência, disponibilidade, Cassandra, entre outras. Para saber em detalhe sobre todas as métricas disponíveis nesta lista, consulte as [Métricas por](monitor-cosmos-db-reference.md) artigo de categoria. Neste exemplo, vamos selecionar a métrica **de consumo de RU normalizada** e **Max** como o valor de agregação.

   Além destes detalhes, também pode selecionar a **gama de tempo** e a **granularidade** do tempo das métricas. No máximo, pode ver métricas nos últimos 30 dias.  Depois de aplicar o filtro, é apresentado um gráfico com base no filtro.

   :::image type="content" source="./media/monitor-normalized-request-units/normalized-request-unit-usage-metric.png" alt-text="Escolha uma métrica do portal Azure":::

### <a name="filters-for-normalized-request-unit-consumption"></a>Filtros para consumo de unidade de pedido normalizado

Também pode filtrar métricas e o gráfico apresentado por um Nome de **Coleção**específico, **DataName,** **PartitionKeyRangeID**e **Região.** Para filtrar as métricas, **selecione Adicionar filtro** e escolha o imóvel necessário, como o **CollectionName** e o valor correspondente em que está interessado. O gráfico apresenta então as unidades de consumo RU normalizadas consumidas para o recipiente durante o período selecionado.  

Pode agrupar métricas utilizando a opção **de divisão De aplicar.**  

A métrica de consumo de unidade de pedido normalizado para cada recipiente é apresentada como indicado na seguinte imagem:

:::image type="content" source="./media/monitor-normalized-request-units/normalized-request-unit-usage-filters.png" alt-text="Aplicar filtros à métrica de consumo de unidade de pedido normalizado":::

## <a name="next-steps"></a>Passos seguintes

* Monitorize os dados do DB do Azure Cosmos utilizando [as definições de diagnóstico](cosmosdb-monitor-resource-logs.md) em Azure.
* [Auditoria Azure Cosmos DB operações de avião de controlo](audit-control-plane-logs.md)
