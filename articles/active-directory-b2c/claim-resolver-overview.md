---
title: Reclamação de resolver em políticas personalizadas
titleSuffix: Azure AD B2C
description: Saiba como utilizar reclamações numa política personalizada no Azure Ative Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 10/28/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 39b61815c33f933e0cdf08bd46382e74eea2f806
ms.sourcegitcommit: 4f4a2b16ff3a76e5d39e3fcf295bca19cff43540
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93040459"
---
# <a name="about-claim-resolvers-in-azure-active-directory-b2c-custom-policies"></a>Sobre as reclamações em Azure Ative Directory B2C políticas personalizadas

As [políticas personalizadas](custom-policy-overview.md) de reclamação em Azure Ative Directory B2C (Azure AD B2C) fornecem informações de contexto sobre um pedido de autorização, tais como o nome da apólice, solicitar iD de correlação, linguagem de interface de utilizador, e muito mais.

Para utilizar uma reclamação de resposta numa reclamação de entrada ou saída, define um **String ClaimType** , sob o elemento [ClaimsSchema,](claimsschema.md) e, em seguida, define o **DefaultValue** para a reclamação no elemento de reivindicação ou de entrada ou saída. Azure AD B2C lê o valor da reclamação e utiliza o valor no perfil técnico.

No exemplo seguinte, um tipo de reclamação nomeado `correlationId` é definido com um **DataType** de `string` .

```xml
<ClaimType Id="correlationId">
  <DisplayName>correlationId</DisplayName>
  <DataType>string</DataType>
  <UserHelpText>Request correlation Id</UserHelpText>
</ClaimType>
```

No perfil técnico, mapear a reclamação para o tipo de reclamação. O Azure AD B2C preenche o valor da reclamação `{Context:CorrelationId}` `correlationId` e envia a reclamação para o perfil técnico.

```xml
<InputClaim ClaimTypeReferenceId="correlationId" DefaultValue="{Context:CorrelationId}" />
```

## <a name="claim-resolver-types"></a>Reclamar tipos de resolver

A lista de secções seguintes disponível.

### <a name="culture"></a>Cultura

| Afirmação | Descrição | Exemplo |
| ----- | ----------- | --------|
| {Cultura:Nome de língua} | O código ISO de duas letras para a língua. | en |
| {Cultura:LCID}   | O LCID do código linguístico. | 1033 |
| {Cultura:Região Nome} | O código ISO de duas letras para a região. | EUA |
| {Cultura:RFC5646} | O código linguístico RFC5646. | en-PT |

### <a name="policy"></a>Política

| Afirmação | Descrição | Exemplo |
| ----- | ----------- | --------|
| {Política:PolicyId} | O nome da política do partido. | B2C_1A_signup_signin |
| {Política:RelyingPartyTenantId} | A identificação do inquilino da política do partido. | your-tenant.onmicrosoft.com |
| {Política:TenantObjectId} | O inquilino opõe a identificação da política do partido. | 00000000-0000-0000-0000-000000000000 |
| {Política:TrustFrameworkTenantId} | A identificação do inquilino do quadro de confiança. | your-tenant.onmicrosoft.com |

### <a name="openid-connect"></a>OpenID Connect

| Afirmação | Descrição | Exemplo |
| ----- | ----------- | --------|
| {OIDC:AutenticaçãoContextoreferências} |O `acr_values` parâmetro da corda de consulta. | N/D |
| {OIDC:ClientId} |O `client_id`  parâmetro da corda de consulta. | 00000000-0000-0000-0000-000000000000 |
| {OIDC:DomainHint} |O `domain_hint`  parâmetro da corda de consulta. | facebook.com |
| {OIDC:LoginHint} |  O `login_hint` parâmetro da corda de consulta. | someone@contoso.com |
| {OIDC:MaxAge} | O campo `max_age`. | N/D |
| {OIDC:Nonce} |O `Nonce`  parâmetro da corda de consulta. | padrãoNonce |
| {OIDC:Password}| As [credenciais de senha do proprietário do recurso fluem](ropc-custom.md) a palavra-passe do utilizador.| senha1| 
| {OIDC:Prompt} | O `prompt` parâmetro da corda de consulta. | início de sessão |
| {OIDC:RedirectUri} |O `redirect_uri`  parâmetro da corda de consulta. | https://jwt.ms |
| {OIDC:Recurso} |O `resource`  parâmetro da corda de consulta. | N/D |
| {OIDC:Scope} |O `scope`  parâmetro da corda de consulta. | openid |
| {OIDC:Username}| As [credenciais de senha do proprietário](ropc-custom.md) do recurso fluem o nome de utilizador do utilizador.| emily@contoso.com| 

