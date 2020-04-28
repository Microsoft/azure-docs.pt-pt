---
title: Cabeçalhos de resposta Azure Cosmos DB Gremlin
description: Documentação de referência para metadados de resposta ao servidor que permite resolução adicional de problemas
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: reference
ms.date: 09/03/2019
author: luisbosquez
ms.author: lbosq
ms.openlocfilehash: 95677f4c45c0213de5ffac5521bac1c6bf7294e4
ms.sourcegitcommit: fad3aaac5af8c1b3f2ec26f75a8f06e8692c94ed
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/27/2020
ms.locfileid: "72755086"
---
# <a name="azure-cosmos-db-gremlin-server-response-headers"></a>Cabeçalhos de resposta ao servidor Azure Cosmos DB Gremlin
Este artigo aborda os cabeçalhos devolvidos pelo servidor do Gremlin no Cosmos DB ao chamador mediante a execução do pedido. Estes cabeçalhos são úteis para resolver problemas de desempenho de pedidos, ao criar aplicações que se integram nativamente no serviço Cosmos DB e ao simplificar o suporte ao cliente.

Tenha em mente que, tomando a dependência destes cabeçalhos, está a limitar a portabilidade da sua aplicação a outras implementações da Gremlin. Em troca, está a ganhar uma integração mais apertada com cosmos DB Gremlin. Estes cabeçalhos não são um padrão TinkerPop.

## <a name="headers"></a>Cabeçalhos

