---
title: Push seguros de Hubs de notificação do Azure
description: Saiba como enviar notificações push seguro para aplicações iOS do Azure. Exemplos de código escritos em Objective-C e c#.
documentationcenter: ios
author: jwargo
manager: patniko
editor: spelluru
services: notification-hubs
ms.assetid: 17d42b0a-2c80-4e35-a1ed-ed510d19f4b4
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ios
ms.devlang: objective-c
ms.topic: article
ms.date: 01/04/2019
ms.author: jowargo
ms.openlocfilehash: d88bdb1eaeb95413df84bf69ed4fc763b6d4901f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "61458509"
---
# <a name="azure-notification-hubs-secure-push"></a>Push seguros de Hubs de notificação do Azure

> [!div class="op_single_selector"]
> * [Windows Universal](notification-hubs-aspnet-backend-windows-dotnet-wns-secure-push-notification.md)
> * [iOS](notification-hubs-aspnet-backend-ios-push-apple-apns-secure-notification.md)
> * [Android](notification-hubs-aspnet-backend-android-secure-google-gcm-push-notification.md)

## <a name="overview"></a>Descrição geral

Suporte de notificação push no Microsoft Azure permite-lhe aceder uma infraestrutura de push fáceis de usar, várias plataformas, aumentadas horizontalmente, o que simplifica bastante a implementação de notificações push para aplicações de consumidor e empresariais para dispositivos móveis plataformas.

Devido à regulamentação ou restrições de segurança, por vezes, um aplicativo pode querer incluir algo na notificação que não pode ser transmitida através da infraestrutura de notificação push padrão. Este tutorial descreve como atingir a mesma experiência ao enviar informações confidenciais por meio de uma ligação autenticada e segura entre o dispositivo de cliente e o back-end de aplicação.

Num alto nível, o fluxo é o seguinte:

1. O back-end da aplicação:
   * Payload de arquivos seguro na base de dados de back-end.
   * Envia o ID de notificação para o dispositivo (não existem informações de seguras são enviadas).
2. A aplicação no dispositivo, ao receber a notificação:
   * O dispositivo entra em contacto com o back-end solicitando o payload de seguro.
   * A aplicação pode mostrar o payload como uma notificação no dispositivo.

É importante observar que no fluxo anterior (e neste tutorial), partimos do princípio de que o dispositivo armazena um token de autenticação no armazenamento local, depois do usuário fizer logon. Isso garante uma experiência totalmente integrada, como o dispositivo pode obter o payload de seguros a notificação com este token. Se seu aplicativo não armazena os tokens de autenticação do dispositivo, ou se estes tokens podem ter expirados, a aplicação de dispositivo, ao receber a notificação deverá apresentar uma notificação genérica pedir ao utilizador para iniciar a aplicação. A aplicação, em seguida, autentica o utilizador e mostra o payload de notificação.

Este tutorial de Push seguro mostra como enviar uma notificação push de forma segura. O tutorial baseia-se a [notificar utilizadores](notification-hubs-aspnet-backend-ios-apple-apns-notification.md) tutorial, pelo que deverá concluir os passos esse tutorial primeiro.

> [!NOTE]
> Este tutorial pressupõe que já criou e configurou o notification hub conforme descrito em [introdução aos Hubs de notificação (iOS)](notification-hubs-ios-apple-push-notification-apns-get-started.md).

[!INCLUDE [notification-hubs-aspnet-backend-securepush](../../includes/notification-hubs-aspnet-backend-securepush.md)]

## <a name="modify-the-ios-project"></a>Modificar o projeto iOS

Agora que modificou o seu back-end da aplicação para enviar apenas o *ID* de uma notificação, tem de alterar a sua aplicação iOS para lidar com que a notificação e o back-end para obter a mensagem segura a apresentar do retorno de chamada.

Para atingir esse objetivo, temos de escrever a lógica para obter o conteúdo seguro de back-end de aplicação.

1. No `AppDelegate.m`, certifique-se de que os registros de aplicação para notificações silenciosas, por isso processa o ID de notificação enviado a partir do back-end. Adicionar o `UIRemoteNotificationTypeNewsstandContentAvailability` opção na didFinishLaunchingWithOptions:

    ```objc
    [[UIApplication sharedApplication] registerForRemoteNotificationTypes: UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound | UIRemoteNotificationTypeNewsstandContentAvailability];
    ```
2. No seu `AppDelegate.m` adicionar uma secção de implementação na parte superior com a seguinte declaração:

    ```objc
    @interface AppDelegate ()
    - (void) retrieveSecurePayloadWithId:(int)payloadId completion: (void(^)(NSString*, NSError*)) completion;
    @end
    ```
3. Em seguida, adicione a secção de implementação o código a seguir, substituindo o marcador de posição `{back-end endpoint}` com o ponto final para o back-end que obteve anteriormente:

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

    Esse método chama o back-end de aplicação para obter o conteúdo de notificações com as credenciais armazenadas nas preferências partilhadas.

4. Agora temos de lidar com a notificação de entrada e usar o método acima para obter o conteúdo para apresentar. Em primeiro lugar, temos de ativar a sua aplicação iOS ser executado em segundo plano quando receber uma notificação push. Na **XCode**, selecione seu projeto de aplicação no painel esquerdo, em seguida, clique no destino da sua aplicação principal no **destinos** secção no painel central.
5. Em seguida, clique em sua **capacidades** separador na parte superior do seu painel central e verificar o **notificações remotas** caixa de verificação.

    ![][IOS1]

6. No `AppDelegate.m` adicione o seguinte método para processar as notificações push:

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

    Tenha em atenção que é preferível para tratar de casos de propriedade de cabeçalho de autenticação em falta ou rejeição pelo back-end. O processamento específico nestes casos depende em grande parte de sua experiência de utilizador de destino. É uma opção apresentar uma notificação com uma linha de comandos genérica para o utilizador autenticar para recuperar a notificação real.

## <a name="run-the-application"></a>Executar a aplicação

Para executar a aplicação, faça o seguinte:

1. No XCode, execute a aplicação num dispositivo iOS físico (push notificações não funcionará no simulador).
2. Na aplicação do iOS da interface do Usuário, introduza um nome de utilizador e palavra-passe. Estes podem ser qualquer cadeia de caracteres, mas têm de ser o mesmo valor.
3. Na aplicação do iOS da interface do Usuário, clique em **iniciar sessão**. Em seguida, clique em **enviar push**. Deverá ver a notificação de segura que está a ser apresentada no seu centro de notificações.

[IOS1]: ./media/notification-hubs-aspnet-backend-ios-secure-push/secure-push-ios-1.png
