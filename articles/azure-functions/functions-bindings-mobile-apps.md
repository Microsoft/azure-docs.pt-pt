---
title: Enlaces de aplicações móveis para as funções do Azure
description: Aprenda a usar associações de aplicações móveis do Azure nas funções do Azure.
services: functions
documentationcenter: na
author: craigshoemaker
manager: jeconnoc
keywords: das funções do Azure, funções, processamento de eventos, computação dinâmica, arquitetura sem servidor
ms.service: azure-functions
ms.devlang: multiple
ms.topic: reference
ms.date: 11/21/2017
ms.author: cshoe
ms.openlocfilehash: 5fd220f15f363c1987f1576009519e4b2feae6b9
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "61438130"
---
# <a name="mobile-apps-bindings-for-azure-functions"></a>Enlaces de aplicações móveis para as funções do Azure 

> [!NOTE]
> Enlaces de aplicações móveis do Azure só estão disponíveis para as funções do Azure 1.x. Não são suportados nas funções do Azure 2.x.

Este artigo explica como trabalhar com [Mobile Apps do Azure](../app-service-mobile/app-service-mobile-value-prop.md) enlaces no funções do Azure. Funções do Azure suporta de entrada e saída enlaces para aplicações móveis.

Os enlaces de aplicações móveis permitem-lhe ler e atualizar tabelas de dados nas aplicações móveis.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>Pacotes - funções 1.x

Enlaces de aplicações móveis são fornecidos na [Microsoft.Azure.WebJobs.Extensions.MobileApps](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.MobileApps) pacote NuGet, versão 1.x. Código-fonte para o pacote está no [azure-webjobs-sdk-extensões](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions.MobileApps/) repositório do GitHub.

