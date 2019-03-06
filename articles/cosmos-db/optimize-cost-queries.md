---
title: Otimizar unidades de pedido e os custos para executar consultas no Azure Cosmos DB
description: Saiba como avaliar os custos da unidade de pedido para uma consulta e otimizar a consulta em termos de desempenho e custo.
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: rimman
ms.openlocfilehash: e6814224827aac0da9c6faf5108ecf585bae7c35
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57445386"
---
# <a name="optimize-query-cost-in-azure-cosmos-db"></a>Otimizar o custo de consulta no Azure Cosmos DB

O Azure Cosmos DB oferece um conjunto avançado de operações de banco de dados, incluindo consultas relacionais e hierárquicas que operam nos itens dentro de um contêiner. O custo associado a cada uma destas operações varia consoante a CPU, IO e memória necessários para concluir a operação. Em vez de pensar e gerir recursos de hardware, pode pensar numa unidade de pedido (RU) como medida única para os recursos necessários para executar várias operações de base de dados para servir um pedido. Este artigo descreve como avaliar os custos da unidade de pedido para uma consulta e otimizar a consulta em termos de desempenho e custo. 

Consultas no Azure Cosmos DB são normalmente ordenadas do mais rápida/mais eficiente para mais lenta/menos eficiente em termos de débito da seguinte forma:  

* Operação de obtenção de uma chave de partição única e a chave do item.

* Consultar com uma cláusula de filtro dentro de uma chave de partição única.

* Consultar sem uma cláusula de filtro de endereços ou intervalos de igualdade em qualquer propriedade.

* Consulta sem filtros.

Consultas de leitura de dados de um ou mais partições incorrem latência superior e consumam o número mais elevado de unidades de pedido. Uma vez que cada partição tem de indexação automática para todas as propriedades, a consulta pode ser satisfeita de forma eficiente do índice. Pode fazer consultas que usam várias partições mais rápido, utilizando as opções de paralelismo. Para saber mais sobre a criação de partições e chaves de partição, veja [criação de partições no Azure Cosmos DB](partitioning-overview.md).

## <a name="evaluate-request-unit-charge-for-a-query"></a>Avalie o custo da unidade de pedido para uma consulta

Depois de ter armazenados alguns dados em seus contentores do Cosmos do Azure, pode utilizar o Data Explorer no portal do Azure para construir e execute as suas consultas. Também pode obter o custo das consultas ao utilizar o data explorer. Este método irá dar-lhe uma noção dos custos reais envolvidos com consultas típicas e operações que suporta o seu sistema.

Também pode obter o custo de consultas através de programação através de SDKs. Para medir a sobrecarga de qualquer operação, tais como criar, atualizar ou eliminar inspecionar o `x-ms-request-charge` cabeçalho ao utilizar a REST API. Se estiver a utilizar o .net ou o SDK de Java, o `RequestCharge` propriedade é a propriedade equivalente para obter os encargos de pedidos e esta propriedade está presente na ResourceResponse ou FeedResponse.

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

## <a name="factors-influencing-request-unit-charge-for-a-query"></a>Fatores influência de cobranças de unidades de pedido para uma consulta

Unidades de pedido para consultas dependem de vários fatores. Por exemplo, o número de itens de Azure Cosmos carregado/retornado, o número de pesquisas contra o índice, a compilação de consulta tempo que os detalhes de etc. O Azure Cosmos DB garante que a mesma consulta quando executada nos mesmos dados consumirá sempre o mesmo número de unidades de pedido, mesmo com repetições de execuções. O perfil de consulta com métricas de execução de consulta oferece uma boa idéia de como as unidades de pedido são gasto.  

Em alguns casos, pode ver uma seqüência de 200 429 respostas e unidades de pedido de variável numa execução paginada de consultas, que é porque as consultas posteriores serão executadas mais rapidamente possível com base no RUs disponível. Poderá ver uma execução de consulta dividir em várias páginas/arredondar viagens entre servidor e cliente. Por exemplo, 10 000 itens podem ser devolvidos como várias páginas, cada uma cobrada com base no cálculo realizado para essa página. Quando somar entre essas páginas, obterá o mesmo número de Urs como teria de toda a consulta.  

## <a name="metrics-for-troubleshooting"></a>Métricas para resolução de problemas

O desempenho e o débito consumido por consultas, funções definidas pelo utilizador (UDFs) principalmente depende para o corpo da função. É a maneira mais fácil para descobrir de quanto tempo é gasto a execução da consulta a UDF e o número de RUs consumidos, ao ativar as métricas de consulta. Se utilizar o SDK do .net, aqui estão as métricas de consulta de exemplo retornadas pelo SDK:

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

## <a name="best-practices-to-cost-optimize-queries"></a>Melhores práticas para otimizar as consultas 

Ao otimizar as consultas de custo, considere as seguintes práticas recomendadas:

* **Colocalizar vários tipos de entidade**

   Tente colocar vários tipos de entidade dentro de um único ou menor número de contentores. Este método produz benefícios não apenas de uma perspectiva de preços, mas também para execução da consulta e transações. Consultas estão confinadas a um único contentor; e transações atômicas ao longo de vários registos através de procedimentos armazenados/acionadores estão no âmbito de uma chave de partição dentro de um único contentor. Colocalizar as entidades no mesmo contentor pode reduzir o número de rede IDA e volta para resolver relações em registos. Por isso, ele aumenta o desempenho ponto a ponto, permite que as transações atômicas ao longo de vários registos para um conjunto de dados maior e assim reduz os custos. Se colocalizar vários tipos de entidade dentro de um único ou menor número de contentores costuma ser difícil para o seu cenário, uma vez que está a migrar uma aplicação existente e não pretender efetuar quaisquer alterações de código –, em seguida, deve considerar o aprovisionamento taxa de transferência ao nível da base de dados.  

* **Medir e otimizar para pedido inferior unidades/segundo utilização**

   A complexidade de uma consulta tem impacto sobre o número de unidades de pedido (RUs) são consumidas para uma operação. O número de predicados, a natureza dos predicados, UDFs e o tamanho do conjunto de dados de origem. Todos esses fatores influenciam o custo das operações de consulta. 

   Encargo de pedido devolvido no cabeçalho do pedido indica o custo de uma determinada consulta. Por exemplo, se uma consulta devolve 1000 itens de 1 KB, o custo da operação é 1000. Como tal, dentro de um segundo, o servidor honra apenas dois esses pedidos antes dos pedidos subsequentes de limitação de taxas. Para obter mais informações, consulte [unidades de pedido](request-units.md) artigo e a Calculadora de unidade de pedido. 

## <a name="next-steps"></a>Passos Seguintes

Em seguida, pode avançar para obter mais informações sobre a otimização de custos no Azure Cosmos DB com os seguintes artigos:

* Saiba mais sobre [Cosmos de Azure como funcionam os preços](how-pricing-works.md)
* Saiba mais sobre [otimizar para desenvolvimento e teste](optimize-dev-test.md)
* Saiba mais sobre [entender a sua fatura do Azure Cosmos DB](understand-your-bill.md)
* Saiba mais sobre [otimizar o custo de débito](optimize-cost-throughput.md)
* Saiba mais sobre [otimizar o custo de armazenamento](optimize-cost-storage.md)
* Saiba mais sobre [otimizar o custo de leituras e gravações](optimize-cost-reads-writes.md)
* Saiba mais sobre [otimizar o custo de contas do Cosmos do Azure de várias regiões](optimize-cost-regions.md)
* Saiba mais sobre [capacidade de reserva do Azure Cosmos DB](cosmos-db-reserved-capacity.md)

