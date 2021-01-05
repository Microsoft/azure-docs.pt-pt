---
title: Gerir ligações em Funções Azure
description: Saiba como evitar problemas de desempenho em Funções Azure utilizando clientes de ligação estática.
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.date: 02/25/2018
ms.openlocfilehash: 53848e6273cf59439d44b431652981b18bdd5ba6
ms.sourcegitcommit: 90caa05809d85382c5a50a6804b9a4d8b39ee31e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/23/2020
ms.locfileid: "97755961"
---
# <a name="manage-connections-in-azure-functions"></a>Gerir ligações em Funções Azure

As funções numa aplicação de função partilham recursos. Entre esses recursos partilhados encontram-se ligações: conexões HTTP, ligações de base de dados e ligações a serviços como o Azure Storage. Quando muitas funções estão a funcionar simultaneamente, é possível ficar sem ligações disponíveis. Este artigo explica como codificar as suas funções para evitar a utilização de mais ligações do que as necessárias.

## <a name="connection-limit"></a>Limite de ligações

O número de ligações disponíveis é limitado em parte porque uma aplicação de função funciona num [ambiente de caixa de areia.](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox) Uma das restrições que a caixa de areia impõe no seu código é um limite para o número de ligações de saída, que atualmente é de 600 ligações ativas (1.200 no total) por exemplo. Quando se atinge este limite, o tempo de execução das funções escreve a seguinte mensagem para os registos: `Host thresholds exceeded: Connections` . Para mais informações, consulte os limites de [serviço funções.](functions-scale.md#service-limits)

Este limite é por exemplo. Quando o [controlador de escala adiciona instâncias de aplicação](functions-scale.md#how-the-consumption-and-premium-plans-work) de função para lidar com mais pedidos, cada instância tem um limite de ligação independente. Isso significa que não há limite de ligação global, e você pode ter muito mais de 600 conexões ativas em todos os casos ativos.

Ao resolver problemas, certifique-se de que ativou o Application Insights para a sua aplicação de função. O Application Insights permite-lhe visualizar métricas para as suas aplicações de função como execuções. Para obter mais informações, consulte [a telemetria em Insights de Aplicação](analyze-telemetry-data.md#view-telemetry-in-application-insights).  

## <a name="static-clients"></a>Clientes estáticos

Para evitar mais ligações do que o necessário, reutilizar as instâncias do cliente em vez de criar novas com cada invocação de função. Recomendamos a reutilização das ligações do cliente para qualquer idioma em que possa escrever a sua função. Por exemplo, clientes .NET como os clientes [HttpClient,](/dotnet/api/system.net.http.httpclient?view=netcore-3.1&preserve-view=true) [DocumentClient](/dotnet/api/microsoft.azure.documents.client.documentclient)e Azure Storage podem gerir ligações se utilizar um único cliente estático.

Aqui estão algumas diretrizes a seguir quando estiver a utilizar um cliente específico de serviço numa aplicação Azure Functions:

- *Não* crie um novo cliente com todas as funções invocando.
- *Crie* um único cliente estático que cada invocação de funções pode usar.
- *Considere* criar um cliente único e estático numa classe de ajudante partilhada se diferentes funções usarem o mesmo serviço.

## <a name="client-code-examples"></a>Exemplos de código do cliente

Esta secção demonstra as melhores práticas para criar e utilizar clientes a partir do seu código de função.

### <a name="httpclient-example-c"></a>Exemplo httpClient (C#)

Aqui está um exemplo do código de função C# que cria uma instância estática [httpClient:](/dotnet/api/system.net.http.httpclient?view=netcore-3.1&preserve-view=true)

```cs
// Create a single, static HttpClient
private static HttpClient httpClient = new HttpClient();

public static async Task Run(string input)
{
    var response = await httpClient.GetAsync("https://example.com");
    // Rest of function
}
```

Uma pergunta comum sobre [httpClient](/dotnet/api/system.net.http.httpclient?view=netcore-3.1&preserve-view=true) em .NET é "Devo dispor do meu cliente?" Em geral, deita-se fora os objetos que implementam `IDisposable` quando termina a sua utilização. Mas não se descarta um cliente estático porque não se acaba de usá-lo quando a função termina. Quer que o cliente estático viva durante a sua inscrição.

### <a name="http-agent-examples-javascript"></a>Exemplos de agentes HTTP (JavaScript)

Como fornece melhores opções de gestão de ligação, deve utilizar a classe nativa [`http.agent`](https://nodejs.org/dist/latest-v6.x/docs/api/http.html#http_class_http_agent) em vez de métodos não nativos, como o `node-fetch` módulo. Os parâmetros de ligação são configurados através de opções na `http.agent` classe. Para opções detalhadas disponíveis com o agente HTTP, consulte [novo Agente( \[ \] opções)](https://nodejs.org/dist/latest-v6.x/docs/api/http.html#http_new_agent_options).

A classe global `http.globalAgent` utilizada tem todos estes `http.request()` valores definidos para os respetivos incumprimentos. A forma recomendada de configurar os limites de ligação em Funções é definir um número máximo a nível global. O exemplo a seguir define o número máximo de tomadas para a aplicação de função:

```js
http.globalAgent.maxSockets = 200;
```

 O exemplo a seguir cria um novo pedido HTTP com um agente HTTP personalizado apenas para esse pedido:

```js
var http = require('http');
var httpAgent = new http.Agent();
httpAgent.maxSockets = 200;
options.agent = httpAgent;
http.request(options, onResponseCallback);
```

### <a name="documentclient-code-example-c"></a>Exemplo de código documentclient (C#)

[DocumentClient](/dotnet/api/microsoft.azure.documents.client.documentclient) conecta-se a uma instância DB Azure Cosmos. A documentação da Azure Cosmos DB recomenda que [utilize um cliente singleton Azure Cosmos DB para o tempo de vida da sua aplicação.](../cosmos-db/performance-tips.md#sdk-usage) O exemplo a seguir mostra um padrão para fazer isso numa função:

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
Se estiver a trabalhar com funções v3.x, precisa de uma referência a Microsoft.Azure.DocumentDB.Core. Adicione uma referência no código:

```cs
#r "Microsoft.Azure.DocumentDB.Core"
```
Além disso, crie um ficheiro chamado "function.proj" para o seu gatilho e adicione o conteúdo abaixo:

```cs

<Project Sdk="Microsoft.NET.Sdk">
    <PropertyGroup>
        <TargetFramework>netcoreapp3.0</TargetFramework>
    </PropertyGroup>
    <ItemGroup>
        <PackageReference Include="Microsoft.Azure.DocumentDB.Core" Version="2.12.0" />
    </ItemGroup>
</Project>

```
### <a name="cosmosclient-code-example-javascript"></a>Exemplo de código CosmosClient (JavaScript)
[CosmosClient](/javascript/api/@azure/cosmos/cosmosclient) conecta-se a um exemplo de Azure Cosmos DB. A documentação da Azure Cosmos DB recomenda que [utilize um cliente singleton Azure Cosmos DB para o tempo de vida da sua aplicação.](../cosmos-db/performance-tips.md#sdk-usage) O exemplo a seguir mostra um padrão para fazer isso numa função:

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

## <a name="sqlclient-connections"></a>Ligações SqlClient

O seu código de função pode utilizar o Fornecedor de Dados De Quadro .NET para o SQL Server[(SqlClient)](/dotnet/api/system.data.sqlclient)para fazer ligações a uma base de dados relacional SQL. Este é também o fornecedor subjacente aos quadros de dados que dependem de ADO.NET, como [o Quadro de Entidades.](/ef/ef6/) Ao contrário das ligações [HttpClient](/dotnet/api/system.net.http.httpclient) e [DocumentClient,](/dotnet/api/microsoft.azure.documents.client.documentclient) ADO.NET implementa a ligação por padrão. Mas como ainda pode ficar sem ligações, deve otimizar as ligações à base de dados. Para obter mais informações, consulte [o SqL Server Connection Pooling (ADO.NET)](/dotnet/framework/data/adonet/sql-server-connection-pooling).

> [!TIP]
> Alguns quadros de dados, tais como o Entity Framework, obtêm tipicamente cadeias de ligação a partir da secção **ConnectionStrings** de um ficheiro de configuração. Neste caso, deve adicionar explicitamente as cadeias de ligação à base de dados SQL à recolha de **cordas de ligação** das definições da aplicação de funções e [ nolocal.settings.jsem ficheiro](functions-run-local.md#local-settings-file) no seu projeto local. Se estiver a criar uma instância de [SqlConnection](/dotnet/api/system.data.sqlclient.sqlconnection) no seu código de função, deverá armazenar o valor da cadeia de ligação nas **definições de Aplicação** com as suas outras ligações.

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre o porquê de recomendarmos clientes estáticos, consulte [anti-artigo de instantâneo impróprio.](/azure/architecture/antipatterns/improper-instantiation/)

Para obter mais dicas de desempenho do Azure Functions, consulte [Otimizar o desempenho e a fiabilidade das Funções Azure](functions-best-practices.md).
