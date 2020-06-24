---
title: Plataforma de identidade da Microsoft iOS e macOS quickstart Rio Azure
description: Saiba como iniciar serção nos utilizadores e consultar o Microsoft Graph numa aplicação iOS ou macOS.
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
ms.openlocfilehash: b4092ac7bdd03d2e978cd9d9a8aa9fd2aaf1cdd2
ms.sourcegitcommit: c4ad4ba9c9aaed81dfab9ca2cc744930abd91298
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/12/2020
ms.locfileid: "84733296"
---
# <a name="quickstart-sign-in-users-and-call-the-microsoft-graph-api-from-an-ios-or-macos-app"></a>Quickstart: Inicie sísmia nos utilizadores e ligue para a Microsoft Graph API a partir de uma aplicação para iOS ou macOS

Este quickstart contém uma amostra de código que demonstra como uma aplicação nativa do iOS ou macOS pode usar a plataforma de identidade da Microsoft para assinar em contas pessoais, de trabalho e escola, obter um token de acesso e ligar para a Microsoft Graph API.

Este quickstart aplica-se tanto às aplicações iOS como ao macOS. Alguns passos são necessários apenas para aplicações iOS. Esses passos dizem que são apenas para iOS.

![Mostra como funciona a app de amostras gerada por este quickstart](media/quickstart-v2-ios/ios-intro.svg)

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
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-the-code-sample"></a>Opção 1: Registar e configurar automaticamente a sua app e, em seguida, descarregar a amostra de código
> #### <a name="step-1-register-your-application"></a>Passo 1: Registar a aplicação
> Para registar a sua aplicação,
> 1. Aceda ao novo [portal Azure - Painel de inscrições](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/IosQuickstartPage/sourceType/docs) da App.
> 1. Introduza um nome para a sua aplicação e xelecione **Registar**.
> 1. Siga as instruções para transferir e configurar automaticamente a sua nova aplicação com um só clique.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>Opção 2: registar e configurar manualmente a aplicação e o exemplo de código
>
> #### <a name="step-1-register-your-application"></a>Passo 1: Registar a aplicação
> Para registar a sua aplicação e adicionar as informações de registo da aplicação à sua solução manualmente, siga os passos a seguir:
>
> 1. Navegue para a plataforma de identidade da Microsoft para programadores [Página de registos de aplicações.](https://aka.ms/MobileAppReg)
> 1. Selecione **Novo registo**.
> 1. Quando a página **Registar uma aplicação** for apresentada, introduza as informações de registo da aplicação:
>      - Na secção **Nome,** introduza um nome de aplicação significativo que será apresentado aos utilizadores da app quando iniciarem sedudo ou consentirem com a sua aplicação.
>      - Ignore outras configurações nesta página.
>      - Selecione `Register`.
> 1. Na secção **Gerir,** selecione `Authentication`  >  `Add Platform`  >  `iOS` .
>      - Insira o ***identificador de pacotes*** para a sua aplicação. O identificador do pacote é apenas uma corda única que identifica exclusivamente a sua aplicação, por `com.<yourname>.identitysample.MSALMacOS` exemplo. Tome nota do valor que utiliza.
>      - Note que a configuração do iOS também é aplicável às aplicações do macOS.
> 1. Selecione `Configure` e guarde os detalhes ***da Configuração MSAL*** para mais tarde neste arranque rápido.
> [!div renderon="portal" class="sxs-lookup"]
>
> #### <a name="step-1-configure-your-application"></a>Passo 1: Configurar a aplicação
> Para que a amostra de código para este arranque rápido funcione, é necessário adicionar um URI de redirecionamento compatível com o corretor Auth.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Fazer esta alteração por mim]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Já configurada](media/quickstart-v2-ios/green-check.png) A sua aplicação está configurada com estes atributos
> 
> #### <a name="step-2-download-the-sample-project"></a>Passo 2: Descarregue o projeto de amostra
> > [!div id="autoupdate_ios" class="nextstepaction"]
> > [Descarregue a amostra de código para iOS]()
> 
> > [!div id="autoupdate_macos" class="nextstepaction"]
> > [Descarregue a amostra de código para macOS]()
> [!div renderon="docs"]
> #### <a name="step-2-download-the-sample-project"></a>Passo 2: Descarregue o projeto de amostra
> 
> - [Descarregue a amostra de código para iOS](https://github.com/Azure-Samples/active-directory-ios-swift-native-v2/archive/master.zip)
> - [Descarregue a amostra de código para macOS](https://github.com/Azure-Samples/active-directory-macOS-swift-native-v2/archive/master.zip)

#### <a name="step-3-install-dependencies"></a>Passo 3: Instalar dependências

Numa janela terminal, navegue para a pasta com a amostra de código descarregada e corra `pod install` para instalar a mais recente biblioteca MSAL.

> [!div renderon="portal" class="sxs-lookup"]
> #### <a name="step-4-your-app-is-configured-and-ready-to-run"></a>Passo 4: A sua aplicação está configurada e pronta para ser executada
> Configurámos o seu projeto com valores das propriedades da sua aplicação e está pronto para funcionar.
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`
>
> [!div renderon="docs"]
>#### <a name="step-4-configure-your-project"></a>Passo 4: Configurar o seu projeto
> Se selecionou a Opção 1 acima, pode saltar estes passos.
> 1. Extraia o ficheiro zip e abra o projeto no XCode.
> 1. Edite **ViewController.swift** e substitua a linha começando por 'let kClientID' com o seguinte corte de código. Lembre-se de atualizar o valor para `kClientID` com o clientID que guardou quando registou a sua aplicação no portal mais cedo neste arranque rápido:
>    ```swift
>    let kClientID = "Enter_the_Application_Id_Here"
>    ```
> 1. Se estiver a construir uma aplicação para [nuvens nacionais Azure AD,](https://docs.microsoft.com/graph/deployments#app-registration-and-token-service-root-endpoints)substitua a linha que começa por 'let kGraphEndpoint' e 'let kAuthority' com os pontos finais corretos. Para acesso global, utilize valores predefinidos:
>     ```swift
>     let kGraphEndpoint = "https://graph.microsoft.com/"
>     let kAuthority = "https://login.microsoftonline.com/common"
>     ```
> 1. Outros pontos finais estão documentados [aqui.](https://docs.microsoft.com/graph/deployments#app-registration-and-token-service-root-endpoints) Por exemplo, para executar o quickstart com a Azure AD Germany, use o seguinte:
>     ```swift
>     let kGraphEndpoint = "https://graph.microsoft.de/"
>     let kAuthority = "https://login.microsoftonline.de/common"
>     ```
> 1. Abra as definições do projeto. Na secção **Identidade,** insira o **identificador de pacote** que inseriu no portal.
> 1. Clique à direita **Info.plist** e selecione **Abrir Como**  >  **Código Fonte**.
> 1. Sob o nó de raiz dict, `Enter_the_bundle_Id_Here` substitua-o pelo ***ID do pacote*** que usou no portal.
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
> 1. Construa & executar a aplicação!

## <a name="more-information"></a>Mais Informações

Leia estas secções para saber mais sobre este início rápido.

### <a name="get-msal"></a>Obter MSAL

MSAL[(MSAL.framework)](https://github.com/AzureAD/microsoft-authentication-library-for-objc)é a biblioteca utilizada para assinar nos utilizadores e pedir fichas usadas para aceder a uma API protegida pela plataforma de identidade Microsoft. Pode adicionar a MSAL à sua aplicação através do seguinte processo:

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

Executar comando de instalação de Cacau:

```pod install```

### <a name="initialize-msal"></a>Inicializar o MSAL

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
> | `redirectUri` | O URI redirecionado da aplicação. Pode passar 'zero' para utilizar o valor padrão ou o seu URI de redirecionamento personalizado. |

### <a name="for-ios-only-additional-app-requirements"></a>Apenas para iOS, requisitos adicionais de aplicações

A sua aplicação também deve ter o seguinte na `AppDelegate` sua. Isto permite que a MSAL SDK lide com a resposta simbólica da aplicação de corretor Auth quando faz a autenticação.

 ```swift
 func application(_ app: UIApplication, open url: URL, options: [UIApplication.OpenURLOptionsKey : Any] = [:]) -> Bool {

        return MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: options[UIApplication.OpenURLOptionsKey.sourceApplication] as? String)
    }

 ```

> [!NOTE]
> No iOS 13+, se adotar `UISceneDelegate` em vez de , coloque este código na chamada `UIApplicationDelegate` `scene:openURLContexts:` (ver [documentação da Apple).](https://developer.apple.com/documentation/uikit/uiscenedelegate/3238059-scene?language=objc)
> Se apoiar tanto o UISceneDeeDeegate como o UIApplicationDele para compatibilidade com iOS mais antigo, o callback MSAL precisa de ser colocado em ambos os locais.

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

Finalmente, a sua aplicação deve ter uma `LSApplicationQueriesSchemes` entrada na sua ***Info.plist*** ao lado da `CFBundleURLTypes` . A amostra vem com isto incluído.

   ```xml
   <key>LSApplicationQueriesSchemes</key>
   <array>
      <string>msauthv2</string>
      <string>msauthv3</string>
   </array>
   ```

### <a name="sign-in-users--request-tokens"></a>Inscreva-se nos utilizadores & pedido de fichas

A MSAL tem dois métodos que servem para comprar tokens: `acquireToken` e `acquireTokenSilent`.

#### <a name="acquiretoken-get-a-token-interactively"></a>adquirirToken: Obter um símbolo interativamente

Algumas situações exigem que os utilizadores interajam com a plataforma de identidade da Microsoft. Nestes casos, o utilizador final poderá ser obrigado a selecionar a sua conta, introduzir as suas credenciais ou consentir com as permissões da sua aplicação. Por exemplo,

* A primeira vez que os utilizadores iniciam sessão na aplicação
* Se um utilizador redefinir a sua palavra-passe, terá de introduzir as suas credenciais
* Quando a sua aplicação está a solicitar acesso a um recurso pela primeira vez
* Quando são necessárias políticas de acesso condicional ou MFA ou outras

```swift
let parameters = MSALInteractiveTokenParameters(scopes: kScopes, webviewParameters: self.webViewParamaters!)
self.applicationContext!.acquireToken(with: parameters) { (result, error) in /* Add your handling logic */}
```

> |Em que:||
> |---------|---------|
> | `scopes` | Contém os âmbitos solicitados (isto é, `[ "user.read" ]` para o Microsoft Graph ou para `[ "<Application ID URL>/scope" ]` APIs web personalizados ( `api://<Application ID>/access_as_user` ) |

#### <a name="acquiretokensilent-get-an-access-token-silently"></a>adquirirTokenSilent: Obter um token de acesso silenciosamente

As aplicações não devem exigir que os seus utilizadores assinem sempre que solicitam um token. Se o utilizador já se inscreveu, este método permite que as aplicações solicitem tokens silenciosamente.

```swift
self.applicationContext!.getCurrentAccount(with: nil) { (currentAccount, previousAccount, error) in

   guard let account = currentAccount else {
      return
   }

   let silentParams = MSALSilentTokenParameters(scopes: self.kScopes, account: account)
   self.applicationContext!.acquireTokenSilent(with: silentParams) { (result, error) in /* Add your handling logic */}
}
```

> |Em que: ||
> |---------|---------|
> | `scopes` | Contém os âmbitos solicitados (isto é, `[ "user.read" ]` para o Microsoft Graph ou para `[ "<Application ID URL>/scope" ]` APIs web personalizados ( `api://<Application ID>/access_as_user` ) |
> | `account` | A conta está a ser pedida. Este quickstart é sobre uma única aplicação de conta. Se quiser construir uma aplicação multi-conta, terá de definir lógica para identificar qual a conta a utilizar para pedidos simbólicos usando `accountsFromDeviceForParameters:completionBlock:` e passando corretamente`accountIdentifier` |

## <a name="next-steps"></a>Passos seguintes

Experimente o tutorial para iOS e macOS para um guia completo passo a passo sobre aplicações de construção, incluindo uma explicação completa deste arranque rápido.

### <a name="learn-how-to-create-the-application-used-in-this-quickstart"></a>Saiba como criar a aplicação utilizada neste arranque rápido

> [!div class="nextstepaction"]
> [Ligue para o tutorial da API do Gráfico para iOS e macOS](https://docs.microsoft.com/azure/active-directory/develop/guidedsetups/active-directory-ios)

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]
