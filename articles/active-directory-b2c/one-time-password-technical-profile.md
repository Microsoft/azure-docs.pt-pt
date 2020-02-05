---
title: Habilitar a verificação de OTP (senha de uso único)
titleSuffix: Azure AD B2C
description: Saiba como configurar um cenário de OTP (senha de uso único) usando Azure AD B2C políticas personalizadas.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 02/03/2020
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: dab35fbcd221af9f4eb587b8c98a8ff85aeef59f
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/04/2020
ms.locfileid: "76982794"
---
# <a name="define-a-one-time-password-technical-profile-in-an-azure-ad-b2c-custom-policy"></a>Definir um perfil técnico de senha de uso único em uma política personalizada de Azure AD B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

O Azure Active Directory B2C (Azure AD B2C) fornece suporte para gerenciar a geração e a verificação de uma senha de uso único. Use um perfil técnico para gerar um código e, em seguida, verifique esse código posteriormente.

O perfil técnico de senha de uso único também pode retornar uma mensagem de erro durante a verificação de código. Projete a integração com a senha de uso único usando um **perfil técnico de validação**. Um perfil técnico de validação chama o perfil técnico de senha de uso único para verificar um código. O perfil técnico de validação valida os dados fornecidos pelo usuário antes de o percurso do usuário continuar. Com o perfil técnico de validação, uma mensagem de erro é exibida em uma página autodeclarada.

## <a name="protocol"></a>Protocolo

O atributo **Name** do elemento **Protocol** precisa ser definido como `Proprietary`. O atributo **Handler** deve conter o nome totalmente qualificado do assembly do manipulador de protocolo que é usado pelo Azure ad B2C:

```XML
Web.TPEngine.Providers.OneTimePasswordProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null
```

O exemplo a seguir mostra um perfil técnico de senha de uso único:

```XML
<TechnicalProfile Id="VerifyCode">
  <DisplayName>Validate user input verification code</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.OneTimePasswordProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  ...
```

## <a name="generate-code"></a>Gerar código

O primeiro modo deste perfil técnico é gerar um código. Abaixo estão as opções que podem ser configuradas para esse modo.

### <a name="input-claims"></a>Declarações de entrada

O elemento **InputClaims** contém uma lista de declarações necessárias para enviar para o provedor de protocolo de senha de uso único. Você também pode mapear o nome da sua declaração para o nome definido abaixo.

| ClaimReferenceId | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| Identificador | Sim | O identificador para identificar o usuário que precisa verificar o código posteriormente. Normalmente, ele é usado como o identificador do destino para o qual o código é entregue, por exemplo, endereço de email ou número de telefone. |

O elemento **InputClaimsTransformations** pode conter uma coleção de elementos **InputClaimsTransformation** que são usados para modificar as declarações de entrada ou gerar novas antes de enviar para o provedor de protocolo de senha de uso único.

### <a name="output-claims"></a>Declarações de saída

O elemento **OutputClaims** contém uma lista de declarações geradas pelo provedor de protocolo de senha de uso único. Você também pode mapear o nome da sua declaração para o nome definido abaixo.

| ClaimReferenceId | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| otpGenerated | Sim | O código gerado cuja sessão é gerenciada pelo Azure AD B2C. |

O elemento **OutputClaimsTransformations** pode conter uma coleção de elementos **OutputClaimsTransformation** que são usados para modificar as declarações de saída ou gerar novas.

### <a name="metadata"></a>Metadados

As configurações a seguir podem ser usadas para configurar a geração e a manutenção de código:

| Atributo | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| CodeExpirationInSeconds | Não | Tempo em segundos até a expiração do código. Mínimo: `60`; Máximo: `1200`; Padrão: `600`. |
| CodeLength | Não | Comprimento do código. O valor predefinido é `6`. |
| CharacterSet | Não | O conjunto de caracteres para o código formatado para uso em uma expressão regular. Por exemplo, `a-z0-9A-Z`. O valor predefinido é `0-9`. O conjunto de caracteres deve incluir um mínimo de 10 caracteres diferentes no conjunto especificado. |
| NumRetryAttempts | Não | O número de tentativas de verificação antes de o código ser considerado inválido. O valor predefinido é `5`. |
| Operação | Sim | A operação a ser realizada. Valores possíveis: `GenerateCode`, ou `VerifyCode`. |
| ReuseSameCode | Não | Se um código duplicado deve ser fornecido em vez de gerar um novo código quando determinado código não tiver expirado e ainda for válido. O valor predefinido é `false`. |

### <a name="returning-error-message"></a>Retornando mensagem de erro

Não há nenhuma mensagem de erro retornada para o modo de geração de código.

### <a name="example"></a>Exemplo

O `TechnicalProfile` de exemplo a seguir é usado para gerar um código:

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

O segundo modo desse perfil técnico é verificar um código. Abaixo estão as opções que podem ser configuradas para esse modo.

### <a name="input-claims"></a>Declarações de entrada

O elemento **InputClaims** contém uma lista de declarações necessárias para enviar para o provedor de protocolo de senha de uso único. Você também pode mapear o nome da sua declaração para o nome definido abaixo.

| ClaimReferenceId | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| Identificador | Sim | O identificador para identificar o usuário que gerou um código anteriormente. Normalmente, ele é usado como o identificador do destino para o qual o código é entregue, por exemplo, endereço de email ou número de telefone. |
| otpToVerify | Sim | O código de verificação fornecido pelo usuário. |

O elemento **InputClaimsTransformations** pode conter uma coleção de elementos **InputClaimsTransformation** que são usados para modificar as declarações de entrada ou gerar novas antes de enviar para o provedor de protocolo de senha de uso único.

### <a name="output-claims"></a>Declarações de saída

Não há declarações de saída fornecidas durante a verificação de código desse provedor de protocolo.

O elemento **OutputClaimsTransformations** pode conter uma coleção de elementos **OutputClaimsTransformation** que são usados para modificar as declarações de saída ou gerar novas.

### <a name="metadata"></a>Metadados

As configurações a seguir podem ser usadas para configurar a mensagem de erro exibida após a falha de verificação de código:

| Atributo | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| UserMessageIfSessionDoesNotExist | Não | A mensagem a ser exibida para o usuário se a sessão de verificação de código tiver expirado. O código expirou ou o código nunca foi gerado para um determinado identificador. |
| UserMessageIfMaxRetryAttempted | Não | A mensagem a ser exibida para o usuário se ele tiver excedido o máximo de tentativas de verificação permitidas. |
| UserMessageIfInvalidCode | Não | A mensagem a ser exibida para o usuário se ele tiver fornecido um código inválido. |

### <a name="returning-error-message"></a>Retornando mensagem de erro

Conforme descrito em [metadados](#metadata), você pode personalizar a mensagem de erro mostrada para o usuário para casos de erro diferentes. Você pode localizar mais essas mensagens prefixando a localidade, por exemplo:

```XML
<Item Key="en.UserMessageIfInvalidCode">Wrong code has been entered.</Item>
```

### <a name="example"></a>Exemplo

O `TechnicalProfile` de exemplo a seguir é usado para verificar um código:

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
