---
title: Exemplos gerais de transformação de reivindicações para políticas personalizadas
titleSuffix: Azure AD B2C
description: Exemplos gerais de transformação de reivindicações para o quadro de experiência de identidade (IEF) do Azure Ative Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 02/03/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: afdf2f531ede30d868123d89cac94fcfae070384
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "78188550"
---
# <a name="general-claims-transformations"></a>Transformações gerais de sinistros

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Este artigo fornece exemplos para a utilização de transformações gerais de sinistros do quadro de experiência de identidade no Azure Ative Directory B2C (Azure AD B2C). Para mais informações, consulte [ClaimsTransformations](claimstransformations.md).

## <a name="copyclaim"></a>CopyClaim

Valor de cópia de uma reclamação para outra. Ambas as alegações devem ser do mesmo tipo.

| Item | Tipo de reclamação de transformação | Tipo de Dados | Notas |
| ---- | ----------------------- | --------- | ----- |
| Pedido de crédito | inputClaim | cadeia, int | O tipo de reclamação que deve ser copiado. |
| Pedido de saída | saídaReclamada | cadeia, int | O ClaimType que é produzido após esta Transformação de Reclamações foi invocado. |

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

- Créditos de entrada:
    - **inputClaim:**bob@contoso.com
- Alegações de saída:
    - **saídaSClaim:**bob@contoso.com

## <a name="doesclaimexist"></a>Reivindicação Existe

Verifica se o **inputClaim** existe ou não e define **a saídaClaim** para verdade ou falsa em conformidade.

| Item | Tipo de reclamação de transformação | Tipo de Dados | Notas |
| ---- | ----------------------- | --------- | ----- |
| Pedido de crédito | inputClaim |Qualquer | A alegação de entrada cuja existência precisa de ser verificada. |
| Pedido de saída | saídaReclamada | boolean | O ClaimType que é produzido após esta Transformação de Reclamações foi invocado. |

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

- Créditos de entrada:
  - **inputClaim:**someone@contoso.com
- Alegações de saída:
  - **saídaReivindicação**: verdadeiro

## <a name="hash"></a>Hash

Hash o texto simples fornecido usando o sal e um segredo. O algoritmo de hashing usado é SHA-256.

| Item | Tipo de reclamação de transformação | Tipo de Dados | Notas |
| ---- | ----------------------- | --------- | ----- |
| Pedido de crédito | texto simples | string | A alegação de entrada para ser encriptada |
| Pedido de crédito | sal | string | O parâmetro de sal. Pode criar um valor `CreateRandomString` aleatório, utilizando a transformação de sinistros. |
| EntradaParametro | randomizerSecret | string | Aponta para uma **chave política**Azure AD B2C existente. Para criar uma nova chave de política: No seu inquilino Azure AD B2C, ao abrigo **do Manage**, selecione Identity **Experience Framework**. Selecione **teclas de política** para visualizar as chaves disponíveis no seu inquilino. Selecione **Adicionar**. Para **opções,** selecione **Manual**. Forneça um nome (o prefixo *B2C_1A_* pode ser adicionado automaticamente.). Na caixa de texto **Secreta,** introduza qualquer segredo que queira usar, como 1234567890. Para **a utilização da chave,** selecione **Signature**. Selecione **Criar**. |
| Pedido de saída | hash | string | O ClaimType que é produzido após esta transformação de sinistros foi invocado. A reclamação configurada no `plaintext` inputClaim. |

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

- Créditos de entrada:
  - **texto simples:**MyPass@word1
  - **sal**: 487624568
  - **randomizerSecret**: B2C_1A_AccountTransformSecret
- Alegações de saída:
  - **saídaReclama:** CdMNb/KTEfsWzh9MR1kQGRZCKjuxGMWhA5YQNihzV6U=
