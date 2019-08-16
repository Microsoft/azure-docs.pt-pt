---
title: Diagnosticar e solucionar problemas de consulta ao usar o Azure Cosmos DB
description: Saiba como identificar, diagnosticar e solucionar problemas Azure Cosmos DB problemas de consulta SQL.
author: ginamr
ms.service: cosmos-db
ms.topic: troubleshooting
ms.date: 07/10/2019
ms.author: girobins
ms.subservice: cosmosdb-sql
ms.reviewer: sngun
ms.openlocfilehash: d0dd9a371c4912cae0e74b214c673c629fc1ff55
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/15/2019
ms.locfileid: "69515812"
---
# <a name="troubleshoot-query-performance-for-azure-cosmos-db"></a>Solucionar problemas de desempenho de consulta para Azure Cosmos DB
Este artigo aborda como identificar, diagnosticar e solucionar problemas Azure Cosmos DB problemas de consulta SQL. Para obter o desempenho ideal para consultas de Azure Cosmos DB, siga as etapas de solução de problemas abaixo. 

## <a name="collocate-clients-in-same-azure-region"></a>Colocar clientes na mesma região do Azure 
A menor latência possível é obtida garantindo que o aplicativo de chamada esteja localizado na mesma região do Azure que o ponto de extremidade de Azure Cosmos DB provisionado. Para obter uma lista de regiões disponíveis, consulte o artigo [regiões do Azure](https://azure.microsoft.com/global-infrastructure/regions/#services) .

## <a name="check-consistency-level"></a>Verificar nível de consistência
O [nível de consistência](consistency-levels.md) pode afetar o desempenho e os encargos. Verifique se o nível de consistência é apropriado para o cenário fornecido. Para obter mais detalhes, consulte [escolher o nível de consistência](consistency-levels-choosing.md).

## <a name="log-the-executed-sql-query"></a>Registrar a consulta SQL executada 

Você pode registrar a consulta SQL executada em uma conta de armazenamento ou na tabela de log de diagnóstico. [Os logs de consulta SQL por meio de logs de diagnóstico](logging.md#turn-on-logging-in-the-azure-portal) permitem registrar em log a consulta ofuscada em uma conta de armazenamento de sua escolha. Isso permite que você examine os logs e localize a consulta que está usando RUs maior. Posteriormente, você pode usar a ID da atividade para corresponder à consulta real no QueryRuntimeStatistics. A consulta é ofuscada para fins de segurança e nomes de parâmetro de consulta, e seus valores em cláusulas WHERE são diferentes dos nomes e valores reais. Você pode usar o registro em log na conta de armazenamento para manter a retenção de longo prazo das consultas executadas.  

## <a name="log-query-metrics"></a>Métricas de consulta de log

Use `QueryMetrics` para solucionar problemas de consultas lentas ou dispendiosas. 

  * Defina `FeedOptions.PopulateQueryMetrics = true` para ter `QueryMetrics` na resposta.
  * `QueryMetrics`a classe tem uma `.ToString()` função sobrecarregada que pode ser invocada para obter a representação de cadeia de caracteres de. `QueryMetrics` 
  * As métricas podem ser utilizadas para derivar as seguintes informações, entre outras: 
  
      * Se algum componente específico do pipeline de consulta demorou muito tempo para ser concluído (em uma ordem de centenas de milissegundos ou mais). 

          * `TotalExecutionTime`Examine.
          * Se a `TotalExecutionTime` da consulta for menor do que o tempo de execução de ponta a ponta, o tempo será gasto no lado do cliente ou na rede. Verifique se o cliente e a região do Azure estão posicionados.
      
      * Se houveram falsos positivos nos documentos analisados (se a contagem de documentos de saída for muito menor do que a contagem de documentos recuperados).  

          * `Index Utilization`Examine.
          * `Index Utilization`= (Número de documentos retornados/número de documentos carregados)
          * Se o número de documentos retornados for muito menor do que o número carregado, os falsos positivos serão analisados.
          * Limite o número de documentos que estão sendo recuperados com filtros mais restritos.  

      * Como as viagens de ida e volta individuais são transformadas (consulte `Partition Execution Timeline` a `QueryMetrics`partir da representação da cadeia de caracteres de). 
      * Se a consulta consumiu alto custo de solicitação. 

Para obter mais detalhes, consulte [o artigo como obter métricas de execução de consulta SQL](profile-sql-api-query.md) .
      
## <a name="tune-query-feed-options-parameters"></a>Ajustar Parâmetros de Opções do Feed de Consultas 
O desempenho de consultas pode ser ajustado através dos Parâmetros das [Opções do Feed](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.feedoptions?view=azure-dotnet) do pedido. Tente definir as opções abaixo:

  * Defina `MaxDegreeOfParallelism` como-1 primeiro e, em seguida, compare o desempenho entre diferentes valores. 
  * Defina `MaxBufferedItemCount` como-1 primeiro e, em seguida, compare o desempenho entre diferentes valores. 
  * Defina `MaxItemCount` como-1.

Ao comparar o desempenho de valores diferentes, experimente valores como 2, 4, 8, 16 e outros.
 
## <a name="read-all-results-from-continuations"></a>Ler todos os resultados de continuações
Se considerar que não está a obter todos os resultados, confirme que drena totalmente a continuação. Por outras palavras, continue a ler os resultados enquanto o token de continuação tiver mais documentos para gerar.

Pode conseguir drenar totalmente com qualquer um dos seguintes padrões:

  * Continuar processando resultados enquanto a continuação não está vazia.
  * Continue o processamento enquanto a consulta tiver mais resultados. 

    ```csharp
    // using AsDocumentQuery you get access to whether or not the query HasMoreResults
    // If it does, just call ExecuteNextAsync until there are no more results
    // No need to supply a continuation token here as the server keeps track of progress
    var query = client.CreateDocumentQuery<Family>(collectionLink, options).AsDocumentQuery();
    while (query.HasMoreResults)
    {
        foreach (Family family in await query.ExecuteNextAsync())
        {
            families.Add(family);
        }
    }
    ```

## <a name="choose-system-functions-that-utilize-index"></a>Escolher as funções do sistema que utilizam o índice
Se a expressão puder ser traduzida para um intervalo de valores de cadeia, pode utilizar o índice; caso contrário, não pode. 

Aqui está a lista de funções de cadeia que podem utilizar o índice: 
    
  * STARTSWITH(str_expr, str_expr) 
  * LEFT(str_expr, num_expr) = str_expr 
  * SUBSTRING(str_expr, num_expr, num_expr) = str_expr, but only if first num_expr is 0 
    
    Aqui estão alguns exemplos de consulta: 
    
    ```sql

    -- If there is a range index on r.name, STARTSWITH will utilize the index while ENDSWITH won't 
    SELECT * 
    FROM c 
    WHERE STARTSWITH(c.name, 'J') AND ENDSWITH(c.name, 'n')

    ```
    
    ```sql

    -- LEFT will utilize the index while RIGHT won't 
    SELECT * 
    FROM c 
    WHERE LEFT(c.name, 2) = 'Jo' AND RIGHT(c.name, 2) = 'hn'

    ```

  * Evite funções do sistema no filtro (ou na cláusula WHERE) que não são servidas pelo índice. Alguns exemplos dessas funções de sistema incluem contém, Upper e Lower.
  * Sempre que possível, escreva consultas para utilizar um filtro numa chave de partição.
  * Para obter consultas de alto desempenho, evite chamar UPPER/Low no filtro. Em vez disso, Normalize a capitalização de valores após a inserção. Para cada um dos valores, insira o valor com maiúsculas e minúsculas desejado ou insira o valor original e o valor com a capitalização desejada. 

    Por exemplo:
    
    ```sql

    SELECT * FROM c WHERE UPPER(c.name) = "JOE"

    ```
    
    Nesse caso, armazene "JOE" capitalizado ou armazene "Joe" o valor original e "JOE". 
    
    Se a capitalização de dados JSON for normalizada, a consulta se tornará:
    
    ```sql

    SELECT * FROM c WHERE c.name = "JOE"

    ```

    A segunda consulta será mais eficaz, pois não requer a execução de transformações em cada um dos valores para comparar os valores com "JOE".

Para obter mais detalhes da função do sistema, consulte o artigo [funções do sistema](sql-query-system-functions.md) .

## <a name="check-indexing-policy"></a>Verificar política de indexação
Para verificar se a atual [Política de Indexação](index-policy.md) é a ideal:

  * Verifique se todos os caminhos JSON usados em consultas estão incluídos na política de indexação para leituras mais rápidas.
  * Exclua os caminhos não usados em consultas para mais gravações de alto desempenho.

Para obter mais detalhes, consulte [o artigo como gerenciar a política de indexação](how-to-manage-indexing-policy.md) .

## <a name="spatial-data-check-ordering-of-points"></a>Dados espaciais: Verificar a ordem dos pontos
Pontos de dentro de um polígono tem de ser especificados na ordem no sentido. Um polígono especificado por ordem para a direita representa o inverso da região na mesma.

## <a name="optimize-join-expressions"></a>Otimizar expressões de junção
`JOIN`as expressões podem se expandir em grandes produtos cruzados. Quando possível, consulte um espaço de pesquisa menor por meio de um filtro mais estreito.

Subconsultas com vários valores podem `JOIN` otimizar expressões enviando predicados após cada expressão Select-many em vez de todas as junções cruzadas `WHERE` na cláusula. Para obter um exemplo detalhado, consulte o artigo [otimizar expressões de junção](https://docs.microsoft.com/azure/cosmos-db/sql-query-subquery#optimize-join-expressions) .

## <a name="optimize-order-by-expressions"></a>Otimizar expressões ORDENAdas por 
`ORDER BY`o desempenho da consulta poderá ser prejudicado se os campos forem esparsos ou não forem incluídos na política de índice.

  * Para campos esparsos, como tempo, diminua o espaço de pesquisa o máximo possível com filtros. 
  * Para propriedade `ORDER BY`única, inclua a propriedade na política de índice. 
  * Para várias expressões `ORDER BY` de propriedade, defina um [índice composto](https://docs.microsoft.com/azure/cosmos-db/index-policy#composite-indexes) em campos que estão sendo classificados.  

## <a name="many-large-documents-being-loaded-and-processed"></a>Muitos documentos grandes sendo carregados e processados
O tempo e o RUs exigidos por uma consulta não são apenas dependentes do tamanho da resposta, eles também dependem do trabalho feito pelo pipeline de processamento de consulta. O tempo e o RUs aumentam proporcionalmente com a quantidade de trabalho feita pelo pipeline de processamento de consulta inteiro. Mais trabalho é executado para documentos grandes, assim, mais tempo e RUs são necessários para carregar e processar documentos grandes.

## <a name="low-provisioned-throughput"></a>Baixa taxa de transferência provisionada
Garanta que a produtividade provisionada possa lidar com a carga de trabalho. Aumente o orçamento de RU para coleções afetadas.

## <a name="try-upgrading-to-the-latest-sdk-version"></a>Tente atualizar para a versão mais recente do SDK
Para determinar o SDK mais recente, consulte o artigo [download do SDK e notas de versão](sql-api-sdk-dotnet.md) .

## <a name="next-steps"></a>Passos Seguintes
Consulte os documentos abaixo sobre como medir o RUs por consulta, obter estatísticas de execução para ajustar suas consultas e muito mais:

* [Get SQL query execution metrics using .NET SDK](profile-sql-api-query.md) (Obter métricas de execução de consultas SQL através do SDK do .NET)
* [Otimização do desempenho de consulta com o Azure Cosmos DB](sql-api-sql-query-metrics.md)
* [Sugestões de desempenho para o SDK .NET](performance-tips.md)
