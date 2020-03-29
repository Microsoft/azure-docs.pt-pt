---
title: Azure AD B2C (Android MSAL) / Azure
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
ms.openlocfilehash: 0998bb04b0dfc69db4696f2e390cfe259eba6718
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76696526"
---
# <a name="use-msal-for-android-with-b2c"></a>Use MSAL para Android com B2C

A Microsoft Authentication Library (MSAL) permite que os desenvolvedores de aplicações autentiquem os utilizadores com identidades sociais e locais utilizando o [Azure Ative Directory B2C (Azure AD B2C)](https://docs.microsoft.com/azure/active-directory-b2c/). Azure AD B2C é um serviço de gestão de identidade. Use-o para personalizar e controlar a forma como os clientes se inscrevem, fazem o insessão e gerem os seus perfis quando utilizam as suas aplicações.

## <a name="configure-known-authorities-and-redirect-uri"></a>Configure as autoridades conhecidas e redirecione o URI

Em MSAL para Android, as políticas B2C (viagens de utilizador) são configuradas como autoridades individuais.

Dada uma aplicação B2C que tem duas políticas:
- Inscrição / Inscrição
    * Chamado`B2C_1_SISOPolicy`
- Editar Perfil
    * Chamado`B2C_1_EditProfile`

O ficheiro de configuração `authorities`da aplicação declararia dois . Um para cada apólice. A `type` propriedade de cada `B2C`autoridade é.

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

A `redirect_uri` configuração da aplicação deve `AndroidManifest.xml` ser registada e também para apoiar a reorientação durante o fluxo de [concessão](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-oauth-code)do código de autorização .

## <a name="initialize-ipublicclientapplication"></a>Inicializar a Aplicação IPublicClientApplication

`IPublicClientApplication`é construído por um método de fábrica para permitir que a configuração da aplicação seja analisada de forma assíncrona.

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

Para adquirir um símbolo interactivamente com `AcquireTokenParameters` a MSAL, `acquireToken` construa uma instância e forneça-a ao método. O pedido simbólico abaixo `default` utiliza a autoridade.

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

## <a name="silently-renew-a-token"></a>Renovar silenciosamente um símbolo

Para adquirir um símbolo silenciosamente com a `AcquireTokenSilentParameters` MSAL, `acquireTokenSilentAsync` construa uma instância e forneça-a ao método. Ao `acquireToken` contrário do `authority` método, o deve ser especificado para adquirir um símbolo silenciosamente.

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

Dado que as políticas em B2C são representadas como autoridades separadas, `fromAuthority` invocar uma `acquireToken` política `acquireTokenSilent` diferente do incumprimento é alcançada especificando uma cláusula na construção ou parâmetros.  Por exemplo:

```java
AcquireTokenParameters parameters = new AcquireTokenParameters.Builder()
    .startAuthorizationFromActivity(activity)
    .withScopes(Arrays.asList("https://contoso.onmicrosoft.com/contosob2c/read")) // Provide your registered scope here
    .withPrompt(Prompt.LOGIN)
    .callback(...) // provide callback here
    .fromAuthority("<url_of_policy_defined_in_configuration_json>")
    .build();
```

## <a name="handle-password-change-policies"></a>Lidar com as políticas de mudança de palavra-passe

O sinal de inscrição ou fluxo de utilizador de entrada de conta local mostra uma**palavra-passe esquecida?** link. Clicar neste link não despoleta automaticamente um fluxo de utilizador de redefinição de palavra-passe.

Em vez disso, o código `AADB2C90118` de erro é devolvido à sua aplicação. A sua aplicação deve lidar com este código de erro executando um fluxo específico do utilizador que redefine a palavra-passe.

Para capturar um código de erro de redefinição `AuthenticationCallback`de palavra-passe, a seguinte implementação pode ser utilizada no interior do seu:

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

## <a name="use-iauthenticationresult"></a>Utilizar iAuthenticationResult

Uma aquisição simbólica bem `IAuthenticationResult` sucedida resulta num objeto. Contém o sinal de acesso, reclamações de utilizadores e metadados.

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

### <a name="idtoken-claims"></a>Reclamações idToken

As reclamações devolvidas no IdToken são povoadas pelo Serviço de Token de Segurança (STS), e não pela MSAL. Dependendo do fornecedor de identidade (IdP) utilizado, algumas reclamações podem estar ausentes. Alguns idPs não fornecem `preferred_username` atualmente a reclamação. Porque esta alegação é usada pela MSAL para `MISSING FROM THE TOKEN RESPONSE`o cache, um valor de espaço reservado, é usada no seu lugar. Para obter mais informações sobre as alegações do IdToken b2C, consulte a [visão geral das fichas no Diretório Ativo Azure B2C](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-tokens#claims).

## <a name="managing-accounts-and-policies"></a>Gestão de contas e políticas

B2C trata cada política como uma autoridade separada. Assim, os tokens de acesso, fichas de atualização e fichas de identificação devolvidas de cada apólice não são permutáveis. Isto significa que cada `IAccount` política devolve um objeto separado cujos tokens não podem ser usados para invocar outras políticas.

Cada apólice `IAccount` adiciona uma cache para cada utilizador. Se um utilizador assinar uma aplicação e invocar duas `IAccount`políticas, terá dois s. Para remover este utilizador da cache, deve solicitar `removeAccount()` cada apólice.

Quando renovar os símbolos para `acquireTokenSilent`uma política `IAccount` com , fornecer o mesmo que `AcquireTokenSilentParameters`foi devolvido de invocações anteriores da política para . O fornecimento de uma conta devolvida por outra política resultará num erro.

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre o Azure Ative Directory B2C (Azure AD B2C) no [What is Azure Ative Directory B2C?](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-overview)
