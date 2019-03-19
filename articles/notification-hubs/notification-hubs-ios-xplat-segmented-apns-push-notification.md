---
title: Notificações push para dispositivos de específicas do iOS com Notification Hubs do Azure | Documentos da Microsoft
description: Neste tutorial, saiba como utilizar Notification Hubs do Azure para enviar notificações push para dispositivos iOS específico.
services: notification-hubs
documentationcenter: ios
author: jwargo
manager: patniko
editor: spelluru
ms.assetid: 6ead4169-deff-4947-858c-8c6cf03cc3b2
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: article
ms.date: 01/04/2019
ms.author: jowargo
ms.openlocfilehash: dd625dba0e125ccf993af524a0ab0c0cc66555fb
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57834222"
---
# <a name="tutorial-push-notifications-to-specific-ios-devices-using-azure-notification-hubs"></a>Tutorial: Notificações push para dispositivos de específicas do iOS com Notification Hubs do Azure

[!INCLUDE [notification-hubs-selector-breaking-news](../../includes/notification-hubs-selector-breaking-news.md)]

## <a name="overview"></a>Descrição geral

Este tutorial mostra-lhe como utilizar Notification Hubs do Azure para difundir notificações de notícias de última hora para uma aplicação iOS. Quando terminar, são capazes de se registar para a quebra de categorias de notícias, que está interessado e receber notificações de push apenas para essas categorias. Este é um cenário com um padrão comum para muitas aplicações em que as notificações têm de ser enviadas para grupos de utilizadores que mostraram anteriormente interesse nas mesmas, por exemplo, leitor de RSS, aplicações para fãs de música, etc.

Os cenários de transmissão são ativados ao incluir uma ou mais *etiquetas* durante a criação de um registo no Hub de Notificação. Quando as notificações são enviadas para uma etiqueta, os dispositivos que registou para a etiqueta de recebem a notificação. Como as etiquetas são simples cadeias, não têm de ser aprovisionadas com antecedência. Para obter mais informações sobre etiquetas, consulte [Encaminhamento de Hubs de Notificação e Expressões de Etiqueta](notification-hubs-tags-segment-push-message.md).

Neste tutorial, siga os passos seguintes:

> [!div class="checklist"]
> * Adicionar uma seleção de categoria para a aplicação
> * Enviar notificações com etiquetas
> * Enviar notificações a partir do dispositivo
> * Executar a aplicação e gerar notificações

## <a name="prerequisites"></a>Pré-requisitos

Este tópico baseia-se a aplicação que criou no [Tutorial: Notificações push para aplicações iOS com Notification Hubs do Azure][get-started]. Antes de iniciar este tutorial, tem de ter já concluído [Tutorial: Notificações push para aplicações iOS com Notification Hubs do Azure][get-started].

## <a name="add-category-selection-to-the-app"></a>Adicionar a seleção de categorias à aplicação

A primeira etapa é adicionar os elementos de interface do Usuário para seu guião gráfico existente que permitem ao usuário selecionar categorias para se registar. As categorias selecionadas por um utilizador são armazenadas no dispositivo. Quando a aplicação é iniciada, é criado o registo do dispositivo no seu Hub de Notificação com as categorias selecionadas como etiquetas.

1. No seu **MainStoryboard_iPhone.storyboard** adicione os seguintes componentes da biblioteca de objeto:

   * Uma etiqueta com texto de "Notícias de última hora",
   * Etiquetas com textos de categoria "World", "Política", "Empresa", "Tecnologia", "Ciência", "Desporto",
   * Seis comutadores, um por categoria, defina cada interruptor **estado** ser **desativar** por predefinição.
   * Um botão rotulado "Subscribe"

     Seu guião gráfico deve ter o seguinte aspeto:

     ![Construtor de interface do Xcode][3]

2. No editor do assistente, criar de comunicação para todos os comutadores e chamá-los "WorldSwitch", "PoliticsSwitch", "BusinessSwitch", "TechnologySwitch", "ScienceSwitch", "SportsSwitch"
3. Criar uma ação para seu botão chamado `subscribe`; seu `ViewController.h` deve conter o seguinte código:

    ```objc
    @property (weak, nonatomic) IBOutlet UISwitch *WorldSwitch;
    @property (weak, nonatomic) IBOutlet UISwitch *PoliticsSwitch;
    @property (weak, nonatomic) IBOutlet UISwitch *BusinessSwitch;
    @property (weak, nonatomic) IBOutlet UISwitch *TechnologySwitch;
    @property (weak, nonatomic) IBOutlet UISwitch *ScienceSwitch;
    @property (weak, nonatomic) IBOutlet UISwitch *SportsSwitch;

    - (IBAction)subscribe:(id)sender;
    ```

