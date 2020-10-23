---
title: Perguntas frequentes sobre a API de Gremlin em Azure Cosmos DB
description: Obtenha respostas para perguntas frequentes sobre a API de Gremlin em Azure Cosmos DB
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/28/2020
ms.author: sngun
ms.openlocfilehash: b43bd0a076a21429a5f6fe05ef0c20d62674acdf
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/20/2020
ms.locfileid: "92281564"
---
# <a name="frequently-asked-questions-about-the-gremlin-api-in-azure-cosmos-db"></a>Perguntas frequentes sobre a API de Gremlin em Azure Cosmos DB

Este artigo explica respostas a algumas perguntas frequentes sobre a API gremlin em Azure Cosmos DB.

## <a name="how-to-evaluate-the-efficiency-of-gremlin-queries"></a>Como avaliar a eficiência das consultas gremlin

O passo de pré-visualização **do Executário** pode ser utilizado para fornecer uma análise do plano de execução de consultas. Este passo deve ser acrescentado ao fim de qualquer consulta gremlin, tal como ilustrado pelo seguinte exemplo:

**Exemplo de consulta**

```
g.V('mary').out('knows').executionProfile()
```

**Saída de exemplo**

```json
[
  {
    "gremlin": "g.V('mary').out('knows').executionProfile()",
    "totalTime": 8,
    "metrics": [
      {
        "name": "GetVertices",
        "time": 3,
        "annotations": {
          "percentTime": 37.5
        },
        "counts": {
          "resultCount": 1
        }
      },
      {
        "name": "GetEdges",
        "time": 5,
        "annotations": {
          "percentTime": 62.5
        },
        "counts": {
          "resultCount": 0
        },
        "storeOps": [
          {
            "count": 0,
            "size": 0,
            "time": 0.6
          }
        ]
      },
      {
        "name": "GetNeighborVertices",
        "time": 0,
        "annotations": {
          "percentTime": 0
        },
        "counts": {
          "resultCount": 0
        }
      },
      {
        "name": "ProjectOperator",
        "time": 0,
        "annotations": {
          "percentTime": 0
        },
        "counts": {
          "resultCount": 0
        }
      }
    ]
  }
]
```

A saída do perfil acima mostra quanto tempo é gasto a obter os objetos vértices, os objetos de borda e o tamanho do conjunto de dados de trabalho. Isto está relacionado com as medições de custos padrão para consultas DB Azure Cosmos.

## <a name="other-frequently-asked-questions"></a>Outras perguntas frequentes

### <a name="how-are-rus-charged-when-running-queries-on-a-graph-database"></a>Como é cobrado RU/s ao executar consultas numa base de dados de gráficos?

Todos os objetos gráficos, vértices e bordas são mostrados como documentos JSON no backend. Uma vez que uma consulta gremlin pode modificar um ou muitos objetos gráficos de cada vez, o custo associado a ele está diretamente relacionado com os objetos, bordas que são processadas pela consulta. Este é o mesmo processo que a Azure Cosmos DB usa para todas as outras APIs. Para obter mais informações, veja [Unidades de Pedido no Azure Cosmos DB](request-units.md).

A carga RU baseia-se no conjunto de dados de trabalho do traversal, e não no conjunto de resultados. Por exemplo, se uma consulta pretende obter um único vértice como resultado, mas precisa de atravessar mais do que um outro objeto no caminho, então o custo será baseado em todos os objetos gráficos que será necessário para calcular o vértice de um resultado.

### <a name="whats-the-maximum-scale-that-a-graph-database-can-have-in-azure-cosmos-db-gremlin-api"></a>Qual é a escala máxima que uma base de dados de gráficos pode ter na API API AZure Cosmos DB Gremlin?

A Azure Cosmos DB faz uso da [divisória horizontal](partitioning-overview.md) para resolver automaticamente o aumento dos requisitos de armazenamento e produção. A capacidade máxima de produção e armazenamento de uma carga de trabalho é determinada pelo número de divisórias associadas a um determinado recipiente. No entanto, um recipiente Gremlin API tem um conjunto específico de diretrizes para garantir uma experiência de desempenho adequada à escala. Para obter mais informações sobre partição e boas práticas, consulte a partição no artigo [da Azure Cosmos DB.](partitioning-overview.md)

