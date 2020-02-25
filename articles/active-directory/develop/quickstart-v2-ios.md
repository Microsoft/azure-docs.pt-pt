---
title: Plataforma de identidade da Microsoft iOS e macOS quickstart / Azure
description: Saiba como assinar nos utilizadores e consultar o Microsoft Graph numa aplicação iOS ou macOS.
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
ms.openlocfilehash: f0b4d1f557006ba8a343a0497262cc5c8254e86c
ms.sourcegitcommit: dd3db8d8d31d0ebd3e34c34b4636af2e7540bd20
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/22/2020
ms.locfileid: "77561587"
---
# <a name="quickstart-sign-in-users-and-call-the-microsoft-graph-api-from-an-ios-or-macos-app"></a>Quickstart: Inscreva-se nos utilizadores e ligue para o Microsoft Graph API a partir de uma aplicação iOS ou macOS

Este quickstart contém uma amostra de código que demonstra como uma aplicação nativa iOS ou macOS pode usar a plataforma de identidade da Microsoft para assinar em contas pessoais, laborais e escolares, obter um sinal de acesso e ligar para a Microsoft Graph API.

Este quickstart aplica-se tanto às aplicações iOS como macOS. São necessários alguns passos apenas para aplicações iOS. Esses passos chamam a atenção para o facto de serem apenas para iOS.

![Mostra como funciona a aplicação de amostragerada por este quickstart](media/quickstart-v2-ios/ios-intro.svg)

> [!NOTE]
> **Pré-requisitos**
> * XCode 10+
> * iOS 10+ 
> * macOS 10.12+

> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-app"></a>Registar e transferir a aplicação do início rápido
> Tem duas opções para iniciar a aplicação de início rápido:
> * [Express] [Opção 1: registar e configurar automaticamente a sua aplicação e, em seguida, transferir o exemplo de código](#option-1-register-and-auto-configure-your-app-and-then-download-the-code-sample)
> * [Manual] [Opção 2: registar e configurar manualmente a aplicação e o exemplo de código](#option-2-register-and-manually-configure-your-application-and-code-sample)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-the-code-sample"></a>Opção 1: Registe e configure automaticamente a sua aplicação e, em seguida, descarregue a amostra de código
> #### <a name="step-1-register-your-application"></a>Passo 1: Registar a aplicação
> Para registar a sua aplicação,
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
>      - Ignore outras configurações nesta página.
>      - Selecione `Register`.
> 1. Na secção **Gerir,** selecione `Authentication` > `Add Platform` > `iOS`.
>      - Introduza o ***Identificador de Pacote*** para a sua aplicação. O identificador de pacote é apenas uma cadeia única que identifica exclusivamente a sua aplicação, por exemplo `com.<yourname>.identitysample.MSALMacOS`. Tome nota do valor que utiliza.
>      - Note que a configuração do iOS também é aplicável às aplicações macOS.
> 1. Selecione `Configure` e guarde os detalhes da ***Configuração MSAL*** para mais tarde neste arranque rápido.
> [!div renderon="portal" class="sxs-lookup"]
>
> #### <a name="step-1-configure-your-application"></a>Passo 1: Configurar a aplicação
> Para a amostra de código para este arranque rápido funcionar, você precisa adicionar um URI redireccionador compatível com o corretor Auth.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Fazer esta alteração por mim]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Já configurada](media/quickstart-v2-ios/green-check.png) A sua aplicação está configurada com estes atributos

#### <a name="step-2-download-the-sample-project"></a>Passo 2: Descarregue o projeto da amostra

