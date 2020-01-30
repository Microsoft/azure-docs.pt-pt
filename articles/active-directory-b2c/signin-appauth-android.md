---
title: Adquirir um símbolo numa aplicação Android
titleSuffix: Azure AD B2C
description: Como criar uma aplicação Android que utiliza o AppAuth com o Azure Ative Directory B2C para gerir as identidades dos utilizadores e autenticar os utilizadores.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: aa8731329c141ab1390510180233ff93e606ef94
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/29/2020
ms.locfileid: "76848411"
---
# <a name="sign-in-using-an-android-application-in-azure-active-directory-b2c"></a>Iniciar sessão utilizando uma aplicação Android no Diretório Ativo Azure B2C

A plataforma de identidade da Microsoft utiliza as normas de abertura, como o OAuth2 e o OpenID Connect. Estes padrões permitem-lhe alavancar qualquer biblioteca que deseje integrar com o Azure Ative Directory B2C. Para ajudá-lo a usar outras bibliotecas, pode usar um walkthrough como este para demonstrar como configurar bibliotecas de terceiros para se conectar à plataforma de identidade da Microsoft. A maioria das bibliotecas que implementam [a especificação RFC6749 OAuth2](https://tools.ietf.org/html/rfc6749) pode ligar-se à plataforma Microsoft Identity.

> [!WARNING]
> A Microsoft não fornece correções para bibliotecas de terceiros e não fez uma revisão dessas bibliotecas. Esta amostra está a usar uma biblioteca de terceiros chamada AppAuth que foi testada para compatibilidade em cenários básicos com o Azure AD B2C. As questões e pedidos de funcionalidades devem ser direcionados para o projeto de código aberto da biblioteca. Por favor, consulte [este artigo](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-libraries) para mais informações.
>
>

Se não estiver familiarizado com o OAuth2 ou o OpenID Connect, este exemplo de configuração pode não fazer muito sentido para si. Recomendamos que leia a breve [descrição geral do protocolo aqui documentado](protocols-overview.md).

## <a name="get-an-azure-ad-b2c-directory"></a>Obter um diretório do Azure AD B2C

Para poder utilizar o Azure AD B2C, tem de criar um diretório ou inquilino. Um diretório é um contentor para todos os seus utilizadores, aplicações, grupos, etc. Se ainda não tiver um, [crie um diretório do B2C](tutorial-create-tenant.md) antes de continuar.

## <a name="create-an-application"></a>Criar uma aplicação

Em seguida, registe uma inscrição no seu inquilino Azure AD B2C. Isto dá ao Azure AD a informação de que necessita para comunicar de forma segura com a sua aplicação.

[!INCLUDE [active-directory-b2c-appreg-native](../../includes/active-directory-b2c-appreg-native.md)]

Registre a **ID do aplicativo (cliente)** para uso em uma etapa posterior.

Também grave o seu URI personalizado para utilização num passo posterior. Por exemplo, `com.onmicrosoft.contosob2c.exampleapp://oauth/redirect`.

## <a name="create-your-user-flows"></a>Crie os fluxos de utilizador

No Azure AD B2C, cada experiência do utilizador é definida por um fluxo de [utilizador](user-flow-overview.md), que é um conjunto de políticas que controlam o comportamento do Azure AD. Esta aplicação requer um fluxo de utilizador de entrada e inscrição. Quando criar o fluxo do utilizador, certifique-se de:

* Escolha o **nome Display** como um atributo de inscrição no fluxo do utilizador.
* Escolha o **nome display** e as reclamações da aplicação **Object ID** em cada fluxo do utilizador. Também pode escolher outras afirmações.
* Copie o **nome** de cada fluxo de cada utilizador depois de o criar. Deve ter o prefixo `b2c_1_`.  Vai precisar do nome de fluxo do utilizador mais tarde.

Depois de ter criado os fluxos de utilizador, está pronto para construir a sua aplicação.

## <a name="download-the-sample-code"></a>Descarregue o código da amostra

Fornecemos uma amostra de trabalho que utiliza appAuth com Azure AD B2C [no GitHub](https://github.com/Azure-Samples/active-directory-android-native-appauth-b2c). Pode descarregar o código e executá-lo. Pode começar rapidamente com a sua própria app utilizando a sua própria configuração Azure AD AD B2C seguindo as instruções no [README.md](https://github.com/Azure-Samples/active-directory-android-native-appauth-b2c/blob/master/README.md).

A amostra é uma modificação da amostra fornecida pela [AppAuth](https://openid.github.io/AppAuth-Android/). Visite a sua página para saber mais sobre o AppAuth e as suas funcionalidades.

## <a name="modifying-your-app-to-use-azure-ad-b2c-with-appauth"></a>Modificar a sua aplicação para utilizar o Azure AD B2C com appAuth

> [!NOTE]
> AppAuth suporta Android API 16 (Jellybean) e acima. Recomendamos a utilização de API 23 ou superior.
>

### <a name="configuration"></a>Configuração

Pode configurar a comunicação com o Azure AD B2C especificando a descoberta URI ou especificando tanto o ponto final de autorização como os URIs finais simbólicos. Em qualquer dos casos, necessitará das seguintes informações:

* ID do inquilino (por exemplo, contoso.onmicrosoft.com)
* Nome de fluxo do utilizador (por exemplo, B2C\_1\_SignUpIn)

Se optar por descobrir automaticamente a autorização e o token endpoint URIs, terá de recolher informações a partir da descoberta URI. A descoberta URI pode ser gerada substituindo o Inquilino\_ID e o Nome\_Política no seguinte URL:

```java
String mDiscoveryURI = "https://<Tenant_name>.b2clogin.com/<Tenant_ID>/v2.0/.well-known/openid-configuration?p=<Policy_Name>";
```

Em seguida, pode adquirir a autorização e simbólica urIs endpoint e criar um objeto de Configuração de Serviço de Autorização executando o seguinte:

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

Em vez de utilizar a descoberta para obter a autorização e o token endpoint URIs, também pode especificá-los explicitamente substituindo o Id\_inquilino e a política\_Nome no URL abaixo:

```java
String mAuthEndpoint = "https://<Tenant_name>.b2clogin.com/<Tenant_ID>/oauth2/v2.0/authorize?p=<Policy_Name>";

String mTokenEndpoint = "https://<Tenant_name>.b2clogin.com/<Tenant_ID>/oauth2/v2.0/token?p=<Policy_Name>";
```

Executar o seguinte código para criar o seu objeto de Configuração de Serviçode Autorização:

```java
AuthorizationServiceConfiguration config =
        new AuthorizationServiceConfiguration(name, mAuthEndpoint, mTokenEndpoint);

// perform the auth request...
```

### <a name="authorizing"></a>Autorizar

Depois de configurar ou recuperar uma configuração de serviço de autorização, pode ser construído um pedido de autorização. Para criar o pedido, necessitará das seguintes informações:

* ID do cliente (ID de aplicação) que gravou anteriormente. Por exemplo, `00000000-0000-0000-0000-000000000000`.
* Redirecione o URI personalizado que gravou anteriormente. Por exemplo, `com.onmicrosoft.contosob2c.exampleapp://oauth/redirect`.

Ambos os itens deveriam ter sido guardados quando estava [a registar a sua aplicação](#create-an-application).

```java
AuthorizationRequest req = new AuthorizationRequest.Builder(
    config,
    clientId,
    ResponseTypeValues.CODE,
    redirectUri)
    .build();
```

Consulte o [guia AppAuth](https://openid.github.io/AppAuth-Android/) sobre como completar o resto do processo. Se precisar de começar rapidamente com uma aplicação de trabalho, confira a [nossa amostra.](https://github.com/Azure-Samples/active-directory-android-native-appauth-b2c) Siga os passos do [README.md](https://github.com/Azure-Samples/active-directory-android-native-appauth-b2c/blob/master/README.md) para introduzir a sua própria configuração Azure AD AD B2C.
