---
title: Boolean reivindica exemplos de transformação para políticas personalizadas
titleSuffix: Azure AD B2C
description: Boolean reivindica exemplos de transformação para o esquema do Quadro de Experiência de Identidade (IEF) do Azure Ative Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 06/06/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 7c292f939339add06168c55236f8666651e4aace
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "85201281"
---
# <a name="boolean-claims-transformations"></a>Boolean reivindica transformações

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Este artigo fornece exemplos para a utilização das transformações de sinistros booleanos do esquema do Quadro de Experiência de Identidade em Azure Ative Directory B2C (Azure AD B2C). Para obter mais informações, consulte [SinistrosTransformações](claimstransformations.md).

## <a name="andclaims"></a>EClaims

Executa um funcionamento de duas entradas booleanAsClaims e define a saídaClaim com o resultado da operação.

| Item  | TransformaçãoClaimType  | Tipo de Dados  | Notas |
|-------| ------------------------ | ---------- | ----- |
| InputClaim | inputClaim1 | boolean | O primeiro ClaimType a avaliar. |
| InputClaim | inputClaim2  | boolean | O segundo ClaimType a avaliar. |
|OutputClaim | outputClaim | boolean | Os ClaimTypes que serão produzidos após esta transformação de alegações foram invocados (verdadeiros ou falsos). |

A transformação das seguintes alegações demonstra como e dois booleans ClaimTypes: `isEmailNotExist` e `isSocialAccount` . A reclamação de saída `presentEmailSelfAsserted` é definida para se o valor de ambos os `true` pedidos de entrada forem `true` . Em um passo de orquestração, você pode usar uma condição prévia para pré-afinar uma página autoafirmada, apenas se um e-mail de conta social estiver vazio.

```xml
<ClaimsTransformation Id="CheckWhetherEmailBePresented" TransformationMethod="AndClaims">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="isEmailNotExist" TransformationClaimType="inputClaim1" />
    <InputClaim ClaimTypeReferenceId="isSocialAccount" TransformationClaimType="inputClaim2" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="presentEmailSelfAsserted" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example-of-andclaims"></a>Exemplo de EClaims

- Reclamações de entrada:
    - **inputClaim1**: verdadeiro
    - **inputClaim2**: falso
- Reclamações de saída:
    - **outputClaim**: falso


## <a name="assertbooleanclaimisequaltovalue"></a>AssertBooleanClaimIsEqualToValue

Verifica se os valores booleanos de duas reivindicações são iguais, e abre uma exceção se não forem.

| Item | TransformaçãoClaimType  | Tipo de Dados  | Notas |
| ---- | ------------------------ | ---------- | ----- |
| inputClaim | inputClaim | boolean | O ClaimType a ser afirmado. |
| InputParameter |valueToCompareTo | boolean | O valor a comparar (verdadeiro ou falso). |

O **AssertBooleanClaimIsEqualToValue** afirma que a transformação é sempre executada a partir de um perfil técnico de [validação](validation-technical-profile.md) que é chamado por um [perfil técnico autoafirmado.](self-asserted-technical-profile.md) Os metadados de perfil técnico autoafirmados **do UserMessageIfClaimsTransformationBooleanValueIsNotEqual** controlam a mensagem de erro que o perfil técnico apresenta ao utilizador. As mensagens de erro podem ser [localizadas](localization-string-ids.md#claims-transformations-error-messages).

![Execução assertStringClaimsAreEqual](./media/boolean-transformations/assert-execution.png)

A transformação de alegações a seguir demonstra como verificar o valor de um Boolean ClaimType com um `true` valor. Se o valor do `accountEnabled` ClaimType for falso, é lançada uma mensagem de erro.

```xml
<ClaimsTransformation Id="AssertAccountEnabledIsTrue" TransformationMethod="AssertBooleanClaimIsEqualToValue">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="accountEnabled" TransformationClaimType="inputClaim" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="valueToCompareTo" DataType="boolean" Value="true" />
  </InputParameters>
</ClaimsTransformation>
```


O `login-NonInteractive` perfil técnico de validação chama a transformação de `AssertAccountEnabledIsTrue` sinistros.

```xml
<TechnicalProfile Id="login-NonInteractive">
  ...
  <OutputClaimsTransformations>
    <OutputClaimsTransformation ReferenceId="AssertAccountEnabledIsTrue" />
  </OutputClaimsTransformations>
