---
title: Notificações por push para aplicativos iOS Swift que usam hubs de notificação do Azure | Microsoft Docs
description: Saiba como enviar notificações por push para aplicativos iOS Swift que usam os hubs de notificação do Azure.
services: notification-hubs
documentationcenter: ios
author: mikeparker104
manager: femila
editor: jwargo
ms.assetid: 4e3772cf-20db-4b9f-bb74-886adfaaa65d
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ios
ms.devlang: objective-c
ms.topic: article
ms.date: 05/21/2019
ms.author: miparker
ms.reviewer: jowargo
ms.lastreviewed: 05/21/2019
ms.openlocfilehash: 8dae5bcc082ba5dd0953e3e97f609e4031547a35
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/08/2019
ms.locfileid: "72030650"
---
# <a name="tutorial-push-notifications-to-swift-ios-apps-that-use-the-notification-hubs-rest-api"></a>Tutorial: notificações por push para aplicativos iOS Swift que usam a API REST dos hubs de notificação

> [!div class="op_single_selector"]
> * [Objective-C](notification-hubs-ios-apple-push-notification-apns-get-started.md)
> * [Swift](notification-hubs-ios-push-notifications-swift-apps-get-started.md)

Neste tutorial, você usa os hubs de notificação do Azure para enviar notificações por push para um aplicativo iOS baseado em Swift usando a [API REST](/rest/api/notificationhubs/). Você também cria um aplicativo iOS em branco que recebe notificações por push usando o [serviço de notificação por push da Apple (APNs)](https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html#//apple_ref/doc/uid/TP40008194-CH8-SW1).

Este tutorial o conduz pelas seguintes etapas:

> [!div class="checklist"]
> * Gere o arquivo de solicitação de assinatura de certificado.
> * Solicite notificações por push ao seu aplicativo.
> * Crie um perfil de provisionamento para o aplicativo.
> * Criar um hub de notificação.
> * Configure o Hub de notificação com informações de APNs.
> * Conecte seu aplicativo iOS a um hub de notificação.
> * Teste a solução.

## <a name="prerequisites"></a>Pré-requisitos

Para acompanhar, você precisa de:

- Para percorrer a [visão geral dos hubs de notificação do Azure](notification-hubs-push-notification-overview.md) , se você não estiver familiarizado com o serviço.
- Para saber mais sobre os [registros e a instalação](notification-hubs-push-notification-registration-management.md).
- Uma [conta de desenvolvedor da Apple](https://developer.apple.com)ativa.
- Um Mac que executa o Xcode junto com um certificado de desenvolvedor válido instalado em seu conjunto de chaves.
- Um dispositivo do iPhone físico que você pode executar e depurar com o, pois não é possível testar notificações por push com o simulador.
- Seu dispositivo do iPhone físico registrado no [portal da Apple](https://developer.apple.com) e associado ao seu certificado.
- Uma [assinatura do Azure](https://portal.azure.com) em que você pode criar e gerenciar recursos.

Mesmo que você não tenha nenhuma experiência anterior com o desenvolvimento do iOS, você deve ser capaz de acompanhar as etapas para criar este exemplo de princípio. No entanto, você se beneficiará da familiaridade com os seguintes conceitos:

- Criando aplicativos iOS com o Xcode e o Swift.
- Configurando um [Hub de notificação do Azure](notification-hubs-ios-apple-push-notification-apns-get-started.md) para Ios.
- O [portal do desenvolvedor da Apple](https://developer.apple.com) e o [portal do Azure](https://portal.azure.com).

> [!NOTE]
> O Hub de notificação será configurado para usar somente o modo de autenticação de **área restrita** . Você não deve usar esse modo de autenticação para cargas de trabalho de produção.

[!INCLUDE [Notification Hubs Enable Apple Push Notifications](../../includes/notification-hubs-enable-apple-push-notifications.md)]

## <a name="connect-your-ios-app-to-a-notification-hub"></a>Conectar seu aplicativo iOS a um hub de notificação

Nesta seção, você criará o aplicativo iOS que se conectará ao Hub de notificação.  

### <a name="create-an-ios-project"></a>Criar um projeto do iOS

1. No Xcode, crie um novo projeto do iOS e selecione o modelo **Aplicação de Vista Única**.

1. Ao definir as opções para o novo projeto:

   1. Especifique o **nome do produto** (PushDemo) e o **identificador da organização** (`com.<organization>`) que você usou ao definir o **identificador do pacote** no portal do desenvolvedor da Apple.

   1. Escolha a **equipe** para a qual a **ID do aplicativo** foi configurada.

   1. Defina o **idioma** como **Swift**.

   1. Selecione **Seguinte**.

1. Crie uma nova pasta chamada **SupportingFiles**.

1. Crie um novo arquivo de lista p chamado **devsettings. plist** na pasta **SupportingFiles** . Certifique-se de adicionar essa pasta ao arquivo **gitignore** para que ela não seja confirmada ao trabalhar com um repositório git. Em um aplicativo de produção, você provavelmente estaria definindo condicionalmente esses segredos como parte de um processo de compilação automatizado. Essas configurações não são abordadas neste passo a passos.

1. Atualize o **devsettings. plist** para incluir as seguintes entradas de configuração usando seus próprios valores do hub de notificação que você provisionou:

   | Chave                            | Tipo                     | Valor                     |
   |--------------------------------| -------------------------| --------------------------|
   | notificationHubKey             | Cadeia                   | \<hubKey >                  |
   | notificationHubKeyName         | Cadeia                   | \<hubKeyName>              |
   | notificationHubName            | Cadeia                   | \<hubName>                 |
   | notificationHubNamespace       | Cadeia                   | \<hubNamespace>            |

   Você pode encontrar os valores necessários navegando até o recurso do hub de notificação no portal do Azure. Em particular, os valores **notificationHubName** e **notificationHubNamespace** estão no canto superior direito do resumo do **Essentials** na página **visão geral** .

   ![Resumo do Essentials dos hubs de notificação](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/hub-essentials.png)

   Você também pode encontrar os valores **notificationHubKeyName** e **notificationHubKey** navegando até **políticas de acesso** e selecionando a respectiva **política de acesso**, como `DefaultFullSharedAccessSignature`. Depois disso, copie da **cadeia de conexão primária** o valor prefixado com `SharedAccessKeyName=` para `notificationHubKeyName` e o valor prefixado com `SharedAccessKey=` para a `notificationHubKey`.

   A cadeia de conexão deve estar no seguinte formato:

   ```xml
   Endpoint=sb://<namespace>.servicebus.windows.net/;SharedAccessKeyName=<notificationHubKeyName>;SharedAccessKey=<notificationHubKey>
   ```

   Para simplificar, especifique `DefaultFullSharedAccessSignature` para que você possa usar o token para enviar notificações. Na prática, a `DefaultListenSharedAccessSignature` seria uma opção melhor para situações em que você só deseja receber notificações.

1. Em **navegador de projeto**, selecione o **nome do projeto** e, em seguida, selecione a guia **geral** .

1. Localize a **identidade** e defina o valor do **identificador de pacote** para que ele corresponda a `com.<organization>.PushDemo`, que é o valor usado para a ID do **aplicativo** de uma etapa anterior.

1. Localize **assinatura**e, em seguida, selecione a **equipe** apropriada para sua **conta de desenvolvedor da Apple**. O valor da **equipe** deve corresponder ao que você criou para seus certificados e perfis.

1. O Xcode deve extrair automaticamente o valor apropriado do **perfil de provisionamento** com base no **identificador do pacote**. Se você não vir o novo valor de **perfil de provisionamento** , tente atualizar os perfis para a **identidade de assinatura** selecionando **Xcode** > **preferências** > **conta** > **Exibir detalhes**. Selecione **identidade de assinatura**e, em seguida, selecione o botão **Atualizar** no canto inferior direito para baixar os perfis.

1. Selecione a guia **recursos** e verifique se as **notificações por push** estão habilitadas.

1. Abra o arquivo **AppDelegate. Swift** para implementar o protocolo **UNUserNotificationCenterDelegate** e adicione o seguinte código à parte superior da classe:

    ```swift
    @UIApplicationMain
    class AppDelegate: UIResponder, UIApplicationDelegate, UNUserNotificationCenterDelegate {

        ...

        var configValues: NSDictionary?
        var notificationHubNamespace : String?
        var notificationHubName : String?
        var notificationHubKeyName : String?
        var notificationHubKey : String?
        let tags = ["12345"]
        let genericTemplate = PushTemplate(withBody: "{\"aps\":{\"alert\":\"$(message)\"}}")

        ...
    }
    ```

    Você usará esses membros posteriormente. Especificamente, você usará as **marcas** e os membros **genéricostemplate** como parte do registro. Para obter mais informações sobre marcas, consulte [marcas para registros](notification-hubs-tags-segment-push-message.md) e [registros de modelo](notification-hubs-templates-cross-platform-push-messages.md).

1. No mesmo arquivo, adicione o seguinte código à função **didFinishLaunchingWithOptions** :

    ```swift
    if let path = Bundle.main.path(forResource: "devsettings", ofType: "plist") {
        if let configValues = NSDictionary(contentsOfFile: path) {
            self.notificationHubNamespace = configValues["notificationHubNamespace"] as? String
            self.notificationHubName = configValues["notificationHubName"] as? String
            self.notificationHubKeyName = configValues["notificationHubKeyName"] as? String
            self.notificationHubKey = configValues["notificationHubKey"] as? String
        }
    }

    if #available(iOS 10.0, *){
        UNUserNotificationCenter.current().delegate = self
        UNUserNotificationCenter.current().requestAuthorization(options: [.alert, .sound, .badge]) {
            (granted, error) in

            if (granted)
            {
                DispatchQueue.main.async {
                    application.registerForRemoteNotifications()
                }
            }
        }
    }

    return true
    ```

    Esse código recupera os valores de configuração de **devsettings. plist**, define a classe **AppDelegate** como o delegado **UNUserNotificationCenter** , solicita autorização para notificações por push e, em seguida, chama **registerForRemoteNotifications**.

    Para simplificar, o código dá suporte *somente para IOS 10 e posterior*. Você pode adicionar suporte para versões anteriores do sistema operacional usando condicionalmente as respectivas APIs e abordagens como faria normalmente.

1. No mesmo arquivo, adicione as seguintes funções:

    ```swift
    func application(_ application: UIApplication, didRegisterForRemoteNotificationsWithDeviceToken deviceToken: Data) {
        let installationId = (UIDevice.current.identifierForVendor?.description)!
        let pushChannel = deviceToken.reduce("", {$0 + String(format: "%02X", $1)})
    }

    func showAlert(withText text : String) {
        let alertController = UIAlertController(title: "PushDemo", message: text, preferredStyle: UIAlertControllerStyle.alert)
        alertController.addAction(UIAlertAction(title: "OK", style: UIAlertActionStyle.default,handler: nil))
        self.window?.rootViewController?.present(alertController, animated: true, completion: nil)
    }
    ```

    O código usa os valores **InstallationID** e **pushChannel** para se registrar no Hub de notificação. Nesse caso, você está usando **UIDevice. Current. identifierForVendor** para fornecer um valor exclusivo para identificar o dispositivo e, em seguida, formatar o **deviceToken** para fornecer o valor de **pushChannel** desejado. A função de **alerta** existe simplesmente para exibir um texto de mensagem para fins de demonstração.

1. Ainda no arquivo **AppDelegate. Swift** , adicione as funções **willPresent** e **didReceive** ao **UNUserNotificationCenterDelegate**. Essas funções exibem um alerta quando são notificados de que um aplicativo está sendo executado no primeiro plano ou no segundo plano, respectivamente.

    ```swift
    @available(iOS 10.0, *)
    func userNotificationCenter(_ center: UNUserNotificationCenter, 
        willPresent notification: UNNotification, 
        withCompletionHandler completionHandler: @escaping (UNNotificationPresentationOptions) -> Void) {
        showAlert(withText: notification.request.content.body)
    }

    @available(iOS 10.0, *)
    func userNotificationCenter(_ center: UNUserNotificationCenter, 
        didReceive response: UNNotificationResponse, 
        withCompletionHandler completionHandler: @escaping () -> Void) {
        showAlert(withText: response.notification.request.content.body)
    }
    ```

1. Adicione instruções PRINT à parte inferior da função **didRegisterForRemoteNotificationsWithDeviceToken** para verificar se **InstallationID** e **pushChannel** estão sendo valores atribuídos.

1. Crie os **modelos**, os **Serviços**e as pastas de **utilitários** para os componentes fundamentais que você adicionará ao projeto posteriormente.

1. Verifique se o projeto é compilado e executado em um dispositivo físico. As notificações por push não podem ser testadas usando o simulador.

### <a name="create-models"></a>Criar modelos

Nesta etapa, você criará um conjunto de modelos para representar os conteúdos da [API REST dos hubs de notificação](/rest/api/notificationhubs/) e para armazenar os dados de token de SAS (assinatura de acesso compartilhado) necessários.

1. Adicione um novo arquivo Swift chamado **pushtemplate. Swift** à pasta **modelos** . Esse modelo fornece um struct que representa o **corpo** de um modelo individual como parte do conteúdo do **DeviceInstallation** .

    ```swift
    import Foundation

    struct PushTemplate : Codable {
        let body : String

        init(withBody body : String) {
            self.body = body
        }
    }
    ```

1. Adicione um novo arquivo Swift chamado **DeviceInstallation. Swift** à pasta **modelos** . Esse arquivo define um struct que representa a carga para criar ou atualizar uma **instalação de dispositivo**. Adicione o seguinte código ao arquivo:

    ```swift
    import Foundation

    struct DeviceInstallation : Codable {
        let installationId : String
        let pushChannel : String
        let platform : String = "apns"
        var tags : [String]
        var templates : Dictionary<String, PushTemplate>

        init(withInstallationId installationId : String, andPushChannel pushChannel : String) {
            self.installationId = installationId
            self.pushChannel = pushChannel
            self.tags = [String]()
            self.templates = Dictionary<String, PushTemplate>()
        }
    }
    ```

1. Adicione um novo arquivo Swift chamado **TokenData. Swift** à pasta **modelos** . Esse modelo será usado para armazenar um token SAS junto com sua expiração.

    ```swift
    import Foundation

    struct TokenData {

        let token : String
        let expiration : Int

        init(withToken token : String, andTokenExpiration expiration : Int) {
            self.token = token
            self.expiration = expiration
        }
    }
    ```

### <a name="generate-a-sas-token"></a>Gerar um token SAS

Os hubs de notificação usam a mesma infraestrutura de segurança que o barramento de serviço do Azure. Para chamar a API REST, você precisará [gerar programaticamente um token SAS](/rest/api/eventhub/generate-sas-token) que possa ser usado no cabeçalho **Authorization** da solicitação.  

O token resultante estará no seguinte formato:

```xml
SharedAccessSignature sig=<UrlEncodedSignature>&se=<ExpiryEpoch>&skn=<KeyName>&sr=<UrlEncodedResourceUri>
```

O próprio processo envolve as mesmas seis etapas principais:  

1. Computando a expiração no formato de [hora de época do UNIX](https://en.wikipedia.org/wiki/Unix_time) , o que significa o número de segundos decorridos desde o tempo universal coordenado por meia-noite, 1º de janeiro de 1970.
1. Formatando o **ResourceUrl** que representa o recurso que você está tentando acessar para que ele seja codificado por percentual e minúscula. O **ResourceUrl** tem a forma `'https://<namespace>.servicebus.windows.net/<hubName>'`.
1. Preparando o **StringToSign**, que é formatado como `'<UrlEncodedResourceUrl>\n<ExpiryEpoch>'`.
1. Computação e codificação Base64 da **assinatura** usando o hash HMAC-SHA256 do valor **StringToSign** . O valor de hash é usado com a parte da **chave** da **cadeia de conexão** para a respectiva **regra de autorização**.
1. Formatando a **assinatura** codificada em base64 para que ela seja codificada por porcentagem.
1. Construindo o token no formato esperado usando os valores **UrlEncodedSignature**, **ExpiryEpoch**, **KeyName**e **UrlEncodedResourceUrl** .

Consulte a [documentação do barramento de serviço do Azure](../service-bus-messaging/service-bus-sas.md) para obter uma visão geral mais completa da assinatura de acesso compartilhado e como o barramento de serviço do Azure e os hubs de notificação o usam.

Para os fins deste exemplo Swift, você usará a biblioteca **CommonCrypto** de código aberto da Apple para ajudar com o hash da assinatura. Como se trata de uma biblioteca C, ela não está acessível no Swift pronta para uso. Você pode tornar a biblioteca disponível usando um cabeçalho de ponte.

Para adicionar e configurar o cabeçalho de ponte:

1. No Xcode, selecione **arquivo** > **novo** arquivo **de >  > ** **arquivo de cabeçalho**. Nomeie o arquivo de cabeçalho **BridgingHeader. h**.

1. Edite o arquivo para importar **CommonHMAC. h**:

    ```swift
    #import <CommonCrypto/CommonHMAC.h>

    #ifndef BridgingHeader_h
    #define BridgingHeader_h


    #endif /* BridgingHeader_h */
    ```

1. Atualize as configurações de **compilação** do destino para fazer referência ao cabeçalho de ponte:

   1. Abra a guia **configurações de construção** e role para baixo até a seção do **compilador Swift** .

   1. Verifique se a opção de **cabeçalho de compatibilidade instalar objetivo-C** está definida como **Sim**.

   1. Insira o caminho do arquivo `'<ProjectName>/BridgingHeader.h'` na opção de **cabeçalho ponte Objective-C** . Este é o caminho do arquivo para nosso cabeçalho de ponte.

   Se você não encontrar essas opções, verifique se você tem a exibição **todos** selecionada, em vez de **básica** ou **personalizada**.

   Há muitas bibliotecas de wrapper de software livre de terceiros disponíveis que podem tornar o uso de **CommonCrypto** um pouco mais fácil. No entanto, a discussão sobre essas bibliotecas está além do escopo deste artigo.

1. Adicione um novo arquivo Swift chamado **TokenUtility. Swift** na pasta **Utilities** e adicione o seguinte código:

   ```swift
   import Foundation

   struct TokenUtility {    
        typealias Context = UnsafeMutablePointer<CCHmacContext>

        static func getSasToken(forResourceUrl resourceUrl : String, withKeyName keyName : String, andKey key : String, andExpiryInSeconds expiryInSeconds : Int = 3600) -> TokenData {
            let expiry = (Int(NSDate().timeIntervalSince1970) + expiryInSeconds).description
            let encodedUrl = urlEncodedString(withString: resourceUrl)
            let stringToSign = "\(encodedUrl)\n\(expiry)"
            let hashValue = sha256HMac(withData: stringToSign.data(using: .utf8)!, andKey: key.data(using: .utf8)!)
            let signature = hashValue.base64EncodedString(options: .init(rawValue: 0))
            let encodedSignature = urlEncodedString(withString: signature)
            let sasToken = "SharedAccessSignature sr=\(encodedUrl)&sig=\(encodedSignature)&se=\(expiry)&skn=\(keyName)"
            let tokenData = TokenData(withToken: sasToken, andTokenExpiration: expiryInSeconds)

            return tokenData
        }

        private static func sha256HMac(withData data : Data, andKey key : Data) -> Data {
            let context = Context.allocate(capacity: 1)
            CCHmacInit(context, CCHmacAlgorithm(kCCHmacAlgSHA256), (key as NSData).bytes, size_t((key as NSData).length))
            CCHmacUpdate(context, (data as NSData).bytes, (data as NSData).length)
            var hmac = Array<UInt8>(repeating: 0, count: Int(CC_SHA256_DIGEST_LENGTH))
            CCHmacFinal(context, &hmac)

            let result = NSData(bytes: hmac, length: hmac.count)
            context.deallocate()

            return result as Data
        }

        private static func urlEncodedString(withString stringToConvert : String) -> String {
            var encodedString = ""
            let sourceUtf8 = (stringToConvert as NSString).utf8String
            let length = strlen(sourceUtf8)

            let charArray: [Character] = [ ".", "-", "_", "~", "a", "z", "A", "Z", "0", "9"]
            let asUInt8Array = String(charArray).utf8.map{ Int8($0) }

            for i in 0..<length {
                let currentChar = sourceUtf8![i]

                if (currentChar == asUInt8Array[0] || currentChar == asUInt8Array[1] || currentChar == asUInt8Array[2] || currentChar == asUInt8Array[3] ||
                    (currentChar >= asUInt8Array[4] && currentChar <= asUInt8Array[5]) ||
                    (currentChar >= asUInt8Array[6] && currentChar <= asUInt8Array[7]) ||
                    (currentChar >= asUInt8Array[8] && currentChar <= asUInt8Array[9])) {
                    encodedString += String(format:"%c", currentChar)
                }
                else {
                    encodedString += String(format:"%%%02x", currentChar)
                }
            }

            return encodedString
        }
    }
   ```

   Esse utilitário encapsula a lógica responsável por gerar o token SAS.

   Conforme descrito anteriormente, a função **método getsastoken** orquestra as etapas de alto nível necessárias para preparar o token. A função será chamada pelo serviço de instalação mais tarde neste tutorial.

   As outras duas funções são chamadas pela função **método getsastoken** : **sha256HMac** para calcular a assinatura e **urlEncodedString** para codificar a cadeia de caracteres de URL associada. A função **urlEncodedString** é necessária, pois não é possível obter a saída necessária usando a função **addingPercentEncoding** interna.

   O [SDK do IOS do armazenamento do Azure](https://github.com/Azure/azure-storage-ios/blob/master/Lib/Azure%20Storage%20Client%20Library/Azure%20Storage%20Client%20Library/AZSUtil.m) é um excelente exemplo de como abordar essas operações em Objective-C. Mais informações sobre tokens SAS do barramento de serviço do Azure podem ser encontradas na [documentação do barramento de serviço do Azure](../service-bus-messaging/service-bus-sas.md).

### <a name="verify-the-sas-token"></a>Verificar o token SAS

Antes de implementar o serviço de instalação no cliente do, verifique se nosso aplicativo está gerando o token SAS corretamente usando o utilitário HTTP de sua escolha. Para os fins deste tutorial, nossa ferramenta de escolha será o **postmaster**.

Use uma instrução print ou um ponto de interrupção adequadamente posicionado para anotar a **InstallationID** e os valores de **token** que estão sendo gerados pelo aplicativo.

Siga estas etapas para chamar a API de **instalações** :

1. No **postmaster**, abra uma nova guia.

1. Defina a solicitação para **obter** e especifique o seguinte endereço:

    ```xml
    https://<namespace>.servicebus.windows.net/<hubName>/installations/<installationId>?api-version=2015-01
    ```

1. Configure os cabeçalhos de solicitação da seguinte maneira:

   | Chave           | Valor            |
   | ------------- | ---------------- |
   | Tipo de conteúdo  | application/json |
   | Autorização | \<sasToken>       |
   | x-MS-Version  | 2015-01          |

1. Selecione o botão **código** que aparece no canto superior direito no botão **salvar** . A solicitação deve ser semelhante ao exemplo a seguir:

    ```html
    GET /<hubName>/installations/<installationId>?api-version=2015-01 HTTP/1.1
    Host: <namespace>.servicebus.windows.net
    Content-Type: application/json
    Authorization: <sasToken>
    x-ms-version: 2015-01
    Cache-Control: no-cache
    Postman-Token: <postmanToken>
    ```

1. Selecione o botão **Enviar** .

Não existe registro para a **InstallationID** especificada neste ponto. A verificação deve resultar em uma resposta "404 não encontrada" em vez de uma resposta "401 não autorizado". Esse resultado deve confirmar se o token SAS foi aceito.

### <a name="implement-the-installation-service-class"></a>Implementar a classe de serviço de instalação

Em seguida, você implementará nosso wrapper básico em relação à [API REST de instalações](/rest/api/notificationhubs/create-overwrite-installation).  

Adicione um novo arquivo Swift chamado **NotificationRegistrationService. Swift** na pasta **Serviços** e, em seguida, adicione o seguinte código a este arquivo:

```swift
import Foundation

class NotificationRegistrationService {
    private let tokenizedBaseAddress: String = "https://%@.servicebus.windows.net/%@"
    private let tokenizedCreateOrUpdateInstallationRequest = "/installations/%@?api-version=%@"
    private let session = URLSession(configuration: URLSessionConfiguration.default)
    private let apiVersion = "2015-01"
    private let jsonEncoder = JSONEncoder()
    private let defaultHeaders: [String : String]
    private let installationId : String
    private let pushChannel : String
    private let hubNamespace : String
    private let hubName : String
    private let keyName : String
    private let key : String
    private var tokenData : TokenData? = nil

    init(withInstallationId installationId : String,
            andPushChannel pushChannel : String,
            andHubNamespace hubNamespace : String,
            andHubName hubName : String,
            andKeyName keyName : String,
            andKey key: String) {
        self.installationId = installationId
        self.pushChannel = pushChannel
        self.hubNamespace = hubNamespace
        self.hubName = hubName
        self.keyName = keyName
        self.key = key
        self.defaultHeaders = ["Content-Type": "application/json", "x-ms-version": apiVersion]
    }

    func register(
        withTags tags : [String]? = nil,
        andTemplates templates : Dictionary<String, PushTemplate>? = nil,
        completeWith completion: ((_ result: Bool) -> ())? = nil) {

        var deviceInstallation = DeviceInstallation(withInstallationId: installationId, andPushChannel: pushChannel)

        if let tags = tags {
            deviceInstallation.tags = tags
        }

        if let templates = templates {
            deviceInstallation.templates = templates
        }

        if let deviceInstallationJson = encodeToJson(deviceInstallation) {
            let sasToken = getSasToken()
            let requestUrl = String.init(format: tokenizedCreateOrUpdateInstallationRequest, installationId, apiVersion)
            let apiEndpoint = "\(getBaseAddress())\(requestUrl)"

            var request = URLRequest(url: URL(string: apiEndpoint)!)
            request.httpMethod = "PUT"

            for (key,value) in self.defaultHeaders {
                request.addValue(value, forHTTPHeaderField: key)
            }

            request.addValue(sasToken, forHTTPHeaderField: "Authorization")
            request.httpBody = Data(deviceInstallationJson.utf8)

            (self.session.dataTask(with: request) { dat, res, err in
                if let completion = completion {
                        completion(err == nil && (res as! HTTPURLResponse).statusCode == 200)
                }
            }).resume()
        }
    }

    private func getBaseAddress() -> String {
        return String.init(format: tokenizedBaseAddress, hubNamespace, hubName)
    }

    private func getSasToken() -> String {
        if (tokenData == nil ||
            Date(timeIntervalSince1970: Double((tokenData?.expiration)!)) < Date(timeIntervalSinceNow: -(5 * 60))) {
            self.tokenData = TokenUtility.getSasToken(forResourceUrl: getBaseAddress(), withKeyName: self.keyName, andKey: self.key)
        }

        return (tokenData?.token)!
    }

    private func encodeToJson<T : Encodable>(_ object: T) -> String? {
        do {
            let jsonData = try jsonEncoder.encode(object)
            if let jsonString = String(data: jsonData, encoding: .utf8) {
                return jsonString
            } else {
                return nil
            }
        }
        catch {
            return nil
        }
    }
}
```

Os detalhes do requisito são fornecidos como parte da inicialização. As marcas e os modelos são, opcionalmente, passados na função **Register** para formar parte da carga JSON de **instalação do dispositivo** .  

A função **Register** chama as outras funções particulares para preparar a solicitação. Depois que uma resposta é recebida, a conclusão é chamada e indica se o registro foi bem-sucedido.  

O ponto de extremidade de solicitação é construído pela função **getbaseaddress** . A construção usa o *namespace* e o *nome* de parâmetros do hub de notificação que foram fornecidos durante a inicialização.  

A função **método getsastoken** verifica se o token armazenado no momento é válido. Se o token não for válido, a função chamará **TokenUtility** para gerar um novo token e, em seguida, o armazenará antes de retornar um valor.

Por fim, **encodeToJson** converte os objetos de modelo respectivos em JSON para uso como parte do corpo da solicitação.

### <a name="invoke-the-notification-hubs-rest-api"></a>Invocar a API REST dos hubs de notificação

A última etapa é atualizar **AppDelegate** para usar o **NotificationRegistrationService** para se registrar em nosso **NotificationHub**.

1. Abra **AppDelegate. Swift** e adicione uma variável em nível de classe para armazenar uma referência para o **NotificationRegistrationService**:

    ```swift
    var registrationService : NotificationRegistrationService?
    ```

1. No mesmo arquivo, atualize a função **didRegisterForRemoteNotificationsWithDeviceToken** para inicializar o **NotificationRegistrationService** com os parâmetros de requisito e, em seguida, chame a função de **registro** .

    ```swift
    func application(_ application: UIApplication, didRegisterForRemoteNotificationsWithDeviceToken deviceToken: Data) {
        let installationId = (UIDevice.current.identifierForVendor?.description)!
        let pushChannel = deviceToken.reduce("", {$0 + String(format: "%02X", $1)})

        // Initialize the Notification Registration Service
        self.registrationService = NotificationRegistrationService(
            withInstallationId: installationId,
            andPushChannel: pushChannel,
            andHubNamespace: notificationHubNamespace!,
            andHubName: notificationHubName!,
            andKeyName: notificationHubKeyName!,
            andKey: notificationHubKey!)

        // Call register, passing in the tags and template parameters
        self.registrationService!.register(withTags: tags, andTemplates: ["genericTemplate" : self.genericTemplate]) { (result) in
            if !result {
                print("Registration issue")
            } else {
                print("Registered")
            }
        }
    }
    ```

    Para simplificar, você vai usar algumas instruções PRINT para atualizar a janela de saída com o resultado da operação de **registro** .

1. Agora, compile e execute o aplicativo em um dispositivo físico. Você deve ver "registrado" na janela de saída.

## <a name="test-the-solution"></a>Testar a solução

Nosso aplicativo neste estágio está registrado com **NotificationHub** e pode receber notificações por push. No Xcode, pare o depurador e feche o aplicativo se ele estiver em execução no momento. Em seguida, verifique se os detalhes de **instalação do dispositivo** estão conforme o esperado e se nosso aplicativo agora pode receber notificações por push.  

### <a name="verify-the-device-installation"></a>Verificar a instalação do dispositivo

Agora você pode fazer a mesma solicitação como fazia anteriormente usando o **postmaster** para [verificar o token SAS](#verify-the-sas-token). Supondo que o token SAS não tenha expirado, a resposta agora deve incluir os detalhes da instalação fornecidos, como os modelos e as marcas.

```json
{
    "installationId": "<installationId>",
    "pushChannel": "<pushChannel>",
    "pushChannelExpired": false,
    "platform": "apns",
    "expirationTime": "9999-12-31T23:59:59.9999999Z",
    "tags": [
        "12345"
    ],
    "templates": {
        "genericTemplate": {
            "body": "{\"aps\":{\"alert\":\"$(message)\"}}",
            "tags": [
                "genericTemplate"
            ]
        }
    }
}
```

### <a name="send-a-test-notification-azure-portal"></a>Enviar uma notificação de teste (portal do Azure)

A maneira mais rápida de testar que agora você pode receber notificações é navegar até o Hub de notificação no portal do Azure:

1. Na portal do Azure, navegue até a guia **visão geral** no Hub de notificação.

1. Selecione **teste enviar**, que está acima do resumo do **Essentials** no canto superior esquerdo da janela do portal:

    ![Botão enviar teste de resumo do Essentials dos hubs de notificação](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/hub-essentials-test-send.png)

1. Escolha **modelo personalizado** na lista **plataformas** .

1. Insira **12345** para a **expressão enviar para marca**. Você já especificou essa marca em nossa instalação.

1. Opcionalmente, edite a **mensagem** no conteúdo JSON:

    ![Envio de teste de hubs de notificação](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/hub-test-send.png)

1. Selecione **Enviar**. O portal deve indicar se a notificação foi enviada com êxito para o dispositivo:

    ![Resultados de envio de teste dos hubs de notificação](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/hub-test-send-result.png)

    Supondo que o aplicativo não esteja sendo executado em primeiro plano, você também deverá ver uma notificação no **centro de notificações** em seu dispositivo. Tocar na notificação deve abrir o aplicativo e mostrar o alerta.

    ![Exemplo de notificação recebida](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/test-send-notification-received.png)

### <a name="send-a-test-notification-mail-carrier"></a>Enviar uma notificação de teste (transportadora de email)

Você pode enviar notificações por meio da [API REST](/rest/api/notificationhubs/) usando o **postmaster**, que pode ser uma maneira mais conveniente de testar.

1. Abra uma nova guia no **postmaster**.

1. Defina a solicitação como **post**e insira o seguinte endereço:

    ```xml
    https://<namespace>.servicebus.windows.net/<hubName>/messages/?api-version=2015-01
    ```

1. Configure os cabeçalhos de solicitação da seguinte maneira:

   | Chave                            | Valor                          |
   | ------------------------------ | ------------------------------ |
   | Tipo de conteúdo                   | application/json;charset=utf-8 |
   | Autorização                  | \<sasToken>                     |
   | ServiceBusNotification-Format  | modelo                       |
   | Etiquetas                           | "12345"                        |

1. Configure o **corpo** da solicitação para usar **JSON (Application. JSON) bruto** com o seguinte conteúdo JSON:

    ```json
    {
       "message" : "Hello from Postman!"
    }
    ```

1. Selecione o botão **código** , que está sob o botão **salvar** no canto superior direito da janela. A solicitação deve ser semelhante ao exemplo a seguir:

    ```html
    POST /<hubName>/messages/?api-version=2015-01 HTTP/1.1
    Host: <namespace>.servicebus.windows.net
    Content-Type: application/json;charset=utf-8.
    ServiceBusNotification-Format: template
    Tags: "12345"
    Authorization: <sasToken>
    Cache-Control: no-cache
    Postman-Token: <postmanToken>

    {
        "message" : "Hello from Postman!"
    }
    ```

1. Selecione o botão **Enviar** .

Você deve obter um código de status de êxito e receber a notificação no dispositivo cliente.

## <a name="next-steps"></a>Passos seguintes
Agora você tem um aplicativo iOS Swift básico conectado a um hub de notificação por meio da [API REST](/rest/api/notificationhubs/) e pode enviar e receber notificações. Para obter mais informações, veja os artigos seguintes:

- [Visão geral dos hubs de notificação do Azure](notification-hubs-push-notification-overview.md)
- [APIs REST dos hubs de notificação](/rest/api/notificationhubs/)
- [SDK dos hubs de notificação para operações de back-end](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)
- [SDK dos hubs de notificação no GitHub](https://github.com/Azure/azure-notificationhubs)
- [Registrar com o back-end do aplicativo](notification-hubs-ios-aspnet-register-user-from-backend-to-push-notification.md)
- [Gerenciamento de registro](notification-hubs-push-notification-registration-management.md)
- [Trabalhando com marcas](notification-hubs-tags-segment-push-message.md) 
- [Trabalhando com modelos personalizados](notification-hubs-templates-cross-platform-push-messages.md)
- [Controle de acesso do barramento de serviço com assinaturas de acesso compartilhado](../service-bus-messaging/service-bus-sas.md)
- [Gerar programaticamente tokens SAS](/rest/api/eventhub/generate-sas-token)
- [Apple Security: criptografia comum](https://developer.apple.com/security/)
- [Hora da época do UNIX](https://en.wikipedia.org/wiki/Unix_time)
- [HMAC](https://en.wikipedia.org/wiki/HMAC)
