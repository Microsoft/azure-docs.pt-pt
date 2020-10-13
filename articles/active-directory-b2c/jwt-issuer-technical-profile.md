---
title: Defina um perfil técnico para um emitente JWT numa política personalizada
titleSuffix: Azure AD B2C
description: Defina um perfil técnico para um emitente JSON web token (JWT) numa política personalizada no Azure Ative Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 10/12/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: b3ad9c5d19d5d24154a8a63bfc412d6bbfdc1d8b
ms.sourcegitcommit: a2d8acc1b0bf4fba90bfed9241b299dc35753ee6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/12/2020
ms.locfileid: "91949229"
---
# <a name="define-a-technical-profile-for-a-jwt-token-issuer-in-an-azure-active-directory-b2c-custom-policy"></a>Defina um perfil técnico para um emitente de ficha jWT numa política personalizada do Azure Ative Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

O Azure Ative Directory B2C (Azure AD B2C) emite vários tipos de fichas de segurança à medida que processa cada fluxo de autenticação. Um perfil técnico para um emissor de token JWT emite um símbolo JWT que é devolvido à aplicação do partido. Normalmente, este perfil técnico é o último passo de orquestração na jornada do utilizador.

## <a name="protocol"></a>Protocolo

O **atributo nome** do elemento **Protocolo** tem de ser definido para `OpenIdConnect` . Desacorda o elemento **OutputTokenFormat** para `JWT` .

O exemplo a seguir mostra um perfil técnico `JwtIssuer` para:

```xml
<TechnicalProfile Id="JwtIssuer">
  <DisplayName>JWT Issuer</DisplayName>
  <Protocol Name="None" />
  <OutputTokenFormat>JWT</OutputTokenFormat>
  <Metadata>
    <Item Key="client_id">{service:te}</Item>
    <Item Key="issuer_refresh_token_user_identity_claim_type">objectId</Item>
    <Item Key="SendTokenResponseBodyWithJsonNumbers">true</Item>
  </Metadata>
  <CryptographicKeys>
    <Key Id="issuer_secret" StorageReferenceId="B2C_1A_TokenSigningKeyContainer" />
    <Key Id="issuer_refresh_token_key" StorageReferenceId="B2C_1A_TokenEncryptionKeyContainer" />
  </CryptographicKeys>
  <UseTechnicalProfileForSessionManagement ReferenceId="SM-jwt-issuer" />
</TechnicalProfile>
```

## <a name="input-output-and-persist-claims"></a>Entradas, saídas e reivindicações persistentes

Os **elementos InputClaims,** **OutputClaims**e **PersistClaims** estão vazios ou ausentes. Os **elementos InutputClaimsTransformations** e **OutputClaimsTransformations** também estão ausentes.

## <a name="metadata"></a>Metadados

