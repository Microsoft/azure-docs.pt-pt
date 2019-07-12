---
title: Criação de um ponto de extremidade RESTful para fornecedores personalizados
description: Este tutorial irá abordar como criar um ponto de extremidade RESTful para fornecedores personalizados. Ele irá entrar em detalhes sobre como lidar com solicitações e respostas para os métodos RESTful HTTP com suporte.
author: jjbfour
ms.service: managed-applications
ms.topic: tutorial
ms.date: 06/19/2019
ms.author: jobreen
ms.openlocfilehash: 176e3b02cbda7577e306d86363cfe5b41335fb6e
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67800037"
---
# <a name="authoring-a-restful-endpoint-for-custom-providers"></a>Criação de um ponto de extremidade RESTful para fornecedores personalizados

Fornecedores personalizados permitem-lhe personalizar fluxos de trabalho no Azure. Um provedor personalizado é um contrato entre o Azure e um `endpoint`. Neste tutorial irá percorrer o processo de criação de um provedor personalizado RESTful `endpoint`. Se não estiver familiarizado com fornecedores personalizados do Azure, veja [a visão geral sobre provedores de recursos personalizados](./custom-providers-overview.md).

Este tutorial é dividido nos seguintes passos:

- Trabalhar com ações personalizadas e recursos personalizados
- Como particionar os recursos personalizados no armazenamento
- Suporte a métodos RESTful do provedor personalizado
- Integrar o RESTful operations

Neste tutorial, irá criar nos tutoriais seguintes:

- [Configurar as funções do Azure para fornecedores personalizados do Azure](./tutorial-custom-providers-function-setup.md)

> [!NOTE]
> Este tutorial baseia-se desativar o tutorial anterior. Alguns dos passos do tutorial só funcionará se uma função do Azure foi configurado para trabalhar com fornecedores personalizados.

## <a name="working-with-custom-actions-and-custom-resources"></a>Trabalhar com ações personalizadas e recursos personalizados

Neste tutorial, iremos atualizar a função para funcionar como um ponto de extremidade RESTful para nosso fornecedor personalizado. No Azure a recursos e ações são modeladas após a especificação de RESTful básica: COLOCAR - cria um novo recurso GET (instância) - obtém um recurso existente, DELETE - Remove um recurso existente, POST - acionar uma ação e GET (coleção), apresenta uma lista de todos os recursos existentes. Para este tutorial, utilizaremos as tabelas do Azure como o nosso armazenamento, mas qualquer serviço de base de dados ou armazenamento pode trabalhar.

## <a name="how-to-partition-custom-resources-in-storage"></a>Como particionar os recursos personalizados no armazenamento

Uma vez que estamos a criar um serviço RESTful, é necessário armazenar os recursos criados no armazenamento. Para o armazenamento de tabelas do Azure, é necessário gerar chaves de partição e de linha para nossos dados. Para fornecedores personalizados, os dados devem ser particionados para o provedor personalizado. Quando uma solicitação de entrada é enviada para o provedor personalizado, o provedor personalizado irá adicionar os `x-ms-customproviders-requestpath` cabeçalho para o pedido de saída para o `endpoint`.

exemplo `x-ms-customproviders-requestpath` cabeçalho para um recurso personalizado:

```
X-MS-CustomProviders-RequestPath: /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/{myResourceType}/{myResourceName}
```

Com base no exemplo acima `x-ms-customproviders-requestpath` cabeçalho, podemos criar partitionKey e rowKey para nosso armazenamento como o seguinte:

Parâmetro | Modelo | Descrição
---|---
partitionKey | '{subscriptionId}:{resourceGroupName}:{resourceProviderName}' | PartitionKey é a forma como os dados estão particionados. Na maioria dos casos, os dados devem ser particionados pela instância do fornecedor personalizado.
rowKey | '{myResourceType}:{myResourceName}' | O rowKey é o identificador individual para os dados. Na maioria das vezes, este é o nome do recurso.

Além disso, também precisamos criar uma nova classe para modelar o nosso recurso personalizado. Neste tutorial, iremos adicionar o `CustomResource` classe para nossa função, o que é uma classe genérica que aceita qualquer introduzido dados:

```csharp
// Custom Resource Table Entity
public class CustomResource : TableEntity
{
    public string Data { get; set; }
}
```

