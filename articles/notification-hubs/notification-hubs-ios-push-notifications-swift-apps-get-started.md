---
title: Notificações push para aplicações de Swift iOS com Notification Hubs do Azure | Documentos da Microsoft
description: Saiba como notificações push para aplicações de Swift iOS com Notification Hubs do Azure.
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
ms.openlocfilehash: 64a05c6cbd412953ae10b31efc1d141c9cefd062
ms.sourcegitcommit: cfbc8db6a3e3744062a533803e664ccee19f6d63
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/21/2019
ms.locfileid: "65994770"
---
# <a name="tutorial-push-notifications-to-swift-ios-apps-using-the-notification-hubs-rest-api"></a>Tutorial: Notificações push para aplicações de Swift iOS através da API de REST de Hubs de notificação

> [!div class="op_single_selector"]
> * [Objective-C](notification-hubs-ios-apple-push-notification-apns-get-started.md)
> * [Swift](notification-hubs-ios-push-notifications-swift-apps-get-started.md)

Neste tutorial, vai utilizar Notification Hubs do Azure para enviar notificações push para um aplicativo baseado em Swift iOS usando os [REST API](/rest/api/notificationhubs/). Irá criar uma aplicação iOS em branco que recebe notificações push com o [Serviço Apple Push Notification (APNs)](https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html#//apple_ref/doc/uid/TP40008194-CH8-SW1).

Neste tutorial, siga os seguintes passos:

> [!div class="checklist"]
> * Gerar o ficheiro de pedido de assinatura de certificado
> * Pedir que a aplicação envie notificações push
> * Criar um perfil de aprovisionamento para a aplicação
> * Criar um hub de notificação
> * Configurar o hub de notificação com informações do APNS
> * Ligar a aplicação iOS aos Hubs de Notificação
> * Testar a solução

## <a name="prerequisites"></a>Pré-requisitos
Para acompanhar, terá de:

- Leia o artigo [descrição geral dos Hubs de notificação do Azure](notification-hubs-push-notification-overview.md) se não estiver familiarizado com o serviço. 
- Saiba mais sobre a instalação e de registos: [Gestão de registos](notification-hubs-push-notification-registration-management.md)
- Um ativo [conta de programador da Apple](https://developer.apple.com) 
- Um Mac com Xcode, juntamente com um certificado de programador válido instalado na Keychain
- Um dispositivo iPhone físico pode executar e depurar com (não é possível testar as notificações push com o simulador)
- O dispositivo iPhone físico registado no [Apple Portal](https://developer.apple.com) e associadas com o certificado
- Uma [subscrição do Azure](https://portal.azure.com) onde pode criar e gerir recursos

Deve ser possível em seguir adiante para criar este exemplo primeiro princípios sem nenhuma experiência anterior. No entanto, será benéfica familiaridade com os seguintes conceitos:

- Criar aplicações de iOS com Xcode e Swift
- Configurar uma [Hub de notificação do Azure](notification-hubs-ios-apple-push-notification-apns-get-started.md) para iOS
- Familiaridade com o [Portal do programador da Apple](https://developer.apple.com) e o [portal do Azure](https://portal.azure.com)

> [!NOTE]
> O hub de notificação será configurado para utilizar o *Sandbox* apenas o modo de autenticação. Não deve usar este modo de autenticação para cargas de trabalho de produção.

[!INCLUDE [Notification Hubs Enable Apple Push Notifications](../../includes/notification-hubs-enable-apple-push-notifications.md)]

## <a name="connect-your-ios-app-to-notification-hubs"></a>Ligar a aplicação iOS aos Notification Hubs
Nesta secção, irá criar a aplicação iOS que irá ligar ao hub de notificação.  

### <a name="create-an-ios-project"></a>Crie um projeto do iOS
1. Na **Xcode**, crie um novo projeto do iOS e selecione o **aplicação de vista única** modelo.
2. Quando definir as opções para o novo projeto, siga estes passos:
    1. Utilizar o mesmo **nome do produto** (ou seja, **PushDemo**) e **identificador de organização** (ou seja, `com.<organization>`) que utilizou quando o **pacote Identificador** foi definido no **Portal de programador Apple**. 
    2. Escolha o **equipe** que o **ID de aplicação** foi definido para a cópia de segurança.
    3. Definir o **linguagem** ao **Swift**.
    4. Clique em **Seguinte**
3. Criar uma nova pasta chamada **SupportingFiles**.
4. Criar uma nova **plist** arquivo chamado **devsettings.plist** sob o **SupportingFiles** pasta. Certifique-se de que adicionar esta pasta à sua **gitignore** ficheiro, para que ele não está consolidado ao trabalhar com um **repositório de git**. Num aplicativo de produção, iria provavelmente ser condicionalmente configuração estes segredos como parte de um processo automatizado de compilação. No entanto, não é abrangido como parte destas instruções.
5. Atualização **devsettings.plist** para incluir as seguintes entradas de configuração (utilizando os seus próprios valores a **Hub de notificação** aprovisionado):

   | Chave                            | Type                     | Value                     |               
   |--------------------------------| -------------------------| --------------------------|
   | notificationHubKey             | String                   | \<hubKey\>                |
   | notificationHubKeyName         | String                   | \<hubKeyName\>            |
   | notificationHubName            | String                   | \<hubName\>               |
   | notificationHubNamespace       | String                   | \<hubNamespace\>          |
    
   Pode encontrar os valores necessários ao navegar para o **Hub de notificação** recurso no **portal do Azure**. Pode encontrar o **notificationHubName** e o **notificationHubNamespace** valores no canto superior direito do **Essentials** resumo dentro do  **Descrição geral** página.

   ![Resumo de Essentials de Hubs de notificação](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/hub-essentials.png)

   Pode encontrar os **notificationHubKeyName** e **notificationHubKey** valores ao navegar para o **políticas de acesso**, clicando nos respetivos **acesso Política**. Por exemplo, `DefaultFullSharedAccessSignature`. Em seguida, copiar do **cadeia de ligação primária** o valor com o prefixo `SharedAccessKeyName=` para `notificationHubKeyName` e o valor com o prefixo `SharedAccessKey=` para o `notificationHubKey`. A cadeia de ligação deve estar no seguinte formato:
    
   ```xml
   Endpoint=sb://<namespace>.servicebus.windows.net/;SharedAccessKeyName=<notificationHubKeyName>;SharedAccessKey=<notificationHubKey>
   ```

   Para manter simples, usará *DefaultFullSharedAccessSignature* pelo que pode utilizar o token para enviar notificações também. No entanto, na prática a `DefaultListenSharedAccessSignature` seria uma opção melhor para situações em que apenas pretende receber notificações.       
6. Sob **navegador de projetos**, clique no **nome do projeto**, em seguida, clique no **geral** separador
7. Encontrar **identidade**, em seguida, defina o **identificador do pacote** valor para que ele corresponde ao utilizado para o **ID da aplicação** (a partir do passo anterior) ou seja, `'com.<organization>.PushDemo'`
8. Encontrar **assinatura**, em seguida, certifique-se de que seleciona o adequado **equipe** para sua **conta de programador da Apple** (aquele em que tivesse criado os certificados e perfis de anterior).  **Xcode** deve obter automaticamente o adequado **perfil de aprovisionamento** com base no **identificador de pacote**. Se não vir o novo **perfil de aprovisionamento**, tente atualizar os perfis para o **identidade de assinatura** (*Xcode > Preferências > conta > Ver detalhes*). Clicar no **identidade de assinatura**, em seguida, clicar no **atualizar** botão no canto inferior direito deve transferir os perfis.
9. Selecione o **capacidades** separador e certifique-se de que **notificações push** estão ativadas.
10. Abra sua **Appdelegate** ficheiro para implementar a *UNUserNotificationCenterDelegate* de protocolo e adicione o seguinte código na parte superior da classe:
    
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

    Usará esses membros mais tarde. O *etiquetas* e *genericTemplate* será utilizado como parte do registo. Para obter mais informações sobre etiquetas, consulte [etiquetas para registos](notification-hubs-tags-segment-push-message.md) e [registos de modelos](notification-hubs-templates-cross-platform-push-messages.md).
 
11. No mesmo ficheiro, adicione o seguinte código no *didFinishLaunchingWithOptions* função:

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

    Este código recupera os valores de configuração do **devsettings.plist**, define a **AppDelegate** classe como o *UNUserNotificationCenter* delegar, autorização de solicitações para notificações push e, em seguida, chama *registerForRemoteNotifications*.
    
    Para simplificar, o código suporta **iOS 10 e superior apenas**. Pode adicionar o suporte para versões mais antigas do sistema operacional, condicionalmente utilizando as respetivas APIs e abordagens, como faria normalmente.

12. No mesmo ficheiro, adicione as seguintes funções:

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

    O código utiliza a *installationId* e *pushChannel* valores para registar com o **Hub de notificação**. Neste caso, está usando *UIDevice.current.identifierForVendor* nos forneça um valor exclusivo para identificar o dispositivo e, em seguida, formatar os *deviceToken* nos forneça a pretendido *pushChannel* valor. O *showAlert* função é simplesmente exibirá algum texto de mensagem para fins de demonstração.

13. Ainda na **Appdelegate**, adicione o *willPresent* e *didReceive* **UNUserNotificationCenterDelegate** funciona para apresentar um alerta quando a notificação chegasse quando a aplicação está em execução em primeiro e segundo plano, respetivamente
    
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

14. Adicionar declarações de impressa para o fim dos *didRegisterForRemoteNotificationsWithDeviceToken* função para verificar se *installationId* e *pushChannel* estão a ser valores atribuídos
15. Criar pastas (**modelos**, **serviços**, e **utilitários**) para os componentes básicos, adicionará ao projeto mais tarde
16. Verifique que o projeto é compilada e executada num dispositivo físico (push notificações não não possível testar o uso do simulador)

### <a name="create-models"></a>Criar modelos
Neste passo, irá criar um conjunto de modelos para representar a [API de REST dos Hubs de notificação](/rest/api/notificationhubs/) payloads e para armazenar o necessária **SAS token** dados.


1.  Adicione um novo ficheiro swift para o **modelos** chamado **PushTemplate.swift**. Esse modelo proporciona uma estrutura que representa a **corpo** de um modelo individual como parte do **DeviceInstallation** payload:
    
    ```swift
    import Foundation

    struct PushTemplate : Codable {
        let body : String
    
        init(withBody body : String) {
            self.body = body
        }
    }
    ```

2. Adicione um novo ficheiro swift para o **modelos** com o nome de pasta **DeviceInstallation.swift**. Esse arquivo define uma estrutura que representa o payload para criar ou atualizar uma **instalação de dispositivo**. Adicione o seguinte código para o ficheiro:
    
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

3.  Adicione um novo ficheiro swift sob **modelos** chamado **TokenData.swift**. Este modelo irá ser utilizado para armazenar uma **SAS token** juntamente com sua expiração

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
**Os Hubs de notificação** utilizam a mesma infraestrutura de segurança como **do Azure Service Bus**. Para chamar a API REST, precisará [programaticamente gerar um token SAS](/rest/api/eventhub/generate-sas-token) que podem ser utilizados no **autorização** cabeçalho do pedido.  

O token resultante estará no seguinte formato: 

```xml
SharedAccessSignature sig=<UrlEncodedSignature>&se=<ExpiryEpoch>&skn=<KeyName>&sr=<UrlEncodedResourceUri>
```

O próprio processo envolve os seis passos principais:  

1.  Computação de expiração no [hora "Epoch" de UNIX](https://en.wikipedia.org/wiki/Unix_time) formato (segundos decorridos desde 1 de Janeiro de 1970 do 00:00:00 UTC)
2.  Formatação da **ResourceUrl** (que representa o recurso está a tentar aceder, ou seja, `'https://\<namespace\>.servicebus.windows.net/\<hubName\>'`), portanto, é codificada de percentagem e minúsculas
3.  A preparar a **StringToSign**, que é composto por `'\<**UrlEncodedResourceUrl**\>\n\<**ExpiryEpoch**\>'`
4.  Computação (e a codificação de 64 base) a **assinatura** utilizando o **HMAC-SHA256** do **StringToSign** valor com o **chave** parte do **Cadeia de ligação** (para o respectivo **regra de autorização**)
5.  Formatação de base 64 codificada **assinatura** , portanto, é codificado por cento
6.  Construir a **token** no formato esperado com o **UrlEncodedSignature**, **ExpiryEpoch**, **KeyName**e o **UrlEncodedResourceUrl** valores

Consulte a [documentação do Azure Service Bus](../service-bus-messaging/service-bus-sas.md) para obter uma descrição mais completa de **assinatura de acesso partilhado** e como são utilizadas pelo **Azure Service Bus** e  **Os Hubs de notificação**.

Para efeitos deste exemplo rápido, vai utilizar de código aberto da Apple **CommonCrypto** biblioteca para ajudar com o hash da assinatura. Como é uma biblioteca de C, não é acessível em Swift out-of-the-box. No entanto, pode fazer isso usando um cabeçalho de bridging disponíveis. Para adicionar e configurar o cabeçalho de bridging:

1. Na **Xcode**, aceda a **ficheiro**, em seguida, **New**, em seguida, **ficheiro** e selecione **arquivo de cabeçalho** nomeando- *'BridgingHeader.h'*
2. Editar o ficheiro a importar **CommonHMAC**

    ```swift
    #import <CommonCrypto/CommonHMAC.h>

    #ifndef BridgingHeader_h
    #define BridgingHeader_h


    #endif /* BridgingHeader_h */
    ```

3. Atualizar o destino **definições de criação** para referenciar o cabeçalho de bridging. Abra o **definições de criação** separador e desloque para baixo para o **compilador Swift** secção. Certifique-se de que o **instalar o cabeçalho de compatibilidade de Objective-C** opção definida como **Sim** e introduza o caminho do ficheiro para o nosso cabeçalho de bridging para o **cabeçalho de bridging Objective-C**   opção ou seja `'\<ProjectName\>/BridgingHeader.h'`. Se não conseguir localizar estas opções, certifique-se de que tem o **todos os** vista selecionada (em vez **básica** ou **personalizado**).
    
   Existem muitas wrapper de código-fonte aberto de terceiros bibliotecas disponíveis, que podem fazer usando **CommonCrypto** um pouco mais fácil. Ele está além do escopo deste artigo.

4. Adicione um novo ficheiro Swift sob o **utilitários** pasta chamada **TokenUtility.swift** e adicione o seguinte código:

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
    
    Esse utilitário encapsula a lógica responsável por gerar o **SAS token**. O *getSasToken* função organiza as etapas de alto nível necessárias para preparar o token, conforme descrito anteriormente e será chamada pelo serviço de instalação nos passos mais tarde neste tutorial. As outras duas funções são chamadas pela *getSasToken função*; *sha256HMac* para a assinatura de computação e *urlEncodedString* para codificar a respetiva cadeia de Url. O *urlEncodedString* função era necessária, pois não foi possível obter o resultado necessário utilizar a *addingPercentEncoding* função. O [SDK iOS do armazenamento do Azure](https://github.com/Azure/azure-storage-ios/blob/master/Lib/Azure%20Storage%20Client%20Library/Azure%20Storage%20Client%20Library/AZSUtil.m) serviu como um excelente exemplo de como abordar essas operações, embora no Objective-C. Obter mais informações sobre **tokens SAS de barramento de serviço do Azure** podem ser encontradas no [documentação do Azure Service Bus](../service-bus-messaging/service-bus-sas.md). 

### <a name="verify-the-sas-token"></a>Verifique se o token SAS
Antes de implementar o serviço de instalação no cliente, pode verificar se a aplicação está a gerar corretamente os **SAS token** usando o utilitário de http à escolha. Para efeitos desta mensagem, nossa ferramenta de escolha será **Postman**.

Tome nota dos *installationId* e o *token* valores a ser gerados pela aplicação utilizando um adequadamente colocá-la imprimir instrução ou o ponto de interrupção. 

Siga estes passos para chamar o *instalações* API:

1. Na **Postman**, abra um novo separador
2. Defina o pedido para **obter** e o seguinte endereço:

    ```xml
    https://<namespace>.servicebus.windows.net/<hubName>/installations/<installationId>?api-version=2015-01
    ```

3. Configure os cabeçalhos de pedido da seguinte forma:
    
   | Chave           | Value            |
   | ------------- | ---------------- |
   | Content-Type  | application/json |
   | Autorização | \<sasToken\>     |
   | x-ms-version  | 2015-01          |

4. Clique nas **código** botão (superior-direito, sob o **guardar** botão). O pedido deve ter um aspeto semelhante ao exemplo abaixo:

    ```html
    GET /<hubName>/installations/<installationId>?api-version=2015-01 HTTP/1.1
    Host: <namespace>.servicebus.windows.net
    Content-Type: application/json
    Authorization: <sasToken>
    x-ms-version: 2015-01
    Cache-Control: no-cache
    Postman-Token: <postmanToken>
    ```

5. Clique nas **enviar** botão

Não existe nenhum registo para especificado *installationId* nesse ponto, no entanto, deve resultar num **404 não encontrado** resposta vez **401 não autorizado**. Este resultado deve confirmar que o **SAS token** foi aceite.

### <a name="implement-the-installation-service-class"></a>Implementar a classe de serviço de instalação
Em seguida implementará nosso básica wrapper em torno da [API do REST de instalações](/rest/api/notificationhubs/create-overwrite-installation).  

Adicione um novo ficheiro Swift no **serviços** pasta chamada **NotificationRegistrationService.swift**, em seguida, adicione o seguinte código para este ficheiro:

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
 
Os detalhes necessários são fornecidos como parte da inicialização. Etiquetas e modelos, opcionalmente, serão passados para o *registar* função para a parte do formulário a **instalação de dispositivo** JSON payload.  

O *registar* função chama as outras funções privadas para preparar o pedido. Assim que for recebida uma resposta, a conclusão denomina-se que indica se o registo foi concluída com êxito ou não.  

O ponto de extremidade do pedido é construído à *getBaseAddress* funcionar com o **Hub de notificação** parâmetros **espaço de nomes** e **nome**fornecidas durante a inicialização.  

O *getSasToken* função irá verificar se o token atualmente armazenado é válido, caso contrário, ele irá chamar os **TokenUtility** para gerar um novo e armazená-lo antes de retornar um valor. 

Por fim as *encodeToJson* vai converter os objetos do respetivo modelo em JSON para utilização como parte do corpo do pedido.

### <a name="invoke-the-notification-hubs-rest-api"></a>Invocar os REST API dos Hubs de notificação
A última etapa é atualizar **AppDelegate** para utilizar o **NotifiationRegistrationService** para registar com o nosso **NotificationHub**. 

1. Open **Appdelegate** e adicione uma variável de nível de classe para armazenar uma referência para o **NoficiationRegistrationService**:

    ```swift
    var registrationService : NotificationRegistrationService?
    ```

2. No mesmo ficheiro, atualize o *didRegisterForRemoteNotificationsWithDeviceToken* função para inicializar o **NotificationRegistrationService** com os parâmetros necessários, em seguida, chame o *registar* função.

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
    
        // Call register passing in the tags and template parameters
        self.registrationService!.register(withTags: tags, andTemplates: ["genericTemplate" : self.genericTemplate]) { (result) in
            if !result {
                print("Registration issue")
            } else {
                print("Registered")
            }
        }
    }
    ```

    Para simplificar, vamos usar algumas instruções de impressa para atualizar a janela de saída com o resultado do *registar* operação. 

3. Agora, crie e execute a aplicação (num dispositivo físico). Verá **"Registered"** na janela de saída.

## <a name="test-the-solution"></a>Testar a solução
Nesta fase, nosso aplicativo está registrado com **NotificationHub** e pode receber notificações push. Na **Xcode**, parar o depurador e fechar a aplicação (se estiver em execução). Em seguida, irá verificar que o **instalação de dispositivo** detalhes estão conforme esperado e que nosso aplicativo agora, de fato, pode receber notificações push.  

### <a name="verify-the-device-installation"></a>Verificar a instalação de dispositivo
Agora, pode fazer o mesmo pedido como fez anteriormente usando **Postman** para [verificar o token SAS](#verify-the-sas-token). Supondo que o **SAS token** não tenha expirado, a resposta deve incluir agora os detalhes de instalação fornecidos, como os modelos e as etiquetas.  

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
A maneira mais rápida que agora pode receber notificações de teste é navegar para o **Hub de notificação** no **portal do Azure**.

1. Na **portal do Azure**, navegue para o **descrição geral** separador no seu **Hub de notificação**
2. Clique em **testar envio** (canto superior esquerdo) acima a **Essentials** resumo

    ![Essentials de Hubs de notificação botão de envio de teste de resumo](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/hub-essentials-test-send.png)
3. Escolher **modelo personalizado** na lista de **plataformas**
4. Introduza **12345** para o **enviar para expressão de etiqueta** (tivesse especificado esta etiqueta na nossa instalação)
5. Opcionalmente, editar a **mensagem** no payload de JSON
    
    ![Envio de teste de Hubs de notificação](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/hub-test-send.png)
6. Clique em **enviar** e o portal deve indicar se a notificação foi enviada para o dispositivo com êxito

    ![Teste de Hubs de notificação enviar resultados](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/hub-test-send-result.png)

    Também verá uma notificação no **Centro de notificações** no seu dispositivo (partindo do princípio de que a aplicação não está em execução em primeiro plano). Tocar na notificação deve abrir a aplicação e mostrar o alerta.

    ![Exemplo de recebida a notificação](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/test-send-notification-received.png)

### <a name="send-a-test-notification-postman"></a>Enviar uma notificação de teste (Postman)
Pode enviar notificações através de respetivos [REST API](/rest/api/notificationhubs/) via **Postman** como bem e podem ser uma forma mais conveniente para testar. 

1. Na **Postman**, abra um novo separador
2. Defina o pedido para **POST** e introduza o seguinte endereço:

    ```xml
    https://<namespace>.servicebus.windows.net/<hubName>/messages/?api-version=2015-01
    ```

3. Configure os cabeçalhos de pedido da seguinte forma:
    
   | Chave                            | Value                          |
   | ------------------------------ | ------------------------------ |
   | Content-Type                   | application/json;charset=utf-8 |
   | Autorização                  | \<sasToken\>                   |
   | ServiceBusNotification-Format  | modelo                       |
   | Tags                           | "12345"                        |

4. Configurar a solicitação **corpo** a utilizar **matérias - JSON (application.json)** com o payload JSON seguinte:

    ```json
    {
       "message" : "Hello from Postman!"
    }
    ```

5. Clique nas **código** botão (superior-direito, sob o **guardar** botão). O pedido deve ter um aspeto semelhante ao exemplo abaixo:

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

5. Clique nas **enviar** botão

Deve obter um código de estado de êxito e receber a notificação no dispositivo cliente.

## <a name="next-steps"></a>Passos Seguintes
Tem agora uma aplicação Swift de básica iOS ligada a uma **Hub de notificação** através da [REST API](/rest/api/notificationhubs/) e pode enviar e receber notificações. Para obter mais informações, veja os artigos seguintes: 

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
