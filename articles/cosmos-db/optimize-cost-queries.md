---
title: Otimizar o custo e os RU/s para executar consultas em Azure Cosmos DB
description: Saiba avaliar os custos unitários de pedido para uma consulta e otimizar a consulta em termos de desempenho e custo.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: dd75ad4ed1024292868f113e474fe8b8b73679b0
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75445137"
---
# <a name="optimize-query-cost-in-azure-cosmos-db"></a>Otimizar o custo das consultas no Azure Cosmos DB

O Azure Cosmos DB oferece um conjunto de operações de bases de dados, incluindo consultas relacionais e hierárquicas que operam nos itens de um contentor. O custo associado a cada uma destas operações varia com base na CPU, E/S e memória necessárias para concluir a operação. Em vez de pensar nos recursos de hardware e na sua gestão, pode pensar numa Unidade de Pedido (RU) como medida única para os recursos necessários para executar várias operações de bases de dados para servir um pedido. Este artigo descreve como avaliar os custos das unidades de pedidos de uma consulta e como otimizar a consulta em termos de desempenho e custo. 

As consultas em Azure Cosmos DB são tipicamente encomendadas de mais rápido/mais eficiente para mais lenta/menos eficiente em termos de entrada da seguinte forma:  

* Obtenha o funcionamento numa única tecla de partição e na chave do item.

* Consulta com uma cláusula de filtro dentro de uma única tecla de partição.

* Consulta sem uma cláusula de igualdade ou filtro de alcance em qualquer propriedade.

* Consulta sem filtros.

Consultas que lêem dados de uma ou mais divisórias incorrem em maior latência e consomem um maior número de unidades de pedido. Uma vez que cada divisória tem indexação automática para todas as propriedades, a consulta pode ser servida eficientemente a partir do índice. Pode fazer consultas que usam várias divisórias mais rapidamente utilizando as opções de paralelismo. Para saber mais sobre divisórias e chaves de partição, consulte [A Partição em Azure Cosmos DB](partitioning-overview.md).

## <a name="evaluate-request-unit-charge-for-a-query"></a>Avaliar a taxa unitária de pedido para uma consulta

Depois de armazenar alguns dados nos seus contentores Azure Cosmos, pode utilizar o Data Explorer no portal Azure para construir e executar as suas consultas. Também pode obter o custo das consultas utilizando o explorador de dados. Este método irá dar-lhe uma ideia das acusações reais envolvidas com consultas e operações típicas que o seu sistema suporta.

Também pode obter o custo das consultas programáticamente utilizando os SDKs. Para medir a sobrecarga de qualquer operação, como `x-ms-request-charge` criar, atualizar ou eliminar, inspecione o cabeçalho quando utilizar a API REST. Se estiver a utilizar o .NET ou `RequestCharge` o Java SDK, a propriedade é a propriedade equivalente para obter a taxa de pedido e esta propriedade está presente no RecursoResponse ou FeedResponse.

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

## <a name="factors-influencing-request-unit-charge-for-a-query"></a>Fatores que influenciam a taxa unitária de pedido para uma consulta

As unidades de pedido de consultas dependem de uma série de fatores. Por exemplo, o número de itens Azure Cosmos carregados/devolvidos, o número de inquéritos contra o índice, o tempo de compilação de consultas, etc. detalhes. A Azure Cosmos DB garante que a mesma consulta quando executada nos mesmos dados consumirá sempre o mesmo número de unidades de pedido mesmo com execuções repetidas. O perfil de consulta usando métricas de execução de consulta dá-lhe uma boa ideia de como as unidades de pedido são gastas.  

Em alguns casos, você pode ver uma sequência de 200 e 429 respostas, e unidades de pedido variável em uma execução páginada de consultas, isto é porque as consultas serão executadas o mais rápido possível com base nas RUs disponíveis. Pode ver uma execução de consulta a invadir várias páginas/viagens de ida e volta entre o servidor e o cliente. Por exemplo, 10.000 itens podem ser devolvidos como várias páginas, cada um carregado com base no cálculo realizado para essa página. Quando se resume estas páginas, deve obter o mesmo número de RUs que obteria para toda a consulta.  

## <a name="metrics-for-troubleshooting"></a>Métricas para resolução de problemas

O desempenho e a entrada consumida por consultas, funções definidas pelo utilizador (UDFs) dependem principalmente do corpo de função. A maneira mais fácil de descobrir quanto tempo a execução da consulta é passada na UDF e o número de RUs consumidos, é permitindo as Métricas de Consulta. Se utilizar o .NET SDK, aqui estão as métricas de consulta de amostra devolvidas pelo SDK:

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

## <a name="best-practices-to-cost-optimize-queries"></a>Boas práticas para otimizar consultas 

Considere as seguintes boas práticas ao otimizar consultas de custo:

* **Colocação de vários tipos de entidades**

   Tente colocar vários tipos de entidades dentro de um único ou menor número de recipientes. Este método beneficia não só do ponto de vista dos preços, mas também da execução e das transações de consulta. As consultas são consultadas a um único recipiente; e as transações atómicas através de vários registos através de procedimentos/gatilhos armazenados são reparadas numa chave de partição dentro de um único recipiente. A colocação de entidades dentro do mesmo contentor pode reduzir o número de viagens de ida e volta em rede para resolver relações através de registos. Assim, aumenta o desempenho de ponta a ponta, permite transações atómicas em vários registos para um conjunto de dados maior, e como resultado reduz os custos. Se colocar vários tipos de entidades dentro de um único ou menor número de contentores é difícil para o seu cenário, geralmente porque está a migrar uma aplicação existente e não quer efetuar alterações de código - deve então considerar o fornecimento de entrada ao nível da base de dados.  

* **Medir e sintonizar para unidades de pedido mais baixos/segunda utilização**

   A complexidade de uma consulta tem impacto na quantidade de unidades de pedido (RUs) que são consumidas para uma operação. O número de predicados, a natureza dos predicados, o número de UDFs e o tamanho do conjunto de dados de origem. Todos estes fatores influenciam o custo das operações de consulta. 

   A cobrança de pedidos devolvida no cabeçalho do pedido indica o custo de uma determinada consulta. Por exemplo, se uma consulta devolver 1000 itens 1-KB, o custo da operação é de 1000. Como tal, dentro de um segundo, o servidor honra apenas dois desses pedidos antes de limitar os pedidos subsequentes. Para mais informações, consulte o artigo das [unidades](request-units.md) de pedido e a calculadora da unidade de pedidos. 

## <a name="next-steps"></a>Passos seguintes

Em seguida, poderá proceder a mais informações sobre a otimização de custos em Azure Cosmos DB com os seguintes artigos:

* Saiba mais sobre [como funciona o preço da Azure Cosmos](how-pricing-works.md)
* Saiba mais sobre [Otimização para desenvolvimento e testes](optimize-dev-test.md)
* Saiba mais sobre [compreender a sua conta de DB Azure Cosmos](understand-your-bill.md)
* Saiba mais sobre [otimização do custo de entrada](optimize-cost-throughput.md)
* Saiba mais sobre [otimização do custo de armazenamento](optimize-cost-storage.md)
* Saiba mais sobre [otimizar o custo das leituras e dos escritos](optimize-cost-reads-writes.md)
* Saiba mais sobre [otimizar o custo das contas multi-regiões da Azure Cosmos](optimize-cost-regions.md)
* Saiba mais sobre a capacidade reservada do [Azure Cosmos DB](cosmos-db-reserved-capacity.md)

