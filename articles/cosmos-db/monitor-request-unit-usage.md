---
title: Monitorize o uso de uma operação em Azure Cosmos DB
description: Saiba como monitorizar a utilização da unidade de entrada ou solicitação de uma operação em Azure Cosmos DB. Os proprietários de uma conta Azure Cosmos DB podem entender quais as operações que estão a tomar mais unidades de Pedido.
ms.service: cosmos-db
ms.topic: conceptual
author: kanshiG
ms.author: govindk
ms.date: 04/09/2020
ms.openlocfilehash: bc39ef199a5d40d3eaa75023277d3e00b93e131a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81115433"
---
# <a name="how-to-monitor-throughput-or-request-unit-usage-of-an-operation-in-azure-cosmos-db"></a>Como monitorizar a utilização da unidade de entrada ou solicitação de uma operação em Azure Cosmos DB

O Azure Monitor para o Azure Cosmos DB fornece uma visão métrica para monitorizar a sua conta e criar dashboards. As métricas De DB Do Azure Cosmos são recolhidas por padrão, esta funcionalidade não requer que ative ou configure explicitamente nada. A métrica **Total De pedidos** é usada para obter o uso das unidades de pedido para diferentes tipos de operações. Mais tarde pode analisar quais as operações utilizadas na maior parte da entrada. Por predefinição, os dados de entrada são agregados com um intervalo de um minuto. No entanto, pode alterar a unidade de agregação alterando a opção de granularidade de tempo.

Existem duas formas de analisar os dados de utilização da unidade de pedido:

* Dentro do intervalo de tempo dado, quais as operações que estão a receber mais unidades de pedido.
* Que operações em geral dominam a sua carga de trabalho consumindo mais unidades de pedido.
Esta análise permite-lhe focar-se em operações como inserir, serrantes e olhar para a sua indexação. Pode descobrir se está acima/sob indexação de campos específicos e modificar a política de [indexação](index-policy.md#include-exclude-paths) para incluir ou excluir os caminhos.

Se notar que determinadas consultas estão a tomar mais unidades de pedido, pode tomar medidas como:

* Reconsidere se está a solicitar a quantidade certa de dados.
* Modificar a consulta para utilizar o índice com cláusula de filtro.
* Execute chamadas de função UDF menos dispendiosas.
* Defina as teclas de partição para minimizar o ventilador fora da consulta em diferentes divisórias.
* Também pode usar as métricas de consulta devolvidas na resposta de chamada, os detalhes do registo de diagnóstico e consultar o artigo de [afinação](sql-api-query-metrics.md) de desempenho da consulta para saber mais sobre a execução da consulta.
* Pode começar a partir da soma e, em seguida, olhar para a utilização de Avg usando a dimensão certa.

## <a name="view-the-total-request-unit-usage-metric"></a>Ver a métrica total de utilização da unidade de pedidos

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

1. Selecione **Monitor** a partir da barra de navegação à esquerda e selecione **Métricas**.

   ![Painel de métricas no Monitor Azure](./media/monitor-request-unit-usage/monitor-metrics-blade.png)

1. Do painel de **métricas** > **Selecione um recurso** > escolha a **subscrição**necessária e **o grupo de recursos**. Para o **tipo de Recurso**, selecione contas **Azure Cosmos DB,** escolha uma das suas contas Azure Cosmos existentes e selecione **Apply**.

   ![Escolha a conta Azure Cosmos DB para ver métricas](./media/monitor-request-unit-usage/select-cosmos-db-account.png)

1. Em seguida, selecione a métrica total das **Unidades** de Pedido da lista de métricas disponíveis. Para conhecer em detalhe todas as métricas disponíveis nesta lista, consulte as [Métricas por artigo](monitor-cosmos-db-reference.md) de categoria. Neste exemplo, vamos selecionar **unidades** de pedido total e **Avg** como o valor de agregação. Além destes detalhes, também pode selecionar a gama de **tempo** e a **granularidade** do tempo das métricas. No máximo, pode ver métricas nos últimos 30 dias.  Depois de aplicar o filtro, é apresentado um gráfico com base no filtro. Pode ver o número médio de unidades de pedido consumidas por minuto durante o período selecionado.  

   ![Escolha uma métrica do portal Azure](./media/monitor-request-unit-usage/request-unit-usage-metric.png)

## <a name="filters-for-request-unit-usage"></a>Filtros para utilização da unidade de pedido

Também pode filtrar métricas e obter os gráficos apresentados por um nome de **recolha**específico , Nome de **base de dados,** **OperaçãoTipo,** **Região,** **Estado**e Código **de Estado**. O **filtro Adicionar** e aplicar opções de **divisão** permite-lhe filtrar o uso da unidade de pedido e agrupar as métricas.

Para obter a utilização da unidade de pedido de cada operação, quer por total (soma) quer em média, selecione **Aplicar a divisão** e escolher o tipo de **operação** e o valor do filtro, como mostra a seguinte imagem:

   ![Unidades de Pedido de Db cosmos para operações no monitor Azure](./media/monitor-request-unit-usage/request-unit-usage-operations.png)

Se quiser ver a unidade de pedido de utilização por recolha, selecione **Aplicar a divisão** e escolher o nome da coleção como filtro. Você verá uma conversa como a seguinte com uma escolha de coleções dentro do tablier. Em seguida, pode selecionar um nome de coleção específico para ver mais detalhes:

   ![Unidades de Pedido de Db cosmos para todas as operações pela coleção no monitor Azure](./media/monitor-request-unit-usage/request-unit-usage-collection.png)

## <a name="next-steps"></a>Passos seguintes

* Monitorize os dados do Azure Cosmos DB utilizando [definições](cosmosdb-monitor-resource-logs.md) de diagnóstico em Azure.
* [Audit a Azure Cosmos DB controla operações de aviões](audit-control-plane-logs.md)