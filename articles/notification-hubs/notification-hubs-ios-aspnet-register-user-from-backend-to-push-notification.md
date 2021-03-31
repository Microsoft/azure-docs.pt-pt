---
title: Registe o utilizador atual para notificações push utilizando a API Web | Microsoft Docs
description: Saiba como solicitar o registo de notificação push numa aplicação iOS com Azure Notification Hubs quando o registo é realizado por ASP.NET Web API.
services: notification-hubs
documentationcenter: ios
author: sethmanheim
manager: femila
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ios
ms.devlang: objective-c
ms.topic: article
ms.date: 01/04/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: 0819f5196fffca25a840dc16d1df04cdd0a55029
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "86223317"
---
# <a name="register-the-current-user-for-push-notifications-by-using-aspnet"></a>Registe o utilizador atual para notificações push utilizando ASP.NET

> [!div class="op_single_selector"]
> * [iOS](notification-hubs-ios-aspnet-register-user-from-backend-to-push-notification.md)

## <a name="overview"></a>Descrição Geral

Este tópico mostra-lhe como solicitar o registo de notificação push com os Hubs de Notificação Azure quando o registo é realizado por ASP.NET Web API. Este tópico alarga o tutorial [Notificar os utilizadores com Centros de Notificação]. Já deve ter completado as etapas necessárias nesse tutorial para criar o serviço móvel autenticado. Para obter mais informações sobre o cenário de notificação dos utilizadores, consulte [notificar os utilizadores com Os Centros de Notificação].

## <a name="update-your-app"></a>Atualizar a sua aplicação

1. No seu MainStoryboard_iPhone.storyboard, adicione os seguintes componentes da biblioteca do objeto:

   * **Etiqueta**: "Empurre para o utilizador com centros de notificação"
   * **Etiqueta**: "InstallationId"
   * **Etiqueta**: "Utilizador"
   * **Campo de Texto**: "Utilizador"
   * **Etiqueta**: "Password"
   * **Campo de Texto**: "Password"
   * **Botão**: "Login"

     Neste ponto, o seu storyboard parece o seguinte:

     ![Screenshot da aplicação MainStoryboard_iPhone.storyboard com os componentes adicionados.][0]

2. No editor assistente, crie tomadas para todos os controlos comutados e ligue-os, ligue os campos de texto ao Controlador de Visualização (delegado) e crie uma **Ação** para o botão **de login.**

    ![Screenshot do editor assistente na aplicação MainStoryboard_iPhone.storyboard][1]

    O seu ficheiro BreakingNewsViewController.h deve agora conter o seguinte código:

    ```objc
    @property (weak, nonatomic) IBOutlet UILabel *installationId;
    @property (weak, nonatomic) IBOutlet UITextField *User;
    @property (weak, nonatomic) IBOutlet UITextField *Password;

    - (IBAction)login:(id)sender;
    ```
3. Crie uma classe nomeada `DeviceInfo` , e copie o seguinte código na secção de interface do ficheiro DeviceInfo.h:

    ```objc
    @property (readonly, nonatomic) NSString* installationId;
    @property (nonatomic) NSData* deviceToken;
    ```
4. Copie o seguinte código na secção de implementação do ficheiro .m DeviceInfo:

    ```objc
    @synthesize installationId = _installationId;

    - (id)init {
        if (!(self = [super init]))
            return nil;

        NSUserDefaults *defaults = [NSUserDefaults standardUserDefaults];
        _installationId = [defaults stringForKey:@"PushToUserInstallationId"];
        if(!_installationId) {
            CFUUIDRef newUUID = CFUUIDCreate(kCFAllocatorDefault);
            _installationId = (__bridge_transfer NSString *)CFUUIDCreateString(kCFAllocatorDefault, newUUID);
            CFRelease(newUUID);

            //store the install ID so we don't generate a new one next time
            [defaults setObject:_installationId forKey:@"PushToUserInstallationId"];
            [defaults synchronize];
        }

        return self;
    }

    - (NSString*)getDeviceTokenInHex {
        const unsigned *tokenBytes = [[self deviceToken] bytes];
        NSString *hexToken = [NSString stringWithFormat:@"%08X%08X%08X%08X%08X%08X%08X%08X",
                                ntohl(tokenBytes[0]), ntohl(tokenBytes[1]), ntohl(tokenBytes[2]),
                                ntohl(tokenBytes[3]), ntohl(tokenBytes[4]), ntohl(tokenBytes[5]),
                                ntohl(tokenBytes[6]), ntohl(tokenBytes[7])];
        return hexToken;
    }
    ```

