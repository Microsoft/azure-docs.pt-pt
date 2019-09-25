---
title: Push seguro dos hubs de notificação do Azure
description: Saiba como enviar notificações por push seguras para um aplicativo iOS do Azure. Exemplos de código escritos em Objective- C#C e.
documentationcenter: ios
author: sethmanheim
manager: femila
editor: jwargo
services: notification-hubs
ms.assetid: 17d42b0a-2c80-4e35-a1ed-ed510d19f4b4
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ios
ms.devlang: objective-c
ms.topic: article
ms.date: 01/04/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: 4a175b14d44ef7ba019c28fbd03bac98ada7a2a3
ms.sourcegitcommit: 7df70220062f1f09738f113f860fad7ab5736e88
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/24/2019
ms.locfileid: "71212144"
---
# <a name="azure-notification-hubs-secure-push"></a>Push seguro dos hubs de notificação do Azure

> [!div class="op_single_selector"]
> * [Windows Universal](notification-hubs-aspnet-backend-windows-dotnet-wns-secure-push-notification.md)
> * [iOS](notification-hubs-aspnet-backend-ios-push-apple-apns-secure-notification.md)
> * [Android](notification-hubs-aspnet-backend-android-secure-google-gcm-push-notification.md)

## <a name="overview"></a>Descrição geral

O suporte à notificação por push no Microsoft Azure permite que você acesse uma infraestrutura de envio fácil de usar, multiplataforma e escalável, que simplifica bastante a implementação de notificações por push para aplicativos de consumidor e empresariais para dispositivos móveis compatíveis.

Devido a restrições normativas ou de segurança, às vezes um aplicativo pode querer incluir algo na notificação que não pode ser transmitido por meio da infraestrutura de notificação por push padrão. Este tutorial descreve como obter a mesma experiência ao enviar informações confidenciais por meio de uma conexão segura e autenticada entre o dispositivo cliente e o back-end do aplicativo.

Em um alto nível, o fluxo é o seguinte:

1. O back-end do aplicativo:
   * Armazena a carga segura no banco de dados back-end.
   * Envia a ID dessa notificação para o dispositivo (nenhuma informação segura é enviada).
2. O aplicativo no dispositivo, ao receber a notificação:
   * O dispositivo entra em contato com o back-end solicitando a carga segura.
   * O aplicativo pode mostrar a carga como uma notificação no dispositivo.

É importante observar que, no fluxo anterior (e neste tutorial), presumimos que o dispositivo armazene um token de autenticação no armazenamento local, depois que o usuário fizer logon. Isso garante uma experiência simples, pois o dispositivo pode recuperar a carga segura da notificação usando esse token. Se o seu aplicativo não armazenar tokens de autenticação no dispositivo ou se esses tokens puderem expirar, o aplicativo do dispositivo, após receber a notificação, deverá exibir uma notificação genérica solicitando que o usuário inicie o aplicativo. Em seguida, o aplicativo autentica o usuário e mostra a carga de notificação.

Este tutorial de push seguro mostra como enviar uma notificação por push com segurança. O tutorial se baseia no tutorial [notificar usuários](notification-hubs-aspnet-backend-ios-apple-apns-notification.md) , portanto, você deve concluir as etapas nesse tutorial primeiro.

> [!NOTE]
> Este tutorial pressupõe que você criou e configurou seu hub de notificação conforme descrito em [introdução com os hubs de notificação (Ios)](notification-hubs-ios-apple-push-notification-apns-get-started.md).

[!INCLUDE [notification-hubs-aspnet-backend-securepush](../../includes/notification-hubs-aspnet-backend-securepush.md)]

## <a name="modify-the-ios-project"></a>Modificar o projeto do iOS

Agora que você modificou o back-end do aplicativo para enviar apenas a *ID* de uma notificação, você precisa alterar seu aplicativo IOS para lidar com essa notificação e retornar o back-end para recuperar a mensagem segura a ser exibida.

Para atingir esse objetivo, precisamos escrever a lógica para recuperar o conteúdo seguro do back-end do aplicativo.

1. No `AppDelegate.m`, verifique se o aplicativo é registrado para notificações silenciosas para que ele processe a ID de notificação enviada do back-end. Adicione a `UIRemoteNotificationTypeNewsstandContentAvailability` opção em didFinishLaunchingWithOptions:

    ```objc
    [[UIApplication sharedApplication] registerForRemoteNotificationTypes: UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound | UIRemoteNotificationTypeNewsstandContentAvailability];
    ```
2. Na seção `AppDelegate.m` adicionar uma implementação na parte superior, com a seguinte declaração:

    ```objc
    @interface AppDelegate ()
    - (void) retrieveSecurePayloadWithId:(int)payloadId completion: (void(^)(NSString*, NSError*)) completion;
    @end
    ```
