---
title: Perguntas frequentes sobre a API Gremlin em Azure Cosmos DB
description: Obtenha respostas a perguntas frequentes sobre a API Gremlin em Azure Cosmos DB
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/28/2020
ms.author: sngun
ms.openlocfilehash: 416cf4f027b6f1e72641324be39ba0304301db37
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/30/2020
ms.locfileid: "82614502"
---
# <a name="frequently-asked-questions-about-the-gremlin-api-in-azure-cosmos-db"></a>Perguntas frequentes sobre a API Gremlin em Azure Cosmos DB

Este artigo explica respostas a algumas perguntas frequentes sobre a API Gremlin em Azure Cosmos DB.

## <a name="how-to-evaluate-the-efficiency-of-gremlin-queries"></a>Como avaliar a eficiência das consultas de Gremlin

O passo de pré-visualização **do Profile de execução** pode ser utilizado para fornecer uma análise do plano de execução de consulta. Este passo deve ser adicionado ao fim de qualquer consulta de Gremlin, como ilustrado pelo seguinte exemplo:

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

A saída do perfil acima mostra quanto tempo é gasto a obter os objetos vértice, os objetos de borda e o tamanho do conjunto de dados de trabalho. Isto está relacionado com as medições de custopadrão para consultas de DB da Azure Cosmos.

## <a name="other-frequently-asked-questions"></a>Outras perguntas frequentes

### <a name="how-are-rus-charged-when-running-queries-on-a-graph-database"></a>Como é que o RU/s é carregado ao executar consultas numa base de dados de gráficos?

Todos os objetos gráficos, vértices e bordas, são mostrados como documentos JSON no backend. Uma vez que uma consulta de Gremlin pode modificar um ou muitos objetos gráficos de cada vez, o custo associado a ele está diretamente relacionado com os objetos, bordas que são processadas pela consulta. Este é o mesmo processo que o Azure Cosmos DB usa para todas as outras APIs. Para obter mais informações, veja [Unidades de Pedido no Azure Cosmos DB](request-units.md).

A carga RU baseia-se no conjunto de dados de trabalho da travessia, e não no conjunto de resultados. Por exemplo, se uma consulta pretende obter um único vértice como resultado, mas precisa atravessar mais do que um outro objeto no caminho, então o custo será baseado em todos os objetos gráficos que será necessário para calcular o vértice de um resultado.

### <a name="whats-the-maximum-scale-that-a-graph-database-can-have-in-azure-cosmos-db-gremlin-api"></a>Qual é a escala máxima que uma base de dados de gráficos pode ter na API Do Azure Cosmos DB Gremlin?

A Azure Cosmos DB utiliza [a partilha horizontal](partition-data.md) para abordar automaticamente o aumento dos requisitos de armazenamento e de entrada. A capacidade máxima de entrada e armazenamento de uma carga de trabalho é determinada pelo número de divisórias associadas a um determinado recipiente. No entanto, um recipiente Gremlin API tem um conjunto específico de diretrizes para garantir uma experiência de desempenho adequada em escala. Para obter mais informações sobre divisórias e boas práticas, consulte a partilha no artigo [da Azure Cosmos DB.](partition-data.md)

### <a name="for-cnet-development-should-i-use-the-microsoftazuregraphs-package-or-gremlinnet"></a>Para o desenvolvimento de C#/.NET, devo usar o pacote Microsoft.Azure.Graphs ou Gremlin.NET?

A Azure Cosmos DB Gremlin API aproveita os condutores de código aberto como os principais conectores do serviço. Assim, a opção recomendada é utilizar [os condutores que são suportados por Apache Tinkerpop](https://tinkerpop.apache.org/).

### <a name="how-can-i-protect-against-injection-attacks-using-gremlin-drivers"></a>Como posso proteger-me contra ataques de injeção usando condutores gremlin?

A maioria dos condutores nativos apache Tinkerpop Gremlin permitem a opção de fornecer um dicionário de parâmetros para a execução de consulta. Este é um exemplo de como fazê-lo em [Gremlin.Net](https://tinkerpop.apache.org/docs/3.2.7/reference/#gremlin-DotNet) e em [Gremlin-Javascript.](https://github.com/Azure-Samples/azure-cosmos-db-graph-nodejs-getting-started/blob/master/app.js)

### <a name="why-am-i-getting-the-gremlin-query-compilation-error-unable-to-find-any-method-error"></a>Porque é que estou a ter o erro de compilação de consultas de Gremlin: incapaz de encontrar qualquer método?

A Azure Cosmos DB Gremlin API implementa um subconjunto da funcionalidade definida na área de superfície de Gremlin. Para passos apoiados e mais informações, consulte o artigo de [apoio da Gremlin.](gremlin-support.md)

A melhor suposições é reescrever os passos gremlin necessários com a funcionalidade suportada, uma vez que todos os passos essenciais de Gremlin são suportados pela Azure Cosmos DB.

### <a name="why-am-i-getting-the-websocketexception-the-server-returned-status-code-200-when-status-code-101-was-expected-error"></a>Porque é que estou a receber o erro "WebSocketException: O servidor devolveu o código de estado '200' quando era esperado o código de estado '101'?

Este erro é provavelmente lançado quando o ponto final errado está a ser utilizado. O ponto final que gera este erro tem o seguinte padrão:

`https:// YOUR_DATABASE_ACCOUNT.documents.azure.com:443/`

Este é o ponto final dos documentos para a sua base de dados de gráficos.  O ponto final correto a utilizar é o Ponto Final gremlin, que tem o seguinte formato:

`https://YOUR_DATABASE_ACCOUNT.gremlin.cosmosdb.azure.com:443/`

### <a name="why-am-i-getting-the-requestrateistoolarge-error"></a>Porque é que estou a ter o erro do "RequestRateIsTooLarge"?

Este erro significa que as Unidades de Pedido atribuídas por segundo não são suficientes para servir a consulta. Este erro é geralmente visto quando executa uma consulta que obtém todos os vértices:

```
// Query example:
g.V()
```

Esta consulta tentará recuperar todos os vértices do gráfico. Assim, o custo desta consulta será igual ao número de vértices em termos de RUs. A definição RU/s deve ser ajustada para abordar esta consulta.

### <a name="why-do-my-gremlin-driver-connections-get-dropped-eventually"></a>Porque é que as ligações ao condutor do Gremlin acabam por ser abandonadas?

Uma ligação Gremlin é feita através de uma ligação WebSocket. Embora as ligações WebSocket não tenham um tempo específico para viver, a API Azure Cosmos DB Gremlin terminará as ligações inativas após 30 minutos de inatividade.

### <a name="why-cant-i-use-fluent-api-calls-in-the-native-gremlin-drivers"></a>Por que não posso usar chamadas fluentes de API nos motoristas nativos de Gremlin?

As chamadas fluentes da API ainda não são apoiadas pela API Do Cosmos Azure. As chamadas fluentes da API requerem uma funcionalidade de formatação interna conhecida como suporte de bytecode que atualmente não é suportada pela API DD DD DD D. Azure Cosmos. Devido à mesma razão, o mais recente controlador Gremlin-JavaScript também não é suportado.

## <a name="next-steps"></a>Passos seguintes

* [Suporte ao protocolo de arame Azure Cosmos DB Gremlin](gremlin-support.md)
* Criar, consultar e atravessar uma base de dados de gráficos Azure Cosmos DB utilizando a [consola Gremlin](create-graph-gremlin-console.md)
