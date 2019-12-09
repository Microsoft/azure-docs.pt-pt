---
title: Obter um token para chamar APIs Web em aplicativos móveis | Azure
titleSuffix: Microsoft identity platform
description: Saiba como criar um aplicativo móvel que chama APIs da Web (obtendo um token para o aplicativo)
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.reviwer: brandwe
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1101a1923e094c22e9f6a82853f4dec4deb089a1
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/08/2019
ms.locfileid: "74919924"
---
# <a name="mobile-app-that-calls-web-apis---get-a-token"></a>Aplicativo móvel que chama APIs da Web – obter um token

Antes que você possa começar a chamar APIs da Web protegidas, seu aplicativo precisará de um token de acesso. Este artigo orienta você pelo processo de obtenção de um token usando a MSAL (biblioteca de autenticação da Microsoft).

## <a name="scopes-to-request"></a>Escopos a serem solicitados

Ao solicitar um token, você precisa definir um escopo. O escopo determina quais dados seu aplicativo pode acessar.  

A abordagem mais fácil é combinar a `App ID URI` da API Web desejada com o `.default`de escopo. Isso informa à plataforma de identidade da Microsoft que seu aplicativo requer todos os escopos definidos no Portal.

#### <a name="android"></a>Android
```Java
String[] SCOPES = {"https://graph.microsoft.com/.default"};
```

#### <a name="ios"></a>iOS
```swift
let scopes = ["https://graph.microsoft.com/.default"]
```

#### <a name="xamarin"></a>Xamarin
```CSharp 
var scopes = new [] {"https://graph.microsoft.com/.default"};
```

## <a name="get-tokens"></a>Obter tokens

### <a name="acquire-tokens-via-msal"></a>Adquirir tokens via MSAL

O MSAL permite que os aplicativos adquiram tokens de forma silenciosa e interativa. Basta chamar esses métodos e MSAL retornará um token de acesso para os escopos solicitados. O padrão correto é executar uma solicitação silenciosa e fazer fallback para uma solicitação interativa.

#### <a name="android"></a>Android

```Java
String[] SCOPES = {"https://graph.microsoft.com/.default"};
PublicClientApplication sampleApp = new PublicClientApplication(
                    this.getApplicationContext(),
                    R.raw.auth_config);

// Check if there are any accounts we can sign in silently.
// Result is in the silent callback (success or error).
sampleApp.getAccounts(new PublicClientApplication.AccountsLoadedCallback() {
    @Override
    public void onAccountsLoaded(final List<IAccount> accounts) {

        if (accounts.isEmpty() && accounts.size() == 1) {
            // TODO: Create a silent callback to catch successful or failed request.
            sampleApp.acquireTokenSilentAsync(SCOPES, accounts.get(0), getAuthSilentCallback());
        } else {
            /* No accounts or > 1 account. */
        }
    }
});    

[...]

// No accounts found. Interactively request a token.
// TODO: Create an interactive callback to catch successful or failed request.
sampleApp.acquireToken(getActivity(), SCOPES, getAuthInteractiveCallback());        
```

#### <a name="ios"></a>iOS

**Primeiro, tente adquirir um token silenciosamente:**

Objective-C:

```objc

NSArray *scopes = @[@"https://graph.microsoft.com/.default"];
NSString *accountIdentifier = @"my.account.id";
    
MSALAccount *account = [application accountForIdentifier:accountIdentifier error:nil];
    
MSALSilentTokenParameters *silentParams = [[MSALSilentTokenParameters alloc] initWithScopes:scopes account:account];
[application acquireTokenSilentWithParameters:silentParams completionBlock:^(MSALResult *result, NSError *error) {
        
    if (!error)
    {
        // You'll want to get the account identifier to retrieve and reuse the account
        // for later acquireToken calls
        NSString *accountIdentifier = result.account.identifier;
            
        // Access token to call the Web API
        NSString *accessToken = result.accessToken;
    }
        
    // Check the error
    if (error && [error.domain isEqual:MSALErrorDomain] && error.code == MSALErrorInteractionRequired)
    {
        // Interactive auth will be required, call acquireTokenWithParameters:error:
        return;
    }
}];
```
 
Swift

```swift

let scopes = ["https://graph.microsoft.com/.default"]
let accountIdentifier = "my.account.id"
        
guard let account = try? application.account(forIdentifier: accountIdentifier) else { return }
let silentParameters = MSALSilentTokenParameters(scopes: scopes, account: account)
application.acquireTokenSilent(with: silentParameters) { (result, error) in
            
    guard let authResult = result, error == nil else {
                
    let nsError = error! as NSError
                
    if (nsError.domain == MSALErrorDomain &&
        nsError.code == MSALError.interactionRequired.rawValue) {
                    
            // Interactive auth will be required, call acquireToken()
            return
         }
         return
     }
            
    // You'll want to get the account identifier to retrieve and reuse the account
    // for later acquireToken calls
    let accountIdentifier = authResult.account.identifier
            
    // Access token to call the Web API
    let accessToken = authResult.accessToken
}
```

