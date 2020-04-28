---
title: Boolean reclama exemplos de transformação para políticas personalizadas
titleSuffix: Azure AD B2C
description: Boolean reclama exemplos de transformação para o quadro de experiência de identidade (IEF) do Azure Ative Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 04/01/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 007d613a1f170a0ee278a838c92ade2fce9c6dec
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80529193"
---
# <a name="boolean-claims-transformations"></a>Boolean reclama transformações

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Este artigo fornece exemplos para a utilização das transformações booleanas do quadro de experiência de identidade no Azure Ative Directory B2C (Azure AD B2C). Para mais informações, consulte [ClaimsTransformations](claimstransformations.md).

## <a name="andclaims"></a>E Reivindicações

Executa uma operação de duas inputClaims booleanas e define a saídaClaim com resultado da operação.

| Item  | Tipo de reclamação de transformação  | Tipo de Dados  | Notas |
|-------| ------------------------ | ---------- | ----- |
| Pedido de crédito | inputClaim1 | boolean | O primeiro ClaimType a avaliar. |
| Pedido de crédito | inputClaim2  | boolean | O segundo ClaimType a avaliar. |
|Pedido de saída | saídaReclamada | boolean | Os Tipos de Reclamação que serão produzidos após esta transformação de sinistros ter sido invocado (verdadeiro ou falso). |

A transformação de reivindicações que se aseguir demonstra como e duas alegações booleanas: `isEmailNotExist`e `isSocialAccount`. A reclamação `presentEmailSelfAsserted` de `true` saída é definida para `true`se o valor de ambos os créditos de entrada forem . Num passo de orquestração, pode utilizar uma condição prévia para pré-configurar uma página autoafirmada, apenas se um e-mail de conta social estiver vazio.

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

- Créditos de entrada:
    - **inputClaim1**: verdadeiro
    - **inputClaim2**: falso
- Alegações de saída:
    - **saídaClaim**: falso


## <a name="assertbooleanclaimisequaltovalue"></a>Assertboolean ClaimIsEqualToValue

Verifica que os valores booleanos de duas reivindicações são iguais, e abre uma exceção se não forem.

| Item | Tipo de reclamação de transformação  | Tipo de Dados  | Notas |
| ---- | ------------------------ | ---------- | ----- |
| inputClaim | inputClaim | boolean | O ClaimType a afirmar. |
| EntradaParametro |valueToCompareto | boolean | O valor a comparar (verdadeiro ou falso). |

A transformação de **afirmações assertBooleanClaimIsEqualToValue** é sempre executada a partir de um perfil técnico de [validação](validation-technical-profile.md) que é chamado por um [perfil técnico autoafirmado](self-asserted-technical-profile.md). O **UserMessageIfClaimsTransformationBooleanIsNotEqual** os metadados de perfil técnico autoafirmado controlam a mensagem de erro que o perfil técnico apresenta ao utilizador. As mensagens de erro podem ser [localizadas.](localization-string-ids.md#claims-transformations-error-messages)

![AssertstringClaimsAreIgual execução](./media/boolean-transformations/assert-execution.png)

A transformação de reclamações seguinte demonstra como verificar o `true` valor de um Bequetipo de Reclamação booleano com um valor. Se o valor `accountEnabled` do ClaimType for falso, é lançada uma mensagem de erro.

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


O `login-NonInteractive` perfil técnico `AssertAccountEnabledIsTrue` de validação chama a transformação de sinistros.
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

- Créditos de entrada:
    - **inputClaim**: falso
    - **valorToCompareTo:** verdadeiro
- Resultado: Erro lançado

## <a name="comparebooleanclaimtovalue"></a>CompareBooleanClaimToValue

Verifica se o valor booleano `true` `false`de uma reclamação é igual ou, e devolve o resultado da compressão.

| Item | Tipo de reclamação de transformação  | Tipo de Dados  | Notas |
| ---- | ------------------------ | ---------- | ----- |
| Pedido de crédito | inputClaim | boolean | O ClaimType a afirmar. |
| EntradaParametro |valueToCompareto | boolean | O valor a comparar (verdadeiro ou falso). |
| Pedido de saída | compararResultado | boolean | O ClaimType que é produzido após esta Transformação de Reclamações foi invocado. |


A transformação de reclamações seguinte demonstra como verificar o `true` valor de um Bequetipo de Reclamação booleano com um valor. Se o valor `IsAgeOver21Years` do ClaimType `true`for igual `true`a , `false`a transformação de sinistros devoluções, caso contrário.

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

- Créditos de entrada:
    - **inputClaim**: falso
- Parâmetros de entrada:
    - **valorToCompareTo:** verdadeiro
- Alegações de saída:
    - **compararResultado:** falso



## <a name="notclaims"></a>Notalegações

Executa uma operação não da inputClaim booleana e define a saídaClaim com resultado da operação.

| Item | Tipo de reclamação de transformação | Tipo de Dados | Notas |
| ---- | ----------------------- | --------- | ----- |
| Pedido de crédito | inputClaim | boolean | A pretensão de ser operada. |
| Pedido de saída | saídaReclamada | boolean | Os Tipos de Reclamação que são produzidos após esta Transformação de Reclamações foi invocado (verdadeiro ou falso). |

Use esta transformação de reivindicação para realizar negação lógica numa reivindicação.

```XML
<ClaimsTransformation Id="CheckWhetherEmailBePresented" TransformationMethod="NotClaims">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="userExists" TransformationClaimType="inputClaim" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="userExists" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Exemplo

- Créditos de entrada:
    - **inputClaim**: falso
- Alegações de saída:
    - **saídaReivindicação**: verdadeiro

## <a name="orclaims"></a>Reclamações

Computa um Ou de duas inputClaims booleanas e define a saídaClaim com resultado da operação.

| Item | Tipo de reclamação de transformação | Tipo de Dados | Notas |
| ---- | ----------------------- | --------- | ----- |
| Pedido de crédito | inputClaim1 | boolean | O primeiro ClaimType a avaliar. |
| Pedido de crédito | inputClaim2 | boolean | O segundo ClaimType a avaliar. |
| Pedido de saída | saídaReclamada | boolean | Os Tipos de Reclamação que serão produzidos após esta Transformação de Reclamações ter sido invocada (verdadeira ou falsa). |

A transformação de reclamações que se segue demonstra como a `Or` dois tipos de reclamações booleanos. No passo da orquestração, pode utilizar uma condição prévia para pré-configurar uma página `true`autoafirmada, se o valor de uma das reclamações for .

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
```

### <a name="example"></a>Exemplo

- Créditos de entrada:
    - **inputClaim1**: verdadeiro
    - **inputClaim2**: falso
- Alegações de saída:
    - **saídaReivindicação**: verdadeiro
