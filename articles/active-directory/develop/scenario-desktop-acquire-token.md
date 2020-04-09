---
title: Adquirir um símbolo para chamar uma API web (app de desktop) / Azure
titleSuffix: Microsoft identity platform
description: Saiba como construir uma aplicação de desktop que chama APIs web para adquirir um símbolo para a app
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: a5942a9d614bbb06fadb1d4b16d4c68c007434c7
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/08/2020
ms.locfileid: "80885323"
---
# <a name="desktop-app-that-calls-web-apis-acquire-a-token"></a>Aplicação de desktop que chama APIs web: Adquira um símbolo

Depois de ter construído uma instância da aplicação do cliente público, irá usá-la para adquirir um símbolo que usará para chamar uma API web.

## <a name="recommended-pattern"></a>Padrão recomendado

A Web API é `scopes`definida pela sua . Qualquer que seja a experiência que proporciona na sua aplicação, o padrão a utilizar é:

- Tenta sistemáticamente obter um símbolo da cache `AcquireTokenSilent`simbólica, chamando.
- Se esta chamada falhar, `AcquireToken` utilize o fluxo que pretende utilizar, `AcquireTokenXX`que está aqui representado por .

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

# <a name="macos"></a>[MacOS](#tab/macOS)

### <a name="in-msal-for-ios-and-macos"></a>Em MSAL para iOS e macOS

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
Swift:

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
---

Aqui estão as várias formas de adquirir fichas numa aplicação de ambiente de trabalho.

## <a name="acquire-a-token-interactively"></a>Adquirir um símbolo interativamente

O exemplo que se segue mostra código mínimo para obter um símbolo interactivamente para ler o perfil do utilizador com o Microsoft Graph.

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