**Em seguida, se MSAL retornar `MSALErrorInteractionRequired`, tente adquirir tokens interativamente:**

Objective-C:

```objc
UIViewController *viewController = ...; // Pass a reference to the view controller that should be used when getting a token interactively
MSALWebviewParameters *webParameters = [[MSALWebviewParameters alloc] initWithParentViewController:viewController];
MSALInteractiveTokenParameters *interactiveParams = [[MSALInteractiveTokenParameters alloc] initWithScopes:scopes webviewParameters:webParameters];
[application acquireTokenWithParameters:interactiveParams completionBlock:^(MSALResult *result, NSError *error) {
    if (!error) 
    {
        // You'll want to get the account identifier to retrieve and reuse the account
        // for later acquireToken calls
        NSString *accountIdentifier = result.account.identifier;
            
        NSString *accessToken = result.accessToken;
    }
}];
```

Swift

```swift
let viewController = ... // Pass a reference to the view controller that should be used when getting a token interactively
let webviewParameters = MSALWebviewParameters(parentViewController: viewController)
let interactiveParameters = MSALInteractiveTokenParameters(scopes: scopes, webviewParameters: webviewParameters)
application.acquireToken(with: interactiveParameters, completionBlock: { (result, error) in
                
    guard let authResult = result, error == nil else {
        print(error!.localizedDescription)
        return
    }
                
    // Get access token from result
    let accessToken = authResult.accessToken
})
```

