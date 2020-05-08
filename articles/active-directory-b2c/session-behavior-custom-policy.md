---
title: Configure o comportamento da sessão utilizando políticas personalizadas - Diretório Ativo Azure B2C / Microsoft Docs
description: Configure o comportamento da sessão utilizando políticas personalizadas no Diretório Ativo Azure B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 05/07/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 3f6af5e8e1cfadd302eadfedf189a6710ac4aeca
ms.sourcegitcommit: a6d477eb3cb9faebb15ed1bf7334ed0611c72053
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/08/2020
ms.locfileid: "82966600"
---
# <a name="configure-session-behavior-using-custom-policies-in-azure-active-directory-b2c"></a>Configure o comportamento da sessão utilizando políticas personalizadas no Diretório Ativo Azure B2C

A gestão da sessão de sessão de início de [sessão (SSO)](session-overview.md) no Azure Ative Directory B2C (Azure AD B2C) permite a um administrador controlar a interação com um utilizador depois de o utilizador já ter autenticado. Por exemplo, o administrador pode controlar se a seleção dos fornecedores de identidade é apresentada ou se os detalhes da conta precisam de ser novamente introduzidos. Este artigo descreve como configurar as definições sSO para Azure AD B2C.

## <a name="session-behavior-properties"></a>Propriedades do comportamento da sessão

Pode utilizar as seguintes propriedades para gerir sessões de aplicação web:

- Vida útil da sessão da **aplicação web (minutos)** - A vida útil do cookie de sessão do Azure AD B2C armazenado no navegador do utilizador após a autenticação bem sucedida.
    - Predefinição = 86400 segundos (1440 minutos).
    - Mínimo (inclusivo) = 900 segundos (15 minutos).
    - Máximo (inclusivo) = 86400 segundos (1440 minutos).
- **Timeout** de sessão de aplicação web - O tipo de expiração da [sessão,](session-overview.md#session-expiry-type) *Rolling*, ou *Absolute*. 
- **Configuração de inscrição única** - O âmbito da [sessão](session-overview.md#session-scope) do comportamento de entrada única (SSO) em várias aplicações e fluxos de utilizadores no seu inquilino Azure AD B2C. 

## <a name="configure-the-properties"></a>Configure as propriedades

Para alterar o comportamento da sua sessão e as configurações SSO, adicione um elemento **UserJourneyBehaviors** dentro do elemento [RelyingParty.](relyingparty.md)  O elemento **UserJourneyBehaviors** deve seguir imediatamente a **Viagem**de Utilizador Padrão . O seu elemento **UserJourneyBehavors** deve parecer este exemplo:

```XML
<UserJourneyBehaviors>
   <SingleSignOn Scope="Application" />
   <SessionExpiryType>Absolute</SessionExpiryType>
   <SessionExpiryInSeconds>86400</SessionExpiryInSeconds>
</UserJourneyBehaviors>
```

## <a name="single-sign-out"></a>Fim de sessão único

### <a name="configure-the-applications"></a>Configure as aplicações

Ao redirecionar o utilizador para o ponto final de sinalização Azure AD B2C (para os protocolos OAuth2 e SAML), o Azure AD B2C iliba a sessão do utilizador a partir do navegador.  Para permitir a [inscrição única,](session-overview.md#single-sign-out)desestabeleça a `LogoutUrl` aplicação do portal Azure:

1. Navegue para o [portal Azure.](https://portal.azure.com)
1. Escolha o seu diretório Azure AD AD B2C clicando na sua conta no canto superior direito da página.
1. No menu esquerdo, escolha **Azure AD B2C,** selecione registos de **aplicações**e, em seguida, selecione a sua aplicação.
1. Selecione **Definições,** selecione **Propriedades**e, em seguida, encontre a caixa de texto **URL de Logout.** 

### <a name="configure-the-token-issuer"></a>Configure o emitente simbólico 

Para apoiar a inscrição única, os perfis técnicos do emitente simbólico tanto para a JWT como para a SAML devem especificar:

- O nome do protocolo, como`<Protocol Name="OpenIdConnect" />`
- A referência ao perfil técnico `UseTechnicalProfileForSessionManagement ReferenceId="SM-jwt-issuer" />`da sessão, como .

O exemplo que se segue ilustra os emitentes de token JWT e SAML com uma única inscrição:

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
      <UseTechnicalProfileForSessionManagement ReferenceId="SM-jwt-issuer" />
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

- Saiba mais sobre a [sessão Azure AD B2C.](session-overview.md)