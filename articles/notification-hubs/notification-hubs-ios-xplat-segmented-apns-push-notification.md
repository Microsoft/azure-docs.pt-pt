---
title: Enviar notificações push para dispositivos iOS específicos usando hubs de notificação Azure Microsoft Docs
description: Neste tutorial, você aprende a usar os Hubs de Notificação Azure para enviar notificações push para dispositivos iOS específicos.
services: notification-hubs
documentationcenter: ios
author: sethmanheim
manager: femila
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: article
ms.date: 11/07/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 11/07/2019
ms.openlocfilehash: 585bdfdd7033f75e5beeba7246c8fbdd03a5e6e8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86530037"
---
# <a name="tutorial-send-push-notifications-to-specific-ios-devices-using-azure-notification-hubs"></a>Tutorial: Enviar notificações push para dispositivos específicos do iOS usando hubs de notificação Azure

[!INCLUDE [notification-hubs-selector-breaking-news](../../includes/notification-hubs-selector-breaking-news.md)]

## <a name="overview"></a>Descrição geral

Este tutorial mostra-lhe como usar os Hubs de Notificação Azure para transmitir notificações de notícias de última hora para uma aplicação iOS. Quando estiver concluído, pode inscrever-se para as categorias de notícias de última hora em que está interessado, e receber apenas notificações push para essas categorias. Este é um cenário com um padrão comum para muitas aplicações em que as notificações têm de ser enviadas para grupos de utilizadores que mostraram anteriormente interesse nas mesmas, por exemplo, leitor de RSS, aplicações para fãs de música, etc.

Os cenários de transmissão são ativados ao incluir uma ou mais *etiquetas* durante a criação de um registo no Hub de Notificação. Quando as notificações são enviadas para uma etiqueta, os dispositivos que se registaram para a etiqueta recebem a notificação. Como as etiquetas são simples cadeias, não têm de ser aprovisionadas com antecedência. Para obter mais informações sobre etiquetas, consulte [Encaminhamento de Hubs de Notificação e Expressões de Etiqueta](notification-hubs-tags-segment-push-message.md).

Neste tutorial, siga os seguintes passos:

> [!div class="checklist"]
> * Adicione uma seleção de categorias à aplicação
> * Enviar notificações marcadas
> * Enviar notificações do dispositivo
> * Executar a aplicação e gerar notificações

## <a name="prerequisites"></a>Pré-requisitos

Este tópico baseia-se na aplicação que criou no [Tutorial: Push notificações para aplicações iOS usando Hubs de Notificação Azure][get-started]. Antes de iniciar este tutorial, já deve ter concluído [Tutorial: Empurre as notificações para aplicações iOS utilizando os Hubs de Notificação Azure][get-started].

## <a name="add-category-selection-to-the-app"></a>Adicionar a seleção de categorias à aplicação

O primeiro passo é adicionar os elementos de UI ao seu storyboard existente que permita ao utilizador selecionar categorias para se registar. As categorias selecionadas por um utilizador são armazenadas no dispositivo. Quando a aplicação é iniciada, é criado um registo do dispositivo no seu hub de notificação com as categorias selecionadas como etiquetas.

1. No seu **MainStoryboard_iPhone.storyboard** adicione os seguintes componentes da biblioteca do objeto:

   * Uma etiqueta com texto "Notícias de Última Hora",
   * Rótulos com textos de categoria "Mundo", "Política", "Negócios", "Tecnologia", "Ciência", "Desporto", "Desporto",
   * Seis interruptores, um por categoria, definem cada **Estado** de comutação para estar **desligado** por predefinição.
   * Um botão marcado "Subscrever"

     O seu storyboard deve parecer o seguinte:

     ![Construtor de interface de Xcode][3]

2. No editor assistente, crie pontos de venda para todos os comutadores e chame-os de "WorldSwitch", "PoliticsSwitch", "BusinessSwitch", "TechnologySwitch", "ScienceSwitch", "SportsSwitch"

3. Crie uma Ação para o seu botão `subscribe` chamado; `ViewController.h` deve conter o seguinte código:

    ```objc
    @property (weak, nonatomic) IBOutlet UISwitch *WorldSwitch;
    @property (weak, nonatomic) IBOutlet UISwitch *PoliticsSwitch;
    @property (weak, nonatomic) IBOutlet UISwitch *BusinessSwitch;
    @property (weak, nonatomic) IBOutlet UISwitch *TechnologySwitch;
    @property (weak, nonatomic) IBOutlet UISwitch *ScienceSwitch;
    @property (weak, nonatomic) IBOutlet UISwitch *SportsSwitch;

    - (IBAction)subscribe:(id)sender;
    ```