O MSAL para iOS e macOS dá suporte a vários modificadores ao obter um token interativamente ou silenciosamente.
* [Parâmetros comuns ao obter um token](https://azuread.github.io/microsoft-authentication-library-for-objc/Classes/MSALTokenParameters.html#/Configuration%20parameters)
* [Parâmetros para aquisição de token interativo](https://azuread.github.io/microsoft-authentication-library-for-objc/Classes/MSALInteractiveTokenParameters.html#/Configuring%20MSALInteractiveTokenParameters)
* [Parâmetros para aquisição de token silenciosa](https://azuread.github.io/microsoft-authentication-library-for-objc/Classes/MSALSilentTokenParameters.html)

#### <a name="xamarin"></a>Xamarin

O exemplo a seguir mostra o código mínimo para obter um token interativamente para ler o perfil do usuário com Microsoft Graph.

```CSharp
string[] scopes = new string[] {"user.read"};
var app = PublicClientApplicationBuilder.Create(clientId).Build();
var accounts = await app.GetAccountsAsync();
AuthenticationResult result;
try
{
 result = await app.AcquireTokenSilent(scopes, accounts.FirstOrDefault())
             .ExecuteAsync();
}
catch(MsalUiRequiredException)
{
 result = await app.AcquireTokenInteractive(scopes)
             .ExecuteAsync();
}
```

#### <a name="mandatory-parameters-in-msalnet"></a>Parâmetros obrigatórios em MSAL.NET

`AcquireTokenInteractive` tem apenas um parâmetro obrigatório ``scopes``, que contém uma enumeração de cadeias de caracteres que definem os escopos para os quais um token é necessário. Se o token for para o Microsoft Graph, os escopos necessários poderão ser encontrados na referência de API de cada API do Microsoft Graph na seção denominada "permissões". Por exemplo, para [listar os contatos do usuário](https://developer.microsoft.com/graph/docs/api-reference/v1.0/api/user_list_contacts), o escopo "User. Read", "Contacts. Read" precisará ser usado. Consulte também [Microsoft Graph referência de permissões](https://developer.microsoft.com/graph/docs/concepts/permissions_reference).

Se você não o tiver especificado ao criar o aplicativo, no Android, também precisará especificar a atividade pai (usando `.WithParentActivityOrWindow`, veja abaixo) para que o token volte para essa atividade pai após a interação. Se você não especificá-lo, uma exceção será lançada ao chamar `.ExecuteAsync()`.

#### <a name="specific-optional-parameters-in-msalnet"></a>Parâmetros opcionais específicos em MSAL.NET

##### <a name="withprompt"></a>WithPrompt

`WithPrompt()` é usado para controlar a interatividade com o usuário especificando um prompt

<img src="https://user-images.githubusercontent.com/13203188/53438042-3fb85700-39ff-11e9-9a9e-1ff9874197b3.png" width="25%" />

A classe define as seguintes constantes:

- ``SelectAccount``: forçará o STS a apresentar a caixa de diálogo de seleção de conta que contém contas para as quais o usuário tem uma sessão. Essa opção é útil quando os desenvolvedores de aplicativos desejam permitir que o usuário escolha entre diferentes identidades. Essa opção conduz MSAL para enviar ``prompt=select_account`` para o provedor de identidade. Essa opção é o padrão e faz um bom trabalho de fornecer a melhor experiência possível com base nas informações disponíveis (conta, presença de uma sessão para o usuário e assim por diante). ...). Não a altere, a menos que você tenha um bom motivo para fazê-lo.
- ``Consent``: permite que o desenvolvedor do aplicativo Force o consentimento do usuário, mesmo que o consentimento tenha sido concedido antes. Nesse caso, o MSAL envia `prompt=consent` para o provedor de identidade. Essa opção pode ser usada em alguns aplicativos focados em segurança em que o controle da organização exige que o usuário tenha apresentado a caixa de diálogo de consentimento sempre que o aplicativo for usado.
- ``ForceLogin``: permite que o desenvolvedor do aplicativo tenha o usuário solicitado a fornecer credenciais pelo serviço, mesmo que esse prompt de usuário não seja necessário. Essa opção pode ser útil se a aquisição de um token falhar, para permitir que o usuário entre novamente. Nesse caso, o MSAL envia `prompt=login` para o provedor de identidade. Mais uma vez, vimos que ele é usado em alguns aplicativos focados em segurança em que o controle da organização exige que o usuário faça o logon novamente, sempre que acessarem partes específicas de um aplicativo.
- ``Never`` (somente para .NET 4,5 e WinRT) não solicitará o usuário, mas tentará usar o cookie armazenado na exibição da Web oculta inserida (veja abaixo: exibições da Web no MSAL.NET). O uso dessa opção pode falhar e, nesse caso, `AcquireTokenInteractive` gerará uma exceção para notificar que uma interação de interface do usuário é necessária e você precisará usar outro parâmetro `Prompt`.
- ``NoPrompt``: não enviará nenhum prompt para o provedor de identidade. Essa opção só é útil para Azure AD B2C editar políticas de perfil (consulte as [especificações do B2C](https://aka.ms/msal-net-b2c-specificities)).

##### <a name="withextrascopetoconsent"></a>WithExtraScopeToConsent

Esse modificador é usado em um cenário avançado em que você deseja que o usuário concorde com vários recursos antecipadamente (e não queira usar o consentimento incremental, que normalmente é usado com MSAL.NET/a plataforma de identidade da Microsoft v 2.0). Para obter detalhes [, consulte Como: ter o consentimento de usuário antecipado para vários recursos](scenario-desktop-production.md#how-to-have--the-user-consent-upfront-for-several-resources).

```CSharp
var result = await app.AcquireTokenInteractive(scopesForCustomerApi)
                     .WithExtraScopeToConsent(scopesForVendorApi)
                     .ExecuteAsync();
```

##### <a name="other-optional-parameters"></a>Outros parâmetros opcionais

Saiba mais sobre todos os outros parâmetros opcionais para `AcquireTokenInteractive` da documentação de referência para [AcquireTokenInteractiveParameterBuilder](/dotnet/api/microsoft.identity.client.acquiretokeninteractiveparameterbuilder?view=azure-dotnet-preview#methods)

### <a name="acquire-tokens-via-the-protocol"></a>Adquirir tokens por meio do protocolo

Não é recomendável usar o protocolo diretamente. Se você fizer isso, o aplicativo não dará suporte a alguns cenários de logon único (SSO), gerenciamento de dispositivos e acesso condicional.

Ao usar o protocolo para obter tokens para aplicativos móveis, você precisa fazer duas solicitações: Obtenha um código de autorização e troque-o por um token.

#### <a name="get-authorization-code"></a>Obter código de autorização

```Text
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=<CLIENT_ID>
&response_type=code
&redirect_uri=<ENCODED_REDIRECT_URI>
&response_mode=query
&scope=openid%20offline_access%20https%3A%2F%2Fgraph.microsoft.com%2F.default
&state=12345
```

#### <a name="get-access-and-refresh-token"></a>Obter acesso e atualizar token

```Text
POST /{tenant}/oauth2/v2.0/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=<CLIENT_ID>
&scope=https%3A%2F%2Fgraph.microsoft.com%2F.default
&code=OAAABAAAAiL9Kn2Z27UubvWFPbm0gLWQJVzCTE9UkP3pSx1aXxUjq3n8b2JRLk4OxVXr...
&redirect_uri=<ENCODED_REDIRECT_URI>
&grant_type=authorization_code
```

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Chamando uma API Web](scenario-mobile-call-api.md)