[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="input"></a>Input

O enlace de entrada de aplicações móveis carrega um registo a partir de um ponto de final de tabela móveis e passa-o para a sua função. No C# e F# as funções, todas as alterações feitas no registo são automaticamente enviadas para a tabela quando a função é encerrado com êxito.

## <a name="input---example"></a>Entrada - exemplo

Veja o exemplo de idioma específico:

* [Script do c# (.csx)](#input---c-script-example)
* JavaScript

### <a name="input---c-script-example"></a>Introdução - exemplo de script do c#

O exemplo seguinte mostra um enlace de entrada de aplicações móveis num *Function* ficheiro e uma [função de script do c#](functions-reference-csharp.md) que utiliza o enlace. A função é acionada por uma mensagem de fila que tem um identificador de registo. A função lê o registo especificado e modifica seu `Text` propriedade.

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

### <a name="input---javascript"></a>Introdução - JavaScript

O exemplo seguinte mostra um enlace de entrada de aplicações móveis num *Function* ficheiro e uma [função JavaScript](functions-reference-node.md) que utiliza o enlace. A função é acionada por uma mensagem de fila que tem um identificador de registo. A função lê o registo especificado e modifica seu `Text` propriedade.

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

Na [bibliotecas de classes do c#](functions-dotnet-class-library.md), utilize o [MobileTable](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.MobileApps/MobileTableAttribute.cs) atributo.

Para obter informações sobre as propriedades do atributo que pode configurar, consulte [a seguinte secção de configuração](#input---configuration).

## <a name="input---configuration"></a>Entrada - configuração

A tabela seguinte explica as propriedades de configuração de ligação definida no *Function* ficheiro e o `MobileTable` atributo.

|propriedade de Function | Propriedade de atributo |Descrição|
|---------|---------|----------------------|
| **type**|| Tem de ser definido como "mobileTable"|
| **direction**||Tem de ser definido para "in"|
| **name**|| Nome do parâmetro de entrada na assinatura de função.|
|**tableName** |**TableName**|Nome da tabela de dados da aplicação móvel|
| **id**| **Id** | O identificador do registo a obter. Pode ser estático ou com base no acionador que invoca a função. Por exemplo, se utilizar um acionador de fila para a função e, em seguida, `"id": "{queueTrigger}"` utiliza o valor de cadeia de caracteres da mensagem de fila como o ID de registo a obter.|
|**ligação**|**ligação**|O nome de uma definição de aplicação que tenha o URL da aplicação móvel. A função utiliza este URL para construir as operações necessárias de REST em relação a sua aplicação móvel. Criar uma definição de aplicação na sua aplicação de função que contém o URL da aplicação móvel, em seguida, especifique o nome da definição de aplicação no `connection` propriedade no enlace de entrada. O URL é semelhante a `http://<appname>.azurewebsites.net`.
|**apiKey**|**ApiKey**|O nome de uma definição de aplicação que tenha a chave de API da sua aplicação móvel. Fornecer o se chave de API que [implementar uma chave de API na sua aplicação móvel do node. js](https://github.com/Azure/azure-mobile-apps-node/tree/master/samples/api-key), ou [implementar uma chave de API na aplicação móvel para .NET](https://github.com/Azure/azure-mobile-apps-net-server/wiki/Implementing-Application-Key). Para fornecer a chave, crie uma definição de aplicação na sua aplicação de função que contém a chave de API, em seguida, adicione o `apiKey` propriedade no seu enlace de entrada com o nome da definição de aplicação. |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

> [!IMPORTANT]
> Não partilhe a chave de API com seus clientes de aplicações móveis. Ele deve apenas ser distribuído com segurança para clientes de lado do serviço, como as funções do Azure. As funções do Azure armazena suas informações de ligação e chaves de API como definições da aplicação, para que eles não são verificados no seu repositório de controle de origem. Esta salvaguarda as suas informações confidenciais.

## <a name="input---usage"></a>Introdução - utilização

No c# das funções, quando o registo com o ID especificado é encontrado, ele é passado para o nomeado [JObject](https://www.newtonsoft.com/json/help/html/t_newtonsoft_json_linq_jobject.htm) parâmetro. Quando o registo não for encontrado, o valor do parâmetro é `null`. 

Nas funções de JavaScript, o registo é passado para o `context.bindings.<name>` objeto. Quando o registo não for encontrado, o valor do parâmetro é `null`. 

No C# e F# as funções, quaisquer alterações efetuadas para a entrada de registo (parâmetro de entrada) são automaticamente enviados para a tabela quando a função é encerrado com êxito. Não é possível modificar um registo nas funções de JavaScript.

## <a name="output"></a>Saída

Utilize a saída de aplicações móveis ligar a um novo registo de escrita para uma tabela de aplicações móveis.  

## <a name="output---example"></a>Saída - exemplo

Veja o exemplo de idioma específico:

* [C#](#output---c-example)
* [Script do c# (.csx)](#output---c-script-example)
* [JavaScript](#output---javascript-example)

### <a name="output---c-example"></a>Saída - exemplo do c#

A exemplo a seguir mostra um [função c#](functions-dotnet-class-library.md) que é acionado por uma mensagem de fila e cria um registo numa tabela de aplicação móvel.

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

O exemplo seguinte mostra uma saída de aplicações móveis de enlace num *Function* ficheiro e uma [função de script do c#](functions-reference-csharp.md) que utiliza o enlace. A função é acionada por uma mensagem de fila e cria um novo registo com embutidos no valor para o `Text` propriedade.

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

O exemplo seguinte mostra uma saída de aplicações móveis de enlace num *Function* ficheiro e uma [função JavaScript](functions-reference-node.md) que utiliza o enlace. A função é acionada por uma mensagem de fila e cria um novo registo com embutidos no valor para o `Text` propriedade.

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

Na [bibliotecas de classes do c#](functions-dotnet-class-library.md), utilize o [MobileTable](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.MobileApps/MobileTableAttribute.cs) atributo.

Para obter informações sobre as propriedades do atributo que pode configurar, consulte [de saída - configuração](#output---configuration). Aqui está um `MobileTable` exemplo de atributo numa assinatura do método:

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
| **type**|| Tem de ser definido como "mobileTable"|
| **direction**||Tem de ser definido para "Sair"|
| **name**|| Nome do parâmetro de saída na assinatura de função.|
|**tableName** |**TableName**|Nome da tabela de dados da aplicação móvel|
|**ligação**|**MobileAppUriSetting**|O nome de uma definição de aplicação que tenha o URL da aplicação móvel. A função utiliza este URL para construir as operações necessárias de REST em relação a sua aplicação móvel. Criar uma definição de aplicação na sua aplicação de função que contém o URL da aplicação móvel, em seguida, especifique o nome da definição de aplicação no `connection` propriedade no enlace de entrada. O URL é semelhante a `http://<appname>.azurewebsites.net`.
|**apiKey**|**ApiKeySetting**|O nome de uma definição de aplicação que tenha a chave de API da sua aplicação móvel. Fornecer o se chave de API que [implementar uma chave de API no seu back-end de aplicação móvel do node. js](https://github.com/Azure/azure-mobile-apps-node/tree/master/samples/api-key), ou [implementar uma chave de API no seu back-end de aplicação móvel do .NET](https://github.com/Azure/azure-mobile-apps-net-server/wiki/Implementing-Application-Key). Para fornecer a chave, crie uma definição de aplicação na sua aplicação de função que contém a chave de API, em seguida, adicione o `apiKey` propriedade no seu enlace de entrada com o nome da definição de aplicação. |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

> [!IMPORTANT]
> Não partilhe a chave de API com seus clientes de aplicações móveis. Ele deve apenas ser distribuído com segurança para clientes de lado do serviço, como as funções do Azure. As funções do Azure armazena suas informações de ligação e chaves de API como definições da aplicação, para que eles não são verificados no seu repositório de controle de origem. Esta salvaguarda as suas informações confidenciais.

## <a name="output---usage"></a>Saída - utilização

Em c# funções de script, utilize um parâmetro de saída com o nome do tipo `out object` para acessar o registo de saída. No c# bibliotecas de classes, o `MobileTable` atributo pode ser usado com qualquer um dos seguintes tipos:

* `ICollector<T>` ou `IAsyncCollector<T>`, onde `T` está `JObject` ou qualquer tipo com um `public string Id` propriedade.
* `out JObject`
* `out T` ou `out T[]`, onde `T` é qualquer tipo com um `public string Id` propriedade.

Nas funções do node. js, utilize `context.bindings.<name>` para acessar o registo de saída.

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Saiba mais sobre as funções do Azure acionadores e enlaces](functions-triggers-bindings.md)
