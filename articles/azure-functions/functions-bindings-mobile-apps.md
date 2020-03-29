---
title: Ligações de aplicativos móveis para funções azure
description: Entenda como utilizar ligações de Aplicações Móveis Azure em Funções Azure.
author: craigshoemaker
ms.topic: reference
ms.date: 11/21/2017
ms.author: cshoe
ms.openlocfilehash: 952a94797e01a3931fdd151461250af0c2590c11
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76120546"
---
# <a name="mobile-apps-bindings-for-azure-functions"></a>Ligações de aplicativos móveis para funções azure 

> [!NOTE]
> As ligações das Aplicações Móveis Azure só estão disponíveis para as Funções Azure 1.x. Não são suportados nas Funções Azure 2.x e superiores.

Este artigo explica como trabalhar com ligações [azure mobile apps](../app-service-mobile/app-service-mobile-value-prop.md) em Funções Azure. A Azure Functions suporta encadernações de entrada e saída para aplicações móveis.

As ligações das Aplicações Móveis permitem-lhe ler e atualizar tabelas de dados em aplicações móveis.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>Pacotes - Funções 1.x

As ligações de aplicações móveis são fornecidas no pacote [Microsoft.Azure.WebJobs.Extensions.MobileApps](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.MobileApps) NuGet, versão 1.x. O código fonte para o pacote está no repositório [GitHub-extensões azure-webjobs-sdk.](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions.MobileApps/)

