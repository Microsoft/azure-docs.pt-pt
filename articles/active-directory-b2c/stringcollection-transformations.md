---
title: StringCollection reclama exemplos de transformação para políticas personalizadas
titleSuffix: Azure AD B2C
description: A StringCollection reclama exemplos de transformação para o esquema do Quadro de Experiência de Identidade (IEF) do Diretório Ativo Azure B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 02/27/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 6aea537ebff4ae61e00861e6cafe742a7feb165e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78186782"
---
# <a name="stringcollection-claims-transformations"></a>StringCollection reclama transformações

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Este artigo fornece exemplos para a utilização da recolha de cordas alega transformações do quadro de experiência de identidade em Azure Ative Directory B2C (Azure AD B2C). Para mais informações, consulte [ClaimsTransformations](claimstransformations.md).

## <a name="additemtostringcollection"></a>AddItemToStringCollection

Adiciona uma reivindicação de cordas a uma nova reivindicação de valores únicos.

| Item | Tipo de reclamação de transformação | Tipo de Dados | Notas |
| ---- | ----------------------- | --------- | ----- |
| Pedido de crédito | item | string | O ClaimType a adicionar à reivindicação de saída. |
| Pedido de crédito | coleção | stringCollection | [Opcional] Se especificado, a transformação de sinistros copia os itens desta coleção e adiciona o item ao fim da reclamação de recolha de saída. |
| Pedido de saída | coleção | stringCollection | O ClaimType que é produzido após esta transformação de sinistros foi invocado, com o valor especificado na alegação de entrada. |

Use esta transformação de reclamações para adicionar uma corda a uma nova ou existente stringCollection. É comumente usado num perfil técnico **AAD-UserWriteUsingAlternativeSecurityId.** Antes de ser criada uma nova conta social, a **CreateOtherMailsFromEmail** afirma que a transformação de reclamações lê o ClaimType e adiciona o valor ao **outro MailS** ClaimType.

A seguinte transformação de sinistros adiciona o **email** ClaimType a **outros Mensagens** ClaimType.

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

- Créditos de entrada:
  - **coleção**:someone@outlook.com[" "]
  - **item**:admin@contoso.com"
- Alegações de saída:
  - **coleção**:someone@outlook.com["admin@contoso.com", "]

## <a name="addparametertostringcollection"></a>Coleção de cadeias addparametertostring

Adiciona um parâmetro de corda a uma nova reivindicação de stringCollection de valores únicos.

| Item | Tipo de reclamação de transformação | Tipo de Dados | Notas |
| ---- | ----------------------- | --------- | ----- |
| Pedido de crédito | coleção | stringCollection | [Opcional] Se especificado, a transformação de sinistros copia os itens desta coleção e adiciona o item ao fim da reclamação de recolha de saída. |
| EntradaParametro | item | string | O valor a ser acrescentado à reivindicação de saída. |
| Pedido de saída | coleção | stringCollection | O ClaimType que é produzido após esta transformação de sinistros foi invocado, com o valor especificado no parâmetro de entrada. |

Utilize esta transformação de sinistros para adicionar um valor de cadeia a uma nova ou existente stringCollection. O exemplo seguinte adiciona umadmin@contoso.comendereço de e-mail constante () à reivindicação de **outros Mails.**

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

- Créditos de entrada:
  - **coleção**:someone@outlook.com[" "]
- Parâmetros de entrada
  - **item**:admin@contoso.com"
- Alegações de saída:
  - **coleção**:someone@outlook.com["admin@contoso.com", "]

## <a name="getsingleitemfromstringcollection"></a>GetSingleItemFromStringCollection

Obtém o primeiro item da coleção de cordas fornecida.

| Item | Tipo de reclamação de transformação | Tipo de Dados | Notas |
| ---- | ----------------------- | --------- | ----- |
| Pedido de crédito | coleção | stringCollection | Os Tipos de Reclamação que são utilizados pela transformação de sinistros para obter o item. |
| Pedido de saída | extraídoItem | string | Os Tipos de Reclamação que são produzidos após esta Transformação de Reclamações foi invocado. O primeiro item da coleção. |

O exemplo seguinte lê os **outros Mails** reclamare devolver o primeiro item na reclamação de **e-mail.**

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

- Créditos de entrada:
  - **coleção**:someone@outlook.com["someone@contoso.com", "]
- Alegações de saída:
  - **extraídoItem**:someone@outlook.com"


## <a name="stringcollectioncontains"></a>StringCollectionContém

Verifica se um tipo de reclamação stringCollection contém um elemento

| Item | Tipo de reclamação de transformação | Tipo de Dados | Notas |
| ---- | ----------------------- | --------- | ----- |
| Pedido de crédito | inputClaim | stringCollection | O tipo de reclamação que deve ser revistado. |
|EntradaParametro|item|string|O valor para procurar.|
|EntradaParametro|ignorarCaso|string|Especifica se esta comparação deve ignorar o caso das cordas que estão a ser comparadas.|
| Pedido de saída | saídaReclamada | boolean | O ClaimType que é produzido após esta Transformação de Reclamações foi invocado. Um indicador booleano se a coleção contiver tal corda |

Após verificação do `roles` exemplo se o tipo de reclamação stringCollection contém o valor da **administração**.

```XML
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

- Créditos de entrada:
    - **inputClaim**: ["leitor", "autor", "administrador"]
- Parâmetros de entrada:
    - **artigo**: "Administrador"
    - **ignoraCaso**: "verdadeiro"
- Alegações de saída:
    - **saídaReivindicação**: "verdadeiro"


