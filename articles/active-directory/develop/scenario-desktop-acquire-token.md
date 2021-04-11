---
title: Adquira um token para chamar uma API web (app de desktop) | Rio Azure
titleSuffix: Microsoft identity platform
description: Saiba como construir uma aplicação de desktop que chama APIs web para adquirir um símbolo para a app
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 01/06/2021
ms.author: jmprieur
ms.custom: aaddev, devx-track-python
ms.openlocfilehash: c63ee686ae218a696069465bb8d2d1d7413a998e
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104799093"
---
# <a name="desktop-app-that-calls-web-apis-acquire-a-token"></a>Aplicativo de desktop que chama APIs web: Adquirir um token

Depois de ter construído um caso da aplicação de cliente público, irá usá-la para adquirir um símbolo que usará para chamar uma API web.

## <a name="recommended-pattern"></a>Padrão recomendado

A API web é definida pela sua `scopes` . Qualquer que seja a experiência que proporciona na sua aplicação, o padrão a utilizar é:

- Tenta sistematicamente obter um símbolo da cache simbólica chamando `AcquireTokenSilent` .
- Se esta chamada falhar, utilize o `AcquireToken` fluxo que pretende utilizar, que é aqui representado por `AcquireTokenXX` .

# <a name="net"></a>[.NET](#tab/dotnet)

### <a name="in-msalnet"></a>Em MSAL.NET

```csharp
AuthenticationResult result;
var accounts = await app.GetAccountsAsync();
IAccount account = ChooseAccount(accounts); // for instance accounts.FirstOrDefault
                                            // if the app manages is at most one account
try
{
 result = await app.AcquireTokenSilent(scopes, account)
                   .ExecuteAsync();
}
catch(MsalUiRequiredException ex)
{
  result = await app.AcquireTokenXX(scopes, account)
                    .WithOptionalParameterXXX(parameter)
                    .ExecuteAsync();
}
```

# <a name="java"></a>[Java](#tab/java)

```java

Set<IAccount> accountsInCache = pca.getAccounts().join();
// Take first account in the cache. In a production application, you would filter
// accountsInCache to get the right account for the user authenticating.
IAccount account = accountsInCache.iterator().next();

IAuthenticationResult result;
try {
    SilentParameters silentParameters =
            SilentParameters
                    .builder(SCOPE, account)
                    .build();

    // try to acquire token silently. This call will fail since the token cache
    // does not have any data for the user you are trying to acquire a token for
    result = pca.acquireTokenSilently(silentParameters).join();
} catch (Exception ex) {
    if (ex.getCause() instanceof MsalException) {

        InteractiveRequestParameters parameters = InteractiveRequestParameters
                .builder(new URI("http://localhost"))
                .scopes(SCOPE)
                .build();

        // Try to acquire a token interactively with system browser. If successful, you should see
        // the token and account information printed out to console
        result = pca.acquireToken(parameters).join();
    } else {
        // Handle other exceptions accordingly
        throw ex;
    }
}
return result;

```

# <a name="macos"></a>[macOS](#tab/macOS)

### <a name="in-msal-for-ios-and-macos"></a>No MSAL para iOS e macOS

Objetivo C:

```objc
MSALAccount *account = [application accountForIdentifier:accountIdentifier error:nil];

MSALSilentTokenParameters *silentParams = [[MSALSilentTokenParameters alloc] initWithScopes:scopes account:account];
[application acquireTokenSilentWithParameters:silentParams completionBlock:^(MSALResult *result, NSError *error) {

    // Check the error
    if (error && [error.domain isEqual:MSALErrorDomain] && error.code == MSALErrorInteractionRequired)
    {
        // Interactive auth will be required, call acquireTokenWithParameters:error:
    }
}];
```
Rápido:

```swift
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
}
```

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

No nó MSAL, você adquire fichas através do fluxo de código de autorização com chave de prova para troca de códigos (PKCE). O Nó MSAL utiliza uma cache de ficha na memória para ver se existem contas de utilizador na cache. Se houver, o objeto da conta pode ser passado para o `acquireTokenSilent()` método para recuperar um token de acesso em cache.

```JavaScript

const msal = require("@azure/msal-node");

const msalConfig = {
    auth: {
        clientId: "your_client_id_here",
        authority: "your_authority_here",
    }
};

const pca = new msal.PublicClientApplication(msalConfig);
const msalTokenCache = pca.getTokenCache();

let accounts = await msalTokenCache.getAllAccounts();

    if (accounts.length > 0) {

        const silentRequest = {
            account: accounts[0], // Index must match the account that is trying to acquire token silently
            scopes: ["user.read"],
        };
    
        pca.acquireTokenSilent(silentRequest).then((response) => {
            console.log("\nSuccessful silent token acquisition");
            console.log("\nResponse: \n:", response);
            res.sendStatus(200);
        }).catch((error) => console.log(error));
    } else {
        const {verifier, challenge} = await msal.cryptoProvider.generatePkceCodes();

        const authCodeUrlParameters = {
            scopes: ["User.Read"],
            redirectUri: "your_redirect_uri",
            codeChallenge: challenge, // PKCE Code Challenge
            codeChallengeMethod: "S256" // PKCE Code Challenge Method 
        };
        
        // get url to sign user in and consent to scopes needed for application
        pca.getAuthCodeUrl(authCodeUrlParameters).then((response) => {
            console.log(response);
        
            const tokenRequest = {
                code: response["authorization_code"],
                codeVerifier: verifier // PKCE Code Verifier 
                redirectUri: "your_redirect_uri",
                scopes: ["User.Read"],
            };
            
            // acquire a token by exchanging the code
            pca.acquireTokenByCode(tokenRequest).then((response) => {
                console.log("\nResponse: \n:", response);
            }).catch((error) => {
                console.log(error);
            });
        }).catch((error) => console.log(JSON.stringify(error)));
    }
```

# <a name="python"></a>[Python](#tab/python)

```Python
result = None

# Firstly, check the cache to see if this end user has signed in before
accounts = app.get_accounts(username=config["username"])
if accounts:
    result = app.acquire_token_silent(config["scope"], account=accounts[0])

if not result:
    result = app.acquire_token_by_xxx(scopes=config["scope"])
```
---

Aqui estão as várias formas de adquirir fichas numa aplicação para desktop.

## <a name="acquire-a-token-interactively"></a>Adquira um símbolo interativamente

O exemplo a seguir mostra um código mínimo para obter um símbolo interativamente para ler o perfil do utilizador com o Microsoft Graph.

# <a name="net"></a>[.NET](#tab/dotnet)

### <a name="in-msalnet"></a>Em MSAL.NET

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

### <a name="mandatory-parameters"></a>Parâmetros obrigatórios

`AcquireTokenInteractive` tem apenas um parâmetro obrigatório, ``scopes`` que contém uma enumeração de cordas que definem os âmbitos para os quais é necessário um símbolo. Se o token for Microsoft Graph, os âmbitos necessários podem ser encontrados na referência API de cada API do Microsoft na secção denominada "Permissões". Por exemplo, para [listar os contactos do utilizador,](/graph/api/user-list-contacts)deve ser utilizado o âmbito "User.Read", "Contactos.Read". Para obter mais informações, consulte a [referência de permissões do Microsoft Graph](/graph/permissions-reference).

No Android, também é necessário especificar a atividade dos pais utilizando `.WithParentActivityOrWindow` , como mostra, para que o token volte à atividade dos pais após a interação. Se não o especificar, é lançada uma exceção ao `.ExecuteAsync()` ligar.

### <a name="specific-optional-parameters-in-msalnet"></a>Parâmetros opcionais específicos em MSAL.NET

#### <a name="withparentactivityorwindow"></a>ComParentActivityOrWindow

A UI é importante porque é interativa. `AcquireTokenInteractive` tem um parâmetro opcional específico que pode especificar, para plataformas que o suportam, a UI principal. Quando usado numa aplicação de desktop, `.WithParentActivityOrWindow` tem um tipo diferente, que depende da plataforma. Em alternativa, pode omitir o parâmetro opcional da janela dos pais para criar uma janela, se não quiser controlar onde o diálogo de entrada aparece no ecrã. Isto seria aplicável para aplicações baseadas em linha de comando, usadas para passar chamadas para qualquer outro serviço de backend e não precisam de janelas para a interação do utilizador.

```csharp
// net45
WithParentActivityOrWindow(IntPtr windowPtr)
WithParentActivityOrWindow(IWin32Window window)

// Mac
WithParentActivityOrWindow(NSWindow window)

// .Net Standard (this will be on all platforms at runtime, but only on NetStandard at build time)
WithParentActivityOrWindow(object parent).
```

Observações:

- No .NET Standard, o esperado `object` é `Activity` para Android, `UIViewController` iOS, `NSWindow` MAC e `IWin32Window` `IntPr` Windows.
- No Windows, tem de ligar `AcquireTokenInteractive` a partir da linha UI para que o navegador incorporado obtenha o contexto de sincronização de UI apropriado. Não ligar da linha UI pode fazer com que as mensagens não bombeiem corretamente e os cenários de impasse com a UI. Uma forma de ligar para as Bibliotecas de Autenticação da Microsoft (MSALs) a partir da linha UI se ainda não estiver na linha UI é utilizar o `Dispatcher` no WPF.
- Se estiver a usar o WPF, para obter uma janela de um controlo WPF, pode usar a `WindowInteropHelper.Handle` aula. Em seguida, a chamada é de um controlo WPF `this` ():

  ```csharp
  result = await app.AcquireTokenInteractive(scopes)
                    .WithParentActivityOrWindow(new WindowInteropHelper(this).Handle)
                    .ExecuteAsync();
  ```