[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="input"></a>Input

A encadernação de entrada de Aplicativos Móveis carrega um registo a partir de um ponto final de mesa móvel e passa-o para a sua função. Nas funções C# e F#, quaisquer alterações feitas ao registo são automaticamente enviadas de volta para a tabela quando a função sai com sucesso.

## <a name="input---example"></a>Entrada - exemplo

Consulte o exemplo específico da linguagem:

* [Script C# (.csx)](#input---c-script-example)
* Javascript

### <a name="input---c-script-example"></a>Exemplo de argumento De Entrada - C#

O exemplo seguinte mostra uma ligação de entrada de Aplicativos Móveis num ficheiro *function.json* e uma [função de script C#](functions-reference-csharp.md) que utiliza a ligação. A função é desencadeada por uma mensagem de fila que tem um identificador de registo. A função lê o registo `Text` especificado e modifica a sua propriedade.

Aqui estão os dados vinculativos no ficheiro *função.json:*

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
A secção de [configuração](#input---configuration) explica estas propriedades.

Aqui está o código de script C#:

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

### <a name="input---javascript"></a>Entrada - JavaScript

O exemplo seguinte mostra uma ligação de entrada de Aplicações Móveis num ficheiro *function.json* e uma [função JavaScript](functions-reference-node.md) que utiliza a ligação. A função é desencadeada por uma mensagem de fila que tem um identificador de registo. A função lê o registo `Text` especificado e modifica a sua propriedade.

Aqui estão os dados vinculativos no ficheiro *função.json:*

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
A secção de [configuração](#input---configuration) explica estas propriedades.

Aqui está o código JavaScript:

```javascript
module.exports = function (context, myQueueItem) {    
    context.log(context.bindings.record);
    context.done();
};
```

## <a name="input---attributes"></a>Entrada - atributos

Nas [bibliotecas da classe C#,](functions-dotnet-class-library.md)utilize o atributo [MobileTable.](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.MobileApps/MobileTableAttribute.cs)

Para obter informações sobre propriedades de atributo que possa configurar, consulte a seguinte secção de [configuração](#input---configuration).

## <a name="input---configuration"></a>Entrada - configuração

A tabela a seguir explica as propriedades de configuração de ligação que definiu no ficheiro *função.json* e no `MobileTable` atributo.

|propriedade fun.json | Propriedade de atributo |Descrição|
|---------|---------|----------------------|
| **tipo**| n/d | Deve ser definido para "mobileTable"|
| **direção**| n/d |Deve ser definido para "in"|
| **nome**| n/d | Nome do parâmetro de entrada na assinatura da função.|
|**tableName** |**Nome de mesa**|Nome da tabela de dados da aplicação móvel|
| **ID**| **Id** | O identificador do registo para recuperar. Pode ser estática ou baseada no gatilho que invoca a função. Por exemplo, se utilizar um gatilho de `"id": "{queueTrigger}"` fila para a sua função, utilize o valor de cadeia da mensagem de fila como o ID de registo para recuperar.|
|**conexão**|**Conexão**|O nome de uma definição de aplicação que tem o URL da aplicação móvel. A função utiliza este URL para construir as operações de REST necessárias contra a sua aplicação móvel. Crie uma definição de aplicação na sua aplicação de função que `connection` contenha o URL da aplicação móvel e, em seguida, especifique o nome da definição de aplicação na propriedade na sua encadernação de entrada. O URL `http://<appname>.azurewebsites.net`parece.
|**apiKey**|**ApiKey**|O nome de uma definição de aplicação que tem a chave API da sua aplicação móvel. Forneça a tecla API se [implementar uma chave API na sua aplicação móvel Node.js,](https://github.com/Azure/azure-mobile-apps-node/tree/master/samples/api-key)ou [implementar uma chave API na sua aplicação móvel .NET](https://github.com/Azure/azure-mobile-apps-net-server/wiki/Implementing-Application-Key). Para fornecer a chave, crie uma definição de aplicação na `apiKey` sua aplicação de função que contenha a chave API e, em seguida, adicione a propriedade na sua ligação de entrada com o nome da definição da aplicação. |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

> [!IMPORTANT]
> Não partilhe a chave API com os seus clientes de aplicações móveis. Só deve ser distribuído de forma segura a clientes do lado do serviço, como as Funções Azure. A Azure Functions armazena as suas informações de ligação e as teclas API como configurações da aplicação para que não sejam verificadas no seu repositório de controlo de fonte. Isto salvaguarda a sua informação sensível.

## <a name="input---usage"></a>Entrada - utilização

Nas funções C#, quando o registo com o ID especificado é encontrado, é passado para o parâmetro [JObject.](https://www.newtonsoft.com/json/help/html/t_newtonsoft_json_linq_jobject.htm) Quando o registo não é encontrado, `null`o valor do parâmetro é . 

Nas funções JavaScript, o registo `context.bindings.<name>` é passado para o objeto. Quando o registo não é encontrado, `null`o valor do parâmetro é . 

Nas funções C# e F#, quaisquer alterações que efetuar no registo de entrada (parâmetro de entrada) são automaticamente enviadas de volta para a tabela quando a função sair com sucesso. Não é possível modificar um registo nas funções JavaScript.

## <a name="output"></a>Saída

Utilize a ligação de saída de Aplicações Móveis para escrever um novo disco para uma tabela de Aplicações Móveis.  

## <a name="output---example"></a>Saída - exemplo

Consulte o exemplo específico da linguagem:

* [C #](#output---c-example)
* [Script C# (.csx)](#output---c-script-example)
* [JavaScript](#output---javascript-example)

### <a name="output---c-example"></a>Saída - C# exemplo

O exemplo seguinte mostra uma [função C#](functions-dotnet-class-library.md) que é desencadeada por uma mensagem de fila e cria um registo numa tabela de aplicações móveis.

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

### <a name="output---c-script-example"></a>Exemplo de script C#

O exemplo seguinte mostra uma ligação de saída de Aplicativos Móveis num ficheiro *function.json* e uma [função de script C#](functions-reference-csharp.md) que utiliza a ligação. A função é desencadeada por uma mensagem de fila e cria `Text` um novo registo com valor codificado para a propriedade.

Aqui estão os dados vinculativos no ficheiro *função.json:*

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

A secção de [configuração](#output---configuration) explica estas propriedades.

Aqui está o código de script C#:

```cs
public static void Run(string myQueueItem, out object record)
{
    record = new {
        Text = $"I'm running in a C# function! {myQueueItem}"
    };
}
```

### <a name="output---javascript-example"></a>Saída - Exemplo JavaScript

O exemplo seguinte mostra uma ligação de saída de Aplicativos Móveis num ficheiro *function.json* e uma [função JavaScript](functions-reference-node.md) que utiliza a ligação. A função é desencadeada por uma mensagem de fila e cria `Text` um novo registo com valor codificado para a propriedade.

Aqui estão os dados vinculativos no ficheiro *função.json:*

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

A secção de [configuração](#output---configuration) explica estas propriedades.

Aqui está o código JavaScript:

```javascript
module.exports = function (context, myQueueItem) {

    context.bindings.record = {
        text : "I'm running in a Node function! Data: '" + myQueueItem + "'"
    }   

    context.done();
};
```

## <a name="output---attributes"></a>Saída - atributos

Nas [bibliotecas da classe C#,](functions-dotnet-class-library.md)utilize o atributo [MobileTable.](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.MobileApps/MobileTableAttribute.cs)

Para obter informações sobre propriedades de atributo que possa configurar, consulte [A Saída - configuração](#output---configuration). Aqui está `MobileTable` um exemplo de atributo numa assinatura de método:

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

Para um exemplo completo, consulte [a saída - C# exemplo](#output---c-example).

## <a name="output---configuration"></a>Saída - configuração

A tabela a seguir explica as propriedades de configuração de ligação que definiu no ficheiro *função.json* e no `MobileTable` atributo.

|propriedade fun.json | Propriedade de atributo |Descrição|
|---------|---------|----------------------|
| **tipo**| n/d | Deve ser definido para "mobileTable"|
| **direção**| n/d |Deve ser definido para "fora"|
| **nome**| n/d | Nome do parâmetro de saída na assinatura da função.|
|**tableName** |**Nome de mesa**|Nome da tabela de dados da aplicação móvel|
|**conexão**|**MobileAppuriSetting**|O nome de uma definição de aplicação que tem o URL da aplicação móvel. A função utiliza este URL para construir as operações de REST necessárias contra a sua aplicação móvel. Crie uma definição de aplicação na sua aplicação de função que `connection` contenha o URL da aplicação móvel e, em seguida, especifique o nome da definição de aplicação na propriedade na sua encadernação de entrada. O URL `http://<appname>.azurewebsites.net`parece.
|**apiKey**|**ApiKeySetting**|O nome de uma definição de aplicação que tem a chave API da sua aplicação móvel. Forneça a tecla API se [implementar uma chave API no seu backend de aplicação móvel Node.js,](https://github.com/Azure/azure-mobile-apps-node/tree/master/samples/api-key)ou [implementar uma chave API no seu backend de aplicação móvel .NET](https://github.com/Azure/azure-mobile-apps-net-server/wiki/Implementing-Application-Key). Para fornecer a chave, crie uma definição de aplicação na `apiKey` sua aplicação de função que contenha a chave API e, em seguida, adicione a propriedade na sua ligação de entrada com o nome da definição da aplicação. |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

> [!IMPORTANT]
> Não partilhe a chave API com os seus clientes de aplicações móveis. Só deve ser distribuído de forma segura a clientes do lado do serviço, como as Funções Azure. A Azure Functions armazena as suas informações de ligação e as teclas API como configurações da aplicação para que não sejam verificadas no seu repositório de controlo de fonte. Isto salvaguarda a sua informação sensível.

## <a name="output---usage"></a>Saída - utilização

Nas funções do script C#, utilize `out object` um parâmetro de saída de tipo nomeado para aceder ao registo de saída. Nas bibliotecas da `MobileTable` classe C#, o atributo pode ser utilizado com qualquer um dos seguintes tipos:

* `ICollector<T>`ou, `IAsyncCollector<T>` `T` onde `JObject` é ou qualquer `public string Id` tipo com uma propriedade.
* `out JObject`
* `out T`ou, `out T[]` `T` onde está qualquer `public string Id` Tipo com uma propriedade.

Nas funções Node.js, utilize `context.bindings.<name>` para aceder ao registo de saída.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Saiba mais sobre as funções azure gatilhos e encadernações](functions-triggers-bindings.md)
