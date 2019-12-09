---
title: Azure AD B2C (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: Saiba mais sobre considerações específicas ao usar Azure AD B2C com a biblioteca de autenticação da Microsoft para .NET (MSAL.NET).
services: active-directory
author: TylerMSFT
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/29/2019
ms.author: jeferrie
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: b8940ca6887e5c37659dd5b8d5a24ba7a2f4b889
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/08/2019
ms.locfileid: "74921932"
---
# <a name="use-msalnet-to-sign-in-users-with-social-identities"></a>Usar o MSAL.NET para conectar usuários com identidades sociais

Você pode usar o MSAL.NET para conectar usuários com identidades sociais usando [Azure Active Directory B2C (Azure ad B2C)](https://aka.ms/aadb2c). Azure AD B2C é criado com base na noção de políticas. No MSAL.NET, a especificação de uma política se traduz em fornecer uma autoridade.

- Ao instanciar o aplicativo cliente público, você precisa especificar a política na autoridade.
- Quando desejar aplicar uma política, você precisará chamar uma substituição de `AcquireTokenInteractive` que contém um parâmetro `authority`.

Esta página é para MSAL 3. x. Se você estiver interessado no MSAL 2. x, consulte [Azure ad B2C especificos no MSAL 2. x](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/AAD-B2C-Specifics-MSAL-2.x).

## <a name="authority-for-a-azure-ad-b2c-tenant-and-policy"></a>Autoridade para um locatário Azure AD B2C e uma política

A autoridade a ser usada é `https://{azureADB2CHostname}/tfp/{tenant}/{policyName}` em que:

- `azureADB2CHostname` é o nome do locatário de Azure AD B2C mais o host (por exemplo `{your-tenant-name}.b2clogin.com`),
- `tenant` é o nome completo do locatário do Azure AD B2C (por exemplo, `{your-tenant-name}.onmicrosoft.com`) ou o GUID do locatário, 
- `policyName` o nome da política ou do fluxo do usuário a ser aplicado (por exemplo, "b2c_1_susi" para inscrição/entrada).

Para obter mais informações sobre as autoridades de Azure AD B2C, consulte esta [documentação](/azure/active-directory-b2c/b2clogin).

## <a name="instantiating-the-application"></a>Instanciando o aplicativo

Ao criar o aplicativo, você precisa fornecer a autoridade.

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

## <a name="acquire-a-token-to-apply-a-policy"></a>Adquirir um token para aplicar uma política

Adquirir um token para uma API protegida Azure AD B2C em um aplicativo cliente público exige que você use as substituições com uma autoridade:

```csharp
IEnumerable<IAccount> accounts = await application.GetAccountsAsync();
AuthenticationResult ar = await application .AcquireTokenInteractive(scopes)
                                            .WithAccount(GetAccountByPolicy(accounts, policy))
                                            .WithParentActivityOrWindow(ParentActivityOrWindow)
                                            .ExecuteAsync();
```

Por:

- `policy` sendo uma das cadeias de caracteres anteriores (por exemplo `PolicySignUpSignIn`).
- `ParentActivityOrWindow` é necessário para o Android (a atividade) e opcional para outras plataformas que dão suporte à interface do usuário pai, como Windows no Windows e UIViewController no iOS. Veja mais informações [aqui na caixa de diálogo da interface do usuário](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Acquiring-tokens-interactively#withparentactivityorwindow).
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

A aplicação de uma política ou fluxo de usuário (por exemplo, permitindo que o usuário final edite seu perfil ou Redefina sua senha) é feita no momento chamando `AcquireTokenInteractive`. No caso dessas duas políticas, você não usa o resultado de token/autenticação retornado.

## <a name="special-case-of-editprofile-and-resetpassword-policies"></a>Caso especial de políticas de EditProfile e ResetPassword

Quando desejar fornecer uma experiência em que os usuários finais se conectem com uma identidade social e editem seu perfil, você deseja aplicar a política de Azure AD B2C Editar perfil. A maneira de fazer isso é chamando `AcquireTokenInteractive` com a autoridade específica para essa política e um prompt definido como `Prompt.NoPrompt` para impedir que a caixa de diálogo de seleção de conta seja exibida (pois o usuário já está conectado e tem uma sessão de cookie ativa).

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
## <a name="resource-owner-password-credentials-ropc-with-azure-ad-b2c"></a>Credenciais de senha do proprietário do recurso (ROPC) com Azure AD B2C
Para obter mais detalhes sobre o fluxo do ROPC, consulte esta [documentação](v2-oauth-ropc.md).

Esse fluxo **não é recomendado** porque seu aplicativo solicita a senha de um usuário não é seguro. Para obter mais informações sobre esse problema, consulte [Este artigo](https://news.microsoft.com/features/whats-solution-growing-problem-passwords-says-microsoft/). 

Ao usar o nome de usuário/senha, você está dando várias coisas:
- principais princípios da identidade moderna: a senha é inpeixe e reproduzida. Porque temos esse conceito de um segredo de compartilhamento que pode ser interceptado. Isso é incompatível com sem senha.
- Os usuários que precisam fazer a MFA não poderão entrar (como não há nenhuma interação).
- Os usuários não poderão fazer logon único.

### <a name="configure-the-ropc-flow-in-azure-ad-b2c"></a>Configurar o fluxo ROPC no Azure AD B2C
No locatário do Azure AD B2C, crie um novo fluxo de usuário e selecione **entrar usando ROPC**. Isso habilitará a política ROPC para seu locatário. Consulte [Configurar o fluxo de credenciais de senha do proprietário do recurso](/azure/active-directory-b2c/configure-ropc) para obter mais detalhes.

`IPublicClientApplication` contém um método:
```csharp
AcquireTokenByUsernamePassword(
            IEnumerable<string> scopes,
            string username,
            SecureString password)
```

Esse método usa como parâmetros:
- Os *escopos* para os quais solicitar um token de acesso.
- Um *nome de usuário*.
- Uma *senha* de SecureString para o usuário.

Lembre-se de usar a autoridade que contém a política ROPC.

### <a name="limitations-of-the-ropc-flow"></a>Limitações do fluxo de ROPC
 - O fluxo ROPC **só funciona para contas locais** (em que você se registra com Azure ad B2C usando um email ou nome de usuário). Esse fluxo não funcionará se for federado a qualquer um dos provedores de identidade com suporte pelo Azure AD B2C (Facebook, Google etc.).

## <a name="google-auth-and-embedded-webview"></a>Google auth e Embedded WebView

Se você for um desenvolvedor de Azure AD B2C usando o Google como um provedor de identidade, recomandore usará o navegador do sistema, pois o Google não permite a [autenticação de webviews inseridos](https://developers.googleblog.com/2016/08/modernizing-oauth-interactions-in-native-apps.html). Atualmente, `login.microsoftonline.com` é uma autoridade confiável com o Google. O uso dessa autoridade funcionará com o WebView incorporado. No entanto, usar `b2clogin.com` não é uma autoridade confiável com o Google, de modo que os usuários não poderão se autenticar.

Forneceremos uma atualização para esse [problema](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/688) se as coisas forem alteradas.

## <a name="caching-with-azure-ad-b2c-in-msalnet"></a>Caching com Azure AD B2C em MSAL.Net 

### <a name="known-issue-with-azure-ad-b2c"></a>Problema conhecido com o Azure AD B2C

O MSAL.Net dá suporte a um [cache de token](/dotnet/api/microsoft.identity.client.tokencache?view=azure-dotnet). A chave de cache de token é baseada nas declarações retornadas pelo provedor de identidade. Atualmente, o MSAL.Net precisa de duas declarações para criar uma chave de cache de token:  
- `tid` que é a ID de locatário do Azure AD e 
- `preferred_username` 

Ambas as declarações estão ausentes em muitos dos cenários de Azure AD B2C. 

O impacto do cliente é que, ao tentar exibir o campo username, você está obtendo "faltando na resposta do token" como o valor? Nesse caso, isso ocorre porque Azure AD B2C não retorna um valor no token para o preferred_username devido a limitações com as contas sociais e os IdPs (provedores de identidade externa). O Azure AD retorna um valor para preferred_username porque ele sabe quem é o usuário, mas por Azure AD B2C, porque o usuário pode entrar com uma conta local, Facebook, Google, GitHub etc. não há um valor consistente para Azure AD B2C usar para preferred_username. Para desbloquear o MSAL de distribuir a compatibilidade de cache com a ADAL, decidimos usar "ausente da resposta de token" em nosso lado ao lidar com as contas de Azure AD B2C quando o token não retorna nada para preferred_username. MSAL deve retornar um valor para preferred_username para manter a compatibilidade de cache entre bibliotecas.

### <a name="workarounds"></a>Soluções alternativas

#### <a name="mitigation-for-the-missing-tenant-id"></a>Mitigação para a ID de locatário ausente

A solução alternativa sugerida é usar o [cache por política](#acquire-a-token-to-apply-a-policy)

Como alternativa, você pode usar a declaração de `tid`, se estiver usando as [políticas personalizadas do B2C](https://aka.ms/ief), pois ela fornece a capacidade de retornar declarações adicionais ao aplicativo. Para saber mais sobre a [transformação de declarações](/azure/active-directory-b2c/claims-transformation-technical-profile)

#### <a name="mitigation-for-missing-from-the-token-response"></a>Mitigação para "ausente da resposta do token"
Uma opção é usar a declaração "Name" como o nome de usuário preferencial. O processo é mencionado neste > de [documentos B2C](../../active-directory-b2c/active-directory-b2c-reference-policies.md) "na coluna declaração de retorno, escolha as declarações que você deseja que sejam retornadas nos tokens de autorização enviados de volta para o aplicativo após uma experiência de edição de perfil bem-sucedida. Por exemplo, selecione nome de exibição, CEP. "

## <a name="next-steps"></a>Passos seguintes 

Mais detalhes sobre como adquirir tokens interativamente com o MSAL.NET para aplicativos Azure AD B2C são fornecidos no exemplo a seguir.

| Exemplo | Plataforma | Descrição|
|------ | -------- | -----------|
|[active-directory-b2c-xamarin-native](https://github.com/Azure-Samples/active-directory-b2c-xamarin-native) | Xamarin iOS, Xamarin Android, UWP | Um aplicativo Xamarin Forms simples mostrando como usar o MSAL.NET para autenticar usuários por meio de Azure AD B2C e acessar uma API Web com os tokens resultantes.|
