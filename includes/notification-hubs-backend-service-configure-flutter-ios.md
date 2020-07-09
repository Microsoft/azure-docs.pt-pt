---
author: mikeparker104
ms.author: miparker
ms.date: 07/07/2020
ms.service: notification-hubs
ms.topic: include
ms.openlocfilehash: 9a8b76c0887cff498edd90aedf5cc6eb674fc7e5
ms.sourcegitcommit: 5cace04239f5efef4c1eed78144191a8b7d7fee8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/08/2020
ms.locfileid: "86147822"
---
### <a name="configure-the-runner-target-and-infoplist"></a>Configurar o alvo do Runner e info.plist

1. No **Código do Estúdio Visual,** **Control**  +  **Clique** na pasta **ios** e, em seguida, escolha **Abrir no Xcode**.

1. No **Xcode,** clique no **Runner** (o **xcodeproj** na parte superior, não na pasta) e, em seguida, selecione o alvo **do Corredor** e, em seguida, assine & **Capacidades**. Com a configuração de construção **All** selecionada, escolha a sua conta Developer para a **Equipa**. Certifique-se de que a opção "Gestão automática da assinatura" é verificada e que o seu Certificado de Assinatura e Perfil de Provisionamento são automaticamente selecionados.

    > [!NOTE]
    > Se não vir o novo valor do Perfil de Provisionamento, tente refrescar os perfis da Identidade de Assinatura selecionando a Conta preferências **do Xcode,**  >  **Preferences**  >  **Account** selecione o botão **Descodificador Perfis Manuais** para descarregar os perfis.

1. Clique em **+ Capacidade**e, em seguida, procure **notificações push**. **Clique duas vezes** em **Notificações push** para adicionar esta capacidade.

1. Abra **a info.plist** e desabrote a **versão mínima** do sistema para **13.0**.

    > [!NOTE]
    > Apenas os dispositivos que executam **o iOS 13.0 ou acima** são suportados para efeitos deste tutorial, no entanto pode alargá-lo para dispositivos de suporte que executam versões mais antigas.

1. Open **Runner.direitos** e certifique-se de que a definição **de ApS Environment** está definida para o **desenvolvimento**.

### <a name="handle-push-notifications-for-ios"></a>Lidar com notificações push para iOS

1. **Controlo**  +  **Clique** na pasta **Runner** (dentro do projeto Runner), em seguida, escolha **Novo Grupo** usando **Os Serviços** como o nome.

1. **Controlo**  +  **Clique** na pasta **Serviços** e, em seguida, escolha **Novo Ficheiro...**. Em seguida, escolha **Swift File** e clique em **Seguinte**. Especifique **o Serviço de instalação do Dispositivo** para o nome e, em seguida, clique em **Criar**.

