---
title: Enviar notificações por push para usuários específicos usando hubs de notificação do Azure | Microsoft Docs
description: Saiba como enviar notificações push para utilizadores específicos com os Hubs de Notificação do Azure.
documentationcenter: ios
author: sethm
manager: femila
editor: jwargo
services: notification-hubs
ms.assetid: 1f7d1410-ef93-4c4b-813b-f075eed20082
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ios
ms.devlang: objective-c
ms.topic: article
ms.date: 01/04/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: 48135ea614bbab4ca6649a83895ae5f632918c61
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/16/2019
ms.locfileid: "72387465"
---
# <a name="tutorial-send-push-notifications-to-specific-users-using-azure-notification-hubs"></a>Tutorial: enviar notificações por push para usuários específicos usando os hubs de notificação do Azure

[!INCLUDE [notification-hubs-selector-aspnet-backend-notify-users](../../includes/notification-hubs-selector-aspnet-backend-notify-users.md)]

Este tutorial mostra-lhe como pode utilizar os Hubs de Notificação do Azure para enviar notificações push para um utilizador da aplicação específico num dispositivo específico. Um back-end ASP.NET WebAPI é usado para autenticar clientes e gerar notificações, conforme mostrado no tópico de diretrizes [registrando-se no back-end do aplicativo](notification-hubs-push-notification-registration-management.md#registration-management-from-a-backend).

Neste tutorial, siga os seguintes passos:

> [!div class="checklist"]
> * Criar o Projeto WebAPI
> * Autenticar clientes no back-end de WebAPI
> * Utilizar o back-end de WebAPI para registar notificações
> * Enviar notificações a partir do back-end de WebAPI
> * Publicar o back-end de WebAPI novo
> * Modificar seu aplicativo iOS
> * Testar a aplicação

## <a name="prerequisites"></a>Pré-requisitos

Este tutorial pressupõe que você criou e configurou seu hub de notificação conforme descrito em [introdução com os hubs de notificação (Ios)](notification-hubs-ios-apple-push-notification-apns-get-started.md). Este tutorial também é o pré-requisito para o tutorial de [Push seguro (Ios)](notification-hubs-aspnet-backend-ios-push-apple-apns-secure-notification.md) .
Se você quiser usar aplicativos móveis como seu serviço de back-end, consulte os [aplicativos móveis introdução ao Push](../app-service-mobile/app-service-mobile-ios-get-started-push.md).

[!INCLUDE [notification-hubs-aspnet-backend-notifyusers](../../includes/notification-hubs-aspnet-backend-notifyusers.md)]

## <a name="modify-your-ios-app"></a>Modificar seu aplicativo iOS

1. Abra o aplicativo de exibição de página única criado no tutorial [introdução com hubs de notificação (Ios)](notification-hubs-ios-apple-push-notification-apns-get-started.md) .

   > [!NOTE]
   > Esta seção pressupõe que seu projeto esteja configurado com um nome de organização vazio. Caso contrário, você precisará preceder o nome da sua organização a todos os nomes de classe.

2. No arquivo `Main.storyboard`, adicione os componentes mostrados na captura de tela da biblioteca de objetos.

    ![Editar storyboard no Xcode Interface Builder][1]

   * **Nome de usuário**: um UITextField com texto de espaço reservado, *Insira o nome de usuário*, imediatamente abaixo do rótulo enviar resultados e restringido às margens esquerda e direita e abaixo do rótulo enviar resultados.
   * **Senha**: um UITextField com texto de espaço reservado, *Insira a senha*, imediatamente abaixo do campo de texto nome de usuário e restrito às margens esquerda e direita e abaixo do campo de texto nome de usuário. Marque a opção de **entrada de texto seguro** no Inspetor de atributo, em *chave de retorno*.
   * **Fazer logon**: um UIButton rotulado imediatamente abaixo do campo de texto de senha e desmarque a opção **habilitado** no Inspetor de atributos, em *controle de conteúdo*
   * **WNS**: rotule e alterne para habilitar o envio do serviço de notificação do Windows de notificação se ele tiver sido configurado no Hub. Consulte o tutorial de [introdução do Windows](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md) .
   * **GCM**: rotular e alternar para habilitar o envio da notificação para Google Cloud Messaging se ela tiver sido configurada no Hub. Consulte o tutorial de [introdução do Android](notification-hubs-android-push-notification-google-gcm-get-started.md) .
   * **APNS**: Label e switch para habilitar o envio da notificação para o serviço de notificação da plataforma Apple.
   * **Nome de usuário do destinatário: um** UITextField com texto de espaço reservado, *marca de nome de usuário do destinatário*, imediatamente abaixo do rótulo do gcm e restrito às margens esquerda e direita e abaixo do rótulo gcm.

     Alguns componentes foram adicionados no tutorial [introdução com os hubs de notificação (Ios)](notification-hubs-ios-apple-push-notification-apns-get-started.md) .

3. **Ctrl** arraste dos componentes na exibição para `ViewController.h` e adicione essas novas saídas.

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

4. No `ViewController.h`, adicione o seguinte `#define` após as instruções de importação. Substitua o espaço reservado `<Enter Your Backend Endpoint>` pela URL de destino usada para implantar o back-end do aplicativo na seção anterior. Por exemplo, `http://your_backend.azurewebsites.net`.

    ```objc
    #define BACKEND_ENDPOINT @"<Enter Your Backend Endpoint>"
    ```

5. Em seu projeto, crie uma nova classe Cocoa Touch chamada `RegisterClient` para interface com o back-end ASP.NET que você criou. Crie a classe que herda de `NSObject`. Em seguida, adicione o código a seguir no `RegisterClient.h`.

    ```objc
    @interface RegisterClient : NSObject

    @property (strong, nonatomic) NSString* authenticationHeader;

    -(void) registerWithDeviceToken:(NSData*)token tags:(NSSet*)tags
        andCompletion:(void(^)(NSError*))completion;

    -(instancetype) initWithEndpoint:(NSString*)Endpoint;

    @end
    ```

6. No `RegisterClient.m`, atualize a seção `@interface`:

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

7. Substitua a seção `@implementation` no RegisterClient. m pelo seguinte código:

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

    Esse código implementa a lógica explicada no artigo de diretrizes [registrando de seu back-end do aplicativo](notification-hubs-push-notification-registration-management.md#registration-management-from-a-backend) usando NSURLSession para executar chamadas REST para o back-end do aplicativo e NSUserDefaults para armazenar localmente o RegistrationId retornado pelo hub de notificação.

    Essa classe requer que sua propriedade `authorizationHeader` seja definida para funcionar corretamente. Essa propriedade é definida pela classe `ViewController` após o logon.

8. Em `ViewController.h`, adicione uma instrução `#import` para `RegisterClient.h`. Em seguida, adicione uma declaração para o token do dispositivo e faça referência a uma instância `RegisterClient` na seção `@interface`:

    ```objc
    #import "RegisterClient.h"

    @property (strong, nonatomic) NSData* deviceToken;
    @property (strong, nonatomic) RegisterClient* registerClient;
    ```

9. Em ViewController. m, adicione uma declaração de método particular na seção `@interface`:

    ```objc
    @interface ViewController () <UITextFieldDelegate, NSURLConnectionDataDelegate, NSXMLParserDelegate>

    // create the Authorization header to perform Basic authentication with your app back-end
    -(void) createAndSetAuthenticationHeaderWithUsername:(NSString*)username
                    AndPassword:(NSString*)password;

    @end
    ```

    > [!NOTE]
    > O trecho a seguir não é um esquema de autenticação seguro, você deve substituir a implementação do `createAndSetAuthenticationHeaderWithUsername:AndPassword:` pelo mecanismo de autenticação específico que gera um token de autenticação a ser consumido pela classe de cliente de registro, por exemplo, OAuth, Active Directory.

10. Em seguida, na seção `@implementation` de `ViewController.m`, adicione o código a seguir, que adiciona a implementação para definir o cabeçalho de autenticação e o token do dispositivo.

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

    Observe como a configuração do token do Dispositivo habilita o botão fazer logon. Isso ocorre porque, como parte da ação de logon, o controlador de exibição se registra para notificações por push com o back-end do aplicativo. Portanto, não quero que a ação de logon esteja acessível até que o token do dispositivo tenha sido configurado corretamente. Você pode desassociar o logon do registro por push desde que o primeiro ocorra antes do último.

11. Em ViewController. m, use os trechos de código a seguir para implementar o método de ação para o botão de **logon** e um método para enviar a mensagem de notificação usando o back-end ASP.net.

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

12. Atualize a ação para o botão **enviar notificação** para usar o back-end ASP.net e enviar para qualquer PNS habilitado por um comutador.

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

13. Na função `ViewDidLoad`, adicione o seguinte para instanciar a instância de `RegisterClient` e definir o delegado para seus campos de texto.

    ```objc
    self.UsernameField.delegate = self;
    self.PasswordField.delegate = self;
    self.RecipientField.delegate = self;
    self.registerClient = [[RegisterClient alloc] initWithEndpoint:BACKEND_ENDPOINT];
    ```

14. Agora, em `AppDelegate.m`, remova todo o conteúdo do método `application:didRegisterForPushNotificationWithDeviceToken:` e substitua-o pelo seguinte (para garantir que o controlador de exibição contenha o token de dispositivo mais recente recuperado do APNs):

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

15. Por fim, no `AppDelegate.m`, verifique se você tem o seguinte método:

    ```objc
    - (void)application:(UIApplication *)application didReceiveRemoteNotification: (NSDictionary *)userInfo {
        NSLog(@"%@", userInfo);
        [self MessageBox:@"Notification" message:[[userInfo objectForKey:@"aps"] valueForKey:@"alert"]];
    }
    ```

## <a name="test-the-application"></a>Testar a aplicação

1. No XCode, execute o aplicativo em um dispositivo iOS físico (as notificações por push não funcionam no simulador).
2. Na interface do usuário do aplicativo iOS, insira o mesmo valor para nome de usuário e senha. Em seguida, clique em **fazer logon**.

    ![aplicativo de teste do iOS][2]

3. Você deverá ver um pop-up informando sobre o sucesso do registro. Clique em **OK**.

    ![notificação de teste do iOS exibida][3]

4. No campo de texto de*marca de nome* de usuário do * destinatário, insira a marca de nome da usuária usada com o registro de outro dispositivo.
5. Insira uma mensagem de notificação e clique em **enviar notificação**. Somente os dispositivos que têm um registro com a marca de nome de usuário do destinatário recebem a mensagem de notificação. Ele é enviado somente para esses usuários.

    ![notificação de teste sinalizada do iOS][4]

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, aprendeu a enviar notificações push para utilizadores específicos que têm etiquetas associadas aos respetivos registos. Para saber como enviar notificações push com base na localização, avance para o seguinte tutorial: 

> [!div class="nextstepaction"]
>[Enviar notificações push com base na localização](notification-hubs-push-bing-spatial-data-geofencing-notification.md)

[1]: ./media/notification-hubs-aspnet-backend-ios-notify-users/notification-hubs-ios-notify-users-interface.png
[2]: ./media/notification-hubs-aspnet-backend-ios-notify-users/notification-hubs-ios-notify-users-enter-user-pwd.png
[3]: ./media/notification-hubs-aspnet-backend-ios-notify-users/notification-hubs-ios-notify-users-registered.png
[4]: ./media/notification-hubs-aspnet-backend-ios-notify-users/notification-hubs-ios-notify-users-enter-msg.png
