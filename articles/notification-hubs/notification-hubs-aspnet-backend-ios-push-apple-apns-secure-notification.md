---
title: Hubs de notificação Azure pressionam para iOS
description: Saiba como enviar notificações de push seguras para uma aplicação iOS do Azure. Amostras de código escritas nos Objetivos C e C#.
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
ms.openlocfilehash: 96d1dd514f6fb9c11d7194714337583d6b4387cf
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75530753"
---
# <a name="azure-notification-hubs-secure-push"></a>Push seguro dos hubs de notificação azure

> [!div class="op_single_selector"]
> * [Windows Universal](notification-hubs-aspnet-backend-windows-dotnet-wns-secure-push-notification.md)
> * [iOS](notification-hubs-aspnet-backend-ios-push-apple-apns-secure-notification.md)
> * [Android](notification-hubs-aspnet-backend-android-secure-google-gcm-push-notification.md)

## <a name="overview"></a>Descrição geral

O suporte de notificação push no Microsoft Azure permite-lhe aceder a uma infraestrutura push de fácil utilização, multiplataforma e dimensionada, que simplifica consideravelmente a implementação de notificações push para aplicações de consumidores e empresas para plataformas móveis.

Devido a restrições regulamentares ou de segurança, por vezes, um pedido pode querer incluir algo na notificação que não pode ser transmitido através da infraestrutura padrão de notificação push. Este tutorial descreve como obter a mesma experiência enviando informações sensíveis através de uma ligação segura e autenticada entre o dispositivo cliente e o backend da aplicação.

A um nível elevado, o fluxo é o seguinte:

1. A aplicação back-end:
   * As lojas asseguram a carga útil na base de dados de back-end.
   * Envia a identificação desta notificação para o dispositivo (não são enviadas informações seguras).
2. A aplicação no dispositivo, ao receber a notificação:
   * O dispositivo contacta a parte traseira solicitando a carga útil segura.
   * A aplicação pode mostrar a carga útil como uma notificação no dispositivo.

É importante notar que no fluxo anterior (e neste tutorial), assumimos que o dispositivo armazena um símbolo de autenticação no armazenamento local, após o início do utilizador. Isto garante uma experiência perfeita, uma vez que o dispositivo pode recuperar a carga útil segura da notificação utilizando esta ficha. Se a sua aplicação não armazenar fichas de autenticação no dispositivo, ou se estas fichas puderem ser caducadas, a aplicação do dispositivo, ao receber a notificação, deverá apresentar uma notificação genérica que o utilizador lance a aplicação. A aplicação autentica então o utilizador e mostra a carga útil da notificação.

Este tutorial Secure Push mostra como enviar uma notificação push de forma segura. O tutorial baseia-se no tutorial notificar [utilizadores,](notification-hubs-aspnet-backend-ios-apple-apns-notification.md) pelo que deve completar os passos desse tutorial primeiro.

> [!NOTE]
> Este tutorial assume que criou e configurou o seu centro de notificação conforme descrito em [Getting Started with Notification Hubs (iOS)](notification-hubs-ios-apple-push-notification-apns-get-started.md).

[!INCLUDE [notification-hubs-aspnet-backend-securepush](../../includes/notification-hubs-aspnet-backend-securepush.md)]

## <a name="modify-the-ios-project"></a>Modificar o projeto iOS

Agora que modificou o back-end da sua aplicação para enviar apenas o *ID* de uma notificação, tem de alterar a sua aplicação iOS para lidar com essa notificação e voltar a ligar para a sua parte de trás para recuperar a mensagem segura a exibir.

Para atingir este objetivo, temos de escrever a lógica para recuperar o conteúdo seguro a partir do back-end da app.

1. Em `AppDelegate.m`, certifique-se de que a aplicação se regista para notificações silenciosas para que processe o ID de notificação enviado a partir do backend. Adicione `UIRemoteNotificationTypeNewsstandContentAvailability` a opção em didFinishLaunchingWithOptions:

    ```objc
    [[UIApplication sharedApplication] registerForRemoteNotificationTypes: UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound | UIRemoteNotificationTypeNewsstandContentAvailability];
    ```
2. Na `AppDelegate.m` sua adição, adicione uma secção de implementação no topo com a seguinte declaração:

    ```objc
    @interface AppDelegate ()
    - (void) retrieveSecurePayloadWithId:(int)payloadId completion: (void(^)(NSString*, NSError*)) completion;
    @end
    ```
3. Em seguida, adicione na secção de implementação `{back-end endpoint}` o seguinte código, substituindo o espaço reservado pelo ponto final para o seu back-end obtido anteriormente:

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

    Este método chama a sua aplicação de volta para recuperar o conteúdo de notificação usando as credenciais armazenadas nas preferências partilhadas.

4. Agora temos de lidar com a notificação recebida e usar o método acima para recuperar o conteúdo para exibir. Em primeiro lugar, temos de permitir que a sua aplicação iOS corra em segundo plano ao receber uma notificação push. No **XCode**, selecione o seu projeto de aplicação no painel esquerdo e, em seguida, clique no alvo principal da aplicação na secção **Targets** a partir do painel central.
5. Em seguida, clique no separador **Capabilities** na parte superior do seu painel central e verifique a caixa de verificação de **Notificações Remotas.**

    ![][IOS1]

6. Adicione `AppDelegate.m` o seguinte método para lidar com notificações push:

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

    Note que é preferível lidar com os casos de propriedade de cabeçalho de autenticação em falta ou rejeição até ao final. O tratamento específico destes casos depende principalmente da experiência do utilizador-alvo. Uma opção é apresentar uma notificação com um pedido genérico para que o utilizador autentique para recuperar a notificação real.

## <a name="run-the-application"></a>Executar a Aplicação

Para executar a aplicação, faça o seguinte:

1. No XCode, execute a aplicação num dispositivo físico iOS (as notificações push não funcionarão no simulador).
2. Na aplicação iOS UI, introduza um nome de utilizador e uma senha. Estas podem ser qualquer corda, mas devem ter o mesmo valor.
3. Na aplicação iOS UI, clique **em Iniciar sessão**. Em seguida, clique em **Enviar**. Deve ver a notificação segura a ser apresentada no seu centro de notificação.

[IOS1]: ./media/notification-hubs-aspnet-backend-ios-secure-push/secure-push-ios-1.png
