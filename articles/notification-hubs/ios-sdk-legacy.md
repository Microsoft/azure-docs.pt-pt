---
title: Envie notificações push para iOS usando Azure Notification Hubs e a versão iOS SDK 2.0.4
description: Neste tutorial, aprende a utilizar os Hubs de Notificação Azure e o serviço de Notificação Apple Push para enviar notificações push para dispositivos iOS (versão 2.0.4).
author: sethmanheim
ms.author: sethm
ms.date: 06/19/2020
ms.topic: tutorial
ms.service: notification-hubs
ms.reviewer: thsomasu
ms.lastreviewed: 06/01/2020
ms.openlocfilehash: a1017f7c7aa0ede9e3c91acd3dba510618e15fb6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "100628473"
---
# <a name="tutorial-send-push-notifications-to-ios-apps-using-azure-notification-hubs-legacy-api"></a>Tutorial: Enviar notificações push para aplicações iOS usando Hubs de Notificação Azure (Legacy API)

Este tutorial mostra-lhe como usar os Hubs de Notificação Azure para enviar notificações push para uma aplicação iOS, utilizando o Azure Notification Hubs SDK para APIs Apple Legacy.

Este tutorial contém os seguintes passos:

- Crie uma aplicação de amostra iOS.
- Ligue a sua aplicação iOS aos Hubs de Notificação Azure.
- Envie notificações push de teste.
- Verifique se a sua aplicação recebe notificações.

