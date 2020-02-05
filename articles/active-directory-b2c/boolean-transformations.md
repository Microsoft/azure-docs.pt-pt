---
title: Boolean reclama exemplos de transformação para políticas personalizadas
titleSuffix: Azure AD B2C
description: Boolean reclama exemplos de transformação para o quadro de experiência de identidade (IEF) do Azure Ative Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 02/03/2020
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: f0d6d74271cc4ff0be4a653b389cc70ad5c56ef9
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/04/2020
ms.locfileid: "76983083"
---
# <a name="boolean-claims-transformations"></a>Boolean reclama transformações

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Este artigo fornece exemplos para a utilização das transformações booleanas do quadro de experiência de identidade no Azure Ative Directory B2C (Azure AD B2C). Para obter mais informações, consulte [ClaimsTransformations](claimstransformations.md).

## <a name="andclaims"></a>E Reivindicações

Executa uma operação de duas inputClaims booleanas e define a saídaClaim com resultado da operação.

| Item  | TransformationClaimType  | Tipo de Dados  | Notas |
|-------| ------------------------ | ---------- | ----- |
| InputClaim | inputClaim1 | boolean | O primeiro ClaimType a avaliar. |
| InputClaim | inputClaim2  | boolean | O segundo ClaimType a avaliar. |
|OutputClaim | outputClaim | boolean | Os Tipos de Reclamação que serão produzidos após esta transformação de sinistros ter sido invocado (verdadeiro ou falso). |

A transformação de reivindicações que se segue demonstra como e duas reivindicações booleanas: `isEmailNotExist`, e `isSocialAccount`. A `presentEmailSelfAsserted` de reivindicação de saída deverá `true` se o valor de ambos os créditos de entrada for em `true`. Num passo de orquestração, pode utilizar uma condição prévia para pré-configurar uma página autoafirmada, apenas se um e-mail de conta social estiver vazio.

```XML
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

### <a name="example"></a>Exemplo

- Declarações de entrada:
    - **inputClaim1**: verdadeiro
    - **inputClaim2**: falso
- Declarações de saída:
    - **saídaClaim**: falso


## <a name="assertbooleanclaimisequaltovalue"></a>AssertBooleanClaimIsEqualToValue

Verifica que os valores booleanos de duas reivindicações são iguais, e abre uma exceção se não forem.

| Item | TransformationClaimType  | Tipo de Dados  | Notas |
| ---- | ------------------------ | ---------- | ----- |
| InputClaim | InputClaim | boolean | O ClaimType a afirmar. |
| InputParameter |valueToCompareTo | boolean | O valor a comparar (verdadeiro ou falso). |

A transformação de **afirmações assertBooleanClaimIsEqualToValue** é sempre executada a partir de um perfil técnico de [validação](validation-technical-profile.md) que é chamado por um [perfil técnico autoafirmado](self-asserted-technical-profile.md). O **UserMessageIfClaimsTransformationBooleanIsNotEqual** os metadados de perfil técnico autoafirmado controlam a mensagem de erro que o perfil técnico apresenta ao utilizador.

![AssertstringClaimsAreIgual execução](./media/boolean-transformations/assert-execution.png)

A transformação de reclamações seguinte demonstra como verificar o valor de um ClaimType booleano com um valor `true`. Se o valor do `accountEnabled` ClaimType for falso, é lançada uma mensagem de erro.

```XML
<ClaimsTransformation Id="AssertAccountEnabledIsTrue" TransformationMethod="AssertBooleanClaimIsEqualToValue">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="accountEnabled" TransformationClaimType="inputClaim" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="valueToCompareTo" DataType="boolean" Value="true" />
  </InputParameters>
</ClaimsTransformation>
```


O perfil técnico de validação `login-NonInteractive` chama a transformação de reivindicações `AssertAccountEnabledIsTrue`.
```XML
<TechnicalProfile Id="login-NonInteractive">
  ...
  <OutputClaimsTransformations>
    <OutputClaimsTransformation ReferenceId="AssertAccountEnabledIsTrue" />
  </OutputClaimsTransformations>
