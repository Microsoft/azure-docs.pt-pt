---
title: Exemplos de transformação de afirmações booleano para a identidade experiência de estrutura de esquema do Azure Active Directory B2C | Documentos da Microsoft
description: Booleano exemplos de transformação de afirmações para a identidade experiência de estrutura de esquema do Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: b1e56e9126b1dd93ed790da1526b64c49524149d
ms.sourcegitcommit: 5a9be113868c29ec9e81fd3549c54a71db3cec31
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/11/2018
ms.locfileid: "44381463"
---
# <a name="boolean-claims-transformations"></a>Transformações de afirmações booleano

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Este artigo fornece exemplos para usar as transformações de afirmações booleano do esquema de arquitetura de experiências de identidade no Azure Active Directory (Azure AD) B2C. Para obter mais informações, consulte [ClaimsTransformations](claimstransformations.md).

## <a name="andclaims"></a>Equeixas

Executa uma operação e de dois inputClaims booleanos e define a outputClaim com o resultado da operação.

| Item  | TransformationClaimType  | Tipo de Dados  | Notas |
|-------| ------------------------ | ---------- | ----- |
| InputClaim | inputClaim1 | boolean | O ClaimType primeiro a avaliar. |
| InputClaim | inputClaim2  | boolean | O segundo ClaimType a avaliar. |
|outputClaim | outputClaim | boolean | Os ClaimTypes que serão produzidos depois de esta transformação de afirmações ser invocado (true ou false). |

A transformação de declarações seguinte demonstra como e dois ClaimTypes booleanos: `isEmailNotExist`, e `isSocialAccount`. A afirmação de saída `presentEmailSelfAsserted` está definido como `true` se o valor de ambas as afirmações de entrada é `true`. Um passo de orquestração, pode utilizar uma condição prévia para predefinir uma página de declaração própria, apenas se um e-mail de conta de redes sociais está vazio.

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

- Afirmações de entrada:
    - **inputClaim1**: VERDADEIRO
    - **inputClaim2**: Falso
- Afirmações de saída:
    - **outputClaim**: Falso


## <a name="assertbooleanclaimisequaltovalue"></a>AssertBooleanClaimIsEqualToValue

Verifica se os valores booleanos de duas afirmações são iguais e lança uma exceção se não forem.

| Item | TransformationClaimType  | Tipo de Dados  | Notas |
| ---- | ------------------------ | ---------- | ----- |
| InputClaim | InputClaim | boolean | ClaimType a avaliar relativamente. |
| InputParameter |valueToCompareTo | boolean | O valor a comparar (true ou false). |

O **AssertBooleanClaimIsEqualToValue** transformação de declarações é sempre executada a partir de um [perfil técnico de validação](validation-technical-profile.md) que é chamado por um [autodeclarativas perfil técnico](self-asserted-technical-profile.md). O **UserMessageIfClaimsTransformationBooleanValueIsNotEqual** metadados de declaração própria perfil técnico controla a mensagem de erro que o perfil técnico apresenta ao usuário.

![Execução de AssertStringClaimsAreEqual](./media/boolean-transformations/assert-execution.png)

A transformação de declarações seguinte demonstra como verificar o valor de um booleano ClaimType com um `true` valor. Se o valor da `accountEnabled` ClaimType é false, é gerada uma mensagem de erro.

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


O `login-NonInteractive` chamadas de perfil técnico de validação a `AssertAccountEnabledIsTrue` transformação de afirmações.
```XML
<TechnicalProfile Id="login-NonInteractive">
  ...
  <OutputClaimsTransformations>
    <OutputClaimsTransformation ReferenceId="AssertAccountEnabledIsTrue" />
  </OutputClaimsTransformations>
</TechnicalProfile>
```

O perfil técnico de declaração própria chama a validação **NonInteractive de início de sessão** perfil técnico.

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

- Afirmações de entrada:
    - **inputClaim**: Falso
    - **valueToCompareTo**: VERDADEIRO
- Resultado: Erro gerado

## <a name="notclaims"></a>NotClaims

Executa uma operação não da inputClaim booleana e define a outputClaim com o resultado da operação.

| Item | TransformationClaimType | Tipo de Dados | Notas |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | InputClaim | boolean | A afirmação a ser operado. |
| outputClaim | outputClaim | boolean | Os ClaimTypes que são produzidos este ClaimsTransformation po vyvolání (true ou false). |

Use essa transformação de afirmação para executar a negação lógica numa afirmação.

```XML
<ClaimsTransformation Id="CheckWhetherEmailBePresented" TransformationMethod="AndClaims">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="userExists" TransformationClaimType="inputClaim" />
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="userExists" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Exemplo

- Afirmações de entrada:
    - **inputClaim**: Falso
- Afirmações de saída:
    - **outputClaim**: VERDADEIRO

## <a name="orclaims"></a>OrClaims 

Calcula um Or de dois inputClaims booleanos e define a outputClaim com o resultado da operação.

| Item | TransformationClaimType | Tipo de Dados | Notas |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim1 | boolean | O ClaimType primeiro a avaliar. |
| InputClaim | inputClaim2 | boolean | O segundo ClaimType a avaliar. |
| outputClaim | outputClaim | boolean | Os ClaimTypes que serão produzidos este ClaimsTransformation po vyvolání (true ou false). |

A transformação de declarações seguinte demonstra como `Or` dois ClaimTypes booleanos. O passo de orquestração, pode usar uma condição prévia para predefinir uma página de declaração própria, se o valor de uma das afirmações é `true`.

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

- Afirmações de entrada:
    - **inputClaim1**: VERDADEIRO
    - **inputClaim2**: Falso
- Afirmações de saída:
    - **outputClaim**: VERDADEIRO

