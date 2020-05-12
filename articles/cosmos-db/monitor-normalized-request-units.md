---
title: Monitorize RU/s normalizado para um recipiente Azure Cosmos ou uma conta
description: Saiba como monitorizar o uso normalizado da unidade de pedidos de uma operação em Azure Cosmos DB. Os proprietários de uma conta Azure Cosmos DB podem entender quais as operações que estão a consumir mais unidades de pedido.
ms.service: cosmos-db
ms.topic: conceptual
author: kanshiG
ms.author: govindk
ms.date: 05/10/2020
ms.openlocfilehash: 23001bdaab0732dbeb088ebadefa90a27e622b19
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/12/2020
ms.locfileid: "83118829"
---
# <a name="how-to-monitor-normalized-rus-for-an-azure-cosmos-container-or-an-account"></a>Como monitorizar os RU/s normalizados para um contentor Azure Cosmos ou uma conta

O Azure Monitor para o Azure Cosmos DB fornece uma visão métrica para monitorizar a sua conta e criar dashboards. As métricas De DB Do Azure Cosmos são recolhidas por padrão, esta funcionalidade não requer que ative ou configure explicitamente nada.

A métrica **de consumo de RU normalizada** é usada para ver quão bem saturadas são as réplicas do consumo das unidades de pedido através das gamas-chave da divisória. Azure Cosmos DB distribui a entrada igualmente em todas as divisórias físicas. Esta métrica proporciona uma visão por segundo da utilização máxima de entrada dentro de um conjunto de réplicas. Ao utilizar esta métrica, se vir uma elevada percentagem de utilização de unidades de pedido, deverá aumentar a entrada para satisfazer as necessidades da sua carga de trabalho.

## <a name="what-to-expect-and-do-when-normalized-rus-is-higher"></a>O que esperar e fazer quando ru/s normalizado é maior

Quando o consumo normalizado de RU/s atinge os 100%, o cliente recebe erros de limitação da taxa. O cliente deve respeitar o tempo de espera e a nova tentativa. Se houver um pico curto que atinge 100% de utilização, significa que a entrada na réplica atingiu o seu limite máximo de desempenho. Por exemplo, uma única operação, como um procedimento armazenado que consome todos os RU/s numa réplica, conduzirá a um pequeno aumento no consumo normalizado de RU/s. Nesses casos, não haverá erros de limitação de taxas imediatas se a taxa de pedido for baixa. Isto porque, a Azure Cosmos DB permite que os pedidos cobram mais do que os RU/s previstos para o pedido específico e outros pedidos dentro desse período são limitados.

As métricas do Monitor Azure ajudam-no a encontrar as operações por código de estado utilizando a métrica **Total Requests.** Mais tarde, pode filtrar estes pedidos pelo código de estado 429 e dividi-los por **Tipo de Operação**.

Para encontrar os pedidos que são limitados, a forma recomendada é obter esta informação através de registos de diagnóstico.

Se houver um pico contínuo de consumo de RU/s 100% normalizado ou perto de 100%, é aconselhável aumentar a produção. Pode descobrir quais as operações pesadas e o seu pico de utilização utilizando as métricas de monitor Azure e os registos de monitores Azure.

A métrica **de consumo de RU normalizada** também é usada para ver qual a gama de chaves de partição mais quente em termos de utilização; dando-lhe assim o enviesamento de entrada para uma gama de chaves de partição. Mais tarde, pode acompanhar o registo **do PartitionKeyRUConsumption** nos registos do Monitor Azure para obter informações sobre quais as teclas de partição lógicas quentes em termos de utilização.

## <a name="view-the-normalized-request-unit-consumption-metric"></a>Ver a métrica de consumo da unidade de pedido normalizada

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

2. Selecione **Monitor** a partir da barra de navegação à esquerda e selecione **Métricas**.

   ![Painel de métricas no Monitor Azure](./media/monitor-normalized-request-units/monitor-metrics-blade.png)

3. Do painel de **métricas** > **Selecione um recurso** > escolha a **subscrição**necessária e **o grupo de recursos**. Para o **tipo de Recurso**, selecione contas **Azure Cosmos DB,** escolha uma das suas contas Azure Cosmos existentes e selecione **Apply**.

   ![Escolha uma conta Azure Cosmos para ver métricas](./media/monitor-normalized-request-units/select-cosmos-db-account.png)

4. Em seguida, pode selecionar uma métrica da lista de métricas disponíveis. Pode selecionar métricas específicas para solicitar unidades, armazenamento, latência, disponibilidade, Cassandra, entre outros. Para conhecer em detalhe todas as métricas disponíveis nesta lista, consulte as [Métricas por artigo](monitor-cosmos-db-reference.md) de categoria. Neste exemplo, vamos selecionar a métrica de **consumo de RU normalizada** e **Max** como o valor de agregação.

   Além destes detalhes, também pode selecionar a gama de **tempo** e a **granularidade** do tempo das métricas. No máximo, pode ver métricas nos últimos 30 dias.  Depois de aplicar o filtro, é apresentado um gráfico com base no filtro.

   ![Escolha uma métrica do portal Azure](./media/monitor-normalized-request-units/normalized-request-unit-usage-metric.png)

### <a name="filters-for-normalized-request-unit-consumption"></a>Filtros para consumo normalizado da unidade de pedidos

Também pode filtrar métricas e o gráfico apresentado por um nome de **recolha**específico, Nome de base de **dados,** **PartitionKeyRangeID,** e **Região**. Para filtrar as métricas, selecione **Adicionar filtro** e escolha a propriedade necessária, como O Nome **de Recolha** e o valor correspondente em que está interessado. O gráfico exibe então as unidades de consumo RU normalizadas consumidas para o recipiente durante o período selecionado.  

Pode agrupar métricas utilizando a opção **de divisão Apply.**  

A métrica normalizada de consumo da unidade de pedido para cada recipiente é apresentada como mostrado na seguinte imagem:

![Aplicar filtros à métrica de consumo da unidade de pedido normalizada](./media/monitor-normalized-request-units/normalized-request-unit-usage-filters.png)

## <a name="next-steps"></a>Passos seguintes

* Monitorize os dados do Azure Cosmos DB utilizando [definições](cosmosdb-monitor-resource-logs.md) de diagnóstico em Azure.
* [Audit a Azure Cosmos DB controla operações de aviões](audit-control-plane-logs.md)