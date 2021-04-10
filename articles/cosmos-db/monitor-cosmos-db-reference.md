---
title: Monitorização da referência de dados da Azure Cosmos DB | Microsoft Docs
description: Material de referência importante necessário quando monitoriza registos e métricas em Azure Cosmos DB.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: how-to
ms.date: 12/07/2020
ms.author: sngun
ms.custom: subject-monitoring
ms.openlocfilehash: 5f542b35110a6d967640ad91faead75f6cc0e0c2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "100593289"
---
# <a name="monitoring-azure-cosmos-db-data-reference"></a>Monitorização da referência de dados do Azure Cosmos DB

[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Este artigo fornece uma referência dos registos e dos dados métricos recolhidos para analisar o desempenho e a disponibilidade do Azure Cosmos DB. Consulte o artigo do [Monitor Azure Cosmos DB](monitor-cosmos-db.md) para obter detalhes sobre a recolha e análise de dados de monitorização para Azure Cosmos DB.

## <a name="metrics"></a>Métricas

Todas as métricas correspondentes à Azure Cosmos DB estão armazenadas nas **métricas padrão cosmos DB** do espaço. Para obter uma lista de todas as métricas de suporte do Azure Monitor (incluindo Azure Cosmos DB), consulte [as métricas suportadas pelo Azure Monitor](../azure-monitor/essentials/metrics-supported.md). Esta secção lista todas as métricas da plataforma recolhidas automaticamente para a Azure Cosmos DB.  

### <a name="request-metrics"></a>Solicitar métricas

|Métrica (Nome de Exibição Métrica)|Unidade (Tipo de agregação) |Description|Dimensões| Granularidades do tempo| Mapeamento métrico legado | Utilização |
|---|---|---|---| ---| ---| ---|
| Total de Reques (Total de Pedidos) | Contagem (Contagem) | Número de pedidos feitos| Data de dadosName, Nome de Recolha, Região, StatusCode| Todos | TotalRequests, Http 2xx, Http 3xx, Http 400, Http 401, Erro do Servidor Interno, Serviço Indisponível, Pedidos De Throttled, Pedidos Médios por Segundo | Usado para monitorizar pedidos por código de estado, recipiente a um minuto de granularidade. Para obter pedidos médios por segundo, use a agregação count ao minuto e divida por 60. |
| MetadadosRequests (Pedidos de Metadados) |Contagem (Contagem) | Contagem de pedidos de metadados. O Azure Cosmos DB mantém o contentor de metadados do sistema para cada conta, que lhe permite enumerar recolhas, bases de dados, etc., e as suas configurações, gratuitamente. | Data de dadosName, Nome de Recolha, Região, StatusCode| Todos| |Usado para monitorizar aceleradores devido a pedidos de metadados.|
| MongoRequests (Pedidos de Mongo) | Contagem (Contagem) | Número de pedidos da Mongo Feitos | Nome de dados, Nome de Recolha, Região, Nome de Comando, Código de Erro| Todos |Taxa de pedido de consulta de Mongo, Taxa de Pedido de Atualização de Mongo, Taxa de Pedido de Apagamento de Mongo, Taxa de Pedido de Inserção de Mongo, Taxa de Pedido de Contagem de Mongo| Usado para monitorizar erros de pedido de Mongo, utilizações por tipo de comando. |

### <a name="request-unit-metrics"></a>Métricas da Unidade de Pedido

|Métrica (Nome de Exibição Métrica)|Unidade (Tipo de agregação)|Description|Dimensões| Granularidades do tempo| Mapeamento métrico legado | Utilização |
|---|---|---|---| ---| ---| ---|
| MongoRequestCharge (Taxa de Pedido de Mongo) | Contagem (Total) |Unidades de pedido da Mongo Consumidas| Nome de dados, Nome de Recolha, Região, Nome de Comando, Código de Erro| Todos |Mongo Query Request Charge, Mongo Update Request Charge, Mongo Delete Request Charge, Mongo Insert Request Charge, Mongo Count Request Charge| Usado para monitorizar os recursos da Mongo RUs num minuto.|
| Total DerequestUnits (Unidades totais de pedido)| Contagem (Total) | Unidades de pedido consumidas| Data de dadosName, Nome de Recolha, Região, StatusCode |Todos| TotalRequestUnits| Utilizado para monitorizar o uso total de RU a um minuto de granularidade. Para obter ru médio consumido por segundo, use agregação total ao minuto e divida por 60.|
| ProvisionedThroughput (Provisioned Throughput)| Contagem (Máximo) |Produção a provisionada na granularidade do contentor| Nome de dados, nome de contentor| 5M| | Usado para monitorizar a produção a provisionada por contentor.|

### <a name="storage-metrics"></a>Métricas de armazenamento

|Métrica (Nome de Exibição Métrica)|Unidade (Tipo de agregação)|Description|Dimensões| Granularidades do tempo| Mapeamento métrico legado | Utilização |
|---|---|---|---| ---| ---| ---|
| Availablestorage (Armazenamento Disponível) |Bytes (Total) | Armazenamento total disponível reportado a 5 minutos de granularidade por região| Nome de dados, Nome de Recolha, Região| 5M| Armazenamento Disponível| Utilizado para monitorizar a capacidade de armazenamento disponível (aplicável apenas para recolhas fixas de armazenamento) A granularidade mínima deve ser de 5 minutos.| 
| DataUsage (Utilização de Dados) |Bytes (Total) |Total de utilização de dados reportados a 5 minutos de granularidade por região| Nome de dados, Nome de Recolha, Região| 5M |Tamanho dos dados | Utilizado para monitorizar o uso total de dados no contentor e na região, a granularidade mínima deve ser de 5 minutos.|
| IndexUsage (Utilização do Índice) | Bytes (Total) |Utilização total do Índice reportada em 5 minutos de granularidade por região| Nome de dados, Nome de Recolha, Região| 5M| Tamanho do índice| Utilizado para monitorizar o uso total de dados no contentor e na região, a granularidade mínima deve ser de 5 minutos. |
| DocumentoQuota (Quota de Documento) | Bytes (Total) | Quota total de armazenamento reportada em 5 minutos de granularidade por região.| Nome de dados, Nome de Recolha, Região| 5M |Capacidade de Armazenamento| Utilizado para monitorizar a quota total no contentor e na região, a granularidade mínima deve ser de 5 minutos.|
| Contagem de Documentos (Contagem de Documentos) | Contagem (Total) |Contagem total de documentos reportada em 5 minutos granularidade por região| Nome de dados, Nome de Recolha, Região| 5M |Contagem de documentos|Utilizado para monitorizar a contagem de documentos no contentor e na região, a granularidade mínima deve ser de 5 minutos.|

### <a name="latency-metrics"></a>Métricas de latência

|Métrica (Nome de Exibição Métrica)|Unidade (Tipo de agregação)|Description|Dimensões| Granularidades do tempo| Utilização |
|---|---|---|---| ---| ---|
| ReplicationLatency (Latência de replicação)| MilliSegundos (Mínimo, Máximo, Média) | P99 Replicação Latência em todas as regiões de origem e alvo para conta geo-habilitada| SourceRegion, TargetRegion| Todos | Usado para monitorizar a latência de replicação P99 entre duas regiões para uma conta geo-replicada. |
| Latência do lado do servidor| MilliSegundos (Média) | Tempo demorado pelo servidor para processar o pedido. | ColeçãoName, ConnectionMode, DatabaseName, OperationType, PublicAPIType, Região | Todos | Usado para monitorizar a latência do pedido no servidor DB Azure Cosmos. |

### <a name="availability-metrics"></a>Métricas de disponibilidade

|Métrica (Nome de Exibição Métrica) |Unidade (Tipo de agregação)|Description| Granularidades do tempo| Mapeamento métrico legado | Utilização |
|---|---|---|---| ---| ---|
| ServiçoSadisponibilidade (Disponibilidade de Serviço)| Por cento (Mínimo, Máximo) | Conta solicita disponibilidade a uma hora de granularidade| 1H | Disponibilidade de serviço | Representa a percentagem do total de pedidos aprovados. Considera-se que um pedido foi falhado devido a um erro do sistema se o código de estado for 410, 500 ou 503 Utilizado para monitorizar a disponibilidade da conta à hora de granularidade. |

### <a name="cassandra-api-metrics"></a>Métricas da API de Cassandra

|Métrica (Nome de Exibição Métrica)|Unidade (Tipo de agregação)|Description|Dimensões| Granularidades do tempo| Utilização |
|---|---|---|---| ---| ---|
| CassandraRequests (Pedidos de Cassandra) | Contagem (Contagem) | Número de pedidos da Cassandra API feitos| Nome de dados, Nome de Recolha, Código de Erro, Região, OperaçãoType,Tip de Recursos| Todos| Usado para monitorizar os pedidos de Cassandra num minuto de granularidade. Para obter pedidos médios por segundo, use a agregação count ao minuto e divida por 60.|
| CassandraRequestCharges (Cassandra Request Charges) | Conde (Soma, Min, Max, Avg) | Unidades de pedido consumidas pela API cassandra | Nome de dados, Nome de Recolha, Região, OperaçãoType,Tip de Recursos| Todos| Usado para monitorizar rUs usados por minuto por uma conta API Cassandra.|
| CassandraConnectionClosures (Encerramentos de Conexões Cassandra) |Contagem (Contagem) |Número de Ligações Cassandra encerrado| ClosureReason, Região| Todos | Usado para monitorizar a conectividade entre clientes e a API API AZURE Cosmos DB Cassandra.|

Para mais informações, consulte uma lista de [todas as métricas da plataforma suportadas no Azure Monitor.](../azure-monitor/essentials/metrics-supported.md)

## <a name="resource-logs"></a>Registos do recurso

A tabela que se segue lista as propriedades dos registos de recursos em Azure Cosmos DB. Os registos de recursos são recolhidos em Registos monitores Azure ou Azure Storage. No Azure Monitor, os registos são recolhidos na tabela **AzureDiagnostics** sob o nome do fornecedor de recursos** de `MICROSOFT.DOCUMENTDB` .

| Campo ou propriedade de Armazenamento Azure | Propriedade Azure Monitor Logs | Description |
| --- | --- | --- |
| **Hora** | **TimeGenerated** | A data e a hora (UTC) quando ocorreu a operação. |
| **recursosId** | **Recurso** | A conta DB do Azure Cosmos para os registos que estão ativados.|
| **categoria** | **Categoria** | Para Azure Cosmos DB, **DataPlaneRequests**, **MongoRequests**, **QueryRuntimeStatistics**, **PartitionKeyStatistics**, **PartitionKeyRUConsumption,** **ControlPlaneRequests,** **CassandraRequests,** **GremlinRequests** são os tipos de registos disponíveis. |
| **operationName** | **Operação Nome** | O nome da operação. O nome da operação pode ser  `Create` , , , , , , , , `Update` , , `Read` , , , `ReadFeed` ou `Delete` `Replace` `Execute` `SqlQuery` `Query` `JSQuery` `Head` `HeadFeed` `Upsert` .   |
| **propriedades** | n/a | O conteúdo deste campo é descrito nas linhas que se seguem. |
| **activityId** | **activityId_g** | O GUIA único para a operação registada. |
| **userAgent** | **userAgent_s** | Uma cadeia que especifica o agente utilizador cliente a partir do qual o pedido foi enviado. O formato do agente utilizador é `{user agent name}/{version}` .|
| **solicitamResourceType** | **requestResourceType_s** | O tipo de recurso acedido. Este valor pode ser base de dados, contentor, documento, anexo, utilizador, permissão, procedimento armazenado, gatilho, função definida pelo utilizador ou uma oferta. |
| **statusCode** | **statusCode_s** | O estado de resposta da operação. |
| **requestResourceId** | **ResourceId** | Os recursosid que diz respeito ao pedido. Dependendo da operação realizada, este valor pode apontar para `databaseRid` `collectionRid` , ou `documentRid` .|
| **clienteIpAddress** | **clientIpAddress_s** | O endereço IP do cliente. |
| **requestCharge** | **requestCharge_s** | O número de RU/s que são usados pela operação |
| **coleçãoRid** | **collectionId_s** | A identificação única para a coleção.|
| **duração** | **duration_d** | A duração da operação, em milissegundos. |
| **solicitaçãoLength** | **requestLength_s** | A duração do pedido, em bytes. |
| **respostaLength** | **responseLength_s** | A duração da resposta, em bytes.|
| **recursoTokenPermissionId** | **resourceTokenPermissionId_s** | Esta propriedade indica o id de permissão de sinal de recurso que você especificou. Para saber mais sobre permissões, consulte o acesso Seguro ao seu artigo [de dados.](./secure-access-to-data.md#permissions) |
| **recursoTokenPermissionMode** | **resourceTokenPermissionMode_s** | Esta propriedade indica o modo de permissão que definiu ao criar o token de recurso. O modo de permissão pode ter valores como "todos" ou "ler". Para saber mais sobre permissões, consulte o acesso Seguro ao seu artigo [de dados.](./secure-access-to-data.md#permissions) |
| **recursoTokenUserRid** | **resourceTokenUserRid_s** | Este valor não é vazio quando [os tokens de recursos](./secure-access-to-data.md#resource-tokens) são utilizados para a autenticação. O valor aponta para o ID de recurso do utilizador. |
| **respostaLength** | **responseLength_s** | A duração da resposta, em bytes.|

Para obter uma lista de todas as categorias de registos do Azure Monitor e links para esquemas associados, consulte [as categorias e esquemas de Registos do Monitor Azure.](../azure-monitor/essentials/resource-logs-schema.md) 

## <a name="azure-monitor-logs-tables"></a>Tabelas de registos do monitor Azure

A Azure Cosmos DB utiliza as tabelas Kusto a partir de Registos monitores Azure. Pode consultar estas tabelas com analítica de log. Para obter uma lista de utilizações de fardos de Kusto, consulte o artigo de referência da [tabela Azure Monitor Logs.](/azure/azure-monitor/reference/tables/tables-resourcetype#azure-cosmos-db)

## <a name="see-also"></a>Consulte também

- Consulte [a Monitorização Azure Cosmos DB](monitor-cosmos-db.md) para uma descrição da monitorização do Azure Cosmos DB.
- Consulte [os recursos de Monitor Azure com o Azure Monitor](../azure-monitor/essentials/monitor-azure-resource.md) para obter informações sobre a monitorização dos recursos do Azure.