---
title: Enviar notificações push para aplicações iOS com Hubs de Notificação do Azure | Microsoft Docs
description: Neste tutorial, irá aprender a utilizar os Notification Hubs do Azure para enviar notificações push para uma aplicação iOS.
services: notification-hubs
documentationcenter: ios
keywords: notificação push, notificações push, notificações push do ios
author: jwargo
manager: patniko
editor: spelluru
ms.assetid: b7fcd916-8db8-41a6-ae88-fc02d57cb914
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: tutorial
ms.custom: mvc
ms.date: 01/04/2019
ms.author: jowargo
ms.openlocfilehash: 520d01327b5809d453bb777165899770ea4c130b
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57885038"
---
# <a name="tutorial-push-notifications-to-ios-apps-using-azure-notification-hubs"></a>Tutorial: Notificações push para aplicações iOS com Notification Hubs do Azure

[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

Neste tutorial, pode utilizar os Hubs de Notificação do Azure para enviar notificações push para uma aplicação iOS. Irá criar uma aplicação iOS em branco que recebe notificações push com o [Serviço Apple Push Notification (APNs)](https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html#//apple_ref/doc/uid/TP40008194-CH8-SW1).

Neste tutorial, siga os seguintes passos:

> [!div class="checklist"]
> * Gerar o ficheiro de pedido de assinatura de certificado
> * Pedir que a aplicação envie notificações push
> * Criar um perfil de aprovisionamento para a aplicação
> * Configurar o hub de notificação para notificações push do iOS
> * Ligar a aplicação iOS aos Hubs de Notificação
> * Enviar notificações push de teste
> * Verificar que a aplicação recebe notificações

O código de conclusão deste tutorial pode ser encontrado [no GitHub](https://github.com/Azure/azure-notificationhubs-samples/tree/master/iOS/GetStartedNH/GetStarted). 

## <a name="prerequisites"></a>Pré-requisitos

* Uma conta ativa do Azure. Se não tiver uma conta, pode [criar uma conta gratuita do Azure](https://azure.microsoft.com/free) em apenas alguns minutos.
* [Framework de Mensagens do Windows Azure]
* Versão mais recente do [Xcode].
* Um dispositivo compatível com iOS 10 (ou versão posterior)
* Associação ao [Programa de Programador da Apple](https://developer.apple.com/programs/)
  
  > [!NOTE]
  > Devido aos requisitos de configuração das notificações push, deve implementar e testar as notificações push num dispositivo iOS físico (iPhone ou iPad) em vez do simulador iOS.
  
A conclusão deste tutorial é um pré-requisito para todos os outros tutoriais dos Notification Hubs para aplicações iOS.

[!INCLUDE [Notification Hubs Enable Apple Push Notifications](../../includes/notification-hubs-enable-apple-push-notifications.md)]

## <a name="configure-your-notification-hub-for-ios-push-notifications"></a>Configurar o Notification Hub para notificações push do iOS

Nesta secção, irá criar um Hub de Notificação e configurar a autenticação com APNS através do certificado push **.p12** que criou anteriormente. Se pretender utilizar um Notification Hub que já tenha criado, pode avançar para o passo 5.

[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

### <a name="configure-your-notification-hub-with-apns-information"></a>Configurar um Hub de Notificação com informações de APNs

1. Em **Serviços de Notificação**, selecione **Apple (APNS)**.
2. Selecione **Certificado**.
3. Selecione o **ícone de ficheiro**.
4. Selecione o ficheiro **.p12** que exportou anteriormente.
5. Especifique a **palavra-passe** correta.
6. Selecione o modo **Sandbox**. Utilize o modo **Produção** apenas se pretender enviar notificações push a utilizadores que já tenham adquirido a aplicação da loja.

    ![Configurar certificação APNS no portal do Azure][7]

O Notification Hub já está configurado para trabalhar com APNs e tem as cadeias de ligação para registar a sua aplicação e enviar notificações push.O Notification Hub já está configurado para trabalhar com APNs e tem as cadeias de ligação para registar a sua aplicação e enviar notificações push.

## <a name="connect-your-ios-app-to-notification-hubs"></a>Ligar a aplicação iOS aos Notification Hubs

1. No Xcode, crie um novo projeto do iOS e selecione o modelo **Aplicação de Vista Única**.

    ![Xcode – Aplicação de Vista Única][8]

2. Ao definir as opções para o seu novo projeto, não se esqueça de utilizar o mesmo **Nome do Produto** e **Identificador de Organização** que utilizou quando definiu o identificador de pacote no portal de Programador da Apple.

    ![Xcode – opções do projeto][11]

3. Em Navegador de Projeto, clique no nome do projeto, clique no separador **Geral** e localize **Assinatura**. Certifique-se de que seleciona a Equipa adequada para a conta de Programador da Apple. XCode deve obter automaticamente o Perfil de Aprovisionamento que criou anteriormente com base no identificador de pacote.

    Se não vir o novo perfil de aprovisionamento que criou no Xcode, tente atualizar os perfis para a sua identidade de assinatura. Clique em **Xcode** na barra de menus, clique em **Preferências**, clique em **Conta** clique no botão **Ver Detalhes**, clique na sua identidade de assinatura e clique no botão atualizar no canto inferior direito.

    ![Xcode – perfil de aprovisionamento][9]

4. Selecione o separador **Capacidades** e certifique-se de ativar as Notificações Push

    ![Xcode – capacidades de push][12]

5. Adicione os módulos do SDK dos Notification Hubs do Azure.

   Pode integrar o SDK dos Notification Hubs do Azure na sua aplicação através de [Cocoapods](https://cocoapods.org) ou ao adicionar manualmente os binários ao seu projeto.

   - Integração através de Cocoapods

     Adicione as seguintes dependências ao seu `podfile` para incluir o SDK dos Notification Hubs do Azure na sua aplicação.

     ```ruby
     pod 'AzureNotificationHubs-iOS'
     ```

     Execute `pod install` instalar seu pod definida recentemente e abrir seus `.xcworkspace`.

     > [!NOTE]
     > Se vir um erro, como ```[!] Unable to find a specification for `AzureNotificationHubs-iOS` ``` durante a execução `pod install`, execute `pod repo update` para obter os pods mais recente a partir do repositório Cocoapods e, em seguida, executar `pod install`.

   - Integração através de Carthage

     Adicione as seguintes dependências ao seu `Cartfile` para incluir o SDK dos Notification Hubs do Azure na sua aplicação.

     ```ruby
     github "Azure/azure-notificationhubs-ios"
     ```

     Em seguida, atualizar e criar dependências:

     ```shell
     $ carthage update
     ```

     Para obter mais informações sobre como utilizar Carthage, consulte a [repositório do Carthage GitHub](https://github.com/Carthage/Carthage).

   - Integração ao copiar os binários no seu projeto

     1. Transfira o [SDK dos Notification Hubs do Azure](https://github.com/Azure/azure-notificationhubs-ios/releases) framework fornecida como um ficheiro zip e Descompacte-o.

     2. No Xcode, clique com o botão direito do rato no projeto e clique na opção **Adicionar Ficheiros a** para adicionar a pasta **WindowsAzureMessaging.framework** ao seu projeto Xcode. Selecione **Opções** e certifique-se de que **Copiar itens, se necessário** está selecionado e, em seguida, clique em **Adicionar**.

        ![Deszipar o Azure SDK][10]

6. Adicione um novo ficheiro de cabeçalho ao projeto com o nome `HubInfo.h`. Este ficheiro detém as constantes para o Hub de Notificação. Adicione as seguintes definições e substitua os marcadores de posição literais de cadeia pelo seu *nome do hub* e pela *DefaultListenSharedAccessSignature* anotado anteriormente.

    ```objc
    #ifndef HubInfo_h
    #define HubInfo_h

        #define HUBNAME @"<Enter the name of your hub>"
        #define HUBLISTENACCESS @"<Enter your DefaultListenSharedAccess connection string"

    #endif /* HubInfo_h */
    ```

7. Abra o ficheiro `AppDelegate.h` e adicione as diretivas de importação seguintes:

    ```objc
    #import <WindowsAzureMessaging/WindowsAzureMessaging.h>
    #import <UserNotifications/UserNotifications.h>
    #import "HubInfo.h"
    ```
8. No seu `AppDelegate.m` de ficheiros, adicione o seguinte código no `didFinishLaunchingWithOptions` método com base na sua versão do iOS. Este código regista o seu identificador de dispositivo com APNs:

    ```objc
    UIUserNotificationSettings *settings = [UIUserNotificationSettings settingsForTypes:UIUserNotificationTypeSound |
        UIUserNotificationTypeAlert | UIUserNotificationTypeBadge categories:nil];

    [[UIApplication sharedApplication] registerUserNotificationSettings:settings];
    [[UIApplication sharedApplication] registerForRemoteNotifications];
    ```

9. No mesmo ficheiro, adicione os seguintes métodos:

    ```objc
        - (void) application:(UIApplication *) application didRegisterForRemoteNotificationsWithDeviceToken:(NSData *) deviceToken {
        SBNotificationHub* hub = [[SBNotificationHub alloc] initWithConnectionString:HUBLISTENACCESS
                                    notificationHubPath:HUBNAME];

        [hub registerNativeWithDeviceToken:deviceToken tags:nil completion:^(NSError* error) {
            if (error != nil) {
                NSLog(@"Error registering for notifications: %@", error);
            }
            else {
                [self MessageBox:@"Registration Status" message:@"Registered"];
            }
        }];
        }

    -(void)MessageBox:(NSString *) title message:(NSString *)messageText
    {
        UIAlertController *alert = [UIAlertController alertControllerWithTitle:title message:messageText preferredStyle:UIAlertControllerStyleAlert];
        UIAlertAction *okAction = [UIAlertAction actionWithTitle:@"OK" style:UIAlertActionStyleDefault handler:nil];
        [alert addAction:okAction];
        [[[[UIApplication sharedApplication] keyWindow] rootViewController] presentViewController:alert animated:YES completion:nil];
    }
    ```

    Este código liga-se ao Notification Hub utilizando as informações de ligação que especificou em HubInfo.h. Em seguida, atribui o token do dispositivo Notification Hub para que este possa enviar notificações.

10. No mesmo ficheiro, adicione o método seguinte para apresentar um **UIAlert** caso a notificação seja recebida enquanto a aplicação está ativa:

    ```objc
    - (void)application:(UIApplication *)application didReceiveRemoteNotification: (NSDictionary *)userInfo {
        NSLog(@"%@", userInfo);
        [self MessageBox:@"Notification" message:[[userInfo objectForKey:@"aps"] valueForKey:@"alert"]];
    }
    ```

11. Para confirmar que não existem falhas, crie e execute a aplicação no dispositivo.

## <a name="send-test-push-notifications"></a>Enviar notificações push de teste

Pode testar a receção das notificações na aplicação com a opção *Envio de Teste* no [portal do Azure]. Esta ação envia uma notificação push de teste para o seu dispositivo.

![Portal do Azure – Envio de Teste][30]

[!INCLUDE [notification-hubs-sending-notifications-from-the-portal](../../includes/notification-hubs-sending-notifications-from-the-portal.md)]

## <a name="verify-that-your-app-receives-push-notifications"></a>Verificar que a aplicação recebe notificações push

Para testar as notificações push no iOS, tem de implementar a aplicação para um dispositivo iOS físico. Não pode enviar notificações push da Apple utilizando o Simulator iOS.

1. Execute a aplicação, verifique se o registo é concluído com êxito e prima **OK**.

    ![Teste do Registo de Notificações Push da Aplicação iOS][33]

2. Em seguida, envie uma notificação push de teste a partir do [portal do Azure], conforme descrito na secção anterior.

3. A notificação push é enviada a todos os dispositivos que estejam registados para receber as notificações do Notification Hub específico.

    ![Teste de Receção de Notificações Push da Aplicação iOS][35]

## <a name="next-steps"></a>Passos Seguintes

Neste exemplo simples, difundiu notificações push para todos os dispositivos iOS. Para saber como enviar notificações push para dispositivos iOS específicos, avance para o seguinte tutorial:

> [!div class="nextstepaction"]
>[Enviar notificações push para dispositivos específicos](notification-hubs-ios-xplat-segmented-apns-push-notification.md)

<!-- Images. -->
[6]: ./media/notification-hubs-ios-get-started/notification-hubs-apple-config.png
[7]: ./media/notification-hubs-ios-get-started/notification-hubs-apple-config-cert.png
[8]: ./media/notification-hubs-ios-get-started/notification-hubs-create-ios-app.png
[9]: ./media/notification-hubs-ios-get-started/notification-hubs-create-ios-app2.png
[10]: ./media/notification-hubs-ios-get-started/notification-hubs-create-ios-app3.png
[11]: ./media/notification-hubs-ios-get-started/notification-hubs-xcode-product-name.png
[12]: ./media/notification-hubs-ios-get-started/notification-hubs-enable-push.png
[30]: ./media/notification-hubs-ios-get-started/notification-hubs-test-send.png
[31]: ./media/notification-hubs-ios-get-started/notification-hubs-ios-ui.png
[32]: ./media/notification-hubs-ios-get-started/notification-hubs-storyboard-view.png
[33]: ./media/notification-hubs-ios-get-started/notification-hubs-test1.png
[35]: ./media/notification-hubs-ios-get-started/notification-hubs-test3.png

<!-- URLs. -->
[Framework de Mensagens do Windows Azure]: https://go.microsoft.com/fwlink/?LinkID=799698&clcid=0x409
[Mobile Services iOS SDK]: https://go.microsoft.com/fwLink/?LinkID=266533
[Submit an app page]: https://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: https://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: https://go.microsoft.com/fwlink/p/?LinkId=262253
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started-ios
[Notification Hubs Guidance]: https://msdn.microsoft.com/library/jj927170.aspx
[XCode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[iOS Provisioning Portal]: https://go.microsoft.com/fwlink/p/?LinkId=272456
[Get started with push notifications in Mobile Services]: ../mobile-services-javascript-backend-ios-get-started-push.md
[Azure Notification Hubs Notify Users for iOS with .NET backend]: notification-hubs-aspnet-backend-ios-apple-apns-notification.md
[Use Notification Hubs to send breaking news]: notification-hubs-ios-xplat-segmented-apns-push-notification.md
[Local and Push Notification Programming Guide]: https://developer.apple.com/library/mac/#documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html#//apple_ref/doc/uid/TP40008194-CH100-SW1
[Portal do Azure]: https://portal.azure.com