4. Crie uma nova **Classe De Toque de Cacau** chamada `Notifications` . Copie o seguinte código na secção de interface do ficheiro Notificações.h:

    ```objc
    @property NSData* deviceToken;

    - (id)initWithConnectionString:(NSString*)listenConnectionString HubName:(NSString*)hubName;

    - (void)storeCategoriesAndSubscribeWithCategories:(NSArray*)categories
                completion:(void (^)(NSError* error))completion;

    - (NSSet*)retrieveCategories;

    - (void)subscribeWithCategories:(NSSet*)categories completion:(void (^)(NSError *))completion;
    ```

5. Adicione à Notificação a seguinte diretiva de importação:

    ```objc
    #import <WindowsAzureMessaging/WindowsAzureMessaging.h>
    ```

6. Copie o seguinte código na secção de implementação do ficheiro Notificações.m.

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

    Esta classe utiliza armazenamento local para armazenar e recuperar as categorias de notícias que este dispositivo recebe. Além disso, contém um método para se registar para estas categorias usando um registo [de modelo.](notification-hubs-templates-cross-platform-push-messages.md)

7. No `AppDelegate.h` ficheiro, adicione uma declaração de importação `Notifications.h` e adicione um imóvel para uma instância da `Notifications` classe:

    ```objc
    #import "Notifications.h"

    @property (nonatomic) Notifications* notifications;
    ```

8. No `didFinishLaunchingWithOptions` método em `AppDelegate.m` , adicione o código para inicializar a instância de notificações no início do método.  
    `HUBNAME` e `HUBLISTENACCESS` (definido em `hubinfo.h` ) já deve ter os espaços e espaços `<hub name>` `<connection string with listen access>` reservados substituídos pelo nome do seu hub de notificação e a cadeia de conexão para *DefaultListdAccessSignature* que obteve anteriormente

    ```objc
    self.notifications = [[Notifications alloc] initWithConnectionString:HUBLISTENACCESS HubName:HUBNAME];
    ```

    > [!NOTE]
    > Uma vez que, de um modo geral, as credenciais que são distribuídas com uma aplicação cliente não são seguras, só deve distribuir a chave para acesso de escuta com a sua aplicação cliente. O acesso de escuta permite que a sua aplicação seja registada para receber notificações, mas não é possível modificar os registos existentes nem enviar notificações. A chave de acesso total é utilizada num serviço de back-end protegido para o envio de notificações e a alteração de registos existentes.

9. No `didRegisterForRemoteNotificationsWithDeviceToken` método `AppDelegate.m` em , substitua o código no método pelo seguinte código para passar o token do dispositivo para a `notifications` classe. A `notifications` classe realiza o registo de notificações com as categorias. Se o utilizador alterar as seleções de categorias, ligue para o `subscribeWithCategories` método em resposta ao botão de **subscrição** para os atualizar.

    > [!NOTE]
    > Uma vez que o símbolo do dispositivo atribuído pelo Apple Push Notification Service (APNS) pode ser alterado a qualquer momento, deve registar-se para notificações frequentemente para evitar falhas de notificação. Este exemplo regista-se em notificações sempre que a aplicação é iniciada. Relativamente às aplicações executadas com frequência, ou seja, mais do que uma vez por dia, pode provavelmente ignorar o registo de modo a preservar a largura de banda caso tenha passado menos de um dia desde o registo anterior.

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

    Neste momento, não deve haver outro código no `didRegisterForRemoteNotificationsWithDeviceToken` método.

10. Os seguintes métodos já devem estar presentes `AppDelegate.m` desde a conclusão do tutorial Get start com o tutorial [de Aviso Hubs.][get-started] Se não, adicione-os.

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

    Este método trata das notificações recebidas quando a aplicação está a ser executada exibindo um simples **UIAlert**.

11. Em `ViewController.m` , adicionar uma declaração e copiar o seguinte código no método gerado pelo `import` `AppDelegate.h` `subscribe` XCode. Este código atualiza o registo de notificação para utilizar as novas etiquetas de categoria que o utilizador escolheu na interface do utilizador.

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

    Este método cria uma `NSMutableArray` de categorias e utiliza a `Notifications` classe para armazenar a lista no armazenamento local e regista as etiquetas correspondentes com o seu centro de notificação. Quando as categorias são alteradas, o registo é recriado com as novas categorias.

