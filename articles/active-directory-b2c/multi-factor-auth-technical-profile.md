---
title: Perfis técnicos do Azure MFA em políticas personalizadas
titleSuffix: Azure AD B2C
description: Referência de política personalizada para perfis técnicos da MFA (autenticação multifator) do Azure no Azure AD B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/17/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: a8aaea6b2afb4d89e6e667edba0eeba2f4ddcca8
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75480219"
---
# <a name="define-an-azure-mfa-technical-profile-in-an-azure-ad-b2c-custom-policy"></a>Definir um perfil técnico do Azure MFA em uma política personalizada de Azure AD B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) fornece suporte para verificar um número de telefone usando a MFA (autenticação multifator do Azure). Use este perfil técnico para gerar e enviar um código para um número de telefone e, em seguida, verificar o código.

O perfil técnico do Azure MFA também pode retornar uma mensagem de erro. Você pode criar a integração com o Azure MFA usando um **perfil técnico de validação**. Um perfil técnico de validação chama o serviço do Azure MFA. O perfil técnico de validação valida os dados fornecidos pelo usuário antes de o percurso do usuário continuar. Com o perfil técnico de validação, uma mensagem de erro é exibida em uma página autodeclarada.

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="protocol"></a>Protocolo

O atributo **Name** do elemento **Protocol** precisa ser definido como `Proprietary`. O atributo **Handler** deve conter o nome totalmente qualificado do assembly do manipulador de protocolo que é usado pelo Azure ad B2C:

```
Web.TPEngine.Providers.AzureMfaProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null
```

O exemplo a seguir mostra um perfil técnico do Azure MFA:

```XML
<TechnicalProfile Id="AzureMfa-SendSms">
    <DisplayName>Send Sms</DisplayName>
    <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AzureMfaProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
    ...
```

## <a name="send-sms"></a>Enviar SMS

O primeiro modo deste perfil técnico é gerar um código e enviá-lo. As opções a seguir podem ser configuradas para esse modo.

### <a name="input-claims"></a>Declarações de entrada

O elemento **InputClaims** contém uma lista de declarações a serem enviadas para o Azure MFA. Você também pode mapear o nome da sua declaração para o nome definido no perfil técnico do MFA.

| ClaimReferenceId | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| userPrincipalName | Sim | O identificador do usuário que possui o número de telefone. |
| phoneNumber | Sim | O número de telefone para o qual enviar um código SMS. |
| companyName | Não |O nome da empresa no SMS. Se não for fornecido, o nome do seu aplicativo será usado. |
| região | Não | A localidade do SMS. Se não for fornecido, a localidade do navegador do usuário será usada. |

O elemento **InputClaimsTransformations** pode conter uma coleção de elementos **InputClaimsTransformation** que são usados para modificar as declarações de entrada ou gerar novas antes de enviar para o serviço do Azure MFA.

### <a name="output-claims"></a>Declarações de saída

O provedor de protocolo MFA do Azure não retorna nenhum **OutputClaims**, portanto, não é necessário especificar declarações de saída. No entanto, você pode incluir declarações que não são retornadas pelo provedor de identidade do Azure MFA contanto que você defina o atributo `DefaultValue`.

O elemento **OutputClaimsTransformations** pode conter uma coleção de elementos **OutputClaimsTransformation** que são usados para modificar as declarações de saída ou gerar novas.

### <a name="metadata"></a>Metadados

| Atributo | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| Operação | Sim | Deve ser **OneWaySMS**.  |
| UserMessageIfInvalidFormat | Não | Mensagem de erro personalizada se o número de telefone fornecido não for um número de telefone válido |
| UserMessageIfCouldntSendSms | Não | Mensagem de erro personalizada se o número de telefone fornecido não aceitar SMS |
| UserMessageIfServerError | Não | Mensagem de erro personalizada se o servidor encontrou um erro interno |

### <a name="return-an-error-message"></a>Retornar uma mensagem de erro

