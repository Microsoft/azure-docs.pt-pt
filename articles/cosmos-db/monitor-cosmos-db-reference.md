---
title: Referência de dados de monitorização da Azure Cosmos DB Microsoft Docs
description: Referência de log e métricas para monitorização de dados do Azure Cosmos DB.
author: bwren
services: azure-monitor
ms.service: azure-monitor
ms.topic: reference
ms.date: 11/11/2019
ms.author: bwren
ms.custom: subject-monitoring
ms.subservice: logs
ms.openlocfilehash: d243224192b5761af45d387690f5fb41b84481e6
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77588727"
---
# <a name="azure-cosmos-db-monitoring-data-reference"></a>Referência dos dados de monitorização do Azure Cosmos DB
Este artigo fornece uma referência dos registos e dos dados métricos recolhidos para analisar o desempenho e a disponibilidade do Azure Cosmos DB. Consulte [a Monitorização do Cosmos DB](monitor-cosmos-db.md) para obter detalhes sobre a recolha e análise de dados de monitorização para o Azure Cosmos DB.


## <a name="resource-logs"></a>Registos do recurso
A tabela seguinte lista as propriedades dos registos de recursos da Azure Cosmos DB quando são recolhidas em Registos de Monitores Azure ou armazenamento azure. Nos Registos do Monitor Azure, são recolhidos na tabela **AzureDiagnostics** com um valor **De recursos da** *MICROSOFT. DOCUMENTDB*. 

