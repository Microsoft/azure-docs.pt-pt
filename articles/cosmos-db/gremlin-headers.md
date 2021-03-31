---
title: Cabeçalhos de resposta Azure Cosmos DB Gremlin
description: Documentação de referência para metadados de resposta ao servidor que permite a resolução adicional de problemas
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: reference
ms.date: 09/03/2019
author: christopheranderson
ms.author: chrande
ms.openlocfilehash: 0442d21aebe1cf577c50d14a5aeff40bd1f6cd9c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98600525"
---
# <a name="azure-cosmos-db-gremlin-server-response-headers"></a>Cabeçalhos de resposta do servidor Azure Cosmos DB Gremlin
[!INCLUDE[appliesto-gremlin-api](includes/appliesto-gremlin-api.md)]

Este artigo aborda os cabeçalhos devolvidos pelo servidor do Gremlin no Cosmos DB ao chamador mediante a execução do pedido. Estes cabeçalhos são úteis para resolver problemas de desempenho de pedidos, ao criar aplicações que se integram nativamente no serviço Cosmos DB e ao simplificar o suporte ao cliente.

Tenha em mente que, tendo em conta estes cabeçalhos, está a limitar a portabilidade da sua aplicação a outras implementações da Gremlin. Em troca, está a ganhar uma integração mais apertada com a Cosmos DB Gremlin. Estes cabeçalhos não são um padrão TinkerPop.

## <a name="headers"></a>Cabeçalhos

| Cabeçalho | Tipo | Valor da amostra | Quando Incluído | Explicação |
| --- | --- | --- | --- | --- |
| **x-ms-request-charge** | double | 11.3243 | Êxito e Falha | Quantidade de recolha ou produção de base de dados consumida em [unidades de pedido (RU/s ou RUs)](request-units.md) para uma mensagem de resposta parcial. Este cabeçalho está presente em todas as continuações para pedidos que têm vários pedaços. Reflete a carga de um determinado pedaço de resposta. Apenas para pedidos que consistem de uma única resposta este cabeçalho corresponde ao custo total de traversal. No entanto, para a maioria dos traversais complexos este valor representa um custo parcial. |
| **x-ms-total-request-charge** | double | 423.987 | Êxito e Falha | Quantidade de recolha ou produção de base de dados consumida em [unidades de pedido (RU/s ou RUs)](request-units.md) para pedidos completos. Este cabeçalho está presente em todas as continuações para pedidos que têm vários pedaços. Indica carga cumulativa desde o início do pedido. O valor deste cabeçalho na última parte indica a cobrança completa do pedido. |
| **x-ms-servidor-time-ms** | double | 13.75 | Êxito e Falha | Este cabeçalho está incluído para efeitos de resolução de problemas de latência. Indica a quantidade de tempo, em milissegundos, que o servidor Cosmos DB Gremlin levou para executar e produzir uma mensagem de resposta parcial. A utilização do valor deste cabeçalho e a comparação com as aplicações globais de latência de pedidos podem calcular a latência da rede. |
| **x-ms-total-servidor-time-ms** | double | 130.512 | Êxito e Falha | Tempo total, em milissegundos, que o servidor Cosmos DB Gremlin demorou a executar todo o traversal. Este cabeçalho está incluído em todas as respostas parciais. Representa um período de execução cumulativo desde o início do pedido. A última resposta indica o tempo total de execução. Este cabeçalho é útil para diferenciar entre cliente e servidor como fonte de latência. Pode comparar o tempo de execução transversal do cliente com o valor deste cabeçalho. |
| **x-ms-status-code** | long | 200 | Êxito e Falha | O cabeçalho indica razão interna para a conclusão ou rescisão do pedido. A aplicação é aconselhada a olhar para o valor deste cabeçalho e a tomar medidas corretivas. |
| **x-ms-substatus-code** | long | 1003 | Apenas falha | Cosmos DB é uma base de dados multi-modelo que é construída em cima de camadas de armazenamento unificadas. Este cabeçalho contém insights adicionais sobre a razão da falha quando a falha ocorre dentro de camadas inferiores de pilha de alta disponibilidade. A aplicação é aconselhada a armazenar este cabeçalho e usá-lo quando contactar o suporte do cliente da Cosmos DB. O valor deste cabeçalho é útil para o engenheiro da Cosmos DB para uma rápida resolução de problemas. |
| **x-ms-retry-after-ms** | cadeia (TimeSpan) | "00:00:03.9500000" | Apenas falha | Este cabeçalho é uma representação de cordas de um tipo .NET [TimeSpan.](/dotnet/api/system.timespan) Este valor só será incluído nos pedidos falhados devidos à exaustão de produção. A aplicação deve voltar aubscrevê-la após o período de tempo instruído. |
| **x-ms-activity-id** | corda (Guid) | "A9218E01-3A3A-4716-9636-5BD86B056613" | Êxito e Falha | O cabeçalho contém um identificador único do lado do servidor de um pedido. Cada pedido é atribuído um identificador único pelo servidor para fins de rastreio. As aplicações devem registar identificadores de atividade devolvidos pelo servidor para pedidos que os clientes possam querer contactar o suporte do cliente. O pessoal de suporte da Cosmos DB pode encontrar pedidos específicos por estes identificadores na telemetria de serviço da Cosmos DB. |

