---
title: RelyingParty-Azure Active Directory B2C | Microsoft Docs
description: Especifique o elemento RelyingParty de uma política personalizada em Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 01/25/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: bf8d4889c277d59d0c42894281a89345fbf90a84
ms.sourcegitcommit: a52f17307cc36640426dac20b92136a163c799d0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/01/2019
ms.locfileid: "68716699"
---
# <a name="relyingparty"></a>RelyingParty

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

O elemento **RelyingParty** especifica o percurso do usuário a ser aplicado à solicitação atual para Azure Active Directory (Azure AD) B2C. Ele também especifica a lista de declarações que o aplicativo RP (terceira parte confiável) precisa como parte do token emitido. Um aplicativo RP, como um aplicativo Web, móvel ou de área de trabalho, chama o arquivo de política de RP. O arquivo de política de RP executa uma tarefa específica, como entrar, redefinir uma senha ou editar um perfil. Vários aplicativos podem usar a mesma política de RP e um único aplicativo pode usar várias políticas. Todos os aplicativos RP recebem o mesmo token com declarações e o usuário passa pela mesma jornada do usuário.

O exemplo a seguir mostra um elemento **RelyingParty** no arquivo de política *B2C_1A_signup_signin* :

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

O elemento opcional **RelyingParty** contém os seguintes elementos:

| Elemento | Ocorrências | Descrição |
| ------- | ----------- | ----------- |
| DefaultUserJourney | 1:1 | O percurso do usuário padrão para o aplicativo RP. |
| UserJourneyBehaviors | 0:1 | O escopo dos comportamentos de jornada do usuário. |
| TechnicalProfile | 1:1 | Um perfil técnico com suporte do aplicativo RP. O perfil técnico fornece um contrato para o aplicativo RP entrar em contato Azure AD B2C. |

## <a name="defaultuserjourney"></a>DefaultUserJourney

O `DefaultUserJourney` elemento Especifica uma referência ao identificador do percurso do usuário que geralmente é definido na política de base ou extensões. Os exemplos a seguir mostram a jornada do usuário de inscrição ou de entrada especificada no elemento **RelyingParty** :

Política de *B2C_1A_signup_signin* :

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

O elemento **DefaultUserJourney** contém o seguinte atributo:

| Atributo | Requerido | Descrição |
| --------- | -------- | ----------- |
| ReferenceId | Sim | Um identificador da jornada do usuário na política. Para obter mais informações, consulte [viagens do usuário](userjourneys.md) |

## <a name="userjourneybehaviors"></a>UserJourneyBehaviors

O elemento **UserJourneyBehaviors** contém os seguintes elementos:

| Elemento | Ocorrências | Descrição |
| ------- | ----------- | ----------- |
| SingleSignOn | 0:1 | O escopo do comportamento de sessão de logon único (SSO) de um percurso do usuário. |
| SessionExpiryType |0:1 | O comportamento de autenticação da sessão. Valores possíveis: `Rolling` ou `Absolute`. O `Rolling` valor (padrão) indica que o usuário permanece conectado, desde que o usuário esteja ativo continuamente no aplicativo. O `Absolute` valor indica que o usuário é forçado a autenticar novamente após o período especificado pelo tempo de vida da sessão do aplicativo. |
| SessionExpiryInSeconds | 0:1 | O tempo de vida do cookie de sessão B2C do Azure AD especificado como um inteiro armazenado no navegador do usuário após a autenticação bem-sucedida. |
| JourneyInsights | 0:1 | A chave de instrumentação do Aplicativo Azure insights a ser usada. |
| ContentDefinitionParameters | 0:1 | A lista de pares de valores de chave a serem acrescentados ao URI de carga de definição de conteúdo. |

### <a name="singlesignon"></a>SingleSignOn

O elemento **logon único** contém no seguinte atributo:

