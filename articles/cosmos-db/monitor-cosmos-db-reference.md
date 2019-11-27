---
title: Azure Cosmos DB referência de dados de monitoramento | Microsoft Docs
description: Referência de log e métricas para monitorar dados de Azure Cosmos DB.
author: bwren
services: azure-monitor
ms.service: azure-monitor
ms.topic: reference
ms.date: 11/11/2019
ms.author: bwren
ms.custom: subject-monitoring
ms.subservice: logs
ms.openlocfilehash: d131523e3031f55a818bb1919f39119bf073cb75
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/24/2019
ms.locfileid: "74456525"
---
# <a name="azure-cosmos-db-monitoring-data-reference"></a>Azure Cosmos DB referência de dados de monitoramento
Este artigo fornece uma referência de dados de log e métricas coletados para analisar o desempenho e a disponibilidade de Azure Cosmos DB. Consulte [monitoramento Cosmos DB](monitor-cosmos-db.md) para obter detalhes sobre como coletar e analisar dados de monitoramento para Azure Cosmos DB.


## <a name="resource-logs"></a>Registos do recurso
A tabela a seguir lista as propriedades de Azure Cosmos DB logs de recursos quando eles são coletados nos logs de Azure Monitor ou no armazenamento do Azure. Nos logs de Azure Monitor, eles são coletados na tabela **AzureDiagnostics** com um valor **resourceprovider** da *Microsoft. DOCUMENTDB*. 