| Atributo | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| issuer_refresh_token_user_identity_claim_type | Sim | A alegação que deve ser usada como reivindicação de identidade de utilizador dentro dos códigos de autorização OAuth2 e fichas de atualização. Por predefinição, deverá defini-lo para `objectId` , a menos que especifique um tipo de reclamação SubjectNamingInfo diferente. |
| SendTokenResponseBodyWithJsonNumbers | Não | Sempre definido para `true` . Para o formato legado onde os valores numéricos são dados como cordas em vez de números JSON, definido para `false` . Este atributo é necessário para clientes que tenham assumido uma dependência de uma implementação anterior que devolveu propriedades como cordas. |
| token_lifetime_secs | Não | Acesso a vidas simbólicas. A vida útil do símbolo portador da OAuth 2.0 usado para ter acesso a um recurso protegido. O predefinido é de 3.600 segundos (1 hora). O mínimo (inclusivo) é de 300 segundos (5 minutos). O máximo (inclusive) é de 86.400 segundos (24 horas). |
| id_token_lifetime_secs | Não | ID simbólica vida inteira. O predefinido é de 3.600 segundos (1 hora). O mínimo (inclusivo) é de 300 segundos (5 minutos). O máximo (inclusive) é de segundos 86.400 (24 horas). |
| refresh_token_lifetime_secs | Não | Refrescar vidas simbólicas. O prazo máximo antes do qual um token de atualização pode ser usado para adquirir um novo token de acesso, se a sua aplicação tivesse sido concedida o âmbito offline_access. O padrão é de 120.9600 segundos (14 dias). O mínimo (inclusive) é de 86.400 segundos (24 horas). O máximo (inclusive) é de 7.776.000 segundos (90 dias). |
| rolling_refresh_token_lifetime_secs | Não | Refrescar a vida útil da janela deslizante. Após este período de tempo, o utilizador é obrigado a reautenticar, independentemente do período de validade do mais recente token de atualização adquirido pela aplicação. Se não quiser impor uma janela deslizante, desapece o valor de allow_infinite_rolling_refresh_token para `true` . O padrão é de 7.776.000 segundos (90 dias). O mínimo (inclusive) é de 86.400 segundos (24 horas). O máximo (inclusive) é de 31.536.000 segundos (365 dias). |
| allow_infinite_rolling_refresh_token | Não | Se estiver `true` definido, a janela de deslizamento de torção de torção de atualização nunca expira. |
| EmissãoClaimPattern | Não | Controla a reivindicação do Emitente (iss). Um dos valores:<ul><li>AuthorityAndTenantGuid - A alegação do ISS inclui o seu nome de domínio, como `login.microsoftonline` ou , e o seu `tenant-name.b2clogin.com` identificador de inquilino https: \/ /login.microsoftonline.com/00000000-0000-0000-0000-000000000000/v2.0/</li><li>AuthorityWithTfp - A alegação do ISS inclui o seu nome de domínio, como `login.microsoftonline` `tenant-name.b2clogin.com` ou, o seu identificador de inquilino e o nome da política do seu partido. https: \/ /login.microsoftonline.com/tfp/00000000-0000-0000-0000-000000000000/b2c_1a_tp_sign-up-or-sign-in/v2.0/</li></ul> Valor predefinido: AuthorityAndTenantGuid |
| AutenticaçãoContextReferenceClaimPattern | Não | Controla o valor da `acr` reclamação.<ul><li>Nenhum - Azure AD B2C não emite a alegação acr</li><li>PolicyId - a `acr` alegação contém o nome da apólice</li></ul>As opções para a definição deste valor são a TFP (política de enquadramento fiduciário) e a ACR (referência de contexto de autenticação). Recomenda-se definir este valor à TFP, para definir o valor, garantir o `<Item>` que existe e o valor é `Key="AuthenticationContextReferenceClaimPattern"` `None` . Na sua política partidária, adicione `<OutputClaims>` item, adicione este elemento `<OutputClaim ClaimTypeReferenceId="trustFrameworkPolicy" Required="true" DefaultValue="{policy}" />` . Certifique-se também de que a sua política contém o tipo de reclamação `<ClaimType Id="trustFrameworkPolicy">   <DisplayName>trustFrameworkPolicy</DisplayName>     <DataType>string</DataType> </ClaimType>` |
|RefreshTokenUserJourneyId| Não | O identificador de uma viagem de utilizador que deve ser executada durante a [atualização de um](authorization-code-flow.md#4-refresh-the-token) pedido de POST de acesso ao `/token` ponto final. |

## <a name="cryptographic-keys"></a>Chaves criptográficas

O elemento CryptographicKeys contém os seguintes atributos:

| Atributo | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| issuer_secret | Sim | O certificado X509 (conjunto de teclas RSA) para usar para assinar o token JWT. Esta é a `B2C_1A_TokenSigningKeyContainer` chave que configura em Começar com políticas [personalizadas.](custom-policy-get-started.md) |
| issuer_refresh_token_key | Sim | O certificado X509 (conjunto de teclas RSA) para usar para encriptar o token de atualização. Configuraste a `B2C_1A_TokenEncryptionKeyContainer` chave em Começar com políticas [personalizadas](custom-policy-get-started.md) |

## <a name="session-management"></a>Gestão de sessões

Para configurar as sessões Azure AD B2C entre Azure AD B2C e uma aplicação de partido de confiança, no atributo do `UseTechnicalProfileForSessionManagement` elemento, adicione uma referência à sessão [OAuthSSOSessionProvider](custom-policy-reference-sso.md#oauthssosessionprovider) SSO.














