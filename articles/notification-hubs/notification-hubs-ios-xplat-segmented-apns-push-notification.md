---
title: Notificações por push para dispositivos iOS específicos usando hubs de notificação do Azure | Microsoft Docs
description: Neste tutorial, você aprenderá a usar os hubs de notificação do Azure para enviar notificações por push para dispositivos iOS específicos.
services: notification-hubs
documentationcenter: ios
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: 6ead4169-deff-4947-858c-8c6cf03cc3b2
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: article
ms.date: 07/28/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 07/28/2019
ms.openlocfilehash: 8299725cf6977ca309d57b40f4792ff9b074a8cb
ms.sourcegitcommit: 7df70220062f1f09738f113f860fad7ab5736e88
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/24/2019
ms.locfileid: "71213227"
---
# <a name="tutorial-push-notifications-to-specific-ios-devices-using-azure-notification-hubs"></a>Tutorial: Notificações por push para dispositivos iOS específicos usando os hubs de notificação do Azure

[!INCLUDE [notification-hubs-selector-breaking-news](../../includes/notification-hubs-selector-breaking-news.md)]

## <a name="overview"></a>Descrição geral

Este tutorial mostra como usar os hubs de notificação do Azure para transmitir notificações de últimas notícias para um aplicativo iOS. Ao concluir, você poderá se registrar para as categorias de últimas notícias nas quais está interessado e receber apenas notificações por push para essas categorias. Este é um cenário com um padrão comum para muitas aplicações em que as notificações têm de ser enviadas para grupos de utilizadores que mostraram anteriormente interesse nas mesmas, por exemplo, leitor de RSS, aplicações para fãs de música, etc.

Os cenários de transmissão são ativados ao incluir uma ou mais *etiquetas* durante a criação de um registo no Hub de Notificação. Quando as notificações são enviadas a uma marca, os dispositivos que foram registrados para a marca recebem a notificação. Como as etiquetas são simples cadeias, não têm de ser aprovisionadas com antecedência. Para obter mais informações sobre etiquetas, consulte [Encaminhamento de Hubs de Notificação e Expressões de Etiqueta](notification-hubs-tags-segment-push-message.md).

Neste tutorial, siga os passos seguintes:

> [!div class="checklist"]
> * Adicionar uma seleção de categoria ao aplicativo
> * Enviar notificações com etiquetas
> * Enviar notificações do dispositivo
> * Executar a aplicação e gerar notificações

## <a name="prerequisites"></a>Pré-requisitos

Este tópico se baseia no aplicativo criado no [tutorial: Notificações por push para aplicativos iOS usando os hubs][get-started]de notificação do Azure. Antes de iniciar este tutorial, você já deve ter [concluído o tutorial: Notificações por push para aplicativos iOS usando os hubs][get-started]de notificação do Azure.

## <a name="add-category-selection-to-the-app"></a>Adicionar a seleção de categorias à aplicação

A primeira etapa é adicionar os elementos da interface do usuário ao storyboard existente que permitem ao usuário selecionar categorias para registrar. As categorias selecionadas por um utilizador são armazenadas no dispositivo. Quando a aplicação é iniciada, é criado o registo do dispositivo no seu Hub de Notificação com as categorias selecionadas como etiquetas.

1. Em seu **MainStoryboard_iPhone. Storyboard** , adicione os seguintes componentes da biblioteca de objetos:

   * Um rótulo com texto de "últimas notícias",
   * Rótulos com textos de categoria "mundo", "política", "negócios", "tecnologia", "ciência", "esportes",
   * Seis opções, uma por categoria, definem cada **estado** de comutador como **off** por padrão.
   * Um botão rotulado como "assinar"

     O storyboard deve ter a seguinte aparência:

     ![Construtor de interface do Xcode][3]

