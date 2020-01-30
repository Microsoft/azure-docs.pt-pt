---
title: Adquirir um token para chamar uma API da Web (aplicativo de desktop) | Azure
titleSuffix: Microsoft identity platform
description: Saiba como criar um aplicativo de área de trabalho que chama APIs da Web para adquirir um token para o aplicativo
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
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 50ac62ded92b69f44324f4f9c5eacee939159449
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/29/2020
ms.locfileid: "76834132"
---
# <a name="desktop-app-that-calls-web-apis-acquire-a-token"></a>Aplicativo de área de trabalho que chama APIs da Web: adquirir um token

Depois de criar uma instância do aplicativo cliente público, você a usará para adquirir um token que será usado para chamar uma API da Web.

## <a name="recommended-pattern"></a>Padrão recomendado

A API da Web é definida por sua `scopes`. Qualquer que seja a experiência que você fornecer em seu aplicativo, o padrão a ser usado é:

- Tente sistematicamente obter um token do cache de token chamando `AcquireTokenSilent`.
- Se essa chamada falhar, use o fluxo de `AcquireToken` que você deseja usar, que é representado aqui por `AcquireTokenXX`.

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

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

# <a name="javatabjava"></a>[Java](#tab/java)

```java
CompletableFuture<IAuthenticationResult> future = app.acquireToken(parameters);

future.handle((res, ex) -> {
    if(ex != null) {
        System.out.println("Oops! We have an exception - " + ex.getMessage());
        return "Unknown!";
    }

    Collection<IAccount> accounts = app.getAccounts().join();

    CompletableFuture<IAuthenticationResult> future1;
    try {
        future1 = app.acquireTokenSilently
                (SilentParameters.builder(Collections.singleton(TestData.GRAPH_DEFAULT_SCOPE),
                        accounts.iterator().next())
                        .forceRefresh(true)
                        .build());

    } catch (MalformedURLException e) {
        e.printStackTrace();
        throw new RuntimeException();
    }

    future1.join();
    IAccount account = app.getAccounts().join().iterator().next();
    app.removeAccount(account).join();

    return res;
}).join();
```

# <a name="pythontabpython"></a>[Python](#tab/python)

```Python
result = None

# Firstly, check the cache to see if this end user has signed in before
accounts = app.get_accounts(username=config["username"])
if accounts:
    result = app.acquire_token_silent(config["scope"], account=accounts[0])

if not result:
    result = app.acquire_token_by_xxx(scopes=config["scope"])
```

# <a name="macostabmacos"></a>[MacOS](#tab/macOS)

### <a name="in-msal-for-ios-and-macos"></a>No MSAL para iOS e macOS

Objective-C:

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
Swift

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

Aqui estão as várias maneiras de adquirir tokens em um aplicativo de área de trabalho.

## <a name="acquire-a-token-interactively"></a>Adquirir um token interativamente

O exemplo a seguir mostra o código mínimo para obter um token interativamente para ler o perfil do usuário com Microsoft Graph.

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)
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

