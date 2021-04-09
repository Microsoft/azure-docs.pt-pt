---
title: Perfis técnicos Azure AD SSPR em políticas personalizadas
titleSuffix: Azure AD B2C
description: Referência de política personalizada para perfis técnicos Azure AD SSPR em Azure AD B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 06/23/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 3e6fcf956639d827a8654c5ee80e7cab8cadf930
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "85383602"
---
# <a name="define-an-azure-ad-sspr-technical-profile-in-an-azure-ad-b2c-custom-policy"></a>Defina um perfil técnico AZure AD SSPR numa política personalizada Azure AD B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

O Azure Ative Directory B2C (Azure AD B2C) fornece suporte para verificar um endereço de e-mail para reset de senha de autosserviço (SSPR). Utilize o perfil técnico Azure AD SSPR para gerar e enviar um código para um endereço de e-mail e, em seguida, verificar o código. O perfil técnico Azure AD SSPR também pode devolver uma mensagem de erro. O perfil técnico de validação valida os dados fornecidos pelo utilizador antes da viagem do utilizador continuar. Com o perfil técnico de validação, uma mensagem de erro aparece numa página autoafirmada.

Este perfil técnico:

- Não fornece uma interface para interagir com o utilizador. Em vez disso, a interface do utilizador é chamada a partir de um perfil técnico [autoafirmado,](self-asserted-technical-profile.md) ou de um [controlo de exibição](display-controls.md) como [um perfil técnico de validação](validation-technical-profile.md).
- Utiliza o serviço Azure AD SSPR para gerar e enviar um código para um endereço de e-mail e, em seguida, verifica o código.  
- Valida um endereço de e-mail através de um código de verificação.

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="protocol"></a>Protocolo

O **atributo nome** do elemento **Protocolo** tem de ser definido para `Proprietary` . O **atributo handler** deve conter o nome totalmente qualificado do conjunto de manipuladores de protocolo que é utilizado pela Azure AD B2C:

```
Web.TPEngine.Providers.AadSsprProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null
```

O exemplo a seguir mostra um perfil técnico da Azure AD SSPR:

```XML
<TechnicalProfile Id="AadSspr-SendCode">
  <DisplayName>Send Code</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AadSsprProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
    ...
```

## <a name="send-email"></a>Enviar e-mail

O primeiro modo deste perfil técnico é gerar um código e enviá-lo. As seguintes opções podem ser configuradas para este modo.

### <a name="input-claims"></a>Reclamações de entrada

O elemento **InputClaims** contém uma lista de reclamações a enviar para a Azure AD SSPR. Também pode mapear o nome da sua reclamação para o nome definido no perfil técnico SSPR.

| ReclamaçãoReferênciaId | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| e-mailAddress | Yes | O identificador para o utilizador que possui o endereço de e-mail. A `PartnerClaimType` propriedade do pedido de entrada deve ser definida para `emailAddress` . |


O elemento **InputClaimsTransformations** pode conter uma coleção de elementos de **entradaClaimsTransformation** que são utilizados para modificar as reclamações de entrada ou gerar novas antes de enviar para o serviço Azure AD SSPR.

### <a name="output-claims"></a>Reclamações de saída

O fornecedor de protocolo SSPR da Azure AD não devolve quaisquer **Resultados de Saída,** pelo que não há necessidade de especificar reclamações de saída. Pode, no entanto, incluir reclamações que não sejam devolvidas pelo fornecedor de protocoloS Azure AD SSPR, desde que desemconfiem do `DefaultValue` atributo.

O elemento **OutputClaimsTransformations** pode conter uma coleção de elementos de **saídaClaimsTransformation** que são utilizados para modificar as alegações de saída ou gerar novos.

### <a name="metadata"></a>Metadados

| Atributo | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| Operação | Yes | Deve ser **o SendCode.**  |

#### <a name="ui-elements"></a>Elementos da IU

