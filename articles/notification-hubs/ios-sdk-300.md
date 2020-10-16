---
title: Envie notificações push para iOS usando Azure Notification Hubs e a versão iOS SDK 3.0.0 pré-visualização 1
description: Neste tutorial, aprende a utilizar os Hubs de Notificação Azure e o serviço de Notificação Apple Push para enviar notificações push para dispositivos iOS (versão 3.0.0-preview1).
author: sethmanheim
ms.author: sethm
ms.date: 06/19/2020
ms.topic: tutorial
ms.service: notification-hubs
ms.reviewer: thsomasu
ms.lastreviewed: 06/01/2020
ms.openlocfilehash: 25f18eb0f55560b7abd250b8511b2e250ea55852
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91250441"
---
# <a name="tutorial-send-push-notifications-to-ios-apps-using-azure-notification-hubs-version-300-preview1"></a>Tutorial: Enviar notificações push para aplicações iOS usando Hubs de Notificação Azure (versão 3.0.0-preview1)

Este tutorial mostra-lhe como utilizar os Hubs de Notificação Azure para enviar notificações push para uma aplicação iOS, utilizando a versão 2.0.4 dos Hubs de Notificação Azure.

Este tutorial contém os seguintes passos:

- Crie uma aplicação de amostra iOS.
- Ligue a sua aplicação iOS aos Hubs de Notificação Azure.
- Envie notificações push de teste.
- Verifique se a sua aplicação recebe notificações.