2. No editor do assistente, crie saídas para todos os comutadores e chame-os de "WorldSwitch", "PoliticsSwitch", "BusinessSwitch", "TechnologySwitch", "ScienceSwitch", "SportsSwitch"
3. Crie uma ação para o botão chamado `subscribe`; seu `ViewController.h` deve conter o seguinte código:

    ```objc
    @property (weak, nonatomic) IBOutlet UISwitch *WorldSwitch;
    @property (weak, nonatomic) IBOutlet UISwitch *PoliticsSwitch;
    @property (weak, nonatomic) IBOutlet UISwitch *BusinessSwitch;
    @property (weak, nonatomic) IBOutlet UISwitch *TechnologySwitch;
    @property (weak, nonatomic) IBOutlet UISwitch *ScienceSwitch;
    @property (weak, nonatomic) IBOutlet UISwitch *SportsSwitch;

    - (IBAction)subscribe:(id)sender;
    ```

4. Crie uma nova **classe Cocoa Touch** chamada `Notifications`. Copie o código a seguir na seção interface do arquivo Notifications. h:

    ```objc
    @property NSData* deviceToken;

    - (id)initWithConnectionString:(NSString*)listenConnectionString HubName:(NSString*)hubName;

    - (void)storeCategoriesAndSubscribeWithCategories:(NSArray*)categories
                completion:(void (^)(NSError* error))completion;

    - (NSSet*)retrieveCategories;

    - (void)subscribeWithCategories:(NSSet*)categories completion:(void (^)(NSError *))completion;
    ```

5. Adicione a seguinte diretiva de importação a notificações. m:

    ```objc
    #import <WindowsAzureMessaging/WindowsAzureMessaging.h>
    ```

6. Copie o código a seguir na seção implementação do arquivo Notifications. m.

    ```objc
    SBNotificationHub* hub;

    - (id)initWithConnectionString:(NSString*)listenConnectionString HubName:(NSString*)hubName{

        hub = [[SBNotificationHub alloc] initWithConnectionString:listenConnectionString
                                    notificationHubPath:hubName];

        return self;
    }

    - (void)storeCategoriesAndSubscribeWithCategories:(NSSet *)categories completion:(void (^)(NSError *))completion {
        NSUserDefaults* defaults = [NSUserDefaults standardUserDefaults];
        [defaults setValue:[categories allObjects] forKey:@"BreakingNewsCategories"];

        [self subscribeWithCategories:categories completion:completion];
    }

    - (NSSet*)retrieveCategories {
        NSUserDefaults* defaults = [NSUserDefaults standardUserDefaults];

        NSArray* categories = [defaults stringArrayForKey:@"BreakingNewsCategories"];

        if (!categories) return [[NSSet alloc] init];
        return [[NSSet alloc] initWithArray:categories];
    }

    - (void)subscribeWithCategories:(NSSet *)categories completion:(void (^)(NSError *))completion
    {
        //[hub registerNativeWithDeviceToken:self.deviceToken tags:categories completion: completion];

        NSString* templateBodyAPNS = @"{\"aps\":{\"alert\":\"$(messageParam)\"}}";

        [hub registerTemplateWithDeviceToken:self.deviceToken name:@"simpleAPNSTemplate" 
            jsonBodyTemplate:templateBodyAPNS expiryTemplate:@"0" tags:categories completion:completion];
    }
    ```

    Essa classe usa o armazenamento local para armazenar e recuperar as categorias de notícias que esse dispositivo recebe. Além disso, ele contém um método para se registrar nessas categorias usando um registro de [modelo](notification-hubs-templates-cross-platform-push-messages.md) .

7. No arquivo, adicione uma instrução de importação para `Notifications.h` e adicione uma propriedade `Notifications` para uma instância da classe: `AppDelegate.h`

    ```objc
    #import "Notifications.h"

    @property (nonatomic) Notifications* notifications;
    ```

8. No método no `AppDelegate.m`, adicione o código para inicializar a instância de notificações no início do método. `didFinishLaunchingWithOptions`  
    `HUBNAME`e `HUBLISTENACCESS` (definido em `hubinfo.h`) já deve ter os `<hub name>` espaços `<connection string with listen access>` reservados e substituídos pelo nome do hub de notificação e pela cadeia de conexão para *DefaultListenSharedAccessSignature* que você obteve superior

    ```objc
    self.notifications = [[Notifications alloc] initWithConnectionString:HUBLISTENACCESS HubName:HUBNAME];
    ```

    > [!NOTE]
    > Uma vez que, de um modo geral, as credenciais que são distribuídas com uma aplicação cliente não são seguras, só deve distribuir a chave para acesso de escuta com a sua aplicação cliente. O acesso de escuta permite que a sua aplicação seja registada para receber notificações, mas não é possível modificar os registos existentes nem enviar notificações. A chave de acesso total é utilizada num serviço back-end protegido para enviar notificações e alterar registos existentes.

