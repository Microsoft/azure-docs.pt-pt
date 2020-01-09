---
title: Migrando para o MSAL.NET
titleSuffix: Microsoft identity platform
description: Saiba mais sobre as diferenças entre a MSAL.NET (biblioteca de autenticação da Microsoft para .NET) e a ADAL.NET (biblioteca de autenticação do Azure AD para .NET) e como migrar para o MSAL.NET.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/10/2019
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9aa2bf2bb2e77f5e543b53b583ddeeacd46de243
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75424180"
---
# <a name="migrating-applications-to-msalnet"></a>Migrando aplicativos para o MSAL.NET

A MSAL.NET (biblioteca de autenticação da Microsoft para .NET) e a ADAL.NET (biblioteca de autenticação do Azure AD para .NET) são usadas para autenticar entidades do Azure AD e solicitar tokens do Azure AD. Até agora, a maioria dos desenvolvedores trabalhou com o Azure AD para a plataforma de desenvolvedores (v 1.0) para autenticar identidades do Azure AD (contas corporativas e de estudante) solicitando tokens usando a ADAL (biblioteca de autenticação do Azure AD). Usando MSAL:

- Você pode autenticar um conjunto mais amplo de identidades da Microsoft (identidades do Azure AD e contas da Microsoft e contas sociais e locais por meio do Azure AD B2C), pois ele usa o ponto de extremidade da plataforma de identidade da Microsoft,
- os usuários terão a melhor experiência de logon único.
- seu aplicativo pode habilitar o consentimento incremental e o suporte ao acesso condicional é mais fácil
- Você se beneficia da inovação.

**MSAL.net agora é a biblioteca de autenticação recomendada para usar com a plataforma de identidade da Microsoft**. Nenhum recurso novo será implementado em ADAL.NET. Os esforços se concentram em melhorar o MSAL.

Este artigo descreve as diferenças entre a biblioteca de autenticação da Microsoft para .NET (MSAL.NET) e a biblioteca de autenticação do AD do Azure para .NET (ADAL.NET) e ajuda a migrar para o MSAL.  

## <a name="differences-between-adal-and-msal-apps"></a>Diferenças entre os aplicativos ADAL e MSAL

Na maioria dos casos, você deseja usar o MSAL.NET e o ponto de extremidade da plataforma Microsoft Identity, que é a última geração de bibliotecas de autenticação da Microsoft. Usando o MSAL.NET, você adquire tokens para usuários que se conectam ao seu aplicativo com o Azure AD (contas corporativas e de estudante), MSA (contas pessoais) da Microsoft ou Azure AD B2C. 

Se você já estiver familiarizado com o ponto de extremidade do Azure AD para desenvolvedores (v 1.0) (e ADAL.NET), talvez queira ler [o que há de diferente no ponto de extremidade da plataforma Microsoft Identity (v 2.0)?](active-directory-v2-compare.md).

