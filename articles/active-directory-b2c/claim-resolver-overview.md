---
title: Cerca de afirmação resoluções de políticas personalizadas do Azure Active Directory B2C | Documentos da Microsoft
description: Saiba mais sobre como as resoluções de afirmações são utilizadas numa política personalizada no Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 01/25/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: a13d4b0b44c51f78a068b1619fe083a08756af6b
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/04/2019
ms.locfileid: "66511611"
---
# <a name="about-claim-resolvers-in-azure-active-directory-b2c-custom-policies"></a>Sobre as resoluções de afirmação em políticas personalizadas do Azure Active Directory B2C

Resoluções no Azure Active Directory (Azure AD) B2C de afirmação [políticas personalizadas](active-directory-b2c-overview-custom.md) fornecem informações de contexto sobre um pedido de autorização, tais como o nome da política, ID de correlação de pedido, o idioma de interface do usuário e muito mais.

Para utilizar um resolvedor de afirmação numa afirmação de entrada ou de saída, define uma cadeia de caracteres **ClaimType**, no [ClaimsSchema](claimsschema.md) elemento e, em seguida, defina o **DefaultValue** para a afirmação elemento de afirmação resolvedor da entrada ou saída. O Azure AD B2C lê o valor de resolução de afirmação e utiliza o valor no perfil técnico. 

No exemplo seguinte, o nome de um tipo de afirmação `correlationId` está definida com um **DataType** de `string`.  

```XML
<ClaimType Id="correlationId">
  <DisplayName>correlationId</DisplayName>
  <DataType>string</DataType>
  <UserHelpText>Request correlation Id</UserHelpText>
</ClaimType>
```

No perfil técnico, mapear o Resolvedor de afirmação para o tipo de afirmação. O Azure AD B2C preenche o valor de resolução de afirmação `{Context:CorrelationId}` para a afirmação `correlationId` e envia a afirmação para o perfil técnico.

```XML
<InputClaim ClaimTypeReferenceId="correlationId" DefaultValue="{Context:CorrelationId}" />
```

## <a name="claim-resolver-types"></a>Resolvedor de tipos de afirmação

As secções seguintes listam as resoluções de declarações disponíveis.

### <a name="culture"></a>Cultura

| Afirmação | Descrição | Exemplo |
| ----- | ----------- | --------|
| {Culture:LanguageName} | As duas letras código ISO para o idioma. | en |
| {Culture:LCID}   | O LCID de código de idioma. | 1033 |
| {Culture:RegionName} | As duas letras código ISO para a região. | EUA |
| {Culture:RFC5646} | O código de idioma RFC5646. | en-US |

### <a name="policy"></a>Política

| Afirmação | Descrição | Exemplo |
| ----- | ----------- | --------|
| {Policy:PolicyId} | O nome de política de terceiros entidade confiadora. | B2C_1A_signup_signin |
| {Policy:RelyingPartyTenantId} | O ID de inquilino da política da entidade confiadora de terceiros. | your-tenant.onmicrosoft.com |
| {Policy:TenantObjectId} | O ID de objeto do inquilino da política da entidade confiadora de terceiros. | 00000000-0000-0000-0000-000000000000 |
| {Policy:TrustFrameworkTenantId} | O ID de inquilino do framework de confiança. | your-tenant.onmicrosoft.com |

### <a name="openid-connect"></a>OpenID Connect

| Afirmação | Descrição | Exemplo |
| ----- | ----------- | --------|
| {OIDC:AuthenticationContextReferences} |O `acr_values` parâmetro de cadeia de caracteres de consulta. | N/A |
| {OIDC:ClientId} |O `client_id` parâmetro de cadeia de caracteres de consulta. | 00000000-0000-0000-0000-000000000000 |
| {OIDC:DomainHint} |O `domain_hint` parâmetro de cadeia de caracteres de consulta. | facebook.com |
| {OIDC:LoginHint} |  O `login_hint` parâmetro de cadeia de caracteres de consulta. | someone@contoso.com |
| {OIDC:MaxAge} | O `max_age`. | N/A |
| {OIDC:Nonce} |O `Nonce` parâmetro de cadeia de caracteres de consulta. | defaultNonce |
| {OIDC:Prompt} | O `prompt` parâmetro de cadeia de caracteres de consulta. | início de sessão |
| {OIDC:Resource} |O `resource` parâmetro de cadeia de caracteres de consulta. | N/A |
| {OIDC:scope} |O `scope` parâmetro de cadeia de caracteres de consulta. | openid |

### <a name="context"></a>Contexto

