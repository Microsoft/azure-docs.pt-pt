---
title: Guia de migração ADAL para MSAL (MSAL4j) | Rio Azure
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
ms.custom: aaddev, devx-track-java
ms.openlocfilehash: 620f77655f8281919ba0831a7e53af8cd28bc5f2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "99583998"
---
# <a name="adal-to-msal-migration-guide-for-java"></a>Guia de migração ADAL para MSAL para Java

Este artigo destaca as alterações que precisa de fazer para migrar uma aplicação que utiliza a Biblioteca de Autenticação ativa do Azure Ative (ADAL) para utilizar a Biblioteca de Autenticação do Microsoft (MSAL).

Tanto a Biblioteca de Autenticação da Microsoft para Java (MSAL4J) como a Azure AD Authentication Library for Java (ADAL4J) são utilizadas para autenticar entidades AD do Azure e solicitar fichas da Azure AD. Até agora, a maioria dos desenvolvedores tem trabalhado com a Azure AD para a plataforma de desenvolvedores (v1.0) para autenticar identidades AZure AD (contas de trabalho e escola) solicitando tokens usando Azure AD Authentication Library (ADAL).

A MSAL oferece os seguintes benefícios:

- Uma vez que utiliza a mais recente plataforma de identidade da Microsoft, pode autenticar um conjunto mais alargado de identidades da Microsoft, tais como identidades AD AD Azure, contas da Microsoft e contas sociais e locais através do Azure AD Business to Consumer (B2C).
- Os seus utilizadores terão a melhor experiência de um único sinal.
- A sua aplicação pode permitir o consentimento incremental, e o acesso condicional é mais fácil.

MSAL para Java é a biblioteca de auth que recomendamos que utilize com a plataforma de identidade da Microsoft. Nenhuma novidade será implementada no ADAL4J. Todos os esforços a seguir estão focados em melhorar o MSAL.

## <a name="differences"></a>Diferenças

Se tem trabalhado com o AD AZure para programadores (v1.0) (e ADAL4J), talvez queira ler [O que há de diferente na plataforma de identidade da Microsoft?](../azuread-dev/azure-ad-endpoint-comparison.md)

## <a name="scopes-not-resources"></a>Âmbitos não recursos

A ADAL4J adquire fichas para recursos, enquanto a MSAL para a Java adquire fichas para âmbitos. Uma série de MSAL para as classes java requerem um parâmetro de âmbito. Este parâmetro é uma lista de cordas que declaram as permissões e recursos pretendidos que são solicitados. Consulte [os âmbitos do Microsoft Graph](/graph/permissions-reference) para ver os âmbitos de exemplo.

Pode adicionar o `/.default` sufixo de âmbito ao recurso para ajudar a migrar as suas aplicações do ADAL para o MSAL. Por exemplo, para o valor de recursos `https://graph.microsoft.com` de, o valor de âmbito equivalente é `https://graph.microsoft.com/.default` .  Se o recurso não estiver no formulário URL, mas sim num ID de recurso do `XXXXXXXX-XXXX-XXXX-XXXXXXXXXXXX` formulário, ainda pode utilizar o valor de âmbito como `XXXXXXXX-XXXX-XXXX-XXXXXXXXXXXX/.default` .

Para obter mais detalhes sobre os diferentes tipos de âmbitos, consulte [permissões e consentimento na plataforma de identidade da Microsoft](./v2-permissions-and-consent.md) e nos [Âmbitos para uma API Web aceitando artigos de fichas v1.0.](./msal-v1-app-scopes.md)

## <a name="core-classes"></a>Classes principais

Em ADAL4J, a classe representa a `AuthenticationContext` sua ligação ao Serviço de Token de Segurança (STS), ou servidor de autorização, através de uma Autoridade. No entanto, a MSAL para Java é projetada em torno de aplicações de clientes. Fornece duas classes distintas: `PublicClientApplication` e para representar as `ConfidentialClientApplication` aplicações do cliente.  Este `ConfidentialClientApplication` último, representa uma aplicação que é projetada para manter um segredo seguro, como um identificador de aplicações para uma app daemon.

A tabela a seguir mostra como o ADAL4J funciona mapear para o novo MSAL para funções de Java:

| Método ADAL4J| Método MSAL4J|
|------|-------|
|adquirirToken (recurso de corda, credencial ClientCredential, Autenticação Chamada de retorno) | adquirirToken (ClientCredentialParameters)|
|adquirirToken (recurso de corda, afirmação De ClienteAssertion, Autenticação Chamada de retorno)|adquirirToken (ClientCredentialParameters)|
|adquirirToken (recurso de corda, credencial AssimétricaKeyCredential, AutenticaçãoCallback callback)|adquirirToken (ClientCredentialParameters)|
|adquirirToken (recurso de corda, String clientId, nome de utilizador de corda, password de corda, chamada AuthenticationCallback)| adquirirToken (UsernamePasswordParameters)|
|adquirirToken (String clientId, String username, String password=null, AuthenticationCallback callback)|adquirirToken (IntegraedWindowsAuthenticationParameters)|
|adquirirToken (recurso de corda, utilizador de UtilizadorAssertion, Credencial ClienteCredential, Autenticação Chamada de Retorno)| adquirirToken (OnBehalfOfParameters)|
|adquirirTokenByAuthorizationCode() | adquirirToken (AutorizaçõesCodeParameters) |
| adquirirDeviceCode() e adquirirTokenByDeviceCode()| adquirirToken (DeviceCodeParameters)|
|adquirirTokenByRefreshToken()| adquirirTokenSilently (SilentParameters)|

