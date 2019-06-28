---
title: Obter métricas do Azure Cosmos DB do Azure Monitor
description: ''
author: SnehaGunda
ms.author: sngun
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/18/2019
ms.openlocfilehash: 2eb61a6b9afa3cabf1733be120dfbdacb7de4534
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/20/2019
ms.locfileid: "67276798"
---
# <a name="monitor-and-debug-azure-cosmos-db-metrics-from-azure-monitor"></a>Monitorizar e depurar as métricas do Azure Cosmos DB do Azure Monitor

Pode ver as métricas do Azure Cosmos DB a partir da API do Azure Monitor. O Azure Monitor proporciona várias formas de interagir com métricas, incluindo o portal do Azure, acesso aos mesmos através da API REST ou consultando-os com o PowerShell ou CLI. Métricas do Azure Cosmos DB são valores numéricos de baixa latência, coletados na frequência de um minuto por padrão, também pode agregar estas métricas. Estas métricas são capazes de oferecer suporte a cenários em tempo real.  

Este artigo descreve os diferentes do Azure Cosmos DB métricas que pode ver do Azure Monitor através do portal do Azure. Se estiver interessado em comum casos de utilização e como o Azure Cosmos DB métricas re utilizadas para analisar e depurar esses problemas ver [Monitor e de depuração com métricas na Azure Cosmos DB](use-metrics.md) artigo. Irá utilizar uma das suas contas do Cosmos do Azure existentes e ver as métricas diferentes no banco de dados, contentores, região, pedido ou níveis de operação. Por isso, certifique-se de que tem uma conta do Cosmos do Azure com dados de exemplo e realizar operações de CRUD nesses dados.

## <a name="view-metrics-from-azure-portal"></a>Ver métricas no portal do Azure

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

1. Selecione **Monitor** no painel de navegação esquerda da barra e selecione **métricas**.

   ![Painel de métricas no Azure Monitor](./media/cosmos-db-azure-monitor-metrics/monitor-metrics-blade.png)

1. Do **métricas** painel > **selecione um recurso** > Escolha necessários **subscrição**, e **grupo de recursos**. Para o **tipo de recurso**, selecione **contas do Azure Cosmos DB**, escolha uma das suas contas do Cosmos do Azure existentes e selecione **aplicar**. 

   ![Escolha uma conta do Cosmos DB para visualizar as métricas](./media/cosmos-db-azure-monitor-metrics/select-cosmosdb-account.png)

