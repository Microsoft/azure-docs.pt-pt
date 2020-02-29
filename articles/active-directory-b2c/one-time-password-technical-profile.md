---
title: Ativar a verificação de senha única (OTP)
titleSuffix: Azure AD B2C
description: Aprenda a configurar um cenário de senha única (OTP) utilizando políticas personalizadas Azure AD B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 02/10/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 701fb64dd85526bc79cab48bf36d4583da71ca76
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/29/2020
ms.locfileid: "78184031"
---
# <a name="define-a-one-time-password-technical-profile-in-an-azure-ad-b2c-custom-policy"></a>Defina um perfil técnico de senha única numa política personalizada Azure AD B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

O Azure Ative Directory B2C (Azure AD B2C) fornece suporte para gerir a geração e verificação de uma senha única. Utilize um perfil técnico para gerar um código e, em seguida, verifique esse código mais tarde.

O perfil técnico de senha única também pode devolver uma mensagem de erro durante a verificação do código. Desenhe a integração com a senha única utilizando um perfil técnico de **Validação**. Um perfil técnico de validação chama o perfil técnico de senha única para verificar um código. O perfil técnico de validação valida os dados fornecidos pelo utilizador antes da viagem do utilizador continuar. Com o perfil técnico de validação, uma mensagem de erro é exibida numa página autoafirmada.

## <a name="protocol"></a>Protocolo

O **nome** atributo do elemento **protocolo** tem de ser definido para `Proprietary`. O atributo do **manipulador** deve conter o nome totalmente qualificado do conjunto de manipuladores de protocolos que é utilizado pelo Azure AD B2C:

```XML
Web.TPEngine.Providers.OneTimePasswordProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null
```

O exemplo que se segue mostra um perfil técnico de senha única:

```XML
<TechnicalProfile Id="VerifyCode">
  <DisplayName>Validate user input verification code</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.OneTimePasswordProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  ...
```

## <a name="generate-code"></a>Gerar código

O primeiro modo deste perfil técnico é gerar um código. Abaixo estão as opções que podem ser configuradas para este modo.

### <a name="input-claims"></a>Reclamações de entrada

O elemento **InputClaims** contém uma lista de reclamações necessárias para enviar ao fornecedor de protocolos de senha única. Também pode mapear o nome da sua reclamação para o nome definido abaixo.

| ReivindicaçãoReferenceid | Necessário | Descrição |
| --------- | -------- | ----------- |
| Identificador | Sim | O identificador para identificar o utilizador que precisa de verificar o código mais tarde. É comumente usado como o identificador do destino para onde o código é entregue, por exemplo, endereço de e-mail ou número de telefone. |

O elemento **InputClaimsTransformations** pode conter uma coleção de elementos **inputClaimsTransformation** que são usados para modificar as reclamações de entrada ou gerar novos antes de enviar para o fornecedor de protocolo de senha única.

### <a name="output-claims"></a>Reclamações de produção

O elemento **OutputClaims** contém uma lista de reclamações geradas pelo fornecedor de protocolos de senha única. Também pode mapear o nome da sua reclamação para o nome definido abaixo.

| ReivindicaçãoReferenceid | Necessário | Descrição |
| --------- | -------- | ----------- |
| otpGenerated | Sim | O código gerado cuja sessão é gerida pelo Azure AD B2C. |

O elemento **OutputClaimsTransformations** pode conter uma coleção de elementos **outputClaimsTransformation** que são usados para modificar as reclamações de saída ou gerar novos.

### <a name="metadata"></a>Metadados

As seguintes definições podem ser utilizadas para configurar a geração e manutenção de códigos:

| Atributo | Necessário | Descrição |
| --------- | -------- | ----------- |
| CodeExpirationInSeconds | Não | Tempo em segundos até a expiração do código. Mínimo: `60`; Máximo: `1200`; Predefinição: `600`. |
| Comprimento de código | Não | Comprimento do código. O valor predefinido é `6`. |
| Conjunto de caracteres | Não | O conjunto de caracteres para o código, formatado para uso numa expressão regular. Por exemplo, `a-z0-9A-Z`. O valor predefinido é `0-9`. O conjunto de caracteres deve incluir um mínimo de 10 caracteres diferentes no conjunto especificado. |
| Tentativas de Numretry | Não | O número de tentativas de verificação antes do código é considerado inválido. O valor predefinido é `5`. |
| Operação | Sim | A operação a ser realizada. Valores possíveis: `GenerateCode`, ou `VerifyCode`. |
| Reutilização Do SameCode | Não | Se um código duplicado deve ser dado em vez de gerar um novo código quando o código não expirou e ainda é válido. O valor predefinido é `false`. |