</TechnicalProfile>
```

O perfil técnico autoafirmado chama o perfil técnico **de login-NonInteractive de** validação.

```xml
<TechnicalProfile Id="SelfAsserted-LocalAccountSignin-Email">
  <Metadata>
    <Item Key="UserMessageIfClaimsTransformationBooleanValueIsNotEqual">Custom error message if account is disabled.</Item>
  </Metadata>
  <ValidationTechnicalProfiles>
    <ValidationTechnicalProfile ReferenceId="login-NonInteractive" />
  </ValidationTechnicalProfiles>
</TechnicalProfile>
```

### <a name="example-of-assertbooleanclaimisequaltovalue"></a>Exemplo de AssertBooleanClaimIsEqualToValue

- Reclamações de entrada:
    - **inputClaim**: falso
    - **valueToCompareTo**: verdadeiro
- Resultado: Erro lançado

## <a name="comparebooleanclaimtovalue"></a>CompareBooleanClaimToValue

Verifica se o valor booleano de uma reclamação é igual `true` ou , e devolve o resultado da `false` compressão.

| Item | TransformaçãoClaimType  | Tipo de Dados  | Notas |
| ---- | ------------------------ | ---------- | ----- |
| InputClaim | inputClaim | boolean | O ClaimType a ser afirmado. |
| InputParameter |valueToCompareTo | boolean | O valor a comparar (verdadeiro ou falso). |
| OutputClaim | compararResult | boolean | O ClaimType que é produzido após esta ReclamaçãoTransformação foi invocado. |

A transformação de alegações a seguir demonstra como verificar o valor de um Boolean ClaimType com um `true` valor. Se o valor do `IsAgeOver21Years` ClaimType for igual `true` a, a transformação de sinistros retorna, `true` caso contrário `false` .

```xml
<ClaimsTransformation Id="AssertAccountEnabled" TransformationMethod="CompareBooleanClaimToValue">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="IsAgeOver21Years" TransformationClaimType="inputClaim" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="valueToCompareTo" DataType="boolean" Value="true" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim  ClaimTypeReferenceId="accountEnabled" TransformationClaimType="compareResult"/>
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example-of-comparebooleanclaimtovalue"></a>Exemplo de CompareBooleanClaimToValue

- Reclamações de entrada:
    - **inputClaim**: falso
- Parâmetros de entrada:
    - **valueToCompareTo**: verdadeiro
- Reclamações de saída:
    - **compareResult**: falso

## <a name="notclaims"></a>NãoClaims

Executa um Não funcionamento da entrada booleanaClaim e define a saídaClaim com o resultado da operação.

| Item | TransformaçãoClaimType | Tipo de Dados | Notas |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim | boolean | A pretensão de ser operado. |
| OutputClaim | outputClaim | boolean | Os ClaimTypes que são produzidos após esta ReclamaçãoTransformação foi invocado (verdadeiro ou falso). |

Use esta transformação de reivindicação para executar a negação lógica numa reivindicação.

```xml
<ClaimsTransformation Id="CheckWhetherEmailBePresented" TransformationMethod="NotClaims">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="userExists" TransformationClaimType="inputClaim" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="userExists" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example-of-notclaims"></a>Exemplo de NotClaims

- Reclamações de entrada:
    - **inputClaim**: falso
- Reclamações de saída:
    - **outputClaim**: verdadeiro

## <a name="orclaims"></a>OrClaims

Calcula um ou de dois inputs booleanOsClaims e define a saídaClaim com o resultado da operação.

| Item | TransformaçãoClaimType | Tipo de Dados | Notas |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim1 | boolean | O primeiro ClaimType a avaliar. |
| InputClaim | inputClaim2 | boolean | O segundo ClaimType a avaliar. |
| OutputClaim | outputClaim | boolean | Os ClaimTypes que serão produzidos após esta ReclamaçãoTransformação foi invocado (verdadeiro ou falso). |

A transformação de alegações a seguir demonstra como dois `Or` Boolean ClaimTypes. Na etapa de orquestração, pode utilizar uma condição prévia para pré-desofiar uma página autoafirmada, se o valor de uma das reivindicações for `true` .

```xml
<ClaimsTransformation Id="CheckWhetherEmailBePresented" TransformationMethod="OrClaims">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="isLastTOSAcceptedNotExists" TransformationClaimType="inputClaim1" />
    <InputClaim ClaimTypeReferenceId="isLastTOSAcceptedGreaterThanNow" TransformationClaimType="inputClaim2" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="presentTOSSelfAsserted" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example-of-orclaims"></a>Exemplo de OrClaims

- Reclamações de entrada:
    - **inputClaim1**: verdadeiro
    - **inputClaim2**: falso
- Reclamações de saída:
    - **outputClaim**: verdadeiro
