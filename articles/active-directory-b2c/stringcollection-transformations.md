---
title: StringCollection reclama exemplos de transformação para políticas personalizadas
titleSuffix: Azure AD B2C
description: StringCollection reivindica exemplos de transformação para o esquema do Quadro de Experiência de Identidade (IEF) do Azure Ative Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/04/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: c9104fb4598eb62ed96d0b21734053fa118b5237
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2021
ms.locfileid: "102120287"
---
# <a name="stringcollection-claims-transformations"></a>StringCollection reivindica transformações

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Este artigo fornece exemplos para a utilização das transformações de alegações de recolha de cordas do esquema do Quadro de Experiência de Identidade em Azure Ative Directory B2C (Azure AD B2C). Para obter mais informações, consulte [SinistrosTransformações](claimstransformations.md).

## <a name="additemtostringcollection"></a>AddItemToStringCollection

Adiciona uma reivindicação de corda a um novo valor único alegação de stringCollection.

| Item | TransformaçãoClaimType | Tipo de Dados | Notas |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | item | string | O ClaimType a ser adicionado à reclamação de saída. |
| InputClaim | coleção | stringCollection | A coleção de cordas a ser adicionada à reivindicação de saída. Se a coleção contiver itens, a transformação de sinistros copia os itens e adiciona o item ao fim da reclamação de recolha de saída. |
| OutputClaim | coleção | stringCollection | O ClaimType que é produzido após esta transformação de sinistros foi invocado, com o valor especificado na reclamação de entrada. |

Utilize esta transformação de alegações para adicionar uma corda a uma cadeia nova ou existenteCollection. É comumente usado num perfil técnico **AAD-UserWriteUsingAlternativeSecurityId.** Antes de uma nova conta social ser criada, **a CreateOtherMailsFromEmail** alega que a transformação lê o ClaimType e acrescenta o valor aos **outros Correios** ClaimType.

A transformação de reclamações seguinte adiciona o **email** ClaimType a **outros Mensagens** ClaimType.

```xml
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

- Reclamações de entrada:
  - **coleção:** someone@outlook.com [" "]
  - **ponto:** admin@contoso.com " "
- Reclamações de saída:
  - **coleção**: someone@outlook.com [" admin@contoso.com "" "]

## <a name="addparametertostringcollection"></a>AddParameterToStringCollection

Adiciona um parâmetro de corda a um novo valor único alegação de cadeias.

| Item | TransformaçãoClaimType | Tipo de Dados | Notas |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | coleção | stringCollection | A coleção de cordas a ser adicionada à reivindicação de saída. Se a coleção contiver itens, a transformação de sinistros copia os itens e adiciona o item ao fim da reclamação de recolha de saída. |
| InputParameter | item | string | O valor a adicionar à reivindicação de saída. |
| OutputClaim | coleção | stringCollection | O ClaimType que é produzido após esta transformação de sinistros foi invocado, com o valor especificado no parâmetro de entrada. |

Utilize esta transformação de alegações para adicionar um valor de corda a uma cadeia nova ou existenteCollection. O exemplo a seguir adiciona um endereço de e-mail constante admin@contoso.com () à outra reivindicação **Enviar.**

```xml
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

- Reclamações de entrada:
  - **coleção:** someone@outlook.com [" "]
- Parâmetros de entrada
  - **ponto:** admin@contoso.com " "
- Reclamações de saída:
  - **coleção**: someone@outlook.com [" admin@contoso.com "" "]

## <a name="getsingleitemfromstringcollection"></a>GetSingleItemFromStringCollection

Obtém o primeiro item da coleção de cordas fornecida.

| Item | TransformaçãoClaimType | Tipo de Dados | Notas |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | coleção | stringCollection | Os ClaimTypes que são usados pela transformação de sinistros para obter o item. |
| OutputClaim | extraídoItem | string | Os ClaimTypes que são produzidos após esta ReclamaçãoTransformação foi invocado. O primeiro item da coleção. |

O exemplo a seguir lê a **alegação de outros Emails** e devolve o primeiro item na reclamação de **e-mail.**

```xml
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

- Reclamações de entrada:
  - **coleção**: someone@outlook.com [" someone@contoso.com "" "]
- Reclamações de saída:
  - **extraídoItem**: someone@outlook.com " "


## <a name="stringcollectioncontains"></a>StringCollectionContains

Verifica se um tipo de reclamação StringCollection contém um elemento.

| Item | TransformaçãoClaimType | Tipo de Dados | Notas |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim | stringCollection | O tipo de reclamação que deve ser revistado. |
|InputParameter|item|string|O valor a procurar.|
|InputParameter|ignoreCase|string|Especifica se esta comparação deve ignorar o caso das cordas que estão a ser comparadas.|
| OutputClaim | outputClaim | boolean | O ClaimType que é produzido após esta ReclamaçãoTransformação foi invocado. Um indicador boolean se a coleção contém tal cadeia |

O exemplo seguinte verifica se o `roles` tipo de reclamação de cadeiasCollection contém o valor da **administração**.

```xml
<ClaimsTransformation Id="IsAdmin" TransformationMethod="StringCollectionContains">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="roles" TransformationClaimType="inputClaim"/>
  </InputClaims>
  <InputParameters>
    <InputParameter  Id="item" DataType="string" Value="Admin"/>
    <InputParameter  Id="ignoreCase" DataType="string" Value="true"/>
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="isAdmin" TransformationClaimType="outputClaim"/>
  </OutputClaims>
</ClaimsTransformation>
```

- Reclamações de entrada:
    - **inputClaim**: ["leitor", "autor", "administrador"]
- Parâmetros de entrada:
    - **ponto**: "Administrador"
    - **ignoreCase**: "verdadeiro"
- Reclamações de saída:
    - **outputClaim**: "verdadeiro"

## <a name="stringcollectioncontainsclaim"></a>StringCollectionContainsClaim

Verifica se um tipo de reclamação StringCollection contém um valor de reclamação.

| Item | TransformaçãoClaimType | Tipo de Dados | Notas |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | coleção | stringCollection | O tipo de reclamação que deve ser revistado. |
| InputClaim | item|string| O tipo de reclamação que contém o valor a procurar.|
|InputParameter|ignoreCase|string|Especifica se esta comparação deve ignorar o caso das cordas que estão a ser comparadas.|
| OutputClaim | outputClaim | boolean | O ClaimType que é produzido após esta ReclamaçãoTransformação foi invocado. Um indicador boolean se a coleção contém tal cadeia |

O exemplo seguinte verifica se o `roles` tipo de reclamação de cadeiasColection contém o valor do `role` tipo de reclamação.

```xml
<ClaimsTransformation Id="HasRequiredRole" TransformationMethod="StringCollectionContainsClaim">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="roles" TransformationClaimType="collection" />
    <InputClaim ClaimTypeReferenceId="role" TransformationClaimType="item" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="ignoreCase" DataType="string" Value="true" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="hasAccess" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation> 
```

- Reclamações de entrada:
    - **coleção**: ["leitor", "autor", "administrador"]
    - **ponto**: "Administrador"
- Parâmetros de entrada:
    - **ignoreCase**: "verdadeiro"
- Reclamações de saída:
    - **outputClaim**: "verdadeiro"
