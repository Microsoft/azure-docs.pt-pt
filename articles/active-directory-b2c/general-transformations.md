---
title: Exemplos gerais de transformação de reivindicações para políticas personalizadas
titleSuffix: Azure AD B2C
description: Exemplos gerais de transformação de reivindicações para o quadro de experiência de identidade (IEF) do Azure Ative Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 02/03/2020
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 98d9730168764f0ba683a246f9ac224c13d3bf31
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/04/2020
ms.locfileid: "76982811"
---
# <a name="general-claims-transformations"></a>Transformações gerais de sinistros

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Este artigo fornece exemplos para a utilização de transformações gerais de sinistros do quadro de experiência de identidade no Azure Ative Directory B2C (Azure AD B2C). Para obter mais informações, consulte [ClaimsTransformations](claimstransformations.md).

## <a name="copyclaim"></a>CopyClaim

Valor de cópia de uma reclamação para outra. Ambas as alegações devem ser do mesmo tipo.

| Item | TransformationClaimType | Tipo de Dados | Notas |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | InputClaim | cadeia, int | O tipo de reclamação que deve ser copiado. |
| OutputClaim | outputClaim | cadeia, int | O ClaimType que é produzido após esta Transformação de Reclamações foi invocado. |

Utilize esta transformação de sinistros para copiar um valor de uma sequência ou reivindicação numérica, para outra reclamação. O exemplo seguinte copia o valor de reclamação de email externo para a reclamação de e-mail.

```XML
<ClaimsTransformation Id="CopyEmailAddress" TransformationMethod="CopyClaim"> 
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="externalEmail" TransformationClaimType="inputClaim"/>
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="email" TransformationClaimType="outputClaim"/>
  </OutputClaims>         
</ClaimsTransformation>
```

### <a name="example"></a>Exemplo

- Declarações de entrada:
    - **inputClaim**: bob@contoso.com
- Declarações de saída:
    - **saída :** bob@contoso.com 

## <a name="doesclaimexist"></a>Reivindicação Existe

Verifica se o **inputClaim** existe ou não e define **a saídaClaim** para verdade ou falsa em conformidade.

| Item | TransformationClaimType | Tipo de Dados | Notas |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | InputClaim |Qualquer | A alegação de entrada cuja existência precisa de ser verificada. |
| OutputClaim | outputClaim | boolean | O ClaimType que é produzido após esta Transformação de Reclamações foi invocado. |

Utilize esta transformação de reclamações para verificar se existe uma reclamação ou contém qualquer valor. O valor de retorno é uma booleana que indica se a reclamação existe. A seguir, verifica o exemplo se o endereço de e-mail existe.

```XML
<ClaimsTransformation Id="CheckIfEmailPresent" TransformationMethod="DoesClaimExist">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="inputClaim" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="isEmailPresent" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Exemplo

- Declarações de entrada:
  - **inputClaim**: someone@contoso.com
- Declarações de saída:
  - **saídaReivindicação**: verdadeiro

## <a name="hash"></a>Hash

Hash o texto simples fornecido usando o sal e um segredo. O algoritmo de hashing usado é SHA-256.

| Item | TransformationClaimType | Tipo de Dados | Notas |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | texto simples | string | A alegação de entrada para ser encriptada |
| InputClaim | sal | string | O parâmetro de sal. Pode criar um valor aleatório, usando `CreateRandomString` transformação de sinistros. |
| InputParameter | randomizerSecret | string | Aponta para uma **chave política**Azure AD B2C existente. Para criar uma nova chave de política: No seu inquilino Azure AD B2C, ao abrigo **do Manage**, selecione Identity **Experience Framework**. Selecione **teclas de política** para visualizar as chaves disponíveis no seu inquilino. Selecione **Adicionar**. Para **Opções**, selecione **manual**. Forneça um nome (o prefixo *B2C_1A_* pode ser adicionado automaticamente.). Na caixa de texto **Secreta,** introduza qualquer segredo que queira usar, como 1234567890. Para **uso de chave**, selecione **assinatura**. Selecione **Criar**. |
| OutputClaim | hash | string | O ClaimType que é produzido após esta transformação de sinistros foi invocado. A reclamação configurada na `plaintext` inputClaim. |

```XML
<ClaimsTransformation Id="HashPasswordWithEmail" TransformationMethod="Hash">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="password" TransformationClaimType="plaintext" />
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="salt" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="randomizerSecret" DataType="string" Value="B2C_1A_AccountTransformSecret" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="hashedPassword" TransformationClaimType="hash" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Exemplo

- Declarações de entrada:
  - **texto simples**: MyPass@word1
  - **sal**: 487624568
  - **randomizerSecret**: B2C_1A_AccountTransformSecret
- Declarações de saída:
  - **saídaReclama:** CdMNb/KTEfsWzh9MR1kQGRZCKjuxGMWhA5YQNihzV6U=
