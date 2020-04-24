---
title: ADAL ao guia de migração MSAL (MSAL4j) [ Azure
titleSuffix: Microsoft identity platform
description: Saiba como migrar a sua app Azure Ative Directory Authentication Library (ADAL) Java para a Microsoft Authentication Library (MSAL).
services: active-directory
author: sangonzal
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.tgt_pltfrm: Java
ms.workload: identity
ms.date: 11/04/2019
ms.author: sagonzal
ms.reviewer: nacanuma, twhitney
ms.custom: aaddev
ms.openlocfilehash: 7729a30acb1b191378960887164bb4b32e225c36
ms.sourcegitcommit: edccc241bc40b8b08f009baf29a5580bf53e220c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/24/2020
ms.locfileid: "82128014"
---
# <a name="adal-to-msal-migration-guide-for-java"></a>Guia de migração DaAL para Java

Este artigo destaca as alterações que precisa de fazer para migrar uma aplicação que utiliza a Biblioteca de Autenticação de Diretórios Ativos Azure (ADAL) para utilizar a Biblioteca de Autenticação da Microsoft (MSAL).

Tanto a Microsoft Authentication Library for Java (MSAL4J) como a Azure AD Authentication Library for Java (ADAL4J) são usadas para autenticar entidades da AD Azure e solicitar fichas da Azure AD. Até agora, a maioria dos desenvolvedores tem trabalhado com a Azure AD para a plataforma de desenvolvedores (v1.0) para autenticar identidades azure AD (contas de trabalho e escola) solicitando fichas usando a Biblioteca de Autenticação AD Azure (ADAL).

A MSAL oferece os seguintes benefícios:

- Uma vez que utiliza o ponto final da plataforma de identidade microsoft mais recente, pode autenticar um conjunto mais amplo de identidades da Microsoft, tais como identidades da AD Azure, contas microsoft e contas sociais e locais através do Azure AD Business to Consumer (B2C).
- Os seus utilizadores terão a melhor experiência de inscrição individual.
- A sua aplicação pode permitir o consentimento incremental e apoiar o acesso condicional é mais fácil.

MSAL para Java é a biblioteca auth que recomendamos que use com a plataforma de identidade microsoft. Não serão implementadas novas funcionalidades no ADAL4J. Todos os esforços em curso estão focados na melhoria do MSAL.

## <a name="differences"></a>Diferenças

Se tem trabalhado com o Azure AD para programadores (v1.0) endpoint (e ADAL4J), talvez queira ler [O que há de diferente no ponto final da plataforma de identidade da Microsoft (v2.0) .](https://docs.microsoft.com/azure/active-directory/develop/azure-ad-endpoint-comparison)

## <a name="scopes-not-resources"></a>Âmbitos não recursos

A ADAL4J adquire fichas para recursos, enquanto a MSAL para a Java adquire fichas para âmbitos. Uma série de MSAL para aulas de Java requerem um parâmetro de âmbito. Este parâmetro é uma lista de cordas que declaram as permissões e recursos desejados que são solicitados. Consulte [os âmbitos do Microsoft Graph](https://docs.microsoft.com/graph/permissions-reference) para ver os exemplos.

Pode adicionar `/.default` o sufixo de âmbito ao recurso para ajudar a migrar as suas aplicações do ponto final v1.0 (ADAL) para o ponto final da plataforma de identidade da Microsoft (MSAL). Por exemplo, para o `https://graph.microsoft.com`valor de recursos `https://graph.microsoft.com/.default`de , o valor de âmbito equivalente é .  Se o recurso não estiver no formulário URL, `XXXXXXXX-XXXX-XXXX-XXXXXXXXXXXX`mas sim numa identificação `XXXXXXXX-XXXX-XXXX-XXXXXXXXXXXX/.default`de recurso do formulário, ainda pode utilizar o valor de âmbito como .

Para mais detalhes sobre os diferentes tipos de âmbitos, consulte [permissões e consentimento na plataforma de identidade da Microsoft](https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent) e nos [Âmbitos para uma Web API aceitando artigos de tokens v1.0.](https://docs.microsoft.com/azure/active-directory/develop/msal-v1-app-scopes)

## <a name="core-classes"></a>Aulas de núcleo

No ADAL4J, `AuthenticationContext` a classe representa a sua ligação ao Serviço de Token de Segurança (STS), ou servidor de autorização, através de uma Autoridade. No entanto, a MSAL para Java é projetada em torno de aplicações de clientes. Fornece duas classes distintas: `PublicClientApplication` e `ConfidentialClientApplication` para representar as aplicações do cliente.  Este último, `ConfidentialClientApplication`representa uma aplicação que é projetada para manter de forma segura um segredo, como um identificador de aplicação para uma aplicação daemon.

A tabela que se segue mostra como o ADAL4J funciona no mapa para as funções do novo MSAL para java:

| Método ADAL4J| Método MSAL4J|
|------|-------|
|adquiretotoken (recurso de cadeia, credencial ClientCredential, AutenticaçãoCallback callback) | adquirirToken (ClientCredentialParameters)|
|adquirirToken (recurso de cadeia, afirmação de Afirmação de ClientAfirmação, AutenticaçãoCallback callback)|adquirirToken (ClientCredentialParameters)|
|adquiretoToken (recurso de cadeia, credencial assimétricaKeyCredential, autenticaçãoCallback callback)|adquirirToken (ClientCredentialParameters)|
|adquirirToken (recurso string, String clientId, String username, String password, AuthenticationCallback callback)| adquiretoToken (UsernamePasswordParameters)|
|adquiretoken (recurso string, String clientId, String username, String password=nulo, AuthenticationCallback callback)|adquiretosToken (IntegradoWindowsAuthenticationParameters)|
|adquiretoken (recurso de cadeia, userAssertion userAssertion, ClientCredential credencial, AutenticaçãoCallback callback)| adquirirToken (OnBehalfOfParameters)|
|adquiretoByAuthorizationCode() | adquiretosToken (AutorizaçõesCodeParameters) |
| adquirirDeviceCode() e adquirirTokenByDeviceDeviceCode()| adquirirToken (DispositivoCódigoParâmetros)|
|adquirirTokenByRefreshToken()| adquirirTokenSilently (SilentParameters)|

## <a name="iaccount-instead-of-iuser"></a>IAccount em vez de IUser

AADAL4J manipulou os utilizadores. Embora um utilizador represente um único agente humano ou de software, pode ter uma ou mais contas no sistema de identidade da Microsoft. Por exemplo, um utilizador pode ter várias contas pessoais Azure AD, Azure AD B2C ou Microsoft.

MSAL para Java define o conceito `IAccount` de Conta através da interface. Esta é uma mudança de rutura da ADAL4J, mas é boa porque captura o facto de que o mesmo utilizador pode ter várias contas, e talvez mesmo em diferentes diretórios da Azure AD. A MSAL para java fornece uma melhor informação nos cenários dos hóspedes porque a informação sobre a conta doméstica é fornecida.

## <a name="cache-persistence"></a>Persistência de cache

A ADAL4J não tinha apoio para cache simbólico.
A MSAL para a Java adiciona uma [cache simbólica](msal-acquire-cache-tokens.md) para simplificar a gestão das vidas token, refrescando automaticamente os tokens expirados quando possível e evitando indicações desnecessárias para o utilizador fornecer credenciais sempre que possível.

## <a name="common-authority"></a>Autoridade Comum

Em v1.0, se `https://login.microsoftonline.com/common` utilizar a autoridade, os utilizadores podem iniciar sessão com qualquer conta de Diretório Ativo Azure (AAD) (para qualquer organização).

Se utilizar `https://login.microsoftonline.com/common` a autoridade em v2.0, os utilizadores podem iniciar sessão com qualquer organização AAD, ou mesmo uma conta pessoal da Microsoft (MSA). No MSAL para Java, se quiser restringir o login a qualquer `https://login.microsoftonline.com/organizations` conta AAD, precisa de usar a autoridade (que é o mesmo comportamento que com a ADAL4J). Para especificar uma autoridade, `authority` defina o parâmetro no método [PublicClientApplication.Builder](https://javadoc.io/doc/com.microsoft.azure/msal4j/1.0.0/com/microsoft/aad/msal4j/PublicClientApplication.Builder.html) quando criar a sua `PublicClientApplication` classe.

## <a name="v10-and-v20-tokens"></a>v1.0 e v2.0 fichas

O ponto final v1.0 (utilizado pela ADAL) apenas emite fichas v1.0.

O ponto final v2.0 (utilizado pela MSAL) pode emitir fichas v1.0 e v2.0. Uma propriedade do manifesto de aplicação da Web API permite que os desenvolvedores escolham qual versão do token é aceite. Consulte `accessTokenAcceptedVersion` a documentação de referência manifesto da [aplicação.](https://docs.microsoft.com/azure/active-directory/develop/reference-app-manifest)

Para mais informações sobre as fichas v1.0 e v2.0, consulte as fichas de acesso ao [Diretório Ativo azure](https://docs.microsoft.com/azure/active-directory/develop/access-tokens).

## <a name="adal-to-msal-migration"></a>Migração ADAL para MSAL

No ADAL4J, as fichas de atualização foram expostas- o que permitiu que os desenvolvedores as cache. Em seguida, `AcquireTokenByRefreshToken()` utilizariam para permitir soluções como a implementação de serviços de longo prazo que atualizam os dashboards em nome do utilizador quando o utilizador já não está ligado.

A MSAL para Java não expõe fichas de atualização por razões de segurança. Em vez disso, a MSAL lida com fichas refrescantes para si.

A MSAL for Java tem uma API que lhe permite migrar tokens de atualização que adquiriu com a ADAL4j na ClientApplication: [acquireToken (RefreshTokenParameters)](https://javadoc.io/static/com.microsoft.azure/msal4j/1.0.0/com/microsoft/aad/msal4j/PublicClientApplication.html#acquireToken-com.microsoft.aad.msal4j.RefreshTokenParameters-). Com este método, pode fornecer o token de atualização usado anteriormente juntamente com quaisquer âmbitos (recursos) que desejar. O token de atualização será trocado por um novo e em cache para utilização pela sua aplicação.

O seguinte código de snippet mostra algum código de migração numa aplicação confidencial do cliente:

```java
String rt = GetCachedRefreshTokenForSIgnedInUser(); // Get refresh token from where you have them stored
Set<String> scopes = Collections.singleton("SCOPE_FOR_REFRESH_TOKEN");

RefreshTokenParameters parameters = RefreshTokenParameters.builder(scopes, rt).build();

PublicClientApplication app = PublicClientApplication.builder(CLIENT_ID) // ClientId for your application
                .authority(AUTHORITY)  //plug in your authority
                .build();

IAuthenticationResult result = app.acquireToken(parameters);
```

O `IAuthenticationResult` retorna um token de acesso e ficha de identificação, enquanto o seu novo token de atualização é armazenado na cache.
O pedido também irá agora conter um IAccount:

```java
Set<IAccount> accounts =  app.getAccounts().join();
```

Para usar as fichas que estão agora na cache, ligue:

```java
SilentParameters parameters = SilentParameters.builder(scope, accounts.iterator().next()).build();
IAuthenticationResult result = app.acquireToken(parameters);
```
