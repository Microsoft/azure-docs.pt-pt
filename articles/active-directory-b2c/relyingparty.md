---
title: RelyingParty - Diretório Ativo Azure B2C  Microsoft Docs
description: Especifique o elemento RelyingParty de uma política personalizada no Diretório Ativo Azure B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 02/24/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 90ac6f35cafbe63e8c6cdb77450089d00c0e3099
ms.sourcegitcommit: 668b3480cb637c53534642adcee95d687578769a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2020
ms.locfileid: "78925748"
---
# <a name="relyingparty"></a>RelyingParty

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

O elemento **RelyingParty** especifica a viagem do utilizador para fazer cumprir o pedido atual ao Diretório Ativo Azure B2C (Azure AD B2C). Especifica igualmente a lista de alegações de que a aplicação do partido que depende (RP) necessita como parte do token emitido. Uma aplicação RP, como uma aplicação web, móvel ou desktop, chama o ficheiro de política rp. O ficheiro de política de RP executa uma tarefa específica, como iniciar sessão, repor uma palavra-passe ou editar um perfil. Várias aplicações podem usar a mesma política de RP e uma única aplicação pode usar múltiplas políticas. Todas as aplicações RP recebem o mesmo sinal com reclamações, e o utilizador passa pela mesma viagem de utilizador.

O exemplo seguinte mostra um elemento **RelyingParty** no ficheiro de política *B2C_1A_signup_signin:*

```XML
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
      <SingleSignOn Scope="TrustFramework" KeepAliveInDays="7"/>
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

O elemento Opcional **RelyingParty** contém os seguintes elementos:

| Elemento | Ocorrências | Descrição |
| ------- | ----------- | ----------- |
| DefaultUserJourney | 1:1 | A viagem padrão do utilizador para a aplicação RP. |
| UserJourneyBehaviors | 0:1 | O âmbito dos comportamentos da viagem do utilizador. |
| Perfil Técnico | 1:1 | Um perfil técnico que é suportado pela aplicação RP. O perfil técnico fornece um contrato para a aplicação RP contactar o Azure AD B2C. |

## <a name="defaultuserjourney"></a>DefaultUserJourney

O elemento `DefaultUserJourney` especifica uma referência ao identificador da viagem de utilizador que é normalmente definida na política base ou extensões. Os seguintes exemplos mostram a viagem de inscrição ou inscrição no utilizador especificada no elemento **RelyingParty:**

*B2C_1A_signup_signin* política:

```XML
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn">
  ...
```

*B2C_1A_TrustFrameWorkBase* ou *B2C_1A_TrustFrameworkExtensionPolicy:*

```XML
<UserJourneys>
  <UserJourney Id="SignUpOrSignIn">
  ...
