---
title: Obter Azure Cosmos DB métricas de Azure Monitor
description: Saiba como exibir diferentes categorias de Azure Cosmos DB métricas de Azure Monitor usando portal do Azure.
author: SnehaGunda
ms.author: sngun
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/01/2019
ms.openlocfilehash: 905eca99c137af2fd40a1243de8fabd15314477c
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/05/2019
ms.locfileid: "71973744"
---
# <a name="monitor-and-debug-azure-cosmos-db-metrics-from-azure-monitor"></a>Monitorar e depurar Azure Cosmos DB métricas de Azure Monitor

Você pode exibir Azure Cosmos DB métricas de Azure Monitor API. O Azure Monitor fornece várias maneiras de interagir com as métricas, incluindo o portal do Azure, acessá-los por meio da API REST ou consultá-los usando o PowerShell ou a CLI. Azure Cosmos DB métricas são valores numéricos de baixa latência, coletados em frequência de um minuto por padrão, você também pode agregar essas métricas. Essas métricas são capazes de dar suporte a cenários em tempo real.  

Este artigo descreve as diferentes métricas de Azure Cosmos DB que você pode exibir de Azure Monitor usando portal do Azure. Se você estiver interessado em casos de uso comuns e como Azure Cosmos DB métricas reutilizadas para analisar e depurar esses problemas, consulte [monitorar e depurar com métricas no artigo Azure Cosmos DB](use-metrics.md) . Você usará uma das contas do Azure Cosmos existentes e exibirá as diferentes métricas nos níveis de banco de dados, contêiner, região, solicitação ou operação. Portanto, verifique se você tem uma conta do Azure cosmos com dados de exemplo e execute operações CRUD nesses dados.

## <a name="view-metrics-from-azure-portal"></a>Exibir métricas de portal do Azure

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

1. Selecione **Monitor** na barra de navegação à esquerda e selecione **métricas**.

   ![Painel de métricas no Azure Monitor](./media/cosmos-db-azure-monitor-metrics/monitor-metrics-blade.png)

1. No painel de **métricas** > **selecione um recurso** > escolha a **assinatura**necessária e o **grupo de recursos**. Para o **tipo de recurso**, selecione **contas de Azure Cosmos DB**, escolha uma das contas existentes do Azure Cosmos e selecione **aplicar**. 

   ![Escolha uma conta de Cosmos DB para exibir as métricas](./media/cosmos-db-azure-monitor-metrics/select-cosmosdb-account.png)

