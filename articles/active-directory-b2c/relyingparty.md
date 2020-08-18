---
title: RelyingParty - Azure Ative Directory B2C / Microsoft Docs
description: Especificar o elemento RelyingParty de uma política personalizada no Azure Ative Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 08/17/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 40672ac958e84d816d4b582472ae04502a910c6a
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/18/2020
ms.locfileid: "88521268"
---
# <a name="relyingparty"></a>RelyingParty

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

O elemento **RelyingParty** especifica a jornada do utilizador para fazer cumprir o pedido atual ao Azure Ative Directory B2C (Azure AD B2C). Especifica igualmente a lista de reclamações de que a parte dependente (RP) necessita como parte do token emitido. Uma aplicação RP, como uma aplicação web, móvel ou desktop, chama o ficheiro de política rp. O ficheiro de política RP executa uma tarefa específica, como iniciar sessão, redefinir uma palavra-passe ou editar um perfil. Várias aplicações podem usar a mesma política de RP e uma única aplicação pode usar várias políticas. Todas as aplicações RP recebem o mesmo sinal com reclamações, e o utilizador passa pela mesma viagem de utilizador.

O exemplo a seguir mostra um elemento **DaParte de Apoio** no ficheiro de política *B2C_1A_signup_signin:*

```xml
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<TrustFrameworkPolicy
  xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance"
  xmlns:xsd="https://www.w3.org/2001/XMLSchema"
  xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
  PolicySchemaVersion="0.3.0.0"
  TenantId="your-tenant.onmicrosoft.com"
  PolicyId="B2C_1A_signup_signin"
  PublicPolicyUri="http://your-tenant.onmicrosoft.com/B2C_1A_signup_signin">

  <BasePolicy>
    <TenantId>your-tenant.onmicrosoft.com</TenantId>
    <PolicyId>B2C_1A_TrustFrameworkExtensions</PolicyId>
  </BasePolicy>

  <RelyingParty>
    <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
    <UserJourneyBehaviors>
      <SingleSignOn Scope="Tenant" KeepAliveInDays="7"/>
      <SessionExpiryType>Rolling</SessionExpiryType>
      <SessionExpiryInSeconds>300</SessionExpiryInSeconds>
      <JourneyInsights TelemetryEngine="ApplicationInsights" InstrumentationKey="your-application-insights-key" DeveloperMode="true" ClientEnabled="false" ServerEnabled="true" TelemetryVersion="1.0.0" />
      <ContentDefinitionParameters>
        <Parameter Name="campaignId">{OAUTH-KV:campaignId}</Parameter>
      </ContentDefinitionParameters>
    </UserJourneyBehaviors>
    <TechnicalProfile Id="PolicyProfile">
      <DisplayName>PolicyProfile</DisplayName>
      <Description>The policy profile</Description>
      <Protocol Name="OpenIdConnect" />
      <Metadata>collection of key/value pairs of data</Metadata>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="displayName" />
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surname" />
        <OutputClaim ClaimTypeReferenceId="email" />
        <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
        <OutputClaim ClaimTypeReferenceId="identityProvider" />
        <OutputClaim ClaimTypeReferenceId="loyaltyNumber" />
      </OutputClaims>
      <SubjectNamingInfo ClaimType="sub" />
    </TechnicalProfile>
  </RelyingParty>
  ...
```

O elemento **opcional RelyingParty** contém os seguintes elementos:

| Elemento | Ocorrências | Description |
| ------- | ----------- | ----------- |
| PredefiniçãoJourney | 1:1 | A viagem de utilizador predefinido para a aplicação RP. |
| UserJourneyBehaviors | 0:1 | O âmbito dos comportamentos de viagem do utilizador. |
| Ficha Técnica | 1:1 | Um perfil técnico que é suportado pela aplicação RP. O perfil técnico fornece um contrato para a aplicação RP para contactar a Azure AD B2C. |

## <a name="defaultuserjourney"></a>PredefiniçãoJourney

O `DefaultUserJourney` elemento especifica uma referência ao identificador da viagem do utilizador que é normalmente definida na política base ou extensões. Os exemplos a seguir mostram a viagem de utilizador de inscrição ou de inscrição especificada no elemento **RelyingParty:**

*B2C_1A_signup_signin* política:

```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn">
  ...
```

