---
title: Exemplos de transformação de afirmações geral para a identidade experiência de estrutura de esquema do Azure Active Directory B2C | Documentos da Microsoft
description: Exemplos de transformação de afirmações geral para a identidade experiência de estrutura de esquema do Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 6a9a819e75e487999a2b50ae758b8d9c6c716a4f
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58084900"
---
# <a name="general-claims-transformations"></a>Transformações de afirmações geral

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Este artigo fornece exemplos para a utilização de transformações de afirmações geral do esquema de arquitetura de experiências de identidade no Azure Active Directory (Azure AD) B2C. Para obter mais informações, consulte [ClaimsTransformations](claimstransformations.md).

## <a name="doesclaimexist"></a>DoesClaimExist

Verifica se o **inputClaim** existe ou não e define **outputClaim** como verdadeira ou falsa em conformidade.

| Item | TransformationClaimType | Tipo de Dados | Notas |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim |Qualquer | A afirmação de entrada cuja existência tem de ser verificado. |
| OutputClaim | outputClaim | boolean | ClaimType produzido este ClaimsTransformation po vyvolání. |

Utilize que este afirmações de transformação para verificar se uma afirmação existe ou contém qualquer valor. O valor de retorno é um valor booleano que indica se a afirmação existe. Exemplo a seguir verifica se existe o endereço de e-mail.

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

- Afirmações de entrada:
  - **inputClaim**: someone@contoso.com
- Afirmações de saída: 
    - **outputClaim**: true

## <a name="hash"></a>Hash

O texto sem formatação fornecido com o salt e um segredo de hash.

| Item | TransformationClaimType | Tipo de Dados | Notas |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | texto sem formatação | string | A afirmação de entrada sejam encriptados |
| InputClaim | Salt | string | O parâmetro de salt. Pode criar um aleatório de valor, usando `CreateRandomString` transformação de afirmações. |
| InputParameter | randomizerSecret | string | Aponta para um existente do Azure AD B2C **chaves de política**. Para criar um novo: No seu inquilino do Azure AD B2C, selecione **definições do B2C > Framework de experiência de identidade**. Selecione **chaves de política** para exibir as chaves que estão disponíveis no seu inquilino. Selecione **Adicionar**. Para **opções**, selecione **Manual**. Forneça um nome (prefixo B2C_1A_ podem ser adicionados automaticamente.). Na caixa de segredo, introduza qualquer segredo que pretende utilizar, como 1234567890. Para utilização de chave, selecione **segredo**. Selecione **Criar**. |
| OutputClaim | Hash | string | Po vyvolání o ClaimType produzido depois de esta transformação de afirmações. A afirmação configurada no `plaintext` inputClaim. |

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

- Afirmações de entrada:
    - **texto sem formatação**: MyPass@word1
    - **salt**: 487624568
    - **randomizerSecret**: B2C_1A_AccountTransformSecret
- Afirmações de saída: 
    - **outputClaim**: CdMNb/KTEfsWzh9MR1kQGRZCKjuxGMWhA5YQNihzV6U=



