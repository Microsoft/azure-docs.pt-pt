---
title: Adquirir um token com uma aplicação Android no Azure Active Directory B2C | Documentos da Microsoft
description: Este artigo irá mostrar como criar uma aplicação Android que utiliza AppAuth com o Azure Active Directory B2C para gerir identidades de utilizador e autenticar utilizadores.
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 3aa2969f8373ae935b0905160f7ad1b57ce17f01
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60317943"
---
# <a name="sign-in-using-an-android-application-in-azure-active-directory-b2c"></a>Inicie sessão com uma aplicação Android no Azure Active Directory B2C

A plataforma de identidade da Microsoft utiliza as normas de abertura, como o OAuth2 e o OpenID Connect. Esses padrões permitem-lhe tirar partido de qualquer biblioteca que pretende integrar com o Azure Active Directory B2C. Para ajudar a utilizar outras bibliotecas, pode utilizar um passo a passo como esta para demonstrar como configurar o 3º bibliotecas de terceiros para ligar à plataforma de identidade da Microsoft. A maioria das bibliotecas que implementam [especificação do especificação RFC6749 OAuth2](https://tools.ietf.org/html/rfc6749) pode ligar-se para a plataforma do Microsoft Identity.

> [!WARNING]
> A Microsoft não fornece correções para 3rd bibliotecas de terceiros e não fez uma revisão dessas bibliotecas. Este exemplo está a utilizar uma biblioteca de terceiros 3º chamada AppAuth que foi testado quanto à compatibilidade em cenários básicos com o Azure AD B2C. Problemas e pedidos de funcionalidades devem ser direcionados para o projeto de código aberto da biblioteca. Veja [este artigo](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-libraries) para obter mais informações.  
>
>

Se não estiver familiarizado com o OAuth2 ou o OpenID Connect, este exemplo de configuração pode não fazer muito sentido para si. Recomendamos que leia a breve [descrição geral do protocolo aqui documentado](active-directory-b2c-reference-protocols.md).

## <a name="get-an-azure-ad-b2c-directory"></a>Obter um diretório do Azure AD B2C

Para poder utilizar o Azure AD B2C, tem de criar um diretório ou inquilino. Um diretório é um contentor para todos os seus utilizadores, aplicações, grupos, etc. Se ainda não tiver um, [crie um diretório do B2C](tutorial-create-tenant.md) antes de continuar.

## <a name="create-an-application"></a>Criar uma aplicação

Em seguida, precisa de criar uma aplicação no diretório do B2C. Isto proporciona informações do Azure AD de que necessita para comunicar de forma segura com a sua aplicação. Para criar uma aplicação móvel, siga [estas instruções](active-directory-b2c-app-registration.md). É necessário:

* Incluir uma **Native Client** no aplicativo.
* Copiar a **ID da Aplicação** atribuída à aplicação. Irá precisar mais tarde.
* Configurar um cliente nativo **URI de redirecionamento** (por exemplo, com.onmicrosoft.fabrikamb2c.exampleapp://oauth/redirect). Também irá precisar deste mais tarde.

## <a name="create-your-user-flows"></a>Criar os seus fluxos de utilizador

No Azure AD B2C, cada experiência de utilizador é definida por um [fluxo de utilizador](active-directory-b2c-reference-policies.md), que é um conjunto de políticas que controlam o comportamento do Azure AD. Esta aplicação requer um fluxo de utilizador de início de sessão e inscrição. Quando cria o fluxo de utilizador, certifique-se de que para:

* Escolha o **nome a apresentar** como um atributo Inscreva-se no seu fluxo de utilizador.
* Escolha o **nome a apresentar** e **ID de objeto** afirmações de aplicação em cada fluxo de utilizador. Também pode escolher outras afirmações.
* Copiar o **nome** de cada fluxo de utilizador depois de o criar. Deve ter o prefixo `b2c_1_`.  Terá o nome do fluxo de utilizador mais tarde.

Depois de criar os fluxos de utilizador, está pronto para criar a sua aplicação.

## <a name="download-the-sample-code"></a>Baixe o código de exemplo

Fornecemos um exemplo funcional que utiliza AppAuth com o Azure AD B2C [no GitHub](https://github.com/Azure-Samples/active-directory-android-native-appauth-b2c). Pode baixar o código e executá-lo. Pode iniciar rapidamente a sua própria aplicação com a sua própria configuração do Azure AD B2C, seguindo as instruções no [README.md](https://github.com/Azure-Samples/active-directory-android-native-appauth-b2c/blob/master/README.md).

O exemplo é uma modificação do exemplo fornecida pela [AppAuth](https://openid.github.io/AppAuth-Android/). Visite a página para saber mais sobre AppAuth e as respetivas funcionalidades.

## <a name="modifying-your-app-to-use-azure-ad-b2c-with-appauth"></a>Modificar a sua aplicação para utilizar o Azure AD B2C com AppAuth

> [!NOTE]
> AppAuth suporta Android API 16 (Jellybean) e superior. Recomendamos que utilize a API 23 e acima.
>

### <a name="configuration"></a>Configuração

Pode configurar a comunicação com o Azure AD B2C, especificando a URI de deteção ou especificando o ponto final de autorização e o ponto final do token URIs. Em ambos os casos, terá as seguintes informações:

* ID do inquilino (por exemplo, contoso.onmicrosoft.com)
* Nome do fluxo de utilizador (por exemplo, B2C\_1\_SignUpIn)

Se optar por detetar automaticamente a autorização e o ponto final do token URIs, terá de obter informações de deteção de URI. A deteção de URI pode ser gerado, substituindo o inquilino\_ID e a política de\_nome no URL seguinte:

```java
String mDiscoveryURI = "https://<Tenant_name>.b2clogin.com/<Tenant_ID>/v2.0/.well-known/openid-configuration?p=<Policy_Name>";
```

Em seguida, pode adquirir a autorização e o ponto final do token URIs e criar um objeto de AuthorizationServiceConfiguration executando o seguinte:

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

Em vez de utilizar a deteção para obter a autorização e o ponto final do token URIs, também pode especificá-los explicitamente, substituindo o inquilino\_ID e a política de\_nome o URL abaixo:

```java
String mAuthEndpoint = "https://<Tenant_name>.b2clogin.com/<Tenant_ID>/oauth2/v2.0/authorize?p=<Policy_Name>";

String mTokenEndpoint = "https://<Tenant_name>.b2clogin.com/<Tenant_ID>/oauth2/v2.0/token?p=<Policy_Name>";
```

Execute o seguinte código para criar o seu objeto AuthorizationServiceConfiguration:

```java
AuthorizationServiceConfiguration config =
        new AuthorizationServiceConfiguration(name, mAuthEndpoint, mTokenEndpoint);

// perform the auth request...
```

### <a name="authorizing"></a>A Autorizar

Depois de configurar ou obter uma configuração de serviço de autorização, pode ser construído um pedido de autorização. Para criar o pedido, terá as seguintes informações:

* ID de cliente (por exemplo, 00000000-0000-0000-0000-000000000000)
* URI de redirecionamento com um esquema personalizado (por exemplo, com.onmicrosoft.fabrikamb2c.exampleapp://oauthredirect)

Ambos os itens devem ter sido guardados quando era [registar a sua aplicação](#create-an-application).

```java
AuthorizationRequest req = new AuthorizationRequest.Builder(
    config,
    clientId,
    ResponseTypeValues.CODE,
    redirectUri)
    .build();
```

Consulte a [guia de AppAuth](https://openid.github.io/AppAuth-Android/) sobre como concluir o resto do processo. Se precisar de iniciar rapidamente a uma aplicação de trabalho, veja [nosso exemplo](https://github.com/Azure-Samples/active-directory-android-native-appauth-b2c). Siga os passos a [README.md](https://github.com/Azure-Samples/active-directory-android-native-appauth-b2c/blob/master/README.md) para introduzir a sua própria configuração do Azure AD B2C.

