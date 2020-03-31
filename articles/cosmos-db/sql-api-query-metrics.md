---
title: Métricas de consulta SQL para Azure Cosmos DB SQL API
description: Saiba como instrumentar e desinvomar o desempenho da consulta SQL dos pedidos da Azure Cosmos DB.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: sngun
ms.openlocfilehash: ae1773ec1d470b9cff2efb00c200427b7b4c2fb4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "69614819"
---
# <a name="tuning-query-performance-with-azure-cosmos-db"></a>Otimização do desempenho de consulta com o Azure Cosmos DB

A Azure Cosmos DB fornece um [SQL API para consulta](how-to-sql-query.md)de dados, sem necessitar de esquemas ou índices secundários. Este artigo fornece as seguintes informações para os desenvolvedores:

* Detalhes de alto nível sobre como funciona a execução de consulta SQL da Azure Cosmos DB
* Detalhes sobre pedidos de consulta e cabeçalhos de resposta, e opções sDK do cliente
* Dicas e boas práticas para o desempenho da consulta
* Exemplos de como utilizar as estatísticas de execução do SQL para depurar o desempenho da consulta

## <a name="about-sql-query-execution"></a>Sobre a execução de consulta SQL

No Azure Cosmos DB, armazena dados em contentores, que podem crescer até qualquer tamanho de armazenamento ou entrada de [pedidos.](partition-data.md) O Azure Cosmos DB escala perfeitamente os dados através das divisórias físicas sob as coberturas para lidar com o crescimento de dados ou o aumento da entrada aprovisionada. Pode emitir consultas SQL a qualquer recipiente que utilize a API REST ou um dos [SDKs SQL](sql-api-sdk-dotnet.md)suportados .

Uma breve visão geral da partilha: define-se uma chave de partição como "cidade", que determina como os dados são divididos entre divisórias físicas. Os dados pertencentes a uma única chave de partição (por exemplo, "cidade" == "Seattle") são armazenados dentro de uma divisória física, mas tipicamente uma única divisória física tem múltiplas teclas de partição. Quando uma divisória atinge o seu tamanho de armazenamento, o serviço divide perfeitamente a divisória em duas novas divisórias, e divide a chave de divisória uniformemente através destas divisórias. Uma vez que as divisórias são transitórias, as APIs usam uma abstração de uma "gama de chaves de partição", que denota as gamas de hashes chave de partição. 

Quando emite uma consulta ao Azure Cosmos DB, o SDK realiza estes passos lógicos:

* Analise a consulta SQL para determinar o plano de execução da consulta. 
* Se a consulta incluir um filtro contra `SELECT * FROM c WHERE c.city = "Seattle"`a chave de partição, como, é encaminhado para uma única divisória. Se a consulta não tiver um filtro na chave de partição, então é executada em todas as divisórias, e os resultados são fundidos do lado do cliente.
* A consulta é executada dentro de cada partição em série ou paralelo, com base na configuração do cliente. Dentro de cada partição, a consulta pode fazer uma ou mais viagens de ida e volta dependendo da complexidade da consulta, do tamanho da página configurada e do fornecimento de entrada da coleção. Cada execução devolve o número de [unidades](request-units.md) de pedido consumidas pela execução de consulta, e opcionalmente, estatísticas de execução de consulta. 
* O SDK realiza uma resumição dos resultados da consulta através das divisórias. Por exemplo, se a consulta envolve uma ORDEM BY através de divisórias, então os resultados de divisórias individuais são classificados para devolver resultados em ordem global. Se a consulta for uma agregação como, `COUNT`as contagens de divisórias individuais são resumidas para produzir a contagem geral.

Os SDKs fornecem várias opções para a execução de consultas. Por exemplo, em .NET estas `FeedOptions` opções estão disponíveis na classe. A tabela que se segue descreve estas opções e como impactam o tempo de execução da consulta. 

