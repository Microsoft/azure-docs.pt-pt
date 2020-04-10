---
title: Como monitorizar a latência do lado do servidor para operações em Azure Cosmos DB
description: Saiba como monitorizar a latência do servidor para operações na conta Azure Cosmos DB ou num recipiente. Os proprietários de uma conta Azure Cosmos DB podem entender os problemas de latência do lado do servidor com as suas contas Azure Cosmos.
ms.service: cosmos-db
ms.topic: conceptual
author: kanshiG
ms.author: govindk
ms.date: 04/07/2020
ms.openlocfilehash: 62c10a2ada9ff7d3bf7090028dd9684192517d02
ms.sourcegitcommit: a53fe6e9e4a4c153e9ac1a93e9335f8cf762c604
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/09/2020
ms.locfileid: "80991422"
---
# <a name="how-to-monitor-the-server-side-latency-for-operations-in-an-azure-cosmos-db-container-or-account"></a>Como monitorizar a latência do lado do servidor para operações num contentor ou conta Azure Cosmos DB

O Azure Monitor para o Azure Cosmos DB fornece uma visão métrica para monitorizar a sua conta e criar dashboards. As métricas De DB Do Azure Cosmos são recolhidas por padrão, esta funcionalidade não requer que ative ou configure explicitamente nada. A métrica de latência do lado do servidor é usada para visualizar a latência do lado do servidor de uma operação. A Azure Cosmos DB fornece SLA de menos de 10 ms para operações de leitura/escrita de pontos com conectividade direta. Para operações de leitura e escrita de pontos, as SLAs são calculadas de acordo com o [documento SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_3/).

Se vir uma latência invulgarmente grande para operações pontiagudas, tais como:

* Uma operação de obter ou definir com chave de partição e ID no modo direto
* Uma operação de leitura ou escrita ou
* Uma consulta

Pode procurar o registo de diagnóstico para ver o tamanho dos dados devolvidos. Se vir uma latência elevada sustentada para operações de consulta, pode procurar o registo de diagnóstico para o tamanho dos dados devolvidos, [a entrada ou o RU/s utilizados,](cosmosdb-monitor-resource-logs.md#diagnostic-queries) ou o número de operações deste tipo num determinado período. Desta forma, pode desinviar os problemas de latência do lado do servidor.

## <a name="view-server-side-latency-metric"></a>Ver métrica de latência do lado do servidor

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

1. Selecione **Monitor** a partir da barra de navegação à esquerda e selecione **Métricas**.

   ![Painel de métricas no Monitor Azure](./media/monitor-server-side-latency/monitor-metrics-blade.png)

1. Do painel de **métricas** > **Selecione um recurso** > escolha a **subscrição**necessária e **o grupo de recursos**. Para o **tipo de Recurso**, selecione contas **Azure Cosmos DB,** escolha uma das suas contas Azure Cosmos existentes e selecione **Apply**.
   
   ![Escolha uma conta Cosmos DB para ver métricas](./media/monitor-server-side-latency/select-cosmos-db-account.png)

1. Em seguida, selecione a métrica de **Latência lateral** do servidor da lista de métricas disponíveis. Para conhecer em detalhe todas as métricas disponíveis nesta lista, consulte as [Métricas por artigo](monitor-cosmos-db-reference.md) de categoria. Neste exemplo, vamos selecionar a **Latência lateral** do servidor e o **Avg** como o valor de agregação. Além destes detalhes, também pode selecionar a gama de **tempo** e a **granularidade** do tempo das métricas. No máximo, pode ver métricas nos últimos 30 dias.  Depois de aplicar o filtro, é apresentado um gráfico com base no filtro. Pode ver o número médio de unidades de pedido consumidas por minuto durante o período selecionado.  

   ![Escolha a métrica de latência do lado do servidor do portal Azure](./media/monitor-server-side-latency/server-side-latency-metric.png)

## <a name="filters-for-server-side-latency"></a>Filtros para latência do lado do servidor

Também pode filtrar métricas e o gráfico apresentado por um **nome**de recolha específico, **Modo de Ligação,** **Nome de Base de Dados,** **FunçãoTipo,** **Região**e **PublicAPIType**. 

Para filtrar as métricas, selecione **Adicionar filtro** e escolha a propriedade necessária, como **PublicAPIType** e selecione o valor **sql**. Adicione outro filtro para **OperationType**. Em seguida, o gráfico exibe a latência do lado do servidor para diferentes operações durante o período selecionado. As operações executadas através do procedimento Armazenado não estão registadas, pelo que não estão disponíveis na métrica OperationType.

As métricas de **Latência Lateral do Servidor** para cada operação são apresentadas como mostrado na seguinte imagem:

![Filtros para métricas de latência do lado do servidor](./media/monitor-server-side-latency/server-side-latency-filters.png)

Também pode agrupar as métricas utilizando a opção **de divisão Apply.**  

## <a name="next-steps"></a>Passos seguintes

* Monitor De dados do Azure Cosmos DB utilizando [definições](cosmosdb-monitor-resource-logs.md) de diagnóstico no Azure
* [Audit a Azure Cosmos DB controla operações de aviões](audit-control-plane-logs.md)