9. No método no `AppDelegate.m`, substitua o código no método pelo código a seguir para passar o token do dispositivo para a `notifications` classe. `didRegisterForRemoteNotificationsWithDeviceToken` A `notifications` classe executa o registro para notificações com as categorias. Se o usuário alterar as seleções de categoria, chame `subscribeWithCategories` o método em resposta ao botão **assinar** para atualizá-las.

    > [!NOTE]
    > Como o token de dispositivo atribuído pelo Apple Push Notification Service (APNS) pode ser possível a qualquer momento, você deve se registrar para receber notificações com frequência para evitar falhas de notificação. Este exemplo regista-se em notificações sempre que a aplicação é iniciada. Relativamente às aplicações executadas com frequência, ou seja, mais do que uma vez por dia, pode provavelmente ignorar o registo para poupar a largura de banda, caso tenha passado menos de um dia desde o registo anterior.

    ```objc
    self.notifications.deviceToken = deviceToken;

    // Retrieves the categories from local storage and requests a registration for these categories
    // each time the app starts and performs a registration.

    NSSet* categories = [self.notifications retrieveCategories];
    [self.notifications subscribeWithCategories:categories completion:^(NSError* error) {
        if (error != nil) {
            NSLog(@"Error registering for notifications: %@", error);
        }
    }];
    ```

    Neste ponto, não deve haver nenhum outro código no `didRegisterForRemoteNotificationsWithDeviceToken` método.

10. Os métodos a seguir já devem estar presentes `AppDelegate.m` no concluindo o tutorial introdução [aos hubs de notificação][get-started] . Caso contrário, adicione-os.

    ```objc
    - (void)MessageBox:(NSString *)title message:(NSString *)messageText
    {

        UIAlertView *alert = [[UIAlertView alloc] initWithTitle:title message:messageText delegate:self
            cancelButtonTitle:@"OK" otherButtonTitles: nil];
        [alert show];
    }

    - (void)application:(UIApplication *)application didReceiveRemoteNotification:
       (NSDictionary *)userInfo {
       NSLog(@"%@", userInfo);
       [self MessageBox:@"Notification" message:[[userInfo objectForKey:@"aps"] valueForKey:@"alert"]];
     }
    ```

    Esse método manipula as notificações recebidas quando o aplicativo está em execução exibindo um **UIAlert**simples.

11. No `ViewController.m`, adicione uma `import` instrução para `AppDelegate.h` e copie o código a seguir para o método gerado `subscribe` pelo Xcode. Esse código atualiza o registro de notificação para usar as novas marcas de categoria que o usuário escolheu na interface do usuário.

    ```objc
    #import "Notifications.h"

    NSMutableArray* categories = [[NSMutableArray alloc] init];

    if (self.WorldSwitch.isOn) [categories addObject:@"World"];
    if (self.PoliticsSwitch.isOn) [categories addObject:@"Politics"];
    if (self.BusinessSwitch.isOn) [categories addObject:@"Business"];
    if (self.TechnologySwitch.isOn) [categories addObject:@"Technology"];
    if (self.ScienceSwitch.isOn) [categories addObject:@"Science"];
    if (self.SportsSwitch.isOn) [categories addObject:@"Sports"];

    Notifications* notifications = [(AppDelegate*)[[UIApplication sharedApplication]delegate] notifications];

    [notifications storeCategoriesAndSubscribeWithCategories:categories completion: ^(NSError* error) {
        if (!error) {
            UIAlertView *alert = [[UIAlertView alloc] initWithTitle:"Notification" message:"Subscribed" delegate:self
            cancelButtonTitle:@"OK" otherButtonTitles: nil];
            [alert show];
        } else {
            NSLog(@"Error subscribing: %@", error);
        }
    }];
    ```

    Esse método cria uma `NSMutableArray` de categorias e usa a `Notifications` classe para armazenar a lista no armazenamento local e registra as marcas correspondentes no Hub de notificação. Quando as categorias são alteradas, o registo é recriado com as novas categorias.

