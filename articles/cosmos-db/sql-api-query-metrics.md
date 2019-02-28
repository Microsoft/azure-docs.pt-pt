---
title: Métricas de consulta SQL para o Azure Cosmos DB SQL API
description: Saiba mais sobre como instrumentar e depurar o desempenho de consulta SQL de pedidos do Azure Cosmos DB.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 11/02/2017
ms.author: sngun
ms.openlocfilehash: c7b62f66830e17fd8f6607e0a629307a9ab6fc78
ms.sourcegitcommit: 1afd2e835dd507259cf7bb798b1b130adbb21840
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/28/2019
ms.locfileid: "56983596"
---
# <a name="tuning-query-performance-with-azure-cosmos-db"></a>Otimização do desempenho de consulta com o Azure Cosmos DB

O Azure Cosmos DB fornece uma [API de SQL para consultar dados](how-to-sql-query.md), sem a necessidade de esquema ou índices secundários. Este artigo fornece as seguintes informações para desenvolvedores:

* Detalhes de alto nível sobre como funciona a execução de consultas SQL do Azure Cosmos DB
* Obter detalhes sobre os cabeçalhos de solicitação e resposta da consulta e as opções do SDK de cliente
* Dicas e melhores práticas para desempenho da consulta
* Exemplos de como utilizar as estatísticas de execução do SQL para depurar o desempenho da consulta

## <a name="about-sql-query-execution"></a>Sobre a execução da consulta SQL

Azure Cosmos DB, vai armazenar dados em contentores, que podem crescer para qualquer [débito de tamanho ou a pedido do armazenamento](partition-data.md). O Azure Cosmos DB dimensiona facilmente dados pelas partições físicas nos bastidores para lidar com crescimento de dados ou aumentar o débito aprovisionado. Pode emitir consultas SQL a nenhum contêiner usando a API REST ou um suportadas [SDKs de SQL](sql-api-sdk-dotnet.md).

Uma breve descrição geral da criação de partições: definir uma chave de partição, como "Cidade", que determina como os dados são divididos em partições físicas. Dados que pertencem a chave de partição única (por exemplo, "Cidade" = = "Seattle") são armazenados dentro de uma partição física, mas normalmente uma única partição física tem várias chaves de partição. Quando uma partição atinge o tamanho de armazenamento, o serviço de forma totalmente integrada divide a partição em duas novas partições e divide a chave de partição uniformemente entre estas partições. Uma vez que as partições são transitórias, as APIs utilizam uma abstração de um "partição intervalo de chaves", que denota os intervalos de hashes de chave de partição. 

Quando emite uma consulta ao Azure Cosmos DB, o SDK efetua estes passos lógicos:

* Analisar a consulta SQL para determinar o plano de execução da consulta. 
* Se a consulta inclui um filtro contra a chave de partição, como `SELECT * FROM c WHERE c.city = "Seattle"`, é encaminhado para uma única partição. Se a consulta não tem um filtro numa chave de partição, em seguida, ele é executado em todas as partições e os resultados são mesclados do lado do cliente.
* A consulta é executada em cada partição em série ou parallel, com base na configuração de cliente. Em cada partição, a consulta poderá fazer um ou mais ida e volta, dependendo da complexidade da consulta, configurado o tamanho da página e aprovisionar o débito da coleção. Cada execução devolve o número de [unidades de pedido](request-units.md) consumidos pela execução da consulta e, opcionalmente, estatísticas de execução de consulta. 
* O SDK efetua um resumo dos resultados da consulta entre partições. Por exemplo, se a consulta envolve uma ORDER BY em várias partições, em seguida, resultados de partições individuais são ordenados de intercalação para devolver resultados em ordem classificada globalmente. Se a consulta é uma agregação, como `COUNT`, as contagens de partições individuais são somadas para produzir a contagem de geral.

Os SDKs oferecem várias opções para a execução da consulta. Por exemplo, no .NET em que estas opções estão disponíveis no `FeedOptions` classe. A tabela seguinte descreve estas opções e seu impacto tempo de execução da consulta. 

