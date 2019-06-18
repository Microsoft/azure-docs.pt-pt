---
title: Push de avançado de Hubs de notificação do Azure
description: Saiba como enviar notificações push avançado para uma aplicação iOS do Azure. Exemplos de código escritos em Objective-C e c#.
documentationcenter: ios
services: notification-hubs
author: jwargo
manager: patniko
editor: spelluru
ms.assetid: 590304df-c0a4-46c5-8ef5-6a6486bb3340
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ios
ms.devlang: objective-c
ms.topic: article
ms.date: 01/04/2019
ms.author: jowargo
ms.openlocfilehash: dd808a04dff77388248bf7309f5ff804e6dd065c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60873113"
---
# <a name="azure-notification-hubs-rich-push"></a>Push de avançado de Hubs de notificação do Azure

## <a name="overview"></a>Descrição geral

Para interagir com os utilizadores com conteúdo elaborado instantâneo, uma aplicação poderá querer enviar por push para além de texto sem formatação. Estas notificações promovem interações de utilizador e o conteúdo presente como urls, sons, imagens/cupões e muito mais. Este tutorial baseia-se a [notificar utilizadores](notification-hubs-aspnet-backend-ios-apple-apns-notification.md) tópico e mostra como enviar notificações push que incorporam payloads (por exemplo, a imagem).

Este tutorial é compatível com iOS 7 e 8.

  ![][IOS1]

Num alto nível:

1. O back-end da aplicação:
   * Armazena o payload avançado (neste caso, a imagem) no armazenamento de base de dados/local do back-end
   * Envia o ID de notificação avançada para o dispositivo
2. Aplicação no dispositivo:
   * Entra em contacto com o back-end o payload avançado com o ID que recebe a pedir
   * Envia notificações de utilizadores no dispositivo quando obtenção de dados estiver concluída e mostra o payload imediatamente quando os utilizadores tocam em para saber mais

## <a name="webapi-project"></a>Projeto WebAPI

1. No Visual Studio, abra a **AppBackend** projeto que criou no [notificar utilizadores](notification-hubs-aspnet-backend-ios-apple-apns-notification.md) tutorial.
2. Obter uma imagem que pretende notificar os utilizadores com e o colocarei num **img** pasta no seu diretório do projeto.
3. Clique em **Show All Files** no Explorador de soluções e com o botão direito na pasta para **incluir no projeto**.
4. Com a imagem selecionada, altere a ação de compilação de mensagens em fila na janela de propriedades para **Embedded Resource**.

    ![][IOS2]
5. No `Notifications.cs`, adicione o seguinte usando a instrução:

    ```c#
    using System.Reflection;
    ```
6. Atualizar o todo `Notifications` classe com o código a seguir. Certifique-se de que substitui os marcadores com as suas credenciais do hub de notificação e o nome de ficheiro de imagem.

    ```c#
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
   > (opcional) Consulte a [como incorporar e aceder aos recursos através do elemento Visual C# ](https://support.microsoft.com/kb/319292) para obter mais informações sobre como adicionar e obter recursos de projeto.

7. No `NotificationsController.cs`, redefinir ' NotificationsController com os seguintes fragmentos. Isso envia um id de notificação de avançada silencioso inicial para o dispositivo e permite a obtenção do lado do cliente da imagem:

    ```c#
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
8. Agora, vamos voltar implementar esta aplicação num website do Azure para que seja acessível a partir de todos os dispositivos. Clique com o botão direito do rato no projeto **AppBackend** e selecione **Publicar**.
9. Selecione o Web site do Azure como o destino da publicação. Iniciar sessão com a sua conta do Azure, selecione um site novo ou existente e anote o **URL de destino** propriedade na **ligação** separador. Vamos referir-nos a este URL como o *ponto final do seu back-end* mais adiante neste tutorial. Clique em **Publicar**.

## <a name="modify-the-ios-project"></a>Modificar o projeto iOS

Agora que fez o seu back-end de aplicação para enviar apenas o *id* de uma notificação, irá alterar o seu aplicativo iOS para lidar com esse id e obter a mensagem avançada da sua back-end.

1. Abra o projeto iOS e ativar notificações remotas indo para o destino da sua aplicação principal no **destinos** secção.
2. Clique em **capacidades**, ative **modos em segundo plano**e verificar o **notificações remotas** caixa de verificação.

    ![][IOS3]