12. No `ViewController.m`, adicione o código a seguir `viewDidLoad` no método para definir a interface do usuário com base nas categorias salvas anteriormente.

    ```objc
    // This updates the UI on startup based on the status of previously saved categories.

    Notifications* notifications = [(AppDelegate*)[[UIApplication sharedApplication]delegate] notifications];

    NSSet* categories = [notifications retrieveCategories];

    if ([categories containsObject:@"World"]) self.WorldSwitch.on = true;
    if ([categories containsObject:@"Politics"]) self.PoliticsSwitch.on = true;
    if ([categories containsObject:@"Business"]) self.BusinessSwitch.on = true;
    if ([categories containsObject:@"Technology"]) self.TechnologySwitch.on = true;
    if ([categories containsObject:@"Science"]) self.ScienceSwitch.on = true;
    if ([categories containsObject:@"Sports"]) self.SportsSwitch.on = true;
    ```

Agora, o aplicativo pode armazenar um conjunto de categorias no armazenamento local do dispositivo usado para se registrar no Hub de notificação sempre que o aplicativo for iniciado. O usuário pode alterar a seleção de categorias em tempo de execução e `subscribe` clicar no método para atualizar o registro do dispositivo. Em seguida, você atualiza o aplicativo para enviar as notificações de últimas notícias diretamente no próprio aplicativo.

## <a name="optional-send-tagged-notifications"></a>adicional Enviar notificações marcadas

Se você não tiver acesso ao Visual Studio, poderá pular para a próxima seção e enviar notificações do próprio aplicativo. Você também pode enviar a notificação de modelo apropriada do [Azure portal] usando a guia Depurar do hub de notificação.

[!INCLUDE [notification-hubs-send-categories-template](../../includes/notification-hubs-send-categories-template.md)]

## <a name="optional-send-notifications-from-the-device"></a>adicional Enviar notificações do dispositivo

Normalmente, as notificações seriam enviadas por um serviço de back-end, mas você pode enviar notificações de últimas notícias diretamente do aplicativo. Para fazer isso, atualize o `SendNotificationRESTAPI` método que você definiu no tutorial introdução [aos hubs de notificação][get-started] .

1. No `ViewController.m`, atualize o `SendNotificationRESTAPI` método da seguinte maneira para que ele aceite um parâmetro para a marca de categoria e envie a notificação de [modelo](notification-hubs-templates-cross-platform-push-messages.md) apropriada.

    ```objc
    - (void)SendNotificationRESTAPI:(NSString*)categoryTag
    {
        NSURLSession* session = [NSURLSession sessionWithConfiguration:[NSURLSessionConfiguration
                                    defaultSessionConfiguration] delegate:nil delegateQueue:nil];

        NSString *json;

        // Construct the messages REST endpoint
        NSURL* url = [NSURL URLWithString:[NSString stringWithFormat:@"%@%@/messages/%@", HubEndpoint,
                                            HUBNAME, API_VERSION]];

        // Generated the token to be used in the authorization header.
        NSString* authorizationToken = [self generateSasToken:[url absoluteString]];

        //Create the request to add the template notification message to the hub
        NSMutableURLRequest *request = [NSMutableURLRequest requestWithURL:url];
        [request setHTTPMethod:@"POST"];

        // Add the category as a tag
        [request setValue:categoryTag forHTTPHeaderField:@"ServiceBusNotification-Tags"];

        // Template notification
        json = [NSString stringWithFormat:@"{\"messageParam\":\"Breaking %@ News : %@\"}",
                categoryTag, self.notificationMessage.text];

        // Signify template notification format
        [request setValue:@"template" forHTTPHeaderField:@"ServiceBusNotification-Format"];

        // JSON Content-Type
        [request setValue:@"application/json;charset=utf-8" forHTTPHeaderField:@"Content-Type"];

        //Authenticate the notification message POST request with the SaS token
        [request setValue:authorizationToken forHTTPHeaderField:@"Authorization"];

        //Add the notification message body
        [request setHTTPBody:[json dataUsingEncoding:NSUTF8StringEncoding]];

        // Send the REST request
        NSURLSessionDataTask* dataTask = [session dataTaskWithRequest:request
                    completionHandler:^(NSData *data, NSURLResponse *response, NSError *error)
            {
            NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*) response;
                if (error || httpResponse.statusCode != 200)
                {
                    NSLog(@"\nError status: %d\nError: %@", httpResponse.statusCode, error);
                }
                if (data != NULL)
                {
                    //xmlParser = [[NSXMLParser alloc] initWithData:data];
                    //[xmlParser setDelegate:self];
                    //[xmlParser parse];
                }
            }];

        [dataTask resume];
    }
    ```

