---
title: Aplicação de ambiente de trabalho que chama o web APIs (aquisição de um token da aplicação) - plataforma de identidade da Microsoft
description: Saiba como criar uma aplicação de ambiente de trabalho que chama a web APIs (obter um token para a aplicação |)
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
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: ecf5b874345a94e8fd3d3a0783f8e48c7484377d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67111261"
---
# <a name="desktop-app-that-calls-web-apis---acquire-a-token"></a>Aplicação de ambiente de trabalho que chama o web APIs - adquirir um token

Depois de criar `IPublicClientApplication`, vai usá-lo para adquirir um token que, em seguida, usará para chamar uma API web.

## <a name="recommended-pattern"></a>Padrão recomendado

A API web é definida por seu `scopes`. Seja qual for a experiência que fornece em seu aplicativo, o padrão que vai querer usar é:

- Sistematicamente tentar obter um token da cache de token chamando `AcquireTokenSilent`
- Se esta chamada falhar, utilize o `AcquireToken` fluxo que pretende utilizar (aqui é representado por `AcquireTokenXX`)

```CSharp
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

Aqui é agora o detalhe das várias formas de adquirir tokens num aplicativo de desktop

## <a name="acquiring-a-token-interactively"></a>Adquirir um token de forma interativa

O exemplo seguinte mostra o código mínimo para obter um token de forma interativa para ler o perfil do usuário com o Microsoft Graph.

```CSharp
string[] scopes = new string["user.read"];
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

