---
title: Enlaces de Hubs de notificação para as funções do Azure
description: Aprenda a usar a ligação do Hub de notificação do Azure nas funções do Azure.
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
ms.openlocfilehash: 8ab1ae5d1f6db563caef5b6c27ccd4fcbbd2e255
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57777639"
---
# <a name="notification-hubs-output-binding-for-azure-functions"></a>Ligação para as funções do Azure de saída dos Hubs de notificação

Este artigo explica como enviar notificações push com [os Hubs de notificação do Azure](../notification-hubs/notification-hubs-push-notification-overview.md) enlaces no funções do Azure. Azure funções suporta enlaces de saída para os Hubs de notificação.

Os Hubs de notificação do Azure tem de ser configurados para o serviço de plataforma notificações (PNS) que pretende utilizar. Para saber como obter notificações push na sua aplicação de cliente dos Hubs de notificação, veja [introdução aos Hubs de notificação](../notification-hubs/notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md) e selecionar a plataforma de cliente de destino na lista pendente junto à parte superior da página.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>Pacotes - funções 1.x

Os enlaces de Hubs de notificação são fornecidos na [Microsoft.Azure.WebJobs.Extensions.NotificationHubs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.NotificationHubs) pacote NuGet, versão 1.x. Código-fonte para o pacote está no [azure-webjobs-sdk-extensões](https://github.com/Azure/azure-webjobs-sdk-extensions/tree/v2.x/src/WebJobs.Extensions.NotificationHubs) repositório do GitHub.

[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="packages---functions-2x"></a>Pacotes - funções 2.x

Essa ligação não está disponível nas funções 2.x.

## <a name="example---template"></a>Exemplo - modelo

As notificações que envia podem ser notificações nativas ou [notificações de modelo](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md). Notificações nativas direcionar uma plataforma de cliente específico, conforme configurado no `platform` propriedade do enlace de saída. Uma notificação de modelo pode ser utilizada para várias plataformas de destino.   

Veja o exemplo de idioma específico:

* [Script c# - parâmetro out](#c-script-template-example---out-parameter)
* [Script c# - assíncrona](#c-script-template-example---asynchronous)
* [Script c# - JSON](#c-script-template-example---json)
* [Script c# - tipos de biblioteca](#c-script-template-example---library-types)
* [F#](#f-template-example)
* [JavaScript](#javascript-template-example)

### <a name="c-script-template-example---out-parameter"></a>C# exemplo de modelo de script - parâmetro out

Neste exemplo envia uma notificação um [registo de modelo](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md) que contém um `message` marcador de posição no modelo.

```cs
using System;
using System.Threading.Tasks;
using System.Collections.Generic;

public static void Run(string myQueueItem,  out IDictionary<string, string> notification, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    notification = GetTemplateProperties(myQueueItem);
}

private static IDictionary<string, string> GetTemplateProperties(string message)
{
    Dictionary<string, string> templateProperties = new Dictionary<string, string>();
    templateProperties["message"] = message;
    return templateProperties;
}
```

### <a name="c-script-template-example---asynchronous"></a>C# modelo exemplo do script - assíncrono

Se estiver a utilizar o código assíncrono, parâmetros de saída não são permitidas. Neste caso utilize `IAsyncCollector` para retornar a notificação de modelo. O código a seguir é um exemplo assíncrono do código acima. 

```cs
using System;
using System.Threading.Tasks;
using System.Collections.Generic;

public static async Task Run(string myQueueItem, IAsyncCollector<IDictionary<string,string>> notification, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");

    log.Info($"Sending Template Notification to Notification Hub");
    await notification.AddAsync(GetTemplateProperties(myQueueItem));    
}

private static IDictionary<string, string> GetTemplateProperties(string message)
{
    Dictionary<string, string> templateProperties = new Dictionary<string, string>();
    templateProperties["user"] = "A new user wants to be added : " + message;
    return templateProperties;
}
```

### <a name="c-script-template-example---json"></a>C# script modelo JSON de exemplo-

Neste exemplo envia uma notificação um [registo de modelo](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md) que contém um `message` marcador de posição no modelo usando uma cadeia de caracteres do JSON válida.

```cs
using System;

public static void Run(string myQueueItem,  out string notification, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    notification = "{\"message\":\"Hello from C#. Processed a queue item!\"}";
}
```

### <a name="c-script-template-example---library-types"></a>C# exemplo de modelo script - tipos de biblioteca

Este exemplo mostra como usar tipos definidos na [biblioteca de Hubs de notificação do Microsoft Azure](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/). 

```cs
#r "Microsoft.Azure.NotificationHubs"

using System;
using System.Threading.Tasks;
using Microsoft.Azure.NotificationHubs;

public static void Run(string myQueueItem,  out Notification notification, TraceWriter log)
{
   log.Info($"C# Queue trigger function processed: {myQueueItem}");
   notification = GetTemplateNotification(myQueueItem);
}

private static TemplateNotification GetTemplateNotification(string message)
{
    Dictionary<string, string> templateProperties = new Dictionary<string, string>();
    templateProperties["message"] = message;
    return new TemplateNotification(templateProperties);
}
```

### <a name="f-template-example"></a>F#exemplo de modelo

Neste exemplo envia uma notificação um [registo de modelo](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md) que contém `location` e `message`.

```fsharp
let Run(myTimer: TimerInfo, notification: byref<IDictionary<string, string>>) =
    notification = dict [("location", "Redmond"); ("message", "Hello from F#!")]
```

### <a name="javascript-template-example"></a>Exemplo de modelo do JavaScript

Neste exemplo envia uma notificação um [registo de modelo](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md) que contém `location` e `message`.

```javascript
module.exports = function (context, myTimer) {
    var timeStamp = new Date().toISOString();

    if (myTimer.IsPastDue)
    {
        context.log('Node.js is running late!');
    }
    context.log('Node.js timer trigger function ran!', timeStamp);  
    context.bindings.notification = {
        location: "Redmond",
        message: "Hello from Node!"
    };
    context.done();
};
```

## <a name="example---apns-native"></a>Exemplo - nativo do APNS

Este exemplo de script do c# mostra como enviar uma notificação de APNS nativa. 

```cs
#r "Microsoft.Azure.NotificationHubs"
#r "Newtonsoft.Json"

using System;
using Microsoft.Azure.NotificationHubs;
using Newtonsoft.Json;

public static async Task Run(string myQueueItem, IAsyncCollector<Notification> notification, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");

    // In this example the queue item is a new user to be processed in the form of a JSON string with 
    // a "name" value.
    //
    // The JSON format for a native APNS notification is ...
    // { "aps": { "alert": "notification message" }}  

    log.LogInformation($"Sending APNS notification of a new user");    
    dynamic user = JsonConvert.DeserializeObject(myQueueItem);    
    string apnsNotificationPayload = "{\"aps\": {\"alert\": \"A new user wants to be added (" + 
                                        user.name + ")\" }}";
    log.LogInformation($"{apnsNotificationPayload}");
    await notification.AddAsync(new AppleNotification(apnsNotificationPayload));        
}
```

## <a name="example---gcm-native"></a>Exemplo - nativo do GCM

Este exemplo de script do c# mostra como enviar uma notificação de GCM nativa. 

```cs
#r "Microsoft.Azure.NotificationHubs"
#r "Newtonsoft.Json"

using System;
using Microsoft.Azure.NotificationHubs;
using Newtonsoft.Json;

public static async Task Run(string myQueueItem, IAsyncCollector<Notification> notification, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");

    // In this example the queue item is a new user to be processed in the form of a JSON string with 
    // a "name" value.
    //
    // The JSON format for a native GCM notification is ...
    // { "data": { "message": "notification message" }}  

    log.Info($"Sending GCM notification of a new user");    
    dynamic user = JsonConvert.DeserializeObject(myQueueItem);    
    string gcmNotificationPayload = "{\"data\": {\"message\": \"A new user wants to be added (" + 
                                        user.name + ")\" }}";
    log.Info($"{gcmNotificationPayload}");
    await notification.AddAsync(new GcmNotification(gcmNotificationPayload));        
}
```

## <a name="example---wns-native"></a>Exemplo - WNS nativo

Este exemplo de script do c# mostra como usar tipos definidos na [biblioteca de Hubs de notificação do Microsoft Azure](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/) para enviar uma notificação de alerta do WNS nativa. 

```cs
#r "Microsoft.Azure.NotificationHubs"
#r "Newtonsoft.Json"

using System;
using Microsoft.Azure.NotificationHubs;
using Newtonsoft.Json;

public static async Task Run(string myQueueItem, IAsyncCollector<Notification> notification, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");

    // In this example the queue item is a new user to be processed in the form of a JSON string with 
    // a "name" value.
    //
    // The XML format for a native WNS toast notification is ...
    // <?xml version="1.0" encoding="utf-8"?>
    // <toast>
    //      <visual>
    //     <binding template="ToastText01">
    //       <text id="1">notification message</text>
    //     </binding>
    //   </visual>
    // </toast>

    log.Info($"Sending WNS toast notification of a new user");    
    dynamic user = JsonConvert.DeserializeObject(myQueueItem);    
    string wnsNotificationPayload = "<?xml version=\"1.0\" encoding=\"utf-8\"?>" +
                                    "<toast><visual><binding template=\"ToastText01\">" +
                                        "<text id=\"1\">" + 
                                            "A new user wants to be added (" + user.name + ")" + 
                                        "</text>" +
                                    "</binding></visual></toast>";

    log.Info($"{wnsNotificationPayload}");
    await notification.AddAsync(new WindowsNotification(wnsNotificationPayload));        
}
```

## <a name="attributes"></a>Atributos

Na [bibliotecas de classes do c#](functions-dotnet-class-library.md), utilize o [NotificationHub](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions.NotificationHubs/NotificationHubAttribute.cs) atributo.

O atributo parâmetros do construtor e propriedades são descritas na [configuração](#configuration) secção.

## <a name="configuration"></a>Configuração

A tabela seguinte explica as propriedades de configuração de ligação definida no *Function* ficheiro e o `NotificationHub` atributo:

|propriedade de Function | Propriedade de atributo |Descrição|
|---------|---------|----------------------|
|**tipo** |n/d| Tem de ser definido para "notificationHub". |
|**direção** |n/d| Tem de ser definido para "Sair". | 
|**name** |n/d| Nome da variável no código de função para a mensagem do hub de notificação. |
|**tagExpression** |**TagExpression** | Expressões de etiqueta permitem que especifique que notificações ser entregue a um conjunto de dispositivos que se registraram para receber notificações que correspondem à expressão de etiqueta.  Para obter mais informações, consulte [expressões de encaminhamento e a etiqueta](../notification-hubs/notification-hubs-tags-segment-push-message.md). |
|**hubName** | **HubName** | Nome do recurso de hub de notificação no portal do Azure. |
|**ligação** | **ConnectionStringSetting** | O nome de uma definição de aplicação que contém uma cadeia de ligação de Hubs de notificação.  A cadeia de ligação tem de ser definida o *DefaultFullSharedAccessSignature* valor para o seu hub de notificação. Ver [configuração da cadeia de ligação](#connection-string-setup) mais adiante neste artigo.|
|**platform** | **Plataforma** | A propriedade de plataforma indica a plataforma de cliente seus destinos de notificação. Por predefinição, se a propriedade platform é omitida do enlace de saída, notificações de modelo podem ser utilizadas para destinar qualquer plataforma configurada no Hub de notificação do Azure. Para obter mais informações sobre como utilizar os modelos em geral para enviar cruzada notificações de plataforma com um Hub de notificação do Azure, consulte [modelos](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md). Quando definida **plataforma** tem de ser um dos seguintes valores: <ul><li><code>apns</code>&mdash;Serviço Apple Push Notification. Para obter mais informações sobre a configuração do hub de notificação para APNS e receber a notificação num aplicativo cliente, consulte [enviando notificações push para dispositivos iOS com Notification Hubs do Azure](../notification-hubs/notification-hubs-ios-apple-push-notification-apns-get-started.md).</li><li><code>adm</code>&mdash;[Amazon Device Messaging](https://developer.amazon.com/device-messaging). Para obter mais informações sobre a configuração do hub de notificação para ADM e receber a notificação numa aplicação Kindle, consulte [introdução aos Hubs de notificação para aplicações Kindle](../notification-hubs/notification-hubs-kindle-amazon-adm-push-notification.md).</li><li><code>gcm</code>&mdash;[Google Cloud Messaging](https://developers.google.com/cloud-messaging/). Firebase Cloud Messaging, que é a nova versão do GCM, também é suportado. Para obter mais informações, consulte [enviando notificações push para o Android com Hubs de notificação do Azure](../notification-hubs/notification-hubs-android-push-notification-google-fcm-get-started.md).</li><li><code>wns</code>&mdash;[Windows Push Notification Services](https://msdn.microsoft.com/windows/uwp/controls-and-patterns/tiles-and-notifications-windows-push-notification-services--wns--overview) visando plataformas do Windows. Windows Phone 8.1 e posterior, também é suportado pelo WNS. Para obter mais informações, consulte [introdução ao Hubs de notificação para o Windows Universal plataforma aplicações](../notification-hubs/notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md).</li><li><code>mpns</code>&mdash;[O serviço de notificações Push da Microsoft](https://msdn.microsoft.com/library/windows/apps/ff402558.aspx). Esta plataforma oferece suporte a Windows Phone 8 e plataformas anteriores do Windows Phone. Para obter mais informações, consulte [Sending as notificações push com Hubs de notificação do Azure no Windows Phone](../notification-hubs/notification-hubs-windows-mobile-push-notifications-mpns.md).</li></ul> |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

### <a name="functionjson-file-example"></a>exemplo de ficheiro Function

Eis um exemplo de uma ligação de Hubs de notificação numa *Function* ficheiro.

```json
{
  "bindings": [
    {
      "type": "notificationHub",
      "direction": "out",
      "name": "notification",
      "tagExpression": "",
      "hubName": "my-notification-hub",
      "connection": "MyHubConnectionString",
      "platform": "gcm"
    }
  ],
  "disabled": false
}
```

### <a name="connection-string-setup"></a>Configuração de cadeia de ligação

Para utilizar um enlace de saída do hub notificação, tem de configurar a cadeia de ligação do hub. Pode selecionar um hub de notificação existente ou criar um novo um direito do *integrar* separador no portal do Azure. Também pode configurar a cadeia de ligação manualmente. 

Para configurar a cadeia de ligação para um hub de notificação existente:

1. Navegue para o hub de notificação no [portal do Azure](https://portal.azure.com), escolha **políticas de acesso**e selecione o botão Copiar junto aos **DefaultFullSharedAccessSignature** política. Esta ação copia a cadeia de ligação para o *DefaultFullSharedAccessSignature* política ao seu hub de notificação. Esta cadeia de ligação permite que a sua função de enviar mensagens de notificação para o hub.
    ![Copie a cadeia de ligação do hub de notificação](./media/functions-bindings-notification-hubs/get-notification-hub-connection.png)
1. Navegue para a aplicação de função no portal do Azure, escolha **as definições da aplicação**, adicione uma chave, como **MyHubConnectionString**, cole o texto copiado *DefaultFullSharedAccessSignature* para o seu hub de notificação, como o valor e, em seguida, clique **guardar**.

O nome desta definição de aplicação é o que acontece na definição de ligação de enlace de saída na *Function* ou o atributo de .NET. Consulte a [seção de configuração](#configuration) no início deste artigo.

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="exceptions-and-return-codes"></a>Exceções e códigos de retorno

| Vínculo | Referência |
|---|---|
| Hub de Notificação | [Guia de operações](https://docs.microsoft.com/rest/api/notificationhubs/) |

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Saiba mais sobre as funções do Azure acionadores e enlaces](functions-triggers-bindings.md)

