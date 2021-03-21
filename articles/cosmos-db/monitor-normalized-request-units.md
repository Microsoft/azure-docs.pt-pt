---
title: Monitorize RU/s normalizados para um recipiente Azure Cosmos ou uma conta
description: Saiba como monitorizar o uso normalizado da unidade de pedido de uma operação em Azure Cosmos DB. Os proprietários de uma conta DB da Azure Cosmos podem entender quais as operações que estão a consumir mais unidades de pedido.
ms.service: cosmos-db
ms.topic: how-to
author: kanshiG
ms.author: govindk
ms.date: 01/07/2021
ms.openlocfilehash: ec82532b54e7834b62fcc03d3ee7de1345a0f546
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98027813"
---
# <a name="how-to-monitor-normalized-rus-for-an-azure-cosmos-container-or-an-account"></a>Como monitorizar RU/s normalizados para um contentor Azure Cosmos ou uma conta
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

O Azure Monitor for Azure Cosmos DB fornece uma visão métrica para monitorizar a sua conta e criar dashboards. As métricas DB do Azure Cosmos são recolhidas por padrão, esta funcionalidade não requer que você ative ou configure nada explicitamente.

A métrica **de consumo RU normalizada** é usada para ver quão bem saturadas são as gamas-chave de partição no que diz respeito ao tráfego. A Azure Cosmos DB distribui a produção igualmente em todas as gamas de chaves de partição. Esta métrica fornece uma visão por segundo da utilização máxima de produção para intervalo de chaves de partição. Utilize esta métrica para calcular a utilização ru/s através da gama de chaves de partição para um determinado recipiente. Ao utilizar esta métrica, se você vê uma alta percentagem de utilização de unidades de pedido em todas as gamas de chaves de partição no monitor Azure, você deve aumentar a produção para atender às necessidades da sua carga de trabalho. Exemplo - A utilização normalizada é definida como a máxima da utilização RU/s em todas as gamas de chaves de partição. Por exemplo, suponha que a sua potência máxima seja de 20.000 RU/s e que tem duas gamas de chaves de partição, P_1 e P_2, cada uma capaz de escalar para 10.000 RU/s. Num dado segundo, se P_1 utilizou 6000 RUs, e P_2 8000 RUs, a utilização normalizada é MAX (6000 RU / 10.000 RU, 8000 RU / 10.000 RU) = 0,8.

## <a name="what-to-expect-and-do-when-normalized-rus-is-higher"></a>O que esperar e fazer quando RU/s normalizado é maior

Quando o consumo normalizado de RU/s atinge 100% para a gama de chaves de partição dada, e se um cliente ainda fizer pedidos nesse intervalo de tempo de 1 segundo para essa gama específica de chaves de partição - recebe um erro limitado de taxa. O cliente deve respeitar o tempo de espera sugerido e voltar a tentar o pedido. O SDK facilita a vida a esta situação, reformulando os tempos pré-configurados, esperando adequadamente.  Não é necessário que veja o erro de limitação da taxa RU apenas porque o RU normalizado atingiu os 100%. Isto porque o RU normalizado é um único valor que representa o uso máximo em todas as gamas de chaves de partição, uma gama de chaves de partição pode estar ocupada, mas as outras gamas de chaves de partição podem servir os pedidos sem problemas. Por exemplo, uma única operação, como um procedimento armazenado que consome todos os RU/s numa gama de chaves de partição, conduzirá a um curto pico no consumo ru/s normalizado. Em tais casos, não haverá erros de limitação de taxa imediatos se a taxa do pedido for baixa ou se forem feitos pedidos a outras partições em diferentes intervalos de chaves de partição. 

As métricas do Azure Monitor ajudam-no a encontrar as operações por código de estado para a API SQL utilizando a métrica **Total de Pedidos.** Posteriormente, pode filtrar estes pedidos pelo código de estado 429 e dividi-los por **Operação Tipo**.  

Para encontrar os pedidos, que são limitados, a forma recomendada é obter esta informação através de registos de diagnóstico.

