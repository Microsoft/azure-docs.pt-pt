---
title: Diagnosticar e resolver problemas de consulta ao utilizar o Azure Cosmos DB
description: Saiba como identificar, diagnosticar e resolver problemas de consulta de SQL do Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: troubleshooting
ms.date: 07/10/2019
ms.author: girobins
ms.subservice: cosmosdb-sql
ms.reviewer: sngun
ms.openlocfilehash: 079e8677febfe6683d4f0e60a0e7ba6b06ea549d
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67835838"
---
# <a name="troubleshoot-query-performance-for-azure-cosmos-db"></a>Resolver problemas de desempenho de consulta para o Azure Cosmos DB
Este artigo aborda como identificar, diagnosticar e resolver problemas de consulta de SQL do Azure Cosmos DB. Para poder alcançar um desempenho ideal para consultas do Azure Cosmos DB, siga os passos de resolução de problemas abaixo. 

## <a name="collocate-clients-in-same-azure-region"></a>Colocar os clientes na mesma região do Azure 
A menor latência possível é obtida ao garantir que o aplicativo de chamada está localizado na mesma região do Azure como o ponto de final aprovisionado do Azure Cosmos DB. Para obter uma lista de regiões disponíveis, consulte [regiões do Azure](https://azure.microsoft.com/global-infrastructure/regions/#services) artigo.

## <a name="check-consistency-level"></a>Verifique o nível de consistência
[Nível de consistência](consistency-levels.md) pode afetar o desempenho e os encargos. Certifique-se de que seu nível de consistência é adequado para o cenário determinado. Para obter mais detalhes, consulte [escolher um nível de consistência](consistency-levels-choosing.md).

## <a name="log-query-metrics"></a>Métricas de consulta de registo
Utilize `QueryMetrics` para resolver problemas de consultas lentas ou dispendiosas. 

  * Definir `FeedOptions.PopulateQueryMetrics = true` ter `QueryMetrics` na resposta.
  * `QueryMetrics` classe tem um sobrecarregado `.ToString()` função que pode ser invocada para obter a representação de cadeia de caracteres de `QueryMetrics`. 
  * As métricas podem ser utilizadas para derivar as informações seguintes, entre outras: 
  
      * Se qualquer componente específico do pipeline de consulta demorou anormalmente longo para concluir (em ordem de centenas de milissegundos ou mais). 

          * Examinar `TotalExecutionTime`.
          * Se o `TotalExecutionTime` da consulta é menor que o tempo de execução de ponto a ponto, em seguida, o tempo é gasto no lado do cliente ou de rede. Volte a verificar que o cliente e a região do Azure estão colocalizados.
      
      * Se há falsos positivos nos documentos analisados (se a contagem de documentos de saída é muito menor do que o obtido de contagem de documentos).  

          * Examinar `Index Utilization`.
          * `Index Utilization` = (Devolvido de um número de documentos / carregado do número de documentos)
          * Se o número de documentos retornados é muito inferior ao número carregado, estão a ser analisados falsos positivos.
          * Limite o número de documentos a ser obtidas com filtros mais estreito.  

      * Ida e volta como individuais dos (consulte a `Partition Execution Timeline` partir da representação de cadeia de caracteres de `QueryMetrics`). 
      * Se a consulta consumido de encargos de pedidos elevada. 

Para obter mais detalhes, consulte [como obter métricas de execução de consulta SQL](profile-sql-api-query.md) artigo.
      
## <a name="tune-query-feed-options-parameters"></a>Otimizar os parâmetros de opções de Feed de consulta 
Desempenho de consulta pode ser ajustado por meio da solicitação [Feed opções](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.feedoptions?view=azure-dotnet) parâmetros. Experimente a definição as seguintes opções:

  * Definir `MaxDegreeOfParallelism` para-1 primeiro e, em seguida, compare desempenho em valores diferentes. 
  * Definir `MaxBufferedItemCount` para-1 primeiro e, em seguida, compare desempenho em valores diferentes. 
  * Definir `MaxItemCount` como -1.

Ao comparar o desempenho de valores diferentes, tente valores como 2, 4, 8, 16 e outras pessoas.
 
## <a name="read-all-results-from-continuations"></a>Ler todos os resultados de continuações
Se acha que não estiver a obter todos os resultados, certifique-se drenar a continuação totalmente. Em outras palavras, mantenha os resultados de leitura, enquanto que o token de continuação tem mais documentos para gerar.

Totalmente a ser drenado pode ser conseguido com qualquer um dos seguintes padrões:

  * Continue o processamento de resultados enquanto continuação não está vazia.
  * Continue o processamento, enquanto a consulta tem mais resultados. 

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

## <a name="choose-system-functions-that-utilize-index"></a>Escolha as funções de sistema que utilizam o índice
Se a expressão pode ser convertida num intervalo de valores de cadeia de caracteres, em seguida, ela pode utilizar o índice; caso contrário, ele não é possível. 

Eis a lista de funções de cadeia de caracteres que podem utilizar o índice: 
    
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

  * Evite as funções de sistema no filtro (ou cláusula WHERE) que não são processados pelo índice. Alguns exemplos de tais funções de sistema incluem Contains, superior, inferior.
  * Sempre que possível, escreva consultas para utilizar um filtro numa chave de partição.
  * Para alcançar com bom desempenho consultas evitar chamar o canto superior/inferior no filtro. Em vez disso, normalizar maiúsculas/minúsculas do valores mediante a inserção. Para cada um dos valores inserir o valor com letras maiúsculas e minúsculas pretendido ou inserir o valor original e o valor com as maiúsculas e minúsculas pretendido. 

    Por exemplo:
    
    ```sql

    SELECT * FROM c WHERE UPPER(c.name) = "JOE"

    ```
    
    Para esse caso, o arquivo de "JOÃO" em maiúsculas ou armazenar o valor original de "João" e "JOÃO". 
    
    Se as maiúsculas e minúsculas de dados do JSON são normalizadas, torna-se a consulta:
    
    ```sql

    SELECT * FROM c WHERE c.name = "JOE"

    ```

    A segunda consulta será um melhor desempenho, à medida que não é necessário executar transformações em cada um dos valores para comparar os valores para "JOÃO".

Para a função de sistema mais detalhes, veja [funções de sistema](sql-query-system-functions.md) artigo.

## <a name="check-indexing-policy"></a>Verifique a política de indexação
Para verificar se o atual [política de indexação](index-policy.md) é o ideal:

  * Certifique-se de que todos os caminhos JSON utilizados nas consultas estão incluídos na política de indexação para leituras mais rápidas.
  * Exclua caminhos não são utilizados nas consultas para obter mais escritas de alto desempenho.

Para obter mais detalhes, consulte [como para gerir a política de indexação](how-to-manage-indexing-policy.md) artigo.

## <a name="spatial-data-check-ordering-of-points"></a>Dados geográficos: Ordenação de pontos de verificação
Pontos de dentro de um polígono tem de ser especificados na ordem no sentido. Um polígono especificado por ordem para a direita representa o inverso da região na mesma.

## <a name="optimize-join-expressions"></a>Otimizar as expressões de associação
`JOIN` expressões podem expandir-se em grande em vários produtos. Quando possível, a consulta em relação a uma pesquisa mais pequena espaço por meio de um filtro mais estreito.

Podem otimizar os valores múltiplos subconsultas `JOIN` expressões ao enviar predicados após cada expressão select-muitos, em vez depois de todos os entre-junções no `WHERE` cláusula. Para obter um exemplo detalhado, consulte [otimizar expressões de associação](https://docs.microsoft.com/azure/cosmos-db/sql-query-subquery#optimize-join-expressions) artigo.

## <a name="optimize-order-by-expressions"></a>Otimizar as expressões ORDER BY 
`ORDER BY` desempenho de consulta pode ser afetado se os campos estão dispersas ou não incluídos na política de índice.

  * Para os campos esparsos, como o tempo, reduzir o espaço de pesquisa tanto quanto possível com filtros. 
  * Para a propriedade de única `ORDER BY`, incluir a propriedade na política de índice. 
  * Para a propriedade vários `ORDER BY` expressões, definir um [índice composto](https://docs.microsoft.com/azure/cosmos-db/index-policy#composite-indexes) em campos que está a ser ordenados.  

## <a name="many-large-documents-being-loaded-and-processed"></a>Muitos documentos grandes que está a ser carregados e processados
O tempo e o RUs que são necessários para uma consulta não são apenas dependentes do tamanho da resposta, mas também dependentes do trabalho que é feito pelo pipeline de processamento de consulta. Tempo e RUs proporcionalmente aumentam com a quantidade de trabalho realizado pelo pipeline de processamento de consulta inteira. Mais de trabalho é executada com documentos grandes, portanto, mais tempo e RUs são necessárias para carregar e processar documentos grandes.

## <a name="low-provisioned-throughput"></a>Baixo débito aprovisionado
Certifique-se de que o débito aprovisionado pode processar a carga de trabalho. Aumente o orçamento de RU para coleções afetados.

## <a name="try-upgrading-to-the-latest-sdk-version"></a>Experimente atualizar para a versão mais recente do SDK
Para determinar os mais recentes, consulte SDK [SDK Download e notas de versão](sql-api-sdk-dotnet.md) artigo.

## <a name="next-steps"></a>Passos Seguintes
Consulte documentos abaixo sobre como medir RUs por consulta, obter estatísticas de execução para otimizar as suas consultas e muito mais:

* [Obter métricas de execução de consulta SQL com o .NET SDK](profile-sql-api-query.md)
* [Otimização do desempenho de consulta com o Azure Cosmos DB](sql-api-sql-query-metrics.md)
* [Sugestões de desempenho para o .NET SDK](performance-tips.md)