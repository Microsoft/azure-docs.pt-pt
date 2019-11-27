---
title: Associações de hubs de notificação para Azure Functions
description: Entenda como usar a associação do hub de notificação do Azure no Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 11/21/2017
ms.author: cshoe
ms.openlocfilehash: 8bc7f879a2c2e8b1e0e2d82216241704a466ad60
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74231145"
---
# <a name="notification-hubs-output-binding-for-azure-functions"></a>Associação de saída dos hubs de notificação para Azure Functions

Este artigo explica como enviar notificações por push usando associações de [hubs de notificação do Azure](../notification-hubs/notification-hubs-push-notification-overview.md) no Azure functions. Azure Functions dá suporte a associações de saída para hubs de notificação.

Os hubs de notificação do Azure devem ser configurados para o PNS (serviço de notificações de plataforma) que você deseja usar. Para saber como obter notificações por push em seu aplicativo cliente nos hubs de notificação, consulte [introdução aos hubs de notificação](../notification-hubs/notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md) e selecione a plataforma do cliente de destino na lista suspensa, próximo à parte superior da página.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

> [!IMPORTANT]
> O Google [preteriu Google Cloud Messaging (GCM) em favor do firebase Cloud Messaging (FCM)](https://developers.google.com/cloud-messaging/faq). Esta associação de saída não dá suporte a FCM. Para enviar notificações usando o FCM, use a [API do firebase](https://firebase.google.com/docs/cloud-messaging/server#choosing-a-server-option) diretamente em sua função ou use notificações de [modelo](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md).

## <a name="packages---functions-1x"></a>Pacotes - funções 1.x

As associações de hubs de notificação são fornecidas no pacote NuGet [Microsoft. Azure. webjobs. Extensions. NotificationHubs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.NotificationHubs) , versão 1. x. O código-fonte do pacote está no repositório GitHub [Azure-webjobs-SDK-Extensions](https://github.com/Azure/azure-webjobs-sdk-extensions/tree/v2.x/src/WebJobs.Extensions.NotificationHubs) .

[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="packages---functions-2x"></a>Pacotes - funções 2.x

Essa associação não está disponível no functions 2. x.

## <a name="example---template"></a>Exemplo-modelo

As notificações que você envia podem ser notificações nativas ou [notificações de modelo](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md). As notificações nativas visam uma plataforma de cliente específica, conforme configurado na propriedade `platform` da Associação de saída. Uma notificação de modelo pode ser usada para direcionar várias plataformas.   

Veja o exemplo de idioma específico:

* [C#parâmetro de saída de script](#c-script-template-example---out-parameter)
* [C#script-assíncrono](#c-script-template-example---asynchronous)
* [C#script-JSON](#c-script-template-example---json)
* [C#tipos de biblioteca de scripts](#c-script-template-example---library-types)
* [F#](#f-template-example)
* [JavaScript](#javascript-template-example)

### <a name="c-script-template-example---out-parameter"></a>C#exemplo de modelo de script-parâmetro out

Este exemplo envia uma notificação para um [registro de modelo](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md) que contém um espaço reservado `message` no modelo.

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

### <a name="c-script-template-example---asynchronous"></a>C#exemplo de modelo de script – assíncrono

Se você estiver usando código assíncrono, os parâmetros de saída não serão permitidos. Nesse caso, use `IAsyncCollector` para retornar a notificação de modelo. O código a seguir é um exemplo assíncrono do código acima. 

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

### <a name="c-script-template-example---json"></a>C#exemplo de modelo de script – JSON

Este exemplo envia uma notificação para um [registro de modelo](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md) que contém um espaço reservado `message` no modelo usando uma cadeia de caracteres JSON válida.

```cs
using System;

public static void Run(string myQueueItem,  out string notification, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    notification = "{\"message\":\"Hello from C#. Processed a queue item!\"}";
}
```

### <a name="c-script-template-example---library-types"></a>C#exemplo de modelo de script – tipos de biblioteca

Este exemplo mostra como usar tipos definidos na biblioteca de [hubs de notificações do Microsoft Azure](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/). 

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

Este exemplo envia uma notificação para um [registro de modelo](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md) que contém `location` e `message`.

```fsharp
let Run(myTimer: TimerInfo, notification: byref<IDictionary<string, string>>) =
    notification = dict [("location", "Redmond"); ("message", "Hello from F#!")]
```

### <a name="javascript-template-example"></a>Exemplo de modelo de JavaScript

Este exemplo envia uma notificação para um [registro de modelo](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md) que contém `location` e `message`.

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

## <a name="example---apns-native"></a>Exemplo – APNS nativo

Este C# exemplo de script mostra como enviar uma notificação nativa de APNS. 

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

## <a name="example---wns-native"></a>Exemplo – WNS nativo

Este C# exemplo de script mostra como usar tipos definidos na [biblioteca de hubs de notificações do Microsoft Azure](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/) para enviar uma notificação de sistema WNS nativa. 

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

Em [ C# bibliotecas de classes](functions-dotnet-class-library.md), use o atributo [NotificationHub](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions.NotificationHubs/NotificationHubAttribute.cs) .

Os parâmetros e as propriedades do construtor do atributo são descritos na seção [configuração](#configuration) .

## <a name="configuration"></a>Configuração

A tabela a seguir explica as propriedades de configuração de associação que você define no arquivo *Function. JSON* e o atributo `NotificationHub`:

|propriedade de Function | Propriedade de atributo |Descrição|
|---------|---------|----------------------|
|**tipo** |n/d| Deve ser definido como `notificationHub`. |
|**direção** |n/d| Deve ser definido como `out`. | 
|**nomes** |n/d| Nome da variável usada no código de função para a mensagem do hub de notificação. |
|**tagExpression** |**TagExpression** | As expressões de marca permitem que você especifique que as notificações sejam entregues a um conjunto de dispositivos que se registraram para receber notificações que correspondam à expressão de marca.  Para obter mais informações, consulte [expressões de marca e de roteamento](../notification-hubs/notification-hubs-tags-segment-push-message.md). |
|**hubName** | **HubName** | Nome do recurso do hub de notificação no portal do Azure. |
|**conexão** | **ConnectionStringSetting** | O nome de uma configuração de aplicativo que contém uma cadeia de conexão de hubs de notificação.  A cadeia de conexão deve ser definida como o valor *DefaultFullSharedAccessSignature* para o Hub de notificação. Consulte [configuração da cadeia de conexão](#connection-string-setup) mais adiante neste artigo.|
|**plataforma** | **Plataforma** | A propriedade Platform indica a plataforma de cliente à qual sua notificação se destina. Por padrão, se a propriedade de plataforma for omitida da Associação de saída, as notificações de modelo poderão ser usadas para direcionar qualquer plataforma configurada no Hub de notificação do Azure. Para obter mais informações sobre como usar modelos em geral para enviar notificações entre plataformas com um hub de notificação do Azure, consulte [modelos](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md). Quando definido, a **plataforma** deve ser um dos seguintes valores: <ul><li><code>apns</code>&mdash;Apple Push Notification Service. Para obter mais informações sobre como configurar o Hub de notificação para APNS e receber a notificação em um aplicativo cliente, consulte como [enviar notificações por push para o Ios com os hubs de notificação do Azure](../notification-hubs/notification-hubs-ios-apple-push-notification-apns-get-started.md).</li><li><code>adm</code>&mdash;[Amazon Device Messaging](https://developer.amazon.com/device-messaging). Para obter mais informações sobre como configurar o Hub de notificação para ADM e receber a notificação em um aplicativo Kindle, consulte [introdução com hubs de notificação para aplicativos Kindle](../notification-hubs/notification-hubs-kindle-amazon-adm-push-notification.md).</li><li><code>wns</code>&mdash;[Windows Push Notification Services](/windows/uwp/design/shell/tiles-and-notifications/windows-push-notification-services--wns--overview) direcionamento a plataformas Windows. O Windows Phone 8,1 e posterior também tem suporte pelo WNS. Para obter mais informações, consulte [introdução aos hubs de notificação para aplicativos da plataforma universal do Windows](../notification-hubs/notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md).</li><li><code>mpns</code>&mdash;[serviço de notificação por push da Microsoft](/previous-versions/windows/apps/ff402558(v=vs.105)). Esta plataforma dá suporte às plataformas Windows Phone 8 e versões anteriores Windows Phone. Para obter mais informações, consulte [enviando notificações por push com os hubs de notificação do Azure em Windows Phone](../notification-hubs/notification-hubs-windows-mobile-push-notifications-mpns.md).</li></ul> |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

### <a name="functionjson-file-example"></a>exemplo de arquivo function. JSON

Aqui está um exemplo de uma associação de hubs de notificação em um arquivo *Function. JSON* .

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
      "platform": "apns"
    }
  ],
  "disabled": false
}
```

### <a name="connection-string-setup"></a>Configuração da cadeia de conexão

Para usar uma associação de saída do hub de notificação, você deve configurar a cadeia de conexão para o Hub. Você pode selecionar um hub de notificação existente ou criar um novo direito na guia *integrar* na portal do Azure. Você também pode configurar a cadeia de conexão manualmente. 

Para configurar a cadeia de conexão para um hub de notificação existente:

1. Navegue até o Hub de notificação no [portal do Azure](https://portal.azure.com), escolha **políticas de acesso**e selecione o botão copiar ao lado da política **DefaultFullSharedAccessSignature** . Isso copia a cadeia de conexão da política *DefaultFullSharedAccessSignature* para o Hub de notificação. Essa cadeia de conexão permite que sua função envie mensagens de notificação para o Hub.
    ![copiar a cadeia de conexão do hub de notificação](./media/functions-bindings-notification-hubs/get-notification-hub-connection.png)
1. Navegue até seu aplicativo de funções no portal do Azure, escolha **configurações de aplicativo**, adicione uma chave como **MyHubConnectionString**, Cole o *DefaultFullSharedAccessSignature* copiado para o Hub de notificação como o valor e, em seguida, clique em **salvar**.

O nome dessa configuração de aplicativo é o que acontece na configuração de conexão de associação de saída em *Function. JSON* ou no atributo .net. Consulte a [seção configuração](#configuration) anteriormente neste artigo.

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="exceptions-and-return-codes"></a>Exceções e códigos de retorno

| Vínculo | Referência |
|---|---|
| Hub de Notificação | [Guia de operações](https://docs.microsoft.com/rest/api/notificationhubs/) |

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Saiba mais sobre os gatilhos e associações do Azure Functions](functions-triggers-bindings.md)