`AcquireTokenInteractive` tem apenas um parâmetro obrigatório ``scopes``, que contém uma enumeração de cadeias de caracteres que definem os âmbitos para as quais é necessário um token. Se o token for para o Microsoft Graph, os âmbitos necessários podem ser encontrados na referência da api do cada Microsoft graph API na secção com o nome "Permissões". Por exemplo, para [lista de contactos do utilizador](https://developer.microsoft.com/graph/docs/api-reference/v1.0/api/user_list_contacts), o âmbito "User.Read", "Contacts.Read" tem de ser utilizado. Consulte também [referência de permissões do Microsoft Graph](https://developer.microsoft.com/graph/docs/concepts/permissions_reference).

No Android, tem de especificar também a atividade principal (usando `.WithParentActivityOrWindow`, ver abaixo), para que o token recebe de volta para que a atividade principal após a interação. Se não especificar isso, será emitida uma exceção ao chamar `.ExecuteAsync()`.

### <a name="specific-optional-parameters"></a>Parâmetros opcionais específicos

#### <a name="withparentactivityorwindow"></a>WithParentActivityOrWindow

Interface do Usuário que está a ser interativo, é importante. `AcquireTokenInteractive` tem um parâmetro opcional específico, que permite para especificar, para plataformas suportá-lo, o principal da interface do Usuário. Quando utilizado num aplicativo de desktop, `.WithParentActivityOrWindow` tem um tipo diferente, dependendo da plataforma:

```CSharp
// net45
WithParentActivityOrWindow(IntPtr windowPtr)
WithParentActivityOrWindow(IWin32Window window)

// Mac
WithParentActivityOrWindow(NSWindow window)

// .Net Standard (this will be on all platforms at runtime, but only on NetStandard at build time)
WithParentActivityOrWindow(object parent).
```

Observações:

- No .NET Standard, a esperada `object` é um `Activity` no Android, uma `UIViewController` no iOS, uma `NSWindow` no MAC e um `IWin32Window` ou `IntPr` no Windows.
- No Windows, deve chamar `AcquireTokenInteractive` da interface do Usuário do thread para que o browser incorporado obtém o contexto de sincronização apropriado da interface do Usuário.  Não chamar do thread da interface do Usuário pode fazer com que as mensagens não bombardeia corretamente e/ou travar cenários com a interface do Usuário. Uma forma de chamar MSAL do thread da interface do Usuário, se não estiver no thread da interface do Usuário já está a utilizar o `Dispatcher` no WPF.
- Se estiver usando o WPF, para obter uma janela de um controle do WPF, pode usar `WindowInteropHelper.Handle` classe. A chamada é então, a partir de um controle do WPF (`this`):
  
  ```CSharp
  result = await app.AcquireTokenInteractive(scopes)
                    .WithParentActivityOrWindow(new WindowInteropHelper(this).Handle)
                    .ExecuteAsync();
  ```

#### <a name="withprompt"></a>WithPrompt

`WithPrompt()` é utilizado para controlar a interatividade com o utilizador, especificando uma linha de comandos

<img src="https://user-images.githubusercontent.com/13203188/53438042-3fb85700-39ff-11e9-9a9e-1ff9874197b3.png" width="25%" />

A classe define as seguintes constantes:

- ``SelectAccount``: forçará o STS para apresentar a caixa de diálogo de seleção conta que contém contas para a qual o utilizador tem uma sessão. Esta opção é útil quando os desenvolvedores de aplicativos que deseja permitir que o usuário escolher entre diferentes identidades. Esta opção unidades MSAL para enviar ``prompt=select_account`` para o fornecedor de identidade. Esta opção é o padrão, e ele faz de bom trabalho ao fornecer a melhor experiência possível com base nas informações disponíveis (conta, a presença de uma sessão para o utilizador e assim por diante. ...). Não alterá-lo a menos que tenha boa razão para fazê-lo.
- ``Consent``: permite à aplicação desenvolvedor obrigue o usuário solicitado consentimento, mesmo que a autorização ser concedida antes. Neste caso, envia a MSAL `prompt=consent` para o fornecedor de identidade. Esta opção pode ser utilizada em alguns aplicativos de segurança concentra-se em que a governação de organização exige que o utilizador é apresentado a caixa de diálogo de consentimento sempre que o aplicativo é usado.
- ``ForceLogin``: permite que o desenvolvedor de aplicativo para que o usuário que lhe forem pedidas credenciais pelo serviço, mesmo se este pedido de utilizador que não seja necessário. Esta opção pode ser útil se adquirir um token de falhar, permitir que o usuário voltar-sessão-in. Neste caso, envia a MSAL `prompt=login` para o fornecedor de identidade. Novamente, vimos que costumava em alguns aplicativos de segurança concentra-se em que a governação de organização exige que o utilizador relogs sessão sempre que acederem partes específicas de um aplicativo.
- ``Never`` (para .NET 4.5 e apenas WinRT) não pedir ao utilizador, mas em vez disso, tentará utilizar o cookie armazenado na vista web embedded ocultos (veja a seguir: Vistas Web no MSAL.NET). Utilizar esta opção pode falhar e, nesse caso `AcquireTokenInteractive` lançará uma exceção para notificar que é necessária uma interação de interface do Usuário, e terá de utilizar outra `Prompt` parâmetro.
- ``NoPrompt``: Não enviará qualquer linha de comandos para o fornecedor de identidade. Esta opção só é útil para políticas de edição de perfis do Azure AD B2C (consulte [especificações de B2C](https://aka.ms/msal-net-b2c-specificities)).

#### <a name="withextrascopetoconsent"></a>WithExtraScopeToConsent

Este modificador é usado num cenário avançado onde pretende que o utilizador para autorizar previamente vários recursos de antemão (e não pretender utilizar o consentimento incremental, o que é normalmente utilizado com MSAL.NET / v2.0 de plataforma de identidade da Microsoft). Para obter detalhes, consulte [instruções: peça ao utilizador consentimento de antemão para vários recursos](scenario-desktop-production.md#how-to-have--the-user-consent-upfront-for-several-resources).

```CSharp
var result = await app.AcquireTokenInteractive(scopesForCustomerApi)
                     .WithExtraScopeToConsent(scopesForVendorApi)
                     .ExecuteAsync();
```

#### <a name="withcustomwebui"></a>WithCustomWebUi

##### <a name="withcustomwebui-is-an-extensibility-point"></a>WithCustomWebUi é um ponto de extensibilidade

`WithCustomWebUi` é um ponto de extensibilidade que permite a que fornecer sua própria interface do Usuário em aplicativos cliente público e permitir que o usuário percorra o ponto de extremidade /Authorize do fornecedor de identidade e permitem-los iniciarem sessão em dar consentimento. Em seguida, MSAL.NET podem resgatar o código de autenticação e obter um token. Por exemplo é utilizado no Visual Studio ter elétrons aplicativos (por exemplo VS comentários) fornecem a interação da web, mas deixá-la para MSAL.NET para fazer a maior parte do trabalho. Também pode utilizá-lo, se quiser fornecem automação da interface do Usuário. Em aplicativos cliente público, MSAL.NET utiliza o padrão PKCE ([RFC 7636 - chave de prova para o Exchange de código por clientes públicos do OAuth](https://tools.ietf.org/html/rfc7636)) para se certificar de que a segurança é respeitada: MSAL.NET só pode resgatar o código.

  ```CSharp
  using Microsoft.Identity.Client.Extensions;
  ```

##### <a name="how-to-use-withcustomwebui"></a>Como utilizar WithCustomWebUi

Para poder utilizar `.WithCustomWebUI`, terá de:
  
  1. Implementar o `ICustomWebUi` interface (consulte [aqui](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/blob/053a98d16596be7e9ca1ab916924e5736e341fe8/src/Microsoft.Identity.Client/Extensibility/ICustomWebUI.cs#L32-L70). Basicamente, terá de implementar um método `AcquireAuthorizationCodeAsync` aceitar o URL de código de autorização (que é calculado pelo MSAL.NET), permitindo que o usuário percorra a interação com o fornecedor de identidade e, em seguida, retornar fazer uma cópia de URL através do qual seria o fornecedor de identidade ter chamado a sua implementação novamente (incluindo o código de autorização). Se tiver problemas, sua implementação deve lançar uma `MsalExtensionException` exceção que cooperem muito bem com MSAL.
  2. No seu `AcquireTokenInteractive` chamada, pode usar `.WithCustomUI()` modificador passando a instância do seu personalizado da interface do Usuário da web

     ```CSharp
     result = await app.AcquireTokenInteractive(scopes)
                       .WithCustomWebUi(yourCustomWebUI)
                       .ExecuteAsync();
     ```

##### <a name="examples-of-implementation-of-icustomwebui-in-test-automation---seleniumwebui"></a>Exemplos de implementação de ICustomWebUi na Automação de teste - SeleniumWebUI

A equipe MSAL.NET ter reescrito nossos testes da interface do Usuário para aproveitar esse mecanismo de extensibilidade. Caso esteja interessado pode dar uma olhada a [SeleniumWebUI](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/blob/053a98d16596be7e9ca1ab916924e5736e341fe8/tests/Microsoft.Identity.Test.Integration/Infrastructure/SeleniumWebUI.cs#L15-L160) classe no código-fonte MSAL.NET

#### <a name="other-optional-parameters"></a>Outros parâmetros opcionais

Saiba mais sobre todos os outros parâmetros opcionais para `AcquireTokenInteractive` da documentação de referência para [AcquireTokenInteractiveParameterBuilder](/dotnet/api/microsoft.identity.client.acquiretokeninteractiveparameterbuilder?view=azure-dotnet-preview#methods)

## <a name="integrated-windows-authentication"></a>Autenticação integrada do Windows

Se quiser entrar um utilizador de domínio num domínio ou do Azure AD associado a um computador, tem de utilizar:

```csharp
AcquireTokenByIntegratedWindowsAuth(IEnumerable<string> scopes)
```

### <a name="constraints"></a>Restrições

- AcquireTokenByIntegratedWindowsAuth (IWA) só pode ser utilizado para **federados** utilizadores apenas, que é, os utilizadores criados num Active Directory e apoiadas pelo Azure Active Directory. Os utilizadores criados diretamente no AAD, sem segurança AD - **geridos** utilizadores - não é possível utilizar este fluxo de autenticação. Esta limitação não afeta o fluxo de nome de utilizador/palavra-passe.
- IWA é para as aplicações escritas para o .NET Framework, .NET Core e plataformas UWP
- IWA não ignorar MFA (multi-factor authentication). Se a MFA estiver configurada, IWA poderá falhar se um desafio MFA é necessário, porque a MFA requer interação do utilizador.
  > [!NOTE]
  > Isso é complicado. IWA é não interativa, mas 2FA requer interatividade de usuário. Não controlar quando o fornecedor de identidade solicita 2FA a ser executada, faz o administrador de inquilino. Da nossa observações, 2FA é necessário quando iniciar sessão a partir de um país diferente, quando não ligado através de VPN a uma rede empresarial e, às vezes, até mesmo quando estiver ligado através de VPN. Não espere um conjunto determinístico de regras, o Azure Active Directory utiliza ia para continuamente saber se é necessária uma 2FA. Deve fallback para uma linha de comandos do utilizador (interativo autenticação ou de dispositivo de fluxo de código) se IWA falhar.

- A autoridade passado o `PublicClientApplicationBuilder` tem de ser:
  - ed de inquilino (do formulário `https://login.microsoftonline.com/{tenant}/` onde `tenant` é qualquer um do guid que representa a ID do inquilino ou um domínio associado ao inquilino.
  - para qualquer trabalho contas escolares ou profissionais (`https://login.microsoftonline.com/organizations/`)

  > Não são suportadas contas pessoais da Microsoft (não é possível utilizar /common ou /consumers inquilinos)

- Uma vez que a autenticação integrada do Windows é um fluxo silencioso:
  - o utilizador da sua aplicação tem tiver anteriormente autorizado a utilizar a aplicação
  - ou o administrador inquilino tem tiver autorizado anteriormente a todos os utilizadores no inquilino a utilizar a aplicação.
  - Em outras palavras:
    - optar por, como um desenvolvedor ter pressionado a **concessão** botão no portal do Azure para que mesmo
    - ou um administrador de inquilinos pressionou o **conceder/revogar o consentimento de administrador para {domínio de inquilino}** botão no **permissões de API** separador de registo para o aplicativo (consulte [adicionar permissões para APIs da web do acesso](https://docs.microsoft.com/azure/active-directory/develop/quickstart-configure-app-access-web-apis#add-permissions-to-access-web-apis))
    - ou que forneceu uma maneira para os utilizadores consentirem que o aplicativo (consulte [pedir consentimento do usuário individual](https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent#requesting-individual-user-consent))
    - ou que forneceu uma maneira para que o administrador de inquilinos dar consentimento para a aplicação (veja [consentimento de administrador](https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent#requesting-consent-for-an-entire-tenant))

- Este fluxo está ativado para ambiente de trabalho do .net, .net core e Universal do Windows (UWP) aplicações. No .NET core apenas a sobrecarga de colocar o nome de utilizador está disponível, como a plataforma .NET Core, não pode pedir o nome de utilizador para o sistema operacional.
  
Para obter mais informações sobre o consentimento, consulte [v2.0 permissões e consentimento](https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent)

### <a name="how-to-use-it"></a>Como a utilizar

Normalmente, terá apenas um parâmetro (`scopes`). No entanto consoante a forma como o administrador do Windows configurou as políticas, pode ser possível que aplicativos no seu computador windows não têm permissão para procurar o utilizador com sessão iniciada. Nesse caso, utilizar um segundo método `.WithUsername()` e transmita o nome de utilizador do utilizador com sessão iniciada como um formato UPN - `joe@contoso.com`.

O exemplo a seguir apresenta o caso mais recente, com explicações do tipo de exceções, que pode obter e respetivas mitigações

```CSharp
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

Para obter a lista de possíveis Modificadores na AcquireTokenByIntegratedWindowsAuthentication, consulte [AcquireTokenByIntegratedWindowsAuthParameterBuilder](/dotnet/api/microsoft.identity.client.acquiretokenbyintegratedwindowsauthparameterbuilder?view=azure-dotnet-preview#methods)

## <a name="username--password"></a>Nome de utilizador / palavra-passe

Também pode adquirir um token ao fornecer o nome de utilizador e palavra-passe. Este fluxo é limitado e não recomendado, mas há casos em que é necessário continuar a utilizar.

### <a name="this-flow-isnt-recommended"></a>Este fluxo não é recomendado

Este fluxo é **não recomendada** porque seu aplicativo perguntar um utilizador a palavra-passe não é seguro. Para obter mais informações sobre este problema, consulte [este artigo](https://news.microsoft.com/features/whats-solution-growing-problem-passwords-says-microsoft/). O fluxo preferencial para obter um token silenciosamente em máquinas de associados a um domínio do Windows é [a autenticação integrada do Windows](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Integrated-Windows-Authentication). Caso contrário, pode também utilizar [fluxo de código de dispositivo](https://aka.ms/msal-net-device-code-flow)

> Embora isso seja útil em alguns casos (cenários de DevOps), se pretender utilizar o nome de utilizador/palavra-passe em cenários interativos em que forneça sua onw da interface do Usuário, deve realmente pensar sobre como mover para fora. Com o nome de utilizador/palavra-passe está fornecendo-up uma série de coisas:

> - núcleos de inquilinos de identidade moderna: palavra-passe obtém fished, repetidos. Como temos este conceito de um segredo de partilha que pode ser interceptado.
> Isso é incompatível com o início.
> - os utilizadores que precisam de fazer a MFA não será capazes de início de sessão (como não há nenhuma interação do)
> - Os utilizadores não será capazes de início de sessão único

### <a name="constraints"></a>Restrições

Também se aplicam as seguintes restrições:

- O fluxo de nome de utilizador/palavra-passe não é compatível com acesso condicional e a autenticação multifator: Como conseqüência, se a aplicação for executada no inquilino do Azure AD em que o administrador de inquilinos requer autenticação multifator, não é possível utilizar este fluxo. Muitas organizações fazem isso.
- Ele funciona apenas para o trabalho e contas de instituição de ensino (não MSA)
- O flow está disponível no ambiente de trabalho do .net e .net core, mas não em UWP

### <a name="b2c-specifics"></a>Especificações do B2C

[Obter mais informações sobre como utilizar ROPC com B2C](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/AAD-B2C-specifics#resource-owner-password-credentials-ropc-with-b2c).

### <a name="how-to-use-it"></a>Como usá-lo?

`IPublicClientApplication`contém o método `AcquireTokenByUsernamePassword`

O exemplo a seguir apresenta um caso simplificado

```CSharp
static async Task GetATokenForGraph()
{
 string authority = "https://login.microsoftonline.com/contoso.com";
 string[] scopes = new string[] { "user.read" };
 IPublicClientApplication app;
 app = PublicClientApplicationBuild.Create(clientId)
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

O exemplo a seguir apresenta o caso mais recente, com explicações do tipo de exceções, que pode obter e respetivas mitigações

```CSharp
static async Task GetATokenForGraph()
{
 string authority = "https://login.microsoftonline.com/contoso.com";
 string[] scopes = new string[] { "user.read" };
 IPublicClientApplication app;
 app = PublicClientApplicationBuild.Create(clientId)
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

Para obter detalhes sobre todos os modificadores que podem ser aplicados a `AcquireTokenByUsernamePassword`, consulte [AcquireTokenByUsernamePasswordParameterBuilder](/dotnet/api/microsoft.identity.client.acquiretokenbyusernamepasswordparameterbuilder?view=azure-dotnet-preview#methods)

## <a name="command-line-tool-without-web-browser"></a>Ferramenta de linha de comando (sem o navegador da web)

### <a name="device-code-flow-why-and-how"></a>Código de dispositivo de fluxo por quê? e como?

Se estiver escrevendo uma ferramenta de linha de comandos (que não tem de controles da Web) e não pode ou não pretender utilizar os fluxos anteriores, precisará usar `AcquireTokenWithDeviceCode`.

Autenticação interativa com o Azure AD necessita de um navegador da web (para detalhes, veja [utilização de navegadores da web](https://aka.ms/msal-net-uses-web-browser)). No entanto, para autenticar os utilizadores em dispositivos ou sistemas operativos que não fornecem um navegador da Web, o fluxo de código de dispositivo permite que o utilizador utilize outro dispositivo (por exemplo, outro computador ou um número de telemóvel) para iniciar sessão interativamente. Ao utilizar o fluxo de código de dispositivo, o aplicativo obtém tokens através de um processo de dois passos especialmente concebida para estes dispositivos/OS. Exemplos de tais aplicativos são aplicações em execução no iOT ou as ferramentas da linha de comandos (CLI). A idéia é que:

1. Sempre que for necessária a autenticação de utilizador, a aplicação fornece um código e pede ao utilizador para utilizar outro dispositivo (por exemplo, um smartphone ligados à internet) para navegar para uma URL (por exemplo, `https://microsoft.com/devicelogin`), onde o usuário será solicitado para introduzir o código. Que terminar, a página da web direciona o utilizador através de uma experiência de autenticação normal, incluindo pedidos de consentimento e a autenticação multifator, se necessário.

2. Após a autenticação com êxito, a aplicação de linha de comandos irá receber os tokens necessários através de um canal de back e irá utilizá-lo para efetuar as chamadas à API web que precisa.

### <a name="code"></a>Código

`IPublicClientApplication`contém um método chamado `AcquireTokenWithDeviceCode`

```CSharp
 AcquireTokenWithDeviceCode(IEnumerable<string> scopes,
                            Func<DeviceCodeResult, Task> deviceCodeResultCallback)
```

Este método aceita como parâmetros:

- O `scopes` para pedir um token de acesso
- Um retorno de chamada que receberá o `DeviceCodeResult`

  ![image](https://user-images.githubusercontent.com/13203188/56024968-7af1b980-5d11-11e9-84c2-5be2ef306dc5.png)

O código de exemplo seguinte apresenta o caso mais recente, com explicações do tipo de exceções, que pode obter e seus atenuação.

```CSharp
static async Task<AuthenticationResult> GetATokenForGraph()
{
 string authority = "https://login.microsoftonline.com/contoso.com";
 string[] scopes = new string[] { "user.read" };
 IPublicClientApplication pca = PublicClientApplicationBuilder
      .Create(clientId)
      .WithAuthority(authority)
      .Build();

 AuthenticationResult result = null;
 var accounts = await app.GetAccountsAsync();

 // All AcquireToken* methods store the tokens in the cache, so check the cache first
 try
 {
  result = await app.AcquireTokenSilent(scopes, accounts.FirstOrDefault())
       .ExecuteAsync();
 }
 catch (MsalUiRequiredException ex)
 {
  // A MsalUiRequiredException happened on AcquireTokenSilent.
  // This indicates you need to call AcquireTokenInteractive to acquire a token
  System.Diagnostics.Debug.WriteLine($"MsalUiRequiredException: {ex.Message}");
 }

 try
 {
  result = await app.AcquireTokenWithDeviceCode(scopes,
      deviceCodeCallback =>
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
  // If you use a CancellationToken, and call the Cancel() method on it, then this may be triggered
  // to indicate that the operation was cancelled.
  // See https://docs.microsoft.com/dotnet/standard/threading/cancellation-in-managed-threads
  // for more detailed information on how C# supports cancellation in managed threads.
 }
 catch (MsalClientException ex)
 {
  // Verification code expired before contacting the server
  // This exception will occur if the user does not manage to sign-in before a time out (15 mins) and the
  // call to `AcquireTokenWithDeviceCode` is not cancelled in between
 }
}
```

## <a name="file-based-token-cache"></a>Cache de tokens baseada em ficheiros

MSAL.NET, uma cache de token na memória é fornecido por predefinição.

### <a name="serialization-is-customizable-in-windows-desktop-apps-and-web-appsweb-apis"></a>Serialização é personalizável em aplicativos de desktop do Windows e de aplicações web a APIs/web

No caso do .NET Framework e .NET core, se não fizer nada extra, o cache de token na memória dura a duração do aplicativo. Para compreender por que a serialização não foi fornecida imediato, lembre-se de .NET de MSAL os aplicativos de área de trabalho/core podem ser console ou aplicativos do Windows (que teriam acesso ao sistema de arquivos), **, mas também** aplicativos da Web ou web API. Estas aplicações Web e web APIs podem usar alguns mecanismos de cache específico, como bases de dados, as caches distribuídas, caches de redis e assim por diante. Para que um aplicativo de cache de token persistente na área de trabalho do .NET ou Core, terá de personalizar a serialização.

Classes e interfaces envolvidos na serialização de cache de tokens são os seguintes tipos:

- ``ITokenCache``, que define os eventos para subscrever a pedidos de serialização de cache de tokens, bem como métodos ao serializar ou anular a serialização do cache em vários formatos (v3.0 da ADAL, MSAL 2.x e MSAL 3.x = v5.0 da ADAL)
- ``TokenCacheCallback`` é um retorno de chamada passado para os eventos para que pode manipular a serialização. elas se chamarão com argumentos de tipo ``TokenCacheNotificationArgs``.
- ``TokenCacheNotificationArgs`` apenas fornece a ``ClientId`` da aplicação e uma referência ao utilizador para o qual o token está disponível

  ![image](https://user-images.githubusercontent.com/13203188/56027172-d58d1480-5d15-11e9-8ada-c0292f1800b3.png)

> [!IMPORTANT]
> MSAL.NET cria caches de token para e fornece-lhe com o `IToken` coloca em cache quando chamar um aplicativo `GetUserTokenCache` e `GetAppTokenCache` métodos. Não deve implementar a interface por conta própria. É sua responsabilidade, ao implementar uma serialização de cache de token personalizados:
>
> - Reaja aos `BeforeAccess` e `AfterAccess` "eventos". O`BeforeAccess` delegado é da responsabilidade de anular a serialização da cache, ao passo que o `AfterAccess` um é responsável pela serialização do cache.
> - Parte desses eventos armazenar ou carregar blobs, que são transmitidas o argumento do evento para qualquer armazenamento que pretende.

As estratégias são diferentes, dependendo se estiver escrevendo uma serialização de cache de token para uma aplicação de cliente público (Desktop) ou uma aplicação de cliente confidencial (web aplicação/API web, aplicação de daemon).

Desde a V2.x de MSAL tem várias opções, dependendo se desejar serializar o cache apenas para o formato MSAL.NET (cache de formato unificado que é comum com MSAL, mas também entre as plataformas), ou se também queira oferecer suporte a [herdados](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Token-cache-serialization) Serialização de cache de token do ADAL V3.

A personalização de serialização de cache de Token para partilhar o estado SSO entre ADAL.NET 3.x, ADAL.NET 5.x e MSAL.NET é explicado em parte do seguinte exemplo: [active-directory-dotnet-v1-to-v2](https://github.com/Azure-Samples/active-directory-dotnet-v1-to-v2)

### <a name="simple-token-cache-serialization-msal-only"></a>Serialização de cache de tokens simples (MSAL apenas)

Segue-se um exemplo de uma implementação ingênua de serialização personalizada de uma cache de token para aplicativos de desktop. Aqui o cache de token de utilizador num ficheiro na mesma pasta que o aplicativo.

Depois de compilar o aplicativo, habilitar a serialização chamando ``TokenCacheHelper.EnableSerialization()`` passando o aplicativo `UserTokenCache`

```CSharp
app = PublicClientApplicationBuilder.Create(ClientId)
    .Build();
TokenCacheHelper.EnableSerialization(app.UserTokenCache);
```

A classe auxiliar é parecido com o seguinte trecho de código:

```CSharp
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

Uma pré-visualização de uma cache token de qualidade de produtos baseados em ficheiros serializador para aplicações de cliente público (para aplicações de ambiente de trabalho em execução no Windows, Mac e linux) está disponível a partir da [Microsoft.Identity.Client.Extensions.Msal](https://github.com/AzureAD/microsoft-authentication-extensions-for-dotnet/tree/master/src/Microsoft.Identity.Client.Extensions.Msal) biblioteca de código-fonte aberto. Pode incluí-lo em seus aplicativos a partir do pacote nuget do seguinte: [Microsoft.Identity.Client.Extensions.Msal](https://www.nuget.org/packages/Microsoft.Identity.Client.Extensions.Msal/).

> Exclusão de responsabilidade. A biblioteca de Microsoft.Identity.Client.Extensions.Msal é uma extensão ao longo do MSAL.NET. Classes dessas bibliotecas podem se tornam em MSAL.NET no futuro, como está ou com alterações significativas.

### <a name="dual-token-cache-serialization-msal-unified-cache--adal-v3"></a>Serialização de cache de tokens dupla (cache MSAL unificada + V3 da ADAL)

Se pretender implementar a serialização de cache de token em cache com a unificação formato (comuns a ADAL.NET 4.x e MSAL.NET 2.x e com outros MSALs da mesma geração ou mais, na mesma plataforma), pode obter inspirado pelo seguinte código :

```CSharp
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

Desta vez, a classe auxiliar terá um aspeto semelhante ao seguinte código:

```CSharp
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
   usertokenCache = cache;
   UnifiedCacheFileName = unifiedCacheFileName;
   AdalV3CacheFileName = adalV3CacheFileName;

   usertokenCache.SetBeforeAccess(BeforeAccessNotification);
   usertokenCache.SetAfterAccess(AfterAccessNotification);
  }

  /// <summary>
  /// Token cache
  /// </summary>
  static ITokenCache usertokenCache;

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

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Chamar uma API web a partir da aplicação de ambiente de trabalho](scenario-desktop-call-api.md)
