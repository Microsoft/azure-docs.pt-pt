---
title: Envie notificações push a utilizadores específicos que utilizem os Hubs de Notificação Azure Microsoft Docs
description: Saiba como enviar notificações push para utilizadores específicos do iOS utilizando os Hubs de Notificação Azure.
documentationcenter: ios
author: sethmanheim
manager: femila
services: notification-hubs
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ios
ms.devlang: objective-c
ms.topic: article
ms.date: 08/07/2020
ms.author: sethm
ms.reviewer: thsomasu
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: 167c666c536ee33531fd069dbd1edb530331a9f3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91302194"
---
# <a name="tutorial-send-push-notifications-to-specific-users-using-azure-notification-hubs"></a>Tutorial: Enviar notificações push a utilizadores específicos usando hubs de notificação do Azure

[!INCLUDE [notification-hubs-selector-aspnet-backend-notify-users](../../includes/notification-hubs-selector-aspnet-backend-notify-users.md)]

Este tutorial mostra-lhe como utilizar os Hubs de Notificação do Azure para enviar notificações push para um utilizador específico da aplicação num dispositivo específico. Um backend ASP.NET WebAPI é utilizado para autenticar clientes e gerar notificações, como mostra o tópico de orientação [Registrando a partir do seu backend de aplicações](notification-hubs-push-notification-registration-management.md#registration-management-from-a-backend).

Neste tutorial, siga os seguintes passos:

> [!div class="checklist"]
> * Criar o Projeto WebAPI
> * Autenticar clientes no back-end de WebAPI
> * Utilizar o back-end de WebAPI para registar notificações
> * Enviar notificações a partir do back-end de WebAPI
> * Publicar o back-end de WebAPI novo
> * Modifique a sua aplicação iOS
> * Testar a aplicação

## <a name="prerequisites"></a>Pré-requisitos

Este tutorial pressupõe que criou e configura o seu centro de notificação como descrito no [Enviar notificações push para aplicações iOS usando Azure Notification Hubs](ios-sdk-get-started.md). Este tutorial é também o pré-requisito para o tutorial [Secure Push (iOS).](notification-hubs-aspnet-backend-ios-push-apple-apns-secure-notification.md)
Se pretender utilizar as Aplicações Móveis como serviço de backend, consulte as [aplicações móveis Começar com push](/previous-versions/azure/app-service-mobile/app-service-mobile-ios-get-started-push).

[!INCLUDE [notification-hubs-aspnet-backend-notifyusers](../../includes/notification-hubs-aspnet-backend-notifyusers.md)]

## <a name="modify-your-ios-app"></a>Modifique a sua aplicação iOS

1. Abra a aplicação de visualização de página única criada nas [notificações push para aplicações iOS usando o tutorial de Azure Notification Hubs.](ios-sdk-get-started.md)

   > [!NOTE]
   > Esta secção assume que o seu projeto está configurado com um nome de organização vazio. Caso contrário, prepare o nome da organização para todos os nomes de classe.

2. No `Main.storyboard` ficheiro, adicione os componentes mostrados na imagem da biblioteca do objeto.

    ![Editar storyboard no construtor de interface Xcode][1]

   * **Nome de utilizador**: Um UITextField com texto reservado, *Insira*o nome de utilizador, imediatamente abaixo da etiqueta de resultados de envio e limitado às margens esquerda e direita e abaixo da etiqueta de resultados de envio.
   * **Palavra-passe**: Um UITextField com texto reservado, *Introduza a Palavra-passe,* imediatamente por baixo do campo de texto do nome de utilizador e limitado às margens esquerda e direita e abaixo do campo de texto do nome de utilizador. Consulte a opção **de entrada de texto seguro** no Inspetor de Atributos, na *Tecla Devolução*.
   * **Iniciar sessão :** Um UIButton rotulado imediatamente por baixo do campo de texto de palavra-passe e desmarque a opção **Ativada** no Inspetor atributos, em *Conteúdo de Controlo*
   * **WNS**: Etiquetar e mudar para permitir o envio do Serviço de Notificação do Windows se tiver sido criado no hub. Consulte o tutorial [windows getting start.](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md)
   * **GCM**: Etiquetar e mudar para permitir o envio da notificação para o Google Cloud Messaging se tiver sido configurado no hub. Consulte o tutorial [android Getting Started.](notification-hubs-android-push-notification-google-gcm-get-started.md)
   * **APNS**: Etiquetar e mudar para permitir o envio da notificação para o Serviço de Notificação da Plataforma Apple.
   * **Nome de utilizador destinatário:A** UITextField com texto reservado, *etiqueta de nome de utilizador recipient,* imediatamente abaixo da etiqueta GCM e limitado às margens esquerda e direita e abaixo da etiqueta GCM.

     Alguns componentes foram adicionados no [Enviar notificações push para aplicações iOS usando o tutorial Azure Notification Hubs.](ios-sdk-get-started.md)

3. **Ctrl** arrasta-se dos componentes à vista `ViewController.h` e adiciona estas novas saídas:

    ```objc
    @property (weak, nonatomic) IBOutlet UITextField *UsernameField;
    @property (weak, nonatomic) IBOutlet UITextField *PasswordField;
    @property (weak, nonatomic) IBOutlet UITextField *RecipientField;
    @property (weak, nonatomic) IBOutlet UITextField *NotificationField;

    // Used to enable the buttons on the UI
    @property (weak, nonatomic) IBOutlet UIButton *LogInButton;
    @property (weak, nonatomic) IBOutlet UIButton *SendNotificationButton;

    // Used to enabled sending notifications across platforms
    @property (weak, nonatomic) IBOutlet UISwitch *WNSSwitch;
    @property (weak, nonatomic) IBOutlet UISwitch *GCMSwitch;
    @property (weak, nonatomic) IBOutlet UISwitch *APNSSwitch;

    - (IBAction)LogInAction:(id)sender;
    ```

4. In `ViewController.h` , adicione o seguinte após as suas `#define` declarações de importação. Substitua o `<Your backend endpoint>` espaço reservado com o URL de destino que usou para implementar o seu backend de aplicações na secção anterior. Por `http://your_backend.azurewebsites.net` exemplo:

    ```objc
    #define BACKEND_ENDPOINT @"<Your backend endpoint>"
    ```

5. No seu projeto, crie uma nova classe Cocoa Touch nomeada `RegisterClient` para interagir com o back-end ASP.NET que criou. Crie a classe herdada de `NSObject` . Em seguida, adicione o seguinte código `RegisterClient.h` no:

    ```objc
    @interface RegisterClient : NSObject

    @property (strong, nonatomic) NSString* authenticationHeader;

    -(void) registerWithDeviceToken:(NSData*)token tags:(NSSet*)tags
        andCompletion:(void(^)(NSError*))completion;

    -(instancetype) initWithEndpoint:(NSString*)Endpoint;

    @end
    ```

6. Na `RegisterClient.m` secção , atualizar a `@interface` secção:

    ```objc
    @interface RegisterClient ()

    @property (strong, nonatomic) NSURLSession* session;
    @property (strong, nonatomic) NSURLSession* endpoint;

    -(void) tryToRegisterWithDeviceToken:(NSData*)token tags:(NSSet*)tags retry:(BOOL)retry
                andCompletion:(void(^)(NSError*))completion;
    -(void) retrieveOrRequestRegistrationIdWithDeviceToken:(NSString*)token
                completion:(void(^)(NSString*, NSError*))completion;
    -(void) upsertRegistrationWithRegistrationId:(NSString*)registrationId deviceToken:(NSString*)token
                tags:(NSSet*)tags andCompletion:(void(^)(NSURLResponse*, NSError*))completion;

    @end
    ```

7. Substitua a `@implementation` secção no RegisterClient.m pelo seguinte código:

    ```objc
    @implementation RegisterClient

    // Globals used by RegisterClient
    NSString *const RegistrationIdLocalStorageKey = @"RegistrationId";

    -(instancetype) initWithEndpoint:(NSString*)Endpoint
    {
        self = [super init];
        if (self) {
            NSURLSessionConfiguration* config = [NSURLSessionConfiguration defaultSessionConfiguration];
            _session = [NSURLSession sessionWithConfiguration:config delegate:nil delegateQueue:nil];
            _endpoint = Endpoint;
        }
        return self;
    }

    -(void) registerWithDeviceToken:(NSData*)token tags:(NSSet*)tags
                andCompletion:(void(^)(NSError*))completion
    {
        [self tryToRegisterWithDeviceToken:token tags:tags retry:YES andCompletion:completion];
    }

    -(void) tryToRegisterWithDeviceToken:(NSData*)token tags:(NSSet*)tags retry:(BOOL)retry
                andCompletion:(void(^)(NSError*))completion
    {
        NSSet* tagsSet = tags?tags:[[NSSet alloc] init];

        NSString *deviceTokenString = [[token description]
            stringByTrimmingCharactersInSet:[NSCharacterSet characterSetWithCharactersInString:@"<>"]];
        deviceTokenString = [[deviceTokenString stringByReplacingOccurrencesOfString:@" " withString:@""]
                                uppercaseString];

        [self retrieveOrRequestRegistrationIdWithDeviceToken: deviceTokenString
            completion:^(NSString* registrationId, NSError *error) {
            NSLog(@"regId: %@", registrationId);
            if (error) {
                completion(error);
                return;
            }

            [self upsertRegistrationWithRegistrationId:registrationId deviceToken:deviceTokenString
                tags:tagsSet andCompletion:^(NSURLResponse * response, NSError *error) {
                if (error) {
                    completion(error);
                    return;
                }

                NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*)response;
                if (httpResponse.statusCode == 200) {
                    completion(nil);
                } else if (httpResponse.statusCode == 410 && retry) {
                    [self tryToRegisterWithDeviceToken:token tags:tags retry:NO andCompletion:completion];
                } else {
                    NSLog(@"Registration error with response status: %ld", (long)httpResponse.statusCode);

                    completion([NSError errorWithDomain:@"Registration" code:httpResponse.statusCode
                                userInfo:nil]);
                }

            }];
        }];
    }

    -(void) upsertRegistrationWithRegistrationId:(NSString*)registrationId deviceToken:(NSData*)token
                tags:(NSSet*)tags andCompletion:(void(^)(NSURLResponse*, NSError*))completion
    {
        NSDictionary* deviceRegistration = @{@"Platform" : @"apns", @"Handle": token,
                                                @"Tags": [tags allObjects]};
        NSData* jsonData = [NSJSONSerialization dataWithJSONObject:deviceRegistration
                            options:NSJSONWritingPrettyPrinted error:nil];

        NSLog(@"JSON registration: %@", [[NSString alloc] initWithData:jsonData
                                            encoding:NSUTF8StringEncoding]);

        NSString* endpoint = [NSString stringWithFormat:@"%@/api/register/%@", _endpoint,
                                registrationId];
        NSURL* requestURL = [NSURL URLWithString:endpoint];
        NSMutableURLRequest* request = [NSMutableURLRequest requestWithURL:requestURL];
        [request setHTTPMethod:@"PUT"];
        [request setHTTPBody:jsonData];
        NSString* authorizationHeaderValue = [NSString stringWithFormat:@"Basic %@",
                                                self.authenticationHeader];
        [request setValue:authorizationHeaderValue forHTTPHeaderField:@"Authorization"];
        [request setValue:@"application/json" forHTTPHeaderField:@"Content-Type"];

        NSURLSessionDataTask* dataTask = [self.session dataTaskWithRequest:request
            completionHandler:^(NSData *data, NSURLResponse *response, NSError *error)
        {
            if (!error)
            {
                completion(response, error);
            }
            else
            {
                NSLog(@"Error request: %@", error);
                completion(nil, error);
            }
        }];
        [dataTask resume];
    }

    -(void) retrieveOrRequestRegistrationIdWithDeviceToken:(NSString*)token
                completion:(void(^)(NSString*, NSError*))completion
    {
        NSString* registrationId = [[NSUserDefaults standardUserDefaults]
                                    objectForKey:RegistrationIdLocalStorageKey];

        if (registrationId)
        {
            completion(registrationId, nil);
            return;
        }

        // request new one & save
        NSURL* requestURL = [NSURL URLWithString:[NSString stringWithFormat:@"%@/api/register?handle=%@",
                                _endpoint, token]];
        NSMutableURLRequest* request = [NSMutableURLRequest requestWithURL:requestURL];
        [request setHTTPMethod:@"POST"];
        NSString* authorizationHeaderValue = [NSString stringWithFormat:@"Basic %@",
                                                self.authenticationHeader];
        [request setValue:authorizationHeaderValue forHTTPHeaderField:@"Authorization"];

        NSURLSessionDataTask* dataTask = [self.session dataTaskWithRequest:request
            completionHandler:^(NSData *data, NSURLResponse *response, NSError *error)
        {
            NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*) response;
            if (!error && httpResponse.statusCode == 200)
            {
                NSString* registrationId = [[NSString alloc] initWithData:data
                    encoding:NSUTF8StringEncoding];

                // remove quotes
                registrationId = [registrationId substringWithRange:NSMakeRange(1,
                                    [registrationId length]-2)];

                [[NSUserDefaults standardUserDefaults] setObject:registrationId
                    forKey:RegistrationIdLocalStorageKey];
                [[NSUserDefaults standardUserDefaults] synchronize];

                completion(registrationId, nil);
            }
            else
            {
                NSLog(@"Error status: %ld, request: %@", (long)httpResponse.statusCode, error);
                if (error)
                    completion(nil, error);
                else {
                    completion(nil, [NSError errorWithDomain:@"Registration" code:httpResponse.statusCode
                                userInfo:nil]);
                }
            }
        }];
        [dataTask resume];
    }

    @end
    ```

    Este código implementa a lógica explicada no artigo de orientação [Registando-se a partir do seu backend da app](notification-hubs-push-notification-registration-management.md#registration-management-from-a-backend) utilizando a NSURLSession para executar chamadas REST para o seu backend de aplicações, e NSUserDefaults para armazenar localmente o registroD devolvido pelo centro de notificação.

    Esta classe requer que a sua propriedade `authorizationHeader` seja definida para funcionar corretamente. Esta propriedade é definida pela `ViewController` classe após o início de sessão.

8. In `ViewController.h` , adicionar uma declaração para `#import` `RegisterClient.h` . Em seguida, adicione uma declaração para o token do dispositivo e referência a um `RegisterClient` caso na `@interface` secção:

    ```objc
    #import "RegisterClient.h"

    @property (strong, nonatomic) NSData* deviceToken;
    @property (strong, nonatomic) RegisterClient* registerClient;
    ```

9. Em ViewController.m, adicione uma declaração de método privado na `@interface` secção:

    ```objc
    @interface ViewController () <UITextFieldDelegate, NSURLConnectionDataDelegate, NSXMLParserDelegate>

    // create the Authorization header to perform Basic authentication with your app back-end
    -(void) createAndSetAuthenticationHeaderWithUsername:(NSString*)username
                    AndPassword:(NSString*)password;

    @end
    ```

    > [!NOTE]
    > O seguinte corte não é um esquema de autenticação segura, deve substituir a implementação do com o seu mecanismo específico de    `createAndSetAuthenticationHeaderWithUsername:AndPassword:` autenticação que gere um token de autenticação a ser consumido pela classe cliente de registo, por exemplo, OAuth, Ative Directy.

10. Em seguida, na `@implementation` secção de , adicione o seguinte `ViewController.m` código, que adiciona a implementação para definir o token do dispositivo e o cabeçalho de autenticação.

    ```objc
    -(void) setDeviceToken: (NSData*) deviceToken
    {
        _deviceToken = deviceToken;
        self.LogInButton.enabled = YES;
    }

    -(void) createAndSetAuthenticationHeaderWithUsername:(NSString*)username
                    AndPassword:(NSString*)password;
    {
        NSString* headerValue = [NSString stringWithFormat:@"%@:%@", username, password];

        NSData* encodedData = [[headerValue dataUsingEncoding:NSUTF8StringEncoding] base64EncodedDataWithOptions:NSDataBase64EncodingEndLineWithCarriageReturn];

        self.registerClient.authenticationHeader = [[NSString alloc] initWithData:encodedData
                                                    encoding:NSUTF8StringEncoding];
    }

    -(BOOL)textFieldShouldReturn:(UITextField *)textField
    {
        [textField resignFirstResponder];
        return YES;
    }
    ```

    Note como a definição do token do dispositivo permite o botão **Iniciar sessão.** É porque, como parte da ação de login, o controlador de visualização regista-se para notificações push com o backend da app. Não pretende que o **Log em** ação esteja acessível até que o token do dispositivo tenha sido corretamente configurado. Pode dissociar o login do registo push desde que o primeiro aconteça antes deste último.

11. Em ViewController.m, utilize os seguintes cortes para implementar o método de ação para o seu botão **Iniciar sessão** e um método para enviar a mensagem de notificação utilizando o backend ASP.NET.

    ```objc
    - (IBAction)LogInAction:(id)sender {
        // create authentication header and set it in register client
        NSString* username = self.UsernameField.text;
        NSString* password = self.PasswordField.text;

        [self createAndSetAuthenticationHeaderWithUsername:username AndPassword:password];

        __weak ViewController* selfie = self;
        [self.registerClient registerWithDeviceToken:self.deviceToken tags:nil
            andCompletion:^(NSError* error) {
            if (!error) {
                dispatch_async(dispatch_get_main_queue(),
                ^{
                    selfie.SendNotificationButton.enabled = YES;
                    [self MessageBox:@"Success" message:@"Registered successfully!"];
                });
            }
        }];
    }

    - (void)SendNotificationASPNETBackend:(NSString*)pns UsernameTag:(NSString*)usernameTag
                Message:(NSString*)message
    {
        NSURLSession* session = [NSURLSession
            sessionWithConfiguration:[NSURLSessionConfiguration defaultSessionConfiguration] delegate:nil
            delegateQueue:nil];

        // Pass the pns and username tag as parameters with the REST URL to the ASP.NET backend
        NSURL* requestURL = [NSURL URLWithString:[NSString
            stringWithFormat:@"%@/api/notifications?pns=%@&to_tag=%@", BACKEND_ENDPOINT, pns,
            usernameTag]];

        NSMutableURLRequest* request = [NSMutableURLRequest requestWithURL:requestURL];
        [request setHTTPMethod:@"POST"];

        // Get the mock authenticationheader from the register client
        NSString* authorizationHeaderValue = [NSString stringWithFormat:@"Basic %@",
            self.registerClient.authenticationHeader];
        [request setValue:authorizationHeaderValue forHTTPHeaderField:@"Authorization"];

        //Add the notification message body
        [request setValue:@"application/json;charset=utf-8" forHTTPHeaderField:@"Content-Type"];
        [request setHTTPBody:[message dataUsingEncoding:NSUTF8StringEncoding]];

        // Execute the send notification REST API on the ASP.NET Backend
        NSURLSessionDataTask* dataTask = [session dataTaskWithRequest:request
            completionHandler:^(NSData *data, NSURLResponse *response, NSError *error)
        {
            NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*) response;
            if (error || httpResponse.statusCode != 200)
            {
                NSString* status = [NSString stringWithFormat:@"Error Status for %@: %d\nError: %@\n",
                                    pns, httpResponse.statusCode, error];
                dispatch_async(dispatch_get_main_queue(),
                ^{
                    // Append text because all 3 PNS calls may also have information to view
                    [self.sendResults setText:[self.sendResults.text stringByAppendingString:status]];
                });
                NSLog(status);
            }

            if (data != NULL)
            {
                xmlParser = [[NSXMLParser alloc] initWithData:data];
                [xmlParser setDelegate:self];
                [xmlParser parse];
            }
        }];
        [dataTask resume];
    }
    ```

12. Atualize a ação para o botão **Enviar Notificação** para utilizar o backend ASP.NET e enviar para qualquer PNS ativado por um interruptor.

    ```objc
    - (IBAction)SendNotificationMessage:(id)sender
    {
        //[self SendNotificationRESTAPI];
        [self SendToEnabledPlatforms];
    }

    -(void)SendToEnabledPlatforms
    {
        NSString* json = [NSString stringWithFormat:@"\"%@\"",self.notificationMessage.text];

        [self.sendResults setText:@""];

        if ([self.WNSSwitch isOn])
            [self SendNotificationASPNETBackend:@"wns" UsernameTag:self.RecipientField.text Message:json];

        if ([self.GCMSwitch isOn])
            [self SendNotificationASPNETBackend:@"gcm" UsernameTag:self.RecipientField.text Message:json];

        if ([self.APNSSwitch isOn])
            [self SendNotificationASPNETBackend:@"apns" UsernameTag:self.RecipientField.text Message:json];
    }
    ```

13. Na `ViewDidLoad` função, adicione o seguinte para instantanear o `RegisterClient` caso e definir o delegado para os seus campos de texto.

    ```objc
    self.UsernameField.delegate = self;
    self.PasswordField.delegate = self;
    self.RecipientField.delegate = self;
    self.registerClient = [[RegisterClient alloc] initWithEndpoint:BACKEND_ENDPOINT];
    ```

14. Agora em `AppDelegate.m` , remover todo o conteúdo do método e `application:didRegisterForPushNotificationWithDeviceToken:` substituí-lo pelo seguinte (para garantir que o controlador de visualização contém o mais recente símbolo do dispositivo recuperado de APNs):

    ```objc
    // Add import to the top of the file
    #import "ViewController.h"

    - (void)application:(UIApplication *)application
                didRegisterForRemoteNotificationsWithDeviceToken:(NSData *)deviceToken
    {
        ViewController* rvc = (ViewController*) self.window.rootViewController;
        rvc.deviceToken = deviceToken;
    }
    ```

15. Finalmente, `AppDelegate.m` certifique-se de ter o seguinte método:

    ```objc
    - (void)application:(UIApplication *)application didReceiveRemoteNotification: (NSDictionary *)userInfo {
        NSLog(@"%@", userInfo);
        [self MessageBox:@"Notification" message:[[userInfo objectForKey:@"aps"] valueForKey:@"alert"]];
    }
    ```

## <a name="test-the-application"></a>Testar a aplicação

1. No XCode, execute a aplicação num dispositivo físico iOS (as notificações push não funcionam no simulador).
2. Na aplicação iOS UI, introduza o mesmo valor tanto para o nome de utilizador como para a palavra-passe. Em seguida, clique **em Iniciar sessão**.

    ![Aplicação de teste iOS][2]

3. Devia ver um pop-up informando-o do sucesso do registo. Clique em **OK**.

    ![Notificação de teste iOS apresentada][3]

4. No campo de texto*de identificação de utilizador * Destinatário,* introduza a etiqueta de nome de utilizador utilizada com o registo de outro dispositivo.
5. Introduza uma mensagem de notificação e clique em **Enviar Notificação**. Apenas os dispositivos que têm registo com a etiqueta do nome do utilizador destinatário recebem a mensagem de notificação. Só é enviado para esses utilizadores.

    ![Notificação marcada por teste do iOS][4]

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, aprendeu a enviar notificações push para utilizadores específicos que têm etiquetas associadas aos respetivos registos. Para saber como enviar notificações push com base na localização, avance para o seguinte tutorial:

> [!div class="nextstepaction"]
>[Enviar notificações push baseadas na localização](notification-hubs-push-bing-spatial-data-geofencing-notification.md)

[1]: ./media/notification-hubs-aspnet-backend-ios-notify-users/notification-hubs-ios-notify-users-interface.png
[2]: ./media/notification-hubs-aspnet-backend-ios-notify-users/notification-hubs-ios-notify-users-enter-user-pwd.png
[3]: ./media/notification-hubs-aspnet-backend-ios-notify-users/notification-hubs-ios-notify-users-registered.png
[4]: ./media/notification-hubs-aspnet-backend-ios-notify-users/notification-hubs-ios-notify-users-enter-msg.png
