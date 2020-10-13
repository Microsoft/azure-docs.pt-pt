---
title: Configurar comportamento de sessão usando políticas personalizadas - Azure Ative Directy B2C / Microsoft Docs
description: Configure o comportamento da sessão usando políticas personalizadas no Azure Ative Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 05/07/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 31257d795dbd06da65e3d07e18a16d9bdf7e782a
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/13/2020
ms.locfileid: "91961107"
---
# <a name="configure-session-behavior-using-custom-policies-in-azure-active-directory-b2c"></a>Configurar comportamento de sessão usando políticas personalizadas no Azure Ative Directory B2C

A gestão [de sessão de sessão de sessão de sessão de sessão de sessão de sessão](session-overview.md) em Azure Ative Directory B2C (Azure AD B2C) permite que um administrador controle a interação com um utilizador depois de o utilizador já ter autenticado. Por exemplo, o administrador pode controlar se a seleção de fornecedores de identidade é apresentada ou se os detalhes da conta precisam de ser introduzidos novamente. Este artigo descreve como configurar as definições SSO para Azure AD B2C.

## <a name="session-behavior-properties"></a>Propriedades de comportamento de sessão

Pode utilizar as seguintes propriedades para gerir sessões de aplicações web:

- **Duração da sessão de aplicações web (minutos)** - A vida útil do cookie de sessão do Azure AD B2C armazenado no navegador do utilizador após a autenticação bem sucedida.
  - Predefinição = 86400 segundos (1440 minutos).
  - Mínimo (inclusivo) = 900 segundos (15 minutos).
  - Máximo (inclusivo) = 86400 segundos (1440 minutos).
- **Tempo limite de sessão de aplicações web** - O [tipo de sessão expirado,](session-overview.md#session-expiry-type) *Rolling,* ou *Absoluto*. 
- **Configuração única de entrada** - O âmbito de [sessão](session-overview.md#session-scope) do comportamento de sessão única (SSO) em várias aplicações e fluxos de utilizador no seu inquilino Azure AD B2C. 

## <a name="configure-the-properties"></a>Configure as propriedades

Para alterar o comportamento da sessão e as configurações SSO, adicione um elemento **UserJourneyBehaviors** dentro do elemento [RelyingParty.](relyingparty.md)  O elemento **UserJourneyBehaviors** deve seguir imediatamente o **DefaultUserJourney**. O elemento **UserJourneyBehavors** deve parecer-se com este exemplo:

```xml
<UserJourneyBehaviors>
   <SingleSignOn Scope="Application" />
   <SessionExpiryType>Absolute</SessionExpiryType>
   <SessionExpiryInSeconds>86400</SessionExpiryInSeconds>
</UserJourneyBehaviors>
```

## <a name="configure-sign-out-behavior"></a>Configurar o comportamento de assinatura

### <a name="secure-your-logout-redirect"></a>Fixe o seu redirecionamento de logout

Após o logout, o utilizador é redirecionado para o URI especificado no `post_logout_redirect_uri` parâmetro, independentemente dos URLs de resposta especificados para a aplicação. No entanto, se um válido `id_token_hint` for aprovado e o **Token de ID requere em pedidos de logout** é ligado, Azure AD B2C verifica que o valor da `post_logout_redirect_uri` correspondência corresponde a um dos URIs de redirecionamento configurados da aplicação antes de efetuar o redirecionamento. Se não tiver sido configurado um URL de resposta correspondente para a aplicação, é apresentada uma mensagem de erro e o utilizador não é redirecionado. 

Para exigir um Token de ID em pedidos de logout, adicione um elemento **UserJourneyBehaviors** dentro do elemento [RelyingParty.](relyingparty.md) Em seguida, desloque o **EnforceIdTokenHintOnLogout** do elemento **SingleSignOn** para `true` . O elemento **UserJourneyBehaviors** deve parecer-se com este exemplo:

```xml
<UserJourneyBehaviors>
  <SingleSignOn Scope="Tenant" EnforceIdTokenHintOnLogout="true"/>
</UserJourneyBehaviors>
```

Para configurar a sua aplicação Logout URL:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Certifique-se de que está a utilizar o diretório que contém o seu inquilino Azure AD B2C selecionando o filtro **de subscrição Diretório +** no menu superior e escolhendo o diretório que contém o seu inquilino Azure AD B2C.
1. Escolha **todos os serviços** no canto superior esquerdo do portal Azure e, em seguida, procure e selecione **Azure AD B2C**.
1. Selecione **as inscrições da App**e, em seguida, selecione a sua aplicação.
1. Selecione **Autenticação**.
1. Na caixa de texto **URL logout,** digite o seu logout post redirecione URI e, em seguida, selecione **Save**.

### <a name="single-sign-out"></a>Fim de sessão único

#### <a name="configure-the-applications"></a>Configurar as aplicações

Quando redireciona o utilizador para o ponto final de assinatura Azure AD B2C (tanto para os protocolos OAuth2 como PARA SAML), o Azure AD B2C limpa a sessão do utilizador do navegador.  Para permitir a [assinatura única,](session-overview.md#single-sign-out)descreva `LogoutUrl` a aplicação a partir do portal Azure:

1. Navegue até ao [portal Azure.](https://portal.azure.com)
1. Escolha o seu diretório Azure AD B2C clicando na sua conta no canto superior direito da página.
1. No menu esquerdo, escolha **Azure AD B2C,** selecione **as inscrições da App**e, em seguida, selecione a sua aplicação.
1. Selecione **Autenticação**.
1. Na caixa de texto **URL logout,** digite o seu logout post redirecione URI e, em seguida, selecione **Save**.

#### <a name="configure-the-token-issuer"></a>Configure o emitente simbólico 

Para apoiar uma única assinatura, os perfis técnicos do emitente simbólico para o JWT e para a SAML devem especificar:

- O nome do protocolo, como `<Protocol Name="OpenIdConnect" />`
- A referência ao perfil técnico da sessão, tal como `UseTechnicalProfileForSessionManagement ReferenceId="SM-OAuth-issuer" />` .

O exemplo a seguir ilustra os emitentes JWT e SAML com uma única súmia:

```xml
<ClaimsProvider>
  <DisplayName>Local Account SignIn</DisplayName>
  <TechnicalProfiles>
    <!-- JWT Token Issuer -->
    <TechnicalProfile Id="JwtIssuer">
      <DisplayName>JWT token Issuer</DisplayName>
      <Protocol Name="OpenIdConnect" />
      <OutputTokenFormat>JWT</OutputTokenFormat>
      ...    
      <UseTechnicalProfileForSessionManagement ReferenceId="SM-OAuth-issuer" />
    </TechnicalProfile>

    <!-- Session management technical profile for OIDC based tokens -->
    <TechnicalProfile Id="SM-OAuth-issuer">
      <DisplayName>Session Management Provider</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.OAuthSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
    </TechnicalProfile>

    <!--SAML token issuer-->
    <TechnicalProfile Id="Saml2AssertionIssuer">
      <DisplayName>SAML token issuer</DisplayName>
      <Protocol Name="SAML2" />
      <OutputTokenFormat>SAML2</OutputTokenFormat>
      ...
      <UseTechnicalProfileForSessionManagement ReferenceId="SM-Saml-issuer" />
    </TechnicalProfile>

    <!-- Session management technical profile for SAML based tokens -->
    <TechnicalProfile Id="SM-Saml-issuer">
      <DisplayName>Session Management Provider</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.SamlSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre [a sessão Azure AD B2C](session-overview.md).
