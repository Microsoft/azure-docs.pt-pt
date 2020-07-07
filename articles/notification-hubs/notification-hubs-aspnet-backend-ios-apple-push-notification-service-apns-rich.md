---
title: Azure Notification Hubs Rich Push
description: Saiba como enviar notificações push ricas para uma aplicação iOS da Azure. Amostras de código escritas em Objective-C e C#.
documentationcenter: ios
services: notification-hubs
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: 590304df-c0a4-46c5-8ef5-6a6486bb3340
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ios
ms.devlang: objective-c
ms.topic: article
ms.date: 01/04/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: 9da629929ca88f406dc503710477104be94c47e3
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "71212204"
---
# <a name="azure-notification-hubs-rich-push"></a>Azure Notification Hubs Rich Push

## <a name="overview"></a>Descrição Geral

Para envolver os utilizadores com conteúdos instantâneos e ricos, uma aplicação pode querer ir além do texto simples. Estas notificações promovem interações de utilizadores e apresentam conteúdos como urls, sons, imagens/cupões, entre outros. Este tutorial baseia-se no tópico ['Notificar utilizadores'](notification-hubs-aspnet-backend-ios-apple-apns-notification.md) e mostra como enviar notificações push que incorporam cargas útil (por exemplo, imagem).

Este tutorial é compatível com o iOS 7 & 8.

  ![][IOS1]

A um nível elevado:

1. O backend da aplicação:
   * Armazena a carga útil rica (neste caso, imagem) na base de dados de backend/armazenamento local
   * Envia ID desta rica notificação para o dispositivo
2. Aplicação no dispositivo:
   * Contacta o backend solicitando a carga útil rica com o ID que recebe
   * Envia notificações dos utilizadores no dispositivo quando a recuperação de dados está completa, e mostra a carga útil imediatamente quando os utilizadores tocam para saber mais

## <a name="webapi-project"></a>Projeto WebAPI

1. No Visual Studio, abra o projeto **AppBackend** que criou no tutorial [de Notifica users.](notification-hubs-aspnet-backend-ios-apple-apns-notification.md)
2. Obtenha uma imagem com a quais gostaria de notificar os utilizadores e colocá-la numa pasta **img** no seu diretório de projeto.
3. Clique em **Mostrar Todos os Ficheiros** no Explorador de Solução e clique com o botão direito na pasta para **Incluir no Projeto**.
4. Com a imagem selecionada, altere a sua janela Build Action in Properties para **o Recurso Incorporado.**

    ![][IOS2]
5. Em `Notifications.cs` , adicione a seguinte declaração de utilização:

    ```csharp
    using System.Reflection;
    ```
