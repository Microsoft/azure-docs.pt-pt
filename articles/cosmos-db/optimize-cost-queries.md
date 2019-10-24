---
title: Otimizar as unidades de solicitação e o custo para executar consultas no Azure Cosmos DB
description: Saiba como avaliar encargos de unidade de solicitação para uma consulta e otimizar a consulta em termos de desempenho e custo.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: 376c1a32a70951448b35a4c02022719229a3aad2
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/22/2019
ms.locfileid: "72753290"
---
# <a name="optimize-query-cost-in-azure-cosmos-db"></a>Otimizar o custo da consulta no Azure Cosmos DB

O Azure Cosmos DB oferece um conjunto avançado de operações de banco de dados, incluindo consultas relacionais e hierárquicas que operam nos itens de um contêiner. O custo associado a cada uma dessas operações varia de acordo com a CPU, a e/s e a memória necessária para concluir a operação. Em vez de pensar e gerenciar recursos de hardware, você pode pensar em uma RU (unidade de solicitação) como uma medida única para os recursos necessários para executar várias operações de banco de dados para atender a uma solicitação. Este artigo descreve como avaliar encargos de unidade de solicitação para uma consulta e otimizar a consulta em termos de desempenho e custo. 

As consultas em Azure Cosmos DB normalmente são ordenadas de forma mais rápida/mais eficiente para mais lenta/menos eficiente em termos de taxa de transferência da seguinte maneira:  

* Operação de obtenção em uma chave de partição única e chave de item.

* Consulta com uma cláusula de filtro em uma única chave de partição.

* Consulta sem uma cláusula de filtro de igualdade ou de intervalo em qualquer propriedade.

* Consulta sem filtros.

As consultas que lêem dados de uma ou mais partições incorrem em latência mais alta e consomem um número maior de unidades de solicitação. Como cada partição tem indexação automática para todas as propriedades, a consulta pode ser servida com eficiência do índice. Você pode fazer consultas que usam várias partições mais rápido usando as opções de paralelismo. Para saber mais sobre particionamento e chaves de partição, confira [particionamento no Azure Cosmos DB](partitioning-overview.md).

## <a name="evaluate-request-unit-charge-for-a-query"></a>Avaliar o encargo de unidade de solicitação para uma consulta

Depois de armazenar alguns dados em seus contêineres de Cosmos do Azure, você pode usar o Data Explorer no portal do Azure para construir e executar suas consultas. Você também pode obter o custo das consultas usando o data Explorer. Esse método dará a você uma noção dos encargos reais envolvidos em consultas e operações típicas às quais seu sistema dá suporte.

Você também pode obter o custo das consultas programaticamente usando os SDKs. Para medir a sobrecarga de qualquer operação, como criar, atualizar ou excluir, inspecione o cabeçalho `x-ms-request-charge` ao usar a API REST. Se você estiver usando o .NET ou o SDK do Java, a propriedade `RequestCharge` será a propriedade equivalente para obter o encargo da solicitação e essa propriedade estará presente dentro de ResourceResponse ou FeedResponse.

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

## <a name="factors-influencing-request-unit-charge-for-a-query"></a>Fatores que influenciam o encargo de unidade de solicitação para uma consulta

As unidades de solicitação para consultas dependem de vários fatores. Por exemplo, o número de itens do Azure Cosmos carregados/retornados, o número de pesquisas em relação ao índice, o tempo de compilação da consulta, etc. detalhes. Azure Cosmos DB garante que a mesma consulta quando executada nos mesmos dados sempre consumirá o mesmo número de unidades de solicitação, mesmo com execuções repetidas. O perfil de consulta usando métricas de execução de consulta fornece uma boa ideia de como as unidades de solicitação são gastas.  