Os metadados que se seguem podem ser utilizados para configurar as mensagens de erro apresentadas após o envio de falhas de SMS. Os metadados devem ser configurados no perfil técnico [autoafirmado.](self-asserted-technical-profile.md) As mensagens de erro podem ser [localizadas](localization-string-ids.md#azure-ad-sspr).

| Atributo | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| UserMessageIfInternalError | No | Mensagem de erro do utilizador se o servidor tiver encontrado um erro interno. |
| UserMessageIfThrottled| No | Mensagem de erro do utilizador se um pedido tiver sido estrangulado.|


### <a name="example-send-an-email"></a>Exemplo: enviar um e-mail

O exemplo a seguir mostra um perfil técnico Azure AD SSPR que é usado para enviar um código por e-mail.

```XML
<TechnicalProfile Id="AadSspr-SendCode">
  <DisplayName>Send Code</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AadSsprProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="Operation">SendCode</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="email" PartnerClaimType="emailAddress"/>
  </InputClaims>
</TechnicalProfile>
```

## <a name="verify-code"></a>Verificar código

O segundo modo deste perfil técnico é verificar um código. As seguintes opções podem ser configuradas para este modo.

### <a name="input-claims"></a>Reclamações de entrada

O elemento **InputClaims** contém uma lista de reclamações a enviar para a Azure AD SSPR. Também pode mapear o nome da sua reclamação para o nome definido no perfil técnico SSPR.

| ReclamaçãoReferênciaId | Obrigatório | Descrição |
| --------- | -------- | ----------- | ----------- |
| e-mailAddress| Yes | O mesmo endereço de e-mail usado anteriormente para enviar um código. Também é usado para localizar uma sessão de verificação de e-mail. A `PartnerClaimType` propriedade do pedido de entrada deve ser definida para `emailAddress` .|
| verificaçãoDesco  | Yes | O código de verificação fornecido pelo utilizador deve ser verificado. A `PartnerClaimType` propriedade do pedido de entrada deve ser definida para `verificationCode` . |

O elemento **InputClaimsTransformations** pode conter uma coleção de elementos de **entradaClaimsTransformation** que são utilizados para modificar as reclamações de entrada ou gerar novas antes de ligar para o serviço Azure AD SSPR.

### <a name="output-claims"></a>Reclamações de saída

O fornecedor de protocolo SSPR da Azure AD não devolve quaisquer **Resultados de Saída,** pelo que não há necessidade de especificar reclamações de saída. Pode, no entanto, incluir reclamações que não sejam devolvidas pelo fornecedor de protocoloS Azure AD SSPR, desde que desemconfiem do `DefaultValue` atributo.

O elemento **OutputClaimsTransformations** pode conter uma coleção de elementos de **saídaClaimsTransformation** que são utilizados para modificar as alegações de saída ou gerar novos.

### <a name="metadata"></a>Metadados

| Atributo | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| Operação | Yes | Deve ser **verificar Código** |

#### <a name="ui-elements"></a>Elementos da IU

Os metadados que se seguem podem ser utilizados para configurar as mensagens de erro exibidas após falha de verificação de código. Os metadados devem ser configurados no perfil técnico [autoafirmado.](self-asserted-technical-profile.md) As mensagens de erro podem ser [localizadas](localization-string-ids.md#azure-ad-sspr).

| Atributo | Obrigatório | Descrição |
| --------- | -------- | ----------- |
|UserMessageIfChallengeExpired | A mensagem a apresentar ao utilizador se a sessão de verificação de código tiver expirado. Ou o código expirou ou o código nunca foi gerado para um identificador dado.|
|UserMessageIfInternalError | Mensagem de erro do utilizador se o servidor tiver encontrado um erro interno.|
|UserMessageIfThrottled | Mensagem de erro do utilizador se um pedido tiver sido estrangulado.|
|UserMessageIfVerificationFailedNoRetry | A mensagem a apresentar ao utilizador se tiver fornecido um código inválido e o utilizador não estiver autorizado a fornecer o código correto.|
|UserMessageIfVerificationFailedRetryAllowed | A mensagem a apresentar ao utilizador se tiver fornecido um código inválido e o utilizador estiver autorizado a fornecer o código correto.|

### <a name="example-verify-a-code"></a>Exemplo: verificar um código

O exemplo a seguir mostra um perfil técnico Azure AD SSPR utilizado para verificar o código.

```XML
<TechnicalProfile Id="AadSspr-VerifyCode">
  <DisplayName>Verify Code</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AadSsprProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="Operation">VerifyCode</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="verificationCode" PartnerClaimType="verificationCode" />
    <InputClaim ClaimTypeReferenceId="email" PartnerClaimType="emailAddress"/>
  </InputClaims>
</TechnicalProfile>
```