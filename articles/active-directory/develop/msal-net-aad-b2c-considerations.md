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
ms.custom: devx-track-csharp, aaddev
ms.openlocfilehash: ed3e9da628ab779ab47673fa2ce728c5c25539be
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88166438"
---
# <a name="use-msalnet-to-sign-in-users-with-social-identities"></a>Utilize MSAL.NET para assinar em utilizadores com identidades sociais

Pode utilizar MSAL.NET para iniciar súbs nos utilizadores com identidades sociais utilizando [o Azure Ative Directory B2C (Azure AD B2C)](https://aka.ms/aadb2c). Azure AD B2C é construído em torno da noção de políticas. Em MSAL.NET, especificar uma política traduz-se em fornecer uma autoridade.

- Ao instantanear a aplicação do cliente público, tem de especificar a apólice como parte da autoridade.
- Quando quiser aplicar uma apólice, chame uma anulação `AcquireTokenInteractive` que aceite o `authority` parâmetro.

Este artigo aplica-se a MSAL.NET 3.x. Para MSAL.NET 2.x, consulte [as especificidades do Azure AD B2C em MSAL 2.x](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/AAD-B2C-Specifics-MSAL-2.x) no MSAL.NET Wiki no GitHub.

## <a name="authority-for-an-azure-ad-b2c-tenant-and-policy"></a>Autoridade para um inquilino e política Azure AD B2C

O formato de autoridade para Azure AD B2C é: `https://{azureADB2CHostname}/tfp/{tenant}/{policyName}`

- `azureADB2CHostname` - O nome do inquilino Azure AD B2C mais o anfitrião. Por exemplo, *contosob2c.b2clogin.com.*
- `tenant` - O nome de domínio ou o diretório (inquilino) ID do inquilino Azure AD B2C. Por exemplo, *contosob2c.onmicrosoft.com* ou um GUID, respectivamente.
- `policyName` - O nome do fluxo do utilizador ou da política personalizada a aplicar. Por exemplo, uma política de inscrição/inscrição como *b2c_1_susi*.

Para obter mais informações sobre as autoridades Azure AD B2C, consulte [Definir redirecionar URLs para b2clogin.com](../../active-directory-b2c/b2clogin.md).

## <a name="instantiating-the-application"></a>Instantaneamente a aplicação

Forneça a autoridade ligando `WithB2CAuthority()` quando criar o objeto de aplicação:

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

Adquirir um símbolo para uma API protegida por Azure AD B2C numa aplicação de cliente público requer que você use as sobreposições com uma autoridade:

```csharp
IEnumerable<IAccount> accounts = await application.GetAccountsAsync();
AuthenticationResult ar = await application.AcquireTokenInteractive(scopes)
                                           .WithAccount(GetAccountByPolicy(accounts, policy))
                                           .WithParentActivityOrWindow(ParentActivityOrWindow)
                                           .ExecuteAsync();
```

No corte de código anterior:

- `policy` é uma cadeia que contém o nome do fluxo de utilizador Azure AD B2C ou da política personalizada (por exemplo, `PolicySignUpSignIn` ).
- `ParentActivityOrWindow` é necessário para Android (a Atividade) e é opcional para outras plataformas que suportam um UI principal como janelas no Microsoft Windows e UIViewController no iOS. Para obter mais informações sobre o diálogo UI, consulte [WithParentActivityOrWindow](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Acquiring-tokens-interactively#withparentactivityorwindow) no MSAL Wiki.
- `GetAccountByPolicy(IEnumerable<IAccount>, string)` é um método que encontra uma conta para uma determinada política. Por exemplo:

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

A aplicação de uma política de fluxo de utilizador ou de costume (por exemplo, permitir que o utilizador edite o seu perfil ou reinicie a sua palavra-passe) é atualmente feita através da chamada `AcquireTokenInteractive` . Para estas duas apólices, não se utiliza o resultado de token/autenticação devolvido.

## <a name="profile-edit-policies"></a>Políticas de edição de perfis

Para permitir que os seus utilizadores assinem com uma identidade social e depois editem o seu perfil, aplique a política de perfil de edição Azure AD B2C.

Faça-o chamando `AcquireTokenInteractive` com autoridade para esta política. Como o utilizador já está inscrito e tem uma sessão de cookies ativa, utilize `Prompt.NoPrompt` para evitar que o diálogo de seleção da conta seja apresentado.

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

Para obter mais informações sobre o fluxo ROPC, consulte [Iniciar sôs com o subsídio de credenciais de senha do proprietário do recurso.](v2-oauth-ropc.md)

O fluxo ROPC não é **recomendado** porque pedir a um utilizador a sua palavra-passe na sua aplicação não é seguro. Para obter mais informações sobre este problema, veja [qual é a solução para o problema crescente das senhas?](https://news.microsoft.com/features/whats-solution-growing-problem-passwords-says-microsoft/)

Ao utilizar o nome de utilizador/palavra-passe num fluxo ROPC, sacrifica-se várias coisas:

- Princípios fundamentais da identidade moderna: A palavra-passe pode ser pescada ou reproduzida porque o segredo partilhado pode ser intercetado. Por definição, o ROPC é incompatível com fluxos sem palavras-passe.
- Os utilizadores que necessitem de fazer MFA não poderão iniciar sação (uma vez que não existe interação).
- Os utilizadores não poderão utilizar uma única sso (SSO).

### <a name="configure-the-ropc-flow-in-azure-ad-b2c"></a>Configure o fluxo ROPC em Azure AD B2C

No seu inquilino Azure AD B2C, crie um novo fluxo de utilizador e selecione **Iniciar sessão utilizando o ROPC** para permitir o ropc para o fluxo do utilizador. Para obter mais informações, consulte [configurar o fluxo de credenciais de senha do proprietário do recurso](../../active-directory-b2c/configure-ropc.md).

`IPublicClientApplication` contém o `AcquireTokenByUsernamePassword` método:

```csharp
AcquireTokenByUsernamePassword(
            IEnumerable<string> scopes,
            string username,
            SecureString password)
```

Este `AcquireTokenByUsernamePassword` método tem os seguintes parâmetros:

- As *miras* para obter um token de acesso.
- Um *nome de utilizador.*
- Uma *palavra-passe* SecureString para o utilizador.

### <a name="limitations-of-the-ropc-flow"></a>Limitações do fluxo ROPC

O fluxo ROPC **funciona apenas para contas locais,** onde os seus utilizadores se registaram no Azure AD B2C utilizando um endereço de e-mail ou nome de utilizador. Este fluxo não funciona quando se federa a um fornecedor de identidade externo apoiado pelo Azure AD B2C (Facebook, Google, etc.).

## <a name="google-auth-and-embedded-webview"></a>Google auth e webview incorporado

Se estiver a utilizar o Google como fornecedor de identidade, recomendamos que utilize o navegador do sistema, uma vez que o Google não permite [a autenticação a partir de webviews incorporados.](https://developers.googleblog.com/2016/08/modernizing-oauth-interactions-in-native-apps.html) Atualmente, `login.microsoftonline.com` é uma autoridade de confiança com o Google e trabalhará com webview incorporado. No entanto, `b2clogin.com` não é uma autoridade de confiança com a Google, pelo que os utilizadores não poderão autenticar.

Forneceremos uma atualização para este [problema](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/688) se as coisas mudarem.

## <a name="token-caching-in-msalnet"></a>Token caching em MSAL.NET

### <a name="known-issue-with-azure-ad-b2c"></a>Problema conhecido com Azure AD B2C

MSAL.NET suporta uma [cache simbólica.](/dotnet/api/microsoft.identity.client.tokencache?view=azure-dotnet) A chave de caching token baseia-se nos pedidos devolvidos pelo fornecedor de identidade (IdP).

Atualmente, MSAL.NET precisa de duas reivindicações para construir uma chave de cache simbólica:

- `tid` (o ID do inquilino da AZure)
- `preferred_username`

Ambas as alegações podem estar em falta nos cenários Azure AD B2C porque nem todos os fornecedores de identidade social (Facebook, Google e outros) as devolvem nos tokens que regressam ao Azure AD B2C.

Um sintoma de tal cenário é que MSAL.NET devoluções `Missing from the token response` quando você acede ao valor de `preferred_username` reclamação em fichas emitidas por Azure AD B2C. A MSAL utiliza o `Missing from the token response` valor para manter a `preferred_username` compatibilidade cruzada entre bibliotecas.

### <a name="workarounds"></a>Soluções

#### <a name="mitigation-for-missing-tenant-id"></a>Mitigação para iD do inquilino desaparecido

A solução proposta é utilizar [o caching pela política](#acquire-a-token-to-apply-a-policy) descrita anteriormente.

Em alternativa, pode utilizar a `tid` reclamação se estiver a utilizar [políticas personalizadas](../../active-directory-b2c/custom-policy-get-started.md) em Azure AD B2C. As políticas personalizadas podem devolver reclamações adicionais à sua aplicação utilizando a [transformação de sinistros.](../../active-directory-b2c/claims-transformation-technical-profile.md)

#### <a name="mitigation-for-missing-from-the-token-response"></a>Mitigação por "Falta da resposta simbólica"

Uma opção é utilizar a `name` reclamação em vez de `preferred_username` . Para incluir a `name` reclamação em fichas de identificação emitidas pelo Azure AD B2C, selecione **o Display Name** quando configurar o fluxo do utilizador.

Para obter mais informações sobre a especificação das reclamações devolvidas pelos fluxos do seu utilizador, consulte [Tutorial: Crie fluxos de utilizador em Azure AD B2C](../../active-directory-b2c/tutorial-create-user-flows.md).

## <a name="next-steps"></a>Passos seguintes

São fornecidos mais detalhes sobre a aquisição de fichas interativamente com MSAL.NET para aplicações Azure AD B2C na amostra seguinte.

| Sample | Plataforma | Descrição|
|------ | -------- | -----------|
|[active-directy-b2c-xamarin-nativo](https://github.com/Azure-Samples/active-directory-b2c-xamarin-native) | Xamarin iOS, Xamarin Android, UWP | Uma aplicação Xamarin Forms que utiliza MSAL.NET para autenticar os utilizadores através do Azure AD B2C e, em seguida, acede a uma API web com os tokens devolvidos.|