Conforme descrito em [metadados](#metadata), você pode personalizar a mensagem de erro mostrada para o usuário para casos de erro diferentes. Você pode localizar mais essas mensagens prefixando a localidade. Por exemplo:

```XML
<Item Key="en.UserMessageIfInvalidFormat">Invalid phone number.</Item>
```

### <a name="example-send-an-sms"></a>Exemplo: enviar um SMS

O exemplo a seguir mostra um perfil técnico do Azure MFA que é usado para enviar um código via SMS.

```XML
<TechnicalProfile Id="AzureMfa-SendSms">
    <DisplayName>Send Sms</DisplayName>
    <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AzureMfaProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
    <Metadata>
        <Item Key="Operation">OneWaySMS</Item>
    </Metadata>
    <InputClaimsTransformations>
        <InputClaimsTransformation ReferenceId="CombinePhoneAndCountryCode" />
        <InputClaimsTransformation ReferenceId="ConvertStringToPhoneNumber" />
    </InputClaimsTransformations>
    <InputClaims>
        <InputClaim ClaimTypeReferenceId="userPrincipalName" />
        <InputClaim ClaimTypeReferenceId="fullPhoneNumber" PartnerClaimType="phoneNumber" />
    </InputClaims>
</TechnicalProfile>
```

## <a name="verify-code"></a>Verificar código

O segundo modo desse perfil técnico é verificar um código. As opções a seguir podem ser configuradas para esse modo.

### <a name="input-claims"></a>Declarações de entrada

O elemento **InputClaims** contém uma lista de declarações a serem enviadas para o Azure MFA. Você também pode mapear o nome da sua declaração para o nome definido no perfil técnico do MFA.

| ClaimReferenceId | Obrigatório | Descrição |
| --------- | -------- | ----------- | ----------- |
| phoneNumber| Sim | Mesmo número de telefone usado anteriormente para enviar um código. Ele também é usado para localizar uma sessão de verificação de telefone. |
| verificationCode  | Sim | O código de verificação fornecido pelo usuário a ser verificado |

O elemento **InputClaimsTransformations** pode conter uma coleção de elementos **InputClaimsTransformation** que são usados para modificar as declarações de entrada ou gerar novas antes de chamar o serviço de MFA do Azure.

### <a name="output-claims"></a>Declarações de saída

O provedor de protocolo MFA do Azure não retorna nenhum **OutputClaims**, portanto, não é necessário especificar declarações de saída. No entanto, você pode incluir declarações que não são retornadas pelo provedor de identidade do Azure MFA contanto que você defina o atributo `DefaultValue`.

O elemento **OutputClaimsTransformations** pode conter uma coleção de elementos **OutputClaimsTransformation** que são usados para modificar as declarações de saída ou gerar novas.

## <a name="metadata"></a>Metadados

| Atributo | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| Operação | Sim | Deve ser **verificar** |
| UserMessageIfInvalidFormat | Não | Mensagem de erro personalizada se o número de telefone fornecido não for um número de telefone válido |
| UserMessageIfWrongCodeEntered | Não | Mensagem de erro personalizada se o código inserido para verificação estiver errado |
| UserMessageIfMaxAllowedCodeRetryReached | Não | Mensagem de erro personalizada se o usuário tiver tentado um código de verificação muitas vezes |
| UserMessageIfThrottled | Não | Mensagem de erro personalizada se o usuário estiver limitado |
| UserMessageIfServerError | Não | Mensagem de erro personalizada se o servidor encontrou um erro interno |

### <a name="return-an-error-message"></a>Retornar uma mensagem de erro

Conforme descrito em [metadados](#metadata), você pode personalizar a mensagem de erro mostrada para o usuário para casos de erro diferentes. Você pode localizar mais essas mensagens prefixando a localidade. Por exemplo:

```XML
<Item Key="en.UserMessageIfWrongCodeEntered">Wrong code has been entered.</Item>
```

### <a name="example-verify-a-code"></a>Exemplo: verificar um código

O exemplo a seguir mostra um perfil técnico do Azure MFA usado para verificar o código.

```XML
<TechnicalProfile Id="AzureMfa-VerifySms">
    <DisplayName>Verify Sms</DisplayName>
    <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AzureMfaProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
    <Metadata>
        <Item Key="Operation">Verify</Item>
    </Metadata>
    <InputClaims>
        <InputClaim ClaimTypeReferenceId="phoneNumber" PartnerClaimType="phoneNumber" />
        <InputClaim ClaimTypeReferenceId="verificationCode" />
    </InputClaims>
</TechnicalProfile>
```
