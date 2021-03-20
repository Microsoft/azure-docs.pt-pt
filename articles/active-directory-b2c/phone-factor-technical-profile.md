---
title: Defina um perfil técnico de fator telefónico numa política personalizada
titleSuffix: Azure AD B2C
description: Defina um perfil técnico de fator telefónico numa política personalizada no Azure Ative Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 10/12/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 322e4b78fbfb38f1822fb7a7cdcdbfcc0738b303
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "91950402"
---
# <a name="define-a-phone-factor-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Defina um perfil técnico de fator telefónico numa política personalizada do Azure Ative Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

O Azure Ative Directory B2C (Azure AD B2C) fornece suporte para a inscrição e verificação de números de telefone. Este perfil técnico:

- Fornece uma interface de utilizador para interagir com o utilizador para verificar, ou inscrever um número de telefone.
- Suporta chamadas telefónicas e mensagens de texto para validar o número de telefone.
- Suporta vários números de telefone. O utilizador pode selecionar um dos números de telefone para verificar.  
- Devolve uma reclamação indicando se o utilizador forneceu um novo número de telefone. Pode utilizar esta alegação para decidir se o número de telefone deve ser persistido ao perfil de utilizador do Azure AD B2C.  
- Usa uma [definição de conteúdo](contentdefinitions.md) para controlar o aspeto e a sensação.

## <a name="protocol"></a>Protocolo

O **atributo nome** do elemento **Protocolo** tem de ser definido para `Proprietary` . O atributo **do manipulador** deve conter o nome totalmente qualificado do conjunto de manipuladores de protocolo que é utilizado pela Azure AD B2C para o fator telefone: `Web.TPEngine.Providers.PhoneFactorProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null`

O exemplo a seguir mostra um perfil técnico do fator telefónico para a inscrição e validação:

```xml
<TechnicalProfile Id="PhoneFactor-InputOrVerify">
  <DisplayName>PhoneFactor</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.PhoneFactorProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
</TechnicalProfile>
```

## <a name="input-claims-transformations"></a>Input reclama transformações

O elemento InputClaimsTransformations pode conter uma coleção de transformações de reclamações de entrada que são usadas para modificar as alegações de entrada, ou gerar novas. A seguinte entrada alega que a transformação gera uma `UserId` alegação que é usada mais tarde na recolha de reclamações de entrada.

```xml
<InputClaimsTransformations>
  <InputClaimsTransformation ReferenceId="CreateUserIdForMFA" />
</InputClaimsTransformations>
```

## <a name="input-claims"></a>Reclamações de entrada

O elemento InputClaims deve conter as seguintes alegações. Também pode mapear o nome da sua reclamação para o nome definido no perfil técnico do fator telefónico. 

|  Tipo de dados| Obrigatório | Description |
| --------- | -------- | ----------- | 
| cadeia (de carateres)| Yes | Um identificador único para o utilizador. O nome de reclamação, ou PartnerClaimType deve ser definido para `UserId` . Esta alegação não deve conter informações pessoais identificáveis.|
| string| Yes | Lista de tipos de reclamações. Cada reclamação contém um número de telefone. Se alguma das alegações de entrada não contiver um número de telefone, o utilizador será solicitado a inscrever-se e verificar um novo número de telefone. O número de telefone validado é devolvido como reclamação de saída. Se uma das alegações de entrada contiver um número de telefone, o utilizador é solicitado a verificar. Se várias reclamações de entrada contiverem um número de telefone, o utilizador é solicitado a escolher e verificar um dos números de telefone. |

O exemplo a seguir demonstra a utilização de vários números de telefone. Para mais informações, consulte [a política da amostra.](https://github.com/azure-ad-b2c/samples/tree/master/policies/mfa-add-secondarymfa)

```xml
<InputClaims>
  <InputClaim ClaimTypeReferenceId="userIdForMFA" PartnerClaimType="UserId" />
  <InputClaim ClaimTypeReferenceId="strongAuthenticationPhoneNumber" />
  <InputClaim ClaimTypeReferenceId="secondaryStrongAuthenticationPhoneNumber" />
</InputClaims>
```

## <a name="output-claims"></a>Reclamações de saída

O elemento OutputClaims contém uma lista de reclamações devolvidas pelo perfil técnico do fator telefónico.

|  Tipo de dados| Obrigatório | Descrição |
|  -------- | ----------- |----------- |
| boolean | Yes | Indica se o novo número de telefone foi introduzido pelo utilizador. O nome de reclamação, ou PartnerClaimType deve ser definido para `newPhoneNumberEntered`|
| string| Yes | O número de telefone verificado. O nome de reclamação, ou PartnerClaimType deve ser definido para `Verified.OfficePhone` .|

O elemento OutputClaimsTransformations pode conter uma coleção de elementos de saídaClaimsTransformation que são utilizados para modificar as alegações de saída ou gerar novos.

## <a name="cryptographic-keys"></a>Chaves criptográficas

O elemento **CryptographicKeys** não é utilizado.


## <a name="metadata"></a>Metadados

| Atributo | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| ConteúdoDefinitionReferenceId | Yes | O identificador da [definição](contentdefinitions.md) de conteúdo associado a este perfil técnico. |
| ManualPhoneNumberEntryAllowed| No | Especificar se um utilizador pode ou não introduzir manualmente um número de telefone. Valores possíveis: `true` , ou `false` (predefinição).|
| definição.autenticaçãoMode | No | O método para validar o número de telefone. Valores possíveis: `sms` `phone` , , ou `mixed` (padrão).|
| definição.autodial| No| Especificar se o perfil técnico deve marcar automaticamente ou enviar um SMS automático. Valores possíveis: `true` , ou `false` (predefinição). O mostrador automático requer que os `setting.authenticationMode` metadados sejam definidos para `sms` , ou `phone` . A recolha de pedidos de entrada deve ter um único número de telefone. |

### <a name="ui-elements"></a>Elementos da IU

Os elementos de interface de utilizador da página de autenticação do fator telefone podem ser [localizados](localization-string-ids.md#phone-factor-authentication-page-user-interface-elements).

## <a name="next-steps"></a>Passos seguintes

- Consulte as [contas sociais e locais com](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/SocialAndLocalAccountsWithMfa) o pacote de arranque MFA.