| Opção | Descrição |
| ------ | ----------- |
| `EnableCrossPartitionQuery` | Deve ser definido como verdadeiro para qualquer consulta que precise ser executada em mais de uma partição. Esta é uma bandeira explícita que lhe permite fazer trocas conscientes de desempenho durante o tempo de desenvolvimento. |
| `EnableScanInQuery` | Deve ser definido para verdade se você optou por não indexar, mas quer executar a consulta através de um exame de qualquer maneira. Só aplicável se a indexação para a via filtragem solicitada for desativada. | 
| `MaxItemCount` | O número máximo de itens para devolver por ida e volta ao servidor. Ao definir para -1, pode deixar o servidor gerir o número de itens. Ou, você pode baixar este valor para recuperar apenas um pequeno número de itens por ida e volta. 
| `MaxBufferedItemCount` | Esta é uma opção do lado do cliente, e usada para limitar o consumo de memória ao executar a divisão cruzada ORDER BY. Um valor mais elevado ajuda a reduzir a latência da triagem de divisórias cruzadas. |
| `MaxDegreeOfParallelism` | Obtém ou define o número de operações simultâneas executadas do lado do cliente durante a execução de consulta paralela no serviço de base de dados Azure Cosmos. Um valor patrimonial positivo limita o número de operações simultâneas ao valor definido. Se for definido para menos de 0, o sistema decide automaticamente o número de operações simultâneas a executar. |
| `PopulateQueryMetrics` | Permite o registo detalhado das estatísticas do tempo gasto em várias fases de execução de consulta, como o tempo de compilação, o tempo do loop do índice e o tempo de carga do documento. Pode partilhar a produção a partir de estatísticas de consulta com suporte Azure para diagnosticar problemas de desempenho de consulta. |
| `RequestContinuation` | Você pode retomar a execução de consulta passando no token de continuação opaca devolvido por qualquer consulta. O símbolo de continuação encapsula todo o estado necessário para a execução da consulta. |
| `ResponseContinuationTokenLimitInKb` | Pode limitar o tamanho máximo do token de continuação devolvido pelo servidor. Pode ser necessário definir isto se o seu anfitrião de aplicação tiver limites no tamanho do cabeçalho de resposta. A definição disto pode aumentar a duração total e as RUs consumidas para a consulta.  |

Por exemplo, vamos dar um exemplo de consulta sobre a `/city` chave de partição solicitada numa coleção com como chave de partição e aprovisionado com 100.000 RU/s de entrada. Solicita esta consulta `CreateDocumentQuery<T>` utilizando em .NET como o seguinte:

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

O snippet SDK acima indicado corresponde ao seguinte pedido de API REST:

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

Cada página de execução de consulta `POST` corresponde `Accept: application/query+json` a uma API REST com o cabeçalho, e a consulta SQL no corpo. Cada consulta faz uma ou mais viagens `x-ms-continuation` de ida e volta ao servidor com o token ecoado entre o cliente e o servidor para retomar a execução. As opções de configuração no FeedOptions são passadas para o servidor sob a forma de cabeçalhos de pedido. Por exemplo, `MaxItemCount` corresponde `x-ms-max-item-count`a . 

O pedido devolve a seguinte resposta (truncada para a legibilidade):

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

Os cabeçalhos de resposta chave devolvidos da consulta incluem o seguinte:

| Opção | Descrição |
| ------ | ----------- |
| `x-ms-item-count` | O número de itens devolvidos na resposta. Isto depende do `x-ms-max-item-count`fornecido, do número de itens que podem ser ajustados dentro do tamanho máximo da carga útil da resposta, da entrada prevista e do tempo de execução da consulta. |  
| `x-ms-continuation:` | A continuação para retomar a execução da consulta, se houver resultados adicionais disponíveis. | 
| `x-ms-documentdb-query-metrics` | As estatísticas de consulta para a execução. Esta é uma cadeia delimitada contendo estatísticas de tempo gasto nas várias fases da execução de consultas. Devolvido `x-ms-documentdb-populatequerymetrics` se estiver `True`definido para . | 
| `x-ms-request-charge` | O número de [unidades](request-units.md) de pedido consumidas pela consulta. | 

