---
title: Otimizar custos e RU/s para executar consultas em Azure Cosmos DB
description: Saiba como avaliar os custos unitários de pedido para uma consulta e otimize a consulta em termos de desempenho e custo.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/24/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: c6ff67e3c1588f6671173d6fa1eec3dc15883291
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89020920"
---
# <a name="optimize-query-cost-in-azure-cosmos-db"></a>Otimizar o custo das consultas no Azure Cosmos DB

O Azure Cosmos DB oferece um conjunto de operações de bases de dados, incluindo consultas relacionais e hierárquicas que operam nos itens de um contentor. O custo associado a cada uma destas operações varia com base na CPU, E/S e memória necessárias para concluir a operação. Em vez de pensar nos recursos de hardware e na sua gestão, pode pensar numa Unidade de Pedido (RU) como medida única para os recursos necessários para executar várias operações de bases de dados para servir um pedido. Este artigo descreve como avaliar os custos das unidades de pedidos de uma consulta e como otimizar a consulta em termos de desempenho e custo.

As leituras em Azure Cosmos DB são normalmente encomendadas de mais rápido/eficiente para mais lento/menos eficiente em termos de produção da seguinte forma:  

* Leituras de pontos (procura de chave/valor numa única chave de iD de item e chave de partição).

* Consulta com uma cláusula de filtro dentro de uma única chave de partição.

* Consulta sem uma cláusula de filtro de igualdade ou alcance em qualquer propriedade.

* Consulta sem filtros.

Como as pesquisas chave/valor no ID do item são o tipo de leitura mais eficiente, deve certificar-se de que o ID do item tem um valor significativo.

As consultas que lêem dados de uma ou mais divisórias incorrem em maior latência e consomem um maior número de unidades de pedido. Uma vez que cada partição tem indexação automática para todas as propriedades, a consulta pode ser servida eficientemente a partir do índice. Você pode fazer consultas que usam várias divisórias mais rapidamente usando as opções de paralelismo. Para saber mais sobre chaves de partição e partição, consulte [Partition in Azure Cosmos DB](partitioning-overview.md).

## <a name="evaluate-request-unit-charge-for-a-query"></a>Avaliar a taxa da unidade de pedido para uma consulta

Uma vez armazenados alguns dados nos seus contentores Azure Cosmos, pode utilizar o Data Explorer no portal Azure para construir e executar as suas consultas. Também pode obter o custo das consultas utilizando o explorador de dados. Este método irá dar-lhe uma ideia das acusações reais envolvidas com consultas e operações típicas que o seu sistema suporta.

Também pode obter o custo das consultas programáticamente usando os SDKs. Para medir a sobrecarga de qualquer operação como criar, atualizar ou apagar inspecione o `x-ms-request-charge` cabeçalho quando utilizar a API REST. Se estiver a utilizar o .NET ou o Java SDK, a `RequestCharge` propriedade é a propriedade equivalente para obter o custo de pedido e esta propriedade está presente dentro da ResourceResponse ou FeedResponse.

```csharp
// Measure the performance (request units) of writes
ResourceResponse<Document> response = await client.CreateDocumentAsync(collectionSelfLink, myDocument); 

Console.WriteLine("Insert of an item consumed {0} request units", response.RequestCharge); 

// Measure the performance (request units) of queries 
IDocumentQuery<dynamic> queryable = client.CreateDocumentQuery(collectionSelfLink, queryString).AsDocumentQuery(); 

while (queryable.HasMoreResults) 
     { 
          FeedResponse<dynamic> queryResponse = await queryable.ExecuteNextAsync<dynamic>(); 
          Console.WriteLine("Query batch consumed {0} request units", queryResponse.RequestCharge); 
     }
```

## <a name="factors-influencing-request-unit-charge-for-a-query"></a>Fatores que influenciam a taxa da unidade de pedido para uma consulta

As unidades de pedido para consultas dependem de uma série de fatores. Por exemplo, o número de itens Azure Cosmos carregados/devolvidos, o número de procuras contra o índice, o tempo de compilação de consultas, etc. detalhes. A Azure Cosmos DB garante que a mesma consulta quando executada nos mesmos dados consumirá sempre o mesmo número de unidades de pedido, mesmo com execuções repetidas. O perfil de consulta utilizando métricas de execução de consulta dá-lhe uma boa ideia de como as unidades de pedido são gastas.  

Em alguns casos, você pode ver uma sequência de 200 e 429 respostas, e unidades de pedido variável em uma execução paged de consultas, isto é, porque as consultas serão executadas o mais rápido possível com base nas RUs disponíveis. Pode ver uma execução de consulta a partir-se em várias páginas/viagens de ida e volta entre o servidor e o cliente. Por exemplo, 10.000 itens podem ser devolvidos como várias páginas, cada um carregado com base no cálculo realizado para essa página. Quando se soma nestas páginas, deve obter o mesmo número de RUs que obteria para toda a consulta.  

