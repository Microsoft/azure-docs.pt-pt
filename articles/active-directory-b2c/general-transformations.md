---
title: Exemplos gerais de transformação de reivindicações para políticas personalizadas
titleSuffix: Azure AD B2C
description: Exemplos gerais de transformação do esquema do Quadro de Experiência de Identidade (IEF) do Azure Ative Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 02/03/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 52831a1907d5ca8d13b0477c909d0d0358873973
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "85202225"
---
# <a name="general-claims-transformations"></a>Transformações gerais de reclamações

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Este artigo fornece exemplos para a utilização de transformações gerais de sinistros do esquema do Quadro de Experiência de Identidade em Azure Ative Directory B2C (Azure AD B2C). Para obter mais informações, consulte [SinistrosTransformações](claimstransformations.md).

## <a name="copyclaim"></a>CopyClaim

Valor de cópia de uma reclamação a outra. Ambas as alegações devem ser do mesmo tipo.

| Item | TransformaçãoClaimType | Tipo de Dados | Notas |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim | corda, int | O tipo de reclamação que deve ser copiado. |
| OutputClaim | outputClaim | corda, int | O ClaimType que é produzido após esta ReclamaçãoTransformação foi invocado. |

Utilize esta transformação de sinistros para copiar um valor de uma alegação de cadeia ou numérica, para outra reivindicação. O exemplo a seguir copia o valor de reclamação externoEmail para reclamação de e-mail.

```xml
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

- Reclamações de entrada:
    - **inputClaim:**bob@contoso.com
- Reclamações de saída:
    - **outputClaim:**bob@contoso.com

## <a name="doesclaimexist"></a>Faz oClaimExist

Verifica se a **entradaClaim** existe ou não e define **a saídaClaim** para verdadeiro ou falso em conformidade.

| Item | TransformaçãoClaimType | Tipo de Dados | Notas |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim |Qualquer | A alegação de entrada cuja existência precisa de ser verificada. |
| OutputClaim | outputClaim | boolean | O ClaimType que é produzido após esta ReclamaçãoTransformação foi invocado. |

Utilize esta transformação de sinistros para verificar se existe ou contém algum valor. O valor de retorno é um booleano que indica se a reclamação existe. Seguindo o exemplo verifica-se se o endereço de e-mail existe.

```xml
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

- Reclamações de entrada:
  - **inputClaim:**someone@contoso.com
- Reclamações de saída:
  - **outputClaim**: verdadeiro

## <a name="hash"></a>Hash

Hash o texto simples fornecido usando o sal e um segredo. O algoritmo de hashing usado é SHA-256.

| Item | TransformaçãoClaimType | Tipo de Dados | Notas |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | texto simples | string | A alegação de entrada para ser encriptada |
| InputClaim | sal | string | O parâmetro do sal. Pode criar um valor aleatório, utilizando a `CreateRandomString` transformação de sinistros. |
| InputParameter | RandomizerSecret | string | Aponta para uma **chave de política** Azure AD B2C existente . Para criar uma nova chave de política: No seu inquilino Azure AD B2C, em **Manage**, selecione **Identity Experience Framework**. Selecione **as chaves de política** para ver as chaves que estão disponíveis no seu inquilino. Selecione **Adicionar**. Para **opções**, selecione **Manual**. Fornecer um nome (o prefixo *B2C_1A_* pode ser adicionado automaticamente.). Na caixa de texto **Secreta,** insira qualquer segredo que queira usar, como 1234567890. Para **a utilização da chave**, selecione **Signature**. Selecione **Criar**. |
| OutputClaim | hash | string | O ClaimType que é produzido após esta transformação de alegações foi invocado. A reclamação configurada na `plaintext` entradaClaim. |

```xml
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

- Reclamações de entrada:
  - **texto simples:**MyPass@word1
  - **sal**: 487624568
  - **RandomizerSecret**: B2C_1A_AccountTransformSecret
- Reclamações de saída:
  - **outputClaim**: CdMNb/KTEfsWzh9MR1kQGRZCKjuxGMWhA5YQNihzV6U=
