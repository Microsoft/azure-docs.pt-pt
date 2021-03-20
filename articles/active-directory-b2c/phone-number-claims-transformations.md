---
title: Número de telefone reclama transformações em políticas personalizadas
titleSuffix: Azure AD B2C
description: Referência de política personalizada para número de telefone reclama transformações em Azure AD B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 02/26/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: e175a81efc1ab0950c1fda314efb206ff97a2b7f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "85385387"
---
# <a name="define-phone-number-claims-transformations-in-azure-ad-b2c"></a>Definir número de telefone reclama transformações em Azure AD B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Este artigo fornece referências e exemplos para a utilização do número de telefone alega transformações do esquema do Quadro de Experiência de Identidade em Azure Ative Directory B2C (Azure AD B2C). Para obter mais informações sobre as transformações de sinistros em geral, consulte [SinistrosTransformações](claimstransformations.md).

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="convertphonenumberclaimtostring"></a>ConvertePhoneNumberClaimToString

Converte um `phoneNumber` tipo de dado num tipo de `string` dado.

| Item | TransformaçãoClaimType | Tipo de Dados | Notas |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | número de telefone | número de telefone |  O ClaimType para converter para uma corda. |
| OutputClaim | phoneNumberString | string | O ClaimType que é produzido após esta transformação de alegações foi invocado. |

Neste exemplo, a alegação do telemóvelPhoneNumber com um tipo de valor `phoneNumber` é convertida para uma alegação de telemóvel com um tipo de valor de `string` .

```xml
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

- Reclamações de entrada:
  - **telefoneNumber**: +11234567890 (telefoneNumber)
- Reclamações de saída:
  - **phoneNumberString**: +11234567890 (cadeia)


## <a name="convertstringtophonenumberclaim"></a>ConverteStringToPhoneNumberClaim

Esta transformação de reclamação valida o formato do número de telefone. Se estiver num formato válido, altere-o para um formato padrão utilizado pelo Azure AD B2C. Se o número de telefone fornecido não estiver num formato válido, é devolvida uma mensagem de erro.

| Item | TransformaçãoClaimType | Tipo de Dados | Notas |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | phoneNumberString | string |  A alegação de corda para o número de telefone. O número de telefone tem de ser em formato internacional, completo com um código "+" líder e país/região. Se a reclamação de entrada `country` for fornecida, o número de telefone está no formato local (sem o código país/região). |
| InputClaim | país | string | [Opcional] A alegação de cadeia para o código país/região do número de telefone no formato ISO3166 (o código de duas letras ISO-3166 país/região). |
| OutputClaim | outputClaim | número de telefone | O resultado desta transformação de sinistros. |

O **ConvertStringToPhoneNumberClaim** afirma que a transformação é sempre executada a partir de um perfil técnico de [validação](validation-technical-profile.md) que é chamado por um [perfil técnico autoafirmado](self-asserted-technical-profile.md) ou controlo de [exibição](display-controls.md). O **userMessageIfClaimsTransformationInvalidPhoneNumber** autoafirmado perfis técnicos metadados controlam a mensagem de erro que é apresentada ao utilizador.

![Diagrama do caminho de execução da mensagem de erro](./media/phone-authentication/assert-execution.png)

Pode utilizar esta transformação de sinistros para garantir que a reclamação de cadeia fornecida é um número de telefone válido. Caso contrário, é lançada uma mensagem de erro. O exemplo seguinte verifica se o **telefoneString** ClaimType é de facto um número de telefone válido e, em seguida, devolve o número de telefone no formato Azure AD B2C padrão. Caso contrário, é lançada uma mensagem de erro.

```xml
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

```xml
<TechnicalProfile Id="SelfAsserted-LocalAccountSignup-Phone">
  <Metadata>
    <Item Key="UserMessageIfClaimsTransformationInvalidPhoneNumber">Custom error message if the phone number is not valid.</Item>
  </Metadata>
  ...
</TechnicalProfile>
```

### <a name="example-1"></a>Exemplo 1

- Reclamações de entrada:
  - **phoneNumberString**: 033 456-7890
  - **país**: DK
- Reclamações de saída:
  - **outputClaim**: +450334567890

### <a name="example-2"></a>Exemplo 2

- Reclamações de entrada:
  - **telefoneNumberString**: +1 (123) 456-7890
- Reclamações de saída:
  - **outputClaim**: +11234567890


## <a name="getnationalnumberandcountrycodefromphonenumberstring"></a>GetNationalNumberAndCountryCodeFromPhoneNumberString

Isto extrai o código país/região e o número nacional da reclamação de entrada, e opcionalmente abre uma exceção se o número de telefone fornecido não for válido.

| Item | TransformaçãoClaimType | Tipo de Dados | Notas |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | número de telefone | string | A alegação de cadeia do número de telefone. O número de telefone tem de ser em formato internacional, completo com um código "+" líder e país/região. |
| InputParameter | throwExceptionOnFailure | boolean | [Opcional] Um parâmetro que indique se uma exceção é lançada quando o número de telefone não é válido. O valor predefinido é falso. |
| InputParameter | paísCodeType | string | [Opcional] Um parâmetro que indique o tipo de código país/região na reivindicação de saída. Os valores disponíveis são **CallCode** (o código de chamada internacional para um país/região, por exemplo +1) ou **ISO3166** (o código de duas letras ISO-3166 país/região). |
| OutputClaim | NacionalNumber | string | A alegação de corda para o número nacional do número de telefone. |
| OutputClaim | paísDesco | string | A alegação de cadeia para o código país/região do número de telefone. |


Se a transformação de reclamações **GetNationalNumberAndCountryCodeFromPhoneNumberSing** for executada a partir de um perfil técnico de [validação](validation-technical-profile.md) que é chamado por um [perfil técnico autoafirmado](self-asserted-technical-profile.md) ou por uma ação de [controlo do ecrã](display-controls.md#display-control-actions), então o **userMessageIfPhoneNumberParseFailure** autoafirmada de perfil técnico controla a mensagem de erro que é apresentada ao utilizador.

![Diagrama do caminho de execução da mensagem de erro](./media/phone-authentication/assert-execution.png)

Pode utilizar esta transformação de reclamações para dividir um número de telefone completo no código país/região e no número nacional. Se o número de telefone fornecido não for válido, pode optar por enviar uma mensagem de erro.

O exemplo a seguir tenta dividir o número de telefone em número nacional e código país/região. Se o número de telefone for válido, o número de telefone será ultrapassado pelo número nacional. Se o número de telefone não for válido, não será lançada uma exceção e o número de telefone ainda tem o seu valor original.

```xml
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

```xml
<TechnicalProfile Id="SelfAsserted-LocalAccountSignup-Phone">
  <Metadata>
    <Item Key="UserMessageIfPhoneNumberParseFailure">Custom error message if the phone number is not valid.</Item>
  </Metadata>
  ...
</TechnicalProfile>
```

### <a name="example-1"></a>Exemplo 1

- Reclamações de entrada:
  - **telefone Número**: +49 (123) 456-7890
- Parâmetros de entrada:
  - **throwExceptionOnFailure**: falso
  - **countryCodeType**: ISO3166
- Reclamações de saída:
  - **nacional Número**: 1234567890
  - **countryCode**: DE

### <a name="example-2"></a>Exemplo 2

- Reclamações de entrada:
  - **telefone Número**: +49 (123) 456-7890
- Parâmetros de entrada
  - **throwExceptionOnFailure**: falso
  - **paísCodeType**: Chamada Código
- Reclamações de saída:
  - **nacional Número**: 1234567890
  - **countryCode**: +49
