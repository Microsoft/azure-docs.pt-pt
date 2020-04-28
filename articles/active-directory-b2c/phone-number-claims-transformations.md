---
title: Número de telefone reclama transformações em políticas personalizadas
titleSuffix: Azure AD B2C
description: Referência política personalizada para número de telefone reclama transformações em Azure AD B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/26/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: bd26b2b475e293a1fda1b007289ba7c3eef35136
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "78183941"
---
# <a name="define-phone-number-claims-transformations-in-azure-ad-b2c"></a>Definir número de telefone reclama transformações em Azure AD B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Este artigo fornece referência e exemplos para a utilização do número de telefone alega transformações do quadro de experiência de identidade em Azure Ative Directory B2C (Azure AD B2C). Para obter mais informações sobre as transformações de sinistros em geral, consulte [As Transformações de Sinistros](claimstransformations.md).

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="convertphonenumberclaimtostring"></a>Converter Número de Telefone Claimtostring

Converte `phoneNumber` um tipo `string` de dados num tipo de dados.

| Item | Tipo de reclamação de transformação | Tipo de Dados | Notas |
| ---- | ----------------------- | --------- | ----- |
| Pedido de crédito | telefoneNúmero | telefoneNúmero |  O ClaimType para converter numa corda. |
| Pedido de saída | telefoneNumberString | string | O ClaimType que é produzido após esta transformação de sinistros foi invocado. |

Neste exemplo, a alegação do cellNumber `phoneNumber` com um tipo de valor é `string`convertida para uma reivindicação de telemóvelPhone com um tipo de valor de .

```XML
<ClaimsTransformation Id="PhoneNumberToString" TransformationMethod="ConvertPhoneNumberClaimToString">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="cellPhoneNumber" TransformationClaimType="phoneNumber" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="cellPhone" TransformationClaimType="phoneNumberString" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Exemplo

- Créditos de entrada:
  - **telefoneNúmero:**+11234567890 (número de telefone)
- Alegações de saída:
  - **telefoneNumberString**: +11234567890 (cadeia)


## <a name="convertstringtophonenumberclaim"></a>Reconversão stringtoPhoneNumberClaim

Esta transformação de reclamação valida o formato do número de telefone. Se estiver num formato válido, altere-o para um formato padrão utilizado pelo Azure AD B2C. Se o número de telefone fornecido não estiver num formato válido, uma mensagem de erro é devolvida.

| Item | Tipo de reclamação de transformação | Tipo de Dados | Notas |
| ---- | ----------------------- | --------- | ----- |
| Pedido de crédito | telefoneNumberString | string |  A reclamação de cordas para o número de telefone. O número de telefone tem de estar em formato internacional, completo com um código "+" e país. Se for `country` fornecida a reclamação de entrada, o número de telefone encontra-se em formato local (sem o código do país). |
| Pedido de crédito | país | string | [Opcional] A reclamação de cadeias para o código de país do número de telefone no formato ISO3166 (o código de dois letras ISO-3166). |
| Pedido de saída | saídaReclamada | telefoneNúmero | O resultado desta transformação de reivindicações. |

A transformação de reclamações **ConvertStringToPhoneNumberClaim** é sempre executada a partir de um perfil técnico de [validação](validation-technical-profile.md) que é chamado por um [perfil técnico autoafirmado](self-asserted-technical-profile.md) ou controlo de [ecrã](display-controls.md). Os metadados de perfil técnico autoafirmados do **UserMessageIfClaimsTransformationInvalidNumber** controlam a mensagem de erro que é apresentada ao utilizador.

![Diagrama do caminho de execução da mensagem de erro](./media/phone-authentication/assert-execution.png)

Pode utilizar esta transformação de reclamações para garantir que a reclamação de cordas fornecida é um número de telefone válido. Caso contrário, é lançada uma mensagem de erro. O exemplo seguinte verifica se o **telefone String** ClaimType é de facto um número de telefone válido e, em seguida, devolve o número de telefone no formato Padrão Azure AD B2C. Caso contrário, é lançada uma mensagem de erro.

```XML
<ClaimsTransformation Id="ConvertStringToPhoneNumber" TransformationMethod="ConvertStringToPhoneNumberClaim">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="phoneString" TransformationClaimType="phoneNumberString" />
    <InputClaim ClaimTypeReferenceId="countryCode" TransformationClaimType="country" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="phoneNumber" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

O perfil técnico autoafirmado que chama o perfil técnico de validação que contém esta transformação de sinistros pode definir a mensagem de erro.

```XML
<TechnicalProfile Id="SelfAsserted-LocalAccountSignup-Phone">
  <Metadata>
    <Item Key="UserMessageIfClaimsTransformationInvalidPhoneNumber">Custom error message if the phone number is not valid.</Item>
  </Metadata>
  ...
</TechnicalProfile>
```