2. No `ViewController.m`, atualize a `Send Notification` ação conforme mostrado no código a seguir. Para que ele envie as notificações usando cada marca individualmente e envie para várias plataformas.

    ```objc
    - (IBAction)SendNotificationMessage:(id)sender
    {
        self.sendResults.text = @"";

        NSArray* categories = [NSArray arrayWithObjects: @"World", @"Politics", @"Business",
                                @"Technology", @"Science", @"Sports", nil];

        // Lets send the message as breaking news for each category to WNS, FCM, and APNS
        // using a template.
        for(NSString* category in categories)
        {
            [self SendNotificationRESTAPI:category];
        }
    }
    ```

3. Recompile seu projeto e verifique se você não tem nenhum erro de compilação.

## <a name="run-the-app-and-generate-notifications"></a>Executar a aplicação e gerar notificações

1. Pressione o botão Executar para compilar o projeto e iniciar o aplicativo. Selecione algumas opções de últimas notícias para assinar e pressione o botão **assinar** . Você deverá ver uma caixa de diálogo indicando que as notificações foram assinadas.

    ![Notificação de exemplo no iOS][1]

    Quando você escolhe **assinar**, o aplicativo converte as categorias selecionadas em marcas e solicita um novo registro de dispositivo para as marcas selecionadas do hub de notificação.

2. Insira uma mensagem a ser enviada como últimas notícias e pressione o botão **enviar notificação** . Como alternativa, execute o aplicativo de console .NET para gerar notificações.

    ![Alterar as preferências de notificação no iOS][2]

3. Cada dispositivo inscrito nas últimas notícias recebe as notificações de últimas notícias que você acabou de enviar.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, você enviou notificações de difusão para dispositivos iOS específicos que se registraram para as categorias. Para saber como enviar notificações localizadas por push, avance para o tutorial a seguir:

> [!div class="nextstepaction"]
>[Push localized notifications](notification-hubs-ios-xplat-localized-apns-push-notification.md) (Enviar notificações localizadas)

<!-- Images. -->
[1]: ./media/notification-hubs-ios-send-breaking-news/notification-hub-breakingnews-subscribed.png
[2]: ./media/notification-hubs-ios-send-breaking-news/notification-hub-breakingnews-ios1.png
[3]: ./media/notification-hubs-ios-send-breaking-news/notification-hub-breakingnews-ios2.png

<!-- URLs. -->
[How To: Service Bus Notification Hubs (iOS Apps)]: https://msdn.microsoft.com/library/jj927168.aspx
[Use Notification Hubs to broadcast localized breaking news]: notification-hubs-ios-xplat-localized-apns-push-notification.md
[Mobile Service]: /develop/mobile/tutorials/get-started
[Notify users with Notification Hubs]: notification-hubs-aspnet-backend-ios-notify-users.md
[Notification Hubs Guidance]: https://msdn.microsoft.com/library/dn530749.aspx
[Notification Hubs How-To for iOS]: https://msdn.microsoft.com/library/jj927168.aspx
[get-started]: notification-hubs-ios-apple-push-notification-apns-get-started.md
[Azure portal]: https://portal.azure.com
