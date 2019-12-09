---
title: Associações de aplicativos móveis para Azure Functions
description: Entenda como usar as associações de aplicativos móveis do Azure no Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 11/21/2017
ms.author: cshoe
ms.openlocfilehash: 051b8780a102a8a1ec4d3979cc53b686e2823dc8
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/08/2019
ms.locfileid: "74928600"
---
# <a name="mobile-apps-bindings-for-azure-functions"></a>Associações de aplicativos móveis para Azure Functions 

> [!NOTE]
> As associações de aplicativos móveis do Azure só estão disponíveis para Azure Functions 1. x. Eles não têm suporte no Azure Functions 2. x e superior.

Este artigo explica como trabalhar com associações de [aplicativos móveis do Azure](../app-service-mobile/app-service-mobile-value-prop.md) no Azure functions. O Azure Functions dá suporte a associações de entrada e saída para aplicativos móveis.

As associações de aplicativos móveis permitem que você leia e atualize tabelas de dados em aplicativos móveis.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>Pacotes - funções 1.x

As associações de aplicativos móveis são fornecidas no pacote NuGet [Microsoft. Azure. webjobs. Extensions. MobileApps](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.MobileApps) , versão 1. x. O código-fonte do pacote está no repositório GitHub [Azure-webjobs-SDK-Extensions](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions.MobileApps/) .

