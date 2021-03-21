---
title: Métricas de consulta SQL para Azure Cosmos DB SQL API
description: Saiba como instrumentar e depurar o desempenho de consulta SQL dos pedidos de DB da Azure Cosmos.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 01/06/2021
ms.author: sngun
ms.custom: devx-track-csharp
ms.openlocfilehash: 019ca26143a4879efafa973299703f0abcb21162
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102488091"
---
# <a name="tuning-query-performance-with-azure-cosmos-db"></a>Desempenho de consulta de afinação com Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

A Azure Cosmos DB fornece um [API SQL para consulta de dados,](./sql-query-getting-started.md)sem necessidade de esquema ou índices secundários. Este artigo fornece as seguintes informações para os desenvolvedores:

* Detalhes de alto nível sobre como funciona a execução de consultas SQL da Azure Cosmos DB
* Detalhes sobre pedidos de consulta e cabeçalhos de resposta, e opções de SDK do cliente
* Dicas e boas práticas para o desempenho da consulta
* Exemplos de como utilizar as estatísticas de execução do SQL para depurar o desempenho da consulta

## <a name="about-sql-query-execution"></a>Sobre a execução de consulta SQL

Na Azure Cosmos DB, armazena dados em recipientes, que podem crescer a qualquer [tamanho de armazenamento ou produção de pedidos.](partitioning-overview.md) A Azure Cosmos DB escala os dados em divisórias físicas sob as capas para lidar com o crescimento de dados ou aumentar a produção a provisionada. Pode emitir consultas SQL a qualquer recipiente utilizando a API REST ou um dos [SDKs SQL suportados.](sql-api-sdk-dotnet.md)

Uma breve visão geral da partição: define-se uma chave de partição como "cidade", que determina como os dados são divididos entre divisórias físicas. Os dados pertencentes a uma única chave de partição (por exemplo, "city" == "Seattle") são armazenados dentro de uma partição física, mas tipicamente uma única partição física tem várias teclas de partição. Quando uma divisória atinge o seu tamanho de armazenamento, o serviço divide perfeitamente a partição em duas novas divisórias, e divide a chave de partição uniformemente através destas divisórias. Uma vez que as divisórias são transitórias, as APIs usam uma abstração de uma "gama de chaves de partição", que denota as gamas de hashes chave de partição. 

Quando emite uma consulta à Azure Cosmos DB, o SDK executa estes passos lógicos:

* Parse a consulta SQL para determinar o plano de execução de consultas. 
* Se a consulta incluir um filtro contra a tecla de partição, por `SELECT * FROM c WHERE c.city = "Seattle"` tipo, é encaminhado para uma única divisória. Se a consulta não tiver um filtro na chave de partição, então é executada em todas as divisórias, e os resultados são fundidos do lado do cliente.
* A consulta é executada dentro de cada divisória em série ou paralelo, com base na configuração do cliente. Dentro de cada divisória, a consulta pode fazer uma ou mais viagens de ida e volta dependendo da complexidade da consulta, tamanho de página configurado e produção prevista da coleção. Cada execução devolve o número de unidades de [pedido consumidas](request-units.md) por execução de consultas, e opcionalmente, estatísticas de execução de consultas. 
* O SDK realiza uma resumo dos resultados da consulta através das divisórias. Por exemplo, se a consulta envolve uma ORDEM BY entre divisórias, então os resultados de divisórias individuais são triagem para retorno resultados em ordem globalmente ordenada. Se a consulta for uma agregação `COUNT` como, as contagens de divisórias individuais são resumidas para produzir a contagem geral.

Os SDKs fornecem várias opções para a execução de consultas. Por exemplo, em .NET estas opções estão disponíveis na `FeedOptions` classe. A tabela a seguir descreve estas opções e como impactam o tempo de execução da consulta. 

