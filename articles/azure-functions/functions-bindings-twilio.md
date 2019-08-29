---
title: Azure Functions Associação de twilio
description: Entenda como usar associações twilio com Azure Functions.
services: functions
documentationcenter: na
author: craigshoemaker
manager: gwallace
keywords: das funções do Azure, funções, processamento de eventos, computação dinâmica, arquitetura sem servidor
ms.service: azure-functions
ms.topic: reference
ms.date: 07/09/2018
ms.author: cshoe
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 32f987caed915aff8c581b974dec7689fc90b007
ms.sourcegitcommit: 8e1fb03a9c3ad0fc3fd4d6c111598aa74e0b9bd4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70114358"
---
# <a name="twilio-binding-for-azure-functions"></a>Associação de twilio para Azure Functions

Este artigo explica como enviar mensagens de texto usando associações [twilio](https://www.twilio.com/) no Azure functions. Azure Functions dá suporte a associações de saída para twilio.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>Pacotes - funções 1.x

As associações twilio são fornecidas no pacote NuGet [Microsoft. Azure. webjobs. Extensions. twilio](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Twilio) , versão 1. x. Código-fonte para o pacote está no [sdk do webjobs do azure](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions.Twilio/) repositório do GitHub.

[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="packages---functions-2x"></a>Pacotes - funções 2.x

As associações twilio são fornecidas no pacote NuGet [Microsoft. Azure. webjobs. Extensions. twilio](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Twilio) , versão 3. x. Código-fonte para o pacote está no [sdk do webjobs do azure](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.Twilio/) repositório do GitHub.

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="example---functions-1x"></a>Exemplo – funções 1. x

Veja o exemplo de idioma específico:

* [C#](#c-example)
* [Script do c# (.csx)](#c-script-example)
* [JavaScript](#javascript-example)

### <a name="c-example"></a>C#exemplo

O exemplo a seguir mostra uma [ C# função](functions-dotnet-class-library.md) que envia uma mensagem de texto quando disparada por uma mensagem da fila.

```cs
[FunctionName("QueueTwilio")]
[return: TwilioSms(AccountSidSetting = "TwilioAccountSid", AuthTokenSetting = "TwilioAuthToken", From = "+1425XXXXXXX" )]
public static SMSMessage Run(
    [QueueTrigger("myqueue-items", Connection = "AzureWebJobsStorage")] JObject order,
    TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {order}");

    var message = new SMSMessage()
    {
        Body = $"Hello {order["name"]}, thanks for your order!",
        To = order["mobileNumber"].ToString()
    };

    return message;
}
```

Este exemplo usa o `TwilioSms` atributo com o valor de retorno do método. Uma alternativa é usar o atributo com um `out SMSMessage` parâmetro ou um `ICollector<SMSMessage>` parâmetro ou `IAsyncCollector<SMSMessage>` .

### <a name="c-script-example"></a>C#exemplo de script

O exemplo a seguir mostra uma associação de saída twilio em um arquivo *Function. JSON* e uma [ C# função de script](functions-reference-csharp.md) que usa a associação. A função usa um `out` parâmetro para enviar uma mensagem de texto.

Aqui estão os dados de associação no arquivo *Function. JSON* :

Function. JSON de exemplo:

```json
{
  "type": "twilioSms",
  "name": "message",
  "accountSid": "TwilioAccountSid",
  "authToken": "TwilioAuthToken",
  "to": "+1704XXXXXXX",
  "from": "+1425XXXXXXX",
  "direction": "out",
  "body": "Azure Functions Testing"
}
```

Aqui está C# o código de script:

```cs
#r "Newtonsoft.Json"
#r "Twilio.Api"

using System;
using Newtonsoft.Json;
using Twilio;

public static void Run(string myQueueItem, out SMSMessage message,  TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");

    // In this example the queue item is a JSON string representing an order that contains the name of a 
    // customer and a mobile number to send text updates to.
    dynamic order = JsonConvert.DeserializeObject(myQueueItem);
    string msg = "Hello " + order.name + ", thank you for your order.";

    // Even if you want to use a hard coded message and number in the binding, you must at least 
    // initialize the SMSMessage variable.
    message = new SMSMessage();

    // A dynamic message can be set instead of the body in the output binding. In this example, we use 
    // the order information to personalize a text message to the mobile number provided for
    // order status updates.
    message.Body = msg;
    message.To = order.mobileNumber;
}
```

Você não pode usar parâmetros de saída no código assíncrono. Este é um exemplo C# de código de script assíncrono:

```cs
#r "Newtonsoft.Json"
#r "Twilio.Api"

using System;
using Microsoft.Extensions.Logging;
using Newtonsoft.Json;
using Twilio;

public static async Task Run(string myQueueItem, IAsyncCollector<SMSMessage> message,  ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed: {myQueueItem}");

    // In this example the queue item is a JSON string representing an order that contains the name of a 
    // customer and a mobile number to send text updates to.
    dynamic order = JsonConvert.DeserializeObject(myQueueItem);
    string msg = "Hello " + order.name + ", thank you for your order.";

    // Even if you want to use a hard coded message and number in the binding, you must at least 
    // initialize the SMSMessage variable.
    SMSMessage smsText = new SMSMessage();

    // A dynamic message can be set instead of the body in the output binding. In this example, we use 
    // the order information to personalize a text message to the mobile number provided for
    // order status updates.
    smsText.Body = msg;
    smsText.To = order.mobileNumber;

    await message.AddAsync(smsText);
}
```

### <a name="javascript-example"></a>Exemplo de JavaScript

O exemplo a seguir mostra uma associação de saída twilio em um arquivo *Function. JSON* e uma [função JavaScript](functions-reference-node.md) que usa a associação.

Aqui estão os dados de associação no arquivo *Function. JSON* :

Function. JSON de exemplo:

```json
{
  "type": "twilioSms",
  "name": "message",
  "accountSid": "TwilioAccountSid",
  "authToken": "TwilioAuthToken",
  "to": "+1704XXXXXXX",
  "from": "+1425XXXXXXX",
  "direction": "out",
  "body": "Azure Functions Testing"
}
```

Eis o código JavaScript:

```javascript
module.exports = function (context, myQueueItem) {
    context.log('Node.js queue trigger function processed work item', myQueueItem);

    // In this example the queue item is a JSON string representing an order that contains the name of a 
    // customer and a mobile number to send text updates to.
    var msg = "Hello " + myQueueItem.name + ", thank you for your order.";

    // Even if you want to use a hard coded message and number in the binding, you must at least 
    // initialize the message binding.
    context.bindings.message = {};

    // A dynamic message can be set instead of the body in the output binding. In this example, we use 
    // the order information to personalize a text message to the mobile number provided for
    // order status updates.
    context.bindings.message = {
        body : msg,
        to : myQueueItem.mobileNumber
    };

    context.done();
};
```

## <a name="example---functions-2x"></a>Exemplo – funções 2. x

Veja o exemplo de idioma específico:

* [2.xC#](#2x-c-example)
* [script 2. C# x (. CSX)](#2x-c-script-example)
* [2. x JavaScript](#2x-javascript-example)

### <a name="2x-c-example"></a>exemplo 2. C# x

O exemplo a seguir mostra uma [ C# função](functions-dotnet-class-library.md) que envia uma mensagem de texto quando disparada por uma mensagem da fila.

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Extensions.Logging;
using Newtonsoft.Json.Linq;
using Twilio.Rest.Api.V2010.Account;
using Twilio.Types;
namespace TwilioQueueOutput
{
    public static class QueueTwilio
    {
        [FunctionName("QueueTwilio")]
        [return: TwilioSms(AccountSidSetting = "TwilioAccountSid", AuthTokenSetting = "TwilioAuthToken", From = "+1425XXXXXXX")]
        public static CreateMessageOptions Run(
        [QueueTrigger("myqueue-items", Connection = "AzureWebJobsStorage")] JObject order,
        ILogger log)
        {
            log.LogInformation($"C# Queue trigger function processed: {order}");

            var message = new CreateMessageOptions(new PhoneNumber(order["mobileNumber"].ToString()))
            {
                Body = $"Hello {order["name"]}, thanks for your order!"
            };

            return message;
        }
    }
}
```

Este exemplo usa o `TwilioSms` atributo com o valor de retorno do método. Uma alternativa é usar o atributo com um `out CreateMessageOptions` parâmetro ou um `ICollector<CreateMessageOptions>` parâmetro ou `IAsyncCollector<CreateMessageOptions>` .

### <a name="2x-c-script-example"></a>exemplo de script C# 2. x

O exemplo a seguir mostra uma associação de saída twilio em um arquivo *Function. JSON* e uma [ C# função de script](functions-reference-csharp.md) que usa a associação. A função usa um `out` parâmetro para enviar uma mensagem de texto.

Aqui estão os dados de associação no arquivo *Function. JSON* :

Function. JSON de exemplo:

```json
{
  "type": "twilioSms",
  "name": "message",
  "accountSidSetting": "TwilioAccountSid",
  "authTokenSetting": "TwilioAuthToken",
  "from": "+1425XXXXXXX",
  "direction": "out",
  "body": "Azure Functions Testing"
}
```

Aqui está C# o código de script:

```cs
#r "Newtonsoft.Json"
#r "Twilio"
#r "Microsoft.Azure.WebJobs.Extensions.Twilio"

using System;
using Microsoft.Extensions.Logging;
using Newtonsoft.Json;
using Microsoft.Azure.WebJobs.Extensions.Twilio;
using Twilio.Rest.Api.V2010.Account;
using Twilio.Types;

public static void Run(string myQueueItem, out CreateMessageOptions message,  ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed: {myQueueItem}");

    // In this example the queue item is a JSON string representing an order that contains the name of a
    // customer and a mobile number to send text updates to.
    dynamic order = JsonConvert.DeserializeObject(myQueueItem);
    string msg = "Hello " + order.name + ", thank you for your order.";

    // You must initialize the CreateMessageOptions variable with the "To" phone number.
    message = new CreateMessageOptions(new PhoneNumber("+1704XXXXXXX"));

    // A dynamic message can be set instead of the body in the output binding. In this example, we use
    // the order information to personalize a text message.
    message.Body = msg;
}
```

Você não pode usar parâmetros de saída no código assíncrono. Este é um exemplo C# de código de script assíncrono:

```cs
#r "Newtonsoft.Json"
#r "Twilio"
#r "Microsoft.Azure.WebJobs.Extensions.Twilio"

using System;
using Microsoft.Extensions.Logging;
using Newtonsoft.Json;
using Microsoft.Azure.WebJobs.Extensions.Twilio;
using Twilio.Rest.Api.V2010.Account;
using Twilio.Types;

public static async Task Run(string myQueueItem, IAsyncCollector<CreateMessageOptions> message,  ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed: {myQueueItem}");

    // In this example the queue item is a JSON string representing an order that contains the name of a
    // customer and a mobile number to send text updates to.
    dynamic order = JsonConvert.DeserializeObject(myQueueItem);
    string msg = "Hello " + order.name + ", thank you for your order.";

    // You must initialize the CreateMessageOptions variable with the "To" phone number.
    CreateMessageOptions smsText = new CreateMessageOptions(new PhoneNumber("+1704XXXXXXX"));

    // A dynamic message can be set instead of the body in the output binding. In this example, we use
    // the order information to personalize a text message.
    smsText.Body = msg;

    await message.AddAsync(smsText);
}
```

### <a name="2x-javascript-example"></a>exemplo de JavaScript 2. x

O exemplo a seguir mostra uma associação de saída twilio em um arquivo *Function. JSON* e uma [função JavaScript](functions-reference-node.md) que usa a associação.

Aqui estão os dados de associação no arquivo *Function. JSON* :

Function. JSON de exemplo:

```json
{
  "type": "twilioSms",
  "name": "message",
  "accountSidSetting": "TwilioAccountSid",
  "authTokenSetting": "TwilioAuthToken",
  "from": "+1425XXXXXXX",
  "direction": "out",
  "body": "Azure Functions Testing"
}
```

Eis o código JavaScript:

```javascript
module.exports = function (context, myQueueItem) {
    context.log('Node.js queue trigger function processed work item', myQueueItem);

    // In this example the queue item is a JSON string representing an order that contains the name of a
    // customer and a mobile number to send text updates to.
    var msg = "Hello " + myQueueItem.name + ", thank you for your order.";

    // Even if you want to use a hard coded message in the binding, you must at least
    // initialize the message binding.
    context.bindings.message = {};

    // A dynamic message can be set instead of the body in the output binding. The "To" number 
    // must be specified in code. 
    context.bindings.message = {
        body : msg,
        to : myQueueItem.mobileNumber
    };

    context.done();
};
```

## <a name="attributes"></a>Atributos

Em [ C# bibliotecas de classes](functions-dotnet-class-library.md), use o atributo [TwilioSms](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.Twilio/TwilioSMSAttribute.cs) .

Para obter informações sobre as propriedades de atributo que você pode configurar, consulte [Configuration](#configuration). Aqui está um `TwilioSms` exemplo de atributo numa assinatura do método:

```csharp
[FunctionName("QueueTwilio")]
[return: TwilioSms(AccountSidSetting = "TwilioAccountSid", AuthTokenSetting = "TwilioAuthToken", From = "+1425XXXXXXX")]
public static CreateMessageOptions Run(
[QueueTrigger("myqueue-items", Connection = "AzureWebJobsStorage")] JObject order, ILogger log)
{
    ...
}
 ```

Para obter um exemplo completo, consulte [ C# exemplo](#c-example).

## <a name="configuration"></a>Configuração

A tabela seguinte explica as propriedades de configuração de ligação definida no *Function* ficheiro e o `TwilioSms` atributo.

| Propriedade de função. JSON v1 | Propriedade de função v2. JSON | Propriedade de atributo |Descrição|
|---------|---------|---------|----------------------|
|**type**|**type**| deve ser definido como `twilioSms`.|
|**direction**|**direction**| deve ser definido como `out`.|
|**name**|**name**| Nome da variável usada no código de função para a mensagem de texto SMS twilio. |
|**accountSid**|**accountSidSetting**| **AccountSidSetting**| Esse valor deve ser definido como o nome de uma configuração de aplicativo que contém o Sid de sua conta do twilio, por exemplo, TwilioAccountSid. Se não estiver definido, o nome da configuração do aplicativo padrão será "AzureWebJobsTwilioAccountSid". |
|**authToken**|**authTokenSetting**|**AuthTokenSetting**| Esse valor deve ser definido como o nome de uma configuração de aplicativo que contém o token de autenticação twilio, por exemplo, TwilioAccountAuthToken. Se não estiver definido, o nome da configuração do aplicativo padrão será "AzureWebJobsTwilioAuthToken". |
|**Para**| N/A-especificar no código | **To**| Esse valor é definido como o número de telefone para o qual o texto SMS é enviado.|
|**De**|**De** | **From**| Esse valor é definido como o número de telefone do qual o texto SMS é enviado.|
|**conteúdo**|**conteúdo** | **Corpo**| Esse valor pode ser usado para codificar a mensagem de texto SMS se você não precisar defini-la dinamicamente no código para sua função. |  

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Saiba mais sobre as funções do Azure acionadores e enlaces](functions-triggers-bindings.md)
