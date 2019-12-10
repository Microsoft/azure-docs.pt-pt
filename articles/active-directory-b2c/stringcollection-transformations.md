---
title: Exemplos de transformação de declarações StringCollection para políticas personalizadas
titleSuffix: Azure AD B2C
description: Exemplos de transformação de declarações StringCollection para o esquema IEF (Identity Experience Framework) de Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: fbbd7b4bdddf2b58e66cb1203414b5a63eec2f27
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/09/2019
ms.locfileid: "74951008"
---
# <a name="stringcollection-claims-transformations"></a>Transformações de declarações de StringCollection

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Este artigo fornece exemplos de como usar as transformações de declarações de coleção de cadeia de caracteres do esquema de estrutura de experiência de identidade em Azure Active Directory B2C (Azure AD B2C). Para obter mais informações, consulte [ClaimsTransformations](claimstransformations.md).

## <a name="additemtostringcollection"></a>AddItemToStringCollection

Adiciona uma declaração de cadeia de caracteres a uma nova declaração StringCollection.

| Item | TransformationClaimType | Tipo de Dados | Notas |
| ---- | ----------------------- | --------- | ----- |
| inputClaim | item | string | O ClaimType a ser adicionado à declaração de saída. |
| inputClaim | coleção | stringCollection | Adicional Se especificado, a transformação declarações copiará os itens dessa coleção e adicionará o item ao final da declaração de coleta de saída. |
| OutputClaim | coleção | stringCollection | O ClaimTypes que é produzido depois desse ClaimsTransformation foi invocado. |

Use esta transformação de declarações para adicionar uma cadeia de caracteres a uma StringCollection nova ou existente. Ele é comumente usado em um perfil técnico do **AAD-UserWriteUsingAlternativeSecurityId** . Antes que uma nova conta social seja criada, a transformação de declarações **CreateOtherMailsFromEmail** lê o ClaimType e adiciona o valor ao ClaimType **otherMails** .

A transformação declarações a seguir adiciona o **email** ClaimType a **otherMails** ClaimType.

```XML
<ClaimsTransformation Id="CreateOtherMailsFromEmail" TransformationMethod="AddItemToStringCollection">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="item" />
    <InputClaim ClaimTypeReferenceId="otherMails" TransformationClaimType="collection" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="otherMails" TransformationClaimType="collection" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Exemplo

- Declarações de entrada:
  - **coleção**: ["someone@outlook.com"]
  - **Item**: "admin@contoso.com"
- Declarações de saída:
  - **coleção**: ["someone@outlook.com", "admin@contoso.com"]

## <a name="addparametertostringcollection"></a>AddParameterToStringCollection

Adiciona um parâmetro de cadeia de caracteres a uma nova declaração StringCollection.

| Item | TransformationClaimType | Tipo de Dados | Notas |
| ---- | ----------------------- | --------- | ----- |
| inputClaim | coleção | stringCollection | Adicional Se especificado, a transformação declarações copiará os itens dessa coleção e adicionará o item ao final da declaração de coleta de saída. |
| InputParameter | item | string | O valor a ser adicionado à declaração de saída. |
| OutputClaim | coleção | stringCollection | O ClaimTypes que será produzido depois que esse ClaimsTransformation tiver sido invocado. |

Use esta transformação de declarações para adicionar um valor de cadeia de caracteres a uma StringCollection nova ou existente. O exemplo a seguir adiciona um endereço de email constante (admin@contoso.com) à Declaração **otherMails** .

```XML
<ClaimsTransformation Id="SetCompanyEmail" TransformationMethod="AddParameterToStringCollection">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="otherMails" TransformationClaimType="collection" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="item" DataType="string" Value="admin@contoso.com" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="otherMails" TransformationClaimType="collection" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Exemplo

- Declarações de entrada:
  - **coleção**: ["someone@outlook.com"]
- Parâmetros de entrada
  - **Item**: "admin@contoso.com"
- Declarações de saída:
  - **coleção**: ["someone@outlook.com", "admin@contoso.com"]

## <a name="getsingleitemfromstringcollection"></a>GetSingleItemFromStringCollection

Obtém o primeiro item da coleção de cadeia de caracteres fornecida.

| Item | TransformationClaimType | Tipo de Dados | Notas |
| ---- | ----------------------- | --------- | ----- |
| inputClaim | coleção | stringCollection | Os ClaimTypes que são usados pela transformação de declarações para obter o item. |
| OutputClaim | extractedItem | string | O ClaimTypes que é produzido depois desse ClaimsTransformation foi invocado. O primeiro item na coleção. |

O exemplo a seguir lê a Declaração **otherMails** e retorna o primeiro item para a declaração de **email** .

```XML
<ClaimsTransformation Id="CreateEmailFromOtherMails" TransformationMethod="GetSingleItemFromStringCollection">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="otherMails" TransformationClaimType="collection" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="email" TransformationClaimType="extractedItem" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Exemplo

- Declarações de entrada:
  - **coleção**: ["someone@outlook.com", "someone@contoso.com"]
- Declarações de saída:
  - **extractedItem**: "someone@outlook.com"

