---
title: Introdução ao iOS e ao macOS – plataforma de identidade da Microsoft | Azure
description: Como os aplicativos iOS e macOS (Swift) podem chamar uma API que exige tokens de acesso usando a plataforma de identidade da Microsoft
services: active-directory
documentationcenter: dev-center-name
author: tylermsft
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.workload: identity
ms.date: 08/30/2019
ms.author: jmprieur
ms.reviewer: oldalton
ms.custom: aaddev, identityplatformtop40
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8cfb61f417597abe52910b012ce3fb79ba48ce97
ms.sourcegitcommit: bc193bc4df4b85d3f05538b5e7274df2138a4574
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/10/2019
ms.locfileid: "73902847"
---
# <a name="sign-in-users-and-call-the-microsoft-graph-from-an-ios-or-macos-app"></a>Conectar usuários e chamar o Microsoft Graph de um aplicativo iOS ou macOS

Neste tutorial, você aprenderá a integrar um aplicativo iOS ou macOS com a plataforma de identidade da Microsoft. O aplicativo conectará um usuário, obterá um token de acesso para chamar a API de Microsoft Graph e fará uma solicitação para a API de Microsoft Graph.  

Quando você tiver concluído o guia, seu aplicativo aceitará entradas de contas pessoais da Microsoft (incluindo outlook.com, live.com e outros) e contas corporativas ou de estudante de qualquer empresa ou organização que usa Azure Active Directory.

## <a name="how-this-tutorial-works"></a>Como este tutorial funciona

![Mostra como o aplicativo de exemplo gerado por este tutorial funciona](../../../includes/media/active-directory-develop-guidedsetup-ios-introduction/iosintro.svg)

O aplicativo neste tutorial conectará os usuários e obterá dados em seu nome.  Esses dados serão acessados por meio de uma API protegida (Microsoft Graph API, nesse caso) que requer autorização e são protegidos pela plataforma de identidade da Microsoft.

Mais especificamente:

* Seu aplicativo conectará o usuário por meio de um navegador ou do Microsoft Authenticator.
* O usuário final aceitará as permissões solicitadas pelo seu aplicativo.
* Seu aplicativo receberá um token de acesso para a API de Microsoft Graph.
* O token de acesso será incluído na solicitação HTTP para a API da Web.
* Processar a resposta de Microsoft Graph.

Este exemplo usa a MSAL (biblioteca de autenticação da Microsoft) para implementar a autenticação. O MSAL renovará automaticamente os tokens, entregará o SSO (logon único) entre outros aplicativos no dispositivo e gerenciará as contas.

Este tutorial é aplicável a aplicativos iOS e macOS. Observe que algumas etapas são diferentes entre essas duas plataformas. 

## <a name="prerequisites"></a>Pré-requisitos

