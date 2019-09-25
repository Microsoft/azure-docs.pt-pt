---
title: Push avançado dos hubs de notificação do Azure
description: Saiba como enviar notificações por push avançadas para um aplicativo iOS do Azure. Exemplos de código escritos em Objective- C#C e.
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
ms.sourcegitcommit: 7df70220062f1f09738f113f860fad7ab5736e88
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/24/2019
ms.locfileid: "71212204"
---
# <a name="azure-notification-hubs-rich-push"></a>Push avançado dos hubs de notificação do Azure

## <a name="overview"></a>Descrição geral

Para envolver os usuários com conteúdo avançado instantâneo, um aplicativo pode querer enviar por push além do texto sem formatação. Essas notificações promovem interações do usuário e apresentam conteúdo como URLs, sons, imagens/cupons e muito mais. Este tutorial se baseia no tópico [notificar usuários](notification-hubs-aspnet-backend-ios-apple-apns-notification.md) e mostra como enviar notificações por push que incorporam cargas (por exemplo, imagem).

Este tutorial é compatível com o iOS 7 & 8.

  ![][IOS1]

Em um alto nível:

1. O back-end do aplicativo:
   * Armazena a carga avançada (neste caso, imagem) no banco de dados de back-end/armazenamento local
   * Envia a ID dessa notificação avançada para o dispositivo
2. Aplicativo no dispositivo:
   * Entra em contato com o back-end solicitando a carga avançada com a ID recebida
   * Envia notificações de usuários no dispositivo quando a recuperação de dados é concluída e mostra a carga imediatamente quando os usuários tocam para saber mais

## <a name="webapi-project"></a>Projeto WebAPI

1. No Visual Studio, abra o projeto **AppBackend** que você criou no tutorial [notificar usuários](notification-hubs-aspnet-backend-ios-apple-apns-notification.md) .
2. Obtenha uma imagem com a qual você gostaria de notificar os usuários e coloque-o em uma pasta **img** no diretório do projeto.
3. Clique em **Mostrar todos os arquivos** na Gerenciador de soluções e clique com o botão direito do mouse na pasta a ser **incluída no projeto**.
4. Com a imagem selecionada, altere sua ação de Build em janela Propriedades para **recurso inserido**.

    ![][IOS2]
5. No `Notifications.cs`, adicione a seguinte instrução Using:

    ```csharp
    using System.Reflection;
    ```
