---
title: Solicitar resolvedores em políticas personalizadas
titleSuffix: Azure AD B2C
description: Saiba como usar resolvedores de declarações em uma política personalizada no Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 01/25/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 3370ec8de0fb49b92c0fb4dd429439e293ad1d8b
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/09/2019
ms.locfileid: "74949879"
---
# <a name="about-claim-resolvers-in-azure-active-directory-b2c-custom-policies"></a>Sobre resolvedores de declaração em Azure Active Directory B2C políticas personalizadas

Os resolvedores de declaração em [políticas personalizadas](active-directory-b2c-overview-custom.md) de Azure Active Directory B2C (Azure ad B2C) fornecem informações de contexto sobre uma solicitação de autorização, como o nome da política, ID de correlação de solicitação, idioma da interface do usuário e muito mais.

Para usar um resolvedor de declaração em uma declaração de entrada ou saída, você define uma **declaração**de cadeia de caracteres, sob o elemento [ClaimsSchema](claimsschema.md) e, em seguida, define o **DefaultValue** para o resolvedor de declaração no elemento de declaração de entrada ou saída. Azure AD B2C lê o valor do resolvedor de declaração e usa o valor no perfil técnico.

No exemplo a seguir, um tipo de declaração chamado `correlationId` é definido com um **tipo de dados** de `string`.

```XML
<ClaimType Id="correlationId">
  <DisplayName>correlationId</DisplayName>
  <DataType>string</DataType>
  <UserHelpText>Request correlation Id</UserHelpText>
</ClaimType>
```

No perfil técnico, mapeie o resolvedor de declaração para o tipo de declaração. Azure AD B2C popula o valor do resolvedor de declaração `{Context:CorrelationId}` na `correlationId` de declaração e envia a declaração para o perfil técnico.

```XML
<InputClaim ClaimTypeReferenceId="correlationId" DefaultValue="{Context:CorrelationId}" />
```

## <a name="claim-resolver-types"></a>Tipos de resolvedor de declaração

As seções a seguir listam os resolvedores de declaração disponíveis.

### <a name="culture"></a>Cultura

| Afirmação | Descrição | Exemplo |
| ----- | ----------- | --------|
| {Culture: LanguageName} | O código ISO de duas letras para o idioma. | en |
| {Culture: LCID}   | O LCID do código de idioma. | 1033 |
| {Culture:RegionName} | O código ISO de duas letras para a região. | EUA |
| {Culture: RFC5646} | O código do idioma RFC5646. | en-US |

### <a name="policy"></a>Política

| Afirmação | Descrição | Exemplo |
| ----- | ----------- | --------|
| {Policy:PolicyId} | O nome da política de terceira parte confiável. | B2C_1A_signup_signin |
| {Policy:RelyingPartyTenantId} | A ID de locatário da política de terceira parte confiável. | your-tenant.onmicrosoft.com |
| {Policy:TenantObjectId} | A ID de objeto de locatário da política de terceira parte confiável. | 00000000-0000-0000-0000-000000000000 |
| {Policy:TrustFrameworkTenantId} | A ID de locatário da estrutura de confiança. | your-tenant.onmicrosoft.com |

### <a name="openid-connect"></a>OpenID Connect

| Afirmação | Descrição | Exemplo |
| ----- | ----------- | --------|
| {OIDC:AuthenticationContextReferences} |O parâmetro de cadeia de caracteres de consulta `acr_values`. | N/A |
| {OIDC:ClientId} |O parâmetro de cadeia de caracteres de consulta `client_id`. | 00000000-0000-0000-0000-000000000000 |
| {OIDC: DomainHint} |O parâmetro de cadeia de caracteres de consulta `domain_hint`. | facebook.com |
| {OIDC:LoginHint} |  O parâmetro de cadeia de caracteres de consulta `login_hint`. | someone@contoso.com |
| {OIDC: MaxAge} | O `max_age`. | N/A |
| {OIDC: nonce} |O parâmetro de cadeia de caracteres de consulta `Nonce`. | defaultnonce |
| {OIDC: prompt} | O parâmetro de cadeia de caracteres de consulta `prompt`. | início de sessão |
| {OIDC: Resource} |O parâmetro de cadeia de caracteres de consulta `resource`. | N/A |
| {OIDC: escopo} |O parâmetro de cadeia de caracteres de consulta `scope`. | OpenID |

### <a name="context"></a>Contexto

| Afirmação | Descrição | Exemplo |
| ----- | ----------- | --------|
| {Context:BuildNumber} | A versão do Identity Experience Framework (número da compilação).  | 1.0.507.0 |
| {Context:CorrelationId} | O ID de correlação.  | 00000000-0000-0000-0000-000000000000 |
| {Context:DateTimeInUtc} |A data e hora em UTC.  | 10/10/2018 12:00:00 PM |
| {Context:DeploymentMode} |O modo de implantação de política.  | Produção |
| {Context:IPAddress} | O endereço IP do usuário. | 11.111.111.11 |


### <a name="non-protocol-parameters"></a>Parâmetros que não são de protocolo

Qualquer nome de parâmetro incluído como parte de uma solicitação OIDC ou OAuth2 pode ser mapeado para uma declaração no percurso do usuário. Por exemplo, a solicitação do aplicativo pode incluir um parâmetro de cadeia de caracteres de consulta com um nome de `app_session`, `loyalty_number`ou qualquer cadeia de caracteres de consulta personalizada.

