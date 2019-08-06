---
title: Início rápido da plataforma Microsoft Identity para iOS | Azure
description: Saiba como conectar usuários e consultar Microsoft Graph em um aplicativo iOS.
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
ms.date: 04/18/2019
ms.author: twhitney
ms.reviewer: jmprieur, saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: e749e46a4a7398f7b862ce5d4dab7a1ba6d57c8f
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/06/2019
ms.locfileid: "68813029"
---
# <a name="quickstart-sign-in-users-and-call-the-microsoft-graph-api-from-an-ios-app"></a>Início rápido: Conectar usuários e chamar a API de Microsoft Graph de um aplicativo iOS

[!INCLUDE [active-directory-develop-applies-v2-msal](../../../includes/active-directory-develop-applies-v2-msal.md)]

Este início rápido contém um exemplo de código que demonstra como uma aplicação iOS nativa pode iniciar sessão em contas pessoais ou contas profissionais e escolares, obter um token de acesso e chamar a Microsoft Graph API.

![Mostra como o aplicativo de exemplo gerado por este início rápido funciona](media/quickstart-v2-ios/ios-intro.svg)

> [!NOTE]
> **Pré-requisitos**
> * XCode 10 +
> * iOS 10+ 

> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-app"></a>Registar e transferir a aplicação do início rápido
> Tem duas opções para iniciar a aplicação de início rápido:
> * Express [Opção 1: Registre e configure automaticamente seu aplicativo e, em seguida, baixe seu exemplo de código](#option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample)
> * Manual [Opção 2: Registrar e configurar manualmente seu aplicativo e exemplo de código](#option-2-register-and-manually-configure-your-application-and-code-sample)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Opção 1: Registre e configure automaticamente seu aplicativo e, em seguida, baixe seu exemplo de código
> #### <a name="step-1-register-your-application"></a>Passo 1: Registar a sua aplicação
> Para registrar seu aplicativo,
> 1. Vá para o novo painel de [registros de aplicativo de portal do Azure](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/IosQuickstartPage/sourceType/docs) .
> 1. Introduza um nome para a sua aplicação e xelecione **Registar**.
> 1. Siga as instruções para transferir e configurar automaticamente a sua nova aplicação com um só clique.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>Opção 2: Registrar e configurar manualmente seu aplicativo e exemplo de código
>
> #### <a name="step-1-register-your-application"></a>Passo 1: Registar a sua aplicação
> Para registar a sua aplicação e adicionar as informações de registo da aplicação à sua solução manualmente, siga os passos a seguir:
>
> 1. Navegue até a página da plataforma Microsoft Identity para desenvolvedores [registros de aplicativo](https://aka.ms/MobileAppReg) .
> 1. Selecione **novo registro**.
> 1. Quando a página **Registar uma aplicação** for apresentada, introduza as informações de registo da aplicação:
>      - Na seção **nome** , insira um nome de aplicativo significativo que será exibido aos usuários do aplicativo quando eles entrarem ou consentirem em seu aplicativo, por exemplo `iOSQuickstart`.
>      - Ignorar outras configurações nesta página. 
>      - Pressione o `Register` botão.
> 1. Clique no novo aplicativo > vá para `Authentication`. `Add Platform`  >   >  `iOS`    
>      - Insira o ***identificador do pacote*** para seu aplicativo. 
> 1. Selecione `Configure` e salve os detalhes de ***configuração do MSAL*** para mais tarde. 

> [!div renderon="portal" class="sxs-lookup"]
>
> #### <a name="step-1-configure-your-application"></a>Passo 1: Configurar a aplicação
> Para que o exemplo de código para este guia de início rápido funcione, você precisa adicionar um URI de redirecionamento compatível com o agente de autenticação. 
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Fazer esta alteração por mim]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Já configurada](media/quickstart-v2-ios/green-check.png) A sua aplicação está configurada com estes atributos

#### <a name="step-2-download-your-web-server-or-project"></a>Passo 2: Baixe seu servidor Web ou projeto

- [Baixar o exemplo de código](https://github.com/Azure-Samples/active-directory-ios-swift-native-v2/archive/master.zip)

#### <a name="step-3-configure-your-project"></a>Passo 3: Configurar seu projeto

> [!div renderon="docs"]
> Se você selecionou a opção 1 acima, poderá ignorar essas etapas. 

> [!div renderon="portal" class="sxs-lookup"]
> 1. Extraia o ficheiro zip e abra o projeto no XCode.
> 1. Edite **ViewController.swift** e substitua a linha que começa com "let kClientID" pelo seguinte fragmento de código:
>    ```swift
>    let kClientID = "Enter_the_Application_Id_here"
>    let kAuthority = "https://login.microsoftonline.com/Enter_the_Tenant_Info_Here"
>    ``` 
> 1. Clique com o botão direito do mouse em **info. plist** e selecione **abrir como** > **código-fonte**.
> 1. No nó raiz do dict, substitua por sua ***ID do pacote***:
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

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > Este guia de início rápido dá suporte a Enter_the_Supported_Account_Info_Here.

> [!div renderon="docs"]
>
> 1. Extraia o ficheiro zip e abra o projeto no XCode.
> 1. Edite **ViewController. Swift** e substitua a linha que começa com ' Let kClientID ' com o seguinte trecho de código:
>
>    ```swift
>    let kClientID = "<ENTER_YOUR_APPLICATION/CLIENT_ID>"
> 
>    ```
> 1. Clique com o botão direito do mouse em **info. plist** e selecione **abrir como** > **código-fonte**.
> 1. No nó raiz do dict, substitua por sua ***ID do pacote***:
>
>    ```xml
>    <key>CFBundleURLTypes</key>
>    <array>
>       <dict>
>          <key>CFBundleURLSchemes</key>
>          <array>
>             <string>msauth.<ENTER_YOUR_BUNDLE_ID></string>
>          </array>
>       </dict>
>    </array>
>
>    ```
> 1. Compilar & executar o aplicativo! 

## <a name="more-information"></a>Mais Informações

Leia estas secções para saber mais sobre este início rápido.

### <a name="getting-msal"></a>Obtendo MSAL

MSAL ([MSAL. Framework](https://github.com/AzureAD/microsoft-authentication-library-for-objc)) é a biblioteca usada para conectar usuários e solicitar tokens usados para acessar uma API protegida pela plataforma de identidade da Microsoft. Pode adicionar a MSAL à sua aplicação através do seguinte processo:

```
$ vi Podfile

```
Adicione o seguinte a este podfile (com o destino do seu projeto):

```
use_frameworks!

target 'MSALiOS' do
   pod 'MSAL', '~> 0.4.0'
end

```

### <a name="msal-initialization"></a>Inicialização da MSAL

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

### <a name="additional-app-requirements"></a>Requisitos de aplicativo adicionais  

Seu aplicativo também deve ter o seguinte no seu `AppDelegate`. Isso permite que o SDK do MSAL manipule a resposta do token do aplicativo do agente de autenticação quando você executa a autenticação.

 ```swift
 func application(_ app: UIApplication, open url: URL, options: [UIApplication.OpenURLOptionsKey : Any] = [:]) -> Bool {
         guard let sourceApplication = options[UIApplication.OpenURLOptionsKey.sourceApplication] as? String else {
             return false
         }
         
         return MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: sourceApplication)
     }

```

Por fim, seu aplicativo deve ter `LSApplicationQueriesSchemes` uma entrada no ***info. plist*** juntamente com `CFBundleURLTypes`o. O exemplo vem com isso incluído. 

   ```xml 
   <key>LSApplicationQueriesSchemes</key>
   <array>
      <string>msauth</string>
      <string>msauthv2</string>
   </array>
   ```

### <a name="sign-in-users--request-tokens"></a>Usuários de entrada & tokens de solicitação

A MSAL tem dois métodos que servem para comprar tokens: `acquireToken` e `acquireTokenSilent`.

#### <a name="acquiretoken-getting-a-token-interactively"></a>AcquireToken Obter um token interativamente

Algumas situações exigem que os usuários interajam com a plataforma de identidade da Microsoft. Nesses casos, o usuário final pode ser solicitado a selecionar sua conta, inserir suas credenciais ou consentir as permissões do seu aplicativo. Por exemplo, 

* A primeira vez que os utilizadores iniciam sessão na aplicação
* Se um usuário redefinir sua senha, será necessário inserir suas credenciais 
* Quando seu aplicativo está solicitando acesso a um recurso pela primeira vez
* Quando MFA ou outras políticas de acesso condicional são necessárias

```swift
let parameters = MSALInteractiveTokenParameters(scopes: kScopes)
applicationContext.acquireToken(with: parameters) { (result, error) in /* Add your handling logic */}
```

> |Em que:||
> |---------|---------|
> | `scopes` | Contém os escopos que estão sendo solicitados `[ "user.read" ]` (ou seja `[ "<Application ID URL>/scope" ]` , para Microsoft Graph ou para`api://<Application ID>/access_as_user`APIs Web personalizadas () |

#### <a name="acquiretokensilent-getting-an-access-token-silently"></a>AcquireTokenSilent Obter um token de acesso silenciosamente

Os aplicativos não devem exigir que seus usuários façam logon toda vez que solicitarem um token. Se o usuário já tiver entrado, esse método permitirá que os aplicativos solicitem tokens silenciosamente. 

```swift
let parameters = MSALSilentTokenParameters(scopes: kScopes, account: applicationContext.allAccounts().first)
applicationContext.acquireTokenSilent(with: parameters) { (result, error) in /* Add your handling logic */}
```

> |Em que: ||
> |---------|---------|
> | `scopes` | Contém os escopos que estão sendo solicitados `[ "user.read" ]` (ou seja `[ "<Application ID URL>/scope" ]` , para Microsoft Graph ou para`api://<Application ID>/access_as_user`APIs Web personalizadas () |
> | `account` | A conta para a qual um token está sendo solicitado. Este início rápido é um aplicativo de conta única, se você quiser criar um aplicativo de várias contas, precisará definir a lógica para identificar qual conta usar para solicitações de token`applicationContext.account(forHomeAccountId: self.homeAccountId)` |

## <a name="next-steps"></a>Passos Seguintes

Experimente o tutorial do iOS para obter um guia passo a passo completo sobre a criação de aplicativos, incluindo uma explicação completa deste início rápido.

### <a name="learn-the-steps-to-create-the-application-used-in-this-quickstart"></a>Conheça os passos para criar a aplicação utilizada neste início rápido

> [!div class="nextstepaction"]
> [Chamar tutorial da Graph API iOS](https://docs.microsoft.com/azure/active-directory/develop/guidedsetups/active-directory-ios)

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

Ajude-nos a melhorar a plataforma Microsoft Identity. Diga-nos o que você imagina ao concluir uma pesquisa curta de duas perguntas.

> [!div class="nextstepaction"]
> [Pesquisa sobre plataforma de identidade da Microsoft](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyKrNDMV_xBIiPGgSvnbQZdUQjFIUUFGUE1SMEVFTkdaVU5YT0EyOEtJVi4u)