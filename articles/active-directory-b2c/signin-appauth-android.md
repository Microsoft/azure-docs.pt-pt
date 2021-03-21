---
title: Adquira um token numa aplicação Android
titleSuffix: Azure AD B2C
description: Como criar uma aplicação Android que utilize o AppAuth com o Azure Ative Directory B2C para gerir as identidades dos utilizadores e autenticar os utilizadores.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 05/12/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: acbd2918bd311cec1c27018763ad10771d779d85
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "94953326"
---
# <a name="sign-in-using-an-android-application-in-azure-active-directory-b2c"></a>Iniciar sação usando uma aplicação Android em Azure Ative Directory B2C

A plataforma de identidade da Microsoft utiliza as normas de abertura, como o OAuth2 e o OpenID Connect. Estas normas permitem tirar partido de qualquer biblioteca que queira integrar com o Azure Active Directory B2C. Para ajudá-lo a usar outras bibliotecas, pode usar uma passagem como esta para demonstrar como configurar bibliotecas de terceiros para se conectar à plataforma de identidade da Microsoft. A maioria das bibliotecas que [implementam a especificação OAuth2 RFC6749](https://tools.ietf.org/html/rfc6749) pode ligar-se à plataforma Microsoft Identity.

> [!WARNING]
> A Microsoft não fornece correções para bibliotecas de terceiros e não fez uma revisão dessas bibliotecas. Esta amostra está a usar uma biblioteca de terceiros chamada AppAuth que foi testada para compatibilidade em cenários básicos com o Azure AD B2C. As questões e os pedidos de funcionalidade devem ser direcionados para o projeto de código aberto da biblioteca. Consulte [este artigo](../active-directory/develop/reference-v2-libraries.md) para mais informações.
>
>

Se não estiver familiarizado com o OAuth2 ou o OpenID Connect, este exemplo de configuração pode não fazer muito sentido para si. Recomendamos que leia a breve [descrição geral do protocolo aqui documentado](protocols-overview.md).

## <a name="get-an-azure-ad-b2c-directory"></a>Obter um diretório do Azure AD B2C

Para poder utilizar o Azure AD B2C, tem de criar um diretório ou inquilino. Um diretório é um contentor para todos os seus utilizadores, aplicações, grupos, etc. Se ainda não tiver um, [crie um diretório do B2C](tutorial-create-tenant.md) antes de continuar.

## <a name="create-an-application"></a>Criar uma aplicação

Em seguida, registe um requerimento no seu inquilino Azure AD B2C. Isto dá ao Azure AD a informação de que necessita para comunicar de forma segura com a sua aplicação.

[!INCLUDE [active-directory-b2c-appreg-native](../../includes/active-directory-b2c-appreg-native.md)]

Grave o **ID da Aplicação (cliente)** para utilização num passo posterior.

Também grave o seu URI de redirecionamento personalizado para utilização num passo posterior. Por exemplo, `com.onmicrosoft.contosob2c.exampleapp://oauth/redirect`.

## <a name="create-your-user-flows"></a>Crie os fluxos do seu utilizador

No Azure AD B2C, cada experiência de utilizador é definida por um fluxo de [utilizador](user-flow-overview.md), que é um conjunto de políticas que controlam o comportamento do Azure AD. Esta aplicação requer um fluxo de utilizador de entrada e inscrição. Quando criar o fluxo do utilizador, certifique-se de:

* Escolha o **nome 'Visor'** como um atributo de inscrição no fluxo do utilizador.
* Escolha as reclamações do **nome do Visor** e **do ID do objeto** em todos os fluxos do utilizador. Também pode escolher outras afirmações.
* Copie o **nome** de cada fluxo de utilizador depois de o criar. Deve ter o prefixo `b2c_1_`.  Mais tarde, vai precisar do nome de fluxo do utilizador.

Depois de criar os fluxos do utilizador, está pronto para construir a sua aplicação.

## <a name="download-the-sample-code"></a>Descarregue o código de amostra

Fornecemos uma amostra de trabalho que utiliza o AppAuth com Azure AD B2C [no GitHub.](https://github.com/Azure-Samples/active-directory-android-native-appauth-b2c) Pode descarregar o código e executá-lo. Pode começar rapidamente com a sua própria aplicação utilizando a sua própria configuração Azure AD B2C seguindo as instruções do [README.md](https://github.com/Azure-Samples/active-directory-android-native-appauth-b2c/blob/master/README.md).

A amostra é uma modificação da amostra fornecida pela [AppAuth](https://openid.github.io/AppAuth-Android/). Visite a sua página para saber mais sobre o AppAuth e as suas funcionalidades.

## <a name="modifying-your-app-to-use-azure-ad-b2c-with-appauth"></a>Modificar a sua app para utilizar o Azure AD B2C com o AppAuth

> [!NOTE]
> AppAuth suporta Android API 16 (Jellybean) e acima. Recomendamos a utilização da API 23 ou superior.
>

### <a name="configuration"></a>Configuração

Pode configurar a comunicação com o Azure AD B2C especificando a descoberta URI ou especificando tanto o ponto final de autorização como os URIs do ponto final simbólico. Em qualquer dos casos, necessitará das seguintes informações:

* ID do inquilino (por exemplo, contoso.onmicrosoft.com)
* Nome de fluxo do utilizador (por exemplo, B2C \_ 1 \_ SignUpIn)

Se optar por descobrir automaticamente a autorização e as URIs de ponto final simbólico, terá de obter informações a partir do URI de descoberta. A descoberta URI pode ser gerada substituindo o `<tenant-id>` e o no seguinte `<policy-name>` URL:

```java
String mDiscoveryURI = "https://<tenant-name>.b2clogin.com/<tenant-id>/<policy-name>/v2.0/.well-known/openid-configuration";
```

Em seguida, pode adquirir a autorização e os URIs de ponto final simbólico e criar um objeto de Confirmação de Serviço de Autorização, executando o seguinte:

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

Em vez de utilizar a descoberta para obter a autorização e as URIs de ponto final simbólico, também pode especirá-las explicitamente substituindo os `<tenant-id>` `<policy-name>` URLs e os URLs abaixo:

```java
String mAuthEndpoint = "https://<tenant-name>.b2clogin.com/<tenant-id>/<policy-name>/oauth2/v2.0/authorize";

String mTokenEndpoint = "https://<tenant-name>.b2clogin.com/<tenant-id>/<policy-name>/oauth2/v2.0/token";
```

Execute o seguinte código para criar o seu objeto de Configuração do Serviço de Autorização:

```java
AuthorizationServiceConfiguration config =
        new AuthorizationServiceConfiguration(name, mAuthEndpoint, mTokenEndpoint);

// perform the auth request...
```

### <a name="authorizing"></a>Autorizar

Após configurar ou recuperar uma configuração do serviço de autorização, pode ser construído um pedido de autorização. Para criar o pedido, necessitará das seguintes informações:

* ID do cliente (ID de aplicação) que gravou anteriormente. Por exemplo, `00000000-0000-0000-0000-000000000000`.
* Redirecionar o URI personalizado que gravou anteriormente. Por exemplo, `com.onmicrosoft.contosob2c.exampleapp://oauth/redirect`.

Ambos os itens deveriam ter sido guardados quando estava [a registar a sua aplicação.](#create-an-application)

```java
AuthorizationRequest req = new AuthorizationRequest.Builder(
    config,
    clientId,
    ResponseTypeValues.CODE,
    redirectUri)
    .build();
```

Consulte o [guia appAuth](https://openid.github.io/AppAuth-Android/) sobre como completar o resto do processo. Se precisa de começar rapidamente com uma aplicação de trabalho, consulte a [nossa amostra.](https://github.com/Azure-Samples/active-directory-android-native-appauth-b2c) Siga os passos na [README.md](https://github.com/Azure-Samples/active-directory-android-native-appauth-b2c/blob/master/README.md) para introduzir a sua própria configuração Azure AD B2C.