| Afirmação | Descrição | Exemplo |
| ----- | ----------------------- | --------|
| {OAUTH-KV:campaignId} | Um parâmetro de cadeia de caracteres de consulta. | Havaí |
| {OAUTH-KV:app_session} | Um parâmetro de cadeia de caracteres de consulta. | A3C5R |
| {OAUTH-KV: loyalty_number} | Um parâmetro de cadeia de caracteres de consulta. | 1234 |
| {OAUTH-KV: qualquer cadeia de caracteres de consulta personalizada} | Um parâmetro de cadeia de caracteres de consulta. | N/A |

### <a name="oauth2"></a>OAuth2

| Afirmação | Descrição | Exemplo |
| ----- | ----------------------- | --------|
| {oauth2:access_token} | O token de acesso. | N/A |

## <a name="how-to-use-claim-resolvers"></a>Como usar resolvedores de declaração

### <a name="restful-technical-profile"></a>Perfil técnico RESTful

Em um perfil técnico [RESTful](restful-technical-profile.md) , talvez você queira enviar o idioma do usuário, o nome da política, o escopo e a ID do cliente. Com base nessas declarações, a API REST pode executar a lógica de negócios personalizada e, se necessário, gerar uma mensagem de erro localizada.

O exemplo a seguir mostra um perfil técnico RESTful:

```XML
<TechnicalProfile Id="REST">
  <DisplayName>Validate user input data and return loyaltyNumber claim</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ServiceUrl">https://your-app.azurewebsites.net/api/identity</Item>
    <Item Key="AuthenticationType">None</Item>
    <Item Key="SendClaimsIn">Body</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="userLanguage" DefaultValue="{Culture:LCID}" />
    <InputClaim ClaimTypeReferenceId="policyName" DefaultValue="{Policy:PolicyId}" />
    <InputClaim ClaimTypeReferenceId="scope" DefaultValue="{OIDC:scope}" />
    <InputClaim ClaimTypeReferenceId="clientId" DefaultValue="{OIDC:ClientId}" />
  </InputClaims>
  <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
</TechnicalProfile>
```

### <a name="direct-sign-in"></a>Início de sessão direto

Usando resolvedores de declaração, você pode pré-popular o nome de entrada ou a entrada direta para um provedor de identidade social específico, como Facebook, LinkedIn ou um conta Microsoft. Para obter mais informações, consulte [Configurar a entrada direta usando o Azure Active Directory B2C](direct-signin.md).

### <a name="dynamic-ui-customization"></a>Personalização da interface do usuário dinâmica

Azure AD B2C permite passar parâmetros de cadeia de caracteres de consulta para seus pontos de extremidade de definição de conteúdo HTML para que você possa renderizar dinamicamente o conteúdo da página. Por exemplo, você pode alterar a imagem de plano de fundo na página Azure AD B2C inscrição ou entrada com base em um parâmetro personalizado que você passa de seu aplicativo Web ou móvel. Para obter mais informações, consulte [Configurar dinamicamente a interface do usuário usando políticas personalizadas no Azure Active Directory B2C](active-directory-b2c-ui-customization-custom-dynamic.md). Você também pode localizar sua página HTML com base em um parâmetro de idioma ou pode alterar o conteúdo com base na ID do cliente.

O exemplo a seguir passa a cadeia de caracteres de consulta um parâmetro chamado **campaignid** com um valor de `hawaii`, um código de **idioma** `en-US`e o **aplicativo** que representa a ID do cliente:

```XML
<UserJourneyBehaviors>
  <ContentDefinitionParameters>
    <Parameter Name="campaignId">{OAUTH-KV:campaignId}</Parameter>
    <Parameter Name="language">{Culture:RFC5646}</Parameter>
    <Parameter Name="app">{OIDC:ClientId}</Parameter>
  </ContentDefinitionParameters>
</UserJourneyBehaviors>
```

Como resultado Azure AD B2C envia os parâmetros acima para a página de conteúdo HTML:

```
/selfAsserted.aspx?campaignId=hawaii&language=en-US&app=0239a9cc-309c-4d41-87f1-31288feb2e82
```

### <a name="application-insights-technical-profile"></a>Application Insights o perfil técnico

Com as informações de Aplicativo Azure e os resolvedores de declaração, você pode obter informações sobre o comportamento do usuário. No perfil técnico do Application Insights, você envia declarações de entrada que são persistidas para Aplicativo Azure insights. Para obter mais informações, consulte [acompanhar o comportamento do usuário em Azure ad B2C jornadas usando Application insights](active-directory-b2c-custom-guide-eventlogger-appins.md). O exemplo a seguir envia a ID da política, a ID de correlação, o idioma e a ID do cliente para Aplicativo Azure insights.

```XML
<TechnicalProfile Id="AzureInsights-Common">
  <DisplayName>Alternate Email</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.Insights.AzureApplicationInsightsProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  ...
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="PolicyId" PartnerClaimType="{property:Policy}" DefaultValue="{Policy:PolicyId}" />
    <InputClaim ClaimTypeReferenceId="CorrelationId" PartnerClaimType="{property:CorrelationId}" DefaultValue="{Context:CorrelationId}" />
    <InputClaim ClaimTypeReferenceId="language" PartnerClaimType="{property:language}" DefaultValue="{Culture:RFC5646}" />
    <InputClaim ClaimTypeReferenceId="AppId" PartnerClaimType="{property:App}" DefaultValue="{OIDC:ClientId}" />
  </InputClaims>
</TechnicalProfile>
```
