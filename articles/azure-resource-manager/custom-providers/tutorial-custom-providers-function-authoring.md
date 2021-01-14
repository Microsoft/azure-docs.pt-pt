---
title: Criar um ponto final RESTful
description: Este tutorial mostra como autorizar um ponto final RESTful para fornecedores personalizados. Detalha como lidar com pedidos e respostas para os métodos RESTful HTTP suportados.
author: jjbfour
ms.topic: tutorial
ms.date: 01/13/2021
ms.author: jobreen
ms.openlocfilehash: 54d0df287865d5d92403bf68227a2d4c5faa8bb4
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/14/2021
ms.locfileid: "98200214"
---
# <a name="author-a-restful-endpoint-for-custom-providers"></a>Autor de um ponto final RESTful para fornecedores personalizados

Um fornecedor personalizado é um contrato entre a Azure e um ponto final. Com fornecedores personalizados, você pode personalizar fluxos de trabalho em Azure. Este tutorial mostra como autorizar um fornecedor personalizado RESTful endpoint. Se não estiver familiarizado com os Fornecedores Personalizados Azure, consulte [a visão geral dos fornecedores de recursos personalizados.](overview.md)

> [!NOTE]
> Este tutorial baseia-se no tutorial [Configurar Funções Azure para Fornecedores Personalizados Azure.](./tutorial-custom-providers-function-setup.md) Alguns dos passos deste trabalho tutorial só se tiver sido criada uma aplicação de função Azure para trabalhar com fornecedores personalizados.

## <a name="work-with-custom-actions-and-custom-resources"></a>Trabalhar com ações personalizadas e recursos personalizados

Neste tutorial, atualiza a app de função para funcionar como um ponto final RESTful para o seu fornecedor personalizado. Os recursos e ações em Azure são modelados após a seguinte especificação restful básica:

- **PUT**: Criar um novo recurso
- **GET (exemplo)**: Recuperar um recurso existente
- **DELETE:** Remover um recurso existente
- **POST**: Desencadear uma ação
- **GET (coleção)**: Listar todos os recursos existentes

 Para este tutorial, utilize o armazenamento da Mesa Azure. Mas qualquer base de dados ou serviço de armazenamento pode funcionar.

## <a name="partition-custom-resources-in-storage"></a>Recursos personalizados de partição no armazenamento

Como está a criar um serviço RESTful, precisa de armazenar os recursos criados. Para o armazenamento da tabela Azure, você precisa gerar chaves de divisória e linha para os seus dados. Para os fornecedores personalizados, os dados devem ser divididos ao fornecedor personalizado. Quando um pedido de entrada é enviado ao fornecedor personalizado, o fornecedor personalizado adiciona o `x-ms-customproviders-requestpath` cabeçalho aos pedidos de saída ao ponto final.

O exemplo a seguir mostra um `x-ms-customproviders-requestpath` cabeçalho para um recurso personalizado:

```
X-MS-CustomProviders-RequestPath: /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/{myResourceType}/{myResourceName}
```

Com base no `x-ms-customproviders-requestpath` cabeçalho do exemplo, pode criar os parâmetros *partitionKey* e *rowKey* para o seu armazenamento, tal como mostrado na tabela seguinte:

Parâmetro | Modelo | Descrição
---|---|---
*partitionKey* | `{subscriptionId}:{resourceGroupName}:{resourceProviderName}` | O parâmetro *partitionKey* especifica como os dados são divididos. Normalmente, os dados são divididos pela instância do fornecedor personalizado.
*rowKey* | `{myResourceType}:{myResourceName}` | O parâmetro *rowKey* especifica o identificador individual para os dados. Normalmente, o identificador é o nome do recurso.

Também precisa de criar uma nova classe para modelar o seu recurso personalizado. Neste tutorial, adicione a seguinte classe **CustomResource** à sua aplicação de função:

```csharp
// Custom Resource Table Entity
public class CustomResource : TableEntity
{
    public string Data { get; set; }
}
```
**CustomResource** é uma classe simples e genérica que aceita quaisquer dados de entrada. Baseia-se na **TableEntity,** que é usada para armazenar dados. A classe **CustomResource** herda duas propriedades da **TableEntity**: **partitionKey** e **rowKey**.

## <a name="support-custom-provider-restful-methods"></a>Apoie métodos RESTful do fornecedor personalizado

> [!NOTE]
> Se não estiver a copiar o código diretamente deste tutorial, o conteúdo de resposta deve ser válido JSON que define o `Content-Type` cabeçalho para `application/json` .

Agora que criou a partilha de dados, crie o CRUD básico e desencadeie métodos para recursos personalizados e ações personalizadas. Como os fornecedores personalizados atuam como proxies, o ponto final RESTful deve modelar e lidar com o pedido e a resposta. Os seguintes fragmentos de código mostram como lidar com as operações restful básicas.

### <a name="trigger-a-custom-action"></a>Desencadear uma ação personalizada

Para fornecedores personalizados, uma ação personalizada é desencadeada através de pedidos POST. Uma ação personalizada pode opcionalmente aceitar um corpo de pedido que contenha um conjunto de parâmetros de entrada. A ação devolve então uma resposta que assinala o resultado da ação e se foi bem sucedida ou falhada.

Adicione o seguinte método **TriggerCustomAction** à sua aplicação de função:

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

