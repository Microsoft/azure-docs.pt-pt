---
title: Envie notificações push para aplicações swift iOS usando hubs de notificação Azure e as APIs REST
description: Neste tutorial, aprende a usar os Hubs de Notificação do Azure e as APIs REST para enviar notificações push para dispositivos iOS.
author: sethmanheim
ms.author: sethm
ms.date: 06/19/2020
ms.topic: tutorial
ms.service: notification-hubs
ms.reviewer: thsomasu
ms.lastreviewed: 06/03/2020
ms.openlocfilehash: 861011874c63f4c5d18ce33b107bbe9a80359045
ms.sourcegitcommit: 398fecceba133d90aa8f6f1f2af58899f613d1e3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/21/2020
ms.locfileid: "85126438"
---
# <a name="tutorial-send-push-notifications-to-swift-ios-apps-using-notification-hubs-rest-apis"></a>Tutorial: Enviar notificações push para aplicações swift iOS usando Os Hubs de Notificação REST APIs

Este tutorial descreve como usar os Hubs de Notificação Azure para enviar notificações push para uma aplicação iOS usando APIs REST.

Este tutorial contém os seguintes passos:

- Crie uma aplicação de amostra iOS.
- Ligue a sua aplicação iOS aos Hubs de Notificação Azure.
- Envie notificações push de teste.
- Verifique se a sua aplicação recebe notificações.