| Afirmação | Descrição | Exemplo |
| ----- | ----------- | --------|
| {Context:BuildNumber} | A versão do Framework de experiência de identidade (número de compilação).  | 1.0.507.0 |
| {Context:CorrelationId} | O ID de correlação.  | 00000000-0000-0000-0000-000000000000 |
| {Context:DateTimeInUtc} |A data da hora em UTC.  | 10/10/2018 12: DAS 12:00:00 |
| {Context:DeploymentMode} |O modo de implementação de política.  | Produção |
| {Context:IPAddress} | O endereço IP do utilizador. | 11.111.111.11 |


### <a name="non-protocol-parameters"></a>Parâmetros de protocolo não

Qualquer nome de parâmetro incluída como parte de um pedido de OIDC ou OAuth2 pode ser mapeada para uma declaração no percurso do utilizador. Por exemplo, o pedido da aplicação pode incluir um parâmetro de cadeia de caracteres de consulta com o nome `app_session`, `loyalty_number`, ou qualquer cadeia de consulta personalizada.

| Afirmação | Descrição | Exemplo |
| ----- | ----------------------- | --------|
| {OAUTH-KV:campaignId} | Um parâmetro de cadeia de caracteres de consulta. | hawaii |
| {OAUTH-KV:app_session} | Um parâmetro de cadeia de caracteres de consulta. | A3C5R |
| {OAUTH-KV:loyalty_number} | Um parâmetro de cadeia de caracteres de consulta. | 1234 |
| {OAUTH-KV: qualquer cadeia de consulta personalizada} | Um parâmetro de cadeia de caracteres de consulta. | N/A |

### <a name="oauth2"></a>OAuth2

| Afirmação | Descrição | Exemplo |
| ----- | ----------------------- | --------|
| {oauth2:access_token} | O token de acesso. | N/A |

## <a name="how-to-use-claim-resolvers"></a>Como utilizar resoluções de afirmação

### <a name="restful-technical-profile"></a>Perfil técnico rESTful

Num [RESTful](restful-technical-profile.md) perfil técnico, talvez queira enviar o idioma do usuário, o nome da política, o âmbito e o ID de cliente. Com base nessas declarações da API REST que pode executar lógica de negócio personalizada e, se for necessário emitir uma mensagem de erro localizados. 

O exemplo seguinte mostra um perfil técnico RESTful:

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

Utilizar resoluções de afirmação, pode preencher o nome de início de sessão ou direto início de sessão para um fornecedor de identidade específicas de redes sociais, como o Facebook, LinkedIn ou uma conta Microsoft. Para obter mais informações, consulte [configurar direto início de sessão através do Azure Active Directory B2C](direct-signin.md).

### <a name="dynamic-ui-customization"></a>Personalização de interface de Usuário dinâmica

O Azure AD B2C permite-lhe transmitir parâmetros de cadeia de caracteres de consulta para os pontos finais de definição de conteúdo de HTML para que dinamicamente pode processar o conteúdo da página. Por exemplo, pode alterar a imagem de fundo da página de inscrição ou início de sessão de B2C do Azure AD com base num parâmetro personalizado que passar de seu aplicativo web ou móvel. Para obter mais informações, consulte [configurar a interface do Usuário de forma dinâmica ao utilizar políticas personalizadas no Azure Active Directory B2C](active-directory-b2c-ui-customization-custom-dynamic.md). Também pode localizar sua página HTML com base num parâmetro de idioma, ou pode alterar o conteúdo com base no ID do cliente.

O exemplo seguinte transmite na cadeia de consulta, um parâmetro denominado **campaignId** com um valor de `hawaii`, um **linguagem** código de `en-US`, e **aplicação** que representa o ID de cliente:

```XML
<UserJourneyBehaviors>
  <ContentDefinitionParameters>
    <Parameter Name="campaignId">{OAUTH-KV:campaignId}</Parameter>
    <Parameter Name="language">{Culture:RFC5646}</Parameter>
    <Parameter Name="app">{OIDC:ClientId}</Parameter>
  </ContentDefinitionParameters>
</UserJourneyBehaviors>
```

Como resultado do Azure AD B2C envia os parâmetros acima para a página de conteúdo de HTML:

```
/selfAsserted.aspx?campaignId=hawaii&language=en-US&app=0239a9cc-309c-4d41-87f1-31288feb2e82
```

### <a name="application-insights-technical-profile"></a>Perfil técnico do Application Insights

Com o Azure Application Insights e resoluções de afirmação, pode obter informações sobre o comportamento do utilizador. O perfil técnico do Application Insights, envia afirmações de entrada que são mantidas para o Azure Application Insights. Para obter mais informações, consulte [jornadas de comportamento do utilizador Track no Azure AD B2C ao utilizar o Application Insights](active-directory-b2c-custom-guide-eventlogger-appins.md). O exemplo seguinte envia o ID de política, ID de correlação, idioma e o ID de cliente para o Azure Application Insights.

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
