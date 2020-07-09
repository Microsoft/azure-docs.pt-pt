---
title: Como monitorizar a latência do lado do servidor para operações em Azure Cosmos DB
description: Saiba como monitorizar a latência do servidor para operações na conta DB da Azure Cosmos ou num contentor. Os proprietários de uma conta DB da Azure Cosmos podem compreender os problemas de latência do lado do servidor com as suas contas Azure Cosmos.
ms.service: cosmos-db
ms.topic: how-to
author: kanshiG
ms.author: govindk
ms.date: 04/07/2020
ms.openlocfilehash: 5be2365fb5850c3f45b320d66c114fb791b22c3e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85262706"
---
# <a name="how-to-monitor-the-server-side-latency-for-operations-in-an-azure-cosmos-db-container-or-account"></a>Como monitorizar a latência do lado do servidor para operações num contentor ou conta DB da Azure Cosmos

O Azure Monitor for Azure Cosmos DB fornece uma visão métrica para monitorizar a sua conta e criar dashboards. As métricas DB do Azure Cosmos são recolhidas por padrão, esta funcionalidade não requer que você ative ou configure nada explicitamente. A métrica de latência do lado do servidor é usada para visualizar a latência do lado do servidor de uma operação. A Azure Cosmos DB fornece SLA de menos de 10 ms para operações de leitura/escrita de pontos com conectividade direta. Para as operações de leitura e escrita pontuais, as AEA são calculadas conforme detalhado no [documento SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_3/).

Se vir uma latência invulgarmente grande para operações pontuais, tais como:

* Uma operação de get ou set com chave de partição e ID em modo direto
* Uma operação de leitura ou escrita ou
* Uma consulta

Pode consultar o registo de diagnóstico para ver o tamanho dos dados devolvidos. Se vir uma latência elevada sustentada para operações de consulta, pode procurar o registo de diagnóstico para o tamanho dos dados devolvidos, [produção ou RU/s](cosmosdb-monitor-resource-logs.md#diagnostic-queries) utilizados, ou o número dessas operações num determinado período. Desta forma, pode depurar os problemas de latência do lado do servidor.

## <a name="view-the-server-side-latency-metric"></a>Ver a métrica de latência do lado do servidor

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

1. Selecione **Monitor** da barra de navegação à esquerda e selecione **Métricas**.

   :::image type="content" source="./media/monitor-server-side-latency/monitor-metrics-blade.png" alt-text="Painel de métricas no Monitor Azure":::

1. A partir do painel **métrica >** **Selecione um recurso** > escolha a **subscrição**necessária e o **grupo de recursos**. Para o **tipo de recurso**, selecione contas **DB Azure Cosmos**, escolha uma das suas contas Azure Cosmos existentes e selecione **Apply**.
   
   :::image type="content" source="./media/monitor-server-side-latency/select-cosmos-db-account.png" alt-text="Escolha a conta DB Azure Cosmos para ver métricas":::

1. Em seguida, selecione a métrica de **latência do lado** do servidor na lista de métricas disponíveis. Para saber em detalhe sobre todas as métricas disponíveis nesta lista, consulte as [Métricas por](monitor-cosmos-db-reference.md) artigo de categoria. Neste exemplo, vamos selecionar **a Latência lateral** do servidor e o **Avg** como o valor de agregação. Além destes detalhes, também pode selecionar a **gama de tempo** e a **granularidade** do tempo das métricas. No máximo, pode ver métricas nos últimos 30 dias.  Depois de aplicar o filtro, é apresentado um gráfico com base no filtro. Pode ver a latência do lado do servidor por minuto durante o período selecionado.  

   :::image type="content" source="./media/monitor-server-side-latency/server-side-latency-metric.png" alt-text="Escolha a métrica de latência do lado do servidor a partir do portal Azure":::

## <a name="filters-for-server-side-latency"></a>Filtros para latência do lado do servidor

Também pode filtrar métricas e obter os gráficos apresentados por um **Nome de Coleção**específico, **ConnectionMode,** **DatabaseName,** **OperationType**, **Region**, e **PublicAPIType**. 

Para filtrar as métricas, **selecione Adicionar filtro** e escolha a propriedade necessária, como **PublicAPIType,** e selecione o valor **sql**. Adicione outro filtro para **o OperationType**. Em seguida, o gráfico apresenta a latência do lado do servidor para diferentes operações durante o período selecionado. As operações executadas através do procedimento armazenado não são registadas, pelo que não estão disponíveis ao abrigo da métrica OperationType.

As métricas **de latência do lado do servidor** para cada operação são apresentadas como mostrado na seguinte imagem:

:::image type="content" source="./media/monitor-server-side-latency/server-side-latency-filters.png" alt-text="Filtros para métricas de latência do lado do servidor":::

Também pode agrupar as métricas utilizando a opção **de divisão De aplicar.**  

## <a name="next-steps"></a>Próximos passos

* Monitorize os dados do DB do Azure Cosmos utilizando [as definições de diagnóstico](cosmosdb-monitor-resource-logs.md) em Azure.
* [Auditoria Azure Cosmos DB operações de avião de controlo](audit-control-plane-logs.md)
