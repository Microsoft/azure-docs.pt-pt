---
author: alexeystrakh
ms.author: alstrakh
ms.date: 06/11/2020
ms.service: notification-hubs
ms.topic: include
ms.openlocfilehash: e67f53e26f2081e1aa12f7c0e6a219c240f5027a
ms.sourcegitcommit: bcb962e74ee5302d0b9242b1ee006f769a94cfb8
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/07/2020
ms.locfileid: "86060503"
---
### <a name="configure-required-ios-packages"></a>Configurar pacotes iOS necessários

O pacote está [automaticamente ligado ao](https://github.com/react-native-community/cli/blob/master/docs/autolinking.md) construir a aplicação. Tudo o que precisa fazer é instalar as cápsulas nativas:

```bash
npx pod-install
```

### <a name="configure-infoplist-and-entitlementsplist"></a>Configure Info.plist e Direitos.plist

1. Entre na pasta "PushDemo/ios" e abra o espaço de trabalho "PushDemo.xcworkspace", selecione o projeto de topo "PushDemo" e selecione o separador "Assinar & Capacidades".

1. Atualizar o identificador de pacotes para corresponder ao valor utilizado no perfil de provisionamento.

1. Adicione duas novas Capacidades utilizando o botão - "+":

    - Capacidade do modo de fundo e notificações remotas.
    - Capacidade de notificações push

### <a name="handle-push-notifications-for-ios"></a>Lidar com notificações push para iOS

1. Abra "AppDelegate.h" e adicione a seguinte importação:

    ```objective-c
    #import <UserNotifications/UNUserNotificationCenter.h>
    ```

1. Lista de protocolos de atualização, suportados pelo "AppDeegate", `UNUserNotificationCenterDelegate` acrescentando:

    ```objective-c
    @interface AppDelegate : UIResponder <UIApplicationDelegate, RCTBridgeDelegate, UNUserNotificationCenterDelegate>
    ```

1. Abra "AppDeegate.m" e configuure todas as chamadas iOS necessárias:

    ```objective-c
    #import <UserNotifications/UserNotifications.h>
    #import <RNCPushNotificationIOS.h>

    ...

    // Required to register for notifications
    - (void)application:(UIApplication *)application didRegisterUserNotificationSettings:(UIUserNotificationSettings *)notificationSettings
    {
     [RNCPushNotificationIOS didRegisterUserNotificationSettings:notificationSettings];
    }

    // Required for the register event.
    - (void)application:(UIApplication *)application didRegisterForRemoteNotificationsWithDeviceToken:(NSData *)deviceToken
    {
     [RNCPushNotificationIOS didRegisterForRemoteNotificationsWithDeviceToken:deviceToken];
    }

    // Required for the notification event. You must call the completion handler after handling the remote notification.
    - (void)application:(UIApplication *)application didReceiveRemoteNotification:(NSDictionary *)userInfo
    fetchCompletionHandler:(void (^)(UIBackgroundFetchResult))completionHandler
    {
      [RNCPushNotificationIOS didReceiveRemoteNotification:userInfo fetchCompletionHandler:completionHandler];
    }

    // Required for the registrationError event.
    - (void)application:(UIApplication *)application didFailToRegisterForRemoteNotificationsWithError:(NSError *)error
    {
     [RNCPushNotificationIOS didFailToRegisterForRemoteNotificationsWithError:error];
    }

    // IOS 10+ Required for localNotification event
    - (void)userNotificationCenter:(UNUserNotificationCenter *)center
    didReceiveNotificationResponse:(UNNotificationResponse *)response
             withCompletionHandler:(void (^)(void))completionHandler
    {
      [RNCPushNotificationIOS didReceiveNotificationResponse:response];
      completionHandler();
    }

    // IOS 4-10 Required for the localNotification event.
    - (void)application:(UIApplication *)application didReceiveLocalNotification:(UILocalNotification *)notification
    {
     [RNCPushNotificationIOS didReceiveLocalNotification:notification];
    }

    //Called when a notification is delivered to a foreground app.
    -(void)userNotificationCenter:(UNUserNotificationCenter *)center willPresentNotification:(UNNotification *)notification withCompletionHandler:(void (^)(UNNotificationPresentationOptions options))completionHandler
    {
      completionHandler(UNAuthorizationOptionSound | UNAuthorizationOptionAlert | UNAuthorizationOptionBadge);
    }
    ```