*B2C_1A_TrustFrameWorkBase* ou *B2C_1A_TrustFrameworkExtensionPolicy:*

```xml
<UserJourneys>
  <UserJourney Id="SignUpOrSignIn">
  ...
```

O elemento **DefaultUserJourney** contém o seguinte atributo:

| Atributo | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| ReferenceId | Yes | Um identificador da viagem de utilizador na apólice. Para mais informações, consulte [as viagens de utilizador](userjourneys.md) |

## <a name="userjourneybehaviors"></a>UserJourneyBehaviors

O elemento **UserJourneyBehaviors** contém os seguintes elementos:

| Elemento | Ocorrências | Description |
| ------- | ----------- | ----------- |
| SingleSignOn | 0:1 | O âmbito do comportamento de sessão de sessão de inscrição única (SSO) de uma viagem de utilizador. |
| SessãoExpiryType |0:1 | O comportamento de autenticação da sessão. Valores possíveis: `Rolling` ou `Absolute` . O `Rolling` valor (predefinido) indica que o utilizador permanece assinado enquanto o utilizador estiver continuamente ativo na aplicação. O `Absolute` valor indica que o utilizador é obrigado a reautenticar após o período de tempo especificado pela sessão de trabalho útil da sessão de aplicação. |
| SessionExpiryInSeconds | 0:1 | O cookie de sessão do Azure AD B2C especificado como um número inteiro armazenado no navegador do utilizador após a autenticação bem sucedida. |
| JourneyInsights | 0:1 | A chave de instrumentação Azure Application Insights a utilizar. |
| ConteúdoDefinitionParameters | 0:1 | A lista de pares de valores-chave a anexar à definição de conteúdo carregar URI. |
|ScriptExecution| 0:1| Os modos de execução [JavaScript](javascript-samples.md) suportados. Valores possíveis: `Allow` ou `Disallow` (predefinição).

### <a name="singlesignon"></a>SingleSignOn

O elemento **SingleSignOn** contém no seguinte atributo:

| Atributo | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| Âmbito | Yes | O âmbito do comportamento de um único sinal. Valores possíveis: `Suppressed` `Tenant` , , ou `Application` `Policy` . O `Suppressed` valor indica que o comportamento é suprimido e o utilizador é sempre solicitado para uma seleção de fornecedor de identidade.  O `Tenant` valor indica que o comportamento é aplicado a todas as políticas do arrendatário. Por exemplo, um utilizador que navegue através de duas viagens políticas para um inquilino não é solicitado para uma seleção de fornecedor de identidade. O `Application` valor indica que o comportamento é aplicado a todas as políticas para a aplicação que faz o pedido. Por exemplo, um utilizador que navega através de duas viagens políticas para uma aplicação não é solicitado para uma seleção de fornecedor de identidade. O `Policy` valor indica que o comportamento só se aplica a uma política. Por exemplo, um utilizador que navegue através de duas viagens políticas para um quadro de confiança é solicitado para uma seleção do fornecedor de identidade ao alternar entre políticas. |
| KeepAliveInDays | Yes | Controla quanto tempo o utilizador permanece assinado. Definir o valor para 0 desliga a funcionalidade KMSI. Para mais informações, consulte [Keep me signed in.](custom-policy-keep-me-signed-in.md) |
|EnforceIdTokenHintOnLogout| No|  Forçar a passar um token de ID previamente emitido para o ponto final do logout como uma dica sobre a sessão autenticada atual do utilizador final com o cliente. Valores possíveis: `false` (padrão), ou `true` . Para obter mais informações, consulte [o web-in com OpenID Connect](openid-connect.md).  |


## <a name="journeyinsights"></a>JourneyInsights

O elemento **JourneyInsights** contém os seguintes atributos:

