---
title: Ligações de Centros de Notificação para Funções Azure
description: Entenda como utilizar a ligação do Hub de Notificação Azure em Funções Azure.
author: craigshoemaker
ms.topic: reference
ms.date: 11/21/2017
ms.author: cshoe
ms.openlocfilehash: 211f8c8a203b81a4df6a8e9515b403f99cec572a
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78371081"
---
# <a name="notification-hubs-output-binding-for-azure-functions"></a>Ligação de saída de Hubs de notificação para funções Azure

Este artigo explica como enviar notificações push utilizando ligações de Hubs de [Notificação Azure](../notification-hubs/notification-hubs-push-notification-overview.md) em Funções Azure. A Azure Functions suporta encadernações de saída para centros de notificação.

Os Hubs de Notificação Azure devem ser configurados para o Serviço de Notificações da Plataforma (PNS) que pretende utilizar. Para saber como obter notificações push na sua aplicação de cliente a partir de Centros de Notificação, consulte [Iniciar-se com Centros](../notification-hubs/notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md) de Notificação e selecionar a sua plataforma de cliente-alvo a partir da lista de drop-down perto do topo da página.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

> [!IMPORTANT]
> A Google [deprecou o Google Cloud Messaging (GCM) a favor da Firebase Cloud Messaging (FCM)](https://developers.google.com/cloud-messaging/faq). Esta ligação de saída não suporta a FCM. Para enviar notificações utilizando a FCM, utilize diretamente a [API firebase](https://firebase.google.com/docs/cloud-messaging/server#choosing-a-server-option) na sua função ou utilize notificações de [modelos](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md).

## <a name="packages---functions-1x"></a>Pacotes - funções 1.x

As ligações dos Centros de Notificação são fornecidas no pacote [Microsoft.Azure.WebJobs.Extensions.NotificationHubs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.NotificationHubs) NuGet, versão 1.x. O código fonte para o pacote está no repositório [GitHub-extensões azure-webjobs-sdk.](https://github.com/Azure/azure-webjobs-sdk-extensions/tree/v2.x/src/WebJobs.Extensions.NotificationHubs)

[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="packages---functions-2x-and-higher"></a>Pacotes - Funções 2.x e superiores

Esta ligação não está disponível nas Funções 2.x e superiores.

## <a name="example---template"></a>Exemplo - modelo

As notificações que envia podem ser notificações nativas ou [notificações de modelos.](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md) As notificações nativas visam uma plataforma específica do cliente, tal como configurada na propriedade `platform` da encadernação de saída. Uma notificação de modelo pode ser usada para direcionar várias plataformas.   

Veja o exemplo de idioma específico:

* [C#script - fora parâmetro](#c-script-template-example---out-parameter)
* [C#script - assíncrono](#c-script-template-example---asynchronous)
* [C#script - JSON](#c-script-template-example---json)
* [C#script - tipos de biblioteca](#c-script-template-example---library-types)
* [F#](#f-template-example)
* [JavaScript](#javascript-template-example)

### <a name="c-script-template-example---out-parameter"></a>C#exemplo de modelo de script - fora parâmetro

Este exemplo envia uma notificação para um registo de [modelo](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md) que contém um `message` espaço reservado no modelo.

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

### <a name="c-script-template-example---asynchronous"></a>C#Exemplo de modelo de script - assíncrono

Se estiver a usar código assíncrono, não são permitidos parâmetros. Neste caso, utilize `IAsyncCollector` para devolver a notificação do modelo. O código que se segue é um exemplo assíncrono do código acima. 

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

### <a name="c-script-template-example---json"></a>C#Exemplo de modelo de script - JSON

Este exemplo envia uma notificação para um registo de [modelo](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md) que contém um espaço reservado `message` no modelo usando uma corda JSON válida.

```cs
using System;

public static void Run(string myQueueItem,  out string notification, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    notification = "{\"message\":\"Hello from C#. Processed a queue item!\"}";
}
```

### <a name="c-script-template-example---library-types"></a>C#Exemplo de modelo de script - tipos de biblioteca

Este exemplo mostra como utilizar tipos definidos na Biblioteca hubs de notificação do [Microsoft Azure](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/). 

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

Este exemplo envia uma notificação para um registo de [modelo](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md) que contém `location` e `message`.

```fsharp
let Run(myTimer: TimerInfo, notification: byref<IDictionary<string, string>>) =
    notification = dict [("location", "Redmond"); ("message", "Hello from F#!")]
```

### <a name="javascript-template-example"></a>Exemplo de modelo JavaScript

Este exemplo envia uma notificação para um registo de [modelo](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md) que contém `location` e `message`.

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

## <a name="example---apns-native"></a>Exemplo - ApNS nativo

Este C# exemplo de guião mostra como enviar uma notificação nativa da APNS. 

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

## <a name="example---wns-native"></a>Exemplo - NATIVO WNS

Este C# exemplo de script mostra como usar tipos definidos na Biblioteca microsoft [Azure Notification Hubs](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/) para enviar uma notificação de torradas WNS nativa. 

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

Nas [ C# bibliotecas de classes,](functions-dotnet-class-library.md)utilize o atributo [NotificationHub.](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions.NotificationHubs/NotificationHubAttribute.cs)

Os parâmetros e propriedades do construtor do atributo são descritos na secção de [configuração.](#configuration)

## <a name="configuration"></a>Configuração

A tabela que se segue explica as propriedades de configuração de ligação que definiu no ficheiro *função.json* e no atributo `NotificationHub`:

|propriedade de Function | Propriedade de atributo |Descrição|
|---------|---------|----------------------|
|**tipo** |n/d| Deve ser definido para `notificationHub`. |
|**direção** |n/d| Deve ser definido para `out`. | 
|**nome** |n/d| Nome variável utilizado no código de função para a mensagem do centro de notificação. |
|**tagExpressão** |**TagExpression** | As expressões de etiqueta permitem especificar que as notificações são entregues a um conjunto de dispositivos que se registaram para receber notificações que correspondam à expressão da etiqueta.  Para mais informações, consulte [as expressões de Encaminhamento e etiqueta.](../notification-hubs/notification-hubs-tags-segment-push-message.md) |
|**hubName** | **HubName** | Nome do recurso hub de notificação no portal Azure. |
|**conexão** | **Definição de cordas de ligação** | O nome de uma definição de aplicação que contém uma cadeia de ligação De Notificação Hubs.  A cadeia de ligação deve ser definida para o valor *DefaultFullSharedAccessSignature* para o seu centro de notificação. Consulte a configuração da [corda de ligação](#connection-string-setup) mais tarde neste artigo.|
|**plataforma** | **Plataforma** | A propriedade da plataforma indica à plataforma do cliente os seus alvos de notificação. Por padrão, se a propriedade da plataforma for omitida da encadernação de saída, as notificações do modelo podem ser usadas para direcionar qualquer plataforma configurada no Hub de Notificação Azure. Para obter mais informações sobre a utilização de modelos em geral para enviar notificações de plataformas cruzadas com um Hub de Notificação Azure, consulte [Os Modelos](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md). Quando definida, a **plataforma** deve ser um dos seguintes valores: <ul><li><code>apns</code>&mdash;Apple Push Notification Service. Para obter mais informações sobre a configuração do centro de notificação para APNS e receber a notificação numa aplicação de cliente, consulte [enviar notificações push para iOS com Hubs de Notificação Azure](../notification-hubs/notification-hubs-ios-apple-push-notification-apns-get-started.md).</li><li><code>adm</code>&mdash;[Mensagens de Dispositivos Amazon](https://developer.amazon.com/device-messaging). Para obter mais informações sobre a configuração do centro de notificação para ADM e receber a notificação numa aplicação Kindle, consulte [Getting Started with Notification Hubs para aplicações Kindle](../notification-hubs/notification-hubs-kindle-amazon-adm-push-notification.md).</li><li><code>wns</code>&mdash;Serviços de [Notificação push do Windows](/windows/uwp/design/shell/tiles-and-notifications/windows-push-notification-services--wns--overview) direcionados para as plataformas windows. O Windows Phone 8.1 e mais tarde também é suportado pela WNS. Para mais informações, consulte [Iniciar-se com Centros de Notificação para Aplicações da Plataforma Universal do Windows](../notification-hubs/notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md).</li><li><code>mpns</code>&mdash;Serviço de [Notificação push](/previous-versions/windows/apps/ff402558(v=vs.105))da Microsoft . Esta plataforma suporta plataformas Windows Phone 8 e Windows Phone anteriores. Para mais informações, consulte [Enviar notificações push com Hubs de Notificação Azure no Windows Phone](../notification-hubs/notification-hubs-windows-mobile-push-notifications-mpns.md).</li></ul> |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

### <a name="functionjson-file-example"></a>exemplo de ficheiro fun.json

Aqui está um exemplo de um comunicado de ligação dos Centros de Notificação num ficheiro *function.json.*

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

### <a name="connection-string-setup"></a>Configuração de cordas de ligação

Para utilizar uma ligação de saída do hub de notificação, deve configurar a cadeia de ligação para o centro. Pode selecionar um centro de notificação existente ou criar um novo direito a partir do separador *Integração* no portal Azure. Também pode configurar manualmente a corda de ligação. 

Para configurar a cadeia de ligação a um centro de notificação existente:

1. Navegue para o seu centro de notificação no [portal Azure,](https://portal.azure.com)escolha **as políticas**de Acesso e selecione o botão de cópia ao lado da política **DefaultFullSharedAccessSignature.** Isto copia o fio de ligação para a política *DefaultFullSharedAccessSignature* para o seu centro de notificação. Esta cadeia de ligação permite que a sua função envie mensagens de notificação para o centro.
    ![Copie a cadeia de conexão do hub de notificação](./media/functions-bindings-notification-hubs/get-notification-hub-connection.png)
1. Navegue para a sua aplicação de função no portal Azure, escolha as definições de **Aplicação,** adicione uma chave como **MyHubConnectionString,** passe a assinatura de *Acesso Partilhado Predevido* copiado para o seu centro de notificação como valor e, em seguida, clique em **Guardar**.

O nome desta definição de aplicação é o que vai na definição de ligação de ligação de saída em *função.json* ou no atributo .NET. Consulte a [secção de Configuração](#configuration) mais cedo neste artigo.

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="exceptions-and-return-codes"></a>Exceções e códigos de retorno

| Vínculo | Referência |
|---|---|
| Centro de Notificação | [Guia de Operações](https://docs.microsoft.com/rest/api/notificationhubs/) |

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Saiba mais sobre as funções azure gatilhos e encadernações](functions-triggers-bindings.md)

