---
title: Envie notificações push localizadas para o iOS utilizando hubs de notificação do Azure [ Hubs de Notificação Azure] Microsoft Docs
description: Saiba como utilizar notificações localizadas para dispositivos iOS utilizando hubs de notificação Do Azure.
services: notification-hubs
documentationcenter: ios
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: 484914b5-e081-4a05-a84a-798bbd89d428
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ios
ms.devlang: objective-c
ms.topic: article
ms.date: 01/04/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: a8614156be5d516d16aff698b604cf0e661d7311
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "72385647"
---
# <a name="tutorial-send-localized-push-notifications-to-ios-using-azure-notification-hubs"></a>Tutorial: Envie notificações de push localizadas para o iOS usando hubs de notificação azure

> [!div class="op_single_selector"]
> * [Windows Store C#](notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification.md)
> * [iOS](notification-hubs-ios-xplat-localized-apns-push-notification.md)

Este tutorial mostra-lhe como usar a funcionalidade de modelos dos [Hubs](notification-hubs-templates-cross-platform-push-messages.md) de Notificação Azure para transmitir notificações de notícias de última hora que foram localizadas por linguagem e dispositivo. Neste tutorial, começa com a aplicação iOS criada nos Centros de [Notificação de Utilização para enviar notícias]de última hora. Quando estiver concluído, pode inscrever-se nas categorias em que está interessado, especificar um idioma para receber as notificações e receber apenas notificações push para as categorias selecionadas nessa língua.

Há duas partes neste cenário:

* A aplicação iOS permite que os dispositivos clientes especifiquem um idioma e subscrevam diferentes categorias de notícias de última hora;
* O back-end transmite as notificações, utilizando as características de **etiqueta** e **modelo** dos Hubs de Notificação Azure.

Neste tutorial, siga os seguintes passos:

> [!div class="checklist"]
> * Atualizar a interface do utilizador da aplicação
> * Construa a app iOS
> * Envie notificações de modelo localizadas a partir da aplicação de consola .NET
> * Enviar notificações de modelo localizadas a partir do dispositivo

## <a name="overview"></a>Descrição geral

Em [Use Notification Hubs para enviar notícias]de última hora, construiu uma aplicação que usou **tags** para subscrever notificações para diferentes categorias de notícias. Muitas aplicações, no entanto, visam vários mercados e requerem localização. Significa que o conteúdo das notificações em si tem de ser localizado e entregue ao conjunto correto de dispositivos. Este tutorial mostra-lhe como usar a funcionalidade de **modelo** de Centros de Notificação para entregar facilmente notificações de notícias de última hora localizadas.

> [!NOTE]
> Uma forma de enviar notificações localizadas é criar várias versões de cada etiqueta. Por exemplo, para apoiar inglês, francês e mandarim, precisaria de três tags diferentes para notícias mundiais: "world_en", "world_fr" e "world_ch". Teria então de enviar uma versão localizada das notícias mundiais para cada uma destas etiquetas. Neste tópico, utiliza modelos para evitar a proliferação de tags e a exigência de envio de várias mensagens.

Os modelos são uma forma de especificar como um dispositivo específico deve receber uma notificação. O modelo especifica o formato de payload exato ao consultar as propriedades que fazem parte da mensagem enviada pelo back-end da sua aplicação. No seu caso, envia uma mensagem agnóstica local contendo todas as línguas apoiadas:

```json
{
    "News_English": "...",
    "News_French": "...",
    "News_Mandarin": "..."
}
```

Em seguida, certifique-se de que os dispositivos se registam com um modelo que se refere à propriedade correta. Por exemplo, uma aplicação iOS que pretende registar-se para registos de notícias franceses utilizando a seguinte sintaxe:

```json
{
    aps: {
        alert: "$(News_French)"
    }
}
```

Para obter mais informações sobre os modelos, consulte o artigo [Modelos.](notification-hubs-templates-cross-platform-push-messages.md)

## <a name="prerequisites"></a>Pré-requisitos

* Complete as notificações push para o tutorial específico de [dispositivos iOS](notification-hubs-ios-xplat-segmented-apns-push-notification.md) e tenha o código disponível, porque este tutorial baseia-se diretamente nesse código.
* O Visual Studio 2019 é opcional.

## <a name="update-the-app-user-interface"></a>Atualizar a interface do utilizador da aplicação

Nesta secção, modifica a aplicação Breaking News que criou no tópico [Use Notification Hubs para enviar notícias] de última hora para enviar notícias de última hora localizadas usando modelos.

No `MainStoryboard_iPhone.storyboard`seu , adicione um Controlo Segmentado com as três línguas: inglês, francês e mandarim.

![Criação do storyboard iOS UI][13]

Em seguida, certifique-se de adicionar um IBOutlet no seu ViewController.h, conforme mostrado na seguinte imagem:

![Criar saídas para os interruptores][14]

## <a name="build-the-ios-app"></a>Construa a app iOS

1. No `Notification.h`seu , `retrieveLocale` adicione o método e modifique a loja e subscreva métodos como mostrado no seguinte código:

    ```objc
    - (void) storeCategoriesAndSubscribeWithLocale:(int) locale categories:(NSSet*) categories completion: (void (^)(NSError* error))completion;

    - (void) subscribeWithLocale:(int) locale categories:(NSSet*) categories completion:(void (^)(NSError *))completion;

    - (NSSet*) retrieveCategories;

    - (int) retrieveLocale;
    ```
    No `Notification.m`seu , `storeCategoriesAndSubscribe` modifique o `locale` método, adicionando o parâmetro e armazenando-o nos predefinidos do utilizador:

    ```objc
    - (void) storeCategoriesAndSubscribeWithLocale:(int) locale categories:(NSSet *)categories completion:(void (^)(NSError *))completion {
        NSUserDefaults* defaults = [NSUserDefaults standardUserDefaults];
        [defaults setValue:[categories allObjects] forKey:@"BreakingNewsCategories"];
        [defaults setInteger:locale forKey:@"BreakingNewsLocale"];
        [defaults synchronize];

        [self subscribeWithLocale: locale categories:categories completion:completion];
    }
    ```

    Em seguida, modifique o método de *subscrição* para incluir o local:

    ```objc
    - (void) subscribeWithLocale: (int) locale categories:(NSSet *)categories completion:(void (^)(NSError *))completion{
        SBNotificationHub* hub = [[SBNotificationHub alloc] initWithConnectionString:@"<connection string>" notificationHubPath:@"<hub name>"];

        NSString* localeString;
        switch (locale) {
            case 0:
                localeString = @"English";
                break;
            case 1:
                localeString = @"French";
                break;
            case 2:
                localeString = @"Mandarin";
                break;
        }

        NSString* template = [NSString stringWithFormat:@"{\"aps\":{\"alert\":\"$(News_%@)\"},\"inAppMessage\":\"$(News_%@)\"}", localeString, localeString];

        [hub registerTemplateWithDeviceToken:self.deviceToken name:@"localizednewsTemplate" jsonBodyTemplate:template expiryTemplate:@"0" tags:categories completion:completion];
    }
    ```

    Usa-se `registerTemplateWithDeviceToken`o método, em vez de. `registerNativeWithDeviceToken` Quando se regista para um modelo, tem de fornecer o modelo json e também um nome para o modelo (como a app pode querer registar diferentes modelos). Certifique-se de registar as suas categorias como tags, pois pretende receber as notificações para essas notícias.

    Adicione um método para recuperar o local das definições predefinidas do utilizador:

    ```objc
    - (int) retrieveLocale {
        NSUserDefaults* defaults = [NSUserDefaults standardUserDefaults];

        int locale = [defaults integerForKey:@"BreakingNewsLocale"];

        return locale < 0?0:locale;
    }
    ```

2. Agora que modificou `Notifications` a aula, tem de `ViewController` se certificar `UISegmentControl`de que o novo. Adicione a seguinte `viewDidLoad` linha no método para se certificar de que mostra o local que está atualmente selecionado:

    ```objc
    self.Locale.selectedSegmentIndex = [notifications retrieveLocale];
    ```

    Em seguida, `subscribe` no seu método, `storeCategoriesAndSubscribe` altere a sua chamada para o seguinte código:

    ```objc
    [notifications storeCategoriesAndSubscribeWithLocale: self.Locale.selectedSegmentIndex categories:[NSSet setWithArray:categories] completion: ^(NSError* error) {
        if (!error) {
            UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Notification" message:
                                    @"Subscribed!" delegate:nil cancelButtonTitle:
                                    @"OK" otherButtonTitles:nil, nil];
            [alert show];
        } else {
            NSLog(@"Error subscribing: %@", error);
        }
    }];
    ```

3. Por fim, tem `didRegisterForRemoteNotificationsWithDeviceToken` de atualizar o método no seu AppDelegate.m, para que possa atualizar corretamente o seu registo quando a sua aplicação começar. Altere a `subscribe` sua chamada para o método de notificações com o seguinte código:

    ```obj-c
    NSSet* categories = [self.notifications retrieveCategories];
    int locale = [self.notifications retrieveLocale];
    [self.notifications subscribeWithLocale: locale categories:categories completion:^(NSError* error) {
        if (error != nil) {
            NSLog(@"Error registering for notifications: %@", error);
        }
    }];
    ```

## <a name="optional-send-localized-template-notifications-from-net-console-app"></a>(opcional) Envie notificações de modelo localizadas a partir da aplicação de consola .NET

[!INCLUDE [notification-hubs-localized-back-end](../../includes/notification-hubs-localized-back-end.md)]

## <a name="optional-send-localized-template-notifications-from-the-device"></a>(opcional) Enviar notificações de modelo localizadas a partir do dispositivo

Se não tiver acesso ao Estúdio Visual, ou pretender apenas testar o envio das notificações do modelo localizado diretamente da aplicação no dispositivo. Pode adicionar os parâmetros do `SendNotificationRESTAPI` modelo localizados ao método definido no tutorial anterior.

```objc
- (void)SendNotificationRESTAPI:(NSString*)categoryTag
{
    NSURLSession* session = [NSURLSession sessionWithConfiguration:[NSURLSessionConfiguration
                                defaultSessionConfiguration] delegate:nil delegateQueue:nil];

    NSString *json;

    // Construct the messages REST endpoint
    NSURL* url = [NSURL URLWithString:[NSString stringWithFormat:@"%@%@/messages/%@", HubEndpoint,
                                        HUBNAME, API_VERSION]];

    // Generated the token to be used in the authorization header.
    NSString* authorizationToken = [self generateSasToken:[url absoluteString]];

    //Create the request to add the template notification message to the hub
    NSMutableURLRequest *request = [NSMutableURLRequest requestWithURL:url];
    [request setHTTPMethod:@"POST"];

    // Add the category as a tag
    [request setValue:categoryTag forHTTPHeaderField:@"ServiceBusNotification-Tags"];

    // Template notification
    json = [NSString stringWithFormat:@"{\"messageParam\":\"Breaking %@ News : %@\","
            \"News_English\":\"Breaking %@ News in English : %@\","
            \"News_French\":\"Breaking %@ News in French : %@\","
            \"News_Mandarin\":\"Breaking %@ News in Mandarin : %@\","
            categoryTag, self.notificationMessage.text,
            categoryTag, self.notificationMessage.text,  // insert English localized news here
            categoryTag, self.notificationMessage.text,  // insert French localized news here
            categoryTag, self.notificationMessage.text]; // insert Mandarin localized news here

    // Signify template notification format
    [request setValue:@"template" forHTTPHeaderField:@"ServiceBusNotification-Format"];

    // JSON Content-Type
    [request setValue:@"application/json;charset=utf-8" forHTTPHeaderField:@"Content-Type"];

    //Authenticate the notification message POST request with the SaS token
    [request setValue:authorizationToken forHTTPHeaderField:@"Authorization"];

    //Add the notification message body
    [request setHTTPBody:[json dataUsingEncoding:NSUTF8StringEncoding]];

    // Send the REST request
    NSURLSessionDataTask* dataTask = [session dataTaskWithRequest:request
                completionHandler:^(NSData *data, NSURLResponse *response, NSError *error)
        {
        NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*) response;
            if (error || httpResponse.statusCode != 200)
            {
                NSLog(@"\nError status: %d\nError: %@", httpResponse.statusCode, error);
            }
            if (data != NULL)
            {
                //xmlParser = [[NSXMLParser alloc] initWithData:data];
                //[xmlParser setDelegate:self];
                //[xmlParser parse];
            }
        }];

    [dataTask resume];
}
```

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, enviou notificações localizadas para dispositivos iOS. Para saber como empurrar notificações a utilizadores específicos de aplicações iOS, avance para o seguinte tutorial:

> [!div class="nextstepaction"]
>[Enviar notificações push para utilizadores específicos](notification-hubs-aspnet-backend-ios-apple-apns-notification.md)

<!-- Images. -->
[13]: ./media/notification-hubs-ios-send-localized-breaking-news/ios_localized1.png
[14]: ./media/notification-hubs-ios-send-localized-breaking-news/ios_localized2.png

<!-- URLs. -->
[How To: Service Bus Notification Hubs (iOS Apps)]: https://msdn.microsoft.com/library/jj927168.aspx
[Utilizar Notification Hubs para enviar notícias de última hora]: notification-hubs-ios-xplat-segmented-apns-push-notification.md
[Mobile Service]: /develop/mobile/tutorials/get-started
[Notify users with Notification Hubs: ASP.NET]: notification-hubs-aspnet-backend-ios-apple-apns-notification.md
[Notify users with Notification Hubs: Mobile Services]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Submit an app page]: https://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: https://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: https://go.microsoft.com/fwlink/p/?LinkId=262253
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started/#create-new-service
[Get started with data]: /develop/mobile/tutorials/get-started-with-data-ios
[Get started with authentication]: /develop/mobile/tutorials/get-started-with-users-ios
[Get started with push notifications]: /develop/mobile/tutorials/get-started-with-push-ios
[Push notifications to app users]: /develop/mobile/tutorials/push-notifications-to-users-ios
[Authorize users with scripts]: /develop/mobile/tutorials/authorize-users-in-scripts-ios
[JavaScript and HTML]: ../get-started-with-push-js.md
[Windows Developer Preview registration steps for Mobile Services]: ../mobile-services-windows-developer-preview-registration.md
[wns object]: https://go.microsoft.com/fwlink/p/?LinkId=260591
[Notification Hubs Guidance]: https://msdn.microsoft.com/library/jj927170.aspx
[Notification Hubs How-To for iOS]: https://msdn.microsoft.com/library/jj927168.aspx
