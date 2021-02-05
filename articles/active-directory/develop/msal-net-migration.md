---
title: Migrar para MSAL.NET
titleSuffix: Microsoft identity platform
description: Saiba mais sobre as diferenças entre a Biblioteca de Autenticação microsoft para .NET (MSAL.NET) e a Biblioteca de Autenticação AD Azure para .NET (ADAL.NET) e como migrar para MSAL.NET.
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
ms.custom: devx-track-csharp, aaddev
ms.openlocfilehash: 2b8577af2c8a6296ae6f4f090e8ff233e51ee6fb
ms.sourcegitcommit: 2817d7e0ab8d9354338d860de878dd6024e93c66
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/05/2021
ms.locfileid: "99583930"
---
# <a name="migrating-applications-to-msalnet"></a>Aplicações migratórias para MSAL.NET

Tanto a Biblioteca de Autenticação microsoft para .NET (MSAL.NET) como a Azure AD Authentication Library for .NET (ADAL.NET) são utilizadas para autenticar entidades AD do Azure e solicitar fichas da Azure AD. Até agora, a maioria dos desenvolvedores tem trabalhado com a Azure AD para a plataforma de desenvolvedores (v1.0) para autenticar identidades AD AZure (trabalho e contas escolares) solicitando fichas usando a Azure AD Authentication Library (ADAL). Utilização do MSAL:

- você pode autenticar um conjunto mais amplo de identidades da Microsoft (identidades AD AD Azure e contas microsoft, e contas sociais e locais através do Azure AD B2C) uma vez que utiliza a plataforma de identidade microsoft,
- os seus utilizadores terão a melhor experiência de um único sinal.
- a sua aplicação pode permitir o consentimento incremental, e apoiar o Acesso Condicional é mais fácil
- beneficia-se da inovação.

**MSAL.NET é agora a biblioteca de auth recomendada para usar com a plataforma de identidade microsoft**. Nenhuma nova funcionalidade será implementada no ADAL.NET. Os esforços concentram-se na melhoria do MSAL.

Este artigo descreve as diferenças entre a Biblioteca de Autenticação da Microsoft para .NET (MSAL.NET) e a Biblioteca de Autenticação AD Azure para .NET (ADAL.NET) e ajuda a migrar para o MSAL.

## <a name="differences-between-adal-and-msal-apps"></a>Diferenças entre aplicações ADAL e MSAL

Na maioria dos casos pretende utilizar MSAL.NET e a plataforma de identidade da Microsoft, que é a última geração de bibliotecas de autenticação da Microsoft. Utilizando MSAL.NET, adquire fichas para os utilizadores que se inscrevam na sua aplicação com Azure AD (contas de trabalho e escola), contas da Microsoft (pessoais) (MSA) ou Azure AD B2C.

Se já está familiarizado com o AD AZure para programadores (v1.0) (e ADAL.NET), talvez queira ler [O que é diferente na plataforma de identidade da Microsoft?](../azuread-dev/azure-ad-endpoint-comparison.md)

