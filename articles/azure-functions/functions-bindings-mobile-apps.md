---
title: Ligações de Aplicativos Móveis para Funções Azure
description: Entenda como utilizar as ligações Azure Mobile Apps em Funções Azure.
author: craigshoemaker
ms.topic: reference
ms.custom: devx-track-csharp
ms.date: 11/21/2017
ms.author: cshoe
ms.openlocfilehash: 5ea58cc3d9f3615a74249b36f3f9ffb79caddda1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "88212241"
---
# <a name="mobile-apps-bindings-for-azure-functions"></a>Ligações de Aplicativos Móveis para Funções Azure 

> [!NOTE]
> As ligações Azure Mobile Apps só estão disponíveis para as Funções Azure 1.x. Não são suportados nas Funções Azure 2.x ou superior.

Este artigo explica como trabalhar com as ligações [Azure Mobile Apps](/previous-versions/azure/app-service-mobile/app-service-mobile-value-prop) em Funções Azure. A Azure Functions suporta entradas e encadernações de saída para aplicações móveis.

As ligações para aplicações móveis permitem ler e atualizar tabelas de dados em aplicações móveis.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>Pacotes - Funções 1.x

As ligações para aplicações móveis são fornecidas no pacote [Microsoft.Azure.WebJobs.Extensions.MobileApps](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.MobileApps) NuGet, versão 1.x. O código fonte para o pacote está no [repositório GitHub.](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions.MobileApps/)