6. Atualize a classe `Notifications` inteira com o código a seguir. Certifique-se de substituir os espaços reservados pelas suas credenciais do hub de notificação e o nome do arquivo de imagem.

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
   > adicional Consulte [como inserir e acessar recursos usando o Visual C# ](https://support.microsoft.com/kb/319292) para obter mais informações sobre como adicionar e obter recursos de projeto.

7. No `NotificationsController.cs`, redefina ' NotificationsController ' com os trechos de código a seguir. Isso envia uma ID de notificação avançada silenciosa inicial para o dispositivo e permite a recuperação do lado do cliente da imagem:

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
8. Agora vamos implantar novamente esse aplicativo em um site do Azure para torná-lo acessível de todos os dispositivos. Clique com o botão direito do rato no projeto **AppBackend** e selecione **Publicar**.
9. Selecione site do Azure como seu destino de publicação. Faça logon com sua conta do Azure e selecione um site novo ou existente e anote a propriedade URL de **destino** na guia **conexão** . Vamos referir-nos a este URL como o *ponto final do seu back-end* mais adiante neste tutorial. Clique em **Publicar**.

## <a name="modify-the-ios-project"></a>Modificar o projeto do iOS

Agora que você modificou o back-end do aplicativo para enviar apenas a *ID* de uma notificação, você alterará seu aplicativo IOS para lidar com essa ID e recuperar a mensagem avançada do seu back-end.

1. Abra seu projeto do iOS e habilite notificações remotas acessando o destino do aplicativo principal na seção **destinos** .
2. Clique em **recursos**, ative os **modos de segundo plano**e marque a caixa de seleção **notificações remotas** .

    ![][IOS3]
3. Abra `Main.storyboard`o e verifique se você tem um controlador de exibição (conhecido como controlador de exibição de página inicial neste tutorial) em [notificar](notification-hubs-aspnet-backend-ios-apple-apns-notification.md) o tutorial do usuário.
4. Adicione um **controlador de navegação** ao seu storyboard e arraste para o controlador de exibição página inicial para torná-lo a **exibição raiz** da navegação. Verifique se o **controlador de exibição inicial está** no Inspetor de atributos está selecionado apenas para o controlador de navegação.
5. Adicione um **controlador de exibição** ao storyboard e adicione uma **exibição de imagem**. Esta é a página que os usuários verão quando escolherem saber mais clicando na notificação. O storyboard deve ter a seguinte aparência:

    ![][IOS4]
6. Clique no **controlador de exibição de página inicial** no storyboard e verifique se ele tem **HomeViewController** como sua **classe personalizada** e a **ID do storyboard** no Inspetor de identidade.
7. Faça o mesmo para o controlador de exibição de imagem como **imageViewController**.
8. Em seguida, crie uma nova classe de controlador de exibição chamada **imageViewController** para manipular a interface do usuário que você acabou de criar.
9. Em **imageViewController. h**, adicione o seguinte às declarações de interface do controlador. Certifique-se de controlar-arrastar da exibição de imagem do storyboard para essas propriedades para vincular as duas:

    ```objc
    @property (weak, nonatomic) IBOutlet UIImageView *myImage;
    @property (strong) UIImage* imagePayload;
    ```
10. No `imageViewController.m`, adicione o seguinte ao final de `viewDidload`:

    ```objc
    // Display the UI Image in UI Image View
    [self.myImage setImage:self.imagePayload];
    ```
11. No `AppDelegate.m`, importe o controlador de imagem que você criou:

    ```objc
    #import "imageViewController.h"
    ```
12. Adicione uma seção de interface com a seguinte declaração:

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
13. No `AppDelegate`, verifique se seu aplicativo se registra para notificações silenciosas no `application: didFinishLaunchingWithOptions`:

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

14. Substitua na seguinte implementação por `application:didRegisterForRemoteNotificationsWithDeviceToken` para que as alterações da interface do usuário do storyboard sejam alteradas em conta:

    ```objc
    // Access navigation controller which is at the root of window
    UINavigationController *nc = (UINavigationController *)self.window.rootViewController;
    // Get home view controller from stack on navigation controller
    homeViewController *hvc = (homeViewController *)[nc.viewControllers objectAtIndex:0];
    hvc.deviceToken = deviceToken;
    ```
15. Em seguida, adicione os métodos a `AppDelegate.m` seguir para recuperar a imagem de seu ponto de extremidade e enviar uma notificação local quando a recuperação for concluída. Certifique-se de substituir o `{backend endpoint}` espaço reservado pelo ponto de extremidade de back-end:

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
16. Manipule a notificação local acima abrindo o controlador de exibição de imagem no `AppDelegate.m` com os seguintes métodos:

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

## <a name="run-the-application"></a>Executar o aplicativo

1. No XCode, execute o aplicativo em um dispositivo iOS físico (as notificações por push não funcionarão no simulador).
2. Na interface do usuário do aplicativo iOS, insira um nome de usuário e senha do mesmo valor para autenticação e clique em **fazer logon**.
3. Clique em **Enviar Push** e você deverá ver um alerta no aplicativo. Se você clicar em **mais**, você será levado para a imagem que escolheu incluir no back-end do aplicativo.
4. Você também pode clicar em **enviar por push** e pressionar imediatamente o botão página inicial do seu dispositivo. Em alguns instantes, você receberá uma notificação por push. Se você tocar nele ou clicar em mais, você será levado ao seu aplicativo e ao conteúdo de imagem avançada.

[IOS1]: ./media/notification-hubs-aspnet-backend-ios-rich-push/rich-push-ios-1.png
[IOS2]: ./media/notification-hubs-aspnet-backend-ios-rich-push/rich-push-ios-2.png
[IOS3]: ./media/notification-hubs-aspnet-backend-ios-rich-push/rich-push-ios-3.png
[IOS4]: ./media/notification-hubs-aspnet-backend-ios-rich-push/rich-push-ios-4.png