Você pode baixar o código completo para este tutorial [a partir de GitHub](https://github.com/Azure/azure-notificationhubs-ios/tree/main/SampleNHAppLegacyObjC).

## <a name="prerequisites"></a>Pré-requisitos

Para completar este tutorial, você precisará dos seguintes pré-requisitos:

- Um Mac que executa [o Xcode,](https://go.microsoft.com/fwLink/p/?LinkID=266532)juntamente com um certificado de desenvolvimento válido instalado no seu Keychain.
- Um iPhone ou iPad a correr a versão 10 ou posterior do iOS.
- O seu dispositivo físico está registado no Portal da [Apple](https://developer.apple.com/)e associado ao seu certificado.

Antes de prosseguir, certifique-se de passar pelo tutorial anterior sobre como começar com os [Hubs de Notificação Azure para aplicações iOS](ios-sdk-get-started.md), para configurar e configurar credenciais push no seu centro de notificações. Mesmo que não tenha experiência prévia com o desenvolvimento do iOS, deverá ser capaz de seguir estes passos.

> [!NOTE]
> Devido aos requisitos de configuração para notificações push, deve implementar e testar notificações push num dispositivo físico iOS (iPhone ou iPad), em vez do emulador iOS.

## <a name="connect-your-ios-app-to-notification-hubs"></a>Ligar a aplicação iOS aos Notification Hubs

1. No Xcode, crie um novo projeto do iOS e selecione o modelo **Aplicação de Vista Única**.

   :::image type="content" source="media/ios-sdk/image1.png" alt-text="Selecione o modelo":::

2. Ao definir as opções para o seu novo projeto, não se esqueça de utilizar o mesmo **Nome do Produto** e **Identificador de Organização** que utilizou quando definiu o identificador de pacote no portal de Programador da Apple.

3. No Âmbito do Project Navigator, selecione o nome do seu projeto em **Targets** e, em seguida, selecione o **separador 'Assinatura & Capabilities'.** Certifique-se de que seleciona a **equipa** adequada para a sua conta Apple Developer. XCode deve obter automaticamente o Perfil de Aprovisionamento que criou anteriormente com base no identificador de pacote.

   Se não vir o novo perfil de aprovisionamento que criou no Xcode, tente atualizar os perfis para a sua identidade de assinatura. Clique em **Xcode** na barra de menus, clique em **Preferências**, clique em **Conta** clique no botão **Ver Detalhes**, clique na sua identidade de assinatura e clique no botão atualizar no canto inferior direito.

   :::image type="content" source="media/ios-sdk/image2.png" alt-text="Ver detalhes":::

4. No **separador 'Signing & Capabilities',** selecione **+ Capacidade**. Clique **duas vezes** em Notificações push para o ativar.

   :::image type="content" source="media/ios-sdk/image3.png" alt-text="Capacidade":::

5. Adicione os módulos SDK dos Hubs de Notificação Azure.

   Pode integrar o Azure Notification Hubs SDK na sua aplicação utilizando [cacau ou](https://cocoapods.org/) adicionando manualmente os binários ao seu projeto.

   - Integração via Cacau: Adicione as seguintes dependências ao seu ficheiro de podfile para incluir os Hubs de Notificação Azure SDK na sua aplicação:

      ```ruby
      pod 'AzureNotificationHubs-iOS'
      ```

      - Executar a instalação do casulo para instalar o seu casulo recém-definido e abrir o seu espaço .xcwork.

         Se vir um erro como **Não conseguir encontrar uma especificação para AzureNotificationHubs-iOS** durante a instalação do pod, corra para obter as `pod repo update` cápsulas mais recentes do repositório de Cacau e, em seguida, executar a instalação do pod.

   - Integração via Cartago: Adicione as seguintes dependências ao seu Cartfile para incluir o Azure Notification Hubs SDK na sua aplicação:

      ```ruby
      github "Azure/azure-notificationhubs-ios"
      ```

      - Em seguida, atualizar as dependências de construção:

      ```shell
      $ carthage update
      ```

      Para obter mais informações sobre a utilização de Cartago, consulte o [repositório de Cartago GitHub.](https://github.com/Carthage/Carthage)

   - Integração copiando os binários no seu projeto: Pode integrar-se copiando os binários no seu projeto, da seguinte forma:

        - Descarregue a estrutura [SDK dos Hubs de Notificação Azure](https://github.com/Azure/azure-notificationhubs-android/releases) fornecida como um ficheiro zip e desaperte-o.

        - No Xcode, clique com o botão direito do rato no projeto e clique na opção **Adicionar Ficheiros a** para adicionar a pasta **WindowsAzureMessaging.framework** ao seu projeto Xcode. Selecione **Opções** e certifique-se de que **Copiar itens, se necessário** está selecionado e, em seguida, clique em **Adicionar**.

          :::image type="content" source="media/ios-sdk/image4.png" alt-text="Adicionar enquadramento":::

6. Adicione as informações para a ligação aos Centros de Notificação Azure na `<string></string>` secção apropriada.  Substitua os espaços reservados literais de cadeia `--HUB-NAME--` e o nome do hub e o `--CONNECTION-STRING--` **DefaultListdAccessssignature,** respectivamente, tal como obteve anteriormente a partir do portal:

    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
    <plist version="1.0">
    <dict>
        <key>HUB_NAME</key>
        <string>--HUB-NAME--</string>
        <key>CONNECTION_STRING</key>
        <string>--CONNECTION-STRING--</string>
    </dict>
    </plist>
    ```

7. Abra o seu ficheiro **AppDeegate.h** do seu projeto e substitua o seu conteúdo pelo seguinte código:

    ```objc
    #import <UIKit/UIKit.h>
    #import <WindowsAzureMessaging/WindowsAzureMessaging.h>
    #import <UserNotifications/UserNotifications.h> 

    @interface AppDelegate : UIResponder <UIApplicationDelegate,   UNUserNotificationCenterDelegate>

    @property (strong, nonatomic) UIWindow *window;

    - (void)handleRegister;
    - (void)handleUnregister;

    @end
    ```

8. No ficheiro **appDeegate .m** projeto, adicione as `import` seguintes declarações:

    ```objc
    #import "NotificationDetailViewController.h"
    ```

12. Também no seu ficheiro **appDeegate.m,** adicione a seguinte linha de código no `didFinishLaunchingWithOptions` método, com base na sua versão do iOS. Este código regista o seu identificador de dispositivo com APNs:

    ```objc
    [[UNUserNotificationCenter currentNotificationCenter] setDelegate:self];
    ```

13. No mesmo ficheiro **appDeegate.m,** substitua todo o código `didFinishLaunchingWithOptions` posteriormente pelo seguinte código:

    ```objc
    // Tells the app that a remote notification arrived that indicates there is data to be fetched.

    - (void)application:(UIApplication *)application didReceiveRemoteNotification:(NSDictionary *)userInfo fetchCompletionHandler:(void (^)(UIBackgroundFetchResult result))completionHandler {
        NSLog(@"Received remote (silent) notification");
        [self logNotificationDetails:userInfo];
        
        //
        // Let the system know the silent notification has been processed.
        //
        completionHandler(UIBackgroundFetchResultNoData);
    }

    // Tells the delegate that the app successfully registered with Apple Push Notification service (APNs).

    - (void) application:(UIApplication *)application didRegisterForRemoteNotificationsWithDeviceToken:(NSData *)deviceToken {
        NSMutableSet *tags = [[NSMutableSet alloc] init];
        
        // Load and parse stored tags
        NSString *unparsedTags = [[NSUserDefaults standardUserDefaults] valueForKey:NHUserDefaultTags];
        if (unparsedTags.length > 0) {
            NSArray *tagsArray = [unparsedTags componentsSeparatedByString: @","];
            [tags addObjectsFromArray:tagsArray];
        }
        
        // Register the device with the Notification Hub.
        // If the device has not already been registered, this will create the registration.
        // If the device has already been registered, this will update the existing registration.
        //
        SBNotificationHub* hub = [self getNotificationHub];
        [hub registerNativeWithDeviceToken:deviceToken tags:tags completion:^(NSError* error) {
            if (error != nil) {
                NSLog(@"Error registering for notifications: %@", error);
            } else {
                [self showAlert:@"Registered" withTitle:@"Registration Status"];
            }
        }];
    }

    // UNUserNotificationCenterDelegate methods
    //
    // Asks the delegate how to handle a notification that arrived while the app was running in the  foreground.

    - (void)userNotificationCenter:(UNUserNotificationCenter *)center willPresentNotification:(UNNotification *)notification withCompletionHandler:(void (^)(UNNotificationPresentationOptions options))completionHandler {
        NSLog(@"Received notification while the application is in the foreground");
        
        // The system calls this delegate method when the app is in the foreground. This allows the app to handle the notification
        // itself (and potentially modify the default system behavior).
        
        // Handle the notification by displaying custom UI.
        //
        [self showNotification:notification.request.content.userInfo];
        
        // Use 'options' to specify which default behaviors to enable.
        // - UNNotificationPresentationOptionsBadge: Apply the notification's badge value to the app’s icon.
        // - UNNotificationPresentationOptionList: Show in the Notification Center
        // - UNNotificationPresentationOptionsSound: Play the sound associated with the notification.
        //
        completionHandler(UNNotificationPresentationOptionsBadge | UNNotificationPresentationOptionsSound);
    }

    // Asks the delegate to process the user's response to a delivered notification.
    //

    - (void)userNotificationCenter:(UNUserNotificationCenter *)center didReceiveNotificationResponse:(UNNotificationResponse *)response withCompletionHandler:(void(^)(void))completionHandler {
        NSLog(@"Received notification while the application is in the background");
        
        // The system calls this delegate method when the user taps or responds to the system notification.
        
        // Handle the notification response by displaying custom UI
        //
        [self showNotification:response.notification.request.content.userInfo];
        
        // Let the system know the response has been processed.
        //
        completionHandler();
    }

    // App logic and helpers

    - (SBNotificationHub *)getNotificationHub {
        NSString *path = [[NSBundle mainBundle] pathForResource:@"DevSettings" ofType:@"plist"];
        NSDictionary *configValues = [NSDictionary dictionaryWithContentsOfFile:path];
        
        NSString *connectionString = [configValues objectForKey:@"CONNECTION_STRING"];
        NSString *hubName = [configValues objectForKey:@"HUB_NAME"];
        
        return [[SBNotificationHub alloc] initWithConnectionString:connectionString notificationHubPath:hubName];
    }

    - (void)handleRegister {
        UNUserNotificationCenter *center = [UNUserNotificationCenter currentNotificationCenter];
        
        UNAuthorizationOptions options =  UNAuthorizationOptionAlert | UNAuthorizationOptionSound | UNAuthorizationOptionBadge;
        [center requestAuthorizationWithOptions:(options) completionHandler:^(BOOL granted, NSError * _Nullable error) {
            if (error != nil) {
                NSLog(@"Error requesting for authorization: %@", error);
            }

            if (granted) {
                NSLog(@"Authorization granted");
            }
        }];
        [[UIApplication sharedApplication] registerForRemoteNotifications];
    }

    - (void)handleUnregister {
        //
        // Unregister the device with the Notification Hub.
        //
        SBNotificationHub *hub = [self getNotificationHub];
        [hub unregisterNativeWithCompletion:^(NSError* error) {
            if (error != nil) {
                NSLog(@"Error unregistering for push: %@", error);
            } else {
                [self showAlert:@"Unregistered" withTitle:@"Registration Status"];
            }
        }];
    }

    - (void)logNotificationDetails:(NSDictionary *)userInfo {
        if (userInfo != nil) {
            UIApplicationState state = [UIApplication sharedApplication].applicationState;
            BOOL background = state != UIApplicationStateActive;
            NSLog(@"Received %@notification: \n%@", background ? @"(background) " : @"", userInfo);
        }
    }

    - (void)showAlert:(NSString *)message withTitle:(NSString *)title {
        if (title == nil) {
            title = @"Alert";
        }

        UIAlertController *alert = [UIAlertController alertControllerWithTitle:title message:message preferredStyle:UIAlertControllerStyleAlert];
        [alert addAction:[UIAlertAction actionWithTitle:@"OK" style:UIAlertActionStyleDefault handler:nil]];
        [[[[UIApplication sharedApplication] keyWindow] rootViewController] presentViewController:alert animated:YES completion:nil];
    }

    - (void)showNotification:(NSDictionary *)userInfo {
        [self logNotificationDetails:userInfo];
        
        NotificationDetailViewController *notificationDetail = [[NotificationDetailViewController alloc] initWithUserInfo:userInfo];
        [[[[UIApplication sharedApplication] keyWindow] rootViewController] presentViewController:notificationDetail animated:YES completion:nil];
    }

    @end
    ```

    Este código liga-se ao centro de notificação utilizando as informações de ligação especificadas em **Constants.h**. Em seguida, dá o token do dispositivo ao centro de notificação, para que o hub possa enviar notificações.

### <a name="create-notificationdetailviewcontroller-header-file"></a>Criar ficheiro de cabeçalho DotailViewController de notificação

1. Semelhante às instruções anteriores, adicione outro ficheiro de cabeçalho chamado **NotificationDetailViewController.h**. Substitua o conteúdo do novo ficheiro do cabeçalho pelo seguinte código:

   ```objc
   #import <UIKit/UIKit.h>

   NS_ASSUME_NONNULL_BEGIN

   @interface NotificationDetailViewController : UIViewController

   @property (strong, nonatomic) IBOutlet UILabel *titleLabel;
   @property (strong, nonatomic) IBOutlet UILabel *bodyLabel;
   @property (strong, nonatomic) IBOutlet UIButton *dismissButton;

   @property (strong, nonatomic) NSDictionary *userInfo;

   - (id)initWithUserInfo:(NSDictionary *)userInfo;

   @end

   NS_ASSUME_NONNULL_END
   ```

2. Adicione o ficheiro de implementação **NotificationDetailViewController.m**. Substitua o conteúdo do ficheiro pelo seguinte código, que implementa os métodos UIViewController:

   ```objc
   #import "NotificationDetailViewController.h"

   @interface NotificationDetailViewController ()

   @end

   @implementation NotificationDetailViewController

   - (id)initWithUserInfo:(NSDictionary *)userInfo {
        self = [super initWithNibName:@"NotificationDetail" bundle:nil];
        if (self) {
            _userInfo = userInfo;
        }
        return self;
   }

   - (void)viewDidLayoutSubviews {
        [self.titleLabel sizeToFit];
        [self.bodyLabel sizeToFit];
   }

   - (void)viewDidLoad {
        [super viewDidLoad];
        
        NSString *title = nil;
        NSString *body = nil;
        
        NSDictionary *aps = [_userInfo valueForKey:@"aps"];
        NSObject *alertObject = [aps valueForKey:@"alert"];
        if (alertObject != nil) {
            if ([alertObject isKindOfClass:[NSDictionary class]]) {
                NSDictionary *alertDict = (NSDictionary *)alertObject;
                title = [alertDict valueForKey:@"title"];
                body = [alertObject valueForKey:@"body"];
            } else if ([alertObject isKindOfClass:[NSString class]]) {
                body = (NSString *)alertObject;
            } else {
                NSLog(@"Unable to parse notification content. Unexpected format: %@", alertObject);
            }
        }
        
        if (title == nil) {
            title = @"<unset>";
        }
        
        if (body == nil) {
            body = @"<unset>";
        }
        
        self.titleLabel.text = title;
        self.bodyLabel.text = body;
   }

   - (IBAction)handleDismiss:(id)sender {
        [self dismissViewControllerAnimated:YES completion:nil];
   }

   @end
   ```

### <a name="viewcontroller"></a>Vercontrolador

1. No ficheiro **ViewController.h** do projeto, adicione as `import` seguintes declarações:

   ```objc
   #import <WindowsAzureMessaging/WindowsAzureMessaging.h>
   #import <UserNotifications/UserNotifications.h>
   ```

2. Também em **ViewController.h,** adicione as seguintes declarações de propriedade após a `@interface` declaração:

   ```objc
   @property (strong, nonatomic) IBOutlet UITextField *tagsTextField;
   @property (strong, nonatomic) IBOutlet UIButton *registerButton;
   @property (strong, nonatomic) IBOutlet UIButton *unregisterButton;
   ```

3. No ficheiro de implementação **ViewController.m** do projeto, substitua o conteúdo do ficheiro pelo seguinte código:

   ```objc
   #import "ViewController.h"
   #import "AppDelegate.h"

    static NSString *const kNHUserDefaultTags = @"notification_tags";

   @interface ViewController ()

   @end

   @implementation ViewController

   // UIViewController methods

   - (void)touchesBegan:(NSSet<UITouch *> *)touches withEvent:(UIEvent *)event {
        // Simple method to dismiss keyboard when user taps outside of the UITextField.
        [self.view endEditing:YES];
   }

   - (void)viewDidLoad {
        [super viewDidLoad];
        
        // Load raw tags text from storage and initialize the text field
        self.tagsTextField.text = [[NSUserDefaults standardUserDefaults] valueForKey:kNHUserDefaultTags];
   }

   - (IBAction)handleRegister:(id)sender {
        // Save raw tags text in storage
        [[NSUserDefaults standardUserDefaults] setValue:self.tagsTextField.text forKey:kNHUserDefaultTags];
        
        // Delegate processing the register action to the app delegate.
        [[[UIApplication sharedApplication] delegate] performSelector:@selector(handleRegister)];
   }

   - (IBAction)handleUnregister:(id)sender {
        [[[UIApplication sharedApplication] delegate] performSelector:@selector(handleUnregister)];
   }

   @end
   ```

4. Para confirmar que não existem falhas, crie e execute a aplicação no dispositivo.

## <a name="send-test-push-notifications"></a>Enviar notificações push de teste

Pode testar a receção das notificações na aplicação com a opção **Envio de Teste** no [portal do Azure](https://portal.azure.com/). Esta ação envia uma notificação push de teste para o dispositivo.

:::image type="content" source="media/ios-sdk/image6.png" alt-text="Enviar teste":::

Normalmente, as notificações push são enviadas num serviço de back-end como Mobile Apps ou ASP.NET com uma biblioteca compatível. Se uma biblioteca não estiver disponível para a sua parte de trás, também pode utilizar a API REST diretamente para enviar mensagens de notificação.

Aqui está uma lista de outros tutoriais que você pode querer rever para o envio de notificações:

- Aplicativos Azure Mobile: Para um exemplo de como enviar notificações de uma aplicação móvel de volta integrada com Os Hubs de Notificação, consulte [Adicionar Notificações push à sua aplicação iOS](/previous-versions/azure/app-service-mobile/app-service-mobile-ios-get-started-push).
- ASP.NET: Utilize [os Centros de Notificação para enviar notificações push aos utilizadores](notification-hubs-aspnet-backend-ios-apple-apns-notification.md).
- SDK Java dos Hubs de Notificação do Azure: veja [Como utilizar Hubs de Notificação de Java](notification-hubs-java-push-notification-tutorial.md) para enviar notificações de Java. Este processo foi testado em Eclipse para Programação Android.
- PHP: [Como utilizar os Hubs de Notificação de PHP](notification-hubs-php-push-notification-tutorial.md).

## <a name="verify-that-your-app-receives-push-notifications"></a>Verificar que a aplicação recebe notificações push

Para testar as notificações push no iOS, tem de implementar a aplicação para um dispositivo iOS físico. Não é possível enviar notificações push da Apple utilizando o simulador iOS.

1. Execute a aplicação, verifique se o registo é concluído com êxito e prima **OK**.

   :::image type="content" source="media/ios-sdk/image7.png" alt-text="Registar":::

2. Em seguida, envie uma notificação de push de teste a partir do [portal Azure](https://portal.azure.com/), conforme descrito na secção anterior.

3. A notificação push é enviada para todos os dispositivos que estão registados para receber as notificações do centro de notificação dado.

   :::image type="content" source="media/ios-sdk/image8.png" alt-text="Enviar teste":::

## <a name="next-steps"></a>Passos seguintes

Neste exemplo simples, transmite notificações push para todos os seus dispositivos iOS registados. Para saber como enviar notificações push para dispositivos iOS específicos, avance para o seguinte tutorial:

[Tutorial: Notificações push para dispositivos específicos](notification-hubs-ios-xplat-segmented-apns-push-notification.md)

Para obter mais informações, veja os seguintes artigos:

- [Visão geral dos Hubs de Notificação do Azure](notification-hubs-push-notification-overview.md)
- [Centros de Notificação REST APIs](/rest/api/notificationhubs/)
- [Hubs de Notificação SDK para operações de back-end](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)
- [Hubs de Notificação SDK no GitHub](https://github.com/Azure/azure-notificationhubs)
- [Registe-se com a parte de trás da aplicação](notification-hubs-ios-aspnet-register-user-from-backend-to-push-notification.md)
- [Gestão de registos](notification-hubs-push-notification-registration-management.md)
- [Trabalhar com etiquetas](notification-hubs-tags-segment-push-message.md)
- [Trabalhando com modelos personalizados](notification-hubs-templates-cross-platform-push-messages.md)
- [Controlo de acesso de ônibus de serviço com assinaturas de acesso compartilhado](../service-bus-messaging/service-bus-sas.md)
- [Programáticamente gerar fichas SAS](/rest/api/eventhub/generate-sas-token)
- [Segurança da Apple: cripto comum](https://developer.apple.com/security/)
- [Tempo de época da UNIX](https://en.wikipedia.org/wiki/Unix_time)
- [HMAC](https://en.wikipedia.org/wiki/HMAC)