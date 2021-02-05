---
title: Adquira um token para chamar uma API web (aplicativos móveis) | Rio Azure
titleSuffix: Microsoft identity platform
description: Saiba como construir uma aplicação móvel que chame APIs web. (Obtenha um símbolo para a aplicação.)
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.reviewer: brandwe
ms.custom: aaddev
ms.openlocfilehash: 6e6eda3d711710ea7450165ab02d7a260067bfcb
ms.sourcegitcommit: 2817d7e0ab8d9354338d860de878dd6024e93c66
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/05/2021
ms.locfileid: "99582558"
---
# <a name="get-a-token-for-a-mobile-app-that-calls-web-apis"></a>Obtenha um símbolo para uma aplicação móvel que chama APIs web

Antes que a sua aplicação possa chamar APIs web protegidas, precisa de um token de acesso. Este artigo acompanha-o através do processo para obter um token utilizando a Microsoft Authentication Library (MSAL).

## <a name="define-a-scope"></a>Definir um âmbito

Quando pedir um token, defina um âmbito. O âmbito determina quais os dados a que a sua aplicação pode aceder.

A forma mais fácil de definir um âmbito é combinar as API's web desejadas `App ID URI` com o âmbito `.default` . Esta definição diz à plataforma de identidade da Microsoft que a sua aplicação requer todos os âmbitos definidos no portal.

### <a name="android"></a>Android
```Java
String[] SCOPES = {"https://graph.microsoft.com/.default"};
```

### <a name="ios"></a>iOS
```swift
let scopes = ["https://graph.microsoft.com/.default"]
```

### <a name="xamarin"></a>Xamarin
```csharp
var scopes = new [] {"https://graph.microsoft.com/.default"};
```

## <a name="get-tokens"></a>Obter fichas

### <a name="acquire-tokens-via-msal"></a>Adquirir fichas via MSAL

A MSAL permite que as aplicações adquiram fichas silenciosamente e interativas. Quando telefona `AcquireTokenSilent()` `AcquireTokenInteractive()` ou, a MSAL devolve um sinal de acesso para os âmbitos solicitados. O padrão correto é fazer um pedido silencioso e depois voltar a um pedido interativo.

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
// TODO: Create an interactive callback to catch successful or failed requests.
sampleApp.acquireToken(getActivity(), SCOPES, getAuthInteractiveCallback());
```

#### <a name="ios"></a>iOS

Primeira tentativa de adquirir um símbolo silenciosamente:

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

        // Access token to call the web API
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

    // Access token to call the web API
    let accessToken = authResult.accessToken
}
```

Se a MSAL `MSALErrorInteractionRequired` voltar, tente adquirir fichas interativamente:

```objc
UIViewController *viewController = ...; // Pass a reference to the view controller that should be used when getting a token interactively
MSALWebviewParameters *webParameters = [[MSALWebviewParameters alloc] initWithAuthPresentationViewController:viewController];
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

```swift
let viewController = ... // Pass a reference to the view controller that should be used when getting a token interactively
let webviewParameters = MSALWebviewParameters(authPresentationViewController: viewController)
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