[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="input"></a>Entrada

A entrada de Aplicativos Móveis carrega um registo a partir de um ponto final de tabela móvel e passa-o para a sua função. Nas funções C# e F#, quaisquer alterações efetuadas ao registo são automaticamente enviadas para a mesa quando a função sai com sucesso.

## <a name="input---example"></a>Entrada - exemplo

Veja o exemplo específico da linguagem:

* [Script C# (.csx)](#input---c-script-example)
* JavaScript

### <a name="input---c-script-example"></a>Entrada - Exemplo de script C#

O exemplo a seguir mostra uma ligação de entrada de Aplicações Móveis numa *function.jsno* ficheiro e uma [função de script C#](functions-reference-csharp.md) que utiliza a ligação. A função é desencadeada por uma mensagem de fila que tem um identificador de registo. A função lê o registo especificado e modifica a sua `Text` propriedade.

Aqui estão os dados vinculativos do *function.jsarquivado:*

```json
{
"bindings": [
    {
        "name": "myQueueItem",
        "queueName": "myqueue-items",
        "connection": "",
        "type": "queueTrigger",
        "direction": "in"
    },
    {
        "name": "record",
        "type": "mobileTable",
        "tableName": "MyTable",
        "id": "{queueTrigger}",
        "connection": "My_MobileApp_Url",
        "apiKey": "My_MobileApp_Key",
        "direction": "in"
    }
]
}
```
A secção [de configuração](#input---configuration) explica estas propriedades.

Aqui está o código do guião C:

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

O exemplo a seguir mostra uma ligação de entrada de Aplicações Móveis numa *function.jsno* ficheiro e numa [função JavaScript](functions-reference-node.md) que utiliza a ligação. A função é desencadeada por uma mensagem de fila que tem um identificador de registo. A função lê o registo especificado e modifica a sua `Text` propriedade.

Aqui estão os dados vinculativos do *function.jsarquivado:*

```json
{
"bindings": [
    {
        "name": "myQueueItem",
        "queueName": "myqueue-items",
        "connection": "",
        "type": "queueTrigger",
        "direction": "in"
    },
    {
        "name": "record",
        "type": "mobileTable",
        "tableName": "MyTable",
        "id": "{queueTrigger}",
        "connection": "My_MobileApp_Url",
        "apiKey": "My_MobileApp_Key",
        "direction": "in"
    }
]
}
```
A secção [de configuração](#input---configuration) explica estas propriedades.

Aqui está o código JavaScript:

```javascript
module.exports = function (context, myQueueItem) {    
    context.log(context.bindings.record);
    context.done();
};
```

## <a name="input---attributes"></a>Entrada - atributos

Nas [bibliotecas de classe C,](functions-dotnet-class-library.md)utilize o atributo [MobileTable.](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.MobileApps/MobileTableAttribute.cs)

Para obter informações sobre propriedades de atributos que possa configurar, consulte [a seguinte secção de configuração](#input---configuration).

## <a name="input---configuration"></a>Entrada - configuração

A tabela seguinte explica as propriedades de configuração de encadernação que definiu no *function.jsno* ficheiro e no `MobileTable` atributo.

|function.jsna propriedade | Propriedade de atributo |Description|
|---------|---------|----------------------|
| **tipo**| n/a | Deve ser definido para "mobileTable"|
| **direção**| n/a |Deve ser definido para "in"|
| **nome**| n/a | Nome do parâmetro de entrada na assinatura da função.|
|**nome de mesa** |**TableName**|Nome da tabela de dados da aplicação móvel|
| **id**| **ID** | O identificador do registo para recuperar. Pode ser estático ou baseado no gatilho que invoca a função. Por exemplo, se utilizar um gatilho de fila para a sua função, `"id": "{queueTrigger}"` utiliza o valor de cadeia da mensagem de fila como iD de registo para recuperar.|
|**conexão**|**Ligação**|O nome de uma configuração de aplicação que tem o URL da aplicação móvel. A função utiliza este URL para construir as operações de REST necessárias contra a sua aplicação móvel. Crie uma definição de aplicação na sua aplicação de função que contenha o URL da aplicação móvel e, em seguida, especifique o nome da definição da aplicação na `connection` propriedade na sua ligação de entrada. A URL `http://<appname>.azurewebsites.net` parece.
|**apiKey**|**ApiKey**|O nome de uma configuração de aplicação que tem a chave API da sua aplicação móvel. Forneça a chave API se [implementar uma chave API na sua aplicação móvel Node.js,](https://github.com/Azure/azure-mobile-apps-node/tree/master/samples/api-key)ou [implementar uma chave API na sua aplicação móvel .NET.](https://github.com/Azure/azure-mobile-apps-net-server/wiki/Implementing-Application-Key) Para fornecer a chave, crie uma definição de app na sua aplicação de função que contenha a chave API e, em seguida, adicione a `apiKey` propriedade na sua ligação de entrada com o nome da configuração da aplicação. |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

> [!IMPORTANT]
> Não partilhe a chave API com os seus clientes de aplicações móveis. Só deve ser distribuído de forma segura a clientes do lado do serviço, como o Azure Functions. A Azure Functions armazena as suas informações de ligação e as teclas API como configurações de aplicações para que não sejam verificadas no seu repositório de controlo de origem. Isto salvaguarda a sua informação sensível.

## <a name="input---usage"></a>Entrada - utilização

Nas funções C#, quando o registo com o ID especificado é encontrado, é passado para o chamado parâmetro [JObject.](https://www.newtonsoft.com/json/help/html/t_newtonsoft_json_linq_jobject.htm) Quando o registo não é encontrado, o valor do parâmetro é `null` . 

Nas funções JavaScript, o registo é transmitido para o `context.bindings.<name>` objeto. Quando o registo não é encontrado, o valor do parâmetro é `null` . 

Nas funções C# e F#, quaisquer alterações que escote ao registo de entrada (parâmetro de entrada) são automaticamente enviadas para a tabela quando a função sai com sucesso. Não é possível modificar um registo nas funções JavaScript.

## <a name="output"></a>Saída

Utilize a ligação de saída de Aplicações Móveis para escrever um novo disco numa tabela de Aplicações Móveis.  

## <a name="output---example"></a>Saída - exemplo

Veja o exemplo específico da linguagem:

* [C#](#output---c-example)
* [Script C# (.csx)](#output---c-script-example)
* [JavaScript](#output---javascript-example)

### <a name="output---c-example"></a>Saída - Exemplo C#

O exemplo a seguir mostra uma [função C#](functions-dotnet-class-library.md) que é desencadeada por uma mensagem de fila e cria um registo numa tabela de aplicações móveis.

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

### <a name="output---c-script-example"></a>Saída - Exemplo de script C#

O exemplo a seguir mostra uma ligação de saída de Aplicações Móveis numa *function.jsno* ficheiro e uma [função de script C#](functions-reference-csharp.md) que utiliza a ligação. A função é desencadeada por uma mensagem de fila e cria um novo registo com valor codificado para a `Text` propriedade.

Aqui estão os dados vinculativos do *function.jsarquivado:*

```json
{
"bindings": [
    {
        "name": "myQueueItem",
        "queueName": "myqueue-items",
        "connection": "",
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

A secção [de configuração](#output---configuration) explica estas propriedades.

Aqui está o código do guião C:

```cs
public static void Run(string myQueueItem, out object record)
{
    record = new {
        Text = $"I'm running in a C# function! {myQueueItem}"
    };
}
```

### <a name="output---javascript-example"></a>Saída - Exemplo JavaScript

O exemplo a seguir mostra uma ligação de saída de Aplicações Móveis numa *function.jsem* ficheiro e numa [função JavaScript](functions-reference-node.md) que utiliza a ligação. A função é desencadeada por uma mensagem de fila e cria um novo registo com valor codificado para a `Text` propriedade.

Aqui estão os dados vinculativos do *function.jsarquivado:*

```json
{
"bindings": [
    {
        "name": "myQueueItem",
        "queueName": "myqueue-items",
        "connection": "",
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

A secção [de configuração](#output---configuration) explica estas propriedades.

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

Nas [bibliotecas de classe C,](functions-dotnet-class-library.md)utilize o atributo [MobileTable.](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.MobileApps/MobileTableAttribute.cs)

Para obter informações sobre propriedades de atributos que possa configurar, consulte [saída - configuração](#output---configuration). Aqui está um `MobileTable` exemplo de atributo numa assinatura de método:

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

Para um exemplo completo, consulte [o exemplo de Saída - C#](#output---c-example).

## <a name="output---configuration"></a>Saída - configuração

A tabela seguinte explica as propriedades de configuração de encadernação que definiu no *function.jsno* ficheiro e no `MobileTable` atributo.

|function.jsna propriedade | Propriedade de atributo |Description|
|---------|---------|----------------------|
| **tipo**| n/a | Deve ser definido para "mobileTable"|
| **direção**| n/a |Deve ser definido para "out"|
| **nome**| n/a | Nome do parâmetro de saída na assinatura da função.|
|**nome de mesa** |**TableName**|Nome da tabela de dados da aplicação móvel|
|**conexão**|**MobileAppUriSetting**|O nome de uma configuração de aplicação que tem o URL da aplicação móvel. A função utiliza este URL para construir as operações de REST necessárias contra a sua aplicação móvel. Crie uma definição de aplicação na sua aplicação de função que contenha o URL da aplicação móvel e, em seguida, especifique o nome da definição da aplicação na `connection` propriedade na sua ligação de entrada. A URL `http://<appname>.azurewebsites.net` parece.
|**apiKey**|**ApiKeySetting**|O nome de uma configuração de aplicação que tem a chave API da sua aplicação móvel. Forneça a chave API se [implementar uma chave API no seu backend de aplicações móveis Node.js,](https://github.com/Azure/azure-mobile-apps-node/tree/master/samples/api-key)ou [implementar uma chave API no seu backend de aplicações móveis .NET](https://github.com/Azure/azure-mobile-apps-net-server/wiki/Implementing-Application-Key). Para fornecer a chave, crie uma definição de app na sua aplicação de função que contenha a chave API e, em seguida, adicione a `apiKey` propriedade na sua ligação de entrada com o nome da configuração da aplicação. |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

> [!IMPORTANT]
> Não partilhe a chave API com os seus clientes de aplicações móveis. Só deve ser distribuído de forma segura a clientes do lado do serviço, como o Azure Functions. A Azure Functions armazena as suas informações de ligação e as teclas API como configurações de aplicações para que não sejam verificadas no seu repositório de controlo de origem. Isto salvaguarda a sua informação sensível.

## <a name="output---usage"></a>Saída - utilização

Nas funções de script C# utilize um parâmetro de saída nomeado `out object` para aceder ao registo de saída. Nas bibliotecas de classe C#, o `MobileTable` atributo pode ser utilizado com qualquer um dos seguintes tipos:

* `ICollector<T>` ou `IAsyncCollector<T>` , onde está ou qualquer tipo com uma `T` `JObject` `public string Id` propriedade.
* `out JObject`
* `out T` ou `out T[]` , onde está qualquer Tipo com uma `T` `public string Id` propriedade.

Em Node.js funções, utilize `context.bindings.<name>` para aceder ao registo de saída.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Saiba mais sobre as funções Azure desencadeia e encaderna](functions-triggers-bindings.md)