## <a name="status-codes"></a>Códigos de estado

Os códigos mais comuns devolvidos para `x-ms-status-code` atributo de estado pelo servidor estão listados abaixo.

| Estado | Explicação |
| --- | --- |
| **401** | A mensagem de erro `"Unauthorized: Invalid credentials provided"` é devolvida quando a palavra-passe de autenticação não corresponde à chave da conta Cosmos DB. Navegue na sua conta Cosmos DB Gremlin no portal Azure e confirme que a chave está correta.|
| **404** | Operações simultâneas que tentam eliminar e atualizar simultaneamente a mesma borda ou vértice. A mensagem de erro `"Owner resource does not exist"` indica que a base de dados ou a coleção especificada está incorreta nos parâmetros de ligação no formato `/dbs/<database name>/colls/<collection or graph name>`.|
| **409** | `"Conflicting request to resource has been attempted. Retry to avoid conflicts."` normalmente, ocorre quando um vértice ou uma margem com um identificador já existe no gráfico.| 
| **412** | O código de estado é complementado com uma mensagem de erro `"PreconditionFailedException": One of the specified pre-condition is not met` . Este erro é indicativo de uma violação otimista do controlo da concordância entre ler uma borda ou vértice e escrevê-lo de volta à loja após modificação. A maioria das situações comuns quando este erro ocorre é modificação de propriedade, por exemplo `g.V('identifier').property('name','value')` . O motor Gremlin lia o vértice, modificava-o e escrevia-o de volta. Se houver outra travessia em paralelo a tentar escrever o mesmo vértice ou uma borda, um deles receberá este erro. A aplicação deve submeter transversalmente ao servidor novamente.| 
| **429** | Pedido foi acelerado e deve ser novamente julgado após valor em **x-ms-retry-after-ms**| 
| **500** | a mensagem de erro que contém `"NotFoundException: Entity with the specified id does not exist in the system."` indica que uma base de dados e/ou coleção foram recriadas com o mesmo nome. Este erro desaparece no espaço de cinco minutos, à medida que a alteração se propaga e invalida as caches em diferentes componentes do Cosmos DB. Para evitar este problema, utilize sempre nomes de bases de dados e coleções exclusivos.| 
| **1000** | Este código de estado é devolvido quando o servidor analisou com sucesso uma mensagem, mas não foi capaz de executar. Geralmente indica um problema com a consulta.| 
| **1001** | Este código é devolvido quando o servidor completa a execução transversal, mas não consegue serializar a resposta de volta ao cliente. Este erro pode ocorrer quando o traversal gera resultados complexos, que são demasiado grandes ou não estão em conformidade com a especificação do protocolo TinkerPop. A aplicação deve simplificar o traversal quando encontra este erro. | 
| **1003** | `"Query exceeded memory limit. Bytes Consumed: XXX, Max: YYY"` é devolvido quando transversalmente excede o limite de memória permitido. O limite de memória é **de 2 GB** por transversal.| 
| **1004** | Este código de estado indica um pedido de gráfico mal formado. O pedido pode ser mal formado quando falha na deserialização, o tipo sem valor está a ser deserializado como tipo de valor ou operação gremlin não suportada solicitada. O pedido não deve voltar a julgar o pedido porque não será bem sucedido. | 
| **1007** | Normalmente, este código de estado é devolvido com mensagem de erro `"Could not process request. Underlying connection has been closed."` . Esta situação pode acontecer se o condutor do cliente tentar utilizar uma ligação que está a ser fechada pelo servidor. A aplicação deve voltar a tentar a travessia numa ligação diferente.
| **1008** | O servidor Cosmos DB Gremlin pode terminar as ligações para reequilibrar o tráfego no cluster. Os controladores de clientes devem lidar com esta situação e usar apenas ligações ao vivo para enviar pedidos para o servidor. Ocasionalmente, os condutores de clientes podem não detetar que a ligação foi fechada. Quando a aplicação encontra um erro, `"Connection is too busy. Please retry after sometime or open more connections."` deve voltar a tentar atravessar numa ligação diferente.
| **1009** | A operação não foi concluída no tempo atribuído e foi cancelada pelo servidor. Otimize os seus traversais para correr rapidamente filtrando vértices ou arestas em cada salto de travessia para estreitar o alcance da pesquisa. O prazo de padrão de pedido é de **60 segundos**. |

## <a name="samples"></a>Amostras

Uma aplicação de cliente de amostra baseada em Gremlin.Net que lê um atributo de estado:

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

Um exemplo que demonstra como ler o atributo de estado do cliente Gremlin Java:

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
* [Códigos de estado HTTP para Azure Cosmos DB](/rest/api/cosmos-db/http-status-codes-for-cosmosdb) 
* [Cabeçalhos de resposta Common Azure Cosmos DB REST](/rest/api/cosmos-db/common-cosmosdb-rest-response-headers)
* [Requisitos do fornecedor do controlador de gráficos TinkerPop]( http://tinkerpop.apache.org/docs/current/dev/provider/#_graph_driver_provider_requirements)
