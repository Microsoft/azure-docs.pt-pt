---
title: Perfis técnicos do Azure MFA em políticas personalizadas
titleSuffix: Azure AD B2C
description: Referência de política personalizada para perfis técnicos de autenticação multi-factor (MFA) azure em Azure AD B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/17/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 05851dba9de06b5dfba2da4f455fbaf5e9376d08
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/29/2020
ms.locfileid: "78184286"
---
# <a name="define-an-azure-mfa-technical-profile-in-an-azure-ad-b2c-custom-policy"></a>Defina um perfil técnico Azure MFA numa política personalizada Azure AD B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

O Diretório Ativo Azure B2C (Azure AD B2C) fornece suporte para verificar um número de telefone utilizando a Autenticação Multi-Factor Azure (MFA). Utilize este perfil técnico para gerar e enviar um código para um número de telefone e, em seguida, verifique o código.

O perfil técnico do Azure MFA também pode devolver uma mensagem de erro. Pode conceber a integração com o Azure MFA utilizando um **perfil técnico de Validação.** Um perfil técnico de validação chama o serviço Azure MFA. O perfil técnico de validação valida os dados fornecidos pelo utilizador antes da viagem do utilizador continuar. Com o perfil técnico de validação, uma mensagem de erro é exibida numa página autoafirmada.

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="protocol"></a>Protocolo

O **nome** atributo do elemento **protocolo** tem de ser definido para `Proprietary`. O atributo do **manipulador** deve conter o nome totalmente qualificado do conjunto de manipuladores de protocolos que é utilizado pelo Azure AD B2C:

```
Web.TPEngine.Providers.AzureMfaProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null
```

O exemplo que se segue mostra um perfil técnico do Azure MFA:

```XML
<TechnicalProfile Id="AzureMfa-SendSms">
    <DisplayName>Send Sms</DisplayName>
    <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AzureMfaProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
    ...
```

## <a name="send-sms"></a>Enviar SMS

O primeiro modo deste perfil técnico é gerar um código e enviá-lo. As seguintes opções podem ser configuradas para este modo.

### <a name="input-claims"></a>Reclamações de entrada

O elemento **InputClaims** contém uma lista de reclamações a enviar para o Azure MFA. Também pode mapear o nome da sua reclamação para o nome definido no perfil técnico do MFA.

| ReivindicaçãoReferenceid | Necessário | Descrição |
| --------- | -------- | ----------- |
| userPrincipalName | Sim | O identificador para o utilizador que possui o número de telefone. |
| phoneNumber | Sim | O número de telefone para enviar um código SMS para. |
| companyName | Não |O nome da empresa no SMS. Caso não seja fornecido, o nome da sua aplicação é utilizado. |
| local | Não | O local do SMS. Caso não seja fornecido, é utilizado o local do navegador do utilizador. |

O elemento **InputClaimsTransformations** pode conter uma coleção de elementos **inputClaimsTransformation** que são usados para modificar as reclamações de entrada ou gerar novos antes de enviar para o serviço Azure MFA.

### <a name="output-claims"></a>Reclamações de produção

O prestador de protocolos Azure MFA não devolve quaisquer **OutputClaims,** pelo que não é necessário especificar os pedidos de saída. Pode, no entanto, incluir alegações que não são devolvidas pelo fornecedor de identidade Azure MFA desde que detetete o atributo `DefaultValue`.

O elemento **OutputClaimsTransformations** pode conter uma coleção de elementos **outputClaimsTransformation** que são usados para modificar as reclamações de saída ou gerar novos.

### <a name="metadata"></a>Metadados

| Atributo | Necessário | Descrição |
| --------- | -------- | ----------- |
| Operação | Sim | Deve ser **oneWaySMS.**  |
| UserMessageIfInvalidformat | Não | Mensagem de erro personalizada se o número de telefone fornecido não for um número de telefone válido |
| UserMessageIfCouldntSendSms | Não | Mensagem de erro personalizada se o número de telefone fornecido não aceitar SMS |
| Erro do servidor usermessageifserver | Não | Mensagem de erro personalizada se o servidor tiver encontrado um erro interno |

### <a name="return-an-error-message"></a>Devolver uma mensagem de erro

Tal como descrito nos [Metadados,](#metadata)pode personalizar a mensagem de erro mostrada ao utilizador para diferentes casos de erro. Pode localizar ainda mais essas mensagens prefixando o local. Por exemplo:

```XML
<Item Key="en.UserMessageIfInvalidFormat">Invalid phone number.</Item>
```

### <a name="example-send-an-sms"></a>Exemplo: enviar um SMS

O exemplo seguinte mostra um perfil técnico Azure MFA que é usado para enviar um código via SMS.

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

O segundo modo deste perfil técnico é verificar um código. As seguintes opções podem ser configuradas para este modo.

### <a name="input-claims"></a>Reclamações de entrada

O elemento **InputClaims** contém uma lista de reclamações a enviar para o Azure MFA. Também pode mapear o nome da sua reclamação para o nome definido no perfil técnico do MFA.

| ReivindicaçãoReferenceid | Necessário | Descrição |
| --------- | -------- | ----------- | ----------- |
| phoneNumber| Sim | O mesmo número de telefone usado anteriormente para enviar um código. Também é usado para localizar uma sessão de verificação do telefone. |
| código de verificação  | Sim | O código de verificação fornecido pelo utilizador a verificar |

O elemento **InputClaimsTransformations** pode conter uma coleção de elementos **inputClaimsTransformation** que são usados para modificar as reclamações de entrada ou gerar novos antes de chamar o serviço Azure MFA.

### <a name="output-claims"></a>Reclamações de produção

O prestador de protocolos Azure MFA não devolve quaisquer **OutputClaims,** pelo que não é necessário especificar os pedidos de saída. Pode, no entanto, incluir alegações que não são devolvidas pelo fornecedor de identidade Azure MFA desde que detetete o atributo `DefaultValue`.

O elemento **OutputClaimsTransformations** pode conter uma coleção de elementos **outputClaimsTransformation** que são usados para modificar as reclamações de saída ou gerar novos.

## <a name="metadata"></a>Metadados

| Atributo | Necessário | Descrição |
| --------- | -------- | ----------- |
| Operação | Sim | Deve ser **verificar** |
| UserMessageIfInvalidformat | Não | Mensagem de erro personalizada se o número de telefone fornecido não for um número de telefone válido |
| UserMessageifWrongCodeEntered | Não | Mensagem de erro personalizada se o código introduzido para verificação estiver errado |
| UserMessageIfMaxAllowedCodeRetryReached | Não | Mensagem de erro personalizada se o utilizador tiver tentado um código de verificação demasiadas vezes |
| UserMessageIfThrottled | Não | Mensagem de erro personalizada se o utilizador estiver estrangulado |
| Erro do servidor usermessageifserver | Não | Mensagem de erro personalizada se o servidor tiver encontrado um erro interno |

### <a name="return-an-error-message"></a>Devolver uma mensagem de erro

Tal como descrito nos [Metadados,](#metadata)pode personalizar a mensagem de erro mostrada ao utilizador para diferentes casos de erro. Pode localizar ainda mais essas mensagens prefixando o local. Por exemplo:

```XML
<Item Key="en.UserMessageIfWrongCodeEntered">Wrong code has been entered.</Item>
```

### <a name="example-verify-a-code"></a>Exemplo: verificar um código

O exemplo que se segue mostra um perfil técnico azure MFA utilizado para verificar o código.

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