| Opção | Descrição |
| ------ | ----------- |
| `EnableCrossPartitionQuery` | Deve ser definido como verdadeiro para qualquer consulta que exija ser executada em mais de uma divisória. Esta é uma bandeira explícita que lhe permite fazer trocas conscientes de desempenho durante o tempo de desenvolvimento. |
| `EnableScanInQuery` | Deve ser definido como verdadeiro se você optou por não indexar, mas quer executar a consulta através de uma digitalização de qualquer maneira. Só é aplicável se a indexação para a trajetória de filtro solicitada for desativada. | 
| `MaxItemCount` | O número máximo de itens para devolver por ida e volta ao servidor. Ao configurar para -1, pode deixar o servidor gerir o número de itens. Ou, você pode baixar este valor para recuperar apenas um pequeno número de itens por ida e volta. 
| `MaxBufferedItemCount` | Esta é uma opção do lado do cliente, e usada para limitar o consumo de memória ao executar a partação cruzada ORDER BY. Um valor mais elevado ajuda a reduzir a latência da triagem de divisórias cruzadas. |
| `MaxDegreeOfParallelism` | Obtém ou define o número de operações simultâneas do lado do cliente durante a execução paralela de consultas no serviço de base de dados Azure Cosmos. Um valor patrimonial positivo limita o número de operações simultâneas ao valor definido. Se for definido para menos de 0, o sistema decide automaticamente o número de operações simultâneas a executar. |
| `PopulateQueryMetrics` | Permite o registo detalhado das estatísticas do tempo gasto em várias fases de execução de consultas como tempo de compilação, tempo de ciclo de índice e tempo de carga documental. Pode partilhar a produção a partir de estatísticas de consulta com o Suporte Azure para diagnosticar problemas de desempenho de consulta. |
| `RequestContinuation` | Pode retomar a execução de consultas passando no token de continuação opaco devolvido por qualquer consulta. O token de continuação encapsula todo o estado necessário para a execução de consultas. |
| `ResponseContinuationTokenLimitInKb` | Pode limitar o tamanho máximo do token de continuação devolvido pelo servidor. Pode ter de definir isto se o seu anfitrião de aplicações tiver limites no tamanho do cabeçalho de resposta. A definição deste objetivo pode aumentar a duração global e as RUs consumidas para a consulta.  |

Por exemplo, vamos tomar um exemplo de consulta sobre a chave de partição solicitada numa coleção com `/city` a chave de partição e a provisionada com 100.000 RU/s de produção. Solicita esta consulta utilizando `CreateDocumentQuery<T>` em .NET o seguinte:

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

O corte SDK acima apresentado corresponde ao seguinte pedido de API REST:

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

Cada página de execução de consulta corresponde a uma API REST `POST` com o `Accept: application/query+json` cabeçalho, e a consulta SQL no corpo. Cada consulta faz uma ou mais viagens de ida e volta ao servidor com o `x-ms-continuation` símbolo ecoado entre o cliente e o servidor para retomar a execução. As opções de configuração nas Opções de Feed são transmitidas para o servidor sob a forma de cabeçalhos de pedido. Por exemplo, `MaxItemCount` corresponde a `x-ms-max-item-count` . 

O pedido devolve a seguinte resposta (truncada para legibilidade):

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

Os cabeçalhos de resposta principais devolvidos da consulta incluem o seguinte:

| Opção | Descrição |
| ------ | ----------- |
| `x-ms-item-count` | O número de artigos devolvidos na resposta. Isto depende do `x-ms-max-item-count` fornecido, do número de itens que podem ser encaixados dentro do tamanho máximo da carga útil de resposta, da produção prevista e do tempo de execução de consultas. |  
| `x-ms-continuation:` | O sinal de continuação para retomar a execução da consulta, se houver resultados adicionais disponíveis. | 
| `x-ms-documentdb-query-metrics` | As estatísticas de consulta para a execução. Esta é uma cadeia delimitada contendo estatísticas do tempo gasto nas várias fases da execução de consultas. Devolvido se `x-ms-documentdb-populatequerymetrics` estiver definido para `True` . | 
| `x-ms-request-charge` | O número de unidades de [pedido consumidas](request-units.md) pela consulta. | 

Para obter mais informações sobre os cabeçalhos e opções de pedidos da API REST, consulte [os recursos de consulta utilizando a API REST](/rest/api/cosmos-db/querying-cosmosdb-resources-using-the-rest-api).

