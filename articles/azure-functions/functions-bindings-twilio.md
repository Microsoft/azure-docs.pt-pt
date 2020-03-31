---
title: Ligação Azure Funções Twilio
description: Entenda como usar encadernações Twilio com funções Azure.
author: craigshoemaker
ms.topic: reference
ms.date: 07/09/2018
ms.author: cshoe
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 1426d6e770cca566c4b77ca4742e2f8a0fbb5465
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76715057"
---
# <a name="twilio-binding-for-azure-functions"></a>Ligação Twilio para funções azure

Este artigo explica como enviar mensagens de texto utilizando encadernações [Twilio](https://www.twilio.com/) em Funções Azure. A Azure Functions suporta encadernações de saída para o Twilio.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>Pacotes - Funções 1.x

As encadernações Twilio são fornecidas no pacote [Microsoft.Azure.WebJobs.Extensions.Twilio](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Twilio) NuGet, versão 1.x. O código fonte para o pacote está no repositório [Azur-webjobs-sdk](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions.Twilio/) GitHub.

[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="packages---functions-2x-and-higher"></a>Pacotes - Funções 2.x e superiores

As encadernações Twilio são fornecidas no pacote [Microsoft.Azure.WebJobs.Extensions.Twilio](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Twilio) NuGet, versão 3.x. O código fonte para o pacote está no repositório [Azur-webjobs-sdk](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.Twilio/) GitHub.

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

<a id="example"></a>

## <a name="example---functions-2x-and-higher"></a>Exemplo - Funções 2.x e superior

# <a name="c"></a>[C #](#tab/csharp)

O exemplo seguinte mostra uma [função C#](functions-dotnet-class-library.md) que envia uma mensagem de texto quando desencadeada por uma mensagem de fila.

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

Este exemplo `TwilioSms` utiliza o atributo com o valor de retorno do método. Uma alternativa é usar o `out CreateMessageOptions` atributo com `ICollector<CreateMessageOptions>` `IAsyncCollector<CreateMessageOptions>` um parâmetro ou um parâmetro.

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

O exemplo seguinte mostra uma ligação de saída Twilio num ficheiro *function.json* e uma [função de script C#](functions-reference-csharp.md) que utiliza a ligação. A função `out` utiliza um parâmetro para enviar uma mensagem de texto.

Aqui estão os dados vinculativos no ficheiro *fun.json:*

Função exemplo.json:

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

Aqui está o código de script C#:

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

Não se pode usar parâmetros em código assíncrono. Aqui está um exemplo de código de script C# assíncrono:

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

# <a name="javascript"></a>[JavaScript](#tab/javascript)

O exemplo seguinte mostra uma ligação de saída Twilio num ficheiro *function.json* e uma [função JavaScript](functions-reference-node.md) que utiliza a ligação.

Aqui estão os dados vinculativos no ficheiro *fun.json:*

Função exemplo.json:

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

Aqui está o código JavaScript:

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

# <a name="python"></a>[Pitão](#tab/python)

O exemplo seguinte mostra como enviar uma mensagem SMS utilizando a ligação de saída tal como definida na *função.js*.

```json
    {
      "type": "twilioSms",
      "name": "twilioMessage",
      "accountSidSetting": "TwilioAccountSID",
      "authTokenSetting": "TwilioAuthToken",
      "from": "+1XXXXXXXXXX",
      "direction": "out",
      "body": "Azure Functions Testing"
    }
```

Pode passar um objeto JSON `func.Out` serializado ao parâmetro para enviar a mensagem SMS.

```python
import logging
import json
import azure.functions as func

def main(req: func.HttpRequest, twilioMessage: func.Out[str]) -> func.HttpResponse:

    message = req.params.get('message')
    to = req.params.get('to')

    value = {
      "body": message,
      "to": to
    }

    twilioMessage.set(json.dumps(value))

    return func.HttpResponse(f"Message sent")
```

# <a name="java"></a>[Java](#tab/java)

O exemplo que se segue mostra como usar a anotação [TwilioSmsOutput](https://docs.microsoft.com/java/api/com.microsoft.azure.functions.annotation.twiliosmsoutput) para enviar uma mensagem SMS. Valores `to` `from`para `body` , e são exigidos na definição de atributo, mesmo que os sobrepor programáticamente.

```java
package com.function;

import java.util.*;
import com.microsoft.azure.functions.annotation.*;
import com.microsoft.azure.functions.*;

public class TwilioOutput {

    @FunctionName("TwilioOutput")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req", methods = { HttpMethod.GET, HttpMethod.POST },
                authLevel = AuthorizationLevel.FUNCTION) HttpRequestMessage<Optional<String>> request,
            @TwilioSmsOutput(
                name = "twilioMessage",
                accountSid = "AzureWebJobsTwilioAccountSID",
                authToken = "AzureWebJobsTwilioAuthToken",
                to = "+1XXXXXXXXXX",
                body = "From Azure Functions",
                from = "+1XXXXXXXXXX") OutputBinding<String> twilioMessage,
            final ExecutionContext context) {

        String message = request.getQueryParameters().get("message");
        String to = request.getQueryParameters().get("to");

        StringBuilder builder = new StringBuilder()
            .append("{")
            .append("\"body\": \"%s\",")
            .append("\"to\": \"%s\"")
            .append("}");

        final String body = String.format(builder.toString(), message, to);

        twilioMessage.setValue(body);

        return request.createResponseBuilder(HttpStatus.OK).body("Message sent").build();
    }
}
```

---

## <a name="attributes-and-annotations"></a>Atributos e anotações

# <a name="c"></a>[C #](#tab/csharp)

Nas [bibliotecas da classe C#,](functions-dotnet-class-library.md)use o atributo [TwilioSms.](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.Twilio/TwilioSMSAttribute.cs)

Para obter informações sobre propriedades de atributo que possa configurar, consulte [Configuração](#configuration). Aqui está `TwilioSms` um exemplo de atributo numa assinatura de método:

```csharp
[FunctionName("QueueTwilio")]
[return: TwilioSms(AccountSidSetting = "TwilioAccountSid", AuthTokenSetting = "TwilioAuthToken", From = "+1425XXXXXXX")]
public static CreateMessageOptions Run(
[QueueTrigger("myqueue-items", Connection = "AzureWebJobsStorage")] JObject order, ILogger log)
{
    ...
}
 ```

Para um exemplo completo, consulte [c# exemplo](#example).

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

Os atributos não são suportados por C# Script.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Os atributos não são suportados pelo JavaScript.

# <a name="python"></a>[Pitão](#tab/python)

Os atributos não são suportados pela Python.

# <a name="java"></a>[Java](#tab/java)

Coloque a anotação [TwilioSmsOutput](https://docs.microsoft.com/java/api/com.microsoft.azure.functions.annotation.twiliosmsoutput) [`OutputBinding<T>`](https://docs.microsoft.com/java/api/com.microsoft.azure.functions.outputbinding) `T` num parâmetro onde possa `int`ser `String` `byte[]`qualquer tipo de Java nativo, como, ou um tipo POJO.

---

## <a name="configuration"></a>Configuração

A tabela a seguir explica as propriedades de configuração de ligação que definiu no ficheiro *função.json* e no `TwilioSms` atributo.

| v1 função.json propriedade | v2 fun.json propriedade | Propriedade de atributo |Descrição|
|---------|---------|---------|----------------------|
|**tipo**|**tipo**| deve ser `twilioSms`definido para .|
|**direção**|**direção**| deve ser `out`definido para .|
|**nome**|**nome**| Nome variável utilizado no código de função para a mensagem de texto SMS Twilio. |
|**contaSid**|**contaSidSetting**| **AccountSidSetting**| Este valor deve ser definido para o nome de uma definição`TwilioAccountSid`de aplicação que detenha o seu Sid de Conta Twilio (). Se não for definido, o nome de definição de aplicação padrão é "AzureWebJobsTwilioAccountSid". |
|**authToken**|**authTokenSetting**|**AuthTokenSetting**| Este valor deve ser definido para o nome de uma definição de`TwilioAccountAuthToken`aplicação que detenha o seu símbolo de autenticação Twilio ( ). Se não for definido, o nome de definição de aplicação padrão é "AzureWebJobsTwilioAuthToken". |
|**para**| N/A - especificar em código | **Para**| Este valor é definido para o número de telefone para o que o texto SMS é enviado.|
|**De**|**De** | **From**| Este valor é definido para o número de telefone de que o texto SMS é enviado.|
|**corpo**|**corpo** | **Corpo**| Este valor pode ser usado para codificar duramente a mensagem de texto SMS se não precisar de o definir de forma dinâmica no código para a sua função. |  

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Saiba mais sobre as funções azure gatilhos e encadernações](functions-triggers-bindings.md)
