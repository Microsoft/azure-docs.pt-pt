---
title: Introdução ao iOS – plataforma de identidade da Microsoft | Azure
description: Como as aplicações de iOS (Swift) podem chamar uma API que requer que os tokens de acesso usando a plataforma de identidade da Microsoft
services: active-directory
documentationcenter: dev-center-name
author: danieldobalian
manager: CelesteDG
ms.service: active-directory
ms.topic: tutorial
ms.workload: identity
ms.date: 07/15/2019
ms.author: jmprieur
ms.reviewer: brandwe
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 910069ab89cef18794e637b6bfbbc57fb732871c
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/15/2019
ms.locfileid: "67872093"
---
# <a name="sign-in-users-and-call-the-microsoft-graph-from-an-ios-app"></a>Iniciar sessão dos utilizadores e chamar o Microsoft Graph a partir de uma aplicação iOS

Neste tutorial, irá aprender como integrar uma aplicação iOS com a plataforma de identidade da Microsoft. A aplicação irá iniciar sessão de um utilizador, obter um token de acesso para chamar a API do Microsoft Graph e fazer um pedido para o Microsoft Graph API.  

Quando concluir o guia, a aplicação irá aceitar inícios de sessão de contas pessoais da Microsoft (incluindo o outlook.com, live.com e outros) e profissional ou escolar contas a partir de qualquer empresa ou organização que utiliza o Azure Active Directory.

## <a name="how-this-tutorial-works"></a>Como funciona este tutorial

![Mostra como funciona a aplicação de exemplo gerada por este tutorial](../../../includes/media/active-directory-develop-guidedsetup-ios-introduction/iosintro.svg)

A aplicação neste tutorial irá iniciar sessão dos utilizadores e obter dados em seu nome.  Estes dados serão acedidos através de uma API protegida (Microsoft Graph API neste caso) que requer autorização e protegida por plataforma de identidades da Microsoft.

Mais especificamente:

* A aplicação irá iniciar sessão no usuário, seja por meio de um navegador ou o Microsoft Authenticator.
* O utilizador final irá aceitar as permissões de que seu aplicativo solicitou.
* A aplicação será emitida um token de acesso para a Microsoft Graph API.
* O token de acesso será incluído na solicitação HTTP para a API web.
* Processe a resposta do Microsoft Graph.

Este exemplo utiliza a biblioteca Microsoft Authentication (MSAL) para implementar a autenticação. MSAL automaticamente irá renovar os tokens, fornecer início de sessão único (SSO) entre as outras aplicações no dispositivo e gerir a conta (s).

## <a name="prerequisites"></a>Pré-requisitos

