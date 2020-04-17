---
title: Migrando para MSAL.NET
titleSuffix: Microsoft identity platform
description: Conheça as diferenças entre a Microsoft Authentication Library para .NET (MSAL.NET) e a Azure AD Authentication Library para .NET (ADAL.NET) e como migrar para MSAL.NET.
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
ms.openlocfilehash: f389943d284c573312473f426048f8aadb79088e
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81533977"
---
# <a name="migrating-applications-to-msalnet"></a>Aplicações migratórias para MSAL.NET

Tanto a Microsoft Authentication Library for .NET (MSAL.NET) como a Azure AD Authentication Library for .NET (ADAL.NET) são utilizadas para autenticar entidades da AD Azure e solicitar fichas da Azure AD. Até agora, a maioria dos desenvolvedores tem trabalhado com a Azure AD para a plataforma de desenvolvedores (v1.0) para autenticar identidades azure AD (contas de trabalho e escola) solicitando fichas usando a Biblioteca de Autenticação AD Azure (ADAL). Utilizando a MSAL:

- pode autenticar um conjunto mais amplo de identidades da Microsoft (identidades AD Azure e contas microsoft, e contas sociais e locais através do Azure AD B2C) uma vez que utiliza o ponto final da plataforma de identidade microsoft,
- os seus utilizadores terão a melhor experiência de inscrição individual.
- a sua aplicação pode permitir o consentimento incremental, e apoiar o Acesso Condicional é mais fácil
- beneficia-se da inovação.

**MSAL.NET é agora a biblioteca auth recomendada para usar com a plataforma de identidade microsoft**. Não serão implementadas novas funcionalidades no ADAL.NET. Os esforços centram-se na melhoria do MSAL.

Este artigo descreve as diferenças entre a Biblioteca de Autenticação da Microsoft para .NET (MSAL.NET) e a Biblioteca de Autenticação AD Azure para .NET (ADAL.NET) e ajuda-o a migrar para mSAL.

## <a name="differences-between-adal-and-msal-apps"></a>Diferenças entre apps ADAL e MSAL

Na maioria dos casos, pretende utilizar MSAL.NET e o ponto final da plataforma de identidade microsoft, que é a mais recente geração de bibliotecas de autenticação da Microsoft. Utilizando MSAL.NET, adquire fichas para utilizadores que saem na sua aplicação com a Azure AD (contas de trabalho e escola), contas Microsoft (pessoais) (MSA) ou Azure AD B2C.

Se já está familiarizado com o Ponto Final do Azure (v1.0) (e ADAL.NET), talvez queira ler [O que há de diferente no ponto final da plataforma de identidade da Microsoft (v2.0) .](active-directory-v2-compare.md)