### <a name="context"></a>Contexto

| Afirmação | Descrição | Exemplo |
| ----- | ----------- | --------|
| {Contexto:BuildNumber} | A versão Quadro de Identidade Experiência (número de construção).  | 1.0.507.0 |
| {Contexto:CorrelationId} | A identificação da correlação.  | 00000000-0000-0000-0000-000000000000 |
| {Contexto:DateTimeInUtc} |A data na UTC.  | 10/10/2018 12:00:00 |
| {Contexto:DeploymentMode} |O modo de implementação da política.  | Produção |
| {Contexto:IPAddress} | O endereço IP do utilizador. | 11.111.111.11 |
| {Contexto:KMSI} | Indica se [me mantém assinado na](custom-policy-keep-me-signed-in.md) caixa de verificação. |  true |

### <a name="claims"></a>Afirmações 

| Afirmação | Descrição | Exemplo |
| ----- | ----------- | --------|
| {Claim:Claim type} | Um identificador de um tipo de reclamação já definido na secção ClaimsSchema no ficheiro de política ou no ficheiro de política dos pais.  Por exemplo: `{Claim:displayName}` ou `{Claim:objectId}` . . | Um valor tipo de reclamação.|


### <a name="oauth2-key-value-parameters"></a>Parâmetros de valor-chave OAuth2

Qualquer nome de parâmetro incluído como parte de um pedido OIDC ou OAuth2 pode ser mapeado para uma reclamação na viagem do utilizador. Por exemplo, o pedido da aplicação pode incluir um parâmetro de cadeia de consulta com um nome de `app_session` , ou qualquer cadeia de consulta `loyalty_number` personalizada.

| Afirmação | Descrição | Exemplo |
| ----- | ----------------------- | --------|
| {OAUTH-KV:campaignId} | Um parâmetro de cadeia de consulta. | Havai |
| {OAUTH-KV:app_session} | Um parâmetro de cadeia de consulta. | A3C5R |
| {OAUTH-KV:loyalty_number} | Um parâmetro de cadeia de consulta. | 1234 |
| {OAUTH-KV:qualquer cadeia de consulta personalizada} | Um parâmetro de cadeia de consulta. | N/D |

### <a name="oauth2"></a>OAuth2

| Afirmação | Descrição | Exemplo |
| ----- | ----------------------- | --------|
| {oauth2:access_token} | O símbolo de acesso. | N/D |
| {oauth2:refresh_token} | O token refresh. | N/D |


### <a name="saml"></a>SAML

| Afirmação | Descrição | Exemplo |
| ----- | ----------- | --------|
| {SAML:AuthnContextClassReferences} | O `AuthnContextClassRef` valor do elemento, a partir do pedido da SAML. | urn:oasis:names:tc:SAML:2.0:ac:classes:PasswordProtectedTransport |
| {SAML:NameIdPolicyFormat} | O `Format` atributo, a partir do `NameIDPolicy` elemento do pedido SAML. | urn:oasis:names:tc:SAML:1.1:nameid-formato:emailAddress |
| {SAML:Emitente} |  O valor do elemento SAML `Issuer` do pedido SAML.| `https://contoso.com` |
| {SAML:Permitir aates} | O `AllowCreate` valor do atributo, a partir do elemento `NameIDPolicy` do pedido SAML. | Verdadeiro |
| {SAML:ForceAuthn} | O `ForceAuthN` valor do atributo, a partir do elemento `AuthnRequest` do pedido SAML. | Verdadeiro |
| {SAML:ProviderName} | O `ProviderName` valor do atributo, a partir do elemento `AuthnRequest` do pedido SAML.| Contoso.com |
| {SAML:RelayState} | O `RelayState` parâmetro da corda de consulta.| 
| {SAML:Assunto} | Do `Subject` elemento NameId do pedido SAML AuthN.| 