- XCode versão 10.x é necessário para criar a aplicação neste guia. Pode baixar o XCode do [Web site do iTunes](https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12 "URL de transferência do XCode").
- Biblioteca de autenticação da Microsoft ([MSAL.framework](https://github.com/AzureAD/microsoft-authentication-library-for-objc)). Pode utilizar um Gestor de dependência ou adicionar a biblioteca manualmente. As instruções abaixo mostram-lhe como.

Neste tutorial, irá criar um novo projeto. Se deseja baixar o tutorial completo em vez disso, [baixar o código](https://github.com/Azure-Samples/active-directory-ios-swift-native-v2/archive/master.zip).

## <a name="create-a-new-project"></a>Criar um novo projeto

1. Abra o Xcode e selecione **criar um novo projeto do Xcode**.
2. Selecione **iOS** > **aplicação de vista única** e selecione **seguinte**.
3. Forneça um nome de produto.
4. Definir o **linguagem** ao **Swift** e selecione **seguinte**.
5. Selecione uma pasta para criar a sua aplicação e clique em **criar**.

## <a name="register-your-application"></a>Registar a sua aplicação

1. Aceda ao [Portal do Azure](https://aka.ms/MobileAppReg)
2. Abra o [painel de registos de aplicação](https://ms.portal.azure.com/?feature.broker=true#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredAppsPreview) e clique em **+ novo registo**.
3. Introduza um **Name** para a sua aplicação e, em seguida, sem definir um URI de redirecionamento, clique em **registar**.
4. Na **Manage** secção do painel que aparece, selecione **autenticação**.
5. Clique em **experimentar a nova experiência** junto à parte superior do ecrã para abrir a nova experiência de registo de aplicação e, em seguida, clique em **+ novo registo** >  **+ adicionar uma plataforma**  >  **iOS**.
    - Introduza o ID de pacote. do seu projeto Se tiver transferido o código, este é `com.microsoft.identitysample.MSALiOS`. Se estiver a criar seu próprio projeto, selecione o seu projeto no Xcode e abra o **gerais** separador. O identificador de pacote é apresentado na **identidade** secção.
6. Clique em `Configure` e guarde o **configuração de MSAL** que aparece na **configuração do iOS** página para que possam inseri-lo quando configurar a sua aplicação mais tarde.  Clique em **Concluído**.

## <a name="add-msal"></a>Adicionar a MSAL

Escolha uma das formas seguintes para instalar a biblioteca MSAL na sua aplicação:

### <a name="cocoapods"></a>CocoaPods

1. Se estiver a utilizar [CocoaPods](https://cocoapods.org/), instale `MSAL` pelo primeiro criar um ficheiro vazio de chamado `podfile` na mesma pasta que o seu projeto `.xcodeproj` ficheiro. Adicione o seguinte ao `podfile`:

   ```
   use_frameworks!
   
   target '<your-target-here>' do
      pod 'MSAL', '~> 0.4.0'
   end
   ```

2. Substitua `<your-target-here>` com o nome do seu projeto.
3. Na janela do terminal, navegue para a pasta que contém o `podfile` que criou e execute `pod install` para instalar a biblioteca MSAL.
4. Feche o Xcode e abra `<your project name>.xcworkspace` para recarregar o projeto no Xcode.

### <a name="carthage"></a>Carthage

Se estiver a utilizar [Carthage](https://github.com/Carthage/Carthage), instale `MSAL` adicionando-o para seu `Cartfile`:

```
github "AzureAD/microsoft-authentication-library-for-objc" "master"
```

### <a name="manually"></a>Manualmente

Também pode utilizar o Git Submodule, ou verifique a versão mais recente para utilizar como uma estrutura em seu aplicativo.

## <a name="add-your-app-registration"></a>Adicionar o registo de aplicação

Em seguida, vamos adicionar o registo de aplicação para o seu código. 

Em primeiro lugar, adicione a seguinte instrução de importação na parte superior dos `ViewController.swift` e `AppDelegate.swift` ficheiros:

```swift
import MSAL
```

Em seguida, adicione o seguinte código ao `ViewController.swift` anteriores ao `viewDidLoad()`:

```swift
let kClientID = "Your_Application_Id_Here"

// Additional variables for Auth and Graph API
let kGraphURI = "https://graph.microsoft.com/v1.0/me/"
let kScopes: [String] = ["https://graph.microsoft.com/user.read"]
let kAuthority = "https://login.microsoftonline.com/common"
var accessToken = String()
var applicationContext : MSALPublicClientApplication?
```

Modificar o valor atribuído a `kClientID`para ser o seu ID da aplicação. Este valor é a parte dos dados de configuração de MSAL que guardou durante o passo no início deste tutorial para registar a aplicação no portal do Azure.

## <a name="configure-url-schemes"></a>Configurar os esquemas de URL

Neste passo, vai registar `CFBundleURLSchemes` para que o utilizador pode ser redirecionado ao aplicativo após o início de sessão. A propósito, `LSApplicationQueriesSchemes` também permite que a aplicação para fazer uso do Microsoft Authenticator.

No Xcode, abra `Info.plist` como um código de origem do ficheiro e adicione o seguinte dentro do `<dict>` secção. Substitua `[BUNDLE_ID]` com o valor que utilizou no portal do Azure que, se tiver transferido o código, é `com.microsoft.identitysample.MSALiOS`. Se estiver a criar seu próprio projeto, selecione o seu projeto no Xcode e abra o **gerais** separador. O identificador de pacote é apresentado na **identidade** secção.

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

## <a name="create-your-apps-ui"></a>Criar a IU da sua aplicação

Agora, crie uma interface do Usuário, que inclui um botão para chamar o Microsoft Graph API, outro para terminar sessão, e um texto visualizados para ver algumas saídas adicionando o seguinte código para o `ViewController`classe:

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

Em seguida, também dentro de `ViewController` classe, substitua o `viewDidLoad()` método com:

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

Adicione as seguintes `InitMSAL` método para o `ViewController` classe:

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

### <a name="handle-the-sign-in-callback"></a>Lidar com o retorno de chamada início de sessão

Abra o ficheiro `AppDelegate.swift`. Para lidar com o retorno de chamada após o início de sessão, adicione `MSALPublicClientApplication.handleMSALResponse` para o `appDelegate` classe assim:

```swift
    func application(_ app: UIApplication, open url: URL, options: [UIApplication.OpenURLOptionsKey : Any] = [:]) -> Bool {
        
        guard let sourceApplication = options[UIApplication.OpenURLOptionsKey.sourceApplication] as? String else {
            return false
        }
        
        return MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: sourceApplication)
    }
```

#### <a name="acquire-tokens"></a>Adquira Tokens

Agora, podemos implementar a lógica de processamento de interface do Usuário do aplicativo e obter os tokens de forma interativa através de MSAL.

A MSAL expõe dois métodos principais para obter os tokens: `acquireTokenSilently()` e `acquireTokenInteractively()`: 

- `acquireTokenSilently()` tenta iniciar sessão de um utilizador e obter os tokens sem qualquer interação do utilizador, desde que uma conta está presente.

- `acquireTokenInteractively()` Mostra sempre a interface do Usuário durante a tentativa de iniciar a sessão do utilizador. Ele pode usar cookies de sessão no browser ou uma conta no Microsoft authenticator para proporcionar uma experiência interativa SSO.

Adicione o seguinte código para o `ViewController` classe:

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

O código a seguir obtém um token pela primeira vez ao criar uma `MSALInteractiveTokenParameters` objeto e chamar `acquireToken`. Em seguida, irá adicionar o código do que:

1. Cria `MSALInteractiveTokenParameters` com âmbitos.
2. Chamadas `acquireToken()` com os parâmetros de criado.
3. Manipula erros. Para obter mais detalhes, consulte a [guia de tratamento de erros do iOS](https://github.com/AzureAD/microsoft-authentication-library-for-objc/wiki/Error-Handling).
4. Processa o caso com êxito.

Adicione o seguinte código para o `ViewController` classe.

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

Para adquirir um token atualizado silenciosamente, adicione o seguinte código para o `ViewController` classe. Ele cria um `MSALSilentTokenParameters` objeto e chamadas `acquireTokenSilent()`:

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

Depois de ter um token, a aplicação pode utilizá-lo no cabeçalho de HTTP para fazer uma solicitação de autorizados para o Microsoft Graph:

| Chave do cabeçalho    | value                 |
| ------------- | --------------------- |
| Autorização | Bearer \<access-token> |

Adicione o seguinte código para o `ViewController` classe:

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

Ver [Microsoft Graph API](https://graph.microsoft.com) para saber mais sobre o Microsoft Graph API.

### <a name="use-msal-for-sign-out"></a>Utilizar a MSAL para fim de sessão

Em seguida, adicionar suporte para fim de sessão.

> [!Important]
> Terminar a sessão com MSAL remove todas as informações conhecidas sobre um usuário do aplicativo, mas o utilizador continuará a ter uma sessão ativa no respetivo dispositivo. Se o utilizador tentar iniciar sessão novamente eles poderão ver o início de sessão da interface do Usuário, mas não poderão ter de reintroduzir as respetivas credenciais porque a sessão do dispositivo ainda está ativa.

Para adicionar capacidade de fim de sessão, adicione o seguinte código dentro do `ViewController` classe. Este método percorre todas as contas e remove-os:

```swift 
    @objc func signOut(_ sender: UIButton) {
        
        guard let applicationContext = self.applicationContext else { return }
        
        guard let account = self.currentAccount() else { return }
        
        do {
            
            /**
             Removes all tokens from the cache for this application for the provided account
             
             - account:    The account to remove from the cache */
            
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

Para ativar a colocação em cache de token:
1. Aceda a definições do projeto Xcode > **guia de funcionalidades** > **ativar a partilha de Keychain**
2. Clique em **+** e introduza `com.microsoft.adalcache` como uma **grupos de Keychain** entrada.

### <a name="add-helper-methods"></a>Adicionar métodos auxiliares

Adicione os seguintes métodos de programa auxiliar para o `ViewController` classe para concluir o exemplo:

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

Esta aplicação baseia-se para um cenário de única conta. A MSAL também oferece suporte a cenários de várias contas, mas requer algum trabalho adicional de aplicações. Terá de criar a interface do Usuário para ajudar o usuário selecione a conta que pretendem utilizar para cada ação que requer tokens. Em alternativa, a sua aplicação pode implementar uma heurística para selecionar a conta a utilizar por meio do `getAccounts()` método.

## <a name="test-your-app"></a>Testar a aplicação

### <a name="run-locally"></a>Executar localmente

Crie e implemente a aplicação para um dispositivo de teste ou emulador. Deve ser capaz de iniciar sessão e obtenha tokens para o Azure AD ou pessoais contas Microsoft.

Na primeira vez que um utilizador inicia sessão na sua aplicação, serão solicitados pela identidade da Microsoft para dar consentimento às permissões solicitadas.  Embora a maioria dos usuários são capazes de consentir, alguns inquilinos do Azure AD tem desativado o consentimento do utilizador, que requer que os administradores de consentir em nome de todos os utilizadores. Para suportar este cenário, registe os âmbitos da sua aplicação no portal do Azure.

Depois de iniciar sessão, a aplicação irá apresentar os dados retornados de Microsoft Graph `/me` ponto final.

## <a name="get-help"></a>Obter ajuda

Visite [ajuda e suporte](https://docs.microsoft.com/azure/active-directory/develop/developer-support-help-options) se tiver problemas com este tutorial, ou com a plataforma de identidade da Microsoft.
