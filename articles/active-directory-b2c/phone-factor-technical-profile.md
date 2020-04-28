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
ms.date: 03/31/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: e2b30e8f6bcbe7c0e739455f4942712f68ff8404
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80437454"
---
# <a name="define-a-phone-factor-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Defina um perfil técnico de fator telefone numa política personalizada do Diretório Ativo Azure B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

O Azure Ative Directory B2C (Azure AD B2C) fornece suporte para a inscrição e verificação dos números de telefone. Este perfil técnico:

- Fornece uma interface de utilizador para interagir com o utilizador para verificar ou inscrever um número de telefone.
- Suporta chamadas telefónicas e mensagens de texto para validar o número de telefone.
- Suporta vários números de telefone. O utilizador pode selecionar um dos números de telefone para verificar.  
- Devolve uma reclamação indicando se o utilizador forneceu um novo número de telefone. Pode utilizar esta alegação para decidir se o número de telefone deve ser percenóno ao perfil de utilizador Do Azure AD B2C.  
- Usa uma [definição](contentdefinitions.md) de conteúdo para controlar o aspeto e a sensação.

## <a name="protocol"></a>Protocolo

O **atributo** nome do elemento **protocolo** `Proprietary`tem de ser definido para . O atributo do **manipulador** deve conter o nome totalmente qualificado do conjunto de manipulador estomato do protocolo que é utilizado pelo Azure AD B2C para o fator telefone:`Web.TPEngine.Providers.PhoneFactorProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null`

O exemplo seguinte mostra um perfil técnico de fator telefone para inscrição e validação:

```XML
<TechnicalProfile Id="PhoneFactor-InputOrVerify">
  <DisplayName>PhoneFactor</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.PhoneFactorProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
</TechnicalProfile>
```

## <a name="input-claims-transformations"></a>Input reclama transformações

O elemento InputClaimsTransformations pode conter uma coleção de transformações de créditos de entrada que são usadas para modificar as reclamações de entrada, ou gerar novas. A seguinte transformação de `UserId` créditos de entrada gera uma reivindicação que é usada mais tarde na recolha de créditos de entrada.

```xml
<InputClaimsTransformations>
  <InputClaimsTransformation ReferenceId="CreateUserIdForMFA" />
</InputClaimsTransformations>
```

## <a name="input-claims"></a>Reclamações de entrada

O elemento InputClaims deve conter as seguintes reclamações. Também pode mapear o nome da sua reclamação para o nome definido no perfil técnico do fator de telefone. 

|  Tipo de dados| Necessário | Descrição |
| --------- | -------- | ----------- | 
| string| Sim | Um identificador único para o utilizador. O nome da reclamação, ou `UserId`PartnerClaimType deve ser definido para . Esta alegação não deve conter informações pessoais identificáveis.|
| string| Sim | Lista de tipos de reclamações. Cada reclamação contém um número de telefone. Se alguma das reclamações de entrada não contiver um número de telefone, o utilizador será solicitado a inscrever-se e verificar um novo número de telefone. O número de telefone validado é devolvido como uma reivindicação de saída. Se uma das alegações de entrada contiver um número de telefone, o utilizador é solicitado a verificar. Se várias reclamações de entrada contiverem um número de telefone, o utilizador é solicitado a escolher e verificar um dos números de telefone. |

O exemplo que se segue demonstra a utilização de vários números de telefone. Para mais informações, consulte a [política da amostra.](https://github.com/azure-ad-b2c/samples/tree/master/policies/mfa-add-secondarymfa)

```XML
<InputClaims>
  <InputClaim ClaimTypeReferenceId="userIdForMFA" PartnerClaimType="UserId" />
  <InputClaim ClaimTypeReferenceId="strongAuthenticationPhoneNumber" />
  <InputClaim ClaimTypeReferenceId="secondaryStrongAuthenticationPhoneNumber" />
</InputClaims>
```

## <a name="output-claims"></a>Reclamações de produção

O elemento OutputClaims contém uma lista de reclamações devolvidas pelo perfil técnico do fator telefone.

|  Tipo de dados| Necessário | Descrição |
|  -------- | ----------- |----------- |
| boolean | Sim | Indica se o novo número de telefone foi introduzido pelo utilizador. O nome da reclamação, ou PartnerClaimType deve ser definido para`newPhoneNumberEntered`|
| string| Sim | O número de telefone verificado. O nome da reclamação, ou `Verified.OfficePhone`PartnerClaimType deve ser definido para .|

O elemento OutputClaimsTransformations pode conter uma coleção de elementos outputClaimsTransformation que são usados para modificar as reclamações de saída, ou gerar novos.

## <a name="cryptographic-keys"></a>Chaves criptográficas

O elemento **CryptographicKeys** não é utilizado.


## <a name="metadata"></a>Metadados

| Atributo | Necessário | Descrição |
| --------- | -------- | ----------- |
| ContentDefinitionReferenceId | Sim | O identificador da [definição](contentdefinitions.md) de conteúdo associado a este perfil técnico. |
| ManualPhoneNumberEntryAllowed| Não | Especifique se um utilizador pode ou não introduzir manualmente um número de telefone. Valores `true`possíveis: ou `false` (padrão).|
| definição.autenticaçãoMode | Não | O método para validar o número de telefone. Valores `sms`possíveis: , `phone`ou `mixed` (padrão).|
| definição.autodial| Não| Especifique se o perfil técnico deve ligar automaticamente ou enviar automaticamente um SMS. Valores `true`possíveis: ou `false` (padrão). O mostrador `setting.authenticationMode` automático requer que `sms`os `phone`metadados sejam definidos para, ou . A recolha de créditos de entrada deve ter um único número de telefone. |

### <a name="ui-elements"></a>Elementos da IU

Os elementos de interface do utilizador da página de autenticação do fator telefone podem ser [localizados](localization-string-ids.md#azure-mfa-error-messages).

## <a name="next-steps"></a>Passos seguintes

- Consulte as [contas sociais e locais com](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/SocialAndLocalAccountsWithMfa) pacote de arranque MFA.