| Atributo | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| TelemetriaEngine | Yes | O valor deve `ApplicationInsights` ser. |
| InstrumentaçãoKey | Yes | A cadeia que contém a chave de instrumentação para o elemento de insights de aplicação. |
| DeveloperMode | Yes | Valores possíveis: `true` ou `false` . Se, `true` Application Insights acelerar a telemetria através do gasoduto de processamento. Esta configuração é boa para o desenvolvimento, mas limitada em volumes elevados Os registos de atividades detalhadas são projetados apenas para ajudar no desenvolvimento de políticas personalizadas. Não utilize o modo de desenvolvimento na produção. Os registos recolhem todas as reclamações enviadas de e para os fornecedores de identidade durante o desenvolvimento. Se for utilizado na produção, o desenvolvedor assume a responsabilidade pelo PII (PrivateLy Identifiable Information) recolhido no registo app Insights que possuem. Estes registos detalhados só são recolhidos quando este valor é definido para `true` .|
| ClienteEnabled | Yes | Valores possíveis: `true` ou `false` . Se `true` , enviar o script do lado do cliente do Application Insights para visualização da página de rastreio e erros do lado do cliente. |
| ServerEnabled | Yes | Valores possíveis: `true` ou `false` . Se `true` , enviar o Atual UserJourneyRecorder JSON como um evento personalizado para a Application Insights. |
| TelemetriaVersão | Yes | O valor deve `1.0.0` ser. |

Para mais informações, consulte [a recolha de registos](troubleshoot-with-application-insights.md)

## <a name="contentdefinitionparameters"></a>ConteúdoDefinitionParameters

Ao utilizar políticas personalizadas em Azure AD B2C, pode enviar um parâmetro numa cadeia de consultas. Ao transmitir o parâmetro para o ponto final HTML, pode alterar dinamicamente o conteúdo da página. Por exemplo, pode alterar a imagem de fundo na página de inscrição ou de início de sessão do Azure AD B2C, com base num parâmetro que transmite a partir da sua aplicação Web ou móvel. Azure AD B2C passa os parâmetros de cadeia de consulta para o seu ficheiro HTML dinâmico, como o ficheiro aspx.

O exemplo a seguir passa um parâmetro nomeado `campaignId` com um valor da cadeia de `hawaii` consulta:

`https://login.microsoft.com/contoso.onmicrosoft.com/oauth2/v2.0/authorize?pB2C_1A_signup_signin&client_id=a415078a-0402-4ce3-a9c6-ec1947fcfb3f&nonce=defaultNonce&redirect_uri=http%3A%2F%2Fjwt.io%2F&scope=openid&response_type=id_token&prompt=login&campaignId=hawaii`

O elemento **ContentDefinitionParameters** contém o seguinte elemento:

| Elemento | Ocorrências | Description |
| ------- | ----------- | ----------- |
| ConteúdoDefinitionParameter | 0:n | Uma cadeia que contém o par de valor chave que é anexado à cadeia de consulta de uma definição de conteúdo carregar URI. |

O elemento **ContentDefinitionParameter** contém o seguinte atributo:

| Atributo | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| Nome | Yes | O nome do par de valores chave. |