MSAL para iOS e macOS suporta vários modificadores para obter um símbolo interativa ou silenciosamente:
* [Parâmetros comuns para obter um símbolo](https://azuread.github.io/microsoft-authentication-library-for-objc/Classes/MSALTokenParameters.html#/Configuration%20parameters)
* [Parâmetros para obter um token interativo](https://azuread.github.io/microsoft-authentication-library-for-objc/Classes/MSALInteractiveTokenParameters.html#/Configuring%20MSALInteractiveTokenParameters)
* [Parâmetros para obter um símbolo silencioso](https://azuread.github.io/microsoft-authentication-library-for-objc/Classes/MSALSilentTokenParameters.html)

#### <a name="xamarin"></a>Xamarin

O exemplo a seguir mostra o código mínimo para obter um símbolo interativamente. O exemplo utiliza o Microsoft Graph para ler o perfil do utilizador.

```csharp
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

`AcquireTokenInteractive` tem apenas um parâmetro obrigatório: `scopes` . O `scopes` parâmetro enumera as cordas que definem os âmbitos para os quais é necessário um símbolo. Se o token for Microsoft Graph, pode encontrar os âmbitos necessários na referência API de cada API do Microsoft Graph. Na referência, aceda à secção "Permissões".

Por exemplo, para [listar os contactos do utilizador,](/graph/api/user-list-contacts)utilize o âmbito "Utilizador.Ler", "Contactos.Ler". Para obter mais informações, consulte a [referência de permissões do Microsoft Graph](/graph/permissions-reference).

No Android, pode especificar a atividade dos pais quando criar a aplicação `PublicClientApplicationBuilder` utilizando. Se não especificar a atividade dos pais nessa altura, poderá especicioná-la utilizando `.WithParentActivityOrWindow` como na secção seguinte. Se especificar a atividade dos pais, então o símbolo volta à atividade dos pais após a interação. Se não o especificar, a `.ExecuteAsync()` chamada abre uma exceção.

#### <a name="specific-optional-parameters-in-msalnet"></a>Parâmetros opcionais específicos em MSAL.NET

As seguintes secções explicam os parâmetros opcionais em MSAL.NET.

##### <a name="withprompt"></a>ComPrompt

O `WithPrompt()` parâmetro controla a interatividade com o utilizador especificando uma solicitação.

![Imagem mostrando os campos na estrutura Prompt. Estes valores constantes controlam a interatividade com o utilizador definindo o tipo de solicitação visualizada pelo parâmetro WithPrompt().](https://user-images.githubusercontent.com/13203188/53438042-3fb85700-39ff-11e9-9a9e-1ff9874197b3.png)

A classe define as seguintes constantes:

- `SelectAccount` força o serviço de símbolo de segurança (STS) a apresentar a caixa de diálogo de seleção de conta. A caixa de diálogo contém as contas para as quais o utilizador tem uma sessão. Pode utilizar esta opção quando pretender deixar o utilizador escolher entre diferentes identidades. Esta opção leva a MSAL a enviar `prompt=select_account` ao fornecedor de identidade.

    A `SelectAccount` constante é o padrão, e fornece efetivamente a melhor experiência possível com base na informação disponível. A informação disponível pode incluir conta, presença de uma sessão para o utilizador, e assim por diante. Não mude este incumprimento a menos que tenha uma boa razão para fazê-lo.
- `Consent` permite-lhe solicitar o consentimento do utilizador mesmo que o consentimento tenha sido concedido antes. Neste caso, a MSAL envia `prompt=consent` ao fornecedor de identidade.

    É possível que queira utilizar a `Consent` constante em aplicações focadas na segurança, onde a governação da organização exige que os utilizadores vejam a caixa de diálogo de consentimento sempre que utilizam a aplicação.
- `ForceLogin` permite que o serviço indique o utilizador para obter credenciais mesmo que a solicitação não seja necessária.

    Esta opção pode ser útil se a aquisição do token falhar e pretender que o utilizador volte a entrar. Neste caso, a MSAL envia `prompt=login` ao fornecedor de identidade. É possível que queira utilizar esta opção em aplicações focadas na segurança, onde a governação da organização exige que o utilizador assine cada vez que acede a partes específicas da aplicação.
- `Never` é apenas .NET 4.5 e Windows Runtime (WinRT). Esta constante não irá incitar o utilizador, mas tentará usar o cookie que está armazenado na vista web incorporada escondida. Para obter mais informações, consulte [utilizar os navegadores web com MSAL.NET.](./msal-net-web-browsers.md)

    Se esta opção falhar, então `AcquireTokenInteractive` lança uma exceção para notificá-lo de que é necessária uma interação de UI. Em seguida, use outro `Prompt` parâmetro.
- `NoPrompt` não envia um pedido para o fornecedor de identidade.

    Esta opção é útil apenas para políticas de perfil de edição no Azure Ative Directory B2C. Para mais informações, consulte [as especificidades do B2C.](https://aka.ms/msal-net-b2c-specificities)

##### <a name="withextrascopetoconsent"></a>ComExtraScopeToConsent

Utilize o `WithExtraScopeToConsent` modificador num cenário avançado onde pretende que o utilizador forneça o consentimento inicial a vários recursos. Pode utilizar este modificador quando não pretender utilizar o consentimento incremental, que normalmente é utilizado com MSAL.NET ou a plataforma de identidade da Microsoft. Para obter mais informações, consulte [o consentimento do utilizador antecipadamente para vários recursos.](scenario-desktop-production.md#have-the-user-consent-upfront-for-several-resources)

Aqui está um exemplo de código:

```csharp
var result = await app.AcquireTokenInteractive(scopesForCustomerApi)
                     .WithExtraScopeToConsent(scopesForVendorApi)
                     .ExecuteAsync();
```

##### <a name="other-optional-parameters"></a>Outros parâmetros opcionais

Para saber mais sobre os outros parâmetros `AcquireTokenInteractive` opcionais, consulte a [documentação de referência para AcquireTokenInteractiveParameterBuilder](/dotnet/api/microsoft.identity.client.acquiretokeninteractiveparameterbuilder#methods).

### <a name="acquire-tokens-via-the-protocol"></a>Adquirir fichas através do protocolo

Não recomendamos usar diretamente o protocolo para obter fichas. Se o fizer, então a aplicação não suportará alguns cenários que envolvem um único sign-on (SSO), gestão de dispositivos e acesso condicional.

Quando utilizar o protocolo para obter fichas para aplicações móveis, faça dois pedidos:

* Obter um código de autorização.
* Troque o código por um símbolo.

#### <a name="get-an-authorization-code"></a>Obtenha um código de autorização

```
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=<CLIENT_ID>
&response_type=code
&redirect_uri=<ENCODED_REDIRECT_URI>
&response_mode=query
&scope=openid%20offline_access%20https%3A%2F%2Fgraph.microsoft.com%2F.default
&state=12345
```

#### <a name="get-access-and-refresh-the-token"></a>Obtenha acesso e refresque o token

```HTTP
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

Passe para o próximo artigo neste cenário, [Chamando uma API web](scenario-mobile-call-api.md).