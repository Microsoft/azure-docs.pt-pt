---
title: ClaimsTransformations - Azure Ative Directory B2C [ Microsoft Docs
description: Definição do elemento Detransformação de Reclamações no Quadro de Experiência de Identidade Schema do Diretório Ativo Azure B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 2a919996d00f8ef3fa00109944b60d53b63d95ff
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80529131"
---
# <a name="claimstransformations"></a>ClaimsTransformations

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

O elemento **ClaimsTransformations** contém uma lista de funções de transformação de sinistros que podem ser usadas nas viagens dos utilizadores como parte de uma [política personalizada](custom-policy-overview.md). Uma transformação de sinistros converte uma dada reivindicação em outra. Na transformação de sinistros, especifica-se o método de transformação, por exemplo, adicionando um item a uma coleção de cordas ou alterando o caso de uma corda.

Para incluir a lista de funções de transformação de sinistros que podem ser utilizadas nas viagens do utilizador, um elemento ClaimsTransformations XML deve ser declarado no âmbito da secção BuildingBlocks da apólice.

```xml
<ClaimsTransformations>
  <ClaimsTransformation Id="<identifier>" TransformationMethod="<method>">
    ...
  </ClaimsTransformation>
</ClaimsTransformations>
```

O elemento **ClaimsTransformation** contém os seguintes atributos:

| Atributo |Necessário | Descrição |
| --------- |-------- | ----------- |
| Id |Sim | Um identificador que é usado para identificar exclusivamente a transformação da reivindicação. O identificador é referenciado a partir de outros elementos XML na política. |
| Método de Transformação | Sim | O método de transformação para usar na transformação de sinistros. Cada reivindicação transformação tem os seus próprios valores. Consulte a referência de transformação de [sinistros](#claims-transformations-reference) para uma lista completa dos valores disponíveis. |

## <a name="claimstransformation"></a>Transformação de Sinistros

O elemento **Destransformação de Reclamações** contém os seguintes elementos:

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


| Elemento | Ocorrências | Descrição |
| ------- | -------- | ----------- |
| Créditos de entrada | 0:1 | Uma lista de elementos **de InputClaim** que especificam tipos de reclamação que são tomados como contributo para a transformação de sinistros. Cada um destes elementos contém uma referência a um ClaimType já definido na secção ClaimsSchema na política. |
| Parâmetros de entrada | 0:1 | Uma lista de elementos **InputParameter** que são fornecidos como entrada para a transformação de sinistros.
| OutputClaims | 0:1 | Uma lista de elementos **OutputClaim** que especificam os tipos de reclamação que são produzidos após a invocação da Transformação de Reclamações. Cada um destes elementos contém referência a um ClaimType já definido na secção ClaimsSchema. |

### <a name="inputclaims"></a>Créditos de entrada

O elemento **InputClaims** contém o seguinte elemento:

| Elemento | Ocorrências | Descrição |
| ------- | ----------- | ----------- |
| Pedido de crédito | 1:n | Um tipo de reclamação de entrada esperado. |

#### <a name="inputclaim"></a>Pedido de crédito

O elemento **InputClaim** contém os seguintes atributos:

| Atributo |Necessário | Descrição |
| --------- | ----------- | ----------- |
| ClaimTypeReferenceId |Sim | Uma referência a um ClaimType já definido na secção ClaimsSchema na política. |
| Tipo de reclamação de transformação |Sim | Um identificador para referenciar um tipo de pedido de transformação. Cada reivindicação transformação tem os seus próprios valores. Consulte a referência de transformação de [sinistros](#claims-transformations-reference) para uma lista completa dos valores disponíveis. |

### <a name="inputparameters"></a>Parâmetros de entrada

O elemento **InputParameters** contém o seguinte elemento:

| Elemento | Ocorrências | Descrição |
| ------- | ----------- | ----------- |
| EntradaParametro | 1:n | Um parâmetro de entrada esperado. |

#### <a name="inputparameter"></a>EntradaParametro

| Atributo | Necessário |Descrição |
| --------- | ----------- |----------- |
| Id | Sim | Um identificador que é uma referência a um parâmetro do método de transformação de sinistros. Cada método de transformação de reivindicações tem os seus próprios valores. Consulte a tabela de transformação de reclamações para obter uma lista completa dos valores disponíveis. |
| DataType | Sim | O tipo de dados do parâmetro, tais como String, Boolean, Int ou DateTime de acordo com a enumeração dataType no esquema de política personalizada XML. Este tipo é utilizado para executar operações aritméticas corretamente. Cada reivindicação transformação tem os seus próprios valores. Consulte a referência de transformação de [sinistros](#claims-transformations-reference) para uma lista completa dos valores disponíveis. |
| Valor | Sim | Um valor que é passado verbatim para a transformação. Alguns dos valores são arbitrários, alguns deles selecionam do método de transformação de sinistros. |

### <a name="outputclaims"></a>OutputClaims

O elemento **OutputClaims** contém o seguinte elemento:

| Elemento | Ocorrências | Descrição |
| ------- | ----------- | ----------- |
| Pedido de saída | 0:n | Um tipo de reivindicação de saída esperado. |

#### <a name="outputclaim"></a>Pedido de saída

O elemento **OutputClaim** contém os seguintes atributos:

| Atributo |Necessário | Descrição |
| --------- | ----------- |----------- |
| ClaimTypeReferenceId | Sim | Uma referência a um ClaimType já definido na secção ClaimsSchema na política.
| Tipo de reclamação de transformação | Sim | Um identificador para referenciar um tipo de pedido de transformação. Cada reivindicação transformação tem os seus próprios valores. Consulte a referência de transformação de [sinistros](#claims-transformations-reference) para uma lista completa dos valores disponíveis. |

Se a reclamação de entrada e a alegação de saída forem do mesmo tipo (corda, ou boolean), pode utilizar a mesma alegação de entrada que a alegação de saída. Neste caso, a transformação de sinistros altera a reclamação de entrada com o valor de saída.

## <a name="example"></a>Exemplo

Por exemplo, pode armazenar a última versão dos seus termos de serviços que o utilizador aceitou. Ao atualizar os termos dos serviços, pode pedir ao utilizador que aceite a nova versão. No exemplo seguinte, a transformação de sinistros **HasTOSVersionChanged** compara o valor da reclamação **toSVersion** com o valor da alegação **LastTOSAcceptedVersion** e, em seguida, devolve a booleana **TOSVersionChange** claim.

```XML
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

## <a name="claims-transformations-reference"></a>Referência de transformações de sinistros

Para exemplo sondar as transformações de sinistros, consulte as seguintes páginas de referência:

- [Booleano](boolean-transformations.md)
- [Date](date-transformations.md)
- [Número inteiro](integer-transformations.md)
- [JSON](json-transformations.md)
- [Número de telefone](phone-number-claims-transformations.md)
- [General](general-transformations.md)
- [Conta social](social-transformations.md)
- [String](string-transformations.md)
- [StringCollection](stringcollection-transformations.md)

