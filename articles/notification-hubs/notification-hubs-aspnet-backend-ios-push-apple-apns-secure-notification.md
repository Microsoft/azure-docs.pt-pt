---
title: Azure Notification Hubs Secure Push para iOS
description: Saiba como enviar notificações push seguras para uma aplicação iOS a partir do Azure. Amostras de código escritas em Objective-C e C#.
documentationcenter: ios
author: sethmanheim
manager: femila
services: notification-hubs
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ios
ms.devlang: objective-c
ms.topic: article
ms.date: 08/17/2020
ms.author: sethm
ms.reviewer: thsomasu
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: a6c85ba017656bd312ddfe3d5f6d98014a3dc89a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "90090351"
---
# <a name="azure-notification-hubs-secure-push"></a>Hubs de notificação Azure Secure Push

> [!div class="op_single_selector"]
> * [Windows Universal](notification-hubs-aspnet-backend-windows-dotnet-wns-secure-push-notification.md)
> * [iOS](notification-hubs-aspnet-backend-ios-push-apple-apns-secure-notification.md)
> * [Android](notification-hubs-aspnet-backend-android-secure-google-gcm-push-notification.md)

## <a name="overview"></a>Descrição Geral

O suporte de notificação push no Microsoft Azure permite-lhe aceder a uma infraestrutura push de fácil utilização, multiplataforma e dimensionada, o que simplifica consideravelmente a implementação de notificações push para aplicações de consumidores e empresas para plataformas móveis.

Devido a restrições regulamentares ou de segurança, por vezes, um pedido pode querer incluir algo na notificação que não pode ser transmitido através da infraestrutura padrão de notificação push. Este tutorial descreve como obter a mesma experiência enviando informações sensíveis através de uma ligação segura e autenticada entre o dispositivo cliente e o backend da app.

A um nível elevado, o fluxo é o seguinte:

1. A aplicação back-end:
   * As lojas protegem a carga útil na base de dados back-end.
   * Envia o ID desta notificação para o dispositivo (não é enviada nenhuma informação segura).
2. A aplicação no dispositivo, ao receber a notificação:
   * O dispositivo contacta a parte de trás solicitando a carga útil segura.
   * A aplicação pode mostrar a carga útil como uma notificação no dispositivo.

É importante notar que no fluxo anterior (e neste tutorial), assumimos que o dispositivo armazena um token de autenticação no armazenamento local, após o registo do utilizador. Isto garante uma experiência perfeita, uma vez que o dispositivo pode recuperar a carga útil segura da notificação utilizando este token. Se a sua aplicação não armazenar fichas de autenticação no dispositivo, ou se estas fichas puderem ser expiradas, a aplicação do dispositivo, ao receber a notificação, deverá apresentar uma notificação genérica que o levou o utilizador a lançar a aplicação. A aplicação autentica então o utilizador e mostra a carga útil da notificação.

Este tutorial Secure Push mostra como enviar uma notificação push de forma segura. O tutorial baseia-se no tutorial de [Notificação dos Utilizadores,](notification-hubs-aspnet-backend-ios-apple-apns-notification.md) pelo que deve completar primeiro os passos nesse tutorial.

> [!NOTE]
> Este tutorial pressupõe que criou e configura o seu centro de notificação como descrito no [Enviar notificações push para aplicações iOS usando Azure Notification Hubs](ios-sdk-get-started.md).

[!INCLUDE [notification-hubs-aspnet-backend-securepush](../../includes/notification-hubs-aspnet-backend-securepush.md)]

## <a name="modify-the-ios-project"></a>Modifique o projeto iOS

Agora que modificou a sua aplicação para enviar apenas o *ID* de uma notificação, tem de alterar a sua aplicação iOS para lidar com essa notificação e voltar a ligar para recuperar a mensagem segura a ser exibida.

Para atingir este objetivo, temos de escrever a lógica para recuperar o conteúdo seguro a partir do back-end da app.

1. Em `AppDelegate.m` , certifique-se de que a aplicação regista para notificações silenciosas para que processe o ID de notificação enviado a partir do backend. Adicione a `UIRemoteNotificationTypeNewsstandContentAvailability` opção em didFinishLaunchingWithOptions:

    ```objc
    [[UIApplication sharedApplication] registerForRemoteNotificationTypes: UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound | UIRemoteNotificationTypeNewsstandContentAvailability];
    ```
2. Na sua `AppDelegate.m` secção de adicionar uma secção de implementação no topo com a seguinte declaração:

    ```objc
    @interface AppDelegate ()
    - (void) retrieveSecurePayloadWithId:(int)payloadId completion: (void(^)(NSString*, NSError*)) completion;
    @end
    ```
3. Em seguida, adicione na secção de implementação o seguinte código, substituindo o espaço reservado `{back-end endpoint}` pelo ponto final para o seu back-end obtido anteriormente:

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

    Este método chama a sua app de back-end para recuperar o conteúdo da notificação usando as credenciais armazenadas nas preferências partilhadas.

4. Agora, manuseie a notificação de entrada e utilize o método acima para recuperar o conteúdo para visualizar. Em primeiro lugar, permita que a sua aplicação iOS seja executada em segundo plano ao receber uma notificação push. No **XCode,** selecione o seu projeto de aplicação no painel esquerdo e, em seguida, clique no seu alvo principal de aplicação na secção **Alvos** a partir do painel central.
5. Em seguida, clique no separador **Capabilites** na parte superior do painel central e verifique a caixa **de Notificações Remotas.**

    ![Screenshot do XCode, com o projeto da aplicação selecionado e o separador Capabilities aberto. A caixa de verificação de notificações remotas é selecionada.][IOS1]

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

    Note que é preferível lidar com os casos de propriedade ou rejeição do cabeçalho de autenticação em falta ou rejeição pelo back-end. O tratamento específico destes casos depende principalmente da experiência do utilizador-alvo. Uma opção é apresentar uma notificação com uma solicitação genérica para que o utilizador autentica para recuperar a notificação real.

## <a name="run-the-application"></a>Executar a Aplicação

Para executar a aplicação, faça o seguinte:

1. No XCode, execute a aplicação num dispositivo físico iOS (as notificações push não funcionarão no simulador).
2. Na aplicação iOS UI, insira um nome de utilizador e senha. Estes podem ser qualquer corda, mas devem ter o mesmo valor.
3. Na aplicação iOS UI, clique **em Iniciarões de Sessão**. Em seguida, clique **em Enviar o empurrão**. Deverá ver a notificação segura a ser exibida no seu centro de notificação.

[IOS1]: ./media/notification-hubs-aspnet-backend-ios-secure-push/secure-push-ios-1.png
