---
title: Enlaces do SendGrid de funções do Azure
description: Referência de enlaces do SendGrid de funções do Azure.
services: functions
documentationcenter: na
author: craigshoemaker
manager: jeconnoc
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 11/29/2017
ms.author: cshoe
ms.openlocfilehash: 663283c4dff16194dea78afbf5dd393f1fa833b1
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56823812"
---
# <a name="azure-functions-sendgrid-bindings"></a>Enlaces do SendGrid de funções do Azure

Este artigo explica como enviar e-mails através da utilização [SendGrid](https://sendgrid.com/docs/User_Guide/index.html) enlaces no funções do Azure. As funções do Azure suporta um enlace de saída para o SendGrid.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>Pacotes - funções 1.x

Os enlaces do SendGrid são fornecidos na [Microsoft.Azure.WebJobs.Extensions.SendGrid](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.SendGrid) pacote NuGet, versão 2.x. Código-fonte para o pacote está no [azure-webjobs-sdk-extensões](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions.SendGrid/) repositório do GitHub.

[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="packages---functions-2x"></a>Pacotes - funções 2.x

Os enlaces do SendGrid são fornecidos na [Microsoft.Azure.WebJobs.Extensions.SendGrid](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.SendGrid) pacote NuGet, versão 3.x. Código-fonte para o pacote está no [azure-webjobs-sdk-extensões](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.SendGrid/) repositório do GitHub.

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="example"></a>Exemplo

Veja o exemplo de idioma específico:

* [C#](#c-example)
* [Script do c# (.csx)](#c-script-example)
* [JavaScript](#javascript-example)
* [Java](#java-example)

### <a name="c-example"></a>Exemplo do c#

A exemplo a seguir mostra um [função c#](functions-dotnet-class-library.md) que acionar a uma fila do Service Bus utiliza e enlace de saída de um SendGrid.

#### <a name="synchronous-c-example"></a>Síncrona C# exemplo:

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
#### <a name="asynchronous-c-example"></a>Assíncrona C# exemplo:

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

Pode omitir a definir o atributo `ApiKey` propriedade se tiver a chave de API numa definição de aplicação com o nome "AzureWebJobsSendGridApiKey".

### <a name="c-script-example"></a>Exemplo de script do c#

O exemplo seguinte mostra uma saída de SendGrid ligando uma *Function* ficheiro e uma [função de script do c#](functions-reference-csharp.md) que utiliza o enlace.

Eis a vinculação de dados a *Function* ficheiro:

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

O [configuração](#configuration) seção explica essas propriedades.

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

### <a name="java-example"></a>Exemplo de Java

O exemplo seguinte utiliza a `@SendGridOutput` anotação do [biblioteca de tempo de execução de funções do Java](/java/api/overview/azure/functions/runtime) enviar um e-mail com o SendGrid enlace de saída.

```java
@FunctionName("SendEmail")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req", methods = {HttpMethod.GET, HttpMethod.POST}, authLevel = AuthorizationLevel.FUNCTION) HttpRequestMessage<Optional<String>> request,
            @SendGridOutput(
                name = "email", dataType = "String", apiKey = "SendGridConnection", to = "test@example.com", from = "test@example.com",
                subject= "Sending with SendGrid", text = "Hello from Azure Functions"
                ) OutputBinding<String> email
            )
    {
        String name = request.getBody().orElse("World");

        final String emailBody = "{\"personalizations\":" +
                                    "[{\"to\":[{\"email\":\"test@example.com\"}]," +
                                    "\"subject\":\"Sending with SendGrid\"}]," +
                                    "\"from\":{\"email\":\"test@example.com\"}," +
                                    "\"content\":[{\"type\":\"text/plain\",\"value\": \"Hello" + name + "\"}]}";

        email.setValue(emailBody);
        return request.createResponseBuilder(HttpStatus.OK).body("Hello, " + name).build();
    }
```

### <a name="javascript-example"></a>Exemplo de JavaScript

O exemplo seguinte mostra uma saída de SendGrid ligando um *Function* ficheiro e uma [função JavaScript](functions-reference-node.md) que utiliza o enlace.

Eis a vinculação de dados a *Function* ficheiro:

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

O [configuração](#configuration) seção explica essas propriedades.

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

## <a name="attributes"></a>Atributos

Na [bibliotecas de classes do c#](functions-dotnet-class-library.md), utilize o [SendGrid](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.SendGrid/SendGridAttribute.cs) atributo.

Para obter informações sobre as propriedades do atributo que pode configurar, consulte [configuração](#configuration). Aqui está um `SendGrid` exemplo de atributo numa assinatura do método:

```csharp
[FunctionName("SendEmail")]
public static void Run(
    [ServiceBusTrigger("myqueue", Connection = "ServiceBusConnection")] OutgoingEmail email,
    [SendGrid(ApiKey = "CustomSendGridKeyAppSettingName")] out SendGridMessage message)
{
    ...
}
```

Para obter um exemplo completo, consulte [exemplo c#](#c-example).

## <a name="configuration"></a>Configuração

A tabela seguinte explica as propriedades de configuração de ligação definida no *Function* ficheiro e o `SendGrid` atributo.

|propriedade de Function | Propriedade de atributo |Descrição|
|---------|---------|----------------------|
|**tipo**|| Necessário - tem de ser definido como `sendGrid`.|
|**direção**|| Necessário - tem de ser definido como `out`.|
|**name**|| Necessário - o nome da variável no código de função para a pedido ou corpo do pedido. Este valor é ```$return``` quando existe apenas um valor de retorno. |
|**apiKey**|**ApiKey**| O nome de uma definição de aplicação que contém a chave de API. Se não conjunto, a definição de aplicação predefinido o nome é "AzureWebJobsSendGridApiKey".|
|**to**|**Para**| endereço de e-mail do destinatário. |
|**from**|**De**| endereço de e-mail do remetente. |
|**subject**|**Assunto**| o assunto do e-mail. |
|**text**|**Text** (Texto)| o conteúdo de e-mail. |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

<a name="host-json"></a>  

## <a name="hostjson-settings"></a>definições de Host. JSON

Esta secção descreve as definições de configuração global disponíveis para essa ligação na versão 2.x. O ficheiro de Host. JSON de exemplo abaixo contém apenas as versão 2.x as definições para este enlace. Para obter mais informações sobre as definições de configuração global na versão 2.x, consulte [referência de Host. JSON para a versão das funções do Azure 2.x](functions-host-json.md).

> [!NOTE]
> Para obter uma referência de Host. JSON nas funções 1.x, consulte [referência de Host. JSON para as funções do Azure 1.x](functions-host-json-v1.md).

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
|de|n/d|Endereço de e-mail do remetente em todas as funções.| 


## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Saiba mais sobre as funções do Azure acionadores e enlaces](functions-triggers-bindings.md)
