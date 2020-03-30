---
title: Reivindicar resolver em políticas personalizadas
titleSuffix: Azure AD B2C
description: Saiba como utilizar as reclamações numa política personalizada no Azure Ative Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/20/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: cba97b84f77bd4e2d4cfd97601fa4f8637105eb4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80051413"
---
# <a name="about-claim-resolvers-in-azure-active-directory-b2c-custom-policies"></a>Sobre pedidos de reclamação em políticas personalizadas do Diretório Ativo Azure B2C

As [políticas personalizadas](custom-policy-overview.md) de reclamação em Azure Ative Directory B2C (Azure AD B2C) fornecem informações de contexto sobre um pedido de autorização, tais como o nome da política, pedido de id de correlação, linguagem de interface do utilizador, e muito mais.

Para utilizar uma reclamação resolver numa reivindicação de entrada ou saída, define um Tipo de **Reclamação**de cadeias , sob o elemento [ClaimsSchema,](claimsschema.md) e, em seguida, define o **DefaultValue** para o resolver de reclamação no elemento de entrada ou de saída. O Azure AD B2C lê o valor da reclamação e utiliza o valor no perfil técnico.

No exemplo seguinte, um `correlationId` tipo de reclamação `string`nomeado é definido com um **DataType** de .

```XML
<ClaimType Id="correlationId">
  <DisplayName>correlationId</DisplayName>
  <DataType>string</DataType>
  <UserHelpText>Request correlation Id</UserHelpText>
</ClaimType>
```

No perfil técnico, mapeie a reclamação resolver o tipo de reclamação. O Azure AD B2C preenche o `{Context:CorrelationId}` valor `correlationId` da reclamação no sinistro e envia a reclamação para o perfil técnico.

```XML
<InputClaim ClaimTypeReferenceId="correlationId" DefaultValue="{Context:CorrelationId}" />
```

## <a name="claim-resolver-types"></a>Tipos de reclamação resolver

A lista de secções seguintes disponível de reclamação resolve.

### <a name="culture"></a>Cultura

| Afirmação | Descrição | Exemplo |
| ----- | ----------- | --------|
| {Cultura:Nome de língua} | O código ISO de duas letras para a língua. | en |
| {Cultura:LCID}   | O LCID do código linguístico. | 1033 |
| {Cultura:Nome da Região} | O código ISO de duas letras para a região. | EUA |
| {Cultura:RFC5646} | O código linguístico RFC5646. | pt-PT |

### <a name="policy"></a>Política

| Afirmação | Descrição | Exemplo |
| ----- | ----------- | --------|
| {Política:PolicyId} | O nome da política do partido. | B2C_1A_signup_signin |
| {Política:RelyingPartyTenantId} | A identificação do inquilino da política do partido. | your-tenant.onmicrosoft.com |
| {Política:TenantObjectId} | A identificação do objeto inquilino da política do partido. | 00000000-0000-0000-0000-000000000000 |
| {Política:TrustFrameworkTenantId} | A identificação do inquilino do quadro fiduciário. | your-tenant.onmicrosoft.com |

### <a name="openid-connect"></a>OpenID Connect

| Afirmação | Descrição | Exemplo |
| ----- | ----------- | --------|
| {OIDC:AutenticaçãoContextReferences} |O `acr_values` parâmetro de corda de consulta. | N/D |
| {OIDC:Clientid} |O `client_id` parâmetro de corda de consulta. | 00000000-0000-0000-0000-000000000000 |
| {OIDC:DomainHint} |O `domain_hint` parâmetro de corda de consulta. | facebook.com |
| {OIDC:LoginHint} |  O `login_hint` parâmetro de corda de consulta. | someone@contoso.com |
| {OIDC:Maxage} | O. `max_age` | N/D |
| {OIDC:Nonce} |O `Nonce` parâmetro de corda de consulta. | padrãoNonce |
| {OIDC:Password}| As credenciais de senha do [proprietário do recurso fluem](ropc-custom.md) a palavra-passe do utilizador.| senha1| 
| {OIDC:Prompt} | O `prompt` parâmetro de corda de consulta. | início de sessão |
| {OIDC:RedirectUri} |O `redirect_uri` parâmetro de corda de consulta. | https://jwt.ms |
| {OIDC:Recurso} |O `resource` parâmetro de corda de consulta. | N/D |
| {OIDC:scope} |O `scope` parâmetro de corda de consulta. | openid |
| {OIDC:Username}| As credenciais de senha do [proprietário do recurso fluem](ropc-custom.md) o nome de utilizador do utilizador.| emily@contoso.com| 