### <a name="example-1"></a>Exemplo 1

- Créditos de entrada:
  - **telefoneNumberString**: 033 456-7890
  - **país**: DK
- Alegações de saída:
  - **saídaSReclamação:**+450334567890

### <a name="example-2"></a>Exemplo 2

- Créditos de entrada:
  - **telefoneNumberString:**+1 (123) 456-7890
- Alegações de saída:
  - **saídaSReclamação:**+11234567890


## <a name="getnationalnumberandcountrycodefromphonenumberstring"></a>GetNationalNumberandCountryCodeFromNumberstring

Isto extrai o código do país e o número nacional da alegação de entrada, e opcionalmente lança uma exceção se o número de telefone fornecido não for válido.

| Item | Tipo de reclamação de transformação | Tipo de Dados | Notas |
| ---- | ----------------------- | --------- | ----- |
| Pedido de crédito | telefoneNúmero | string | A alegação de corda do número de telefone. O número de telefone tem de estar em formato internacional, completo com um código "+" e país. |
| EntradaParametro | throwExceptionOnFailure | boolean | [Opcional] Um parâmetro que indique se é lançada uma exceção quando o número de telefone não é válido. O valor predefinido é falso. |
| EntradaParametro | paísCodeType | string | [Opcional] Um parâmetro que indique o tipo de código do país na reivindicação de saída. Os valores disponíveis são **CallCode** (o código de chamada internacional para um país, por exemplo +1) ou **ISO3166** (o código de dois letras ISO-3166). |
| Pedido de saída | nacionalNúmero | string | A reivindicação de cordas para o número nacional do número de telefone. |
| Pedido de saída | paísCódigo | string | A reivindicação de cordas para o código do país do número de telefone. |


Se o **GetNationalNumberAndCountryCodeFromNumberString** a transformação é executada a partir de um perfil técnico de [validação](validation-technical-profile.md) que é chamado por um [perfil técnico autoafirmado](self-asserted-technical-profile.md) ou por uma ação de controlo de [ecrã](display-controls.md#display-control-actions), então o **userMessageIfNumberParseFailure** autoafirmado metadados de perfil técnico controla a mensagem de erro que é apresentada ao utilizador.

![Diagrama do caminho de execução da mensagem de erro](./media/phone-authentication/assert-execution.png)

Pode usar esta transformação de sinistros para dividir um número de telefone completo no código do país e no número nacional. Se o número de telefone fornecido não for válido, pode optar por enviar uma mensagem de erro.

O exemplo que se segue tenta dividir o número de telefone em número nacional e código de país. Se o número de telefone for válido, o número de telefone será ultrapassado pelo número nacional. Se o número de telefone não for válido, não será lançada uma exceção e o número de telefone ainda tem o seu valor original.

```XML
<ClaimsTransformation Id="GetNationalNumberAndCountryCodeFromPhoneNumberString" TransformationMethod="GetNationalNumberAndCountryCodeFromPhoneNumberString">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="phoneNumber" TransformationClaimType="phoneNumber" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="throwExceptionOnFailure" DataType="boolean" Value="false" />
    <InputParameter Id="countryCodeType" DataType="string" Value="ISO3166" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="nationalNumber" TransformationClaimType="nationalNumber" />
    <OutputClaim ClaimTypeReferenceId="countryCode" TransformationClaimType="countryCode" />
  </OutputClaims>
</ClaimsTransformation>
```

O perfil técnico autoafirmado que chama o perfil técnico de validação que contém esta transformação de sinistros pode definir a mensagem de erro.

```XML
<TechnicalProfile Id="SelfAsserted-LocalAccountSignup-Phone">
  <Metadata>
    <Item Key="UserMessageIfPhoneNumberParseFailure">Custom error message if the phone number is not valid.</Item>
  </Metadata>
  ...
</TechnicalProfile>
```

### <a name="example-1"></a>Exemplo 1

- Créditos de entrada:
  - **telefoneNúmero:**+49 (123) 456-7890
- Parâmetros de entrada:
  - **throwExceptionOnFailure**: falso
  - **paísCódigoType**: ISO3166
- Alegações de saída:
  - **nacionalNúmero**: 1234567890
  - **paísCódigo**: DE

### <a name="example-2"></a>Exemplo 2

- Créditos de entrada:
  - **telefoneNúmero:**+49 (123) 456-7890
- Parâmetros de entrada
  - **throwExceptionOnFailure**: falso
  - **paísCódigoType**: Código de chamada
- Alegações de saída:
  - **nacionalNúmero**: 1234567890
  - **paísCódigo**: +49