`AcquireTokenInteractive`tem apenas um parâmetro ``scopes``obrigatório, que contém uma enumeração de cordas que definem os âmbitos para os quais é necessário um símbolo. Se o símbolo for para o Microsoft Graph, os âmbitos necessários podem ser encontrados na referência API de cada API do Microsoft Graph na secção denominada "Permissões". Por exemplo, para [listar os contactos do utilizador,](https://developer.microsoft.com/graph/docs/api-reference/v1.0/api/user_list_contacts)deve ser utilizado o âmbito "User.Read", "Contactos.Read". Para mais informações, consulte a referência de [permissões do Microsoft Graph](https://developer.microsoft.com/graph/docs/concepts/permissions_reference).

No Android, também é necessário especificar `.WithParentActivityOrWindow`a atividade dos pais utilizando , como mostra, para que o símbolo volte à atividade dos pais após a interação. Se não especificar, é aberta uma exceção `.ExecuteAsync()`ao ligar.

### <a name="specific-optional-parameters-in-msalnet"></a>Parâmetros opcionais específicos em MSAL.NET

#### <a name="withparentactivityorwindow"></a>comparentactivityorwindow

A UI é importante porque é interativa. `AcquireTokenInteractive`tem um parâmetro opcional específico que pode especificar, para plataformas que o suportam, a UI-mãe. Quando utilizado numa aplicação de ambiente de trabalho, `.WithParentActivityOrWindow` tem um tipo diferente, que depende da plataforma.

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

- No .NET Standard, `object` `Activity` o esperado `UIViewController` é no `NSWindow` Android, `IWin32Window` no `IntPr` iOS, no MAC e no Windows.
- No Windows, você `AcquireTokenInteractive` deve ligar a partir do fio UI para que o navegador incorporado obtenha o contexto de sincronização ui apropriado. Não ligar da linha ui pode fazer com que as mensagens não bombeiem corretamente e impossibilitem cenários de impasse com a UI. Uma forma de chamar as Bibliotecas de Autenticação da Microsoft (MSALs) da linha UI se ainda não estiver na linha UI é utilizar o `Dispatcher` no WPF.
- Se estiver a usar wPF, para obter uma janela de `WindowInteropHelper.Handle` um controlo WPF, pode usar a classe. Em seguida, a chamada é`this`de um controlo WPF ( ):

  ```csharp
  result = await app.AcquireTokenInteractive(scopes)
                    .WithParentActivityOrWindow(new WindowInteropHelper(this).Handle)
                    .ExecuteAsync();
  ```

#### <a name="withprompt"></a>ComPrompt

`WithPrompt()`é utilizado para controlar a interatividade com o utilizador, especificando um pedido.

<img src="https://user-images.githubusercontent.com/13203188/53438042-3fb85700-39ff-11e9-9a9e-1ff9874197b3.png" width="25%" />

A classe define as seguintes constantes:

- ``SelectAccount``força o STS a apresentar a caixa de diálogo de seleção de conta que contém contas para as quais o utilizador tem uma sessão. Esta opção é útil quando os desenvolvedores de aplicações querem permitir que os utilizadores escolham entre diferentes identidades. Esta opção leva a ``prompt=select_account`` MSAL a enviar para o fornecedor de identidade. Esta é a opção predefinida. Faz um bom trabalho em proporcionar a melhor experiência possível com base na informação disponível, como conta e presença de uma sessão para o utilizador. Não o mudes a não ser que tenhas boas razões para o fazer.
- ``Consent``permite ao desenvolvedor da aplicação forçar o utilizador a ser solicitado para consentimento, mesmo que o consentimento tenha sido concedido antes. Neste caso, a MSAL envia `prompt=consent` ao fornecedor de identidade. Esta opção pode ser utilizada em algumas aplicações focadas na segurança, onde a governação da organização exige que o utilizador seja apresentado com a caixa de diálogo de consentimento sempre que a aplicação é utilizada.
- ``ForceLogin``permite ao desenvolvedor da aplicação ter o utilizador solicitado para obter credenciais pelo serviço, mesmo que este pedido de utilizador possa não ser necessário. Esta opção pode ser útil para permitir que o utilizador volte a iniciar sessão se a aquisição de um token falhar. Neste caso, a MSAL envia `prompt=login` ao fornecedor de identidade. Por vezes é usado em aplicações focadas na segurança, onde a governação da organização exige que o utilizador reinifique em cada vez que acede a partes específicas de uma aplicação.
- ``Never``(apenas para .NET 4.5 e WinRT) não vai solicitar ao utilizador, mas tenta utilizar o cookie armazenado na vista web incorporada escondida. Para mais informações, consulte as vistas da web em MSAL.NET. Usar esta opção pode falhar. Nesse caso, `AcquireTokenInteractive` abre uma exceção para notificar que é necessária uma interação ui. Vais precisar de `Prompt` usar outro parâmetro.
- ``NoPrompt``não enviará qualquer pedido ao fornecedor de identidade. Esta opção é útil apenas para as políticas de perfil de edição do Azure Ative Directory (Azure AD) B2C. Para mais informações, consulte [as especificidades do Azure AD B2C](https://aka.ms/msal-net-b2c-specificities).

#### <a name="withextrascopetoconsent"></a>ComExtraScopetoConsent

Este modificador é utilizado num cenário avançado em que pretende que o utilizador pré-consente antecipadamente com vários recursos, e não pretende utilizar o consentimento incremental, que normalmente é usado com MSAL.NET/the plataforma de identidade da Microsoft. Para mais informações, consulte [Ter o consentimento do utilizador antecipadamente para vários recursos](scenario-desktop-production.md#have-the-user-consent-upfront-for-several-resources).

```csharp
var result = await app.AcquireTokenInteractive(scopesForCustomerApi)
                     .WithExtraScopeToConsent(scopesForVendorApi)
                     .ExecuteAsync();
```

#### <a name="withcustomwebui"></a>ComCustomWebUi

Um UI web é um mecanismo para invocar um navegador. Este mecanismo pode ser um controlo dedicado do WebBrowser uI ou uma forma de delegar a abertura do navegador.
A MSAL fornece implementações de UI web para a maioria das plataformas, mas existem casos em que você pode querer hospedar o navegador por si mesmo:

- Plataformas que não são explicitamente cobertas pela MSAL, por exemplo, Blazor, Unity e Mono em desktops.
- Você quer uI testar a sua aplicação e usar um navegador automatizado que pode ser usado com Selénio.
- O navegador e a aplicação que executam o MSAL estão em processos separados.

##### <a name="at-a-glance"></a>Num relance

Para isso, dá ao MSAL `start Url`, que precisa de ser exibido num browser de eleição para que o utilizador final possa introduzir itens como o seu nome de utilizador.
Após a autenticação terminar, a sua aplicação `end Url`precisa de ser reposta para a MSAL, que contém um código fornecido pela Azure AD.
O anfitrião `end Url` `redirectUri`de é sempre. Para `end Url`intercetar, faça uma das seguintes coisas:

- Monitorize os redirecionamentos do navegador até `redirect Url` ser atingido.
- Faça o browser redirecionar para um URL, que monitoriza.

##### <a name="withcustomwebui-is-an-extensibility-point"></a>WithCustomWebUi é um ponto de extebilidade

`WithCustomWebUi`é um ponto de exibilidade que pode usar para fornecer o seu próprio UI em aplicações de clientes públicos. Também pode deixar o utilizador passar pelo ponto final /Autorizar o fornecedor de identidade e deixá-lo iniciar sessão e consentir. MSAL.NET pode então resgatar o código de autenticação e obter um símbolo. Por exemplo, é usado no Estúdio Visual para ter aplicações de eletrões (por exemplo, Visual Studio Feedback) fornecem a interação web, mas deixa-o MSAL.NET para fazer a maior parte do trabalho. Também pode usá-lo se quiser fornecer automação ui. Nas aplicações de clientes públicos, MSAL.NET utiliza a norma Proof Key for Code Exchange (PKCE) para garantir que a segurança é respeitada. Só MSAL.NET pode resgatar o código. Para mais informações, consulte [RFC 7636 - Chave de Prova para Troca de Códigos por Clientes Públicos Da OAuth](https://tools.ietf.org/html/rfc7636).

  ```csharp
  using Microsoft.Identity.Client.Extensions;
  ```

##### <a name="use-withcustomwebui"></a>Usar comCustomWebUi

Para `.WithCustomWebUI`utilizar, siga estes passos.

  1. Implemente a interface `ICustomWebUi`. Para mais informações, consulte [este site.](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/blob/053a98d16596be7e9ca1ab916924e5736e341fe8/src/Microsoft.Identity.Client/Extensibility/ICustomWebUI.cs#L32-L70) Implementar `AcquireAuthorizationCodeAsync`um método e aceitar o URL do código de autorização calculado por MSAL.NET. Em seguida, deixe o utilizador passar pela interação com o fornecedor de identidade e devolver o URL pelo qual o fornecedor de identidade teria chamado a sua implementação de volta juntamente com o código de autorização. Se tiver problemas, a sua `MsalExtensionException` implementação deve abrir uma exceção para cooperar bem com a MSAL.
  2. Na `AcquireTokenInteractive` sua chamada, `.WithCustomUI()` utilize o modificador passando a instância da sua UI web personalizada.

     ```csharp
     result = await app.AcquireTokenInteractive(scopes)
                       .WithCustomWebUi(yourCustomWebUI)
                       .ExecuteAsync();
     ```

##### <a name="examples-of-implementation-of-icustomwebui-in-test-automation-seleniumwebui"></a>Exemplos de implementação do ICustomWebUi na automatização de testes: SelénioWebUI

A equipa MSAL.NET reescreveu os testes de UI para usar este mecanismo de extebilidade. Se estiver interessado, veja a classe [SeleniumWebUI](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/blob/053a98d16596be7e9ca1ab916924e5736e341fe8/tests/Microsoft.Identity.Test.Integration/Infrastructure/SeleniumWebUI.cs#L15-L160) no código fonte MSAL.NET.

##### <a name="provide-a-great-experience-with-systemwebviewoptions"></a>Proporcionar uma grande experiência com SystemWebViewOptions

A partir de [`SystemWebViewOptions`](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.systemwebviewoptions?view=azure-dotnet)MSAL.NET 4.1, pode especificar:

- O URI para`BrowserRedirectError`ir a ( )`HtmlMessageError`ou o fragmento HTML para exibir ( ) em caso de erros de entrada ou consentimento no navegador web do sistema.
- O URI para`BrowserRedirectSuccess`ir a ( )`HtmlMessageSuccess`ou o fragmento HTML para exibir ( ) em caso de entrada ou consentimento bem sucedido.
- A ação a executar para iniciar o navegador do sistema. Pode fornecer a sua própria `OpenBrowserAsync` implementação definindo o delegado. A classe também fornece uma implementação `OpenWithEdgeBrowserAsync` padrão `OpenWithChromeEdgeBrowserAsync` para dois navegadores: e para Microsoft Edge e [Microsoft Edge em Chromium](https://www.windowscentral.com/faq-edge-chromium), respectivamente.

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

Para saber mais sobre todos os `AcquireTokenInteractive`outros parâmetros opcionais para , consulte [AcquireTokenInteractiveParameterBuilder](/dotnet/api/microsoft.identity.client.acquiretokeninteractiveparameterbuilder?view=azure-dotnet-preview#methods).

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

# <a name="python"></a>[Python](#tab/python)

A MSAL Python não fornece diretamente um método de aquisição interativo. Em vez disso, requer que o pedido envie um pedido de autorização na sua implementação do fluxo de interação do utilizador para obter um código de autorização. Este código pode então `acquire_token_by_authorization_code` ser passado ao método para obter o símbolo.

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

# <a name="macos"></a>[MacOS](#tab/macOS)

### <a name="in-msal-for-ios-and-macos"></a>Em MSAL para iOS e macOS

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

Swift:

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
---

## <a name="integrated-windows-authentication"></a>Autenticação Integrada do Windows.

Para iniciar sessão num utilizador de domínio numa máquina de adões de domínio ou Azure, utilize a Autenticação Integrada do Windows (IWA).

### <a name="constraints"></a>Restrições

- A Autenticação Integrada do Windows é utilizável apenas para utilizadores *federados+* ou seja, utilizadores criados em Diretório Ativo e apoiados pela Azure AD. Os utilizadores criados diretamente em Azure AD sem suporte ative directory, conhecidos como utilizadores *geridos,* não podem usar este fluxo de autenticação. Esta limitação não afeta o nome de utilizador e o fluxo de palavra-passe.
- O IWA é para aplicações escritas para as plataformas .NET Framework, .NET Core e Universal Windows Platform (UWP).
- A IWA não contorna a autenticação multifactor (MFA). Se o MFA estiver configurado, o IWA pode falhar se for necessário um desafio MFA, porque o MFA requer interação do utilizador.
  > [!NOTE]
  > Este é complicado. O IWA não é interativo, mas o MFA requer interatividade do utilizador. Não se controla quando o fornecedor de identidade pede que o MFA seja executado, o administrador do inquilino faz. A partir das nossas observações, o MFA é necessário quando se inscreve de um país diferente, quando não está ligado via VPN a uma rede corporativa, e às vezes mesmo quando conectado via VPN. Não espere um conjunto de regras determinísticas. A Azure AD utiliza AI para aprender continuamente se o MFA é necessário. Recue para um pedido do utilizador como autenticação interativa ou fluxo de código do dispositivo se o IWA falhar.

- A autoridade aprovada `PublicClientApplicationBuilder` tem de ser:
  - Arrendado do `https://login.microsoftonline.com/{tenant}/`formulário, `tenant` onde está o GUID que representa o ID do arrendatário ou um domínio associado ao inquilino.
  - Para qualquer trabalho e `https://login.microsoftonline.com/organizations/`contas escolares: .
  - As contas pessoais da Microsoft não são suportadas. Não pode usar inquilinos comuns ou /consumidores.

- Porque a autenticação integrada do Windows é um fluxo silencioso:
  - O utilizador da sua aplicação deve ter previamente consentido em utilizar a aplicação.
  - Ou, o administrador do inquilino deve ter previamente consentido todos os utilizadores do arrendatário a utilizar em aplicação.
  - Por outras palavras:
    - Ou você, como desenvolvedor, selecionou o botão **Grant** no portal Azure para si mesmo.
    - Ou, um administrador de inquilino selecionou o **consentimento de administração grant/revogação para o** botão {domínio do inquilino} no separador de **permissões DaPI** do registo para o pedido. Para mais informações, consulte [Adicionar permissões para aceder a APIs web](https://docs.microsoft.com/azure/active-directory/develop/quickstart-configure-app-access-web-apis#add-permissions-to-access-web-apis).
    - Ou, forneceu uma forma de os utilizadores consentirem com a aplicação. Para mais informações, consulte [Solicitando o consentimento individual](https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent#requesting-individual-user-consent)do utilizador .
    - Ou forneceu uma maneira para o administrador inquilino consentir com o pedido. Para mais informações, consulte o [consentimento do Administrador.](https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent#requesting-consent-for-an-entire-tenant)

- Este fluxo está ativado para aplicações de ambiente de trabalho .NET, .NET Core e UWP.

Para obter mais informações sobre o consentimento, consulte [as permissões e consentimento da plataforma de identidade da Microsoft.](https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent)

### <a name="learn-how-to-use-it"></a>Aprenda a usá-lo

# <a name="net"></a>[.NET](#tab/dotnet)

Na MSAL.NET, tem de usar:

```csharp
AcquireTokenByIntegratedWindowsAuth(IEnumerable<string> scopes)
```

Normalmente só precisa de`scopes`um parâmetro . Dependendo da forma como o administrador do Windows configura as políticas, as aplicações na sua máquina Windows podem não ser autorizadas a procurar o utilizador inscrito. Nesse caso, utilize um `.WithUsername()`segundo método, e passe em nome de utilizador do utilizador inscrito `joe@contoso.com`como formato UPN, por exemplo, . No .NET Core, apenas a sobrecarga assumindo o nome de utilizador está disponível porque a plataforma .NET Core não pode pedir o nome de utilizador para o OS.

A amostra seguinte apresenta o caso mais atual, com explicações sobre o tipo de exceções que pode obter e as suas atenuações.

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

Para a lista de possíveis modificadores na AcquireTokenByIntegratedWindowsAuthentication, consulte [AcquireTokenByIntegratedWindowsAuthParameterBuilder](/dotnet/api/microsoft.identity.client.acquiretokenbyintegratedwindowsauthparameterbuilder?view=azure-dotnet-preview#methods).

# <a name="java"></a>[Java](#tab/java)

Este extrato provém das amostras de v [da MSAL Java.](https://github.com/AzureAD/microsoft-authentication-library-for-java/blob/dev/src/samples/public-client/)

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

# <a name="python"></a>[Python](#tab/python)

Este fluxo ainda não é suportado na MSAL Python.

# <a name="macos"></a>[MacOS](#tab/macOS)

Este fluxo não se aplica ao MacOS.

---

## <a name="username-and-password"></a>Nome de utilizador e palavra-passe

Também pode adquirir um símbolo fornecendo o nome de utilizador e a palavra-passe. Este fluxo é limitado e não é recomendado, mas ainda há casos de uso onde é necessário.

### <a name="this-flow-isnt-recommended"></a>Este fluxo não é recomendado

Este fluxo não é *recomendado* porque ter a sua aplicação a pedir a um utilizador a sua palavra-passe não é seguro. Para mais informações, veja [Qual é a solução para o problema crescente das palavras-passe?](https://news.microsoft.com/features/whats-solution-growing-problem-passwords-says-microsoft/) O fluxo preferido para adquirir um símbolo silenciosamente nas máquinas unidas ao domínio windows é a [Autenticação Integrada do Windows](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Integrated-Windows-Authentication). Também pode utilizar o [fluxo de código](https://aka.ms/msal-net-device-code-flow)do dispositivo .

> [!NOTE]
> Usar um nome de utilizador e senha é útil em alguns casos, como cenários de DevOps. Mas se quiser usar um nome de utilizador e uma senha em cenários interativos onde fornece o seu próprio UI, pense em como se afastar dele. Ao usar um nome de utilizador e uma senha, está a abdicar de uma série de coisas:
>
> - Princípios fundamentais da identidade moderna. Uma palavra-passe pode ser phished e reproduzida porque um segredo partilhado pode ser intercetado. É incompatível com sem palavras-passe.
> - Os utilizadores que precisam de fazer MFA não podem entrar porque não há interação.
> - Os utilizadores não podem fazer um único sinal (SSO).

### <a name="constraints"></a>Restrições

Aplicam-se igualmente os seguintes constrangimentos:

- O nome de utilizador e o fluxo de palavra-passe não são compatíveis com acesso condicional e autenticação multifactor. Como consequência, se a sua aplicação for executado num inquilino da AD Azure onde o administrador do inquilino requer autenticação multifactor, não pode utilizar este fluxo. Muitas organizações fazem isso.
- Funciona apenas para trabalho e contas escolares (não MSA).
- O fluxo está disponível em .NET desktop e .NET Core, mas não no UWP.

### <a name="b2c-specifics"></a>Especificidades b2C

Para mais informações, consulte credenciais de [palavra-passe do proprietário de recursos (ROPC) com B2C](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/AAD-B2C-specifics#resource-owner-password-credentials-ropc-with-b2c).

### <a name="use-it"></a>Use-o

# <a name="net"></a>[.NET](#tab/dotnet)

`IPublicClientApplication`contém o `AcquireTokenByUsernamePassword`método .

A amostra seguinte apresenta um caso simplificado.

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

A amostra seguinte apresenta o caso mais atual, com explicações sobre o tipo de exceções que pode obter e as suas atenuações.

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
                    .ExecuteAync();
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

Para obter mais informações sobre todos os `AcquireTokenByUsernamePassword`modificadores que possam ser aplicados, consulte [AcquireTokenByUsernamePasswordParameterBuilder](/dotnet/api/microsoft.identity.client.acquiretokenbyusernamepasswordparameterbuilder?view=azure-dotnet-preview#methods).

# <a name="java"></a>[Java](#tab/java)

O seguinte extrato provém das amostras de v [da MSAL Java.](https://github.com/AzureAD/microsoft-authentication-library-for-java/blob/dev/src/samples/public-client/)

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

# <a name="python"></a>[Python](#tab/python)

Este extrato provém das amostras de v da [MSAL Python.](https://github.com/AzureAD/microsoft-authentication-library-for-python/blob/dev/sample/)

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

# <a name="macos"></a>[MacOS](#tab/macOS)

Este fluxo não é suportado na MSAL para macOS.

---

## <a name="command-line-tool-without-a-web-browser"></a>Ferramenta de linha de comando sem um navegador web

### <a name="device-code-flow"></a>Fluxo de código do dispositivo

Se estiver a escrever uma ferramenta de linha de comando que não tenha controlos web, e não pode ou não quer utilizar os fluxos anteriores, tem de utilizar o fluxo de código do dispositivo.

A autenticação interativa com a AD Azure requer um navegador web. Para mais informações, consulte [o Uso dos navegadores web](https://aka.ms/msal-net-uses-web-browser). Para autenticar os utilizadores em dispositivos ou sistemas operativos que não forneçam um navegador web, o fluxo de código do dispositivo permite ao utilizador utilizar outro dispositivo, como um computador ou um telemóvel para iniciar sessão interativa. Ao utilizar o fluxo de código do dispositivo, a aplicação obtém fichas através de um processo em duas etapas projetado para estes dispositivos ou OSes. Exemplos de tais aplicações são aplicações que funcionam em iOT ou ferramentas de linha de comando (CLI). A ideia é que:

1. Sempre que for necessária a autenticação do utilizador, a aplicação fornece um código para o utilizador. Pede-se ao utilizador que utilize outro dispositivo, como um smartphone ligado à `https://microsoft.com/devicelogin`Internet, para ir a um URL, por exemplo, . Em seguida, o utilizador é solicitado a introduzir o código. Tal, a página web leva o utilizador através de uma experiência de autenticação normal, que inclui solicitações de consentimento e autenticação multifactor, se necessário.

2. Após a autenticação bem sucedida, a aplicação de linha de comando recebe as fichas necessárias através de um canal traseiro e usa-as para executar as chamadas aPi web que necessita.

### <a name="use-it"></a>Use-o

# <a name="net"></a>[.NET](#tab/dotnet)

`IPublicClientApplication`contém um `AcquireTokenWithDeviceCode`método chamado .

```csharp
 AcquireTokenWithDeviceCode(IEnumerable<string> scopes,
                            Func<DeviceCodeResult, Task> deviceCodeResultCallback)
```

Este método toma como parâmetros:

- Para `scopes` pedir um sinal de acesso.
- Uma chamada que recebe `DeviceCodeResult`o .

  ![Propriedades do DeviceCodeResult](https://user-images.githubusercontent.com/13203188/56024968-7af1b980-5d11-11e9-84c2-5be2ef306dc5.png)

O seguinte código de amostra apresenta o caso mais atual, com explicações sobre o tipo de exceções que você pode obter e sua mitigação.

```csharp
private const string ClientId = "<client_guid>";
private const string Authority = "https://login.microsoftonline.com/contoso.com";
private readonly string[] Scopes = new string[] { "user.read" };

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
        return await pca.AcquireTokenSilent(Scopes, accounts.FirstOrDefault())
            .ExecuteAsync();
    }
    catch (MsalUiRequiredException ex)
    {
        // No token found in the cache or AAD insists that a form interactive auth is required (e.g. the tenant admin turned on MFA)
        // If you want to provide a more complex user experience, check out ex.Classification

        return await AcquireByDeviceCodeAsync(pca);
    }         
}

private async Task<AuthenticationResult> AcquireByDeviceCodeAsync(IPublicClientApplication pca)
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

Este extrato provém das amostras de v [da MSAL Java.](https://github.com/AzureAD/microsoft-authentication-library-for-java/blob/dev/src/samples/public-client/)

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

# <a name="python"></a>[Python](#tab/python)

Este extrato provém das amostras de v da [MSAL Python.](https://github.com/AzureAD/microsoft-authentication-library-for-python/blob/dev/sample/)

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

# <a name="macos"></a>[MacOS](#tab/macOS)

Este fluxo não se aplica ao MacOS.

---

## <a name="file-based-token-cache"></a>Cache de fichas baseada em ficheiros

Em MSAL.NET, uma cache de ficha sinuosa é fornecida por padrão.

### <a name="serialization-is-customizable-in-windows-desktop-apps-and-web-apps-or-web-apis"></a>A serialização é personalizável em aplicações de desktop do Windows e aplicações web ou APIs web

No caso de .NET Framework e .NET Core, se não fizer nada extra, a cache token em memória dura a duração da aplicação. Para entender por que razão a serialização não é fornecida fora da caixa, lembre-se que as aplicações mSAL .NET web ou .NET Core podem ser aplicações de consola ou Windows (que teriam acesso ao sistema de ficheiros), *mas também* aplicações web ou APIs web. Estas aplicações web e APIs web podem usar alguns mecanismos específicos de cache, como bases de dados, caches distribuídos e caches Redis. Para ter uma aplicação de cache token persistente em .NET desktop ou .NET Core, você precisará personalizar a serialização.

As classes e interfaces envolvidas na serialização de cache token são os seguintes tipos:

- ``ITokenCache``, que define eventos para subscrever pedidos de serialização de cache simbólicos, e métodos para serializar ou desserializar a cache em vários formatos (ADAL v3.0, MSAL 2.x e MSAL 3.x = ADAL v5.0).
- ``TokenCacheCallback``é um callback passado para os eventos para que você possa lidar com a serialização. Serão chamados com argumentos ``TokenCacheNotificationArgs``de tipo.
- ``TokenCacheNotificationArgs``apenas fornece ``ClientId`` a aplicação e uma referência ao utilizador para o qual o símbolo está disponível.

  ![Diagrama de serialização de cache de token](https://user-images.githubusercontent.com/13203188/56027172-d58d1480-5d15-11e9-8ada-c0292f1800b3.png)

> [!IMPORTANT]
> MSAL.NET cria caches simbólicos para si `IToken` e fornece-lhe a `UserTokenCache` `AppTokenCache` cache quando chama de uma aplicação e propriedades. Não é suposto implementares a interface sozinho. A sua responsabilidade, quando implementa uma cacheização personalizada, é:
>
> - `BeforeAccess` Reaja `AfterAccess` e eventos, ou o seu homólogo *async.* O`BeforeAccess` delegado é responsável por desserializar a cache. O `AfterAccess` delegado é responsável por serializar a cache.
> - Compreenda que parte destes eventos armazenam ou carregam bolhas, que são transmitidas através do argumento do evento para qualquer armazenamento que você quiser.

As estratégias são diferentes dependendo de se estiver a escrever uma serialização de cache simbólica para uma aplicação de cliente público, como um ambiente de trabalho, ou uma aplicação confidencial do cliente, como uma aplicação web ou Web API ou uma aplicação daemon.

Desde mSAL v2.x, você tem várias opções. A sua escolha depende se pretende serializar a cache apenas para o formato MSAL.NET, que é uma cache de formato unificado que é comum com a MSAL mas também através das plataformas. Ou, também pode querer apoiar a cacheização do [legado](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Token-cache-serialization) simbólico da ADAL v3.

A personalização da serialização de cache token para partilhar o estado SSO entre ADAL.NET 3.x, ADAL.NET 5.x e MSAL.NET é explicada em parte da amostra [active-directório-dotnet-v1-to-v2](https://github.com/Azure-Samples/active-directory-dotnet-v1-to-v2).

### <a name="simple-token-cache-serialization-msal-only"></a>Simples serialização de cache de token (apenas MSAL)

O exemplo seguinte é uma implementação ingénua da serialização personalizada de um cache simbólico para aplicações de ambiente de trabalho. Aqui, o cache token do utilizador encontra-se num ficheiro na mesma pasta que a aplicação.

Depois de construir a aplicação, permite ``TokenCacheHelper.EnableSerialization()`` a serialização ligando e passando a aplicação. `UserTokenCache`

```csharp
app = PublicClientApplicationBuilder.Create(ClientId)
    .Build();
TokenCacheHelper.EnableSerialization(app.UserTokenCache);
```

Esta classe de ajudante parece o seguinte código snippet:

```csharp
static class TokenCacheHelper
 {
  public static void EnableSerialization(ITokenCache tokenCache)
  {
   tokenCache.SetBeforeAccess(BeforeAccessNotification);
   tokenCache.SetAfterAccess(AfterAccessNotification);
  }

  /// <summary>
  /// Path to the token cache
  /// </summary>
  public static readonly string CacheFilePath = System.Reflection.Assembly.GetExecutingAssembly().Location + ".msalcache.bin3";

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
> Isenção de responsabilidade: A biblioteca Microsoft.Identity.Client.Extensions.Msal é uma extensão sobre MSAL.NET. As aulas nestas bibliotecas podem entrar em MSAL.NET no futuro, como é o caso ou com mudanças de rutura.

### <a name="dual-token-cache-serialization-msal-unified-cache--adal-v3"></a>Serialização de cache de símbolo duplo (cache unificada MSAL + ADAL v3)

É melhor implementar a serialização de cache simbólica com o formato de cache unificado. Este formato é comum a ADAL.NET 4.x e MSAL.NET 2.x, e com outros MSALs da mesma geração ou mais, na mesma plataforma. Inspire-se no seguinte código:

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

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Ligue para uma Web API da aplicação de desktop](scenario-desktop-call-api.md)
