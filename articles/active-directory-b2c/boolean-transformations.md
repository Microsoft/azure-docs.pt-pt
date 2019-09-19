---
title: Exemplos de transformação de declarações booleanas para o esquema de estrutura de experiência de identidade de Azure Active Directory B2C | Microsoft Docs
description: Exemplos de transformação de declarações booleanas para o esquema de estrutura de experiência de identidade de Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: da4fc4704ee72210e180ef95fe6a821c8d116fa2
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/17/2019
ms.locfileid: "71064566"
---
# <a name="boolean-claims-transformations"></a>Transformações de declarações booleanas

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Este artigo fornece exemplos de como usar as transformações de declarações booleanas do esquema de estrutura de experiência de identidade em Azure Active Directory B2C (Azure AD B2C). Para obter mais informações, consulte [ClaimsTransformations](claimstransformations.md).

## <a name="andclaims"></a>AndClaims

Executa uma operação and de dois inputClaims Boolianos e define o outputClaim com o resultado da operação.

| Item  | TransformationClaimType  | Tipo de Dados  | Notas |
|-------| ------------------------ | ---------- | ----- |
| InputClaim | inputClaim1 | boolean | O primeiro ClaimType a ser avaliado. |
| InputClaim | inputClaim2  | boolean | O segundo ClaimType a ser avaliado. |
|OutputClaim | outputClaim | boolean | O ClaimTypes que será produzido depois que essa transformação de declarações tiver sido invocada (true ou false). |

A transformação de declarações a seguir demonstra como e dois ClaimTypes boolianos `isEmailNotExist`: `isSocialAccount`e. A Declaração `presentEmailSelfAsserted` de saída é definida `true` como se o valor de ambas as declarações `true`de entrada forem. Em uma etapa de orquestração, você pode usar uma pré-condição para predefinir uma página autodeclarada, somente se um email de conta social estiver vazio.

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
| InputClaim | InputClaim | boolean | O ClaimType a ser declarado. |
| InputParameter |valueToCompareTo | boolean | O valor a ser comparado (true ou false). |

A transformação declarações **AssertBooleanClaimIsEqualToValue** é sempre executada de um [perfil técnico de validação](validation-technical-profile.md) que é chamado por um [perfil técnico autodeclarado](self-asserted-technical-profile.md). Os metadados do perfil técnico autodeclarado **UserMessageIfClaimsTransformationBooleanValueIsNotEqual** controlam a mensagem de erro que o perfil técnico apresenta ao usuário.

![Execução de AssertStringClaimsAreEqual](./media/boolean-transformations/assert-execution.png)

A transformação de declarações a seguir demonstra como verificar o valor de um ClaimType booliano `true` com um valor. Se o valor de `accountEnabled` ClaimType for false, uma mensagem de erro será lançada.

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


O `login-NonInteractive` perfil técnico de validação chama `AssertAccountEnabledIsTrue` a transformação declarações.
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
- Disso Erro gerado

## <a name="notclaims"></a>NotClaims

Executa uma operação not do inputClaim booliano e define o outputClaim com o resultado da operação.

| Item | TransformationClaimType | Tipo de Dados | Notas |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | InputClaim | boolean | A declaração a ser operada. |
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
| InputClaim | inputClaim1 | boolean | O primeiro ClaimType a ser avaliado. |
| InputClaim | inputClaim2 | boolean | O segundo ClaimType a ser avaliado. |
| OutputClaim | outputClaim | boolean | O ClaimTypes que será produzido depois que esse ClaimsTransformation tiver sido invocado (true ou false). |

A transformação de declarações a seguir demonstra `Or` como dois ClaimTypes boolianos. Na etapa de orquestração, você pode usar uma pré-condição para predefinir uma página autodeclarada, se o valor de uma das declarações for `true`.

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

