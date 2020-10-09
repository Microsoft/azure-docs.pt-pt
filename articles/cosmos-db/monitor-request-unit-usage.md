---
title: Monitorize o uso de uma operação em Azure Cosmos DB
description: Saiba como monitorizar a produção ou solicitar o uso da unidade de uma operação em Azure Cosmos DB. Os proprietários de uma conta DB da Azure Cosmos podem entender quais as operações que estão a receber mais unidades de pedido.
ms.service: cosmos-db
ms.topic: how-to
author: kanshiG
ms.author: govindk
ms.date: 04/09/2020
ms.openlocfilehash: e3ec3bc0e0c6028eb5d7a90ee1920938d792941b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85260751"
---
# <a name="how-to-monitor-throughput-or-request-unit-usage-of-an-operation-in-azure-cosmos-db"></a>Como monitorizar a produção ou solicitar o uso da unidade de uma operação na Azure Cosmos DB

O Azure Monitor for Azure Cosmos DB fornece uma visão métrica para monitorizar a sua conta e criar dashboards. As métricas DB do Azure Cosmos são recolhidas por padrão, esta funcionalidade não requer que você ative ou configure nada explicitamente. A métrica **Unidades de Pedido Total** é utilizada para obter a utilização das unidades de pedido para diferentes tipos de operações. Mais tarde pode analisar quais as operações usadas na maior parte da produção. Por predefinição, os dados de produção são agregados num intervalo de um minuto. No entanto, pode alterar a unidade de agregação alterando a opção de granularidade temporal.

Existem duas formas de analisar os dados de utilização da unidade de pedido:

* Dentro do intervalo de tempo dado, quais as operações que estão a tomar mais unidades de pedido.
* Que operações em geral dominam a sua carga de trabalho consumindo mais unidades de pedido.
Esta análise permite-lhe focar-se em operações como inserir, intensificar e olhar para a sua indexação. Pode descobrir se está sobre/sob a indexação de campos específicos e modificar [a política de indexação](index-policy.md#include-exclude-paths) para incluir ou excluir os caminhos.

Se notar que certas consultas estão a tomar mais unidades de pedido, pode tomar ações como:

* Reconsidere se estiver a solicitar a quantidade certa de dados.
* Modifique a consulta para utilizar o índice com a cláusula de filtro.
* Execute chamadas de funções UDF menos dispendiosas.
* Defina as teclas de partição para minimizar a ventoinha fora da consulta em diferentes divisórias.
* Também pode utilizar as métricas de consulta devolvidas na resposta de chamada, os detalhes do registo de diagnóstico e consulte o artigo [de sintonização de desempenho](sql-api-query-metrics.md) de consulta para saber mais sobre a execução de consultas.
* Pode começar pela soma e, em seguida, olhar para a utilização do avg usando a dimensão certa.

## <a name="view-the-total-request-unit-usage-metric"></a>Ver a métrica de utilização total da unidade de pedido

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

1. Selecione **Monitor** da barra de navegação à esquerda e selecione **Métricas**.

   :::image type="content" source="./media/monitor-request-unit-usage/monitor-metrics-blade.png" alt-text="Painel de métricas no Monitor Azure":::

1. A partir do painel **métrica >** **Selecione um recurso** > escolha a **subscrição**necessária e o **grupo de recursos**. Para o **tipo de recurso**, selecione contas **DB Azure Cosmos**, escolha uma das suas contas Azure Cosmos existentes e selecione **Apply**.

   :::image type="content" source="./media/monitor-request-unit-usage/select-cosmos-db-account.png" alt-text="Painel de métricas no Monitor Azure":::

1. Em seguida, selecione a métrica **Unidades de Pedido Total** da lista de métricas disponíveis. Para saber em detalhe sobre todas as métricas disponíveis nesta lista, consulte as [Métricas por](monitor-cosmos-db-reference.md) artigo de categoria. Neste exemplo, vamos selecionar **Unidades de Pedido Total** e **Avg** como o valor de agregação. Além destes detalhes, também pode selecionar a **gama de tempo** e a **granularidade** do tempo das métricas. No máximo, pode ver métricas nos últimos 30 dias.  Depois de aplicar o filtro, é apresentado um gráfico com base no filtro. Pode ver o número médio de unidades de pedido consumidas por minuto durante o período selecionado.  

   :::image type="content" source="./media/monitor-request-unit-usage/request-unit-usage-metric.png" alt-text="Painel de métricas no Monitor Azure":::

## <a name="filters-for-request-unit-usage"></a>Filtros para utilização da unidade de pedido

Também pode filtrar métricas e obter os gráficos apresentados por um Nome de Coleção específico, **DataName,** **OperationType**, **Região,** **Estado**e **StatusCode**. **CollectionName** As opções **de divisão do filtro Add** e **Apply** permitem filtrar a utilização da unidade de pedido e agrupar as métricas.

Para obter a utilização da unidade de pedido de cada operação, quer por total (soma) quer pela média, selecione **Aplicar a divisão** e escolher o tipo **de operação** e o valor do filtro, tal como mostrado na imagem seguinte:

   :::image type="content" source="./media/monitor-request-unit-usage/request-unit-usage-operations.png" alt-text="Painel de métricas no Monitor Azure":::

Se pretender ver a utilização da unidade de pedido por recolha, selecione **Aplicar a divisão** e escolher o nome de recolha como filtro. Você verá uma conversa como a seguinte com uma escolha de coleções dentro do painel de instrumentos. Em seguida, pode selecionar um nome de coleção específico para ver mais detalhes:

   :::image type="content" source="./media/monitor-request-unit-usage/request-unit-usage-collection.png" alt-text="Painel de métricas no Monitor Azure":::

## <a name="next-steps"></a>Passos seguintes

* Monitorize os dados do DB do Azure Cosmos utilizando [as definições de diagnóstico](cosmosdb-monitor-resource-logs.md) em Azure.
* [Auditoria Azure Cosmos DB operações de avião de controlo](audit-control-plane-logs.md)
