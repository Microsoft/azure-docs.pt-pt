---
title: Migrar para o MSAL.NET | Azure
description: Saiba mais sobre as diferenças entre a biblioteca de autenticação da Microsoft para .NET (MSAL.NET) e a biblioteca de autenticação do Azure AD para .NET (ADAL.NET) e como migrar para MSAL.NET.
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: celested
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/10/2019
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2576121bfc945b90ce8ec0260ea30ec110e14dd8
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65138833"
---
# <a name="migrating-applications-to-msalnet"></a>Migrando aplicativos para o MSAL.NET

Ambos os Microsoft Authentication Library para .NET (MSAL.NET) e do Azure AD Authentication Library para .NET (ADAL.NET) são utilizados para autenticar as entidades do Azure AD e solicitar tokens do Azure AD. Até agora, a maioria dos desenvolvedores trabalharam com o Azure AD para a plataforma de desenvolvedores (v1.0) para autenticar identidades do Azure AD (contas profissionais e escolares) ao pedir tokens através do Azure AD Authentication Library (ADAL). Agora, com MSAL.NET, pode autenticar um conjunto mais amplo de identidades da Microsoft (identidades do Azure AD e as contas Microsoft e contas de redes sociais e locais através do Azure AD B2C) através do ponto de final de plataforma de identidade Microsoft. 

Este artigo descreve como escolher entre a biblioteca de autenticação da Microsoft para .NET (MSAL.NET) e o Azure AD Authentication Library para .NET (ADAL.NET) e compara as duas bibliotecas.  

## <a name="differences-between-adal-and-msal-apps"></a>Diferenças entre as aplicações ADAL e MSAL
Na maioria dos casos que pretende utilizar MSAL.NET e o Microsoft identity platform ponto final, que é a última geração de bibliotecas de autenticação da Microsoft. Com MSAL.NET, adquira tokens para os utilizadores iniciar sessão para a sua aplicação com o Azure AD (contas profissionais e escolares), contas da Microsoft (pessoal) (MSA), ou do Azure AD B2C. 

Se já estiver familiarizado com o Azure AD para o ponto final de programadores (v1.0) (e ADAL.NET), pode querer ler [quais são as diferenças sobre o ponto de extremidade de plataforma (v2.0) do Microsoft identity?](active-directory-v2-compare.md).

