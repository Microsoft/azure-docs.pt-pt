---
title: Guia de introdução do Microsoft identity plataforma iOS | Azure
description: Saiba como início de sessão aos utilizadores e de consulta Microsoft Graph numa aplicação iOS.
services: active-directory
documentationcenter: dev-center-name
author: danieldobalian
manager: CelesteDG
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/18/2019
ms.author: brandwe
ms.reviewer: jmprieur, saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: b3802d8f92913e416cc6a80f899179fde80cec30
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/21/2019
ms.locfileid: "65962586"
---
# <a name="quickstart-sign-in-users-and-call-the-microsoft-graph-api-from-an-ios-app"></a>Início rápido: Iniciar sessão dos utilizadores e chamar a API do Microsoft Graph a partir de uma aplicação iOS

[!INCLUDE [active-directory-develop-applies-v2-msal](../../../includes/active-directory-develop-applies-v2-msal.md)]

Este início rápido contém um exemplo de código que demonstra como uma aplicação iOS nativa pode iniciar sessão em contas pessoais ou contas profissionais e escolares, obter um token de acesso e chamar a Microsoft Graph API.

![Mostra como funciona a aplicação de exemplo gerada por este início rápido](media/quickstart-v2-ios/ios-intro.svg)

> [!NOTE]
> **Pré-requisitos**
> * XCode 10+
> * iOS 10+ 

> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-app"></a>Registar e transferir a aplicação do início rápido
> Tem duas opções para iniciar a aplicação de início rápido:
> * [Express] [Opção 1: Registre-se e automática configurar a sua aplicação e, em seguida, transferir o exemplo de código](#option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample)
> * [Manual] [Opção 2: Registar e configurar manualmente o seu aplicativo e o código de exemplo](#option-2-register-and-manually-configure-your-application-and-code-sample)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Opção 1: Registre-se e automática configurar a sua aplicação e, em seguida, transferir o exemplo de código
> #### <a name="step-1-register-your-application"></a>Passo 1: Registar a sua aplicação
> Para registar a sua aplicação
> 1. Vá para a nova [portal do Azure – registos de aplicações](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/IosQuickstartPage/sourceType/docs) painel.
> 1. Introduza um nome para a sua aplicação e xelecione **Registar**.
> 1. Siga as instruções para transferir e configurar automaticamente a sua nova aplicação com um só clique.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>Opção 2: Registar e configurar manualmente o seu aplicativo e o código de exemplo
>
> #### <a name="step-1-register-your-application"></a>Passo 1: Registar a sua aplicação
> Para registar a sua aplicação e adicionar as informações de registo da aplicação à sua solução manualmente, siga os passos a seguir:
>
> 1. Navegue para a plataforma de identidade da Microsoft para desenvolvedores [registos das aplicações](https://aka.ms/MobileAppReg) página.
> 1. Selecione **novo registo**.
> 1. Quando a página **Registar uma aplicação** for apresentada, introduza as informações de registo da aplicação:
>      - Na **Name** secção, introduza um nome de aplicativo significativo que será apresentado aos utilizadores da aplicação quando iniciar sessão ou dar consentimento à sua aplicação, por exemplo `iOSQuickstart`.
>      - Ignore outras configurações desta página. 
>      - Pressionar o `Register` botão.
> 1. Clique na nova aplicação > aceda a `Authentication`  >  `Add Platform`  >  `iOS`.    
>      - Introduza o ***identificador de pacote*** para a sua aplicação. 
> 1. Selecione `Configure` e guarde o ***configuração de MSAL*** detalhes para utilizar mais tarde. 

> [!div renderon="portal" class="sxs-lookup"]
>
> #### <a name="step-1-configure-your-application"></a>Passo 1: Configurar a aplicação
> Para o código de exemplo para este início rápido funcionar, terá de adicionar um compatível com o Mediador de autenticação URI de redirecionamento. 
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Fazer esta alteração por mim]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Já configurada](media/quickstart-v2-ios/green-check.png) A sua aplicação está configurada com estes atributos

#### <a name="step-2-download-your-web-server-or-project"></a>Passo 2: Transfira o seu servidor web ou projeto

- [Baixar o código de exemplo](https://github.com/Azure-Samples/active-directory-ios-swift-native-v2/archive/master.zip)

#### <a name="step-3-configure-your-project"></a>Passo 3: Configurar o seu projeto

> [!div renderon="docs"]
> Se tiver selecionado a opção 1 acima, pode ignorar estes passos. 

> [!div renderon="portal" class="sxs-lookup"]
> 1. Extraia o ficheiro zip e abra o projeto no XCode.
> 1. Edite **ViewController.swift** e substitua a linha que começa com "let kClientID" pelo seguinte fragmento de código:
>    ```swift
>    let kClientID = "Enter_the_Application_Id_here"
>    let kAuthority = "https://login.microsoftonline.com/Enter_the_Tenant_Info_Here"
>
>    ```
> 1. Clique com botão direito **Info. plist** e selecione **aberto como** > **código-fonte**.
> 1. No nó de raiz dict, substitua pelo seu ***Id do pacote***:
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
> 1. Criar e executar a aplicação! 

> [!div renderon="docs"]
>
> 1. Extraia o ficheiro zip e abra o projeto no XCode.
> 1. Editar **viewcontroller. SWIFT** e substitua a linha que começa com "permitir que kClientID' com o seguinte fragmento de código:
>
>    ```swift
>    let kClientID = "<ENTER_YOUR_APPLICATION/CLIENT_ID>"
> 
>    ```
> 1. Clique com botão direito **Info. plist** e selecione **aberto como** > **código-fonte**.
> 1. No nó de raiz dict, substitua pelo seu ***Id do pacote***:
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
> 1. Criar e executar a aplicação! 

## <a name="more-information"></a>Mais Informações

Leia estas secções para saber mais sobre este início rápido.

### <a name="getting-msal"></a>Introdução a MSAL

A MSAL ([MSAL.framework](https://github.com/AzureAD/microsoft-authentication-library-for-objc)) é a biblioteca utilizada para iniciar sessão dos utilizadores e solicitar tokens utilizados para aceder a uma API protegida pela plataforma de identidade da Microsoft. Pode adicionar a MSAL à sua aplicação através do seguinte processo:

```
$ vi Podfile

```
Adicione o seguinte a este podfile (com de destino seu projeto):

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
> | `authority` | O Microsoft identity platform ponto final. Na maioria dos casos será *https<span/>: //login.microsoftonline.com/common* |
> | `redirectUri` | O URI da aplicação de redirecionamento. Pode passar 'hodnotu nil"para utilizar o valor predefinido ou o seu URI de redirecionamento personalizado. |

### <a name="additional-app-requirements"></a>Requisitos de aplicações adicionais  

A aplicação também tem de ter o seguinte seu `AppDelegate`. Isso permite manipular a resposta de token a partir da aplicação de Mediador de autenticação, quando efetuar a autenticação de SDK de MSAL.

 ```swift
 func application(_ app: UIApplication, open url: URL, options: [UIApplication.OpenURLOptionsKey : Any] = [:]) -> Bool {
         guard let sourceApplication = options[UIApplication.OpenURLOptionsKey.sourceApplication] as? String else {
             return false
         }
         
         return MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: sourceApplication)
     }

```

Por fim, a aplicação tem tem um `LSApplicationQueriesSchemes` entrada no seu ***Info. plist*** juntamente com o `CFBundleURLTypes`. O exemplo é fornecido com este incluídos. 

   ```xml 
   <key>LSApplicationQueriesSchemes</key>
   <array>
      <string>msauth</string>
      <string>msauthv2</string>
   </array>
   ```

### <a name="sign-in-users--request-tokens"></a>Iniciar sessão dos utilizadores & solicitar tokens

A MSAL tem dois métodos que servem para comprar tokens: `acquireToken` e `acquireTokenSilent`.

#### <a name="acquiretoken-getting-a-token-interactively"></a>acquireToken: Obter um token de forma interativa

Algumas situações exijam os utilizadores interajam com a plataforma de identidade da Microsoft. Nestes casos, o usuário final poderá ser necessário para selecionar a conta, introduza as credenciais ou dar consentimento a permissões da sua aplicação. Por exemplo, 

* A primeira vez que os utilizadores iniciam sessão na aplicação
* Se um utilizador repõe a palavra-passe, terão de introduzir as respetivas credenciais 
* Quando a aplicação está a pedir o acesso a um recurso pela primeira vez
* Quando são necessárias o MFA ou outras políticas de acesso condicional

```swift
let parameters = MSALInteractiveTokenParameters(scopes: kScopes)
applicationContext.acquireToken(with: parameters) { (result, error) in /* Add your handling logic */}
```

> |Em que:||
> |---------|---------|
> | `scopes` | Contém os âmbitos que está a ser solicitados (ou seja, `[ "user.read" ]` para o Microsoft Graph ou `[ "<Application ID URL>/scope" ]` para as APIs da Web personalizado (`api://<Application ID>/access_as_user`) |

#### <a name="acquiretokensilent-getting-an-access-token-silently"></a>acquireTokenSilent: Obter um token de acesso silenciosamente

As aplicações não devem exigir aos utilizadores iniciar sessão sempre que eles solicitar um token. Se o utilizador já tiver iniciado sessão, este método permite que as aplicações pedir tokens silenciosamente. 

```swift
let parameters = MSALSilentTokenParameters(scopes: kScopes, account: applicationContext.allAccounts().first)
applicationContext.acquireTokenSilent(with: parameters) { (result, error) in /* Add your handling logic */}
```

> |Em que: ||
> |---------|---------|
> | `scopes` | Contém os âmbitos que está a ser solicitados (ou seja, `[ "user.read" ]` para o Microsoft Graph ou `[ "<Application ID URL>/scope" ]` para as APIs da Web personalizado (`api://<Application ID>/access_as_user`) |
> | `account` | A conta de um token está a ser requerida para. Este início rápido é um aplicativo de conta única, se quiser criar uma aplicação de várias conta, que precisará definir uma lógica para identificar a conta a utilizar para pedidos de token `applicationContext.account(forHomeAccountId: self.homeAccountId)` |

## <a name="next-steps"></a>Passos Seguintes

Experimente o tutorial de iOS para obter um guia passo a passo completo sobre a criação de aplicativos, incluindo uma explicação completa deste início rápido.

### <a name="learn-the-steps-to-create-the-application-used-in-this-quickstart"></a>Conheça os passos para criar a aplicação utilizada neste início rápido

> [!div class="nextstepaction"]
> [Chamar tutorial da Graph API iOS](https://docs.microsoft.com/azure/active-directory/develop/guidedsetups/active-directory-ios)

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]