| Atributo | Requerido | Descrição |
| --------- | -------- | ----------- |
| Scope | Sim | O escopo do comportamento de logon único. Valores possíveis: `Suppressed`, `Tenant`, `Application`ou. `Policy` O `Suppressed` valor indica que o comportamento é suprimido. Por exemplo, no caso de uma sessão de logon único, nenhuma sessão é mantida para o usuário e o usuário sempre será solicitado a fornecer uma seleção de provedor de identidade. O `TrustFramework` valor indica que o comportamento é aplicado a todas as políticas na estrutura de confiança. Por exemplo, um usuário navegando por duas viagens de política para uma estrutura de confiança não é solicitado para uma seleção de provedor de identidade. O `Tenant` valor indica que o comportamento é aplicado a todas as políticas no locatário. Por exemplo, um usuário navegando por duas viagens de política para um locatário não é solicitado para uma seleção de provedor de identidade. O `Application` valor indica que o comportamento é aplicado a todas as políticas para o aplicativo que faz a solicitação. Por exemplo, um usuário navegando por duas viagens de política para um aplicativo não é solicitado para uma seleção de provedor de identidade. O `Policy` valor indica que o comportamento se aplica somente a uma política. Por exemplo, um usuário navegando por duas viagens de política para uma estrutura confiável é solicitado a fornecer uma seleção de provedor de identidade ao alternar entre as políticas. |
| KeepAliveInDays | Sim | Controla por quanto tempo o usuário permanece conectado. Definir o valor como 0 desativa a funcionalidade KMSI. Para obter mais informações, consulte [Mantenha-me conectado](active-directory-b2c-reference-kmsi-custom.md). |

## <a name="journeyinsights"></a>JourneyInsights

O elemento **JourneyInsights** contém os seguintes atributos:

| Atributo | Requerido | Descrição |
| --------- | -------- | ----------- |
| TelemetryEngine | Sim | O valor deve ser `ApplicationInsights`. |
| InstrumentationKey | Sim | A cadeia de caracteres que contém a chave de instrumentação para o elemento Application insights. |
| DeveloperMode | Sim | Valores possíveis: `true` ou `false`. Se `true`, Application insights agiliza a telemetria por meio do pipeline de processamento. Essa configuração é boa para o desenvolvimento, mas restrita em grandes volumes os logs de atividade detalhados são projetados apenas para auxiliar no desenvolvimento de políticas personalizadas. Não use o modo de desenvolvimento na produção. Os logs coletam todas as declarações enviadas de e para os provedores de identidade durante o desenvolvimento. Se usado na produção, o desenvolvedor assume a responsabilidade por PII (informações de identificação particular) coletadas no log do App insights que eles possuem. Esses logs detalhados são coletados somente quando esse valor é `true`definido como.|
| ClientEnabled | Sim | Valores possíveis: `true` ou `false`. Se `true`, o envia o Application insights script do lado do cliente para controlar a exibição de página e os erros do lado do cliente. |
| ServerEnabled | Sim | Valores possíveis: `true` ou `false`. Se `true`, o envia o JSON UserJourneyRecorder existente como um evento personalizado para Application insights. |
| TelemetryVersion | Sim | O valor deve ser `1.0.0`. |

Para obter mais informações, consulte [coletando logs](active-directory-b2c-troubleshoot-custom.md)

## <a name="contentdefinitionparameters"></a>ContentDefinitionParameters

Usando políticas personalizadas no Azure AD B2C, você pode enviar um parâmetro em uma cadeia de caracteres de consulta. Ao transmitir o parâmetro para o ponto final HTML, pode alterar dinamicamente o conteúdo da página. Por exemplo, pode alterar a imagem de fundo na página de inscrição ou de início de sessão do Azure AD B2C, com base num parâmetro que transmite a partir da sua aplicação Web ou móvel. Azure AD B2C passa os parâmetros de cadeia de caracteres de consulta para o arquivo HTML dinâmico, como o arquivo aspx.

O exemplo a seguir passa um parâmetro `campaignId` chamado com um valor `hawaii` de na cadeia de caracteres de consulta:

`https://login.microsoft.com/contoso.onmicrosoft.com/oauth2/v2.0/authorize?pB2C_1A_signup_signin&client_id=a415078a-0402-4ce3-a9c6-ec1947fcfb3f&nonce=defaultNonce&redirect_uri=http%3A%2F%2Fjwt.io%2F&scope=openid&response_type=id_token&prompt=login&campaignId=hawaii`

O elemento **ContentDefinitionParameters** contém o seguinte elemento:

| Elemento | Ocorrências | Descrição |
| ------- | ----------- | ----------- |
| ContentDefinitionParameter | 0: n | Uma cadeia de caracteres que contém o par chave-valor que é anexado à cadeia de caracteres de consulta de um URI de carga de definição de conteúdo. |

O elemento **ContentDefinitionParameter** contém o seguinte atributo:

| Atributo | Requerido | Descrição |
| --------- | -------- | ----------- |
| Name | Sim | O nome do par chave-valor. |