#### <a name="withprompt"></a>ComPrompt

`WithPrompt()` é utilizado para controlar a interatividade com o utilizador especificando uma solicitação.

![Imagem mostrando os campos na estrutura Prompt. Estes valores constantes controlam a interatividade com o utilizador definindo o tipo de solicitação visualizada pelo método WithPrompt().](https://user-images.githubusercontent.com/13203188/53438042-3fb85700-39ff-11e9-9a9e-1ff9874197b3.png)

A classe define as seguintes constantes:

- ``SelectAccount`` força o STS a apresentar a caixa de diálogo de seleção de conta que contém contas para as quais o utilizador tem uma sessão. Esta opção é útil quando os desenvolvedores de aplicações pretendem permitir que os utilizadores escolham entre diferentes identidades. Esta opção leva a MSAL a enviar ``prompt=select_account`` ao fornecedor de identidade. Esta é a opção predefinida. Faz um bom trabalho em fornecer a melhor experiência possível com base na informação disponível, como a conta e a presença de uma sessão para o utilizador. Não o mude a menos que tenha uma boa razão para fazê-lo.
- ``Consent`` permite que o desenvolvedor de aplicações force o utilizador a ser solicitado para o consentimento, mesmo que o consentimento tenha sido concedido antes. Neste caso, a MSAL envia `prompt=consent` ao fornecedor de identidade. Esta opção pode ser utilizada em algumas aplicações focadas na segurança, onde a governação da organização exige que o utilizador seja apresentado com a caixa de diálogo de consentimento sempre que a aplicação é utilizada.
- ``ForceLogin`` permite que o desenvolvedor de aplicações tenha o utilizador solicitado para credenciais pelo serviço, mesmo que esta solicitação do utilizador possa não ser necessária. Esta opção pode ser útil para permitir que o utilizador volte a entrar se adquirir um símbolo falhar. Neste caso, a MSAL envia `prompt=login` ao fornecedor de identidade. Por vezes é usado em aplicações focadas na segurança, onde a governação da organização exige que o utilizador reassente em cada vez que acede a partes específicas de uma aplicação.
- ``Never`` (apenas para .NET 4.5 e WinRT) não vai solicitar o utilizador, mas tenta utilizar o cookie armazenado na vista web incorporada escondida. Para mais informações, consulte as vistas da web em MSAL.NET. Usar esta opção pode falhar. Nesse caso, `AcquireTokenInteractive` abre uma exceção para notificar que é necessária uma interação com a UI. Tens de usar outro `Prompt` parâmetro.
- ``NoPrompt`` não enviará qualquer aviso ao fornecedor de identidade. Esta opção é útil apenas para as políticas de perfil de edição de Edição Azure Ative (Azure AD) B2C. Para mais informações, consulte [as especificidades do Azure AD B2C](https://aka.ms/msal-net-b2c-specificities).

#### <a name="withextrascopetoconsent"></a>ComExtraScopeToConsent

Este modificador é utilizado num cenário avançado em que pretende que o utilizador pré-consentir com vários recursos antecipadamente, e não pretende utilizar o consentimento incremental, que normalmente é utilizado com MSAL.NET/the plataforma de identidade da Microsoft. Para obter mais informações, consulte [o consentimento do utilizador antecipadamente para vários recursos.](scenario-desktop-production.md#have-the-user-consent-upfront-for-several-resources)

```csharp
var result = await app.AcquireTokenInteractive(scopesForCustomerApi)
                     .WithExtraScopeToConsent(scopesForVendorApi)
                     .ExecuteAsync();
```

#### <a name="withcustomwebui"></a>Com oCustomWebUi

Uma UI web é um mecanismo para invocar um navegador. Este mecanismo pode ser um controlo dedicado do UI WebBrowser ou uma forma de delegar a abertura do navegador.
A MSAL fornece implementações de UI web para a maioria das plataformas, mas existem casos em que você pode querer hospedar o navegador você mesmo:

- Plataformas que não são explicitamente cobertas pela MSAL, por exemplo, Blazor, Unity e Mono em desktops.
- Pretende testar a sua aplicação e utilizar um browser automatizado que pode ser usado com Selénio.
- O navegador e a app que executam o MSAL estão em processos separados.

##### <a name="at-a-glance"></a>Síntese

Para isso, você dá ao MSAL `start Url` , que precisa de ser exibido num navegador de eleição para que o utilizador final possa introduzir itens como o seu nome de utilizador.
Após a autenticação terminada, a sua aplicação precisa de passar para a MSAL `end Url` , que contém um código fornecido pela Azure AD.
O anfitrião `end Url` é `redirectUri` sempre. Para `end Url` intercetar, faça uma das seguintes coisas:

- Monitorize os redirecionamentos do navegador até `redirect Url` ser atingido.
- Tenha o redirecionamento do navegador para um URL, que monitorize.

##### <a name="withcustomwebui-is-an-extensibility-point"></a>Com oCustomWebUi é um ponto de extensibilidade

`WithCustomWebUi` é um ponto de extensibilidade que pode usar para fornecer a sua própria UI em aplicações de clientes públicos. Também pode deixar o utilizador passar pelo ponto final /Autorizar o fornecedor de identidade e deixá-lo entrar e consentir. MSAL.NET pode então resgatar o código de autenticação e obter um token. Por exemplo, é usado no Visual Studio para ter aplicações de eletrões (por exemplo, Visual Studio Feedback) fornecer a interação web, mas deixá-lo para MSAL.NET para fazer a maior parte do trabalho. Também pode usá-lo se quiser fornecer automatização de UI. Nas aplicações de clientes públicos, MSAL.NET usa a norma Proof Key for Code Exchange (PKCE) para garantir que a segurança é respeitada. Só MSAL.NET pode resgatar o código. Para mais informações, consulte [RFC 7636 - Chave de Prova para Troca de Código por Clientes Públicos da OAuth](https://tools.ietf.org/html/rfc7636).

  ```csharp
  using Microsoft.Identity.Client.Extensions;
  ```

##### <a name="use-withcustomwebui"></a>Use com oCustomWebUi

Para `.WithCustomWebUI` utilizar, siga estes passos.

  1. Implemente a interface `ICustomWebUi`. Para mais informações, consulte [este site.](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/blob/053a98d16596be7e9ca1ab916924e5736e341fe8/src/Microsoft.Identity.Client/Extensibility/ICustomWebUI.cs#L32-L70) Implementar um `AcquireAuthorizationCodeAsync` método e aceitar o URL de código de autorização calculado por MSAL.NET. Em seguida, deixe o utilizador passar pela interação com o fornecedor de identidade e devolver o URL pelo qual o fornecedor de identidade teria chamado a sua implementação de volta juntamente com o código de autorização. Se tiver problemas, a sua implementação deve abrir uma `MsalExtensionException` exceção para cooperar bem com a MSAL.
  2. Na sua `AcquireTokenInteractive` chamada, utilize o `.WithCustomUI()` modificador que passa a instância da sua UI web personalizada.

     ```csharp
     result = await app.AcquireTokenInteractive(scopes)
                       .WithCustomWebUi(yourCustomWebUI)
                       .ExecuteAsync();
     ```

##### <a name="examples-of-implementation-of-icustomwebui-in-test-automation-seleniumwebui"></a>Exemplos de implementação do ICustomWebUi na automatização de testes: SeleniumWebUI

A equipa MSAL.NET reescreveu os testes de UI para usar este mecanismo de extensibilidade. Se estiver interessado, veja a classe [SeleniumWebUI](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/blob/053a98d16596be7e9ca1ab916924e5736e341fe8/tests/Microsoft.Identity.Test.Integration/Infrastructure/SeleniumWebUI.cs#L15-L160) no código fonte MSAL.NET.

##### <a name="provide-a-great-experience-with-systemwebviewoptions"></a>Proporcionar uma grande experiência com SystemWebViewOptions

A partir de MSAL.NET 4.1, [`SystemWebViewOptions`](/dotnet/api/microsoft.identity.client.systemwebviewoptions) pode especificar:

- O URI para ir a ( `BrowserRedirectError` ) ou o fragmento HTML para exibir ( ) em caso de `HtmlMessageError` erros de sposição ou consentimento no navegador web do sistema.
- O URI para ir a ( `BrowserRedirectSuccess` ) ou ao fragmento HTML para exibir ( ) em caso de sucesso de iniciar `HtmlMessageSuccess` sing-in ou consentimento.
- A ação para executar para iniciar o navegador do sistema. Pode fornecer a sua própria implementação definindo o `OpenBrowserAsync` delegado. A classe também fornece uma implementação padrão para dois navegadores: `OpenWithEdgeBrowserAsync` e para o Microsoft Edge e Microsoft Edge no `OpenWithChromeEdgeBrowserAsync` [Chromium](https://www.windowscentral.com/faq-edge-chromium), respectivamente.

Para utilizar esta estrutura, escreva algo como o seguinte exemplo:

```csharp
IPublicClientApplication app;
...

options = new SystemWebViewOptions
{
 HtmlMessageError = "<b>Sign-in failed. You can close this tab ...</b>",
 BrowserRedirectSuccess = "https://contoso.com/help-for-my-awesome-commandline-tool.html"
};

var result = app.AcquireTokenInteractive(scopes)
                .WithEmbeddedWebView(false)       // The default in .NET Core
                .WithSystemWebViewOptions(options)
                .Build();
```

#### <a name="other-optional-parameters"></a>Outros parâmetros opcionais

Para saber mais sobre todos os outros parâmetros opcionais para `AcquireTokenInteractive` , consulte [AcquireTokenInteractiveParameterBuilder](/dotnet/api/microsoft.identity.client.acquiretokeninteractiveparameterbuilder#methods).

# <a name="java"></a>[Java](#tab/java)

```java
private static IAuthenticationResult acquireTokenInteractive() throws Exception {

    // Load token cache from file and initialize token cache aspect. The token cache will have
    // dummy data, so the acquireTokenSilently call will fail.
    TokenCacheAspect tokenCacheAspect = new TokenCacheAspect("sample_cache.json");

    PublicClientApplication pca = PublicClientApplication.builder(CLIENT_ID)
            .authority(AUTHORITY)
            .setTokenCacheAccessAspect(tokenCacheAspect)
            .build();

    Set<IAccount> accountsInCache = pca.getAccounts().join();
    // Take first account in the cache. In a production application, you would filter
    // accountsInCache to get the right account for the user authenticating.
    IAccount account = accountsInCache.iterator().next();

    IAuthenticationResult result;
    try {
        SilentParameters silentParameters =
                SilentParameters
                        .builder(SCOPE, account)
                        .build();

        // try to acquire token silently. This call will fail since the token cache
        // does not have any data for the user you are trying to acquire a token for
        result = pca.acquireTokenSilently(silentParameters).join();
    } catch (Exception ex) {
        if (ex.getCause() instanceof MsalException) {

            InteractiveRequestParameters parameters = InteractiveRequestParameters
                    .builder(new URI("http://localhost"))
                    .scopes(SCOPE)
                    .build();

            // Try to acquire a token interactively with system browser. If successful, you should see
            // the token and account information printed out to console
            result = pca.acquireToken(parameters).join();
        } else {
            // Handle other exceptions accordingly
            throw ex;
        }
    }
    return result;
}
```

# <a name="macos"></a>[macOS](#tab/macOS)

### <a name="in-msal-for-ios-and-macos"></a>No MSAL para iOS e macOS

Objetivo C:

```objc
MSALInteractiveTokenParameters *interactiveParams = [[MSALInteractiveTokenParameters alloc] initWithScopes:scopes webviewParameters:[MSALWebviewParameters new]];
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

Rápido:

```swift
let interactiveParameters = MSALInteractiveTokenParameters(scopes: scopes, webviewParameters: MSALWebviewParameters())
application.acquireToken(with: interactiveParameters, completionBlock: { (result, error) in

    guard let authResult = result, error == nil else {
        print(error!.localizedDescription)
        return
    }

    // Get access token from result
    let accessToken = authResult.accessToken
})
```

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

No nó MSAL, você adquire fichas através do fluxo de código de autorização com chave de prova para troca de códigos (PKCE). O processo tem dois passos: primeiro, a aplicação obtém um URL que pode ser usado para gerar um código de autorização. Este URL pode ser aberto num navegador de eleição, onde o utilizador pode inserir as suas credenciais, e será redirecionado de volta para o `redirectUri` (registado durante o registo da aplicação) com um código de autorização. Em segundo lugar, o pedido passa o código de autorização recebido para o método que o `acquireTokenByCode()` troca por um token de acesso.

```JavaScript
const msal = require("@azure/msal-node");

const msalConfig = {
    auth: {
        clientId: "your_client_id_here",
        authority: "your_authority_here",
    }
};

const pca = new msal.PublicClientApplication(msalConfig);

const {verifier, challenge} = await msal.cryptoProvider.generatePkceCodes();

const authCodeUrlParameters = {
    scopes: ["User.Read"],
    redirectUri: "your_redirect_uri",
    codeChallenge: challenge, // PKCE Code Challenge
    codeChallengeMethod: "S256" // PKCE Code Challenge Method 
};

// get url to sign user in and consent to scopes needed for application
pca.getAuthCodeUrl(authCodeUrlParameters).then((response) => {
    console.log(response);

    const tokenRequest = {
        code: response["authorization_code"],
        codeVerifier: verifier // PKCE Code Verifier 
        redirectUri: "your_redirect_uri",
        scopes: ["User.Read"],
    };
    
    // acquire a token by exchanging the code
    pca.acquireTokenByCode(tokenRequest).then((response) => {
        console.log("\nResponse: \n:", response);
    }).catch((error) => {
        console.log(error);
    });
}).catch((error) => console.log(JSON.stringify(error)));
```

# <a name="python"></a>[Python](#tab/python)

A MSAL Python não fornece um método de token de aquisição interativo diretamente. Em vez disso, requer que o pedido envie um pedido de autorização na sua implementação do fluxo de interação do utilizador para obter um código de autorização. Este código pode então ser transmitido ao `acquire_token_by_authorization_code` método para obter o símbolo.

```Python
result = None

# Firstly, check the cache to see if this end user has signed in before
accounts = app.get_accounts(username=config["username"])
if accounts:
    result = app.acquire_token_silent(config["scope"], account=accounts[0])

if not result:
    result = app.acquire_token_by_authorization_code(
         request.args['code'],
         scopes=config["scope"])

```
---

## <a name="integrated-windows-authentication"></a>Autenticação Integrada do Windows.

Para iniciar súm num utilizador de domínio numa máquina de união a um domínio ou Azure AD, utilize a Autenticação Integrada do Windows (IWA).

### <a name="constraints"></a>Restrições

- A Autenticação Integrada do Windows é utilizável apenas para utilizadores *federados+* ou seja, utilizadores criados em Ative Directory e apoiados pelo Azure AD. Os utilizadores criados diretamente no AD Azure sem suporte ative Directory, conhecido como utilizadores *geridos,* não podem utilizar este fluxo de autenticação. Esta limitação não afeta o nome de utilizador e o fluxo de palavra-passe.
- IWA destina-se a aplicações escritas para plataformas .NET Framework, .NET Core e Universal Windows Platform (UWP).
- A IWA não contorna a [autenticação de vários fatores (MFA)](../authentication/concept-mfa-howitworks.md). Se o MFA estiver configurado, o IWA pode falhar se for necessário um desafio MFA, porque o MFA requer interação do utilizador.
  
    A IWA não é interativa, mas o MFA requer interatividade do utilizador. Você não controla quando o fornecedor de identidade pede MFA para ser executado, o administrador inquilino faz. A partir das nossas observações, o MFA é necessário quando você assina de um país/região diferente, quando não conectado via VPN a uma rede corporativa, e às vezes mesmo quando conectado via VPN. Não espere um conjunto de regras deterministas. A Azure AD usa IA para aprender continuamente se o MFA é necessário. Recue para um pedido de utilizador como autenticação interativa ou fluxo de código do dispositivo se a IWA falhar.

- A autoridade aprovada `PublicClientApplicationBuilder` tem de ser:
  - Arrendado do `https://login.microsoftonline.com/{tenant}/` formulário, onde `tenant` está o GUID que representa o ID do inquilino ou um domínio associado ao arrendatário.
  - Para qualquer trabalho e contas escolares: `https://login.microsoftonline.com/organizations/` .
  - As contas pessoais da Microsoft não são suportadas. Não pode usar inquilinos comuns ou /consumidores.

- Porque a autenticação integrada do Windows é um fluxo silencioso:
  - O utilizador da sua aplicação deve ter previamente consentido em utilizar a aplicação.
  - Ou, o administrador inquilino deve ter previamente consentido com todos os utilizadores do arrendatário para usar a aplicação.
  - Por outras palavras:
    - Ou você como um desenvolvedor selecionou o botão **Grant** no portal Azure para si mesmo.
    - Ou, um administrador inquilino selecionou o **consentimento administrativo Grant/revoke para o** botão {tenant domain} no separador **permissões API** do registo para o pedido. Para mais informações, consulte [Adicionar permissões para aceder à sua API web.](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-your-web-api)
    - Ou, forneceu uma forma de os utilizadores consentirem com a aplicação. Para obter mais informações, consulte [Solicitando consentimento individual do utilizador.](./v2-permissions-and-consent.md#requesting-individual-user-consent)
    - Ou, você forneceu uma maneira para o administrador inquilino consentir com o pedido. Para mais informações, consulte [o consentimento do Administrador.](./v2-permissions-and-consent.md#requesting-consent-for-an-entire-tenant)

- Este fluxo está ativado para aplicações .NET desktop, .NET Core e UWP.

Para obter mais informações sobre o consentimento, consulte as permissões e consentimento da [plataforma de identidade da Microsoft.](./v2-permissions-and-consent.md)

### <a name="learn-how-to-use-it"></a>Saiba como usá-lo

# <a name="net"></a>[.NET](#tab/dotnet)

Em MSAL.NET, use:

```csharp
AcquireTokenByIntegratedWindowsAuth(IEnumerable<string> scopes)
```

Normalmente só precisa de um parâmetro `scopes` (). Dependendo da forma como o administrador do Windows configura as políticas, as aplicações na sua máquina Windows podem não ser autorizadas a procurar o utilizador inscrito. Nesse caso, utilize um segundo `.WithUsername()` método, e passe no nome de utilizador do utilizador inscrito como formato UPN, por exemplo, `joe@contoso.com` .

A amostra que se segue apresenta o caso mais atual, com explicações sobre o tipo de exceções que pode obter e as suas mitigações.

```csharp
static async Task GetATokenForGraph()
{
 string authority = "https://login.microsoftonline.com/contoso.com";
 string[] scopes = new string[] { "user.read" };
 IPublicClientApplication app = PublicClientApplicationBuilder
      .Create(clientId)
      .WithAuthority(authority)
      .Build();

 var accounts = await app.GetAccountsAsync();

 AuthenticationResult result = null;
 if (accounts.Any())
 {
  result = await app.AcquireTokenSilent(scopes, accounts.FirstOrDefault())
      .ExecuteAsync();
 }
 else
 {
  try
  {
   result = await app.AcquireTokenByIntegratedWindowsAuth(scopes)
      .ExecuteAsync(CancellationToken.None);
  }
  catch (MsalUiRequiredException ex)
  {
   // MsalUiRequiredException: AADSTS65001: The user or administrator has not consented to use the application
   // with ID '{appId}' named '{appName}'.Send an interactive authorization request for this user and resource.

   // you need to get user consent first. This can be done, if you are not using .NET Core (which does not have any Web UI)
   // by doing (once only) an AcquireToken interactive.

   // If you are using .NET core or don't want to do an AcquireTokenInteractive, you might want to suggest the user to navigate
   // to a URL to consent: https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id={clientId}&response_type=code&scope=user.read

   // AADSTS50079: The user is required to use multi-factor authentication.
   // There is no mitigation - if MFA is configured for your tenant and AAD decides to enforce it,
   // you need to fallback to an interactive flows such as AcquireTokenInteractive or AcquireTokenByDeviceCode
   }
   catch (MsalServiceException ex)
   {
    // Kind of errors you could have (in ex.Message)

    // MsalServiceException: AADSTS90010: The grant type is not supported over the /common or /consumers endpoints. Please use the /organizations or tenant-specific endpoint.
    // you used common.
    // Mitigation: as explained in the message from Azure AD, the authority needs to be tenanted or otherwise organizations

    // MsalServiceException: AADSTS70002: The request body must contain the following parameter: 'client_secret or client_assertion'.
    // Explanation: this can happen if your application was not registered as a public client application in Azure AD
    // Mitigation: in the Azure portal, edit the manifest for your application and set the `allowPublicClient` to `true`
   }
   catch (MsalClientException ex)
   {
      // Error Code: unknown_user Message: Could not identify logged in user
      // Explanation: the library was unable to query the current Windows logged-in user or this user is not AD or AAD
      // joined (work-place joined users are not supported).

      // Mitigation 1: on UWP, check that the application has the following capabilities: Enterprise Authentication,
      // Private Networks (Client and Server), User Account Information

      // Mitigation 2: Implement your own logic to fetch the username (e.g. john@contoso.com) and use the
      // AcquireTokenByIntegratedWindowsAuth form that takes in the username

      // Error Code: integrated_windows_auth_not_supported_managed_user
      // Explanation: This method relies on an a protocol exposed by Active Directory (AD). If a user was created in Azure
      // Active Directory without AD backing ("managed" user), this method will fail. Users created in AD and backed by
      // AAD ("federated" users) can benefit from this non-interactive method of authentication.
      // Mitigation: Use interactive authentication
   }
 }


 Console.WriteLine(result.Account.Username);
}
```

Para obter a lista de possíveis modificadores em AcquireTokenByIntegratedWindowsAuthentication, consulte [AcquireTokenByIntegratedWindowsAuthParameterBuilder](/dotnet/api/microsoft.identity.client.acquiretokenbyintegratedwindowsauthparameterbuilder#methods).

# <a name="java"></a>[Java](#tab/java)

Este extrato é das [amostras dev MSAL Java.](https://github.com/AzureAD/microsoft-authentication-library-for-java/blob/dev/src/samples/public-client/)

```Java
private static IAuthenticationResult acquireTokenIwa() throws Exception {

    // Load token cache from file and initialize token cache aspect. The token cache will have
    // dummy data, so the acquireTokenSilently call will fail.
    TokenCacheAspect tokenCacheAspect = new TokenCacheAspect("sample_cache.json");

    PublicClientApplication pca = PublicClientApplication.builder(CLIENT_ID)
            .authority(AUTHORITY)
            .setTokenCacheAccessAspect(tokenCacheAspect)
            .build();

    Set<IAccount> accountsInCache = pca.getAccounts().join();
    // Take first account in the cache. In a production application, you would filter
    // accountsInCache to get the right account for the user authenticating.
    IAccount account = accountsInCache.iterator().next();

    IAuthenticationResult result;
    try {
        SilentParameters silentParameters =
                SilentParameters
                        .builder(SCOPE, account)
                        .build();

        // try to acquire token silently. This call will fail since the token cache
        // does not have any data for the user you are trying to acquire a token for
        result = pca.acquireTokenSilently(silentParameters).join();
    } catch (Exception ex) {
        if (ex.getCause() instanceof MsalException) {

            IntegratedWindowsAuthenticationParameters parameters =
                    IntegratedWindowsAuthenticationParameters
                            .builder(SCOPE, USER_NAME)
                            .build();

            // Try to acquire a IWA. You will need to generate a Kerberos ticket.
            // If successful, you should see the token and account information printed out to
            // console
            result = pca.acquireToken(parameters).join();
        } else {
            // Handle other exceptions accordingly
            throw ex;
        }
    }
    return result;
}
```

# <a name="macos"></a>[macOS](#tab/macOS)

Este fluxo não se aplica ao macOS.

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

Este fluxo ainda não está suportado no nó MSAL.

# <a name="python"></a>[Python](#tab/python)

Este fluxo ainda não está suportado no MSAL Python.

---

## <a name="username-and-password"></a>Nome de utilizador e palavra-passe

Também pode adquirir um token fornecendo o nome de utilizador e a palavra-passe. Este fluxo é limitado e não recomendado, mas ainda existem casos de uso onde é necessário.

### <a name="this-flow-isnt-recommended"></a>Este fluxo não é recomendado

O nome de utilizador e o fluxo de senha não são *recomendados* porque ter a sua aplicação a pedir a palavra-passe a um utilizador não é seguro. Para mais informações, veja [qual é a solução para o problema crescente das senhas?](https://news.microsoft.com/features/whats-solution-growing-problem-passwords-says-microsoft/) O fluxo preferido para adquirir um símbolo silenciosamente nas máquinas unidas ao domínio windows é [a Autenticação Integrada do Windows](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Integrated-Windows-Authentication). Também pode utilizar o [fluxo de código do dispositivo.](https://aka.ms/msal-net-device-code-flow)

A utilização de um nome de utilizador e palavra-passe é útil em alguns casos, como cenários de DevOps. Mas se quiser usar um nome de utilizador e uma palavra-passe em cenários interativos onde fornece a sua própria UI, pense em como se afastar dele. Ao usar um nome de utilizador e senha, está a abdicar de uma série de coisas:

- Princípios fundamentais da identidade moderna. Uma palavra-passe pode ser phished e reproduzida porque um segredo partilhado pode ser intercetado. É incompatível com sem palavras-passe.
- Os utilizadores que precisam de fazer MFA não podem entrar porque não há interação.
- Os utilizadores não podem fazer um único sinal de inscrição (SSO).

### <a name="constraints"></a>Restrições

Aplicam-se igualmente os seguintes constrangimentos:

- O nome de utilizador e o fluxo de palavra-passe não são compatíveis com o acesso condicional e a autenticação de vários fatores. Como consequência, se a sua aplicação funcionar num inquilino AZure AD onde o administrador inquilino requer autenticação multi-factor, não pode utilizar este fluxo. Muitas organizações fazem isso.
- Funciona apenas para contas de trabalho e escola (não MSA).
- O fluxo está disponível em .NET desktop e .NET Core, mas não em UWP.

### <a name="b2c-specifics"></a>Especificidades B2C

Para obter mais informações, consulte [as credenciais de senha do proprietário do recurso (ROPC) com B2C](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/AAD-B2C-specifics#resource-owner-password-credentials-ropc-with-b2c).

### <a name="use-it"></a>Use-o

# <a name="net"></a>[.NET](#tab/dotnet)

`IPublicClientApplication`contém o `AcquireTokenByUsernamePassword` método.

A amostra que se segue apresenta um caso simplificado.

```csharp
static async Task GetATokenForGraph()
{
 string authority = "https://login.microsoftonline.com/contoso.com";
 string[] scopes = new string[] { "user.read" };
 IPublicClientApplication app;
 app = PublicClientApplicationBuilder.Create(clientId)
       .WithAuthority(authority)
       .Build();
 var accounts = await app.GetAccountsAsync();

 AuthenticationResult result = null;
 if (accounts.Any())
 {
  result = await app.AcquireTokenSilent(scopes, accounts.FirstOrDefault())
                    .ExecuteAsync();
 }
 else
 {
  try
  {
   var securePassword = new SecureString();
   foreach (char c in "dummy")        // you should fetch the password
    securePassword.AppendChar(c);  // keystroke by keystroke

   result = await app.AcquireTokenByUsernamePassword(scopes,
                                                    "joe@contoso.com",
                                                     securePassword)
                      .ExecuteAsync();
  }
  catch(MsalException)
  {
   // See details below
  }
 }
 Console.WriteLine(result.Account.Username);
}
```

A amostra que se segue apresenta o caso mais atual, com explicações sobre o tipo de exceções que pode obter e as suas mitigações.

```csharp
static async Task GetATokenForGraph()
{
 string authority = "https://login.microsoftonline.com/contoso.com";
 string[] scopes = new string[] { "user.read" };
 IPublicClientApplication app;
 app = PublicClientApplicationBuilder.Create(clientId)
                                   .WithAuthority(authority)
                                   .Build();
 var accounts = await app.GetAccountsAsync();

 AuthenticationResult result = null;
 if (accounts.Any())
 {
  result = await app.AcquireTokenSilent(scopes, accounts.FirstOrDefault())
                    .ExecuteAsync();
 }
 else
 {
  try
  {
   var securePassword = new SecureString();
   foreach (char c in "dummy")        // you should fetch the password keystroke
    securePassword.AppendChar(c);  // by keystroke

   result = await app.AcquireTokenByUsernamePassword(scopes,
                                                    "joe@contoso.com",
                                                    securePassword)
                    .ExecuteAsync();
  }
  catch (MsalUiRequiredException ex) when (ex.Message.Contains("AADSTS65001"))
  {
   // Here are the kind of error messages you could have, and possible mitigations

   // ------------------------------------------------------------------------
   // MsalUiRequiredException: AADSTS65001: The user or administrator has not consented to use the application
   // with ID '{appId}' named '{appName}'. Send an interactive authorization request for this user and resource.

   // Mitigation: you need to get user consent first. This can be done either statically (through the portal),
   /// or dynamically (but this requires an interaction with Azure AD, which is not possible with
   // the username/password flow)
   // Statically: in the portal by doing the following in the "API permissions" tab of the application registration:
   // 1. Click "Add a permission" and add all the delegated permissions corresponding to the scopes you want (for instance
   // User.Read and User.ReadBasic.All)
   // 2. Click "Grant/revoke admin consent for <tenant>") and click "yes".
   // Dynamically, if you are not using .NET Core (which does not have any Web UI) by
   // calling (once only) AcquireTokenInteractive.
   // remember that Username/password is for public client applications that is desktop/mobile applications.
   // If you are using .NET core or don't want to call AcquireTokenInteractive, you might want to:
   // - use device code flow (See https://aka.ms/msal-net-device-code-flow)
   // - or suggest the user to navigate to a URL to consent: https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id={clientId}&response_type=code&scope=user.read
   // ------------------------------------------------------------------------

   // ------------------------------------------------------------------------
   // ErrorCode: invalid_grant
   // SubError: basic_action
   // MsalUiRequiredException: AADSTS50079: The user is required to use multi-factor authentication.
   // The tenant admin for your organization has chosen to oblige users to perform multi-factor authentication.
   // Mitigation: none for this flow
   // Your application cannot use the Username/Password grant.
   // Like in the previous case, you might want to use an interactive flow (AcquireTokenInteractive()),
   // or Device Code Flow instead.
   // Note this is one of the reason why using username/password is not recommended;
   // ------------------------------------------------------------------------

   // ------------------------------------------------------------------------
   // ex.ErrorCode: invalid_grant
   // subError: null
   // Message = "AADSTS70002: Error validating credentials.
   // AADSTS50126: Invalid username or password
   // In the case of a managed user (user from an Azure AD tenant opposed to a
   // federated user, which would be owned
   // in another IdP through ADFS), the user has entered the wrong password
   // Mitigation: ask the user to re-enter the password
   // ------------------------------------------------------------------------

   // ------------------------------------------------------------------------
   // ex.ErrorCode: invalid_grant
   // subError: null
   // MsalServiceException: ADSTS50034: To sign into this application the account must be added to
   // the {domainName} directory.
   // or The user account does not exist in the {domainName} directory. To sign into this application,
   // the account must be added to the directory.
   // The user was not found in the directory
   // Explanation: wrong username
   // Mitigation: ask the user to re-enter the username.
   // ------------------------------------------------------------------------
  }
  catch (MsalServiceException ex) when (ex.ErrorCode == "invalid_request")
  {
   // ------------------------------------------------------------------------
   // AADSTS90010: The grant type is not supported over the /common or /consumers endpoints.
   // Please use the /organizations or tenant-specific endpoint.
   // you used common.
   // Mitigation: as explained in the message from Azure AD, the authority you use in the application needs
   // to be tenanted or otherwise "organizations". change the
   // "Tenant": property in the appsettings.json to be a GUID (tenant Id), or domain name (contoso.com)
   // if such a domain is registered with your tenant
   // or "organizations", if you want this application to sign-in users in any Work and School accounts.
   // ------------------------------------------------------------------------

  }
  catch (MsalServiceException ex) when (ex.ErrorCode == "unauthorized_client")
  {
   // ------------------------------------------------------------------------
   // AADSTS700016: Application with identifier '{clientId}' was not found in the directory '{domain}'.
   // This can happen if the application has not been installed by the administrator of the tenant or consented
   // to by any user in the tenant.
   // You may have sent your authentication request to the wrong tenant
   // Cause: The clientId in the appsettings.json might be wrong
   // Mitigation: check the clientId and the app registration
   // ------------------------------------------------------------------------
  }
  catch (MsalServiceException ex) when (ex.ErrorCode == "invalid_client")
  {
   // ------------------------------------------------------------------------
   // AADSTS70002: The request body must contain the following parameter: 'client_secret or client_assertion'.
   // Explanation: this can happen if your application was not registered as a public client application in Azure AD
   // Mitigation: in the Azure portal, edit the manifest for your application and set the `allowPublicClient` to `true`
   // ------------------------------------------------------------------------
  }
  catch (MsalServiceException)
  {
   throw;
  }

  catch (MsalClientException ex) when (ex.ErrorCode == "unknown_user_type")
  {
   // Message = "Unsupported User Type 'Unknown'. Please see https://aka.ms/msal-net-up"
   // The user is not recognized as a managed user, or a federated user. Azure AD was not
   // able to identify the IdP that needs to process the user
   throw new ArgumentException("U/P: Wrong username", ex);
  }
  catch (MsalClientException ex) when (ex.ErrorCode == "user_realm_discovery_failed")
  {
   // The user is not recognized as a managed user, or a federated user. Azure AD was not
   // able to identify the IdP that needs to process the user. That's for instance the case
   // if you use a phone number
   throw new ArgumentException("U/P: Wrong username", ex);
  }
  catch (MsalClientException ex) when (ex.ErrorCode == "unknown_user")
  {
   // the username was probably empty
   // ex.Message = "Could not identify the user logged into the OS. See https://aka.ms/msal-net-iwa for details."
   throw new ArgumentException("U/P: Wrong username", ex);
  }
  catch (MsalClientException ex) when (ex.ErrorCode == "parsing_wstrust_response_failed")
  {
   // ------------------------------------------------------------------------
   // In the case of a Federated user (that is owned by a federated IdP, as opposed to a managed user owned in an Azure AD tenant)
   // ID3242: The security token could not be authenticated or authorized.
   // The user does not exist or has entered the wrong password
   // ------------------------------------------------------------------------
  }
 }

 Console.WriteLine(result.Account.Username);
}
```

Para obter mais informações sobre todos os modificadores a que podem ser aplicados `AcquireTokenByUsernamePassword` , consulte [AcquireTokenByUsernamePasswordParameterBuilder](/dotnet/api/microsoft.identity.client.acquiretokenbyusernamepasswordparameterbuilder#methods).

# <a name="java"></a>[Java](#tab/java)

O seguinte extrato provém das [amostras dev MSAL Java](https://github.com/AzureAD/microsoft-authentication-library-for-java/blob/dev/src/samples/public-client/).

```Java
private static IAuthenticationResult acquireTokenUsernamePassword() throws Exception {

    // Load token cache from file and initialize token cache aspect. The token cache will have
    // dummy data, so the acquireTokenSilently call will fail.
    TokenCacheAspect tokenCacheAspect = new TokenCacheAspect("sample_cache.json");

    PublicClientApplication pca = PublicClientApplication.builder(CLIENT_ID)
            .authority(AUTHORITY)
            .setTokenCacheAccessAspect(tokenCacheAspect)
            .build();

    Set<IAccount> accountsInCache = pca.getAccounts().join();
    // Take first account in the cache. In a production application, you would filter
    // accountsInCache to get the right account for the user authenticating.
    IAccount account = accountsInCache.iterator().next();

    IAuthenticationResult result;
    try {
        SilentParameters silentParameters =
                SilentParameters
                        .builder(SCOPE, account)
                        .build();
        // try to acquire token silently. This call will fail since the token cache
        // does not have any data for the user you are trying to acquire a token for
        result = pca.acquireTokenSilently(silentParameters).join();
    } catch (Exception ex) {
        if (ex.getCause() instanceof MsalException) {

            UserNamePasswordParameters parameters =
                    UserNamePasswordParameters
                            .builder(SCOPE, USER_NAME, USER_PASSWORD.toCharArray())
                            .build();
            // Try to acquire a token via username/password. If successful, you should see
            // the token and account information printed out to console
            result = pca.acquireToken(parameters).join();
        } else {
            // Handle other exceptions accordingly
            throw ex;
        }
    }
    return result;
}
```

# <a name="macos"></a>[macOS](#tab/macOS)

Este fluxo não é suportado no MSAL para o macOS.

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

Este extrato é proveniente das [amostras de dev do nó MSAL](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/samples/msal-node-samples/standalone-samples/username-password). No código abaixo, o nome de utilizador e a palavra-passe são codificados apenas para fins de ilustração. Isto deve ser evitado na produção. Em vez disso, recomenda-se um UI básico que insira o seu nome de utilizador/palavra-passe. 

```JavaScript
const msal = require("@azure/msal-node");

const msalConfig = {
    auth: {
        clientId: "your_client_id_here",
        authority: "your_authority_here",
    }
};

const pca = new msal.PublicClientApplication(msalConfig);

// For testing, enter your username and password below.
// In production, replace this with a UI prompt instead.
const usernamePasswordRequest = {
    scopes: ["user.read"],
    username: "", // Add your username here
    password: "", // Add your password here
};

pca.acquireTokenByUsernamePassword(usernamePasswordRequest).then((response) => {
    console.log("acquired token by password grant");
}).catch((error) => {
    console.log(error);
});
```

# <a name="python"></a>[Python](#tab/python)

Este extrato é das [amostras dev MSAL Python.](https://github.com/AzureAD/microsoft-authentication-library-for-python/blob/dev/sample/)

```Python
# Create a preferably long-lived app instance which maintains a token cache.
app = msal.PublicClientApplication(
    config["client_id"], authority=config["authority"],
    # token_cache=...  # Default cache is in memory only.
                       # You can learn how to use SerializableTokenCache from
                       # https://msal-python.rtfd.io/en/latest/#msal.SerializableTokenCache
    )

# The pattern to acquire a token looks like this.
result = None

# Firstly, check the cache to see if this end user has signed in before
accounts = app.get_accounts(username=config["username"])
if accounts:
    logging.info("Account(s) exists in cache, probably with token too. Let's try.")
    result = app.acquire_token_silent(config["scope"], account=accounts[0])

if not result:
    logging.info("No suitable token exists in cache. Let's get a new one from AAD.")
    # See this page for constraints of Username Password Flow.
    # https://github.com/AzureAD/microsoft-authentication-library-for-python/wiki/Username-Password-Authentication
    result = app.acquire_token_by_username_password(
        config["username"], config["password"], scopes=config["scope"])
```

---

## <a name="command-line-tool-without-a-web-browser"></a>Ferramenta de linha de comando sem um navegador web

### <a name="device-code-flow"></a>Fluxo de código do dispositivo

Se estiver a escrever uma ferramenta de linha de comando que não tenha controlos web e não puder ou não quiser utilizar os fluxos anteriores, utilize o fluxo de código do dispositivo.

A autenticação interativa com Azure AD requer um navegador web. Para mais informações, consulte [a utilização dos navegadores web.](https://aka.ms/msal-net-uses-web-browser) Para autenticar os utilizadores em dispositivos ou sistemas operativos que não fornecem um navegador web, o fluxo de código do dispositivo permite ao utilizador utilizar outro dispositivo, como um computador ou um telemóvel, para iniciar sivamente. Ao utilizar o fluxo de código do dispositivo, a aplicação obtém fichas através de um processo em duas etapas que é projetado para estes dispositivos ou sistemas operativos. Exemplos de tais aplicações são aplicações que funcionam em ferramentas iOT ou linha de comando (CLI). A ideia é que:

1. Sempre que for necessária a autenticação do utilizador, a aplicação fornece um código para o utilizador. Pede-se ao utilizador que utilize outro dispositivo, como um smartphone ligado à Internet, para ir a um URL, por exemplo, `https://microsoft.com/devicelogin` . Em seguida, o utilizador é solicitado a introduzir o código. Assim, a página web lidera o utilizador através de uma experiência de autenticação normal, que inclui pedidos de consentimento e autenticação de vários fatores, se necessário.

2. Após a autenticação bem sucedida, a aplicação da linha de comando recebe os tokens necessários através de um canal traseiro e utiliza-os para executar as chamadas web da API de que necessita.

### <a name="use-it"></a>Use-o

# <a name="net"></a>[.NET](#tab/dotnet)

`IPublicClientApplication`contém um método chamado `AcquireTokenWithDeviceCode` .

```csharp
 AcquireTokenWithDeviceCode(IEnumerable<string> scopes,
                            Func<DeviceCodeResult, Task> deviceCodeResultCallback)
```

Este método tem como parâmetros:

- O `scopes` para pedir um sinal de acesso para.
- Uma chamada que recebe o `DeviceCodeResult` .

  ![Propriedades deviceCodeResult](https://user-images.githubusercontent.com/13203188/56024968-7af1b980-5d11-11e9-84c2-5be2ef306dc5.png)

O seguinte código de amostra apresenta a sinopse da maioria dos casos atuais, com explicações sobre o tipo de exceções que pode obter e a sua mitigação. Para obter uma amostra de código totalmente funcional, consulte o código de código de [dispositivos active-dotnetcore-v2](https://github.com/azure-samples/active-directory-dotnetcore-devicecodeflow-v2) no GitHub.

```csharp
private const string ClientId = "<client_guid>";
private const string Authority = "https://login.microsoftonline.com/contoso.com";
private readonly string[] scopes = new string[] { "user.read" };

static async Task<AuthenticationResult> GetATokenForGraph()
{
    IPublicClientApplication pca = PublicClientApplicationBuilder
            .Create(ClientId)
            .WithAuthority(Authority)
            .WithDefaultRedirectUri()
            .Build();

    var accounts = await pca.GetAccountsAsync();

    // All AcquireToken* methods store the tokens in the cache, so check the cache first
    try
    {
        return await pca.AcquireTokenSilent(scopes, accounts.FirstOrDefault())
            .ExecuteAsync();
    }
    catch (MsalUiRequiredException ex)
    {
        // No token found in the cache or AAD insists that a form interactive auth is required (e.g. the tenant admin turned on MFA)
        // If you want to provide a more complex user experience, check out ex.Classification

        return await AcquireByDeviceCodeAsync(pca);
    }
}

private static async Task<AuthenticationResult> AcquireByDeviceCodeAsync(IPublicClientApplication pca)
{
    try
    {
        var result = await pca.AcquireTokenWithDeviceCode(scopes,
            deviceCodeResult =>
            {
                    // This will print the message on the console which tells the user where to go sign-in using
                    // a separate browser and the code to enter once they sign in.
                    // The AcquireTokenWithDeviceCode() method will poll the server after firing this
                    // device code callback to look for the successful login of the user via that browser.
                    // This background polling (whose interval and timeout data is also provided as fields in the
                    // deviceCodeCallback class) will occur until:
                    // * The user has successfully logged in via browser and entered the proper code
                    // * The timeout specified by the server for the lifetime of this code (typically ~15 minutes) has been reached
                    // * The developing application calls the Cancel() method on a CancellationToken sent into the method.
                    //   If this occurs, an OperationCanceledException will be thrown (see catch below for more details).
                    Console.WriteLine(deviceCodeResult.Message);
                return Task.FromResult(0);
            }).ExecuteAsync();

        Console.WriteLine(result.Account.Username);
        return result;
    }

    // TODO: handle or throw all these exceptions depending on your app
    catch (MsalServiceException ex)
    {
        // Kind of errors you could have (in ex.Message)

        // AADSTS50059: No tenant-identifying information found in either the request or implied by any provided credentials.
        // Mitigation: as explained in the message from Azure AD, the authoriy needs to be tenanted. you have probably created
        // your public client application with the following authorities:
        // https://login.microsoftonline.com/common or https://login.microsoftonline.com/organizations

        // AADSTS90133: Device Code flow is not supported under /common or /consumers endpoint.
        // Mitigation: as explained in the message from Azure AD, the authority needs to be tenanted

        // AADSTS90002: Tenant <tenantId or domain you used in the authority> not found. This may happen if there are
        // no active subscriptions for the tenant. Check with your subscription administrator.
        // Mitigation: if you have an active subscription for the tenant this might be that you have a typo in the
        // tenantId (GUID) or tenant domain name.
    }
    catch (OperationCanceledException ex)
    {
        // If you use a CancellationToken, and call the Cancel() method on it, then this *may* be triggered
        // to indicate that the operation was cancelled.
        // See https://docs.microsoft.com/dotnet/standard/threading/cancellation-in-managed-threads
        // for more detailed information on how C# supports cancellation in managed threads.
    }
    catch (MsalClientException ex)
    {
        // Possible cause - verification code expired before contacting the server
        // This exception will occur if the user does not manage to sign-in before a time out (15 mins) and the
        // call to `AcquireTokenWithDeviceCode` is not cancelled in between
    }
}
```

# <a name="java"></a>[Java](#tab/java)

Este extrato é das [amostras dev MSAL Java.](https://github.com/AzureAD/microsoft-authentication-library-for-java/blob/dev/src/samples/public-client/)

```java
private static IAuthenticationResult acquireTokenDeviceCode() throws Exception {

    // Load token cache from file and initialize token cache aspect. The token cache will have
    // dummy data, so the acquireTokenSilently call will fail.
    TokenCacheAspect tokenCacheAspect = new TokenCacheAspect("sample_cache.json");

    PublicClientApplication pca = PublicClientApplication.builder(CLIENT_ID)
            .authority(AUTHORITY)
            .setTokenCacheAccessAspect(tokenCacheAspect)
            .build();

    Set<IAccount> accountsInCache = pca.getAccounts().join();
    // Take first account in the cache. In a production application, you would filter
    // accountsInCache to get the right account for the user authenticating.
    IAccount account = accountsInCache.iterator().next();

    IAuthenticationResult result;
    try {
        SilentParameters silentParameters =
                SilentParameters
                        .builder(SCOPE, account)
                        .build();

        // try to acquire token silently. This call will fail since the token cache
        // does not have any data for the user you are trying to acquire a token for
        result = pca.acquireTokenSilently(silentParameters).join();
    } catch (Exception ex) {
        if (ex.getCause() instanceof MsalException) {

            Consumer<DeviceCode> deviceCodeConsumer = (DeviceCode deviceCode) ->
                    System.out.println(deviceCode.message());

            DeviceCodeFlowParameters parameters =
                    DeviceCodeFlowParameters
                            .builder(SCOPE, deviceCodeConsumer)
                            .build();

            // Try to acquire a token via device code flow. If successful, you should see
            // the token and account information printed out to console, and the sample_cache.json
            // file should have been updated with the latest tokens.
            result = pca.acquireToken(parameters).join();
        } else {
            // Handle other exceptions accordingly
            throw ex;
        }
    }
    return result;
}
```

# <a name="macos"></a>[macOS](#tab/macOS)

Este fluxo não se aplica ao macOS.

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

Este extrato é proveniente das [amostras de dev do nó MSAL](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/samples/msal-node-samples/standalone-samples/device-code).

```JavaScript
const msal = require('@azure/msal-node');

const msalConfig = {
    auth: {
        clientId: "your_client_id_here",
        authority: "your_authority_here",
    }
};

const pca = new msal.PublicClientApplication(msalConfig);

const deviceCodeRequest = {
    deviceCodeCallback: (response) => (console.log(response.message)),
    scopes: ["user.read"],
    timeout: 20,
};

pca.acquireTokenByDeviceCode(deviceCodeRequest).then((response) => {
    console.log(JSON.stringify(response));
}).catch((error) => {
    console.log(JSON.stringify(error));
});
```

# <a name="python"></a>[Python](#tab/python)

Este extrato é das [amostras dev MSAL Python.](https://github.com/AzureAD/microsoft-authentication-library-for-python/blob/dev/sample/)

```Python
# Create a preferably long-lived app instance which maintains a token cache.
app = msal.PublicClientApplication(
    config["client_id"], authority=config["authority"],
    # token_cache=...  # Default cache is in memory only.
                       # You can learn how to use SerializableTokenCache from
                       # https://msal-python.rtfd.io/en/latest/#msal.SerializableTokenCache
    )

# The pattern to acquire a token looks like this.
result = None

# Note: If your device-flow app does not have any interactive ability, you can
#   completely skip the following cache part. But here we demonstrate it anyway.
# We now check the cache to see if we have some end users signed in before.
accounts = app.get_accounts()
if accounts:
    logging.info("Account(s) exists in cache, probably with token too. Let's try.")
    print("Pick the account you want to use to proceed:")
    for a in accounts:
        print(a["username"])
    # Assuming the end user chose this one
    chosen = accounts[0]
    # Now let's try to find a token in cache for this account
    result = app.acquire_token_silent(config["scope"], account=chosen)

if not result:
    logging.info("No suitable token exists in cache. Let's get a new one from AAD.")

    flow = app.initiate_device_flow(scopes=config["scope"])
    if "user_code" not in flow:
        raise ValueError(
            "Fail to create device flow. Err: %s" % json.dumps(flow, indent=4))

    print(flow["message"])
    sys.stdout.flush()  # Some terminal needs this to ensure the message is shown

    # Ideally you should wait here, in order to save some unnecessary polling
    # input("Press Enter after signing in from another device to proceed, CTRL+C to abort.")

    result = app.acquire_token_by_device_flow(flow)  # By default it will block
        # You can follow this instruction to shorten the block time
        #    https://msal-python.readthedocs.io/en/latest/#msal.PublicClientApplication.acquire_token_by_device_flow
        # or you may even turn off the blocking behavior,
        # and then keep calling acquire_token_by_device_flow(flow) in your own customized loop
```

---

## <a name="file-based-token-cache"></a>Cache simbólico baseado em ficheiros

Em MSAL.NET, uma cache simbólica na memória é fornecida por padrão.

### <a name="serialization-is-customizable-in-windows-desktop-apps-and-web-apps-or-web-apis"></a>Serialization é personalizável em aplicativos de desktop do Windows e aplicações web ou APIs web

No caso de .NET Framework e .NET Core, se não fizer nada extra, a cache simbólica na memória dura a duração da aplicação. Para entender por que razão a serialização não é fornecida fora da caixa, lembre-se que as aplicações MSAL .NET desktop ou .NET Core podem ser aplicações de consola ou Windows (que teriam acesso ao sistema de ficheiros) *mas também* aplicações web ou APIs web. Estas aplicações web e APIs web podem usar alguns mecanismos de cache específicos, como bases de dados, caches distribuídos e caches Redis. Para ter uma aplicação de cache token persistente em .NET desktop ou .NET Core, terá de personalizar a serialização.

As classes e interfaces envolvidas na serialização da cache simbólica são os seguintes tipos:

- ``ITokenCache``, que define eventos para subscrever pedidos de serialização de cache simbólicos, e métodos para serializar ou deserizar a cache em vários formatos (ADAL v3.0, MSAL 2.x e MSAL 3.x = ADAL v5.0).
- ``TokenCacheCallback`` é uma chamada transmitida para os eventos para que possa lidar com a serialização. Serão chamados com argumentos do ``TokenCacheNotificationArgs`` tipo.
- ``TokenCacheNotificationArgs`` apenas fornece a aplicação ``ClientId`` e uma referência ao utilizador para o qual o token está disponível.

  ![Diagrama de serialização de cache token](https://user-images.githubusercontent.com/13203188/56027172-d58d1480-5d15-11e9-8ada-c0292f1800b3.png)

> [!IMPORTANT]
> MSAL.NET cria caches simbólicos para si e fornece-lhe a `IToken` cache quando chama as propriedades e propriedades de uma `UserTokenCache` `AppTokenCache` aplicação. Não é suposto implementares a interface sozinho. A sua responsabilidade, quando implementa uma serialização de cache de símbolo personalizado, é:
>
> - Reagir `BeforeAccess` e `AfterAccess` eventos, ou o seu homólogo *assíduo.* O `BeforeAccess` delegado é responsável pela deserização da cache. O `AfterAccess` delegado é responsável pela serialização da cache.
> - Compreenda que parte destes eventos armazenam ou carregam bolhas, que são passadas através do argumento do evento para qualquer armazenamento que você deseja.

As estratégias são diferentes dependendo se estiver a escrever uma serialização de cache simbólica para uma aplicação de cliente público, como um ambiente de trabalho, ou uma aplicação de cliente confidencial, como uma aplicação web ou uma API web ou uma app daemon.

Desde o MSAL v2.x, tem várias opções. A sua escolha depende se pretende serializar a cache apenas para o formato MSAL.NET, que é uma cache de formato unificado que é comum com o MSAL mas também através das plataformas. Ou, você também pode querer apoiar a [serialização](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Token-cache-serialization) de cache de legado de ADAL v3.

A personalização da serialização da cache simbólica para partilhar o estado SSO entre ADAL.NET 3,x, ADAL.NET 5,x e MSAL.NET é explicada em parte da amostra [activa-directório-dotnet-v1-v2](https://github.com/Azure-Samples/active-directory-dotnet-v1-to-v2).

### <a name="simple-token-cache-serialization-msal-only"></a>Serialização simples da cache simbólica (apenas MSAL)

O exemplo a seguir é uma implementação ingénua da serialização personalizada de uma cache simbólica para aplicações de ambiente de trabalho. Aqui, a cache de ficha do utilizador está num ficheiro na mesma pasta que a aplicação ou, numa pasta por utilizador por aplicação, no caso de a aplicação ser uma [aplicação de ambiente de trabalho embalado.](/windows/msix/desktop/desktop-to-uwp-behind-the-scenes) Para obter o código completo, consulte a seguinte amostra: [active-directory-dotnet-desktop-msgraph-v2](https://github.com/Azure-Samples/active-directory-dotnet-desktop-msgraph-v2).

Depois de construir a aplicação, permite a serialização ligando ``TokenCacheHelper.EnableSerialization()`` e passando a aplicação `UserTokenCache` .

```csharp
app = PublicClientApplicationBuilder.Create(ClientId)
    .Build();
TokenCacheHelper.EnableSerialization(app.UserTokenCache);
```

Esta classe de ajudante parece o seguinte corte de código:

```csharp
static class TokenCacheHelper
 {
  public static void EnableSerialization(ITokenCache tokenCache)
  {
   tokenCache.SetBeforeAccess(BeforeAccessNotification);
   tokenCache.SetAfterAccess(AfterAccessNotification);
   try
   {
    // For packaged desktop apps (MSIX packages) the executing assembly folder is read-only. 
    // In that case we need to use Windows.Storage.ApplicationData.Current.LocalCacheFolder.Path + "\msalcache.bin" 
    // which is a per-app read/write folder for packaged apps.
    // See https://docs.microsoft.com/windows/msix/desktop/desktop-to-uwp-behind-the-scenes
    CacheFilePath = System.IO.Path.Combine(Windows.Storage.ApplicationData.Current.LocalCacheFolder.Path, "msalcache.bin3");
   }
   catch (System.InvalidOperationException)
   {
    // Fall back for an un-packaged desktop app
    CacheFilePath = System.Reflection.Assembly.GetExecutingAssembly().Location + ".msalcache.bin";
   }
  }

  /// <summary>
  /// Path to the token cache
  /// </summary>
  public static string CacheFilePath { get; private set; }

  private static readonly object FileLock = new object();

  private static void BeforeAccessNotification(TokenCacheNotificationArgs args)
  {
   lock (FileLock)
   {
    args.TokenCache.DeserializeMsalV3(File.Exists(CacheFilePath)
            ? ProtectedData.Unprotect(File.ReadAllBytes(CacheFilePath),
                                      null,
                                      DataProtectionScope.CurrentUser)
            : null);
   }
  }

  private static void AfterAccessNotification(TokenCacheNotificationArgs args)
  {
   // if the access operation resulted in a cache update
   if (args.HasStateChanged)
   {
    lock (FileLock)
    {
     // reflect changesgs in the persistent store
     File.WriteAllBytes(CacheFilePath,
                         ProtectedData.Protect(args.TokenCache.SerializeMsalV3(),
                                                 null,
                                                 DataProtectionScope.CurrentUser)
                         );
    }
   }
  }
 }
```

Uma pré-visualização de um serializador baseado em ficheiros de cache de qualidade do produto para aplicações de clientes públicos para aplicações de desktop em execução no Windows, Mac e Linux está disponível na biblioteca [microsoft.Identity.Client.Extensions.Msal](https://github.com/AzureAD/microsoft-authentication-extensions-for-dotnet/tree/master/src/Microsoft.Identity.Client.Extensions.Msal) open-source. Pode incluí-lo nas suas aplicações a partir do seguinte pacote NuGet: [Microsoft.Identity.Client.Extensions.Msal](https://www.nuget.org/packages/Microsoft.Identity.Client.Extensions.Msal/).

> [!NOTE]
> Isenção de responsabilidade: A biblioteca Microsoft.Identity.Client.Extensions.Msal é uma extensão sobre MSAL.NET. As aulas nestas bibliotecas podem entrar em MSAL.NET no futuro, como é ou com mudanças de rutura.

### <a name="dual-token-cache-serialization-msal-unified-cache--adal-v3"></a>Serialização de cache de token duplo (cache unificado MSAL + ADAL v3)

É melhor implementar a serialização da cache simbólica com o formato de cache unificado. Este formato é comum a ADAL.NET 4.x e MSAL.NET 2.x, e com outros MSALs da mesma geração ou mais, na mesma plataforma. Inspire-se no seguinte código:

```csharp
string appLocation = Path.GetDirectoryName(Assembly.GetEntryAssembly().Location;
string cacheFolder = Path.GetFullPath(appLocation) + @"..\..\..\..");
string adalV3cacheFileName = Path.Combine(cacheFolder, "cacheAdalV3.bin");
string unifiedCacheFileName = Path.Combine(cacheFolder, "unifiedCache.bin");

IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
                                    .Build();
FilesBasedTokenCacheHelper.EnableSerialization(app.UserTokenCache,
                                               unifiedCacheFileName,
                                               adalV3cacheFileName);

```

Desta vez, a classe de ajudante parece o seguinte código:

```csharp
using System;
using System.IO;
using System.Security.Cryptography;
using Microsoft.Identity.Client;

namespace CommonCacheMsalV3
{
 /// <summary>
 /// Simple persistent cache implementation of the dual cache serialization (ADAL V3 legacy
 /// and unified cache format) for a desktop applications (from MSAL 2.x)
 /// </summary>
 static class FilesBasedTokenCacheHelper
 {
  /// <summary>
  /// Get the user token cache
  /// </summary>
  /// <param name="adalV3CacheFileName">File name where the cache is serialized with the
  /// ADAL V3 token cache format. Can
  /// be <c>null</c> if you don't want to implement the legacy ADAL V3 token cache
  /// serialization in your MSAL 2.x+ application</param>
  /// <param name="unifiedCacheFileName">File name where the cache is serialized
  /// with the Unified cache format, common to
  /// ADAL V4 and MSAL V2 and above, and also across ADAL/MSAL on the same platform.
  ///  Should not be <c>null</c></param>
  /// <returns></returns>
  public static void EnableSerialization(ITokenCache cache, string unifiedCacheFileName, string adalV3CacheFileName)
  {
   UnifiedCacheFileName = unifiedCacheFileName;
   AdalV3CacheFileName = adalV3CacheFileName;

   cache.SetBeforeAccess(BeforeAccessNotification);
   cache.SetAfterAccess(AfterAccessNotification);
  }

  /// <summary>
  /// File path where the token cache is serialized with the unified cache format
  /// (ADAL.NET V4, MSAL.NET V3)
  /// </summary>
  public static string UnifiedCacheFileName { get; private set; }

  /// <summary>
  /// File path where the token cache is serialized with the legacy ADAL V3 format
  /// </summary>
  public static string AdalV3CacheFileName { get; private set; }

  private static readonly object FileLock = new object();

  public static void BeforeAccessNotification(TokenCacheNotificationArgs args)
  {
   lock (FileLock)
   {
    args.TokenCache.DeserializeAdalV3(ReadFromFileIfExists(AdalV3CacheFileName));
    try
    {
     args.TokenCache.DeserializeMsalV3(ReadFromFileIfExists(UnifiedCacheFileName));
    }
    catch(Exception ex)
    {
     // Compatibility with the MSAL v2 cache if you used one
     args.TokenCache.DeserializeMsalV2(ReadFromFileIfExists(UnifiedCacheFileName));
    }
   }
  }

  public static void AfterAccessNotification(TokenCacheNotificationArgs args)
  {
   // if the access operation resulted in a cache update
   if (args.HasStateChanged)
   {
    lock (FileLock)
    {
     WriteToFileIfNotNull(UnifiedCacheFileName, args.TokenCache.SerializeMsalV3());
     if (!string.IsNullOrWhiteSpace(AdalV3CacheFileName))
     {
      WriteToFileIfNotNull(AdalV3CacheFileName, args.TokenCache.SerializeAdalV3());
     }
    }
   }
  }

  /// <summary>
  /// Read the content of a file if it exists
  /// </summary>
  /// <param name="path">File path</param>
  /// <returns>Content of the file (in bytes)</returns>
  private static byte[] ReadFromFileIfExists(string path)
  {
   byte[] protectedBytes = (!string.IsNullOrEmpty(path) && File.Exists(path))
       ? File.ReadAllBytes(path) : null;
   byte[] unprotectedBytes = encrypt ?
       ((protectedBytes != null) ? ProtectedData.Unprotect(protectedBytes, null, DataProtectionScope.CurrentUser) : null)
       : protectedBytes;
   return unprotectedBytes;
  }

  /// <summary>
  /// Writes a blob of bytes to a file. If the blob is <c>null</c>, deletes the file
  /// </summary>
  /// <param name="path">path to the file to write</param>
  /// <param name="blob">Blob of bytes to write</param>
  private static void WriteToFileIfNotNull(string path, byte[] blob)
  {
   if (blob != null)
   {
    byte[] protectedBytes = encrypt
      ? ProtectedData.Protect(blob, null, DataProtectionScope.CurrentUser)
      : blob;
    File.WriteAllBytes(path, protectedBytes);
   }
   else
   {
    File.Delete(path);
   }
  }

  // Change if you want to test with an un-encrypted blob (this is a json format)
  private static bool encrypt = true;
 }
}
```

## <a name="advanced-accessing-the-users-cached-tokens-in-background-apps-and-services"></a>(Avançado) Acede aos tokens em cache do utilizador em aplicativos e serviços de fundo

[!INCLUDE [advanced-token-caching](../../../includes/advanced-token-cache.md)]

## <a name="next-steps"></a>Passos seguintes

Passe para o próximo artigo neste cenário, [Ligue para uma API web a partir da aplicação de ambiente de trabalho](scenario-desktop-call-api.md).
