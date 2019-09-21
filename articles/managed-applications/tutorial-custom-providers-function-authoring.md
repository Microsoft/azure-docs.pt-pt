---
title: Criar um ponto de extremidade RESTful para provedores personalizados
description: Este tutorial mostra como criar um ponto de extremidade RESTful para provedores personalizados. Ele detalha como lidar com solicitações e respostas para os métodos HTTP RESTful com suporte.
author: jjbfour
ms.service: managed-applications
ms.topic: tutorial
ms.date: 06/19/2019
ms.author: jobreen
ms.openlocfilehash: ae821f07034b038f49a400de8c00e4ace6787192
ms.sourcegitcommit: f2771ec28b7d2d937eef81223980da8ea1a6a531
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/20/2019
ms.locfileid: "71172898"
---
# <a name="author-a-restful-endpoint-for-custom-providers"></a>Criar um ponto de extremidade RESTful para provedores personalizados

Um provedor personalizado é um contrato entre o Azure e um ponto de extremidade. Com provedores personalizados, você pode personalizar fluxos de trabalho no Azure. Este tutorial mostra como criar um ponto de extremidade RESTful de provedor personalizado. Se você não estiver familiarizado com os provedores personalizados do Azure, consulte [a visão geral sobre provedores de recursos personalizados](./custom-providers-overview.md).

> [!NOTE]
> Este tutorial se baseia no tutorial [configurado Azure Functions para provedores personalizados do Azure](./tutorial-custom-providers-function-setup.md). Algumas das etapas neste tutorial só funcionarão se um aplicativo de funções do Azure tiver sido configurado para trabalhar com provedores personalizados.

## <a name="work-with-custom-actions-and-custom-resources"></a>Trabalhar com ações personalizadas e recursos personalizados

Neste tutorial, você atualizará o aplicativo de funções para trabalhar como um ponto de extremidade RESTful para seu provedor personalizado. Os recursos e as ações no Azure são modelados após a seguinte especificação RESTful básica:

- **PUT**: Criar um novo recurso
- **Get (instância)** : Recuperar um recurso existente
- **EXCLUIR**: Remover um recurso existente
- **POST**: Disparar uma ação
- **Get (coleção)** : Listar todos os recursos existentes

 Para este tutorial, você usa o armazenamento de tabelas do Azure. Mas qualquer serviço de armazenamento ou banco de dados pode funcionar.

## <a name="partition-custom-resources-in-storage"></a>Particionar recursos personalizados no armazenamento

Como você está criando um serviço RESTful, você precisa armazenar os recursos criados. Para o armazenamento de tabelas do Azure, você precisa gerar chaves de partição e de linha para seus dados. Para provedores personalizados, os dados devem ser particionados no provedor personalizado. Quando uma solicitação de entrada é enviada para o provedor personalizado, o provedor personalizado adiciona `x-ms-customproviders-requestpath` o cabeçalho às solicitações de saída para o ponto de extremidade.

O exemplo a seguir mostra `x-ms-customproviders-requestpath` um cabeçalho para um recurso personalizado:

```
X-MS-CustomProviders-RequestPath: /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/{myResourceType}/{myResourceName}
```

Com base no cabeçalho do `x-ms-customproviders-requestpath` exemplo, você pode criar os parâmetros *partitionKey* e *rowKey* para o armazenamento, conforme mostrado na tabela a seguir:

Parâmetro | Modelo | Descrição
---|---|---
*partitionKey* | `{subscriptionId}:{resourceGroupName}:{resourceProviderName}` | O parâmetro *partitionKey* especifica como os dados são particionados. Normalmente, os dados são particionados pela instância do provedor personalizado.
*rowKey* | `{myResourceType}:{myResourceName}` | O parâmetro *rowKey* especifica o identificador individual para os dados. Geralmente, o identificador é o nome do recurso.

Você também precisa criar uma nova classe para modelar seu recurso personalizado. Neste tutorial, você adiciona a seguinte classe **CustomResource** ao seu aplicativo de funções:

```csharp
// Custom Resource Table Entity
public class CustomResource : TableEntity
{
    public string Data { get; set; }
}
```
**CustomResource** é uma classe genérica e simples que aceita quaisquer dados de entrada. Ele é baseado em **TableEntity**, que é usado para armazenar dados. A classe **CustomResource** herda duas propriedades de **TableEntity**: **partitionKey** e **rowKey**.

## <a name="support-custom-provider-restful-methods"></a>Suporte a métodos RESTful de provedor personalizado

