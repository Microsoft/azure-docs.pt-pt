---
title: O Azure AD B2C (biblioteca de autenticação da Microsoft para .NET) | Azure
description: Saiba mais sobre as considerações específicas ao utilizar o Azure AD B2C com a biblioteca de autenticação da Microsoft para .NET (MSAL.NET).
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/24/2019
ms.author: ryanwi
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6c26a5007c2dcaa5d41be46f685f0f259866ca2c
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/11/2019
ms.locfileid: "65544064"
---
# <a name="use-msalnet-to-sign-in-users-with-social-identities"></a>Utilizar MSAL.NET para iniciar sessão dos utilizadores com identidades sociais

Pode usar MSAL.NET para iniciar sessão dos utilizadores com identidades sociais usando [do Azure Active Directory B2C (Azure AD B2C)](https://aka.ms/aadb2c). O Azure AD B2C é criado em torno da noção de políticas. No MSAL.NET, a especificação de uma política de traduz-se ao fornecimento de uma autoridade.

- Quando instancia o aplicativo cliente público, tem de especificar a política de autoridade de.
- Quando deseja aplicar uma política, precisa chamar uma substituição do `AcquireTokenInteractive` que contém um `authority` parâmetro.

Esta página é para MSAL 3.x. Se estiver interessado em MSAL 2.x, consulte [especificidades do Azure AD B2C no MSAL 2.x](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/AAD-B2C-Specifics-MSAL-2.x).

## <a name="authority-for-a-azure-ad-b2c-tenant-and-policy"></a>Autoridade para um inquilino do Azure AD B2C e a política

A autoridade para utilizar é `https://login.microsoftonline.com/tfp/{tenant}/{policyName}` onde:

- `tenant` é o nome do inquilino do Azure AD B2C, 
- `policyName` o nome da política para aplicar (por exemplo "b2c_1_susi" para o início de sessão-em/inscrever-se).

A orientação atual do Azure AD B2C é usar `b2clogin.com` como a autoridade. Por exemplo, `$"https://{your-tenant-name}.b2clogin.com/tfp/{your-tenant-ID}/{policyname}"`. Para obter mais informações, consulte esta [documentação](/azure/active-directory-b2c/b2clogin).

## <a name="instantiating-the-application"></a>Criar uma instância da aplicação

Ao criar a aplicação, terá de fornecer a autoridade.

```csharp
// Azure AD B2C Coordinates
public static string Tenant = "fabrikamb2c.onmicrosoft.com";
public static string ClientID = "90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6";
public static string PolicySignUpSignIn = "b2c_1_susi";
public static string PolicyEditProfile = "b2c_1_edit_profile";
public static string PolicyResetPassword = "b2c_1_reset";

public static string AuthorityBase = $"https://fabrikamb2c.b2clogin.com/tfp/{Tenant}/";
public static string Authority = $"{AuthorityBase}{PolicySignUpSignIn}";
public static string AuthorityEditProfile = $"{AuthorityBase}{PolicyEditProfile}";
public static string AuthorityPasswordReset = $"{AuthorityBase}{PolicyResetPassword}";

application = PublicClientApplicationBuilder.Create(ClientID)
               .WithB2CAuthority(Authority)
               .Build();
```

## <a name="acquire-a-token-to-apply-a-policy"></a>Adquirir um token para aplicar uma política

Adquirir um token para um Azure AD B2C o API protegido num aplicativo cliente pública requer a utilização de substituições com uma autoridade de:

```csharp
IEnumerable<IAccount> accounts = await application.GetAccountsAsync();
AuthenticationResult ar = await application .AcquireToken(scopes, parentWindow)
                                            .WithAccount(GetAccountByPolicy(accounts, policy))
                                            .ExecuteAsync();
```

Por:

- `policy` sendo uma das cadeias de caracteres anteriores (por exemplo `PolicySignUpSignIn`).
- `GetAccountByPolicy(IEnumerable<IAccount>, string)` é um método que localiza uma conta para uma determinada política. Por exemplo:

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

Aplicar uma política (por exemplo permitindo que o utilizador final, editar o respetivo perfil ou repor a palavra-passe) atualmente é feito chamando `AcquireTokenInteractive`. No caso destas duas políticas não utiliza o token retornado / resultar de autenticação.

## <a name="special-case-of-editprofile-and-resetpassword-policies"></a>Caso especial de políticas de EditProfile e ResetPassword

Quando deseja fornecer uma experiência onde os utilizadores finais iniciar sessão com uma identidade de redes sociais e, em seguida, editar o respetivo perfil que pretende aplicar a política do Azure AD B2C EditProfile. A forma de fazer isso, é chamando `AcquireTokenInteractive` com a autoridade específica para essa política e uma linha de comandos definida como `Prompt.NoPrompt` para evitar a caixa de diálogo de seleção de conta para ser exibida (como o utilizador já está com sessão iniciada)

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
## <a name="resource-owner-password-credentials-ropc-with-azure-ad-b2c"></a>Credenciais proprietário do recurso palavra-passe (ROPC) com o Azure AD B2C
Para obter mais detalhes sobre o fluxo ROPC, consulte este [documentação](v2-oauth-ropc.md).

Este fluxo é **não recomendada** porque seu aplicativo perguntar um utilizador a palavra-passe não é seguro. Para obter mais informações sobre este problema, consulte [este artigo](https://news.microsoft.com/features/whats-solution-growing-problem-passwords-says-microsoft/). 

Ao utilizar o nome de utilizador/palavra-passe, está fornecendo-up uma série de coisas:
- núcleos de inquilinos de identidade moderna: palavra-passe obtém fished, repetidos. Como temos este conceito de um segredo de partilha que pode ser interceptado. Isso é incompatível com o início.
- Os utilizadores que precisam de fazer a MFA não será possível iniciar sessão (como não há nenhuma interação).
- Os utilizadores não será capazes de início de sessão único.

### <a name="configure-the-ropc-flow-in-azure-ad-b2c"></a>Configurar o fluxo ROPC no Azure AD B2C
No seu inquilino do Azure AD B2C, criar um novo fluxo de utilizador e selecione **iniciar sessão com ROPC**. Isso permitirá que a política ROPC para o seu inquilino. Ver [configurar as credenciais de palavra-passe de proprietário do recurso do fluxo](/azure/active-directory-b2c/configure-ropc) para obter mais detalhes.

`IPublicClientApplication` contém um método:
```csharp
AcquireTokenByUsernamePassword(
            IEnumerable<string> scopes,
            string username,
            SecureString password)
```

Este método aceita como parâmetros:
- O *âmbitos* para pedir um token de acesso.
- R *nome de utilizador*.
- Uma SecureString *palavra-passe* para o utilizador.

Lembre-se utilizar a autoridade que contém a política ROPC.

### <a name="limitations-of-the-ropc-flow"></a>Limitações do fluxo ROPC
 - O fluxo ROPC **funciona apenas para contas locais** (em que se registrar com o Azure AD B2C através de um e-mail ou nome de utilizador). Este fluxo não funciona se o Federação para qualquer um dos provedores de identidade suportados pelo Azure AD B2C (Facebook, Google, etc.).
 - Atualmente, não há **nenhuma id_token devolvido do Azure AD B2C** ao implementar o fluxo ROPC de MSAL. Isso significa que não é possível criar um objeto de conta, que na cache, será a nenhuma conta e nenhum utilizador. O fluxo de AcquireTokenSilent não funcionará neste cenário. No entanto, ROPC não mostra uma interface do Usuário, então, aqui vai sem afetar a experiência do usuário.

## <a name="google-auth-and-embedded-webview"></a>Autenticação do Google e Embedded Webview

Se for um programador do Azure AD B2C com a Google como fornecedor de identidade recommand usa o navegador de sistema, como o Google não permite [autenticação de incorporadas](https://developers.googleblog.com/2016/08/modernizing-oauth-interactions-in-native-apps.html). Atualmente, `login.microsoftonline.com` é uma autoridade confiável com o Google. Usar esta autoridade funcionará com o embedded webview. No entanto usando `b2clogin.com` não é uma autoridade confiável com o Google, para que os utilizadores não poderão autenticar.

Forneceremos uma atualização para o wiki e isso [problema](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/688) se as coisas mudam.

## <a name="caching-with-azure-ad-b2c-in-msalnet"></a>Colocação em cache com o Azure AD B2C no MSAL.Net 

### <a name="known-issue-with-azure-ad-b2c"></a>Problema conhecido com o Azure AD B2C

MSAL.Net suporta um [cache de token](/dotnet/api/microsoft.identity.client.tokencache?view=azure-dotnet). O token de chave de colocação em cache é baseado em afirmações devolvidas pelo fornecedor de identidade. Atualmente MSAL.Net precisa duas afirmações para criar uma chave de cache de token:  
- `tid` qual é o ID de inquilino do Azure AD, e 
- `preferred_username` 

Ambas essas declarações estão em falta em muitos dos cenários do Azure AD B2C. 

O impacto de cliente é que ao tentar exibir o campo de nome de utilizador, está obtendo "Em falta da resposta token" como o valor? Se assim for, isso é porque o Azure AD B2C não devolve um valor no IdToken para o preferred_username devido às limitações com as contas de redes sociais e fornecedores de identidade externo (IdPs). Azure AD devolve um valor para preferred_username porque ele sabe quem é o utilizador, mas para o Azure AD B2C, uma vez que o utilizador pode iniciar sessão com uma conta local, Facebook, Google, GitHub, etc. há não é um valor consistente para o Azure AD B2C utilizar para preferred_username. Para desbloquear a MSAL de lançar a compatibilidade de cache adal, decidimos usar "Em falta da resposta token" do nosso lado ao lidar com as contas do Azure AD B2C, quando o IdToken não retorna nada para preferred_username. A MSAL tem de devolver um valor para preferred_username manter a compatibilidade de cache entre as bibliotecas.

### <a name="workarounds"></a>Soluções alternativas

#### <a name="mitigation-for-the-missing-tenant-id"></a>Mitigação para o ID de inquilino em falta

A solução alternativa sugerida é usar o [colocação em cache por política](#acquire-a-token-to-apply-a-policy)

Em alternativa, pode utilizar o `tid` de afirmação, se estiver a utilizar o [as políticas personalizadas do B2C](https://aka.ms/ief), porque fornece a capacidade para devolver afirmações adicionais para a aplicação. Para saber mais sobre [transformação de declarações](/azure/active-directory-b2c/claims-transformation-technical-profile)

#### <a name="mitigation-for-missing-from-the-token-response"></a>Atenuação para "Em falta da resposta token"
Uma opção é usar a afirmação de "nome" como o nome de utilizador preferencial. O processo é mencionado nisso [B2C doc](/azure/active-directory-b2c/active-directory-b2c-reference-policies#frequently-asked-questions) -> "da coluna de declaração de retorno, escolha as afirmações que quer que sejam devolvidas nos tokens de autorização enviados para a sua aplicação depois de uma experiência de edição de perfis com êxito. Por exemplo, selecione nome a apresentar, Código Postal."

## <a name="next-steps"></a>Passos Seguintes 

No exemplo a seguir, são fornecidos mais detalhes sobre a aquisição de tokens, de forma interativa com MSAL.NET para aplicações do Azure AD B2C.

| Exemplo | Plataforma | Descrição|
|------ | -------- | -----------|
|[active-directory-b2c-xamarin-native](https://github.com/Azure-Samples/active-directory-b2c-xamarin-native) | Xamarin iOS, Xamarin Android, UWP | Uma aplicação Xamarin Forms simples que mostra como utilizar MSAL.NET para autenticar os utilizadores através do Azure AD B2C e aceder a uma API Web com os tokens resultantes.|
