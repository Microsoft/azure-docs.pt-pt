---
title: Como gerir ligações nas funções do Azure
description: Saiba como evitar problemas de desempenho nas funções do Azure com os clientes de ligação estático.
services: functions
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.topic: conceptual
ms.date: 02/25/2018
ms.author: glenga
ms.openlocfilehash: df4fcb505cce17663334d9b80245f5c981cdbe1e
ms.sourcegitcommit: f7f4b83996640d6fa35aea889dbf9073ba4422f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/28/2019
ms.locfileid: "56989631"
---
# <a name="how-to-manage-connections-in-azure-functions"></a>Como gerir ligações nas funções do Azure

As funções na aplicação de função partilhar recursos e entre esses recursos partilhados são ligações &mdash; ligações HTTP, ligações de base de dados e ligações para serviços do Azure, como o armazenamento. Quando muitas funções estão em execução em simultâneo, é possível a ficar sem ligações disponíveis. Este artigo explica como codificar as suas funções para evitar a utilização de ligações de mais do que na verdade, o necessário.

## <a name="connections-limit"></a>Limite de ligações

O número de ligações disponíveis é limitado em parte porque uma aplicação de funções é executado num [ambiente sandbox](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox). Uma das restrições que impõe de área de segurança no seu código é um [limite no número de ligações (atualmente em 600 ligações ativas, total de ligações de 1200)](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox#numerical-sandbox-limits) por instância. Quando atingir este limite, o runtime das funções cria um registo com a seguinte mensagem: `Host thresholds exceeded: Connections`.

Este limite é por instância.  Quando o [controlador de escala adiciona instâncias de aplicações de função](functions-scale.md#how-the-consumption-plan-works) para processar mais pedidos, cada instância tem um limite de ligação independente.  Isso significa que não tem qualquer limite de ligações globais e, no total, pode ter muito mais de 600 ligações ativas em todas as instâncias de Active Directory.

## <a name="use-static-clients"></a>Utilizar clientes estáticos

Para evitar que contém mais ligações do que o necessário, reutilize instâncias de cliente, em vez de criar aplicações novas com cada invocação de função.  Reutilização de ligações de cliente é recomendada para qualquer linguagem, que pode escrever a sua função na. Por exemplo, os clientes .NET, como o [HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.110).aspx), [DocumentClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.documentclient
), e os clientes de armazenamento do Azure podem gerir ligações se utilizar um único cliente estático.

Aqui estão algumas diretrizes a seguir ao utilizar um cliente de serviços específicos de uma aplicação de funções do Azure:

- **Isso não é possível** criar um novo cliente com cada invocação de função.
- **FAZER** criar um único cliente estático que pode ser utilizado por cada invocação de função.
- **CONSIDERE** criação de um único cliente estático numa classe auxiliar partilhado se funções diferentes usam o mesmo serviço.

## <a name="client-code-examples"></a>Exemplos de código do cliente

Esta secção demonstra as melhores práticas para criar e utilizar os clientes a partir do código de função.

### <a name="httpclient-example-c"></a>Exemplo do HttpClient (C#)

Eis um exemplo de C# função de código que cria um estático [HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.110).aspx):

```cs
// Create a single, static HttpClient
private static HttpClient httpClient = new HttpClient();

public static async Task Run(string input)
{
    var response = await httpClient.GetAsync("https://example.com");
    // Rest of function
}
```

Uma pergunta comum sobre o .NET [HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.110).aspx) é "Deve, ser descartar meu cliente?" Em geral, descartar objetos que implementam `IDisposable` quando terminar a utilizá-los. Mas não descartar um cliente estático porque não estão a ser feito usá-lo quando a função termina. Pretender que o cliente estático vivem pela duração da sua aplicação.

### <a name="http-agent-examples-nodejs"></a>Exemplos de agente HTTP (node. js)

Uma vez que ele fornece a ligação melhor as opções de gestão, deve utilizar nativa [ `http.agent` ](https://nodejs.org/dist/latest-v6.x/docs/api/http.html#http_class_http_agent) classe em vez de métodos não nativo, como o `node-fetch` módulo. Parâmetros de ligação estão configurados com opções no `http.agent` classe. Ver [novo agente (\[opções\])](https://nodejs.org/dist/latest-v6.x/docs/api/http.html#http_new_agent_options) para as opções detalhadas disponíveis com o agente HTTP.

O global `http.globalAgent` utilizado pelo `http.request()` tem todos esses valores definidos para as respetivas predefinições. É a forma recomendada de configurar limites de conexão em funções definir um número máximo de globalmente. O exemplo seguinte define o número máximo de soquetes para a aplicação de funções:

```js
http.globalAgent.maxSockets = 200;
```

 O exemplo seguinte cria um novo pedido HTTP com um agente HTTP personalizado apenas para esse pedido.

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

## <a name="sqlclient-connections"></a>Ligações do SqlClient

O código de função pode utilizar o .NET Framework Data Provider para SQL Server ([SqlClient](https://msdn.microsoft.com/library/system.data.sqlclient(v=vs.110).aspx)) para efetuar ligações para uma base de dados relacional do SQL. Isso também é o fornecedor subjacente para estruturas de dados que se baseiam no ADO.NET, como o Entity Framework. Ao contrário [HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.110).aspx) e [DocumentClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.documentclient
) conexões, o ADO.NET implementa de pool de conexões por predefinição. No entanto, uma vez que continua a poder executar fora de ligações, deve otimizar ligações à base de dados. Para obter mais informações, consulte [SQL Server ligação agrupamento (ADO.NET)](https://docs.microsoft.com/dotnet/framework/data/adonet/sql-server-connection-pooling).

> [!TIP]
> Algumas estruturas de dados, tal como [Entity Framework](https://msdn.microsoft.com/library/aa937723(v=vs.113).aspx), normalmente, obtém as cadeias de ligação da **ConnectionStrings** secção de um ficheiro de configuração. Neste caso, tem de adicionar explicitamente cadeias de ligação de base de dados SQL para o **cadeias de ligação** coleção das definições de aplicação de função e, no [ficheiro Settings](functions-run-local.md#local-settings-file) no seu projeto local. Se estiver a criar uma [SqlConnection](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection(v=vs.110).aspx) no código da função, deve armazenar o valor da cadeia de ligação **configurações de aplicativo** com as outras ligações.

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações sobre por que clientes estáticos são recomendados, consulte [anti-padrão de instâncias impróprias](https://docs.microsoft.com/azure/architecture/antipatterns/improper-instantiation/).

Para mais sugestões de desempenho de funções do Azure, consulte [otimizar o desempenho e fiabilidade das funções do Azure](functions-best-practices.md).
