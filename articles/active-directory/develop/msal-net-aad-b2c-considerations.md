---
title: Azure AD B2C (MSAL.NET) / Azure
titleSuffix: Microsoft identity platform
description: Saiba mais sobre considerações específicas ao utilizar o Azure AD B2C com a Biblioteca de Autenticação da Microsoft para .NET (MSAL.NET).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/29/2019
ms.author: jeferrie
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 697b4bc8e3a25085ac6f7d600ea2227dd30a6624
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79262818"
---
# <a name="use-msalnet-to-sign-in-users-with-social-identities"></a>Use MSAL.NET para assinar em utilizadores com identidades sociais

Pode utilizar MSAL.NET para iniciar sessão de utilizadores com identidades sociais utilizando o [Azure Ative Directory B2C (Azure AD B2C)](https://aka.ms/aadb2c). O Azure AD B2C é construído em torno da noção de políticas. Em MSAL.NET, especificar uma política traduz-se em fornecer uma autoridade.

- Quando instantaneamente a aplicação do cliente público, precisa especificar a política com autoridade.
- Quando se pretende aplicar uma apólice, é necessário `AcquireTokenInteractive` pedir `authority` uma anulação da contenção de um parâmetro.

Esta página é para MSAL 3.x. Se estiver interessado em MSAL 2.x, consulte [as especificidades do Azure AD B2C em MSAL 2.x](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/AAD-B2C-Specifics-MSAL-2.x).

## <a name="authority-for-a-azure-ad-b2c-tenant-and-policy"></a>Autoridade para um inquilino e política Azure AD B2C

A autoridade a `https://{azureADB2CHostname}/tfp/{tenant}/{policyName}` utilizar é onde:

- `azureADB2CHostname`é o nome do inquilino Azure AD B2C `{your-tenant-name}.b2clogin.com`mais o hospedeiro (por exemplo),
- `tenant`é o nome completo do inquilino Azure AD `{your-tenant-name}.onmicrosoft.com`B2C (por exemplo, ) ou do GUID para o inquilino, 
- `policyName`o nome da política ou fluxo de utilizador a aplicar (por exemplo, "b2c_1_susi" para o sign-up/inscrição).

Para obter mais informações sobre as autoridades do Azure AD B2C, consulte esta [documentação](/azure/active-directory-b2c/b2clogin).

## <a name="instantiating-the-application"></a>Instantaneamente a aplicação

Ao construir o pedido, precisa fornecer a autoridade.

```csharp
// Azure AD B2C Coordinates
public static string Tenant = "fabrikamb2c.onmicrosoft.com";
public static string AzureADB2CHostname = "fabrikamb2c.b2clogin.com";
public static string ClientID = "90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6";
public static string PolicySignUpSignIn = "b2c_1_susi";
public static string PolicyEditProfile = "b2c_1_edit_profile";
public static string PolicyResetPassword = "b2c_1_reset";

public static string AuthorityBase = $"https://{AzureADB2CHostname}/tfp/{Tenant}/";
public static string Authority = $"{AuthorityBase}{PolicySignUpSignIn}";
public static string AuthorityEditProfile = $"{AuthorityBase}{PolicyEditProfile}";
public static string AuthorityPasswordReset = $"{AuthorityBase}{PolicyResetPassword}";

application = PublicClientApplicationBuilder.Create(ClientID)
               .WithB2CAuthority(Authority)
               .Build();
```

## <a name="acquire-a-token-to-apply-a-policy"></a>Adquirir um símbolo para aplicar uma apólice

Adquirir um símbolo para uma APi protegida Azure AD B2C numa aplicação de cliente público requer que utilize as sobreposições com uma autoridade:

```csharp
IEnumerable<IAccount> accounts = await application.GetAccountsAsync();
AuthenticationResult ar = await application .AcquireTokenInteractive(scopes)
                                            .WithAccount(GetAccountByPolicy(accounts, policy))
                                            .WithParentActivityOrWindow(ParentActivityOrWindow)
                                            .ExecuteAsync();
```

Por:

- `policy`sendo uma das cordas anteriores `PolicySignUpSignIn`(por exemplo).
- `ParentActivityOrWindow`é necessário para Android (a Atividade), e opcional para outras plataformas que suportam o UI principal, como janelas no Windows e UIViewController no iOS. Veja mais informações [aqui no diálogo ui](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Acquiring-tokens-interactively#withparentactivityorwindow).
- `GetAccountByPolicy(IEnumerable<IAccount>, string)`é um método que encontra uma conta para uma determinada política. Por exemplo:

  ```csharp
  private IAccount GetAccountByPolicy(IEnumerable<IAccount> accounts, string policy)
  {
   foreach (var account in accounts)
   {
    string userIdentifier = account.HomeAccountId.ObjectId.Split('.')[0];
    if (userIdentifier.EndsWith(policy.ToLower()))
     return account;
   }
   return null;
  }
  ```

A aplicação de uma política ou fluxo de utilizador (por exemplo, permitindo que `AcquireTokenInteractive`o utilizador final edite o seu perfil ou redefinir a sua palavra-passe) é atualmente feita através da chamada . No caso destas duas políticas, não utiliza o resultado devolvido do token/autenticação.

## <a name="special-case-of-editprofile-and-resetpassword-policies"></a>Caso especial de políticas de EditProfile e ResetPassword

Quando pretende proporcionar uma experiência em que os seus utilizadores finais se inscrevam com uma identidade social e depois editem o seu perfil, pretende aplicar a política de Perfil de Edição De AD B2C azure. A forma de o `AcquireTokenInteractive` fazer é ligando para a autoridade específica `Prompt.NoPrompt` para essa política, e um conjunto de Aviso para impedir que o diálogo de seleção de conta seja exibido (uma vez que o utilizador já está inscrito e tem uma sessão de cookies ativa).

```csharp
private async void EditProfileButton_Click(object sender, RoutedEventArgs e)
{
 IEnumerable<IAccount> accounts = await app.GetAccountsAsync();
 try
 {
  var authResult = await app.AcquireToken(scopes:App.ApiScopes)
                               .WithAccount(GetUserByPolicy(accounts, App.PolicyEditProfile)),
                               .WithPrompt(Prompt.NoPrompt),
                               .WithB2CAuthority(App.AuthorityEditProfile)
                               .ExecuteAsync();
  DisplayBasicTokenInfo(authResult);
 }
 catch
 {
  . . .
 }
}
```
## <a name="resource-owner-password-credentials-ropc-with-azure-ad-b2c"></a>Credenciais de senha do proprietário de recursos (ROPC) com Azure AD B2C
Para mais detalhes sobre o fluxo ropc, consulte esta [documentação](v2-oauth-ropc.md).

Este fluxo não é **recomendado** porque a sua aplicação que pede a um utilizador a sua palavra-passe não é segura. Para mais informações sobre este problema, consulte [este artigo.](https://news.microsoft.com/features/whats-solution-growing-problem-passwords-says-microsoft/) 

Ao utilizar o nome de utilizador/palavra-passe, está a abdicar de uma série de coisas:
- Princípios fundamentais da identidade moderna: a palavra-passe é pescada, reproduzida. Porque temos este conceito de segredo de partilha que pode ser intercetado. Isto é incompatível com sem palavras-passe.
- Os utilizadores que necessitem de fazer MFA não poderão entrar (uma vez que não há interação).
- Os utilizadores não poderão fazer um único sinal.

### <a name="configure-the-ropc-flow-in-azure-ad-b2c"></a>Configure o fluxo ROPC em Azure AD B2C
No seu inquilino Azure AD B2C, crie um novo fluxo de utilizador e selecione **Sign in usando ROPC**. Isto permitirá a política ropc para o seu inquilino. Consulte [configurar as credenciais de senha do proprietário do recurso](/azure/active-directory-b2c/configure-ropc) para obter mais detalhes.

`IPublicClientApplication`contém um método:
```csharp
AcquireTokenByUsernamePassword(
            IEnumerable<string> scopes,
            string username,
            SecureString password)
```

Este método toma como parâmetros:
- Os *âmbitos* para solicitar um sinal de acesso para.
- Um *nome de utilizador*.
- Uma *senha* SecureString para o utilizador.

Lembre-se de usar a autoridade que contém a política ropc.

### <a name="limitations-of-the-ropc-flow"></a>Limitações do fluxo ROPC
 - O fluxo ROPC **funciona apenas para contas locais** (onde se regista com o Azure AD B2C usando um e-mail ou nome de utilizador). Este fluxo não funciona se federar a qualquer um dos fornecedores de identidade suportados pelo Azure AD B2C (Facebook, Google, etc.).

## <a name="google-auth-and-embedded-webview"></a>Google Auth e Webview incorporado

Se é um desenvolvedor de Azure AD B2C usando o Google como fornecedor de identidade, recomando o seu uso do navegador do sistema, uma vez que o Google não permite [a autenticação a partir de webviews incorporados](https://developers.googleblog.com/2016/08/modernizing-oauth-interactions-in-native-apps.html). Atualmente, `login.microsoftonline.com` é uma autoridade de confiança com o Google. A utilização desta autoridade funcionará com webview incorporado. No `b2clogin.com` entanto, a utilização não é uma autoridade de confiança com a Google, pelo que os utilizadores não poderão autenticar.

Forneceremos uma atualização para este [problema](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/688) se as coisas mudarem.

## <a name="caching-with-azure-ad-b2c-in-msalnet"></a>Caching com Azure AD B2C em MSAL.Net 

### <a name="known-issue-with-azure-ad-b2c"></a>Problema conhecido com Azure AD B2C

MSAL.Net suporta uma [cache simbólica.](/dotnet/api/microsoft.identity.client.tokencache?view=azure-dotnet) A chave de cache token baseia-se nos créditos devolvidos pelo Fornecedor de Identidade. Atualmente MSAL.Net precisa de duas reivindicações para construir uma chave de cache simbólica:  
- `tid`que é o Azure AD Tenant ID, e 
- `preferred_username` 

Ambas as alegações estão em falta em muitos dos cenários Do Azure AD B2C. 

O impacto do cliente é que ao tentar exibir o campo de nome de utilizador, está a receber "Missing from the token response" como o valor? Em caso afirmativo, isto deve-se ao facto de o Azure AD B2C não devolver um valor no IdToken para o preferred_username devido a limitações com as contas sociais e fornecedores de identidade externa (IDPs). A Azure AD devolve um valor para preferred_username porque sabe quem é o utilizador, mas para o Azure AD B2C, porque o utilizador pode iniciar sessão com uma conta local, Facebook, Google, GitHub, etc. não existe um valor consistente para o Azure AD B2C utilizar para preferred_username. Para desbloquear a MSAL de lançar a compatibilidade com a ADAL, decidimos usar "Missing from the token response" do nosso lado ao lidar com as contas Azure AD B2C quando o IdToken não devolve nada para preferred_username. A MSAL deve devolver um valor para preferred_username manter a compatibilidade do cache entre bibliotecas.

### <a name="workarounds"></a>Soluções

#### <a name="mitigation-for-the-missing-tenant-id"></a>Atenuação para a identidade do inquilino desaparecido

A suposições sugeridas é usar o [Caching by Policy](#acquire-a-token-to-apply-a-policy)

Em alternativa, pode `tid` utilizar a reclamação, se estiver a utilizar as [políticas personalizadas B2C,](https://aka.ms/ief)pois fornece a capacidade de devolver reclamações adicionais à aplicação. Para saber mais sobre a [Transformação de Reclamações](/azure/active-directory-b2c/claims-transformation-technical-profile)

#### <a name="mitigation-for-missing-from-the-token-response"></a>Mitigação para "Desaparecido da resposta simbólica"
Uma opção é utilizar a alegação de "nome" como nome de utilizador preferido. O processo é mencionado neste [doc-> B2C](../../active-directory-b2c/user-flow-overview.md) "Na coluna de reclamação de retorno, escolha as reclamações que deseja devolvidas nas fichas de autorização enviadas de volta para a sua aplicação após uma experiência de edição de perfil bem sucedida. Por exemplo, selecione 'Nome de exibição, Código Postal'."

## <a name="next-steps"></a>Passos seguintes 

Mais detalhes sobre a aquisição de fichas interactivamente com MSAL.NET para aplicações Azure AD B2C são fornecidos na amostra seguinte.

| Sample | Plataforma | Descrição|
|------ | -------- | -----------|
|[active-directy-b2c-xamarin-nativo](https://github.com/Azure-Samples/active-directory-b2c-xamarin-native) | Xamarin iOS, Xamarin Android, UWP | Uma simples aplicação Xamarin Forms que mostra como usar MSAL.NET para autenticar os utilizadores através do Azure AD B2C, e aceder a uma API Web com as fichas resultantes.|
