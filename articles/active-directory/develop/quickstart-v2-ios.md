---
title: Guia de início rápido do iOS e macOS da plataforma Microsoft Identity | Azure
description: Saiba como conectar usuários e consultar Microsoft Graph em um aplicativo iOS ou macOS.
services: active-directory
documentationcenter: dev-center-name
author: TylerMSFT
manager: CelesteDG
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/24/2019
ms.author: twhitney
ms.reviewer: jmprieur, saeeda
ms.custom: aaddev, identityplatformtop40
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1b8637bb1a1ba397750bd04c88c6535fa3d1caa0
ms.sourcegitcommit: 0486aba120c284157dfebbdaf6e23e038c8a5a15
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/26/2019
ms.locfileid: "71309645"
---
# <a name="quickstart-sign-in-users-and-call-the-microsoft-graph-api-from-an-ios-or-macos-app"></a>Início rápido: Conectar usuários e chamar a API de Microsoft Graph de um aplicativo iOS ou macOS

Este guia de início rápido contém um exemplo de código que demonstra como um aplicativo iOS ou macOS nativo pode entrar em contas pessoais, corporativas e de estudante, obter um token de acesso e chamar a API de Microsoft Graph.

Este início rápido se aplica a aplicativos iOS e macOS. Algumas etapas são necessárias apenas para aplicativos iOS. Essas etapas chamam que são apenas para iOS.

![Mostra como o aplicativo de exemplo gerado por este início rápido funciona](media/quickstart-v2-ios/ios-intro.svg)

> [!NOTE]
> **Pré-requisitos**
> * XCode 10 +
> * iOS 10+ 
> * macOS 10.12 +

> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-app"></a>Registar e transferir a aplicação do início rápido
> Tem duas opções para iniciar a aplicação de início rápido:
> * Express [Opção 1: Registre e configure automaticamente seu aplicativo e, em seguida, baixe seu exemplo de código](#option-1-register-and-auto-configure-your-app-and-then-download-the-code-sample)
> * Manual [Opção 2: Registrar e configurar manualmente seu aplicativo e exemplo de código](#option-2-register-and-manually-configure-your-application-and-code-sample)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-the-code-sample"></a>Opção 1: Registre e configure automaticamente seu aplicativo e, em seguida, baixe o exemplo de código
> #### <a name="step-1-register-your-application"></a>Passo 1: Registar a sua aplicação
> Para registrar seu aplicativo,
> 1. Vá para o novo painel de [registros de aplicativo de portal do Azure](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/IosQuickstartPage/sourceType/docs) .
> 1. Introduza um nome para a sua aplicação e xelecione **Registar**.
> 1. Siga as instruções para transferir e configurar automaticamente a sua nova aplicação com um só clique.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>Opção 2: Registrar e configurar manualmente seu aplicativo e exemplo de código
>
> #### <a name="step-1-register-your-application"></a>Passo 1: Registar a sua aplicação
> Para registar a sua aplicação e adicionar as informações de registo da aplicação à sua solução manualmente, siga os passos a seguir:
>
> 1. Navegue até a página da plataforma Microsoft Identity para desenvolvedores [registros de aplicativo](https://aka.ms/MobileAppReg) .
> 1. Selecione **novo registro**.
> 1. Quando a página **Registar uma aplicação** for apresentada, introduza as informações de registo da aplicação:
>      - Na seção **nome** , insira um nome de aplicativo significativo que será exibido aos usuários do aplicativo quando eles entrarem ou consentirem em seu aplicativo.
>      - Ignorar outras configurações nesta página.
>      - Selecione `Register`.
> 1. Na seção **gerenciar** `Authentication`, selecione.  >  `Add Platform`  >  `iOS`
>      - Insira o ***identificador do pacote*** para seu aplicativo. O identificador de pacote é apenas uma cadeia de caracteres exclusiva que identifica exclusivamente seu aplicativo, `com.<yourname>.identitysample.MSALMacOS`por exemplo. Anote o valor que você usa.
>      - Observe que a configuração do iOS também é aplicável a aplicativos macOS.
> 1. Selecione `Configure` e salve os detalhes de ***configuração do MSAL*** para mais tarde neste guia de início rápido.
> [!div renderon="portal" class="sxs-lookup"]
>
> #### <a name="step-1-configure-your-application"></a>Passo 1: Configurar a sua aplicação
> Para que o exemplo de código para este guia de início rápido funcione, você precisa adicionar um URI de redirecionamento compatível com o agente de autenticação.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Fazer esta alteração por mim]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Já configurada](media/quickstart-v2-ios/green-check.png) A sua aplicação está configurada com estes atributos

#### <a name="step-2-download-the-sample-project"></a>Passo 2: Baixar o projeto de exemplo

- [Baixar o exemplo de código para iOS](https://github.com/Azure-Samples/active-directory-ios-swift-native-v2/archive/master.zip)
- [Baixar o exemplo de código para macOS](https://github.com/Azure-Samples/active-directory-macOS-swift-native-v2/archive/master.zip)

#### <a name="step-3-install-dependencies"></a>Passo 3: Instalar dependências

Em uma janela de terminal, navegue até a pasta com o exemplo de código baixado `pod install` e execute para instalar a biblioteca MSAL mais recente.

#### <a name="step-4-configure-your-project"></a>Passo 4: Configurar seu projeto

> [!div renderon="docs"]
> Se você selecionou a opção 1 acima, poderá ignorar essas etapas. 

> [!div renderon="portal" class="sxs-lookup"]
> 1. Extraia o ficheiro zip e abra o projeto no XCode.
> 1. Edite **ViewController. Swift** e substitua a linha que começa com ' Let kClientID ' com o trecho de código a seguir. Lembre-se de atualizar o `kClientID` valor de com a ID do cliente que você salvou quando registrou seu aplicativo no portal anteriormente no início rápido:
>    ```swift
>    let kClientID = "<ENTER_YOUR_APPLICATION/CLIENT_ID>"
>    ```
> 1. Abra as configurações do projeto. Na seção **identidade** , insira o **identificador do pacote** que você inseriu no Portal.
> 1. Somente para IOS, clique com o botão direito do mouse em **info. plist** e selecione **abrir como** > **código-fonte**.
> 1. Somente para IOS, no nó raiz do dict, substitua `Enter_the_bundle_Id_Here`pela ***ID do pacote*** que você inseriu no Portal.
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
> > Este guia de início rápido dá suporte a Enter_the_Supported_Account_Info_Here.
> [!div renderon="docs"]
>
> 1. Extraia o ficheiro zip e abra o projeto no XCode.
> 1. Edite **ViewController. Swift** e substitua a linha que começa com ' Let kClientID ' com o trecho de código a seguir. Lembre-se de atualizar o `kClientID` valor de com o ClientID que você salvou quando registrou seu aplicativo no portal anteriormente neste guia de início rápido:
>    ```swift
>    let kClientID = "<ENTER_YOUR_APPLICATION/CLIENT_ID>"
>    ```
> 1. Abra as configurações do projeto. Na seção **identidade** , insira o **identificador do pacote** que você inseriu no Portal.
> 1. Somente para IOS, clique com o botão direito do mouse em **info. plist** e selecione **abrir como** > **código-fonte**.
> 1. Somente para IOS, no nó raiz do dict, substitua `Enter_the_bundle_Id_Here`pela ***ID do pacote*** que você usou no Portal.
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

MSAL ([MSAL. Framework](https://github.com/AzureAD/microsoft-authentication-library-for-objc)) é a biblioteca usada para conectar usuários e solicitar tokens usados para acessar uma API protegida pela plataforma de identidade da Microsoft. Pode adicionar a MSAL à sua aplicação através do seguinte processo:

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

### <a name="initialize-msal"></a>Inicializar MSAL

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

Seu aplicativo também deve ter o seguinte no seu `AppDelegate`. Isso permite que o SDK do MSAL manipule a resposta do token do aplicativo do agente de autenticação quando você faz a autenticação.

 ```swift
 func application(_ app: UIApplication, open url: URL, options: [UIApplication.OpenURLOptionsKey : Any] = [:]) -> Bool {
        
        return MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: options[UIApplication.OpenURLOptionsKey.sourceApplication] as? String)
    }

 ```

> [!NOTE]
> No Ios 13 +, se você adotar `UISceneDelegate` o `scene:openURLContexts:` em `UIApplicationDelegate`vez de, coloque esse código no retorno de chamada em vez disso (consulte a [documentação da Apple](https://developer.apple.com/documentation/uikit/uiscenedelegate/3238059-scene?language=objc)).
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

Por fim, seu aplicativo deve ter `LSApplicationQueriesSchemes` uma entrada no ***info. plist*** juntamente com `CFBundleURLTypes`o. O exemplo vem com isso incluído. 

   ```xml 
   <key>LSApplicationQueriesSchemes</key>
   <array>
      <string>msauthv2</string>
      <string>msauthv3</string>
   </array>
   ```

### <a name="sign-in-users--request-tokens"></a>Usuários de entrada & tokens de solicitação

A MSAL tem dois métodos que servem para comprar tokens: `acquireToken` e `acquireTokenSilent`.

#### <a name="acquiretoken-get-a-token-interactively"></a>AcquireToken Obter um token interativamente

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
> | `scopes` | Contém os escopos que estão sendo solicitados `[ "user.read" ]` (ou seja `[ "<Application ID URL>/scope" ]` , para Microsoft Graph ou para`api://<Application ID>/access_as_user`APIs Web personalizadas () |

#### <a name="acquiretokensilent-get-an-access-token-silently"></a>AcquireTokenSilent Obter um token de acesso silenciosamente

Os aplicativos não devem exigir que seus usuários façam logon toda vez que solicitarem um token. Se o usuário já tiver entrado, esse método permitirá que os aplicativos solicitem tokens silenciosamente. 

```swift
guard let account = try self.applicationContext!.allAccounts().first else { return }
        
let silentParams = MSALSilentTokenParameters(scopes: kScopes, account: account)
self.applicationContext!.acquireTokenSilent(with: silentParams) { (result, error) in /* Add your handling logic */}
```

> |Em que: ||
> |---------|---------|
> | `scopes` | Contém os escopos que estão sendo solicitados `[ "user.read" ]` (ou seja `[ "<Application ID URL>/scope" ]` , para Microsoft Graph ou para`api://<Application ID>/access_as_user`APIs Web personalizadas () |
> | `account` | A conta para a qual um token está sendo solicitado. Este início rápido é um aplicativo de conta única, se você quiser criar um aplicativo de várias contas, precisará definir a lógica para identificar qual conta usar para solicitações de token`applicationContext.account(forHomeAccountId: self.homeAccountId)` |

## <a name="next-steps"></a>Passos seguintes

Experimente o tutorial do iOS para obter um guia passo a passo completo sobre a criação de aplicativos, incluindo uma explicação completa deste início rápido.

### <a name="learn-how-to-create-the-application-used-in-this-quickstart"></a>Saiba como criar o aplicativo usado neste guia de início rápido

> [!div class="nextstepaction"]
> [Chamar tutorial da Graph API iOS](https://docs.microsoft.com/azure/active-directory/develop/guidedsetups/active-directory-ios)

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

Ajude-nos a melhorar a plataforma Microsoft Identity. Diga-nos o que você imagina ao concluir uma pesquisa curta de duas perguntas.

> [!div class="nextstepaction"]
> [Pesquisa sobre plataforma de identidade da Microsoft](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyKrNDMV_xBIiPGgSvnbQZdUQjFIUUFGUE1SMEVFTkdaVU5YT0EyOEtJVi4u)