6. Atualize toda a `Notifications` classe com o seguinte código. Certifique-se de substituir os espaços reservados pelas credenciais do seu centro de notificação e nome de ficheiro de imagem.

    ```csharp
    public class Notification {
        public int Id { get; set; }
        // Initial notification message to display to users
        public string Message { get; set; }
        // Type of rich payload (developer-defined)
        public string RichType { get; set; }
        public string Payload { get; set; }
        public bool Read { get; set; }
    }

    public class Notifications {
        public static Notifications Instance = new Notifications();

        private List<Notification> notifications = new List<Notification>();

        public NotificationHubClient Hub { get; set; }

        private Notifications() {
            // Placeholders: replace with the connection string (with full access) for your notification hub and the hub name from the Azure Classics Portal
            Hub = NotificationHubClient.CreateClientFromConnectionString("{conn string with full access}",  "{hub name}");
        }

        public Notification CreateNotification(string message, string richType, string payload) {
            var notification = new Notification() {
                Id = notifications.Count,
                Message = message,
                RichType = richType,
                Payload = payload,
                Read = false
            };

            notifications.Add(notification);

            return notification;
        }

        public Stream ReadImage(int id) {
            var assembly = Assembly.GetExecutingAssembly();
            // Placeholder: image file name (for example, logo.png).
            return assembly.GetManifestResourceStream("AppBackend.img.{logo.png}");
        }
    }
    ```

   > [!NOTE]
   > (opcional) Consulte como [incorporar e aceder aos recursos utilizando o Visual C#](https://support.microsoft.com/kb/319292) para obter mais informações sobre como adicionar e obter recursos do projeto.

7. Em `NotificationsController.cs` , redefinir 'NotificationsController com os seguintes cortes. Isto envia um id de notificação silenciosa e silencioso para o dispositivo e permite a recuperação da imagem do lado do cliente:

    ```csharp
    // Return http response with image binary
    public HttpResponseMessage Get(int id) {
        var stream = Notifications.Instance.ReadImage(id);

        var result = new HttpResponseMessage(HttpStatusCode.OK);
        result.Content = new StreamContent(stream);
        // Switch in your image extension for "png"
        result.Content.Headers.ContentType = new System.Net.Http.Headers.MediaTypeHeaderValue("image/{png}");

        return result;
    }

    // Create rich notification and send initial silent notification (containing id) to client
    public async Task<HttpResponseMessage> Post() {
        // Replace the placeholder with image file name
        var richNotificationInTheBackend = Notifications.Instance.CreateNotification("Check this image out!", "img",  "{logo.png}");

        var usernameTag = "username:" + HttpContext.Current.User.Identity.Name;

        // Silent notification with content available
        var aboutUser = "{\"aps\": {\"content-available\": 1, \"sound\":\"\"}, \"richId\": \"" + richNotificationInTheBackend.Id.ToString() + "\",  \"richMessage\": \"" + richNotificationInTheBackend.Message + "\", \"richType\": \"" + richNotificationInTheBackend.RichType + "\"}";

        // Send notification to apns
        await Notifications.Instance.Hub.SendAppleNativeNotificationAsync(aboutUser, usernameTag);

        return Request.CreateResponse(HttpStatusCode.OK);
    }
    ```
8. Agora vamos re-implantar esta aplicação para um Website Azure de forma a torná-la acessível a partir de todos os dispositivos. Clique com o botão direito do rato no projeto **AppBackend** e selecione **Publicar**.
9. Selecione o Website Azure como o seu alvo de publicação. Faça login com a sua conta Azure e selecione um Website existente ou novo, e tome nota da propriedade URL de **destino** no separador **Ligação.** Vamos referir-nos a este URL como o seu ponto final de *backend* mais tarde neste tutorial. Clique em **Publicar**.

## <a name="modify-the-ios-project"></a>Modifique o projeto iOS

Agora que modificou o seu backend da app para enviar apenas o *id* de uma notificação, irá alterar a sua aplicação iOS para lidar com esse id e recuperar a mensagem rica do seu backend.

1. Abra o seu projeto iOS e permita notificações remotas indo para o seu alvo principal de aplicação na secção **Alvos.**
2. Clique em **Capabilities,** ligue os **modos de fundo**e verifique a caixa **de verificação de Notificações Remotas.**

    ![][IOS3]
3. Abra `Main.storyboard` , e certifique-se de que tem um Controlador de Visualização (referido como Controlador de Visualização Doméstica neste tutorial) a partir do tutorial do [Utilizador notificado.](notification-hubs-aspnet-backend-ios-apple-apns-notification.md)
4. Adicione um **Controlador de Navegação** ao seu storyboard e faça o control-drag para o Controlador home view para torná-lo a **visão raiz** da navegação. Certifique-se de que o **controlador de visualização inicial** em Atributos é selecionado apenas para o Controlador de Navegação.
5. Adicione um **Controlador de visualização** ao storyboard e adicione uma **visualização de imagem**. Esta é a página que os utilizadores verão assim que optarem por saber mais clicando na notificação. O seu storyboard deve parecer o seguinte:

    ![][IOS4]
6. Clique no **Controlador home view** no storyboard e certifique-se de que tem **homeViewController** como o seu **ID de classe personalizada** e **storyboard** sob o inspetor de identidade.
7. Faça o mesmo para o Controlador de Visualização de Imagem como **imageViewController**.
8. Em seguida, crie uma nova classe de Controlador de Visualização intitulada **ImagemViewController** para lidar com o UI que acabou de criar.
9. Em **imageViewController.h,** adicione o seguinte às declarações de interface do controlador. Certifique-se de controlar o arrastar da vista de imagem do storyboard para estas propriedades para ligar as duas:

    ```objc
    @property (weak, nonatomic) IBOutlet UIImageView *myImage;
    @property (strong) UIImage* imagePayload;
    ```
10. In `imageViewController.m` , adicione o seguinte no final `viewDidload` de:

    ```objc
    // Display the UI Image in UI Image View
    [self.myImage setImage:self.imagePayload];
    ```
11. Em `AppDelegate.m` , importe o controlador de imagem que criou:

    ```objc
    #import "imageViewController.h"
    ```
12. Adicione uma secção de interface com a seguinte declaração:

    ```objc
    @interface AppDelegate ()

    @property UIImage* imagePayload;
    @property NSDictionary* userInfo;
    @property BOOL iOS8;

    // Obtain content from backend with notification id
    - (void)retrieveRichImageWithId:(int)richId completion: (void(^)(NSError*)) completion;

    // Redirect to Image View Controller after notification interaction
    - (void)redirectToImageViewWithImage: (UIImage *)img;

    @end
    ```
13. Em `AppDelegate` , certifique-se de que a sua aplicação regista as notificações silenciosas em `application: didFinishLaunchingWithOptions` :

    ```objc
    // Software version
    self.iOS8 = [[UIApplication sharedApplication] respondsToSelector:@selector(registerUserNotificationSettings:)] && [[UIApplication sharedApplication] respondsToSelector:@selector(registerForRemoteNotifications)];

    // Register for remote notifications for iOS8 and previous versions
    if (self.iOS8) {
        NSLog(@"This device is running with iOS8.");

        // Action
        UIMutableUserNotificationAction *richPushAction = [[UIMutableUserNotificationAction alloc] init];
        richPushAction.identifier = @"richPushMore";
        richPushAction.activationMode = UIUserNotificationActivationModeForeground;
        richPushAction.authenticationRequired = NO;
        richPushAction.title = @"More";

        // Notification category
        UIMutableUserNotificationCategory* richPushCategory = [[UIMutableUserNotificationCategory alloc] init];
        richPushCategory.identifier = @"richPush";
        [richPushCategory setActions:@[richPushAction] forContext:UIUserNotificationActionContextDefault];

        // Notification categories
        NSSet* richPushCategories = [NSSet setWithObjects:richPushCategory, nil];

        UIUserNotificationSettings *settings = [UIUserNotificationSettings settingsForTypes:UIUserNotificationTypeSound |
                                                UIUserNotificationTypeAlert |
                                                UIUserNotificationTypeBadge
                                                                                    categories:richPushCategories];

        [[UIApplication sharedApplication] registerUserNotificationSettings:settings];
        [[UIApplication sharedApplication] registerForRemoteNotifications];

    }
    else {
        // Previous iOS versions
        NSLog(@"This device is running with iOS7 or earlier versions.");

        [[UIApplication sharedApplication] registerForRemoteNotificationTypes: UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound | UIRemoteNotificationTypeNewsstandContentAvailability];
    }

    return YES;
    ```

14. Substitua na seguinte implementação `application:didRegisterForRemoteNotificationsWithDeviceToken` para ter em conta as alterações da UI no storyboard:

    ```objc
    // Access navigation controller which is at the root of window
    UINavigationController *nc = (UINavigationController *)self.window.rootViewController;
    // Get home view controller from stack on navigation controller
    homeViewController *hvc = (homeViewController *)[nc.viewControllers objectAtIndex:0];
    hvc.deviceToken = deviceToken;
    ```
15. Em seguida, adicione os seguintes métodos `AppDelegate.m` para recuperar a imagem do seu ponto final e enviar uma notificação local quando a recuperação estiver completa. Certifique-se de substituir o espaço reservado `{backend endpoint}` pelo seu ponto final de backend:

    ```objc
    NSString *const GetNotificationEndpoint = @"{backend endpoint}/api/notifications";

    // Helper: retrieve notification content from backend with rich notification id
    - (void)retrieveRichImageWithId:(int)richId completion: (void(^)(NSError*)) completion {
        UINavigationController *nc = (UINavigationController *)self.window.rootViewController;
        homeViewController *hvc = (homeViewController *)[nc.viewControllers objectAtIndex:0];
        NSString* authenticationHeader = hvc.registerClient.authenticationHeader;
        // Check if authenticated
        if (!authenticationHeader) return;

        NSURLSession* session = [NSURLSession
                                sessionWithConfiguration:[NSURLSessionConfiguration defaultSessionConfiguration]
                                delegate:nil
                                delegateQueue:nil];

        NSURL* requestURL = [NSURL URLWithString:[NSString stringWithFormat:@"%@/%d", GetNotificationEndpoint, richId]];
        NSMutableURLRequest* request = [NSMutableURLRequest requestWithURL:requestURL];
        [request setHTTPMethod:@"GET"];
        NSString* authorizationHeaderValue = [NSString stringWithFormat:@"Basic %@", authenticationHeader];
        [request setValue:authorizationHeaderValue forHTTPHeaderField:@"Authorization"];

        NSURLSessionDataTask* dataTask = [session dataTaskWithRequest:request completionHandler:^(NSData *data, NSURLResponse *response, NSError *error) {

            NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*) response;
            if (!error && httpResponse.statusCode == 200) {
                // From NSData to UIImage
                self.imagePayload = [UIImage imageWithData:data];

                completion(nil);
            }
            else {
                NSLog(@"Error status: %ld, request: %@", (long)httpResponse.statusCode, error);
                if (error)
                    completion(error);
                else {
                    completion([NSError errorWithDomain:@"APICall" code:httpResponse.statusCode userInfo:nil]);
                }
            }
        }];
        [dataTask resume];
    }

    // Handle silent push notifications when id is sent from backend
    - (void)application:(UIApplication *)application didReceiveRemoteNotification:(NSDictionary *)userInfo fetchCompletionHandler:(void (^)(UIBackgroundFetchResult result))handler {
        self.userInfo = userInfo;
        int richId = [[self.userInfo objectForKey:@"richId"] intValue];
        NSString* richType = [self.userInfo objectForKey:@"richType"];

        // Retrieve image data
        if ([richType isEqualToString:@"img"]) {  
            [self retrieveRichImageWithId:richId completion:^(NSError* error) {
                if (!error){
                    // Send local notification
                    UILocalNotification* localNotification = [[UILocalNotification alloc] init];

                    // "5" is arbitrary here to give you enough time to quit out of the app and receive push notifications
                    localNotification.fireDate = [NSDate dateWithTimeIntervalSinceNow:5];
                    localNotification.userInfo = self.userInfo;
                    localNotification.alertBody = [self.userInfo objectForKey:@"richMessage"];
                    localNotification.timeZone = [NSTimeZone defaultTimeZone];

                    // iOS8 categories
                    if (self.iOS8) {
                        localNotification.category = @"richPush";
                    }

                    [[UIApplication sharedApplication] scheduleLocalNotification:localNotification];

                    handler(UIBackgroundFetchResultNewData);
                }
                else{
                    handler(UIBackgroundFetchResultFailed);
                }
            }];
        }
        // Add "else if" here to handle more types of rich content such as url, sound files, etc.
    }
    ```
16. Manuseie a notificação local acima, abrindo o controlador de visualização de imagem `AppDelegate.m` com os seguintes métodos:

    ```objc
    // Helper: redirect users to image view controller
    - (void)redirectToImageViewWithImage: (UIImage *)img {
        UINavigationController *navigationController = (UINavigationController*) self.window.rootViewController;
        UIStoryboard *mainStoryboard = [UIStoryboard storyboardWithName:@"Main" bundle: nil];
        imageViewController *imgViewController = [mainStoryboard instantiateViewControllerWithIdentifier: @"imageViewController"];
        // Pass data/image to image view controller
        imgViewController.imagePayload = img;

        // Redirect
        [navigationController pushViewController:imgViewController animated:YES];
    }

    // Handle local notification sent above in didReceiveRemoteNotification
    - (void)application:(UIApplication *)application didReceiveLocalNotification:(UILocalNotification *)notification {
        if (application.applicationState == UIApplicationStateActive) {
            // Show in-app alert with an extra "more" button
            UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Notification" message:notification.alertBody delegate:self cancelButtonTitle:@"OK" otherButtonTitles:@"More", nil];
            [alert show];
        }
        // App becomes active from user's tap on notification
        else {
            [self redirectToImageViewWithImage:self.imagePayload];
        }
    }

    // Handle buttons in in-app alerts and redirect with data/image
    - (void)alertView:(UIAlertView *)alertView clickedButtonAtIndex:(NSInteger)buttonIndex {
        // Handle "more" button
        if (buttonIndex == 1)
        {
            [self redirectToImageViewWithImage:self.imagePayload];
        }
        // Add "else if" here to handle more buttons
    }

    // Handle notification setting actions in iOS8
    - (void)application:(UIApplication *)application handleActionWithIdentifier:(NSString *)identifier forLocalNotification:(UILocalNotification *)notification completionHandler:(void (^)())completionHandler {
        // Handle richPush related buttons
        if ([identifier isEqualToString:@"richPushMore"]) {
            [self redirectToImageViewWithImage:self.imagePayload];
        }
        completionHandler();
    }
    ```

## <a name="run-the-application"></a>Executar a Aplicação

1. No XCode, execute a aplicação num dispositivo físico iOS (as notificações push não funcionarão no simulador).
2. Na aplicação iOS UI, introduza um nome de utilizador e senha com o mesmo valor para autenticação e clique **em Iniciar sessão**.
3. Clique **em Enviar o push** e deverá ver um alerta na aplicação. Se clicar em **Mais,** será levado à imagem que escolheu incluir no seu fundo de aplicações.
4. Também pode clicar **Em Enviar a pressão** e premir imediatamente o botão inicial do seu dispositivo. Dentro de momentos, receberá uma notificação push. Se tocar nele ou clicar em Mais, será levado para a sua app e para o rico conteúdo de imagem.

[IOS1]: ./media/notification-hubs-aspnet-backend-ios-rich-push/rich-push-ios-1.png
[IOS2]: ./media/notification-hubs-aspnet-backend-ios-rich-push/rich-push-ios-2.png
[IOS3]: ./media/notification-hubs-aspnet-backend-ios-rich-push/rich-push-ios-3.png
[IOS4]: ./media/notification-hubs-aspnet-backend-ios-rich-push/rich-push-ios-4.png