</TechnicalProfile>
```

O perfil técnico autoafirmado chama o perfil técnico **login-nonInteractive** de validação.

```XML
<TechnicalProfile Id="SelfAsserted-LocalAccountSignin-Email">
  <Metadata>
    <Item Key="UserMessageIfClaimsTransformationBooleanValueIsNotEqual">Custom error message if account is disabled.</Item>
  </Metadata>
  <ValidationTechnicalProfiles>
    <ValidationTechnicalProfile ReferenceId="login-NonInteractive" />
  </ValidationTechnicalProfiles>
</TechnicalProfile>
```

### <a name="example"></a>Exemplo

- Declarações de entrada:
    - **inputClaim**: falso
    - **valorToCompareTo:** verdadeiro
- Resultado: Erro lançado

## <a name="comparebooleanclaimtovalue"></a>CompareBooleanClaimToValue

Verifica se o valor booleano de uma reclamação é igual a `true` ou `false`, e devolve o resultado da compressão. 

| Item | TransformationClaimType  | Tipo de Dados  | Notas |
| ---- | ------------------------ | ---------- | ----- |
| InputClaim | InputClaim | boolean | O ClaimType a afirmar. |
| InputParameter |valueToCompareTo | boolean | O valor a comparar (verdadeiro ou falso). |
| OutputClaim | InputClaim | boolean | O ClaimType que é produzido após esta Transformação de Reclamações foi invocado. |


A transformação de reclamações seguinte demonstra como verificar o valor de um ClaimType booleano com um valor `true`. Se o valor do `IsAgeOver21Years` ClaimType for igual a `true`, a transformação de sinistros retorna `true`, caso contrário `false`.

```XML
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

### <a name="example"></a>Exemplo

- Declarações de entrada:
    - **inputClaim**: falso
- Parâmetros de entrada:
    - **valorToCompareTo:** verdadeiro
- Declarações de saída:
    - **compararResultado:** falso 



## <a name="notclaims"></a>NotClaims

Executa uma operação não da inputClaim booleana e define a saídaClaim com resultado da operação.

| Item | TransformationClaimType | Tipo de Dados | Notas |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | InputClaim | boolean | A pretensão de ser operada. |
| OutputClaim | outputClaim | boolean | Os Tipos de Reclamação que são produzidos após esta Transformação de Reclamações foi invocado (verdadeiro ou falso). |

Use esta transformação de reivindicação para realizar negação lógica numa reivindicação.

```XML
<ClaimsTransformation Id="CheckWhetherEmailBePresented" TransformationMethod="NotClaims">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="userExists" TransformationClaimType="inputClaim" />
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="userExists" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Exemplo

- Declarações de entrada:
    - **inputClaim**: falso
- Declarações de saída:
    - **saídaReivindicação**: verdadeiro

## <a name="orclaims"></a>Reclamações

Computa um Ou de duas inputClaims booleanas e define a saídaClaim com resultado da operação.

| Item | TransformationClaimType | Tipo de Dados | Notas |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim1 | boolean | O primeiro ClaimType a avaliar. |
| InputClaim | inputClaim2 | boolean | O segundo ClaimType a avaliar. |
| OutputClaim | outputClaim | boolean | Os Tipos de Reclamação que serão produzidos após esta Transformação de Reclamações ter sido invocada (verdadeira ou falsa). |

A transformação de sinistros seguinte demonstra como `Or` dois tipos de reclamações booleanos. No passo da orquestração, pode utilizar uma condição prévia para pré-configurar uma página autoafirmada, se o valor de uma das reclamações for `true`.

```XML
<ClaimsTransformation Id="CheckWhetherEmailBePresented" TransformationMethod="OrClaims">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="isLastTOSAcceptedNotExists" TransformationClaimType="inputClaim1" />
    <InputClaim ClaimTypeReferenceId="isLastTOSAcceptedGreaterThanNow" TransformationClaimType="inputClaim2" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="presentTOSSelfAsserted" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
</ClaimsTransformation>
```

### <a name="example"></a>Exemplo

- Declarações de entrada:
    - **inputClaim1**: verdadeiro
    - **inputClaim2**: falso
- Declarações de saída:
    - **saídaReivindicação**: verdadeiro
