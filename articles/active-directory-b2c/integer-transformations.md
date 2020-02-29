---
title: Integer reclama exemplos de transformação para políticas personalizadas
titleSuffix: Azure AD B2C
description: A Integer alega exemplos de transformação para o quadro de experiência de identidade (IEF) do Azure Ative Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: ece25e95bbbe9f1a1fa591c29ea9ffda0fefd369
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/29/2020
ms.locfileid: "78187683"
---
# <a name="integer-claims-transformations"></a>Integer reclama transformações

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Este artigo fornece exemplos para a utilização do inteiro alega transformações do quadro de experiência de identidade em Azure Ative Directory B2C (Azure AD B2C). Para mais informações, consulte [ClaimsTransformations](claimstransformations.md).

## <a name="convertnumbertostringclaim"></a>ConvertNumberToStringClaim

Converte um tipo de dados longo num tipo de dados de cadeia.

| Item | TransformationClaimType | Tipo de Dados | Notas |
| ---- | ----------------------- | --------- | ----- |
| inputClaim | inputClaim | longa | O ClaimType para converter numa corda. |
| OutputClaim | outputClaim | string | O ClaimType que é produzido após esta Transformação de Reclamações foi invocado. |

Neste exemplo, a `numericUserId` reivindicação com um tipo de valor de longo prazo é convertida numa reivindicação `UserId` com um tipo de cadeia de valor.

```XML
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

- Créditos de entrada:
    - **inputClaim:** 12334 (longo)
- Alegações de saída:
    - **saídaReivindicação:** "12334" (cadeia)

