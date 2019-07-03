---
title: Introdução ao iOS – plataforma de identidade da Microsoft | Azure
description: Como as aplicações de iOS (Swift) podem chamar uma API que requer que os tokens de acesso usando a plataforma de identidade da Microsoft
services: active-directory
documentationcenter: dev-center-name
author: danieldobalian
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/26/2019
ms.author: jmprieur
ms.reviwer: brandwe
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: b7d68f6f7079872b81b750ba71997117aaa27d33
ms.sourcegitcommit: 978e1b8cac3da254f9d6309e0195c45b38c24eb5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/03/2019
ms.locfileid: "67550563"
---
# <a name="sign-in-users-and-call-the-microsoft-graph-from-an-ios-app"></a>Iniciar sessão dos utilizadores e chamar o Microsoft Graph a partir de uma aplicação iOS

Neste tutorial, irá aprender como criar uma aplicação iOS e integrá-lo na plataforma de identidade da Microsoft. Especificamente, esta aplicação irá iniciar sessão de um utilizador, obter um token de acesso para chamar a API do Microsoft Graph e fazer um pedido de básico para o Microsoft Graph API.  

Quando concluir o guia, a aplicação irá aceitar inícios de sessão de contas pessoais da Microsoft (incluindo o outlook.com, live.com e outros) e profissional ou escolar contas a partir de qualquer empresa ou organização que utiliza o Azure Active Directory.

## <a name="how-this-guide-works"></a>Como funciona este guia

![Mostra como funciona a aplicação de exemplo gerada por este tutorial](../../../includes/media/active-directory-develop-guidedsetup-ios-introduction/iosintro.svg)

A aplicação neste exemplo irá iniciar sessão dos utilizadores e obter dados em seu nome.  Estes dados serão acedidos através de uma API (Microsoft Graph API neste caso) protegida que requer autorização e é também protegida pela plataforma de identidade da Microsoft.

Mais especificamente:

* A aplicação irá iniciar sessão no usuário, seja por meio de um navegador ou o Microsoft Authenticator.
* O utilizador final irá aceitar as permissões de que seu aplicativo solicitou. 
* A aplicação será emitida um token de acesso para a Microsoft Graph API.
* O token de acesso será incluído na solicitação HTTP para a API web.
* Processe a resposta do Microsoft Graph.

Este exemplo utiliza a biblioteca Microsoft Authentication (MSAL) para implementar a autenticação MSAL automaticamente irá renovar os tokens, fornecer SSO entre outras aplicações no dispositivo e gerir a conta (s).

## <a name="prerequisites"></a>Pré-requisitos