```

O elemento **DefaultUserJourney** contém o seguinte atributo:

| Atributo | Necessário | Descrição |
| --------- | -------- | ----------- |
| ReferenceId | Sim | Um identificador da viagem do utilizador na política. Para mais informações, consulte [as viagens dos utilizadores](userjourneys.md) |

## <a name="userjourneybehaviors"></a>UserJourneyBehaviors

O elemento **UserJourneyBehaviors** contém os seguintes elementos:

| Elemento | Ocorrências | Descrição |
| ------- | ----------- | ----------- |
| SingleSignOn | 0:1 | O âmbito do comportamento da sessão de inscrição única (SSO) de uma viagem de utilizador. |
| SessionExpiryType |0:1 | O comportamento de autenticação da sessão. Valores possíveis: `Rolling` ou `Absolute`. O valor `Rolling` (predefinido) indica que o utilizador permanece assinado desde que o utilizador esteja continuamente ativo na aplicação. O valor `Absolute` indica que o utilizador é obrigado a reautenticar após o período de tempo especificado pelo tempo de vida da sessão de aplicação. |
| SessionExpiryInSeconds | 0:1 | O tempo de vida do cookie de sessão do Azure AD B2C especificado como um inteiro armazenado no navegador do utilizador após autenticação bem sucedida. |
| JourneyInsights | 0:1 | A chave de instrumentação Azure Application Insights a utilizar. |
| ContentDefinitionParameters | 0:1 | A lista de pares de valor chave a anexar à carga de definição de conteúdo URI. |
|ScriptExecution| 0:1| Os modos de execução [JavaScript](javascript-samples.md) suportados. Valores possíveis: `Allow` ou `Disallow` (predefinido).

### <a name="singlesignon"></a>SingleSignOn

O elemento **SingleSignOn** contém no seguinte atributo:

| Atributo | Necessário | Descrição |
| --------- | -------- | ----------- |
| Âmbito | Sim | O âmbito do comportamento de inscrição única. Valores possíveis: `Suppressed`, `Tenant`, `Application`ou `Policy`. O valor `Suppressed` indica que o comportamento é suprimido. Por exemplo, no caso de uma única sessão de sessão de sessão de sinalização, não se mantém nenhuma sessão para o utilizador e o utilizador é sempre solicitado para uma seleção do fornecedor de identidade. O valor `TrustFramework` indica que o comportamento é aplicado a todas as políticas no quadro de confiança. Por exemplo, um utilizador que navega através de duas viagens políticas para um quadro de confiança não é solicitado para uma seleção do fornecedor de identidade. O valor `Tenant` indica que o comportamento é aplicado a todas as políticas do arrendatário. Por exemplo, um utilizador que navega através de duas viagens políticas para um inquilino não é solicitado para uma seleção de fornecedor de identidade. O valor `Application` indica que o comportamento é aplicado a todas as políticas para a aplicação que faz o pedido. Por exemplo, um utilizador que navega através de duas viagens políticas para uma aplicação não é solicitado para uma seleção de fornecedor de identidade. O valor `Policy` indica que o comportamento só se aplica a uma política. Por exemplo, um utilizador que navega através de duas viagens políticas para um quadro de confiança é solicitado para uma seleção de fornecedorde identidade ao alternar entre políticas. |
| KeepAliveInDays | Sim | Controla o tempo em que o utilizador permanece inscrito. Definir o valor para 0 desliga a funcionalidade KMSI. Para mais informações, consulte [Mantenha-me inscrito em](custom-policy-keep-me-signed-in.md). |
|EnforceIdTokenHintOnLogout| Não|  Forçar a passar um token de identificação previamente emitido para o ponto final de início de sessão como uma dica sobre a atual sessão autenticada do utilizador final com o cliente. Valores possíveis: `false` (predefinido) ou `true`. Para mais informações, consulte o acesso à Web com o [OpenID Connect](openid-connect.md).  |


## <a name="journeyinsights"></a>JourneyInsights

O elemento **JourneyInsights** contém os seguintes atributos:

| Atributo | Necessário | Descrição |
| --------- | -------- | ----------- |
| TelemetriaEngine | Sim | O valor deve ser `ApplicationInsights`. |
| Chave de Instrumentação | Sim | A cadeia que contém a chave de instrumentação para o elemento de insights de aplicação. |
| DeveloperMode | Sim | Valores possíveis: `true` ou `false`. Se `true`, a Application Insights acelera a telemetria através do gasoduto de processamento. Esta definição é boa para o desenvolvimento, mas limitada em volumes elevados Os registos de atividade sinuosos são concebidos apenas para ajudar no desenvolvimento de políticas personalizadas. Não utilize o modo de desenvolvimento na produção. Os registos recolhem todas as reclamações enviadas de e para os fornecedores de identidade durante o desenvolvimento. Se utilizado na produção, o desenvolvedor assume a responsabilidade pela PII (Informação Particularmente identificável) recolhida no registo app Insights que detém. Estes registos detalhados só são recolhidos quando este valor está definido para `true`.|
| ClienteHabilitado | Sim | Valores possíveis: `true` ou `false`. Se `true`, envie o script do lado do cliente da Application Insights para rastrear a visualização da página e erros do lado do cliente. |
| ServerEnabled | Sim | Valores possíveis: `true` ou `false`. Se `true`, envia o UserJourneyRecorder JSON existente como um evento personalizado para Insights de Aplicação. |
| Versão telemetria | Sim | O valor deve ser `1.0.0`. |

Para mais informações, consulte [Registos de Recolha](troubleshoot-with-application-insights.md)

## <a name="contentdefinitionparameters"></a>ContentDefinitionParameters

Ao utilizar políticas personalizadas em Azure AD B2C, pode enviar um parâmetro numa corda de consulta. Ao transmitir o parâmetro para o ponto final HTML, pode alterar dinamicamente o conteúdo da página. Por exemplo, pode alterar a imagem de fundo na página de inscrição ou de início de sessão do Azure AD B2C, com base num parâmetro que transmite a partir da sua aplicação Web ou móvel. Azure AD B2C passa os parâmetros de corda de consulta para o seu ficheiro HTML dinâmico, como o ficheiro aspx.

O exemplo seguinte passa um parâmetro chamado `campaignId` com um valor de `hawaii` na corda de consulta:

`https://login.microsoft.com/contoso.onmicrosoft.com/oauth2/v2.0/authorize?pB2C_1A_signup_signin&client_id=a415078a-0402-4ce3-a9c6-ec1947fcfb3f&nonce=defaultNonce&redirect_uri=http%3A%2F%2Fjwt.io%2F&scope=openid&response_type=id_token&prompt=login&campaignId=hawaii`

O elemento **ContentDefinitionParameters** contém o seguinte elemento:

| Elemento | Ocorrências | Descrição |
| ------- | ----------- | ----------- |
| ContentDefinitionParameter | 0:n | Uma cadeia que contém o par de valor chave que é anexado à cadeia de consulta de uma carga de definição de conteúdo URI. |

