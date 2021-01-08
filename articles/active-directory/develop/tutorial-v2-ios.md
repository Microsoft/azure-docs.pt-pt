---
title: 'Tutorial: Criar uma aplicação para iOS ou macOS que utilize a plataforma de identidade da Microsoft para autenticação Rio Azure'
titleSuffix: Microsoft identity platform
description: Neste tutorial, você constrói uma aplicação iOS ou macOS que utiliza a plataforma de identidade da Microsoft para assinar nos utilizadores e obter um token de acesso para ligar para a API do Microsoft Graph em seu nome.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.workload: identity
ms.date: 09/18/2020
ms.author: marsma
ms.reviewer: oldalton
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: c9e4997ad08f2dd1d96dd442f80ad4203abf6261
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/08/2021
ms.locfileid: "98015891"
---
# <a name="tutorial-sign-in-users-and-call-microsoft-graph-from-an-ios-or-macos-app"></a>Tutorial: Inscreva-se nos utilizadores e ligue para o Microsoft Graph a partir de uma aplicação para iOS ou macOS

Neste tutorial, você constrói uma aplicação iOS ou macOS que se integra com a plataforma de identidade da Microsoft para assinar utilizadores e obter um token de acesso para ligar para a Microsoft Graph API.

Quando tiver concluído o guia, a sua aplicação aceitará inscrições de contas pessoais da Microsoft (incluindo outlook.com, live.com e outras) e contas de trabalho ou escola de qualquer empresa ou organização que utilize o Azure Ative Directory. Este tutorial é aplicável tanto às aplicações iOS como ao macOS. Alguns passos são diferentes entre as duas plataformas.

Neste tutorial:

> [!div class="checklist"]
> * Criar um projeto de aplicação iOS ou macOS no *Xcode*
> * Registe a app no portal Azure
> * Adicione código para suportar a inscrição do utilizador e a s inscrição
> * Adicione código para ligar para a Microsoft Graph API
> * Testar a aplicação

## <a name="prerequisites"></a>Pré-requisitos

