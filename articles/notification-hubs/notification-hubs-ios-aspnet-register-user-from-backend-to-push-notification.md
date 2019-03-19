---
title: Registar o utilizador atual para as notificações push utilizando a Web API | Documentos da Microsoft
description: Aprenda a pedir o registo de notificação push numa aplicação iOS com Notification Hubs do Azure quando o registo é executado pela ASP.NET Web API.
services: notification-hubs
documentationcenter: ios
author: jwargo
manager: patniko
editor: spelluru
ms.assetid: 4e3772cf-20db-4b9f-bb74-886adfaaa65d
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ios
ms.devlang: objective-c
ms.topic: article
ms.date: 01/04/2019
ms.author: jowargo
ms.openlocfilehash: ff77a955c34941d87a1f653726ab3f19e84aa440
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58101980"
---
# <a name="register-the-current-user-for-push-notifications-by-using-aspnet"></a>Registar o utilizador atual para as notificações push utilizando o ASP.NET

> [!div class="op_single_selector"]
> * [iOS](notification-hubs-ios-aspnet-register-user-from-backend-to-push-notification.md)

## <a name="overview"></a>Descrição geral

Este tópico mostra-lhe como pedir o registo de notificação push com Notification Hubs do Azure quando o registo é executado pela ASP.NET Web API. Este tópico complementa o tutorial [notificar os utilizadores com os Hubs de notificação]. Tem de ter já concluído os passos necessários esse tutorial para criar o serviço móvel autenticado. Para obter mais informações sobre o cenário do notificar os utilizadores, consulte [notificar os utilizadores com os Hubs de notificação].

## <a name="update-your-app"></a>Atualizar a sua aplicação

1. No seu MainStoryboard_iPhone.storyboard, adicione os seguintes componentes da biblioteca de objeto:

   * **Etiqueta**: "Enviar para o utilizador com os Hubs de notificação"
   * **Etiqueta**: "InstallationId"
   * **Etiqueta**: "Utilizador"
   * **Campo de texto**: "Utilizador"
   * **Etiqueta**: "Palavra-passe"
   * **Campo de texto**: "Palavra-passe"
   * **Botão**: "Início de sessão"

     Neste momento, seu guião gráfico é semelhante ao seguinte:

     ![][0]

2. No editor do assistente, criar de comunicação para todos os controles comutadas e chamá-los, ligar os campos de texto com o controlador de vista (delegado) e criar uma **ação** para o **início de sessão** botão.

    ![][1]

    O ficheiro de BreakingNewsViewController.h deve conter, agora, o código a seguir:

    ```objc
    @property (weak, nonatomic) IBOutlet UILabel *installationId;
    @property (weak, nonatomic) IBOutlet UITextField *User;
    @property (weak, nonatomic) IBOutlet UITextField *Password;

    - (IBAction)login:(id)sender;
    ```
3. Criar uma classe chamada `DeviceInfo`e copie o seguinte código para a secção de interface do arquivo DeviceInfo.h:

    ```objc
    @property (readonly, nonatomic) NSString* installationId;
    @property (nonatomic) NSData* deviceToken;
    ```
4. Copie o seguinte código na secção de implementação do arquivo DeviceInfo.m:

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

5. No PushToUserAppDelegate.h, adicione o seguinte singleton de propriedade:

    ```objc
    @property (strong, nonatomic) DeviceInfo* deviceInfo;
    ```
6. Na `didFinishLaunchingWithOptions` método na PushToUserAppDelegate.m, adicione o seguinte código:

    ```objc
    self.deviceInfo = [[DeviceInfo alloc] init];

    [[UIApplication sharedApplication] registerForRemoteNotificationTypes: UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound];
    ```

    A primeira linha inicializa o `DeviceInfo` singleton. A segunda linha inicia o registo para notificações push, que já está presente, se já tiver concluído a [introdução aos Hubs de notificação] tutorial.
7. No PushToUserAppDelegate.m, implementar o método `didRegisterForRemoteNotificationsWithDeviceToken` no seu AppDelegate e adicione o seguinte código:

    ```objc
    self.deviceInfo.deviceToken = deviceToken;
    ```

    Isso define o token do dispositivo para o pedido.

   > [!NOTE]
   > Neste momento, deve não existir qualquer outro código nesse método. Se já tiver uma chamada para o `registerNativeWithDeviceToken` método que foi adicionado quando concluiu a [introdução aos Hubs de notificação](notification-hubs-ios-apple-push-notification-apns-get-started.md) tutorial, tem comente ou remova esta chamada.

8. Na `PushToUserAppDelegate.m` de ficheiros, adicione o seguinte método de manipulador:

    ```objc
    * (void) application:(UIApplication *) application didReceiveRemoteNotification:(NSDictionary *)userInfo {
       NSLog(@"%@", userInfo);
       UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Notification" message:
                             [userInfo objectForKey:@"inAppMessage"] delegate:nil cancelButtonTitle:
                             @"OK" otherButtonTitles:nil, nil];
       [alert show];
    }
    ```

    Esse método apresenta um alerta na interface do Usuário quando a aplicação recebe notificações durante a execução.

9. Abra o `PushToUserViewController.m` de ficheiros e retornar o teclado na seguinte implementação:

    ```objc
    - (BOOL)textFieldShouldReturn:(UITextField *)theTextField {
        if (theTextField == self.User || theTextField == self.Password) {
            [theTextField resignFirstResponder];
        }
        return YES;
    }
    ```

10. Na `viewDidLoad` método na `PushToUserViewController.m` de ficheiros, inicializar o `installationId` etiqueta da seguinte forma:

    ```objc
    DeviceInfo* deviceInfo = [(PushToUserAppDelegate*)[[UIApplication sharedApplication]delegate] deviceInfo];
    Self.installationId.text = deviceInfo.installationId;
    ```

11. Adicione as seguintes propriedades na interface na `PushToUserViewController.m`:

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

13. Copie o seguinte código para o `login` método de manipulador criado pelo XCode:

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

    Este método obtém um ID de instalação e o canal para notificações push e a envia, juntamente com o tipo de dispositivo, para o método de Web API autenticado, que cria um registo nos Hubs de notificação. Esta API da Web foi definido no [notificar os utilizadores com os Hubs de notificação].

Agora que a aplicação de cliente foi atualizada, retornar para o [notificar os utilizadores com os Hubs de notificação] e atualizar o serviço móvel para enviar notificações com os Hubs de notificação.

<!-- Anchors. -->

<!-- Images. -->
[0]: ./media/notification-hubs-ios-aspnet-register-user-push-notifications/notification-hub-user-aspnet-ios1.png
[1]: ./media/notification-hubs-ios-aspnet-register-user-push-notifications/notification-hub-user-aspnet-ios2.png

<!-- URLs. -->
[Notificar os utilizadores com os Hubs de notificação]: notification-hubs-aspnet-backend-ios-apple-apns-notification.md
[Introdução aos Hubs de notificação]: notification-hubs-ios-apple-push-notification-apns-get-started.md