Você pode baixar o código completo para este tutorial [a partir de GitHub](https://github.com/Azure/azure-notificationhubs-ios/tree/v3-preview2/Samples).

## <a name="prerequisites"></a>Pré-requisitos

Para completar este tutorial, você precisará dos seguintes pré-requisitos:

- Um Mac que executa [o Xcode,](https://go.microsoft.com/fwLink/p/?LinkID=266532)juntamente com um certificado de desenvolvimento válido instalado no seu Keychain.
- Um iPhone ou iPad a correr a versão 10 ou posterior do iOS.
- O seu dispositivo físico está registado no Portal da [Apple](https://developer.apple.com/)e associado ao seu certificado.

Antes de prosseguir, certifique-se de passar pelo tutorial anterior sobre como começar com os [Hubs de Notificação Azure para aplicações iOS](ios-sdk-get-started.md), para configurar e configurar credenciais push no seu centro de notificações. Mesmo que não tenha experiência prévia com o desenvolvimento do iOS, deverá ser capaz de seguir estes passos.

> [!NOTE]
> Devido aos requisitos de configuração para notificações push, deve implementar e testar notificações push num dispositivo físico iOS (iPhone ou iPad), em vez do emulador iOS.

## <a name="connect-your-ios-app-to-notification-hubs"></a>Ligar a aplicação iOS aos Notification Hubs

1. No Xcode, crie um novo projeto iOS e selecione o modelo **de Aplicação de Visualização Única.**  

   :::image type="content" source="media/ios-sdk/image1.png" alt-text="Selecione o modelo":::

2. Ao definir as opções para o seu novo projeto, certifique-se de que utiliza o mesmo **nome de produto**e    **identificador de organização**que utilizou quando   definiu o identificador de pacotes no portal Apple Developer.

3. No Âmbito do Project Navigator, selecione o nome do seu projeto em **Targets**e, em seguida, selecione o **separador 'Assinatura & Capabilities'.**   Certifique-se de que seleciona a **equipa**adequada   para a sua conta Apple Developer. XCode deve obter automaticamente o Perfil de Aprovisionamento que criou anteriormente com base no identificador de pacote.

   Se não vir o novo perfil de aprovisionamento que criou no Xcode, tente atualizar os perfis para a sua identidade de assinatura. Clique em **Xcode**   na barra de menu, clique em **Preferências,** clique no separador **'Ver',**   clique no botão Ver **Detalhes,** clique na sua identidade de assinatura   e, em seguida, clique no botão de atualização no canto inferior direito.

   :::image type="content" source="media/ios-sdk/image2.png" alt-text="Selecione o modelo":::

4. No **separador 'Signing & Capabilities',**   selecione **+ Capacidade**. Clique **duas vezes**em Notificações push   para o ativar.

   :::image type="content" source="media/ios-sdk/image3.png" alt-text="Selecione o modelo":::

5. Adicione os módulos SDK dos Hubs de Notificação Azure.

   Pode integrar o Azure Notification Hubs SDK na sua aplicação utilizando [cacau ou](https://cocoapods.org/)   adicionando manualmente os binários ao seu projeto.

   - Integração via Cacau: Adicione as seguintes dependências ao seu ficheiro de podfile para incluir os Hubs de Notificação Azure SDK na sua aplicação:

      ```ruby
      pod 'AzureNotificationHubs-iOS'
      ```

      - Executar a instalação do casulo para instalar o seu casulo recém-definido e abrir o seu espaço .xcwork.

         Se vir um erro como **Não conseguir encontrar uma especificação para AzureNotificationHubs-iOS** durante a instalação do pod, corra para obter as `pod repo update` cápsulas mais recentes do repositório de Cacau e, em seguida, executar a instalação do pod.

   - Integração copiando os binários no seu projeto:

      Pode integrar-se copiando os binários no seu projeto, da seguinte forma:

        - Descarregue a estrutura [SDK dos Hubs de Notificação Azure](https://github.com/Azure/azure-notificationhubs-iOS/releases/)   fornecida como um ficheiro zip e desaperte-o.

        - No Xcode, clique com o botão direito do seu projeto e clique no **Add Files para**a   opção de adicionar a pasta quadro **WindowsAzureMessaging.framework**   ao seu projeto Xcode. Selecione **Opções**   e **certifique-se de que os itens de cópia se necessários**são   selecionados e, em seguida, clique em **Adicionar**.

          :::image type="content" source="media/ios-sdk/image4.png" alt-text="Selecione o modelo":::

6. Adicione um novo ficheiro de cabeçalho ao seu projeto chamado **Constants.h**. Para tal, clique com o botão direito no nome do projeto e selecione **Novo Arquivo...**. Em seguida, **selecione o Ficheiro do Cabeçalho**. Este ficheiro detém as constantes para o Hub de Notificação. Em seguida, selecione **Seguinte**. Nomeie o ficheiro **Constants.h**.

7. Adicione o seguinte código ao ficheiro Constants.h:

   ```objc
   #ifndef Constants_h
   #define Constants_h
   extern NSString* const NHInfoConnectionString;
   extern NSString* const NHInfoHubName;
   extern NSString* const NHUserDefaultTags;
   #endif /* Constants_h */
   ```

8. Adicione o ficheiro de implementação para Constants.h. Para tal, clique com o botão direito no nome do projeto e selecione **Novo Arquivo...**. Selecione **o ficheiro Objective-C**e, em seguida, selecione **Seguinte**. Nomeie o ficheiro **Constants.m.**

   :::image type="content" source="media/ios-sdk/image5.png" alt-text="Selecione o modelo"

   NSString* const NHInfoConnectionString = @"NotificationHubConnectionString";
   NSString* const NHInfoHubName = @"NotificationHubName";NSString* const NHUserDefaultTags = @"notification_tags";
   ```

10. No ficheiro **AppDeegate.h**do   projeto, adicione a seguinte `import` declaração:

    ```objc
    #import "Constants.h"
    ```

11. No mesmo ficheiro **AppDeegate.m,**   substitua todo o código  `didFinishLaunchingWithOptions`   posteriormente pelo seguinte código:

    ```objc
    // Tells the delegate that the app successfully registered with Apple Push Notification service (APNs).


    - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {

    NSString *hubName = [[NSBundle mainBundle] objectForInfoDictionaryKey:NHInfoHubName];
    NSString *connectionString = [[NSBundle mainBundle] objectForInfoDictionaryKey:NHInfoConnectionString];
    [MSNotificationHub initWithConnectionString:connectionString withHubName:hubName];


    NSMutableSet *tags = [[NSMutableSet alloc] init];

    // Load and parse stored tags
    NSString *unparsedTags = [[NSUserDefaults standardUserDefaults] valueForKey:NHUserDefaultTags];
    if (unparsedTags.length > 0) {
        NSArray *tagsArray = [unparsedTags componentsSeparatedByString: @","];

        [MSNotificationHub addTags:tagsArray];
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

    Este código liga-se ao centro de notificação utilizando as informações de ligação especificadas em **Constants.h**. Em seguida, dá o token do dispositivo ao centro de notificação, para que o hub possa enviar notificações.

### <a name="create-notificationdetailviewcontroller-header-file"></a>Criar ficheiro de cabeçalho DotailViewController de notificação

1. Semelhante às instruções anteriores, adicione outro ficheiro de cabeçalho chamado **NotificationDetailViewController.h**. Substitua o conteúdo do novo ficheiro do cabeçalho pelo seguinte código:

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

2. Adicione o ficheiro de implementação **NotificationDetailViewController.m**. Substitua o conteúdo do ficheiro pelo seguinte código, que implementa os métodos UIViewController:

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

1. No ficheiro **ViewController.h**do   projeto, adicione as  `import`   seguintes declarações:

   ```objc
   #import <WindowsAzureMessaging/WindowsAzureMessaging.h>
   #import <UserNotifications/UserNotifications.h>
   ```

2. Também em **ViewController.h**, adicione a seguinte declaração de propriedade após a  `@interface`   declaração:

   ```objc
   @property (strong, nonatomic) IBOutlet UITextField *tagsTextField;
   ```

3. No ficheiro de implementação **ViewController.m**do   projeto, substitua o conteúdo do ficheiro pelo seguinte código:

   ```objc
   #import "ViewController.h"
   #import "Constants.h"
   #import "AppDelegate.h"

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
    self.tagsTextField.text = [[NSUserDefaults standardUserDefaults] valueForKey:NHUserDefaultTags];
   }

   - (IBAction)handleRegister:(id)sender {
    // Save raw tags text in storage
    [[NSUserDefaults standardUserDefaults] setValue:self.tagsTextField.text forKey:NHUserDefaultTags];

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

Pode testar notificações recebidas na sua aplicação com a opção **Enviar**por Teste   no portal [Azure.](https://portal.azure.com/) Esta ação envia uma notificação push de teste para o dispositivo.

:::image type="content" source="media/ios-sdk/image6.png" alt-text="Selecione o modelo":::

Normalmente, as notificações push são enviadas num serviço de back-end como Mobile Apps ou ASP.NET com uma biblioteca compatível. Se uma biblioteca não estiver disponível para a sua parte de trás, também pode utilizar a API REST diretamente para enviar mensagens de notificação.

Aqui está uma lista de outros tutoriais que você pode querer rever para o envio de notificações:

- Aplicativos Azure Mobile: Para um exemplo de como enviar notificações de uma aplicação móvel de volta integrada com Os Hubs de Notificação, consulte [Adicionar Notificações push à sua aplicação iOS](/previous-versions/azure/app-service-mobile/app-service-mobile-ios-get-started-push).
- ASP.NET: Utilize [os Centros de Notificação para enviar notificações push aos utilizadores](notification-hubs-aspnet-backend-ios-apple-apns-notification.md).
- Azure Notification Hubs Java SDK: Veja [como usar os Centros de Notificação de Java](notification-hubs-java-push-notification-tutorial.md)para o envio de   notificações de Java. Este processo foi testado em Eclipse para Programação Android.
- PHP: [Como utilizar os Centros de Notificação da PHP](notification-hubs-php-push-notification-tutorial.md).

## <a name="verify-that-your-app-receives-push-notifications"></a>Verificar que a aplicação recebe notificações push

Para testar as notificações push no iOS, tem de implementar a aplicação para um dispositivo iOS físico. Não é possível enviar notificações push da Apple utilizando o simulador iOS.

1. Executar a aplicação e verificar se o registo tem sucesso e, em seguida, prima **OK**.

   :::image type="content" source="media/ios-sdk/image7.png" alt-text="Selecione o modelo":::

2. Em seguida, envie uma notificação de push de teste a partir do [portal Azure](https://portal.azure.com/), conforme descrito na secção anterior.

3. A notificação push é enviada para todos os dispositivos que estão registados para receber as notificações do centro de notificação dado.

   :::image type="content" source="media/ios-sdk/image8.png" alt-text="Selecione o modelo":::

## <a name="next-steps"></a>Passos seguintes

Neste exemplo simples, transmite notificações push para todos os seus dispositivos iOS registados. Para saber como enviar notificações push para dispositivos iOS específicos, avance para o seguinte tutorial:

[Tutorial: Notificações push para dispositivos específicos](notification-hubs-ios-xplat-segmented-apns-push-notification.md)

Para obter mais informações, veja os artigos seguintes:

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