## <a name="best-practices-for-query-performance"></a>Melhores práticas para desempenho de consulta
Seguem-se os fatores mais comuns que impactam o desempenho da consulta Azure Cosmos DB. Aprofundamos cada um destes tópicos neste artigo.

| Fator | Dica | 
| ------ | -----| 
| Débito aprovisionado | Meça RU por consulta e certifique-se de que tem a produção prevista para as suas consultas. | 
| Chaves de partição e partição | Favoreça as consultas com o valor da chave de partição na cláusula do filtro para baixa latência. |
| Opções de SDK e consulta | Siga as melhores práticas da SDK como conectividade direta e afina as opções de execução de consultas do lado do cliente. |
| Política de Indexação | Certifique-se de que tem os percursos/políticas de indexação necessários para a consulta. |
| Métricas de execução de consultas | Analise as métricas de execução de consulta para identificar potenciais reescritas de consulta e formas de dados.  |

### <a name="provisioned-throughput"></a>Débito aprovisionado
Na Cosmos DB, você cria recipientes de dados, cada um com produção reservada expressa nas unidades de pedido (RU) por segundo. Uma leitura de um documento de 1-KB é 1 RU, e cada operação (incluindo consultas) é normalizada a um número fixo de RUs com base na sua complexidade. Por exemplo, se tiver 1000 RU/s provisões para o seu recipiente, e tiver uma consulta como `SELECT * FROM c WHERE c.city = 'Seattle'` esta consome 5 RUs, então pode realizar (1000 RU/s) / (5 RU/consulta) = 200 consultas/s tais consultas por segundo. 

Se submeter mais de 200 consultas/seg, o serviço começa a limitar a taxa de pedidos de entrada acima de 200/s. Os SDKs lidam automaticamente com este caso executando uma retenção/retrip, pelo que poderá notar uma maior latência para estas consultas. Aumentar a produção a provisionada para o valor exigido melhora a sua latência e produção de consulta. 

Para saber mais sobre unidades de pedido, consulte [unidades Request](request-units.md).

### <a name="partitioning-and-partition-keys"></a>Chaves de partição e partição
Com a Azure Cosmos DB, normalmente as consultas funcionam na seguinte ordem, desde o mais rápido/mais eficiente até ao mais lento/menos eficiente. 

* GET em uma única chave de partição e chave de item
* Consulta com uma cláusula de filtro numa única chave de partição
* Consulta sem igualdade ou cláusula de filtro de alcance em qualquer propriedade
* Consulta sem filtros

As consultas que precisam de consultar todas as divisórias precisam de maior latência, e podem consumir RUs mais elevados. Uma vez que cada partição tem indexação automática contra todas as propriedades, a consulta pode ser servida eficientemente a partir do índice neste caso. Você pode fazer consultas que abrangem divisórias mais rapidamente usando as opções de paralelismo.

Para saber mais sobre chaves de partição e partição, consulte [Partition in Azure Cosmos DB](partitioning-overview.md).

### <a name="sdk-and-query-options"></a>Opções de SDK e consulta
Consulte [dicas de desempenho](performance-tips.md) e [testes](performance-testing.md) de desempenho para obter o melhor desempenho do lado do cliente da Azure Cosmos DB. Isto inclui a utilização dos mais recentes SDKs, configurações específicas da plataforma como o número padrão de ligações, a frequência da recolha de lixo e a utilização de opções de conectividade leves como Direct/TCP. 


#### <a name="max-item-count"></a>Contagem de artigos Max
Para consultas, o valor de `MaxItemCount` pode ter um impacto significativo no tempo de consulta de ponta a ponta. Cada ida e volta ao servidor não devolverá mais do que o número de itens `MaxItemCount` em (Predefinição de 100 itens). Definindo isto para um valor mais elevado (-1 é máximo, e recomendado) melhorará a duração geral da sua consulta, limitando o número de viagens de ida e volta entre o servidor e o cliente, especialmente para consultas com grandes conjuntos de resultados.

```cs
IDocumentQuery<dynamic> query = client.CreateDocumentQuery(
    UriFactory.CreateDocumentCollectionUri(DatabaseName, CollectionName), 
    "SELECT * FROM c WHERE c.city = 'Seattle'", 
    new FeedOptions 
    { 
        MaxItemCount = -1, 
    }).AsDocumentQuery();
```