1. Implementar **DeviceInstallationService.swift** utilizando o seguinte código.

    ```swift
    import Foundation

    class DeviceInstallationService {

        enum DeviceRegistrationError: Error {
            case notificationSupport(message: String)
        }

        var token : Data? = nil

        let DEVICE_INSTALLATION_CHANNEL = "com.<your_organization>.pushdemo/deviceinstallation"
        let GET_DEVICE_ID = "getDeviceId"
        let GET_DEVICE_TOKEN = "getDeviceToken"
        let GET_DEVICE_PLATFORM = "getDevicePlatform"

        private let deviceInstallationChannel : FlutterMethodChannel

        var notificationsSupported : Bool {
            get {
                if #available(iOS 13.0, *) {
                    return true
                }
                else {
                    return false
                }
            }
        }

        init(withBinaryMessenger binaryMessenger : FlutterBinaryMessenger) {
            deviceInstallationChannel = FlutterMethodChannel(name: DEVICE_INSTALLATION_CHANNEL, binaryMessenger: binaryMessenger)
            deviceInstallationChannel.setMethodCallHandler(handleDeviceInstallationCall)
        }

        func getDeviceId() -> String {
            return UIDevice.current.identifierForVendor!.description
        }

        func getDeviceToken() throws -> String {
            if(!notificationsSupported) {
                let notificationSupportError = getNotificationsSupportError()
                throw DeviceRegistrationError.notificationSupport(message: notificationSupportError)
            }

            if (token == nil) {
                throw DeviceRegistrationError.notificationSupport(message: "Unable to resolve token for APNS.")
            }

            return token!.reduce("", {$0 + String(format: "%02X", $1)})
        }

        func getDevicePlatform() -> String {
            return "apns"
        }

        private func handleDeviceInstallationCall(call: FlutterMethodCall, result: @escaping FlutterResult) {
            switch call.method {
            case GET_DEVICE_ID:
                result(getDeviceId())
            case GET_DEVICE_TOKEN:
                getDeviceToken(result: result)
            case GET_DEVICE_PLATFORM:
                result(getDevicePlatform())
            default:
                result(FlutterMethodNotImplemented)
            }
        }

        private func getDeviceToken(result: @escaping FlutterResult) {
            do {
                let token = try getDeviceToken()
                result(token)
            }
            catch let error {
                result(FlutterError(code: "UNAVAILABLE", message: error.localizedDescription, details: nil))
            }
        }

        private func getNotificationsSupportError() -> String {

            if (!notificationsSupported) {
                return "This app only supports notifications on iOS 13.0 and above. You are running \(UIDevice.current.systemVersion)"
            }

            return "An error occurred preventing the use of push notifications."
        }
    }
    ```

    > [!NOTE]
    > Esta classe implementa a contrapartida específica da plataforma para o `com.<your_organization>.pushdemo/deviceinstallation` canal. Isto foi definido na parte flutter da app dentro **do DeviceInstallationService.dart**. Neste caso, as chamadas são feitas do código comum para o hospedeiro nativo. Não se esqueça de substituir **<your_organization>** por sua própria organização onde quer que isso seja usado.
    >
    > Esta classe fornece um ID único (utilizando o valor [UIDevice.identifierForVendor)](https://developer.apple.com/documentation/uikit/uidevice/1620059-identifierforvendor) como parte da carga útil do registo do centro de notificação.

1. Adicione outro **Ficheiro Swift** à pasta **serviços** chamado *NotificationRegistrationService*e, em seguida, adicione o seguinte código.

    ```swift
    import Foundation

    class NotificationRegistrationService {

        let NOTIFICATION_REGISTRATION_CHANNEL = "com.<your_organization>.pushdemo/notificationregistration"
        let REFRESH_REGISTRATION = "refreshRegistration"

        private let notificationRegistrationChannel : FlutterMethodChannel

        init(withBinaryMessenger binaryMessenger : FlutterBinaryMessenger) {
           notificationRegistrationChannel = FlutterMethodChannel(name: NOTIFICATION_REGISTRATION_CHANNEL, binaryMessenger: binaryMessenger)
        }

        func refreshRegistration() {
            notificationRegistrationChannel.invokeMethod(REFRESH_REGISTRATION, arguments: nil)
        }
    }
    ```

    > [!NOTE]
    > Esta classe implementa a contrapartida específica da plataforma para o `com.<your_organization>.pushdemo/notificationregistration` canal. Isto foi definido na parte flutter da app dentro **do NotificationRegistrationService.dart**. Neste caso, as chamadas são feitas do hospedeiro nativo para o código comum. Mais uma vez, tome cuidado para substituir **<your_organization>** pela sua própria organização onde quer que isso seja usado.

1. Adicione outro **Ficheiro Swift** à pasta **serviços** chamado *NotificationActionService*e adicione o seguinte código.

    ```swift
    import Foundation

    class NotificationActionService {

        let NOTIFICATION_ACTION_CHANNEL = "com.<your_organization>.pushdemo/notificationaction"
        let TRIGGER_ACTION = "triggerAction"
        let GET_LAUNCH_ACTION = "getLaunchAction"

        private let notificationActionChannel: FlutterMethodChannel

        var launchAction: String? = nil

        init(withBinaryMessenger binaryMessenger: FlutterBinaryMessenger) {
            notificationActionChannel = FlutterMethodChannel(name: NOTIFICATION_ACTION_CHANNEL, binaryMessenger: binaryMessenger)
            notificationActionChannel.setMethodCallHandler(handleNotificationActionCall)
        }

        func triggerAction(action: String) {
           notificationActionChannel.invokeMethod(TRIGGER_ACTION, arguments: action)
        }

        private func handleNotificationActionCall(call: FlutterMethodCall, result: @escaping FlutterResult) {
            switch call.method {
            case GET_LAUNCH_ACTION:
                result(launchAction)
            default:
                result(FlutterMethodNotImplemented)
            }
        }
    }
    ```

    > [!NOTE]
    > Esta classe implementa a contrapartida específica da plataforma para o `com.<your_organization>.pushdemo/notificationaction` canal. Isto foi definido na parte flutter da app dentro **do NotificationActionService.dart**. As chamadas podem ser feitas em ambas as direções neste caso. Não se esqueça de substituir **<your_organization>** por sua própria organização onde quer que isso seja usado.

1. No **AppDeegate.swift,** adicione variáveis para armazenar uma referência aos serviços que criou anteriormente.

    ```swift
    var deviceInstallationService : DeviceInstallationService?
    var notificationRegistrationService : NotificationRegistrationService?
    var notificationActionService : NotificationActionService?
    ```

1. Adicione uma função chamada **processNotificationActions** para o processamento dos dados de notificação. Desencadeie condicionalmente essa ação ou armazene-a para utilização posterior se a ação estiver a ser processada durante o lançamento da aplicação.

    ```swift
    func processNotificationActions(userInfo: [AnyHashable : Any], launchAction: Bool = false) {
        if let action = userInfo["action"] as? String {
            if (launchAction) {
                notificationActionService?.launchAction = action
            }
            else {
                notificationActionService?.triggerAction(action: action)
            }
        }
    }
    ```

1. Anular a função **didRegisterForRemoteNotificationsWithDeviceToken** definindo o valor **simbólico** para o **DeviceInstallationService**. Em seguida, ligue **para a actualizaçãoRegistration** no **Serviço de Registo de Notificação**.

    ```swift
    override func application(_ application: UIApplication, didRegisterForRemoteNotificationsWithDeviceToken deviceToken: Data) {
      deviceInstallationService?.token = deviceToken
      notificationRegistrationService?.refreshRegistration()
    }
    ```

1. Anular a função **didReceiveRemoteNotification** passando o argumento **userInfo** para a função **processNotificationActions.**

    ```swift
    override func application(_ application: UIApplication, didReceiveRemoteNotification userInfo: [AnyHashable : Any]) {
        processNotificationActions(userInfo: userInfo)
    }
    ```

1. Anular a função **didFailToRegisterForRemoteNotificationsWithError** para registar o erro.

    ```swift
    override func application(_ application: UIApplication, didFailToRegisterForRemoteNotificationsWithError error: Error) {
        print(error);
    }
    ```

    > [!NOTE]
    > Este é muito um espaço reservado. Você vai querer implementar o manuseamento adequado de registos e erros para cenários de produção.

1. In **didFinishLaunchingWithOptions**, instantaneamente o **deviceInstallationService**, **notificationRegistrationService**e **notificationActionService** variáveis.

    ```swift
    let controller : FlutterViewController = window?.rootViewController as! FlutterViewController

    deviceInstallationService = DeviceInstallationService(withBinaryMessenger: controller.binaryMessenger)
    notificationRegistrationService = NotificationRegistrationService(withBinaryMessenger: controller.binaryMessenger)
    notificationActionService = NotificationActionService(withBinaryMessenger: controller.binaryMessenger)
    ```

1. Na mesma função, solicitar condicionalmente autorização e registar-se para notificações remotas.

    ```swift
    if #available(iOS 13.0, *) {
      UNUserNotificationCenter.current().requestAuthorization(options: [.alert, .sound, .badge]) {
          (granted, error) in

          if (granted)
          {
              DispatchQueue.main.async {
                  let pushSettings = UIUserNotificationSettings(types: [.alert, .sound, .badge], categories: nil)
                  application.registerUserNotificationSettings(pushSettings)
                  application.registerForRemoteNotifications()
              }
          }
      }
    }
    ```

1. Se as **opções de lançamento** contiverem a chave **denotificação remota,** chame **o processoNotificationActions** no final da função **DidFinishLaunchingWithOptions.** Passe no objeto utilizador resultante **e** use *o verdadeiro* para o argumento *launchAction.* Um *verdadeiro* valor denota que a ação está a ser processada durante o lançamento da app.

    ```swift
    if let userInfo = launchOptions?[.remoteNotification] as? [AnyHashable : Any] {
        processNotificationActions(userInfo: userInfo, launchAction: true)
    }
    ```
