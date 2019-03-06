---
title: ClaimsTransformations - Azure Active Directory B2C | Documentos da Microsoft
description: Definição do elemento ClaimsTransformations na identidade experiência de estrutura de esquema do Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 59524a9a08b16100598b7713cb0507646245d90b
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57445828"
---
# <a name="claimstransformations"></a>ClaimsTransformations

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

O **ClaimsTransformations** elemento contém uma lista de funções de transformação de afirmações que podem ser utilizados em jornadas de utilizador como parte de um [política personalizada](active-directory-b2c-overview-custom.md). Uma transformação de declarações converte uma determinado afirmação em outro. A transformação de declarações, vai especificar o método de transformação, por exemplo, adicionar um item a uma coleção de cadeia de caracteres ou alterar o caso de uma cadeia de caracteres.

Para incluir a lista de funções de transformação de afirmações que podem ser utilizadas no Jornadas utilizador, um elemento ClaimsTransformations XML tem de ser declarado na secção BuildingBlocks da política.

```xml
<ClaimsTransformations>
  <ClaimsTransformation Id="<identifier>" TransformationMethod="<method>">
    ...
  </ClaimsTransformation>
</ClaimsTransformations>
```

O **ClaimsTransformation** elemento contém os seguintes atributos:

| Atributo |Necessário | Descrição |
| --------- |-------- | ----------- |
| Id |Sim | Um identificador que é utilizado para identificar exclusivamente a transformação de afirmação. O identificador é referenciado a partir de outros elementos XML na política. |
| TransformationMethod | Sim | O método de transformação a utilizar na transformação de afirmações. Cada transformação de afirmações tem seus próprios valores. Consulte a [referência de transformação de afirmações](#Claims-transformations-reference) para obter uma lista completa dos valores disponíveis. |

## <a name="claimstransformation"></a>ClaimsTransformation

O **ClaimsTransformation** elemento contém os seguintes elementos:

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
| InputClaims | 0:1 | Uma lista de **InputClaim** elementos que especificar os tipos de afirmação que são executados como entrada para a transformação de declarações. Cada um desses elementos contém uma referência a um ClaimType já definido na secção ClaimsSchema na política. |
| InputParameters | 0:1 | Uma lista de **InputParameter** elementos que são fornecidos como entrada para a transformação de declarações.  
| OutputClaims | 0:1 | Uma lista de **OutputClaim** elementos que especificar tipos de afirmação que são produzidos depois do ClaimsTransformation ser invocada. Cada um desses elementos contém a referência a um ClaimType já definido na secção ClaimsSchema. |

### <a name="inputclaims"></a>InputClaims

O **InputClaims** elemento contém o seguinte elemento:

| Elemento | Ocorrências | Descrição |
| ------- | ----------- | ----------- |
| InputClaim | 1:n | Tipo de afirmação de entrada esperada. |

#### <a name="inputclaim"></a>InputClaim

O **InputClaim** elemento contém os seguintes atributos:

| Atributo |Necessário | Descrição |
| --------- | ----------- | ----------- |
| ClaimTypeReferenceId |Sim | Uma referência a um ClaimType já definido na secção ClaimsSchema na política. |
| TransformationClaimType |Sim | Tipo de afirmação de um identificador para fazer referência a uma transformação. Cada transformação de afirmações tem seus próprios valores. Consulte a [referência de transformação de afirmações](#Claims-transformations-reference) para obter uma lista completa dos valores disponíveis. |

### <a name="inputparameters"></a>InputParameters

O **InputParameters** elemento contém o seguinte elemento:

| Elemento | Ocorrências | Descrição |
| ------- | ----------- | ----------- |
| InputParameter | 1:n | Um parâmetro de entrada esperado. |

#### <a name="inputparameter"></a>InputParameter

| Atributo | Necessário |Descrição |
| --------- | ----------- |----------- |
| Id | Sim | Um identificador que é uma referência a um parâmetro do método de transformação de afirmações. Cada método de transformação de afirmações tem seus próprios valores. Consulte a tabela de transformação de afirmações para uma lista completa dos valores disponíveis. |
| Tipo de dados | Sim | O tipo de dados do parâmetro, como cadeia, booleano, Int ou DateTime de acordo com a enumeração de tipo de dados no esquema XML de política personalizada. Este tipo é utilizado para efetuar operações aritméticas corretamente. Cada transformação de afirmações tem seus próprios valores. Consulte a [referência de transformação de afirmações](#Claims-transformations-reference) para obter uma lista completa dos valores disponíveis. |
| Value | Sim | Um valor que é passado textual para a transformação. Alguns dos valores são arbitrárias, alguns deles é selecionar o método de transformação de afirmações. |

### <a name="outputclaims"></a>OutputClaims

O **OutputClaims** elemento contém o seguinte elemento:

| Elemento | Ocorrências | Descrição |
| ------- | ----------- | ----------- |
| OutputClaim | 0:n | Tipo de afirmação de um resultado esperado. |

#### <a name="outputclaim"></a>OutputClaim 

O **OutputClaim** elemento contém os seguintes atributos:

| Atributo |Necessário | Descrição |
| --------- | ----------- |----------- |
| ClaimTypeReferenceId | Sim | Uma referência a um ClaimType já definido na secção ClaimsSchema na política.
| TransformationClaimType | Sim | Tipo de afirmação de um identificador para fazer referência a uma transformação. Cada transformação de afirmações tem seus próprios valores. Consulte a [referência de transformação de afirmações](#Claims-transformations-reference) para obter uma lista completa dos valores disponíveis. |
 
Se a afirmação de entrada e de afirmações de saída são o mesmo tipo (cadeia ou booleano), pode utilizar a mesma afirmação de entrada como afirmações de saída. Neste caso, a transformação de declarações altera a afirmação de entrada com o valor de saída.

## <a name="example"></a>Exemplo

Por exemplo, pode armazenar a última versão dos seus termos de serviços que o utilizador aceitou. Quando atualizar os termos de serviços, pode pedir ao utilizador para aceitar a nova versão. No exemplo a seguir, o **HasTOSVersionChanged** transformação de declarações compara o valor da **TOSVersion** a afirmação com o valor da **LastTOSAcceptedVersion**afirmação e, em seguida, devolve o valor **TOSVersionChanged** de afirmação.

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

## <a name="claims-transformations-reference"></a>Referência de transformações de afirmações

Para obter exemplos de transformações de afirmações, consulte as seguintes páginas de referência:

- [valor booleano](boolean-transformations.md)
- [data](date-transformations.md)
- [Integer](integer-transformations.md)
- [JSON](json-transformations.md)
- [Geral](general-transformations.md)
- [Conta de redes sociais](social-transformations.md)
- [Cadeia de caracteres](string-transformations.md)
- [StringCollection](stringcollection-transformations.md)