#### <a name="max-degree-of-parallelism"></a>Grau Máximo de Paralelismo
Para consultas, afina-o `MaxDegreeOfParallelism` para identificar as melhores configurações para a sua aplicação, especialmente se efetuar consultas de partição cruzada (sem filtro no valor da chave de partição). `MaxDegreeOfParallelism`  controla o número máximo de tarefas paralelas, ou seja, o máximo de divisórias a visitar em paralelo. 

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
* D = Número máximo predefinido de tarefas paralelas (= número total de processador na máquina cliente)
* P = Número máximo especificado pelo utilizador de tarefas paralelas
* N = Número de divisórias que precisam de ser visitadas para responder a uma consulta

Seguem-se as implicações de como as consultas paralelas se comportariam para diferentes valores de P.
* (P == 0) => Modo série
* (P == 1) => Máximo de uma tarefa
* (P > 1) => Min (P, N) tarefas paralelas 
* (P < 1) => Min (N, D) tarefas paralelas

Para notas de lançamento da SDK, e detalhes sobre classes e métodos implementados ver [SQL SDKs](sql-api-sdk-dotnet.md)

### <a name="network-latency"></a>Latência de rede
Veja [a distribuição global da Azure Cosmos DB](tutorial-global-distribution-sql-api.md) para como configurar a distribuição global e ligar-se à região mais próxima. A latência da rede tem um impacto significativo no desempenho da consulta quando é necessário fazer várias viagens de ida e volta ou recuperar um grande conjunto de resultados a partir da consulta. 

A secção sobre métricas de execução de consulta explica como recuperar o tempo de execução do servidor de consultas ( ), para `totalExecutionTimeInMs` que possa diferenciar entre o tempo gasto na execução de consultas e o tempo gasto em trânsito de rede.

### <a name="indexing-policy"></a>Política de indexação
Consulte [a política de indexação de configuração](index-policy.md) para indexar caminhos, tipos e modos de indexação e como impactam a execução da consulta. Por padrão, a política de indexação utiliza a indexação de Hash para cadeias, o que é eficaz para consultas de igualdade, mas não para consultas de alcance/ordem por consultas. Se precisar de consultas de alcance para cordas, recomendamos especificar o tipo de índice de gama para todas as cordas. 

Por padrão, a Azure Cosmos DB aplicará a indexação automática a todos os dados. Para cenários de inserção de alto desempenho, considere a exclusão de caminhos, uma vez que isso reduzirá o custo ru para cada operação de inserção. 

## <a name="query-execution-metrics"></a>Métricas de execução de consultas
Pode obter métricas detalhadas sobre a execução de consultas através do cabeçalho opcional `x-ms-documentdb-populatequerymetrics` `FeedOptions.PopulateQueryMetrics` (no .NET SDK). O valor devolvido `x-ms-documentdb-query-metrics` tem os seguintes pares de valor-chave destinados a uma resolução avançada de problemas de execução de consultas. 

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

| Metric | Unidade | Description | 
| ------ | -----| ----------- |
| `totalExecutionTimeInMs` | milissegundos | Tempo de execução de consulta | 
| `queryCompileTimeInMs` | milissegundos | Tempo de compilação de consultas  | 
| `queryLogicalPlanBuildTimeInMs` | milissegundos | Hora de construir um plano de consulta lógica | 
| `queryPhysicalPlanBuildTimeInMs` | milissegundos | Hora de construir um plano de consulta física | 
| `queryOptimizationTimeInMs` | milissegundos | Tempo gasto na otimização da consulta | 
| `VMExecutionTimeInMs` | milissegundos | Tempo gasto em tempo de consulta | 
| `indexLookupTimeInMs` | milissegundos | Tempo gasto na camada de índice físico | 
| `documentLoadTimeInMs` | milissegundos | Tempo gasto em documentos de carregamento  | 
| `systemFunctionExecuteTimeInMs` | milissegundos | Funções do sistema de execução total (incorporado) em milissegundos  | 
| `userFunctionExecuteTimeInMs` | milissegundos | Tempo total gasto a executar funções definidas pelo utilizador em milissegundos | 
| `retrievedDocumentCount` | count | Número total de documentos recuperados  | 
| `retrievedDocumentSize` | bytes | Tamanho total dos documentos recuperados em bytes  | 
| `outputDocumentCount` | count | Número de documentos de saída | 
| `writeOutputTimeInMs` | milissegundos | Tempo de execução de consultas em milissegundos | 
| `indexUtilizationRatio` | relação (<=1) | Relação do número de documentos combinados pelo filtro com o número de documentos carregados  | 

