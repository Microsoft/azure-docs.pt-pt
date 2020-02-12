---
title: Adquirir um símbolo para chamar uma API web (aplicações móveis) / Azure
titleSuffix: Microsoft identity platform
description: Aprenda a construir uma aplicação móvel que chama APIs web. (Obter um símbolo para a aplicação.)
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
ms.openlocfilehash: 9427235f47a31da75426559a4285634ab2837577
ms.sourcegitcommit: f718b98dfe37fc6599d3a2de3d70c168e29d5156
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/11/2020
ms.locfileid: "77132451"
---
# <a name="get-a-token-for-a-mobile-app-that-calls-web-apis"></a>Obtenha um símbolo para uma aplicação móvel que chama APIs web

Antes que a sua aplicação possa ligar para APIs protegidos da web, precisa de um sinal de acesso. Este artigo percorre-o através do processo para obter um símbolo utilizando a Microsoft Authentication Library (MSAL).

## <a name="define-a-scope"></a>Definir um âmbito

Quando pede um símbolo, precisa definir um âmbito. O âmbito determina quais os dados a que a sua aplicação pode aceder.  

A maneira mais fácil de definir um âmbito é combinar o `App ID URI` da Web API com o âmbito `.default`. Esta definição diz à plataforma de identidade da Microsoft que a sua aplicação requer todos os âmbitos definidos no portal.

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

A MSAL permite que as aplicações adquiram fichas silenciosamente e interativamente. Quando liga para `AcquireTokenSilent()` ou `AcquireTokenInteractive()`, a MSAL devolve um sinal de acesso para os âmbitos solicitados. O padrão correto é fazer um pedido silencioso e depois voltar a um pedido interativo.

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

Primeiro tente adquirir um símbolo silenciosamente:

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

Se a MSAL devolver `MSALErrorInteractionRequired`, tente adquirir fichas interativamente:

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