5. Em PushToUserAppDeegate.h, adicione o seguinte singleton de propriedade:

    ```objc
    @property (strong, nonatomic) DeviceInfo* deviceInfo;
    ```
6. No `didFinishLaunchingWithOptions` método em PushToUserAppDeegate.m, adicione o seguinte código:

    ```objc
    self.deviceInfo = [[DeviceInfo alloc] init];

    [[UIApplication sharedApplication] registerForRemoteNotificationTypes: UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound];
    ```

    A primeira linha inicializa o `DeviceInfo` singleton. A segunda linha inicia o registo de notificações push, que já está presente se já tiver concluído o tutorial [Get Started com Notification Hubs.]
7. No PushToUserAppDeegate.m, implemente o método `didRegisterForRemoteNotificationsWithDeviceToken` no seu AppDeegate e adicione o seguinte código:

    ```objc
    self.deviceInfo.deviceToken = deviceToken;
    ```

    Isto define o símbolo do dispositivo para o pedido.

   > [!NOTE]
   > Neste momento, não deverá haver qualquer outro código neste método. Se já tiver uma chamada para o `registerNativeWithDeviceToken` método que foi adicionado quando completou as [notificações de push para aplicações iOS usando o tutorial de Azure Notification Hubs,](ios-sdk-get-started.md) deve comentar ou remover essa chamada.

8. No `PushToUserAppDelegate.m` ficheiro, adicione o seguinte método de manipulador:

    ```objc
    * (void) application:(UIApplication *) application didReceiveRemoteNotification:(NSDictionary *)userInfo {
       NSLog(@"%@", userInfo);
       UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Notification" message:
                             [userInfo objectForKey:@"inAppMessage"] delegate:nil cancelButtonTitle:
                             @"OK" otherButtonTitles:nil, nil];
       [alert show];
    }
    ```

    Este método exibe um alerta na UI quando a sua aplicação recebe notificações enquanto está em execução.

9. Abra o `PushToUserViewController.m` ficheiro e devolva o teclado na seguinte implementação:

    ```objc
    - (BOOL)textFieldShouldReturn:(UITextField *)theTextField {
        if (theTextField == self.User || theTextField == self.Password) {
            [theTextField resignFirstResponder];
        }
        return YES;
    }
    ```

10. No `viewDidLoad` método do `PushToUserViewController.m` ficheiro, inicialize o `installationId` rótulo da seguinte forma:

    ```objc
    DeviceInfo* deviceInfo = [(PushToUserAppDelegate*)[[UIApplication sharedApplication]delegate] deviceInfo];
    Self.installationId.text = deviceInfo.installationId;
    ```

11. Adicione as seguintes propriedades em interface `PushToUserViewController.m` em:

    ```objc
    @property (readonly) NSOperationQueue* downloadQueue;
    - (NSString*)base64forData:(NSData*)theData;
    ```