| Opção | Descrição |
| ------ | ----------- |
| `EnableCrossPartitionQuery` | Tem de ser definido como verdadeiro para qualquer consulta que necessita para ser executados em mais de uma partição. Este é um sinalizador explícito para que possa fazer compensações consciente de desempenho durante o tempo de desenvolvimento. |
| `EnableScanInQuery` | Deve ser definida como verdadeira se tiver optado por fora de indexação, mas pretende executar a consulta através de uma análise de qualquer forma. Apenas aplicável se a indexação para o caminho de filtro solicitado está desativada. | 
| `MaxItemCount` | O número máximo de itens a devolver por ida e volta ao servidor. Por definição como -1, pode permitir que o servidor de gerir o número de itens. Em alternativa, pode reduzir este valor para recuperar apenas um pequeno número de itens por ida e volta. 
| `MaxBufferedItemCount` | Esta é uma opção do lado do cliente e utilizado para limitar o consumo de memória, ao realizar entre partições ORDER BY. Um valor mais alto ajuda a reduzir a latência da classificação de entre partições. |
| `MaxDegreeOfParallelism` | Obtém ou define o número de operações simultâneas são executados do lado do cliente durante a execução paralela da consulta no serviço de base de dados do Azure Cosmos DB. Um valor de propriedade positivo limita o número de operações simultâneas para o valor do conjunto. Se estiver definido como inferior a 0, o sistema decide automaticamente o número de operações simultâneas para executar. |
| `PopulateQueryMetrics` | Registo detalhado de estatísticas de tempo gasto em fases de execução da consulta, como tempo de compilação, o tempo de ciclo de índice e o documento de ativa o tempo de carga. Pode compartilhar a saída de estatísticas de consulta com suporte do Azure para diagnosticar problemas de desempenho de consulta. |
| `RequestContinuation` | Pode retomar a execução da consulta ao passar o token de continuação opaco devolvido por qualquer consulta. O token de continuação encapsula todo o estado necessário para a execução da consulta. |
| `ResponseContinuationTokenLimitInKb` | Pode limitar o tamanho máximo do token de continuação devolvido pelo servidor. Poderá ter de definir esta opção se o seu host de aplicativo tem limites no tamanho do cabeçalho de resposta. Definir este pode aumentar a duração e o RUs consumidas para a consulta global.  |

Por exemplo, vamos dar um exemplo de consulta numa chave de partição que solicitou numa coleção com `/city` como partição de chave e ser provisionada com 100 000 RU/s de débito. Pedido esta consulta utilizando `CreateDocumentQuery<T>` no .NET semelhante ao seguinte:

```cs
IDocumentQuery<dynamic> query = client.CreateDocumentQuery(
    UriFactory.CreateDocumentCollectionUri(DatabaseName, CollectionName), 
    "SELECT * FROM c WHERE c.city = 'Seattle'", 
    new FeedOptions 
    { 
        PopulateQueryMetrics = true, 
        MaxItemCount = -1, 
        MaxDegreeOfParallelism = -1, 
        EnableCrossPartitionQuery = true 
    }).AsDocumentQuery();

FeedResponse<dynamic> result = await query.ExecuteNextAsync();
```

O trecho SDK mostrado acima, corresponde ao pedido de REST API seguinte:

```
POST https://arramacquerymetrics-westus.documents.azure.com/dbs/db/colls/sample/docs HTTP/1.1
x-ms-continuation: 
x-ms-documentdb-isquery: True
x-ms-max-item-count: -1
x-ms-documentdb-query-enablecrosspartition: True
x-ms-documentdb-query-parallelizecrosspartitionquery: True
x-ms-documentdb-query-iscontinuationexpected: True
x-ms-documentdb-populatequerymetrics: True
x-ms-date: Tue, 27 Jun 2017 21:52:18 GMT
authorization: type%3dmaster%26ver%3d1.0%26sig%3drp1Hi83Y8aVV5V6LzZ6xhtQVXRAMz0WNMnUuvriUv%2b4%3d
x-ms-session-token: 7:8,6:2008,5:8,4:2008,3:8,2:2008,1:8,0:8,9:8,8:4008
Cache-Control: no-cache
x-ms-consistency-level: Session
User-Agent: documentdb-dotnet-sdk/1.14.1 Host/32-bit MicrosoftWindowsNT/6.2.9200.0
x-ms-version: 2017-02-22
Accept: application/json
Content-Type: application/query+json
Host: arramacquerymetrics-westus.documents.azure.com
Content-Length: 52
Expect: 100-continue

{"query":"SELECT * FROM c WHERE c.city = 'Seattle'"}
```

