---
title: Inteiro reclama exemplos de transformação para políticas personalizadas
titleSuffix: Azure AD B2C
description: O Inteiro reclama exemplos de transformação para o esquema do Azure Ative Directory B2C .
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 066a6489e6244369453ec5d9f21d5e1e83fcd6c8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85201755"
---
# <a name="integer-claims-transformations"></a>Inteiro reclama transformações

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Este artigo fornece exemplos para a utilização das transformações de sinistros inteiros do esquema do Quadro de Experiência de Identidade em Azure Ative Directory B2C (Azure AD B2C). Para obter mais informações, consulte [SinistrosTransformações](claimstransformations.md).

## <a name="convertnumbertostringclaim"></a>ConverteNumberToStringClaim

Converte um longo tipo de dados num tipo de dados de cadeia.

| Item | TransformaçãoClaimType | Tipo de Dados | Notas |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim | long | O ClaimType para converter para uma corda. |
| OutputClaim | outputClaim | string | O ClaimType que é produzido após esta ReclamaçãoTransformação foi invocado. |

Neste exemplo, a `numericUserId` reclamação com um tipo de valor de comprimento é convertida para uma `UserId` reivindicação com um tipo de cadeia de valor.

```xml
<ClaimsTransformation Id="CreateUserId" TransformationMethod="ConvertNumberToStringClaim">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="numericUserId" TransformationClaimType="inputClaim" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="UserId" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Exemplo

- Reclamações de entrada:
    - **inputClaim**: 12334 (longo)
- Reclamações de saída:
    - **outputClaim**: "12334" (cadeia)

