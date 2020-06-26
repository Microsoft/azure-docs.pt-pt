---
title: Ativar a verificação de senha única (OTP)
titleSuffix: Azure AD B2C
description: Saiba como configurar um cenário de senha única (OTP) utilizando políticas personalizadas Azure AD B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/26/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 9592afbf74e65bcb2fe9319da764bf06d8d4eb6c
ms.sourcegitcommit: b56226271541e1393a4b85d23c07fd495a4f644d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/26/2020
ms.locfileid: "85385727"
---
# <a name="define-a-one-time-password-technical-profile-in-an-azure-ad-b2c-custom-policy"></a>Defina um perfil técnico de senha única numa política personalizada Azure AD B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

O Azure Ative Directory B2C (Azure AD B2C) fornece suporte para a gestão da geração e verificação de uma senha única. Utilize um perfil técnico para gerar um código e, em seguida, verifique esse código mais tarde.

O perfil técnico de senha única também pode devolver uma mensagem de erro durante a verificação do código. Desenhe a integração com a senha única utilizando um **perfil técnico de Validação.** Um perfil técnico de validação chama o perfil técnico de senha única para verificar um código. O perfil técnico de validação valida os dados fornecidos pelo utilizador antes da viagem do utilizador continuar. Com o perfil técnico de validação, é apresentada uma mensagem de erro numa página autoafirmada.

## <a name="protocol"></a>Protocolo

O **atributo nome** do elemento **Protocolo** tem de ser definido para `Proprietary` . O **atributo handler** deve conter o nome totalmente qualificado do conjunto de manipuladores de protocolo que é utilizado pela Azure AD B2C:

```xml
Web.TPEngine.Providers.OneTimePasswordProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null
```

O exemplo a seguir mostra um perfil técnico de senha única:

```xml
<TechnicalProfile Id="VerifyCode">
  <DisplayName>Validate user input verification code</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.OneTimePasswordProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  ...
```

## <a name="generate-code"></a>Gerar código

O primeiro modo deste perfil técnico é gerar um código. Abaixo estão as opções que podem ser configuradas para este modo.

### <a name="input-claims"></a>Reclamações de entrada

O elemento **InputClaims** contém uma lista de reclamações necessárias para enviar ao fornecedor de protocolo de senha de uma única vez. Também pode mapear o nome da sua reclamação para o nome definido abaixo.

| ReclamaçãoReferênciaId | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| identifier | Yes | O identificador para identificar o utilizador que precisa de verificar o código mais tarde. É comumente usado como o identificador do destino onde o código é entregue, por exemplo, endereço de e-mail ou número de telefone. |

O elemento **InputClaimsTransformations** pode conter uma coleção de elementos de **transmissão inputClaimsTransformation** que são utilizados para modificar as reclamações de entrada ou gerar novos antes de enviar para o fornecedor de protocolo de senha de uma única vez.

### <a name="output-claims"></a>Reclamações de saída

O elemento **OutputClaims** contém uma lista de reclamações geradas pelo fornecedor de protocolo de senha de uma única vez. Também pode mapear o nome da sua reclamação para o nome definido abaixo.

| ReclamaçãoReferênciaId | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| otpGenerado | Yes | O código gerado cuja sessão é gerida por Azure AD B2C. |

O elemento **OutputClaimsTransformations** pode conter uma coleção de elementos de **saídaClaimsTransformation** que são utilizados para modificar as alegações de saída ou gerar novos.

### <a name="metadata"></a>Metadados

As seguintes definições podem ser utilizadas para configurar o modo de geração de código:

| Atributo | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| CodeExpirationInSeconds | No | Tempo em segundos até a expiração do código. Mínimo: `60` ; Máximo: `1200` Predefinição: `600` . |
| CodeLength | No | Comprimento do código. O valor predefinido é `6`. |
| Conjunto de Caracteres | No | O conjunto de caracteres para o código, formatado para utilização numa expressão regular. Por exemplo, `a-z0-9A-Z`. O valor predefinido é `0-9`. O conjunto de caracteres deve incluir um mínimo de 10 caracteres diferentes no conjunto especificado. |
| NumRetryAttempts | No | O número de tentativas de verificação antes do código é considerado inválido. O valor predefinido é `5`. |
| Operação | Yes | A operação a ser realizada. Valor possível: `GenerateCode` . |
| Reutilizar OAmeCode | No | Se um código duplicado deve ser dado em vez de gerar um novo código quando determinado código não expirou e ainda é válido. O valor predefinido é `false`. |

