---
title: Defina um perfil técnico de fator de telefone numa política personalizada
titleSuffix: Azure AD B2C
description: Defina um perfil técnico de fator telefone numa política personalizada no Diretório Ativo Azure B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/26/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 3a0511a19477f3d76baf9c453316c5348cc31397
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80332659"
---
# <a name="define-a-phone-factor-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Defina um perfil técnico de fator telefone numa política personalizada do Diretório Ativo Azure B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

O Azure Ative Directory B2C (Azure AD B2C) fornece suporte para a inscrição e verificação dos números de telefone. Este perfil técnico:

- Fornece uma interface de utilizador para interagir com o utilizador.
- Usa a definição de conteúdo para controlar o aspeto e a sensação.
- Suporta chamadas telefónicas e mensagens de texto para validar o número de telefone.
- Suporta vários números de telefone. O utilizador pode selecionar um dos números de telefone para verificar.  
- Se for fornecido um número de telefone, a interface de utilizador do fator telefone pede ao utilizador que verifique o número de telefone. Caso não seja fornecido, pede ao utilizador que inscreveu um novo número de telefone.
- Devolve uma reclamação indicando se o utilizador forneceu um novo número de telefone. Pode utilizar esta alegação para decidir se o número de telefone deve ser percetuado no perfil de utilizador da AD Azure.  

## <a name="protocol"></a>Protocolo

O **atributo** nome do elemento **protocolo** `Proprietary`tem de ser definido para . O atributo do **manipulador** deve conter o nome totalmente qualificado do conjunto de manipulador estomato do protocolo que é utilizado pelo Azure AD B2C para o fator telefone:`Web.TPEngine.Providers.PhoneFactorProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null`

O exemplo seguinte mostra um perfil técnico de fator telefone para inscrição e validação:

```XML
<TechnicalProfile Id="PhoneFactor-InputOrVerify">
  <DisplayName>PhoneFactor</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.PhoneFactorProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
</TechnicalProfile>
```

## <a name="input-claims"></a>Reclamações de entrada

O elemento InputClaims deve conter as seguintes reclamações. Também pode mapear o nome da sua reclamação para o nome definido no perfil técnico do fator de telefone. 

```XML
<InputClaims>
  <!--A unique identifier of the user. The partner claim type must be set to `UserId`. -->
  <InputClaim ClaimTypeReferenceId="userIdForMFA" PartnerClaimType="UserId" />
  <!--A claim that contains the phone number. If the claim is empty, Azure AD B2C asks the user to enroll a new phone number. Otherwise, it asks the user to verify the phone number. -->
  <InputClaim ClaimTypeReferenceId="strongAuthenticationPhoneNumber" />
</InputClaims>
```

O exemplo que se segue demonstra a utilização de vários números de telefone. Para mais informações, consulte a [política da amostra.](https://github.com/azure-ad-b2c/samples/tree/master/policies/mfa-add-secondarymfa)

```XML
<InputClaims>
  <InputClaim ClaimTypeReferenceId="userIdForMFA" PartnerClaimType="UserId" />
  <InputClaim ClaimTypeReferenceId="strongAuthenticationPhoneNumber" />
  <InputClaim ClaimTypeReferenceId="secondaryStrongAuthenticationPhoneNumber" />
</InputClaims>
```

O elemento InputClaimsTransformations pode conter uma coleção de elementos de transformação inputClaims que são usados para modificar as reclamações de entrada ou gerar novas antes de apresentá-las na página do fator telefone.

## <a name="output-claims"></a>Reclamações de produção

O elemento OutputClaims contém uma lista de reclamações devolvidas pelo perfil técnico do fator telefone.

```xml
<OutputClaims>
  <!-- The verified phone number. The partner claim type must be set to `Verified.OfficePhone`. -->
  <OutputClaim ClaimTypeReferenceId="Verified.strongAuthenticationPhoneNumber" PartnerClaimType="Verified.OfficePhone" />
  <!-- Indicates whether the new phone number has been entered by the user. The partner claim type must be set to `newPhoneNumberEntered`. -->
  <OutputClaim ClaimTypeReferenceId="newPhoneNumberEntered" PartnerClaimType="newPhoneNumberEntered" />
</OutputClaims>
```

O elemento OutputClaimsTransformations pode conter uma coleção de elementos outputClaimsTransformation que são usados para modificar as reclamações de saída ou gerar novos.

## <a name="cryptographic-keys"></a>Chaves criptográficas

O elemento **CryptographicKeys** não é utilizado.


## <a name="metadata"></a>Metadados

| Atributo | Necessário | Descrição |
| --------- | -------- | ----------- |
| ContentDefinitionReferenceId | Sim | O identificador da [definição](contentdefinitions.md) de conteúdo associado a este perfil técnico. |
| ManualPhoneNumberEntryAllowed| Não | Especifique se um utilizador pode ou não introduzir manualmente um número de telefone. Valores `true` possíveis: ou `false` (padrão).|

### <a name="ui-elements"></a>Elementos da IU

Os elementos de interface do utilizador da página de autenticação do fator telefone podem ser [localizados](localization-string-ids.md#azure-mfa-error-messages).

## <a name="next-steps"></a>Passos seguintes

- Consulte as [contas sociais e locais com](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/SocialAndLocalAccountsWithMfa) pacote de arranque MFA.