- O XCode versão 10. x ou superior é necessário para compilar o aplicativo neste guia. Você pode baixar o XCode no [site do iTunes](https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12 "URL de download do XCode").
- Biblioteca de autenticação da Microsoft ([MSAL. Framework](https://github.com/AzureAD/microsoft-authentication-library-for-objc)). Você pode usar um Gerenciador de dependência ou adicionar a biblioteca manualmente. As instruções a seguir mostram como.

Este tutorial criará um novo projeto. Se você quiser baixar o tutorial concluído em vez disso, baixe o código:
- [código de exemplo do iOS](https://github.com/Azure-Samples/active-directory-ios-swift-native-v2/archive/master.zip)
- [código de exemplo do macOS](https://github.com/Azure-Samples/active-directory-macOS-swift-native-v2/archive/master.zip)

## <a name="create-a-new-project"></a>Criar um novo projeto

1. Abra o Xcode e selecione **criar um novo projeto do Xcode**.
2. Para aplicativos iOS, selecione **iOS** > **aplicativo de exibição única** e selecione **Avançar**.
3. Para aplicativos macOS, selecione **macOS** > **aplicativo Cocoa** e selecione **Avançar**.
4. Forneça um nome de produto.
5. Defina o **idioma** como **Swift** e selecione **Avançar**.
6. Selecione uma pasta para criar seu aplicativo e clique em **criar**.

## <a name="register-your-application"></a>Registar a sua aplicação

1. Aceda ao [Portal do Azure](https://aka.ms/MobileAppReg)
2. Abra a [folha registros de aplicativo](https://ms.portal.azure.com/?feature.broker=true#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredAppsPreview) e clique em **+ novo registro**.
3. Insira um **nome** para seu aplicativo e, em seguida, sem definir um URI de redirecionamento, clique em **registrar**.
4. Na seção **gerenciar** do painel que aparece, selecione **autenticação**.

5. Clique em **experimentar a nova experiência** perto da parte superior da tela para abrir a nova experiência de registro de aplicativo e, em seguida, clique em **+ novo registro** >  **+ Adicionar uma plataforma** > **Ios**.
    - Insira a ID do pacote do seu projeto. Se você baixou o código, isso é `com.microsoft.identitysample.MSALiOS`. Se você estiver criando seu próprio projeto, selecione seu projeto no Xcode e abra a guia **geral** . O identificador do pacote aparece na seção **identidade** .
    - Observe que, para o macOS, você também deve usar a experiência do iOS. 
6. Clique em `Configure` e salve a **configuração MSAL** que aparece na página de **configuração do IOS** para que você possa inseri-la ao configurar o aplicativo mais tarde.  Clique em **Concluído**.

## <a name="add-msal"></a>Adicionar MSAL

Escolha uma das seguintes maneiras de instalar a biblioteca MSAL em seu aplicativo:

### <a name="cocoapods"></a>CocoaPods

1. Se você estiver usando o [CocoaPods](https://cocoapods.org/), instale `MSAL` primeiro criando um arquivo vazio chamado `podfile` na mesma pasta que o arquivo de `.xcodeproj` do seu projeto. Adicione o seguinte a `podfile`:

   ```
   use_frameworks!
   
   target '<your-target-here>' do
      pod 'MSAL'
   end
   ```

2. Substitua `<your-target-here>` pelo nome do seu projeto.
3. Em uma janela de terminal, navegue até a pasta que contém o `podfile` que você criou e execute `pod install` para instalar a biblioteca MSAL.
4. Feche o Xcode e abra `<your project name>.xcworkspace` para recarregar o projeto no Xcode.

### <a name="carthage"></a>Carthage

Se você estiver usando o [Carthage](https://github.com/Carthage/Carthage), instale `MSAL` adicionando-o ao seu `Cartfile`:

```
github "AzureAD/microsoft-authentication-library-for-objc" "master"
```

Em uma janela de terminal, no mesmo diretório que o `Cartfile`atualizado, execute o seguinte comando para que o Carthage atualize as dependências em seu projeto.

Cisco

```bash
carthage update --platform iOS
```

MacOS

```bash
carthage update --platform macOS
```

### <a name="manually"></a>Manualmente

Você também pode usar o submódulo git ou conferir a versão mais recente para usar como uma estrutura em seu aplicativo.

## <a name="add-your-app-registration"></a>Adicionar o registro do aplicativo

Em seguida, adicionaremos o registro do aplicativo ao seu código. 

Primeiro, adicione a seguinte instrução de importação na parte superior do `ViewController.swift` e `AppDelegate.swift` arquivos:

```swift
import MSAL
```

Em seguida, adicione o seguinte código a `ViewController.swift` antes de `viewDidLoad()`:

```swift
let kClientID = "Your_Application_Id_Here"

// Additional variables for Auth and Graph API
let kGraphURI = "https://graph.microsoft.com/v1.0/me/" // the Microsoft Graph endpoint
let kScopes: [String] = ["https://graph.microsoft.com/user.read"] // request permission to read the profile of the signed-in user
let kAuthority = "https://login.microsoftonline.com/common" // this authority allows a personal Microsoft account and a work or school account in any organization’s Azure AD tenant to sign in
var accessToken = String()
var applicationContext : MSALPublicClientApplication?
var webViewParamaters : MSALWebviewParameters?
```

O único valor que você precisa modificar acima é o valor atribuído a `kClientID`para ser a [ID do aplicativo](https://docs.microsoft.com/azure/active-directory/develop/developer-glossary#application-id-client-id). Esse valor faz parte dos dados de configuração do MSAL que você salvou durante a etapa no início deste tutorial para registrar o aplicativo no portal do Azure.

## <a name="for-ios-only-configure-url-schemes"></a>Somente para iOS, configurar esquemas de URL

Nesta etapa, você registrará `CFBundleURLSchemes` para que o usuário possa ser Redirecionado de volta para o aplicativo depois de entrar. A propósito, `LSApplicationQueriesSchemes` também permite que seu aplicativo faça uso de Microsoft Authenticator.

No Xcode, abra `Info.plist` como um arquivo de código-fonte e adicione o seguinte dentro da seção `<dict>`. Substitua `[BUNDLE_ID]` pelo valor usado na portal do Azure que, se você baixou o código, é `com.microsoft.identitysample.MSALiOS`. Se você estiver criando seu próprio projeto, selecione seu projeto no Xcode e abra a guia **geral** . O identificador do pacote aparece na seção **identidade** .

```xml
<key>CFBundleURLTypes</key>
<array>
    <dict>
        <key>CFBundleURLSchemes</key>
        <array>
            <string>msauth.[BUNDLE_ID]</string>
        </array>
    </dict>
</array>
<key>LSApplicationQueriesSchemes</key>
<array>
    <string>msauthv2</string>
    <string>msauthv3</string>
</array>
```

## <a name="for-macos-only-configure-app-sandbox"></a>Somente para macOS, configure a área restrita do aplicativo

1. Vá para a guia Configurações do projeto do Xcode > a **aba recursos** > **área restrita do aplicativo**
2. Selecione a caixa **de seleção conexões de saída (cliente)** . 

## <a name="create-your-apps-ui"></a>Criar a interface do usuário do seu aplicativo

Agora, crie uma interface do usuário que inclua um botão para chamar a API de Microsoft Graph, outra para sair e uma exibição de texto para ver alguma saída adicionando o seguinte código à classe `ViewController`:

### <a name="ios-ui"></a>interface do usuário do iOS

```swift
var loggingText: UITextView!
var signOutButton: UIButton!
var callGraphButton: UIButton!

func initUI() {
        // Add call Graph button
        callGraphButton  = UIButton()
        callGraphButton.translatesAutoresizingMaskIntoConstraints = false
        callGraphButton.setTitle("Call Microsoft Graph API", for: .normal)
        callGraphButton.setTitleColor(.blue, for: .normal)
        callGraphButton.addTarget(self, action: #selector(callGraphAPI(_:)), for: .touchUpInside)
        self.view.addSubview(callGraphButton)
        
        callGraphButton.centerXAnchor.constraint(equalTo: view.centerXAnchor).isActive = true
        callGraphButton.topAnchor.constraint(equalTo: view.topAnchor, constant: 50.0).isActive = true
        callGraphButton.widthAnchor.constraint(equalToConstant: 300.0).isActive = true
        callGraphButton.heightAnchor.constraint(equalToConstant: 50.0).isActive = true
        
        // Add sign out button
        signOutButton = UIButton()
        signOutButton.translatesAutoresizingMaskIntoConstraints = false
        signOutButton.setTitle("Sign Out", for: .normal)
        signOutButton.setTitleColor(.blue, for: .normal)
        signOutButton.setTitleColor(.gray, for: .disabled)
        signOutButton.addTarget(self, action: #selector(signOut(_:)), for: .touchUpInside)
        self.view.addSubview(signOutButton)
        
        signOutButton.centerXAnchor.constraint(equalTo: view.centerXAnchor).isActive = true
        signOutButton.topAnchor.constraint(equalTo: callGraphButton.bottomAnchor, constant: 10.0).isActive = true
        signOutButton.widthAnchor.constraint(equalToConstant: 150.0).isActive = true
        signOutButton.heightAnchor.constraint(equalToConstant: 50.0).isActive = true
        signOutButton.isEnabled = false
        
        // Add logging textfield
        loggingText = UITextView()
        loggingText.isUserInteractionEnabled = false
        loggingText.translatesAutoresizingMaskIntoConstraints = false
        
        self.view.addSubview(loggingText)
        
        loggingText.topAnchor.constraint(equalTo: signOutButton.bottomAnchor, constant: 10.0).isActive = true
        loggingText.leftAnchor.constraint(equalTo: self.view.leftAnchor, constant: 10.0).isActive = true
        loggingText.rightAnchor.constraint(equalTo: self.view.rightAnchor, constant: 10.0).isActive = true
        loggingText.bottomAnchor.constraint(equalTo: self.view.bottomAnchor, constant: 10.0).isActive = true
    }
```

### <a name="macos-ui"></a>interface do usuário do macOS

```swift

var callGraphButton: NSButton!
var loggingText: NSTextView!
var signOutButton: NSButton!

func initUI() {
        // Add call Graph button
        callGraphButton  = NSButton()
        callGraphButton.translatesAutoresizingMaskIntoConstraints = false
        callGraphButton.title = "Call Microsoft Graph API"
        callGraphButton.target = self
        callGraphButton.action = #selector(callGraphAPI(_:))
        callGraphButton.bezelStyle = .rounded
        self.view.addSubview(callGraphButton)
        
        callGraphButton.centerXAnchor.constraint(equalTo: view.centerXAnchor).isActive = true
        callGraphButton.topAnchor.constraint(equalTo: view.topAnchor, constant: 30.0).isActive = true
        callGraphButton.heightAnchor.constraint(equalToConstant: 34.0).isActive = true
        
        // Add sign out button
        signOutButton = NSButton()
        signOutButton.translatesAutoresizingMaskIntoConstraints = false
        signOutButton.title = "Sign Out"
        signOutButton.target = self
        signOutButton.action = #selector(signOut(_:))
        signOutButton.bezelStyle = .texturedRounded
        self.view.addSubview(signOutButton)
        
        signOutButton.centerXAnchor.constraint(equalTo: view.centerXAnchor).isActive = true
        signOutButton.topAnchor.constraint(equalTo: callGraphButton.bottomAnchor, constant: 10.0).isActive = true
        signOutButton.heightAnchor.constraint(equalToConstant: 34.0).isActive = true
        signOutButton.isEnabled = false
        
        // Add logging textfield
        loggingText = NSTextView()
        loggingText.translatesAutoresizingMaskIntoConstraints = false
        
        self.view.addSubview(loggingText)
        
        loggingText.topAnchor.constraint(equalTo: signOutButton.bottomAnchor, constant: 10.0).isActive = true
        loggingText.leftAnchor.constraint(equalTo: self.view.leftAnchor, constant: 10.0).isActive = true
        loggingText.rightAnchor.constraint(equalTo: self.view.rightAnchor, constant: -10.0).isActive = true
        loggingText.bottomAnchor.constraint(equalTo: self.view.bottomAnchor, constant: -10.0).isActive = true
        loggingText.widthAnchor.constraint(equalToConstant: 500.0).isActive = true
        loggingText.heightAnchor.constraint(equalToConstant: 300.0).isActive = true
    }
```

Em seguida, também dentro da classe `ViewController`, substitua o método `viewDidLoad()` por:

```swift
    override func viewDidLoad() {
        super.viewDidLoad()
        initUI()
        do {
            try self.initMSAL()
        } catch let error {
            self.updateLogging(text: "Unable to create Application Context \(error)")
        }
    }
```

## <a name="use-msal"></a>Usar MSAL

### <a name="initialize-msal"></a>Inicializar MSAL

Adicione o seguinte método `initMSAL` à classe `ViewController`:

```swift
    func initMSAL() throws {
        
        guard let authorityURL = URL(string: kAuthority) else {
            self.updateLogging(text: "Unable to create authority URL")
            return
        }
        
        let authority = try MSALAADAuthority(url: authorityURL)
        
        let msalConfiguration = MSALPublicClientApplicationConfig(clientId: kClientID, redirectUri: nil, authority: authority)
        self.applicationContext = try MSALPublicClientApplication(configuration: msalConfiguration)
        self.initWebViewParams()
    }
```

Adicione o seguinte após `initMSAL` método à classe `ViewController`.

### <a name="ios-code"></a>código do iOS:

```swift
func initWebViewParams() {
        self.webViewParamaters = MSALWebviewParameters(parentViewController: self)
    }
```

### <a name="macos-code"></a>código macOS:

```swift
func initWebViewParams() {
        self.webViewParamaters = MSALWebviewParameters()
        self.webViewParamaters?.webviewType = .wkWebView
    }
```

### <a name="for-ios-only-handle-the-sign-in-callback"></a>Somente para iOS, manipule o retorno de chamada de entrada

Abra o ficheiro `AppDelegate.swift`. Para lidar com o retorno de chamada após a entrada, adicione `MSALPublicClientApplication.handleMSALResponse` à classe `appDelegate` como esta:

```swift
// Inside AppDelegate...
func application(_ app: UIApplication, open url: URL, options: [UIApplication.OpenURLOptionsKey : Any] = [:]) -> Bool {
        
        return MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: options[UIApplication.OpenURLOptionsKey.sourceApplication] as? String)
}

```

**Se você estiver usando o Xcode 11**, deverá posicionar o retorno de chamada MSAL no `SceneDelegate.swift` em vez disso.
Se você oferecer suporte a UISceneDelegate e UIApplicationDelegate para compatibilidade com o iOS mais antigo, o retorno de chamada do MSAL precisaria ser colocado em ambos os arquivos.

```swift
func scene(_ scene: UIScene, openURLContexts URLContexts: Set<UIOpenURLContext>) {
        
        guard let urlContext = URLContexts.first else {
            return
        }
        
        let url = urlContext.url
        let sourceApp = urlContext.options.sourceApplication
        
        MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: sourceApp)
    }
```

#### <a name="acquire-tokens"></a>Adquirir tokens

Agora, podemos implementar a lógica de processamento da interface do usuário do aplicativo e obter os tokens interativamente por meio de MSAL.

MSAL expõe dois métodos principais para obter tokens: `acquireTokenSilently()` e `acquireTokenInteractively()`: 

- `acquireTokenSilently()` tenta entrar em um usuário e obter tokens sem qualquer interação do usuário, desde que uma conta esteja presente.

- `acquireTokenInteractively()` sempre mostra a IU ao tentar entrar no usuário. Ele pode usar cookies de sessão no navegador ou uma conta no Microsoft Authenticator para fornecer uma experiência de SSO interativo.

Adicione o seguinte código à classe `ViewController`:

```swift
    @objc func callGraphAPI(_ sender: AnyObject) {
        
        guard let currentAccount = self.currentAccount() else {
            // We check to see if we have a current logged in account.
            // If we don't, then we need to sign someone in.
            acquireTokenInteractively()
            return
        }
        
        acquireTokenSilently(currentAccount)
    }

    func currentAccount() -> MSALAccount? {
        
        guard let applicationContext = self.applicationContext else { return nil }
        
        // We retrieve our current account by getting the first account from cache
        // In multi-account applications, account should be retrieved by home account identifier or username instead
        
        do {
            let cachedAccounts = try applicationContext.allAccounts()
            if !cachedAccounts.isEmpty {
                return cachedAccounts.first
            }
        } catch let error as NSError {
            self.updateLogging(text: "Didn't find any accounts in cache: \(error)")
        }
        
        return nil
    }
```

#### <a name="get-a-token-interactively"></a>Obter um token interativamente

O código a seguir obtém um token pela primeira vez, criando um objeto de `MSALInteractiveTokenParameters` e chamando `acquireToken`. Em seguida, você adicionará o código que:

1. Cria `MSALInteractiveTokenParameters` com escopos.
2. Chama `acquireToken()` com os parâmetros criados.
3. Manipula erros. Para obter mais detalhes, consulte o [Guia de tratamento de erros do MSAL para IOS e MacOS](msal-handling-exceptions.md).
4. Manipula o caso bem-sucedido.

Adicione o código a seguir à classe `ViewController`.

```swift
func acquireTokenInteractively() {
        
    guard let applicationContext = self.applicationContext else { return }
    guard let webViewParameters = self.webViewParamaters else { return }
        
    // #1
    let parameters = MSALInteractiveTokenParameters(scopes: kScopes, webviewParameters: webViewParameters)
        
    // #2
    applicationContext.acquireToken(with: parameters) { (result, error) in
            
        // #3
        if let error = error {
                
            self.updateLogging(text: "Could not acquire token: \(error)")
            return
        }
            
        guard let result = result else {
                
            self.updateLogging(text: "Could not acquire token: No result returned")
            return
        }
            
        // #4
        self.accessToken = result.accessToken
        self.updateLogging(text: "Access token is \(self.accessToken)")
        self.updateSignOutButton(enabled: true)
        self.getContentWithToken()
    }
}    
```


#### <a name="get-a-token-silently"></a>Obter um token silenciosamente

Para adquirir um token atualizado silenciosamente, adicione o código a seguir à classe `ViewController`. Ele cria um objeto `MSALSilentTokenParameters` e chama `acquireTokenSilent()`:

```swift
    
    func acquireTokenSilently(_ account : MSALAccount!) {
        guard let applicationContext = self.applicationContext else { return }
        let parameters = MSALSilentTokenParameters(scopes: kScopes, account: account)
        
        applicationContext.acquireTokenSilent(with: parameters) { (result, error) in    
            if let error = error {
                let nsError = error as NSError
                if (nsError.domain == MSALErrorDomain) {
                    if (nsError.code == MSALError.interactionRequired.rawValue) {
                        DispatchQueue.main.async {
                            self.acquireTokenInteractively()
                        }
                        return
                    }
                }
                self.updateLogging(text: "Could not acquire token silently: \(error)")
                return
            }
            
            guard let result = result else {
                self.updateLogging(text: "Could not acquire token: No result returned")
                return
            }
            
            self.accessToken = result.accessToken
            self.updateLogging(text: "Refreshed Access token is \(self.accessToken)")
            self.updateSignOutButton(enabled: true)
            self.getContentWithToken()
        }
    }
```

### <a name="call-the-microsoft-graph-api"></a>Chamar a API de Microsoft Graph 

Quando você tiver um token, seu aplicativo poderá usá-lo no cabeçalho HTTP para fazer uma solicitação autorizada para o Microsoft Graph:

| chave de cabeçalho    | valor                 |
| ------------- | --------------------- |
| Autorização | Portador de acesso \<token > |

Adicione o seguinte código à classe `ViewController`:

```swift
    func getContentWithToken() {        
        // Specify the Graph API endpoint
        let url = URL(string: kGraphURI)
        var request = URLRequest(url: url!)
        
        // Set the Authorization header for the request. We use Bearer tokens, so we specify Bearer + the token we got from the result
        request.setValue("Bearer \(self.accessToken)", forHTTPHeaderField: "Authorization")
               
        URLSession.shared.dataTask(with: request) { data, response, error in
               
        if let error = error {
            self.updateLogging(text: "Couldn't get graph result: \(error)")
            return
        }
               
        guard let result = try? JSONSerialization.jsonObject(with: data!, options: []) else {
               
        self.updateLogging(text: "Couldn't deserialize result JSON")
            return
        }
               
        self.updateLogging(text: "Result from Graph: \(result))")
        
        }.resume()
    }
```

Consulte [Microsoft Graph API](https://graph.microsoft.com) para saber mais sobre a api de Microsoft Graph.

### <a name="use-msal-for-sign-out"></a>Usar MSAL para sair

Em seguida, adicione suporte para sair.

> [!Important]
> Sair com o MSAL remove todas as informações conhecidas sobre um usuário do aplicativo, mas o usuário ainda terá uma sessão ativa em seu dispositivo. Se o usuário tentar entrar novamente, poderá ver a interface do usuário de entrada, mas talvez não precise reinserir suas credenciais porque a sessão do dispositivo ainda está ativa.

Para adicionar a capacidade de saída, adicione o código a seguir dentro da classe `ViewController`. Esse método percorre todas as contas e as remove:

```swift 
@objc func signOut(_ sender: AnyObject) {
        
        guard let applicationContext = self.applicationContext else { return }
        
        guard let account = self.currentAccount() else { return }
        
        do {
            
            /**
             Removes all tokens from the cache for this application for the provided account
             
             - account:    The account to remove from the cache
             */
            
            try applicationContext.remove(account)
            self.updateLogging(text: "")
            self.updateSignOutButton(enabled: false)
            self.accessToken = ""
            
        } catch let error as NSError {
            
            self.updateLogging(text: "Received error signing account out: \(error)")
        }
    }
```

### <a name="enable-token-caching"></a>Habilitar cache de token

Por padrão, o MSAL armazena em cache os tokens do aplicativo no conjunto de chaves iOS ou macOS. 

Para habilitar o cache de token:
1. Verifique se seu aplicativo está assinado corretamente
2. Acesse a guia Configurações do projeto do Xcode > de **recursos** > **Habilitar compartilhamento** de conjunto de chaves
3. Clique em **+** e insira uma entrada de grupos de conjunto de **chaves** a seguir: 3. a para IOS, insira `com.microsoft.adalcache` 3. b para o MacOS Enter `com.microsoft.identity.universalstorage`

### <a name="add-helper-methods"></a>Adicionar métodos auxiliares
Adicione os seguintes métodos auxiliares à classe `ViewController` para concluir o exemplo.

### <a name="ios-ui"></a>interface do usuário do iOS:

``` swift
    
    func updateLogging(text : String) {
        
        if Thread.isMainThread {
            self.loggingText.text = text
        } else {
            DispatchQueue.main.async {
                self.loggingText.text = text
            }
        }
    }
    
    func updateSignOutButton(enabled : Bool) {
        if Thread.isMainThread {
            self.signOutButton.isEnabled = enabled
        } else {
            DispatchQueue.main.async {
                self.signOutButton.isEnabled = enabled
            }
        }
    }
```

### <a name="macos-ui"></a>interface do usuário do macOS:

```swift
func updateSignOutButton(enabled : Bool) {
        if Thread.isMainThread {
            self.signOutButton.isEnabled = enabled
        } else {
            DispatchQueue.main.async {
                self.signOutButton.isEnabled = enabled
            }
        }
    }
    
    func updateLogging(text : String) {
        
        if Thread.isMainThread {
            self.loggingText.string = text
        } else {
            DispatchQueue.main.async {
                self.loggingText.string = text
            }
        }
    }
```



### <a name="multi-account-applications"></a>Aplicativos de várias contas

Esse aplicativo é criado para um cenário de conta única. O MSAL também dá suporte a cenários de várias contas, mas requer algum trabalho adicional dos aplicativos. Você precisará criar a interface do usuário para ajudar os usuários a selecionar qual conta deseja usar para cada ação que exija tokens. Como alternativa, seu aplicativo pode implementar uma heurística para selecionar qual conta usar por meio do método `getAccounts()`.

## <a name="test-your-app"></a>Testar a aplicação

### <a name="run-locally"></a>Executar localmente

Crie e implante o aplicativo em um dispositivo de teste ou simulador. Você deve ser capaz de entrar e obter tokens para contas pessoais ou da Microsoft do Azure AD.

Na primeira vez que um usuário entrar em seu aplicativo, ele será solicitado pela identidade da Microsoft para consentir as permissões solicitadas.  Embora a maioria dos usuários seja capaz de consentir, alguns locatários do Azure AD desabilitaram o consentimento do usuário, o que exige que os administradores consentissem em nome de todos os usuários. Para dar suporte a esse cenário, registre os escopos do seu aplicativo no portal do Azure.

Depois de entrar, o aplicativo exibirá os dados retornados do ponto de extremidade do Microsoft Graph `/me`.

## <a name="get-help"></a>Obter ajuda

Visite [ajuda e suporte](https://docs.microsoft.com/azure/active-directory/develop/developer-support-help-options) se você tiver problemas com este tutorial ou com a plataforma de identidade da Microsoft.

Ajude-nos a melhorar a plataforma Microsoft Identity. Diga-nos o que você imagina ao concluir uma pesquisa curta de duas perguntas.

> [!div class="nextstepaction"]
> [Pesquisa sobre plataforma de identidade da Microsoft](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyKrNDMV_xBIiPGgSvnbQZdUQjFIUUFGUE1SMEVFTkdaVU5YT0EyOEtJVi4u)