No entanto, você ainda precisará usar o ADAL.NET se seu aplicativo precisar conectar usuários com versões anteriores do [serviços de Federação do Active Directory (AD FS) (ADFS)](/windows-server/identity/active-directory-federation-services). Para obter mais informações, consulte [suporte do ADFS](https://aka.ms/msal-net-adfs-support).

A imagem a seguir resume algumas das diferenças entre ADAL.NET e MSAL.NET ![código lado a lado](./media/msal-compare-msaldotnet-and-adaldotnet/differences.png)

### <a name="nuget-packages-and-namespaces"></a>Pacotes e namespaces do NuGet

ADAL.NET é consumido do pacote NuGet [Microsoft. IdentityModel. clients. ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory) . o namespace a ser usado é `Microsoft.IdentityModel.Clients.ActiveDirectory`.

Para usar o MSAL.NET, será necessário adicionar o pacote NuGet [Microsoft. Identity. Client](https://www.nuget.org/packages/Microsoft.Identity.Client) e usar o namespace `Microsoft.Identity.Client`

### <a name="scopes-not-resources"></a>Escopos não recursos

O ADAL.NET adquire tokens para *recursos*, mas o MSAL.net adquire tokens para *escopos*. Várias substituições de MSAL.NET AcquireToken exigem um parâmetro chamado escopos (`IEnumerable<string> scopes`). Esse parâmetro é uma lista simples de cadeias de caracteres que declaram as permissões e os recursos desejados que são solicitados. Escopos bem conhecidos são os [escopos de Microsoft Graph](/graph/permissions-reference).

Também é possível em MSAL.NET acessar recursos v 1.0. Consulte os detalhes em [escopos para um aplicativo v 1.0](#scopes-for-a-web-api-accepting-v10-tokens). 

### <a name="core-classes"></a>Classes principais

- O ADAL.NET usa [AuthenticationContext](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/AuthenticationContext:-the-connection-to-Azure-AD) como a representação de sua conexão com o serviço de token de segurança (STS) ou servidor de autorização, por meio de uma autoridade. Em contrário, o MSAL.NET é projetado em relação [aos aplicativos cliente](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Client-Applications). Ele fornece duas classes separadas: `PublicClientApplication` e `ConfidentialClientApplication`

- Aquisição de tokens: ADAL.NET e MSAL.NET têm as mesmas chamadas de autenticação (`AcquireTokenAsync` e `AcquireTokenSilentAsync` para ADAL.NET e `AcquireTokenInteractive` e `AcquireTokenSilent` em MSAL.NET), mas com parâmetros diferentes necessários. Uma diferença é o fato de que, em MSAL.NET, você não precisa mais passar o `ClientID` de seu aplicativo em todas as chamadas de AcquireTokenXX. Na verdade, o `ClientID` é definido apenas uma vez ao criar o (`IPublicClientApplication` ou `IConfidentialClientApplication`).

### <a name="iaccount-not-iuser"></a>IAccount não IUser

ADAL.NET os usuários manipulados. No entanto, um usuário é um agente de software ou humano, mas ele pode ter/é responsável por uma ou mais contas no sistema de identidade da Microsoft (várias contas do Azure AD, Azure AD B2C, contas pessoais da Microsoft). 

MSAL.NET 2. x agora define o conceito de conta (por meio da interface IAccount). Essa alteração significativa fornece a semântica correta: o fato de que o mesmo usuário pode ter várias contas, em diferentes diretórios do Azure AD. Além disso, o MSAL.NET fornece mais informações em cenários de convidado, como informações de conta doméstica são fornecidas.

Para obter mais informações sobre as diferenças entre IUser e IAccount, consulte [MSAL.NET 2. x](https://aka.ms/msal-net-2-released).

### <a name="exceptions"></a>Exceções

#### <a name="interaction-required-exceptions"></a>Exceções necessárias de interação

MSAL.NET tem exceções mais explícitas. Por exemplo, quando a autenticação silenciosa falha no ADAL, o procedimento é capturar a exceção e procurar o `user_interaction_required` código de erro:

```csharp
catch(AdalException exception)
{
 if (exception.ErrorCode == "user_interaction_required")
 {
  try
  {“try to authenticate interactively”}}
 }
}
```

Consulte os detalhes no [padrão recomendado para adquirir um token](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/AcquireTokenSilentAsync-using-a-cached-token#recommended-pattern-to-acquire-a-token) com Adal.net

Usando o MSAL.NET, você pega `MsalUiRequiredException` conforme descrito em [AcquireTokenSilent](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/AcquireTokenSilentAsync-using-a-cached-token).

```csharp
catch(MsalUiRequiredException exception)
{
 try {“try to authenticate interactively”}
}
```

#### <a name="handling-claim-challenge-exceptions"></a>Lidando com exceções de desafio de declaração

No ADAL.NET, as exceções de desafio de declaração são tratadas da seguinte maneira:

- `AdalClaimChallengeException` é uma exceção (derivando de `AdalServiceException`) gerada pelo serviço no caso de um recurso exigir mais declarações do usuário (por exemplo, autenticação de dois fatores). O membro `Claims` contém algum fragmento JSON com as declarações, que são esperadas.
- Ainda em ADAL.NET, o aplicativo cliente público que recebe essa exceção precisa chamar o `AcquireTokenInteractive` substituição com um parâmetro de declarações. Essa substituição de `AcquireTokenInteractive` nem mesmo tenta atingir o cache porque ele não é necessário. O motivo é que o token no cache não tem as declarações certas (caso contrário, um `AdalClaimChallengeException` não teria sido lançado). Portanto, não há necessidade de examinar o cache. Observe que o `ClaimChallengeException` pode ser recebido em um WebAPI fazendo OBO, enquanto o `AcquireTokenInteractive` precisa ser chamado em um aplicativo cliente público que chama essa API da Web.
- para obter detalhes, incluindo exemplos, consulte lidando com [AdalClaimChallengeException](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Exceptions-in-ADAL.NET#handling-adalclaimchallengeexception)

No MSAL.NET, as exceções de desafio de declaração são tratadas da seguinte maneira:

- Os `Claims` são exibidos na `MsalServiceException`.
- Há um método `.WithClaim(claims)` que pode ser aplicado ao construtor de `AcquireTokenInteractive`. 

### <a name="supported-grants"></a>Concessões com suporte

Nem todas as concessões ainda têm suporte em MSAL.NET e no ponto de extremidade v 2.0. Veja a seguir um resumo comparando ADAL.NET e MSAL. Concessões com suporte da rede.

#### <a name="public-client-applications"></a>Aplicativos cliente públicos

Aqui estão as concessões com suporte no ADAL.NET e no MSAL.NET para aplicativos móveis e de desktop

Concessão | ADAL.NET | MSAL.NET
----- |----- | -----
Interativo | [Autenticação interativa](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Acquiring-tokens-interactively---Public-client-application-flows) | [Adquirindo tokens interativamente no MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Acquiring-tokens-interactively)
Autenticação Integrada do Windows | [Autenticação integrada no Windows (Kerberos)](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/AcquireTokenSilentAsync-using-Integrated-authentication-on-Windows-(Kerberos)) | [Autenticação Integrada do Windows](msal-authentication-flows.md#integrated-windows-authentication)
Nome de usuário/senha | [Adquirindo tokens com nome de usuário e senha](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Acquiring-tokens-with-username-and-password)| [Autenticação de senha de nome de usuário](msal-authentication-flows.md#usernamepassword)
Fluxo de código do dispositivo | [Perfil de dispositivo para dispositivos sem navegadores da Web](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Device-profile-for-devices-without-web-browsers) | [Fluxo de código do dispositivo](msal-authentication-flows.md#device-code)

#### <a name="confidential-client-applications"></a>Aplicativos cliente confidenciais

Aqui estão as concessões com suporte em ADAL.NET e MSAL.NET para aplicativos Web, APIs da Web e aplicativos de daemon:

Tipo de aplicativo | Concessão | ADAL.NET | MSAL.NET
----- | ----- | ----- | -----
Aplicativo Web, API da Web, daemon | Credenciais do cliente | [Fluxos de credencial de cliente no ADAL.NET](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Client-credential-flows) | [Fluxos de credencial de cliente em MSAL.net](msal-authentication-flows.md#client-credentials))
API Web | Em nome de | [Chamadas de serviço a serviço em nome do usuário com ADAL.NET](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Service-to-service-calls-on-behalf-of-the-user) | [Em nome de em MSAL.NET](msal-authentication-flows.md#on-behalf-of)
Aplicação Web | Código de autenticação | [Aquisição de tokens com códigos de autorização em aplicativos Web com ADAL.NET](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Acquiring-tokens-with-authorization-codes-on-web-apps) | [Aquisição de tokens com códigos de autorização em aplicativos Web com um MSAL.NET](msal-authentication-flows.md#authorization-code)

### <a name="cache-persistence"></a>Persistência de cache

O ADAL.NET permite que você estenda a classe `TokenCache` para implementar a funcionalidade de persistência desejada em plataformas sem um armazenamento seguro (.NET Framework e .NET Core) usando os métodos `BeforeAccess`e `BeforeWrite`. Para obter detalhes, consulte [serialização de cache de token em Adal.net](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Token-cache-serialization).

MSAL.NET transforma o token em cache em uma classe selada, removendo a capacidade de estendê-lo. Portanto, sua implementação de persistência de cache de token deve estar na forma de uma classe auxiliar que interage com o cache de token lacrado. Essa interação é descrita em [serialização de cache de token em MSAL.net](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/token-cache-serialization).

## <a name="signification-of-the-common-authority"></a>Signification da autoridade comum

Em v 1.0, se você usar a autoridade de https://login.microsoftonline.com/common , permitirá que os usuários entrem com qualquer conta do AAD (para qualquer organização). Consulte [validação de autoridade em Adal.net](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/AuthenticationContext:-the-connection-to-Azure-AD#authority-validation)

Se você usar a autoridade de https://login.microsoftonline.com/common em v 2.0, permitirá que os usuários entrem com qualquer organização do AAD ou uma conta pessoal da Microsoft (MSA). No MSAL.NET, se você quiser restringir o logon a qualquer conta do AAD (mesmo comportamento que com o ADAL.NET), você precisará usar https://login.microsoftonline.com/organizations. Para obter detalhes, consulte o parâmetro `authority` no [aplicativo cliente público](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Client-Applications#publicclientapplication).

## <a name="v10-and-v20-tokens"></a>tokens v 1.0 e v 2.0

Há duas versões de tokens:
- tokens v 1.0
- tokens v 2.0 

O ponto de extremidade v 1.0 (usado pela ADAL) emite apenas tokens v 1.0.

No entanto, o ponto de extremidade v 2.0 (usado por MSAL) emite a versão do token que a API da Web aceita. Uma propriedade do manifesto do aplicativo da API Web permite que os desenvolvedores escolham qual versão do token é aceita. Consulte `accessTokenAcceptedVersion` na documentação de referência do [manifesto do aplicativo](reference-app-manifest.md) .

Para obter mais informações sobre tokens v 1.0 e v 2.0, consulte [tokens de acesso Azure Active Directory](access-tokens.md)

## <a name="scopes-for-a-web-api-accepting-v10-tokens"></a>Escopos para uma API Web aceitando tokens v 1.0

As permissões OAuth2 são escopos de permissão que um aplicativo de API (recurso) da Web v 1.0 expõe para aplicativos cliente. Esses escopos de permissão podem ser concedidos a aplicativos cliente durante o consentimento. Consulte a seção sobre oauth2Permissions no [manifesto do aplicativo Azure Active Directory](active-directory-application-manifest.md).

### <a name="scopes-to-request-access-to-specific-oauth2-permissions-of-a-v10-application"></a>Escopos para solicitar acesso a permissões específicas de OAuth2 de um aplicativo v 1.0

Se você quiser adquirir tokens para escopos específicos de um aplicativo v 1.0 (por exemplo, o grafo do AAD, que é https://graph.windows.net) , você precisará criar `scopes` concatenando um identificador de recurso desejado com uma permissão OAuth2 desejada para esse recurso.

Por exemplo, para acessar no nome do usuário uma API Web v 1.0 da qual o URI da ID do aplicativo é `ResourceId`, você desejaria usar:

```csharp
var scopes = new [] {  ResourceId+"/user_impersonation"};
```

Se você quiser ler e gravar com MSAL.NET Azure Active Directory usando a API do Graph do AAD (https://graph.windows.net/) , você criaria uma lista de escopos como no seguinte trecho de código:

```csharp
ResourceId = "https://graph.windows.net/";
var scopes = new [] { ResourceId + "Directory.Read", ResourceID + "Directory.Write"}
```

#### <a name="warning-should-you-have-one-or-two-slashes-in-the-scope-corresponding-to-a-v10-web-api"></a>Aviso: você deve ter uma ou duas barras no escopo correspondentes a uma API Web v 1.0

Se você quiser gravar o escopo correspondente à API de Azure Resource Manager (https://management.core.windows.net/) , você precisa solicitar o seguinte escopo (Observe as duas barras) 

```csharp
var scopes = new[] {"https://management.core.windows.net//user_impersonation"};
var result = await app.AcquireTokenInteractive(scopes).ExecuteAsync();

// then call the API: https://management.azure.com/subscriptions?api-version=2016-09-01
```

Isso ocorre porque a API do Gerenciador de recursos espera uma barra em sua declaração de público-alvo (`aud`) e, em seguida, há uma barra para separar o nome da API do escopo.

A lógica usada pelo Azure AD é a seguinte:
- Para o ponto de extremidade ADAL (v 1.0) com um token de acesso v 1.0 (o único possível), AUD = recurso
- Para MSAL (ponto de extremidade v 2.0) que solicita um token de acesso para um recurso que aceita tokens v 2.0, AUD = Resource. AppId
- Para MSAL (ponto de extremidade v 2.0) que solicita um token de acesso para um recurso que aceita um token de acesso v 1.0 (que é o caso acima), o Azure AD analisa o público-alvo desejado do escopo solicitado, levando tudo antes da última barra e usando-o como o identificador de recurso. Portanto, se https:\//database.windows.net esperar um público de "https://database.windows.net/ ", você precisará solicitar um escopo de https:\/ /database.windows.net//.default. Veja também o problema n º[747](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/747): a barra à direita da URL do recurso é omitida, o que causou uma falha de autenticação do SQL #747


### <a name="scopes-to-request-access-to-all-the-permissions-of-a-v10-application"></a>Escopos para solicitar acesso a todas as permissões de um aplicativo v 1.0

Por exemplo, se você quiser adquirir um token para todos os escopos estáticos de um aplicativo v 1.0, será necessário usar

```csharp
ResourceId = "someAppIDURI";
var scopes = new [] {  ResourceId+"/.default"};
```

### <a name="scopes-to-request-in-the-case-of-client-credential-flow--daemon-app"></a>Escopos para solicitação no caso do aplicativo fluxo de credencial do cliente/daemon

No caso do fluxo de credenciais do cliente, o escopo a ser aprovado também seria `/.default`. Esse escopo diz ao Azure AD: "todas as permissões de nível de aplicativo que o administrador consentiu no registro do aplicativo.

## <a name="adal-to-msal-migration"></a>Migração de ADAL para MSAL

No ADAL.NET v2. X, os tokens de atualização foram expostos, permitindo que você desenvolva soluções ao usar esses tokens, armazenando-os em cache e usando os métodos de `AcquireTokenByRefreshToken` fornecidos pelo ADAL 2. x. Algumas dessas soluções foram usadas em cenários como:
* Serviços de longa execução que realizam ações, incluindo a atualização de painéis em nome dos usuários, enquanto os usuários não estão mais conectados. 
* Cenários do WebFarm para permitir que o cliente traga o RT para o serviço Web (o armazenamento em cache é feito no lado do cliente, cookie criptografado e não no lado do servidor)

O MSAL.NET não expõe tokens de atualização, por motivos de segurança: MSAL lida com a atualização de tokens para você. 

Felizmente, o MSAL.NET agora tem uma API que permite migrar seus tokens de atualização anteriores (adquiridos com a ADAL) para o `IConfidentialClientApplication`:

```csharp
/// <summary>
/// Acquires an access token from an existing refresh token and stores it and the refresh token into 
/// the application user token cache, where it will be available for further AcquireTokenSilent calls.
/// This method can be used in migration to MSAL from ADAL v2 and in various integration 
/// scenarios where you have a RefreshToken available. 
/// (see https://aka.ms/msal-net-migration-adal2-msal2)
/// </summary>
/// <param name="scopes">Scope to request from the token endpoint. 
/// Setting this to null or empty will request an access token, refresh token and ID token with default scopes</param>
/// <param name="refreshToken">The refresh token from ADAL 2.x</param>
IByRefreshToken.AcquireTokenByRefreshToken(IEnumerable<string> scopes, string refreshToken);
```
 
Com esse método, você pode fornecer o token de atualização usado anteriormente junto com todos os escopos (recursos) que desejar. O token de atualização será trocado por um novo e armazenado em cache em seu aplicativo.  

Como esse método destina-se a cenários que não são típicos, ele não está prontamente acessível com o `IConfidentialClientApplication` sem primeiro convertê-lo em `IByRefreshToken`.

Este trecho de código mostra um código de migração em um aplicativo cliente confidencial. `GetCachedRefreshTokenForSignedInUser` recuperar o token de atualização que foi armazenado em algum armazenamento por uma versão anterior do aplicativo que costumava aproveitar a ADAL 2. x. `GetTokenCacheForSignedInUser` desserializa um cache para o usuário conectado (como aplicativos cliente confidenciais devem ter um cache por usuário).

```csharp
TokenCache userCache = GetTokenCacheForSignedInUser();
string rt = GetCachedRefreshTokenForSignedInUser();

IConfidentialClientApplication app;
app = ConfidentialClientApplicationBuilder.Create(clientId)
 .WithAuthority(Authority)
 .WithRedirectUri(RedirectUri)
 .WithClientSecret(ClientSecret)
 .Build();
IByRefreshToken appRt = app as IByRefreshToken;
         
AuthenticationResult result = await appRt.AcquireTokenByRefreshToken(null, rt)
                                         .ExecuteAsync()
                                         .ConfigureAwait(false);
```

Você verá um token de acesso e um token de ID retornados em seu AuthenticationResult enquanto o novo token de atualização é armazenado no cache.

Você também pode usar esse método para vários cenários de integração em que há um token de atualização disponível.

## <a name="next-steps"></a>Passos seguintes

Você pode encontrar mais informações sobre os escopos em [escopos, permissões e consentimento no ponto de extremidade da plataforma Microsoft Identity](v2-permissions-and-consent.md)