O elemento **ContentDefinitionParameter** contém o seguinte atributo:

| Atributo | Necessário | Descrição |
| --------- | -------- | ----------- |
| Nome | Sim | O nome do par de valor chave. |

Para mais informações, consulte [Configure o UI com conteúdo dinâmico utilizando políticas personalizadas](custom-policy-ui-customization.md#configure-dynamic-custom-page-content-uri)

## <a name="technicalprofile"></a>Perfil Técnico

O elemento **Perfil Técnico** contém o seguinte atributo:

| Atributo | Necessário | Descrição |
| --------- | -------- | ----------- |
| Id | Sim | O valor deve ser `PolicyProfile`. |

O **Perfil Técnico** contém os seguintes elementos:

| Elemento | Ocorrências | Descrição |
| ------- | ----------- | ----------- |
| DisplayName | 1:1 | A corda que contém o nome do perfil técnico. |
| Descrição | 0:1 | A corda que contém a descrição do perfil técnico. |
| Protocolo | 1:1 | O protocolo usado para a federação. |
| Metadados | 0:1 | A coleção de *pares* chave/valor utilizados pelo protocolo para comunicar com o ponto final no decurso de uma transação para configurar a interação entre a parte que depende e outros participantes da comunidade. |
| OutputClaims | 1:1 | Uma lista de tipos de reclamação que são tomados como saída no perfil técnico. Cada um destes elementos contém referência a um **ClaimType** já definido na secção **ClaimsSchema** ou numa política a partir da qual este ficheiro de política herda. |
| SubjectNamingInfo | 1:1 | O nome do assunto usado em fichas. |

O elemento **Protocolo** contém o seguinte atributo:

| Atributo | Necessário | Descrição |
| --------- | -------- | ----------- |
| Nome | Sim | O nome de um protocolo válido suportado pelo Azure AD B2C que é utilizado como parte do perfil técnico. Valores possíveis: `OpenIdConnect` ou `SAML2`. O valor `OpenIdConnect` representa a norma de protocolo OpenID Connect 1.0 de acordo com a especificação da fundação OpenID. O `SAML2` representa a norma do protocolo SAML 2.0 de acordo com a especificação OASIS. Não utilize um símbolo SAML na produção. |

## <a name="outputclaims"></a>OutputClaims

O elemento **OutputClaims** contém o seguinte elemento:

| Elemento | Ocorrências | Descrição |
| ------- | ----------- | ----------- |
| OutputClaim | 0:n | O nome de um tipo de reclamação esperado na lista apoiada para a política a que o partido que confia subscreve. Esta alegação serve como uma saída para o perfil técnico. |

O elemento **OutputClaim** contém os seguintes atributos:

| Atributo | Necessário | Descrição |
| --------- | -------- | ----------- |
| ClaimTypeReferenceId | Sim | Uma referência a um **ClaimType** já definido na secção **ClaimsSchema** no ficheiro de política. |
| Valor Padrão | Não | Um valor predefinido que pode ser usado se o valor da reclamação estiver vazio. |
| Tipo de reclamação de parceiros | Não | Envia a reclamação num nome diferente, tal como configurado na definição Do Tipo de Reclamação. |

### <a name="subjectnaminginfo"></a>SubjectNamingInfo

Com o elemento **SubjectNameingInfo,** controla o valor do assunto simbólico:
- **JWT token** - a reivindicação `sub`. Trata-se de um principal sobre o qual o símbolo afirma informações, como o utilizador de uma aplicação. Este valor é imutável e não pode ser reatribuído ou reutilizado. Pode ser utilizado para efetuar controlos de autorização seguros, como quando o símbolo é usado para aceder a um recurso. Por predefinição, a reclamação do sujeito é povoada com o ID do objeto do utilizador no diretório. Para mais informações, consulte [Token, sessão e configuração de inscrição única](session-behavior.md).
- **Ficha SAML** - o elemento `<Subject><NameID>` que identifica o elemento do sujeito.

O elemento **SubjectNamingInfo** contém o seguinte atributo:

| Atributo | Necessário | Descrição |
| --------- | -------- | ----------- |
| Tipo de reclamação | Sim | Uma referência ao **PartnerClaimType**de uma reclamação de saída . As reivindicações de saída devem ser definidas na **coleção** de resultados da política partidária de base. |

O exemplo que se segue mostra como definir uma festa de fiação OpenID Connect. A informação sobre o nome do assunto é configurada como o `objectId`:

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
    </OutputClaims>
    <SubjectNamingInfo ClaimType="sub" />
  </TechnicalProfile>
</RelyingParty>
```
O símbolo JWT inclui a reclamação `sub` com o objetivo do utilizadorId:

```JSON
{
  ...
  "sub": "6fbbd70d-262b-4b50-804c-257ae1706ef2",
  ...
}
```
