---
title: Guia de início rápido do iOS e macOS da plataforma Microsoft Identity | Azure
description: Saiba como conectar usuários e consultar Microsoft Graph em um aplicativo iOS ou macOS.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 09/24/2019
ms.author: marsma
ms.reviewer: jmprieur, saeeda
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started, languages:iOS
ms.openlocfilehash: c9eb8b3d909313470ee9febdc5b1c37eea834b08
ms.sourcegitcommit: 98a5a6765da081e7f294d3cb19c1357d10ca333f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/20/2020
ms.locfileid: "77484048"
---
# <a name="quickstart-sign-in-users-and-call-the-microsoft-graph-api-from-an-ios-or-macos-app"></a>Início rápido: conectar usuários e chamar a API de Microsoft Graph de um aplicativo iOS ou macOS

Este guia de início rápido contém um exemplo de código que demonstra como um aplicativo nativo do iOS ou macOS pode usar a plataforma de identidade da Microsoft para entrar em contas pessoais, corporativas e de estudante, obter um token de acesso e chamar a API de Microsoft Graph.

Este início rápido se aplica a aplicativos iOS e macOS. Algumas etapas são necessárias apenas para aplicativos iOS. Essas etapas chamam que são apenas para iOS.

![Mostra como funciona a aplicação de amostragerada por este quickstart](media/quickstart-v2-ios/ios-intro.svg)

> [!NOTE]
> **Pré-requisitos**
> * XCode 10 +
> * iOS 10+ 
> * macOS 10.12+

> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-app"></a>Registar e transferir a aplicação do início rápido
> Tem duas opções para iniciar a aplicação de início rápido:
> * [Express] [Opção 1: registar e configurar automaticamente a sua aplicação e, em seguida, transferir o exemplo de código](#option-1-register-and-auto-configure-your-app-and-then-download-the-code-sample)
> * [Manual] [Opção 2: registar e configurar manualmente a aplicação e o exemplo de código](#option-2-register-and-manually-configure-your-application-and-code-sample)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-the-code-sample"></a>Opção 1: registrar e configurar automaticamente seu aplicativo e, em seguida, baixar o exemplo de código
> #### <a name="step-1-register-your-application"></a>Passo 1: Registar a aplicação
> Para registrar seu aplicativo,
> 1. Vá ao novo portal Azure - Painel de inscrições de [aplicativos.](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/IosQuickstartPage/sourceType/docs)
> 1. Introduza um nome para a sua aplicação e xelecione **Registar**.
> 1. Siga as instruções para transferir e configurar automaticamente a sua nova aplicação com um só clique.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>Opção 2: registar e configurar manualmente a aplicação e o exemplo de código
>
> #### <a name="step-1-register-your-application"></a>Passo 1: Registar a aplicação
> Para registar a sua aplicação e adicionar as informações de registo da aplicação à sua solução manualmente, siga os passos a seguir:
>
> 1. Navegue na plataforma de identidade da Microsoft para programadores da página de registos de [aplicações.](https://aka.ms/MobileAppReg)
> 1. Selecione **Novo registo**.
> 1. Quando a página **Registar uma aplicação** for apresentada, introduza as informações de registo da aplicação:
>      - Na secção **Nome,** introduza um nome de aplicação significativo que será apresentado aos utilizadores da app quando iniciar o seu insessão ou consentir com a sua aplicação.
>      - Ignorar outras configurações nesta página.
>      - Selecione `Register`.
> 1. Na secção **Gerir,** selecione `Authentication` > `Add Platform` > `iOS`.
>      - Introduza o ***Identificador de Pacote*** para a sua aplicação. O identificador de pacote é apenas uma cadeia única que identifica exclusivamente a sua aplicação, por exemplo `com.<yourname>.identitysample.MSALMacOS`. Anote o valor que você usa.
>      - Observe que a configuração do iOS também é aplicável a aplicativos macOS.
> 1. Selecione `Configure` e guarde os detalhes da ***Configuração MSAL*** para mais tarde neste arranque rápido.
> [!div renderon="portal" class="sxs-lookup"]
>
> #### <a name="step-1-configure-your-application"></a>Passo 1: Configurar a aplicação
> Para que o exemplo de código para este guia de início rápido funcione, você precisa adicionar um URI de redirecionamento compatível com o agente de autenticação.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Fazer esta alteração por mim]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Já configurada](media/quickstart-v2-ios/green-check.png) A sua aplicação está configurada com estes atributos

#### <a name="step-2-download-the-sample-project"></a>Etapa 2: baixar o projeto de exemplo

- [Descarregue a Amostra de Código para iOS](https://github.com/Azure-Samples/active-directory-ios-swift-native-v2/archive/master.zip)
- [Descarregue a amostra de código para macOS](https://github.com/Azure-Samples/active-directory-macOS-swift-native-v2/archive/master.zip)

#### <a name="step-3-install-dependencies"></a>Etapa 3: instalar dependências

Numa janela terminal, navegue para a pasta com a amostra de código descarregada e faça `pod install` para instalar a mais recente biblioteca MSAL.

#### <a name="step-4-configure-your-project"></a>Etapa 4: configurar seu projeto

> [!div renderon="docs"]
> Se você selecionou a opção 1 acima, poderá ignorar essas etapas. 

> [!div renderon="portal" class="sxs-lookup"]
> 1. Extraia o ficheiro zip e abra o projeto no XCode.
> 1. Editar **ViewController.swift** e substituir a linha começando por 'let kClientID' com o seguinte código snippet. Lembre-se de atualizar o valor para `kClientID` com o ID do cliente que guardou quando registou a sua aplicação no portal mais cedo no início do arranque:
>    ```swift
>    let kClientID = "Enter_the_Application_Id_Here"
>    ```
> 1. Editar **ViewController.swift** e substituir a linha a partir de 'let kAuthority' com o seguinte código snippet:
>    ```swift
>    let kAuthority = "Enter_the_Authority_Endpoint_Host_HereEnter_the_Tenant_Info_Here"
>    ```
> 1. Editar **ViewController.swift** e substituir a linha a partir de 'let kGraphEndpoint' com o seguinte código snippet:
>    ```swift
>    let kGraphEndpoint = "Enter_the_MS_Graph_Endpoint_Host_Here"
>    ```
> 1. Abra as configurações do projeto. Na secção **Identidade,** introduza o **Identificador de Pacote** que introduziu no portal.
> 1. Apenas para iOS, clique à direita **Info.plist** e **selecione Open As** > Código **Fonte**.
> 1. Apenas para iOS, sob o nó raiz dita, substitua `CFBundleURLSchemes` com o ***Id bundle*** que inseriu no portal.
>
>    ```xml
>    <key>CFBundleURLTypes</key>
>    <array>
>       <dict>
>          <key>CFBundleURLSchemes</key>
>          <array>
>             <string>msauth.Enter_the_Bundle_Id_Here</string>
>          </array>
>       </dict>
>    </array>
>    ```
> 1. Compilar & executar o aplicativo!
> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > Este arranque rápido apoia Enter_the_Supported_Account_Info_Here.
> [!div renderon="docs"]
>
> 1. Extraia o ficheiro zip e abra o projeto no XCode.
> 1. Editar **ViewController.swift** e substituir a linha começando por 'let kClientID' com o seguinte código snippet. Lembre-se de atualizar o valor para `kClientID` com o clienteID que guardou quando registou a sua aplicação no portal mais cedo neste arranque rápido:
>    ```swift
>    let kClientID = "Enter_the_Application_Id_Here"
>    ```
> 1. Se estiver a construir uma aplicação para [nuvens nacionais Azure AD,](https://docs.microsoft.com/graph/deployments#app-registration-and-token-service-root-endpoints)substitua a linha começando por "let kGraphEndpoint" e 'let kAuthority' por pontos finais corretos. Para acesso global, utilize valores predefinidos:
>     ```objective-c
>     let kGraphEndpoint = "https://graph.microsoft.com/"
>     let kAuthority = "https://login.microsoftonline.com/common"
>     ```
> 1. Outros pontos finais estão documentados [aqui.](https://docs.microsoft.com/graph/deployments#app-registration-and-token-service-root-endpoints) Por exemplo, para executar o quickstart com a Azure AD Germany, use a seguinte:
>     ```objective-c
>     let kGraphEndpoint = "https://graph.microsoft.de/"
>     let kAuthority = "https://login.microsoftonline.de/common"
>     ```
> 1. Abra as configurações do projeto. Na secção **Identidade,** introduza o **Identificador de Pacote** que introduziu no portal.
> 1. Apenas para iOS, clique à direita **Info.plist** e **selecione Open As** > Código **Fonte**.
> 1. Apenas para iOS, sob o nó raiz dita, substitua `Enter_the_bundle_Id_Here` pelo ***Id bundle*** que utilizou no portal.
>
>    ```xml
>    <key>CFBundleURLTypes</key>
>    <array>
>       <dict>
>          <key>CFBundleURLSchemes</key>
>          <array>
>             <string>msauth.Enter_the_Bundle_Id_Here</string>
>          </array>
>       </dict>
>    </array>
> 
>    ```
> 1. Compilar & executar o aplicativo! 

## <a name="more-information"></a>Mais Informações

Leia estas secções para saber mais sobre este início rápido.

### <a name="get-msal"></a>Obter MSAL

MSAL[(MSAL.framework)](https://github.com/AzureAD/microsoft-authentication-library-for-objc)é a biblioteca usada para assinar utilizadores e solicitar fichas usadas para aceder a uma API protegida pela plataforma de identidade microsoft. Pode adicionar a MSAL à sua aplicação através do seguinte processo:

```
$ vi Podfile

```
Adicione o seguinte a este podfile (com o destino do seu projeto):

```
use_frameworks!

target 'MSALiOS' do
   pod 'MSAL'
end

```

Execute o comando de instalação do CocoaPods:

```pod install```

### <a name="initialize-msal"></a>Inicializar mSAL

Pode adicionar a referência para MSAL, adicionando o código seguinte:

```swift
import MSAL
```

Em seguida, inicialize a MSAL com o código seguinte:

```swift
let authority = try MSALAADAuthority(url: URL(string: kAuthority)!)
            
let msalConfiguration = MSALPublicClientApplicationConfig(clientId: kClientID, redirectUri: nil, authority: authority)
self.applicationContext = try MSALPublicClientApplication(configuration: msalConfiguration)
```

> |Em que: ||
> |---------|---------|
> | `clientId` | O ID de Aplicação da aplicação registada em *portal.azure.com* |
> | `authority` | O ponto de extremidade da plataforma Microsoft Identity. Na maioria dos casos será *https<span/>: //login.microsoftonline.com/common* |
> | `redirectUri` | O URI de redirecionamento do aplicativo. Você pode passar ' nil ' para usar o valor padrão ou o URI de redirecionamento personalizado. |

### <a name="for-ios-only-additional-app-requirements"></a>Somente para iOS, requisitos de aplicativo adicionais

A sua aplicação também deve ter o seguinte na sua `AppDelegate`. Isso permite que o SDK do MSAL manipule a resposta do token do aplicativo do agente de autenticação quando você faz a autenticação.

 ```swift
 func application(_ app: UIApplication, open url: URL, options: [UIApplication.OpenURLOptionsKey : Any] = [:]) -> Bool {
        
        return MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: options[UIApplication.OpenURLOptionsKey.sourceApplication] as? String)
    }

 ```

> [!NOTE]
> No iOS 13+, se adotar `UISceneDelegate` em vez de `UIApplicationDelegate`, coloque este código no `scene:openURLContexts:` backback (consulte a [documentação da Apple).](https://developer.apple.com/documentation/uikit/uiscenedelegate/3238059-scene?language=objc)
> Se você oferecer suporte a UISceneDelegate e UIApplicationDelegate para compatibilidade com iOS mais antigo, o retorno de chamada MSAL precisará ser colocado em ambos os locais.

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

Por fim, a sua aplicação deve ter uma entrada `LSApplicationQueriesSchemes` no seu ***Info.plist*** ao lado do `CFBundleURLTypes`. O exemplo vem com isso incluído. 

   ```xml 
   <key>LSApplicationQueriesSchemes</key>
   <array>
      <string>msauthv2</string>
      <string>msauthv3</string>
   </array>
   ```

### <a name="sign-in-users--request-tokens"></a>Usuários de entrada & tokens de solicitação

A MSAL tem dois métodos que servem para comprar tokens: `acquireToken` e `acquireTokenSilent`.

#### <a name="acquiretoken-get-a-token-interactively"></a>acquireToken: obter um token interativamente

Algumas situações exigem que os usuários interajam com a plataforma de identidade da Microsoft. Nesses casos, o usuário final pode ser solicitado a selecionar sua conta, inserir suas credenciais ou consentir as permissões do seu aplicativo. Por exemplo, 

* A primeira vez que os utilizadores iniciam sessão na aplicação
* Se um usuário redefinir sua senha, ele precisará inserir suas credenciais 
* Quando seu aplicativo está solicitando acesso a um recurso pela primeira vez
* Quando MFA ou outras políticas de acesso condicional são necessárias

```swift
let parameters = MSALInteractiveTokenParameters(scopes: kScopes, webviewParameters: self.webViewParamaters!)
self.applicationContext!.acquireToken(with: parameters) { (result, error) in /* Add your handling logic */}
```

> |Em que:||
> |---------|---------|
> | `scopes` | Contém os âmbitos que estão a ser solicitados (isto é, `[ "user.read" ]` para o Microsoft Graph ou `[ "<Application ID URL>/scope" ]` para APIs web personalizados (`api://<Application ID>/access_as_user`) |

#### <a name="acquiretokensilent-get-an-access-token-silently"></a>acquireTokenSilent: obter um token de acesso silenciosamente

Os aplicativos não devem exigir que seus usuários façam logon toda vez que solicitarem um token. Se o usuário já tiver entrado, esse método permitirá que os aplicativos solicitem tokens silenciosamente. 

```swift
guard let account = try self.applicationContext!.allAccounts().first else { return }
        
let silentParams = MSALSilentTokenParameters(scopes: kScopes, account: account)
self.applicationContext!.acquireTokenSilent(with: silentParams) { (result, error) in /* Add your handling logic */}
```

> |Em que: ||
> |---------|---------|
> | `scopes` | Contém os âmbitos que estão a ser solicitados (isto é, `[ "user.read" ]` para o Microsoft Graph ou `[ "<Application ID URL>/scope" ]` para APIs web personalizados (`api://<Application ID>/access_as_user`) |
> | `account` | A conta para a qual um token está sendo solicitado. Este guia de início rápido é sobre um aplicativo de conta única. Se quiser construir uma aplicação multi-conta, terá de definir lógica para identificar qual a conta a utilizar para pedidos simbólicos utilizando `applicationContext.account(forHomeAccountId: self.homeAccountId)` |

## <a name="next-steps"></a>Passos seguintes

Experimente o tutorial do iOS para obter um guia passo a passo completo sobre a criação de aplicativos, incluindo uma explicação completa deste início rápido.

### <a name="learn-how-to-create-the-application-used-in-this-quickstart"></a>Saiba como criar o aplicativo usado neste guia de início rápido

> [!div class="nextstepaction"]
> [Chamar tutorial da Graph API iOS](https://docs.microsoft.com/azure/active-directory/develop/guidedsetups/active-directory-ios)

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

Ajude-nos a melhorar a plataforma de identidade da Microsoft. Diga-nos o que pensa ao concluir um pequeno inquérito de duas perguntas.

> [!div class="nextstepaction"]
> [Pesquisa da plataforma de identidade da Microsoft](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyKrNDMV_xBIiPGgSvnbQZdUQjFIUUFGUE1SMEVFTkdaVU5YT0EyOEtJVi4u)