> [!NOTE]
> Se você não estiver copiando o código diretamente deste tutorial, o conteúdo da resposta deverá ser um JSON válido `Content-Type` que define `application/json`o cabeçalho como.

Agora que você configurou o particionamento de dados, crie os métodos CRUD e gatilho básicos para recursos personalizados e ações personalizadas. Como os provedores personalizados atuam como proxies, o ponto de extremidade RESTful deve modelar e manipular a solicitação e a resposta. Os trechos de código a seguir mostram como lidar com as operações RESTful básicas.

### <a name="trigger-a-custom-action"></a>Disparar uma ação personalizada

Para provedores personalizados, uma ação personalizada é disparada por meio de solicitações POST. Uma ação personalizada pode, opcionalmente, aceitar um corpo de solicitação que contém um conjunto de parâmetros de entrada. Em seguida, a ação retorna uma resposta que sinaliza o resultado da ação e se ela teve êxito ou falhou.

Adicione o seguinte método **TriggerCustomAction** ao seu aplicativo de funções:

```csharp
/// <summary>
/// Triggers a custom action with some side effects.
/// </summary>
/// <param name="requestMessage">The HTTP request message.</param>
/// <returns>The HTTP response result of the custom action.</returns>
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

O método **TriggerCustomAction** aceita uma solicitação de entrada e simplesmente ecoa de volta a resposta com um código de status.

### <a name="create-a-custom-resource"></a>Criar um recurso personalizado

Para provedores personalizados, um recurso personalizado é criado por meio de solicitações PUT. O provedor personalizado aceita um corpo de solicitação JSON, que contém um conjunto de propriedades para o recurso personalizado. Os recursos no Azure seguem um modelo RESTful. Você pode usar a mesma URL de solicitação para criar, recuperar ou excluir um recurso.

Adicione o seguinte método **CreateCustomResource** para criar novos recursos:

```csharp
/// <summary>
/// Creates a custom resource and saves it to table storage.
/// </summary>
/// <param name="requestMessage">The HTTP request message.</param>
/// <param name="tableStorage">The Azure Table storage account.</param>
/// <param name="azureResourceId">The parsed Azure resource ID.</param>
/// <param name="partitionKey">The partition key for storage. This is the custom provider ID.</param>
/// <param name="rowKey">The row key for storage. This is '{resourceType}:{customResourceName}'.</param>
/// <returns>The HTTP response containing the created custom resource.</returns>
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

O método **CreateCustomResource** atualiza a solicitação de entrada para incluir os campos específicos do Azure **ID**, **nome**e **tipo**. Esses campos são propriedades de nível superior usadas pelos serviços no Azure. Eles permitem que o provedor personalizado interaja com outros serviços, como Azure Policy, modelos de Azure Resource Manager e log de atividades do Azure.

Propriedade | Exemplo | Descrição
---|---|---
**name** | {myCustomResourceName} | O nome do recurso personalizado
**type** | Microsoft. CustomProviders/resourceProviders/{resourceTypename} | O namespace do tipo de recurso
**id** | /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/<br>providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/<br>{resourceTypename}/{myCustomResourceName} | A ID do recurso

Além de adicionar as propriedades, você também salvou o documento JSON no armazenamento de tabelas do Azure.

### <a name="retrieve-a-custom-resource"></a>Recuperar um recurso personalizado

Para provedores personalizados, um recurso personalizado é recuperado por meio de solicitações GET. Um provedor personalizado *não* aceita um corpo de solicitação JSON. Para solicitações GET, o ponto de extremidade `x-ms-customproviders-requestpath` usa o cabeçalho para retornar o recurso já criado.

Adicione o seguinte método **RetrieveCustomResource** para recuperar os recursos existentes:

```csharp
/// <summary>
/// Retrieves a custom resource.
/// </summary>
/// <param name="requestMessage">The HTTP request message.</param>
/// <param name="tableStorage">The Azure Table storage account.</param>
/// <param name="partitionKey">The partition key for storage. This is the custom provider ID.</param>
/// <param name="rowKey">The row key for storage. This is '{resourceType}:{customResourceName}'.</param>
/// <returns>The HTTP response containing the existing custom resource.</returns>
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

No Azure, os recursos seguem um modelo RESTful. A URL de solicitação que cria um recurso também retornará o recurso se uma solicitação GET for executada.

### <a name="remove-a-custom-resource"></a>Remover um recurso personalizado

Para provedores personalizados, um recurso personalizado é removido por meio de solicitações de exclusão. Um provedor personalizado *não* aceita um corpo de solicitação JSON. Para uma solicitação de exclusão, o ponto de `x-ms-customproviders-requestpath` extremidade usa o cabeçalho para excluir o recurso já criado.

Adicione o seguinte método **RemoveCustomResource** para remover os recursos existentes:

```csharp
/// <summary>
/// Removes an existing custom resource.
/// </summary>
/// <param name="requestMessage">The HTTP request message.</param>
/// <param name="tableStorage">The Azure storage account table.</param>
/// <param name="partitionKey">The partition key for storage. This is the custom provider ID.</param>
/// <param name="rowKey">The row key for storage. This is '{resourceType}:{customResourceName}'.</param>
/// <returns>The HTTP response containing the result of the deletion.</returns>
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

