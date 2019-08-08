---
title: Introdução ao iOS-plataforma de identidade da Microsoft | Azure
description: Como os aplicativos iOS (Swift) podem chamar uma API que exige tokens de acesso usando a plataforma de identidade da Microsoft
services: active-directory
documentationcenter: dev-center-name
author: tylermsft
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.workload: identity
ms.date: 07/15/2019
ms.author: jmprieur
ms.reviewer: brandwe
ms.custom: aaddev, identityplatformtop40
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2968ae229c52cdd2b36111710f0aa6c0dc4fce4c
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/08/2019
ms.locfileid: "68852419"
---
# <a name="sign-in-users-and-call-the-microsoft-graph-from-an-ios-app"></a>Conectar usuários e chamar o Microsoft Graph de um aplicativo iOS

Neste tutorial, você aprenderá a integrar um aplicativo iOS com a plataforma de identidade da Microsoft. O aplicativo conectará um usuário, obterá um token de acesso para chamar a API de Microsoft Graph e fará uma solicitação para a API de Microsoft Graph.  

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

## <a name="prerequisites"></a>Pré-requisitos

- O XCode versão 10. x é necessário para compilar o aplicativo neste guia. Você pode baixar o XCode na(https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12 "URL de download do Xcode")do site do [iTunes].
- Biblioteca de autenticação da Microsoft ([MSAL. Framework](https://github.com/AzureAD/microsoft-authentication-library-for-objc)). Você pode usar um Gerenciador de dependência ou adicionar a biblioteca manualmente. As instruções a seguir mostram como.

Este tutorial criará um novo projeto. Se você quiser baixar o tutorial concluído, [Baixe o código](https://github.com/Azure-Samples/active-directory-ios-swift-native-v2/archive/master.zip).

## <a name="create-a-new-project"></a>Criar um novo projeto

1. Abra o Xcode e selecione **criar um novo projeto do Xcode**.
2. Selecione**aplicativo de exibição única** do **Ios** > e selecione **Avançar**.
3. Forneça um nome de produto.
4. Defina o **idioma** como **Swift** e selecione **Avançar**.
5. Selecione uma pasta para criar seu aplicativo e clique em **criar**.

## <a name="register-your-application"></a>Registar a sua aplicação

1. Aceda ao [Portal do Azure](https://aka.ms/MobileAppReg)
2. Abra a [folha registros de aplicativo](https://ms.portal.azure.com/?feature.broker=true#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredAppsPreview) e clique em **+ novo registro**.
3. Insira um **nome** para seu aplicativo e, em seguida, sem definir um URI de redirecionamento, clique em **registrar**.
4. Na seção **gerenciar** do painel que aparece, selecione **autenticação**.
5. Clique em **experimentar a nova experiência** perto da parte superior da tela para abrir a nova experiência de registro de aplicativo e, em seguida, clique em **+ novo registro** >  **+ Adicionar uma plataforma** > **Ios**.
    - Insira a ID do pacote do seu projeto. Se você baixou o código, isso `com.microsoft.identitysample.MSALiOS`é. Se você estiver criando seu próprio projeto, selecione seu projeto no Xcode e abra a guia **geral** . O identificador do pacote aparece na seção **identidade** .
6. Clique `Configure` e salve a **configuração do MSAL** que aparece na página de **configuração do IOS** para que você possa inseri-la ao configurar o aplicativo mais tarde.  Clique em **Concluído**.

## <a name="add-msal"></a>Adicionar MSAL

Escolha uma das seguintes maneiras de instalar a biblioteca MSAL em seu aplicativo:

### <a name="cocoapods"></a>CocoaPods

1. Se você estiver usando o [CocoaPods](https://cocoapods.org/), `MSAL` instale primeiro criando um arquivo vazio chamado `podfile` na mesma pasta que o arquivo do `.xcodeproj` seu projeto. Adicione o seguinte a `podfile`:

   ```
   use_frameworks!
   
   target '<your-target-here>' do
      pod 'MSAL', '~> 0.4.0'
   end
   ```

2. Substitua `<your-target-here>` pelo nome do seu projeto.
3. Em uma janela de terminal, navegue até a pasta que contém `podfile` o que você criou `pod install` e execute para instalar a biblioteca MSAL.
4. Feche o Xcode e `<your project name>.xcworkspace` abra para recarregar o projeto no Xcode.

### <a name="carthage"></a>Carthage

Se você estiver usando o [Carthage](https://github.com/Carthage/Carthage), `MSAL` instale-o adicionando- `Cartfile`o ao seu:

```
github "AzureAD/microsoft-authentication-library-for-objc" "master"
```

### <a name="manually"></a>Manualmente

Você também pode usar o submódulo git ou conferir a versão mais recente para usar como uma estrutura em seu aplicativo.

## <a name="add-your-app-registration"></a>Adicionar o registro do aplicativo

Em seguida, adicionaremos o registro do aplicativo ao seu código. 

Primeiro, adicione a seguinte instrução de importação na parte superior dos `ViewController.swift` arquivos `AppDelegate.swift` e:

```swift
import MSAL
```

Em seguida, adicione o seguinte `ViewController.swift` código ao `viewDidLoad()`antes de:

```swift
let kClientID = "Your_Application_Id_Here"

// Additional variables for Auth and Graph API
let kGraphURI = "https://graph.microsoft.com/v1.0/me/"
let kScopes: [String] = ["https://graph.microsoft.com/user.read"]
let kAuthority = "https://login.microsoftonline.com/common"
var accessToken = String()
var applicationContext : MSALPublicClientApplication?
```

Modifique o valor atribuído `kClientID`a para ser a ID do aplicativo. Esse valor faz parte dos dados de configuração do MSAL que você salvou durante a etapa no início deste tutorial para registrar o aplicativo no portal do Azure.

## <a name="configure-url-schemes"></a>Configurar esquemas de URL

Nesta etapa, você será registrado `CFBundleURLSchemes` para que o usuário possa ser Redirecionado de volta para o aplicativo após a entrada. A propósito, `LSApplicationQueriesSchemes` também permite que seu aplicativo faça uso de Microsoft Authenticator.

No Xcode, abra `Info.plist` como um arquivo de código-fonte e adicione o seguinte dentro `<dict>` da seção. Substitua `[BUNDLE_ID]` pelo valor usado na portal do Azure que, se você baixou o código, é `com.microsoft.identitysample.MSALiOS`. Se você estiver criando seu próprio projeto, selecione seu projeto no Xcode e abra a guia **geral** . O identificador do pacote aparece na seção **identidade** .

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

## <a name="create-your-apps-ui"></a>Criar a interface do usuário do seu aplicativo

Agora, crie uma interface do usuário que inclua um botão para chamar a API de Microsoft Graph, outra para sair e uma exibição de texto para ver alguma saída adicionando o seguinte código `ViewController`à classe:

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

Em seguida, também dentro `ViewController` da classe, substitua `viewDidLoad()` o método por:

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

## <a name="use-msal"></a>Usar MSAL

### <a name="initialize-msal"></a>Inicializar MSAL

Adicione o seguinte `InitMSAL` método `ViewController` à classe:

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

### <a name="handle-the-sign-in-callback"></a>Manipular o retorno de chamada de entrada

Abra o ficheiro `AppDelegate.swift`. Para lidar com o retorno de chamada após a entrada `MSALPublicClientApplication.handleMSALResponse` , adicione `appDelegate` à classe desta forma:

```swift
    func application(_ app: UIApplication, open url: URL, options: [UIApplication.OpenURLOptionsKey : Any] = [:]) -> Bool {
        
        guard let sourceApplication = options[UIApplication.OpenURLOptionsKey.sourceApplication] as? String else {
            return false
        }
        
        return MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: sourceApplication)
    }
```

#### <a name="acquire-tokens"></a>Adquirir tokens

Agora, podemos implementar a lógica de processamento da interface do usuário do aplicativo e obter os tokens interativamente por meio de MSAL.

MSAL expõe dois métodos principais para obter tokens `acquireTokenSilently()` : `acquireTokenInteractively()`e: 

- `acquireTokenSilently()`tenta conectar um usuário e obter tokens sem qualquer interação do usuário, desde que uma conta esteja presente.

- `acquireTokenInteractively()`sempre mostra a IU ao tentar entrar no usuário. Ele pode usar cookies de sessão no navegador ou uma conta no Microsoft Authenticator para fornecer uma experiência de SSO interativo.

Adicione o seguinte código à `ViewController` classe:

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

#### <a name="get-a-token-interactively"></a>Obter um token interativamente

O código a seguir obtém um token pela primeira vez, criando um `MSALInteractiveTokenParameters` objeto e chamando `acquireToken`. Em seguida, você adicionará o código que:

1. Cria `MSALInteractiveTokenParameters` com escopos.
2. Chama `acquireToken()` com os parâmetros criados.
3. Manipula erros. Para obter mais detalhes, consulte o [Guia de tratamento de erros do IOS](https://github.com/AzureAD/microsoft-authentication-library-for-objc/wiki/Error-Handling).
4. Manipula o caso bem-sucedido.

Adicione o código a seguir à `ViewController` classe.

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

Para adquirir um token atualizado silenciosamente, adicione o código a seguir à `ViewController` classe. Ele cria um `MSALSilentTokenParameters` objeto e chama `acquireTokenSilent()`:

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

| chave de cabeçalho    | value                 |
| ------------- | --------------------- |
| Autorização | \<Acesso de portador-> de token |

Adicione o seguinte código à `ViewController` classe:

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

Para adicionar a capacidade de saída, adicione o código a seguir dentro `ViewController` da classe. Esse método percorre todas as contas e as remove:

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

### <a name="enable-token-caching"></a>Habilitar cache de token

Por padrão, o MSAL armazena em cache os tokens do aplicativo no conjunto de chaves do iOS. 

Para habilitar o cache de token:
1. Vá para as configurações do projeto do Xcode > **guia** > recursos**Habilitar compartilhamento** de conjunto de chaves
2. Clique **+** e insira `com.microsoft.adalcache` como uma entrada de grupos de conjunto de **chaves** .

### <a name="add-helper-methods"></a>Adicionar métodos auxiliares

Adicione os seguintes métodos auxiliares à `ViewController` classe para concluir o exemplo:

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

### <a name="multi-account-applications"></a>Aplicativos de várias contas

Esse aplicativo é criado para um cenário de conta única. O MSAL também dá suporte a cenários de várias contas, mas requer algum trabalho adicional dos aplicativos. Você precisará criar a IU para ajudar o usuário a selecionar qual conta deseja usar para cada ação que requer tokens. Como alternativa, seu aplicativo pode implementar uma heurística para selecionar qual conta usar por meio do `getAccounts()` método.

## <a name="test-your-app"></a>Testar a aplicação

### <a name="run-locally"></a>Executar localmente

Crie e implante o aplicativo em um dispositivo de teste ou emulador. Você deve ser capaz de entrar e obter tokens para contas pessoais ou da Microsoft do Azure AD.

Na primeira vez que um usuário entrar em seu aplicativo, ele será solicitado pela identidade da Microsoft para consentir as permissões solicitadas.  Embora a maioria dos usuários seja capaz de consentir, alguns locatários do Azure AD desabilitaram o consentimento do usuário, o que exige que os administradores consentissem em nome de todos os usuários. Para dar suporte a esse cenário, registre os escopos do seu aplicativo no portal do Azure.

Depois de entrar, o aplicativo exibirá os dados retornados do ponto de extremidade `/me` Microsoft Graph.

## <a name="get-help"></a>Obter ajuda

Visite [ajuda e suporte](https://docs.microsoft.com/azure/active-directory/develop/developer-support-help-options) se você tiver problemas com este tutorial ou com a plataforma de identidade da Microsoft.

Ajude-nos a melhorar a plataforma Microsoft Identity. Diga-nos o que você imagina ao concluir uma pesquisa curta de duas perguntas.

> [!div class="nextstepaction"]
> [Pesquisa sobre plataforma de identidade da Microsoft](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyKrNDMV_xBIiPGgSvnbQZdUQjFIUUFGUE1SMEVFTkdaVU5YT0EyOEtJVi4u)