Se houver um pico contínuo de consumo de RU/s 100% normalizado ou perto de 100% em várias gamas de chaves de partição, é recomendado aumentar a produção. Pode descobrir quais as operações pesadas e o seu uso máximo utilizando as métricas do monitor Azure e registos de diagnóstico do monitor Azure.

Em resumo, a métrica **de consumo de RU normalizada** é usada para ver qual a gama de chaves de partição mais quente em termos de utilização. Então, dá-lhe o enviesamento de produção para um alcance de chave de partição. Mais tarde, pode acompanhar o registo **partitionKeyRUConsuconsumption** nos registos do Azure Monitor para obter informações sobre quais as chaves lógicas de partição que estão quentes em termos de utilização. Isto irá apontar para a mudança na escolha chave da partição, ou na mudança na lógica da aplicação. Para resolver a limitação da taxa, distribua a carga de dados por múltiplas divisórias ou apenas aumente a produção conforme é necessário. 

## <a name="view-the-normalized-request-unit-consumption-metric"></a>Ver a métrica de consumo de unidade de pedido normalizado

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

2. Selecione **Monitor** da barra de navegação à esquerda e selecione **Métricas**.

   :::image type="content" source="./media/monitor-normalized-request-units/monitor-metrics-blade.png" alt-text="Painel de métricas no Monitor Azure":::

3. A partir do painel **métrica >** **Selecione um recurso** > escolha a **subscrição** necessária e o **grupo de recursos**. Para o **tipo de recurso**, selecione contas **DB Azure Cosmos**, escolha uma das suas contas Azure Cosmos existentes e selecione **Apply**.

   :::image type="content" source="./media/monitor-normalized-request-units/select-cosmos-db-account.png" alt-text="Escolha uma conta Azure Cosmos para ver métricas":::

4. Em seguida, pode selecionar uma métrica da lista de métricas disponíveis. Pode selecionar métricas específicas para solicitar unidades, armazenamento, latência, disponibilidade, Cassandra, entre outras. Para saber em detalhe sobre todas as métricas disponíveis nesta lista, consulte as [Métricas por](monitor-cosmos-db-reference.md) artigo de categoria. Neste exemplo, vamos selecionar a métrica **de consumo de RU normalizada** e **Max** como o valor de agregação.

   Além destes detalhes, também pode selecionar a **gama de tempo** e a **granularidade** do tempo das métricas. No máximo, pode ver métricas nos últimos 30 dias.  Depois de aplicar o filtro, é apresentado um gráfico com base no filtro.

   :::image type="content" source="./media/monitor-normalized-request-units/normalized-request-unit-usage-metric.png" alt-text="Escolha uma métrica do portal Azure":::

### <a name="filters-for-normalized-request-unit-consumption"></a>Filtros para consumo de unidade de pedido normalizado

Também pode filtrar métricas e o gráfico apresentado por um Nome de **Coleção** específico, **DataName,** **PartitionKeyRangeID** e **Região.** Para filtrar as métricas, **selecione Adicionar filtro** e escolha o imóvel necessário, como o **CollectionName** e o valor correspondente em que está interessado. O gráfico apresenta então as unidades de consumo RU normalizadas consumidas para o recipiente durante o período selecionado.  

Pode agrupar métricas utilizando a opção **de divisão De aplicar.** Para bases de dados de produção partilhada, a métrica RU normalizada mostra apenas dados na granularidade da base de dados, não mostra qualquer dado por recolha. Assim, para a base de dados de produção partilhada, não verá nenhum dado quando se aplica dividir por nome de recolha.

A métrica de consumo de unidade de pedido normalizado para cada recipiente é apresentada como indicado na seguinte imagem:

:::image type="content" source="./media/monitor-normalized-request-units/normalized-request-unit-usage-filters.png" alt-text="Aplicar filtros à métrica de consumo de unidade de pedido normalizado":::

## <a name="next-steps"></a>Passos seguintes

* Monitorize os dados do DB do Azure Cosmos utilizando [as definições de diagnóstico](cosmosdb-monitor-resource-logs.md) em Azure.
* [Auditoria Azure Cosmos DB operações de avião de controlo](audit-control-plane-logs.md)
