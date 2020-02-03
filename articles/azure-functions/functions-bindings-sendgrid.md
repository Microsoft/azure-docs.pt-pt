---
title: Azure Functions associações SendGrid
description: Azure Functions referência de associações SendGrid.
author: craigshoemaker
ms.topic: reference
ms.date: 11/29/2017
ms.author: cshoe
ms.openlocfilehash: e318e5f9b192b9f857a0b97d076ce4cc87cfb73d
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/24/2020
ms.locfileid: "76710978"
---
# <a name="azure-functions-sendgrid-bindings"></a>Azure Functions associações SendGrid

Este artigo explica como enviar e-mail usando ligações [SendGrid](https://sendgrid.com/docs/User_Guide/index.html) em Funções Azure. Azure Functions dá suporte a uma associação de saída para SendGrid.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>Pacotes - funções 1.x

As encadernações SendGrid são fornecidas no pacote [Microsoft.Azure.WebJobs.Extensions.SendGrid](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.SendGrid) NuGet, versão 2.x. O código fonte para o pacote está no repositório [GitHub-extensões azure-webjobs-sdk.](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions.SendGrid/)

[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="packages---functions-2x-and-higher"></a>Pacotes-funções 2. x e superior

As encadernações SendGrid são fornecidas no pacote [Microsoft.Azure.WebJobs.Extensions.SendGrid](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.SendGrid) NuGet, versão 3.x. O código fonte para o pacote está no repositório [GitHub-extensões azure-webjobs-sdk.](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.SendGrid/)

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="example"></a>Exemplo

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

O exemplo seguinte mostra uma [ C# função](functions-dotnet-class-library.md) que utiliza um gatilho de fila de ônibus de serviço e uma ligação de saída SendGrid.

### <a name="synchronous"></a>Sincronizado

```cs
[FunctionName("SendEmail")]
public static void Run(
    [ServiceBusTrigger("myqueue", Connection = "ServiceBusConnection")] Message email,
    [SendGrid(ApiKey = "CustomSendGridKeyAppSettingName")] out SendGridMessage message)
{
var emailObject = JsonConvert.DeserializeObject<OutgoingEmail>(Encoding.UTF8.GetString(email.Body));

message = new SendGridMessage();
message.AddTo(emailObject.To);
message.AddContent("text/html", emailObject.Body);
message.SetFrom(new EmailAddress(emailObject.From));
message.SetSubject(emailObject.Subject);
}

public class OutgoingEmail
{
    public string To { get; set; }
    public string From { get; set; }
    public string Subject { get; set; }
    public string Body { get; set; }
}
```

### <a name="asynchronous"></a>Assíncrono

```cs
[FunctionName("SendEmail")]
public static async void Run(
 [ServiceBusTrigger("myqueue", Connection = "ServiceBusConnection")] Message email,
 [SendGrid(ApiKey = "CustomSendGridKeyAppSettingName")] IAsyncCollector<SendGridMessage> messageCollector)
{
 var emailObject = JsonConvert.DeserializeObject<OutgoingEmail>(Encoding.UTF8.GetString(email.Body));

 var message = new SendGridMessage();
 message.AddTo(emailObject.To);
 message.AddContent("text/html", emailObject.Body);
 message.SetFrom(new EmailAddress(emailObject.From));
 message.SetSubject(emailObject.Subject);
 
 await messageCollector.AddAsync(message);
}

public class OutgoingEmail
{
 public string To { get; set; }
 public string From { get; set; }
 public string Subject { get; set; }
 public string Body { get; set; }
}
```

Pode omiti-lo configurar a propriedade `ApiKey` do atributo se tiver a sua chave API numa definição de aplicação chamada "AzureWebJobsSendGridApiKey".

# <a name="c-scripttabcsharp-script"></a>[C#Roteiro](#tab/csharp-script)

O exemplo seguinte mostra uma ligação de saída SendGrid num ficheiro *function.json* e uma [ C# função de script](functions-reference-csharp.md) que utiliza a ligação.

Aqui estão os dados vinculativos no ficheiro *função.json:*

```json 
{
    "bindings": [
        {
          "type": "queueTrigger",
          "name": "mymsg",
          "queueName": "myqueue",
          "connection": "AzureWebJobsStorage",
          "direction": "in"
        },
        {
          "type": "sendGrid",
          "name": "$return",
          "direction": "out",
          "apiKey": "SendGridAPIKeyAsAppSetting",
          "from": "{FromEmail}",
          "to": "{ToEmail}"
        }
    ]
}
```

A secção de [configuração](#configuration) explica estas propriedades.

Aqui está o código de script do c#:

```csharp
#r "SendGrid"

using System;
using SendGrid.Helpers.Mail;
using Microsoft.Azure.WebJobs.Host;

public static SendGridMessage Run(Message mymsg, ILogger log)
{
    SendGridMessage message = new SendGridMessage()
    {
        Subject = $"{mymsg.Subject}"
    };
    
    message.AddContent("text/plain", $"{mymsg.Content}");

    return message;
}
public class Message
{
    public string ToEmail { get; set; }
    public string FromEmail { get; set; }
    public string Subject { get; set; }
    public string Content { get; set; }
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

O exemplo seguinte mostra uma ligação de saída SendGrid num ficheiro *function.json* e uma [função JavaScript](functions-reference-node.md) que utiliza a ligação.

Aqui estão os dados vinculativos no ficheiro *função.json:*

```json 
{
    "bindings": [
        {
            "name": "$return",
            "type": "sendGrid",
            "direction": "out",
            "apiKey" : "MySendGridKey",
            "to": "{ToEmail}",
            "from": "{FromEmail}",
            "subject": "SendGrid output bindings"
        }
    ]
}
```

A secção de [configuração](#configuration) explica estas propriedades.

Eis o código JavaScript:

```javascript
module.exports = function (context, input) {
    var message = {
         "personalizations": [ { "to": [ { "email": "sample@sample.com" } ] } ],
        from: { email: "sender@contoso.com" },
        subject: "Azure news",
        content: [{
            type: 'text/plain',
            value: input
        }]
    };

    context.done(null, message);
};
```

# <a name="pythontabpython"></a>[python](#tab/python)

O exemplo a seguir mostra uma função disparada por HTTP que envia um email usando a associação SendGrid. Você pode fornecer valores padrão na configuração de associação. Por exemplo, o endereço de e-mail *a partir de* e-mail está configurado em *função.json*. 

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "type": "httpTrigger",
      "authLevel": "function",
      "direction": "in",
      "name": "req",
      "methods": ["get", "post"]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    },
    {
      "type": "sendGrid",
      "name": "sendGridMessage",
      "direction": "out",
      "apiKey": "SendGrid_API_Key",
      "from": "sender@contoso.com"
    }
  ]
}
```

A função a seguir mostra como você pode fornecer valores personalizados para propriedades opcionais.

```python
import logging
import json
import azure.functions as func

def main(req: func.HttpRequest, sendGridMessage: func.Out[str]) -> func.HttpResponse:

    value = "Sent from Azure Functions"

    message = {
        "personalizations": [ {
          "to": [{
            "email": "user@contoso.com"
            }]}],
        "subject": "Azure Functions email with SendGrid",
        "content": [{
            "type": "text/plain",
            "value": value }]}

    sendGridMessage.set(json.dumps(message))

    return func.HttpResponse(f"Sent")
```

# <a name="javatabjava"></a>[Java](#tab/java)

O exemplo seguinte utiliza a `@SendGridOutput` anotação da biblioteca de [tempo de funcionamento](/java/api/overview/azure/functions/runtime) das funções Java para enviar um e-mail utilizando a ligação de saída sendGrid.

```java
package com.function;

import java.util.*;
import com.microsoft.azure.functions.annotation.*;
import com.microsoft.azure.functions.*;

public class HttpTriggerSendGrid {

    @FunctionName("HttpTriggerSendGrid")
    public HttpResponseMessage run(

        @HttpTrigger(
            name = "req",
            methods = { HttpMethod.GET, HttpMethod.POST },
            authLevel = AuthorizationLevel.FUNCTION)
                HttpRequestMessage<Optional<String>> request,

        @SendGridOutput(
            name = "message",
            dataType = "String",
            apiKey = "SendGrid_API_Key",
            to = "user@contoso.com",
            from = "sender@contoso.com",
            subject = "Azure Functions email with SendGrid",
            text = "Sent from Azure Functions")
                OutputBinding<String> message,

        final ExecutionContext context) {

        final String toAddress = "user@contoso.com";
        final String value = "Sent from Azure Functions";

        StringBuilder builder = new StringBuilder()
            .append("{")
            .append("\"personalizations\": [{ \"to\": [{ \"email\": \"%s\"}]}],")
            .append("\"content\": [{\"type\": \"text/plain\", \"value\": \"%s\"}]")
            .append("}");

        final String body = String.format(builder.toString(), toAddress, value);

        message.setValue(body);

        return request.createResponseBuilder(HttpStatus.OK).body("Sent").build();
    }
}
```

---

## <a name="attributes-and-annotations"></a>Atributos e anotações

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Nas [ C# bibliotecas de classes,](functions-dotnet-class-library.md)utilize o atributo [SendGrid.](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.SendGrid/SendGridAttribute.cs)

Para obter informações sobre propriedades de atributo que possa configurar, consulte [Configuração](#configuration). Aqui está um exemplo de atributo `SendGrid` numa assinatura de método:

```csharp
[FunctionName("SendEmail")]
public static void Run(
    [ServiceBusTrigger("myqueue", Connection = "ServiceBusConnection")] OutgoingEmail email,
    [SendGrid(ApiKey = "CustomSendGridKeyAppSettingName")] out SendGridMessage message)
{
    ...
}
```

Para um exemplo [ C# ](#example)completo, consulte o exemplo .

# <a name="c-scripttabcsharp-script"></a>[C#Roteiro](#tab/csharp-script)

O script não dá suporte C# a atributos.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Não há suporte para atributos pelo JavaScript.

# <a name="pythontabpython"></a>[python](#tab/python)

Não há suporte para atributos no Python.

# <a name="javatabjava"></a>[Java](#tab/java)

A anotação [SendGridOutput](https://github.com/Azure/azure-functions-java-library/blob/master/src/main/java/com/microsoft/azure/functions/annotation/SendGridOutput.java) permite-lhe configurar declarativamente a ligação SendGrid fornecendo valores de configuração. Consulte o [exemplo](#example) e as secções de [configuração](#configuration) para obter mais detalhes.

---

## <a name="configuration"></a>Configuração

A tabela seguinte lista as propriedades de configuração de ligação disponíveis no ficheiro *função.json* e o `SendGrid` atributo/anotação.

| *propriedade fun.json* | Propriedade de atributo/anotação | Descrição | Opcional |
|--------------------------|-------------------------------|-------------|----------|
| tipo |n/d| deve ser definido para `sendGrid`.| Não |
| direction |n/d| deve ser definido para `out`.| Não |
| nome |n/d| O nome da variável usada no código de função para a solicitação ou o corpo da solicitação. Este valor é `$return` quando há apenas um valor de retorno. | Não |
| apiKey | apiKey | O nome de uma configuração de aplicativo que contém sua chave de API. Se não for definido, o nome de definição de aplicação padrão é *AzureWebJobsSendGridApiKey*.| Não |
| para| Para | O endereço de email do destinatário. | Sim |
| from| De | O endereço de email do remetente. |  Sim |
| subject| Assunto | O assunto do email. | Sim |
| text| Texto | O conteúdo do email. | Sim |

As propriedades opcionais podem ter valores padrão definidos na associação e adicionadas ou substituídas programaticamente.

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

<a name="host-json"></a>  

## <a name="hostjson-settings"></a>definições de Host. JSON

Esta seção descreve as definições de configuração global disponíveis para essa associação nas versões 2. x e superior. O arquivo host. JSON de exemplo abaixo contém apenas as configurações da versão 2. x + para essa associação. Para obter mais informações sobre as configurações globais de configuração nas versões 2.x e além, consulte a [referência host.json para funções azure](functions-host-json.md).

> [!NOTE]
> Para uma referência do host.json nas funções 1.x, consulte a [referência host.json para as funções Azure 1.x](functions-host-json-v1.md).

```json
{
    "version": "2.0",
    "extensions": {
        "sendGrid": {
            "from": "Azure Functions <samples@functions.com>"
        }
    }
}
```  

|Propriedade  |Predefinição | Descrição |
|---------|---------|---------| 
|from|n/d|O endereço de email do remetente em todas as funções.| 


## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Saiba mais sobre as funções azure gatilhos e encadernações](functions-triggers-bindings.md)
