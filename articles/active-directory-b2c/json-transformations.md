---
title: Exemplos de transformação de afirmações de JSON para a identidade experiência de estrutura de esquema do Azure Active Directory B2C | Documentos da Microsoft
description: JSON de exemplos de transformação de afirmações para a identidade experiência de estrutura de esquema do Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 88d447f86dd54fc9479a6d7d2006b9a8639ad09e
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58089238"
---
# <a name="json-claims-transformations"></a>Transformações de afirmações de JSON

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Este artigo fornece exemplos para usar as transformações de afirmações JSON do esquema de arquitetura de experiências de identidade no Azure Active Directory (Azure AD) B2C. Para obter mais informações, consulte [ClaimsTransformations](claimstransformations.md).

## <a name="getclaimfromjson"></a>GetClaimFromJson

Obter um elemento especificado a partir de um JSON.

| Item | TransformationClaimType | Tipo de Dados | Notas |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputJson | string | Os ClaimTypes que são utilizados pela transformação de declarações para obter o item. |
| InputParameter | claimToExtract | string | o nome do elemento JSON a ser extraído. |
| OutputClaim | extractedClaim | string | O que é produzido depois de esta transformação de afirmações ClaimType ser invocado, o valor do elemento especificado no _claimToExtract_ parâmetro de entrada. |

No exemplo seguinte, a transformação de declarações extraídas a `emailAddress` elemento a partir dos dados JSON: `{"emailAddress": "someone@example.com", "displayName": "Someone"}`

```XML
<ClaimsTransformation Id="GetEmailClaimFromJson" TransformationMethod="GetClaimFromJson">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="customUserData" TransformationClaimType="inputJson" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="claimToExtract" DataType="string" Value="emailAddress" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="extractedEmail" TransformationClaimType="extractedClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Exemplo

- Afirmações de entrada:
  - **inputJson**: {"emailAddress": "someone@example.com", "displayName": "Someone"}
- Parâmetro de entrada:
    - **claimToExtract**: endereço de correio eletrónico
- Afirmações de saída: 
  - **extractedClaim**: someone@example.com


## <a name="getclaimsfromjsonarray"></a>GetClaimsFromJsonArray

Obter uma lista de elementos especificados a partir dos dados Json.

| Item | TransformationClaimType | Tipo de Dados | Notas |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | jsonSourceClaim | string | Os ClaimTypes que são utilizados pela transformação de declarações para obter as afirmações. |
| InputParameter | errorOnMissingClaims | boolean | Especifica se pretende gerar um erro se uma das afirmações está em falta. |
| InputParameter | includeEmptyClaims | string | Especifique se pretende incluem afirmações vazias. |
| InputParameter | jsonSourceKeyName | string | Nome da chave de elemento |
| InputParameter | jsonSourceValueName | string | Nome do valor de elemento |
| OutputClaim | Coleção | cadeia de caracteres, int, Booleano e datetime |Lista de afirmações para extrair. O nome da afirmação deve ser igual àquele especificado no _jsonSourceClaim_ afirmações de entrada. |

No exemplo seguinte, a transformação de declarações extrai as seguintes declarações: e-mail (cadeia), displayName (cadeia), membershipNum (int), Active Directory (boolean) e data de nascimento (datetime) dos dados JSON.

```JSON
[{"key":"email","value":"someone@example.com"}, {"key":"displayName","value":"Someone"}, {"key":"membershipNum","value":6353399}, {"key":"active","value":true}, {"key":"birthdate","value":"1980-09-23T00:00:00Z"}]
```

```XML
<ClaimsTransformation Id="GetClaimsFromJson" TransformationMethod="GetClaimsFromJsonArray">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="jsonSourceClaim" TransformationClaimType="jsonSource" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="errorOnMissingClaims" DataType="boolean" Value="false" />
    <InputParameter Id="includeEmptyClaims" DataType="boolean" Value="false" />
    <InputParameter Id="jsonSourceKeyName" DataType="string" Value="key" />
    <InputParameter Id="jsonSourceValueName" DataType="string" Value="value" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="email" />
    <OutputClaim ClaimTypeReferenceId="displayName" />
    <OutputClaim ClaimTypeReferenceId="membershipNum" />
    <OutputClaim ClaimTypeReferenceId="active" />
    <OutputClaim ClaimTypeReferenceId="birthdate" />
  </OutputClaims>
