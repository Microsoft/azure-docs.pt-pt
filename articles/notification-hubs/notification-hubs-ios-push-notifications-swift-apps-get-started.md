---
title: Envie notificações push às aplicações Swift iOS que utilizam hubs de notificação do Azure Microsoft Docs
description: Saiba como pressionar notificações a aplicações swift iOS que utilizam hubs de notificação do Azure.
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
ms.openlocfilehash: a721c519c7a836e20455c6f1887bcfa7b52951f3
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80336645"
---
# <a name="tutorial-send-push-notifications-to-swift-ios-apps-using-notification-hubs-rest-api"></a>Tutorial: Enviar notificações push para aplicações swift iOS usando Centros de Notificação REST API

> [!div class="op_single_selector"]
> * [Objective-C](notification-hubs-ios-apple-push-notification-apns-get-started.md)
> * [Swift](notification-hubs-ios-push-notifications-swift-apps-get-started.md)

Neste tutorial, utiliza hubs de notificação Do Azure para empurrar notificações para uma aplicação iOS baseada em Swift utilizando a [API REST](/rest/api/notificationhubs/). Também cria uma aplicação iOS em branco que recebe notificações push utilizando o [serviço de Notificação Push Apple (APNs)](https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html#//apple_ref/doc/uid/TP40008194-CH8-SW1).

Este tutorial leva-o através dos seguintes passos:

> [!div class="checklist"]
> * Gere o ficheiro de pedido de assinatura de certificado.
> * Solicite a sua aplicação para notificações push.
> * Crie um perfil de provisionamento para a aplicação.
> * Criar um hub de notificação.
> * Configure o centro de notificação com informações apns.
> * Ligue a sua aplicação iOS a um centro de notificação.
> * Teste a solução.

O código completo para este tutorial pode ser encontrado [no GitHub](https://github.com/xamcat/mobcat-samples/tree/master/notification_hub_rest).

## <a name="prerequisites"></a>Pré-requisitos

Para acompanhar, precisa de:

- Para ver o [Azure Notification Hubs](notification-hubs-push-notification-overview.md) se não estiver familiarizado com o serviço.
- Para conhecer [registos e instalação.](notification-hubs-push-notification-registration-management.md)
- Uma conta ativa do desenvolvedor da [Apple.](https://developer.apple.com)
- Um Mac executando Xcode juntamente com um certificado de desenvolvimento válido instalado no seu Keychain.
- Um dispositivo físico para iPhone com o qual pode executar e depurar, uma vez que não pode testar notificações push com o simulador.
- O seu dispositivo físico iPhone registado no Portal da [Apple](https://developer.apple.com) e associado ao seu certificado.
- Uma [subscrição Azure](https://portal.azure.com) onde pode criar e gerir recursos.

Mesmo que não tenha experiência prévia com o desenvolvimento do iOS, deve ser capaz de seguir os passos para criar este exemplo de primeiros princípios. No entanto, beneficiará da familiaridade com os seguintes conceitos:

- Construindo aplicativos iOS com Xcode e Swift.
- Configurar um hub de [notificação Azure](notification-hubs-ios-apple-push-notification-apns-get-started.md) para iOS.
- O [Portal de Desenvolvimento](https://developer.apple.com) da Apple e o portal [Azure.](https://portal.azure.com)

> [!NOTE]
> O centro de notificação será configurado apenas para utilizar o modo de autenticação **Sandbox.** Não deve utilizar este modo de autenticação para cargas de trabalho de produção.

[!INCLUDE [Notification Hubs Enable Apple Push Notifications](../../includes/notification-hubs-enable-apple-push-notifications.md)]

## <a name="connect-your-ios-app-to-a-notification-hub"></a>Ligue a sua aplicação iOS a um centro de notificação

Nesta secção, irá construir a aplicação iOS que se ligará ao centro de notificação.  

### <a name="create-an-ios-project"></a>Criar um projeto iOS

1. No Xcode, crie um novo projeto do iOS e selecione o modelo **Aplicação de Vista Única**.

1. Ao definir as opções para o novo projeto:

   1. Especifique o Nome do **Produto** (PushDemo) e **o Identificador** de Organização ()`com.<organization>`que utilizou quando definiu o **Identificador de Pacote** no Portal do Desenvolvedor da Apple.

   1. Escolha a Equipa para a **qual** o ID da **aplicação** foi configurado.

   1. Desloque o **idioma** a **Swift.**

   1. Selecione **Seguinte**.

1. Crie uma nova pasta chamada **Ficheiros de Suporte**.

1. Crie um novo ficheiro de lista p chamado **devsettings.plist** na pasta **'Ficheiros de Suporte'.** Certifique-se de adicionar esta pasta ao seu ficheiro **gitignore** para que não seja comprometida ao trabalhar com um repo git. Numa aplicação de produção, provavelmente estaria a definir estes segredos condicionalmente como parte de um processo de construção automatizado. Tais configurações não estão cobertas por esta passagem.

1. Atualize **devsettings.plist** para incluir as seguintes entradas de configuração utilizando os seus próprios valores a partir do centro de notificação que forprovisionou:

   | Chave                            | Tipo                     | Valor                     |
   |--------------------------------| -------------------------| --------------------------|
   | notificaçãoHubKey             | String                   | \<hubKey>                  |
   | notificaçãoHubKeyName         | String                   | \<> hubKeyName              |
   | notificaçãoHubName            | String                   | \<> hubName                 |
   | notificationHubNamespace       | String                   | \<hubNamespace>            |

   Pode encontrar os valores necessários navegando para o recurso hub de notificação no portal Azure. Em particular, os valores do HubName e da **notificação HubNamespace** estão no canto superior direito do resumo **essencial** dentro da página **'Visão Geral'.** **notificationHubName**

   ![Resumo do Essencial dos Centros de Notificação](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/hub-essentials.png)

   Também pode encontrar os valores **hubKeyName** e **notificationHubKey,** navegando para Políticas de `DefaultFullSharedAccessSignature` **Acesso** e selecionando a respetiva Política de **Acesso**, como . Depois disso, copie a partir da Cadeia `SharedAccessKeyName=` `notificationHubKeyName` de **Ligação** Primária `SharedAccessKey=` com o valor pré-fixado e o valor prefixado para o `notificationHubKey`.

   A cadeia de ligação deve estar no seguinte formato:

   ```xml
   Endpoint=sb://<namespace>.servicebus.windows.net/;SharedAccessKeyName=<notificationHubKeyName>;SharedAccessKey=<notificationHubKey>
   ```

   Para mantê-lo `DefaultFullSharedAccessSignature` simples, especifique para que possa usar o símbolo para enviar notificações. Na prática, seria `DefaultListenSharedAccessSignature` uma escolha melhor para situações em que apenas deseja receber notificações.

1. Em **Project Navigator,** selecione o Nome do **Projeto** e, em seguida, selecione o separador **Geral.**

1. Encontre **identidade** e, em seguida, defino o valor do **Identificador de Pacote** para que corresponda `com.<organization>.PushDemo`, que é o valor utilizado para o ID da **app** de um passo anterior.

1. Encontre **a assinatura & Capacidades**, e, em seguida, selecione a **equipa** apropriada para a sua Conta de Desenvolvimento da **Apple**. O valor **da Equipa** deve corresponder ao que criou os seus certificados e perfis.

1. O Xcode deve retirar automaticamente o valor de perfil de **provisionamento** adequado com base no **identificador de pacote**. Se não vir o novo valor do Perfil de **Provisionamento,** tente refrescar os perfis para a **Identidade de Assinatura** selecionando a**Conta** de**Preferências** > do **Xcode** > e, em seguida, selecione o botão de **Descarregamento de Perfis Manuais** para descarregar os perfis.

1. Ainda no separador **De Sincronização & Capacidades,** clique no botão **+ Capability** e toque duas vezes nas **Notificações push** da lista para garantir que as **Notificações push** estão ativadas.

1. Abra o seu ficheiro **AppDelegate.swift** para implementar o protocolo **UNUserNotificationCenterDelegate** e adicione o seguinte código ao topo da classe:

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
        
        ...
    }
    ```

    Usará estes membros mais tarde. Especificamente, utilizará o membro das **etiquetas** como parte do registo utilizando um **modelo personalizado**. Para obter mais informações sobre etiquetas, consulte [etiquetas para registos](notification-hubs-tags-segment-push-message.md) e [registos de modelos.](notification-hubs-templates-cross-platform-push-messages.md)

1. No mesmo ficheiro, adicione o seguinte código à função **didFinishLaunchingWithOptions:**

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

    Este código recupera os valores de definição a partir de **devsettings.plist,** define a classe **AppDelegate** como delegado **do UNUserNotificationCenter,** solicita autorização para notificações push e, em seguida, liga **para registroForRemoteNotifications**.

    Para mantê-lo simples, o código suporta o *iOS 10 e mais tarde apenas*. Pode adicionar suporte para versões anteriores de Os utilizando condicionalmente as respetivas APIs e abordagens como normalmente faria.

1. No mesmo ficheiro, adicione as seguintes funções:

    ```swift
    func application(_ application: UIApplication, didRegisterForRemoteNotificationsWithDeviceToken deviceToken: Data) {
        let installationId = (UIDevice.current.identifierForVendor?.description)!
        let pushChannel = deviceToken.reduce("", {$0 + String(format: "%02X", $1)})
    }

    func showAlert(withText text : String) {
        let alertController = UIAlertController(title: "PushDemo", message: text, preferredStyle: UIAlertControllerStyle.alert)
        alertController.addAction(UIAlertAction(title: "OK", style: UIAlertActionStyle.default,handler: nil))
        let keyWindow = UIApplication.shared.windows.filter {$0.isKeyWindow}.first
        keyWindow?.rootViewController?.present(alertController, animated: true, completion: nil)
    }
    ```

    O código utiliza os valores de **instalaçãoId** e **pushChannel** para registar-se com o centro de notificação. Neste caso, está a utilizar **o UIDevice.current.identifierForVendor** para fornecer um valor único para identificar o dispositivo e, em seguida, formar o **dispositivoToken** para fornecer o valor desejado para o **pushChannel.** A função **showAlert** existe simplesmente para exibir algum texto de mensagem para fins de demonstração.

1. Ainda no ficheiro **AppDelegate.swift,** adicione o **willPresent** e **didReceive** funções para **UNUserNotificationCenterDelegate**. Estas funções mostram um alerta quando são notificadas de que uma aplicação está a funcionar em primeiro plano ou em segundo plano, respectivamente.

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

1. Adicione declarações de impressão na parte inferior da função **didRegisterForRemoteNotificationsWithDeviceToken** para verificar se a **instalaçãoId** e **pushChannel** estão a ser atribuídos valores.

    ```swift
    print(installationId)
    print(pushChannel)
    ```

1. Crie as pastas **Models,** **Serviços**e **Utilities** para os componentes fundamentais que irá adicionar ao projeto mais tarde.

1. Verifique se o projeto constrói e funciona com um dispositivo físico. As notificações push não podem ser testadas utilizando o simulador.

### <a name="create-models"></a>Criar modelos

Neste passo, irá criar um conjunto de modelos para representar as cargas da API do Centro de [Notificação](/rest/api/notificationhubs/) REST e para armazenar os dados de fichas de assinatura de acesso partilhado (SAS) necessários.

1. Adicione um novo ficheiro Swift chamado **PushTemplate.swift** à pasta **Models.** Este modelo fornece uma estrutura representando o **CORPO** de um modelo individual como parte da carga útil da **Instalação** de Dispositivos.

    ```swift
    import Foundation

    struct PushTemplate : Codable {
        let body : String

        init(withBody body : String) {
            self.body = body
        }
    }
    ```

1. Adicione um novo ficheiro Swift chamado **DeviceInstallation.swift** à pasta **Models.** Este ficheiro define uma estrutura que representa a carga útil para criar ou atualizar uma **instalação de dispositivo**. Adicione o seguinte código ao ficheiro:

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

1. Adicione um novo ficheiro Swift chamado **TokenData.swift** à pasta **Models.** Este modelo será utilizado para armazenar um token SAS juntamente com a sua expiração.

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

### <a name="generate-a-sas-token"></a>Gerar um símbolo SAS

Os Centros de Notificação utilizam a mesma infraestrutura de segurança que o Azure Service Bus. Para chamar a API REST, terá de [gerar programáticamente um token SAS](/rest/api/eventhub/generate-sas-token) que pode ser usado no cabeçalho de **Autorização** do pedido.  

O símbolo resultante será no seguinte formato:

```xml
SharedAccessSignature sig=<UrlEncodedSignature>&se=<ExpiryEpoch>&skn=<KeyName>&sr=<UrlEncodedResourceUri>
```

O processo em si envolve os mesmos seis passos-chave:  

1. Calcular a caducidade no formato [de tempo UNIX Epoch,](https://en.wikipedia.org/wiki/Unix_time) o que significa que o número de segundos decorridos desde a meia-noite Hora Universal Coordenada, 1 de janeiro de 1970.
1. Formando o **ResourceUrl** que representa o recurso a que está a tentar aceder, por isso está codificado por cento e minúsculo. O **ResourceUrl** tem `'https://<namespace>.servicebus.windows.net/<hubName>'`o formulário .
1. Preparação do **StringToSign**, `'<UrlEncodedResourceUrl>\n<ExpiryEpoch>'`que é formatado como .
1. Computação e base64 codificando a **Assinatura** utilizando o hash HMAC-SHA256 do valor **StringToSign.** O valor do hash é utilizado com a parte **chave** da cadeia de **ligação** para a respetiva **Regra de Autorização**.
1. Formando a **Assinatura** codificada base64 para que esteja codificada por cento.
1. Construindo o símbolo no formato esperado utilizando os valores **UrlEncodedSignature**, **ExpiryEpoch,** **KeyName**e **UrlEncodedResourceUrl.**

Consulte a documentação do [Azure Service Bus](../service-bus-messaging/service-bus-sas.md) para obter uma visão mais completa da assinatura de acesso partilhado e de como os Hubs de Ônibus e Notificação da Azure Service a utilizam.

Para efeitos deste exemplo Swift, vaiusar a biblioteca **CommonCrypto** de código aberto da Apple para ajudar no hashing da assinatura. Como é uma biblioteca C, não é acessível em Swift fora da caixa. Pode disponibilizar a biblioteca utilizando um cabeçalho de ponte.

Para adicionar e configurar o cabeçalho de ponte:

1. No Xcode, selecione **File** > **New** > **File** > Header**File**. Nomeie o ficheiro do cabeçalho **BridgingHeader.h**.

1. Editar o ficheiro para importar **CommonHMAC.h:**

    ```swift
    #import <CommonCrypto/CommonHMAC.h>

    #ifndef BridgingHeader_h
    #define BridgingHeader_h


    #endif /* BridgingHeader_h */
    ```

1. Atualize as **Definições** de Construção do Alvo para fazer referência ao cabeçalho de ponte:

   1. Toque no projeto **PushDemo** e desloque-se até à secção **Swift Compiler.**

   1. Certifique-se de que a opção de cabeçalho de **compatibilidade Objectivo-C** está definida para **Sim**.

   1. Introduza o `'<ProjectName>/BridgingHeader.h'` caminho do ficheiro na opção Cabeçalho de **Ponte Object-C.** Este é o caminho do arquivo para o nosso cabeçalho de ponte.

   Se não conseguir encontrar estas opções, certifique-se de que tem a visão **All** selecionada em vez de **Basic** ou **Customd**.

   Existem muitas bibliotecas de invólucros de código aberto de terceiros disponíveis que podem tornar a utilização do **CommonCrypto** um pouco mais fácil. No entanto, a discussão destas bibliotecas está fora do âmbito de aplicação deste artigo.

1. Adicione um novo ficheiro Swift chamado **TokenUtility.swift** dentro da pasta **Utilities** e adicione o seguinte código:

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

   Este utilitário engloba a lógica responsável pela geração do símbolo SAS.

   Como descrito anteriormente, a função **getSasToken** orquestra os passos de alto nível necessários para preparar o símbolo. A função será chamada pelo serviço de instalação mais tarde neste tutorial.

   As outras duas funções são chamadas pela função **getSasToken:** **sha256HMac** para computação da assinatura e **urlEncodedString** para codificar a cadeia de URL associada. A função **urlEncodedString** é necessária, uma vez que não é possível obter a saída necessária utilizando a função de adição incorporada **PercentEncoding.**

   O [Azure Storage iOS SDK](https://github.com/Azure/azure-storage-ios/blob/master/Lib/Azure%20Storage%20Client%20Library/Azure%20Storage%20Client%20Library/AZSUtil.m) é um excelente exemplo de como abordar estas operações no Objectivo-C. Mais informações sobre as fichas do Azure Service Bus SAS podem ser encontradas na documentação do [Azure Service Bus.](../service-bus-messaging/service-bus-sas.md)

1. Em **AppDelegate.swift**, adicione o seguinte código à função *didRegisterForRemoteNotificationsWithDeviceToken* para verificar se o **TokenUtility.getSasToken** está a gerar um token válido
    
    ```swift
    let baseAddress = "https://<notificaitonHubNamespace>.servicebus.windows.net/<notifiationHubName>"

    let tokenData = TokenUtility.getSasToken(forResourceUrl: baseAddress,
                                                withKeyName: self.notificationHubKeyName!,
                                                andKey: self.notificationHubKey!)
    
    print(tokenData.token)
    ```

    Certifique-se de substituir os valores do espaço reservado na cadeia **baseAddress** por si próprio

### <a name="verify-the-sas-token"></a>Verifique o token SAS

Antes de implementar o serviço de instalação no cliente, verifique se a nossa aplicação está a gerar corretamente o token SAS utilizando o seu utilitário http de eleição. Para efeitos deste tutorial, a nossa ferramenta de eleição será **o Carteiro.**

Tome nota das **instalaçõesId** **e** valores simbólicos gerados pela aplicação.

Siga estes passos para chamar as **instalações** API:

1. No **Carteiro,** abra um novo separador.

1. Definir o pedido para **OBTER** e especificar o seguinte endereço:

    ```xml
    https://<namespace>.servicebus.windows.net/<hubName>/installations/<installationId>?api-version=2015-01
    ```

1. Configure os cabeçalhos de pedido da seguinte forma:

   | Chave           | Valor            |
   | ------------- | ---------------- |
   | Content-Type  | application/json |
   | Autorização | \<sasToken>       |
   | x-ms-versão  | 2015-01          |

1. Selecione o botão **Código** que aparece na parte superior direita sob o botão **Guardar.** O pedido deve ser semelhante ao seguinte exemplo:

    ```html
    GET /<hubName>/installations/<installationId>?api-version=2015-01 HTTP/1.1
    Host: <namespace>.servicebus.windows.net
    Content-Type: application/json
    Authorization: <sasToken>
    x-ms-version: 2015-01
    Cache-Control: no-cache
    Postman-Token: <postmanToken>
    ```

1. Selecione o botão **Enviar.**

Não existe qualquer registo para a **instalação** especificada Id neste momento. A verificação deve resultar numa resposta "404 Não Encontrada" em vez de uma resposta "401 Não Autorizada". Este resultado deve confirmar que o token SAS foi aceite.

### <a name="implement-the-installation-service-class"></a>Implementar a classe de serviço de instalação

Em seguida, implementará o nosso invólucro básico em torno da [API REST Instalações](/rest/api/notificationhubs/create-overwrite-installation).  

Adicione um novo ficheiro Swift chamado **NotificationRegistrationService.swift** na pasta **Serviços** e, em seguida, adicione o seguinte código a este ficheiro:

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
    private var tokenExpiryDate : Date? = nil
    
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
                        completion(err == nil && 
                        (res as! HTTPURLResponse).statusCode == 200)
                }
            }).resume()
        }
    }
    
    private func getBaseAddress() -> String {
        return String.init(format: tokenizedBaseAddress, hubNamespace, hubName)
    }
    
    private func getSasToken() -> String {
        if (tokenData == nil ||
            tokenExpiryDate == nil ||
            Date() >= tokenExpiryDate!) {
            
            self.tokenData = TokenUtility.getSasToken(
                forResourceUrl: getBaseAddress(),
                withKeyName: self.keyName,
                andKey: self.key)
            
            self.tokenExpiryDate = Date(timeIntervalSinceNow: -(5 * 60))
                .addingTimeInterval(TimeInterval(tokenData!.expiration))
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

Os detalhes necessários são fornecidos como parte da inicialização. As etiquetas e os modelos são opcionalmente passados para a função **de registo** para fazer parte da carga útil JSON de instalação do **dispositivo.**  

A função **de registo** chama as outras funções privadas para preparar o pedido. Após a resposta ser recebida, a conclusão é chamada e indica se o registo foi bem sucedido.  

O ponto final do pedido é construído pela função **getBaseAddress.** A construção utiliza o espaço de *nome* símetros do centro de notificação e *o nome* que foram fornecidos durante a inicialização.  

A função **getSasToken** verifica se o token atualmente armazenado é válido. Se o token não for válido, a função chama **tokenUtility** para gerar um novo token e depois armazená-lo antes de devolver um valor.

Finalmente, **o códigoToJson** converte os respetivos objetos-modelo em JSON para utilização como parte do corpo de pedido.

### <a name="invoke-the-notification-hubs-rest-api"></a>Invocar os Centros de Notificação REST API

O último passo é atualizar **o AppDelegate** para utilizar o **NotificationRegistrationService** para se registar no nosso **NotificationHub**.

1. Abra **appDelegate.swift** e adicione variáveis de nível de classe para armazenar uma referência ao Serviço de **Registos de Noficiation** e ao modelo genérico de **pushtemplate:**

    ```swift
    var registrationService : NotificationRegistrationService?
    let genericTemplate = PushTemplate(withBody: "{\"aps\":{\"alert\":\"$(message)\"}}")
    ```

1. No mesmo ficheiro, atualize a função **didRegisterForRemoteNotificationsWithDeviceToken** para inicializar o Serviço de **Registos** de Notificações com os parâmetros necessários e, em seguida, ligue para a função **de registo.**

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

    Para ser simples, vai usar algumas declarações de impressão para atualizar a janela de saída com o resultado da operação de **registo.**

1. Agora construa e execute a aplicação num dispositivo físico. Deve ver "Registrado" na janela de saída.

## <a name="test-the-solution"></a>Testar a solução

A nossa aplicação nesta fase está registada no **NotificationHub** e pode receber notificações push. No Xcode, pare o debugger e feche a aplicação se estiver em funcionamento. Em seguida, verifique se os detalhes da **Instalação** do Dispositivo estão como esperado e que a nossa aplicação pode agora receber notificações push.  

### <a name="verify-the-device-installation"></a>Verifique a instalação do dispositivo

Pode agora fazer o mesmo pedido que fez anteriormente, utilizando o **Carteiro** para [verificar o token SAS](#verify-the-sas-token). Assumindo que o token SAS não expirou, a resposta deve agora incluir os detalhes de instalação que forneceu, tais como os modelos e etiquetas.

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

Se o seu **token SAS** anterior tiver expirado, pode adicionar um **ponto de rutura** à linha **24** da classe **TokenUtility** para obter um novo **token SAS** e atualizar o cabeçalho **de Autorização** com esse novo valor.

### <a name="send-a-test-notification-azure-portal"></a>Envie uma notificação de teste (portal Azure)

A forma mais rápida de testar que agora pode receber notificações é navegar para o centro de notificação no portal Azure:

1. No portal Azure, navegue para o separador **Overview** no seu centro de notificação.

1. Selecione **Enviar de Teste**, que está acima do resumo **essencial** na parte superior esquerda da janela do portal:

    ![Botão de envio de resumo do teste de resumo do Hubs de Notificação](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/hub-essentials-test-send.png)

1. Escolha **o modelo personalizado** da lista de **plataformas.**

1. Insira **12345** para o **Envio para a Expressão**de Etiqueta . Já tinha especificado esta etiqueta na nossa instalação.

1. Editar opcionalmente a **mensagem** na carga útil da JSON:

    ![Envio de teste de centros de notificação](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/hub-test-send.png)

1. Selecione **Enviar**. O portal deve indicar se a notificação foi enviada com sucesso para o dispositivo:

    ![Teste de envio de hubs de notificação](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/hub-test-send-result.png)

    Assumindo que a aplicação não está em execução em primeiro plano, deverá também ver uma notificação no Centro de **Notificação** do seu dispositivo. Tocar na notificação deve abrir a aplicação e mostrar o alerta.

    ![Notificação Recebida Exemplo](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/test-send-notification-received.png)

### <a name="send-a-test-notification-mail-carrier"></a>Envie uma notificação de teste (transportadora de correio)

Pode enviar notificações através da [API REST](/rest/api/notificationhubs/) utilizando o **Carteiro,** que pode ser uma forma mais conveniente de testar.

1. Abra um novo separador no **Carteiro.**

1. Definir o pedido para **POST**, e inserir o seguinte endereço:

    ```xml
    https://<namespace>.servicebus.windows.net/<hubName>/messages/?api-version=2015-01
    ```

1. Configure os cabeçalhos de pedido da seguinte forma:

   | Chave                            | Valor                          |
   | ------------------------------ | ------------------------------ |
   | Content-Type                   | aplicação/json;charset=utf-8 |
   | Autorização                  | \<sasToken>                     |
   | ServiceBusNotification-Formato  | modelo                       |
   | Etiquetas                           | "12345"                        |

1. Configure o **órgão** de pedido para utilizar **RAW - JSON (application.json)** com a seguinte carga útil JSON:

    ```json
    {
       "message" : "Hello from Postman!"
    }
    ```

1. Selecione o botão **Código,** que está sob o botão **Guardar** na parte superior direita da janela. O pedido deve ser semelhante ao seguinte exemplo:

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

1. Selecione o botão **Enviar.**

Deverá obter um código de estado de sucesso **criado em 201** e receber a notificação no dispositivo cliente..

## <a name="next-steps"></a>Passos seguintes
Tem agora uma aplicação básica do iOS Swift ligada a um centro de notificação através da [API REST](/rest/api/notificationhubs/) e pode enviar e receber notificações. Para obter mais informações, veja os artigos seguintes:

- [Visão geral dos Hubs de Notificação Azure](notification-hubs-push-notification-overview.md)
- [Centros de Notificação REST APIs](/rest/api/notificationhubs/)
- [Centros de Notificação SDK para operações de back-end](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)
- [Centros de notificação SDK no GitHub](https://github.com/Azure/azure-notificationhubs)
- [Registe-se com a aplicação no final](notification-hubs-ios-aspnet-register-user-from-backend-to-push-notification.md)
- [Gestão de registos](notification-hubs-push-notification-registration-management.md)
- [Trabalhar com etiquetas](notification-hubs-tags-segment-push-message.md) 
- [Trabalhar com modelos personalizados](notification-hubs-templates-cross-platform-push-messages.md)
- [Controlo de acesso de ônibus de serviço com assinaturas de acesso partilhado](../service-bus-messaging/service-bus-sas.md)
- [Geram programáticamente tokens SAS](/rest/api/eventhub/generate-sas-token)
- [Segurança da Apple: cripto comum](https://developer.apple.com/security/)
- [Tempo da época UNIX](https://en.wikipedia.org/wiki/Unix_time)
- [HMAC](https://en.wikipedia.org/wiki/HMAC)
