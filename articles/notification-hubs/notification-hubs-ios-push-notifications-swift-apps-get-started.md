---
title: Notificações push para aplicações Swift iOS que utilizam Notification Hubs do Azure | Documentos da Microsoft
description: Saiba como notificações push para aplicações Swift iOS que utilizam Notification Hubs do Azure.
services: notification-hubs
documentationcenter: ios
author: mikeparker104
manager: patniko
editor: spelluru
ms.assetid: 4e3772cf-20db-4b9f-bb74-886adfaaa65d
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ios
ms.devlang: objective-c
ms.topic: article
ms.date: 05/21/2019
ms.author: miparker
ms.openlocfilehash: a4773ddd8114659118e89cfee57e73ddb39ff6b6
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67116690"
---
# <a name="tutorial-push-notifications-to-swift-ios-apps-that-use-the-notification-hubs-rest-api"></a>Tutorial: Notificações push para aplicações Swift iOS que utilizam a API de REST de Hubs de notificação

> [!div class="op_single_selector"]
> * [Objective-C](notification-hubs-ios-apple-push-notification-apns-get-started.md)
> * [Swift](notification-hubs-ios-push-notifications-swift-apps-get-started.md)

Neste tutorial, vai utilizar Notification Hubs do Azure para enviar notificações push para uma aplicação iOS Swift com base em utilizando o [REST API](/rest/api/notificationhubs/). Também é criar uma aplicação iOS vazia que recebe notificações push utilizando o [serviço Apple Push Notification (APNs)](https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html#//apple_ref/doc/uid/TP40008194-CH8-SW1).

Este tutorial guia-o ao longo dos passos seguintes:

> [!div class="checklist"]
> * Gere o ficheiro de pedido de assinatura de certificado.
> * Solicite a sua aplicação para notificações push.
> * Crie um perfil de aprovisionamento da aplicação.
> * Criar um hub de notificação.
> * Configure o hub de notificação com informações do APNs.
> * Ligar a sua aplicação iOS para um hub de notificação.
> * Teste a solução.

## <a name="prerequisites"></a>Pré-requisitos

Para acompanhar, terá de:

- Percorrer [descrição geral dos Hubs de notificação do Azure](notification-hubs-push-notification-overview.md) se não estiver familiarizado com o serviço.
- Para saber mais sobre [registos e instalação](notification-hubs-push-notification-registration-management.md).
- Um ativo [conta de programador da Apple](https://developer.apple.com).
- Um Mac com o Xcode, juntamente com um certificado de programador válido instalado na Keychain.
- Um dispositivo físico do iPhone, pode executar e depurar com, como não é possível testar as notificações push com o simulador.
- O dispositivo iPhone físico registado no [Apple Portal](https://developer.apple.com) e associadas com o certificado.
- Uma [subscrição do Azure](https://portal.azure.com) onde pode criar e gerir os recursos.

Mesmo que tenha sem ter experiência anterior com o desenvolvimento de iOS, deve ser capaz de acompanhar as etapas para criar este exemplo de princípios de primeiro. No entanto, irá se beneficiar familiaridade com os seguintes conceitos:

- Criar aplicativos do iOS com Xcode e Swift.
- Configurar uma [hub de notificação do Azure](notification-hubs-ios-apple-push-notification-apns-get-started.md) para iOS.
- O [Portal do programador da Apple](https://developer.apple.com) e o [portal do Azure](https://portal.azure.com).

> [!NOTE]
> O hub de notificação será configurado para utilizar o **Sandbox** apenas o modo de autenticação. Não deve usar este modo de autenticação para cargas de trabalho de produção.

[!INCLUDE [Notification Hubs Enable Apple Push Notifications](../../includes/notification-hubs-enable-apple-push-notifications.md)]

## <a name="connect-your-ios-app-to-a-notification-hub"></a>Ligar a sua aplicação iOS para um hub de notificação

Nesta secção, irá criar a aplicação iOS que irá ligar ao hub de notificação.  

### <a name="create-an-ios-project"></a>Crie um projeto do iOS

1. No Xcode, crie um novo projeto do iOS e selecione o modelo **Aplicação de Vista Única**.

1. Quando definir as opções para o novo projeto:

   1. Especifique a **nome do produto** (PushDemo) e **identificador de organização** (`com.<organization>`) que utilizou quando definiu **identificador de pacote** no Portal de programador da Apple.

   1. Escolha o **equipe** que o **ID de aplicação** foi definido para a cópia de segurança.

   1. Definir o **linguagem** ao **Swift**.

   1. Selecione **Seguinte**.

1. Criar uma nova pasta chamada **SupportingFiles**.

1. Crie um novo ficheiro de lista de p chamado **devsettings.plist** no **SupportingFiles** pasta. Certifique-se de que adicionar esta pasta à sua **gitignore** ficheiro, para que ele não está consolidado ao trabalhar com um repositório de git. Num aplicativo de produção, iria provavelmente ser condicionalmente configuração estes segredos como parte de um processo automatizado de compilação. Essas configurações não são abrangidas nestas instruções.

1. Atualização **devsettings.plist** para incluir as seguintes entradas de configuração ao utilizar os seus próprios valores do hub de notificação que aprovisionou:

   | Chave                            | Tipo                     | Value                     |
   |--------------------------------| -------------------------| --------------------------|
   | notificationHubKey             | String                   | <hubKey>                  |
   | notificationHubKeyName         | String                   | <hubKeyName>              |
   | notificationHubName            | String                   | <hubName>                 |
   | notificationHubNamespace       | String                   | <hubNamespace>            |

   Pode encontrar os valores necessários ao navegar para o recurso do hub de notificação no portal do Azure. Em particular, o **notificationHubName** e **notificationHubNamespace** valores são no canto superior direito do **Essentials** resumo dentro do **Descrição geral** página.

   ![Resumo do Essentials de Hubs de notificação](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/hub-essentials.png)

   Também pode encontrar os **notificationHubKeyName** e **notificationHubKey** valores ao navegar para o **políticas de acesso** e selecionando o respetivo  **Política de acesso**, tais como `DefaultFullSharedAccessSignature`. Depois disso, copiar do **cadeia de ligação primária** o valor com o prefixo `SharedAccessKeyName=` para `notificationHubKeyName` e o valor com o prefixo `SharedAccessKey=` para o `notificationHubKey`.

   A cadeia de ligação deve estar no seguinte formato:

   ```xml
   Endpoint=sb://<namespace>.servicebus.windows.net/;SharedAccessKeyName=<notificationHubKeyName>;SharedAccessKey=<notificationHubKey>
   ```

   Para simplificar, especifique `DefaultFullSharedAccessSignature` pelo que pode utilizar o token para enviar notificações. Na prática a `DefaultListenSharedAccessSignature` seria uma opção melhor para situações em que apenas pretende receber notificações.

1. Sob **navegador de projetos**, selecione a **nome do projeto** e, em seguida, selecione o **geral** separador.

1. Encontrar **identidade** e, em seguida, defina o **identificador do pacote** valor, de modo a que corresponda ao `com.<organization>.PushDemo`, que é utilizado o valor para o **ID da aplicação** do passo anterior.

1. Encontrar **assinatura**e, em seguida, selecione o adequado **equipe** para sua **conta de programador da Apple**. O **equipe** valor deve corresponder à que sob a qual criou a perfis e certificados.

1. Xcode deve obter automaticamente o adequado **perfil de aprovisionamento** valor com base na **identificador de pacote**. Se não vir o novo **perfil de aprovisionamento** valor, tente atualizar os perfis para o **identidade de assinatura** selecionando **Xcode**  >  **Preferências** > **conta** > **ver detalhes**. Selecione **identidade de assinatura**e, em seguida, selecione a **atualizar** botão à direita na parte inferior para transferir os perfis.

1. Selecione o **capacidades** separador e certifique-se de que **notificações push** estão ativadas.

1. Abra sua **Appdelegate** ficheiro para implementar a **UNUserNotificationCenterDelegate** de protocolo e adicione o seguinte código na parte superior da classe:

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

    Usará esses membros mais tarde. Especificamente, usará o **etiquetas** e **genericTemplate** membros como parte do registo. Para obter mais informações sobre etiquetas, consulte [etiquetas para registos](notification-hubs-tags-segment-push-message.md) e [registos de modelos](notification-hubs-templates-cross-platform-push-messages.md).

1. No mesmo ficheiro, adicione o seguinte código para o **didFinishLaunchingWithOptions** função:

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

    Este código recupera os valores de configuração do **devsettings.plist**, define a **AppDelegate** classe como o **UNUserNotificationCenter** delegar, autorização de solicitações para notificações push e, em seguida, chama **registerForRemoteNotifications**.

    Para simplificar, o código suporta *iOS 10 e posterior apenas*. Pode adicionar suporte para versões anteriores do sistema operacional, condicionalmente utilizando as respetivas APIs e abordagens, como faria normalmente.

1. No mesmo ficheiro, adicione as seguintes funções:

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

    O código utiliza a **installationId** e **pushChannel** valores para registar com o hub de notificação. Neste caso, está usando **UIDevice.current.identifierForVendor** para fornecer um valor exclusivo para identificar o dispositivo e, em seguida, em seguida, a formatação a **deviceToken** para fornecer o desejado  **pushChannel** valor. O **showAlert** função existe simplesmente para exibirá algum texto de mensagem para fins de demonstração.

1. Ainda na **Appdelegate** do ficheiro, adicione o **willPresent** e **didReceive** funciona para **UNUserNotificationCenterDelegate**. Estas funções apresentam um alerta quando eles foi notificados de que uma aplicação está em execução em primeiro plano ou segundo plano, respetivamente.

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

1. Adicionar declarações de impressa para o fim dos **didRegisterForRemoteNotificationsWithDeviceToken** função para verificar se **installationId** e **pushChannel** estão a ser valores atribuídos.

1. Criar a **modelos**, **serviços**, e **utilitários** pastas para os componentes fundamentais que adicionará ao projeto mais tarde.

1. Verifique se o projeto é compilada e executada num dispositivo físico. Não não possível testar notificações push utilizando o simulator.

### <a name="create-models"></a>Criar modelos

Neste passo, irá criar um conjunto de modelos para representar a [API de REST dos Hubs de notificação](/rest/api/notificationhubs/) payloads e para armazenar o acesso partilhado necessário dados de token de assinatura (SAS).

1. Adicione um novo ficheiro Swift chamado **PushTemplate.swift** para o **modelos** pasta. Esse modelo proporciona uma estrutura que representa a **corpo** de um modelo individual como parte do **DeviceInstallation** payload.

    ```swift
    import Foundation

    struct PushTemplate : Codable {
        let body : String

        init(withBody body : String) {
            self.body = body
        }
    }
    ```

1. Adicione um novo ficheiro Swift chamado **DeviceInstallation.swift** para o **modelos** pasta. Esse arquivo define uma estrutura que representa o payload para criar ou atualizar uma **instalação de dispositivo**. Adicione o seguinte código para o ficheiro:

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

1. Adicione um novo ficheiro Swift chamado **TokenData.swift** para o **modelos** pasta. Este modelo irá ser utilizado para armazenar um token SAS, juntamente com sua expiração.

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

Os Hubs de notificação utilizam a mesma infraestrutura de segurança, como o Azure Service Bus. Para chamar a API REST, precisará [programaticamente gerar um token SAS](/rest/api/eventhub/generate-sas-token) que podem ser utilizados no **autorização** cabeçalho do pedido.  

O token resultante estará no seguinte formato:

```xml
SharedAccessSignature sig=<UrlEncodedSignature>&se=<ExpiryEpoch>&skn=<KeyName>&sr=<UrlEncodedResourceUri>
```

O próprio processo envolve os seis passos principais:  

1. Computação de expiração no [hora "Epoch" de UNIX](https://en.wikipedia.org/wiki/Unix_time) formato, o que significa que o número de segundos decorridos desde a meia-noite coordenada Universal Time, 1 de Janeiro de 1970.
1. Formatação da **ResourceUrl** que representa o recurso que está a tentar aceder, portanto, é codificada de percentagem e minúsculas. O **ResourceUrl** tem o formato `'https://<namespace>.servicebus.windows.net/<hubName>'`.
1. A preparar a **StringToSign**, que é formatado como `'<UrlEncodedResourceUrl>\n<ExpiryEpoch>'`.
1. Computação e a codificação de Base64 a **assinatura** utilizando o hash de SHA256 do HMAC da **StringToSign** valor. O valor de hash é utilizado com o **chave** faz parte do **cadeia de ligação** para o respetivo **regra de autorização**.
1. Formatação de codificação de Base64 **assinatura** , portanto, é codificado por cento.
1. Construir o token no formato esperado utilizando o **UrlEncodedSignature**, **ExpiryEpoch**, **KeyName**, e **UrlEncodedResourceUrl** valores.

Consulte a [documentação do Azure Service Bus](../service-bus-messaging/service-bus-sas.md) para obter uma descrição mais completa de assinatura de acesso partilhado e utilizá-lo como Azure Service Bus e os Hubs de notificação.

Para efeitos deste exemplo rápido, vai utilizar de código aberto da Apple **CommonCrypto** biblioteca para ajudar com o hash da assinatura. Como é uma biblioteca de C, não é acessível no Swift prontos a utilizar. Pode disponibilizar a biblioteca com um cabeçalho de bridging.

Para adicionar e configurar o cabeçalho de bridging:

1. No Xcode, selecione **arquivo** > **New** > **ficheiro** > **arquivo de cabeçalho**. Nomeie o arquivo de cabeçalho **BridgingHeader.h**.

1. Editar o ficheiro a importar **CommonHMAC.h**:

    ```swift
    #import <CommonCrypto/CommonHMAC.h>

    #ifndef BridgingHeader_h
    #define BridgingHeader_h


    #endif /* BridgingHeader_h */
    ```

1. Atualizar o destino **definições de criação** para referenciar o cabeçalho de bridging:

   1. Abra o **definições de criação** separador e desloque para baixo para o **compilador Swift** secção.

   1. Certifique-se de que o **instalar o cabeçalho de compatibilidade de Objective-C** opção estiver definida como **Sim**.

   1. Introduza o caminho do ficheiro `'<ProjectName>/BridgingHeader.h'` para o **cabeçalho de bridging Objective-C** opção. Este é o caminho de ficheiro ao nosso cabeçalho de bridging.

   Se não conseguir localizar estas opções, certifique-se de que tem o **todos os** vista selecionada vez **básica** ou **personalizado**.

   Há muitos wrapper de código-fonte aberto de terceiros bibliotecas disponíveis que pode fazer usando **CommonCrypto** um pouco mais fácil. No entanto, a discussão sobre essas bibliotecas é além do escopo deste artigo.

1. Adicione um novo ficheiro Swift com o nome **TokenUtility.swift** dentro do **utilitários** pasta e adicione o seguinte código:

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

   Conforme descrito anteriormente, o **getSasToken** função orquestra as etapas de alto nível necessárias para preparar o token. A função será chamada pelo serviço de instalação mais tarde neste tutorial.

   As outras duas funções são chamadas pela **getSasToken** função: **sha256HMac** para a assinatura de computação e **urlEncodedString** para codificar a URL associada cadeia de caracteres. O **urlEncodedString** função é necessária, pois não é possível obter o resultado necessário, utilizando o incorporado **addingPercentEncoding** função.

   O [SDK iOS do armazenamento do Azure](https://github.com/Azure/azure-storage-ios/blob/master/Lib/Azure%20Storage%20Client%20Library/Azure%20Storage%20Client%20Library/AZSUtil.m) é um excelente exemplo de como abordar essas operações em Objective-C. Obter mais informações sobre os tokens de SAS de barramento de serviço do Azure podem ser encontradas no [documentação do Azure Service Bus](../service-bus-messaging/service-bus-sas.md).

### <a name="verify-the-sas-token"></a>Verifique se o token SAS

Antes de implementar o serviço de instalação no cliente, verifique se a nossa aplicação corretamente está a gerar o token SAS com o utilitário de HTTP à escolha. Para efeitos deste tutorial, vai ser a nossa ferramenta de eleição **Postman**.

Utilizar uma instrução print colocá-la adequadamente ou ponto de interrupção a ter em conta a **installationId** e o **token** valores a ser gerados pela aplicação.

Siga estes passos para chamar o **instalações** API:

1. Na **Postman**, abra um novo separador.

1. Defina o pedido para **obter** e especifique o seguinte endereço:

    ```xml
    https://<namespace>.servicebus.windows.net/<hubName>/installations/<installationId>?api-version=2015-01
    ```

1. Configure os cabeçalhos de pedido da seguinte forma:

   | Chave           | Value            |
   | ------------- | ---------------- |
   | Content-Type  | application/json |
   | Autorização | <sasToken>       |
   | x-ms-version  | 2015-01          |

1. Selecione o **código** botão é exibido no canto superior direito sob a **guardar** botão. O pedido deve ter um aspeto semelhante ao seguinte exemplo:

    ```html
    GET /<hubName>/installations/<installationId>?api-version=2015-01 HTTP/1.1
    Host: <namespace>.servicebus.windows.net
    Content-Type: application/json
    Authorization: <sasToken>
    x-ms-version: 2015-01
    Cache-Control: no-cache
    Postman-Token: <postmanToken>
    ```

1. Selecione o **enviar** botão.

Nenhum registo existe para a especificado **installationId** neste momento. A verificação deve resultar numa resposta "404 não encontrado", em vez de uma resposta "401 não autorizado". Este resultado deve confirmar que o token SAS foi aceite.

### <a name="implement-the-installation-service-class"></a>Implementar a classe de serviço de instalação

Em seguida implementará nosso básica wrapper em torno da [API do REST de instalações](/rest/api/notificationhubs/create-overwrite-installation).  

Adicione um novo ficheiro Swift chamado **NotificationRegistrationService.swift** sob a **serviços** pasta e, em seguida, adicione o seguinte código para este ficheiro:

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

Os detalhes necessários são fornecidos como parte da inicialização. Etiquetas e modelos, opcionalmente, serão passados para o **registar** função para a parte do formulário a **instalação de dispositivo** JSON payload.  

O **registar** função chama as outras funções privadas para preparar o pedido. Depois de uma resposta é recebida, a conclusão é chamada e indica se o registo foi concluída com êxito.  

O ponto de extremidade do pedido é construído à **getBaseAddress** função. A construção utiliza os parâmetros de hub de notificação *espaço de nomes* e *nome* que foram fornecidos durante a inicialização.  

O **getSasToken** função verifica se o token atualmente armazenado é válido. Se o token não é válido, a função chama **TokenUtility** para gerar um novo token e, em seguida, armazena-a antes de retornar um valor.

Por fim, **encodeToJson** converte os objetos do respetivo modelo em JSON para utilização como parte do corpo do pedido.

### <a name="invoke-the-notification-hubs-rest-api"></a>Invocar os REST API dos Hubs de notificação

O último passo está a ser atualizada **AppDelegate** a utilizar **NotificationRegistrationService** para registar com o nosso **NotificationHub**.

1. Open **Appdelegate** e adicione uma variável de nível de classe para armazenar uma referência para o **NotificationRegistrationService**:

    ```swift
    var registrationService : NotificationRegistrationService?
    ```

1. No mesmo ficheiro, atualize o **didRegisterForRemoteNotificationsWithDeviceToken** função para inicializar o **NotificationRegistrationService** com os parâmetros necessários e, em seguida, chamamos o **registar** função.

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

    Para simplificar, vamos usar algumas instruções de impressa para atualizar a janela de saída com o resultado do **registar** operação.

1. Agora, crie e execute a aplicação num dispositivo físico. Deverá ver "Registada" na janela de saída.

## <a name="test-the-solution"></a>Testar a solução

Nosso aplicativo nesse estágio está registrado com **NotificationHub** e pode receber notificações push. No Xcode, pare o depurador e fechar a aplicação se está em execução. Em seguida, verifique se o **instalação de dispositivo** detalhes estão conforme esperado e que a nossa aplicação pode agora receber notificações push.  

### <a name="verify-the-device-installation"></a>Verificar a instalação de dispositivo

Agora, pode fazer o mesmo pedido como fez antes usando **Postman** para [verificar o token SAS](#verify-the-sas-token). Supondo que o token SAS ainda não tiver expirado, a resposta deve incluir agora os detalhes de instalação fornecidos, como as etiquetas e modelos.

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

É a maneira mais rápida que agora pode receber notificações de teste navegar para o hub de notificação no portal do Azure:

1. No portal do Azure, navegue para o **descrição geral** separador no seu hub de notificação.

1. Selecione **testar envio**, que se encontra acima do **Essentials** resumo no canto superior esquerdo da janela do portal:

    ![Essentials de Hubs de notificação botão de envio de teste de resumo](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/hub-essentials-test-send.png)

1. Escolher **modelo personalizado** partir do **plataformas** lista.

1. Introduza **12345** para o **enviar para expressão de etiqueta**. Anteriormente tivesse especificado esta etiqueta na nossa instalação.

1. Opcionalmente, editar a **mensagem** no payload de JSON:

    ![Envio de teste de Hubs de notificação](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/hub-test-send.png)

1. Selecione **Enviar**. O portal deve indicar se a notificação foi enviada com êxito para o dispositivo:

    ![Teste de Hubs de notificação enviar resultados](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/hub-test-send-result.png)

    Supondo que a aplicação não está em execução em primeiro plano, também deverá ver uma notificação no **Centro de notificações** no seu dispositivo. Tocar na notificação, deve abrir a aplicação e mostrar o alerta.

    ![Exemplo de recebida a notificação](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/test-send-notification-received.png)

### <a name="send-a-test-notification-mail-carrier"></a>Enviar uma notificação de teste (operadora de correio)

Pode enviar notificações através da [REST API](/rest/api/notificationhubs/) utilizando **Postman**, que pode ser uma forma mais conveniente para testar.

1. Abra um novo separador no **Postman**.

1. Defina o pedido para **POST**e introduza o seguinte endereço:

    ```xml
    https://<namespace>.servicebus.windows.net/<hubName>/messages/?api-version=2015-01
    ```

1. Configure os cabeçalhos de pedido da seguinte forma:

   | Chave                            | Value                          |
   | ------------------------------ | ------------------------------ |
   | Content-Type                   | application/json;charset=utf-8 |
   | Autorização                  | <sasToken>                     |
   | ServiceBusNotification-Format  | Modelo                       |
   | Tags                           | "12345"                        |

1. Configurar a solicitação **corpo** a utilizar **matérias - JSON (application.json)** com o payload JSON seguinte:

    ```json
    {
       "message" : "Hello from Postman!"
    }
    ```

1. Selecione o **código** botão, que está sob a **guardar** botão no canto superior direito da janela. O pedido deve ter um aspeto semelhante ao seguinte exemplo:

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

1. Selecione o **enviar** botão.

Deve obter um código de estado de êxito e receber a notificação no dispositivo cliente.

## <a name="next-steps"></a>Passos Seguintes
Agora tem uma aplicação Swift de iOS básica ligada a um hub de notificação através do [REST API](/rest/api/notificationhubs/) e pode enviar e receber notificações. Para obter mais informações, veja os artigos seguintes:

- [Descrição geral dos Hubs de notificação do Azure](notification-hubs-push-notification-overview.md)
- [REST APIs dos Hubs de notificação](/rest/api/notificationhubs/)
- [SDK dos Notification Hubs para operações de back-end](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)
- [Hubs de notificação SDK no GitHub](https://github.com/Azure/azure-notificationhubs)
- [Registar com o back-end de aplicação](notification-hubs-ios-aspnet-register-user-from-backend-to-push-notification.md)
- [Gestão de registos](notification-hubs-push-notification-registration-management.md)
- [Trabalhar com etiquetas](notification-hubs-tags-segment-push-message.md) 
- [Trabalhar com modelos personalizados](notification-hubs-templates-cross-platform-push-messages.md)
- [Controlo de acesso do Service Bus com assinaturas de acesso partilhado](../service-bus-messaging/service-bus-sas.md)
- [Por meio de programação gerar tokens SAS](/rest/api/eventhub/generate-sas-token)
- [Segurança da Apple: crypto comuns](https://developer.apple.com/security/)
- [Hora "Epoch" do UNIX](https://en.wikipedia.org/wiki/Unix_time)
- [HMAC](https://en.wikipedia.org/wiki/HMAC)