- [Descarregue a Amostra de Código para iOS](https://github.com/Azure-Samples/active-directory-ios-swift-native-v2/archive/master.zip)
- [Descarregue a amostra de código para macOS](https://github.com/Azure-Samples/active-directory-macOS-swift-native-v2/archive/master.zip)

#### <a name="step-3-install-dependencies"></a>Passo 3: Instalar dependências

Numa janela terminal, navegue para a pasta com a amostra de código descarregada e faça `pod install` para instalar a mais recente biblioteca MSAL.

#### <a name="step-4-configure-your-project"></a>Passo 4: Configure o seu projeto

> [!div renderon="docs"]
> Se selecionou a Opção 1 acima, pode saltar estes passos. 

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
> 1. Abra as definições do projeto. Na secção **Identidade,** introduza o **Identificador de Pacote** que introduziu no portal.
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
> 1. Construir e executar a app!
> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > Enter_the_Supported_Account_Info_Here
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
> 1. Abra as definições do projeto. Na secção **Identidade,** introduza o **Identificador de Pacote** que introduziu no portal.
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
> 1. Construir e executar a app! 

## <a name="more-information"></a>Mais Informações

Leia estas secções para saber mais sobre este início rápido.

### <a name="get-msal"></a>Obter MSAL

MSAL[(MSAL.framework)](https://github.com/AzureAD/microsoft-authentication-library-for-objc)é a biblioteca usada para assinar utilizadores e solicitar fichas usadas para aceder a uma API protegida pela plataforma de identidade microsoft. Pode adicionar a MSAL à sua aplicação através do seguinte processo:

```
$ vi Podfile

```
Adicione o seguinte a este podfile (com o alvo do seu projeto):

```
use_frameworks!

target 'MSALiOS' do
   pod 'MSAL'
end

```

Executar comando de instalação de CocoaPods:

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
> | `authority` | O ponto final da plataforma de identidade da Microsoft. Na maioria dos casos será *https<span/>: //login.microsoftonline.com/common* |
> | `redirectUri` | O redirecionamento uri da aplicação. Pode passar 'nil' para utilizar o valor predefinido, ou o seu URI de redirecionamento personalizado. |

### <a name="for-ios-only-additional-app-requirements"></a>Apenas para iOS, requisitos adicionais de aplicativos

A sua aplicação também deve ter o seguinte na sua `AppDelegate`. Isto permite que a MSAL SDK manuseie a resposta simbólica da aplicação de corretor Auth quando fizer a autenticação.

 ```swift
 func application(_ app: UIApplication, open url: URL, options: [UIApplication.OpenURLOptionsKey : Any] = [:]) -> Bool {
        
        return MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: options[UIApplication.OpenURLOptionsKey.sourceApplication] as? String)
    }

 ```

> [!NOTE]
> No iOS 13+, se adotar `UISceneDelegate` em vez de `UIApplicationDelegate`, coloque este código no `scene:openURLContexts:` backback (consulte a [documentação da Apple).](https://developer.apple.com/documentation/uikit/uiscenedelegate/3238059-scene?language=objc)
> Se apoiar tanto o UISceneDelegate como o UIApplicationDelegate para a compatibilidade com iOS mais antigo, o backback da MSAL tem de ser colocado em ambos os locais.

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

Por fim, a sua aplicação deve ter uma entrada `LSApplicationQueriesSchemes` no seu ***Info.plist*** ao lado do `CFBundleURLTypes`. A amostra vem com isto incluído. 

   ```xml 
   <key>LSApplicationQueriesSchemes</key>
   <array>
      <string>msauthv2</string>
      <string>msauthv3</string>
   </array>
   ```

### <a name="sign-in-users--request-tokens"></a>Inscreva-se em utilizadores e tokens de pedido

A MSAL tem dois métodos que servem para comprar tokens: `acquireToken` e `acquireTokenSilent`.

#### <a name="acquiretoken-get-a-token-interactively"></a>adquirirToken: Obter um símbolo interativamente

Algumas situações exigem que os utilizadores interajam com a plataforma de identidade da Microsoft. Nestes casos, o utilizador final pode ser obrigado a selecionar a sua conta, introduzir as suas credenciais ou consentir com as permissões da sua aplicação. Por exemplo, 

* A primeira vez que os utilizadores iniciam sessão na aplicação
* Se um utilizador redefinir a sua palavra-passe, terá de introduzir as suas credenciais 
* Quando a sua aplicação está a solicitar acesso a um recurso pela primeira vez
* Quando mFA ou outras políticas de acesso condicional são necessárias

```swift
let parameters = MSALInteractiveTokenParameters(scopes: kScopes, webviewParameters: self.webViewParamaters!)
self.applicationContext!.acquireToken(with: parameters) { (result, error) in /* Add your handling logic */}
```

> |Em que:||
> |---------|---------|
> | `scopes` | Contém os âmbitos que estão a ser solicitados (isto é, `[ "user.read" ]` para o Microsoft Graph ou `[ "<Application ID URL>/scope" ]` para APIs web personalizados (`api://<Application ID>/access_as_user`) |

#### <a name="acquiretokensilent-get-an-access-token-silently"></a>adquirirTokenSilent: Obtenha um sinal de acesso silenciosamente

As aplicações não devem exigir que os seus utilizadores assinem sempre que pedem um sinal. Se o utilizador já se inscreveu, este método permite que as aplicações solicitem tokens em silêncio. 

```swift
guard let account = try self.applicationContext!.allAccounts().first else { return }
        
let silentParams = MSALSilentTokenParameters(scopes: kScopes, account: account)
self.applicationContext!.acquireTokenSilent(with: silentParams) { (result, error) in /* Add your handling logic */}
```

> |Em que: ||
> |---------|---------|
> | `scopes` | Contém os âmbitos que estão a ser solicitados (isto é, `[ "user.read" ]` para o Microsoft Graph ou `[ "<Application ID URL>/scope" ]` para APIs web personalizados (`api://<Application ID>/access_as_user`) |
> | `account` | A conta está a ser pedida. Este quickstart é sobre um pedido de conta única. Se quiser construir uma aplicação multi-conta, terá de definir lógica para identificar qual a conta a utilizar para pedidos simbólicos utilizando `applicationContext.account(forHomeAccountId: self.homeAccountId)` |

## <a name="next-steps"></a>Passos seguintes

Experimente o tutorial do iOS para um guia passo a passo completo sobre as aplicações de construção, incluindo uma explicação completa deste arranque rápido.

### <a name="learn-how-to-create-the-application-used-in-this-quickstart"></a>Saiba como criar a aplicação usada neste arranque rápido

> [!div class="nextstepaction"]
> [Chamar tutorial da Graph API iOS](https://docs.microsoft.com/azure/active-directory/develop/guidedsetups/active-directory-ios)

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

Ajude-nos a melhorar a plataforma de identidade da Microsoft. Diga-nos o que pensa ao concluir um pequeno inquérito de duas perguntas.

> [!div class="nextstepaction"]
> [Pesquisa da plataforma de identidade da Microsoft](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyKrNDMV_xBIiPGgSvnbQZdUQjFIUUFGUE1SMEVFTkdaVU5YT0EyOEtJVi4u)
