---
title: RelyingParty - Azure Active Directory B2C | Documentos da Microsoft
description: Especifique o elemento de RelyingParty de uma política personalizada no Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 01/25/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 2b5e9bfe6eaa9b84e259d941760792635a2994f4
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66512845"
---
# <a name="relyingparty"></a>RelyingParty

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

O **RelyingParty** elemento Especifica o percurso do utilizador para impor a solicitação atual para o Azure Active Directory (Azure AD) B2C. Também especifica a lista de afirmações que precisa da aplicação da entidade confiadora de terceiros (RP) como parte do token emitido. Um aplicativo da RP, tais como aplicações web, móveis ou ambiente de trabalho, chama o arquivo de diretiva da RP. O ficheiro de política RP executa uma tarefa específica, como iniciar sessão, repor uma palavra-passe ou editar um perfil. Vários aplicativos podem usar a mesma diretiva RP e um único aplicativo pode usar várias políticas. Todos os aplicativos de RP recebem o mesmo token com declarações e o utilizador atravessa o mesma percurso do utilizador.

A exemplo a seguir mostra um **RelyingParty** elemento no *B2C_1A_signup_signin* ficheiro de política:

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

O opcional **RelyingParty** elemento contém os seguintes elementos:

| Elemento | Ocorrências | Descrição |
| ------- | ----------- | ----------- |
| DefaultUserJourney | 1:1 | Percurso de utilizador predefinido para o aplicativo da RP. |
| UserJourneyBehaviors | 0:1 | O âmbito dos comportamentos de percurso do utilizador. |
| TechnicalProfile | 1:1 | Um perfil técnico que é suportado pelo aplicativo da RP. O perfil técnico fornece um contrato para o aplicativo da RP contactar o Azure AD B2C. |

## <a name="defaultuserjourney"></a>DefaultUserJourney

O `DefaultUserJourney` elemento Especifica uma referência para o identificador do percurso do utilizador que normalmente, é definido na política de Base ou extensões. Os exemplos seguintes mostram o percurso do utilizador de inscrição ou início de sessão especificado na **RelyingParty** elemento:

*B2C_1A_signup_signin* política:

```XML
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn">
  ...
```

*B2C_1A_TrustFrameWorkBase* ou *B2C_1A_TrustFrameworkExtensionPolicy*:

```XML
<UserJourneys>
  <UserJourney Id="SignUpOrSignIn">
  ...
```

O **DefaultUserJourney** elemento contém o atributo seguinte:

| Atributo | Necessário | Descrição |
| --------- | -------- | ----------- |
| ReferenceId | Sim | Um identificador do percurso do utilizador na política. Para obter mais informações, consulte [jornadas de utilizador](userjourneys.md) |

## <a name="userjourneybehaviors"></a>UserJourneyBehaviors

O **UserJourneyBehaviors** elemento contém os seguintes elementos:

| Elemento | Ocorrências | Descrição |
| ------- | ----------- | ----------- |
| SingleSignOn | 0:1 | O âmbito do único início de sessão (SSO) sessão comportamento de um percurso do utilizador. |
| SessionExpiryType |0:1 | O comportamento de autenticação da sessão. Valores possíveis: `Rolling` ou `Absolute`. O `Rolling` valor (padrão) indica que o usuário permaneça com sessão iniciada, desde que o utilizador está continuamente ativo no aplicativo. O `Absolute` valor indica que o utilizador é forçado a autenticar-se após o período de tempo especificado por sessão do aplicativo tempo de vida. |
| SessionExpiryInSeconds | 0:1 | A vida útil do cookie de sessão do Azure AD B2C especificado como um número inteiro é armazenado no navegador do usuário após a autenticação com êxito. |
| JourneyInsights | 0:1 | A chave de instrumentação do Application Insights do Azure a ser utilizado. |
| ContentDefinitionParameters | 0:1 | A lista de pares chave-valor acrescentado para a carga de definição de conteúdo URI. |

### <a name="singlesignon"></a>SingleSignOn

O **SingleSignOn** elemento contém o atributo seguinte:

