---
title: Funções Azure SendGrid
description: Referência de ligações Azure Functions SendGrid.
author: craigshoemaker
ms.topic: reference
ms.custom: devx-track-csharp
ms.date: 11/29/2017
ms.author: cshoe
ms.openlocfilehash: b3d09ec4c4ab578a87f0d983c0f243bee2a84597
ms.sourcegitcommit: 9889a3983b88222c30275fd0cfe60807976fd65b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2020
ms.locfileid: "94991235"
---
# <a name="azure-functions-sendgrid-bindings"></a>Funções Azure SendGrid

Este artigo explica como enviar e-mail usando as ligações [SendGrid](https://sendgrid.com/docs/User_Guide/index.html) em Funções Azure. A Azure Functions suporta uma ligação de saída para a SendGrid.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>Pacotes - Funções 1.x

As ligações SendGrid são fornecidas no pacote [Microsoft.Azure.WebJobs.Extensions.SendGrid](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.SendGrid) NuGet, versão 2.x. O código fonte para o pacote está no [repositório GitHub.](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions.SendGrid/)

[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="packages---functions-2x-and-higher"></a>Pacotes - Funções 2.x e superior

As ligações SendGrid são fornecidas no pacote [Microsoft.Azure.WebJobs.Extensions.SendGrid](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.SendGrid) NuGet, versão 3.x. O código fonte para o pacote está no [repositório GitHub.](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.SendGrid/)

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="example"></a>Exemplo

# <a name="c"></a>[C#](#tab/csharp)

O exemplo a seguir mostra uma [função C#](functions-dotnet-class-library.md) que utiliza um gatilho de fila de ônibus de serviço e uma ligação de saída SendGrid.

### <a name="synchronous"></a>Synchronous (Síncrono)

```cs
using SendGrid.Helpers.Mail;
using System.Text.Json;

...

[FunctionName("SendEmail")]
public static void Run(
    [ServiceBusTrigger("myqueue", Connection = "ServiceBusConnection")] Message email,
    [SendGrid(ApiKey = "CustomSendGridKeyAppSettingName")] out SendGridMessage message)
{
var emailObject = JsonSerializer.Deserialize<OutgoingEmail>(Encoding.UTF8.GetString(email.Body));

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
using SendGrid.Helpers.Mail;
using System.Text.Json;

...

[FunctionName("SendEmail")]
public static async Task Run(
 [ServiceBusTrigger("myqueue", Connection = "ServiceBusConnection")] Message email,
 [SendGrid(ApiKey = "CustomSendGridKeyAppSettingName")] IAsyncCollector<SendGridMessage> messageCollector)
{
 var emailObject = JsonSerializer.Deserialize<OutgoingEmail>(Encoding.UTF8.GetString(email.Body));

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

Pode omitir a definição da propriedade do atributo se tiver a `ApiKey` sua chave API numa definição de aplicação chamada "AzureWebJobsSendGridApiKey".

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

O exemplo a seguir mostra uma ligação de saída SendGrid numa *function.jsno* ficheiro e uma [função de script C#](functions-reference-csharp.md) que utiliza a ligação.

Aqui estão os dados vinculativos do *function.jsarquivado:*

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

A secção [de configuração](#configuration) explica estas propriedades.

Aqui está o código do guião C:

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

# <a name="javascript"></a>[JavaScript](#tab/javascript)

O exemplo a seguir mostra uma ligação de saída SendGrid numa *function.jsem* ficheiro e numa [função JavaScript](functions-reference-node.md) que utiliza a ligação.

Aqui estão os dados vinculativos do *function.jsarquivado:*

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

A secção [de configuração](#configuration) explica estas propriedades.

Aqui está o código JavaScript:

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

# <a name="python"></a>[Python](#tab/python)

O exemplo a seguir mostra uma função desencadeada por HTTP que envia um e-mail utilizando a ligação SendGrid. Pode fornecer valores predefinidos na configuração de encadernação. Por exemplo, o endereço *de* e-mail é configurado em *function.jsem*. 

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

A seguinte função mostra como pode fornecer valores personalizados para propriedades opcionais.

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

# <a name="java"></a>[Java](#tab/java)

O exemplo a seguir utiliza a `@SendGridOutput` anotação da biblioteca de [funções Java](/java/api/overview/azure/functions/runtime) para enviar um e-mail utilizando a ligação de saída SendGrid.

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

# <a name="c"></a>[C#](#tab/csharp)

Nas [bibliotecas de classe C,](functions-dotnet-class-library.md)utilize o atributo [SendGrid.](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.SendGrid/SendGridAttribute.cs)

Para obter informações sobre propriedades de atributos que possa configurar, consulte [Configuração](#configuration). Aqui está um `SendGrid` exemplo de atributo numa assinatura de método:

```csharp
[FunctionName("SendEmail")]
public static void Run(
    [ServiceBusTrigger("myqueue", Connection = "ServiceBusConnection")] OutgoingEmail email,
    [SendGrid(ApiKey = "CustomSendGridKeyAppSettingName")] out SendGridMessage message)
{
    ...
}
```

Para um exemplo completo, consulte [o exemplo C#](#example).

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

Os atributos não são suportados pelo Script C#.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Os atributos não são suportados pelo JavaScript.

# <a name="python"></a>[Python](#tab/python)

Os atributos não são suportados pela Python.

# <a name="java"></a>[Java](#tab/java)

A anotação [SendGridOutput](https://github.com/Azure/azure-functions-java-library/blob/master/src/main/java/com/microsoft/azure/functions/annotation/SendGridOutput.java) permite-lhe configurar declarativamente a ligação SendGrid fornecendo valores de configuração. Consulte as secções [de exemplo](#example) e [configuração](#configuration) para obter mais detalhes.

---

## <a name="configuration"></a>Configuração

A tabela que se segue lista as propriedades de configuração de encadernação disponíveis no  *function.jsno* ficheiro e o `SendGrid` atributo/anotação.

| *function.jsna* propriedade | Propriedade de atributo/anotação | Descrição | Opcional |
|--------------------------|-------------------------------|-------------|----------|
| tipo |n/a| Deve ser definido para `sendGrid` .| No |
| direção |n/a| Deve ser definido para `out` .| No |
| name |n/a| O nome variável utilizado no código de função para o órgão de pedido ou pedido. Este valor é `$return` quando há apenas um valor de retorno. | No |
| apiKey | ApiKey | O nome de uma definição de aplicação que contém a sua chave API. Se não for definido, o nome de definição de aplicação predefinido é *AzureWebJobsSendGridApiKey*.| No |
| para| Para | O endereço de e-mail do destinatário. | Yes |
| De| De | O endereço de e-mail do remetente. |  Yes |
| Assunto| Assunto | O assunto do e-mail. | Yes |
| texto| Texto | O conteúdo do e-mail. | Yes |

As propriedades opcionais podem ter valores predefinidos definidos na ligação e adicionados ou ultrapassados programáticamente.

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

<a name="host-json"></a>  

## <a name="hostjson-settings"></a>host.jsnas definições

Esta secção descreve as definições de configuração global disponíveis para esta ligação nas versões 2.x ou superiores. O exemplo host.jsno ficheiro abaixo contém apenas as definições da versão 2.x+ para esta ligação. Para obter mais informações sobre as configurações globais nas versões 2.x e não só, consulte [host.jsem referência para Funções Azure](functions-host-json.md).

> [!NOTE]
> Para obter uma referência de host.jsem Funções 1.x, consulte [host.jsem referência para as Funções Azure 1.x](functions-host-json-v1.md).

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
|De|n/a|O endereço de e-mail do remetente em todas as funções.| 


## <a name="next-steps"></a>Próximos passos

> [!div class="nextstepaction"]
> [Saiba mais sobre as funções Azure desencadeia e encaderna](functions-triggers-bindings.md)
