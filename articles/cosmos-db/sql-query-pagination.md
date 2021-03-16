---
title: Paginação em Azure Cosmos DB
description: Saiba mais sobre conceitos de paging e fichas de continuação
author: timsander1
ms.author: tisande
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 03/15/2021
ms.openlocfilehash: d47a7b9e2229ffbd747b1ff7b9491ce99e9b190f
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/16/2021
ms.locfileid: "103492239"
---
# <a name="pagination-in-azure-cosmos-db"></a>Paginação em Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Em Azure Cosmos DB, as consultas podem ter várias páginas de resultados. Este documento explica critérios que o motor de consulta da Azure Cosmos DB utiliza para decidir se a consulta divide os resultados em várias páginas. Pode utilizar, opcionalmente, fichas de continuação para gerir resultados de consulta que se estendem por várias páginas.

## <a name="understanding-query-executions"></a>Compreensão das execuções de consulta

Por vezes, os resultados da consulta serão divididos em várias páginas. Os resultados de cada página são gerados por uma execução de consulta separada. Quando os resultados da consulta não puderem ser devolvidos numa única execução, a Azure Cosmos DB dividirá automaticamente os resultados em várias páginas.

Pode especificar o número máximo de itens devolvidos por uma consulta, definindo o `MaxItemCount` . O `MaxItemCount` é especificado por pedido e diz ao motor de consulta que irá devolver esse número de itens ou menos. Pode definir `MaxItemCount` `-1` se não quiser colocar um limite no número de resultados por execução de consultas.

Além disso, existem outras razões para que o motor de consulta possa precisar de dividir os resultados da consulta em várias páginas. Incluem-se:

- O contentor foi estrangulado e não havia RUs disponíveis para devolver mais resultados de consulta
- A resposta da execução de consulta era muito grande
- O tempo da execução da consulta foi muito longo
- Foi mais eficiente para o motor de consulta devolver resultados em execuções adicionais

O número de artigos devolvidos por execução de consultas será sempre inferior ou igual a `MaxItemCount` . No entanto, é possível que outros critérios possam ter limitado o número de resultados que a consulta poderia devolver. Se executar a mesma consulta várias vezes, o número de páginas pode não ser constante. Por exemplo, se uma consulta for acelerada, pode haver menos resultados disponíveis por página, o que significa que a consulta terá páginas adicionais. Em alguns casos, também é possível que a sua consulta possa devolver uma página vazia de resultados.

## <a name="handling-multiple-pages-of-results"></a>Manipulação de várias páginas de resultados

Para garantir resultados de consulta precisos, deve progredir em todas as páginas. Deve continuar a executar consultas até que não haja páginas adicionais.

Aqui estão alguns exemplos para processar resultados de consultas com várias páginas:

- [SDK do .NET](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/Queries/Program.cs#L280)
- [SDK Java](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/documentcrud/sync/DocumentCRUDQuickstart.java#L162-L176)
- [Node.js SDK](https://github.com/Azure/azure-sdk-for-js/blob/83fcc44a23ad771128d6e0f49043656b3d1df990/sdk/cosmosdb/cosmos/samples/IndexManagement.ts#L128-L140)
- [Python SDK](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/examples.py#L89)

## <a name="continuation-tokens"></a>Fichas de continuação

No .NET SDK e Java SDK, pode utilizar opcionalmente fichas de continuação como um marcador para o progresso da sua consulta. As execuções de consulta DB do Azure Cosmos são apátridas no lado do servidor e podem ser retomadas a qualquer momento usando o token de continuação. As fichas de continuação não são suportadas no Node.js SDK. Para o Python SDK, é suportado para consultas de partição única, e o PK deve ser especificado no objeto de opções porque não é suficiente tê-lo na própria consulta.

Aqui estão alguns exemplos para a utilização de fichas de continuação:

- [SDK do .NET](https://github.com/Azure/azure-cosmos-dotnet-v2/blob/master/samples/code-samples/Queries/Program.cs#L699-L734)
- [SDK Java](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L216)
- [Python SDK](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/test/test_query.py#L533)

Se a consulta devolver um token de continuação, então há resultados de consulta adicionais.

Na API REST da Azure Cosmos DB, você pode gerir fichas de continuação com o `x-ms-continuation` cabeçalho. Tal como acontece com a consulta com o .NET ou Java SDK, se o cabeçalho de `x-ms-continuation` resposta não estiver vazio, significa que a consulta tem resultados adicionais.

Enquanto estiver a utilizar a mesma versão SDK, os tokens de continuação nunca expiram. Pode restringir opcionalmente [o tamanho de um token de continuação](/dotnet/api/microsoft.azure.documents.client.feedoptions.responsecontinuationtokenlimitinkb#Microsoft_Azure_Documents_Client_FeedOptions_ResponseContinuationTokenLimitInKb). Independentemente da quantidade de dados ou do número de divisórias físicas no seu recipiente, as consultas devolvem um único sinal de continuação.

Não é possível utilizar fichas de continuação para consultas com [GROUP BY](sql-query-group-by.md) ou [DISTINCT](sql-query-keywords.md#distinct) porque estas consultas exigiriam armazenar uma quantidade significativa de estado. Para consultas `DISTINCT` com, pode utilizar fichas de continuação se adicionar `ORDER BY` à consulta.

Aqui está um exemplo de uma consulta com `DISTINCT` que poderia usar um símbolo de continuação:

```sql
SELECT DISTINCT VALUE c.name
FROM c
ORDER BY c.name
```

## <a name="next-steps"></a>Passos seguintes

- [Introdução ao Azure Cosmos DB](introduction.md)
- [Amostras de Azure Cosmos DB .NET](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Cláusula ORDER BY](sql-query-order-by.md)
