---
title: SinistrosTransformações - Azure Ative Directory B2C | Microsoft Docs
description: Definição do elemento SinistrosDformações no Esquema-Quadro de Experiência de Identidade do Azure Ative Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 37d9bd78a80ac52d2a790537bf47e33807720349
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "85202964"
---
# <a name="claimstransformations"></a>ClaimsTransformations

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

O elemento **ClaimsTransformations** contém uma lista de funções de transformação de sinistros que podem ser usadas em viagens de utilizador como parte de uma [política personalizada.](custom-policy-overview.md) Uma transformação de afirmações converte uma determinada afirmação noutra. Na transformação de sinistros, especifica-se o método de transformação, por exemplo, adicionar um item a uma coleção de cordas ou alterar o caso de uma cadeia.

Para incluir a lista de funções de transformação de sinistros que podem ser utilizadas nas viagens do utilizador, um elemento XML de Sinistros deve ser declarado na secção Blocos de Construção da política.

```xml
<ClaimsTransformations>
  <ClaimsTransformation Id="<identifier>" TransformationMethod="<method>">
    ...
  </ClaimsTransformation>
</ClaimsTransformations>
```

O elemento **sinistrosaformação** contém os seguintes atributos:

| Atributo |Obrigatório | Descrição |
| --------- |-------- | ----------- |
| Id |Yes | Um identificador que é usado para identificar exclusivamente a transformação da alegação. O identificador é referenciado a partir de outros elementos XML na política. |
| TransformaçãoMethod | Yes | O método de transformação a ser usado na transformação de sinistros. Cada transformação de reivindicação tem os seus próprios valores. Consulte a [referência de transformação](#claims-transformations-reference) de sinistros para uma lista completa dos valores disponíveis. |

## <a name="claimstransformation"></a>Transferência de Reclamações

O elemento **sinistrosaformação** contém os seguintes elementos:

```xml
<ClaimsTransformation Id="<identifier>" TransformationMethod="<method>">
  <InputClaims>
    ...
  </InputClaims>
  <InputParameters>
    ...
  </InputParameters>
  <OutputClaims>
    ...
  </OutputClaims>
</ClaimsTransformation>
```


| Elemento | Ocorrências | Description |
| ------- | -------- | ----------- |
| InputClaims | 0:1 | Uma lista de elementos **InputClaim** que especificam tipos de reclamações que são tomados como entrada para a transformação de sinistros. Cada um destes elementos contém uma referência a um ClaimType já definido na secção ClaimsSchema na apólice. |
| InputParameters | 0:1 | Uma lista de elementos **InputParameter** que são fornecidos como entrada para a transformação de sinistros.
| Resultados | 0:1 | Uma lista de elementos **OutputClaim** que especificam tipos de reclamações que são produzidos após a invocação do SinistrosTransformação. Cada um destes elementos contém referência a um ClaimType já definido na secção ClaimsSchema. |

### <a name="inputclaims"></a>InputClaims

O elemento **InputClaims** contém o seguinte elemento:

| Elemento | Ocorrências | Description |
| ------- | ----------- | ----------- |
| InputClaim | 1:n | Um tipo de pedido de entrada esperado. |

#### <a name="inputclaim"></a>InputClaim

O elemento **InputClaim** contém os seguintes atributos:

| Atributo |Obrigatório | Descrição |
| --------- | ----------- | ----------- |
| ClaimTypeReferenceId |Yes | Uma referência a umTip de Reclamação já definido na secção ClaimsSchema na apólice. |
| TransformaçãoClaimType |Yes | Um identificador para fazer referência a um tipo de pedido de transformação. Cada transformação de reivindicação tem os seus próprios valores. Consulte a [referência de transformação](#claims-transformations-reference) de sinistros para uma lista completa dos valores disponíveis. |

### <a name="inputparameters"></a>InputParameters

O elemento **InputParameters** contém o seguinte elemento:

| Elemento | Ocorrências | Description |
| ------- | ----------- | ----------- |
| InputParameter | 1:n | Um parâmetro de entrada esperado. |

#### <a name="inputparameter"></a>InputParameter

| Atributo | Obrigatório |Descrição |
| --------- | ----------- |----------- |
| Id | Yes | Um identificador que é uma referência a um parâmetro do método de transformação de sinistros. Cada método de transformação de reivindicações tem os seus próprios valores. Consulte a tabela de transformação de reclamações para obter uma lista completa dos valores disponíveis. |
| DataType | Yes | O tipo de dados do parâmetro, tais como String, Boolean, Int ou DateTime de acordo com a enumeração dataType no esquema XML da política personalizada. Este tipo é utilizado para efetuar corretamente as operações aritméticas. Cada transformação de reivindicação tem os seus próprios valores. Consulte a [referência de transformação](#claims-transformations-reference) de sinistros para uma lista completa dos valores disponíveis. |
| Valor | Yes | Um valor que é passado verbatim à transformação. Alguns dos valores são arbitrários, alguns deles selecionam do método de transformação de sinistros. |

### <a name="outputclaims"></a>Resultados

O elemento **OutputClaims** contém o seguinte elemento:

| Elemento | Ocorrências | Description |
| ------- | ----------- | ----------- |
| OutputClaim | 0:n | Um tipo de reivindicação de saída esperada. |

#### <a name="outputclaim"></a>OutputClaim

O elemento **OutputClaim** contém os seguintes atributos:

| Atributo |Obrigatório | Descrição |
| --------- | ----------- |----------- |
| ClaimTypeReferenceId | Yes | Uma referência a umTip de Reclamação já definido na secção ClaimsSchema na apólice.
| TransformaçãoClaimType | Yes | Um identificador para fazer referência a um tipo de pedido de transformação. Cada transformação de reivindicação tem os seus próprios valores. Consulte a [referência de transformação](#claims-transformations-reference) de sinistros para uma lista completa dos valores disponíveis. |

Se a reclamação de entrada e a reclamação de saída forem do mesmo tipo (string, ou boolean), pode utilizar a mesma reivindicação de entrada que a alegação de saída. Neste caso, a transformação de sinistros altera a reclamação de entrada com o valor de saída.

## <a name="example"></a>Exemplo

Por exemplo, pode armazenar a última versão dos seus termos de serviços que o utilizador aceitou. Ao atualizar os termos dos serviços, pode pedir ao utilizador que aceite a nova versão. No exemplo seguinte, a transformação de **sinistros HasTOSVersionChanged** compara o valor da **reclamação TOSVersion** com o valor da **reclamação LastTOSAcceptedVersion** e, em seguida, devolve a **reclamação tosversion alterada.**

```xml
<BuildingBlocks>
  <ClaimsSchema>
    <ClaimType Id="TOSVersionChanged">
      <DisplayName>Indicates if the TOS version accepted by the end user is equal to the current version</DisplayName>
      <DataType>boolean</DataType>
    </ClaimType>
    <ClaimType Id="TOSVersion">
      <DisplayName>TOS version</DisplayName>
      <DataType>string</DataType>
    </ClaimType>
    <ClaimType Id="LastTOSAcceptedVersion">
      <DisplayName>TOS version accepted by the end user</DisplayName>
      <DataType>string</DataType>
    </ClaimType>
  </ClaimsSchema>

  <ClaimsTransformations>
    <ClaimsTransformation Id="HasTOSVersionChanged" TransformationMethod="CompareClaims">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="TOSVersion" TransformationClaimType="inputClaim1" />
        <InputClaim ClaimTypeReferenceId="LastTOSAcceptedVersion" TransformationClaimType="inputClaim2" />
      </InputClaims>
      <InputParameters>
        <InputParameter Id="operator" DataType="string" Value="NOT EQUAL" />
      </InputParameters>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="TOSVersionChanged" TransformationClaimType="outputClaim" />
      </OutputClaims>
    </ClaimsTransformation>
  </ClaimsTransformations>
</BuildingBlocks>
```

## <a name="claims-transformations-reference"></a>Referência de transformações de reclamações

Por exemplo, transformações de reclamações, consulte as seguintes páginas de referência:

- [Booleano](boolean-transformations.md)
- [Data](date-transformations.md)
- [Número inteiro](integer-transformations.md)
- [JSON](json-transformations.md)
- [Número de telefone](phone-number-claims-transformations.md)
- [Geral](general-transformations.md)
- [Conta social](social-transformations.md)
- [String](string-transformations.md)
- [StringCollection](stringcollection-transformations.md)