A MSAL para iOS e macOS suporta vários modificadores para obter um símbolo interativa ou silenciosamente:
* [Parâmetros comuns para obter um símbolo](https://azuread.github.io/microsoft-authentication-library-for-objc/Classes/MSALTokenParameters.html#/Configuration%20parameters)
* [Parâmetros para obter um símbolo interativo](https://azuread.github.io/microsoft-authentication-library-for-objc/Classes/MSALInteractiveTokenParameters.html#/Configuring%20MSALInteractiveTokenParameters)
* [Parâmetros para obter um símbolo silencioso](https://azuread.github.io/microsoft-authentication-library-for-objc/Classes/MSALSilentTokenParameters.html)

#### <a name="xamarin"></a>Xamarin

O exemplo que se segue mostra o código mínimo para obter um símbolo interactivamente. O exemplo utiliza o Microsoft Graph para ler o perfil do utilizador.

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

`AcquireTokenInteractive` tem apenas um parâmetro obrigatório: `scopes`. O parâmetro `scopes` enumera cordas que definem os âmbitos para os quais é necessário um símbolo. Se o símbolo for para o Microsoft Graph, pode encontrar os âmbitos necessários na referência API de cada API do Microsoft Graph. Na referência, vá à secção "Permissões". 

Por exemplo, para [listar os contactos do utilizador,](https://developer.microsoft.com/graph/docs/api-reference/v1.0/api/user_list_contacts)utilize o âmbito "User.Read", "Contactos.Read". Para mais informações, consulte a referência de [permissões do Microsoft Graph](https://developer.microsoft.com/graph/docs/concepts/permissions_reference).

No Android, pode especificar a atividade dos pais quando criar a aplicação utilizando `PublicClientApplicationBuilder`. Se não especificar a atividade dos pais nessa altura, poderá especificá-la utilizando `.WithParentActivityOrWindow` como na seguinte secção. Se especificar a atividade dos pais, o símbolo volta à atividade dos pais após a interação. Se não especificar, a chamada `.ExecuteAsync()` abre uma exceção.

#### <a name="specific-optional-parameters-in-msalnet"></a>Parâmetros opcionais específicos em MSAL.NET

As seguintes secções explicam os parâmetros opcionais em MSAL.NET. 

##### <a name="withprompt"></a>ComPrompt

O parâmetro `WithPrompt()` controla a interatividade com o utilizador especificando um aviso.

<img src="https://user-images.githubusercontent.com/13203188/53438042-3fb85700-39ff-11e9-9a9e-1ff9874197b3.png" width="25%" />

A classe define as seguintes constantes:

- `SelectAccount` obriga o serviço de fichas de segurança (STS) a apresentar a caixa de diálogo de seleção de contas. A caixa de diálogo contém as contas para as quais o utilizador tem uma sessão. Pode utilizar esta opção quando pretender deixar o utilizador escolher entre diferentes identidades. Esta opção leva a MSAL a enviar `prompt=select_account` ao fornecedor de identidade. 
    
    A `SelectAccount` constante é o padrão, e fornece efetivamente a melhor experiência possível com base na informação disponível. As informações disponíveis podem incluir conta, presença de uma sessão para o utilizador, e assim por diante. Não mude este padrão a menos que tenha uma boa razão para fazê-lo.
- `Consent` permite-lhe pedir consentimento ao utilizador mesmo que o consentimento tenha sido concedido antes. Neste caso, a MSAL envia `prompt=consent` ao fornecedor de identidade. 

    É possível que queira utilizar o `Consent` constante em aplicações focadas na segurança, onde a governação da organização exige que os utilizadores vejam a caixa de diálogo de consentimento sempre que utilizam a aplicação.
- `ForceLogin` permite que o serviço indique credenciais ao utilizador, mesmo que a solicitação não seja necessária. 

    Esta opção pode ser útil se a aquisição do token falhar e pretender que o utilizador volte a fazer o seu início. Neste caso, a MSAL envia `prompt=login` ao fornecedor de identidade. É possível que pretenda utilizar esta opção em aplicações centradas na segurança, onde a governação da organização exige que o utilizador assine cada vez que acede a partes específicas da aplicação.
- `Never` é para apenas .NET 4.5 e Windows Runtime (WinRT). Esta constante não vai levar o utilizador, mas tentará usar o cookie que está armazenado na vista web incorporada escondida. Para mais informações, consulte [A utilização de navegadores web com MSAL.NET](https://docs.microsoft.com/azure/active-directory/develop/msal-net-web-browsers).

    Se esta opção falhar, `AcquireTokenInteractive` abre uma exceção para notificá-lo de que é necessária uma interação ui. Então tens de usar outro parâmetro `Prompt`.
- `NoPrompt` não envia um pedido ao fornecedor de identidade. 

    Esta opção é útil apenas para políticas de perfil de edição no Azure Ative Directory B2C. Para mais informações, consulte [as especificidades do B2C](https://aka.ms/msal-net-b2c-specificities).

##### <a name="withextrascopetoconsent"></a>WithExtraScopeToConsent

Utilize o modificador `WithExtraScopeToConsent` num cenário avançado onde pretenda que o utilizador forneça o consentimento antecipado a vários recursos. Pode utilizar este modificador quando não quiser utilizar o consentimento incremental, que normalmente é utilizado com MSAL.NET ou plataforma de identidade microsoft 2.0. Para mais informações, consulte [Ter o consentimento do utilizador antecipadamente para vários recursos](scenario-desktop-production.md#have-the-user-consent-upfront-for-several-resources).

Aqui está um exemplo de código: 

```csharp
var result = await app.AcquireTokenInteractive(scopesForCustomerApi)
                     .WithExtraScopeToConsent(scopesForVendorApi)
                     .ExecuteAsync();
```

##### <a name="other-optional-parameters"></a>Outros parâmetros opcionais

Para conhecer os outros parâmetros opcionais para `AcquireTokenInteractive`, consulte a [documentação de referência para AcquireTokenInteractiveParameterBuilder](/dotnet/api/microsoft.identity.client.acquiretokeninteractiveparameterbuilder?view=azure-dotnet-preview#methods).

### <a name="acquire-tokens-via-the-protocol"></a>Adquirir fichas através do protocolo

Não recomendamos usar diretamente o protocolo para obter fichas. Se o fizer, a aplicação não suportará alguns cenários que envolvam um único sinal de inscrição (SSO), gestão de dispositivos e acesso condicional.

Quando utilizar o protocolo para obter fichas para aplicações móveis, faça dois pedidos: 

* Arranja um código de autorização.
* Troque o código por um símbolo.

#### <a name="get-an-authorization-code"></a>Obter um código de autorização

```Text
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=<CLIENT_ID>
&response_type=code
&redirect_uri=<ENCODED_REDIRECT_URI>
&response_mode=query
&scope=openid%20offline_access%20https%3A%2F%2Fgraph.microsoft.com%2F.default
&state=12345
```

#### <a name="get-access-and-refresh-the-token"></a>Tenha acesso e refresque o símbolo

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
> [Chamando uma API web](scenario-mobile-call-api.md)
