---
title: Definir um perfil técnico para um emissor de tokens JWT numa política personalizada no Azure Active Directory B2C | Documentos da Microsoft
description: Defina um perfil técnico para um emissor de tokens JWT numa política personalizada no Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 10/30/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 247ebdc8156453062eefe6738c5c281d393a9923
ms.sourcegitcommit: 70550d278cda4355adffe9c66d920919448b0c34
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2019
ms.locfileid: "58436059"
---
# <a name="define-a-technical-profile-for-a-jwt-token-issuer-in-an-azure-active-directory-b2c-custom-policy"></a>Definir um perfil técnico para um emissor de tokens JWT em políticas personalizadas do Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

O Azure Active Directory (Azure AD) B2C emite vários tipos de tokens de segurança à medida que processa cada fluxo de autenticação. Um perfil técnico para um emissor de tokens JWT emite um token JWT, que é devolvido para a aplicação da entidade confiadora de terceiros. Normalmente, este perfil técnico é o último passo de orquestração no percurso do utilizador.

## <a name="protocol"></a>Protocolo

O **Name** atributo da **protocolo** elemento tem de ser definido como `None`. Definir o **OutputTokenFormat** elemento `JWT`.

O exemplo seguinte mostra um perfil técnico para `JwtIssuer`:

```XML
<TechnicalProfile Id="JwtIssuer">
  <DisplayName>JWT Issuer</DisplayName>
  <Protocol Name="None" />
  <OutputTokenFormat>JWT</OutputTokenFormat>
  ...
</TechnicalProfile>
```
 
## <a name="input-output-and-persist-claims"></a>De entrada, saída e manter afirmações

O **InputClaims**, **OutputClaims**, e **PersistClaims** elementos estão vazios ou ausentes. O **InutputClaimsTransformations** e **OutputClaimsTransformations** elementos também estão ausentes.

## <a name="metadata"></a>Metadados

| Atributo | Necessário | Descrição |
| --------- | -------- | ----------- |
| issuer_refresh_token_user_identity_claim_type | Sim | A afirmação que deve ser utilizada como a identidade de usuário solicitadas em códigos de autorização de OAuth2 e tokens de atualização. Por padrão, deve defini-lo como `objectId`, a menos que especifique o tipo de afirmação de um SubjectNamingInfo diferente. | 
| SendTokenResponseBodyWithJsonNumbers | Não | Sempre definido como `true`. Para o formato herdado em que recebem valores numéricos como cadeias de caracteres em vez de números JSON, definido como `false`. Este atributo é necessário para os clientes que tenham obtido uma dependência numa implementação anterior que devolveu essas propriedades como cadeias de caracteres. | 
| token_lifetime_secs | Não | Durações de token de acesso. A duração do token de portador OAuth 2.0 utilizado para aceder a um recurso protegido. A predefinição é de 3600 segundos (1 hora). O mínimo (inclusive) é de 300 segundos (5 minutos). O máximo (inclusive) é de 86.400 segundos (24 horas). | 
| id_token_lifetime_secs | Não | Durações de token de ID. A predefinição é de 3600 segundos (1 hora). O mínimo (inclusive) é de 300 segundos (5 minutos). O máximo (inclusive) é segundos 86,400 (24 horas). | 
| refresh_token_lifetime_secs | Não | Durações de token de atualização. O período de tempo máximo antes do qual um token de atualização pode ser utilizado para adquirir um novo token de acesso, se seu aplicativo tenha recebido o âmbito de offline_access. A predefinição é 120,9600 segundos (14 dias). O mínimo (inclusive) é de 86.400 segundos (24 horas). O máximo (inclusive) é 7,776,000 segundos (90 dias). | 
| rolling_refresh_token_lifetime_secs | Não | Atualize token duração da janela deslizante. Após este período de tempo decorrido o utilizador é forçado a autenticar, independentemente do período de validade mais recente do token atualizado adquirido pela aplicação. Se não pretender impor uma duração de janela deslizante, defina o valor da allow_infinite_rolling_refresh_token para `true`. A predefinição é 7,776,000 segundos (90 dias). O mínimo (inclusive) é de 86.400 segundos (24 horas). O máximo (inclusive) é 31,536,000 segundos (365 dias). | 
| allow_infinite_rolling_refresh_token | Não | Se definido como `true`, o token de atualização janela deslizante duração nunca expira. |
| IssuanceClaimPattern | Sim | Controla a afirmação do emissor (iss). Um dos valores:<ul><li>AuthorityAndTenantGuid - a afirmação de iss inclui seu nome de domínio, tal como `login.microsoftonline` ou `tenant-name.b2clogin.com`e o identificador do inquilino https://login.microsoftonline.com/00000000-0000-0000-0000-000000000000/v2.0/</li><li>AuthorityWithTfp - a afirmação de iss inclui seu nome de domínio, tal como `login.microsoftonline` ou `tenant-name.b2clogin.com`, o seu inquilino do identificador e o nome da sua política terceiros entidade confiadora. https://login.microsoftonline.com/tfp/00000000-0000-0000-0000-000000000000/b2c_1a_tp_sign-up-or-sign-in/v2.0/</li></ul> | 
| AuthenticationContextReferenceClaimPattern | Não | Controles a `acr` valor da afirmação.<ul><li>Nenhum - Azure AD B2C não emita a afirmação do acr</li><li>PolicyId - o `acr` afirmação contém o nome da política</li></ul>As opções para a definição deste valor são TFP (política de estrutura de confiança) e ACR (referência de contexto de autenticação). Recomenda-se definir este valor para TFP, para definir o valor, certifique-se a `<Item>` com o `Key="AuthenticationContextReferenceClaimPattern"` existe e o valor é `None`. Na sua política de terceiros entidade confiadora, adicione `<OutputClaims>` de item, adicione este elemento `<OutputClaim ClaimTypeReferenceId="trustFrameworkPolicy" Required="true" DefaultValue="{policy}" />`. Além disso, certifique-se de que a sua política contém o tipo de afirmação `<ClaimType Id="trustFrameworkPolicy">   <DisplayName>trustFrameworkPolicy</DisplayName>     <DataType>string</DataType> </ClaimType>` | 

## <a name="cryptographic-keys"></a>Chaves criptográficas

O elemento de CryptographicKeys contém os seguintes atributos:

| Atributo | Necessário | Descrição |
| --------- | -------- | ----------- |
| issuer_secret | Sim | O X509 certificado (conjunto de chaves RSA) a ser usado para assinar o token JWT. Este é o `B2C_1A_TokenSigningKeyContainer` chave configurada no [começar a utilizar com políticas personalizadas](active-directory-b2c-get-started-custom.md). | 
| issuer_refresh_token_key | Sim | O X509 certificado (conjunto de chaves RSA) a ser usado para criptografar o token de atualização. Que configurou o `B2C_1A_TokenEncryptionKeyContainer` principais no [introdução às políticas personalizadas](active-directory-b2c-get-started-custom.md) |