Cada página de execução de consulta corresponde a uma API REST `POST` com o `Accept: application/query+json` cabeçalho e a consulta SQL no corpo. Cada consulta faz com que um ou mais de ida e volta para o servidor com o `x-ms-continuation` token refletido entre o cliente e servidor para continuar a execução. As opções de configuração no FeedOptions são transmitidas para o servidor sob a forma de cabeçalhos de pedido. Por exemplo, `MaxItemCount` corresponde ao `x-ms-max-item-count`. 

A solicitação retorna a resposta (truncado para facilitar a leitura) seguintes:

```
HTTP/1.1 200 Ok
Cache-Control: no-store, no-cache
Pragma: no-cache
Transfer-Encoding: chunked
Content-Type: application/json
Server: Microsoft-HTTPAPI/2.0
Strict-Transport-Security: max-age=31536000
x-ms-last-state-change-utc: Tue, 27 Jun 2017 21:01:57.561 GMT
x-ms-resource-quota: documentSize=10240;documentsSize=10485760;documentsCount=-1;collectionSize=10485760;
x-ms-resource-usage: documentSize=1;documentsSize=884;documentsCount=2000;collectionSize=1408;
x-ms-item-count: 2000
x-ms-schemaversion: 1.3
x-ms-alt-content-path: dbs/db/colls/sample
x-ms-content-path: +9kEANVq0wA=
x-ms-xp-role: 1
x-ms-documentdb-query-metrics: totalExecutionTimeInMs=33.67;queryCompileTimeInMs=0.06;queryLogicalPlanBuildTimeInMs=0.02;queryPhysicalPlanBuildTimeInMs=0.10;queryOptimizationTimeInMs=0.00;VMExecutionTimeInMs=32.56;indexLookupTimeInMs=0.36;documentLoadTimeInMs=9.58;systemFunctionExecuteTimeInMs=0.00;userFunctionExecuteTimeInMs=0.00;retrievedDocumentCount=2000;retrievedDocumentSize=1125600;outputDocumentCount=2000;writeOutputTimeInMs=18.10;indexUtilizationRatio=1.00
x-ms-request-charge: 604.42
x-ms-serviceversion: version=1.14.34.4
x-ms-activity-id: 0df8b5f6-83b9-4493-abda-cce6d0f91486
x-ms-session-token: 2:2008
x-ms-gatewayversion: version=1.14.33.2
Date: Tue, 27 Jun 2017 21:59:49 GMT
```

Os cabeçalhos de resposta da chave retornados da consulta incluem o seguinte:

| Opção | Descrição |
| ------ | ----------- |
| `x-ms-item-count` | O número de itens devolvidos na resposta. Isto é dependente do fornecido `x-ms-max-item-count`, o número de itens que podem ser ajustadas o tamanho de payload de resposta máximo, o débito aprovisionado e o tempo de execução da consulta. |  
| `x-ms-continuation:` | O token de continuação para retomar a execução da consulta, se os resultados adicionais estão disponíveis. | 
| `x-ms-documentdb-query-metrics` | As estatísticas de consulta para a execução. Esta é uma seqüência de caracteres delimitada que contém as estatísticas de tempo gasto em várias fases de execução da consulta. If retornado `x-ms-documentdb-populatequerymetrics` está definido como `True`. | 
| `x-ms-request-charge` | O número de [unidades de pedido](request-units.md) consumidos pela consulta. | 

