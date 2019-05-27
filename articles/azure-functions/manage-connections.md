---
title: Gerir ligações nas funções do Azure
description: Saiba como evitar problemas de desempenho nas funções do Azure com os clientes de ligação estático.
services: functions
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.topic: conceptual
ms.date: 02/25/2018
ms.author: glenga
ms.openlocfilehash: 69425129d5f049254a60032283ddc6ca2ab84d5c
ms.sourcegitcommit: 4c2b9bc9cc704652cc77f33a870c4ec2d0579451
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/17/2019
ms.locfileid: "65872697"
---
# <a name="manage-connections-in-azure-functions"></a>Gerir ligações nas funções do Azure

As funções numa aplicação de funções partilham recursos. Entre esses recursos partilhados são ligações: Ligações HTTP, ligações de base de dados e ligações para serviços como o armazenamento do Azure. Quando muitas funções estão em execução em simultâneo, é possível a ficar sem ligações disponíveis. Este artigo explica como codificar as suas funções para evitar a utilização de ligações de mais do que o necessário.

## <a name="connection-limit"></a>Limite de ligação

O número de ligações disponíveis é limitado em parte porque uma aplicação de funções é executado num [ambiente sandbox](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox). Uma das restrições que impõe de área de segurança no seu código é um limite no número de ligações de saída, que atualmente é de 600 por instância de ligações (total de 1200) ativas. Quando atingir este limite, o runtime das funções escreve a seguinte mensagem nos registos: `Host thresholds exceeded: Connections`. Para obter mais informações, consulte a [limites de serviço de funções](functions-scale.md#service-limits).

Este limite é por instância. Quando o [controlador de escala adiciona instâncias de aplicações de função](functions-scale.md#how-the-consumption-and-premium-plans-work) para processar mais pedidos, cada instância tem um limite de ligação independente. Isso significa que não tem qualquer limite de ligações globais, e pode ter muito mais de 600 ligações ativas em todas as instâncias de Active Directory.

Quando a resolução de problemas, certifique-se de que ativou o Application Insights para a sua aplicação de função. O Application Insights permite-lhe ver as métricas para as suas aplicações de função como execuções. Para obter mais informações, consulte [ver a telemetria no Application Insights](functions-monitoring.md#view-telemetry-in-application-insights).  

## <a name="static-clients"></a>Clientes estáticos

Para evitar que contém mais ligações do que o necessário, reutilize instâncias de cliente, em vez de criar aplicações novas com cada invocação de função. Recomendamos a reutilização de ligações de cliente para qualquer idioma que pode escrever a sua função na. Por exemplo, os clientes .NET, como o [HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.110).aspx), [DocumentClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.documentclient
), e os clientes de armazenamento do Azure podem gerir ligações se utilizar um único cliente estático.

Aqui estão algumas diretrizes a seguir quando estiver a utilizar um cliente específico do serviço num aplicativo de funções do Azure:

- *Isso não é possível* criar um novo cliente com cada invocação de função.
- *Fazer* criar um único cliente estático que pode utilizar cada invocação de função.
- *Considere* criação de um único cliente estático numa classe auxiliar partilhado se funções diferentes usam o mesmo serviço.

## <a name="client-code-examples"></a>Exemplos de código do cliente

Esta secção demonstra as melhores práticas para criar e utilizar os clientes a partir do código de função.

### <a name="httpclient-example-c"></a>Exemplo do HttpClient (C#)

Eis um exemplo de C# função de código que cria um estático [HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.110).aspx) instância:

```cs
// Create a single, static HttpClient
private static HttpClient httpClient = new HttpClient();

public static async Task Run(string input)
{
    var response = await httpClient.GetAsync("https://example.com");
    // Rest of function
}
```

Uma pergunta comum sobre [HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.110).aspx) no .NET é "Deve eu descartar meu cliente?" Em geral, descartar objetos que implementam `IDisposable` quando terminar a utilizá-los. Mas não descartar um cliente estático porque não estão a ser feito usá-lo quando a função termina. Pretender que o cliente estático vivem pela duração da sua aplicação.

### <a name="http-agent-examples-javascript"></a>Exemplos de agente HTTP (JavaScript)

Uma vez que ele fornece a ligação melhor as opções de gestão, deve utilizar nativa [ `http.agent` ](https://nodejs.org/dist/latest-v6.x/docs/api/http.html#http_class_http_agent) classe em vez de métodos não nativo, como o `node-fetch` módulo. Parâmetros de ligação são configurados por meio de opções no `http.agent` classe. Para opções detalhadas disponíveis com o agente HTTP, consulte [novo agente (\[opções\])](https://nodejs.org/dist/latest-v6.x/docs/api/http.html#http_new_agent_options).

O global `http.globalAgent` classe usada pelo `http.request()` tem todos esses valores definidos para as respetivas predefinições. É a forma recomendada de configurar limites de conexão em funções definir um número máximo de globalmente. O exemplo seguinte define o número máximo de soquetes para a aplicação de funções:

```js
http.globalAgent.maxSockets = 200;
```

 O exemplo seguinte cria um novo pedido HTTP com um agente HTTP personalizado apenas para esse pedido:

```js
var http = require('http');
var httpAgent = new http.Agent();
httpAgent.maxSockets = 200;
options.agent = httpAgent;
http.request(options, onResponseCallback);
```

### <a name="documentclient-code-example-c"></a>Exemplo de código do DocumentClient (C#)

[DocumentClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.documentclient
) se liga a uma instância do Azure Cosmos DB. A documentação do Azure Cosmos DB recomenda que [utilizar o cliente do Azure Cosmos DB singleton durante o ciclo de vida do seu aplicativo](https://docs.microsoft.com/azure/cosmos-db/performance-tips#sdk-usage). O exemplo seguinte mostra um padrão para fazer isso de uma função:

```cs
#r "Microsoft.Azure.Documents.Client"
using Microsoft.Azure.Documents.Client;

private static Lazy<DocumentClient> lazyClient = new Lazy<DocumentClient>(InitializeDocumentClient);
private static DocumentClient documentClient => lazyClient.Value;

private static DocumentClient InitializeDocumentClient()
{
    // Perform any initialization here
    var uri = new Uri("example");
    var authKey = "authKey";
    
    return new DocumentClient(uri, authKey);
}

public static async Task Run(string input)
{
    Uri collectionUri = UriFactory.CreateDocumentCollectionUri("database", "collection");
    object document = new { Data = "example" };
    await documentClient.UpsertDocumentAsync(collectionUri, document);
    
    // Rest of function
}
```

### <a name="cosmosclient-code-example-javascript"></a>Exemplo de código CosmosClient (JavaScript)
[CosmosClient](/javascript/api/@azure/cosmos/cosmosclient) se liga a uma instância do Azure Cosmos DB. A documentação do Azure Cosmos DB recomenda que [utilizar o cliente do Azure Cosmos DB singleton durante o ciclo de vida do seu aplicativo](../cosmos-db/performance-tips.md#sdk-usage). O exemplo seguinte mostra um padrão para fazer isso de uma função:

```javascript
const cosmos = require('@azure/cosmos');
const endpoint = process.env.COSMOS_API_URL;
const masterKey = process.env.COSMOS_API_KEY;
const { CosmosClient } = cosmos;

const client = new CosmosClient({ endpoint, auth: { masterKey } });
// All function invocations also reference the same database and container.
const container = client.database("MyDatabaseName").container("MyContainerName");

module.exports = async function (context) {
    const { result: itemArray } = await container.items.readAll().toArray();
    context.log(itemArray);
}
```

## <a name="sqlclient-connections"></a>Ligações do SqlClient

O código de função pode utilizar o .NET Framework Data Provider para SQL Server ([SqlClient](https://msdn.microsoft.com/library/system.data.sqlclient(v=vs.110).aspx)) para efetuar ligações para uma base de dados relacional do SQL. Isso também é o fornecedor subjacente para estruturas de dados que se baseiam no ADO.NET, como [Entity Framework](https://msdn.microsoft.com/library/aa937723(v=vs.113).aspx). Ao contrário [HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.110).aspx) e [DocumentClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.documentclient
) conexões, o ADO.NET implementa de pool de conexões por predefinição. Mas, porque pode continuar a executar fora de ligações, deve otimizar ligações à base de dados. Para obter mais informações, consulte [SQL Server ligação agrupamento (ADO.NET)](https://docs.microsoft.com/dotnet/framework/data/adonet/sql-server-connection-pooling).

> [!TIP]
> Algumas estruturas de dados, como o Entity Framework, normalmente, obtém cadeias de ligação a partir da **ConnectionStrings** secção de um ficheiro de configuração. Neste caso, tem de adicionar explicitamente cadeias de ligação de base de dados SQL para o **cadeias de ligação** coleção das definições de aplicação de função e, no [ficheiro Settings](functions-run-local.md#local-settings-file) no seu projeto local. Se estiver a criar uma instância do [SqlConnection](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection(v=vs.110).aspx) no código da função, deve armazenar o valor de cadeia de ligação na **configurações de aplicativo** com as outras ligações.

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações sobre por que recomendamos que os clientes estáticos, consulte [anti-padrão de instâncias impróprias](https://docs.microsoft.com/azure/architecture/antipatterns/improper-instantiation/).

Para mais sugestões de desempenho de funções do Azure, consulte [otimizar o desempenho e fiabilidade das funções do Azure](functions-best-practices.md).