Para obter mais informações, consulte [configurar a UI com conteúdo dinâmico utilizando políticas personalizadas](custom-policy-ui-customization.md#configure-dynamic-custom-page-content-uri)

## <a name="technicalprofile"></a>Ficha Técnica

O elemento **TécnicoProfile** contém o seguinte atributo:

| Atributo | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| Id | Yes | O valor deve `PolicyProfile` ser. |

O **Ficheiro Técnico** contém os seguintes elementos:

| Elemento | Ocorrências | Description |
| ------- | ----------- | ----------- |
| DisplayName | 1:1 | A cadeia que contém o nome do perfil técnico. |
| Description | 0:1 | A cadeia que contém a descrição do perfil técnico. |
| Protocolo | 1:1 | O protocolo usado para a federação. |
| Metadados | 0:1 | A recolha de *Itens* de pares chave/valor utilizados pelo protocolo para comunicar com o ponto final no decurso de uma transação para configurar a interação entre a parte de confiança e outros participantes da comunidade. |
| Resultados | 1:1 | Uma lista de tipos de reclamações que são tomados como saída no perfil técnico. Cada um destes elementos contém referência a um **ClaimType** já definido na secção **ClaimsSchema** ou numa política a partir da qual este ficheiro de política herda. |
| Nomeação de AssuntosInfo | 1:1 | O nome usado em fichas. |

O elemento **protocolo** contém o seguinte atributo:

| Atributo | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| Nome | Yes | O nome de um protocolo válido suportado pelo Azure AD B2C que é utilizado como parte do perfil técnico. Valores possíveis: `OpenIdConnect` ou `SAML2` . O `OpenIdConnect` valor representa o padrão de protocolo OpenID Connect 1.0 de acordo com a especificação da fundação OpenID. O `SAML2` valor representa a norma do protocolo SAML 2.0 de acordo com a especificação OASIS. |

### <a name="metadata"></a>Metadados

Quando o protocolo é `SAML` , um elemento de metadados contém os seguintes elementos.

| Atributo | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| XmlSignatureAlgorithm | No | O método que a Azure AD B2C utiliza para assinar a Resposta SAML. Valores possíveis: `Sha256` `Sha384` , , ou `Sha512` `Sha1` . Certifique-se de configurar o algoritmo de assinatura em ambos os lados com o mesmo valor. Use apenas o algoritmo que o seu certificado suporta. Para configurar a afirmação do SAML, consulte [metadados de perfil técnico do emitente SAML](saml-issuer-technical-profile.md#metadata). |

### <a name="outputclaims"></a>Resultados

O elemento **OutputClaims** contém o seguinte elemento:

| Elemento | Ocorrências | Description |
| ------- | ----------- | ----------- |
| OutputClaim | 0:n | O nome de um tipo de reclamação esperada na lista apoiada para a política a que o partido que conta subscrever. Esta alegação serve de saída para o perfil técnico. |

O elemento **OutputClaim** contém os seguintes atributos:

| Atributo | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| ClaimTypeReferenceId | Yes | Uma referência a um **ClaimType** já definido na secção **ClaimsSchema** no ficheiro de política. |
| PadrãoValue | No | Um valor predefinido que pode ser usado se o valor da reclamação estiver vazio. |
| PartnerClaimType | No | Envia a reclamação num nome diferente, tal como configurado na definição DeTipo de Reclamação. |

### <a name="subjectnaminginfo"></a>Nomeação de AssuntosInfo

Com o elemento **SubjectNameingInfo,** controla-se o valor do sujeito simbólico:
- **JWT token** - a `sub` reivindicação. Este é um principal sobre o qual o símbolo afirma informações, como o utilizador de uma aplicação. Este valor é imutável e não pode ser reatribuído ou reutilizado. Pode ser usado para efetuar verificações de autorização seguras, como quando o token é usado para aceder a um recurso. Por predefinição, a reclamação do sujeito é povoada com o ID do objeto do utilizador no diretório. Para obter mais informações, consulte [Token, sessão e configuração de sessão única](session-behavior.md).
- **Símbolo SAML** - o `<Subject><NameID>` elemento que identifica o elemento sujeito. O formato NameId pode ser modificado.

O elemento **SubjectNamingInfo** contém o seguinte atributo:

| Atributo | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| ClaimType | Yes | Uma referência ao **PartnerClaimType**de uma reclamação de saída. As reclamações de saída devem ser definidas na política do partido dependente Da recolha **OutputClaims.** |
| Formato | No | Utilizado para as partes de confiança SAML para definir o **formato NameId** devolvido na Afirmação SAML. |

O exemplo a seguir mostra como definir uma parte de confiante OpenID Connect. A informação sobre o nome do sujeito é configurada `objectId` como:

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
    </OutputClaims>
    <SubjectNamingInfo ClaimType="sub" />
  </TechnicalProfile>
</RelyingParty>
```
O token JWT inclui a `sub` reclamação com o utilizador objectId:

```json
{
  ...
  "sub": "6fbbd70d-262b-4b50-804c-257ae1706ef2",
  ...
}
```

O exemplo a seguir mostra como definir uma festa de singing SAML. A informação do nome do sujeito é configurada como `objectId` o , e o NameId foi `format` fornecido:

```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
  <TechnicalProfile Id="PolicyProfile">
    <DisplayName>PolicyProfile</DisplayName>
    <Protocol Name="SAML2" />
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="displayName" />
      <OutputClaim ClaimTypeReferenceId="givenName" />
      <OutputClaim ClaimTypeReferenceId="surname" />
      <OutputClaim ClaimTypeReferenceId="email" />
      <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
      <OutputClaim ClaimTypeReferenceId="identityProvider" />
    </OutputClaims>
    <SubjectNamingInfo ClaimType="sub" Format="urn:oasis:names:tc:SAML:2.0:nameid-format:transient"/>
  </TechnicalProfile>
</RelyingParty>
```