| Atributo | Necessário | Descrição |
| --------- | -------- | ----------- |
| Scope | Sim | O âmbito do comportamento de início de sessão único. Valores possíveis: `Suppressed`, `Tenant`, `Application`, ou `Policy`. O `Suppressed` valor indica que o comportamento suprimido. Por exemplo, no caso de uma única sessão de logon, nenhuma sessão é mantido para o utilizador e sempre é pedido ao utilizador para uma seleção de fornecedor de identidade. O `TrustFramework` valor indica que é aplicado o comportamento para todas as políticas do Framework de confiança. Por exemplo, um usuário navegar pelos dois jornadas de política para uma estrutura de confiança não é pedido para uma seleção de fornecedor de identidade. O `Tenant` valor indica que o comportamento é aplicado a todas as políticas no inquilino. Por exemplo, um usuário navegar pelos dois jornadas de política para um inquilino não é pedido para uma seleção de fornecedor de identidade. O `Application` valor indica que o comportamento é aplicado a todas as políticas para a aplicação que efetua o pedido. Por exemplo, um usuário navegar pelos dois jornadas de política para um aplicativo não é pedido para uma seleção de fornecedor de identidade. O `Policy` valor indica que o comportamento aplica-se apenas a uma política. Por exemplo, um usuário navegar pelos dois jornadas de política para uma estrutura de confiança é pedido para uma seleção de fornecedor de identidade quando se alterna entre as políticas. |
| KeepAliveInDays | Sim | controla o tempo que o usuário permanece com sessão iniciada. Definindo o valor como 0 desliga funcionalidade KMSI. Para obter mais informações, consulte [manter sessão iniciada](active-directory-b2c-reference-kmsi-custom.md). |

## <a name="journeyinsights"></a>JourneyInsights

O **JourneyInsights** elemento contém os seguintes atributos:

| Atributo | Necessário | Descrição |
| --------- | -------- | ----------- |
| TelemetryEngine | Sim | O valor tem de ser `ApplicationInsights`. | 
| InstrumentationKey | Sim | A cadeia de caracteres que contém a chave de instrumentação para o elemento do application insights. |
| DeveloperMode | Sim | Valores possíveis: `true` ou `false`. Se `true`, Application Insights agiliza a telemetria através do pipeline de processamento. Esta definição é bom para o desenvolvimento, mas restrito em grandes volumes, os registos de atividade detalhado destinam-se apenas para ajudar no desenvolvimento de políticas personalizadas. Não utilize o modo de desenvolvimento em produção. Registos de recolhem todas as afirmações enviadas de e para os fornecedores de identidade durante o desenvolvimento. Se usado na produção, o desenvolvedor assume a responsabilidade de PII (em privado informações de identificação) recolhida no registo do App Insights que possuem. Estes registos detalhados apenas são recolhidos quando este valor está definido como `true`.|
| ClientEnabled | Sim | Valores possíveis: `true` ou `false`. Se `true`, envia o script de lado do cliente do Application Insights para rastrear erros de vista e do lado do cliente da página. | 
| ServerEnabled | Sim | Valores possíveis: `true` ou `false`. Se `true`, envia o JSON de UserJourneyRecorder existente como um evento personalizado para o Application Insights. | 
| TelemetryVersion | Sim | O valor tem de ser `1.0.0`. | 

Para obter mais informações, consulte [recolher registos](active-directory-b2c-troubleshoot-custom.md)

## <a name="contentdefinitionparameters"></a>ContentDefinitionParameters

Ao utilizar políticas personalizadas no Azure AD B2C, pode enviar um parâmetro de uma cadeia de consulta. Ao transmitir o parâmetro para o ponto final HTML, pode alterar dinamicamente o conteúdo da página. Por exemplo, pode alterar a imagem de fundo na página de inscrição ou de início de sessão do Azure AD B2C, com base num parâmetro que transmite a partir da sua aplicação Web ou móvel. O Azure AD B2C passa a consulta parâmetros de cadeia de caracteres para seu arquivo HTML dinâmico, por exemplo, o arquivo aspx. 

O exemplo seguinte transmite um parâmetro denominado `campaignId` com um valor de `hawaii` na cadeia de consulta:

`https://login.microsoft.com/contoso.onmicrosoft.com/oauth2/v2.0/authorize?pB2C_1A_signup_signin&client_id=a415078a-0402-4ce3-a9c6-ec1947fcfb3f&nonce=defaultNonce&redirect_uri=http%3A%2F%2Fjwt.io%2F&scope=openid&response_type=id_token&prompt=login&campaignId=hawaii`

O **ContentDefinitionParameters** elemento contém o seguinte elemento:

| Elemento | Ocorrências | Descrição |
| ------- | ----------- | ----------- |
| ContentDefinitionParameter | 0:n | Uma cadeia que contém o par chave-valor que é acrescentado à cadeia de consulta de um URI de carga de definição de conteúdo. |

O **ContentDefinitionParameter** elemento contém o atributo seguinte:

| Atributo | Necessário | Descrição |
| --------- | -------- | ----------- |
| Name | Sim | O nome do par de chave-valor. |

