---
title: Definir um perfil técnico para um emissor de token JWT em uma política personalizada no Azure Active Directory B2C | Microsoft Docs
description: Defina um perfil técnico para um emissor de token JWT em uma política personalizada no Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 10/30/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: a5b8f299826c5688eb80eaea11ffc3b2b5176297
ms.sourcegitcommit: 4d177e6d273bba8af03a00e8bb9fe51a447196d0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/04/2019
ms.locfileid: "71959670"
---
# <a name="define-a-technical-profile-for-a-jwt-token-issuer-in-an-azure-active-directory-b2c-custom-policy"></a>Definir um perfil técnico para um emissor de token JWT em uma política personalizada de Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) emite vários tipos de tokens de segurança à medida que processa cada fluxo de autenticação. Um perfil técnico para um emissor de token JWT emite um token JWT que é retornado para o aplicativo de terceira parte confiável. Normalmente, esse perfil técnico é a última etapa de orquestração no percurso do usuário.

## <a name="protocol"></a>Protocol

O atributo **Name** do elemento **Protocol** precisa ser definido como `None`. Defina o elemento **OutputTokenFormat** como `JWT`.

O exemplo a seguir mostra um perfil técnico para `JwtIssuer`:

```XML
<TechnicalProfile Id="JwtIssuer">
  <DisplayName>JWT Issuer</DisplayName>
  <Protocol Name="None" />
  <OutputTokenFormat>JWT</OutputTokenFormat>
  ...
</TechnicalProfile>
```

## <a name="input-output-and-persist-claims"></a>Declarações de entrada, saída e persistência

Os elementos **InputClaims**, **OutputClaims**e **PersistClaims** estão vazios ou ausentes. Os elementos **InutputClaimsTransformations** e **OutputClaimsTransformations** também estão ausentes.

## <a name="metadata"></a>Metadados

| Atributo | Requerido | Descrição |
| --------- | -------- | ----------- |
| issuer_refresh_token_user_identity_claim_type | Sim | A declaração que deve ser usada como declaração de identidade do usuário dentro dos códigos de autorização do OAuth2 e tokens de atualização. Por padrão, você deve defini-lo como `objectId`, a menos que especifique um tipo de declaração SubjectNamingInfo diferente. |
| SendTokenResponseBodyWithJsonNumbers | Não | Sempre definido como `true`. Para o formato herdado em que valores numéricos são fornecidos como cadeias de caracteres em vez de números JSON, defina como `false`. Esse atributo é necessário para clientes que assumiram uma dependência de uma implementação anterior que retornasse propriedades como cadeias de caracteres. |
| token_lifetime_secs | Não | Tempos de vida do token de acesso. O tempo de vida do token de portador OAuth 2,0 usado para obter acesso a um recurso protegido. O padrão é 3.600 segundos (1 hora). O mínimo (inclusivo) é de 300 segundos (5 minutos). O máximo (inclusivo) é de 86.400 segundos (24 horas). |
| id_token_lifetime_secs | Não | Tempos de vida de token de ID. O padrão é 3.600 segundos (1 hora). O mínimo (inclusivo) é de 300 segundos (5 minutos). O máximo (inclusivo) é de segundos 86.400 (24 horas). |
| refresh_token_lifetime_secs | Não | Tempos de vida de token de atualização. O período de tempo máximo antes do qual um token de atualização pode ser usado para adquirir um novo token de acesso, se o seu aplicativo tiver recebido o escopo offline_access. O padrão é 120, 9600 segundos (14 dias). O mínimo (inclusivo) é de 86.400 segundos (24 horas). O máximo (inclusivo) é de 7.776.000 segundos (90 dias). |
| rolling_refresh_token_lifetime_secs | Não | Tempo de vida da janela deslizante do token de atualização. Após esse período de tempo decorrido, o usuário é forçado a autenticar novamente, independentemente do período de validade do token de atualização mais recente adquirido pelo aplicativo. Se você não quiser impor um tempo de vida da janela deslizante, defina o valor de allow_infinite_rolling_refresh_token como `true`. O padrão é 7.776.000 segundos (90 dias). O mínimo (inclusivo) é de 86.400 segundos (24 horas). O máximo (inclusivo) é de 31.536.000 segundos (365 dias). |
| allow_infinite_rolling_refresh_token | Não | Se definido como `true`, o tempo de vida da janela deslizante do token de atualização nunca expirará. |
| IssuanceClaimPattern | Sim | Controla a declaração do emissor (ISS). Um dos valores:<ul><li>AuthorityAndTenantGuid-a declaração ISS inclui seu nome de domínio, como `login.microsoftonline` ou `tenant-name.b2clogin.com`, e seu identificador de locatário https: \//login. microsoftonline. com/00000000-0000-0000-0000-000000000000/v 2.0/</li><li>AuthorityWithTfp-a declaração ISS inclui seu nome de domínio, como `login.microsoftonline` ou `tenant-name.b2clogin.com`, seu identificador de locatário e o nome da política de terceira parte confiável. https: \//login. microsoftonline. com/TFP/00000000-0000-0000-0000-000000000000/b2c_1a_tp_sign-up-ou-Sign-in/v 2.0/</li></ul> |
| AuthenticationContextReferenceClaimPattern | Não | Controla o valor de declaração `acr`.<ul><li>Nenhum-Azure AD B2C não emite a declaração ACR</li><li>PolicyId-a declaração `acr` contém o nome da política</li></ul>As opções para definir esse valor são TFP (Diretiva de estrutura de confiança) e ACR (referência de contexto de autenticação). É recomendável definir esse valor como TFP para definir o valor, garantir que o `<Item>` com o `Key="AuthenticationContextReferenceClaimPattern"` exista e o valor seja `None`. Na política de terceira parte confiável, adicione o item `<OutputClaims>`, adicione esse elemento `<OutputClaim ClaimTypeReferenceId="trustFrameworkPolicy" Required="true" DefaultValue="{policy}" />`. Verifique também se a política contém o tipo de declaração `<ClaimType Id="trustFrameworkPolicy">   <DisplayName>trustFrameworkPolicy</DisplayName>     <DataType>string</DataType> </ClaimType>` |

## <a name="cryptographic-keys"></a>Chaves de criptografia

O elemento CryptographicKeys contém os seguintes atributos:

| Atributo | Requerido | Descrição |
| --------- | -------- | ----------- |
| issuer_secret | Sim | O certificado X509 (conjunto de chaves RSA) a ser usado para assinar o token JWT. Essa é a chave `B2C_1A_TokenSigningKeyContainer` que você está configurada em introdução [às políticas personalizadas](active-directory-b2c-get-started-custom.md). |
| issuer_refresh_token_key | Sim | O certificado X509 (conjunto de chaves RSA) a ser usado para criptografar o token de atualização. Você configurou a chave `B2C_1A_TokenEncryptionKeyContainer` em introdução [às políticas personalizadas](active-directory-b2c-get-started-custom.md) |