- [Xcode 11.x+](https://developer.apple.com/xcode/)

## <a name="how-tutorial-app-works"></a>Como funciona a aplicação tutorial

![Mostra como funciona a app de amostras gerada por este tutorial](../../../includes/media/active-directory-develop-guidedsetup-ios-introduction/iosintro.svg)

A aplicação neste tutorial pode iniciar sísmis nos utilizadores e obter dados do Microsoft Graph em seu nome. Estes dados serão acedidos através de uma API protegida (Microsoft Graph API neste caso) que requer autorização e está protegida pela plataforma de identidade da Microsoft.

Mais especificamente:

* A sua aplicação irá iniciar sôms no utilizador através de um browser ou do Microsoft Authenticator.
* O utilizador final aceitará as permissões que a sua aplicação solicitou.
* A sua aplicação será emitida um token de acesso para a API do Gráfico da Microsoft.
* O token de acesso será incluído no pedido HTTP para a API web.
* Processe a resposta do Microsoft Graph.

Esta amostra utiliza a Biblioteca de Autenticação do Microsoft (MSAL) para implementar a Autenticação. A MSAL renovará automaticamente os tokens, entregará um único sign-on (SSO) entre outras aplicações no dispositivo e gerirá a conta.

Se quiser descarregar uma versão completa da app que constrói neste tutorial, pode encontrar ambas as versões no GitHub:

- [amostra de código iOS](https://github.com/Azure-Samples/active-directory-ios-swift-native-v2/) (GitHub)
- [amostra de código macOS](https://github.com/Azure-Samples/active-directory-macOS-swift-native-v2/) (GitHub)

## <a name="create-a-new-project"></a>Criar um novo projeto

1. Abra o Xcode e **selecione Criar um novo projeto Xcode**.
2. Para aplicações iOS, selecione a aplicação de visualização única **do iOS**  >   e selecione **Next**.
3. Para aplicações para macOS, selecione **macOS**  >  **App Cocoa** e selecione **Next**.
4. Forneça um nome de produto.
5. Desa estaca o **idioma** para **Swift** e selecione **Seguinte**.
6. Selecione uma pasta para criar a sua aplicação e selecione **Criar**.

## <a name="register-your-application"></a>Registar a aplicação

1. Inscreva-se no <a href="https://portal.azure.com/" target="_blank">portal <span class="docon docon-navigate-external x-hidden-focus"></span> Azure</a>.
1. Se tiver acesso a vários inquilinos, utilize o filtro **de subscrição Diretório +** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: no menu superior para selecionar o inquilino no qual pretende registar uma candidatura.
1. Procure e selecione **Azure Active Directory**.
1. Em **Gestão**, selecione **registos de aplicações**  >  **Novo registo**.
1. Insira um **Nome** para a sua inscrição. Os utilizadores da sua aplicação podem ver este nome, e pode alterá-lo mais tarde.
1. Selecione **Contas em qualquer diretório organizacional (qualquer diretório AD AZure - Multitenant) e contas pessoais da Microsoft (por exemplo, Skype, Xbox)** sob **tipos de conta suportados**.
1. Selecione **Registar**.
1. Em **Gestão**, selecione **Autenticação**  >  **Adicione uma plataforma**  >  **iOS/macOS**.
1. Insira o Bundle ID do seu projeto. Se descarregou o código, este é `com.microsoft.identitysample.MSALiOS` . Se estiver a criar o seu próprio projeto, selecione o seu projeto no Xcode e abra o separador **Geral.** O identificador do pacote aparece na secção **identidade.**
1. Selecione **Configurar** e guarde a **Configuração MSAL** que aparece na página **de configuração MSAL** para que possa introduzi-la quando configurar a sua aplicação mais tarde. 
1. Selecione **Concluído**.

## <a name="add-msal"></a>Adicionar MSAL

Escolha uma das seguintes formas de instalar a biblioteca MSAL na sua aplicação:

### <a name="cocoapods"></a>Cacau

1. Se estiver a utilizar [Cacau, instale-o](https://cocoapods.org/) `MSAL` primeiro criando um ficheiro vazio chamado `podfile` na mesma pasta que o ficheiro do seu `.xcodeproj` projeto. Adicione o seguinte `podfile` a:

   ```
   use_frameworks!

   target '<your-target-here>' do
      pod 'MSAL'
   end
   ```

2. `<your-target-here>`Substitua-o pelo nome do seu projeto.
3. Numa janela terminal, navegue para a pasta que contém a `podfile` que criou e corra para instalar a biblioteca `pod install` MSAL.
4. Feche o Xcode e abra `<your project name>.xcworkspace` para recarregar o projeto em Xcode.

### <a name="carthage"></a>Cartago

Se estiver a utilizar [a Cartago,](https://github.com/Carthage/Carthage)instale-a `MSAL` adicionando-a ao seu `Cartfile` :

```
github "AzureAD/microsoft-authentication-library-for-objc" "master"
```

A partir de uma janela terminal, no mesmo diretório que o `Cartfile` atualizado, executar o seguinte comando para que Cartago atualize as dependências do seu projeto.

iOS:

```bash
carthage update --platform iOS
```

macOS:

```bash
carthage update --platform macOS
```

### <a name="manually"></a>Manualmente

Também pode utilizar o Git Submodule ou consultar a versão mais recente para usar como estrutura na sua aplicação.

## <a name="add-your-app-registration"></a>Adicione o registo da sua aplicação

Em seguida, adicionaremos o seu registo de aplicações ao seu código.

Em primeiro lugar, adicione a seguinte declaração de importação ao topo do `ViewController.swift` , bem como ou `AppDelegate.swift` `SceneDelegate.swift` ficheiros:

```swift
import MSAL
```

Em seguida, adicione o seguinte código `ViewController.swift` a `viewDidLoad()` antes de:

```swift
// Update the below to your client ID you received in the portal. The below is for running the demo only
let kClientID = "Your_Application_Id_Here"
let kGraphEndpoint = "https://graph.microsoft.com/" // the Microsoft Graph endpoint
let kAuthority = "https://login.microsoftonline.com/common" // this authority allows a personal Microsoft account and a work or school account in any organization's Azure AD tenant to sign in

let kScopes: [String] = ["user.read"] // request permission to read the profile of the signed-in user

var accessToken = String()
var applicationContext : MSALPublicClientApplication?
var webViewParameters : MSALWebviewParameters?
var currentAccount: MSALAccount?
```

O único valor que precisa de modificar acima é o valor atribuído `kClientID` para ser o seu [ID de aplicação.](./developer-glossary.md#application-id-client-id) Este valor faz parte dos dados de Configuração MSAL que guardou durante o passo no início deste tutorial para registar a aplicação no portal Azure.

## <a name="configure-xcode-project-settings"></a>Configurar definições de projeto xcode

Adicione um novo grupo de chaveiros ao seu projeto **Assinando & Capacidades**. O grupo keychain deve estar `com.microsoft.adalcache` no iOS e `com.microsoft.identity.universalstorage` no macOS.

![Xcode UI mostrando como o grupo de chavechain deve ser configurado](../../../includes/media/active-directory-develop-guidedsetup-ios-introduction/iosintro-keychainShare.png)

## <a name="for-ios-only-configure-url-schemes"></a>Apenas para iOS, configurar esquemas de URL

Neste passo, registar-se-á `CFBundleURLSchemes` para que o utilizador possa ser redirecionado de volta para a app após o início de sedu. A propósito, `LSApplicationQueriesSchemes` também permite que a sua aplicação utilize o Microsoft Authenticator.

Em Xcode, abra `Info.plist` como um ficheiro de código fonte e adicione o seguinte dentro da `<dict>` secção. `[BUNDLE_ID]`Substitua-o pelo valor utilizado no portal Azure. Se descarregou o código, o identificador do pacote é `com.microsoft.identitysample.MSALiOS` . Se estiver a criar o seu próprio projeto, selecione o seu projeto no Xcode e abra o separador **Geral.** O identificador do pacote aparece na secção **identidade.**

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

## <a name="for-macos-only-configure-app-sandbox"></a>Apenas para macOS, configurar app Sandbox

1. Aceda ao seu projeto Xcode Definições > **capabilites tab**  >  **App Sandbox**
2. Selecione a caixa **de verificação Desaudido (Cliente).**

## <a name="create-your-apps-ui"></a>Crie uI da sua aplicação

Agora crie um UI que inclua um botão para ligar para a API do Gráfico microsoft, outro para assinar e uma visão de texto para ver alguma saída adicionando o seguinte código à `ViewController` classe:

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

### <a name="macos-ui"></a>UI macOS

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

Em seguida, também dentro da `ViewController` classe, substitua o `viewDidLoad()` método por:

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

## <a name="use-msal"></a>Utilizar MSAL

### <a name="initialize-msal"></a>Inicializar o MSAL

Adicione o seguinte `initMSAL` método à `ViewController` classe:

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

Adicione o seguinte `initMSAL` método após método à `ViewController` classe.

### <a name="ios-code"></a>Código iOS:

```swift
func initWebViewParams() {
        self.webViewParameters = MSALWebviewParameters(authPresentationViewController: self)
    }
```

### <a name="macos-code"></a>código macOS:

```swift
func initWebViewParams() {
        self.webViewParameters = MSALWebviewParameters()
    }
```

### <a name="for-ios-only-handle-the-sign-in-callback"></a>Apenas para iOS, manuseie a chamada de entrada

Abra o ficheiro `AppDelegate.swift`. Para lidar com a chamada após o início de sôr- in, adicione `MSALPublicClientApplication.handleMSALResponse` à `appDelegate` classe desta forma:

```swift
// Inside AppDelegate...
func application(_ app: UIApplication, open url: URL, options: [UIApplication.OpenURLOptionsKey : Any] = [:]) -> Bool {

        return MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: options[UIApplication.OpenURLOptionsKey.sourceApplication] as? String)
}

```

**Se estiver a utilizar o Xcode 11,** deverá colocar a chamada MSAL no `SceneDelegate.swift` mesmo.
Se apoiar tanto o UISceneDeeDeegate como o UIApplicationDeegate para compatibilidade com iOS mais antigo, o callback MSAL teria de ser colocado em ambos os ficheiros.

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

Agora, podemos implementar a lógica de processamento de UI da aplicação e obter fichas interativamente através da MSAL.

A MSAL expõe dois métodos primários para obter `acquireTokenSilently()` fichas: `acquireTokenInteractively()`

- `acquireTokenSilently()` tenta assinar num utilizador e obter fichas sem qualquer interação do utilizador, desde que uma conta esteja presente. `acquireTokenSilently()` requer um válido `MSALAccount` que pode ser recuperado utilizando uma das APIs de enumeração da conta MSAL. Esta amostra utiliza `applicationContext.getCurrentAccount(with: msalParameters, completionBlock: {})` para recuperar a conta corrente.

- `acquireTokenInteractively()` mostra sempre UI quando se tenta assinar no utilizador. Pode utilizar cookies de sessão no navegador ou uma conta no autenticador da Microsoft para fornecer uma experiência interativa-SSO.

Adicione o seguinte código à `ViewController` classe:

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

#### <a name="get-a-token-interactively"></a>Obter um símbolo interativamente

O seguinte corte de código recebe um símbolo pela primeira vez criando um `MSALInteractiveTokenParameters` objeto e chamando `acquireToken` . Em seguida, adicionará código que:

1. Cria `MSALInteractiveTokenParameters` com miras.
2. Chamadas `acquireToken()` com os parâmetros criados.
3. Lida com erros. Para obter mais detalhes, consulte o MSAL para o [guia de manuseamento de erros iOS e macOS](msal-error-handling-ios.md).
4. Lida com o caso de sucesso.

Adicione o seguinte código à classe `ViewController`.

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

A `promptType` propriedade de `MSALInteractiveTokenParameters` configurar o comportamento rápido de autenticação e consentimento. São suportados os seguintes valores:

- `.promptIfNecessary` (predefinição) - O utilizador só é solicitado se necessário. A experiência SSO é determinada pela presença de cookies na webview, e pelo tipo de conta. Se vários utilizadores estiverem inscritos, é apresentada a experiência de seleção de conta. *Este é o comportamento padrão.*
- `.selectAccount` - Se nenhum utilizador for especificado, a visão web de autenticação apresenta uma lista de contas atualmente assinadas para o utilizador selecionar.
- `.login` - Requer que o utilizador autentica na webview. Apenas uma conta pode ser assinada de cada vez se especificar este valor.
- `.consent` - Exige que o utilizador consinta no conjunto atual de âmbitos para o pedido.

#### <a name="get-a-token-silently"></a>Obter um símbolo silenciosamente

Para adquirir um token atualizado silenciosamente, adicione o seguinte código à `ViewController` classe. Cria um `MSALSilentTokenParameters` objeto e `acquireTokenSilent()` chama:

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

Uma vez que tenha um token, a sua aplicação pode usá-la no cabeçalho HTTP para fazer um pedido autorizado ao Gráfico da Microsoft:

| chave cabeçalho    | valor                 |
| ------------- | --------------------- |
| Autorização | Portador \<access-token> |

Adicione o seguinte código à `ViewController` classe:

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

Consulte [a Microsoft Graph API](https://graph.microsoft.com) para saber mais sobre a API do Gráfico microsoft.

### <a name="use-msal-for-sign-out"></a>Utilize o MSAL para iniciar s-out

Em seguida, adicione suporte para a assinatura.

> [!Important]
> A assinatura com o MSAL remove todas as informações conhecidas sobre um utilizador da aplicação, bem como a remoção de uma sessão ativa no seu dispositivo quando permitida pela configuração do dispositivo. Também pode assinar opcionalmente o utilizador a partir do navegador.

Para adicionar a capacidade de inscrição, adicione o seguinte código dentro da `ViewController` classe.

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

### <a name="enable-token-caching"></a>Permitir caching token

Por predefinição, o MSAL caches os tokens da sua aplicação no chaveiro iOS ou macOS.

Para permitir o caching token:

1. Certifique-se de que a sua candidatura está devidamente assinada
1. Aceda ao seu separador de Definições de Projeto Xcode > **Capacidades**  >  **Ativar a partilha de chavechains**
1. Selecione **+** e introduza um dos **seguintes grupos keychain**:
    - iOS: `com.microsoft.adalcache`
    - macOS: `com.microsoft.identity.universalstorage`

### <a name="add-helper-methods"></a>Adicionar métodos de ajuda
Adicione os seguintes métodos de ajuda à `ViewController` classe para completar a amostra.

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

### <a name="macos-ui"></a>macOS UI:

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

### <a name="for-ios-only-get-additional-device-information"></a>Apenas para iOS, obtenha informações adicionais do dispositivo

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

### <a name="multi-account-applications"></a>Aplicações multi-conta

Esta aplicação foi construída para um cenário de conta única. O MSAL também suporta cenários de várias contas, mas requer algum trabalho adicional a partir de apps. Terá de criar UI para ajudar os utilizadores a selecionar em que conta querem utilizar para cada ação que requer tokens. Em alternativa, a sua aplicação pode implementar uma heurística para selecionar qual a conta a utilizar consultando todas as contas do MSAL. Por exemplo, ver `accountsFromDeviceForParameters:completionBlock:` [API](https://azuread.github.io/microsoft-authentication-library-for-objc/Classes/MSALPublicClientApplication.html#/c:objc(cs)MSALPublicClientApplication(im)accountsFromDeviceForParameters:completionBlock:)

## <a name="test-your-app"></a>Testar a aplicação

Construa e implemente a aplicação para um dispositivo de teste ou simulador. Você deve ser capaz de iniciar seduca e obter fichas para Azure AD ou contas pessoais da Microsoft.

A primeira vez que um utilizador entra na sua aplicação, será solicitado pela identidade da Microsoft para consentir com as permissões solicitadas. Embora a maioria dos utilizadores sejam capazes de consentir, alguns inquilinos da Azure AD têm o consentimento do utilizador desativado, o que requer que os administradores consintam em nome de todos os utilizadores. Para suportar este cenário, registe os âmbitos da sua aplicação no portal Azure.

Depois de iniciar sposição, a aplicação apresentará os dados devolvidos a partir do ponto final do Microsoft `/me` Graph.

## <a name="next-steps"></a>Próximos passos

Saiba mais sobre a construção de aplicações móveis que chamem APIs web protegidas na nossa série de cenários multi-partes.

> [!div class="nextstepaction"]
> [Cenário: Aplicação móvel que chama APIs web](scenario-mobile-overview.md)