No entanto, ainda precisa de utilizar ADAL.NET se a sua aplicação precisar de assinar em utilizadores com versões anteriores dos Serviços da [Federação de Diretórios Ativos (ADFS)](/windows-server/identity/active-directory-federation-services). Para mais informações, consulte [o suporte da ADFS.](https://aka.ms/msal-net-adfs-support)

A imagem que se segue resume algumas das diferenças entre ADAL.NET e MSAL.NET ![ código lado a lado](./media/msal-compare-msaldotnet-and-adaldotnet/differences.png)

### <a name="nuget-packages-and-namespaces"></a>Pacotes NuGet e Espaços de Nome

ADAL.NET é consumido a partir do pacote [Microsoft.IdentityModel.Clients.ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory) NuGet. o espaço de nomes a utilizar é `Microsoft.IdentityModel.Clients.ActiveDirectory` .

Para utilizar MSAL.NET terá de adicionar o pacote [Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client) NuGet e utilizar o `Microsoft.Identity.Client` espaço de nome

### <a name="scopes-not-resources"></a>Âmbitos não recursos

ADAL.NET adquire fichas para *recursos,* mas MSAL.NET adquire fichas para *âmbitos.* Uma série de MSAL.NET a AcquireToken substitui um parâmetro chamado `IEnumerable<string> scopes` scopes. Este parâmetro é uma lista simples de cordas que declaram as permissões e recursos pretendidos que são solicitados. Os âmbitos bem conhecidos são os [âmbitos do Microsoft Graph.](/graph/permissions-reference)

Também é possível em MSAL.NET aceder aos recursos v1.0. Consulte os detalhes em [Âmbitos para uma aplicação v1.0](#scopes-for-a-web-api-accepting-v10-tokens).

### <a name="core-classes"></a>Classes principais

- ADAL.NET utiliza [o AutenticaçãoContexto](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/AuthenticationContext:-the-connection-to-Azure-AD) como representação da sua ligação ao Serviço de Token de Segurança (STS) ou servidor de autorização, através de uma Autoridade. Pelo contrário, MSAL.NET é projetado em torno de [aplicações de clientes.](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Client-Applications) Fornece duas classes separadas: `PublicClientApplication` e `ConfidentialClientApplication`

- Aquisição de Tokens: ADAL.NET e MSAL.NET têm as mesmas chamadas de autenticação ( `AcquireTokenAsync` e  `AcquireTokenSilentAsync` para ADAL.NET, e em `AcquireTokenInteractive` `AcquireTokenSilent` MSAL.NET), mas com parâmetros diferentes necessários. Uma diferença é o facto de, em MSAL.NET, já não ter de passar na `ClientID` sua aplicação em todas as chamadas AcquireTokenXX. Na verdade, o `ClientID` conjunto é definido apenas uma vez quando se constrói o ( `IPublicClientApplication` ou `IConfidentialClientApplication` .

### <a name="iaccount-not-iuser"></a>IAccount não IUser

ADAL.NET utilizadores manipulados. No entanto, um utilizador é um humano ou um agente de software, mas pode possuir/possuir/ser responsável por uma ou mais contas no sistema de identidade da Microsoft (várias contas AD AZure, Azure AD B2C, contas pessoais da Microsoft).

MSAL.NET 2.x define agora o conceito de Conta (através da interface IAccount). Esta mudança de rutura proporciona a semântica certa: o facto de o mesmo utilizador poder ter várias contas, em diferentes diretórios AD do Azure. Também MSAL.NET fornece uma melhor informação em cenários de hóspedes, uma vez que a informação da conta de casa é fornecida.

Para obter mais informações sobre as diferenças entre iUser e IAccount, consulte [MSAL.NET 2.x](https://aka.ms/msal-net-2-released).

### <a name="exceptions"></a>Exceções

#### <a name="interaction-required-exceptions"></a>Interação necessárias exceções

MSAL.NET tem exceções mais explícitas. Por exemplo, quando a autenticação silenciosa falha no ADAL, o procedimento consiste em captar a exceção e procurar o código de `user_interaction_required` erro:

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

Veja detalhes no [padrão recomendado para adquirir um token](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/AcquireTokenSilentAsync-using-a-cached-token#recommended-pattern-to-acquire-a-token) com ADAL.NET

Utilizando MSAL.NET, apanha `MsalUiRequiredException` como descrito no [AcquireTokenSilent](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/AcquireTokenSilentAsync-using-a-cached-token).

```csharp
catch(MsalUiRequiredException exception)
{
 try {“try to authenticate interactively”}
}
```

#### <a name="handling-claim-challenge-exceptions"></a>Tratamento de reclamações de impugnação exceções

Em ADAL.NET, as exceções de impugnação de reclamações são tratadas da seguinte forma:

- `AdalClaimChallengeException` é uma exceção (derivada de) lançada pelo serviço no caso de `AdalServiceException` um recurso exigir mais reclamações do utilizador (por exemplo, a autenticação de dois fatores). O `Claims` membro contém algum fragmento JSON com as alegações, que são esperadas.
- Ainda em ADAL.NET, a aplicação do cliente público que recebe esta exceção precisa de chamar a `AcquireTokenInteractive` anulação tendo um parâmetro de reclamações. Esta sobreposição `AcquireTokenInteractive` nem sequer tenta atingir a cache, uma vez que não é necessário. A razão é que o símbolo na cache não tem as reivindicações certas (caso contrário, um `AdalClaimChallengeException` não teria sido atirado). Portanto, não há necessidade de olhar para a cache. Note que o `ClaimChallengeException` pode ser recebido num WebAPI fazendo OBO, enquanto que a necessidade de ser chamado em uma `AcquireTokenInteractive` aplicação de cliente público chamando esta API web.
- para mais detalhes, incluindo amostras ver Handling [AdalClaimChallengeException](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Exceptions-in-ADAL.NET#handling-adalclaimchallengeexception)

Em MSAL.NET, as exceções de impugnação de reivindicação são tratadas da seguinte forma:

- Os `Claims` são surgidos no `MsalServiceException` .
- Há um `.WithClaim(claims)` método que pode aplicar-se ao `AcquireTokenInteractive` construtor.

### <a name="supported-grants"></a>Subvenções apoiadas

Nem todas as subvenções são ainda apoiadas em MSAL.NET e no ponto final v2.0. Segue-se um resumo que compara ADAL.NET e o MSAL. As subvenções apoiadas pela NET.

#### <a name="public-client-applications"></a>Aplicações de clientes públicos

Aqui estão as bolsas suportadas em ADAL.NET e MSAL.NET para aplicações desktop e mobile

Concessão | ADAL.NET | MSAL.NET
----- |----- | -----
Interativo | [Auth Interativo](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Acquiring-tokens-interactively---Public-client-application-flows) | [Aquisição de fichas interativamente em MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Acquiring-tokens-interactively)
Autenticação Integrada do Windows. | [Autenticação integrada no Windows (Kerberos)](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/AcquireTokenSilentAsync-using-Integrated-authentication-on-Windows-(Kerberos)) | [Autenticação Integrada do Windows](msal-authentication-flows.md#integrated-windows-authentication)
Nome de utilizador / Senha | [Aquisição de fichas com nome de utilizador e senha](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Acquiring-tokens-with-username-and-password)| [Autenticação de senha de nome de utilizador](msal-authentication-flows.md#usernamepassword)
Fluxo de código do dispositivo | [Perfil do dispositivo para dispositivos sem navegadores web](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Device-profile-for-devices-without-web-browsers) | [Fluxo de código do dispositivo](msal-authentication-flows.md#device-code)

#### <a name="confidential-client-applications"></a>Aplicações confidenciais de clientes

Aqui estão as subvenções suportadas em ADAL.NET e MSAL.NET para aplicações web, APIs web e aplicações daemon:

Tipo de App | Concessão | ADAL.NET | MSAL.NET
----- | ----- | ----- | -----
Web app, web API, daemon | Credenciais de Cliente | [A credencial do cliente flui em ADAL.NET](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Client-credential-flows) | [A credencial do cliente flui em MSAL.NET](msal-authentication-flows.md#client-credentials)
API Web | Em nome de | [Serviço para chamadas de serviço em nome do utilizador com ADAL.NET](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Service-to-service-calls-on-behalf-of-the-user) | [Em nome de MSAL.NET](msal-authentication-flows.md#on-behalf-of)
Aplicação Web | Código Auth | [Aquisição de fichas com códigos de autorização em aplicações web com ADAL.NET](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Acquiring-tokens-with-authorization-codes-on-web-apps) | [Aquisição de fichas com códigos de autorização em aplicações web com A MSAL.NET](msal-authentication-flows.md#authorization-code)

### <a name="cache-persistence"></a>Persistência cache

ADAL.NET permite alargar a classe `TokenCache` para implementar a funcionalidade de persistência desejada nas plataformas sem um armazenamento seguro (.NET Framework e .NET core) utilizando o `BeforeAccess` , e `BeforeWrite` métodos. Para mais detalhes, consulte [a Serialização da Cache Token em ADAL.NET](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Token-cache-serialization).

MSAL.NET faz da cache simbólica uma classe selada, removendo a capacidade de estender. Portanto, a sua implementação da persistência da cache simbólica deve ser na forma de uma classe de ajudante que interage com a cache simbólica selada. Esta interação é descrita na [Serialização token Cache em MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/token-cache-serialization).

## <a name="signification-of-the-common-authority"></a>Importância da autoridade comum

Em v1.0, se utilizar a `https://login.microsoftonline.com/common` autoridade, permitirá que os utilizadores entrem com qualquer conta AAD (para qualquer organização). Ver [Validação da Autoridade em ADAL.NET](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/AuthenticationContext:-the-connection-to-Azure-AD#authority-validation)

Se utilizar a `https://login.microsoftonline.com/common` autoridade em v2.0, permitirá que os utilizadores entrem em súmido com qualquer organização AAD ou uma conta pessoal da Microsoft (MSA). Em MSAL.NET, se pretender restringir o login a qualquer conta AAD (o mesmo comportamento que com ADAL.NET), utilize `https://login.microsoftonline.com/organizations` . Para mais detalhes, consulte o `authority` parâmetro na [aplicação do cliente público.](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Client-Applications#publicclientapplication)

## <a name="v10-and-v20-tokens"></a>v1.0 e v2.0 fichas

Existem duas versões de fichas:
- v1.0 fichas
- v2.0 fichas

O ponto final v1.0 (utilizado pela ADAL) só emite tokens v1.0.

No entanto, o ponto final v2.0 (usado pelo MSAL) emite a versão do token que a API web aceita. Uma propriedade do manifesto de aplicação da API web permite que os desenvolvedores escolham qual versão de token é aceite. Consulte `accessTokenAcceptedVersion` na documentação de referência [manifesto da Aplicação.](reference-app-manifest.md)

Para obter mais informações sobre os tokens v1.0 e v2.0, consulte [os tokens de acesso do Azure Ative Directory](access-tokens.md)

## <a name="scopes-for-a-web-api-accepting-v10-tokens"></a>Âmbitos para uma API web aceitando fichas v1.0

As permissões OAuth2 são âmbitos de permissão que uma aplicação de API (recurso) da web v1.0 expõe às aplicações do cliente. Estes âmbitos de permissão podem ser concedidos aos pedidos do cliente durante o consentimento. Consulte a secção sobre as oauth2Permissions in [Azure Ative Directory manifesto](./reference-app-manifest.md).

### <a name="scopes-to-request-access-to-specific-oauth2-permissions-of-a-v10-application"></a>Âmbitos para solicitar acesso a permissões específicas de OAuth2 de uma aplicação v1.0

Se quiser adquirir fichas para uma aplicação que aceite tokens v1.0 (por exemplo, a Microsoft Graph API, ou https://graph.microsoft.com) seja, terá de criar `scopes` através da concatetação de um identificador de recursos pretendido com uma permissão OAuth2 desejada para esse recurso.

Por exemplo, para aceder em nome do utilizador a V1.0 web API que é App ID `ResourceId` URI, você gostaria de usar:

```csharp
var scopes = new [] {  ResourceId+"/user_impersonation"};
```

Se quiser ler e escrever com MSAL.NET Azure Ative Directory utilizando a Microsoft Graph API https://graph.microsoft.com/) (, criaria uma lista de âmbitos como no seguinte corte:

```csharp
string ResourceId = "https://graph.microsoft.com/"; 
string[] scopes = { ResourceId + "Directory.Read", ResourceId + "Directory.Write" }
```

#### <a name="warning-should-you-have-one-or-two-slashes-in-the-scope-corresponding-to-a-v10-web-api"></a>Aviso: Se tiver uma ou duas barras no âmbito correspondente a uma API web v1.0

Se pretender escrever o âmbito correspondente à API gestora de recursos Azure https://management.core.windows.net/) (, solicite o seguinte âmbito (note as duas barras).

```csharp
var scopes = new[] {"https://management.core.windows.net//user_impersonation"};
var result = await app.AcquireTokenInteractive(scopes).ExecuteAsync();

// then call the API: https://management.azure.com/subscriptions?api-version=2016-09-01
```

Isto porque a API gestora de recursos espera um corte na sua reivindicação de audiência ( `aud` ), e depois há um corte para separar o nome API do âmbito.

A lógica utilizada pela Azure AD é a seguinte:
- Para o ponto final ADAL (v1.0) com um token de acesso v1.0 (o único possível), aud=recurso
- Para o MSAL (ponto final v2.0) pedir um token de acesso para um recurso que aceite tokens v2.0, aud=recurso. AppId
- Para o MSAL (ponto final v2.0) pedir um token de acesso para um recurso que aceite um token de acesso v1.0 (o que é o caso acima), a Azure AD analisa o público desejado a partir do âmbito solicitado, tomando tudo antes do último corte e usando-o como identificador de recursos. Portanto, se https: \/ /database.windows.net espera uma audiência de " https://database.windows.net/ ", terá de solicitar um âmbito de https: \/ /database.windows.net//.default. Ver também a edição[nº 747](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/747): O corte de rasto da url de recursos é omitido, o que causou falha de auth sql #747


### <a name="scopes-to-request-access-to-all-the-permissions-of-a-v10-application"></a>Âmbitos para solicitar acesso a todas as permissões de uma aplicação v1.0

Por exemplo, se quiser adquirir um símbolo para todos os âmbitos estáticos de uma aplicação v1.0, seria necessário utilizar

```csharp
ResourceId = "someAppIDURI";
var scopes = new [] {  ResourceId+"/.default"};
```

### <a name="scopes-to-request-in-the-case-of-client-credential-flow--daemon-app"></a>Âmbitos a solicitar no caso de fluxo de credencial de cliente/app daemon

No caso do fluxo de credencial do cliente, o âmbito de passagem seria também `/.default` . Este âmbito diz à Azure AD: "todas as permissões ao nível da aplicação a que o administrador consentiu no registo da aplicação.

## <a name="adal-to-msal-migration"></a>Migração ADAL para MSAL

Em ADAL.NET v2. X, os tokens de atualização foram expostos permitindo-lhe desenvolver soluções em torno da utilização destes tokens, caching-los e usando os `AcquireTokenByRefreshToken` métodos fornecidos por ADAL 2.x.
Algumas dessas soluções foram utilizadas em cenários como:
* Serviços de longa duração que fazem ações, incluindo dashboards refrescantes em nome dos utilizadores, enquanto os utilizadores já não estão ligados.
* Cenários WebFarm para permitir que o cliente leve o RT para o serviço web (o caching é feito lado do cliente, cookie encriptado e não o lado do servidor)

MSAL.NET não expõe fichas de atualização, por razões de segurança: a MSAL lida com fichas refrescantes para si.

Felizmente, MSAL.NET agora tem uma API que lhe permite migrar os seus tokens de atualização anteriores (adquiridos com ADAL) para `IConfidentialClientApplication` o :

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

Com este método, pode fornecer o token de atualização anteriormente usado juntamente com quaisquer âmbitos (recursos) que desejar. O token de atualização será trocado por um novo e incorporado na sua aplicação.

Como este método se destina a cenários que não são típicos, não é facilmente acessível com o `IConfidentialClientApplication` sem primeiro o lançar para `IByRefreshToken` .

Este código mostra algum código de migração numa aplicação confidencial do cliente. `GetCachedRefreshTokenForSignedInUser` recuperar o token de atualização que foi armazenado em algum armazenamento por uma versão anterior da aplicação que usado para alavancar ADAL 2.x. `GetTokenCacheForSignedInUser` deserializa uma cache para o utilizador inscrito (como aplicações confidenciais de cliente devem ter uma cache por utilizador).

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

Verá um token de acesso e um token de ID devolvidos no seu AuthenticationResult enquanto o seu novo token de atualização é armazenado na cache.

Você também pode usar este método para vários cenários de integração onde você tem um token de atualização disponível.

## <a name="next-steps"></a>Passos seguintes

Pode encontrar mais informações sobre os âmbitos de [aplicação em Scopes, permissões e consentimento na plataforma de identidade da Microsoft](v2-permissions-and-consent.md)