Esta ação cria uma classe básica com base em `TableEntity`, que é utilizado para armazenar dados. O `CustomResource` classe herda duas propriedades de `TableEntity`: partitionKey e rowKey.

## <a name="support-custom-provider-restful-methods"></a>Suporte a métodos RESTful do provedor personalizado

> [!NOTE]
> Se não estiver a copiar o código diretamente a partir do tutorial, o conteúdo de resposta deve ser um JSON válido e define a `Content-Type` cabeçalho como `application/json`.

Agora que temos a partição de configuração de dados, vamos estruturar os métodos CRUD e de Acionador básica para recursos personalizados e ações personalizadas. Uma vez que os fornecedores personalizados atuam como um proxy, a solicitação e resposta tem de ser modeladas e manipulados pelo RESTful `endpoint`. Siga o abaixo trechos de código para processar as operações básicas de RESTful:

### <a name="trigger-custom-action"></a>Acionar uma ação personalizada

Para fornecedores personalizados, uma ação personalizada é acionada através de `POST` pedidos. Uma ação personalizada, opcionalmente, pode aceitar um corpo de pedido contém um conjunto de parâmetros de entrada. A ação deve, em seguida, fazer uma cópia de devolver uma resposta signally o resultado da ação, bem como se foi concluída com êxito ou falha. Neste tutorial, iremos adicionar o método `TriggerCustomAction` para nossa função:

```csharp
/// <summary>
/// Triggers a custom action with some side effect.
/// </summary>
/// <param name="requestMessage">The http request message.</param>
/// <returns>The http response result of the custom action.</returns>
public static async Task<HttpResponseMessage> TriggerCustomAction(HttpRequestMessage requestMessage)
{
    var myCustomActionRequest = await requestMessage.Content.ReadAsStringAsync();

    var actionResponse = requestMessage.CreateResponse(HttpStatusCode.OK);
    actionResponse.Content = myCustomActionRequest != string.Empty ? 
        new StringContent(JObject.Parse(myCustomActionRequest).ToString(), System.Text.Encoding.UTF8, "application/json") :
        null;
    return actionResponse;
}
```

O `TriggerCustomAction` método aceita uma solicitação de entrada e simplesmente ecoa volta a resposta com um código de estado de êxito. 

### <a name="create-custom-resource"></a>Criar recursos personalizados

Para fornecedores personalizados, um recurso personalizado é criado através de `PUT` pedidos. O provedor personalizado aceitará um corpo de pedido do JSON, que contém um conjunto de propriedades para o recurso personalizado. No Azure, os recursos seguem um modelo de RESTful. O mesmo URL de pedido que foi utilizado para criar um recurso também deve ser capaz de obter e eliminar o recurso. Neste tutorial, iremos adicionar o método `CreateCustomResource` para criar novos recursos:

```csharp
/// <summary>
/// Creates a custom resource and saves it to table storage.
/// </summary>
/// <param name="requestMessage">The http request message.</param>
/// <param name="tableStorage">The Azure Storage Account table.</param>
/// <param name="azureResourceId">The parsed Azure resource Id.</param>
/// <param name="partitionKey">The partition key for storage. This is the custom provider id.</param>
/// <param name="rowKey">The row key for storage. This is '{resourceType}:{customResourceName}'.</param>
/// <returns>The http response containing the created custom resource.</returns>
public static async Task<HttpResponseMessage> CreateCustomResource(HttpRequestMessage requestMessage, CloudTable tableStorage, ResourceId azureResourceId, string partitionKey, string rowKey)
{
    // Adds the Azure top-level properties.
    var myCustomResource = JObject.Parse(await requestMessage.Content.ReadAsStringAsync());
    myCustomResource["name"] = azureResourceId.Name;
    myCustomResource["type"] = azureResourceId.FullResourceType;
    myCustomResource["id"] = azureResourceId.Id;

    // Save the resource into storage.
    var insertOperation = TableOperation.InsertOrReplace(
        new CustomResource
        {
            PartitionKey = partitionKey,
            RowKey = rowKey,
            Data = myCustomResource.ToString(),
        });
    await tableStorage.ExecuteAsync(insertOperation);

    var createResponse = requestMessage.CreateResponse(HttpStatusCode.OK);
    createResponse.Content = new StringContent(myCustomResource.ToString(), System.Text.Encoding.UTF8, "application/json");
    return createResponse;
}
```