3. Open `Main.storyboard`, e certifique-se de que tem um controlador de exibição (referido como home page de View Controller neste tutorial) da [notificar utilizador](notification-hubs-aspnet-backend-ios-apple-apns-notification.md) tutorial.
4. Adicionar uma **controlador de navegação** para o seu guião e controle arrastar para a home page de View Controller para torná-lo a **vista de raiz** de navegação. Certifique-se de que o **controlador de vista do inicial é** nos atributos de inspetor de está selecionado para o controlador de navegação apenas.
5. Adicionar uma **View Controller** para executar o storyboard e adicionar um **vista de imagem**. Esta é a página será apresentada aos utilizadores assim que escolherem obter mais informações ao clicar na notificação. Seu guião gráfico deve ter o seguinte aspeto:

    ![][IOS4]
6. Clique nas **controlador de exibição home page** no storyboard e verifique se tem **homeViewController** como seu **classe personalizada** e **Storyboard ID**sob o inspetor de identidade.
7. Faça o mesmo para o controlador de vista da imagem como **imageViewController**.
8. Em seguida, crie uma nova classe de controlador de vista intitulada **imageViewController** para lidar com a interface do Usuário que acabou de criar.
9. Na **imageViewController.h**, adicione o seguinte para declarações de interface do controlador. Certifique-se arrastar de controle da vista de imagem do storyboard a essas propriedades para ligar os dois:

    ```objc
    @property (weak, nonatomic) IBOutlet UIImageView *myImage;
    @property (strong) UIImage* imagePayload;
    ```
10. Na `imageViewController.m`, adicione o seguinte no final do `viewDidload`:

    ```objc
    // Display the UI Image in UI Image View
    [self.myImage setImage:self.imagePayload];
    ```
11. No `AppDelegate.m`, importar o controlador de imagem que criou:

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
13. Na `AppDelegate`, certifique-se de que seu aplicativo registra silenciosas notificações no `application: didFinishLaunchingWithOptions`:

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

14. Substituto na seguinte implementação para `application:didRegisterForRemoteNotificationsWithDeviceToken` para tirar o storyboard de interface do Usuário altera-se em consideração:

    ```objc
    // Access navigation controller which is at the root of window
    UINavigationController *nc = (UINavigationController *)self.window.rootViewController;
    // Get home view controller from stack on navigation controller
    homeViewController *hvc = (homeViewController *)[nc.viewControllers objectAtIndex:0];
    hvc.deviceToken = deviceToken;
    ```
15. Em seguida, adicione os seguintes métodos para `AppDelegate.m` para obter a imagem do ponto final e enviar uma notificação local quando a recuperação estiver concluída. Certifique-se de substituir o marcador de posição `{backend endpoint}` com o ponto final de back-end:

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
16. Processar a notificação local acima, abrindo o controlador de vista de imagem no `AppDelegate.m` com os seguintes métodos:

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

## <a name="run-the-application"></a>Executar a aplicação

1. No XCode, execute a aplicação num dispositivo iOS físico (push notificações não funcionará no simulador).
2. Na aplicação do iOS da interface do Usuário, introduza um nome de utilizador e palavra-passe do mesmo valor para autenticação e clique em **sessão**.
3. Clique em **enviar push** e deverá ver um alerta na aplicação. Se clicar em **mais**, será reencaminhado para a imagem que escolheu para incluir no seu back-end de aplicação.
4. Também pode clicar **enviar push** e imediatamente prima o botão de início do seu dispositivo. Em alguns instantes, receberá uma notificação push. Se tocar no mesmo ou clique em mais, será reencaminhado para a sua aplicação e o conteúdo de imagem sofisticada.

[IOS1]: ./media/notification-hubs-aspnet-backend-ios-rich-push/rich-push-ios-1.png
[IOS2]: ./media/notification-hubs-aspnet-backend-ios-rich-push/rich-push-ios-2.png
[IOS3]: ./media/notification-hubs-aspnet-backend-ios-rich-push/rich-push-ios-3.png
[IOS4]: ./media/notification-hubs-aspnet-backend-ios-rich-push/rich-push-ios-4.png
