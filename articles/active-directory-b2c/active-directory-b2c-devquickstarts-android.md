---
title: Adquirindo um token usando um aplicativo Android no Azure Active Directory B2C | Microsoft Docs
description: Este artigo mostrará como criar um aplicativo Android que usa o AppAuth com o Azure Active Directory B2C para gerenciar identidades de usuário e autenticar usuários.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 28b1c3622ca449b0ce539937369fe43bd1d508ee
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73468958"
---
# <a name="sign-in-using-an-android-application-in-azure-active-directory-b2c"></a>Entrar usando um aplicativo Android no Azure Active Directory B2C

A plataforma de identidade da Microsoft utiliza as normas de abertura, como o OAuth2 e o OpenID Connect. Esses padrões permitem que você aproveite qualquer biblioteca que deseja integrar com Azure Active Directory B2C. Para ajudá-lo a usar outras bibliotecas, você pode usar uma explicação como esta para demonstrar como configurar bibliotecas de terceiros para se conectar à plataforma de identidade da Microsoft. A maioria das bibliotecas que implementam [a especificação especificação rfc6749 OAuth2](https://tools.ietf.org/html/rfc6749) pode se conectar à plataforma Microsoft Identity.

> [!WARNING]
> A Microsoft não fornece correções para bibliotecas de terceiros e não fez uma análise dessas bibliotecas. Este exemplo está usando uma biblioteca de terceiros chamada AppAuth que foi testada quanto à compatibilidade em cenários básicos com o Azure AD B2C. Problemas e solicitações de recursos devem ser direcionados para o projeto de código-fonte aberto da biblioteca. Consulte [Este artigo](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-libraries) para obter mais informações.
>
>

Se não estiver familiarizado com o OAuth2 ou o OpenID Connect, este exemplo de configuração pode não fazer muito sentido para si. Recomendamos que leia a breve [descrição geral do protocolo aqui documentado](active-directory-b2c-reference-protocols.md).

## <a name="get-an-azure-ad-b2c-directory"></a>Obter um diretório do Azure AD B2C

Para poder utilizar o Azure AD B2C, tem de criar um diretório ou inquilino. Um diretório é um contentor para todos os seus utilizadores, aplicações, grupos, etc. Se ainda não tiver um, [crie um diretório do B2C](tutorial-create-tenant.md) antes de continuar.

## <a name="create-an-application"></a>Criar uma aplicação

Em seguida, registre um aplicativo em seu locatário de Azure AD B2C. Isso dá ao Azure AD as informações de que ele precisa para se comunicar de forma segura com seu aplicativo.

[!INCLUDE [active-directory-b2c-appreg-native](../../includes/active-directory-b2c-appreg-native.md)]

Registre a **ID do aplicativo (cliente)** para uso em uma etapa posterior.

Registre também seu URI de redirecionamento personalizado para uso em uma etapa posterior. Por exemplo, `com.onmicrosoft.contosob2c.exampleapp://oauth/redirect`.

## <a name="create-your-user-flows"></a>Criar seus fluxos de usuário

No Azure AD B2C, cada experiência de usuário é definida por um [fluxo de usuário](active-directory-b2c-reference-policies.md), que é um conjunto de políticas que controlam o comportamento do Azure AD. Este aplicativo requer um fluxo de usuário de entrada e de inscrição. Ao criar o fluxo de usuário, certifique-se de:

* Escolha o **nome de exibição** como um atributo de inscrição em seu fluxo de usuário.
* Escolha as declarações de aplicativo **nome de exibição** e **ID de objeto** em cada fluxo de usuário. Também pode escolher outras afirmações.
* Copie o **nome** de cada fluxo de usuário depois de criá-lo. Deve ter o prefixo `b2c_1_`.  Você precisará do nome do fluxo do usuário mais tarde.

Depois de criar seus fluxos de usuário, você estará pronto para compilar seu aplicativo.

## <a name="download-the-sample-code"></a>Baixar o código de exemplo

Fornecemos um exemplo funcional que usa AppAuth com Azure AD B2C [no GitHub](https://github.com/Azure-Samples/active-directory-android-native-appauth-b2c). Você pode baixar o código e executá-lo. Você pode começar rapidamente com seu próprio aplicativo usando sua própria configuração de Azure AD B2C seguindo as instruções no [README.MD](https://github.com/Azure-Samples/active-directory-android-native-appauth-b2c/blob/master/README.md).

O exemplo é uma modificação do exemplo fornecido pelo [APPAUTH](https://openid.github.io/AppAuth-Android/). Visite sua página para saber mais sobre o AppAuth e seus recursos.

## <a name="modifying-your-app-to-use-azure-ad-b2c-with-appauth"></a>Modificando seu aplicativo para usar o Azure AD B2C com AppAuth

> [!NOTE]
> O AppAuth dá suporte à API do Android 16 (Jellybean) e superior. É recomendável usar a API 23 e superior.
>

### <a name="configuration"></a>Configuração

Você pode configurar a comunicação com Azure AD B2C especificando o URI de descoberta ou especificando o ponto de extremidade de autorização e URIs de ponto de extremidade de token. Em ambos os casos, você precisará das seguintes informações:

* ID do locatário (por exemplo, contoso.onmicrosoft.com)
* Nome do fluxo do usuário (por exemplo, B2C\_1\_inscrição)

Se você optar por descobrir automaticamente a autorização e URIs de ponto de extremidade de token, será necessário buscar informações do URI de descoberta. O URI de descoberta pode ser gerado substituindo a ID de\_do locatário e o nome da\_de política na seguinte URL:

```java
String mDiscoveryURI = "https://<Tenant_name>.b2clogin.com/<Tenant_ID>/v2.0/.well-known/openid-configuration?p=<Policy_Name>";
```

Você pode adquirir os URIs de ponto de extremidade de token e autorização e criar um objeto AuthorizationServiceConfiguration executando o seguinte:

```java
final Uri issuerUri = Uri.parse(mDiscoveryURI);
AuthorizationServiceConfiguration config;

AuthorizationServiceConfiguration.fetchFromIssuer(
    issuerUri,
    new RetrieveConfigurationCallback() {
      @Override public void onFetchConfigurationCompleted(
          @Nullable AuthorizationServiceConfiguration serviceConfiguration,
          @Nullable AuthorizationException ex) {
        if (ex != null) {
            Log.w(TAG, "Failed to retrieve configuration for " + issuerUri, ex);
        } else {
            // service configuration retrieved, proceed to authorization...
        }
      }
  });
```

Em vez de usar a descoberta para obter os URIs de ponto de extremidade de autorização e de token, você também pode especificá-los explicitamente substituindo a ID de\_do locatário e o nome da\_de política na URL abaixo:

```java
String mAuthEndpoint = "https://<Tenant_name>.b2clogin.com/<Tenant_ID>/oauth2/v2.0/authorize?p=<Policy_Name>";

String mTokenEndpoint = "https://<Tenant_name>.b2clogin.com/<Tenant_ID>/oauth2/v2.0/token?p=<Policy_Name>";
```

Execute o seguinte código para criar o objeto AuthorizationServiceConfiguration:

```java
AuthorizationServiceConfiguration config =
        new AuthorizationServiceConfiguration(name, mAuthEndpoint, mTokenEndpoint);

// perform the auth request...
```

### <a name="authorizing"></a>Autorizando

Depois de configurar ou recuperar uma configuração de serviço de autorização, uma solicitação de autorização pode ser construída. Para criar a solicitação, você precisará das seguintes informações:

* ID do cliente (ID do aplicativo) que você registrou anteriormente. Por exemplo, `00000000-0000-0000-0000-000000000000`.
* URI de redirecionamento personalizado que você registrou anteriormente. Por exemplo, `com.onmicrosoft.contosob2c.exampleapp://oauth/redirect`.

Ambos os itens devem ter sido salvos quando você estava [registrando seu aplicativo](#create-an-application).

```java
AuthorizationRequest req = new AuthorizationRequest.Builder(
    config,
    clientId,
    ResponseTypeValues.CODE,
    redirectUri)
    .build();
```

Consulte o guia de [APPAUTH](https://openid.github.io/AppAuth-Android/) sobre como concluir o restante do processo. Se você precisar começar rapidamente com um aplicativo em funcionamento, confira [nosso exemplo](https://github.com/Azure-Samples/active-directory-android-native-appauth-b2c). Siga as etapas em [README.MD](https://github.com/Azure-Samples/active-directory-android-native-appauth-b2c/blob/master/README.md) para inserir sua própria configuração de Azure ad B2C.
