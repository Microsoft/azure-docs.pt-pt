---
title: Gerir ligações em Funções Azure
description: Aprenda a evitar problemas de desempenho nas Funções Azure utilizando clientes de conexão estática.
ms.topic: conceptual
ms.date: 02/25/2018
ms.openlocfilehash: 872ad9a1b8f0a7da6fe410e68f08469ac11045a5
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78370467"
---
# <a name="manage-connections-in-azure-functions"></a>Gerir ligações em Funções Azure

As funções numa aplicação de função partilham recursos. Entre esses recursos partilhados estão ligações: conexões HTTP, ligações de base de dados e ligações a serviços como o Armazenamento Azure. Quando muitas funções estão a funcionar simultaneamente, é possível ficar sem ligações disponíveis. Este artigo explica como codificar as suas funções para evitar usar mais ligações do que as necessárias.

## <a name="connection-limit"></a>Limite de ligação

O número de ligações disponíveis é limitado em parte porque uma aplicação de função funciona num ambiente de caixa de [areia.](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox) Uma das restrições que a caixa de areia impõe ao seu código é um limite para o número de ligações de saída, que atualmente são 600 ligações ativas (1.200 no total) por exemplo. Quando se chega a este limite, as funções de tempo de funcionamento escrevem a seguinte mensagem aos registos: `Host thresholds exceeded: Connections`. Para mais informações, consulte os limites de [serviço functions](functions-scale.md#service-limits).

Este limite é por exemplo. Quando o controlador de [escala adiciona instâncias](functions-scale.md#how-the-consumption-and-premium-plans-work) de aplicações de função para lidar com mais pedidos, cada instância tem um limite de ligação independente. Isso significa que não há limite de ligação global, e você pode ter muito mais de 600 conexões ativas em todos os casos ativos.

Ao resolver problemas, certifique-se de que ativou os Insights de Aplicação para a sua aplicação de função. Os Insights de Aplicação permitem-lhe visualizar métricas para as suas aplicações de função, como execuções. Para mais informações, consulte [a telemetria ver em Insights de Aplicação](functions-monitoring.md#view-telemetry-in-application-insights).  

## <a name="static-clients"></a>Clientes estáticos

Para evitar a detenção de mais ligações do que o necessário, reutilize as instâncias dos clientes em vez de criar novas com cada invocação de função. Recomendamos que reutilize as ligações do cliente para qualquer idioma em que possa escrever a sua função. Por exemplo, clientes .NET como os clientes [HttpClient,](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.110).aspx) [DocumentClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.documentclient
)e Azure Storage podem gerir ligações se utilizar um único cliente estático.

Aqui ficam algumas diretrizes a seguir quando está a usar um cliente específico do serviço numa aplicação de Funções Azure:

- *Não* crie um novo cliente com todas as funções de invocação.
- *Crie* um único cliente estático que cada invocação de função possa usar.
- *Considere* criar um único cliente estático numa classe de ajudante compartilhado se diferentes funções usarem o mesmo serviço.

## <a name="client-code-examples"></a>Exemplos de código de cliente

Esta secção demonstra as melhores práticas para criar e utilizar clientes a partir do seu código de funcionamento.

### <a name="httpclient-example-c"></a>Exemplo httpClientC#()

Aqui está um C# exemplo de código de função que cria uma instância estática [httpClient:](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.110).aspx)

```cs
// Create a single, static HttpClient
private static HttpClient httpClient = new HttpClient();

public static async Task Run(string input)
{
    var response = await httpClient.GetAsync("https://example.com");
    // Rest of function
}
```

Uma pergunta comum sobre [httpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.110).aspx) em .NET é "Devo dispor do meu cliente?" Em geral, elimina-se os objetos que implementam `IDisposable` quando acaba de os utilizar. Mas não se livra de um cliente estático porque ainda não acaba de usá-lo quando a função termina. Quer que o cliente estático viva durante a duração da sua candidatura.

### <a name="http-agent-examples-javascript"></a>Exemplos de agente HTTP (JavaScript)

Como fornece melhores opções de gestão de ligação, você deve usar a classe [de`http.agent`](https://nodejs.org/dist/latest-v6.x/docs/api/http.html#http_class_http_agent) nativo em vez de métodos não nativos, como o módulo `node-fetch`. Os parâmetros de ligação são configurados através de opções na classe `http.agent`. Para obter opções detalhadas disponíveis com o agente HTTP, consulte o novo Agente,\[[opções\].](https://nodejs.org/dist/latest-v6.x/docs/api/http.html#http_new_agent_options)

A classe global `http.globalAgent` usada pela `http.request()` tem todos estes valores definidos para os respetivos incumprimentos. A forma recomendada de configurar os limites de ligação nas Funções é definir um número máximo a nível global. O exemplo que se segue define o número máximo de tomadas para a aplicação de funções:

```js
http.globalAgent.maxSockets = 200;
```

 O exemplo seguinte cria um novo pedido http com um agente HTTP personalizado apenas para esse pedido:

```js
var http = require('http');
var httpAgent = new http.Agent();
httpAgent.maxSockets = 200;
options.agent = httpAgent;
http.request(options, onResponseCallback);
```

### <a name="documentclient-code-example-c"></a>Exemplo de códigoC#Do Cliente documental ()

[O DocumentClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.documentclient
) liga-se a uma instância de Azure Cosmos DB. A documentação Azure Cosmos DB recomenda que [utilize um cliente Singleton Azure Cosmos DB durante toda a sua aplicação.](https://docs.microsoft.com/azure/cosmos-db/performance-tips#sdk-usage) O exemplo que se segue mostra um padrão para fazer isso numa função:

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
[CosmosClient](/javascript/api/@azure/cosmos/cosmosclient) conecta-se a uma instância De Db Azure Cosmos. A documentação Azure Cosmos DB recomenda que [utilize um cliente Singleton Azure Cosmos DB durante toda a sua aplicação.](../cosmos-db/performance-tips.md#sdk-usage) O exemplo que se segue mostra um padrão para fazer isso numa função:

```javascript
const cosmos = require('@azure/cosmos');
const endpoint = process.env.COSMOS_API_URL;
const key = process.env.COSMOS_API_KEY;
const { CosmosClient } = cosmos;

const client = new CosmosClient({ endpoint, key });
// All function invocations also reference the same database and container.
const container = client.database("MyDatabaseName").container("MyContainerName");

module.exports = async function (context) {
    const { resources: itemArray } = await container.items.readAll().fetchAll();
    context.log(itemArray);
}
```

## <a name="sqlclient-connections"></a>Conexões SqlClient

O seu código de funcionamento pode utilizar o Fornecedor de Dados de Enquadramento .NET para o Servidor SQL[(SqlClient)](https://msdn.microsoft.com/library/system.data.sqlclient(v=vs.110).aspx)para efetuar ligações a uma base de dados relacional SQL. Este é também o fornecedor subjacente a quadros de dados que dependem de ADO.NET, como o Quadro de [Entidades.](https://msdn.microsoft.com/library/aa937723(v=vs.113).aspx) Ao contrário das ligações [HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.110).aspx) e [DocumentClient,](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.documentclient
) ADO.NET implementa o agrupamento de ligação por padrão. Mas como ainda pode ficar sem ligações, deve otimizar as ligações à base de dados. Para mais informações, consulte o Pooling de Ligação ao [Servidor SQL (ADO.NET)](https://docs.microsoft.com/dotnet/framework/data/adonet/sql-server-connection-pooling).

> [!TIP]
> Algumas estruturas de dados, como o Quadro de Entidades, normalmente obtêm cordas de ligação da secção **ConnectionStrings** de um ficheiro de configuração. Neste caso, deve adicionar explicitamente as cadeias de ligação à base de dados SQL à recolha de **cordas de Ligação** das definições da sua aplicação de função e no [ficheiro local.settings.json](functions-run-local.md#local-settings-file) no seu projeto local. Se estiver a criar uma instância de [SqlConnection](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection(v=vs.110).aspx) no seu código de funcionamento, deverá armazenar o valor de cadeia de ligação nas definições da **Aplicação** com as suas outras ligações.

## <a name="next-steps"></a>Passos Seguintes

Para mais informações sobre o porquê de recomendarmos clientes estáticos, consulte [antipadrão](https://docs.microsoft.com/azure/architecture/antipatterns/improper-instantiation/)de instantânea impróprio .

Para obter mais dicas de desempenho das Funções Azure, consulte [Otimizar o desempenho e a fiabilidade das Funções Azure.](functions-best-practices.md)