12. Em seguida, adicione a seguinte implementação:

    ```objc
    - (NSOperationQueue *)downloadQueue {
        if (!_downloadQueue) {
            _downloadQueue = [[NSOperationQueue alloc] init];
            _downloadQueue.name = @"Download Queue";
            _downloadQueue.maxConcurrentOperationCount = 1;
        }
        return _downloadQueue;
    }

    // base64 encoding
    - (NSString*)base64forData:(NSData*)theData
    {
        const uint8_t* input = (const uint8_t*)[theData bytes];
        NSInteger length = [theData length];

        static char table[] = "ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789+/=";

        NSMutableData* data = [NSMutableData dataWithLength:((length + 2) / 3) * 4];
        uint8_t* output = (uint8_t*)data.mutableBytes;

        NSInteger i;
        for (i=0; i < length; i += 3) {
            NSInteger value = 0;
            NSInteger j;
            for (j = i; j < (i + 3); j++) {
                value <<= 8;

                if (j < length) {
                    value |= (0xFF & input[j]);
                }
            }

            NSInteger theIndex = (i / 3) * 4;
            output[theIndex + 0] =                    table[(value >> 18) & 0x3F];
            output[theIndex + 1] =                    table[(value >> 12) & 0x3F];
            output[theIndex + 2] = (i + 1) < length ? table[(value >> 6)  & 0x3F] : '=';
            output[theIndex + 3] = (i + 2) < length ? table[(value >> 0)  & 0x3F] : '=';
        }

        return [[NSString alloc] initWithData:data encoding:NSASCIIStringEncoding];
    }
    ```

13. Copie o seguinte código no `login` método de manipulador criado por XCode:

    ```objc
    DeviceInfo* deviceInfo = [(PushToUserAppDelegate*)[[UIApplication sharedApplication]delegate] deviceInfo];

    // build JSON
    NSString* json = [NSString stringWithFormat:@"{\"platform\":\"ios\", \"instId\":\"%@\", \"deviceToken\":\"%@\"}", deviceInfo.installationId, [deviceInfo getDeviceTokenInHex]];

    // build auth string
    NSString* authString = [NSString stringWithFormat:@"%@:%@", self.User.text, self.Password.text];

    NSMutableURLRequest* request = [NSMutableURLRequest requestWithURL:[NSURL URLWithString:@"http://nhnotifyuser.azurewebsites.net/api/register"]];
    [request setHTTPMethod:@"POST"];
    [request setHTTPBody:[json dataUsingEncoding:NSUTF8StringEncoding]];
    [request addValue:[@([json lengthOfBytesUsingEncoding:NSUTF8StringEncoding]) description] forHTTPHeaderField:@"Content-Length"];
    [request addValue:@"application/json" forHTTPHeaderField:@"Content-Type"];
    [request addValue:[NSString stringWithFormat:@"Basic %@",[self base64forData:[authString dataUsingEncoding:NSUTF8StringEncoding]]] forHTTPHeaderField:@"Authorization"];

    // connect with POST
    [NSURLConnection sendAsynchronousRequest:request queue:[self downloadQueue] completionHandler:^(NSURLResponse* response, NSData* data, NSError* error) {
        // add UIAlert depending on response.
        if (error != nil) {
            NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*)response;
            if ([httpResponse statusCode] == 200) {
                UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Back-end registration" message:@"Registration successful" delegate:nil cancelButtonTitle: @"OK" otherButtonTitles:nil, nil];
                [alert show];
            } else {
                NSLog(@"status: %ld", (long)[httpResponse statusCode]);
            }
        } else {
            NSLog(@"error: %@", error);
        }
    }];
    ```

    Este método obtém um ID de instalação e um canal para notificações push e envia-o, juntamente com o tipo de dispositivo, para o método API web autenticado que cria um registo em Centros de Notificação. Esta API web foi definida em [Notificar utilizadores com Centros de Notificação].

Agora que a aplicação do cliente foi atualizada, volte aos [utilizadores da Notificação com Os Centros de Notificação] e atualize o serviço móvel para enviar notificações utilizando os Centros de Notificação.

<!-- Anchors. -->

<!-- Images. -->
[0]: ./media/notification-hubs-ios-aspnet-register-user-push-notifications/notification-hub-user-aspnet-ios1.png
[1]: ./media/notification-hubs-ios-aspnet-register-user-push-notifications/notification-hub-user-aspnet-ios2.png

<!-- URLs. -->
[Notificar os utilizadores com centros de notificação]: notification-hubs-aspnet-backend-ios-apple-apns-notification.md
[Começar com centros de notificação]: ios-sdk-get-started.md