### <a name="returning-error-message"></a>Mensagem de erro de retorno

Não há nenhuma mensagem de erro devolvida para o modo de geração de códigos.

### <a name="example"></a>Exemplo

O exemplo seguinte `TechnicalProfile` é utilizado para gerar um código:

```XML
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

O elemento **InputClaims** contém uma lista de reclamações necessárias para enviar ao fornecedor de protocolos de senha única. Também pode mapear o nome da sua reclamação para o nome definido abaixo.

| ReivindicaçãoReferenceid | Necessário | Descrição |
| --------- | -------- | ----------- |
| Identificador | Sim | O identificador para identificar o utilizador que já gerou um código. É comumente usado como o identificador do destino para onde o código é entregue, por exemplo, endereço de e-mail ou número de telefone. |
| otpToVerificar | Sim | O código de verificação fornecido pelo utilizador. |

O elemento **InputClaimsTransformations** pode conter uma coleção de elementos **inputClaimsTransformation** que são usados para modificar as reclamações de entrada ou gerar novos antes de enviar para o fornecedor de protocolo de senha única.

### <a name="output-claims"></a>Reclamações de produção

Não existem pedidos de saída fornecidos durante a verificação do código deste prestador de protocolos.

O elemento **OutputClaimsTransformations** pode conter uma coleção de elementos **outputClaimsTransformation** que são usados para modificar as reclamações de saída ou gerar novos.

### <a name="metadata"></a>Metadados

As seguintes definições podem ser utilizadas para configurar a mensagem de erro apresentada após falha de verificação de código:

| Atributo | Necessário | Descrição |
| --------- | -------- | ----------- |
| UserMessageifSessionNão existe | Não | A mensagem a mostrar ao utilizador se a sessão de verificação de código tiver expirado. Ou o código expirou ou o código nunca foi gerado para um determinado identificador. |
| UserMessageIfMaxRetryTryTry | Não | A mensagem para mostrar ao utilizador se tiver excedido as tentativas máximas de verificação permitidas. |
| UserMessageIfInvalidcode | Não | A mensagem para mostrar ao utilizador se tiver fornecido um código inválido. |

### <a name="returning-error-message"></a>Mensagem de erro de retorno

Tal como descrito nos [Metadados,](#metadata)pode personalizar a mensagem de erro mostrada ao utilizador para diferentes casos de erro. Pode localizar ainda mais essas mensagens prefixando o local, por exemplo:

```XML
<Item Key="en.UserMessageIfInvalidCode">Wrong code has been entered.</Item>
```

### <a name="example"></a>Exemplo

É utilizado o seguinte exemplo `TechnicalProfile` para verificar um código:

```XML
<TechnicalProfile Id="VerifyCode">
    <DisplayName>Verify Code</DisplayName>
    <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.OneTimePasswordProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
    <Metadata>
        <Item Key="Operation">VerifyCode</Item>
        <Item Key="UserMessageIfInvalidCode">Wrong code has been entered.</Item>
        <Item Key="UserMessageIfSessionDoesNotExist">Code has expired.</Item>
        <Item Key="UserMessageIfMaxRetryAttempted">You've tried too many times.</Item>
    </Metadata>
    <InputClaims>
        <InputClaim ClaimTypeReferenceId="identifier" PartnerClaimType="identifier" />
        <InputClaim ClaimTypeReferenceId="otpGenerated" PartnerClaimType="otpToVerify" />
    </InputClaims>
</TechnicalProfile>
```

## <a name="next-steps"></a>Passos seguintes

Consulte o seguinte artigo, por exemplo, de utilização de um perfil técnico de senha única com verificação personalizada de e-mail:

- [Verificação personalizada de e-mail no Diretório Ativo Azure B2C](custom-email.md)