4. Criar uma nova **a classe de Touch Cocoa** chamado `Notifications`. Copie o código a seguir na seção de interface do arquivo Notifications.h:

    ```objc
    @property NSData* deviceToken;

    - (id)initWithConnectionString:(NSString*)listenConnectionString HubName:(NSString*)hubName;

    - (void)storeCategoriesAndSubscribeWithCategories:(NSArray*)categories
                completion:(void (^)(NSError* error))completion;

    - (NSSet*)retrieveCategories;

    - (void)subscribeWithCategories:(NSSet*)categories completion:(void (^)(NSError *))completion;
    ```

5. Adicione a seguinte diretiva de importação para Notifications.m:

    ```objc
    #import <WindowsAzureMessaging/WindowsAzureMessaging.h>
    ```

6. Copie o seguinte código na secção de implementação do arquivo Notifications.m.

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

    Esta classe utiliza o armazenamento local para armazenar e obter as categorias de notícias que recebe este dispositivo. Além disso, contém um método para se registrar para essas categorias com uma [modelo](notification-hubs-templates-cross-platform-push-messages.md) registo.

7. Na `AppDelegate.h` de ficheiros, adicione uma instrução de importação para `Notifications.h` e adicione uma propriedade para uma instância do `Notifications` classe:

    ```objc
    #import "Notifications.h"

    @property (nonatomic) Notifications* notifications;
    ```

8. Na `didFinishLaunchingWithOptions` método na `AppDelegate.m`, adicione o código para inicializar a instância de notificações no início do método.  
    `HUBNAME` e `HUBLISTENACCESS` (definidos na `hubinfo.h`) já deverá ter o `<hub name>` e `<connection string with listen access>` marcadores de posição substituído pelo nome do seu hub de notificação e a cadeia de ligação para *DefaultListenSharedAccessSignature*que obteve anteriormente

    ```objc
    self.notifications = [[Notifications alloc] initWithConnectionString:HUBLISTENACCESS HubName:HUBNAME];
    ```

    > [!NOTE]
    > Uma vez que, de um modo geral, as credenciais que são distribuídas com uma aplicação cliente não são seguras, só deve distribuir a chave para acesso de escuta com a sua aplicação cliente. O acesso de escuta permite que a sua aplicação seja registada para receber notificações, mas não é possível modificar os registos existentes nem enviar notificações. A chave de acesso total é utilizada num serviço back-end protegido para enviar notificações e alterar registos existentes.

9. Na `didRegisterForRemoteNotificationsWithDeviceToken` método na `AppDelegate.m`, substitua o código no método com o seguinte código para transmitir o token de dispositivo para o `notifications` classe. O `notifications` classe executa o Registro para notificações com as categorias. Se o utilizador altera as seleções de categorias, chamar o `subscribeWithCategories` método em resposta ao **subscrever** botão para atualizá-los.

    > [!NOTE]
    > Porque o token do dispositivo atribuído pelo Apple Push Notification Service (APNS) pode chance em qualquer altura, deve se registrar para notificações com frequência evitar falhas de notificação. Este exemplo regista-se em notificações sempre que a aplicação é iniciada. Relativamente às aplicações executadas com frequência, ou seja, mais do que uma vez por dia, pode provavelmente ignorar o registo para poupar a largura de banda, caso tenha passado menos de um dia desde o registo anterior.

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

    Neste momento, não deve haver nenhum outro código no `didRegisterForRemoteNotificationsWithDeviceToken` método.

10. Os seguintes métodos já devem estar presentes no `AppDelegate.m` a conclusão da [introdução aos Hubs de notificação] [ get-started] tutorial. Caso contrário, adicioná-los.

    ```objc
    -(void)MessageBox:(NSString *)title message:(NSString *)messageText
    {

        UIAlertView *alert = [[UIAlertView alloc] initWithTitle:title message:messageText delegate:self
            cancelButtonTitle:@"OK" otherButtonTitles: nil];
        [alert show];
    }

    * (void)application:(UIApplication *)application didReceiveRemoteNotification:
       (NSDictionary *)userInfo {
       NSLog(@"%@", userInfo);
       [self MessageBox:@"Notification" message:[[userInfo objectForKey:@"aps"] valueForKey:@"alert"]];
     }
    ```

    Este método manipula notificações recebidas quando a aplicação está em execução ao apresentar um simples **UIAlert**.