Para mais detalhes sobre os cabeçalhos e opções de pedido da API REST, consulte os recursos de [consulta utilizando a API REST](https://docs.microsoft.com/rest/api/cosmos-db/querying-cosmosdb-resources-using-the-rest-api).

## <a name="best-practices-for-query-performance"></a>Boas práticas para o desempenho da consulta
Seguem-se os fatores mais comuns que impactam o desempenho da consulta do Azure Cosmos DB. Aprofundamos cada um destes tópicos neste artigo.

| Fator | Sugestão | 
| ------ | -----| 
| Débito aprovisionado | Meça o RU por consulta e certifique-se de que tem o medidor necessário para as suas consultas. | 
| Chaves de partição e partição | Favor consultas com o valor chave da divisória na cláusula de filtro para baixa latência. |
| Opções de SDK e consulta | Siga as melhores práticas da SDK, como conectividade direta, e sintonize as opções de execução de consulta do lado do cliente. |
| Latência de rede | Contabilize as despesas gerais da rede na medição e utilize APIs multi-homing para ler a partir da região mais próxima. |
| Política de Indexação | Certifique-se de que tem os caminhos/políticas de indexação necessários para a consulta. |
| Métricas de execução de consulta | Analise as métricas de execução da consulta para identificar potenciais reescritas de formas de consulta e dados.  |

### <a name="provisioned-throughput"></a>Débito aprovisionado
Na Cosmos DB, cria-se recipientes de dados, cada um com entrada reservada expressa em unidades de pedido (RU) por segundo. Uma leitura de um documento de 1 KB é 1 RU, e todas as operações (incluindo consultas) são normalizadas para um número fixo de RUs com base na sua complexidade. Por exemplo, se tiver 1000 RU/s provisionados para o `SELECT * FROM c WHERE c.city = 'Seattle'` seu recipiente, e tiver uma consulta como a que consome 5 RUs, então pode realizar (1000 RU/s) / (5 RU/consulta) = 200 consultas/s tais consultas por segundo. 

Se submeter mais de 200 consultas/seg, o serviço inicia pedidos de entrada limitadores de taxas acima de 200/s. Os SDKs lidam automaticamente com este caso executando um backoff/retry, pelo que poderá notar uma latência mais elevada para estas consultas. Aumentar a entrada prevista para o valor exigido melhora a sua latência e a sua entrada. 

Para saber mais sobre unidades de pedido, consulte [Unidades de Pedido](request-units.md).

### <a name="partitioning-and-partition-keys"></a>Chaves de partição e partição
Com o Azure Cosmos DB, as consultas normalmente funcionam na seguinte ordem, desde o mais rápido/mais eficiente a mais lento/menos eficiente. 

* OBTENHA uma única chave de partição e chave de item
* Consulta com uma cláusula de filtro numa única chave de partição
* Consulta sem igualdade ou cláusula de filtro de alcance em qualquer propriedade
* Consulta sem filtros

As consultas que precisam de consultar todas as divisórias precisam de maior latência, e podem consumir RUs mais elevados. Uma vez que cada partição tem indexação automática contra todas as propriedades, a consulta pode ser servida eficientemente a partir do índice neste caso. Pode fazer consultas que abrangem divisórias mais rapidamente utilizando as opções de paralelismo.

Para saber mais sobre divisórias e chaves de partição, consulte [A Partição em Azure Cosmos DB](partition-data.md).

### <a name="sdk-and-query-options"></a>Opções de SDK e consulta
Consulte dicas de [desempenho](performance-tips.md) e testes [de desempenho](performance-testing.md) para obter o melhor desempenho do lado do cliente da Azure Cosmos DB. Isto inclui a utilização dos Mais recentes SDKs, configuração de configurações específicas da plataforma, como o número padrão de ligações, frequência de recolha de lixo e utilização de opções de conectividade leve como Direct/TCP. 


#### <a name="max-item-count"></a>Contagem de artigos max
Para consultas, o `MaxItemCount` valor pode ter um impacto significativo no tempo de consulta de ponta a ponta. Cada ida e volta ao servidor não devolverá `MaxItemCount` mais do que o número de itens em (Padrão de 100 itens). Defini-lo para um valor mais elevado (-1 é máximo, e recomendado) irá melhorar a sua duração de consulta em geral, limitando o número de viagens redondas entre servidor e cliente, especialmente para consultas com grandes conjuntos de resultados.

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
Para consultas, sintonize as `MaxDegreeOfParallelism` melhores configurações para a sua aplicação, especialmente se realizar consultas de divisória cruzada (sem filtro no valor da chave de divisória). `MaxDegreeOfParallelism`controla o número máximo de tarefas paralelas, ou seja, o máximo de divisórias a visitar em paralelo. 

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
* D = Número máximo de tarefas paralelas (= número total de processador na máquina cliente)
* P = Número máximo especificado pelo utilizador de tarefas paralelas
* N = Número de divisórias que precisam de ser visitadas para responder a uma consulta

Seguem-se as implicações de como as consultas paralelas se comportariam para diferentes valores de P.
* (P == 0) => Modo de Série
* (P == 1) => Máximo de uma tarefa
* (P > 1) = tarefas paralelas> Min (P, N) 
* (P < 1) = tarefas paralelas> Min (N, D)

Para notas de lançamento do SDK, e detalhes sobre classes e métodos implementados ver [SDKs SQL](sql-api-sdk-dotnet.md)

### <a name="network-latency"></a>Latência de rede
Consulte a [distribuição global da Azure Cosmos DB](tutorial-global-distribution-sql-api.md) para saber como criar distribuição global e ligar-se à região mais próxima. A latência da rede tem um impacto significativo no desempenho da consulta quando você precisa fazer várias voltas ou recuperar um grande resultado definido a partir da consulta. 

A secção sobre métricas de execução de consulta explica como `totalExecutionTimeInMs`recuperar o tempo de execução do servidor de consultas ( ), para que possa diferenciar entre o tempo gasto na execução de consulta e o tempo gasto em trânsito de rede.

### <a name="indexing-policy"></a>Política de indexação
Consulte a política de indexação de [configuração](index-policy.md) para indexar caminhos, tipos e modos, e como impactam a execução de consultas. Por padrão, a política de indexação utiliza a indexação hash para cordas, o que é eficaz para consultas de igualdade, mas não para consultas de alcance/ordem por consultas. Se necessitar de consultas de alcance para cordas, recomendamos especificar o tipo de índice Gama para todas as cordas. 

Por padrão, o Azure Cosmos DB aplicará indexação automática a todos os dados. Para cenários de inserção de alto desempenho, considere excluir caminhos, uma vez que isso reduzirá o custo do RU para cada operação de inserção. 

## <a name="query-execution-metrics"></a>Métricas de execução de consulta
Pode obter métricas detalhadas sobre a execução da consulta, passando no cabeçalho opcional `x-ms-documentdb-populatequerymetrics` (no`FeedOptions.PopulateQueryMetrics` .NET SDK). O valor `x-ms-documentdb-query-metrics` devolvido tem os seguintes pares de valor-chave destinados a resolução avançada de problemas de execução de consulta. 

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
| `queryCompileTimeInMs` | milissegundos | Hora de compilação de consultas  | 
| `queryLogicalPlanBuildTimeInMs` | milissegundos | Hora de construir plano de consulta lógica | 
| `queryPhysicalPlanBuildTimeInMs` | milissegundos | Hora de construir plano de consulta física | 
| `queryOptimizationTimeInMs` | milissegundos | Tempo gasto na otimização da consulta | 
| `VMExecutionTimeInMs` | milissegundos | Tempo gasto em tempo de consulta | 
| `indexLookupTimeInMs` | milissegundos | Tempo gasto em camada de índice físico | 
| `documentLoadTimeInMs` | milissegundos | Tempo gasto em documentos de carregamento  | 
| `systemFunctionExecuteTimeInMs` | milissegundos | O tempo total gasto do sistema de execução (incorporado) funciona em milissegundos  | 
| `userFunctionExecuteTimeInMs` | milissegundos | Total de tempo gasto executando funções definidas pelo utilizador em milissegundos | 
| `retrievedDocumentCount` | count | Número total de documentos recuperados  | 
| `retrievedDocumentSize` | bytes | Tamanho total dos documentos recuperados em bytes  | 
| `outputDocumentCount` | count | Número de documentos de saída | 
| `writeOutputTimeInMs` | milissegundos | Tempo de execução de consulta em milissegundos | 
| `indexUtilizationRatio` | relação (<=1) | Relação entre o número de documentos comparado pelo filtro ao número de documentos carregados  | 

Os SDKs do cliente podem internamente fazer múltiplas operações de consulta para servir a consulta dentro de cada partição. O cliente faz mais do que uma chamada `x-ms-max-item-count`por partição se os resultados totais excederem, se a consulta exceder a produção prevista para a partição, ou se a carga útil da consulta atingir o tamanho máximo por página, ou se a consulta atingir o limite de tempo atribuído ao sistema atribuído. Cada execução parcial `x-ms-documentdb-query-metrics` de consulta devolve uma para aquela página. 

Aqui estão algumas consultas de amostra, e como interpretar algumas das métricas devolvidas da execução da consulta: 

| Consulta | Métrica da amostra | Descrição | 
| ------ | -----| ----------- |
| `SELECT TOP 100 * FROM c` | `"RetrievedDocumentCount": 101` | O número de documentos recuperados é de 100+1 para corresponder à cláusula TOP. O tempo de consulta `WriteOutputTime` `DocumentLoadTime` é gasto principalmente e como é um exame. | 
| `SELECT TOP 500 * FROM c` | `"RetrievedDocumentCount": 501` | O RecuperadoDocumentCount é agora mais elevado (500+1 para corresponder à cláusula TOP). | 
| `SELECT * FROM c WHERE c.N = 55` | `"IndexLookupTime": "00:00:00.0009500"` | Cerca de 0,9 ms são gastos no IndexLookupTime para uma procura `/N/?`chave, porque é uma análise de índice em . | 
| `SELECT * FROM c WHERE c.N > 55` | `"IndexLookupTime": "00:00:00.0017700"` | Um pouco mais de tempo (1,7 ms) gasto no IndexLookupTime ao longo `/N/?`de uma varredura de gama, porque é uma análise de índice em . | 
| `SELECT TOP 500 c.N FROM c` | `"IndexLookupTime": "00:00:00.0017700"` | O mesmo `DocumentLoadTime` tempo gasto em consultas `WriteOutputTime` anteriores, mas mais baixo porque estamos projetando apenas uma propriedade. | 
| `SELECT TOP 500 udf.toPercent(c.N) FROM c` | `"UserDefinedFunctionExecutionTime": "00:00:00.2136500"` | Cerca de 213 ms são gastos na `UserDefinedFunctionExecutionTime` `c.N`execução da UDF em cada valor de . |
| `SELECT TOP 500 c.Name FROM c WHERE STARTSWITH(c.Name, 'Den')` | `"IndexLookupTime": "00:00:00.0006400", "SystemFunctionExecutionTime": "00:00:00.0074100"` | Cerca de 0,6 ms `/Name/?`são gastos em `IndexLookupTime` . A maior parte do tempo de execução `SystemFunctionExecutionTime`da consulta (~7 ms) em . |
| `SELECT TOP 500 c.Name FROM c WHERE STARTSWITH(LOWER(c.Name), 'den')` | `"IndexLookupTime": "00:00:00", "RetrievedDocumentCount": 2491,  "OutputDocumentCount": 500` | A consulta é realizada como uma `LOWER`digitalização porque usa , e 500 de 2491 documentos recuperados são devolvidos. |


## <a name="next-steps"></a>Passos seguintes
* Para conhecer os operadores de consulta SQL suportados e palavras-chave, consulte a [consulta SQL](sql-query-getting-started.md). 
* Para saber mais sobre unidades de pedido, consulte [unidades de pedido](request-units.md).
* Para aprender sobre a política de indexação, consulte [a política de indexação](index-policy.md) 


