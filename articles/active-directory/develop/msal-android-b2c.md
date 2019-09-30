---
title: Azure AD B2C (biblioteca de autenticação da Microsoft para Android) | Azure
description: Saiba mais sobre considerações específicas ao usar Azure AD B2C com a biblioteca de autenticação da Microsoft para Android (MSAL. Android
services: active-directory
documentationcenter: dev-center-name
author: brianmel
manager: omkrishn
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 9/18/2019
ms.author: brianmel
ms.reviewer: rapong
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: c55356b19c8150c76858efb4edc593406c1722a4
ms.sourcegitcommit: 5f0f1accf4b03629fcb5a371d9355a99d54c5a7e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/30/2019
ms.locfileid: "71679740"
---
# <a name="use-msal-for-android-with-b2c"></a>Usar o MSAL para Android com B2C

A MSAL (biblioteca de autenticação da Microsoft) permite que os desenvolvedores de aplicativos autentiquem usuários com identidades sociais e locais usando o [Azure Active Directory B2C (Azure ad B2C)](https://docs.microsoft.com/azure/active-directory-b2c/). Azure AD B2C é um serviço de gerenciamento de identidade. Use-o para personalizar e controlar como os clientes se inscrevem, entram e gerenciam seus perfis quando usam seus aplicativos.

## <a name="configure-known-authorities-and-redirect-uri"></a>Configurar autoridades conhecidas e URI de redirecionamento

No MSAL para Android, as políticas B2C (viagens do usuário) são configuradas como autoridades individuais.

Dado um aplicativo B2C que tem duas políticas:
- Inscrição/entrada
    * Chamado `B2C_1_SISOPolicy`
- Editar perfil
    * Chamado `B2C_1_EditProfile`

O arquivo de configuração para o aplicativo declararia dois `authorities`. Uma para cada política. A propriedade `type` de cada autoridade é `B2C`.

### `app/src/main/res/raw/msal_config.json`
```json
{
    "client_id": "<your_client_id_here>",
    "redirect_uri": "<your_redirect_uri_here>",
    "authorities": [{
            "type": "B2C",
            "authority_url": "https://contoso.b2clogin.com/tfp/contoso.onmicrosoft.com/B2C_1_SISOPolicy/",
            "default": true
        },
        {
            "type": "B2C",
            "authority_url": "https://contoso.b2clogin.com/tfp/contoso.onmicrosoft.com/B2C_1_EditProfile/"
        }
    ]
}
```

O `redirect_uri` deve ser registrado na configuração do aplicativo e também em `AndroidManifest.xml` para dar suporte ao redirecionamento durante o [fluxo de concessão do código de autorização](https://docs.microsoft.com/en-us/azure/active-directory-b2c/active-directory-b2c-reference-oauth-code).

## <a name="initialize-ipublicclientapplication"></a>Inicializar IPublicClientApplication

`IPublicClientApplication` é construído por um método de fábrica para permitir que a configuração do aplicativo seja analisada de forma assíncrona.

```java
PublicClientApplication.createMultipleAccountPublicClientApplication(
    context, // Your application Context
    R.raw.msal_config, // Id of app JSON config
    new IPublicClientApplication.ApplicationCreatedListener() {
        @Override
        public void onCreated(IMultipleAccountPublicClientApplication pca) {
            // Application has been initialized.
        }

        @Override
        public void onError(MsalException exception) {
            // Application could not be created.
            // Check Exception message for details.
        }
    }
);
```

## <a name="interactively-acquire-a-token"></a>Adquirir um token de forma interativa

Para adquirir um token interativamente com o MSAL, crie uma instância `AcquireTokenParameters` e forneça-a ao método `acquireToken`. A solicitação de token abaixo usa a autoridade `default`.

```java
IMultipleAccountPublicClientApplication pca = ...; // Initialization not shown

AcquireTokenParameters parameters = new AcquireTokenParameters.Builder()
    .startAuthorizationFromActivity(activity)
    .withScopes(Arrays.asList("https://contoso.onmicrosoft.com/contosob2c/read")) // Provide your registered scope here
    .withPrompt(Prompt.LOGIN)
    .callback(new AuthenticationCallback() {
        @Override
        public void onSuccess(IAuthenticationResult authenticationResult) {
            // Token request was successful, inspect the result
        }

        @Override
        public void onError(MsalException exception) {
            // Token request was unsuccessful, inspect the exception
        }

        @Override
        public void onCancel() {
            // The user cancelled the flow
        }
    }).build();

pca.acquireToken(parameters);
```

## <a name="silently-renew-a-token"></a>Renovar silenciosamente um token

Para adquirir um token silenciosamente com MSAL, crie uma instância `AcquireTokenSilentParameters` e forneça-a ao método `acquireTokenSilentAsync`. Ao contrário do método `acquireToken`, o `authority` deve ser especificado para adquirir um token silenciosamente.

```java
IMultilpeAccountPublicClientApplication pca = ...; // Initialization not shown
AcquireTokenSilentParameters parameters = new AcquireTokenSilentParameters.Builder()
    .withScopes(Arrays.asList("https://contoso.onmicrosoft.com/contosob2c/read")) // Provide your registered scope here
    .forAccount(account)
    // Select a configured authority (policy), mandatory for silent auth requests
    .fromAuthority("https://contoso.b2clogin.com/tfp/contoso.onmicrosoft.com/B2C_1_SISOPolicy/")
    .callback(new SilentAuthenticationCallback() {
        @Override
        public void onSuccess(IAuthenticationResult authenticationResult) {
            // Token request was successful, inspect the result
        }

        @Override
        public void onError(MsalException exception) {
            // Token request was unsuccesful, inspect the exception
        }
    })
    .build();

pca.acquireTokenSilentAsync(parameters);
```

## <a name="specify-a-policy"></a>Especificar uma política

Como as políticas no B2C são representadas como autoridades separadas, invocar uma política diferente do padrão é obtido especificando uma cláusula `fromAuthority` ao construir parâmetros `acquireToken` ou `acquireTokenSilent`.  Por exemplo:

```java
AcquireTokenParameters parameters = new AcquireTokenParameters.Builder()
    .startAuthorizationFromActivity(activity)
    .withScopes(Arrays.asList("https://contoso.onmicrosoft.com/contosob2c/read")) // Provide your registered scope here
    .withPrompt(Prompt.LOGIN)
    .callback(...) // provide callback here
    .fromAuthority("<url_of_policy_defined_in_configuration_json>")
    .build();
```

## <a name="handle-password-change-policies"></a>Manipular políticas de alteração de senha

O fluxo de usuário de inscrição ou entrada da conta local mostra uma '**esqueceu a senha?** ' criar. Clicar nesse link não disparará automaticamente um fluxo de usuário de redefinição de senha.

Em vez disso, o código de erro `AADB2C90118` é retornado ao seu aplicativo. Seu aplicativo deve lidar com esse código de erro executando um fluxo de usuário específico que redefine a senha.

Para capturar um código de erro de redefinição de senha, a seguinte implementação pode ser usada dentro de seu `AuthenticationCallback`:

```java
new AuthenticationCallback() {

    @Override
    public void onSuccess(IAuthenticationResult authenticationResult) {
        // ..
    }

    @Override
    public void onError(MsalException exception) {
        final String B2C_PASSWORD_CHANGE = "AADB2C90118";

        if (exception.getMessage().contains(B2C_PASSWORD_CHANGE)) {
            // invoke password reset flow
        }
    }

    @Override
    public void onCancel() {
        // ..
    }
}
```

## <a name="use-iauthenticationresult"></a>Usar IAuthenticationResult

Uma aquisição de token bem-sucedida resulta em um objeto `IAuthenticationResult`. Ele contém o token de acesso, as declarações do usuário e os metadados.

### <a name="get-the-access-token-and-related-properties"></a>Obter o token de acesso e as propriedades relacionadas

```java
// Get the raw bearer token
String accessToken = authenticationResult.getAccessToken();

// Get the scopes included in the access token
String[] accessTokenScopes = authenticationResult.getScope();

// Gets the access token's expiry
Date expiry = authenticationResult.getExpiresOn();

// Get the tenant for which this access token was issued
String tenantId = authenticationResult.getTenantId();
```

### <a name="get-the-authorized-account"></a>Obter a conta autorizada

```java
// Get the account from the result
IAccount account = authenticationResult.getAccount();

// Get the id of this account - note for B2C, the policy name is a part of the id
String id = account.getId();

// Get the IdToken Claims
//
// For more information about B2C token claims, see reference documentation
// https://docs.microsoft.com/en-us/azure/active-directory-b2c/active-directory-b2c-reference-tokens
Map<String, ?> claims = account.getClaims();

// Get the 'preferred_username' claim through a convenience function
String username = account.getUsername();

// Get the tenant id (tid) claim through a convenience function
String tenantId = account.getTenantId();
```

### <a name="idtoken-claims"></a>Declarações de token

As declarações retornadas no token são populadas pelo serviço de token de segurança (STS), não por MSAL. Dependendo do IdP (provedor de identidade) usado, algumas declarações podem estar ausentes. Alguns IdPs não fornecem atualmente a declaração `preferred_username`. Como essa declaração é usada pelo MSAL para cache, um valor de espaço reservado, `MISSING FROM THE TOKEN RESPONSE`, é usado em seu lugar. Para obter mais informações sobre as declarações do B2C token, consulte [visão geral dos tokens em Azure Active Directory B2C](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-tokens#claims).

## <a name="managing-accounts-and-policies"></a>Gerenciando contas e políticas

O B2C trata cada política como uma autoridade separada. Portanto, os tokens de acesso, os tokens de atualização e os tokens de ID retornados de cada política não são intercambiáveis. Isso significa que cada política retorna um objeto `IAccount` separado cujos tokens não podem ser usados para invocar outras políticas.

Cada política adiciona um `IAccount` ao cache para cada usuário. Se um usuário entrar em um aplicativo e invocar duas políticas, ele terá dois `IAccount`s. Para remover esse usuário do cache, você deve chamar `removeAccount()` para cada política.

Quando você renova tokens para uma política com `acquireTokenSilent`, forneça o mesmo `IAccount` que foi retornado de invocações anteriores da política para `AcquireTokenSilentParameters`. Fornecer uma conta retornada por outra política resultará em um erro.

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre o Azure Active Directory B2C (Azure AD B2C) em [o que está Azure Active Directory B2C?](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-overview)
