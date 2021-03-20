---
title: Perfis técnicos Azure AD MFA em políticas personalizadas
titleSuffix: Azure AD B2C
description: Referência de política personalizada para perfis técnicos de autenticação multi-factor Ad Ad (MFA) em Azure AD B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/26/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: e81ac35555e6653cecb602e5af2f19aa3e2f05e9
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "94840598"
---
# <a name="define-an-azure-ad-mfa-technical-profile-in-an-azure-ad-b2c-custom-policy"></a>Defina um perfil técnico Azure AD MFA numa política personalizada AZure AD B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

O Azure Active Directory B2C (Azure AD B2C) proporciona suporte para verificar um número de telefone ao utilizar a Autenticação Multifator (MFA) do Azure Active Directory. Utilize este perfil técnico para gerar e enviar um código para um número de telefone e, em seguida, verificar o código. O perfil técnico do Azure AD MFA também pode devolver uma mensagem de erro.  O perfil técnico de validação valida os dados fornecidos pelo utilizador antes da viagem do utilizador continuar. Com o perfil técnico de validação, uma mensagem de erro aparece numa página autoafirmada.

Este perfil técnico:

- Não fornece uma interface para interagir com o utilizador. Em vez disso, a interface do utilizador é chamada a partir de um perfil técnico [autoafirmado,](self-asserted-technical-profile.md) ou de um [controlo de exibição](display-controls.md) como [um perfil técnico de validação](validation-technical-profile.md).
- Utiliza o serviço Azure AD MFA para gerar e enviar um código para um número de telefone e, em seguida, verifica o código.  
- Valida um número de telefone através de mensagens de texto.

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="protocol"></a>Protocolo

O **atributo nome** do elemento **Protocolo** tem de ser definido para `Proprietary` . O **atributo handler** deve conter o nome totalmente qualificado do conjunto de manipuladores de protocolo que é utilizado pela Azure AD B2C:

```
Web.TPEngine.Providers.AzureMfaProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null
```

O exemplo a seguir mostra um perfil técnico Azure AD MFA:

```xml
<TechnicalProfile Id="AzureMfa-SendSms">
    <DisplayName>Send Sms</DisplayName>
    <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AzureMfaProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
    ...
```

## <a name="send-sms"></a>Enviar SMS

O primeiro modo deste perfil técnico é gerar um código e enviá-lo. As seguintes opções podem ser configuradas para este modo.

### <a name="input-claims"></a>Reclamações de entrada

O elemento **InputClaims** contém uma lista de reclamações a enviar para Azure AD MFA. Também pode mapear o nome da sua reclamação para o nome definido no perfil técnico do MFA.

| ReclamaçãoReferênciaId | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| userPrincipalName | Yes | O identificador do utilizador que possui o número de telefone. |
| número de telefone | Yes | O número de telefone para enviar um código SMS para. |
| nome da empresa | No |O nome da empresa na SMS. Se não for fornecido, o nome da sua aplicação é utilizado. |
| região | No | A localidade do SMS. Se não for fornecida, é utilizada a localidade do navegador do utilizador. |

O elemento **InputClaimsTransformations** pode conter uma coleção de **elementos inputClaimsTransformation** que são utilizados para modificar as reclamações de entrada ou gerar novos antes de enviar para o serviço MFA Azure AD.

### <a name="output-claims"></a>Reclamações de saída

O fornecedor de protocolo Azure AD MFA não devolve quaisquer **Resultados de Saída,** pelo que não há necessidade de especificar reclamações de saída. Pode, no entanto, incluir reclamações que não sejam devolvidas pelo fornecedor de identidade Azure AD MFA desde que desfaça o `DefaultValue` atributo.

O elemento **OutputClaimsTransformations** pode conter uma coleção de elementos de **saídaClaimsTransformation** que são utilizados para modificar as alegações de saída ou gerar novos.

### <a name="metadata"></a>Metadados

| Atributo | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| Operação | Yes | Deve ser **oneWaySMS**.  |

#### <a name="ui-elements"></a>Elementos da IU

Os metadados que se seguem podem ser utilizados para configurar as mensagens de erro apresentadas após o envio de falhas de SMS. Os metadados devem ser configurados no perfil técnico [autoafirmado.](self-asserted-technical-profile.md) As mensagens de erro podem ser [localizadas](localization-string-ids.md#azure-ad-mfa-error-messages).

| Atributo | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| UserMessageIfCodntSendSms | No | Mensagem de erro do utilizador se o número de telefone fornecido não aceitar SMS. |
| UserMessageIfInvalidFormat | No | Mensagem de erro do utilizador se o número de telefone fornecido não for um número de telefone válido. |
| UserMessageIfServerError | No | Mensagem de erro do utilizador se o servidor tiver encontrado um erro interno. |
| UserMessageIfThrottled| No | Mensagem de erro do utilizador se um pedido tiver sido estrangulado.|

### <a name="example-send-an-sms"></a>Exemplo: enviar um SMS

O exemplo a seguir mostra um perfil técnico Azure AD MFA que é utilizado para enviar um código via SMS.

```xml
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

O elemento **InputClaims** contém uma lista de reclamações a enviar para Azure AD MFA. Também pode mapear o nome da sua reclamação para o nome definido no perfil técnico do MFA.

| ReclamaçãoReferênciaId | Obrigatório | Descrição |
| --------- | -------- | ----------- | ----------- |
| número de telefone| Yes | O mesmo número de telefone usado anteriormente para enviar um código. Também é usado para localizar uma sessão de verificação de telefone. |
| verificaçãoDesco  | Yes | O código de verificação fornecido pelo utilizador a ser verificado |

O elemento **InputClaimsTransformations** pode conter uma coleção de elementos de **entradaClaimsTransformation** que são utilizados para modificar as reclamações de entrada ou gerar novos antes de ligar para o serviço MFA Azure AD.

### <a name="output-claims"></a>Reclamações de saída

O fornecedor de protocolo Azure AD MFA não devolve quaisquer **Resultados de Saída,** pelo que não há necessidade de especificar reclamações de saída. Pode, no entanto, incluir reclamações que não sejam devolvidas pelo fornecedor de identidade Azure AD MFA desde que desfaça o `DefaultValue` atributo.

O elemento **OutputClaimsTransformations** pode conter uma coleção de elementos de **saídaClaimsTransformation** que são utilizados para modificar as alegações de saída ou gerar novos.

### <a name="metadata"></a>Metadados

| Atributo | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| Operação | Yes | Deve ser **verificar** |

#### <a name="ui-elements"></a>Elementos da IU

Os metadados que se seguem podem ser utilizados para configurar as mensagens de erro exibidas após falha de verificação de código. Os metadados devem ser configurados no perfil técnico [autoafirmado.](self-asserted-technical-profile.md) As mensagens de erro podem ser [localizadas](localization-string-ids.md#azure-ad-mfa-error-messages).

| Atributo | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| UserMessageIfMaxAllowedCodeRetryReached| No | Mensagem de erro do utilizador se o utilizador tiver tentado um código de verificação demasiadas vezes. |
| UserMessageIfServerError | No | Mensagem de erro do utilizador se o servidor tiver encontrado um erro interno. |
| UserMessageIfThrottled| No | Mensagem de erro do utilizador se o pedido for acelerado.|
| UserMessageIfWrongCodeEntered| No| Mensagem de erro do utilizador se o código introduzido para verificação estiver errado.|

### <a name="example-verify-a-code"></a>Exemplo: verificar um código

O exemplo a seguir mostra um perfil técnico Azure AD MFA utilizado para verificar o código.

```xml
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