Os SDKs do cliente podem internamente fazer múltiplas operações de consulta para servir a consulta dentro de cada divisória. O cliente faz mais do que uma chamada por partição se os resultados totais `x-ms-max-item-count` excederem, se a consulta exceder a produção prevista para a partição, ou se a carga útil de consulta atingir o tamanho máximo por página, ou se a consulta atingir o limite de tempo atribuído pelo sistema. Cada execução de consulta parcial devolve uma `x-ms-documentdb-query-metrics` para esta página. 

Aqui estão algumas consultas de amostra, e como interpretar algumas das métricas devolvidas da execução de consultas: 

| Consulta | Métrica da amostra | Description | 
| ------ | -----| ----------- |
| `SELECT TOP 100 * FROM c` | `"RetrievedDocumentCount": 101` | O número de documentos recuperados é 100+1 para corresponder à cláusula TOP. O tempo de consulta é maioritariamente gasto `WriteOutputTime` e uma vez que é um `DocumentLoadTime` scan. | 
| `SELECT TOP 500 * FROM c` | `"RetrievedDocumentCount": 501` | RecuperadoDoConmentCount é agora mais alto (500+1 para corresponder à cláusula TOP). | 
| `SELECT * FROM c WHERE c.N = 55` | `"IndexLookupTime": "00:00:00.0009500"` | Cerca de 0,9 ms é gasto no IndexLookupTime para uma procura chave, porque é um índice de procura em `/N/?` . | 
| `SELECT * FROM c WHERE c.N > 55` | `"IndexLookupTime": "00:00:00.0017700"` | Um pouco mais de tempo (1,7 ms) gasto no IndexLookupTime ao longo de uma varredura de intervalo, porque é uma análise de índice em `/N/?` . | 
| `SELECT TOP 500 c.N FROM c` | `"IndexLookupTime": "00:00:00.0017700"` | O mesmo tempo gasto em `DocumentLoadTime` consultas anteriores, mas mais baixo `WriteOutputTime` porque estamos projetando apenas uma propriedade. | 
| `SELECT TOP 500 udf.toPercent(c.N) FROM c` | `"UserDefinedFunctionExecutionTime": "00:00:00.2136500"` | Cerca de 213 ms é gasto na `UserDefinedFunctionExecutionTime` execução do UDF em cada valor de `c.N` . |
| `SELECT TOP 500 c.Name FROM c WHERE STARTSWITH(c.Name, 'Den')` | `"IndexLookupTime": "00:00:00.0006400", "SystemFunctionExecutionTime": "00:00:00.0074100"` | Cerca de 0,6 ms é gasto em `IndexLookupTime` `/Name/?` . A maior parte do tempo de execução de consultas (~7 ms) em `SystemFunctionExecutionTime` . |
| `SELECT TOP 500 c.Name FROM c WHERE STARTSWITH(LOWER(c.Name), 'den')` | `"IndexLookupTime": "00:00:00", "RetrievedDocumentCount": 2491,  "OutputDocumentCount": 500` | A consulta é realizada como uma varredura porque utiliza `LOWER` , e 500 de 2491 documentos recuperados são devolvidos. |


## <a name="next-steps"></a>Passos seguintes
* Para saber mais sobre os operadores de consulta SQL suportados e palavras-chave, consulte [a consulta SQL](sql-query-getting-started.md). 
* Para saber mais sobre unidades de pedido, consulte [unidades de pedido.](request-units.md)
* Para conhecer a política de indexação, consulte [a política de indexação](index-policy.md)