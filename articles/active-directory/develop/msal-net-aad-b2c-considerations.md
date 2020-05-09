---
title: Azure AD B2C e MSAL.NET
titleSuffix: Microsoft identity platform
description: Considerações ao utilizar o Azure AD B2C com a Microsoft Authentication Library para .NET (MSAL.NET).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2020
ms.author: jeferrie
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 3aac63369dffa5b8ba0b9e55b5063ad8136c95cf
ms.sourcegitcommit: d815163a1359f0df6ebfbfe985566d4951e38135
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/07/2020
ms.locfileid: "82883231"
---
# <a name="use-msalnet-to-sign-in-users-with-social-identities"></a>Use MSAL.NET para assinar em utilizadores com identidades sociais

Pode utilizar MSAL.NET para iniciar sessão de utilizadores com identidades sociais utilizando o [Azure Ative Directory B2C (Azure AD B2C)](https://aka.ms/aadb2c). O Azure AD B2C é construído em torno da noção de políticas. Em MSAL.NET, especificar uma política traduz-se em fornecer uma autoridade.

- Quando instantaneamente a aplicação do cliente público, precisa especificar a política como parte da autoridade.
- Quando quiser aplicar uma apólice, peça `AcquireTokenInteractive` uma anulação `authority` que aceite o parâmetro.

Este artigo aplica-se a MSAL.NET 3.x. Para MSAL.NET 2.x, consulte [as especificidades do Azure AD B2C em MSAL 2.x](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/AAD-B2C-Specifics-MSAL-2.x) no MSAL.NET Wiki no GitHub.

## <a name="authority-for-an-azure-ad-b2c-tenant-and-policy"></a>Autoridade para um inquilino e política Azure AD B2C

O formato de autoridade para Azure AD B2C é:`https://{azureADB2CHostname}/tfp/{tenant}/{policyName}`

- `azureADB2CHostname`- O nome do inquilino Azure AD B2C mais o anfitrião. Por exemplo, *contosob2c.b2clogin.com.*
- `tenant`- O nome de domínio ou o id de diretório (inquilino) do inquilino Azure AD B2C. Por exemplo, *contosob2c.onmicrosoft.com* ou um GUID, respectivamente.
- `policyName`- O nome do fluxo do utilizador ou a política personalizada a aplicar. Por exemplo, uma política de inscrição/inscrição como *b2c_1_susi*.

Para obter mais informações sobre as autoridades azure AD B2C, consulte [set redirecionamento de URLs para b2clogin.com](../../active-directory-b2c/b2clogin.md).

## <a name="instantiating-the-application"></a>Instantaneamente a aplicação

Forneça a autoridade `WithB2CAuthority()` ligando quando criar o objeto de aplicação:

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

Adquirir um símbolo para uma API protegida por AD B2C em uma aplicação de cliente público requer que você use as substituições com uma autoridade:

```csharp
IEnumerable<IAccount> accounts = await application.GetAccountsAsync();
AuthenticationResult ar = await application.AcquireTokenInteractive(scopes)
                                           .WithAccount(GetAccountByPolicy(accounts, policy))
                                           .WithParentActivityOrWindow(ParentActivityOrWindow)
                                           .ExecuteAsync();
```

No código anterior:

- `policy`é uma cadeia que contém o nome do fluxo de utilizador Do `PolicySignUpSignIn`seu Azure AD B2C ou política personalizada (por exemplo, ).
- `ParentActivityOrWindow`é necessário para Android (a Atividade) e é opcional para outras plataformas que suportam um UI pai como janelas no Microsoft Windows e UIViewController no iOS. Para obter mais informações sobre o diálogo UI, consulte [WithParentActivityOrWindow](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Acquiring-tokens-interactively#withparentactivityorwindow) no Wiki MSAL.
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

A aplicação de uma política de fluxo de utilizador ou de costume (por exemplo, `AcquireTokenInteractive`permitindo que o utilizador edite o seu perfil ou redefinir a sua palavra-passe) é atualmente feita através da chamada . Para estas duas políticas, não utiliza o resultado de token/autenticação devolvido.

## <a name="profile-edit-policies"></a>Políticas de edição de perfil

Para permitir que os seus utilizadores se inscrevam com uma identidade social e, em seguida, editar o seu perfil, aplique a política de perfil de edição Azure AD AD B2C.

Faça-o ligando `AcquireTokenInteractive` com a autoridade para esta política. Como o utilizador já está inscrito e tem `Prompt.NoPrompt` uma sessão de cookies ativa, utilize para evitar que o diálogo de seleção de conta seja exibido.

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
    }
}
```

## <a name="resource-owner-password-credentials-ropc"></a>Credenciais de senha do proprietário de recursos (ROPC)

Para obter mais informações sobre o fluxo ROPC, consulte [Iniciar sessão com a concessão](v2-oauth-ropc.md)de credenciais de senha do proprietário de recursos.

O fluxo ROPC não é **recomendado** porque pedir a uma palavra-passe a um utilizador na sua aplicação não é seguro. Para obter mais informações sobre este problema, veja [Qual é a solução para o problema crescente das palavras-passe?](https://news.microsoft.com/features/whats-solution-growing-problem-passwords-says-microsoft/)

Ao utilizar o nome de utilizador/palavra-passe num fluxo ROPC, sacrifica várias coisas:

- Princípios fundamentais da identidade moderna: A palavra-passe pode ser pescada ou reproduzida porque o segredo partilhado pode ser intercetado. Por definição, o ROPC é incompatível com fluxos sem palavras-passe.
- Os utilizadores que necessitem de fazer MFA não poderão entrar (uma vez que não há interação).
- Os utilizadores não poderão utilizar um único sinal (SSO).

### <a name="configure-the-ropc-flow-in-azure-ad-b2c"></a>Configure o fluxo ROPC em Azure AD B2C

No seu inquilino Azure AD B2C, crie um novo fluxo de utilizador e selecione **Sign in usando ROPC** para ativar ropc para o fluxo do utilizador. Para mais informações, consulte [Configurar o fluxo](/azure/active-directory-b2c/configure-ropc)de credenciais de senha do proprietário do recurso .

`IPublicClientApplication`contém `AcquireTokenByUsernamePassword` o método:

```csharp
AcquireTokenByUsernamePassword(
            IEnumerable<string> scopes,
            string username,
            SecureString password)