</ClaimsTransformation>
```    

- Afirmações de entrada:
  - **jsonSourceClaim**: [{"chave": "email", "value": "someone@example.com"}, {"chave": "displayName", "value": "Alguém"}, {"chave": "membershipNum", "value": 6353399}, {"chave": "ativo", "value": true}, {"chave": "data de nascimento", "value": "1980-09-23T00:0 0:00Z"}]
- Parâmetros de entrada:
    - **errorOnMissingClaims**: false
    - **includeEmptyClaims**: false
    - **jsonSourceKeyName**: key
    - **jsonSourceValueName**: value
- Afirmações de saída:
  - **correio eletrónico**: "someone@example.com"
  - **displayName**: "Alguém"
  - **membershipNum**: 6353399
  - **Active Directory**: VERDADEIRO
  - **birthdate**: 1980-09-23T00:00:00Z

## <a name="getnumericclaimfromjson"></a>GetNumericClaimFromJson

Obtém um numérico (muito) o elemento especificado a partir de um dados JSON.

| Item | TransformationClaimType | Tipo de Dados | Notas |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputJson | string | Os ClaimTypes que são utilizados pela transformação de declarações para obter a afirmação. |
| InputParameter | claimToExtract | string | O nome do elemento JSON para extrair. |
| OutputClaim | extractedClaim | longa | ClaimType produzido depois deste ClaimsTransformation ser invocada, o valor do elemento especificado na _claimToExtract_ parâmetros de entrada. |

No exemplo seguinte, a transformação de declarações extrai o `id` elemento a partir dos dados JSON.

```JSON
{
    "emailAddress": "someone@example.com", 
    "displayName": "Someone", 
    "id" : 6353399
}
```

```XML
<ClaimsTransformation Id="GetIdFromResponse" TransformationMethod="GetNumericClaimFromJson">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="exampleInputClaim" TransformationClaimType="inputJson" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="claimToExtract" DataType="string" Value="id" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="membershipId" TransformationClaimType="extractedClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Exemplo

- Afirmações de entrada:
  - **inputJson**: {"emailAddress": "someone@example.com", "displayName": "Alguém", "id": 6353399}
- Parâmetros de entrada
    - **claimToExtract**: id
- Afirmações de saída: 
    - **extractedClaim**: 6353399

## <a name="getsinglevaluefromjsonarray"></a>GetSingleValueFromJsonArray

Obtém o primeiro elemento de uma matriz de dados JSON.

| Item | TransformationClaimType | Tipo de Dados | Notas |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputJsonClaim | string | Os ClaimTypes que são utilizados pela transformação de declarações para obter o item da matriz JSON. |
| OutputClaim | extractedClaim | string | O que é produzido este ClaimsTransformation po vyvolání ClaimType, o primeiro elemento na matriz JSON. |

No exemplo seguinte, a transformação de declarações extrai o primeiro elemento (endereço de correio eletrónico) da matriz JSON `["someone@example.com", "Someone", 6353399]`.

```XML
<ClaimsTransformation Id="GetEmailFromJson" TransformationMethod="GetSingleValueFromJsonArray">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="userData" TransformationClaimType="inputJsonClaim" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="email" TransformationClaimType="extractedClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Exemplo

- Afirmações de entrada:
  - **inputJsonClaim**: ["someone@example.com", "Someone", 6353399]
- Afirmações de saída: 
  - **extractedClaim**: someone@example.com

## <a name="xmlstringtojsonstring"></a>XmlStringToJsonString

Converte dados XML em formato JSON.

| Item | TransformationClaimType | Tipo de Dados | Notas |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | xml | string | Os ClaimTypes que são utilizados pela transformação de declarações para converter os dados XML em formato JSON. |
| OutputClaim | json | string | O que é produzido este ClaimsTransformation po vyvolání ClaimType, os dados no formato JSON. |

```XML
<ClaimsTransformation Id="ConvertXmlToJson" TransformationMethod="XmlStringToJsonString">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="intpuXML" TransformationClaimType="xml" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="outputJson" TransformationClaimType="json" />
  </OutputClaims>
</ClaimsTransformation>
```

No exemplo seguinte, a transformação de declarações converte os seguintes dados XML no formato JSON.

#### <a name="example"></a>Exemplo
Afirmações de entrada:

```XML
<user>
  <name>Someone</name>
  <email>someone@example.com</email>
</user>
```

Afirmações de saída:

```JSON
{
  "user": {
    "name":"Someone",
    "email":"someone@example.com"
  }
}
```