O código completo para este tutorial pode ser descarregado a partir do [GitHub.](https://github.com/Azure/azure-notificationhubs-ios/tree/v3-preview2/Samples)

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, precisa dos seguintes pré-requisitos:

- Um Mac que executa [o Xcode,](https://go.microsoft.com/fwLink/p/?LinkID=266532)juntamente com um certificado de desenvolvimento válido instalado no seu Keychain.

- Um iPhone ou iPad a correr a versão 10 ou posterior do iOS.

- O seu dispositivo físico registado no Portal da [Apple](https://developer.apple.com/)   e associado ao seu certificado.

Antes de prosseguir, certifique-se de passar pelo tutorial anterior sobre como começar com os [Hubs de Notificação Azure para aplicações iOS](https://go.microsoft.com/fwlink/?linkid=2129801) para configurar e configurar credenciais push no seu centro de notificação. Mesmo que não tenha experiência prévia com o desenvolvimento do iOS, deverá ser capaz de seguir estes passos.

> [!NOTE]
> Devido aos requisitos de configuração para notificações push, deve implementar e testar notificações push num dispositivo físico iOS (iPhone ou iPad), em vez do emulador iOS.

Nas secções seguintes, constrói uma aplicação iOS que se conecta ao centro de notificações.

## <a name="create-an-ios-project"></a>Criar um projeto iOS

1. No Xcode, crie um novo projeto iOS e selecione o modelo **de Aplicação de Visualização Única.**  

2. Ao definir as opções para o novo projeto:
   - Especifique o **Nome do Produto**   (PushDemo) e **o Identificador de Organização**   `com.<organization>` () que utilizou quando definiu o **identificador de pacotes**   no Portal do Desenvolvedor da Apple.
   - Escolha a **equipa**   para a qual foi criado o **ID da aplicação.**  
   - Desateia a **linguagem**   a **Swift.**
   - Selecione **Seguinte**.

3. Criar uma nova pasta chamada **SupportFiles**.

4. Crie um novo ficheiro de lista p chamado **devsettings.plist**   na pasta **SupportFiles.**   Certifique-se de adicionar esta pasta ao seu ficheiro **gitignore**   para que não se comprometa ao trabalhar com um repo git. Numa aplicação de produção, poderá estar a definir estes segredos condicionalmente como parte de um processo de construção automatizado. Estas definições não estão abrangidas por este tutorial.

5. Atualizar **devsettings.plist**   para incluir as seguintes entradas de configuração utilizando os seus próprios valores a partir do centro de notificação que a provisionou:

   | **Chave**                  | **Tipo** | **Valor**        |
   | ------------------------ | -------- | ---------------- |
   | notificationHubKey       | String   | `<hubKey>`       |
   | nome notificaçãoHubKey   | String   | `<hubKeyName>`   |
   | notificaçãoNão Dom      | String   | `<hubName>`      |
   | notificationHubNamespace | String   | `<hubNamespace>` |

   Pode encontrar os valores necessários navegando para o recurso do hub de notificação no portal Azure. Em particular, os **valores do NotificaçãoHubName**   e **notificação Os**valores do Espaço Panorama   estão no canto superior direito do resumo do **Essencial**dentro da   página **'Visão**   Geral'.

   :::image type="content" source="media/ios-sdk-swift-rest/image1.png" alt-text="Valores necessários":::

   Também pode encontrar os valores do **GraffitiKeyName**   e do **notificationHubKey**   navegando nas Políticas de **Acesso**   e selecionando a respetiva Política de **Acesso**, como **DefaultFullSharedAccessssignature**. Depois disso, copie da Cadeia de **Ligação Primária**   o valor pré-fixado para  `SharedAccessKeyName=`    **notificaçãoHubKeyName**, e o valor pré-fixado  `SharedAccessKey=`   para o **notificationHubKey .** A cadeia de ligação deve estar no seguinte formato:

   ```xml
   Endpoint=sb://<namespace>.servicebus.windows.net/;SharedAccessKeyName=<notificationHubKeyName>;SharedAccessKey=<notificationHubKey>
   ```

   Para simplificar, especifique **DefaultFulFulFullPartdAccessssignature**, para que possa utilizar o token para enviar notificações. Na prática, o **DefaultListdAccessSignature**   é uma escolha melhor para situações em que apenas deseja receber notificações.

6. No **Âmbito do Project Navigator,** selecione o Nome do **Projeto**   e, em seguida, selecione o separador **Geral.**  

7. Encontre **a Identidade**   e, em seguida, desaprote o valor **do Identificador de Pacote**para que   corresponda , que é o valor utilizado para  `com.<organization>.PushDemo` o **ID**da app   a partir de um passo anterior.

8. Encontre **a assinatura & capacidades**e, em seguida, selecione a **equipa**adequada para a sua conta   de **desenvolvedores apple**. O valor **da Equipa**deve corresponder ao que criou os   seus certificados e perfis.

9. O Xcode deve descarregar automaticamente o valor adequado do **Perfil de Provisionamento**   com base no **Identificador de Pacotes**. Se não vir o novo valor **do Perfil de Provisionamento,**   tente refrescar os perfis da Identidade de **Assinatura**   selecionando **Xcode**, em seguida, **Preferências**, **Conta**, e, em seguida, selecione o botão **Descodificar Perfis Manuais**   para descarregar os perfis.

10. Ainda no **separador 'Signing & Capabilities',**   clique no botão + **Capacidade**e   toque **duas vezes**nas notificações   push da lista para garantir que as **Notificações push**   estão ativadas.

11. Abra o seu ficheiro **AppDeegate.swift**   para implementar o protocolo **UNUserNotificationCenterDelegate**   e adicione o seguinte código ao topo da classe:

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

    Usará estes membros mais tarde. Especificamente, você usará o **tags**   membro tags como parte do registo usando um modelo **personalizado**. Para obter mais informações sobre etiquetas, consulte [tags para inscrições](notification-hubs-tags-segment-push-message.md)   e [inscrições de Modelos.](notification-hubs-templates-cross-platform-push-messages.md)

12. No mesmo ficheiro, adicione o seguinte código à função **DidFinishLaunchingWithOptions:**

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

    Este código recupera as definições de **devsettings.plist,** define a classe **AppDeegate**   como delegado do **UnUserNotificationCenter,**   solicita autorização para notificações push e, em seguida, chama o registo **ForRemoteNotifications**.

    Para simplificar, o código só suporta o iOS 10 e posteriormente. Pode adicionar suporte para versões anteriores do iOS utilizando condicionalmente as respetivas APIs e abordagens, como normalmente faria.

13. No mesmo ficheiro, adicione o seguinte código:

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

    Este código utiliza os valores **de instalaçãoId**   e **pushChannel**   para registar-se com o centro de notificação. Neste caso, está a utilizar **uiDevice.current.identifierForVendor**   para fornecer um valor único para identificar o dispositivo e, em seguida, formatar o **dispositivoToken**   para fornecer o valor **pushChannel**   desejado. A função **showAlert**   existe simplesmente para exibir algum texto de mensagem para fins de demonstração.

14. Ainda no ficheiro **AppDeegate.swift,**   adicione as funções **willPresent**   e **didReceive**ao    **UNUserNotificationCenterDelegate**. Estas funções exibem um alerta quando são notificados de que uma aplicação está a ser executada em primeiro plano ou em segundo plano.

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

15. Adicione `print` declarações ao fundo da função **DidRegisterForRemoteNotificationsWithDeviceToken**   para verificar se a **instalaçãoId**   e **pushChannel**   estão a ser atribuídos valores:

    ```swift
    print(installationId)
    print(pushChannel)
    ```

16. Crie as **pastas Modelos,** **Serviços**e **Utilitários**   para os componentes fundamentais que irá adicionar ao projeto mais tarde.

17. Verifique se o projeto constrói e funciona com um dispositivo físico. As notificações push não podem ser testadas utilizando o simulador.

## <a name="create-models"></a>Criar modelos

Neste passo, cria um conjunto de modelos para representar as cargas de API do Centro de [Notificação REST](/rest/api/notificationhubs/)   e para armazenar os dados simbólicos de assinatura de acesso partilhado (SAS) necessários.

1. Adicione um novo ficheiro Swift chamado **PushTemplate.swift**   à pasta **Modelos.**   Este modelo define uma estrutura que representa o **CORPO**   de um modelo individual como parte da carga útil **de instalação do DeviceInstall.**  

    ```swift
    import Foundation

    struct PushTemplate : Codable {
        let body : String

        init(withBody body : String) {
            self.body = body
        }
    }
    ```

2. Adicione um novo ficheiro Swift chamado **DeviceInstall.swift**   à pasta **Modelos.**   Este ficheiro define uma estrutura que representa a carga útil para a criação ou atualização de uma **Instalação do Dispositivo.** Adicione o seguinte código ao ficheiro:

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

3. Adicione um novo ficheiro Swift chamado **TokenData.swift**   à pasta **Modelos.**   Este modelo é utilizado para armazenar um token SAS juntamente com a sua expiração. Adicione o seguinte código ao ficheiro:

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

## <a name="generate-a-sas-token"></a>Gerar um token SAS

O Notification Hubs utiliza a mesma infraestrutura de segurança que o Azure Service Bus. Para chamar a API REST, [gere programáticamente um token SAS](/rest/api/eventhub/generate-sas-token)   que pode ser usado no **cabeçalho**de Autorização   do pedido.

O token resultante será no seguinte formato:

```xml
SharedAccessSignature sig=\<UrlEncodedSignature\>\&se=\<ExpiryEpoch\>\&skn=\<KeyName\>\&sr=\<UrlEncodedResourceUri\> |
```

O processo em si envolve os mesmos seis passos:

1. Calcular a expiração no formato [tempo da EPOCH UNIX,](https://en.wikipedia.org/wiki/Unix_time)   o que significa o número de segundos decorridos desde a meia-noite Hora Coordenada Universal, 1 de janeiro de 1970.

2. Formato o **ResourceUrl**   que representa o recurso a que está a tentar aceder, por isso está codificado por cento e minúsculo. O **ResourceUrl**   tem o `https://<namespace>.servicebus.windows.net/<hubName>` formato.

3. Prepare o **StringToSign**, que é formatado como `<UrlEncodedResourceUrl>\n<ExpiryEpoch>` .

4. Compute e Base64 codificam a **Assinatura**   utilizando o hash HMAC-SHA256 do valor **StringToSign.**   O valor do haxixe é utilizado com a parte **chave**   da cadeia de **ligação**   para a respetiva Regra de **Autorização**.

5. Formato a **Assinatura**codificada base64,   por isso está codificada por cento.

6. Construa o token no formato esperado utilizando os **valores urlEncodedSignature**, **ExpirayEpoch,** **KeyName**e **UrlEncodedResourceUrl.**  

Consulte a documentação do [Azure Service Bus](../service-bus-messaging/service-bus-sas.md)   para obter uma visão geral mais completa das assinaturas de acesso partilhado e como a Azure Service Bus e os Centros de Notificação a utilizam.

Para efeitos deste exemplo Swift, utiliza a biblioteca **CommonCrypto**de código aberto da Apple   para ajudar no hashing da assinatura. Como é uma biblioteca C, não é acessível em Swift fora da caixa. Pode disponibilizar a biblioteca utilizando um cabeçalho de ponte.

Para adicionar e configurar o cabeçalho de ponte:

1. No Xcode, selecione **'Ficheiro'** e, em seguida, **Novo,** em seguida, **Ficheiro**de Cabeçalho e, em seguida, selecione **o Ficheiro cabeçalho**. Nomeie o ficheiro de **cabeçalho BridgingHeader.h**.

2. Editar o ficheiro para importar **CommonHMAC.h:**

   ```swift
   #import <CommonCrypto/CommonHMAC.h>

   #ifndef BridgingHeader_h
   #define BridgingHeader_h

   #endif /* BridgingHeader_h */
   ```

3. Atualize as **Definições de Construção**do alvo   para fazer referência ao cabeçalho de ponte:

   1. Selecione o projeto **PushDemo**   e desloque-se para baixo para a secção **Swift Compiler.**  

   2. Certifique-se de que a opção **de compatibilidade Objective-C**   está definida para **Sim**.

   3. Introduza o percurso de ficheiro  `<ProjectName>/BridgingHeader.h`   na opção **cabeçalho de ponte Objectivo-C.**   Este é o caminho do ficheiro para o cabeçalho da ponte.

   Se não conseguir encontrar estas opções, certifique-se de que tem a vista **All**   selecionada, em vez de **Básica**   ou **Personalizada.**

   Existem muitas bibliotecas de invólucros de código aberto de terceiros disponíveis que podem tornar a utilização **do CommonCrypto**   um pouco mais fácil. No entanto, a discussão destas bibliotecas está fora do âmbito deste artigo.

4. Adicione um novo ficheiro Swift chamado **TokenUtility.swift**   dentro da pasta **Utilities**   e adicione o seguinte código:

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

   Este utilitário encapsula a lógica responsável pela geração do token SAS.

   Como descrito anteriormente, a função **getSasToken**   orquestra os passos de alto nível necessários para preparar o token. A função será chamada pelo serviço de instalação mais tarde neste tutorial.

   As outras duas funções são chamadas pela função **getSasToken:**    **sha256HMac**   para computação da assinatura, e **urlEncodedString**   para codificar a cadeia URL associada. É necessária a função **urlEncodedString,**   uma vez que não é possível obter a saída necessária utilizando a função de **adicionarPercentEncoding**   incorporado.

   O [Azure Storage iOS SDK](https://github.com/Azure/azure-storage-ios/blob/master/Lib/Azure%20Storage%20Client%20Library/Azure%20Storage%20Client%20Library/AZSUtil.m)   é um excelente exemplo de como abordar estas operações no Objective-C. Mais informações sobre fichas SAS da Azure Service Bus podem ser encontradas na documentação do [Azure Service Bus](../service-bus-messaging/service-bus-sas.md).

5. Em **AppDeegate.swift**, adicione o seguinte código à  `didRegisterForRemoteNotificationsWithDeviceToken`   função para verificar se o **TokenUtility.getSasToken**   está a gerar um token válido

   ```swift
   let baseAddress = "https://<notificaitonHubNamespace>.servicebus.windows.net/<notifiationHubName>"

   let tokenData = TokenUtility.getSasToken(forResourceUrl: baseAddress,
                                            withKeyName: self.notificationHubKeyName!,
                                            andKey: self.notificationHubKey!)

   print(tokenData.token)
   ```

   Certifique-se de que substitui os espaços reservados na cadeia **baseAddress**   pelos seus próprios valores.

## <a name="verify-the-sas-token"></a>Verifique o token SAS

Antes de implementar o serviço de instalação no cliente, verifique se a sua aplicação está a gerar corretamente o token SAS utilizando um utilitário HTTP. Para efeitos deste tutorial, a nossa ferramenta de eleição é **o Carteiro.**

Tome nota dos valores **de instalaçãoId**   e **token**   gerados pela app.

Siga estes passos para chamar as **instalações**   API:

1. No **Carteiro,** abra uma nova conta.
2. Desaça o pedido para **GET**   e especifique o seguinte endereço:

   ```xml
   https://<namespace>.servicebus.windows.net/<hubName>/installations/<installationId>?api-version=2015-01
   ```

3. Configure os cabeçalhos de pedido da seguinte forma:

   | **Chave**       | **Valor**        |
   | ------------- | ---------------- |
   | Content-Type  | application/json |
   | Autorização | \<sasToken\>     |
   | x-ms-versão  | 2015-01          |

4. Selecione o botão **Código**   que aparece no canto superior direito sob o botão **Guardar.**   O pedido deve ser semelhante ao seguinte exemplo:

   ```xml
   GET /<hubName>/installations/<installationId>?api-version=2015-01 HTTP/1.1
   Host: <namespace>.servicebus.windows.net
   Content-Type: application/json
   Authorization: <sasToken>
   x-ms-version: 2015-01
   Cache-Control: no-cache
   Postman-Token: <postmanToken>
   ```

5. Selecione o **Send**   botão Enviar.

Não existe qualquer registo para a **instalação especificadaId**   neste momento. A verificação deve resultar numa resposta "404 Não Encontrada" em vez de uma resposta "401 Não Autorizada". Este resultado deve confirmar que o token SAS foi aceite.

## <a name="implement-the-installation-service-class"></a>Implementar a classe de serviço de instalação

Em seguida, implemente um invólucro básico em torno da [Instalações REST API](/rest/api/notificationhubs/create-overwrite-installation).

Adicione um novo ficheiro Swift chamado **NotificationRegistrationService.swift**   sob a pasta **Serviços**   e, em seguida, adicione o seguinte código a este ficheiro:

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

Os detalhes necessários são fornecidos como parte da inicialização. As etiquetas e os modelos são opcionalmente passados para a função **de registo**para fazer parte da carga   útil JSON de **instalação**do   dispositivo.

A função **de registo**liga para   as outras funções privadas para preparar o pedido. Após a resposta ser recebida, a conclusão é convocada e indica se o registo foi bem sucedido.

O ponto final do pedido é construído pela função **getBaseAddress.**   A construção utiliza os parâmetros do centro de notificação *espaço*   e *nome*que foram fornecidos durante   a inicialização.

A função **getSasToken**   verifica se o token armazenado atualmente é válido. Se o token não for válido, a função chama **TokenUtility**   para gerar um novo token e, em seguida, armazena-o antes de devolver um valor.

Finalmente, **o encodeToJson**   converte os respetivos objetos do modelo em JSON para utilização como parte do corpo de pedido.

## <a name="invoke-the-notification-hubs-rest-api"></a>Invocar os Centros de Notificação REST API

O último passo é atualizar **o AppDeegate**   para utilizar o **NotificationRegistrationService**   para se registar no **NotificationHub**.

1. Abrir **appDeegate.swift**   e adicionar variáveis de nível de classe para armazenar uma referência ao **NoficiationRegistrationService**   e ao genérico **PushTemplate**:

   ```swift
   var registrationService : NotificationRegistrationService?
   let genericTemplate = PushTemplate(withBody: "{\"aps\":{\"alert\":\"$(message)\"}}")
   ```

2. No mesmo ficheiro, atualize a função **DeRegisterForRemoteNotificationsWithDeviceToken**   para rubricar o **NotificationRegistrationService**   com os parâmetros necessários e, em seguida, ligar para a função de **registo.**  

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

   Para simplificar, o código utiliza `print` declarações para atualizar a janela de saída com o resultado da operação de **registo.**  

3. Construa e execute a aplicação num dispositivo físico. Devia ver **registado** na janela de saída.

## <a name="test-the-solution"></a>Testar a solução

Neste momento a aplicação está registada no **NotificationHub**   e pode receber notificações push. No Xcode, pare o depurar e feche a app se estiver atualmente em execução. Em seguida, verifique se os detalhes da **Instalação**do Dispositivo   aparecem como esperado e que a aplicação pode agora receber notificações push.

### <a name="verify-the-device-installation"></a>Verifique a instalação do dispositivo

Pode agora fazer o mesmo pedido que fez anteriormente, utilizando **o Carteiro**   para verificar o [token SAS](notification-hubs-ios-push-notifications-swift-apps-get-started.md#verify-the-sas-token). Assumindo que o token SAS não expirou, a resposta deve agora incluir os detalhes de instalação que forneceu, como os modelos e tags.

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

Se o seu token SAS anterior tiver expirado, pode adicionar um ponto de rutura à linha 24 da classe **TokenUtility**   para obter um novo token SAS e atualizar o cabeçalho de **Autorização**com esse   novo valor.

### <a name="send-a-test-notification-azure-portal"></a>Enviar uma notificação de teste (portal Azure)

A forma mais rápida de testar que agora pode receber notificações é navegar pelo centro de notificações no portal Azure:

1. No portal Azure, consulte o **separador Visão Geral**no seu centro de   notificação.

2. Selecione **Enviar**, que está acima do resumo **Essencial**   na parte superior esquerda da janela do portal:

   :::image type="content" source="media/ios-sdk-swift-rest/image2.png" alt-text="Aviso de envio de teste sumário essencial dos Hubs":::

3. Escolha **o modelo personalizado**na lista de    **plataformas.**  

4. Introduza **12345**   para a Expressão Enviar para **Etiquetar**. Já tinha especificado esta etiqueta na sua instalação.

5. Opcionalmente, edite a **mensagem**   na carga útil do JSON:

   :::image type="content" source="media/ios-sdk-swift-rest/image3.png" alt-text="Teste de aviso hubs enviar":::

6. Selecione **Enviar**. O portal deve indicar se a notificação foi enviada com sucesso para o dispositivo:

   :::image type="content" source="media/ios-sdk-swift-rest/image4.png" alt-text="Resultado do envio de teste":::

   Assumindo que a aplicação não está em primeiro plano, também deverá ver uma notificação no **Centro de Notificação**   do seu dispositivo. Tocar na notificação deve abrir a aplicação e mostrar o alerta.

   :::image type="content" source="media/ios-sdk-swift-rest/image5.png" alt-text="Notificação de teste":::

### <a name="send-a-test-notification-mail-carrier"></a>Enviar uma notificação de teste (transportadora de correio)

Pode enviar notificações através da [API REST](/rest/api/notificationhubs/)   usando o **Carteiro,** o que pode ser uma forma mais conveniente de testar.

1. Abra uma nova conta no **Carteiro.**

2. Desaça o pedido ao **POST,** e insira o seguinte endereço:

   ```xml
   https://<namespace>.servicebus.windows.net/<hubName>/messages/?api-version=2015-01
   ```

3. Configure os cabeçalhos de pedido da seguinte forma:

   | Chave                           | Valor                          |
   | ----------------------------- | ------------------------------ |
   | Content-Type                  | aplicação/json;charset=utf-8 |
   | Autorização                 | \<sasToken\>                   |
   | ServiceBusNotification-Formato | modelo                       |
   | Etiquetas                          | "12345"                        |

4. Configure o pedido **BODY**   para utilizar RAW - **JSON (application.jsligado)**   com a seguinte carga útil JSON:

   ```json
   {
   "message" : "Hello from Postman!"
   }
   ```

5. Selecione o **Code**   botão Código, que está no botão **Guardar**   no canto superior direito da janela. O pedido deve ser semelhante ao seguinte exemplo:

   ```xml
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

6. Selecione o **Send**   botão Enviar.

Deverá obter um código de estado de sucesso **201 Criado**   e receber a notificação no dispositivo cliente.

## <a name="next-steps"></a>Passos seguintes

Tem agora uma aplicação básica do iOS Swift ligada a um hub de notificações através do [Centro de Notificações REST API](/rest/api/notificationhubs/), e pode enviar e receber notificações. Para saber mais sobre como enviar notificações para dispositivos específicos, avance para o seguinte tutorial:

- [Tutorial: Notificações push para dispositivos específicos](notification-hubs-ios-xplat-segmented-apns-push-notification.md)

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