11. Na `ViewController.m`, adicione um `import` instrução para `AppDelegate.h` e copie o seguinte código para gerados pelo XCode `subscribe` método. Esse código atualiza o registo de notificação para utilizar as novas marcas de categoria, que o usuário tiver escolhido na interface do usuário.

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
            [(AppDelegate*)[[UIApplication sharedApplication]delegate] MessageBox:@"Notification" message:@"Subscribed!"];
        } else {
            NSLog(@"Error subscribing: %@", error);
        }
    }];
    ```

    Este método cria um `NSMutableArray` de categorias e utiliza o `Notifications` classe para armazenar a lista no armazenamento local e registros correspondentes de etiquetas com o seu hub de notificação. Quando as categorias são alteradas, o registo é recriado com as novas categorias.

12. Na `ViewController.m`, adicione o seguinte código no `viewDidLoad` método para definir a interface do usuário com base nas categorias a guardado anteriormente.

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

A aplicação agora pode armazenar um conjunto de categorias no armazenamento local do dispositivo utilizado para registar com o notification hub sempre que a aplicação for iniciada. O utilizador pode alterar a seleção de categorias no tempo de execução e clique no `subscribe` método para atualizar o registo para o dispositivo. Em seguida, atualizar a aplicação para enviar as notificações de notícias de última hora diretamente na própria aplicação.

## <a name="optional-send-tagged-notifications"></a>(opcional) Enviar notificações marcadas

Se não tiver acesso ao Visual Studio, pode avançar para a secção seguinte e enviar notificações a partir da aplicação em si. Também pode enviar a notificação de modelo adequado dos [portal do Azure] utilizando o separador de depuração para o seu hub de notificação.

[!INCLUDE [notification-hubs-send-categories-template](../../includes/notification-hubs-send-categories-template.md)]

## <a name="optional-send-notifications-from-the-device"></a>(opcional) Enviar notificações a partir do dispositivo

Normalmente notificações seriam enviadas por um serviço de back-end, mas pode enviar notificações de notícias de última hora diretamente a partir da aplicação. Para tal, atualizar o `SendNotificationRESTAPI` método que definiu no [introdução aos Hubs de notificação] [ get-started] tutorial.

1. Na `ViewController.m`, atualize o `SendNotificationRESTAPI` método como se segue para que ele aceita um parâmetro para a etiqueta de categoria e envia o elemento adequado [modelo](notification-hubs-templates-cross-platform-push-messages.md) notificação.

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

2. Na `ViewController.m`, atualize o `Send Notification` ação, conforme mostrado no código a seguir. Para que ele envia notificações usando cada marca individualmente e envia a várias plataformas.

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

3. Recrie seu projeto e certifique-se de que não existem erros de compilação.

## <a name="run-the-app-and-generate-notifications"></a>Executar a aplicação e gerar notificações

1. Prima o botão de execução para compilar o projeto e iniciar a aplicação. Selecione algumas opções de notícias de última hora para subscrever e, em seguida, prima a **Subscribe** botão. Deverá ver uma caixa de diálogo que indica que as notificações foram subscritos.

    ![Notificação de exemplo no iOS][1]

    Quando escolhe **Subscribe**, a aplicação converte categorias selecionadas em marcas e solicita um novo registo de dispositivos para as etiquetas selecionadas do hub de notificação.

2. Introduza uma mensagem a ser enviados como notícias de última hora, em seguida, prima a **Enviar notificação** botão. Em alternativa, execute a aplicação de consola .NET gerar notificações.

    ![Preferências de notificações de alteração no iOS][2]

3. Cada dispositivo inscrito notícias de última hora recebe as notificações de notícias de última hora que acabou de enviar.

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, enviou notificações de difusão para dispositivos de específicas do iOS que registou para as categorias. Para saber como enviar notificações localizadas, avance para o tutorial seguinte:

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
[Portal do Azure]: https://portal.azure.com