3. Em seguida, adicione na seção de implementação o código a seguir, substituindo o espaço reservado `{back-end endpoint}` pelo ponto de extremidade do seu back-end obtido anteriormente:

    ```objc
    NSString *const GetNotificationEndpoint = @"{back-end endpoint}/api/notifications";

    - (void) retrieveSecurePayloadWithId:(int)payloadId completion: (void(^)(NSString*, NSError*)) completion;
    {
        // check if authenticated
        ANHViewController* rvc = (ANHViewController*) self.window.rootViewController;
        NSString* authenticationHeader = rvc.registerClient.authenticationHeader;
        if (!authenticationHeader) return;

        NSURLSession* session = [NSURLSession
                                    sessionWithConfiguration:[NSURLSessionConfiguration defaultSessionConfiguration]
                                    delegate:nil
                                    delegateQueue:nil];

        NSURL* requestURL = [NSURL URLWithString:[NSString stringWithFormat:@"%@/%d", GetNotificationEndpoint, payloadId]];
        NSMutableURLRequest* request = [NSMutableURLRequest requestWithURL:requestURL];
        [request setHTTPMethod:@"GET"];
        NSString* authorizationHeaderValue = [NSString stringWithFormat:@"Basic %@", authenticationHeader];
        [request setValue:authorizationHeaderValue forHTTPHeaderField:@"Authorization"];

        NSURLSessionDataTask* dataTask = [session dataTaskWithRequest:request completionHandler:^(NSData *data, NSURLResponse *response, NSError *error) {
            NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*) response;
            if (!error && httpResponse.statusCode == 200)
            {
                NSLog(@"Received secure payload: %@", [[NSString alloc] initWithData:data encoding:NSUTF8StringEncoding]);

                NSMutableDictionary *json = [NSJSONSerialization JSONObjectWithData:data options: NSJSONReadingMutableContainers error: &error];

                completion([json objectForKey:@"Payload"], nil);
            }
            else
            {
                NSLog(@"Error status: %ld, request: %@", (long)httpResponse.statusCode, error);
                if (error)
                    completion(nil, error);
                else {
                    completion(nil, [NSError errorWithDomain:@"APICall" code:httpResponse.statusCode userInfo:nil]);
                }
            }
        }];
        [dataTask resume];
    }
    ```

    Esse método chama o back-end do aplicativo para recuperar o conteúdo de notificação usando as credenciais armazenadas nas preferências compartilhadas.

4. Agora temos que lidar com a notificação de entrada e usar o método acima para recuperar o conteúdo a ser exibido. Primeiro, precisamos habilitar seu aplicativo iOS para ser executado em segundo plano ao receber uma notificação por push. No **Xcode**, selecione seu projeto de aplicativo no painel esquerdo e, em seguida, clique no destino do aplicativo principal na seção **destinos** do painel central.
5. Em seguida, clique na guia **recursos** na parte superior do painel central e marque a caixa de seleção **notificações remotas** .

    ![][IOS1]

6. Em `AppDelegate.m` adicionar o seguinte método para lidar com notificações por push:

    ```objc
    -(void)application:(UIApplication *)application didReceiveRemoteNotification:(NSDictionary *)userInfo fetchCompletionHandler:(void (^)(UIBackgroundFetchResult))completionHandler
    {
        NSLog(@"%@", userInfo);

        [self retrieveSecurePayloadWithId:[[userInfo objectForKey:@"secureId"] intValue] completion:^(NSString * payload, NSError *error) {
            if (!error) {
                // show local notification
                UILocalNotification* localNotification = [[UILocalNotification alloc] init];
                localNotification.fireDate = [NSDate dateWithTimeIntervalSinceNow:0];
                localNotification.alertBody = payload;
                localNotification.timeZone = [NSTimeZone defaultTimeZone];
                [[UIApplication sharedApplication] scheduleLocalNotification:localNotification];

                completionHandler(UIBackgroundFetchResultNewData);
            } else {
                completionHandler(UIBackgroundFetchResultFailed);
            }
        }];

    }
    ```

    Observe que é preferível manipular os casos de falta de propriedade de cabeçalho de autenticação ou rejeição pelo back-end. A manipulação específica desses casos depende principalmente da experiência do usuário de destino. Uma opção é exibir uma notificação com um prompt genérico para que o usuário se autentique para recuperar a notificação real.

## <a name="run-the-application"></a>Executar o aplicativo

Para executar o aplicativo, faça o seguinte:

1. No XCode, execute o aplicativo em um dispositivo iOS físico (as notificações por push não funcionarão no simulador).
2. Na interface do usuário do aplicativo iOS, insira um nome de usuário e senha. Elas podem ser qualquer cadeia de caracteres, mas devem ter o mesmo valor.
3. Na interface do usuário do aplicativo iOS, clique em **fazer logon**. Em seguida, clique em **enviar envio por push**. Você deve ver a notificação segura sendo exibida no centro de notificações.

[IOS1]: ./media/notification-hubs-aspnet-backend-ios-secure-push/secure-push-ios-1.png