No entanto, ainda tem de utilizar ADAL.NET se precisar de iniciar sessão dos utilizadores com versões anteriores do seu aplicativo [serviços de Federação do Active Directory (ADFS)](/windows-server/identity/active-directory-federation-services). Para obter mais detalhes, consulte [ADFS suportar](https://aka.ms/msal-net-adfs-support).

A imagem que se segue resume algumas das diferenças entre ADAL.NET e MSAL.NET ![código do lado a lado](./media/msal-compare-msaldotnet-and-adaldotnet/differences.png)

### <a name="nuget-packages-and-namespaces"></a>Pacotes NuGet e espaços de nomes

ADAL.NET é consumidos a partir da [ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory) pacote NuGet. o espaço de nomes a utilizar é `Microsoft.IdentityModel.Clients.ActiveDirectory`.

Para utilizar MSAL.NET terá de adicionar o [Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client) NuGet empacotamento e a utilizar o `Microsoft.Identity.Client` espaço de nomes

### <a name="scopes-not-resources"></a>Âmbitos não recursos

ADAL.NET adquire tokens para *recursos*, mas MSAL.NET adquire os tokens do *âmbitos*. Um número de substituições de MSAL.NET AcquireToken requerem um parâmetro chamado âmbitos (`IEnumerable<string> scopes`). Este parâmetro é apenas uma lista de cadeias de caracteres que declarar as permissões pretendidas e os recursos que são solicitados. Âmbitos conhecidos são os [âmbitos do Microsoft Graph](/graph/permissions-reference).

Também é possível no MSAL.NET para aceder aos recursos da versão 1.0. Ver detalhes nos [âmbitos para uma aplicação de v1.0](#scopes-for-a-web-api-accepting-v10-tokens). 

### <a name="core-classes"></a>Classes de núcleo

- Utiliza ADAL.NET [AuthenticationContext](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/AuthenticationContext:-the-connection-to-Azure-AD) como a representação da sua ligação para o servidor de serviço de Token de segurança (STS) ou autorização, por meio de uma autoridade. Por outro lado, MSAL.NET foi criado em torno [aplicativos cliente](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Client-Applications). Ele fornece duas classes separadas: `PublicClientApplication` e `ConfidentialClientApplication`

- Aquisição de Tokens: ADAL.NET e MSAL.NET têm as mesmas chamadas de autenticação (`AcquireTokenAsync` e `AcquireTokenSilentAsync` para ADAL.NET, e `AqquireTokenInteractive` e `AcquireTokenSilent` no MSAL.NET), mas com diferentes parâmetros necessários. Uma diferença é o fato de que, no MSAL.NET, não precisará passar o `ClientID` da sua aplicação em todas as chamadas de AcquireTokenXX. Na verdade, o `ClientID` é definido apenas uma vez, ao criar a (`IPublicClientApplication` ou `IConfidentialClientApplication`).

### <a name="iaccount-not-iuser"></a>IAccount IUser não

ADAL.NET manipulado utilizadores. No entanto, um utilizador é uma pessoa ou um agente de software, mas pode possuir/proprietário/ser responsável por uma ou mais contas do sistema de identidade da Microsoft (várias contas do Azure AD contas, o Azure AD B2C, Microsoft pessoais). 

MSAL.NET 2.x agora define o conceito de conta (por meio da interface de IAccount). Esta alteração de última hora fornece a semântica de certa: o fato de que o mesmo utilizador pode ter várias contas, no Azure diferente diretórios AD. Também MSAL.NET fornece melhores informações em cenários de convidado, são fornecidas informações de conta de raiz.

Para obter mais informações sobre as diferenças entre IUser e IAccount, consulte [MSAL.NET 2.x](https://aka.ms/msal-net-2-released).

### <a name="exceptions"></a>Exceções

#### <a name="interaction-required-exceptions"></a>Exceções de interação necessária

MSAL.NET tem exceções mais explícitas. Por exemplo, quando a autenticação silenciosa falhar em ADAL é o procedimento capturar a exceção e procurar o `user_interaction_required` código de erro:

```csharp
catch(AdalException exception)
{
 if (exception.ErrorCode == “user_interaction_required”)
 {
  try
  {“try to authenticate interactively”}}
 }
}
```

Ver detalhes nos [o padrão recomendado para adquirir um token](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/AcquireTokenSilentAsync-using-a-cached-token#recommended-pattern-to-acquire-a-token) com ADAL.NET

Com MSAL.NET, capturar `MsalUiRequiredException` conforme descrito na [AcquireTokenSilent](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/AcquireTokenSilentAsync-using-a-cached-token).

```csharp
catch(MsalUiRequiredException exception)
{
 try {“try to authenticate interactively”}
}
```

#### <a name="handling-claim-challenge-exceptions"></a>Exceções de desafio de afirmação de tratamento

No ADAL.NET, de solicitação de desafio exceções são tratadas da seguinte forma:

- `AdalClaimChallengeException` é uma exceção (derivadas de `AdalServiceException`) geradas pelo serviço, no caso de um recurso requer mais afirmações de utilizador (por exemplo a autenticação de dois fatores). O `Claims` membro contém alguns fragmento JSON, com as declarações, que são esperadas.
- Ainda no ADAL.NET, a aplicação de cliente pública a receber esta exceção tem chamar o `AcquireTokenInteractive` substituir a ter um parâmetro de afirmações. Esta substituição de `AcquireTokenInteractive` nem mesmo tentar para a cache de acessos não é necessário. O motivo é que o token em cache não tem as afirmações certas (caso contrário, um `AdalClaimChallengeException` seria não ter sido lançada). Por conseguinte, não é necessário examinar o cache. Tenha em atenção que o `ClaimChallengeException` podem ser recebidos numa API Web fazendo OBO, ao passo que o `AcquireTokenInteractive` precisa ser chamado num aplicativo de cliente público chamar esta API Web.
- Para obter detalhes, incluindo exemplos, consulte manipulação [AdalClaimChallengeException](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Exceptions-in-ADAL.NET#handling-adalclaimchallengeexception)

No MSAL.NET, de solicitação de desafio exceções são tratadas da seguinte forma:

- O `Claims` são apresentadas no `MsalServiceException`.
- Há uma `.WithClaim(claims)` método que pode aplicar o `AcquireTokenInteractive` builder. 

### <a name="supported-grants"></a>Concede suportados

Nem todas as concessões ainda são suportadas no MSAL.NET e o ponto final v2.0. Segue-se um resumo comparando ADAL.NET e MSAL. NET do suporte de concessões.

#### <a name="public-client-applications"></a>Aplicativos cliente público

Aqui estão as concessões suportadas no ADAL.NET e MSAL.NET para aplicativos de Desktop e Mobile

Conceder | ADAL.NET | MSAL.NET
----- |----- | -----
Interativo | [Autenticação interativa](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Acquiring-tokens-interactively---Public-client-application-flows) | [Aquisição de tokens interativamente na MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Acquiring-tokens-interactively)
Autenticação Integrada do Windows | [Autenticação integrada do Windows (Kerberos)](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/AcquireTokenSilentAsync-using-Integrated-authentication-on-Windows-(Kerberos)) | [Autenticação integrada do Windows](msal-authentication-flows.md#integrated-windows-authentication)
Nome de utilizador / palavra-passe | [Aquisição de tokens com o nome de utilizador e palavra-passe](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Acquiring-tokens-with-username-and-password)| [Autenticação de palavra-passe do nome de utilizador](msal-authentication-flows.md#usernamepassword)
Fluxo de código do dispositivo | [Perfil de dispositivo para dispositivos sem navegadores da web](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Device-profile-for-devices-without-web-browsers) | [Fluxo de código de dispositivo](msal-authentication-flows.md#device-code)

#### <a name="confidential-client-applications"></a>Aplicações de cliente confidencial

Aqui estão as concessões suportadas no ADAL.NET e MSAL.NET para aplicativos Web, APIs da Web e aplicativos de daemon:

Tipo de aplicação | Conceder | ADAL.NET | MSAL.NET
----- | ----- | ----- | -----
Web App, Web API, o daemon | Credenciais de cliente | [Fluxos de credencial de cliente no ADAL.NET](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Client-credential-flows) | [Credencial de cliente vem em MSAL.NET](msal-authentication-flows.md#client-credentials))
API Web | Em nome de | [Chamadas serviço a serviço em nome do utilizador com ADAL.NET](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Service-to-service-calls-on-behalf-of-the-user) | [Em nome no MSAL.NET](msal-authentication-flows.md#on-behalf-of)
Aplicação Web | Código de autenticação | [Aquisição de tokens com códigos de autorização nas aplicações web com ADAL.NET](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Acquiring-tokens-with-authorization-codes-on-web-apps) | [Aquisição de tokens com códigos de autorização nas aplicações web com A MSAL.NET](msal-authentication-flows.md#authorization-code)

### <a name="cache-persistence"></a>Persistência da cache

ADAL.NET permite-lhe expandir as `TokenCache` classe para implementar a funcionalidade de persistência desejada em plataformas sem um armazenamento seguro (.NET Framework e .NET core), utilizando o `BeforeAccess`, e `BeforeWrite` métodos. Para obter detalhes, consulte [serialização de Cache de Token em ADAL.NET](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Token-cache-serialization).

MSAL.NET faz o cache de tokens de uma classe selada, remover a funcionalidade estendê-lo. Portanto, sua implementação de persistência da cache de token deve estar na forma de uma classe auxiliar que interage com a cache de token selada. Esta interação é descrita em [serialização de Cache de Token em MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/token-cache-serialization).

## <a name="signification-of-the-common-authority"></a>Signification da autoridade de comuns

Na versão 1.0, se usar o https://login.microsoftonline.com/common autoridade, permitirá aos utilizadores iniciar sessão com qualquer conta AAD (para qualquer organização). Consulte [validação de autoridade no ADAL.NET](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/AuthenticationContext:-the-connection-to-Azure-AD#authority-validation)

Se utilizar o https://login.microsoftonline.com/common autoridade na versão 2.0, permitirá aos utilizadores iniciar sessão com qualquer organização de AAD ou uma conta pessoal da Microsoft (MSA). No MSAL.NET, se quiser restringir o início de sessão a qualquer conta AAD (mesmo comportamento que com ADAL.NET), tem de utilizar https://login.microsoftonline.com/organizations. Para obter detalhes, consulte a `authority` parâmetro na [aplicação cliente público](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Client-Applications#publicclientapplication).

## <a name="v10-and-v20-tokens"></a>tokens v1.0 e v2.0

Existem duas versões de tokens:
- Tokens v1.0
- Tokens v2.0 

O ponto de extremidade v1.0 (utilizado pelo ADAL) apenas emite tokens de v1.0.

No entanto, o ponto final v2.0 (utilizado pelo MSAL) emite a versão do token que aceita a API Web. Uma propriedade de manifesto da aplicação da Web API permite aos desenvolvedores escolher qual versão do token é aceite. Ver `accessTokenAcceptedVersion` no [manifesto do aplicativo](reference-app-manifest.md) documentação de referência.

Para obter mais informações sobre versões v1.0 e v2.0 tokens, consulte [tokens de acesso do Azure Active Directory](access-tokens.md)

## <a name="scopes-for-a-web-api-accepting-v10-tokens"></a>Âmbitos para uma API Web a aceitação de tokens de v1.0

Permissões de OAuth2 são âmbitos de permissões de uma aplicação de API (recurso) de web v1.0 expõe para as aplicações cliente. Estes âmbitos de permissão podem ser concedidos às aplicações cliente durante o consentimento. Consulte a secção sobre oauth2Permissions na [manifesto de aplicação do Azure Active Directory](active-directory-application-manifest.md).

### <a name="scopes-to-request-access-to-specific-oauth2-permissions-of-a-v10-application"></a>Âmbitos para solicitar acesso a permissões de OAuth2 específicas de um aplicativo de v1.0

Se deseja adquirir tokens para âmbitos específicos de um aplicativo de v1.0 (por exemplo AAD graph, que é https://graph.windows.net), terá de criar `scopes` concatenando um identificador de recurso pretendido com uma permissão de OAuth2 pretendido para esse recurso.

Por exemplo, para acesso no nome de utilizador uma API Web que é o URI de ID de aplicação de v1.0 `ResourceId`, iria querer usar:

```csharp
var scopes = new [] {  ResourceId+"/user_impersonation"};
```

Se pretender ler e escrever com MSAL.NET do Azure Active Directory com o graph API do AAD (https://graph.windows.net/), criaria uma lista de âmbitos, como no seguinte fragmento:

```csharp
ResourceId = "https://graph.windows.net/";
var scopes = new [] { ResourceId + “Directory.Read”, ResourceID + “Directory.Write”}
```

#### <a name="warning-should-you-have-one-or-two-slashes-in-the-scope-corresponding-to-a-v10-web-api"></a>Aviso: Tem uma ou duas barras no escopo correspondente a uma Web API de v1.0

Se quiser escrever o escopo correspondente para a API do Azure Resource Manager (https://management.core.windows.net/), tem de pedir ao seguinte âmbito (tenha em atenção as duas barras) 

```csharp
var scopes = new[] {"https://management.core.windows.net//user_impersonation"};
var result = await app.AcquireTokenInteractive(scopes).ExecuteAsync();

// then call the API: https://management.azure.com/subscriptions?api-version=2016-09-01
```

Isto acontece porque a API do Resource Manager espera uma barra no seu público-alvo de afirmações (`aud`), e, em seguida, há uma barra para separar o nome da API do âmbito.

A lógica usada pelo Azure AD é o seguinte:
- Para o ponto final da ADAL (v1.0) com um token de v1.0 acesso (possível única), aud = recursos
- Para solicitar um token de acesso a um recurso de aceitação de tokens de v2.0, aud MSAL (ponto final v2.0) = resource. AppId
- Para MSAL (ponto final v2.0) solicitar um token de acesso a um recurso de aceitar um token de acesso de v1.0 (que é o caso acima), o Azure AD analisa o público-alvo pretendido de âmbito do pedido Considerando tudo o que antes da última barra e utilizá-lo como o identificador de recurso. Portanto se https://database.windows.net espera um público-alvo de "https://database.windows.net/", terá de solicitar um escopo de https://database.windows.net//.default. Consulte também emitir #[747](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/747): Barra do url de recurso for omitida, o que causou a falha de autenticação de sql #747


### <a name="scopes-to-request-access-to-all-the-permissions-of-a-v10-application"></a>Âmbitos para solicitar acesso a todas as permissões de um aplicativo de v1.0

Por exemplo, se desejar adquirir um token para todos os âmbitos estáticos de um aplicativo de v1.0, um seria necessário usar

```csharp
ResourceId = "someAppIDURI";
var scopes = new [] {  ResourceId+"/.default"};
```

### <a name="scopes-to-request-in-the-case-of-client-credential-flow--daemon-app"></a>Fluxo de credenciais de âmbitos para solicitar no caso de cliente / aplicação daemon

No caso do fluxo de credenciais de cliente, o âmbito de passar também seria `/.default`. Isso informa ao Azure AD: "todas ao nível da aplicação permissões que o administrador já autorizou do registo de aplicação.

## <a name="adal-to-msal-migration"></a>ADAL para a migração de MSAL

No ADAL.NET v2. X, a atualização tokens foram expostos, permitindo-lhe desenvolver soluções em torno do uso desses tokens de colocação em cache-los e usando o `AcquireTokenByRefreshToken` métodos fornecidos pela 2.x da ADAL. Algumas dessas soluções foram usadas em cenários, tais como:
* Serviços de longa execução que realizar ações, incluindo a atualizar dashboards em nome dos utilizadores, ao passo que os utilizadores já não estão ligados. 
* Os cenários de WebFarm para habilitar o cliente trazer o RT para o serviço web (colocação em cache é realizada do lado do cliente, o cookie encriptado e não do lado do servidor)

Não é o caso com MSAL.NET, no entanto, como podemos não recomendamos mais tempo a utilização de atualização de tokens dessa maneira, por motivos de segurança. Isso seria dificultam a migrar para o MSAL 3.x como a API não fornece uma forma de passar tokens de atualização adquirido anteriormente. 

Felizmente, o MSAL.NET tem agora uma API que permite-lhe migrar seus tokens de atualização anterior para o `IConfidentialClientApplication` 

```CSharp
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
 
Com esse método, pode fornecer o token de atualização utilizadas anteriormente, juntamente com quaisquer âmbitos (recursos) desejado. O token de atualização irá ser trocado por um novo e armazenados em cache em seu aplicativo.  

Como este método destina-se a cenários que não são típicos, não é imediatamente acessível com o `IConfidentialClientApplication` sem primeiro convertê-la para `IByRefreshToken`.

Este fragmento de código mostra alguns códigos de migração num aplicativo de cliente confidencial. `GetCachedRefreshTokenForSignedInUser` obter o token de atualização que foram armazenado em algum armazenamento por uma versão anterior da aplicação que é utilizado para tirar partido da ADAL 2.x. `GetTokenCacheForSignedInUser` desserializa uma cache para o utilizador com sessão iniciada (como aplicativos de cliente confidencial devem ter um cache por utilizador).

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

Verá um token de acesso e o token de ID devolvido na sua authenticationresult de que, enquanto o seu novo token de atualização é armazenado na cache.

Também pode utilizar este método para vários cenários de integração em que tem um token de atualização disponível.

## <a name="next-steps"></a>Passos Seguintes

Pode encontrar mais informações sobre âmbitos no [âmbitos, permissões e consentimento no ponto de final de plataforma de identidade do Microsoft](v2-permissions-and-consent.md)