No entanto, ainda precisa de utilizar ADAL.NET se a sua aplicação precisar de contratar utilizadores com versões anteriores dos Serviços da Federação de [DiretórioAtivo (ADFS).](/windows-server/identity/active-directory-federation-services) Para mais informações, consulte o [suporte da ADFS](https://aka.ms/msal-net-adfs-support).

A imagem que se segue resume algumas ![das diferenças entre ADAL.NET e MSAL.NET código lado a lado](./media/msal-compare-msaldotnet-and-adaldotnet/differences.png)

### <a name="nuget-packages-and-namespaces"></a>Pacotes NuGet e espaços de nome

ADAL.NET é consumido a partir do pacote [Microsoft.IdentityModel.Clients.ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory) NuGet. o espaço de `Microsoft.IdentityModel.Clients.ActiveDirectory`nome a utilizar é .

Para utilizar MSAL.NET terá de adicionar o pacote [Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client) NuGet e utilizar o espaço de `Microsoft.Identity.Client` nome

### <a name="scopes-not-resources"></a>Âmbitos não recursos

ADAL.NET adquire fichas para *recursos,* mas MSAL.NET adquire fichas para *âmbitos.* Alguns MSAL.NET substituições acquireToken requerem um`IEnumerable<string> scopes`parâmetro chamado scopes( ). Este parâmetro é uma simples lista de cordas que declaram as permissões e recursos desejados que são solicitados. Os âmbitos bem conhecidos são os [âmbitos do Microsoft Graph.](/graph/permissions-reference)

Também é possível em MSAL.NET aceder aos recursos v1.0. Consulte os detalhes em [Scopes para obter uma aplicação v1.0](#scopes-for-a-web-api-accepting-v10-tokens).

### <a name="core-classes"></a>Aulas de núcleo

- ADAL.NET utiliza o [AuthenticationContext](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/AuthenticationContext:-the-connection-to-Azure-AD) como representação da sua ligação ao Serviço de Token de Segurança (STS) ou servidor de autorização, através de uma Autoridade. Pelo contrário, MSAL.NET é projetado em torno de [aplicações de clientes.](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Client-Applications) Fornece duas classes separadas: `PublicClientApplication` e`ConfidentialClientApplication`

- Aquisição de Tokens: ADAL.NET e MSAL.NET têm`AcquireTokenAsync` `AcquireTokenSilentAsync` as mesmas `AcquireTokenInteractive` chamadas `AcquireTokenSilent` de autenticação ( e para ADAL.NET, e em MSAL.NET), mas com diferentes parâmetros necessários. Uma diferença é o facto de, em MSAL.NET, `ClientID` já não ter de passar na sua aplicação em todas as chamadas AcquireTokenXX. Na verdade, `ClientID` o é definido apenas`IPublicClientApplication` `IConfidentialClientApplication`uma vez quando se constrói o ( ou ).

### <a name="iaccount-not-iuser"></a>IAccount não IUser

ADAL.NET utilizadores manipulados. No entanto, um utilizador é um agente humano ou um agente de software, mas pode possuir/possuir/ser responsável por uma ou mais contas no sistema de identidade da Microsoft (várias contas Azure AD, Azure AD B2C, contas pessoais da Microsoft).

MSAL.NET 2.x define agora o conceito de Conta (através da interface IAccount). Esta alteração de rutura fornece a semântica certa: o facto de o mesmo utilizador poder ter várias contas, em diferentes diretórios da AD Azure. Também MSAL.NET fornece melhores informações nos cenários dos hóspedes, uma vez que a informação sobre a conta doméstica é fornecida.

Para obter mais informações sobre as diferenças entre iUser e IAccount, consulte [MSAL.NET 2.x](https://aka.ms/msal-net-2-released).

### <a name="exceptions"></a>Exceções

#### <a name="interaction-required-exceptions"></a>Interação requerida exceções

MSAL.NET tem exceções mais explícitas. Por exemplo, quando a autenticação silenciosa falha na ADAL, o `user_interaction_required` procedimento é apanhar a exceção e procurar o código de erro:

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

Consulte detalhes no [padrão recomendado para adquirir um símbolo](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/AcquireTokenSilentAsync-using-a-cached-token#recommended-pattern-to-acquire-a-token) com ADAL.NET

Utilizando MSAL.NET, `MsalUiRequiredException` apanha-se como descrito no [AcquireTokenSilent](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/AcquireTokenSilentAsync-using-a-cached-token).

```csharp
catch(MsalUiRequiredException exception)
{
 try {“try to authenticate interactively”}
}
```

#### <a name="handling-claim-challenge-exceptions"></a>Manipulação de pedidos de impugnação exceções

Em ADAL.NET, as exceções ao desafio de reclamação são tratadas da seguinte forma:

- `AdalClaimChallengeException`é uma exceção (derivada de `AdalServiceException`) lançada pelo serviço no caso de um recurso exigir mais reclamações do utilizador (por exemplo, autenticação de dois fatores). O `Claims` membro contém um fragmento jSON com as reivindicações, que são esperadas.
- Ainda em ADAL.NET, a aplicação do cliente `AcquireTokenInteractive` público que recebe esta exceção precisa de chamar a anulação com um parâmetro de reclamações. Esta sobreposição `AcquireTokenInteractive` nem sequer tenta atingir a cache, uma vez que não é necessária. A razão é que o símbolo na cache não tem `AdalClaimChallengeException` as reivindicações certas (caso contrário, um não teria sido atirado). Portanto, não há necessidade de olhar para a cache. Note que `ClaimChallengeException` o pode ser recebido em um WebAPI fazendo OBO, enquanto que a `AcquireTokenInteractive` necessidade deve ser chamada em uma aplicação de cliente público chamando esta Web API.
- para mais detalhes, incluindo amostras ver Handling [AdalClaimChallengeException](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Exceptions-in-ADAL.NET#handling-adalclaimchallengeexception)

Em MSAL.NET, as exceções ao desafio de reclamação são tratadas da seguinte forma:

- As `Claims` são surgidas `MsalServiceException`na .
- Há um `.WithClaim(claims)` método que pode `AcquireTokenInteractive` aplicar-se ao construtor.

### <a name="supported-grants"></a>Subvenções apoiadas

Nem todas as subvenções são ainda apoiadas em MSAL.NET e no ponto final v2.0. Segue-se um resumo que compara ADAL.NET e MSAL. As subvenções apoiadas pela NET.

#### <a name="public-client-applications"></a>Aplicações de clientes públicos

Aqui estão as bolsas suportadas em ADAL.NET e MSAL.NET para aplicações desktop e mobile

Concessão | ADAL.NET | MSAL.NET
----- |----- | -----
Interativo | [Auth Interativo](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Acquiring-tokens-interactively---Public-client-application-flows) | [Adquirir fichas interativamente em MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Acquiring-tokens-interactively)
Autenticação Integrada do Windows. | [Autenticação integrada no Windows (Kerberos)](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/AcquireTokenSilentAsync-using-Integrated-authentication-on-Windows-(Kerberos)) | [Autenticação Integrada do Windows](msal-authentication-flows.md#integrated-windows-authentication)
Nome de utilizador / Palavra-passe | [Adquirir fichas com nome de utilizador e senha](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Acquiring-tokens-with-username-and-password)| [Autenticação de palavra-passe de nome de utilizador](msal-authentication-flows.md#usernamepassword)
Fluxo de código do dispositivo | [Perfil do dispositivo para dispositivos sem navegadorweb](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Device-profile-for-devices-without-web-browsers) | [Fluxo de código de dispositivo](msal-authentication-flows.md#device-code)

#### <a name="confidential-client-applications"></a>Aplicações confidenciais de clientes

Aqui estão as subvenções suportadas em ADAL.NET e MSAL.NET para aplicações web, APIs web e aplicações daemon:

Tipo de App | Concessão | ADAL.NET | MSAL.NET
----- | ----- | ----- | -----
Web app, web API, daemon | Credenciais de cliente | [Fluxos credenciais de cliente em ADAL.NET](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Client-credential-flows) | [Fluxos credenciais](msal-authentication-flows.md#client-credentials)de cliente em MSAL.NET)
API Web | Em nome de | [Serviço de atendimento de chamadas em nome do utilizador com ADAL.NET](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Service-to-service-calls-on-behalf-of-the-user) | [Em nome de MSAL.NET](msal-authentication-flows.md#on-behalf-of)
Aplicação Web | Código Auth | [Adquirir fichas com códigos de autorização em aplicações web com ADAL.NET](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Acquiring-tokens-with-authorization-codes-on-web-apps) | [Adquirir fichas com códigos de autorização em aplicações web com A MSAL.NET](msal-authentication-flows.md#authorization-code)

### <a name="cache-persistence"></a>Persistência de cache

ADAL.NET permite-lhe `TokenCache` estender a classe para implementar a funcionalidade de persistência desejada nas plataformas sem `BeforeAccess`um `BeforeWrite` armazenamento seguro (.QUADRO NET e núcleo .NET) utilizando os métodos e métodos. Para mais detalhes, consulte [Token Cache Serialization em ADAL.NET](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Token-cache-serialization).

MSAL.NET faz da cache simbólica uma classe selada, removendo a capacidade de a estender. Portanto, a sua implementação da persistência de cache simbólica deve ser na forma de uma classe de ajudante que interaja com a cache token selada. Esta interação é descrita em [Token Cache Serialization em MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/token-cache-serialization).

## <a name="signification-of-the-common-authority"></a>Significado da autoridade comum

Em v1.0, se `https://login.microsoftonline.com/common` utilizar a autoridade, permitirá que os utilizadores assinem qualquer conta AAD (para qualquer organização). Ver [Validação da Autoridade em ADAL.NET](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/AuthenticationContext:-the-connection-to-Azure-AD#authority-validation)

Se utilizar `https://login.microsoftonline.com/common` a autoridade em v2.0, permitirá que os utilizadores se inscrevam em qualquer organização AAD ou numa conta pessoal da Microsoft (MSA). Na MSAL.NET, se quiser restringir o login a qualquer conta AAD (o mesmo comportamento que com ADAL.NET), precisa de usar `https://login.microsoftonline.com/organizations`. Para mais detalhes, consulte o parâmetro na aplicação do `authority` cliente [público.](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Client-Applications#publicclientapplication)

## <a name="v10-and-v20-tokens"></a>v1.0 e v2.0 fichas

Há duas versões de fichas:
- v1.0 fichas
- fichas v2.0

O ponto final v1.0 (utilizado pela ADAL) apenas emite fichas v1.0.

No entanto, o ponto final v2.0 (usado pela MSAL) emite a versão do símbolo que a Web API aceita. Uma propriedade do manifesto de aplicação da Web API permite que os desenvolvedores escolham qual versão do token é aceite. Consulte `accessTokenAcceptedVersion` a documentação de referência manifesto da [Aplicação.](reference-app-manifest.md)

Para mais informações sobre as fichas v1.0 e v2.0, consulte fichas de acesso ao [Diretório Ativo Azure](access-tokens.md)

## <a name="scopes-for-a-web-api-accepting-v10-tokens"></a>Âmbitos para uma Web API aceitando fichas v1.0

As permissões OAuth2 são âmbitos de permissão que uma aplicação v1.0 web API (recurso) expõe às aplicações do cliente. Estes âmbitos de autorização podem ser concedidos aos pedidos do cliente durante o consentimento. Consulte a secção sobre oauth2Permissions no manifesto de [aplicação do Diretório Ativo Azure](active-directory-application-manifest.md).

### <a name="scopes-to-request-access-to-specific-oauth2-permissions-of-a-v10-application"></a>Âmbitos para solicitar acesso a permissões específicas da OAuth2 de um pedido v1.0

Se quiser adquirir fichas para uma aplicação que aceite fichas v1.0 (por https://graph.microsoft.com)exemplo, a Microsoft `scopes` Graph API, ou seja, teria de criar concatenando um identificador de recursos desejado com uma permissão OAuth2 desejada para esse recurso.

Por exemplo, para aceder em nome do utilizador a um API `ResourceId`web v1.0 que o App ID URI é, você gostaria de usar:

```csharp
var scopes = new [] {  ResourceId+"/user_impersonation"};
```

Se quiser ler e escrever com MSAL.NET Diretório Ativo Azurehttps://graph.microsoft.com/)utilizando o Microsoft Graph API ( , criará uma lista de âmbitos como no seguinte corte:

```csharp
ResourceId = "https://graph.microsoft.com/";
var scopes = new [] { ResourceId + "Directory.Read", ResourceID + "Directory.Write"}
```

#### <a name="warning-should-you-have-one-or-two-slashes-in-the-scope-corresponding-to-a-v10-web-api"></a>Aviso: Se tiver uma ou duas barras no âmbito correspondente a uma API web v1.0

Se pretender escrever o âmbito correspondente à APIhttps://management.core.windows.net/)do Gestor de Recursos do Azure , tem de solicitar o seguinte âmbito (note as duas barras)

```csharp
var scopes = new[] {"https://management.core.windows.net//user_impersonation"};
var result = await app.AcquireTokenInteractive(scopes).ExecuteAsync();

// then call the API: https://management.azure.com/subscriptions?api-version=2016-09-01
```

Isto porque a API, gestora de recursos, espera um corte na sua reivindicação de audiência (`aud`) e depois há um corte para separar o nome API do âmbito.

A lógica utilizada pela Azure AD é a seguinte:
- Para o ponto final ADAL (v1.0) com um token de acesso v1.0 (o único possível), aud=recurso
- Para mSAL (v2.0 endpoint) pedir um sinal de acesso para um recurso que aceite fichas v2.0, aud=recurso. Appid
- Para a MSAL (v2.0 endpoint) pedir um sinal de acesso para um recurso que aceite um token de acesso v1.0 (o que é o caso acima), a Azure AD analisa o público desejado do âmbito solicitado, tomando tudo antes do último corte e usando-o como identificador de recursos. Portanto, se\/https: /database.windows.net esperahttps://database.windows.net/uma audiência de " ", terá\/de solicitar um âmbito de https: /database.windows.net//.default. Ver também edição[#747](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/747): Omitida a barra de rasto do url de recursos, o que causou falha no sql auth #747


### <a name="scopes-to-request-access-to-all-the-permissions-of-a-v10-application"></a>Âmbitos para solicitar acesso a todas as permissões de um pedido v1.0

Por exemplo, se quiser adquirir um símbolo para todos os âmbitos estáticos de uma aplicação v1.0, é necessário utilizar

```csharp
ResourceId = "someAppIDURI";
var scopes = new [] {  ResourceId+"/.default"};
```

### <a name="scopes-to-request-in-the-case-of-client-credential-flow--daemon-app"></a>Âmbitos a solicitar no caso da aplicação de fluxo/daemon do cliente

No caso do fluxo de credenciais do cliente, `/.default`o âmbito a passar seria também. Este âmbito diz à Azure AD: "todas as permissões ao nível da aplicação que o administrador consentiu no registo da candidatura.

## <a name="adal-to-msal-migration"></a>Migração ADAL para MSAL

Em ADAL.NET v2. X, as fichas de atualização foram expostas permitindo-lhe desenvolver soluções em torno `AcquireTokenByRefreshToken` da utilização destes tokens, apertando-os e utilizando os métodos fornecidos pela ADAL 2.x.
Algumas dessas soluções foram utilizadas em cenários como:
* Serviços de longo prazo que fazem ações incluindo painéis refrescantes em nome dos utilizadores, enquanto os utilizadores já não estão ligados.
* Cenários webFarm para permitir ao cliente trazer o RT para o serviço web (caching é feito lado do cliente, cookie encriptado, e não lado do servidor)

MSAL.NET não expõe tokens de atualização, por razões de segurança: a MSAL lida com tokens refrescantes para si.

Felizmente, MSAL.NET agora tem uma API que lhe permite migrar as suas fichas de `IConfidentialClientApplication`atualização anteriores (adquiridas com a ADAL) para:

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

Com este método, pode fornecer o token de atualização usado anteriormente juntamente com quaisquer âmbitos (recursos) que desejar. O token de atualização será trocado por um novo e colocado em cache na sua aplicação.

Como este método se destina a cenários que não são `IConfidentialClientApplication` típicos, `IByRefreshToken`não é facilmente acessível com o sem primeiro o lançar para .

Este código mostra algum código de migração numa aplicação confidencial do cliente. `GetCachedRefreshTokenForSignedInUser`recuperar o token de atualização que foi armazenado em algum armazenamento por uma versão anterior da aplicação que costumava alavancar ADAL 2.x. `GetTokenCacheForSignedInUser`desserializa uma cache para o utilizador inscrito (uma vez que as aplicações confidenciais do cliente devem ter uma cache por utilizador).

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

Verá um token de acesso e um token de identificação devolvidos no seu AuthenticationResult enquanto o seu novo token de atualização está guardado na cache.

Também pode utilizar este método para vários cenários de integração onde tem um token atualizado disponível.

## <a name="next-steps"></a>Passos seguintes

Pode encontrar mais informações sobre os âmbitos em [Scopes, permissões e consentimento no ponto final da plataforma de identidade da Microsoft](v2-permissions-and-consent.md)
