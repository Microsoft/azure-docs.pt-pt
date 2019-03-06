---
title: Notificações push para utilizadores específicos com os Hubs de notificação do Azure | Documentos da Microsoft
description: Saiba como enviar notificações push para utilizadores específicos com os Hubs de Notificação do Azure.
documentationcenter: ios
author: jwargo
manager: patniko
editor: spelluru
services: notification-hubs
ms.assetid: 1f7d1410-ef93-4c4b-813b-f075eed20082
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ios
ms.devlang: objective-c
ms.topic: article
ms.date: 01/04/2019
ms.author: jowargo
ms.openlocfilehash: d69a2a26fb0b487a3efa4b5376ca6480569aa846
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57450894"
---
# <a name="tutorial-push-notifications-to-specific-users-using-azure-notification-hubs"></a>Tutorial: Notificações push para utilizadores específicos com os Hubs de notificação do Azure

[!INCLUDE [notification-hubs-selector-aspnet-backend-notify-users](../../includes/notification-hubs-selector-aspnet-backend-notify-users.md)]

Este tutorial mostra-lhe como utilizar os Hubs de Notificação do Azure para enviar notificações push para um utilizador específico da aplicação num dispositivo específico. Um back-end de ASP.NET WebAPI é utilizado para autenticar clientes e gerar notificações, conforme mostrado no tópico de documentação de orientação [registar a partir do seu back-end de aplicação](notification-hubs-push-notification-registration-management.md#registration-management-from-a-backend).

Neste tutorial, siga os passos seguintes:

> [!div class="checklist"]
> * Criar o Projeto WebAPI
> * Autenticar clientes no back-end de WebAPI
> * Utilizar o back-end de WebAPI para registar notificações
> * Enviar notificações a partir do back-end de WebAPI
> * Publicar o back-end de WebAPI novo
> * Modificar as suas aplicações iOS
> * Testar a aplicação

## <a name="prerequisites"></a>Pré-requisitos

Este tutorial pressupõe que já criou e configurou o notification hub conforme descrito em [introdução aos Hubs de notificação (iOS)](notification-hubs-ios-apple-push-notification-apns-get-started.md). Este tutorial também é o pré-requisito para o [Secure Push (iOS)](notification-hubs-aspnet-backend-ios-push-apple-apns-secure-notification.md) tutorial.
Se pretender utilizar aplicações móveis que o seu serviço de back-end, consulte a [Mobile Apps introdução ao Push](../app-service-mobile/app-service-mobile-ios-get-started-push.md).

[!INCLUDE [notification-hubs-aspnet-backend-notifyusers](../../includes/notification-hubs-aspnet-backend-notifyusers.md)]

## <a name="modify-your-ios-app"></a>Modificar as suas aplicações iOS

1. Abra a aplicação de vista de página única que criou no [introdução aos Hubs de notificação (iOS)](notification-hubs-ios-apple-push-notification-apns-get-started.md) tutorial.

   > [!NOTE]
   > Esta secção assume que seu projeto está configurado com um nome de organização vazio. Caso contrário, terá de preceder o nome da sua organização para todos os nomes de classe.

2. Na `Main.storyboard` de ficheiros, adicione os componentes mostrados na captura de ecrã da biblioteca do objeto.

    ![Editar o storyboard no construtor de interface do Xcode][1]

   * **Nome de utilizador**: Um UITextField com texto de marcador de posição *introduza o nome de utilizador*, imediatamente abaixo do envio resultados Etiquetar e restrita para as margens esquerdas e direita e sob a etiqueta de resultados de envio.
   * **palavra-passe**: Um UITextField com texto de marcador de posição *introduza a palavra-passe*, imediatamente abaixo do nome de utilizador restrito para as margens esquerdas e direita e sob o campo de texto do nome de utilizador e de campo de texto. Verifique a **Secure entrada de texto** opção o Inspetor de atributo, em *devolver chave*.
   * **Inicie sessão no**: Um UIButton etiquetados imediatamente abaixo do campo de texto da palavra-passe e desmarque o **Enabled** opção o Inspetor de atributos, em *conteúdo de controle*
   * **WNS**: Etiquetar e alternar para ativar o envio da notificação de serviço de notificação do Windows, se tiver sido configurado no hub. Consulte a [introdução ao Windows](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md) tutorial.
   * **GCM**: Etiquetar e alternar para ativar o envio da notificação para a Google Cloud Messaging, se tiver sido configurado no hub. Ver [introdução ao Android](notification-hubs-android-push-notification-google-gcm-get-started.md) tutorial.
   * **APNS**: Etiqueta e o comutador para permitir o envio da notificação para o serviço de notificação de plataforma da Apple.
   * **Destinatário Username:A** UITextField com texto de marcador de posição *etiqueta de nome de utilizador do destinatário*, imediatamente abaixo do GCM identificar e restrita para as margens esquerdas e direita e sob a etiqueta do GCM.

    Alguns componentes foram adicionados na [introdução aos Hubs de notificação (iOS)](notification-hubs-ios-apple-push-notification-apns-get-started.md) tutorial.

3. **CTRL** arraste a partir dos componentes na vista para `ViewController.h` e adicione essas saídas de novo.

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

4. Na `ViewController.h`, adicione o seguinte `#define` após suas declarações de importação. Substitua o `<Enter Your Backend Endpoint>` espaço reservado com o URL de destino que utilizou para implementar o seu back-end de aplicação na secção anterior. Por exemplo, `http://your_backend.azurewebsites.net`.

    ```objc
    #define BACKEND_ENDPOINT @"<Enter Your Backend Endpoint>"
    ```

5. No seu projeto, crie uma nova classe Cocoa Touch com o nome `RegisterClient` a interface com o back-end ASP.NET que criou. Criar a classe a herdar de `NSObject`. Em seguida, adicione o seguinte código no `RegisterClient.h`.

    ```objc
    @interface RegisterClient : NSObject

    @property (strong, nonatomic) NSString* authenticationHeader;

    -(void) registerWithDeviceToken:(NSData*)token tags:(NSSet*)tags
        andCompletion:(void(^)(NSError*))completion;

    -(instancetype) initWithEndpoint:(NSString*)Endpoint;

    @end
    ```

6. Na `RegisterClient.m`, atualize o `@interface` secção:

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

7. Substitua o `@implementation` secção RegisterClient.m com o código a seguir:

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

    Esse código implementa a lógica, explicada no artigo de documentação de orientação [registar a partir do seu back-end de aplicação](notification-hubs-push-notification-registration-management.md#registration-management-from-a-backend) utilizando NSURLSession para efetuar o REST é chamado para seu back-end de aplicação e NSUserDefaults para armazenar localmente o registrationId devolvido pela hub de notificação.

    Essa classe requer sua propriedade `authorizationHeader` ser definida para que funcionem corretamente. Esta propriedade é definida `ViewController` classe após o início de sessão.

8. Na `ViewController.h`, adicione um `#import` instrução para `RegisterClient.h`. Em seguida, adicione uma declaração para o token de dispositivo e de referência para um `RegisterClient` instância no `@interface` secção:

    ```objc
    #import "RegisterClient.h"

    @property (strong, nonatomic) NSData* deviceToken;
    @property (strong, nonatomic) RegisterClient* registerClient;
    ```

9. No ViewController.m, adicione uma declaração do método particular no `@interface` secção:

    ```objc
    @interface ViewController () <UITextFieldDelegate, NSURLConnectionDataDelegate, NSXMLParserDelegate>

    // create the Authorization header to perform Basic authentication with your app back-end
    -(void) createAndSetAuthenticationHeaderWithUsername:(NSString*)username
                    AndPassword:(NSString*)password;

    @end
    ```

    > [!NOTE]
    > O fragmento seguinte não é um esquema de autenticação segura, deve substituir a implementação do `createAndSetAuthenticationHeaderWithUsername:AndPassword:` com o mecanismo de autenticação específicos que gera um token de autenticação para serem consumidos por classe de cliente Registre-se, por exemplo, OAuth, do Active Directory.

10. Em seguida, no `@implementation` secção do `ViewController.m`, adicione o seguinte código, que adiciona a implementação para definir o cabeçalho de token e a autenticação do dispositivo.

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

    Observe como a definição do token de dispositivo permite que o botão de início de sessão. É porque como parte da ação de início de sessão, o controlador de vista se registra para notificações push com o back-end de aplicação. Por conseguinte, não pretende que Log em ação para estar acessível até que o token do dispositivo foi corretamente configurado. É possível desacoplar o início de sessão do registo de push, desde que o primeiro acontece antes do último.

11. No ViewController.m, utilize os seguintes fragmentos para implementar o método de ação para sua **sessão** botão e um método para enviar a mensagem de notificação utilizando o back-end ASP.NET.

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

12. Atualizar a ação para o **Enviar notificação** botão para utilizar o back-end ASP.NET e enviar para qualquer PNS ativada por um comutador.

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

13. Na `ViewDidLoad` funcionar, adicione o seguinte para criar uma instância a `RegisterClient` de instância e defina o delegado para os campos de texto.

    ```objc
    self.UsernameField.delegate = self;
    self.PasswordField.delegate = self;
    self.RecipientField.delegate = self;
    self.registerClient = [[RegisterClient alloc] initWithEndpoint:BACKEND_ENDPOINT];
    ```

14. Agora na `AppDelegate.m`, remova todo o conteúdo do método `application:didRegisterForPushNotificationWithDeviceToken:` e substituí-lo com o seguinte (para se certificar-se de que o controlador de vista contém o token de dispositivo mais recente obtido a partir do APNs):

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

15. Por fim no `AppDelegate.m`, certifique-se de que tem o seguinte método:

    ```objc
    - (void)application:(UIApplication *)application didReceiveRemoteNotification: (NSDictionary *)userInfo {
        NSLog(@"%@", userInfo);
        [self MessageBox:@"Notification" message:[[userInfo objectForKey:@"aps"] valueForKey:@"alert"]];
    }
    ```

## <a name="test-the-application"></a>Testar a aplicação

1. No XCode, execute a aplicação num dispositivo iOS físico (push notificações não funcionam no simulador).
2. Na aplicação do iOS da interface do Usuário, introduza o mesmo valor para o nome de utilizador e palavra-passe. Em seguida, clique em **sessão**.

    ![testar a aplicação iOS][2]

3. Verá um pop-up informando-o de sucesso de registo. Clique em **OK**.

    ![notificação apresentada de teste de iOS][3]

4. Na **etiqueta de nome de utilizador do destinatário* texto, insira a etiqueta de nome de utilizador utilizada com o registo a partir de outro dispositivo.
5. Introduza uma mensagem de notificação e clique em **Enviar notificação**. Apenas os dispositivos que têm um registo com a etiqueta de nome de utilizador destinatários recebem a mensagem de notificação. É enviada apenas aos usuários.

    ![marcado de notificação de teste de iOS][4]

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, aprendeu a enviar notificações push para utilizadores específicos que têm etiquetas associadas aos respetivos registos. Para saber como enviar notificações push com base na localização, avance para o seguinte tutorial: 

> [!div class="nextstepaction"]
>[Enviar notificações push com base na localização](notification-hubs-push-bing-spatial-data-geofencing-notification.md)

[1]: ./media/notification-hubs-aspnet-backend-ios-notify-users/notification-hubs-ios-notify-users-interface.png
[2]: ./media/notification-hubs-aspnet-backend-ios-notify-users/notification-hubs-ios-notify-users-enter-user-pwd.png
[3]: ./media/notification-hubs-aspnet-backend-ios-notify-users/notification-hubs-ios-notify-users-registered.png
[4]: ./media/notification-hubs-aspnet-backend-ios-notify-users/notification-hubs-ios-notify-users-enter-msg.png