### <a name="example"></a>Exemplo

O exemplo a seguir `TechnicalProfile` é utilizado para gerar um código:

```xml
<TechnicalProfile Id="GenerateCode">
  <DisplayName>Generate Code</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.OneTimePasswordProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="Operation">GenerateCode</Item>
    <Item Key="CodeExpirationInSeconds">600</Item>
    <Item Key="CodeLength">6</Item>
    <Item Key="CharacterSet">0-9</Item>
    <Item Key="NumRetryAttempts">5</Item>
    <Item Key="ReuseSameCode">false</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="identifier" PartnerClaimType="identifier" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="otpGenerated" PartnerClaimType="otpGenerated" />
  </OutputClaims>
</TechnicalProfile>
```

## <a name="verify-code"></a>Verificar código

O segundo modo deste perfil técnico é verificar um código. Abaixo estão as opções que podem ser configuradas para este modo.

### <a name="input-claims"></a>Reclamações de entrada

O elemento **InputClaims** contém uma lista de reclamações necessárias para enviar ao fornecedor de protocolo de senha de uma única vez. Também pode mapear o nome da sua reclamação para o nome definido abaixo.

| ReclamaçãoReferênciaId | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| identifier | Yes | O identificador para identificar o utilizador que já gerou um código. É comumente usado como o identificador do destino onde o código é entregue, por exemplo, endereço de e-mail ou número de telefone. |
| otpToVerificar | Yes | O código de verificação fornecido pelo utilizador. |

O elemento **InputClaimsTransformations** pode conter uma coleção de elementos de **transmissão inputClaimsTransformation** que são utilizados para modificar as reclamações de entrada ou gerar novos antes de enviar para o fornecedor de protocolo de senha de uma única vez.

### <a name="output-claims"></a>Reclamações de saída

Não existem pedidos de saída fornecidos durante a verificação de código deste fornecedor de protocolo.

O elemento **OutputClaimsTransformations** pode conter uma coleção de elementos de **saídaClaimsTransformation** que são utilizados para modificar as alegações de saída ou gerar novos.

### <a name="metadata"></a>Metadados

As seguintes definições podem ser utilizadas no modo de verificação de códigos:

| Atributo | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| Operação | Yes | A operação a ser realizada. Valor possível: `VerifyCode` . |


### <a name="ui-elements"></a>Elementos da IU

Os metadados que se seguem podem ser utilizados para configurar as mensagens de erro exibidas após falha de verificação de código. Os metadados devem ser configurados no perfil técnico [autoafirmado.](self-asserted-technical-profile.md) As mensagens de erro podem ser [localizadas](localization-string-ids.md#one-time-password-error-messages).

| Atributo | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| UserMessageIfSessionDoesNotExist | No | A mensagem a apresentar ao utilizador se a sessão de verificação de código tiver expirado. Ou o código expirou ou o código nunca foi gerado para um identificador dado. |
| UserMessageIfMaxRetryAttempted | No | A mensagem a apresentar ao utilizador se exceder as tentativas de verificação máxima permitidas. |
| UserMessageIfInvalidCode | No | A mensagem a apresentar ao utilizador se tiver fornecido um código inválido. |
| UserMessageIfVerificationFailedRetryAllowed | No | A mensagem a apresentar ao utilizador se tiver fornecido um código inválido e o utilizador estiver autorizado a fornecer o código correto.  |
|UserMessageIfSessionConflict|No| A mensagem a apresentar ao utilizador se o código não puder ser verificado.|

### <a name="example"></a>Exemplo

É utilizado o exemplo seguinte `TechnicalProfile` para verificar um código:

```xml
<TechnicalProfile Id="VerifyCode">
  <DisplayName>Verify Code</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.OneTimePasswordProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="Operation">VerifyCode</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="identifier" PartnerClaimType="identifier" />
    <InputClaim ClaimTypeReferenceId="otpGenerated" PartnerClaimType="otpToVerify" />
  </InputClaims>
</TechnicalProfile>
```

## <a name="next-steps"></a>Passos seguintes

Consulte o seguinte artigo, por exemplo, utilizando um perfil técnico de senha única com verificação de e-mail personalizado:

- Verificação personalizada de e-mail em Azure Ative Directory B2C[(Mailjet,](custom-email-mailjet.md) [SendGrid)](custom-email-sendgrid.md)

