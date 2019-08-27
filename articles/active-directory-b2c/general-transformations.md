---
title: Exemplos de transformação de declarações gerais para o esquema de estrutura de experiência de identidade de Azure Active Directory B2C
description: Exemplos de transformação de declarações gerais para o esquema de estrutura de experiência de identidade de Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 08/27/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 7cea33cb61f8f8d0fe305a757f11c80bc5da24ca
ms.sourcegitcommit: 94ee81a728f1d55d71827ea356ed9847943f7397
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/26/2019
ms.locfileid: "70032889"
---
# <a name="general-claims-transformations"></a>Transformações de declarações gerais

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Este artigo fornece exemplos de como usar as transformações de declarações gerais do esquema de estrutura de experiência de identidade em Azure Active Directory B2C (Azure AD B2C). Para obter mais informações, consulte [ClaimsTransformations](claimstransformations.md).

## <a name="doesclaimexist"></a>DoesClaimExist

Verifica se o **inputClaim** existe ou não e define **outputClaim** como verdadeiro ou falso de acordo.

| Item | TransformationClaimType | Tipo de Dados | Notas |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim |Any | A declaração de entrada cuja existência precisa ser verificada. |
| OutputClaim | outputClaim | boolean | O ClaimType que é produzido após esse ClaimsTransformation foi invocado. |

Use essa transformação de declarações para verificar se uma declaração existe ou contém qualquer valor. O valor de retorno é um booliano que indica se a declaração existe. O exemplo a seguir verifica se o endereço de email existe.

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
  - **inputClaim**:someone@contoso.com
- Declarações de saída:
  - **outputClaim**: true

## <a name="hash"></a>Hash

Hash do texto sem formatação fornecido usando o Salt e um segredo. O algoritmo de hash usado é SHA-256.

| Item | TransformationClaimType | Tipo de Dados | Notas |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | comum | Cadeia de caracteres | A declaração de entrada a ser criptografada |
| InputClaim | Salt | Cadeia de caracteres | O parâmetro Salt. Você pode criar um valor aleatório usando `CreateRandomString` a transformação declarações. |
| InputParameter | randomizerSecret | Cadeia de caracteres | Aponta para uma chave de **política**de Azure ad B2C existente. Para criar uma nova chave de política: No locatário do Azure AD B2C, em **gerenciar**, selecione **estrutura de experiência de identidade**. Selecione **chaves de política** para exibir as chaves que estão disponíveis em seu locatário. Selecione **Adicionar**. Para **Opções**, selecione **manual**. Forneça um nome (o prefixo *B2C_1A_* pode ser adicionado automaticamente.). Na caixa de texto **segredo** , insira qualquer segredo que você queira usar, como 1234567890. Para **uso de chave**, selecione **assinatura**. Selecione **Criar**. |
| OutputClaim | tralha | Cadeia de caracteres | O ClaimType que é produzido depois que essa transformação de declarações foi invocada. A declaração configurada `plaintext` no inputClaim. |

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
  - **texto sem formatação**:MyPass@word1
  - **salt**: 487624568
  - **randomizerSecret**: B2C_1A_AccountTransformSecret
- Declarações de saída:
  - **outputClaim**: CdMNb/KTEfsWzh9MR1kQGRZCKjuxGMWhA5YQNihzV6U=
