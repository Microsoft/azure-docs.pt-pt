---
title: Enviar notificações localizadas por push para dispositivos iOS usando hubs de notificação do Azure | Microsoft Docs
description: Saiba como usar notificações localizadas por push para dispositivos iOS usando os hubs de notificação do Azure.
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
ms.openlocfilehash: 8eb4cf5e12c16c3c164ecce41a84a9cd32fd85ee
ms.sourcegitcommit: 7df70220062f1f09738f113f860fad7ab5736e88
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/24/2019
ms.locfileid: "71211891"
---
# <a name="tutorial-push-localized-notifications-to-ios-devices-using-azure-notification-hubs"></a>Tutorial: Enviar notificações localizadas por push para dispositivos iOS usando os hubs de notificação do Azure

> [!div class="op_single_selector"]
> * [Windows Store C#](notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification.md)
> * [iOS](notification-hubs-ios-xplat-localized-apns-push-notification.md)

Este tutorial mostra como usar o recurso de [modelos](notification-hubs-templates-cross-platform-push-messages.md) dos hubs de notificação do Azure para transmitir notificações de últimas notícias que foram localizadas por idioma e dispositivo. Neste tutorial, você começa com o aplicativo iOS criado em [Use Notification Hubs to send breaking news (Utilizar Hubs de Notificação para enviar notícias de última hora)]. Ao concluir, você pode se registrar em categorias nas quais está interessado, especificar um idioma no qual receber as notificações e receber notificações por push para as categorias selecionadas nesse idioma.

Há duas partes nesse cenário:

* o aplicativo iOS permite que os dispositivos cliente especifiquem um idioma e assinem diferentes categorias de notícias.
* O back-end transmite as notificações, usando os recursos de **marca** e **modelo** dos hubs de notificação do Azure.

Neste tutorial, siga os passos seguintes:

> [!div class="checklist"]
> * Atualizar a interface do usuário do aplicativo
> * Compilar o aplicativo iOS
> * Enviar notificações de modelo localizadas do aplicativo de console do .NET
> * Enviar notificações de modelo localizadas do dispositivo

## <a name="overview"></a>Descrição geral

Em [Use Notification Hubs to send breaking news (Utilizar Hubs de Notificação para enviar notícias de última hora)], você criou um aplicativo que usava **marcas** para assinar notificações para diferentes categorias de notícias. Muitos aplicativos, no entanto, têm como alvo vários mercados e exigem localização. Isso significa que o conteúdo das notificações em si precisa ser localizado e entregue ao conjunto correto de dispositivos. Este tutorial mostra como usar o recurso de **modelo** dos hubs de notificação para entregar facilmente notificações de últimas notícias localizadas.

> [!NOTE]
> Uma maneira de enviar notificações localizadas é criar várias versões de cada marca. Por exemplo, para dar suporte a inglês, francês e Mandarim, você precisaria de três marcas diferentes para notícias mundiais: "world_en", "world_fr" e "world_ch". Em seguida, você precisaria enviar uma versão localizada do mundo notícias para cada uma dessas marcas. Neste tópico, você usa modelos para evitar a proliferação de marcas e o requisito de envio de várias mensagens.

Os modelos são uma maneira de especificar como um dispositivo específico deve receber uma notificação. O modelo especifica o formato de payload exato ao consultar as propriedades que fazem parte da mensagem enviada pelo back-end da sua aplicação. No seu caso, você envia uma mensagem independente de localidade contendo todos os idiomas com suporte:

```json
{
    "News_English": "...",
    "News_French": "...",
    "News_Mandarin": "..."
}
```

Em seguida, você garante que os dispositivos se registrem com um modelo que se refere à propriedade correta. Por exemplo, um aplicativo iOS que deseja registrar para as notícias francesas registra usando a seguinte sintaxe:

```json
{
    aps: {
        alert: "$(News_French)"
    }
}
```

Para obter mais informações sobre modelos, consulte o artigo [modelos](notification-hubs-templates-cross-platform-push-messages.md) .

## <a name="prerequisites"></a>Pré-requisitos

* Conclua o tutorial [notificações por push para dispositivos IOS específicos](notification-hubs-ios-xplat-segmented-apns-push-notification.md) e tenha o código disponível, pois este tutorial se baseia diretamente no código.
* O Visual Studio 2019 é opcional.

## <a name="update-the-app-user-interface"></a>Atualizar a interface do usuário do aplicativo

Nesta seção, você modificará o aplicativo de últimas notícias criado no tópico [Use Notification Hubs to send breaking news (Utilizar Hubs de Notificação para enviar notícias de última hora)] para enviar últimas notícias localizadas usando modelos.

Em seu `MainStoryboard_iPhone.storyboard`, adicione um controle segmentado com os três idiomas: Inglês, francês e Mandarim.

![Criando o storyboard da interface do usuário do iOS][13]

Em seguida, certifique-se de adicionar um IBOutlet em seu ViewController. h, conforme mostrado na imagem a seguir:

![Criar saídas para os comutadores][14]

## <a name="build-the-ios-app"></a>Compilar o aplicativo iOS

1. No, adicione o `retrieveLocale` método e modifique os métodos Store e Subscribe, conforme mostrado no código a seguir: `Notification.h`

    ```objc
    - (void) storeCategoriesAndSubscribeWithLocale:(int) locale categories:(NSSet*) categories completion: (void (^)(NSError* error))completion;

    - (void) subscribeWithLocale:(int) locale categories:(NSSet*) categories completion:(void (^)(NSError *))completion;

    - (NSSet*) retrieveCategories;

    - (int) retrieveLocale;
    ```
    No, modifique o `storeCategoriesAndSubscribe` método, adicionando o `locale` parâmetro e armazenando-o nos padrões do usuário: `Notification.m`

    ```objc
    - (void) storeCategoriesAndSubscribeWithLocale:(int) locale categories:(NSSet *)categories completion:(void (^)(NSError *))completion {
        NSUserDefaults* defaults = [NSUserDefaults standardUserDefaults];
        [defaults setValue:[categories allObjects] forKey:@"BreakingNewsCategories"];
        [defaults setInteger:locale forKey:@"BreakingNewsLocale"];
        [defaults synchronize];

        [self subscribeWithLocale: locale categories:categories completion:completion];
    }
    ```

    Em seguida, modifique o método *Subscribe* para incluir a localidade:

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

    Você usa o método `registerTemplateWithDeviceToken`, em vez `registerNativeWithDeviceToken`de. Ao se registrar para um modelo, você precisa fornecer o modelo JSON e também um nome para o modelo (já que o aplicativo talvez queira registrar modelos diferentes). Certifique-se de registrar suas categorias como marcas, pois você deseja ter certeza de receber as notificações para essas notícias.

    Adicione um método para recuperar a localidade das configurações padrão do usuário:

    ```objc
    - (int) retrieveLocale {
        NSUserDefaults* defaults = [NSUserDefaults standardUserDefaults];

        int locale = [defaults integerForKey:@"BreakingNewsLocale"];

        return locale < 0?0:locale;
    }
    ```

2. Agora que você modificou `Notifications` a classe, é necessário certificar-se de `ViewController` que o utiliza o novo `UISegmentControl`. Adicione a seguinte linha no `viewDidLoad` método para ter certeza de mostrar a localidade selecionada no momento:

    ```objc
    self.Locale.selectedSegmentIndex = [notifications retrieveLocale];
    ```

    Em seguida, em `subscribe` seu método, altere sua chamada para `storeCategoriesAndSubscribe` o para o seguinte código:

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

3. Por fim, você precisa atualizar o `didRegisterForRemoteNotificationsWithDeviceToken` método em seu AppDelegate. m, para que você possa atualizar corretamente seu registro quando seu aplicativo for iniciado. Altere sua chamada para o `subscribe` método de notificações com o seguinte código:

    ```obj-c
    NSSet* categories = [self.notifications retrieveCategories];
    int locale = [self.notifications retrieveLocale];
    [self.notifications subscribeWithLocale: locale categories:categories completion:^(NSError* error) {
        if (error != nil) {
            NSLog(@"Error registering for notifications: %@", error);
        }
    }];
    ```

## <a name="optional-send-localized-template-notifications-from-net-console-app"></a>adicional Enviar notificações de modelo localizadas do aplicativo de console do .NET

[!INCLUDE [notification-hubs-localized-back-end](../../includes/notification-hubs-localized-back-end.md)]

## <a name="optional-send-localized-template-notifications-from-the-device"></a>adicional Enviar notificações de modelo localizadas do dispositivo

Se você não tiver acesso ao Visual Studio ou quiser apenas testar o envio das notificações de modelo localizadas diretamente do aplicativo no dispositivo. Você pode adicionar os parâmetros de modelo localizados ao `SendNotificationRESTAPI` método definido no tutorial anterior.

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

Neste tutorial, você enviou notificações localizadas para dispositivos iOS. Para saber como enviar notificações por push a usuários específicos de aplicativos iOS, avance para o tutorial a seguir:

> [!div class="nextstepaction"]
>[Push notifications to specific users](notification-hubs-aspnet-backend-ios-apple-apns-notification.md) (Enviar notificações para utilizadores específicos)

<!-- Images. -->
[13]: ./media/notification-hubs-ios-send-localized-breaking-news/ios_localized1.png
[14]: ./media/notification-hubs-ios-send-localized-breaking-news/ios_localized2.png

<!-- URLs. -->
[How To: Service Bus Notification Hubs (iOS Apps)]: https://msdn.microsoft.com/library/jj927168.aspx
[Use Notification Hubs to send breaking news (Utilizar Hubs de Notificação para enviar notícias de última hora)]: notification-hubs-ios-xplat-segmented-apns-push-notification.md
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