`AcquireTokenInteractive` tem apenas um parâmetro obrigatório, ``scopes``, que contém uma enumeração de cadeias de caracteres que definem os escopos para os quais um token é necessário. Se o token for para Microsoft Graph, os escopos necessários poderão ser encontrados na referência de API de cada API Microsoft Graph na seção chamada "Permissions". Por exemplo, para [listar os contatos do usuário](https://developer.microsoft.com/graph/docs/api-reference/v1.0/api/user_list_contacts), o escopo "User. Read", "Contacts. Read" deve ser usado. Para obter mais informações, consulte [referência de permissões de Microsoft Graph](https://developer.microsoft.com/graph/docs/concepts/permissions_reference).

No Android, você também precisa especificar a atividade pai usando `.WithParentActivityOrWindow`, conforme mostrado, para que o token volte para essa atividade pai após a interação. Se você não especificá-lo, uma exceção será lançada ao chamar `.ExecuteAsync()`.

### <a name="specific-optional-parameters-in-msalnet"></a>Parâmetros opcionais específicos em MSAL.NET

#### <a name="withparentactivityorwindow"></a>WithParentActivityOrWindow

A interface do usuário é importante porque é interativa. `AcquireTokenInteractive` tem um parâmetro opcional específico que pode especificar, para plataformas que dão suporte a ele, a interface do usuário pai. Quando usado em um aplicativo de área de trabalho, `.WithParentActivityOrWindow` tem um tipo diferente, que depende da plataforma.

```csharp
// net45
WithParentActivityOrWindow(IntPtr windowPtr)
WithParentActivityOrWindow(IWin32Window window)

// Mac
WithParentActivityOrWindow(NSWindow window)

// .Net Standard (this will be on all platforms at runtime, but only on NetStandard at build time)
WithParentActivityOrWindow(object parent).
```

Comentários

- No .NET Standard, o `object` esperado é `Activity` no Android, `UIViewController` no iOS, `NSWindow` no MAC e `IWin32Window` ou `IntPr` no Windows.
- No Windows, você deve chamar `AcquireTokenInteractive` do thread da interface do usuário para que o navegador incorporado obtenha o contexto de sincronização da interface do usuário apropriado. Não chamar a partir do thread de interface do usuário pode fazer com que as mensagens não sejam bombeadas corretamente e cenários de deadlock com a interface do usuário. Uma maneira de chamar MSALs (bibliotecas de autenticação da Microsoft) do thread da interface do usuário se você já não estiver no thread da interface do usuário é usar o `Dispatcher` no WPF.
- Se você estiver usando o WPF, para obter uma janela de um controle WPF, poderá usar a classe `WindowInteropHelper.Handle`. Em seguida, a chamada é de um controle WPF (`this`):

  ```csharp
  result = await app.AcquireTokenInteractive(scopes)
                    .WithParentActivityOrWindow(new WindowInteropHelper(this).Handle)
                    .ExecuteAsync();
  ```

#### <a name="withprompt"></a>WithPrompt

`WithPrompt()` é usado para controlar a interatividade com o usuário, especificando um prompt.

<img src="https://user-images.githubusercontent.com/13203188/53438042-3fb85700-39ff-11e9-9a9e-1ff9874197b3.png" width="25%" />

A classe define as seguintes constantes:

- ``SelectAccount`` força o STS a apresentar a caixa de diálogo de seleção de conta que contém contas para as quais o usuário tem uma sessão. Essa opção é útil quando os desenvolvedores de aplicativos desejam permitir que os usuários escolham entre diferentes identidades. Esta opção leva a MSAL a enviar ``prompt=select_account`` ao fornecedor de identidade. Esta opção é o padrão. Ele faz um bom trabalho de fornecer a melhor experiência possível com base nas informações disponíveis, como a conta e a presença de uma sessão para o usuário. Não a altere, a menos que você tenha um bom motivo para fazê-lo.
- ``Consent`` permite que o desenvolvedor do aplicativo Force o consentimento do usuário, mesmo que o consentimento tenha sido concedido antes. Nesse caso, o MSAL envia `prompt=consent` para o provedor de identidade. Essa opção pode ser usada em alguns aplicativos focados em segurança em que o controle da organização exige que o usuário tenha a caixa de diálogo de consentimento sempre que o aplicativo for usado.
- ``ForceLogin`` permite que o desenvolvedor do aplicativo solicite as credenciais ao usuário pelo serviço, mesmo que esse prompt de usuário não seja necessário. Essa opção pode ser útil para permitir que o usuário entre novamente se a aquisição de um token falhar. Nesse caso, o MSAL envia `prompt=login` para o provedor de identidade. Às vezes, ele é usado em aplicativos com foco em segurança, em que o controle da organização exige que o usuário se reconecte a cada vez que acessam partes específicas de um aplicativo.
- ``Never`` (somente para .NET 4,5 e WinRT) não solicitará o usuário, mas tentará usar o cookie armazenado na exibição da Web oculta inserida. Para obter mais informações, consulte exibições da Web no MSAL.NET. O uso dessa opção pode falhar. Nesse caso, `AcquireTokenInteractive` gera uma exceção para notificar que uma interação de interface do usuário é necessária. Você precisará usar outro parâmetro `Prompt`.
- ``NoPrompt`` não enviará nenhum prompt para o provedor de identidade. Essa opção só é útil para as políticas de perfil de edição do Azure Active Directory (Azure AD) B2C. Para obter mais informações, consulte [Azure ad B2C específicos](https://aka.ms/msal-net-b2c-specificities).

#### <a name="withextrascopetoconsent"></a>WithExtraScopeToConsent

Esse modificador é usado em um cenário avançado em que você deseja que o usuário concorde com vários recursos antecipadamente e não deseja usar o consentimento incremental, que normalmente é usado com a plataforma de identidade da Microsoft MSAL.NET/the. Para obter mais informações, consulte [ter o consentimento de usuário antecipado para vários recursos](scenario-desktop-production.md#have-the-user-consent-upfront-for-several-resources).

```csharp
var result = await app.AcquireTokenInteractive(scopesForCustomerApi)
                     .WithExtraScopeToConsent(scopesForVendorApi)
                     .ExecuteAsync();
```

#### <a name="withcustomwebui"></a>WithCustomWebUi

Uma interface do usuário da Web é um mecanismo para invocar um navegador. Esse mecanismo pode ser um controle WebBrowser de interface do usuário dedicada ou uma maneira de delegar a abertura do navegador.
O MSAL fornece implementações de interface do usuário da Web para a maioria das plataformas, mas há casos em que você pode desejar hospedar o navegador por conta própria:

- Plataformas que não são explicitamente cobertas por MSAL, por exemplo, mais incrivelmente, Unity e mono em desktops.
- Você deseja testar o aplicativo na interface do usuário e usar um navegador automatizado que possa ser usado com o Selenium.
- O navegador e o aplicativo que executa o MSAL estão em processos separados.

##### <a name="at-a-glance"></a>Síntese

Para fazer isso, você dá ao MSAL `start Url`, que precisa ser exibido em um navegador de escolha para que o usuário final possa inserir itens como seu nome de usuário.
Após a conclusão da autenticação, seu aplicativo precisa passar de volta para o MSAL `end Url`, que contém um código fornecido pelo Azure AD.
O host de `end Url` é sempre `redirectUri`. Para interceptar `end Url`, siga um destes procedimentos:

- Monitore redirecionamentos de navegador até que `redirect Url` seja atingido.
- Fazer com que o navegador Redirecione para uma URL, que você monitora.

##### <a name="withcustomwebui-is-an-extensibility-point"></a>WithCustomWebUi é um ponto de extensibilidade

`WithCustomWebUi` é um ponto de extensibilidade que você pode usar para fornecer sua própria interface do usuário em aplicativos cliente públicos. Você também pode permitir que o usuário passe pelo ponto de extremidade/Authorize do provedor de identidade e permita que eles se conectem e consentim. O MSAL.NET pode então resgatar o código de autenticação e obter um token. Por exemplo, ele é usado no Visual Studio para que os aplicativos elétrons (por exemplo, comentários do Visual Studio) forneçam a interação com a Web, mas deixe que o MSAL.NET faça a maior parte do trabalho. Você também pode usá-lo se desejar fornecer automação de interface do usuário. Em aplicativos cliente públicos, o MSAL.NET usa a chave de prova para o padrão de troca de código (PKCE) para garantir que a segurança seja respeitada. Somente MSAL.NET pode resgatar o código. Para obter mais informações, consulte [a chave de prova da RFC 7636 para troca de código por clientes públicos do OAuth](https://tools.ietf.org/html/rfc7636).

  ```csharp
  using Microsoft.Identity.Client.Extensions;
  ```

##### <a name="use-withcustomwebui"></a>Usar WithCustomWebUi

Para usar `.WithCustomWebUI`, siga estas etapas.

  1. Implemente a interface `ICustomWebUi`. Para obter mais informações, consulte [este site](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/blob/053a98d16596be7e9ca1ab916924e5736e341fe8/src/Microsoft.Identity.Client/Extensibility/ICustomWebUI.cs#L32-L70). Implemente um método `AcquireAuthorizationCodeAsync`e aceite a URL do código de autorização calculada por MSAL.NET. Em seguida, permita que o usuário percorra a interação com o provedor de identidade e retorne a URL pela qual o provedor de identidade teria chamado sua implementação novamente junto com o código de autorização. Se você tiver problemas, sua implementação deverá lançar uma exceção `MsalExtensionException` para cooperar com MSAL.
  2. Em sua chamada de `AcquireTokenInteractive`, use o modificador `.WithCustomUI()` passando a instância da interface do usuário da Web personalizada.

     ```csharp
     result = await app.AcquireTokenInteractive(scopes)
                       .WithCustomWebUi(yourCustomWebUI)
                       .ExecuteAsync();
     ```

##### <a name="examples-of-implementation-of-icustomwebui-in-test-automation-seleniumwebui"></a>Exemplos de implementação de ICustomWebUi na automação de teste: SeleniumWebUI

A equipe do MSAL.NET reescreveu os testes da interface do usuário para usar esse mecanismo de extensibilidade. Se estiver interessado, examine a classe [SeleniumWebUI](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/blob/053a98d16596be7e9ca1ab916924e5736e341fe8/tests/Microsoft.Identity.Test.Integration/Infrastructure/SeleniumWebUI.cs#L15-L160) no código-fonte MSAL.net.

##### <a name="provide-a-great-experience-with-systemwebviewoptions"></a>Fornecer uma ótima experiência com o SystemWebViewOptions

No MSAL.NET 4,1 [`SystemWebViewOptions`](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.systemwebviewoptions?view=azure-dotnet), você pode especificar:

- O URI para acessar (`BrowserRedirectError`) ou o fragmento HTML a ser exibido (`HtmlMessageError`) em caso de erros de logon ou de consentimento no navegador da Web do sistema.
- O URI a ser acessado (`BrowserRedirectSuccess`) ou o fragmento HTML a ser exibido (`HtmlMessageSuccess`) no caso de uma entrada ou consentimento bem-sucedido.
- a ação a ser executada para iniciar o navegador do sistema. Você pode fornecer sua própria implementação definindo o `OpenBrowserAsync` delegado. A classe também fornece uma implementação padrão para dois navegadores: `OpenWithEdgeBrowserAsync` e `OpenWithChromeEdgeBrowserAsync` para Microsoft Edge e [Microsoft Edge em Chromium](https://www.windowscentral.com/faq-edge-chromium), respectivamente.

Para usar essa estrutura, escreva algo semelhante ao exemplo a seguir:

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

Para saber mais sobre todos os outros parâmetros opcionais para `AcquireTokenInteractive`, consulte [AcquireTokenInteractiveParameterBuilder](/dotnet/api/microsoft.identity.client.acquiretokeninteractiveparameterbuilder?view=azure-dotnet-preview#methods).

# <a name="javatabjava"></a>[Java](#tab/java)

O MSAL Java não fornece um método de token de aquisição interativo diretamente. Em vez disso, ele requer que o aplicativo envie uma solicitação de autorização em sua implementação do fluxo de interação do usuário para obter um código de autorização. Esse código pode então ser passado para o método `acquireToken` para obter o token.

```java
AuthorizationCodeParameters parameters =  AuthorizationCodeParameters.builder(
                authorizationCode, redirectUri)
                .build();
CompletableFuture<IAuthenticationResult> future = app.acquireToken(parameters);

future.handle((res, ex) -> {
    if(ex != null) {
        System.out.println("Oops! We have an exception - " + ex.getMessage());
        return "Unknown!";
    }

    Collection<IAccount> accounts = app.getAccounts().join();

    CompletableFuture<IAuthenticationResult> future1;
    try {
        future1 = app.acquireTokenSilently
                (SilentParameters.builder(Collections.singleton(TestData.GRAPH_DEFAULT_SCOPE),
                        accounts.iterator().next())
                        .forceRefresh(true)
                        .build());

    } catch (MalformedURLException e) {
        e.printStackTrace();
        throw new RuntimeException();
    }

    future1.join();
    IAccount account = app.getAccounts().join().iterator().next();
    app.removeAccount(account).join();

    return res;
}).join();
```

# <a name="pythontabpython"></a>[Python](#tab/python)

O Python MSAL não fornece um método de token de aquisição interativo diretamente. Em vez disso, ele requer que o aplicativo envie uma solicitação de autorização em sua implementação do fluxo de interação do usuário para obter um código de autorização. Este código pode então ser passado para o método `acquire_token_by_authorization_code` para obter o símbolo.

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

# <a name="macostabmacos"></a>[MacOS](#tab/macOS)

### <a name="in-msal-for-ios-and-macos"></a>No MSAL para iOS e macOS

Objective-C:

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

Swift

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

## <a name="integrated-windows-authentication"></a>Autenticação Integrada do Windows

Para conectar um usuário de domínio em um domínio ou computador ingressado no Azure AD, use a autenticação integrada do Windows (IWA).

### <a name="constraints"></a>Restrições

- A autenticação integrada do Windows é utilizável somente para usuários *federados e +* , ou seja, os usuários criados no Active Directory e apoiados pelo Azure AD. Os usuários criados diretamente no Azure AD sem Active Directory backup, conhecidos como usuários *gerenciados* , não podem usar esse fluxo de autenticação. Essa limitação não afeta o fluxo de nome de usuário e senha.
- O IWA é para aplicativos escritos para as plataformas .NET Framework, .NET Core e Plataforma Universal do Windows (UWP).
- IWA não ignora a MFA (autenticação multifator). Se a MFA estiver configurada, IWA poderá falhar se um desafio de MFA for necessário, pois a MFA exige a interação do usuário.
  > [!NOTE]
  > Essa é uma complicada. IWA é não interativo, mas a MFA requer interatividade do usuário. Você não controla quando o provedor de identidade solicita que a MFA seja executada, o administrador de locatários faz. Em nossas observações, a MFA é necessária quando você entra em um país diferente, quando não conectada via VPN a uma rede corporativa e, às vezes, mesmo quando conectada via VPN. Não espere um conjunto determinístico de regras. O Azure AD usa o ia para aprender continuamente se a MFA é necessária. Volte para um prompt de usuário como autenticação interativa ou fluxo de código de dispositivo se IWA falhar.

- A autoridade passada `PublicClientApplicationBuilder` precisa ser:
  - O locatário do formulário `https://login.microsoftonline.com/{tenant}/`, em que `tenant` é o GUID que representa a ID do locatário ou um domínio associado ao locatário.
  - Para qualquer trabalho e contas escolares: `https://login.microsoftonline.com/organizations/`.
  - Não há suporte para contas pessoais da Microsoft. Você não pode usar locatários/Common ou/consumers.

- Como a autenticação integrada do Windows é um fluxo silencioso:
  - O usuário do seu aplicativo deve ter consentido antes de usar o aplicativo.
  - Ou, o administrador de locatário deve ter consentido anteriormente para todos os usuários no locatário para usar o aplicativo.
  - Por outras palavras:
    - Você, como desenvolvedor, selecionou o botão **Grant** na portal do Azure por conta própria.
    - Ou então, um administrador de locatário selecionou o botão **conceder/revogar consentimento do administrador para {locatário Domain}** na guia **permissões de API** do registro do aplicativo. Para obter mais informações, consulte [adicionar permissões para acessar APIs da Web](https://docs.microsoft.com/azure/active-directory/develop/quickstart-configure-app-access-web-apis#add-permissions-to-access-web-apis).
    - Ou você forneceu uma maneira para os usuários consentirem com o aplicativo. Para obter mais informações, consulte [solicitando consentimento de usuário individual](https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent#requesting-individual-user-consent).
    - Ou você forneceu uma maneira para o administrador do locatário consentir com o aplicativo. Para obter mais informações, consulte [consentimento do administrador](https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent#requesting-consent-for-an-entire-tenant).

- Esse fluxo está habilitado para aplicativos .NET desktop, .NET Core e UWP.

Para obter mais informações sobre consentimento, consulte [permissões e consentimento da plataforma de identidade da Microsoft](https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent).

### <a name="learn-how-to-use-it"></a>Saiba como usá-lo

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

No MSAL.NET, você precisa usar:

```csharp
AcquireTokenByIntegratedWindowsAuth(IEnumerable<string> scopes)
```

Normalmente, você precisa de apenas um parâmetro (`scopes`). Dependendo da maneira como o administrador do Windows configurou as políticas, os aplicativos em seu computador Windows podem não ter permissão para pesquisar o usuário conectado. Nesse caso, use um segundo método, `.WithUsername()`, e transmita o nome de usuário que está conectado como um formato UPN, por exemplo, `joe@contoso.com`. No .NET Core, apenas a sobrecarga que leva o nome de usuário está disponível porque a plataforma .NET Core não pode solicitar o nome de usuário ao sistema operacional.

O exemplo a seguir apresenta o caso mais atual, com explicações do tipo de exceções que você pode obter e suas atenuações.

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

Para obter a lista de possíveis modificadores em AcquireTokenByIntegratedWindowsAuthentication, consulte [AcquireTokenByIntegratedWindowsAuthParameterBuilder](/dotnet/api/microsoft.identity.client.acquiretokenbyintegratedwindowsauthparameterbuilder?view=azure-dotnet-preview#methods).

# <a name="javatabjava"></a>[Java](#tab/java)

Essa extração é dos [exemplos de desenvolvimento do MSAL Java](https://github.com/AzureAD/microsoft-authentication-library-for-java/blob/dev/src/samples/public-client/). Aqui está a classe usada em exemplos de desenvolvimento de Java MSAL para configurar os exemplos: [TestData](https://github.com/AzureAD/microsoft-authentication-library-for-java/blob/dev/src/samples/public-client/TestData.java).

```Java
PublicClientApplication app = PublicClientApplication.builder(TestData.PUBLIC_CLIENT_ID)
         .authority(TestData.AUTHORITY_ORGANIZATION)
         .telemetryConsumer(new Telemetry.MyTelemetryConsumer().telemetryConsumer)
         .build();

 IntegratedWindowsAuthenticationParameters parameters =
         IntegratedWindowsAuthenticationParameters.builder(
                 Collections.singleton(TestData.GRAPH_DEFAULT_SCOPE), TestData.USER_NAME)
                 .build();

 Future<IAuthenticationResult> future = app.acquireToken(parameters);

 IAuthenticationResult result = future.get();

 return result;
```

# <a name="pythontabpython"></a>[Python](#tab/python)

Esse fluxo ainda não tem suporte no Python MSAL.

# <a name="macostabmacos"></a>[MacOS](#tab/macOS)

Este fluxo não se aplica ao MacOS.

---

## <a name="username-and-password"></a>Nome de utilizador e senha

Você também pode adquirir um token fornecendo o nome de usuário e a senha. Esse fluxo é limitado e não recomendado, mas ainda há casos de uso em que é necessário.

### <a name="this-flow-isnt-recommended"></a>Este fluxo não é recomendado

Esse fluxo *não é recomendado* porque fazer com que seu aplicativo solicite a senha de um usuário não é seguro. Para obter mais informações, consulte [qual é a solução para o crescente problema de senhas?](https://news.microsoft.com/features/whats-solution-growing-problem-passwords-says-microsoft/). O fluxo preferencial para adquirir um token silenciosamente em computadores ingressados no domínio do Windows é a [autenticação integrada do Windows](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Integrated-Windows-Authentication). Você também pode usar o [fluxo de código do dispositivo](https://aka.ms/msal-net-device-code-flow).

> [!NOTE]
> Usar um nome de usuário e uma senha é útil em alguns casos, como cenários DevOps. Mas se você quiser usar um nome de usuário e senha em cenários interativos onde você fornece sua própria interface do usuário, pense em como sair dela. Usando um nome de usuário e uma senha, você está dando várias coisas:
>
> - Princípios básicos da identidade moderna. Uma senha pode ser phisheda e reproduzida porque um segredo compartilhado pode ser interceptado. Ele é incompatível com sem senha.
> - Os usuários que precisam fazer a MFA não podem entrar porque não há nenhuma interação.
> - Os usuários não podem fazer logon único (SSO).

### <a name="constraints"></a>Restrições

As seguintes restrições também se aplicam:

- O fluxo de nome de usuário e senha não é compatível com acesso condicional e autenticação multifator. Como consequência, se seu aplicativo for executado em um locatário do Azure AD em que o administrador de locatários requer autenticação multifator, você não poderá usar esse fluxo. Muitas organizações fazem isso.
- Ele funciona apenas para contas corporativas e de estudante (não MSA).
- O fluxo está disponível no .NET desktop e no .NET Core, mas não no UWP.

### <a name="b2c-specifics"></a>Especificações B2C

Para obter mais informações, consulte [credenciais de senha do proprietário do recurso (ROPC) com B2C](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/AAD-B2C-specifics#resource-owner-password-credentials-ropc-with-b2c).

### <a name="use-it"></a>Use-o

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

`IPublicClientApplication`contém o método `AcquireTokenByUsernamePassword`.

O exemplo a seguir apresenta um caso simplificado.

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

O exemplo a seguir apresenta o caso mais atual, com explicações do tipo de exceções que você pode obter e suas atenuações.

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

Para obter mais informações sobre todos os modificadores que podem ser aplicados a `AcquireTokenByUsernamePassword`, consulte [AcquireTokenByUsernamePasswordParameterBuilder](/dotnet/api/microsoft.identity.client.acquiretokenbyusernamepasswordparameterbuilder?view=azure-dotnet-preview#methods).

# <a name="javatabjava"></a>[Java](#tab/java)

A extração a seguir é dos [exemplos de desenvolvimento do MSAL Java](https://github.com/AzureAD/microsoft-authentication-library-for-java/blob/dev/src/samples/public-client/). Aqui está a classe usada em exemplos de desenvolvimento de Java MSAL para configurar os exemplos: [TestData](https://github.com/AzureAD/microsoft-authentication-library-for-java/blob/dev/src/samples/public-client/TestData.java).

```Java
PublicClientApplication app = PublicClientApplication.builder(TestData.PUBLIC_CLIENT_ID)
        .authority(TestData.AUTHORITY_ORGANIZATION)
        .build();

UserNamePasswordParameters parameters = UserNamePasswordParameters.builder(
        Collections.singleton(TestData.GRAPH_DEFAULT_SCOPE),
        TestData.USER_NAME,
        TestData.USER_PASSWORD.toCharArray())
        .build();

CompletableFuture<IAuthenticationResult> future = app.acquireToken(parameters);

future.handle((res, ex) -> {
    if(ex != null) {
        System.out.println("Oops! We have an exception - " + ex.getMessage());
        return "Unknown!";
    }

    Collection<IAccount> accounts = app.getAccounts().join();

    CompletableFuture<IAuthenticationResult> future1;
    try {
        future1 = app.acquireTokenSilently
                (SilentParameters.builder(Collections.singleton(TestData.GRAPH_DEFAULT_SCOPE),
                        accounts.iterator().next())
                        .forceRefresh(true)
                        .build());

    } catch (MalformedURLException e) {
        e.printStackTrace();
        throw new RuntimeException();
    }

    future1.join();

    IAccount account = app.getAccounts().join().iterator().next();
    app.removeAccount(account).join();

    return res;
}).join();
```

# <a name="pythontabpython"></a>[Python](#tab/python)

Essa extração é dos [exemplos de desenvolvimento do MSAL Python](https://github.com/AzureAD/microsoft-authentication-library-for-python/blob/dev/sample/).

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

# <a name="macostabmacos"></a>[MacOS](#tab/macOS)

Esse fluxo não tem suporte no MSAL para macOS.

---

## <a name="command-line-tool-without-a-web-browser"></a>Ferramenta de linha de comando sem um navegador da Web

### <a name="device-code-flow"></a>Fluxo de código do dispositivo

Se você estiver escrevendo uma ferramenta de linha de comando que não tem controles da Web e não desejar ou não quiser usar os fluxos anteriores, será necessário usar o fluxo de código do dispositivo.

A autenticação interativa com o Azure AD requer um navegador da Web. Para obter mais informações, consulte [uso de navegadores da Web](https://aka.ms/msal-net-uses-web-browser). Para autenticar usuários em dispositivos ou sistemas operacionais que não fornecem um navegador da Web, o fluxo de código do dispositivo permite que o usuário use outro dispositivo, como um computador ou um telefone celular, para entrar interativamente. Usando o fluxo de código do dispositivo, o aplicativo obtém tokens por meio de um processo de duas etapas projetado para esses dispositivos ou sistemas operacionais. Exemplos desses aplicativos são aplicativos que são executados em iOT ou em ferramentas de linha de comando (CLI). A ideia é que:

1. Sempre que a autenticação do usuário é necessária, o aplicativo fornece um código para o usuário. O usuário é solicitado a usar outro dispositivo, como um smartphone conectado à Internet, para ir para uma URL, por exemplo, `https://microsoft.com/devicelogin`. Em seguida, o usuário será solicitado a inserir o código. Isso feito, a página da Web leva o usuário por uma experiência de autenticação normal, que inclui prompts de consentimento e autenticação multifator, se necessário.

2. Após a autenticação bem-sucedida, o aplicativo de linha de comando recebe os tokens necessários por meio de um canal de fundo e os usa para executar as chamadas de API Web necessárias.

### <a name="use-it"></a>Use-o

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

`IPublicClientApplication`contém um método chamado `AcquireTokenWithDeviceCode`.

```csharp
 AcquireTokenWithDeviceCode(IEnumerable<string> scopes,
                            Func<DeviceCodeResult, Task> deviceCodeResultCallback)
```

Esse método usa como parâmetros:

- O `scopes` para solicitar um token de acesso para.
- Um retorno de chamada que recebe o `DeviceCodeResult`.

  ![Propriedades de DeviceCodeResult](https://user-images.githubusercontent.com/13203188/56024968-7af1b980-5d11-11e9-84c2-5be2ef306dc5.png)

O código de exemplo a seguir apresenta o caso mais atual, com explicações do tipo de exceções que você pode obter e sua mitigação.

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
# <a name="javatabjava"></a>[Java](#tab/java)

Essa extração é dos [exemplos de desenvolvimento do MSAL Java](https://github.com/AzureAD/microsoft-authentication-library-for-java/blob/dev/src/samples/public-client/). Aqui está a classe usada em exemplos de desenvolvimento de Java MSAL para configurar os exemplos: [TestData](https://github.com/AzureAD/microsoft-authentication-library-for-java/blob/dev/src/samples/public-client/TestData.java).

```java
PublicClientApplication app = PublicClientApplication.builder(TestData.PUBLIC_CLIENT_ID)
        .authority(TestData.AUTHORITY_COMMON)
        .build();

Consumer<DeviceCode> deviceCodeConsumer = (DeviceCode deviceCode) -> {
    System.out.println(deviceCode.message());
};

CompletableFuture<IAuthenticationResult> future = app.acquireToken(
        DeviceCodeFlowParameters.builder(
                Collections.singleton(TestData.GRAPH_DEFAULT_SCOPE),
                deviceCodeConsumer)
                .build());

future.handle((res, ex) -> {
    if(ex != null) {
        System.out.println("Oops! We have an exception of type - " + ex.getClass());
        System.out.println("message - " + ex.getMessage());
        return "Unknown!";
    }
    System.out.println("Returned ok - " + res);

    return res;
});

future.join();
```

# <a name="pythontabpython"></a>[Python](#tab/python)

Essa extração é dos [exemplos de desenvolvimento do MSAL Python](https://github.com/AzureAD/microsoft-authentication-library-for-python/blob/dev/sample/).

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

# <a name="macostabmacos"></a>[MacOS](#tab/macOS)

Este fluxo não se aplica ao MacOS.

---

## <a name="file-based-token-cache"></a>Cache de token baseado em arquivo

No MSAL.NET, um cache de token na memória é fornecido por padrão.

### <a name="serialization-is-customizable-in-windows-desktop-apps-and-web-apps-or-web-apis"></a>A serialização é personalizável em aplicativos de área de trabalho do Windows e aplicativos Web ou APIs Web

No caso do .NET Framework e do .NET Core, se você não fizer nada extra, o cache de token na memória durará a duração do aplicativo. Para entender por que a serialização não é fornecida prontamente, lembre-se de que os aplicativos MSAL .NET desktop ou .NET Core podem ser aplicativos de console ou do Windows (que teriam acesso ao sistema de arquivos) *, mas também* aplicativos Web ou APIs Web. Esses aplicativos Web e APIs Web podem usar alguns mecanismos de cache específicos, como bancos de dados, caches distribuídos e caches Redis. Para ter um aplicativo de cache de token persistente no .NET desktop ou no .NET Core, você precisará personalizar a serialização.

Classes e interfaces envolvidas na serialização de cache de token são os seguintes tipos:

- ``ITokenCache``, que define os eventos para assinar solicitações de serialização de cache de token e métodos para serializar ou desserializar o cache em vários formatos (ADAL v 3.0, MSAL 2. x e MSAL 3. x = ADAL v 5.0).
- ``TokenCacheCallback`` é um retorno de chamada passado para os eventos para que você possa manipular a serialização. Eles serão chamados com argumentos do tipo ``TokenCacheNotificationArgs``.
- ``TokenCacheNotificationArgs`` fornece apenas o ``ClientId`` do aplicativo e uma referência ao usuário para o qual o token está disponível.

  ![Diagrama de serialização de cache de token](https://user-images.githubusercontent.com/13203188/56027172-d58d1480-5d15-11e9-8ada-c0292f1800b3.png)

> [!IMPORTANT]
> O MSAL.NET cria caches de token para você e fornece o cache `IToken` quando você chama as propriedades `UserTokenCache` e `AppTokenCache` de um aplicativo. Você não deve implementar a interface por conta própria. Sua responsabilidade, quando você implementa uma serialização personalizada de cache de token, é:
>
> - Reagir a `BeforeAccess` e `AfterAccess` eventos ou seus equivalentes *assíncronos* . O delegado`BeforeAccess` é responsável por desserializar o cache. O delegado `AfterAccess` é responsável por serializar o cache.
> - Entenda que parte desses eventos armazena ou carrega BLOBs, que são passados pelo argumento de evento para qualquer armazenamento que você desejar.

As estratégias são diferentes dependendo se você estiver escrevendo uma serialização de cache de token para um aplicativo cliente público, como um desktop ou um aplicativo cliente confidencial, como um aplicativo Web ou uma API da Web ou um aplicativo daemon.

Desde o MSAL v2. x, você tem várias opções. Sua escolha depende se você deseja serializar o cache somente para o formato MSAL.NET, que é um cache de formato unificado que é comum com o MSAL, mas também entre as plataformas. Ou talvez você também queira dar suporte à serialização de cache de token [herdado](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Token-cache-serialization) da Adal v3.

A personalização da serialização de cache de token para compartilhar o estado de SSO entre ADAL.NET 3. x, ADAL.NET 5. x e MSAL.NET é explicada em parte do exemplo de [Active-Directory-dotnet-v1-to-v2](https://github.com/Azure-Samples/active-directory-dotnet-v1-to-v2).

### <a name="simple-token-cache-serialization-msal-only"></a>Serialização de cache de token simples (somente MSAL)

O exemplo a seguir é uma implementação ingênua de serialização personalizada de um cache de token para aplicativos de área de trabalho. Aqui, o cache de token de usuário está em um arquivo na mesma pasta que o aplicativo.

Depois de criar o aplicativo, você habilita a serialização chamando ``TokenCacheHelper.EnableSerialization()`` e passando a `UserTokenCache`do aplicativo.

```csharp
app = PublicClientApplicationBuilder.Create(ClientId)
    .Build();
TokenCacheHelper.EnableSerialization(app.UserTokenCache);
```

Essa classe auxiliar é semelhante ao seguinte trecho de código:

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

Uma visualização de um serializador de token de qualidade de produto cache baseado em arquivo para aplicativos cliente públicos para aplicativos de desktop em execução no Windows, Mac e Linux está disponível na biblioteca de código-fonte aberto [Microsoft. Identity. Client. Extensions. MSAL](https://github.com/AzureAD/microsoft-authentication-extensions-for-dotnet/tree/master/src/Microsoft.Identity.Client.Extensions.Msal) . Você pode incluí-lo em seus aplicativos do seguinte pacote NuGet: [Microsoft. Identity. Client. Extensions. MSAL](https://www.nuget.org/packages/Microsoft.Identity.Client.Extensions.Msal/).

> [!NOTE]
> Isenção de responsabilidade: a biblioteca Microsoft. Identity. cliente. Extensions. MSAL é uma extensão em MSAL.NET. As classes nessas bibliotecas podem fazer seu caminho no MSAL.NET no futuro, como está ou com alterações significativas.

### <a name="dual-token-cache-serialization-msal-unified-cache--adal-v3"></a>Serialização de cache de token duplo (cache unificado MSAL + ADAL v3)

Talvez você queira implementar a serialização de cache de token com o formato de cache unificado. Esse formato é comum a ADAL.NET 4. x e MSAL.NET 2. x, e com outros MSALs da mesma geração ou mais antigo, na mesma plataforma. Fique inspirado pelo seguinte código:

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

Desta vez, a classe auxiliar é semelhante ao seguinte código:

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
> [Chamar uma API Web do aplicativo de desktop](scenario-desktop-call-api.md)
