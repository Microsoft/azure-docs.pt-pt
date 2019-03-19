---
title: Enviar notificações localizadas para dispositivos iOS com Notification Hubs do Azure | Documentos da Microsoft
description: Saiba como utilizar notificações push localizada para dispositivos iOS com Notification Hubs do Azure.
services: notification-hubs
documentationcenter: ios
author: jwargo
manager: patniko
editor: spelluru
ms.assetid: 484914b5-e081-4a05-a84a-798bbd89d428
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ios
ms.devlang: objective-c
ms.topic: article
ms.date: 01/04/2019
ms.author: jowargo
ms.openlocfilehash: 527e9979b624970dd55b4300fe63c27386640ac4
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57886614"
---
# <a name="tutorial-push-localized-notifications-to-ios-devices-using-azure-notification-hubs"></a>Tutorial: Notificações push localizada para dispositivos iOS com Notification Hubs do Azure

> [!div class="op_single_selector"]
> * [Windows Store C#](notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification.md)
> * [iOS](notification-hubs-ios-xplat-localized-apns-push-notification.md)

Este tutorial mostra-lhe como utilizar o [modelos](notification-hubs-templates-cross-platform-push-messages.md) funcionalidade dos Hubs de notificação do Azure para difundir notificações de notícias de última hora que foram localizadas por idioma e o dispositivo. Neste tutorial, começa com a aplicação iOS criada no [Use Notification Hubs to send breaking news (Utilizar Hubs de Notificação para enviar notícias de última hora)]. Quando terminar, pode se registrar para categorias de que interesse, especificar um idioma no qual pretende receber as notificações e receber apenas notificações push para as categorias selecionadas nesse idioma.

Existem duas partes para este cenário:

* aplicação iOS permite cliente dispositivos para especificar um idioma e subscrever as categorias de notícias de última hora diferentes;
* O back-end difunde as notificações, utilizando o **tag** e **modelo** funcionalidades dos Hubs de notificação do Azure.

Neste tutorial, siga os passos seguintes:

> [!div class="checklist"]
> * Atualizar a interface de utilizador de aplicação
> * Compilar a aplicação para iOS
> * Enviar notificações de localizado do modelo de aplicação de consola .NET
> * Enviar notificações localizadas de modelo do dispositivo

## <a name="overview"></a>Descrição geral

Na [Use Notification Hubs to send breaking news (Utilizar Hubs de Notificação para enviar notícias de última hora)], criou uma aplicação que utilizada **etiquetas** subscrever notificações para as categorias de notícias diferentes. Muitas aplicações, no entanto, vários mercados de destino e requerem localização. Isso significa que o conteúdo das notificações próprios tem de estar localizado e entregues para o conjunto correto de dispositivos. Este tutorial mostra-lhe como utilizar o **modelo** funcionalidade dos Hubs de notificação para fornecer facilmente notificações de notícias de última hora localizada.

> [!NOTE]
> É uma forma de enviar notificações localizadas criar várias versões de cada etiqueta. Por exemplo, para oferecer suporte a inglês, francês e Mandarim, terá de ter três etiquetas diferentes para notícias do mundo: "world_en", "world_fr," e "world_ch". Em seguida, terá de enviar uma versão localizada de notícias do mundo para cada uma dessas marcas. Neste tópico, utilize modelos para evitar a proliferação de etiquetas e o requisito de enviar várias mensagens.

Num alto nível, os modelos são uma forma de especificar a forma como um dispositivo específico deve receber uma notificação. O modelo especifica o formato de payload exato ao consultar as propriedades que fazem parte da mensagem enviada pelo back-end da sua aplicação. No seu caso, envia uma mensagem de independente de localidade que contém todos os idiomas suportados:

```json
{
    "News_English": "...",
    "News_French": "...",
    "News_Mandarin": "..."
}
```

Em seguida, é garantir que os dispositivos registrar com um modelo que se refere-se para a propriedade correta. Por exemplo, uma aplicação iOS que pretende registar de notícias francês registra utilizando a seguinte sintaxe:

```json
{
    aps: {
        alert: "$(News_French)"
    }
}
```

Para obter mais informações sobre modelos, consulte [modelos](notification-hubs-templates-cross-platform-push-messages.md) artigo.

## <a name="prerequisites"></a>Pré-requisitos

* Concluir o [notificações Push para dispositivos iOS específico](notification-hubs-ios-xplat-segmented-apns-push-notification.md) tutorial e ter o código disponível, porque este tutorial baseia-se diretamente nesse código.
* Visual Studio 2017 é opcional.

## <a name="update-the-app-user-interface"></a>Atualizar a interface de utilizador de aplicação

Nesta secção, modificar a aplicação de notícias de última hora que criou no tópico [Use Notification Hubs to send breaking news (Utilizar Hubs de Notificação para enviar notícias de última hora)] enviar localizadas através de modelos de notícias de última hora.

No seu `MainStoryboard_iPhone.storyboard`, adicione um controlo segmentados com os três idiomas: Inglês, francês e Mandarim.

![Criar o storyboard de interface do Usuário do iOS][13]

Em seguida, certifique-se adicionar um IBOutlet no seu viewcontroller. H, conforme mostrado na imagem seguinte:

![Criar para os comutadores de comunicação][14]

## <a name="build-the-ios-app"></a>Compilar a aplicação para iOS

1. No seu `Notification.h` adicione o `retrieveLocale` método e modificar o arquivo e subscrever métodos, conforme mostrado no seguinte código:

    ```objc
    - (void) storeCategoriesAndSubscribeWithLocale:(int) locale categories:(NSSet*) categories completion: (void (^)(NSError* error))completion;

    - (void) subscribeWithLocale:(int) locale categories:(NSSet*) categories completion:(void (^)(NSError *))completion;

    - (NSSet*) retrieveCategories;

    - (int) retrieveLocale;
    ```
    No seu `Notification.m`, modifique o `storeCategoriesAndSubscribe` método, adicionando o `locale` parâmetro e armazená-la nas predefinições de utilizador:

    ```objc
    - (void) storeCategoriesAndSubscribeWithLocale:(int) locale categories:(NSSet *)categories completion:(void (^)(NSError *))completion {
        NSUserDefaults* defaults = [NSUserDefaults standardUserDefaults];
        [defaults setValue:[categories allObjects] forKey:@"BreakingNewsCategories"];
        [defaults setInteger:locale forKey:@"BreakingNewsLocale"];
        [defaults synchronize];

        [self subscribeWithLocale: locale categories:categories completion:completion];
    }
    ```

    Em seguida, modifique o *subscrever* método para incluir a Localidade:

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

    Use o método `registerTemplateWithDeviceToken`, em vez de `registerNativeWithDeviceToken`. Quando se registra para um modelo, terá de fornecer o modelo json e também um nome para o modelo (como a aplicação deve registar modelos diferentes). Certifique-se registrar seu categorias como etiquetas, desejar certificar-se receber as notificações para obter as notícias.

    Adicione um método para recuperar a Localidade das predefinições de utilizador:

    ```objc
    - (int) retrieveLocale {
        NSUserDefaults* defaults = [NSUserDefaults standardUserDefaults];

        int locale = [defaults integerForKey:@"BreakingNewsLocale"];

        return locale < 0?0:locale;
    }
    ```

2. Agora que modificou o `Notifications` classe, tem para se certificar de que o `ViewController` faz uso da nova `UISegmentControl`. Adicione a seguinte linha no `viewDidLoad` método para certificar-se de que mostram a Localidade atualmente selecionado:

    ```objc
    self.Locale.selectedSegmentIndex = [notifications retrieveLocale];
    ```

    Em seguida, no seu `subscribe` método, alterar a sua chamada para o `storeCategoriesAndSubscribe` para o código a seguir:

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

3. Por fim, é necessário atualizar o `didRegisterForRemoteNotificationsWithDeviceToken` método no seu Appdelegate, para que corretamente, pode atualizar o registo quando a aplicação for iniciada. Alterar a sua chamada para o `subscribe` método de notificações com o código a seguir:

    ```obj-c
    NSSet* categories = [self.notifications retrieveCategories];
    int locale = [self.notifications retrieveLocale];
    [self.notifications subscribeWithLocale: locale categories:categories completion:^(NSError* error) {
        if (error != nil) {
            NSLog(@"Error registering for notifications: %@", error);
        }
    }];
    ```

## <a name="optional-send-localized-template-notifications-from-net-console-app"></a>(opcional) Enviar notificações de localizado do modelo de aplicação de consola .NET

[!INCLUDE [notification-hubs-localized-back-end](../../includes/notification-hubs-localized-back-end.md)]

## <a name="optional-send-localized-template-notifications-from-the-device"></a>(opcional) Enviar notificações localizadas de modelo do dispositivo

Se não tem acesso ao Visual Studio, ou apenas pretende teste enviar as notificações de modelo localizada diretamente a partir da aplicação no dispositivo. Pode adicionar parâmetros do modelo localizada para a `SendNotificationRESTAPI` método definido no tutorial anterior.

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

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, enviou notificações localizadas para dispositivos iOS. Para saber como enviar notificações push para utilizadores específicos de aplicações iOS, avance para o tutorial seguinte:

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