## <a name="using-claim-resolvers"></a>Utilização de resolverções de reclamações

Pode utilizar as reclamações com os seguintes elementos:

| Item | Elemento | Definições |
| ----- | ----------------------- | --------|
|Perfil técnico de Insights de Aplicação |`InputClaim` | |
|Perfil técnico [do Diretório Ativo Azure](active-directory-technical-profile.md)| `InputClaim`, `OutputClaim`| 1, 2|
|Perfil técnico [OAuth2](oauth2-technical-profile.md)| `InputClaim`, `OutputClaim`| 1, 2|
|Perfil técnico [openID Connect](openid-connect-technical-profile.md)| `InputClaim`, `OutputClaim`| 1, 2|
|Perfil técnico [de transformação de sinistros](claims-transformation-technical-profile.md)| `InputClaim`, `OutputClaim`| 1, 2|
|Perfil técnico [do fornecedor RESTful](restful-technical-profile.md)| `InputClaim`| 1, 2|
|Perfil técnico [do fornecedor de identidade SAML](saml-identity-provider-technical-profile.md)| `OutputClaim`| 1, 2|
|Perfil técnico [autoafirmado](self-asserted-technical-profile.md)| `InputClaim`, `OutputClaim`| 1, 2|
|[Definição de conteúdo](contentdefinitions.md)| `LoadUri`| |
|[ConteúdoDefinitionParameters](relyingparty.md#contentdefinitionparameters)| `Parameter` | |
|Perfil técnico [doParte](relyingparty.md#technicalprofile)| `OutputClaim`| 2 |

Configurações:
1. Os `IncludeClaimResolvingInClaimsHandling` metadados devem ser definidos para `true` .
1. O atributo de reclamações de entrada ou saída `AlwaysUseDefaultValue` deve ser definido para `true` .

## <a name="claim-resolvers-samples"></a>Reclamação resolve amostras

### <a name="restful-technical-profile"></a>Perfil técnico RESTful

Num perfil técnico [RESTful,](restful-technical-profile.md) pode querer enviar o idioma do utilizador, o nome da política, o âmbito e o ID do cliente. Com base nas alegações, a API REST pode executar lógicas de negócio personalizadas e, se necessário, levantar uma mensagem de erro localizada.

O exemplo a seguir mostra um perfil técnico RESTful com este cenário:

```xml
<TechnicalProfile Id="REST">
  <DisplayName>Validate user input data and return loyaltyNumber claim</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ServiceUrl">https://your-app.azurewebsites.net/api/identity</Item>
    <Item Key="AuthenticationType">None</Item>
    <Item Key="SendClaimsIn">Body</Item>
    <Item Key="IncludeClaimResolvingInClaimsHandling">true</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="userLanguage" DefaultValue="{Culture:LCID}" AlwaysUseDefaultValue="true" />
    <InputClaim ClaimTypeReferenceId="policyName" DefaultValue="{Policy:PolicyId}" AlwaysUseDefaultValue="true" />
    <InputClaim ClaimTypeReferenceId="scope" DefaultValue="{OIDC:Scope}" AlwaysUseDefaultValue="true" />
    <InputClaim ClaimTypeReferenceId="clientId" DefaultValue="{OIDC:ClientId}" AlwaysUseDefaultValue="true" />
  </InputClaims>
  <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
</TechnicalProfile>
```

### <a name="direct-sign-in"></a>Início de sessão direto

Utilizando os problemas de reclamação, pode pré-fazer o sinal ou iniciar sôs-in direto a um fornecedor específico de identidade social, como o Facebook, LinkedIn ou uma conta Microsoft. Para obter mais informações, consulte [Configurar o insusimento direto utilizando o Azure Ative Directory B2C](direct-signin.md).

### <a name="dynamic-ui-customization"></a>Personalização dinâmica da UI

O Azure AD B2C permite-lhe passar parâmetros de cadeia de consulta para os seus pontos finais de definição de conteúdo HTML para tornar dinamicamente o conteúdo da página. Por exemplo, esta funcionalidade permite modificar a imagem de fundo na página de inscrição ou inscrição Azure AD B2C com base num parâmetro personalizado que passa a partir da sua web ou aplicação móvel. Para obter mais informações, consulte [a Configuração Dinâmica da UI utilizando políticas personalizadas no Azure Ative Directory B2C](custom-policy-ui-customization.md#configure-dynamic-custom-page-content-uri). Também pode localizar a sua página HTML com base num parâmetro de idioma, ou pode alterar o conteúdo com base no ID do cliente.

O exemplo a seguir passa no parâmetro de cadeia de consulta nomeado **campanhaId** com um valor de `Hawaii` , um código de **idioma** de `en-US` - e **app** que representa o ID do cliente:

```xml
<UserJourneyBehaviors>
  <ContentDefinitionParameters>
    <Parameter Name="campaignId">{OAUTH-KV:campaignId}</Parameter>
    <Parameter Name="language">{Culture:RFC5646}</Parameter>
    <Parameter Name="app">{OIDC:ClientId}</Parameter>
  </ContentDefinitionParameters>
</UserJourneyBehaviors>
```

Como resultado, a Azure AD B2C envia os parâmetros acima para a página de conteúdo HTML:

```
/selfAsserted.aspx?campaignId=hawaii&language=en-US&app=0239a9cc-309c-4d41-87f1-31288feb2e82
```

### <a name="content-definition"></a>Definição de conteúdo

Numa [Definição de Conteúdo,](contentdefinitions.md) `LoadUri` pode enviar reclamações para retirar conteúdo de diferentes locais, com base nos parâmetros utilizados.

```xml
<ContentDefinition Id="api.signuporsignin">
  <LoadUri>https://contoso.blob.core.windows.net/{Culture:LanguageName}/myHTML/unified.html</LoadUri>
  ...
</ContentDefinition>
```

### <a name="application-insights-technical-profile"></a>Perfil técnico de Insights de Aplicação

Com a Azure Application Insights e alegar resolver, pode obter informações sobre o comportamento do utilizador. No perfil técnico do Application Insights, envia alegações de entrada que são persistidos ao Azure Application Insights. Para obter mais informações, consulte [track user behavior in Azure AD B2C journeys using Application Insights](analytics-with-application-insights.md). O exemplo a seguir envia o ID da política, iD de correlação, idioma e o iD do cliente para Azure Application Insights.

```xml
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

### <a name="relying-party-policy"></a>Política partidária de confiar

Num perfil técnico de política [do partido Desemando,](relyingparty.md) você pode querer enviar o ID do inquilino, ou identificação de correlação para a aplicação do partido confiando dentro do JWT.

```xml
<RelyingParty>
    <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
    <TechnicalProfile Id="PolicyProfile">
      <DisplayName>PolicyProfile</DisplayName>
      <Protocol Name="OpenIdConnect" />
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="displayName" />
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surname" />
        <OutputClaim ClaimTypeReferenceId="email" />
        <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
        <OutputClaim ClaimTypeReferenceId="identityProvider" />
        <OutputClaim ClaimTypeReferenceId="tenantId" AlwaysUseDefaultValue="true" DefaultValue="{Policy:TenantObjectId}" />
        <OutputClaim ClaimTypeReferenceId="correlationId" AlwaysUseDefaultValue="true" DefaultValue="{Context:CorrelationId}" />
      </OutputClaims>
      <SubjectNamingInfo ClaimType="sub" />
    </TechnicalProfile>
  </RelyingParty>
```
