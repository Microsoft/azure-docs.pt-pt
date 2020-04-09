---
title: MSAL para iOS & tutorial macOS - plataforma de identidade da Microsoft Azure
description: Saiba como as aplicações iOS e MacOS (Swift) podem chamar uma API que requer acesso a fichas usando a plataforma de identidade da Microsoft
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.workload: identity
ms.date: 08/30/2019
ms.author: jmprieur
ms.reviewer: oldalton
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 3569d0404f6c0c7f78344ba53733ba2c0c43d979
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/08/2020
ms.locfileid: "80880810"
---
# <a name="sign-in-users-and-call-the-microsoft-graph-from-an-ios-or-macos-app"></a>Inscreva-se nos utilizadores e ligue para o Microsoft Graph a partir de uma aplicação iOS ou macOS

Neste tutorial, você vai aprender a integrar uma aplicação iOS ou macOS com a plataforma de identidade Microsoft. A aplicação irá assinar num utilizador, obter um sinal de acesso para ligar para a Microsoft Graph API e fazer um pedido para a API do Microsoft Graph.  

Quando tiver concluído o guia, a sua aplicação aceitará inscrições de contas pessoais da Microsoft (incluindo outlook.com, live.com e outras) e contas de trabalho ou escola de qualquer empresa ou organização que utilize o Diretório Ativo Azure.

>[!NOTE]
> Se for novidade na plataforma de identidade da Microsoft, recomendamos que comece com o [Sign in users e ligue para o Microsoft Graph API a partir de uma aplicação iOS ou macOS](quickstart-v2-ios.md).

## <a name="how-this-tutorial-works"></a>Como funciona este tutorial

![Mostra como funciona a aplicação de amostragerada por este tutorial](../../../includes/media/active-directory-develop-guidedsetup-ios-introduction/iosintro.svg)

A aplicação neste tutorial irá inscrever os utilizadores e obter dados em seu nome.  Estes dados serão acedidos através de uma API protegida (Microsoft Graph API neste caso) que requer autorização e está protegida pela plataforma de identidade da Microsoft.

Mais especificamente:

* A sua aplicação irá iniciar sessão no utilizador através de um browser ou do Autenticador Microsoft.
* O utilizador final aceitará as permissões que a sua aplicação solicitou.
* A sua aplicação será emitida um sinal de acesso para a API do Microsoft Graph.
* O token de acesso será incluído no pedido http para a Web API.
* Processe a resposta do Microsoft Graph.

Esta amostra utiliza a biblioteca de autenticação da Microsoft (MSAL) para implementar a Autenticação. A MSAL renovará automaticamente as fichas, entregará um único sinal (SSO) entre outras aplicações no dispositivo e gerirá a conta(s).

Este tutorial é aplicável tanto às aplicações iOS como macOS. Note que alguns passos são diferentes entre estas duas plataformas. 

## <a name="prerequisites"></a>Pré-requisitos