O método **TriggerCustomAction** aceita um pedido de entrada e simplesmente ecoa a resposta com um código de estado.

### <a name="create-a-custom-resource"></a>Criar um recurso personalizado

Para fornecedores personalizados, um recurso personalizado é criado através de pedidos PUT. O fornecedor personalizado aceita um corpo de pedido JSON, que contém um conjunto de propriedades para o recurso personalizado. Os recursos em Azure seguem um modelo RESTful. Pode utilizar o mesmo URL de pedido para criar, recuperar ou eliminar um recurso.

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

O método **CreateCustomResource** atualiza o pedido de entrada para incluir o **id** de campos específicos do Azure, **nome** e **tipo**. Estes campos são propriedades de alto nível usadas por serviços em Azure. Eles permitem que o fornecedor personalizado interopera com outros serviços como Azure Policy, Azure Resource Manager Modelos e Azure Activity Log.

Propriedade | Exemplo | Descrição
---|---|---
**nome** | {myCustomResourceName} | O nome do recurso personalizado
**tipo** | Microsoft.CustomProviders/resourceProviders/{resourceTypeName} | O espaço de nome do tipo de recurso
**id** | /subscrições/{subscriçãoId}/resourceGroups/{resourceGroupName}/<br>fornecedores/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/<br>{recursoTypeName}/{myCustomResourceName} | O ID do recurso

Além de adicionar as propriedades, também guardou o documento JSON para o armazenamento da Mesa Azure.

### <a name="retrieve-a-custom-resource"></a>Recuperar um recurso personalizado

Para fornecedores personalizados, um recurso personalizado é recuperado através de pedidos GET. Um fornecedor personalizado *não* aceita um órgão de pedido JSON. Para pedidos GET, o ponto final utiliza o `x-ms-customproviders-requestpath` cabeçalho para devolver o recurso já criado.

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

Em Azure, os recursos seguem um modelo RESTful. O URL de pedido que cria um recurso também devolve o recurso se for realizado um pedido GET.

### <a name="remove-a-custom-resource"></a>Remover um recurso personalizado

Para os fornecedores personalizados, um recurso personalizado é removido através de pedidos DELETE. Um fornecedor personalizado *não* aceita um órgão de pedido JSON. Para um pedido de DELETE, o ponto final utiliza o `x-ms-customproviders-requestpath` cabeçalho para eliminar o recurso já criado.

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

Em Azure, os recursos seguem um modelo RESTful. O URL de pedido que cria um recurso também elimina o recurso se for realizado um pedido de DELETE.

### <a name="list-all-custom-resources"></a>Listar todos os recursos personalizados

Para fornecedores personalizados, pode enumerar uma lista de recursos personalizados existentes utilizando pedidos GET de recolha. Um fornecedor personalizado *não* aceita um órgão de pedido JSON. Para uma recolha de pedidos GET, o ponto final usa o `x-ms-customproviders-requestpath` cabeçalho para enumerar os recursos já criados.

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
> A sintaxe de armazenamento de tabela de tabela RowKey.GreaterThan e QueryComparisons.LessThan é a sintaxe de armazenamento da tabela Azure para executar uma consulta "startwith" para cordas.

Para listar todos os recursos existentes, gere uma consulta de armazenamento da Tabela Azure que garante que os recursos existem sob a sua partição personalizada do fornecedor. A consulta verifica então se a tecla da linha começa com o mesmo `{myResourceType}` valor.

## <a name="integrate-restful-operations"></a>Integrar operações RESTful

Depois de todos os métodos RESTful serem adicionados à aplicação de função, atualize o método de **Execução** principal que chama as funções para lidar com os diferentes pedidos DE REST:

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

O método **de execução** atualizado inclui agora a ligação de entrada *de armazenamento de quadros* que adicionou para o armazenamento da tabela Azure. A primeira parte do método lê o `x-ms-customproviders-requestpath` cabeçalho e utiliza a `Microsoft.Azure.Management.ResourceManager.Fluent` biblioteca para analisar o valor como iD de recurso. O `x-ms-customproviders-requestpath` cabeçalho é enviado pelo fornecedor personalizado e especifica o caminho do pedido de entrada.

Ao utilizar o ID de recursos analisado, pode gerar os valores **de partiçãoKey** e **rowKey** para que os dados procurem ou armazenem recursos personalizados.

Depois de adicionar os métodos e as aulas, precisa atualizar os métodos **de utilização** para a aplicação de função. Adicione o seguinte código à parte superior do ficheiro C#:

```csharp
#r "Newtonsoft.Json"
#r "Microsoft.WindowsAzure.Storage"
#r "../bin/Microsoft.Azure.Management.ResourceManager.Fluent"

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

Se se perder em qualquer ponto deste tutorial, pode encontrar a amostra de código completa na referência do [ponto final restful do fornecedor personalizado C# RESTful](./reference-custom-providers-csharp-endpoint.md). Depois de terminar a aplicação de função, guarde o URL da aplicação de função. Pode ser usado para desencadear a aplicação de função em tutoriais posteriores.

## <a name="next-steps"></a>Passos seguintes

Neste artigo, foi autor de um ponto final RESTful para trabalhar com um ponto final do Azure Custom Provider. Para aprender a criar um fornecedor personalizado, vá ao artigo [Tutorial: Criar um fornecedor personalizado.](./tutorial-custom-providers-create.md)