12. Em `ViewController.m` , adicione o seguinte código no método para definir a interface do utilizador com base nas `viewDidLoad` categorias previamente guardadas.

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

A aplicação pode agora armazenar um conjunto de categorias no dispositivo de armazenamento local utilizado para se registar no centro de notificações sempre que a aplicação começa. O utilizador pode alterar a seleção de categorias em tempo de execução e clicar no `subscribe` método para atualizar o registo do dispositivo. Em seguida, atualize a app para enviar as notificações de notícias de última hora diretamente na própria app.

## <a name="optional-send-tagged-notifications"></a>(opcional) Enviar notificações marcadas

Se não tiver acesso ao Visual Studio, pode saltar para a secção seguinte e enviar notificações da própria app. Também pode enviar a notificação de modelo adequada a partir do [portal Azure] utilizando o separador depuração para o seu centro de notificações.

[!INCLUDE [notification-hubs-send-categories-template](../../includes/notification-hubs-send-categories-template.md)]

## <a name="optional-send-notifications-from-the-device"></a>(opcional) Enviar notificações do dispositivo

Normalmente, as notificações seriam enviadas por um serviço de backend, mas, pode enviar notificações de notícias de última hora diretamente da aplicação. Para tal, atualiza o `SendNotificationRESTAPI` método que definiu no tutorial ['Iniciar's com][get-started] o tutorial de Centros de Notificação.

1. Em `ViewController.m` , atualizar o método da seguinte forma para que aceite um parâmetro para a etiqueta de categoria e `SendNotificationRESTAPI` envie a notificação adequada do [modelo.](notification-hubs-templates-cross-platform-push-messages.md)

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

2. Em `ViewController.m` , atualizar a `Send Notification` ação como mostrado no código que se segue. Para que envie as notificações usando cada tag individualmente e envie para várias plataformas.

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

3. Reconstrua o seu projeto e certifique-se de que não tem erros de construção.

## <a name="run-the-app-and-generate-notifications"></a>Executar a aplicação e gerar notificações

1. Prima o botão Executar para compilar o projeto e iniciar a aplicação. Selecione algumas opções de notícias de última hora para subscrever e, em seguida, prima o botão **Subscrever.** Deverá ver um diálogo que indique que as notificações foram subscritas.

    ![Notificação de exemplo no iOS][1]

    Quando escolhe **Subscrever,** a aplicação converte as categorias selecionadas em etiquetas e solicita um novo registo do dispositivo para as tags selecionadas a partir do centro de notificações.

2. Introduza uma mensagem a ser enviada como notícia de última hora e, em seguida, prima o botão **Enviar Notificação.** Em alternativa, execute a aplicação de consola .NET para gerar notificações.

    ![Alterar preferências de notificação no iOS][2]

3. Cada dispositivo subscrito a notícias de última hora recebe as notificações de última hora que acabou de enviar.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, enviou notificações de transmissão a dispositivos iOS específicos que se registaram para as categorias. Para aprender a empurrar notificações localizadas, avance para o seguinte tutorial:

> [!div class="nextstepaction"]
>[Push localized notifications](notification-hubs-ios-xplat-localized-apns-push-notification.md) (Enviar notificações localizadas)

<!-- Images. -->
[1]: ./media/notification-hubs-ios-send-breaking-news/notification-hub-breakingnews-subscribed.png
[2]: ./media/notification-hubs-ios-send-breaking-news/notification-hub-breakingnews-ios1.png
[3]: ./media/notification-hubs-ios-send-breaking-news/notification-hub-breakingnews-ios2.png

<!-- URLs. -->
[How To: Service Bus Notification Hubs (iOS Apps)]: /previous-versions/azure/azure-services/jj927170(v=azure.100)
[Use Notification Hubs to broadcast localized breaking news]: notification-hubs-ios-xplat-localized-apns-push-notification.md
[Mobile Service]: /develop/mobile/tutorials/get-started
[Notify users with Notification Hubs]: notification-hubs-aspnet-backend-ios-notify-users.md
[Notification Hubs Guidance]: /previous-versions/azure/azure-services/dn530749(v=azure.100)
[Notification Hubs How-To for iOS]: /previous-versions/azure/azure-services/jj927170(v=azure.100)
[get-started]: ios-sdk-get-started.md
[Portal do Azure]: https://portal.azure.com