| Cabeçalho | Tipo | Valor da Amostra | Quando Incluído | Explicação |
| --- | --- | --- | --- | --- |
| **x-ms-request-charge** | double | 11.3243 | Êxito e Falha | Quantidade de entrada de recolha ou base de dados consumida em unidades de [pedido (RU/s ou RUs)](request-units.md) para uma mensagem de resposta parcial. Este cabeçalho está presente em todas as continuações para pedidos que têm vários pedaços. Reflete a acusação de um pedaço de resposta particular. Apenas para pedidos que consistem numa única parte de resposta este cabeçalho corresponde ao custo total de traversal. No entanto, para a maioria das travessias complexas este valor representa um custo parcial. |
| **x-ms-total-request-charge** | double | 423.987 | Êxito e Falha | Quantidade de entrada de recolha ou base de dados consumida em unidades de [pedido (RU/s ou RUs)](request-units.md) para todo o pedido. Este cabeçalho está presente em todas as continuações para pedidos que têm vários pedaços. Indica encargos cumulativos desde o início do pedido. O valor deste cabeçalho no último pedaço indica uma carga completa de pedido. |
| **x-ms-servidor-tempo-ms** | double | 13.75 | Êxito e Falha | Este cabeçalho está incluído para efeitos de resolução de problemas de latência. Indica a quantidade de tempo, em milissegundos, que o servidor Cosmos DB Gremlin levou para executar e produzir uma mensagem de resposta parcial. A utilização do valor deste cabeçalho e a sua comparação com aplicações globais de latência de pedidos podem calcular as despesas gerais de latência da rede. |
| **x-ms-total-servidor-tempo-ms** | double | 130.512 | Êxito e Falha | O tempo total, em milissegundos, que o servidor Cosmos DB Gremlin levou para executar toda a travessia. Este cabeçalho está incluído em todas as respostas parciais. Representa um tempo de execução cumulativo desde o início do pedido. A última resposta indica o tempo total de execução. Este cabeçalho é útil para diferenciar entre cliente e servidor como fonte de latência. Pode comparar o tempo de execução transversal do cliente ao valor deste cabeçalho. |
| **x-ms-status-code** | long | 200 | Êxito e Falha | O cabeçalho indica uma razão interna para a conclusão ou rescisão do pedido. A aplicação é aconselhada a olhar para o valor deste cabeçalho e a tomar medidas corretivas. |
| **x-ms-substatus-código** | long | 1003 | Apenas falha | Cosmos DB é uma base de dados multi-modelo que é construída em cima da camada de armazenamento unificada. Este cabeçalho contém informações adicionais sobre a razão da falha quando a falha ocorre dentro de camadas mais baixas de pilha de alta disponibilidade. A aplicação é aconselhada a armazenar este cabeçalho e usá-lo ao contactar o apoio ao cliente da Cosmos DB. O valor deste cabeçalho é útil para o engenheiro Cosmos DB para resolução rápida de problemas. |
| **x-ms-retry-after-ms** | corda (TimeSpan) | "00:00:03.9500000" | Apenas falha | Este cabeçalho é uma representação de cadeia de um tipo .NET [TimeSpan.](https://docs.microsoft.com/dotnet/api/system.timespan) Este valor só será incluído nos pedidos falhados devidoà exaustão de entrada. O pedido deve voltar a submeter-se à travessia após o período de tempo instruído. |
| **x-ms-activity-id** | corda (Guia) | "A9218E01-3A3A-4716-9636-5BD86B056613" | Êxito e Falha | O cabeçalho contém um identificador exclusivo do lado do servidor de um pedido. Cada pedido é atribuído a um identificador único pelo servidor para fins de rastreamento. As aplicações devem registar identificadores de atividade devolvidos pelo servidor para pedidos que os clientes possam querer contactar com o apoio do cliente. O pessoal de apoio da Cosmos DB pode encontrar pedidos específicos destes identificadores na telemetria de serviço cosmos DB. |

## <a name="status-codes"></a>Códigos de estado

Os códigos de estado mais comuns devolvidos pelo servidor estão listados abaixo.

| Estado | Explicação |
| --- | --- |
| **401** | A `"Unauthorized: Invalid credentials provided"` mensagem de erro é devolvida quando a palavra-passe de autenticação não corresponde à chave da conta Cosmos DB. Navegue para a sua conta Cosmos DB Gremlin no portal Azure e confirme que a chave está correta.|
| **404** | Operações simultâneas que tentam eliminar e atualizar simultaneamente a mesma borda ou vértice. A mensagem de erro `"Owner resource does not exist"` indica que a base de dados ou a coleção especificada está incorreta nos parâmetros de ligação no formato `/dbs/<database name>/colls/<collection or graph name>`.|
| **408** | `"Server timeout"`indica que a travessia demorou mais de **30 segundos** e foi cancelada pelo servidor. Otimize as suas traversals para correr rapidamente filtrando vértices ou arestas em cada salto de traverso para reduzir o âmbito de pesquisa.|
| **409** | `"Conflicting request to resource has been attempted. Retry to avoid conflicts."` normalmente, ocorre quando um vértice ou uma margem com um identificador já existe no gráfico.| 
| **412** | O código de estado `"PreconditionFailedException": One of the specified pre-condition is not met`é complementado com uma mensagem de erro. Este erro é indicativo de uma violação otimista do controlo da moeda entre ler uma borda ou um vértice e reescrevê-la na loja após modificação. Situações mais comuns quando este erro ocorre `g.V('identifier').property('name','value')`é a modificação de propriedade, por exemplo. O motor Gremlin lia o vértice, modificava-o e escrevia-o de volta. Se houver outra travessia em paralelo tentando escrever o mesmo vértice ou uma borda, um deles receberá este erro. A aplicação deve submeter-se de novo a traverso ao servidor.| 
| **429** | O pedido foi estrangulado e deve ser novamente julgado após o valor em **x-ms-retry-after-ms**| 
| **500** | a mensagem de erro que contém `"NotFoundException: Entity with the specified id does not exist in the system."` indica que uma base de dados e/ou coleção foram recriadas com o mesmo nome. Este erro desaparece no espaço de cinco minutos, à medida que a alteração se propaga e invalida as caches em diferentes componentes do Cosmos DB. Para evitar este problema, utilize sempre nomes de bases de dados e coleções exclusivos.| 
| **1000** | Este código de estado é devolvido quando o servidor analisou com sucesso uma mensagem, mas não foi capaz de executar. Geralmente indica um problema com a consulta.| 
| **1001** | Este código é devolvido quando o servidor completa a execução transversal, mas não consegue serializar a resposta de volta ao cliente. Este erro pode acontecer quando a traversal gera resultados complexos, que são demasiado grandes ou não estão em conformidade com a especificação do protocolo TinkerPop. A aplicação deve simplificar a travessia quando se depara com este erro. | 
| **1003** | `"Query exceeded memory limit. Bytes Consumed: XXX, Max: YYY"`é devolvido quando a travessia excede o limite de memória permitido. O limite de memória é de **2 GB** por traversal.| 
| **1004** | Este código de estado indica pedido de gráfico mal formado. O pedido pode ser mal informado quando falha na desserialização, o tipo de não-valor está a ser desserializado como tipo de valor ou operação de gremlin não suportada solicitada. O pedido não deve voltar a julgar o pedido porque não será bem sucedido. | 
| **1007** | Normalmente, este código de `"Could not process request. Underlying connection has been closed."`estado é devolvido com uma mensagem de erro . Esta situação pode acontecer se o condutor do cliente tentar utilizar uma ligação que está a ser fechada pelo servidor. A aplicação deve voltar a tentar a travessia numa ligação diferente.
| **1008** | O servidor Cosmos DB Gremlin pode terminar ligações para reequilibrar o tráfego no cluster. Os condutores de clientes devem lidar com esta situação e usar apenas ligações ao vivo para enviar pedidos para o servidor. Ocasionalmente, os condutores de clientes podem não detetar que a ligação estava fechada. Quando a aplicação `"Connection is too busy. Please retry after sometime or open more connections."` encontra um erro, deve voltar a tentar atravessar uma ligação diferente.

## <a name="samples"></a>Amostras

Uma aplicação de cliente de amostra com base em Gremlin.Net que lê um atributo de estado:

```csharp
// Following example reads a status code and total request charge from server response attributes.
// Variable "server" is assumed to be assigned to an instance of a GremlinServer that is connected to Cosmos DB account.
using (GremlinClient client = new GremlinClient(server, new GraphSON2Reader(), new GraphSON2Writer(), GremlinClient.GraphSON2MimeType))
{
  ResultSet<dynamic> responseResultSet = await GremlinClientExtensions.SubmitAsync<dynamic>(client, requestScript: "g.V().count()");
  long statusCode = (long)responseResultSet.StatusAttributes["x-ms-status-code"];
  double totalRequestCharge = (double)responseResultSet.StatusAttributes["x-ms-total-request-charge"];

  // Status code and request charge are logged into application telemetry.
}
```

Um exemplo que demonstra como ler o atributo do cliente gremlin java:

```java
try {
  ResultSet resultSet = this.client.submit("g.addV().property('id', '13')");
  List<Result> results = resultSet.all().get();

  // Process and consume results

} catch (ResponseException re) {
  // Check for known errors that need to be retried or skipped
  if (re.getStatusAttributes().isPresent()) {
    Map<String, Object> attributes = re.getStatusAttributes().get();
    int statusCode = (int) attributes.getOrDefault("x-ms-status-code", -1);

    // Now we can check for specific conditions
    if (statusCode == 409) {
        // Handle conflicting writes
      }
    }

    // Check if we need to delay retry
    if (attributes.containsKey("x-ms-retry-after-ms")) {
      // Read the value of the attribute as is
      String retryAfterTimeSpan = (String) attributes.get("x-ms-retry-after-ms"));

      // Convert the value into actionable duration
            LocalTime locaTime = LocalTime.parse(retryAfterTimeSpan);
            Duration duration = Duration.between(LocalTime.MIN, locaTime);

      // Perform a retry after "duration" interval of time has elapsed
    }
  }
}

```

## <a name="next-steps"></a>Passos seguintes
* [Códigos de estado HTTP para Azure Cosmos DB](https://docs.microsoft.com/rest/api/cosmos-db/http-status-codes-for-cosmosdb) 
* [Cabeçalhos de resposta Common Azure Cosmos DB REST](https://docs.microsoft.com/rest/api/cosmos-db/common-cosmosdb-rest-response-headers)
* [Requisitos do fornecedor de motorista de gráfico stinkerpop]( http://tinkerpop.apache.org/docs/current/dev/provider/#_graph_driver_provider_requirements)
