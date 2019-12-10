---
title: Exemplos de transformação de declarações booleanas para políticas personalizadas
titleSuffix: Azure AD B2C
description: Exemplos de transformação de declarações booleanas para o esquema IEF (Identity Experience Framework) de Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: dcebcc3e2021938f3fd3bde236ef08e4f26b8a97
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/09/2019
ms.locfileid: "74949896"
---
# <a name="boolean-claims-transformations"></a>Transformações de declarações booleanas

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Este artigo fornece exemplos de como usar as transformações de declarações booleanas do esquema de estrutura de experiência de identidade em Azure Active Directory B2C (Azure AD B2C). Para obter mais informações, consulte [ClaimsTransformations](claimstransformations.md).

## <a name="andclaims"></a>AndClaims

Executa uma operação and de dois inputClaims Boolianos e define o outputClaim com o resultado da operação.

| Item  | TransformationClaimType  | Tipo de Dados  | Notas |
|-------| ------------------------ | ---------- | ----- |
| inputClaim | inputClaim1 | boolean | O primeiro ClaimType a ser avaliado. |
| inputClaim | inputClaim2  | boolean | O segundo ClaimType a ser avaliado. |
|OutputClaim | outputClaim | boolean | O ClaimTypes que será produzido depois que essa transformação de declarações tiver sido invocada (true ou false). |

A transformação de declarações a seguir demonstra como e dois ClaimTypes boolianos: `isEmailNotExist`e `isSocialAccount`. O `presentEmailSelfAsserted` de declaração de saída é definido como `true` se o valor de ambas as declarações de entrada for `true`. Em uma etapa de orquestração, você pode usar uma pré-condição para predefinir uma página autodeclarada, somente se um email de conta social estiver vazio.

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
    - **inputClaim1**: true
    - **inputClaim2**: false
- Declarações de saída:
    - **outputClaim**: false


## <a name="assertbooleanclaimisequaltovalue"></a>AssertBooleanClaimIsEqualToValue

Verifica se os valores Boolianos de duas declarações são iguais e gera uma exceção se elas não forem.

| Item | TransformationClaimType  | Tipo de Dados  | Notas |
| ---- | ------------------------ | ---------- | ----- |
| inputClaim | inputClaim | boolean | O ClaimType a ser declarado. |
| InputParameter |valueToCompareTo | boolean | O valor a ser comparado (true ou false). |

A transformação declarações **AssertBooleanClaimIsEqualToValue** é sempre executada de um [perfil técnico de validação](validation-technical-profile.md) que é chamado por um [perfil técnico autodeclarado](self-asserted-technical-profile.md). Os metadados do perfil técnico autodeclarado **UserMessageIfClaimsTransformationBooleanValueIsNotEqual** controlam a mensagem de erro que o perfil técnico apresenta ao usuário.

![Execução de AssertStringClaimsAreEqual](./media/boolean-transformations/assert-execution.png)

A transformação de declarações a seguir demonstra como verificar o valor de um ClaimType booliano com um valor `true`. Se o valor de `accountEnabled` ClaimType for false, uma mensagem de erro será lançada.

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


O perfil técnico de validação de `login-NonInteractive` chama a transformação declarações de `AssertAccountEnabledIsTrue`.
```XML
<TechnicalProfile Id="login-NonInteractive">
  ...
  <OutputClaimsTransformations>
    <OutputClaimsTransformation ReferenceId="AssertAccountEnabledIsTrue" />
  </OutputClaimsTransformations>
</TechnicalProfile>
```

O perfil técnico autodeclarado chama o logon de validação **–** perfil técnico não interativo.

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
    - **inputClaim**: false
    - **valueToCompareTo**: true
- Resultado: erro gerado

## <a name="notclaims"></a>NotClaims

Executa uma operação not do inputClaim booliano e define o outputClaim com o resultado da operação.

| Item | TransformationClaimType | Tipo de Dados | Notas |
| ---- | ----------------------- | --------- | ----- |
| inputClaim | inputClaim | boolean | A declaração a ser operada. |
| OutputClaim | outputClaim | boolean | As ClaimTypes que são produzidas após esse ClaimsTransformation foram invocadas (true ou false). |

Use essa transformação de declaração para executar a negação lógica em uma declaração.

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
    - **inputClaim**: false
- Declarações de saída:
    - **outputClaim**: true

## <a name="orclaims"></a>OrClaims

Computa um ou dois inputClaims Boolianos e define o outputClaim com o resultado da operação.

| Item | TransformationClaimType | Tipo de Dados | Notas |
| ---- | ----------------------- | --------- | ----- |
| inputClaim | inputClaim1 | boolean | O primeiro ClaimType a ser avaliado. |
| inputClaim | inputClaim2 | boolean | O segundo ClaimType a ser avaliado. |
| OutputClaim | outputClaim | boolean | O ClaimTypes que será produzido depois que esse ClaimsTransformation tiver sido invocado (true ou false). |

A transformação de declarações a seguir demonstra como `Or` dois ClaimTypes boolianos. Na etapa de orquestração, você pode usar uma pré-condição para predefinir uma página autodeclarada, se o valor de uma das declarações for `true`.

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
    - **inputClaim1**: true
    - **inputClaim2**: false
- Declarações de saída:
    - **outputClaim**: true

