---
title: StringCollection reclama exemplos de transformação para políticas personalizadas
titleSuffix: Azure AD B2C
description: A StringCollection reclama exemplos de transformação para o esquema do Quadro de Experiência de Identidade (IEF) do Diretório Ativo Azure B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 02/03/2020
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 8f91db91eff3320691a5979d9453bf515ccd59a2
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/04/2020
ms.locfileid: "76982301"
---
# <a name="stringcollection-claims-transformations"></a>StringCollection reclama transformações

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Este artigo fornece exemplos para a utilização da recolha de cordas alega transformações do quadro de experiência de identidade em Azure Ative Directory B2C (Azure AD B2C). Para obter mais informações, consulte [ClaimsTransformations](claimstransformations.md).

## <a name="additemtostringcollection"></a>AddItemToStringCollection

Adiciona uma reivindicação de cordas a uma nova reivindicação de stringCollection.

| Item | TransformationClaimType | Tipo de Dados | Notas |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | item | string | O ClaimType a adicionar à reivindicação de saída. |
| InputClaim | coleção | stringCollection | [Opcional] Se especificado, a transformação de sinistros copia os itens desta coleção e adiciona o item ao fim da reclamação de recolha de saída. |
| OutputClaim | coleção | stringCollection | Os Tipos de Reclamação que são produzidos após esta Transformação de Reclamações foi invocado. |

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

- Declarações de entrada:
  - **:** ["someone@outlook.com"]
  - **item**: "admin@contoso.com"
- Declarações de saída:
  - **coleção**: ["someone@outlook.com", "admin@contoso.com"]

## <a name="addparametertostringcollection"></a>AddParameterToStringCollection

Adiciona um parâmetro de corda a uma nova reivindicação de stringCollection.

| Item | TransformationClaimType | Tipo de Dados | Notas |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | coleção | stringCollection | [Opcional] Se especificado, a transformação de sinistros copia os itens desta coleção e adiciona o item ao fim da reclamação de recolha de saída. |
| InputParameter | item | string | O valor a ser acrescentado à reivindicação de saída. |
| OutputClaim | coleção | stringCollection | Os Tipos de Reclamação que serão produzidos após esta Transformação de Reclamações ter sido invocada. |

Utilize esta transformação de sinistros para adicionar um valor de cadeia a uma nova ou existente stringCollection. O exemplo seguinte adiciona um endereço de e-mail constante (admin@contoso.com) à reivindicação de **outros Mails.**

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
  - **:** ["someone@outlook.com"]
- Parâmetros de entrada
  - **item**: "admin@contoso.com"
- Declarações de saída:
  - **coleção**: ["someone@outlook.com", "admin@contoso.com"]

## <a name="getsingleitemfromstringcollection"></a>GetSingleItemFromStringCollection

Obtém o primeiro item da coleção de cordas fornecida.

| Item | TransformationClaimType | Tipo de Dados | Notas |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | coleção | stringCollection | Os ClaimTypes que são usados pela transformação de declarações para obter o item. |
| OutputClaim | extractedItem | string | Os Tipos de Reclamação que são produzidos após esta Transformação de Reclamações foi invocado. O primeiro item da coleção. |

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

- Declarações de entrada:
  - **coleção**: ["someone@outlook.com", "someone@contoso.com"]
- Declarações de saída:
  - **item extraído**: "someone@outlook.com"


## <a name="stringcollectioncontains"></a>StringCollectionContém

Verifica se um tipo de reclamação stringCollection contém um elemento

| Item | TransformationClaimType | Tipo de Dados | Notas |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | InputClaim | stringCollection | O tipo de reclamação que deve ser revistado. |
|InputParameter|item|string|O valor para procurar.|
|InputParameter|ignoreCase|string|Especifica se esta comparação deve ignorar o caso das cordas que estão a ser comparadas.|
| OutputClaim | outputClaim | boolean | O ClaimType que é produzido após esta Transformação de Reclamações foi invocado. Um indicador booleano se a coleção contiver tal corda |

Na sequência do exemplo, verifica se o tipo de reclamação `roles` stringCollection contém o valor da **administração**.

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

- Declarações de entrada:
    - **inputClaim**: ["leitor", "autor", "administrador"]
- Parâmetros de entrada:
    - **artigo**: "Administrador"
    - **ignoraCaso**: "verdadeiro"
- Declarações de saída:
    - **saídaReivindicação**: "verdadeiro"