### <a name="for-cnet-development-should-i-use-the-microsoftazuregraphs-package-or-gremlinnet"></a>Para o desenvolvimento de C#/.NET, devo utilizar o pacote Microsoft.Azure.Graphs ou Gremlin.NET?

A Azure Cosmos DB Gremlin API aproveita os controladores de código aberto como os principais conectores para o serviço. Assim, a opção recomendada é utilizar [condutores que sejam apoiados pela Apache Tinkerpop.](https://tinkerpop.apache.org/)

### <a name="how-can-i-protect-against-injection-attacks-using-gremlin-drivers"></a>Como posso proteger-me contra ataques de injeção usando condutores de Gremlin?

A maioria dos condutores nativos de Apache Tinkerpop Gremlin permitem a opção de fornecer um dicionário de parâmetros para a execução de consultas. Este é um exemplo de como fazê-lo em [Gremlin.Net](https://tinkerpop.apache.org/docs/3.2.7/reference/#gremlin-DotNet) e em [Gremlin-Javascript.](https://github.com/Azure-Samples/azure-cosmos-db-graph-nodejs-getting-started/blob/master/app.js)

### <a name="why-am-i-getting-the-gremlin-query-compilation-error-unable-to-find-any-method-error"></a>Porque estou a receber o erro de compilação "Gremlin Query Compilation Error: Incapaz de encontrar qualquer método"?

A Azure Cosmos DB Gremlin API implementa um subconjunto da funcionalidade definida na área da superfície de Gremlin. Para etapas apoiadas e mais informações, consulte o artigo [de apoio da Gremlin.](gremlin-support.md)

A melhor solução é reescrever os passos necessários de Gremlin com a funcionalidade suportada, uma vez que todos os passos essenciais da Gremlin são suportados pela Azure Cosmos DB.

### <a name="why-am-i-getting-the-websocketexception-the-server-returned-status-code-200-when-status-code-101-was-expected-error"></a>Porque estou a obter o erro "WebSocketException: The server returned status code '200" quando o código de estado '101' era esperado?

Este erro é provavelmente lançado quando o ponto final errado está a ser utilizado. O ponto final que gera este erro tem o seguinte padrão:

`https:// YOUR_DATABASE_ACCOUNT.documents.azure.com:443/`

Este é o ponto final dos documentos para a sua base de dados de gráficos.  O ponto final correto a utilizar é o Gremlin Endpoint, que tem o seguinte formato:

`https://YOUR_DATABASE_ACCOUNT.gremlin.cosmosdb.azure.com:443/`

### <a name="why-am-i-getting-the-requestrateistoolarge-error"></a>Porque estou a receber o erro "RequestRateIsTooLarge"?

Este erro significa que as Unidades de Pedido alocadas por segundo não são suficientes para servir a consulta. Este erro é geralmente visto quando se faz uma consulta que obtém todos os vértices:

```
// Query example:
g.V()
```

Esta consulta tentará recuperar todos os vértices do gráfico. Assim, o custo desta consulta será igual, pelo menos, ao número de vértices em termos de RUs. A regulação RU/s deve ser ajustada para dar resposta a esta consulta.

### <a name="why-do-my-gremlin-driver-connections-get-dropped-eventually"></a>Porque é que as minhas ligações ao condutor do Gremlin acabam por ser retiradas?

Uma ligação Gremlin é feita através de uma ligação WebSocket. Embora as ligações WebSocket não tenham um tempo específico para viver, a Azure Cosmos DB Gremlin API terminará as ligações inativas após 30 minutos de inatividade.

### <a name="why-cant-i-use-fluent-api-calls-in-the-native-gremlin-drivers"></a>Por que não posso usar chamadas fluentes da API nos condutores nativos de Gremlin?

As chamadas fluentes da API ainda não são apoiadas pela API AZure Cosmos DB Gremlin. As chamadas fluentes da API requerem uma funcionalidade de formatação interna conhecida como suporte bytecode que atualmente não é suportado pela AZure Cosmos DB Gremlin API. Pelo mesmo motivo, o mais recente Gremlin-JavaScript condutor também não está suportado.

## <a name="next-steps"></a>Passos seguintes

* [Suporte ao protocolo de arame Azure Cosmos DB Gremlin](gremlin-support.md)
* Crie, questione e percorle uma base de dados de gráficos Azure Cosmos DB usando a [consola Gremlin](create-graph-gremlin-console.md)