```

Este `AcquireTokenByUsernamePassword` método toma os seguintes parâmetros:

- Os *âmbitos* para obter um sinal de acesso.
- Um *nome de utilizador*.
- Uma *senha* SecureString para o utilizador.

### <a name="limitations-of-the-ropc-flow"></a>Limitações do fluxo ROPC

O fluxo ROPC **funciona apenas para contas locais,** onde os seus utilizadores se registaram no Azure AD B2C utilizando um endereço de e-mail ou nome de utilizador. Este fluxo não funciona quando se federa a um fornecedor de identidade externo apoiado pelo Azure AD B2C (Facebook, Google, etc.).

## <a name="google-auth-and-embedded-webview"></a>Google auth e webview incorporado

Se estiver a usar o Google como fornecedor de identidade, recomendamos que utilize o navegador do sistema, uma vez que o Google não permite a [autenticação a partir de webviews incorporados](https://developers.googleblog.com/2016/08/modernizing-oauth-interactions-in-native-apps.html). Atualmente, `login.microsoftonline.com` é uma autoridade de confiança com a Google e trabalhará com webview incorporado. No `b2clogin.com` entanto, não é uma autoridade de confiança com a Google, pelo que os utilizadores não poderão autenticar.

Forneceremos uma atualização sobre este [problema](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/688) se as coisas mudarem.

## <a name="token-caching-in-msalnet"></a>Token caching em MSAL.NET

### <a name="known-issue-with-azure-ad-b2c"></a>Problema conhecido com Azure AD B2C

MSAL.NET suporta uma [cache simbólica.](/dotnet/api/microsoft.identity.client.tokencache?view=azure-dotnet) A chave de cache token baseia-se nos pedidos devolvidos pelo fornecedor de identidade (IDP).

Atualmente, MSAL.NET precisa de duas reivindicações para construir uma chave de cache simbólica:

- `tid`(id do inquilino da AD Azure)
- `preferred_username`

Ambas as alegações podem estar em falta em cenários De AD B2C do Azure porque nem todos os fornecedores de identidade social (Facebook, Google e outros) as devolvem nos tokens que regressam ao Azure AD B2C.

Um sintoma de tal cenário `Missing from the token response` é que `preferred_username` MSAL.NET retorna quando você acede ao valor de reclamação em fichas emitidas pelo Azure AD B2C. A MSAL `Missing from the token response` utiliza `preferred_username` o valor para manter a compatibilidade cruzada do cache entre bibliotecas.

### <a name="workarounds"></a>Soluções

#### <a name="mitigation-for-missing-tenant-id"></a>Atenuação para identidade de inquilino desaparecido

A suposições sugeridas é utilizar o [cache por política](#acquire-a-token-to-apply-a-policy) descrita anteriormente.

Em alternativa, pode `tid` utilizar a reclamação se estiver a utilizar [políticas personalizadas](../../active-directory-b2c/custom-policy-get-started.md) em Azure AD B2C. As políticas personalizadas podem devolver reclamações adicionais à sua aplicação utilizando a transformação de [sinistros.](/azure/active-directory-b2c/claims-transformation-technical-profile)

#### <a name="mitigation-for-missing-from-the-token-response"></a>Mitigação para "Desaparecido da resposta simbólica"

Uma opção é `name` utilizar `preferred_username`a reclamação em vez de . Para incluir `name` a reclamação em fichas de identificação emitidas pelo Azure AD B2C, selecione **'Display Name'** quando configurar o fluxo do utilizador.

Para obter mais informações sobre a especificação das reclamações devolvidas pelos fluxos do utilizador, consulte [Tutorial: Criar fluxos de utilizadores em Azure AD B2C](../../active-directory-b2c/tutorial-create-user-flows.md).

## <a name="next-steps"></a>Passos seguintes

Mais detalhes sobre a aquisição de fichas interactivamente com MSAL.NET para aplicações Azure AD B2C são fornecidos na amostra seguinte.

| Sample | Plataforma | Descrição|
|------ | -------- | -----------|
|[active-directy-b2c-xamarin-nativo](https://github.com/Azure-Samples/active-directory-b2c-xamarin-native) | Xamarin iOS, Xamarin Android, UWP | Uma aplicação Xamarin Forms que usa MSAL.NET para autenticar os utilizadores através do Azure AD B2C e depois aceder a uma API web com os tokens devolvidos.|