Para obter mais informações, consulte [Configurar a interface do usuário com conteúdo dinâmico usando políticas personalizadas](active-directory-b2c-ui-customization-custom-dynamic.md)

## <a name="technicalprofile"></a>TechnicalProfile

O elemento **TechnicalProfile** contém o seguinte atributo:

| Atributo | Requerido | Descrição |
| --------- | -------- | ----------- |
| ID | Sim | O valor deve ser `PolicyProfile`. |

O **TechnicalProfile** contém os seguintes elementos:

| Elemento | Ocorrências | Descrição |
| ------- | ----------- | ----------- |
| DisplayName | 0:1 | A cadeia de caracteres que contém o nome do perfil técnico que é exibido aos usuários. |
| Descrição | 0:1 | A cadeia de caracteres que contém a descrição do perfil técnico exibido aos usuários. |
| Protocol | 1:1 | O protocolo usado para a Federação. |
| Metadados | 0:1 | A coleção de *itens* de pares chave/valor utilizados pelo protocolo para se comunicar com o ponto de extremidade no decorrer de uma transação para configurar a interação entre a terceira parte confiável e outros participantes da Comunidade. |
| OutputClaims | 0:1 | Uma lista de tipos de declaração que são usados como saída no perfil técnico. Cada um desses elementos contém referência a um **ClaimType** já definido na seção **ClaimsSchema** ou em uma política da qual esse arquivo de política herda. |
| SubjectNamingInfo | 0:1 | O nome da entidade usada em tokens. |

O elemento **Protocol** contém o seguinte atributo:

| Atributo | Requerido | Descrição |
| --------- | -------- | ----------- |
| Name | Sim | O nome de um protocolo válido com suporte pelo Azure AD B2C que é usado como parte do perfil técnico. Valores possíveis: `OpenIdConnect` ou `SAML2`. O `OpenIdConnect` valor representa o padrão do protocolo OpenID Connect 1,0 de acordo com a especificação do OpenID Foundation. O `SAML2` representa o padrão de protocolo SAML 2,0 de acordo com a especificação Oasis. Não use um token SAML em produção. |

## <a name="outputclaims"></a>OutputClaims

O elemento **OutputClaims** contém o seguinte elemento:

| Elemento | Ocorrências | Descrição |
| ------- | ----------- | ----------- |
| OutputClaim | 0: n | O nome de um tipo de declaração esperado na lista com suporte para a política na qual a terceira parte confiável se inscreveu. Essa declaração serve como uma saída para o perfil técnico. |

O elemento **OutputClaim** contém os seguintes atributos:

| Atributo | Requerido | Descrição |
| --------- | -------- | ----------- |
| ClaimTypeReferenceId | Sim | Uma referência a um **ClaimType** já definida na seção **ClaimsSchema** no arquivo de política. |
| ValorPadrão | Não | Um valor padrão que pode ser usado se o valor da declaração estiver vazio. |
| PartnerClaimType | Não | Envia a declaração em um nome diferente, conforme configurado na definição de ClaimType. |

### <a name="subjectnaminginfo"></a>SubjectNamingInfo

Com o elemento **SubjectNameingInfo** , você controla o valor da entidade do token:
- **Token JWT** -a `sub` declaração. Essa é uma entidade de segurança sobre a qual o token declara informações, como o usuário de um aplicativo. Esse valor é imutável e não pode ser reatribuído ou reutilizado. Ele pode ser usado para executar verificações de autorização seguras, como quando o token é usado para acessar um recurso. Por padrão, a declaração de assunto é populada com a ID de objeto do usuário no diretório. Para obter mais informações, consulte [token, sessão e configuração de logon único](active-directory-b2c-token-session-sso.md).
- **Token SAML** -o `<Subject><NameID>` elemento que identifica o elemento Subject.

O elemento **SubjectNamingInfo** contém o seguinte atributo:

| Atributo | Requerido | Descrição |
| --------- | -------- | ----------- |
| ClaimType | Sim | Uma referência a um **PartnerClaimType**de declaração de saída. As declarações de saída devem ser definidas na coleção **OutputClaims** da política de terceira parte confiável. |

O exemplo a seguir mostra como definir uma terceira parte confiável do OpenID Connect. As informações do nome da entidade são configuradas como `objectId`:

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
O token JWT inclui a `sub` declaração com o ObjectID do usuário:

```JSON
{
  ...
  "sub": "6fbbd70d-262b-4b50-804c-257ae1706ef2",
  ...
}
```


