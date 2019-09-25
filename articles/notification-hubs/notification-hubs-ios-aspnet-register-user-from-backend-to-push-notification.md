---
title: Registrar o usuário atual para notificações por push usando a API Web | Microsoft Docs
description: Saiba como solicitar o registro de notificação por push em um aplicativo iOS com hubs de notificação do Azure quando o registro é executado por ASP.NET Web API.
services: notification-hubs
documentationcenter: ios
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: 4e3772cf-20db-4b9f-bb74-886adfaaa65d
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ios
ms.devlang: objective-c
ms.topic: article
ms.date: 01/04/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: 3fec04a1a45f8b154e27a1e5303e44111f4cb421
ms.sourcegitcommit: 7df70220062f1f09738f113f860fad7ab5736e88
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/24/2019
ms.locfileid: "71211866"
---
# <a name="register-the-current-user-for-push-notifications-by-using-aspnet"></a>Registrar o usuário atual para notificações por push usando ASP.NET

> [!div class="op_single_selector"]
> * [iOS](notification-hubs-ios-aspnet-register-user-from-backend-to-push-notification.md)

## <a name="overview"></a>Descrição geral

Este tópico mostra como solicitar o registro de notificação por push com os hubs de notificação do Azure quando o registro é executado pelo ASP.NET Web API. Este tópico estende o tutorial [Notificar usuários com hubs de notificação]. Você já deve ter concluído as etapas necessárias nesse tutorial para criar o serviço móvel autenticado. Para obter mais informações sobre o cenário notificar usuários, consulte [notificar usuários com hubs de notificação].

## <a name="update-your-app"></a>Atualizar seu aplicativo

1. Em seu MainStoryboard_iPhone. Storyboard, adicione os seguintes componentes da biblioteca de objetos:

   * **Rótulo**: "Enviar por push para o usuário com hubs de notificação"
   * **Rótulo**: InstallationId
   * **Rótulo**: Usuário
   * **Campo de texto**: Usuário
   * **Rótulo**: La
   * **Campo de texto**: La
   * **Botão**: Entrar

     Neste ponto, o storyboard é semelhante ao seguinte:

     ![][0]

2. No editor do assistente, crie saídas para todos os controles alternados e chame-os, conecte os campos de texto com o controlador de exibição (delegado) e crie uma **ação** para o botão de **logon** .

    ![][1]

    O arquivo BreakingNewsViewController. h agora deve conter o código a seguir:

    ```objc
    @property (weak, nonatomic) IBOutlet UILabel *installationId;
    @property (weak, nonatomic) IBOutlet UITextField *User;
    @property (weak, nonatomic) IBOutlet UITextField *Password;

    - (IBAction)login:(id)sender;
    ```
3. Crie uma classe chamada `DeviceInfo`e copie o código a seguir na seção de interface do arquivo DeviceInfo. h:

    ```objc
    @property (readonly, nonatomic) NSString* installationId;
    @property (nonatomic) NSData* deviceToken;
    ```
4. Copie o código a seguir na seção de implementação do arquivo DeviceInfo. m:

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

5. Em PushToUserAppDelegate. h, adicione a propriedade singleton a seguir:

    ```objc
    @property (strong, nonatomic) DeviceInfo* deviceInfo;
    ```
6. `didFinishLaunchingWithOptions` No método em PushToUserAppDelegate. m, adicione o seguinte código:

    ```objc
    self.deviceInfo = [[DeviceInfo alloc] init];

    [[UIApplication sharedApplication] registerForRemoteNotificationTypes: UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound];
    ```

    A primeira linha Inicializa o `DeviceInfo` singleton. A segunda linha inicia o registro para notificações por push, que já está presente se você já tiver concluído o tutorial introdução [Introdução aos hubs de notificação] .
7. Em PushToUserAppDelegate. m, implemente o `didRegisterForRemoteNotificationsWithDeviceToken` método em seu AppDelegate e adicione o seguinte código:

    ```objc
    self.deviceInfo.deviceToken = deviceToken;
    ```

    Isso define o token do dispositivo para a solicitação.

   > [!NOTE]
   > Neste ponto, não deve haver nenhum outro código nesse método. Se você já tiver uma chamada para o `registerNativeWithDeviceToken` método que foi adicionado quando concluiu o tutorial introdução [aos hubs de notificação](notification-hubs-ios-apple-push-notification-apns-get-started.md) , deverá comentar ou remover essa chamada.

8. `PushToUserAppDelegate.m` No arquivo, adicione o seguinte método de manipulador:

    ```objc
    * (void) application:(UIApplication *) application didReceiveRemoteNotification:(NSDictionary *)userInfo {
       NSLog(@"%@", userInfo);
       UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Notification" message:
                             [userInfo objectForKey:@"inAppMessage"] delegate:nil cancelButtonTitle:
                             @"OK" otherButtonTitles:nil, nil];
       [alert show];
    }
    ```

    Esse método exibe um alerta na interface do usuário quando seu aplicativo recebe notificações enquanto está em execução.

9. Abra o `PushToUserViewController.m` arquivo e retorne o teclado na seguinte implementação:

    ```objc
    - (BOOL)textFieldShouldReturn:(UITextField *)theTextField {
        if (theTextField == self.User || theTextField == self.Password) {
            [theTextField resignFirstResponder];
        }
        return YES;
    }
    ```

10. No método no arquivo, inicialize o rótulo da `installationId` seguinte maneira: `PushToUserViewController.m` `viewDidLoad`

    ```objc
    DeviceInfo* deviceInfo = [(PushToUserAppDelegate*)[[UIApplication sharedApplication]delegate] deviceInfo];
    Self.installationId.text = deviceInfo.installationId;
    ```

11. Adicione as seguintes propriedades na interface em `PushToUserViewController.m`:

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

13. Copie o código a seguir no `login` método de manipulador criado pelo Xcode:

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

    Esse método obtém uma ID de instalação e um canal para notificações por push e o envia, juntamente com o tipo de dispositivo, para o método de API Web autenticado que cria um registro nos hubs de notificação. Essa API Web foi definida em [notificar usuários com hubs de notificação].

Agora que o aplicativo cliente foi atualizado, retorne para [Notificar usuários com hubs de notificação] e atualizar o serviço móvel para enviar notificações usando hubs de notificação.

<!-- Anchors. -->

<!-- Images. -->
[0]: ./media/notification-hubs-ios-aspnet-register-user-push-notifications/notification-hub-user-aspnet-ios1.png
[1]: ./media/notification-hubs-ios-aspnet-register-user-push-notifications/notification-hub-user-aspnet-ios2.png

<!-- URLs. -->
[Notificar usuários com hubs de notificação]: notification-hubs-aspnet-backend-ios-apple-apns-notification.md
[Introdução aos hubs de notificação]: notification-hubs-ios-apple-push-notification-apns-get-started.md