## <a name="metrics-for-troubleshooting"></a>Métricas para resolução de problemas

O desempenho e a produção consumidos por consultas, funções definidas pelo utilizador (UDFs) dependem principalmente do corpo da função. A maneira mais fácil de descobrir quanto tempo a execução de consulta é gasta na UDF e o número de RUs consumidos, é permitindo as Métricas de Consulta. Se utilizar o .NET SDK, aqui estão as métricas de consulta de amostra devolvidas pelo SDK:

```bash
Retrieved Document Count                 :               1              
Retrieved Document Size                  :           9,963 bytes        
Output Document Count                    :               1              
Output Document Size                     :          10,012 bytes        
Index Utilization                        :          100.00 %            
Total Query Execution Time               :            0.48 milliseconds 
  Query Preparation Times 
    Query Compilation Time               :            0.07 milliseconds 
    Logical Plan Build Time              :            0.03 milliseconds 
    Physical Plan Build Time             :            0.05 milliseconds 
    Query Optimization Time              :            0.00 milliseconds 
  Index Lookup Time                      :            0.06 milliseconds 
  Document Load Time                     :            0.03 milliseconds 
  Runtime Execution Times 
    Query Engine Execution Time          :            0.03 milliseconds 
    System Function Execution Time       :            0.00 milliseconds 
    User-defined Function Execution Time :            0.00 milliseconds 
  Document Write Time                    :            0.00 milliseconds 
  Client Side Metrics 
    Retry Count                          :               1              
    Request Charge                       :            3.19 RUs  
```

## <a name="best-practices-to-cost-optimize-queries"></a>Melhores práticas para permitir a otimização de consultas 

Considere as seguintes boas práticas ao otimizar consultas por custo:

* **Colocar vários tipos de entidades**

   Tente alotear vários tipos de entidades num único ou menor número de contentores. Este método beneficia não só do ponto de vista dos preços, mas também da execução de consultas e transações. As consultas são procuradas num único contentor; e as transações atómicas sobre vários registos através de procedimentos/gatilhos armazenados são telescópios numa chave de partição dentro de um único recipiente. As entidades de colocação dentro do mesmo contentor podem reduzir o número de viagens de ida e volta de rede para resolver relações entre registos. Assim, aumenta o desempenho de ponta a ponta, permite transações atómicas em vários registos para um conjunto de dados maior, e como resultado reduz os custos. Se a colocação de vários tipos de entidades num único ou menor número de contentores é difícil para o seu cenário, normalmente porque está a migrar uma aplicação existente e não quer fazer alterações de código - deve então considerar o fornecimento de produção ao nível da base de dados.  

* **Medida e sintonização para unidades de pedido mais baixas/segunda utilização**

   A complexidade de uma consulta tem impacto no número de unidades de pedido (RUs) consumidas para uma operação. O número de predicados, a natureza dos predicados, o número de UDFs e o tamanho do conjunto de dados de origem. Todos estes fatores influenciam o custo das operações de consulta. 

   A taxa de pedido devolvida no cabeçalho do pedido indica o custo de uma determinada consulta. Por exemplo, se uma consulta devolver 1000 itens 1-KB, o custo da operação é de 1000. Como tal, dentro de um segundo, o servidor honra apenas dois desses pedidos antes de taxa limitando os pedidos subsequentes. Para mais informações, consulte o artigo [das unidades de pedido](request-units.md) e a calculadora da unidade de pedido. 

## <a name="next-steps"></a>Passos seguintes

Em seguida, pode proceder para saber mais sobre a otimização de custos na Azure Cosmos DB com os seguintes artigos:

* Saiba mais sobre [como funciona o preço da Azure Cosmos](how-pricing-works.md)
* Saiba mais sobre [Otimização para desenvolvimento e testes](optimize-dev-test.md)
* Saiba mais sobre [compreender a sua conta de DB da Azure Cosmos](understand-your-bill.md)
* Saiba mais sobre [otimizar o custo de produção](optimize-cost-throughput.md)
* Saiba mais sobre [otimizar o custo de armazenamento](optimize-cost-storage.md)
* Saiba mais sobre [otimizar o custo das leituras e dos escritos](optimize-cost-reads-writes.md)
* Saiba mais sobre [otimizar o custo das contas da Azure Cosmos em várias regiões](optimize-cost-regions.md)
* Saiba mais sobre [a capacidade reservada da Azure Cosmos DB](cosmos-db-reserved-capacity.md)