Para obter detalhes sobre as opções e cabeçalhos de pedido de REST API, consulte [consulta de recursos com a API REST](https://docs.microsoft.com/rest/api/cosmos-db/querying-cosmosdb-resources-using-the-rest-api).

## <a name="best-practices-for-query-performance"></a>Melhores práticas para desempenho da consulta
Seguem-se os fatores mais comuns que afetam o desempenho das consultas do Azure Cosmos DB. Vamos aprofundar mais cada um desses tópicos neste artigo.

| Fator | Sugestão | 
| ------ | -----| 
| Débito aprovisionado | Medir RU por consulta e certifique-se de que tem o débito aprovisionado necessário para as suas consultas. | 
| Criação de partições e chaves de partição | Favorece o consultas com o valor de chave de partição na cláusula de filtro de baixa latência. |
| Opções de consulta e SDK | Siga as melhores práticas do SDK, como conectividade direta e otimizar as opções de execução de consulta do lado do cliente. |
| Latência de rede | Rede sobrecarga na medida em conta e utilizar as APIs multi-homing para ler a partir da região mais próxima. |
| Política de Indexação | Certifique-se de que tem a indexação caminhos/política necessária para a consulta. |
| Métricas de execução de consulta | Analise as métricas de execução de consulta para identificar potenciais reescritas de formas de dados e de consultas.  |

### <a name="provisioned-throughput"></a>Débito aprovisionado
No Cosmos DB, criar contentores de dados, cada um com débito reservado, expressado em pedido (RU) de unidades por-segundo. Uma leitura de um documento de 1 KB é 1 RU e cada operação (incluindo consultas) são normalizados para um número fixo de RUs com base na sua complexidade. Por exemplo, se tiver de 1000 RU/s aprovisionada para o contentor e tiver uma consulta como `SELECT * FROM c WHERE c.city = 'Seattle'` que consome 5 RUs, em seguida, pode realizar (1000 RU/s) / (5 RU/consulta) = 200 consulta/s estas consultas por segundo. 

Se submeter consultas de mais de 200/segundo, o serviço começa a limitação de taxa de pedidos recebidos acima 200/s. Os SDKs processam automaticamente neste caso, efetuando uma repetição/término, portanto, pode observar uma latência superior para estas consultas. Aumentar o débito aprovisionado para o valor necessário melhora o débito e latência de consulta. 

Para saber mais sobre unidades de pedido, consulte [unidades de pedido](request-units.md).

### <a name="partitioning-and-partition-keys"></a>Criação de partições e chaves de partição
Com o Azure Cosmos DB, normalmente consultas realizam pela seguinte ordem de mais rápida/mais eficiente para mais lenta/menos eficiente. 

* OBTER uma chave de partição única e a chave do item
* Consultar com uma cláusula de filtro numa chave de partição única
* Consultar sem uma cláusula de filtro de endereços ou intervalos de igualdade em qualquer propriedade
* Sem filtros de consulta

As consultas que precisam consultar todas as partições têm latência superior e podem consumir RUs superior. Uma vez que cada partição tem de indexação automática em relação a todas as propriedades, a consulta pode ser satisfeita de forma eficiente do índice neste caso. Pode fazer consultas que abrangem as partições mais rápido, utilizando as opções de paralelismo.

Para saber mais sobre a criação de partições e chaves de partição, veja [criação de partições no Azure Cosmos DB](partition-data.md).

### <a name="sdk-and-query-options"></a>Opções de consulta e SDK
Ver [sugestões de desempenho](performance-tips.md) e [testes de desempenho](performance-testing.md) para saber como obter o melhor desempenho do lado do cliente do Azure Cosmos DB. Isto inclui com os SDKs mais recentes, configurações específicas da plataforma, como o número predefinido de ligações, frequência da coleta de lixo, a configurar e utilizar as opções de conectividade simples, como o Direct/TCP. 


#### <a name="max-item-count"></a>Contagem Máxima de Itens
Para consultas, o valor de `MaxItemCount` pode ter um impacto significativo no momento da consulta de ponto-a-ponto. Cada ida e volta para o servidor irá devolver nada mais do que o número de itens na `MaxItemCount` (predefinição de 100 itens). Definir este tipo como um valor mais alto (-1 é a máxima e recomendado) irá melhorar a sua duração de consulta geral ao limitar o número de ida e volta entre o servidor e cliente, especialmente para consultas com conjuntos de resultados grande.

```cs
IDocumentQuery<dynamic> query = client.CreateDocumentQuery(
    UriFactory.CreateDocumentCollectionUri(DatabaseName, CollectionName), 
    "SELECT * FROM c WHERE c.city = 'Seattle'", 
    new FeedOptions 
    { 
        MaxItemCount = -1, 
    }).AsDocumentQuery();
```

#### <a name="max-degree-of-parallelism"></a>Grau máximo de paralelismo
Para consultas, otimizar o `MaxDegreeOfParallelism` para identificar as melhores configurações para a sua aplicação, especialmente se executar consultas entre partições (sem um filtro no valor de chave de partição). `MaxDegreeOfParallelism`  controla o número máximo de tarefas paralelas, ou seja, o número máximo de partições visitas em paralelo. 

```cs
IDocumentQuery<dynamic> query = client.CreateDocumentQuery(
    UriFactory.CreateDocumentCollectionUri(DatabaseName, CollectionName), 
    "SELECT * FROM c WHERE c.city = 'Seattle'", 
    new FeedOptions 
    { 
        MaxDegreeOfParallelism = -1, 
        EnableCrossPartitionQuery = true 
    }).AsDocumentQuery();
```

Vamos supor que
* 1!d = número máximo predefinido de tarefas paralelas (= número total de processador no computador cliente)
* P = especificado pelo utilizador número máximo de tarefas paralelas
* N = número de partições que tem de ser visitado pela resposta a uma consulta

Seguem-se as implicações de como as consultas paralelas comportaria para diferentes valores de P.
* (P = = 0) = > modo Serial
* (P = = 1) = > máximo de uma tarefa
* (P > 1) = > tarefas paralelas de Min (P, N) 
* (P < 1) = > tarefas paralelas de Min (N, D)

Notas de versão do SDK e detalhes sobre classes implementadas e métodos, consulte [SDKs de SQL](sql-api-sdk-dotnet.md)

### <a name="network-latency"></a>Latência de rede
Ver [distribuição global do Azure Cosmos DB](tutorial-global-distribution-sql-api.md) para saber como configurar a distribuição global e ligar-se para a região mais próxima. Latência de rede tem um impacto significativo no desempenho de consulta quando precisar de fazer várias viagens de ida e volta ou obter um grande conjunto de resultados de consulta. 

A seção sobre as métricas de execução de consulta explica como obter o tempo de execução do servidor de consultas ( `totalExecutionTimeInMs`), para que é capaz de distinguir entre o tempo gasto na execução da consulta e o tempo gasto em trânsito de rede.

### <a name="indexing-policy"></a>Política de indexação
Ver [configurar a política de indexação](index-policy.md) para indexação caminhos, tipos e modos e como elas impactam a execução da consulta. Por predefinição, a política de indexação utiliza indexação de Hash para cadeias de caracteres, que é eficiente para consultas de igualdade, mas não para consultas de intervalo/ordenar por consultas. Se precisar de consultas de intervalo para cadeias de caracteres, é recomendável especificar o tipo de índice do intervalo para todas as cadeias de caracteres. 

Por predefinição, o Azure Cosmos DB será aplicada a indexação automática para todos os dados. Elevado desempenho inserir cenários, considere excluir caminhos como isto irá reduzir o custo de RU para cada operação de inserção. 

## <a name="query-execution-metrics"></a>Métricas de execução de consulta
Pode obter métricas detalhadas da execução de consulta ao transmitir o opcional `x-ms-documentdb-populatequerymetrics` cabeçalho (`FeedOptions.PopulateQueryMetrics` no SDK do .NET). O valor devolvido na `x-ms-documentdb-query-metrics` tem os seguintes pares de chave-valor destina a resolução de problemas de execução da consulta avançada. 

```cs
IDocumentQuery<dynamic> query = client.CreateDocumentQuery(
    UriFactory.CreateDocumentCollectionUri(DatabaseName, CollectionName), 
    "SELECT * FROM c WHERE c.city = 'Seattle'", 
    new FeedOptions 
    { 
        PopulateQueryMetrics = true, 
    }).AsDocumentQuery();

FeedResponse<dynamic> result = await query.ExecuteNextAsync();

// Returns metrics by partition key range Id
IReadOnlyDictionary<string, QueryMetrics> metrics = result.QueryMetrics;

```

| Métrica | Unidade | Descrição | 
| ------ | -----| ----------- |
| `totalExecutionTimeInMs` | milissegundos | Tempo de execução de consulta | 
| `queryCompileTimeInMs` | milissegundos | Tempo de compilação de consulta  | 
| `queryLogicalPlanBuildTimeInMs` | milissegundos | Tempo para criar o plano de consulta de lógica | 
| `queryPhysicalPlanBuildTimeInMs` | milissegundos | Tempo para criar o plano de consulta físico | 
| `queryOptimizationTimeInMs` | milissegundos | O tempo gasto na otimização de consulta | 
| `VMExecutionTimeInMs` | milissegundos | Tempo despendido no tempo de execução de consulta | 
| `indexLookupTimeInMs` | milissegundos | O tempo gasto na camada física de índice | 
| `documentLoadTimeInMs` | milissegundos | Tempo gasto no carregamento de documentos  | 
| `systemFunctionExecuteTimeInMs` | milissegundos | Tempo total gasto funções (interna) do sistema em execução em milissegundos  | 
| `userFunctionExecuteTimeInMs` | milissegundos | Tempo total gasto as funções definidas pelo utilizador em execução, em milissegundos | 
| `retrievedDocumentCount` | count | Número total de documentos obtidos  | 
| `retrievedDocumentSize` | bytes | Tamanho total de documentos obtidos em bytes  | 
| `outputDocumentCount` | count | Número de documentos de saída | 
| `writeOutputTimeInMs` | milissegundos | Tempo de execução de consulta em milissegundos | 
| `indexUtilizationRatio` | proporção (< = 1) | Rácio do número de documentos correspondida ao filtro para o número de documentos carregados  | 

Os SDKs de cliente internamente podem fazer várias operações de consulta para atender a consulta em cada partição. O cliente faz mais do que uma chamada por partição se excederem os resultados totais `x-ms-max-item-count`, se a consulta excede o débito aprovisionado para a partição, ou se a carga de consulta atingir o tamanho máximo por página ou se a consulta atingir o sistema alocado limite de tempo limite. Cada execução de consulta parcial retorna um `x-ms-documentdb-query-metrics` para essa página. 

Aqui estão alguns exemplos de consultas e como interpretar algumas das métricas retornado da execução da consulta: 

| Consulta | Métrica de exemplo | Descrição | 
| ------ | -----| ----------- |
| `SELECT TOP 100 * FROM c` | `"RetrievedDocumentCount": 101` | O número de documentos obtido é a mais de 100 a 1 de acordo com a cláusula TOP. Tempo de consulta principalmente é gasto em `WriteOutputTime` e `DocumentLoadTime` , uma vez que é uma análise. | 
| `SELECT TOP 500 * FROM c` | `"RetrievedDocumentCount": 501` | RetrievedDocumentCount agora é mais elevado (500 + 1 para corresponder a cláusula TOP). | 
| `SELECT * FROM c WHERE c.N = 55` | `"IndexLookupTime": "00:00:00.0009500"` | Cerca de 0.9 ms é gasto em IndexLookupTime para uma pesquisa de chave, porque é uma pesquisa de índice `/N/?`. | 
| `SELECT * FROM c WHERE c.N > 55` | `"IndexLookupTime": "00:00:00.0017700"` | Um pouco mais de tempo (ms versão 1.7) despendido no IndexLookupTime através de uma verificação de intervalo, porque é uma pesquisa de índice `/N/?`. | 
| `SELECT TOP 500 c.N FROM c` | `"IndexLookupTime": "00:00:00.0017700"` | Mesmo tempo gasto em `DocumentLoadTime` como as consultas anteriores, mas inferior `WriteOutputTime` porque estamos estiver Projetando apenas uma propriedade. | 
| `SELECT TOP 500 udf.toPercent(c.N) FROM c` | `"UserDefinedFunctionExecutionTime": "00:00:00.2136500"` | Cerca de 213 ms é gasto na `UserDefinedFunctionExecutionTime` execução do UDF em cada valor de `c.N`. |
| `SELECT TOP 500 c.Name FROM c WHERE STARTSWITH(c.Name, 'Den')` | `"IndexLookupTime": "00:00:00.0006400", "SystemFunctionExecutionTime": "00:00:00.0074100"` | Cerca de 0,6 ms é gasto na `IndexLookupTime` no `/Name/?`. Maior parte do tempo de execução de consulta (~ 7 ms) em `SystemFunctionExecutionTime`. |
| `SELECT TOP 500 c.Name FROM c WHERE STARTSWITH(LOWER(c.Name), 'den')` | `"IndexLookupTime": "00:00:00", "RetrievedDocumentCount": 2491,  "OutputDocumentCount": 500` | Consulta é executada como uma análise porque utiliza `LOWER`, e 500 fora 2491 documentos obtidos são devolvidos. |


## <a name="next-steps"></a>Passos Seguintes
* Para saber mais sobre os operadores de consulta SQL e palavras-chave suportados, consulte [consulta SQL](how-to-sql-query.md). 
* Para saber mais sobre unidades de pedido, consulte [unidades de pedido](request-units.md).
* Para saber mais sobre a política de indexação, consulte o artigo [política de indexação](index-policy.md) 