1. Em seguida pode selecionar uma métrica da lista de métricas disponíveis. Pode selecionar métricas específicas de unidades de pedido, armazenamento, latência, disponibilidade, Cassandra e outras pessoas. Para obter detalhes sobre todas as métricas disponíveis nesta lista, consulte a [métricas por categoria](#metrics-by-category) seção deste artigo. Neste exemplo, vamos selecionar **unidades de pedido** e **média** como o valor de agregação. 

   Para além estes detalhes, também pode selecionar o **intervalo de tempo** e **granularidade de tempo** das métricas. No máximo, pode ver métricas nos últimos 30 dias.  Depois de aplicar o filtro, um gráfico é apresentado com base no seu filtro. Pode ver o número médio de unidades de pedido consumidas por minuto durante o período selecionado.  

   ![Escolha uma métrica do portal do Azure](./media/cosmos-db-azure-monitor-metrics/metric-types.png)

## <a name="add-filters-to-metrics"></a>Adicionar filtros de métricas

Também pode filtrar as métricas e o gráfico apresentado por um específico **CollectionName**, **DatabaseName**, **OperationType**, **região**, e **StatusCode**. Para filtrar as métricas, selecione **Adicionar filtro** e selecione a propriedade necessária como **OperationType** e selecione um valor como **consulta**. O gráfico, em seguida, apresenta as unidades de pedido consumidas para a operação de consulta para o período selecionado. As operações executadas por meio do procedimento armazenado não tem sessão iniciadas para que não estejam disponíveis sob a métrica de OperationType.

![Adicionar um filtro para selecionar a granularidade de métrica](./media/cosmos-db-azure-monitor-metrics/add-metrics-filter.png)

Pode agrupar as métricas ao utilizar o **aplicam-se de que a divisão** opção. Por exemplo, pode agrupar as unidades de pedido por tipo de operação e ver o gráfico para todas as operações ao mesmo tempo, conforme mostrado na imagem seguinte: 

![Adicionar aplicar filtro divisor](./media/cosmos-db-azure-monitor-metrics/apply-metrics-splitting.png)


## <a id="metrics-by-category"></a>Métricas por categoria

### <a name="request-metrics"></a>Métricas de pedidos
            
|Métrica (nome a apresentar métricas)|Unidade (tipo de agregação) |Descrição|Dimensões| Nas duas granularidades de tempo| Mapeamento de métrica de legado | Utilização |
|---|---|---|---| ---| ---| ---|
| TotalRequests (Total de pedidos) | Contagem (contagem) | Número de pedidos efetuados| DatabaseName, CollectionName, Region, StatusCode| Todos | TotalRequests, Http 2xx, 3xx de Http, Http 400, Http 401, erro de servidor interno, serviço indisponível, limitado de pedidos, pedidos médios por segundo | Utilizado para monitorizar pedidos por código de estado, a coleção numa granularidade ao minuto. Para obter pedidos médios por segundo, utilizar a agregação de contagem em minutos e divida por 60. |
| MetadataRequests (pedidos de metadados) |Contagem (contagem) | Contagem de pedidos de metadados. O Azure Cosmos DB mantém a coleção de metadados do sistema para cada conta, que permite que enumere coleções, bases de dados, etc., e suas configurações, sem encargos. | DatabaseName, CollectionName, Region, StatusCode| Todos| |Utilizado para monitorizar os limitadores devido a pedidos de metadados.|
| MongoRequests (Mongo pedidos) | Contagem (contagem) | Número de pedidos de Mongo efetuados | DatabaseName, CollectionName, região, CommandName, código de erro| Todos |Taxa de pedidos de consulta mongo, atualização de Mongo pedir taxa, Mongo eliminar taxa de pedidos, Mongo inserir a taxa de pedidos, taxa de pedidos de contagem de Mongo| Utilizações por comando utilizado para monitorizar erros de pedido do Mongo, escreva. |

### <a name="request-unit-metrics"></a>Métricas de unidade de pedido

|Métrica (nome a apresentar métricas)|Unidade (tipo de agregação)|Descrição|Dimensões| Nas duas granularidades de tempo| Mapeamento de métrica de legado | Utilização |
|---|---|---|---| ---| ---| ---|
| MongoRequestCharge (encargo de pedido do Mongo) | Contagem (Total) |Unidades de pedido de mongo consumidas| DatabaseName, CollectionName, região, CommandName, código de erro| Todos |Encargos de pedidos de consulta mongo, atualização de Mongo encargo de pedido de encargos de pedidos de eliminar Mongo, gratuita, Mongo inserir de encargos de pedidos, contagem de Mongo pedido| Utilizado para monitorizar os recursos de Mongo RUs num minuto.|
| TotalRequestUnits (unidades de pedido Total)| Contagem (Total) | Solicitar que unidades consumidas| DatabaseName, CollectionName, Region, StatusCode |Todos| TotalRequestUnits| Utilizado para monitorizar a utilização de Total RU numa granularidade ao minuto. Para obter a média RU consumida por segundo, utilizar a agregação Total ao minuto e divida por 60.|
| ProvisionedThroughput (débito aprovisionado)| Contagem (máximo) |Débito aprovisionado na granularidade de coleção| DatabaseName, CollectionName| 5M| | Utilizado para monitorizar o débito aprovisionado por coleção.|

### <a name="storage-metrics"></a>Métricas de armazenamento

|Métrica (nome a apresentar métricas)|Unidade (tipo de agregação)|Descrição|Dimensões| Nas duas granularidades de tempo| Mapeamento de métrica de legado | Utilização |
|---|---|---|---| ---| ---| ---|
| AvailableStorage (armazenamento disponível) |Bytes (Total) | Total de armazenamento disponível reportados na granularidade de 5 minutos por região| DatabaseName, CollectionName, Region| 5M| Armazenamento disponível| Utilizado para monitorizar o armazenamento disponível granularidade mínima de capacidade (aplicável apenas para recolhas de armazenamento fixa) deve ser de 5 minutos.| 
| DataUsage (utilização de dados) |Bytes (Total) |Utilização total de dados reportada na granularidade de 5 minutos por região| DatabaseName, CollectionName, Region| 5M |Tamanho dos dados | Utilizado para monitorizar a utilização total de dados na coleção e a região, granularidade mínima deve ser de 5 minutos.|
| IndexUsage (uso do índice) | Bytes (Total) |Utilização total do índice relatados na granularidade de 5 minutos por região| DatabaseName, CollectionName, Region| 5M| Tamanho do índice| Utilizado para monitorizar a utilização total de dados na coleção e a região, granularidade mínima deve ser de 5 minutos. |
| DocumentQuota (Quota de documento) | Bytes (Total) | Quota de armazenamento total comunicados na granularidade de 5 minutos por região.| DatabaseName, CollectionName, Region| 5M |Capacidade de Armazenamento| Utilizado para monitorizar a quota total na coleção e a região, granularidade mínima deve ser de 5 minutos.|
| DocumentCount (contagem de documentos) | Contagem (Total) |Contagem de documentos total comunicados na granularidade de 5 minutos por região| DatabaseName, CollectionName, Region| 5M |Contagem de documentos|Utilizado para monitorizar a contagem de documentos na coleção e a região, granularidade mínima deve ser de 5 minutos.|

### <a name="latency-metrics"></a>Métricas de latência

|Métrica (nome a apresentar métricas)|Unidade (tipo de agregação)|Descrição|Dimensões| Nas duas granularidades de tempo| Utilização |
|---|---|---|---| ---| ---|
| ReplicationLatency (latência de replicação)| Milissegundos (mínimo, máximo, média) | P99 a latência de replicação entre regiões de origem e de destino para a conta geo-ativada| SourceRegion, TargetRegion| Todos | Usado para monitorar a latência de replicação de P99 entre quaisquer duas regiões para uma conta georreplicado. |


### <a name="availability-metrics"></a>Métricas de disponibilidade

|Métrica (nome a apresentar métricas) |Unidade (tipo de agregação)|Descrição| Nas duas granularidades de tempo| Mapeamento de métrica de legado | Utilização |
|---|---|---|---| ---| ---|
| ServiceAvailability (disponibilidade do serviço)| Por cento (mínimo, máximo) | Disponibilidade de pedidos de conta na granularidade de uma hora| 1H | Disponibilidade do serviço | Representa a percentagem do total transmitido pedidos. Um pedido é considerado para ser falhou devido a erro de sistema se o código de estado é 410, 500 ou 503 utilizado para monitorizar a disponibilidade da conta na granularidade de hora. |


### <a name="cassandra-api-metrics"></a>Métrica de API de Cassandra

|Métrica (nome a apresentar métricas)|Unidade (tipo de agregação)|Descrição|Dimensões| Nas duas granularidades de tempo| Utilização |
|---|---|---|---| ---| ---|
| CassandraRequests (Cassandra pedidos) | Contagem (contagem) | Número de pedidos de API para Cassandra efetuados| DatabaseName, CollectionName, ErrorCode, Region, OperationType, ResourceType| Todos| Utilizado para monitorizar os pedidos de Cassandra numa granularidade ao minuto. Para obter pedidos médios por segundo, utilizar a agregação de contagem em minutos e divida por 60.|
| CassandraRequestCharges (custos de pedido de Cassandra) | Contagem (soma, Min, Máx, média) | Solicitar unidades consumidas por pedidos de API de Cassandra| DatabaseName, CollectionName, Region, OperationType, ResourceType| Todos| Utilizado para monitorizar o RUs utilizados por minuto por uma conta da API de Cassandra.|
| CassandraConnectionClosures (Cassandra ligação fechamentos) |Contagem (contagem) |Número de ligações de Cassandra fechados| ClosureReason, região| Todos | Utilizado para monitorizar a conectividade entre clientes e a API de Cassandra do Azure Cosmos DB.|

## <a name="next-steps"></a>Passos Seguintes

* [Ver e monitorizar as métricas, a partir do painel de métricas de conta do Azure Cosmos DB](use-metrics.md)

* [Registo de diagnósticos no Azure Cosmos DB](logging.md)
