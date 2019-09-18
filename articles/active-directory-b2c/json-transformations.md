---
title: Exemplos de transformação de declarações JSON para o esquema de estrutura de experiência de identidade de Azure Active Directory B2C | Microsoft Docs
description: Exemplos de transformação de declarações JSON para o esquema de estrutura de experiência de identidade de Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: ff70b2f54304c83f70ff578e1947d752aafb34a7
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/17/2019
ms.locfileid: "71064171"
---
# <a name="json-claims-transformations"></a>Transformações de declarações JSON

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Este artigo fornece exemplos de como usar as transformações de declarações JSON do esquema de estrutura de experiência de identidade em Azure Active Directory B2C (Azure AD B2C). Para obter mais informações, consulte [ClaimsTransformations](claimstransformations.md).

## <a name="getclaimfromjson"></a>GetClaimFromJson

Obter um elemento especificado de dados JSON.

| Item | TransformationClaimType | Tipo de Dados | Notas |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputJson | Cadeia de caracteres | Os ClaimTypes que são usados pela transformação de declarações para obter o item. |
| InputParameter | claimToExtract | Cadeia de caracteres | o nome do elemento JSON a ser extraído. |
| OutputClaim | extractedClaim | Cadeia de caracteres | O ClaimType que é produzido após essa transformação de declarações foi invocado, o valor do elemento especificado no parâmetro de entrada _claimToExtract_ . |

No exemplo a seguir, a transformação declarações extraiu o `emailAddress` elemento dos dados JSON:`{"emailAddress": "someone@example.com", "displayName": "Someone"}`

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

- Declarações de entrada:
  - **inputJson**: {"emailAddress": "someone@example.com", "displayName": "Alguém"}
- Parâmetro de entrada:
    - **claimToExtract**: EmailAddress
- Declarações de saída:
  - **extractedClaim**:someone@example.com


## <a name="getclaimsfromjsonarray"></a>GetClaimsFromJsonArray

Obter uma lista de elementos especificados de dados JSON.

| Item | TransformationClaimType | Tipo de Dados | Notas |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | jsonSourceClaim | Cadeia de caracteres | Os ClaimTypes que são usados pela transformação de declarações para obter as declarações. |
| InputParameter | errorOnMissingClaims | boolean | Especifica se um erro deve ser gerado se uma das declarações estiver ausente. |
| InputParameter | includeEmptyClaims | Cadeia de caracteres | Especifique se deseja incluir declarações vazias. |
| InputParameter | jsonSourceKeyName | Cadeia de caracteres | Nome da chave do elemento |
| InputParameter | jsonSourceValueName | Cadeia de caracteres | Nome do valor do elemento |
| OutputClaim | Collection | Cadeia de caracteres, int, booliano e DateTime |Lista de declarações a serem extraídas. O nome da declaração deve ser igual ao especificado na declaração de entrada _jsonSourceClaim_ . |

No exemplo a seguir, a transformação declarações extrai as seguintes declarações: email (cadeia de caracteres), displayName (cadeia de caracteres), membershipNum (int), ativo (booliano) e DataDeNascimento (DateTime) dos dados JSON.

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

- Declarações de entrada:
  - **jsonSourceClaim**: [{"Key": "email", "value": "someone@example.com"}, {"Key": "DisplayName", "value": "alguém"}, {"Key": "membershipNum", "value": 6353399}, {"Key": "active", "value": true}, {"Key": "BirthDate", "value": "1980-09-23T00:00:00Z "}]
- Parâmetros de entrada:
    - **errorOnMissingClaims**: false
    - **includeEmptyClaims**: false
    - **jsonSourceKeyName**: key
    - **jsonSourceValueName**: value
- Declarações de saída:
  - **email**: "someone@example.com"
  - **displayName**: Qualquer
  - **membershipNum**: 6353399
  - **ativo**: verdadeiro
  - **DataDeNascimento**: 1980-09-23T00:00:00Z

## <a name="getnumericclaimfromjson"></a>GetNumericClaimFromJson

Obtém um elemento numérico (longo) especificado de um dado JSON.

| Item | TransformationClaimType | Tipo de Dados | Notas |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputJson | Cadeia de caracteres | Os ClaimTypes que são usados pela transformação de declarações para obter a declaração. |
| InputParameter | claimToExtract | Cadeia de caracteres | O nome do elemento JSON a ser extraído. |
| OutputClaim | extractedClaim | long | O ClaimType que é produzido após esse ClaimsTransformation foi invocado, o valor do elemento especificado nos parâmetros de entrada _claimToExtract_ . |

No exemplo a seguir, a transformação declarações extrai o `id` elemento dos dados JSON.

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

- Declarações de entrada:
  - **inputJson**: {"emailAddress": "someone@example.com", "displayName": "Alguém", "ID": 6353399}
- Parâmetros de entrada
    - **claimToExtract**: ID
- Declarações de saída:
    - **extractedClaim**: 6353399

## <a name="getsinglevaluefromjsonarray"></a>GetSingleValueFromJsonArray

Obtém o primeiro elemento de uma matriz de dados JSON.

| Item | TransformationClaimType | Tipo de Dados | Notas |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputJsonClaim | Cadeia de caracteres | Os ClaimTypes que são usados pela transformação de declarações para obter o item da matriz JSON. |
| OutputClaim | extractedClaim | Cadeia de caracteres | O ClaimType que é produzido após esse ClaimsTransformation foi invocado, o primeiro elemento na matriz JSON. |

No exemplo a seguir, a transformação declarações extrai o primeiro elemento (endereço de email) da matriz `["someone@example.com", "Someone", 6353399]`JSON.

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

- Declarações de entrada:
  - **inputJsonClaim**: ["someone@example.com", "alguém", 6353399]
- Declarações de saída:
  - **extractedClaim**:someone@example.com

## <a name="xmlstringtojsonstring"></a>XmlStringToJsonString

Converte dados XML em formato JSON.

| Item | TransformationClaimType | Tipo de Dados | Notas |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | xml | Cadeia de caracteres | Os ClaimTypes que são usados pela transformação de declarações para converter os dados de XML para o formato JSON. |
| OutputClaim | json | Cadeia de caracteres | O ClaimType que é produzido após esse ClaimsTransformation foi invocado, os dados no formato JSON. |

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

No exemplo a seguir, a transformação declarações converte os seguintes dados XML no formato JSON.

#### <a name="example"></a>Exemplo
Declaração de entrada:

```XML
<user>
  <name>Someone</name>
  <email>someone@example.com</email>
</user>
```

Declaração de saída:

```JSON
{
  "user": {
    "name":"Someone",
    "email":"someone@example.com"
  }
}
```