[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="input"></a>Input

A associação de entrada dos aplicativos móveis carrega um registro de um ponto de extremidade de tabela móvel e o transmite para sua função. Em C# funções F# e, todas as alterações feitas no registro são enviadas automaticamente de volta para a tabela quando a função é encerrada com êxito.

## <a name="input---example"></a>Entrada-exemplo

Veja o exemplo de idioma específico:

* [Script do c# (.csx)](#input---c-script-example)
* JavaScript

### <a name="input---c-script-example"></a>Exemplo de C# script de entrada

O exemplo a seguir mostra uma associação de entrada de aplicativos móveis em um arquivo *Function. JSON* e uma [ C# função de script](functions-reference-csharp.md) que usa a associação. A função é disparada por uma mensagem da fila que tem um identificador de registro. A função lê o registro especificado e modifica sua propriedade `Text`.

Eis a vinculação de dados a *Function* ficheiro:

```json
{
"bindings": [
    {
    "name": "myQueueItem",
    "queueName": "myqueue-items",
    "connection":"",
    "type": "queueTrigger",
    "direction": "in"
    },
    {
        "name": "record",
        "type": "mobileTable",
        "tableName": "MyTable",
        "id" : "{queueTrigger}",
        "connection": "My_MobileApp_Url",
        "apiKey": "My_MobileApp_Key",
        "direction": "in"
    }
]
}
```
O [configuração](#input---configuration) seção explica essas propriedades.

Aqui está o código de script do c#:

```cs
#r "Newtonsoft.Json"    
using Newtonsoft.Json.Linq;

public static void Run(string myQueueItem, JObject record)
{
    if (record != null)
    {
        record["Text"] = "This has changed.";
    }    
}
```

### <a name="input---javascript"></a>Entrada-JavaScript

O exemplo a seguir mostra uma associação de entrada de aplicativos móveis em um arquivo *Function. JSON* e uma [função JavaScript](functions-reference-node.md) que usa a associação. A função é disparada por uma mensagem da fila que tem um identificador de registro. A função lê o registro especificado e modifica sua propriedade `Text`.

Eis a vinculação de dados a *Function* ficheiro:

```json
{
"bindings": [
    {
    "name": "myQueueItem",
    "queueName": "myqueue-items",
    "connection":"",
    "type": "queueTrigger",
    "direction": "in"
    },
    {
        "name": "record",
        "type": "mobileTable",
        "tableName": "MyTable",
        "id" : "{queueTrigger}",
        "connection": "My_MobileApp_Url",
        "apiKey": "My_MobileApp_Key",
        "direction": "in"
    }
]
}
```
O [configuração](#input---configuration) seção explica essas propriedades.

Eis o código JavaScript:

```javascript
module.exports = function (context, myQueueItem) {    
    context.log(context.bindings.record);
    context.done();
};
```

## <a name="input---attributes"></a>Introdução - atributos

Em [ C# bibliotecas de classes](functions-dotnet-class-library.md), use o atributo [mobiletable](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.MobileApps/MobileTableAttribute.cs) .

Para obter informações sobre as propriedades de atributo que você pode configurar, consulte [a seção de configuração a seguir](#input---configuration).

## <a name="input---configuration"></a>Entrada - configuração

A tabela seguinte explica as propriedades de configuração de ligação definida no *Function* ficheiro e o `MobileTable` atributo.

|propriedade de Function | Propriedade de atributo |Descrição|
|---------|---------|----------------------|
| **tipo**|| Deve ser definido como "mobiletable"|
| **direção**||Deve ser definido como "in"|
| **name**|| Nome do parâmetro de entrada na assinatura de função.|
|**tableName** |**TableName**|Nome da tabela de dados do aplicativo móvel|
| **id**| **Id** | O identificador do registro a ser recuperado. Pode ser estático ou baseado no gatilho que invoca a função. Por exemplo, se você usar um gatilho de fila para sua função, `"id": "{queueTrigger}"` usará o valor de cadeia de caracteres da mensagem da fila como a ID de registro a ser recuperada.|
|**ligação**|**ligação**|O nome de uma configuração de aplicativo que tem a URL do aplicativo móvel. A função usa essa URL para construir as operações REST necessárias em seu aplicativo móvel. Crie uma configuração de aplicativo em seu aplicativo de funções que contenha a URL do aplicativo móvel e, em seguida, especifique o nome da configuração do aplicativo na propriedade `connection` em sua associação de entrada. A URL é semelhante a `http://<appname>.azurewebsites.net`.
|**apiKey**|**ApiKey**|O nome de uma configuração de aplicativo que tem a chave de API do aplicativo móvel. Forneça a chave de API se você [implementar uma chave de API em seu aplicativo móvel node. js](https://github.com/Azure/azure-mobile-apps-node/tree/master/samples/api-key)ou [implementar uma chave de API em seu aplicativo móvel .net](https://github.com/Azure/azure-mobile-apps-net-server/wiki/Implementing-Application-Key). Para fornecer a chave, crie uma configuração de aplicativo em seu aplicativo de funções que contém a chave de API e, em seguida, adicione a propriedade `apiKey` em sua associação de entrada com o nome da configuração do aplicativo. |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

> [!IMPORTANT]
> Não compartilhe a chave de API com seus clientes de aplicativos móveis. Ele só deve ser distribuído com segurança para clientes do lado do serviço, como Azure Functions. Azure Functions armazena as informações de conexão e as chaves de API como configurações de aplicativo para que elas não sejam verificadas em seu repositório de controle do código-fonte. Isso protege suas informações confidenciais.

## <a name="input---usage"></a>Introdução - utilização

Em C# funções, quando o registro com a ID especificada é encontrado, ele é passado para o parâmetro [JObject](https://www.newtonsoft.com/json/help/html/t_newtonsoft_json_linq_jobject.htm) nomeado. Quando o registro não é encontrado, o valor do parâmetro é `null`. 

Em funções de JavaScript, o registro é passado para o objeto `context.bindings.<name>`. Quando o registro não é encontrado, o valor do parâmetro é `null`. 

Em C# funções F# e, todas as alterações feitas no registro de entrada (parâmetro de entrada) são enviadas automaticamente de volta para a tabela quando a função é encerrada com êxito. Não é possível modificar um registro em funções JavaScript.

## <a name="output"></a>Saída

Use a associação de saída dos aplicativos móveis para gravar um novo registro em uma tabela de aplicativos móveis.  

## <a name="output---example"></a>Saída - exemplo

Veja o exemplo de idioma específico:

* [C#](#output---c-example)
* [Script do c# (.csx)](#output---c-script-example)
* [JavaScript](#output---javascript-example)

### <a name="output---c-example"></a>Saída - exemplo do c#

O exemplo a seguir mostra uma [ C# função](functions-dotnet-class-library.md) que é disparada por uma mensagem da fila e cria um registro em uma tabela de aplicativo móvel.

```csharp
[FunctionName("MobileAppsOutput")]        
[return: MobileTable(ApiKeySetting = "MyMobileAppKey", TableName = "MyTable", MobileAppUriSetting = "MyMobileAppUri")]
public static object Run(
    [QueueTrigger("myqueue-items", Connection = "AzureWebJobsStorage")] string myQueueItem,
    TraceWriter log)
{
    return new { Text = $"I'm running in a C# function! {myQueueItem}" };
}
```

### <a name="output---c-script-example"></a>Saída - exemplo de script do c#

O exemplo a seguir mostra uma associação de saída dos aplicativos móveis em um arquivo *Function. JSON* e uma [ C# função de script](functions-reference-csharp.md) que usa a associação. A função é disparada por uma mensagem da fila e cria um novo registro com valor embutido em código para a propriedade `Text`.

Eis a vinculação de dados a *Function* ficheiro:

```json
{
"bindings": [
    {
    "name": "myQueueItem",
    "queueName": "myqueue-items",
    "connection":"",
    "type": "queueTrigger",
    "direction": "in"
    },
    {
    "name": "record",
    "type": "mobileTable",
    "tableName": "MyTable",
    "connection": "My_MobileApp_Url",
    "apiKey": "My_MobileApp_Key",
    "direction": "out"
    }
]
}
```

O [configuração](#output---configuration) seção explica essas propriedades.

Aqui está o código de script do c#:

```cs
public static void Run(string myQueueItem, out object record)
{
    record = new {
        Text = $"I'm running in a C# function! {myQueueItem}"
    };
}
```

### <a name="output---javascript-example"></a>Saída - exemplo de JavaScript

O exemplo a seguir mostra uma associação de saída dos aplicativos móveis em um arquivo *Function. JSON* e uma [função JavaScript](functions-reference-node.md) que usa a associação. A função é disparada por uma mensagem da fila e cria um novo registro com valor embutido em código para a propriedade `Text`.

Eis a vinculação de dados a *Function* ficheiro:

```json
{
"bindings": [
    {
    "name": "myQueueItem",
    "queueName": "myqueue-items",
    "connection":"",
    "type": "queueTrigger",
    "direction": "in"
    },
    {
    "name": "record",
    "type": "mobileTable",
    "tableName": "MyTable",
    "connection": "My_MobileApp_Url",
    "apiKey": "My_MobileApp_Key",
    "direction": "out"
    }
],
"disabled": false
}
```

O [configuração](#output---configuration) seção explica essas propriedades.

Eis o código JavaScript:

```javascript
module.exports = function (context, myQueueItem) {

    context.bindings.record = {
        text : "I'm running in a Node function! Data: '" + myQueueItem + "'"
    }   

    context.done();
};
```

## <a name="output---attributes"></a>Saída - atributos

Em [ C# bibliotecas de classes](functions-dotnet-class-library.md), use o atributo [mobiletable](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.MobileApps/MobileTableAttribute.cs) .

Para obter informações sobre as propriedades de atributo que podem ser configuradas, consulte [saída-configuração](#output---configuration). Aqui está um `MobileTable` exemplo de atributo numa assinatura do método:

```csharp
[FunctionName("MobileAppsOutput")]        
[return: MobileTable(ApiKeySetting = "MyMobileAppKey", TableName = "MyTable", MobileAppUriSetting = "MyMobileAppUri")]
public static object Run(
    [QueueTrigger("myqueue-items", Connection = "AzureWebJobsStorage")] string myQueueItem,
    TraceWriter log)
{
    ...
}
```

Para obter um exemplo completo, consulte [saída - exemplo do c#](#output---c-example).

## <a name="output---configuration"></a>Saída - configuração

A tabela seguinte explica as propriedades de configuração de ligação definida no *Function* ficheiro e o `MobileTable` atributo.

|propriedade de Function | Propriedade de atributo |Descrição|
|---------|---------|----------------------|
| **tipo**|| Deve ser definido como "mobiletable"|
| **direção**||Deve ser definido como "out"|
| **name**|| Nome do parâmetro de saída na assinatura de função.|
|**tableName** |**TableName**|Nome da tabela de dados do aplicativo móvel|
|**ligação**|**MobileAppUriSetting**|O nome de uma configuração de aplicativo que tem a URL do aplicativo móvel. A função usa essa URL para construir as operações REST necessárias em seu aplicativo móvel. Crie uma configuração de aplicativo em seu aplicativo de funções que contenha a URL do aplicativo móvel e, em seguida, especifique o nome da configuração do aplicativo na propriedade `connection` em sua associação de entrada. A URL é semelhante a `http://<appname>.azurewebsites.net`.
|**apiKey**|**ApiKeySetting**|O nome de uma configuração de aplicativo que tem a chave de API do aplicativo móvel. Forneça a chave de API se você [implementar uma chave de API no back-end do aplicativo móvel do node. js](https://github.com/Azure/azure-mobile-apps-node/tree/master/samples/api-key)ou [implementar uma chave de API no back-end do aplicativo móvel do .net](https://github.com/Azure/azure-mobile-apps-net-server/wiki/Implementing-Application-Key). Para fornecer a chave, crie uma configuração de aplicativo em seu aplicativo de funções que contém a chave de API e, em seguida, adicione a propriedade `apiKey` em sua associação de entrada com o nome da configuração do aplicativo. |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

> [!IMPORTANT]
> Não compartilhe a chave de API com seus clientes de aplicativos móveis. Ele só deve ser distribuído com segurança para clientes do lado do serviço, como Azure Functions. Azure Functions armazena as informações de conexão e as chaves de API como configurações de aplicativo para que elas não sejam verificadas em seu repositório de controle do código-fonte. Isso protege suas informações confidenciais.

## <a name="output---usage"></a>Saída - utilização

Em C# funções de script, use um parâmetro de saída nomeado do tipo `out object` para acessar o registro de saída. Em C# bibliotecas de classes, o atributo `MobileTable` pode ser usado com qualquer um dos seguintes tipos:

* `ICollector<T>` ou `IAsyncCollector<T>`, em que `T` é `JObject` ou qualquer tipo com uma propriedade `public string Id`.
* `out JObject`
* `out T` ou `out T[]`, em que `T` é qualquer tipo com uma propriedade `public string Id`.

Nas funções do node. js, use `context.bindings.<name>` para acessar o registro de saída.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Saiba mais sobre as funções do Azure acionadores e enlaces](functions-triggers-bindings.md)