1. Em seguida, você pode selecionar uma métrica na lista de métricas disponíveis. Você pode selecionar métricas específicas para unidades de solicitação, armazenamento, latência, disponibilidade, Cassandra e outros. Para saber mais detalhadamente sobre todas as métricas disponíveis nesta lista, consulte a seção [métricas por categoria](#metrics-by-category) deste artigo. Neste exemplo, vamos selecionar unidades de **solicitação** e **Méd** como o valor de agregação. 

   Além desses detalhes, você também pode selecionar o intervalo de **tempo** e a **granularidade de tempo** das métricas. No máximo, você pode exibir as métricas dos últimos 30 dias.  Depois de aplicar o filtro, um gráfico é exibido com base no seu filtro. Você pode ver o número médio de unidades de solicitação consumidas por minuto para o período selecionado.  

   ![Escolha uma métrica no portal do Azure](./media/cosmos-db-azure-monitor-metrics/metric-types.png)

## <a name="add-filters-to-metrics"></a>Adicionar filtros a métricas

Você também pode filtrar as métricas e o gráfico exibidos por um **CollectionName**, **DatabaseName**, **OperationType**, **região**e **StatusCode**específicos. Para filtrar as métricas, selecione **Adicionar filtro** e escolha a propriedade necessária, como **OperationType** , e selecione um valor como **consulta**. Em seguida, o grafo exibe as unidades de solicitação consumidas para a operação de consulta para o período selecionado. As operações executadas por meio do procedimento armazenado não são registradas para que não estejam disponíveis na métrica OperationType.

![Adicionar um filtro para selecionar a granularidade da métrica](./media/cosmos-db-azure-monitor-metrics/add-metrics-filter.png)

Você pode agrupar as métricas usando a opção **aplicar divisão** . Por exemplo, você pode agrupar as unidades de solicitação por tipo de operação e exibir o grafo para todas as operações de uma só vez, conforme mostrado na imagem a seguir: 

![Adicionar filtro de divisão de aplicação](./media/cosmos-db-azure-monitor-metrics/apply-metrics-splitting.png)


## <a id="metrics-by-category"></a>Métricas por categoria

### <a name="request-metrics"></a>Métricas de pedidos
            
|Métrica (nome para exibição da métrica)|Unidade (tipo de agregação) |Descrição|Dimensões| Granularidades de tempo| Mapeamento de métricas herdadas | Utilização |
|---|---|---|---| ---| ---| ---|
| TotalRequests (total de solicitações) | Contagem (contagem) | Número de solicitações feitas| DatabaseName, CollectionName, região, StatusCode| Todos | TotalRequests, http 2xx, http 3xx, http 400, http 401, erro interno do servidor, serviço não disponível, solicitações limitadas, média de solicitações por segundo | Usado para monitorar solicitações por código de status, contêiner a uma granularidade de minuto. Para obter a média de solicitações por segundo, use a agregação de contagem em minuto e divida por 60. |
| MetadataRequests (solicitações de metadados) |Contagem (contagem) | Contagem de solicitações de metadados. Azure Cosmos DB mantém o contêiner de metadados do sistema para cada conta, que permite enumerar coleções, bancos de dados, etc., e suas configurações, gratuitamente. | DatabaseName, CollectionName, região, StatusCode| Todos| |Usado para monitorar limitadores devido a solicitações de metadados.|
| MongoRequests (solicitações de Mongo) | Contagem (contagem) | Número de solicitações Mongo feitas | DatabaseName, CollectionName, região, CommandName, ErrorCode| Todos |Taxa de solicitação de consulta Mongo, taxa de solicitação de atualização Mongo, taxa de solicitação de exclusão de Mongo, taxa de solicitação de inserção de Mongo, taxa de solicitação de contagem Mongo| Usado para monitorar erros de solicitação Mongo, usos por tipo de comando. |

### <a name="request-unit-metrics"></a>Métricas de unidade de solicitação

|Métrica (nome para exibição da métrica)|Unidade (tipo de agregação)|Descrição|Dimensões| Granularidades de tempo| Mapeamento de métricas herdadas | Utilização |
|---|---|---|---| ---| ---| ---|
| MongoRequestCharge (encargo de solicitação Mongo) | Contagem (total) |Unidades de solicitação Mongo consumidas| DatabaseName, CollectionName, região, CommandName, ErrorCode| Todos |Encargo de solicitação de consulta Mongo, encargo de solicitação de atualização Mongo, cobrança de solicitação de exclusão Mongo, Mongo de cobrança de solicitação de inserção, custo de solicitação de contagem de Mongo| Usado para monitorar o RUs do recurso Mongo em um minuto.|
| TotalRequestUnits (total de unidades de solicitação)| Contagem (total) | Unidades de solicitação consumidas| DatabaseName, CollectionName, região, StatusCode |Todos| TotalRequestUnits| Usado para monitorar o uso total de RU a uma granularidade de minuto. Para obter a média de RU consumido por segundo, use a agregação total em minuto e divida por 60.|
| ProvisionedThroughput (taxa de transferência provisionada)| Contagem (máximo) |Taxa de transferência provisionada na granularidade do contêiner| DatabaseName, ContainerName| 5 MIN| | Usado para monitorar a taxa de transferência provisionada por contêiner.|

### <a name="storage-metrics"></a>Métricas de armazenamento

|Métrica (nome para exibição da métrica)|Unidade (tipo de agregação)|Descrição|Dimensões| Granularidades de tempo| Mapeamento de métricas herdadas | Utilização |
|---|---|---|---| ---| ---| ---|
| AvailableStorage (armazenamento disponível) |Bytes (total) | Armazenamento total disponível relatado com granularidade de 5 minutos por região| DatabaseName, CollectionName, região| 5 MIN| Armazenamento disponível| Usado para monitorar a capacidade de armazenamento disponível (aplicável somente para coleções de armazenamento fixa) a granularidade mínima deve ser de 5 minutos.| 
| Datautilization (uso de dados) |Bytes (total) |Uso total de dados relatados com granularidade de 5 minutos por região| DatabaseName, CollectionName, região| 5 MIN |Tamanho dos dados | Usado para monitorar o uso total de dados no contêiner e na região, a granularidade mínima deve ser de 5 minutos.|
| IndexUsage (uso de índice) | Bytes (total) |Uso total do índice relatado com granularidade de 5 minutos por região| DatabaseName, CollectionName, região| 5 MIN| Tamanho do índice| Usado para monitorar o uso total de dados no contêiner e na região, a granularidade mínima deve ser de 5 minutos. |
| DocumentQuota (cota de documentos) | Bytes (total) | Cota de armazenamento total relatada à granularidade de 5 minutos por região.| DatabaseName, CollectionName, região| 5 MIN |Capacidade de Armazenamento| Usado para monitorar a cota total no contêiner e na região, a granularidade mínima deve ser de 5 minutos.|
| DocumentCount (contagem de documentos) | Contagem (total) |Contagem total de documentos relatados à granularidade de 5 minutos por região| DatabaseName, CollectionName, região| 5 MIN |Contagem de documentos|Usado para monitorar a contagem de documentos no contêiner e na região, a granularidade mínima deve ser de 5 minutos.|

### <a name="latency-metrics"></a>Métricas de latência

|Métrica (nome para exibição da métrica)|Unidade (tipo de agregação)|Descrição|Dimensões| Granularidades de tempo| Utilização |
|---|---|---|---| ---| ---|
| ReplicationLatency (latência de replicação)| Milissegundos (mínimo, máximo, média) | Latência de replicação P99 nas regiões de origem e de destino para a conta habilitada para Geografia| SourceRegion, TargetRegion| Todos | Usado para monitorar a latência de replicação P99 entre duas regiões para uma conta replicada geograficamente. |


### <a name="availability-metrics"></a>Métricas de disponibilidade

|Métrica (nome para exibição da métrica) |Unidade (tipo de agregação)|Descrição| Granularidades de tempo| Mapeamento de métricas herdadas | Utilização |
|---|---|---|---| ---| ---|
| Indisponibilidade (disponibilidade do serviço)| Percentual (mínimo, máximo) | Disponibilidade de solicitações de conta em granularidade de uma hora| 1H | Disponibilidade do serviço | Representa a porcentagem do total de solicitações passadas. Uma solicitação é considerada como falha devido a um erro do sistema se o código de status for 410, 500 ou 503 usado para monitorar a disponibilidade da conta na granularidade de hora. |


### <a name="cassandra-api-metrics"></a>Métricas de API do Cassandra

|Métrica (nome para exibição da métrica)|Unidade (tipo de agregação)|Descrição|Dimensões| Granularidades de tempo| Utilização |
|---|---|---|---| ---| ---|
| CassandraRequests (solicitações de Cassandra) | Contagem (contagem) | Número de solicitações de API do Cassandra feitas| DatabaseName, CollectionName, ErrorCode, região, OperationType, ResourceType| Todos| Usado para monitorar solicitações Cassandra a uma granularidade de minuto. Para obter a média de solicitações por segundo, use a agregação de contagem em minuto e divida por 60.|
| CassandraRequestCharges (encargos de solicitação do Cassandra) | Contagem (Sum, min, Max, AVG) | Unidades de solicitação consumidas por API do Cassandra solicitações| DatabaseName, CollectionName, Region, OperationType, ResourceType| Todos| Usado para monitorar RUs usado por minuto por uma conta de API do Cassandra.|
| CassandraConnectionClosures (fechamentos de conexão do Cassandra) |Contagem (contagem) |Número de conexões Cassandra fechadas| ClosureReason, região| Todos | Usado para monitorar a conectividade entre os clientes do e o API do Cassandra de Azure Cosmos DB.|

## <a name="next-steps"></a>Passos seguintes

* [Exibir e monitorar métricas de Azure Cosmos DB painel de métricas de conta](use-metrics.md)

* [Log de diagnóstico em Azure Cosmos DB](logging.md)
