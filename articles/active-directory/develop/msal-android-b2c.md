---
title: Azure AD B2C (MSAL Android) Rio Azure
titleSuffix: Microsoft identity platform
description: Saiba mais sobre considerações específicas ao utilizar o Azure AD B2C com a Microsoft Authentication Library para Android (MSAL). Android)
services: active-directory
author: brianmel
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 9/18/2019
ms.author: brianmel
ms.reviewer: rapong
ms.custom: aaddev
ms.openlocfilehash: 1c4f47fd771cfb92b3896963c96b39d9eb7d97b8
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/30/2020
ms.locfileid: "96344883"
---
# <a name="use-msal-for-android-with-b2c"></a>Use o MSAL para Android com B2C

A Microsoft Authentication Library (MSAL) permite que os desenvolvedores de aplicações autuçam os utilizadores com identidades sociais e locais utilizando [o Azure Ative Directory B2C (Azure AD B2C)](../../active-directory-b2c/index.yml). Azure AD B2C é um serviço de gestão de identidade. Use-o para personalizar e controlar a forma como os clientes se inscrevem, se inscrevem e gerem os seus perfis quando utilizam as suas aplicações.

## <a name="configure-known-authorities-and-redirect-uri"></a>Configure as autoridades conhecidas e redirecione a URI

No MSAL para Android, as políticas B2C (viagens de utilizador) são configuradas como autoridades individuais.

Dada uma aplicação B2C que tem duas políticas:
- Inscrição / Inscrição
    * Chamado `B2C_1_SISOPolicy`
- Editar Perfil
    * Chamado `B2C_1_EditProfile`

O ficheiro de configuração da aplicação declararia dois `authorities` . Um para cada política. A `type` propriedade de cada autoridade `B2C` é.

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

O `redirect_uri` deve ser registado na configuração da aplicação, bem como para apoiar a  `AndroidManifest.xml` reorientação durante o fluxo de [concessão](../../active-directory-b2c/authorization-code-flow.md)de código de autorização .

## <a name="initialize-ipublicclientapplication"></a>Inicializar IPublicClientApplication

`IPublicClientApplication` é construído por um método de fábrica para permitir que a configuração da aplicação seja analisada assíncroneamente.

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

## <a name="interactively-acquire-a-token"></a>Adquirir interativamente um símbolo

Para adquirir um símbolo interativamente com a MSAL, construa uma `AcquireTokenParameters` instância e forneça-a ao `acquireToken` método. O pedido simbólico abaixo usa a `default` autoridade.

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

## <a name="silently-renew-a-token"></a>Silenciosamente renovar um símbolo

Para adquirir um símbolo silenciosamente com a MSAL, construa uma `AcquireTokenSilentParameters` instância e forneça-a ao `acquireTokenSilentAsync` método. Ao contrário do `acquireToken` método, `authority` o deve ser especificado para adquirir um símbolo silenciosamente.

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
            // Token request was unsuccessful, inspect the exception
        }
    })
    .build();

pca.acquireTokenSilentAsync(parameters);
```

## <a name="specify-a-policy"></a>Especificar uma política

Uma vez que as políticas em B2C são representadas como autoridades separadas, invocar uma política que não o padrão é conseguida especificando uma `fromAuthority` cláusula na construção `acquireToken` ou `acquireTokenSilent` parâmetros.  Por exemplo:

```java
AcquireTokenParameters parameters = new AcquireTokenParameters.Builder()
    .startAuthorizationFromActivity(activity)
    .withScopes(Arrays.asList("https://contoso.onmicrosoft.com/contosob2c/read")) // Provide your registered scope here
    .withPrompt(Prompt.LOGIN)
    .callback(...) // provide callback here
    .fromAuthority("<url_of_policy_defined_in_configuration_json>")
    .build();
```

## <a name="handle-password-change-policies"></a>Lidar com políticas de mudança de senha

A inscrição na conta local ou o fluxo de utilizador de entrada mostra uma **' Palavra-passe esquecida?** . Clicar neste link não aciona automaticamente o fluxo do utilizador de redefinição de palavra-passe.

Em vez disso, o código de erro `AADB2C90118` é devolvido à sua aplicação. A sua aplicação deve lidar com este código de erro executando um fluxo de utilizador específico que reinicie a palavra-passe.

Para capturar um código de erro de redefinição de palavra-passe, pode ser utilizada a seguinte implementação dentro do seu `AuthenticationCallback` :

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

## <a name="use-iauthenticationresult"></a>Utilizar IAuthenticationResult

Uma aquisição de fichas bem sucedida resulta num `IAuthenticationResult` objeto. Contém o token de acesso, reclamações de utilizador e metadados.

### <a name="get-the-access-token-and-related-properties"></a>Obtenha o token de acesso e propriedades relacionadas

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

### <a name="get-the-authorized-account"></a>Obtenha a conta autorizada

```java
// Get the account from the result
IAccount account = authenticationResult.getAccount();

// Get the id of this account - note for B2C, the policy name is a part of the id
String id = account.getId();

// Get the IdToken Claims
//
// For more information about B2C token claims, see reference documentation
// https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-tokens
Map<String, ?> claims = account.getClaims();

// Get the 'preferred_username' claim through a convenience function
String username = account.getUsername();

// Get the tenant id (tid) claim through a convenience function
String tenantId = account.getTenantId();
```

### <a name="idtoken-claims"></a>IdToken reclama

As reclamações devolvidas no IdToken são povoadas pelo Serviço de Token de Segurança (STS), e não pela MSAL. Dependendo do fornecedor de identidade (IdP) utilizado, algumas reclamações podem estar ausentes. Alguns IDPs não fornecem a `preferred_username` reclamação. Uma vez que esta alegação é utilizada pela MSAL para caching, um valor de espaço `MISSING FROM THE TOKEN RESPONSE` reservado, é usado no seu lugar. Para obter mais informações sobre as reclamações do B2C IdToken, consulte [a visão geral dos tokens no Azure Ative Directory B2C](../../active-directory-b2c/tokens-overview.md#claims).

## <a name="managing-accounts-and-policies"></a>Gestão de contas e políticas

B2C trata cada política como uma autoridade separada. Assim, os tokens de acesso, tokens de atualização e fichas de identificação devolvidas de cada política não são permutáveis. Isto significa que cada política devolve um objeto separado `IAccount` cujos símbolos não podem ser usados para invocar outras políticas.

Cada política adiciona uma `IAccount` cache para cada utilizador. Se um utilizador assinar uma aplicação e invocar duas apólices, terá duas `IAccount` s. Para remover este utilizador da cache, tem de pedir `removeAccount()` cada apólice.

Quando renovar os tokens para uma política com `acquireTokenSilent` , forneça o mesmo que foi devolvido de `IAccount` invocações anteriores da apólice para  `AcquireTokenSilentParameters` . Fornecer uma conta devolvida por outra apólice resultará num erro.

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre o Azure Ative Directory B2C (Azure AD B2C) no [What is Azure Ative Directory B2C?](../../active-directory-b2c/overview.md)