| Campo de armazenamento azure ou propriedade | Propriedade Azure Monitor Logs | Descrição |
| --- | --- | --- |
| **tempo** | **TimeGenerated** | A data e a hora (UTC) quando ocorreu a operação ocorreram. |
| **recursosId** | **Recurso** | A conta Azure Cosmos DB para a qual os registos estão ativados.|
| **categoria** | **Categoria** | Para os registos do Azure Cosmos DB, **DataPlaneRequests**, **MongoRequests**, **QueryRuntimeStatistics**, **PartitionKeyStatistics**, **PartitionKeyRUConsumption,** **ControlPlaneRequests** são os tipos de registo disponíveis. |
| **operaçãoNome** | **OperationName** | Nome da operação. Este valor pode ser qualquer uma das seguintes operações: Criar, Atualizar, Ler, Ler, Excluir, Substituir, Executar, SqlQuery, Consulta, JSQuery, HeadFeed ou Upsert.   |
| **propriedades** | n/d | O conteúdo deste campo é descrito nas linhas que se seguem. |
| **atividadeId** | **activityId_g** | O GUIA único para a operação logged. |
| **userAgent** | **userAgent_s** | Uma cadeia que especifica o agente utilizador cliente que está a realizar o pedido. O formato é {nome do agente utilizador}/{versão}.|
| **pedidoRecursoType** | **requestResourceType_s** | O tipo de recurso acedido. Este valor pode ser qualquer um dos seguintes tipos de recursos: Base de Dados, Recipiente, Documento, Anexo, Utilizador, Permissão, Procedimento Armazenado, Gatilho, Função Definida do Utilizador ou Oferta. |
| **estadoCódigo** | **statusCode_s** | O estado de resposta da operação. |
| **solicitarResourceId** | **ResourceId** | O recurso Id que diz respeito ao pedido. O valor pode apontar para base de dadosRid, coleção rid ou documentRid dependendo da operação executada.|
| **clienteIpAddress** | **clientIpAddress_s** | O endereço IP do cliente. |
| **pedidoCharge** | **requestCharge_s** | O número de RUs que são usados pela operação |
| **coleçãoRid** | **collectionId_s** | A identificação única para a coleção.|
| **duração** | **duration_s** | A duração da operação, em milissegundos. |
| **pedidoComprimento** | **requestLength_s** | A duração do pedido, em bytes. |
| **respostaComprimento** | **responseLength_s** | A duração da resposta, em bytes.|
| **recursosTokenUserRid** | **resourceTokenUserRid_s** | Este valor não é vazio quando são [utilizadas fichas](https://docs.microsoft.com/azure/cosmos-db/secure-access-to-data#resource-tokens) de recurso para autenticação. O valor aponta para a identificação do recurso do utilizador. |

Para obter uma lista de todas as categorias de registo do Monitor Azure e ligações a schemas associados, consulte as categorias de [Registos do Monitor Azure e os esquemas](../azure-monitor/platform/diagnostic-logs-schema.md). 

## <a name="metrics"></a>Métricas
As tabelas seguintes listam as métricas da plataforma recolhidas para o Azure CosmOS DB. Todas as métricas são armazenadas nas **métricas padrão cosmos DB**do espaço.

Para obter uma lista de todas as métricas de suporte do Monitor Azure (incluindo cosmosDB), consulte [as métricas suportadas pelo Monitor Azure.](../azure-monitor/platform/metrics-supported.md) 

#### <a name="request-metrics"></a>Métricas de pedido
            
|Métrica (nome de exibição métrica)|Unidade (Tipo de agregação) |Descrição|Dimensões| Granularidades do tempo| Mapeamento métrico legado | Utilização |
|---|---|---|---| ---| ---| ---|
| Total de Pedidos (Total de Pedidos) | Contagem (Contagem) | Número de pedidos feitos| Nome de base de dados, nome de recolha, região, código de estado| Todos | TotalRequests, Http 2xx, Http 3xx, Http 400, Http 401, Erro do Servidor Interno, Indisponível de Serviço, Pedidos Throttled, Pedidos Médios por Segundo | Utilizado para monitorizar pedidos por código de estado, recipiente a um minuto de granularidade. Para obter pedidos médios por segundo, use a agregação do Conde ao minuto e divida por 60. |
| MetadadosPedidos (Pedidos de Metadados) |Contagem (Contagem) | Contagem de pedidos de metadados. O Azure Cosmos DB mantém o recipiente de metadados do sistema para cada conta, que lhe permite enumerar coleções, bases de dados, etc., e as suas configurações, gratuitamente. | Nome de base de dados, nome de recolha, região, código de estado| Todos| |Usado para monitorizar os aceleradores devido a pedidos de metadados.|
| Pedidos de Mongo (Pedidos de Mongo) | Contagem (Contagem) | Número de pedidos de Mongo feitos | Nome de base de dados, nome de recolha, região, nome de comando, código de erro| Todos |Taxa de pedido de consulta de Mongo, Taxa de Pedido de Atualização de Mongo, Taxa de Pedido de Exclusão de Mongo, Taxa de Pedido de Inserção de Mongo, Taxa de Pedido de Contagem de Mongo| Usado para monitorizar erros de pedido de Mongo, utilizações por tipo de comando. |

#### <a name="request-unit-metrics"></a>Métricas da Unidade de Pedido

|Métrica (nome de exibição métrica)|Unidade (Tipo de agregação)|Descrição|Dimensões| Granularidades do tempo| Mapeamento métrico legado | Utilização |
|---|---|---|---| ---| ---| ---|
| MongoRequestCharge (Mongo Request Charge) | Contagem (Total) |Unidades de Pedido de Mongo Consumidas| Nome de base de dados, nome de recolha, região, nome de comando, código de erro| Todos |Mongo Consulta Pedido de Pedido, Taxa de Pedido de Atualização de Mongo, Mongo Excluir Taxa de Pedido, Pedido de Inserção mongo, Taxa de Pedido de Contagem de Mongo| Usado para monitorizar as RUs de recursos de Mongo em um minuto.|
| Total de Unidades de Pedidos (Unidades de Pedido Totais)| Contagem (Total) | Unidades de Pedido consumidas| Nome de base de dados, nome de recolha, região, código de estado |Todos| TotaldeUnidades de Pedidos| Usado para monitorizar o uso total de RU a um minuto granularidade. Para obter a RU média consumida por segundo, use a gregação total ao minuto e divida por 60.|
| ProvisionedThroughput (Provisioned Throughput)| Contagem (máximo) |Entrada aprovisionada na granularidade do contentor| Nome de base de dados, nome do recipiente| 5M| | Utilizado para monitorizar a entrada aprovisionada por recipiente.|

#### <a name="storage-metrics"></a>Métricas de armazenamento

|Métrica (nome de exibição métrica)|Unidade (Tipo de agregação)|Descrição|Dimensões| Granularidades do tempo| Mapeamento métrico legado | Utilização |
|---|---|---|---| ---| ---| ---|
| Armazenamento Disponível (Armazenamento Disponível) |Bytes (Total) | Total de armazenamento disponível reportado em granularidade de 5 minutos por região| Nome de base de dados, nome de recolha, região| 5M| Armazenamento Disponível| Utilizado para monitorizar a capacidade de armazenamento disponível (aplicável apenas para recolhas de armazenamento fixos) A granularidade mínima deve ser de 5 minutos.| 
| Utilização de dados (utilização de dados) |Bytes (Total) |Utilização total de dados reportada sinuosidade de 5 minutos por região| Nome de base de dados, nome de recolha, região| 5M |Tamanho dos dados | Utilizado para monitorizar a utilização total de dados no contentor e na região, a granularidade mínima deve ser de 5 minutos.|
| Utilização do índice (utilização do índice) | Bytes (Total) |Utilização total do Índice reportada em granularidade de 5 minutos por região| Nome de base de dados, nome de recolha, região| 5M| Tamanho do índice| Utilizado para monitorizar a utilização total de dados no contentor e na região, a granularidade mínima deve ser de 5 minutos. |
| DocumentQuota (Quota de Documentos) | Bytes (Total) | Quota total de armazenamento reportada em granularidade de 5 minutos por região.| Nome de base de dados, nome de recolha, região| 5M |Capacidade de Armazenamento| Utilizado para monitorizar a quota total no contentor e na região, a granularidade mínima deve ser de 5 minutos.|
| Contagem de documentos (Contagem de Documentos) | Contagem (Total) |Contagem total de documentos reportada em granularidade de 5 minutos por região| Nome de base de dados, nome de recolha, região| 5M |Contagem de Documentos|Utilizado para monitorizar a contagem de documentos no contentor e na região, a granularidade mínima deve ser de 5 minutos.|

#### <a name="latency-metrics"></a>Métricas de latência

|Métrica (nome de exibição métrica)|Unidade (Tipo de agregação)|Descrição|Dimensões| Granularidades do tempo| Utilização |
|---|---|---|---| ---| ---|
| ReplicaçãoLatency (Latência de Replicação)| Milliseconds (mínimo, máximo, média) | Latência de replicação P99 entre fonte e regiões-alvo para conta geoactiva| SourceRegion| Todos | Usado para monitorizar a latência de replicação P99 entre duas regiões para uma conta geo-replicada. |
| Latência lateral do servidor| MilliSeconds (Média) | Tempo tomado pelo servidor para processar o pedido. | Nome de recolha, Modo de Ligação, Nome de base de dados, tipo de operação, publicaPIType, região | Todos | Usado para monitorizar a latência de pedido no servidor Azure Cosmos DB. |



#### <a name="availability-metrics"></a>Métricas de disponibilidade

|Métrica (nome de exibição métrica) |Unidade (Tipo de agregação)|Descrição| Granularidades do tempo| Mapeamento métrico legado | Utilização |
|---|---|---|---| ---| ---|
| Disponibilidade de Serviços (Disponibilidade de Serviço)| Por cento (mínimo, máximo) | Conta solicita disponibilidade a uma hora de granularidade| 1H | Disponibilidade de Serviço | Representa a percentagem do total de pedidos aprovados. Considera-se que um pedido é falhado devido a erro do sistema se o código de estado for de 410, 500 ou 503 Utilizado para monitorizar a disponibilidade da conta à hora de granularidade. |


#### <a name="cassandra-api-metrics"></a>Métricas da API de Cassandra

|Métrica (nome de exibição métrica)|Unidade (Tipo de agregação)|Descrição|Dimensões| Granularidades do tempo| Utilização |
|---|---|---|---| ---| ---|
| CassandraRequests (Pedidos de Cassandra) | Contagem (Contagem) | Número de pedidos da API de Cassandra feitos| Nome de base de dados, nome de recolha, código de erro, região, tipo de funcionamento, ResourceType| Todos| Usado para monitorizar os pedidos de Cassandra a um minuto de granularidade. Para obter pedidos médios por segundo, use a agregação do Conde ao minuto e divida por 60.|
| CassandraRequestCharges (Cassandra Request Charges) | Conde (Soma, Min, Max, Avg) | Unidades de Pedido consumidas por pedidos da API de Cassandra| Nome de base de dados, nome de recolha, região, operação, Tipo de Recursos| Todos| Usado para monitorizar as RUs usadas por minuto por uma conta da Cassandra API.|
| CassandraConnectionClosures (Encerramentos de Conexão Cassandra) |Contagem (Contagem) |Número de Ligações Cassandra encerradas| ClosureReason| Todos | Usado para monitorizar a conectividade entre os clientes e a API De Db Cassandra Azure Cosmos.|

## <a name="see-also"></a>Veja também

- Consulte [a Monitorização Azure Cosmos DB](monitor-cosmos-db.md) para obter uma descrição da monitorização do Azure Cosmos DB.
- Consulte [a Monitorização dos recursos do Azure com o Monitor Azure](../azure-monitor/insights/monitor-azure-resource.md) para obter mais informações sobre a monitorização dos recursos do Azure.
