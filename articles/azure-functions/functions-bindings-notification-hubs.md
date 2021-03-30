---
title: Ligações de Hubs de Notificação para Funções Azure
description: Entenda como utilizar a ligação do Hub de Notificação Azure em Funções Azure.
author: craigshoemaker
ms.topic: reference
ms.custom: devx-track-csharp
ms.date: 11/21/2017
ms.author: cshoe
ms.openlocfilehash: c4198a1b73f76d61e39324befc85b55bd260e363
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "88212229"
---
# <a name="notification-hubs-output-binding-for-azure-functions"></a>Ligação de saída de Hubs de Notificação para Funções Azure

Este artigo explica como enviar notificações push usando ligações [Azure Notification Hubs](../notification-hubs/notification-hubs-push-notification-overview.md) em Funções Azure. A Azure Functions suporta encadernações de saída para centros de notificação.

Os Hubs de Notificação Azure devem ser configurados para o Serviço de Notificações da Plataforma (PNS) que pretende utilizar. Para saber como obter notificações push na sua aplicação de clientes a partir de Centros de Notificação, consulte Começar com Os Centros de [Notificação](../notification-hubs/notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md) e selecione a plataforma do cliente alvo a partir da lista de drop-down perto do topo da página.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

> [!IMPORTANT]
> A Google [depreciou o Google Cloud Messaging (GCM) a favor da Firebase Cloud Messaging (FCM)](https://developers.google.com/cloud-messaging/faq). Esta ligação de saída não suporta a FCM. Para enviar notificações utilizando a FCM, utilize a [API firebase](https://firebase.google.com/docs/cloud-messaging/server#choosing-a-server-option) diretamente na sua função ou utilize [notificações de modelos](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md).

## <a name="packages---functions-1x"></a>Pacotes - Funções 1.x

As ligações 'Hubs de Notificação' são fornecidas no pacote [Microsoft.Azure.WebJobs.Extensions.NotificationHubs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.NotificationHubs) NuGet, versão 1.x. O código fonte para o pacote está no [repositório GitHub.](https://github.com/Azure/azure-webjobs-sdk-extensions/tree/v2.x/src/WebJobs.Extensions.NotificationHubs)

[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="packages---functions-2x-and-higher"></a>Pacotes - Funções 2.x e superior

Esta ligação não está disponível nas Funções 2.x ou superior.

## <a name="example---template"></a>Exemplo - modelo

As notificações que envia podem ser notificações nativas ou [notificações de modelos](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md). As notificações nativas visam uma plataforma específica do cliente, tal como configurada na `platform` propriedade da vinculação de saída. Uma notificação de modelo pode ser usada para direcionar várias plataformas.   

Veja o exemplo específico da linguagem:

* [C# script - fora do parâmetro](#c-script-template-example---out-parameter)
* [C# script - assíncrona](#c-script-template-example---asynchronous)
* [C# script - JSON](#c-script-template-example---json)
* [C# script - tipos de biblioteca](#c-script-template-example---library-types)
* [F#](#f-template-example)
* [JavaScript](#javascript-template-example)

### <a name="c-script-template-example---out-parameter"></a>C# exemplo do modelo de script - fora do parâmetro

Este exemplo envia uma notificação para um [registo de modelo](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md) que contém um espaço reservado no `message` modelo.

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

### <a name="c-script-template-example---asynchronous"></a>C# exemplo do modelo de script - assíncronos

Se estiver a utilizar código assíncronos, não são permitidos parâmetros de saída. Neste caso, utilize `IAsyncCollector` para devolver a notificação do seu modelo. O seguinte código é um exemplo assíncronos do código acima. 

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

### <a name="c-script-template-example---json"></a>C# exemplo de modelo de script - JSON

Este exemplo envia uma notificação para um [registo de modelo](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md) que contém um espaço reservado no modelo `message` usando uma cadeia JSON válida.

```cs
using System;

public static void Run(string myQueueItem,  out string notification, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    notification = "{\"message\":\"Hello from C#. Processed a queue item!\"}";
}
```

### <a name="c-script-template-example---library-types"></a>C# exemplo de modelo de script - tipos de biblioteca

Este exemplo mostra como utilizar tipos definidos na Biblioteca de [Centros de Notificação do Microsoft Azure](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/). 

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

### <a name="f-template-example"></a>Exemplo de modelo F#

Este exemplo envia uma notificação para um [registo de modelo](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md) que contém e `location` `message` .

```fsharp
let Run(myTimer: TimerInfo, notification: byref<IDictionary<string, string>>) =
    notification = dict [("location", "Redmond"); ("message", "Hello from F#!")]
```

### <a name="javascript-template-example"></a>Exemplo do modelo JavaScript

Este exemplo envia uma notificação para um [registo de modelo](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md) que contém e `location` `message` .

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

## <a name="example---apns-native"></a>Exemplo - APNS nativo

Este exemplo de script C# mostra como enviar uma notificação de APNS nativa. 

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

## <a name="example---wns-native"></a>Exemplo - WNS nativo

Este exemplo de script C# mostra como usar tipos definidos na [Microsoft Azure Notification Hubs Library](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/) para enviar uma notificação de torradas WNS nativa. 

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

Nas [bibliotecas de classe C,](functions-dotnet-class-library.md)utilize o atributo [NotificationHub.](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions.NotificationHubs/NotificationHubAttribute.cs)

Os parâmetros e propriedades do construtor do atributo são descritos na secção [de configuração.](#configuration)

## <a name="configuration"></a>Configuração

A tabela seguinte explica as propriedades de configuração de encadernação que definiu no *function.jsno* ficheiro e no `NotificationHub` atributo:

|function.jsna propriedade | Propriedade de atributo |Description|
|---------|---------|----------------------|
|**tipo** |n/a| Deve ser definido para `notificationHub` . |
|**direção** |n/a| Deve ser definido para `out` . | 
|**nome** |n/a| Nome variável usado no código de função para a mensagem do centro de notificação. |
|**tagExpressão** |**TagExpression** | As expressões de etiqueta permitem especificar que as notificações são entregues num conjunto de dispositivos que se registaram para receber notificações que correspondam à expressão da etiqueta.  Para obter mais informações, consulte [expressões de encaminhamento e etiquetagem](../notification-hubs/notification-hubs-tags-segment-push-message.md). |
|**nome hub** | **Nome Hub** | Nome do recurso do centro de notificação no portal Azure. |
|**conexão** | **ConexãoStringSetting** | O nome de uma definição de aplicação que contém uma cadeia de conexão De Centros de Notificação.  O fio de ligação deve ser definido para o valor *defaultFulFuldAccessSignature* para o seu hub de notificação. Consulte [a configuração da cadeia de ligação](#connection-string-setup) mais tarde neste artigo.|
|**plataforma** | **Plataforma** | A propriedade da plataforma indica a plataforma do cliente os seus alvos de notificação. Por padrão, se a propriedade da plataforma for omitida da ligação de saída, as notificações de modelo podem ser usadas para direcionar qualquer plataforma configurada no Azure Notification Hub. Para obter mais informações sobre a utilização de modelos em geral para enviar notificações de plataforma cruzada com um Centro de Notificação Azure, consulte [Modelos](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md). Quando definida, **a plataforma** deve ser um dos seguintes valores: <ul><li><code>apns</code>&mdash;Serviço de Notificação de Push apple. Para obter mais informações sobre a configuração do centro de notificação da APNS e receber a notificação numa aplicação do cliente, consulte [enviar notificações push para iOS com Azure Notification Hubs](../notification-hubs/xamarin-notification-hubs-ios-push-notification-apns-get-started.md).</li><li><code>adm</code>&mdash;[Mensagens de dispositivos da Amazon](https://developer.amazon.com/device-messaging). Para obter mais informações sobre a configuração do centro de notificação para ADM e receber a notificação numa aplicação Kindle, consulte ["Começar com centros de notificação" para aplicações Kindle](../notification-hubs/notification-hubs-android-push-notification-google-fcm-get-started.md).</li><li><code>wns</code>&mdash;[Serviços](/windows/uwp/design/shell/tiles-and-notifications/windows-push-notification-services--wns--overview) de notificação do Windows Push direcionados para plataformas Windows. O Windows Phone 8.1 e mais tarde também é suportado pela WNS. Para obter mais informações, consulte [Começar com os Centros de Notificação para aplicações da Plataforma Universal do Windows.](../notification-hubs/notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md)</li><li><code>mpns</code>&mdash;[Serviço de Notificação push microsoft](/previous-versions/windows/apps/ff402558(v=vs.105)). Esta plataforma suporta o Windows Phone 8 e as plataformas anteriores do Windows Phone. Para obter mais informações, consulte [enviar notificações push com os Hubs de Notificação Azure no Windows Phone](../notification-hubs/notification-hubs-windows-mobile-push-notifications-mpns.md).</li></ul> |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

### <a name="functionjson-file-example"></a>function.jsno exemplo do arquivo

Aqui está um exemplo de um aviso de ligação de Centros de Notificação numa *function.jsem* arquivo.

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

### <a name="connection-string-setup"></a>Configuração de cadeia de ligação

Para utilizar uma ligação de saída do hub de notificação, tem de configurar a cadeia de ligação para o hub. Pode selecionar um hub de notificação existente ou criar um novo a partir do *separador Integração* no portal Azure. Também pode configurar manualmente a cadeia de ligação. 

Para configurar a cadeia de ligação a um centro de notificação existente:

1. Navegue para o seu centro de notificação no [portal Azure,](https://portal.azure.com)escolha **as políticas de Acesso** e selecione o botão de cópia ao lado da política de **assinaturas DefaultFulFuldAccess.** Isto copia a cadeia de ligação para a política *defaultFulFullSharedAccessSignature* para o seu centro de notificação. Esta cadeia de ligação permite que a sua função envie mensagens de notificação para o centro.
    ![Copie a cadeia de ligação do hub de notificação](./media/functions-bindings-notification-hubs/get-notification-hub-connection.png)
1. Navegue para a sua aplicação de função no portal Azure, escolha **configurações de aplicação**, adicione uma chave como **MyHubConnectionString**, cole a *assinatura defaultFulFuldAccessss*  para o seu centro de notificação como valor e, em seguida, clique em **Guardar**.

O nome desta definição de aplicação é o que vai na definição de ligação de ligação de saída em *function.jsou* no atributo .NET. Consulte a [secção configuração](#configuration) mais cedo neste artigo.

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="exceptions-and-return-codes"></a>Exceções e códigos de devolução

| Enlace | Referência |
|---|---|
| Hub de Notificação | [Guia de Operações](/rest/api/notificationhubs/) |

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Saiba mais sobre as funções Azure desencadeia e encaderna](functions-triggers-bindings.md)
