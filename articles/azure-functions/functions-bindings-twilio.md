---
title: Ligação Azure Funções Twilio
description: Entenda como usar encadernações Twilio com funções Azure.
author: craigshoemaker
ms.topic: reference
ms.date: 07/09/2018
ms.author: cshoe
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 1426d6e770cca566c4b77ca4742e2f8a0fbb5465
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/24/2020
ms.locfileid: "76715057"
---
# <a name="twilio-binding-for-azure-functions"></a>Ligação Twilio para funções azure

Este artigo explica como enviar mensagens de texto utilizando encadernações [Twilio](https://www.twilio.com/) em Funções Azure. A Azure Functions suporta encadernações de saída para o Twilio.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>Pacotes - funções 1.x

As encadernações Twilio são fornecidas no pacote [Microsoft.Azure.WebJobs.Extensions.Twilio](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Twilio) NuGet, versão 1.x. Código-fonte para o pacote está no [sdk do webjobs do azure](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions.Twilio/) repositório do GitHub.

[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="packages---functions-2x-and-higher"></a>Pacotes-funções 2. x e superior

As encadernações Twilio são fornecidas no pacote [Microsoft.Azure.WebJobs.Extensions.Twilio](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Twilio) NuGet, versão 3.x. Código-fonte para o pacote está no [sdk do webjobs do azure](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.Twilio/) repositório do GitHub.

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

<a id="example"></a>

## <a name="example---functions-2x-and-higher"></a>Exemplo - Funções 2.x e superior

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

O exemplo que se segue mostra uma [ C# função](functions-dotnet-class-library.md) que envia uma mensagem de texto quando desencadeada por uma mensagem de fila.

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

Este exemplo utiliza o atributo `TwilioSms` com o valor de retorno do método. Uma alternativa é usar o atributo com um parâmetro `out CreateMessageOptions` ou um parâmetro `ICollector<CreateMessageOptions>` ou `IAsyncCollector<CreateMessageOptions>`.

# <a name="c-scripttabcsharp-script"></a>[C#Prescritiva](#tab/csharp-script)

O exemplo seguinte mostra uma ligação de saída Twilio num ficheiro *function.json* e uma [ C# função de script](functions-reference-csharp.md) que utiliza a ligação. A função utiliza um parâmetro `out` para enviar uma mensagem de texto.

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

Aqui está C# o código do guião:

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

Não se pode usar parâmetros em código assíncrono. Aqui está um exemplo de C# código de script assíncrono:

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

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

O exemplo seguinte mostra uma ligação de saída Twilio num ficheiro *function.json* e uma [função JavaScript](functions-reference-node.md) que utiliza a ligação.

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

# <a name="pythontabpython"></a>[Python](#tab/python)

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

Pode passar um objeto JSON serializado ao parâmetro `func.Out` para enviar a mensagem SMS.

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

# <a name="javatabjava"></a>[Java](#tab/java)

O exemplo que se segue mostra como usar a anotação [TwilioSmsOutput](https://docs.microsoft.com/java/api/com.microsoft.azure.functions.annotation.twiliosmsoutput) para enviar uma mensagem SMS. Os valores para `to`, `from`, e `body` são exigidos na definição de atributo, mesmo que os sobrepores programáticamente.

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

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Nas [ C# bibliotecas de classes,](functions-dotnet-class-library.md)use o atributo [TwilioSms.](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.Twilio/TwilioSMSAttribute.cs)

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

Para obter um exemplo completo, consulte [ C# exemplo](#example).

# <a name="c-scripttabcsharp-script"></a>[C#Prescritiva](#tab/csharp-script)

O script não dá suporte C# a atributos.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Não há suporte para atributos pelo JavaScript.

# <a name="pythontabpython"></a>[Python](#tab/python)

Não há suporte para atributos no Python.

# <a name="javatabjava"></a>[Java](#tab/java)

Coloque a anotação [TwilioSmsOutput](https://docs.microsoft.com/java/api/com.microsoft.azure.functions.annotation.twiliosmsoutput) num parâmetro [de`OutputBinding<T>`](https://docs.microsoft.com/java/api/com.microsoft.azure.functions.outputbinding) onde `T` possa ser do tipo java nativo, como `int`, `String`, `byte[]`ou um tipo POJO.

---

## <a name="configuration"></a>Configuração

A tabela seguinte explica as propriedades de configuração de ligação definida no *Function* ficheiro e o `TwilioSms` atributo.

| v1 função.json propriedade | v2 fun.json propriedade | Propriedade de atributo |Descrição|
|---------|---------|---------|----------------------|
|**tipo**|**tipo**| deve ser definido para `twilioSms`.|
|**direção**|**direção**| deve ser definido para `out`.|
|**name**|**name**| Nome variável utilizado no código de função para a mensagem de texto SMS Twilio. |
|**accountSid**|**accountSidSetting**| **AccountSidSetting**| Este valor deve ser definido para o nome de uma definição de aplicação que detenha o seu Sid de Conta Twilio (`TwilioAccountSid`). Se não for definido, o nome de definição de aplicação padrão é "AzureWebJobsTwilioAccountSid". |
|**authToken**|**authTokenSetting**|**AuthTokenSetting**| Este valor deve ser definido para o nome de uma definição de aplicação que detenha o seu símbolo de autenticação Twilio (`TwilioAccountAuthToken`). Se não for definido, o nome de definição de aplicação padrão é "AzureWebJobsTwilioAuthToken". |
|**Para**| N/A - especificar em código | **Para**| Este valor é definido para o número de telefone para o que o texto SMS é enviado.|
|**De**|**De** | **From**| Este valor é definido para o número de telefone de que o texto SMS é enviado.|
|**conteúdo**|**conteúdo** | **Corpo**| Este valor pode ser usado para codificar duramente a mensagem de texto SMS se não precisar de o definir de forma dinâmica no código para a sua função. |  

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Saiba mais sobre as funções do Azure acionadores e enlaces](functions-triggers-bindings.md)