O `CreateCustomResource` método atualiza a solicitação de entrada para incluir os campos específicos do Azure: `id`, `name`, e `type`. Estas são as propriedades de nível superior que são utilizadas por serviços em todo o Azure. Eles permitirá que o provedor personalizado para integrar com outros serviços como o Azure Policy, modelos do Azure Resource Manager e registos de atividades do Azure.

Propriedade | Sample | Descrição
---|---|---
name | '{myCustomResourceName}' | O nome do recurso personalizado.
type | 'Microsoft.CustomProviders/resourceProviders/{resourceTypeName}' | O espaço de nomes do tipo de recurso.
id | '/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/<br>providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/<br>{resourceTypeName}/{myCustomResourceName}' | O ID de recurso.

Além de adicionar as propriedades, podemos também salvar o documento para armazenamento de tabelas do Azure. 

### <a name="retrieve-custom-resource"></a>Obter o recurso personalizado

Para fornecedores personalizados, um recurso personalizado é recuperado por meio `GET` pedidos. O provedor personalizado será *não* aceitar um corpo de pedido do JSON. No caso da `GET` pedidos, o **ponto final** deve usar o `x-ms-customproviders-requestpath` cabeçalho para retornar o recurso já criado. Neste tutorial, iremos adicionar o método `RetrieveCustomResource` para recuperar os recursos existentes:

```csharp
/// <summary>
/// Retrieves a custom resource.
/// </summary>
/// <param name="requestMessage">The http request message.</param>
/// <param name="tableStorage">The Azure Storage Account table.</param>
/// <param name="partitionKey">The partition key for storage. This is the custom provider id.</param>
/// <param name="rowKey">The row key for storage. This is '{resourceType}:{customResourceName}'.</param>
/// <returns>The http response containing the existing custom resource.</returns>
public static async Task<HttpResponseMessage> RetrieveCustomResource(HttpRequestMessage requestMessage, CloudTable tableStorage, string partitionKey, string rowKey)
{
    // Attempt to retrieve the Existing Stored Value
    var tableQuery = TableOperation.Retrieve<CustomResource>(partitionKey, rowKey);
    var existingCustomResource = (CustomResource)(await tableStorage.ExecuteAsync(tableQuery)).Result;

    var retrieveResponse = requestMessage.CreateResponse(
        existingCustomResource != null ? HttpStatusCode.OK : HttpStatusCode.NotFound);

    retrieveResponse.Content = existingCustomResource != null ?
            new StringContent(existingCustomResource.Data, System.Text.Encoding.UTF8, "application/json"):
            null;
    return retrieveResponse;
}
```

No Azure, os recursos devem seguir um modelo de RESTful. O URL do pedido que criou o recurso deve também retornar o recurso se um `GET` pedido é efetuado.

### <a name="remove-custom-resource"></a>Remover recurso personalizado

Para fornecedores personalizados, um recurso personalizado será removido por meio de `DELETE` pedidos. O provedor personalizado será *não* aceitar um corpo de pedido do JSON. No caso da `DELETE` pedidos, o **ponto final** deve usar o `x-ms-customproviders-requestpath` cabeçalho para eliminar o recurso já criado. Neste tutorial, iremos adicionar o método `RemoveCustomResource` para remover recursos existentes:

```csharp
/// <summary>
/// Removes an existing custom resource.
/// </summary>
/// <param name="requestMessage">The http request message.</param>
/// <param name="tableStorage">The Azure Storage Account table.</param>
/// <param name="partitionKey">The partition key for storage. This is the custom provider id.</param>
/// <param name="rowKey">The row key for storage. This is '{resourceType}:{customResourceName}'.</param>
/// <returns>The http response containing the result of the delete.</returns>
public static async Task<HttpResponseMessage> RemoveCustomResource(HttpRequestMessage requestMessage, CloudTable tableStorage, string partitionKey, string rowKey)
{
    // Attempt to retrieve the Existing Stored Value
    var tableQuery = TableOperation.Retrieve<CustomResource>(partitionKey, rowKey);
    var existingCustomResource = (CustomResource)(await tableStorage.ExecuteAsync(tableQuery)).Result;

    if (existingCustomResource != null) {
        var deleteOperation = TableOperation.Delete(existingCustomResource);
        await tableStorage.ExecuteAsync(deleteOperation);
    }

    return requestMessage.CreateResponse(
        existingCustomResource != null ? HttpStatusCode.OK : HttpStatusCode.NoContent);
}
```