| Campo de armazenamento do Azure ou a propriedade | Propriedade de logs de Azure Monitor | Descrição |
| --- | --- | --- |
| **momento** | **TimeGenerated** | A data e hora (UTC) em que ocorreu a operação. |
| **Identificação** | **Recurso** | A conta do Azure Cosmos DB para o qual os registos estão ativados.|
| **Categorias** | **Categoria** | Para logs de Azure Cosmos DB, **DataPlaneRequests**, **MongoRequests**, **QueryRuntimeStatistics**, **PartitionKeyStatistics**, **PartitionKeyRUConsumption**, **ControlPlaneRequests** são os tipos de log disponíveis. |
| **operationName** | **OperationName** | Nome da operação. Este valor pode ser qualquer uma das seguintes operações: criar, atualização, leitura, ReadFeed, eliminar, Replace, Execute, SqlQuery, consulta, JSQuery, Head, HeadFeed ou Upsert.   |
| **Properties** | n/d | O conteúdo deste campo é descrito nas linhas que se seguem. |
| **activityId** | **activityId_g** | O GUID exclusivo para a operação com sessão iniciada. |
| **userAgent** | **userAgent_s** | Uma cadeia de caracteres que especifica o agente de utilizador do cliente que está a efetuar o pedido. O formato é {nome do agente de utilizador} / {version}.|
| **requestResourceType** | **requestResourceType_s** | O tipo de recurso acedido. Este valor pode ser qualquer um dos seguintes tipos de recurso: base de dados, contentor, documentos, anexo, utilizador, permissão, StoredProcedure, acionador, UserDefinedFunction ou oferta. |
| **statusCode** | **statusCode_s** | O estado de resposta da operação. |
| **requestResourceId** | **Identificação** | O resourceId que diz respeito ao pedido. O valor pode apontar para databaseRid, collectionRid ou documentRid dependendo da operação efetuada.|
| **clientIpAddress** | **clientIpAddress_s** | Endereço IP do cliente. |
| **requestCharge** | **requestCharge_s** | O número de RUs que são utilizados pela operação |
| **collectionRid** | **collectionId_s** | O ID exclusivo para a coleção.|
| **permanência** | **duration_s** | A duração da operação, em milissegundos. |
| **requestLength** | **requestLength_s** | O comprimento do pedido, em bytes. |
| **responseLength** | **responseLength_s** | O comprimento da resposta, em bytes.|
| **resourceTokenUserRid** | **resourceTokenUserRid_s** | Esse valor não é vazio quando [tokens de recurso](https://docs.microsoft.com/azure/cosmos-db/secure-access-to-data#resource-tokens) são usados para autenticação. O valor aponta para o ID de recurso do utilizador. |

Para obter uma lista de todas as categorias de log Azure Monitor e links para esquemas associados, consulte [Azure monitor categorias e esquemas de logs](../azure-monitor/platform/diagnostic-logs-schema.md). 

## <a name="metrics"></a>Métricas
As tabelas a seguir listam as métricas de plataforma coletadas para o Azure CosmOS DB. Todas as métricas são armazenadas no namespace **Cosmos DB métricas padrão**.

Para obter uma lista de todas as métricas de suporte de Azure Monitor (incluindo CosmosDB), consulte [Azure monitor métricas com suporte](../azure-monitor/platform/metrics-supported.md). 

#### <a name="request-metrics"></a>Métricas de pedidos
            
|Métrica (nome para exibição da métrica)|Unidade (tipo de agregação) |Descrição|Dimensões| Granularidades de tempo| Mapeamento de métricas herdadas | Utilização |
|---|---|---|---| ---| ---| ---|
| TotalRequests (total de solicitações) | Contagem (contagem) | Número de solicitações feitas| DatabaseName, CollectionName, região, StatusCode| Todos | TotalRequests, http 2xx, http 3xx, http 400, http 401, erro interno do servidor, serviço não disponível, solicitações limitadas, média de solicitações por segundo | Usado para monitorar solicitações por código de status, contêiner a uma granularidade de minuto. Para obter a média de solicitações por segundo, use a agregação de contagem em minuto e divida por 60. |
| MetadataRequests (solicitações de metadados) |Contagem (contagem) | Contagem de solicitações de metadados. Azure Cosmos DB mantém o contêiner de metadados do sistema para cada conta, que permite enumerar coleções, bancos de dados, etc., e suas configurações, gratuitamente. | DatabaseName, CollectionName, região, StatusCode| Todos| |Usado para monitorar limitadores devido a solicitações de metadados.|
| MongoRequests (solicitações de Mongo) | Contagem (contagem) | Número de solicitações Mongo feitas | DatabaseName, CollectionName, região, CommandName, ErrorCode| Todos |Taxa de solicitação de consulta Mongo, taxa de solicitação de atualização Mongo, taxa de solicitação de exclusão de Mongo, taxa de solicitação de inserção de Mongo, taxa de solicitação de contagem Mongo| Usado para monitorar erros de solicitação Mongo, usos por tipo de comando. |

#### <a name="request-unit-metrics"></a>Métricas de unidade de solicitação

|Métrica (nome para exibição da métrica)|Unidade (tipo de agregação)|Descrição|Dimensões| Granularidades de tempo| Mapeamento de métricas herdadas | Utilização |
|---|---|---|---| ---| ---| ---|
| MongoRequestCharge (encargo de solicitação Mongo) | Contagem (total) |Unidades de solicitação Mongo consumidas| DatabaseName, CollectionName, região, CommandName, ErrorCode| Todos |Encargo de solicitação de consulta Mongo, encargo de solicitação de atualização Mongo, cobrança de solicitação de exclusão Mongo, Mongo de cobrança de solicitação de inserção, custo de solicitação de contagem de Mongo| Usado para monitorar o RUs do recurso Mongo em um minuto.|
| TotalRequestUnits (total de unidades de solicitação)| Contagem (total) | Unidades de solicitação consumidas| DatabaseName, CollectionName, região, StatusCode |Todos| TotalRequestUnits| Usado para monitorar o uso total de RU a uma granularidade de minuto. Para obter a média de RU consumido por segundo, use a agregação total em minuto e divida por 60.|
| ProvisionedThroughput (taxa de transferência provisionada)| Contagem (máximo) |Taxa de transferência provisionada na granularidade do contêiner| DatabaseName, ContainerName| 5 min| | Usado para monitorar a taxa de transferência provisionada por contêiner.|

#### <a name="storage-metrics"></a>Métricas de armazenamento

|Métrica (nome para exibição da métrica)|Unidade (tipo de agregação)|Descrição|Dimensões| Granularidades de tempo| Mapeamento de métricas herdadas | Utilização |
|---|---|---|---| ---| ---| ---|
| AvailableStorage (armazenamento disponível) |Bytes (total) | Armazenamento total disponível relatado com granularidade de 5 minutos por região| DatabaseName, CollectionName, região| 5 min| Armazenamento disponível| Usado para monitorar a capacidade de armazenamento disponível (aplicável somente para coleções de armazenamento fixa) a granularidade mínima deve ser de 5 minutos.| 
| Datautilization (uso de dados) |Bytes (total) |Uso total de dados relatados com granularidade de 5 minutos por região| DatabaseName, CollectionName, região| 5 min |Tamanho dos dados | Usado para monitorar o uso total de dados no contêiner e na região, a granularidade mínima deve ser de 5 minutos.|
| IndexUsage (uso de índice) | Bytes (total) |Uso total do índice relatado com granularidade de 5 minutos por região| DatabaseName, CollectionName, região| 5 min| Tamanho do índice| Usado para monitorar o uso total de dados no contêiner e na região, a granularidade mínima deve ser de 5 minutos. |
| DocumentQuota (cota de documentos) | Bytes (total) | Cota de armazenamento total relatada à granularidade de 5 minutos por região.| DatabaseName, CollectionName, região| 5 min |Capacidade de Armazenamento| Usado para monitorar a cota total no contêiner e na região, a granularidade mínima deve ser de 5 minutos.|
| DocumentCount (contagem de documentos) | Contagem (total) |Contagem total de documentos relatados à granularidade de 5 minutos por região| DatabaseName, CollectionName, região| 5 min |Contagem de documentos|Usado para monitorar a contagem de documentos no contêiner e na região, a granularidade mínima deve ser de 5 minutos.|

#### <a name="latency-metrics"></a>Métricas de latência

|Métrica (nome para exibição da métrica)|Unidade (tipo de agregação)|Descrição|Dimensões| Granularidades de tempo| Utilização |
|---|---|---|---| ---| ---|
| ReplicationLatency (latência de replicação)| Milissegundos (mínimo, máximo, média) | Latência de replicação P99 nas regiões de origem e de destino para a conta habilitada para Geografia| SourceRegion, TargetRegion| Todos | Usado para monitorar a latência de replicação P99 entre duas regiões para uma conta replicada geograficamente. |


#### <a name="availability-metrics"></a>Métricas de disponibilidade

|Métrica (nome para exibição da métrica) |Unidade (tipo de agregação)|Descrição| Granularidades de tempo| Mapeamento de métricas herdadas | Utilização |
|---|---|---|---| ---| ---|
| Indisponibilidade (disponibilidade do serviço)| Percentual (mínimo, máximo) | Disponibilidade de solicitações de conta em granularidade de uma hora| 1H | Disponibilidade do serviço | Representa a porcentagem do total de solicitações passadas. Uma solicitação é considerada como falha devido a um erro do sistema se o código de status for 410, 500 ou 503 usado para monitorar a disponibilidade da conta na granularidade de hora. |


#### <a name="cassandra-api-metrics"></a>Métricas de API do Cassandra

|Métrica (nome para exibição da métrica)|Unidade (tipo de agregação)|Descrição|Dimensões| Granularidades de tempo| Utilização |
|---|---|---|---| ---| ---|
| CassandraRequests (solicitações de Cassandra) | Contagem (contagem) | Número de solicitações de API do Cassandra feitas| DatabaseName, CollectionName, ErrorCode, região, OperationType, ResourceType| Todos| Usado para monitorar solicitações Cassandra a uma granularidade de minuto. Para obter a média de solicitações por segundo, use a agregação de contagem em minuto e divida por 60.|
| CassandraRequestCharges (encargos de solicitação do Cassandra) | Contagem (Sum, min, Max, AVG) | Unidades de solicitação consumidas por API do Cassandra solicitações| DatabaseName, CollectionName, Region, OperationType, ResourceType| Todos| Usado para monitorar RUs usado por minuto por uma conta de API do Cassandra.|
| CassandraConnectionClosures (fechamentos de conexão do Cassandra) |Contagem (contagem) |Número de conexões Cassandra fechadas| ClosureReason, região| Todos | Usado para monitorar a conectividade entre os clientes do e o API do Cassandra de Azure Cosmos DB.|

## <a name="see-also"></a>Consulte Também

- Consulte [monitoramento Azure Cosmos DB](monitor-cosmos-db.md) para obter uma descrição do Azure Cosmos DB de monitoramento.
- Consulte [monitorando recursos do Azure com Azure monitor](../azure-monitor/insights/monitor-azure-resource.md) para obter detalhes sobre como monitorar recursos do Azure.