No Azure, os recursos seguem um modelo RESTful. A URL de solicitação que cria um recurso também excluirá o recurso se uma solicitação de exclusão for executada.

### <a name="list-all-custom-resources"></a>Listar todos os recursos personalizados

Para provedores personalizados, você pode enumerar uma lista de recursos personalizados existentes usando solicitações GET de coleção. Um provedor personalizado *não* aceita um corpo de solicitação JSON. Para uma coleção de solicitações GET, o ponto de extremidade `x-ms-customproviders-requestpath` usa o cabeçalho para enumerar os recursos já criados.

Adicione o seguinte método **EnumerateAllCustomResources** para enumerar os recursos existentes:

```csharp
/// <summary>
/// Enumerates all the stored custom resources for a given type.
/// </summary>
/// <param name="requestMessage">The HTTP request message.</param>
/// <param name="tableStorage">The Azure Table storage account.</param>
/// <param name="partitionKey">The partition key for storage. This is the custom provider ID.</param>
/// <param name="resourceType">The resource type of the enumeration.</param>
/// <returns>The HTTP response containing a list of resources stored under 'value'.</returns>
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
> RowKey QueryComparisons. GreaterThan e QueryComparisons. LessThan é a sintaxe de armazenamento de tabela do Azure para executar uma consulta "StartsWith" para cadeias de caracteres.

Para listar todos os recursos existentes, gere uma consulta de armazenamento de tabela do Azure que garanta que os recursos existam em sua partição de provedor personalizado. Em seguida, a consulta verifica se a chave de linha começa `{myResourceType}` com o mesmo valor.

## <a name="integrate-restful-operations"></a>Integrar operações RESTful

Depois que todos os métodos RESTful forem adicionados ao aplicativo de funções, atualize o método de **execução** principal que chama as funções para lidar com as diferentes solicitações REST:

```csharp
/// <summary>
/// Entry point for the Azure function app webhook that acts as the service behind a custom provider.
/// </summary>
/// <param name="requestMessage">The HTTP request message.</param>
/// <param name="log">The logger.</param>
/// <param name="tableStorage">The Azure Table storage account.</param>
/// <returns>The HTTP response for the custom Azure API.</returns>
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
        // Action request for a custom action.
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

O método de **execução** atualizado agora inclui a associação de entrada *tableStorage* que você adicionou para o armazenamento de tabelas do Azure. A primeira parte do método lê o `x-ms-customproviders-requestpath` cabeçalho e usa a `Microsoft.Azure.Management.ResourceManager.Fluent` biblioteca para analisar o valor como uma ID de recurso. O `x-ms-customproviders-requestpath` cabeçalho é enviado pelo provedor personalizado e especifica o caminho da solicitação de entrada.

Usando a ID do recurso analisado, você pode gerar os valores de **partitionKey** e **rowKey** para os dados a serem pesquisados ou para armazenar recursos personalizados.

Depois de adicionar os métodos e as classes, você precisa atualizar os métodos **usando** para o aplicativo de funções. Adicione o seguinte código à parte superior do C# arquivo:

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

Se você for perdido em qualquer ponto deste tutorial, poderá encontrar o exemplo de código completo na referência do [ponto de C# extremidade RESTful do provedor personalizado](./reference-custom-providers-csharp-endpoint.md). Depois de concluir o aplicativo de funções, salve a URL do aplicativo de funções. Ele pode ser usado para disparar o aplicativo de funções em Tutoriais posteriores.

## <a name="next-steps"></a>Passos seguintes

Neste artigo, você criou um ponto de extremidade RESTful para trabalhar com um ponto de extremidade do provedor personalizado do Azure. Para saber como criar um provedor personalizado, vá para o tutorial do [artigo: Criando um provedor](./tutorial-custom-providers-create.md)personalizado.