- XCode versão 10.x é necessária para o exemplo que é criado neste guia. Pode baixar o XCode do [Web site do iTunes](https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12 "URL de transferência do XCode").
- Biblioteca de autenticação da Microsoft ([MSAL.framework](https://github.com/AzureAD/microsoft-authentication-library-for-objc)). Pode utilizar o Gestor de dependências ou adicionar manualmente. Há a seção a seguir com [mais informações](#add-msal). 

## <a name="set-up-your-project"></a>Configurar seu projeto

Neste tutorial, irá criar um novo projeto. Se deseja baixar o tutorial completo em vez disso, [baixar o código](https://github.com/Azure-Samples/active-directory-ios-swift-native-v2/archive/master.zip).

### <a name="create-a-new-project"></a>Criar um novo projeto

1. Abra o Xcode e selecione **criar um novo projeto do Xcode**.
2. Selecione **iOS > aplicação de vista única** e selecione **próxima**.
3. Dê um nome de produto e selecione **seguinte**.
4. Selecione uma pasta para criar a sua aplicação e clique em *criar*.

## <a name="register-your-application"></a>Registar a sua aplicação 

Pode registar a sua aplicação em qualquer uma das duas formas, conforme descrito nas próximas duas secções.

### <a name="register-your-app"></a>Registar a sua aplicação

1. Vá para o [portal do Azure](https://aka.ms/MobileAppReg) > selecione `New registration`. 
2. Introduza um **Name** para a sua aplicação > `Register`. **Não defina um URI de redirecionamento nesta fase**. 
3. Na `Manage` secção, aceda a `Authentication` > `Add a platform` > `iOS`
    - Introduza o ID de pacote. do seu projeto Se tiver transferido o código, este é `com.microsoft.identitysample.MSALiOS`.
4. Pressionar `Configure` e armazenar o `MSAL Configuration` para utilizar mais tarde. 

## <a name="add-msal"></a>Adicionar a MSAL

### <a name="get-msal"></a>Obter a MSAL

#### <a name="cocoapods"></a>CocoaPods

Pode usar [CocoaPods](https://cocoapods.org/) para instalar `MSAL` adicionando-o para seu `Podfile` em destino:

```
use_frameworks!

target '<your-target-here>' do
   pod 'MSAL', '~> 0.4.0'
end
```

#### <a name="carthage"></a>Carthage

Pode usar [Carthage](https://github.com/Carthage/Carthage) para instalar `MSAL` adicionando-o para seu `Cartfile`: 

```
github "AzureAD/microsoft-authentication-library-for-objc" "master"
```

#### <a name="manually"></a>Manualmente

Também pode utilizar o Git Submodule ou verifique a versão mais recente e usar como estrutura em seu aplicativo.

### <a name="add-your-app-registration"></a>Adicionar o registo de aplicação

Em seguida, adicione o seu registo de aplicações para o seu código. Adicionar seu ***Client / ID de aplicação*** para `ViewController.swift`:

```swift
let kClientID = "Your_Application_Id_Here"

// Additional variables for Auth and Graph API
let kGraphURI = "https://graph.microsoft.com/v1.0/me/"
let kScopes: [String] = ["https://graph.microsoft.com/user.read"]
let kAuthority = "https://login.microsoftonline.com/common"
var accessToken = String()
var applicationContext : MSALPublicClientApplication?
```

### <a name="configure-url-schemes"></a>Configurar os esquemas de URL

Registar `CFBundleURLSchemes` para permitir que um retorno de chamada para que o utilizador pode ser redirecionado para a aplicação após o início de sessão.

`LSApplicationQueriesSchemes` permite utilizar a sua aplicação para utilizar o Microsoft Authenticator, se disponível. 

Para tal, abra `Info.plist` como uma origem de código e adicione o seguinte, substituindo o ***BUNDLE_ID*** com o que configurou no portal do Azure.

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
    <string>msauth</string>
    <string>msauthv2</string>
</array>
```

### <a name="import-msal"></a>Importar MSAL

Estrutura MSAL de importação no `ViewController.swift` e `AppDelegate.swift` ficheiros:

```swift
import MSAL
```

## <a name="create-your-apps-ui"></a>Criar a IU da sua aplicação

Para este tutorial, terá de criar:

- Botão de API de gráfico de chamada
- Termine de botão
- Registo textview

Na `ViewController.swift`, definir propriedades e `initUI()` da seguinte forma:

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

Em seguida, adicione a `viewDidLoad()` de viewcontroller. SWIFT:

```swift
    override func viewDidLoad() {
        super.viewDidLoad()
        initUI()
        do {
            try self.initMSAL()
        } catch let error {
            self.loggingText.text = "Unable to create Application Context \(error)"
        }
    }
```

## <a name="use-msal"></a>Utilizar a MSAL

### <a name="initialize-msal"></a>Inicializar a MSAL

Em primeiro lugar, tem de criar uma `MSALPublicClientApplication` com uma instância de `MSALPublicClientConfiguration` para a sua aplicação:

```swift
    func initMSAL() throws {
        
        guard let authorityURL = URL(string: kAuthority) else {
            self.loggingText.text = "Unable to create authority URL"
            return
        }
        
        let authority = try MSALAADAuthority(url: authorityURL)
        
        let msalConfiguration = MSALPublicClientApplicationConfig(clientId: kClientID, redirectUri: nil, authority: authority)
        self.applicationContext = try MSALPublicClientApplication(configuration: msalConfiguration)
    }
```

### <a name="handle-the-callback"></a>Lidar com o retorno de chamada

Para lidar com o retorno de chamada após o início de sessão, adicione `MSALPublicClientApplication.handleMSALResponse` em `appDelegate`:

```swift
    func application(_ app: UIApplication, open url: URL, options: [UIApplication.OpenURLOptionsKey : Any] = [:]) -> Bool {
        
        guard let sourceApplication = options[UIApplication.OpenURLOptionsKey.sourceApplication] as? String else {
            return false
        }
        
        return MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: sourceApplication)
    }
```

#### <a name="acquire-tokens"></a>Adquira Tokens

Agora, podemos implementar lógica de processamento e obter tokens de forma interativa através de MSAL da interface do Usuário a aplicação. 

A MSAL expõe dois métodos principais para obter os tokens: `acquireTokenSilently` e `acquireTokenInteractively`.  

`acquireTokenSilently()` tenta iniciar sessão de um utilizador e obter tokens sem qualquer interação do utilizador, se uma conta estiver presente.

`acquireTokenInteractively` mostrará sempre da interface do Usuário durante a tentativa de iniciar a sessão do utilizador e obter tokens; No entanto, ele poderá usar cookies de sessão no browser ou uma conta no Microsoft authenticator para lhe proporcionar uma experiência interativa SSO. 

```swift
    @objc func callGraphAPI(_ sender: UIButton) {
        
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

#### <a name="get-a-token-interactively"></a>Obter um token de forma interativa

Para adquirir um token pela primeira vez, terá de criar uma `MSALInteractiveTokenParameters` e chamar `acquireToken`.

1. Criar `MSALInteractiveTokenParameters` com âmbitos.
2. Chamar `acquireToken` com os parâmetros criados.
3. Processe o erro em conformidade. Para obter mais detalhes, consulte a [guia de tratamento de erros do iOS](https://github.com/AzureAD/microsoft-authentication-library-for-objc/wiki/Error-Handling).
4. Administrar o caso de êxito. 

```swift
    func acquireTokenInteractively() {
   
        guard let applicationContext = self.applicationContext else { return }
     // #1    
        let parameters = MSALInteractiveTokenParameters(scopes: kScopes)
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

Para adquirir um token atualizado silenciosamente, terá de criar uma `MSALSilentTokenParameters` e chamar `acquireTokenSilent`:

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

### <a name="call-the-microsoft-graph-api"></a>Chamar o Microsoft Graph API 

Depois de ter um token através do `self.accessToken`, a aplicação pode utilizar este valor no cabeçalho de HTTP para fazer uma solicitação de autorizados para o Microsoft Graph:

| Chave do cabeçalho    | value                 |
| ------------- | --------------------- |
| Autorização | Bearer \<access-token> |

Adicione o seguinte ao `ViewController.swift`:

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

Saiba mais sobre o [Microsoft Graph API](https://graph.microsoft.com)

### <a name="use-msal-for-sign-out"></a>Utilizar a MSAL para fim de sessão

Em seguida, vamos adicionar suporte para fim de sessão à nossa aplicação. 

É importante observar, a fim de sessão com MSAL remove todas as informações conhecidas sobre um utilizador desta aplicação, mas o utilizador continuará a ter uma sessão ativa no respetivo dispositivo. Se o utilizador tentar iniciar sessão novamente eles podem ver a interação, mas não poderão ter de reintroduzir as respetivas credenciais devido à sessão de dispositivo que está a ser Active Directory. 

Para adicionar a fim de sessão, copie o seguinte método em seu `ViewController.swift`:

```swift 
    @objc func signOut(_ sender: UIButton) {
        
        guard let applicationContext = self.applicationContext else { return }
        
        guard let account = self.currentAccount() else { return }
        
        do {
            
            /**
             Removes all tokens from the cache for this application for the provided account
             
             - account:    The account to remove from the cache
             */
            
            try applicationContext.remove(account)
            self.loggingText.text = ""
            self.signOutButton.isEnabled = false
            
        } catch let error as NSError {
            
            self.updateLogging(text: "Received error signing account out: \(error)")
        }
    }
```

### <a name="enable-token-caching"></a>Ativar colocação em cache de token

Por predefinição, a MSAL armazena em cache tokens da sua aplicação na keychain do iOS. 

Para ativar tokens em cache, aceda a definições do projeto Xcode > `Capabilities tab`  >  `Enable Keychain Sharing` > clique em `Plus` > Enter **adalcache**.

### <a name="add-helper-methods"></a>Adicionar métodos auxiliares

Adicione esses métodos auxiliares para concluir o exemplo:

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

### <a name="multi-account-applications"></a>Aplicações de várias contas

Esta aplicação baseia-se para um cenário de única conta. Cenários de várias contas também oferece suporte a MSAL, mas requer algum trabalho adicional de aplicações. Terá de criar a interface do Usuário para ajudar o usuário selecione a conta que pretendem utilizar para cada ação que requer tokens. Em alternativa, a sua aplicação pode implementar uma heurística para selecionar a conta a utilizar por meio do `getAllAccounts(...)` método.

## <a name="test-your-app"></a>Testar a aplicação

### <a name="run-locally"></a>Executar localmente

Se tiver acompanhado o código acima, tente criar e implementar a aplicação para um dispositivo de teste ou emulador. Deve ser capaz de iniciar sessão e obtenha tokens para o Azure AD ou pessoais contas da Microsoft! Depois de um utilizador iniciar sessão, esta aplicação irá apresentar os dados retornados de Microsoft Graph `/me` ponto final. 

Se tiver quaisquer problemas, pode abrir um problema neste documento ou na biblioteca de MSAL e informe-nos. 

### <a name="consent-to-your-app"></a>Consentimento para a sua aplicação

Na primeira vez que qualquer utilizador inicia sessão na sua aplicação, serão solicitados pela identidade da Microsoft para dar consentimento às permissões solicitadas.  Embora a maioria dos usuários são capazes de consentir, alguns inquilinos do Azure AD tem desativado o consentimento do utilizador - exigindo que os administradores consentir em nome de todos os utilizadores.  Para suportar este cenário, certifique-se de que registe os âmbitos da sua aplicação no portal do Azure.

## <a name="help-and-support"></a>Ajuda e suporte

Teve problemas com este tutorial, ou com a plataforma de identidade da Microsoft? Consulte [ajuda e suporte](https://docs.microsoft.com/azure/active-directory/develop/developer-support-help-options)