- A versão XCode 11.x ou maior é necessária para construir a aplicação neste guia. Pode descarregar o XCode a partir do site do [iTunes.](https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12 "URL de descarregamento de XCode")
- Biblioteca de Autenticação da Microsoft[(MSAL.framework).](https://github.com/AzureAD/microsoft-authentication-library-for-objc) Pode utilizar um gestor de dependência ou adicionar a biblioteca manualmente. As instruções abaixo mostram-lhe como.

Este tutorial vai criar um novo projeto. Se quiser descarregar o tutorial completo, faça o download do código:
- [código de amostra iOS](https://github.com/Azure-Samples/active-directory-ios-swift-native-v2/archive/master.zip)
- [código de amostra macOS](https://github.com/Azure-Samples/active-directory-macOS-swift-native-v2/archive/master.zip)

## <a name="create-a-new-project"></a>Criar um novo projeto

1. Abra o Xcode e selecione **Criar um novo projeto Xcode**.
2. Para aplicações iOS, selecione **iOS** > **Single view App** e selecione **Next**.
3. Para aplicações macOS, selecione **macOS** > **Cocoa App** e selecione **Next**.
4. Forneça um nome de produto.
5. Desloque o **idioma** para **Swift** e selecione **Next**.
6. Selecione uma pasta para criar a sua aplicação e clique em **Criar**.

## <a name="register-your-application"></a>Registar a sua aplicação

1. Aceda ao [Portal do Azure](https://aka.ms/MobileAppReg)
2. Abra a lâmina de registos da App e clique **em +Nova inscrição.**
3. Introduza um **Nome** para a sua aplicação e, em seguida, sem configurar um Redirect URI.
4. Selecione **Contas em qualquer diretório organizacional (Qualquer diretório DaA Azure - Multitenant) e contas pessoais da Microsoft (por exemplo, Skype, Xbox)** sob tipos de **conta suportada**
5. Clique **no Registo**
6. Na secção **Gerir** o painel que aparece, selecione **Autenticação**.

7. Clique **Em Experimentar a nova experiência** perto da parte superior do ecrã para abrir a nova experiência de registo de aplicações e, em seguida, clique em **+Nova inscrição** > **+ Adicione uma plataforma** > **iOS/macOS**.
    - Insira o Bundle ID do seu projeto. Se descarregou o código, `com.microsoft.identitysample.MSALiOS`isto é. Se estiver a criar o seu próprio projeto, selecione o seu projeto em Xcode e abra o separador **Geral.** O identificador de pacote aparece na secção **identidade.**
8. Clique `Configure` e guarde a **Configuração MSAL** que aparece na página de configuração do **MSAL** para que possa inseri-la quando configurar a sua aplicação mais tarde.  Clique em **Concluído**.

## <a name="add-msal"></a>Adicionar MSAL

Escolha uma das seguintes formas de instalar a biblioteca MSAL na sua aplicação:

### <a name="cocoapods"></a>CocoaPods

1. Se estiver a utilizar [CocoaPods,](https://cocoapods.org/) `MSAL` instale-se `podfile` primeiro criando um ficheiro `.xcodeproj` vazio chamado na mesma pasta que o ficheiro do seu projeto. Adicione o `podfile`seguinte:

   ```
   use_frameworks!
   
   target '<your-target-here>' do
      pod 'MSAL'
   end
   ```

2. Substitua-o `<your-target-here>` pelo nome do seu projeto.
3. Numa janela terminal, navegue para `podfile` a pasta `pod install` que contém a que criou e corra para instalar a biblioteca MSAL.
4. Feche o Xcode e abra `<your project name>.xcworkspace` para recarregar o projeto em Xcode.

### <a name="carthage"></a>Cartato

Se estiver a utilizar [cartada,](https://github.com/Carthage/Carthage)instale `MSAL` `Cartfile`adicionando-a ao seu:

```
github "AzureAD/microsoft-authentication-library-for-objc" "master"
```

A partir de uma janela terminal, `Cartfile`no mesmo diretório que o atualizado, execute o seguinte comando para que a Cartagem atualize as dependências do seu projeto.

iOS:

```bash
carthage update --platform iOS
```

macOS:

```bash
carthage update --platform macOS
```

### <a name="manually"></a>Manualmente

Também pode utilizar o Submódulo Git ou verificar a versão mais recente para usar como enquadramento na sua aplicação.

## <a name="add-your-app-registration"></a>Adicione o registo da sua aplicação

Em seguida, adicionaremos o registo da sua aplicação ao seu código. 

Em primeiro lugar, adicione a seguinte `ViewController.swift`declaração de `AppDelegate.swift` `SceneDelegate.swift` importação ao topo do , bem como ou ficheiros:

```swift
import MSAL
```

Em seguida, adicione `ViewController.swift` o `viewDidLoad()`seguinte código a anterior a:

```swift
// Update the below to your client ID you received in the portal. The below is for running the demo only
let kClientID = "Your_Application_Id_Here"
let kGraphEndpoint = "https://graph.microsoft.com/" // the Microsoft Graph endpoint
let kAuthority = "https://login.microsoftonline.com/common" // this authority allows a personal Microsoft account and a work or school account in any organization’s Azure AD tenant to sign in
    
let kScopes: [String] = ["user.read"] // request permission to read the profile of the signed-in user
    
var accessToken = String()
var applicationContext : MSALPublicClientApplication?
var webViewParameters : MSALWebviewParameters?
var currentAccount: MSALAccount?
```

O único valor que precisa de modificar `kClientID`acima é o valor atribuído para ser o seu ID de [aplicação](https://docs.microsoft.com/azure/active-directory/develop/developer-glossary#application-id-client-id). Este valor faz parte dos dados de Configuração MSAL que guardou durante o passo no início deste tutorial para registar a aplicação no portal Azure.

## <a name="for-ios-only-configure-url-schemes"></a>Apenas para iOS, configure esquemas de URL

Neste passo, registar-se-á `CFBundleURLSchemes` para que o utilizador possa ser redirecionado para a aplicação após o início do início. A propósito, `LSApplicationQueriesSchemes` também permite que a sua aplicação utilize o Microsoft Authenticator.

No Xcode, `Info.plist` abra como um ficheiro código fonte `<dict>` e adicione o seguinte dentro da secção. Substitua `[BUNDLE_ID]` pelo valor utilizado no portal Azure que, se `com.microsoft.identitysample.MSALiOS`descarregou o código, é . Se estiver a criar o seu próprio projeto, selecione o seu projeto em Xcode e abra o separador **Geral.** O identificador de pacote aparece na secção **identidade.**

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

## <a name="for-macos-only-configure-app-sandbox"></a>Apenas para macOS, configure App Sandbox

1. Vá ao seu Xcode Project Definições > **Capabilities aba** > **App Sandbox**
2. Selecione **'Outgoing Connections ' (Cliente)** checkbox. 

## <a name="create-your-apps-ui"></a>Crie o UI da sua aplicação

Agora crie um UI que inclua um botão para ligar para o Microsoft Graph API, outro `ViewController`para assinar, e uma visão de texto para ver alguma saída adicionando o seguinte código à classe:

### <a name="ios-ui"></a>IOS UI

```swift
var loggingText: UITextView!
var signOutButton: UIButton!
var callGraphButton: UIButton!
var usernameLabel: UILabel!

func initUI() {
        
    usernameLabel = UILabel()
    usernameLabel.translatesAutoresizingMaskIntoConstraints = false
    usernameLabel.text = ""
    usernameLabel.textColor = .darkGray
    usernameLabel.textAlignment = .right
        
    self.view.addSubview(usernameLabel)
        
    usernameLabel.topAnchor.constraint(equalTo: view.topAnchor, constant: 50.0).isActive = true
    usernameLabel.rightAnchor.constraint(equalTo: view.rightAnchor, constant: -10.0).isActive = true
    usernameLabel.widthAnchor.constraint(equalToConstant: 300.0).isActive = true
    usernameLabel.heightAnchor.constraint(equalToConstant: 50.0).isActive = true
        
    // Add call Graph button
    callGraphButton  = UIButton()
    callGraphButton.translatesAutoresizingMaskIntoConstraints = false
    callGraphButton.setTitle("Call Microsoft Graph API", for: .normal)
    callGraphButton.setTitleColor(.blue, for: .normal)
    callGraphButton.addTarget(self, action: #selector(callGraphAPI(_:)), for: .touchUpInside)
    self.view.addSubview(callGraphButton)
        
    callGraphButton.centerXAnchor.constraint(equalTo: view.centerXAnchor).isActive = true
    callGraphButton.topAnchor.constraint(equalTo: view.topAnchor, constant: 120.0).isActive = true
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
        
    let deviceModeButton = UIButton()
    deviceModeButton.translatesAutoresizingMaskIntoConstraints = false
    deviceModeButton.setTitle("Get device info", for: .normal);
    deviceModeButton.setTitleColor(.blue, for: .normal);
    deviceModeButton.addTarget(self, action: #selector(getDeviceMode(_:)), for: .touchUpInside)
    self.view.addSubview(deviceModeButton)
        
    deviceModeButton.centerXAnchor.constraint(equalTo: view.centerXAnchor).isActive = true
    deviceModeButton.topAnchor.constraint(equalTo: signOutButton.bottomAnchor, constant: 10.0).isActive = true
    deviceModeButton.widthAnchor.constraint(equalToConstant: 150.0).isActive = true
    deviceModeButton.heightAnchor.constraint(equalToConstant: 50.0).isActive = true
        
    // Add logging textfield
    loggingText = UITextView()
    loggingText.isUserInteractionEnabled = false
    loggingText.translatesAutoresizingMaskIntoConstraints = false
        
    self.view.addSubview(loggingText)
        
    loggingText.topAnchor.constraint(equalTo: deviceModeButton.bottomAnchor, constant: 10.0).isActive = true
    loggingText.leftAnchor.constraint(equalTo: self.view.leftAnchor, constant: 10.0).isActive = true
    loggingText.rightAnchor.constraint(equalTo: self.view.rightAnchor, constant: -10.0).isActive = true
    loggingText.bottomAnchor.constraint(equalTo: self.view.bottomAnchor, constant: 10.0).isActive = true
}

func platformViewDidLoadSetup() {
                
    NotificationCenter.default.addObserver(self,
                        selector: #selector(appCameToForeGround(notification:)),
                        name: UIApplication.willEnterForegroundNotification,
                        object: nil)
        
}
    
@objc func appCameToForeGround(notification: Notification) {
    self.loadCurrentAccount()
}

```

### <a name="macos-ui"></a>MacOS UI

```swift

var callGraphButton: NSButton!
var loggingText: NSTextView!
var signOutButton: NSButton!
    
var usernameLabel: NSTextField!

func initUI() {
        
    usernameLabel = NSTextField()
    usernameLabel.translatesAutoresizingMaskIntoConstraints = false
    usernameLabel.stringValue = ""
    usernameLabel.isEditable = false
    usernameLabel.isBezeled = false
    self.view.addSubview(usernameLabel)
        
    usernameLabel.topAnchor.constraint(equalTo: view.topAnchor, constant: 30.0).isActive = true
    usernameLabel.rightAnchor.constraint(equalTo: view.rightAnchor, constant: -10.0).isActive = true
        
    // Add call Graph button
    callGraphButton  = NSButton()
    callGraphButton.translatesAutoresizingMaskIntoConstraints = false
    callGraphButton.title = "Call Microsoft Graph API"
    callGraphButton.target = self
    callGraphButton.action = #selector(callGraphAPI(_:))
    callGraphButton.bezelStyle = .rounded
    self.view.addSubview(callGraphButton)
        
    callGraphButton.centerXAnchor.constraint(equalTo: view.centerXAnchor).isActive = true
    callGraphButton.topAnchor.constraint(equalTo: view.topAnchor, constant: 50.0).isActive = true
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

func platformViewDidLoadSetup() {}

```

Em seguida, `ViewController` também dentro `viewDidLoad()` da aula, substitua o método por:

```swift
    override func viewDidLoad() {

        super.viewDidLoad()

        initUI()
        
        do {
            try self.initMSAL()
        } catch let error {
            self.updateLogging(text: "Unable to create Application Context \(error)")
        }
        
        self.loadCurrentAccount()
        self.platformViewDidLoadSetup()
    }
```

## <a name="use-msal"></a>Use MSAL

### <a name="initialize-msal"></a>Inicializar mSAL

Adicione o `initMSAL` seguinte `ViewController` método à classe:

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

Adicione o `initMSAL` seguinte método `ViewController` após a aula.

### <a name="ios-code"></a>código iOS:

```swift
func initWebViewParams() {
        self.webViewParameters = MSALWebviewParameters(parentViewController: self)
    }
```

### <a name="macos-code"></a>código macOS:

```swift
func initWebViewParams() {
        self.webViewParameters = MSALWebviewParameters()
    }
```

### <a name="for-ios-only-handle-the-sign-in-callback"></a>Apenas para iOS, manuseie o backback de inscrição

Abra o ficheiro `AppDelegate.swift`. Para lidar com a chamada após `MSALPublicClientApplication.handleMSALResponse` o `appDelegate` início de sessão, adicione à classe como esta:

```swift
// Inside AppDelegate...
func application(_ app: UIApplication, open url: URL, options: [UIApplication.OpenURLOptionsKey : Any] = [:]) -> Bool {
        
        return MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: options[UIApplication.OpenURLOptionsKey.sourceApplication] as? String)
}

```

Se estiver a utilizar o **Xcode 11,** deve `SceneDelegate.swift` colocar a chamada MSAL no lugar.
Se apoiar tanto o UISceneDelegate como o UIApplicationDelegate para a compatibilidade com iOS mais antigo, o backback do MSAL terá de ser colocado em ambos os ficheiros.

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

#### <a name="acquire-tokens"></a>Adquirir Fichas

Agora, podemos implementar a lógica de processamento de UI da aplicação e obter fichas interativamente através do MSAL.

A MSAL expõe dois métodos primários `acquireTokenSilently()` `acquireTokenInteractively()`para obter fichas: e: 

- `acquireTokenSilently()`tenta assinar num utilizador e obter fichas sem qualquer interação do utilizador enquanto uma conta estiver presente. `acquireTokenSilently()`requer o fornecimento `MSALAccount` de um válido que pode ser recuperado utilizando uma das APIs de enumeração da conta MSAL. Esta amostra `applicationContext.getCurrentAccount(with: msalParameters, completionBlock: {})` utiliza para recuperar a conta corrente. 

- `acquireTokenInteractively()`mostra sempre uI ao tentar assinar no utilizador. Pode utilizar cookies de sessão no navegador ou uma conta no autenticador da Microsoft para fornecer uma experiência interativa-SSO.

Adicione o seguinte `ViewController` código à classe:

```swift
    func getGraphEndpoint() -> String {
        return kGraphEndpoint.hasSuffix("/") ? (kGraphEndpoint + "v1.0/me/") : (kGraphEndpoint + "/v1.0/me/");
    }

    @objc func callGraphAPI(_ sender: AnyObject) {
        
        self.loadCurrentAccount { (account) in
            
            guard let currentAccount = account else {
                
                // We check to see if we have a current logged in account.
                // If we don't, then we need to sign someone in.
                self.acquireTokenInteractively()
                return
            }
            
            self.acquireTokenSilently(currentAccount)
        }
    }

    typealias AccountCompletion = (MSALAccount?) -> Void

    func loadCurrentAccount(completion: AccountCompletion? = nil) {
        
        guard let applicationContext = self.applicationContext else { return }
        
        let msalParameters = MSALParameters()
        msalParameters.completionBlockQueue = DispatchQueue.main
                
        applicationContext.getCurrentAccount(with: msalParameters, completionBlock: { (currentAccount, previousAccount, error) in
            
            if let error = error {
                self.updateLogging(text: "Couldn't query current account with error: \(error)")
                return
            }
            
            if let currentAccount = currentAccount {
                
                self.updateLogging(text: "Found a signed in account \(String(describing: currentAccount.username)). Updating data for that account...")
                
                self.updateCurrentAccount(account: currentAccount)
                
                if let completion = completion {
                    completion(self.currentAccount)
                }
                
                return
            }
            
            self.updateLogging(text: "Account signed out. Updating UX")
            self.accessToken = ""
            self.updateCurrentAccount(account: nil)
            
            if let completion = completion {
                completion(nil)
            }
        })
    }
```

#### <a name="get-a-token-interactively"></a>Obtenha um símbolo interativamente

O código abaixo recebe um símbolo pela `MSALInteractiveTokenParameters` primeira vez `acquireToken`criando um objeto e chamando . Em seguida, irá adicionar código que:

1. Cria `MSALInteractiveTokenParameters` com miras.
2. Chamadas `acquireToken()` com os parâmetros criados.
3. Lida com erros. Para mais detalhes, consulte o MSAL para obter o guia de manuseamento de [erros iOS e macOS](msal-handling-exceptions.md).
4. Lida com o caso de sucesso.

Adicione o seguinte `ViewController` código à classe.

```swift
func acquireTokenInteractively() {
        
    guard let applicationContext = self.applicationContext else { return }
    guard let webViewParameters = self.webViewParameters else { return }
        
    // #1
    let parameters = MSALInteractiveTokenParameters(scopes: kScopes, webviewParameters: webViewParameters)
    parameters.promptType = .selectAccount
        
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
        self.updateCurrentAccount(account: result.account)
        self.getContentWithToken()
    }
}    
```


#### <a name="get-a-token-silently"></a>Pegue um símbolo silenciosamente

Para adquirir um token atualizado silenciosamente, `ViewController` adicione o seguinte código à classe. Cria um `MSALSilentTokenParameters` objeto `acquireTokenSilent()`e chama:

```swift
    
    func acquireTokenSilently(_ account : MSALAccount!) {
        
        guard let applicationContext = self.applicationContext else { return }
        
        /**
         
         Acquire a token for an existing account silently
         
         - forScopes:           Permissions you want included in the access token received
         in the result in the completionBlock. Not all scopes are
         guaranteed to be included in the access token returned.
         - account:             An account object that we retrieved from the application object before that the
         authentication flow will be locked down to.
         - completionBlock:     The completion block that will be called when the authentication
         flow completes, or encounters an error.
         */
        
        let parameters = MSALSilentTokenParameters(scopes: kScopes, account: account)
        
        applicationContext.acquireTokenSilent(with: parameters) { (result, error) in
            
            if let error = error {
                
                let nsError = error as NSError
                
                // interactionRequired means we need to ask the user to sign-in. This usually happens
                // when the user's Refresh Token is expired or if the user has changed their password
                // among other possible reasons.
                
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

### <a name="call-the-microsoft-graph-api"></a>Ligue para a Microsoft Graph API 

Uma vez que tenha um token, a sua aplicação pode usá-la no cabeçalho HTTP para fazer um pedido autorizado ao Microsoft Graph:

| chave cabeçalho    | valor                 |
| ------------- | --------------------- |
| Autorização | > \<de acesso ao portador |

Adicione o seguinte `ViewController` código à classe:

```swift
    func getContentWithToken() {
        
        // Specify the Graph API endpoint
        let graphURI = getGraphEndpoint()
        let url = URL(string: graphURI)
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

Consulte a [Microsoft Graph API](https://graph.microsoft.com) para saber mais sobre a API do Microsoft Graph.

### <a name="use-msal-for-sign-out"></a>Utilizar o MSAL para o sign-out

Em seguida, adicione suporte para a inscrição.

> [!Important]
> A assinatura com o MSAL remove todas as informações conhecidas sobre um utilizador da aplicação, bem como a remoção de uma sessão ativa no seu dispositivo quando permitida a configuração do dispositivo. Também pode opcionalmente assinar o utilizador a partir do navegador.

Para adicionar capacidade de inscrição, adicione `ViewController` o seguinte código dentro da classe. 

```swift 
@objc func signOut(_ sender: AnyObject) {
        
        guard let applicationContext = self.applicationContext else { return }
        
        guard let account = self.currentAccount else { return }
        
        do {
            
            /**
             Removes all tokens from the cache for this application for the provided account
             
             - account:    The account to remove from the cache
             */
            
            let signoutParameters = MSALSignoutParameters(webviewParameters: self.webViewParameters!)
            signoutParameters.signoutFromBrowser = false // set this to true if you also want to signout from browser or webview
            
            applicationContext.signout(with: account, signoutParameters: signoutParameters, completionBlock: {(success, error) in
                
                if let error = error {
                    self.updateLogging(text: "Couldn't sign out account with error: \(error)")
                    return
                }
                
                self.updateLogging(text: "Sign out completed successfully")
                self.accessToken = ""
                self.updateCurrentAccount(account: nil)
            })
            
        }
    }
```

### <a name="enable-token-caching"></a>Ativar o caching de token

Por padrão, a MSAL coloca as fichas da sua aplicação no porta-chaves iOS ou macOS. 

Para permitir o cache do token:
1. Certifique-se de que a sua candidatura está devidamente assinada
2. Vá ao separador de definições de > **do** > projeto Xcode,**enable keychain Sharing**
3. Clique **+** e introduza uma seguinte entrada **de Grupos keychain:** 3.a Para iOS, insira `com.microsoft.adalcache` 3.b Para entrar macOS`com.microsoft.identity.universalstorage`

### <a name="add-helper-methods"></a>Adicione métodos de ajuda
Adicione os seguintes métodos de ajudante à `ViewController` classe para completar a amostra.

### <a name="ios-ui"></a>IOS UI:

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
    
    func updateAccountLabel() {
        
        guard let currentAccount = self.currentAccount else {
            self.usernameLabel.text = "Signed out"
            return
        }
        
        self.usernameLabel.text = currentAccount.username
    }
    
    func updateCurrentAccount(account: MSALAccount?) {
        self.currentAccount = account
        self.updateAccountLabel()
        self.updateSignOutButton(enabled: account != nil)
    }
```

### <a name="macos-ui"></a>MacOS UI:

```swift
    func updateLogging(text : String) {
        
        if Thread.isMainThread {
            self.loggingText.string = text
        } else {
            DispatchQueue.main.async {
                self.loggingText.string = text
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
    
     func updateAccountLabel() {

         guard let currentAccount = self.currentAccount else {
            self.usernameLabel.stringValue = "Signed out"
            return
        }

        self.usernameLabel.stringValue = currentAccount.username ?? ""
        self.usernameLabel.sizeToFit()
     }

     func updateCurrentAccount(account: MSALAccount?) {
        self.currentAccount = account
        self.updateAccountLabel()
        self.updateSignOutButton(enabled: account != nil)
    }
```

### <a name="for-ios-only-get-additional-device-information"></a>Apenas para iOS, obtenha informações adicionais sobre dispositivos

Utilize o seguinte código para ler a configuração do dispositivo atual, incluindo se o dispositivo está configurado como partilhado:

```swift
    @objc func getDeviceMode(_ sender: AnyObject) {
        
        if #available(iOS 13.0, *) {
            self.applicationContext?.getDeviceInformation(with: nil, completionBlock: { (deviceInformation, error) in
                
                guard let deviceInfo = deviceInformation else {
                    self.updateLogging(text: "Device info not returned. Error: \(String(describing: error))")
                    return
                }
                
                let isSharedDevice = deviceInfo.deviceMode == .shared
                let modeString = isSharedDevice ? "shared" : "private"
                self.updateLogging(text: "Received device info. Device is in the \(modeString) mode.")
            })
        } else {
            self.updateLogging(text: "Running on older iOS. GetDeviceInformation API is unavailable.")
        }
    }
```

### <a name="multi-account-applications"></a>Aplicações multi-contas

Esta aplicação é construída para um cenário de conta única. A MSAL também suporta cenários multi-contas, mas requer algum trabalho adicional a partir de apps. Terá de criar UI para ajudar os utilizadores a selecionar em que conta querem utilizar para cada ação que requer fichas. Em alternativa, a sua aplicação pode implementar uma heurística para selecionar qual a conta a utilizar consultando todas as contas da MSAL. Por exemplo, `accountsFromDeviceForParameters:completionBlock:` ver [API](https://azuread.github.io/microsoft-authentication-library-for-objc/Classes/MSALPublicClientApplication.html#/c:objc(cs)MSALPublicClientApplication(im)accountsFromDeviceForParameters:completionBlock:)

## <a name="test-your-app"></a>Testar a aplicação

### <a name="run-locally"></a>Executar localmente

Construa e implemente a aplicação para um dispositivo de teste ou simulador. Deverá poder iniciar sessão e obter fichas para contas Azure AD ou pessoais da Microsoft.

A primeira vez que um utilizador entra na sua aplicação, será solicitado pela identidade da Microsoft para consentir com as permissões solicitadas.  Embora a maioria dos utilizadores seja capaz de consentir, alguns inquilinos da AD Azure têm o consentimento de utilizador desativado, o que requer administradores para consentir em nome de todos os utilizadores. Para suportar este cenário, registe os âmbitos da sua aplicação no portal Azure.

Depois de iniciar sessão, a aplicação apresentará os dados devolvidos a partir do ponto final do Microsoft Graph. `/me`

## <a name="get-help"></a>Obter ajuda

Visite [ajuda e suporte](https://docs.microsoft.com/azure/active-directory/develop/developer-support-help-options) se tiver problemas com este tutorial ou com a plataforma de identidade da Microsoft.

Ajude-nos a melhorar a plataforma de identidade da Microsoft. Diga-nos o que pensa ao concluir um pequeno inquérito de duas perguntas.

> [!div class="nextstepaction"]
> [Pesquisa da plataforma de identidade da Microsoft](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyKrNDMV_xBIiPGgSvnbQZdUQjFIUUFGUE1SMEVFTkdaVU5YT0EyOEtJVi4u)