Para obter mais informações, consulte [configurar a interface do Usuário com conteúdo dinâmico ao utilizar políticas personalizadas](active-directory-b2c-ui-customization-custom-dynamic.md)

## <a name="technicalprofile"></a>TechnicalProfile

O **TechnicalProfile** elemento contém o atributo seguinte:

| Atributo | Necessário | Descrição |
| --------- | -------- | ----------- | 
| Id | Sim | O valor tem de ser `PolicyProfile`. |

O **TechnicalProfile** contém os seguintes elementos:

| Elemento | Ocorrências | Descrição |
| ------- | ----------- | ----------- |
| displayName | 0:1 | A cadeia de caracteres que contém o nome do perfil técnico que é apresentado aos utilizadores. |
| Descrição | 0:1 | A cadeia de caracteres que contém a descrição do perfil técnico que é apresentado aos utilizadores. |
| Protocol | 1:1 | O protocolo utilizado para a Federação. |
| Metadados | 0:1 | A coleção de *Item* de pares chave/valor utilizadas pelo protocolo de comunicação com o ponto final no decorrer de uma transação para configurar a interação entre a entidade confiadora e outros participantes da Comunidade. |
| OutputClaims | 0:1 | Uma lista de tipos de afirmação que são executadas como saída no perfil técnico. Cada um desses elementos contém a referência a um **ClaimType** já definidos no **ClaimsSchema** secção ou numa política a partir do qual herda este ficheiro de política. |
| SubjectNamingInfo | 0:1 | O nome de assunto utilizado nos tokens. |

O **protocolo** elemento contém o atributo seguinte:

| Atributo | Necessário | Descrição |
| --------- | -------- | ----------- |
| Name | Sim | O nome de um protocolo válido suportado pelo Azure AD B2C, que é utilizado como parte do perfil técnico. Valores possíveis: `OpenIdConnect` ou `SAML2`. O `OpenIdConnect` valor representa o padrão de protocolo do OpenID Connect 1.0 de acordo com a especificação do OpenID foundation. O `SAML2` representa o padrão de protocolo SAML 2.0 de acordo com a especificação OASIS. Não utilize um token SAML em produção. |

## <a name="outputclaims"></a>OutputClaims

O **OutputClaims** elemento contém o seguinte elemento:

| Elemento | Ocorrências | Descrição |
| ------- | ----------- | ----------- |
| OutputClaim | 0:n | O nome de um tipo de afirmação esperado na lista suportada para a política para que a entidade confiadora subscreve. Esta afirmação serve como uma saída para o perfil técnico. |

O **OutputClaim** elemento contém os seguintes atributos:

| Atributo | Necessário | Descrição |
| --------- | -------- | ----------- |
| ClaimTypeReferenceId | Sim | Uma referência a um **ClaimType** já definidos no **ClaimsSchema** seção no arquivo de política. |
| defaultValue | Não | Um valor predefinido que pode ser utilizado se o valor de afirmação está vazio. |
| PartnerClaimType | Não | Envia a afirmação de um nome diferente, conforme configurado na definição do ClaimType. |

### <a name="subjectnaminginfo"></a>SubjectNamingInfo

Com o **SubjectNameingInfo** elemento, controla o valor do requerente do token:
- **JWT token** – a `sub` de afirmação. Esta é uma entidade de segurança sobre o qual o token declara informações, como o utilizador de um aplicativo. Este valor é imutável e não pode ser reatribuído ou reutilizado. Ele pode ser usado para realizar verificações de autorização seguro como, por exemplo, quando o token é utilizado para aceder a um recurso. Por predefinição, a afirmação do requerente é preenchida com o ID de objeto do utilizador no diretório. Para obter mais informações, consulte [Token, sessão e configuração de início de sessão único](active-directory-b2c-token-session-sso.md).
- **SAML token** – a `<Subject><NameID>` elemento que identifica o elemento de assunto.

O **SubjectNamingInfo** elemento contém o atributo seguinte:

| Atributo | Necessário | Descrição |
| --------- | -------- | ----------- |
| ClaimType | Sim | Uma referência a uma declaração de saída **PartnerClaimType**. A saída de afirmações tem de ser definido na política de entidade confiadora de terceiros **OutputClaims** coleção. |

O exemplo seguinte mostra como definir um OpenId Connect de entidade confiadora. As informações de nome de requerente está configurada como o `objectId`:

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
O token JWT inclui o `sub` a afirmação com o objectId do utilizador:

```JSON
{
  ...
  "sub": "6fbbd70d-262b-4b50-804c-257ae1706ef2",
  ...
}
```