No Azure, os recursos devem seguir um modelo de RESTful. O URL do pedido que criou o recurso também deve eliminar o recurso se um `DELETE` pedido é efetuado.

### <a name="list-all-custom-resources"></a>Lista de todos os recursos personalizados

Para fornecedores personalizados, uma lista de recursos personalizados existentes pode ser enumerada por meio da coleção `GET` pedidos. O provedor personalizado será *não* aceitar um corpo de pedido do JSON. No caso de coleção `GET` pedidos, o `endpoint` deve usar o `x-ms-customproviders-requestpath` cabeçalho para enumerar os recursos já criados. Neste tutorial, iremos adicionar o método `EnumerateAllCustomResources` para enumerar os recursos existentes.

```csharp
/// <summary>
/// Enumerates all the stored custom resources for a given type.
/// </summary>
/// <param name="requestMessage">The http request message.</param>
/// <param name="tableStorage">The Azure Storage Account table.</param>
/// <param name="partitionKey">The partition key for storage. This is the custom provider id.</param>
/// <param name="resourceType">The resource type of the enumeration.</param>
/// <returns>The http response containing a list of resources stored under 'value'.</returns>
public static async Task<HttpResponseMessage> EnumerateAllCustomResources(HttpRequestMessage requestMessage, CloudTable tableStorage, string partitionKey, string resourceType)
{
    // Generate upper bound of the query.
    var rowKeyUpperBound = new StringBuilder(resourceType);
    rowKeyUpperBound[rowKeyUpperBound.Length - 1]++;

    // Create the enumeration query.
    var enumerationQuery = new TableQuery<CustomResource>().Where(
        TableQuery.CombineFilters(
            TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, partitionKey),
            TableOperators.And,
            TableQuery.CombineFilters(
                TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.GreaterThan, resourceType),
                TableOperators.And,
                TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.LessThan, rowKeyUpperBound.ToString()))));
    
    var customResources = (await tableStorage.ExecuteQuerySegmentedAsync(enumerationQuery, null))
        .ToList().Select(customResource => JToken.Parse(customResource.Data));

    var enumerationResponse = requestMessage.CreateResponse(HttpStatusCode.OK);
    enumerationResponse.Content = new StringContent(new JObject(new JProperty("value", customResources)).ToString(), System.Text.Encoding.UTF8, "application/json");
    return enumerationResponse;
}
```

> [!NOTE]
> A chave de linha maior ou menor que é a sintaxe de tabelas do Azure para executar uma consulta "startswith" para cadeias de caracteres. 

Para listar todos os recursos existentes, iremos gerar uma consulta de tabela do Azure que garante que os recursos existem em nossa partição de fornecedor personalizado. A consulta, em seguida, verifica se a chave de linha começa com o mesmo `{myResourceType}`.

## <a name="integrate-restful-operations"></a>Integrar o RESTful operations

Depois de todos os métodos RESTful são adicionados à função, podemos atualizar principal `Run` pedidos de método para chamar as funções para manipular o RESTANTE diferente:

```csharp
/// <summary>
/// Entry point for the Azure Function webhook and acts as the service behind a custom provider.
/// </summary>
/// <param name="requestMessage">The http request message.</param>
/// <param name="log">The logger.</param>
/// <param name="tableStorage">The Azure Storage Account table.</param>
/// <returns>The http response for the custom Azure API.</returns>
public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, ILogger log, CloudTable tableStorage)
{
    // Get the unique Azure request path from request headers.
    var requestPath = req.Headers.GetValues("x-ms-customproviders-requestpath").FirstOrDefault();

    if (requestPath == null)
    {
        var missingHeaderResponse = req.CreateResponse(HttpStatusCode.BadRequest);
        missingHeaderResponse.Content = new StringContent(
            new JObject(new JProperty("error", "missing 'x-ms-customproviders-requestpath' header")).ToString(),
            System.Text.Encoding.UTF8, 
            "application/json");
    }

    log.LogInformation($"The Custom Provider Function received a request '{req.Method}' for resource '{requestPath}'.");

    // Determines if it is a collection level call or action.
    var isResourceRequest = requestPath.Split('/').Length % 2 == 1;
    var azureResourceId = isResourceRequest ? 
        ResourceId.FromString(requestPath) :
        ResourceId.FromString($"{requestPath}/");

    // Create the Partition Key and Row Key
    var partitionKey = $"{azureResourceId.SubscriptionId}:{azureResourceId.ResourceGroupName}:{azureResourceId.Parent.Name}";
    var rowKey = $"{azureResourceId.FullResourceType.Replace('/', ':')}:{azureResourceId.Name}";

    switch (req.Method)
    {
        // Action request for an custom action.
        case HttpMethod m when m == HttpMethod.Post && !isResourceRequest:
            return await TriggerCustomAction(
                requestMessage: req);

        // Enumerate request for all custom resources.
        case HttpMethod m when m == HttpMethod.Get && !isResourceRequest:
            return await EnumerateAllCustomResources(
                requestMessage: req,
                tableStorage: tableStorage,
                partitionKey: partitionKey,
                resourceType: rowKey);

        // Retrieve request for a custom resource.
        case HttpMethod m when m == HttpMethod.Get && isResourceRequest:
            return await RetrieveCustomResource(
                requestMessage: req,
                tableStorage: tableStorage,
                partitionKey: partitionKey,
                rowKey: rowKey);

        // Create request for a custom resource.
        case HttpMethod m when m == HttpMethod.Put && isResourceRequest:
            return await CreateCustomResource(
                requestMessage: req,
                tableStorage: tableStorage,
                azureResourceId: azureResourceId,
                partitionKey: partitionKey,
                rowKey: rowKey);

        // Remove request for a custom resource.
        case HttpMethod m when m == HttpMethod.Delete && isResourceRequest:
            return await RemoveCustomResource(
                requestMessage: req,
                tableStorage: tableStorage,
                partitionKey: partitionKey,
                rowKey: rowKey);

        // Invalid request received.
        default:
            return req.CreateResponse(HttpStatusCode.BadRequest);
    }
}
``` 

A atualização `Run` método agora irá incluir o `tableStorage` enlace que foi adicionada para o armazenamento de tabelas do Azure de entrada. A primeira parte do método agora irá ler a `x-ms-customproviders-requestpath` cabeçalho e utilize o `Microsoft.Azure.Management.ResourceManager.Fluent` biblioteca para analisar o valor como um ID de recurso. O `x-ms-customproviders-requestpath` cabeçalho é enviado pelo fornecedor personalizado e designa o caminho do pedido a receber. Com o ID de recurso analisado, podemos agora gerar partitionKey e rowKey para os dados pesquisar ou armazenar recursos personalizados.

Além de adicionar as classes e métodos, é necessário atualizar os com métodos para a função. Adicione o seguinte na parte superior do ficheiro:

```csharp
#r "Newtonsoft.Json"
#r "Microsoft.WindowsAzure.Storage"
#r "../bin/Microsoft.Azure.Management.ResourceManager.Fluent.dll"

using System;
using System.Net;
using System.Net.Http;
using System.Net.Http.Headers;
using System.Configuration;
using System.Text;
using System.Threading;
using System.Globalization;
using System.Collections.Generic;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.WindowsAzure.Storage.Table;
using Microsoft.Azure.Management.ResourceManager.Fluent.Core;
using Newtonsoft.Json;
using Newtonsoft.Json.Linq;
```

Se se perdeu durante qualquer ponto deste tutorial, o exemplo de código de conclusão pode ser encontrado no [provedor personalizado C# referência de ponto de extremidade RESTful](./reference-custom-providers-csharp-endpoint.md). Assim que a função estiver concluída, guarde o URL da função que pode ser utilizado para acionar a função que será utilizada em tutoriais posteriores.

## <a name="next-steps"></a>Passos Seguintes

Neste artigo, é criado um ponto de extremidade RESTful para trabalhar com o fornecedor personalizado do Azure `endpoint`. Vá para o artigo seguinte para saber como criar um provedor personalizado.

- [Tutorial: Criar um provedor personalizado](./tutorial-custom-providers-create.md)