### <a name="context"></a>Contexto

| Afirmação | Descrição | Exemplo |
| ----- | ----------- | --------|
| {Context:BuildNumber} | A versão Quadro de Experiência de Identidade (número de construção).  | 1.0.507.0 |
| {Contexto:Correlação} | A identificação da correlação.  | 00000000-0000-0000-0000-000000000000 |
| {Contexto:DateTimeInUtc} |A data de data na UTC.  | 10/10/2018 12:00:00 |
| {Contexto:Modo de Implantação} |O modo de implantação da política.  | Produção |
| {Contexto:IPAddress} | O endereço IP do utilizador. | 11.111.111.11 |
| {Contexto:KMSI} | Indica se [me mantenho assinado na](custom-policy-keep-me-signed-in.md) caixa de verificação. |  true |

### <a name="non-protocol-parameters"></a>Parâmetros não protocolares

Qualquer nome de parâmetro incluído como parte de um pedido oIDC ou OAuth2 pode ser mapeado para uma reclamação na viagem do utilizador. Por exemplo, o pedido da aplicação pode incluir um `app_session`parâmetro `loyalty_number`de corda de consulta com um nome de , ou qualquer corda de consulta personalizada.

| Afirmação | Descrição | Exemplo |
| ----- | ----------------------- | --------|
| {OAUTH-KV:campaignId} | Um parâmetro de corda de consulta. | Havai |
| {OAUTH-KV:app_session} | Um parâmetro de corda de consulta. | A3C5R |
| {OAUTH-KV:loyalty_number} | Um parâmetro de corda de consulta. | 1234 |
| {OAUTH-KV:qualquer corda de consulta personalizada} | Um parâmetro de corda de consulta. | N/D |

### <a name="oauth2"></a>OAuth2

| Afirmação | Descrição | Exemplo |
| ----- | ----------------------- | --------|
| {oauth2:access_token} | O sinal de acesso. | N/D |


### <a name="saml"></a>SAML

| Afirmação | Descrição | Exemplo |
| ----- | ----------- | --------|
| {SAML:AuthnContextClassReferences} | O `AuthnContextClassRef` valor do elemento, a partir do pedido saml. | urn:oasis:names:tc:SAML:2.0:ac:classes:PasswordProtectedTransport |
| {SAML:Formato NameidPolicy} | O `Format` atributo, `NameIDPolicy` a partir do elemento do pedido SAML. | urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress |
| {SAML:Emitente} |  O valor `Issuer` do elemento SAML do pedido SAML.| `https://contoso.com` |
| {SAML:Permitir Criar} | O `AllowCreate` valor do atributo, a partir do `NameIDPolicy` elemento do pedido SAML. | Verdadeiro |
| {SAML:ForceAuthn} | O `ForceAuthN` valor do atributo, a partir do `AuthnRequest` elemento do pedido SAML. | Verdadeiro |
| {SAML:ProviderName} | O `ProviderName` valor do atributo, a partir do `AuthnRequest` elemento do pedido SAML.| Contoso.com |

## <a name="using-claim-resolvers"></a>Utilização de pedidos de indemnização

Pode utilizar reclamações com os seguintes elementos:

| Item | Elemento | Definições |
| ----- | ----------------------- | --------|
|Perfil técnico de Insights de Aplicação |`InputClaim` | |
|Perfil técnico [do Diretório Ativo Azure](active-directory-technical-profile.md)| `InputClaim`, `OutputClaim`| 1, 2|
|Perfil técnico [OAuth2](oauth2-technical-profile.md)| `InputClaim`, `OutputClaim`| 1, 2|
|Perfil técnico [openID Connect](openid-connect-technical-profile.md)| `InputClaim`, `OutputClaim`| 1, 2|
|Perfil técnico de transformação de [sinistros](claims-transformation-technical-profile.md)| `InputClaim`, `OutputClaim`| 1, 2|
|Perfil técnico do [fornecedor RESTful](restful-technical-profile.md)| `InputClaim`| 1, 2|
|Perfil técnico [SAML2](saml-technical-profile.md)| `OutputClaim`| 1, 2|
|Perfil técnico [autoafirmado](self-asserted-technical-profile.md)| `InputClaim`, `OutputClaim`| 1, 2|
|[Definição de Conteúdo](contentdefinitions.md)| `LoadUri`| |
|[Parâmetros de Definição de Conteúdo](relyingparty.md#contentdefinitionparameters)| `Parameter` | |
|[Perfil](relyingparty.md#technicalprofile) técnico do Partido Da Base| `OutputClaim`| 2 |

Configurações:
1. Os `IncludeClaimResolvingInClaimsHandling` metadados devem `true`ser definidos para .
1. O atributo `AlwaysUseDefaultValue` de créditos de `true`entrada ou de saída deve ser definido para .

## <a name="claim-resolvers-samples"></a>Reclamar amostras resolveria

### <a name="restful-technical-profile"></a>Perfil técnico RESTful

Num perfil técnico [RESTful,](restful-technical-profile.md) pode querer enviar a linguagem do utilizador, nome de política, âmbito e ID do cliente. Com base nas alegações, a API REST pode executar a lógica de negócio personalizada, e se necessário levantar uma mensagem de erro localizada.

O exemplo seguinte mostra um perfil técnico RESTful com este cenário:

```XML
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
    <InputClaim ClaimTypeReferenceId="scope" DefaultValue="{OIDC:scope}" AlwaysUseDefaultValue="true" />
    <InputClaim ClaimTypeReferenceId="clientId" DefaultValue="{OIDC:ClientId}" AlwaysUseDefaultValue="true" />
  </InputClaims>
  <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
</TechnicalProfile>
```

### <a name="direct-sign-in"></a>Início de sessão direto

Utilizando resolver pedidos de reclamação, pode pré-povoar o nome de sessão ou iniciar sessão direta a um fornecedor específico de identidade social, como facebook, LinkedIn ou uma conta Microsoft. Para mais informações, consulte Configurar o início de sessão direta utilizando o [Diretório Ativo Azure B2C](direct-signin.md).

### <a name="dynamic-ui-customization"></a>Personalização dinâmica da UI

O Azure AD B2C permite-lhe passar parâmetros de cadeia de consulta para os pontos finais da definição de conteúdo HTML para tornar dinamicamente o conteúdo da página. Por exemplo, isto permite modificar a imagem de fundo na página de inscrição ou inscrição do Azure AD B2C com base num parâmetro personalizado que passa da sua aplicação web ou móvel. Para mais informações, consulte [Dynamicly configure o UI utilizando políticas personalizadas no Diretório Ativo Azure B2C](custom-policy-ui-customization.md). Também pode localizar a sua página HTML com base num parâmetro de idioma, ou pode alterar o conteúdo com base no ID do cliente.

O exemplo seguinte passa no parâmetro de corda de `Hawaii`consulta chamado **campaignId** com um valor de , um código **de linguagem** de `en-US`, e **app** que representa o ID do cliente:

```XML
<UserJourneyBehaviors>
  <ContentDefinitionParameters>
    <Parameter Name="campaignId">{OAUTH-KV:campaignId}</Parameter>
    <Parameter Name="language">{Culture:RFC5646}</Parameter>
    <Parameter Name="app">{OIDC:ClientId}</Parameter>
  </ContentDefinitionParameters>
</UserJourneyBehaviors>
```

Como resultado, o Azure AD B2C envia os parâmetros acima para a página de conteúdo HTML:

```
/selfAsserted.aspx?campaignId=hawaii&language=en-US&app=0239a9cc-309c-4d41-87f1-31288feb2e82
```

### <a name="content-definition"></a>Definição de conteúdo

Numa [Definição](contentdefinitions.md) `LoadUri`de Conteúdo, pode enviar pedidos de reclamação para retirar conteúdo de diferentes locais, com base nos parâmetros utilizados.

```XML
<ContentDefinition Id="api.signuporsignin">
  <LoadUri>https://contoso.blob.core.windows.net/{Culture:LanguageName}/myHTML/unified.html</LoadUri>
  ...
</ContentDefinition>
```

### <a name="application-insights-technical-profile"></a>Perfil técnico de Insights de Aplicação

Com o Azure Application Insights e alegações resolver ias pode obter informações sobre o comportamento do utilizador. No perfil técnico da Application Insights, envia reclamações de entrada que persistem para insights de aplicação do Azure. Para mais informações, consulte o [comportamento do utilizador em viagens Azure AD B2C utilizando insights](analytics-with-application-insights.md)de aplicação . O exemplo seguinte envia o ID da política, id de correlação, linguagem e o ID do cliente para Azure Application Insights.

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

### <a name="relying-party-policy"></a>Política partidária de base

Num perfil técnico de política [do partido Relying,](relyingparty.md) pode querer enviar o ID do inquilino, ou identificação de correlação para a aplicação do partido que confia dentro do JWT.

```XML
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