Em alguns casos, você pode ver uma sequência de respostas de 200 e 429 e unidades de solicitação variáveis em uma execução paginada de consultas, isto é, porque as consultas serão executadas o mais rápido possível com base no RUs disponível. Você pode ver uma quebra de execução de consulta em várias páginas/viagens de ida e volta entre o servidor e o cliente. Por exemplo, 10.000 itens podem ser retornados como várias páginas, cada um cobrado com base no cálculo realizado para essa página. Ao somar essas páginas, você deve obter o mesmo número de RUs que obteria para a consulta inteira.  

## <a name="metrics-for-troubleshooting"></a>Métricas para solução de problemas

O desempenho e a taxa de transferência consumida por consultas, UDFs (funções definidas pelo usuário) dependem principalmente do corpo da função. A maneira mais fácil de descobrir quanto tempo a execução da consulta é gasta no UDF e o número de RUs consumidas, é habilitando as métricas de consulta. Se você usar o SDK do .NET, aqui estão as métricas de consulta de exemplo retornadas pelo SDK:

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

## <a name="best-practices-to-cost-optimize-queries"></a>Práticas recomendadas para otimizar o custo de consultas 

Considere as seguintes práticas recomendadas ao otimizar consultas por custo:

* **Colocar vários tipos de entidade**

   Tente colocar vários tipos de entidade em um número único ou menor de contêineres. Esse método gera benefícios não apenas de uma perspectiva de preços, mas também para execução de consultas e transações. As consultas estão no escopo de um único contêiner; e transações atômicas sobre vários registros por meio de procedimentos armazenados/gatilhos têm como escopo uma chave de partição em um único contêiner. A colocação de entidades no mesmo contêiner pode reduzir o número de viagens de ida e volta da rede para resolver relações entre registros. Então, ele aumenta o desempenho de ponta a ponta, permite transações atômicas em vários registros para um conjunto de uma maior e, como resultado, reduz os custos. Se a colocação de vários tipos de entidade dentro de um número único ou menor de contêineres for difícil para seu cenário, geralmente porque você está migrando um aplicativo existente e não deseja fazer nenhuma alteração de código-você deve considerar o provisionamento taxa de transferência no nível do banco de dados.  

* **Medir e ajustar para unidades de solicitação menores/segundo uso**

   A complexidade de uma consulta afeta quantas unidades de solicitação (RUs) são consumidas para uma operação. O número de predicados, a natureza dos predicados, o número de UDFs e o tamanho do conjunto de dados de origem. Todos esses fatores influenciam o custo das operações de consulta. 

   O encargo da solicitação retornado no cabeçalho da solicitação indica o custo de uma determinada consulta. Por exemplo, se uma consulta retornar itens de 1000 1 KB, o custo da operação será 1000. Como tal, dentro de um segundo, o servidor honra apenas duas solicitações desse tipo antes de limitar a taxa de solicitações subsequentes. Para obter mais informações, consulte o artigo [unidades de solicitação](request-units.md) e a calculadora de unidade de solicitação. 

## <a name="next-steps"></a>Passos seguintes

Em seguida, você pode prosseguir para saber mais sobre a otimização de custos no Azure Cosmos DB com os seguintes artigos:

* Saiba mais sobre [como funciona o preço do Azure Cosmos](how-pricing-works.md)
* Saiba mais sobre como [otimizar para desenvolvimento e teste](optimize-dev-test.md)
* Saiba mais sobre como [entender sua fatura de Azure Cosmos DB](understand-your-bill.md)
* Saiba mais sobre como [otimizar o custo da taxa de transferência](optimize-cost-throughput.md)
* Saiba mais sobre como [otimizar o custo de armazenamento](optimize-cost-storage.md)
* Saiba mais sobre como [otimizar o custo de leituras e gravações](optimize-cost-reads-writes.md)
* Saiba mais sobre como [otimizar o custo de contas do Azure Cosmos de várias regiões](optimize-cost-regions.md)
* Saiba mais sobre [Azure Cosmos DB capacidade reservada](cosmos-db-reserved-capacity.md)