## <a name="iaccount-instead-of-iuser"></a>IAconser em vez de IUser

ADAL4J manipulou os utilizadores. Embora um utilizador represente um único agente humano ou de software, pode ter uma ou mais contas no sistema de identidade da Microsoft. Por exemplo, um utilizador pode ter várias contas pessoais Azure AD, Azure AD B2C ou Microsoft.

MSAL para Java define o conceito de Conta através da `IAccount` interface. Esta é uma mudança de rutura de ADAL4J, mas é uma boa porque captura o facto de que o mesmo utilizador pode ter várias contas, e talvez até em diferentes diretórios AD do Azure. A MSAL para a Java fornece melhores informações em cenários de hóspedes porque a informação da conta de casa é fornecida.

## <a name="cache-persistence"></a>Persistência cache

A ADAL4J não tinha apoio para cache simbólico.
A MSAL para a Java adiciona uma [cache simbólica](msal-acquire-cache-tokens.md) para simplificar a gestão de vidas simbólicas, refrescando automaticamente fichas expiradas quando possível e evitando solicitações desnecessárias para que o utilizador forneça credenciais sempre que possível.

## <a name="common-authority"></a>Autoridade Comum

Em v1.0, se utilizar a `https://login.microsoftonline.com/common` autoridade, os utilizadores podem iniciar sação com qualquer conta Azure Ative Directory (AAD) (para qualquer organização).

Se utilizar a `https://login.microsoftonline.com/common` autoridade em v2.0, os utilizadores podem iniciar scontabilidade com qualquer organização AAD ou até mesmo uma conta pessoal da Microsoft (MSA). No MSAL para a Java, se pretender restringir o login a qualquer conta AAD, utilize a `https://login.microsoftonline.com/organizations` autoridade (que é o mesmo comportamento que com a ADAL4J). Para especificar uma autoridade, desapecie o `authority` parâmetro no método [PublicClientApplication.Builder](https://javadoc.io/doc/com.microsoft.azure/msal4j/1.0.0/com/microsoft/aad/msal4j/PublicClientApplication.Builder.html) quando criar a sua `PublicClientApplication` classe.

## <a name="v10-and-v20-tokens"></a>v1.0 e v2.0 fichas

O ponto final v1.0 (utilizado pela ADAL) só emite tokens v1.0.

O ponto final v2.0 (utilizado pelo MSAL) pode emitir tokens v1.0 e v2.0. Uma propriedade do manifesto de aplicação da API web permite que os desenvolvedores escolham qual versão de token é aceite. Consulte `accessTokenAcceptedVersion` na documentação de referência manifesto do [pedido.](./reference-app-manifest.md)

Para obter mais informações sobre os tokens v1.0 e v2.0, consulte [os tokens de acesso do Azure Ative Directory](./access-tokens.md).

## <a name="adal-to-msal-migration"></a>Migração ADAL para MSAL

Em ADAL4J, os tokens de atualização foram expostos - o que permitiu que os desenvolvedores os cache. Utilizariam então `AcquireTokenByRefreshToken()` soluções como a implementação de serviços de longa duração que atualizam os dashboards em nome do utilizador quando o utilizador já não está ligado.

MSAL para Java não expõe fichas de atualização por razões de segurança. Em vez disso, a MSAL lida com fichas refrescantes para si.

A MSAL para a Java tem uma API que permite migrar fichas de atualização que adquiriu com a ADAL4j para a ClientApplication: [acquireToken (RefreshTokenParameters)](https://javadoc.io/static/com.microsoft.azure/msal4j/1.0.0/com/microsoft/aad/msal4j/PublicClientApplication.html#acquireToken-com.microsoft.aad.msal4j.RefreshTokenParameters-). Com este método, pode fornecer o token de atualização anteriormente usado juntamente com quaisquer âmbitos (recursos) que desejar. O token de atualização será trocado por um novo e em cache para uso pela sua aplicação.

O seguinte código snippet mostra algum código de migração numa aplicação confidencial do cliente:

```java
String rt = GetCachedRefreshTokenForSignedInUser(); // Get refresh token from where you have them stored
Set<String> scopes = Collections.singleton("SCOPE_FOR_REFRESH_TOKEN");

RefreshTokenParameters parameters = RefreshTokenParameters.builder(scopes, rt).build();

PublicClientApplication app = PublicClientApplication.builder(CLIENT_ID) // ClientId for your application
                .authority(AUTHORITY)  //plug in your authority
                .build();

IAuthenticationResult result = app.acquireToken(parameters);
```

O `IAuthenticationResult` token de acesso e id token, enquanto o seu novo token de atualização é armazenado na cache.
A aplicação também irá agora conter uma contagem IAccount:

```java
Set<IAccount> accounts =  app.getAccounts().join();
```

Para usar os tokens que estão agora na cache, ligue:

```java
SilentParameters parameters = SilentParameters.builder(scope, accounts.iterator().next()).build();
IAuthenticationResult result = app.acquireToken(parameters);
```
