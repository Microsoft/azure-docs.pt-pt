---
title: Exemplos de transformação de declarações JSON para políticas personalizadas
titleSuffix: Azure AD B2C
description: Exemplos de transformação de declarações JSON para o esquema IEF (Identity Experience Framework) de Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 12/10/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 56c46b8f2804e37544c94ec2d6ced7e8879b1ffa
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75367132"
---
# <a name="json-claims-transformations"></a>Transformações de declarações JSON

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Este artigo fornece exemplos de como usar as transformações de declarações JSON do esquema de estrutura de experiência de identidade em Azure Active Directory B2C (Azure AD B2C). Para obter mais informações, consulte [ClaimsTransformations](claimstransformations.md).

## <a name="generatejson"></a>GenerateJson

Use valores de declaração ou constantes para gerar uma cadeia de caracteres JSON. A cadeia de caracteres de caminho após a notação de ponto é usada para indicar onde inserir os dados em uma cadeia de caracteres JSON. Após a divisão por pontos, todos os inteiros são interpretados como o índice de uma matriz JSON e não inteiros são interpretados como o índice de um objeto JSON.

| Item | TransformationClaimType | Tipo de Dados | Notas |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | Qualquer cadeia de caracteres após a notação de ponto | string | O JsonPath do JSON em que o valor da declaração será inserido. |
| InputParameter | Qualquer cadeia de caracteres após a notação de ponto | string | O JsonPath do JSON em que o valor da cadeia de caracteres constante será inserido. |
| OutputClaim | outputClaim | string | A cadeia de caracteres JSON gerada. |

O exemplo a seguir gera uma cadeia de caracteres JSON com base no valor de declaração de "email" e "OTP", bem como cadeias de caracteres constantes.

```XML
<ClaimsTransformation Id="GenerateRequestBody" TransformationMethod="GenerateJson">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="personalizations.0.to.0.email" />
    <InputClaim ClaimTypeReferenceId="otp" TransformationClaimType="personalizations.0.dynamic_template_data.otp" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="template_id" DataType="string" Value="d-4c56ffb40fa648b1aa6822283df94f60"/>
    <InputParameter Id="from.email" DataType="string" Value="service@contoso.com"/>
    <InputParameter Id="personalizations.0.subject" DataType="string" Value="Contoso account email verification code"/>
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="requestBody" TransformationClaimType="outputClaim"/>
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Exemplo

A transformação declarações a seguir gera uma declaração de cadeia de caracteres JSON que será o corpo da solicitação enviada para SendGrid (um provedor de email de terceiros). A estrutura do objeto JSON é definida pelas IDs na notação de ponto de InputParameters e TransformationClaimTypes do InputClaims. Os números na notação de ponto implicam matrizes. Os valores são provenientes dos valores de InputClaims e das propriedades de "valor" de InputParameters.

- Declarações de entrada:
  - **email**, personalizações de tipo de Declaração **de transformação. 0. para. 0. email**: "someone@example.com"
  - **OTP**, as personalizações de tipo de declaração de transformação **. 0. dynamic_template_data. OTP** "346349"
- Parâmetro de entrada:
  - **template_id**: "d-4c56ffb40fa648b1aa6822283df94f60"
  - **de. email**: "service@contoso.com"
  - **personalizações. 0. assunto** "código de verificação de email da conta contoso"
- Declaração de saída:
  - **requestBody**: valor JSON

```JSON
{
  "personalizations": [
    {
      "to": [
        {
          "email": "someone@example.com"
        }
      ],
      "dynamic_template_data": {
        "otp": "346349",
        "verify-email" : "someone@example.com"
      },
      "subject": "Contoso account email verification code"
    }
  ],
  "template_id": "d-989077fbba9746e89f3f6411f596fb96",
  "from": {
    "email": "service@contoso.com"
  }
}
```

## <a name="getclaimfromjson"></a>GetClaimFromJson

Obter um elemento especificado de dados JSON.

| Item | TransformationClaimType | Tipo de Dados | Notas |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputJson | string | Os ClaimTypes que são usados pela transformação de declarações para obter o item. |
| InputParameter | claimToExtract | string | o nome do elemento JSON a ser extraído. |
| OutputClaim | extractedClaim | string | O ClaimType que é produzido após essa transformação de declarações foi invocado, o valor do elemento especificado no parâmetro de entrada _claimToExtract_ . |

No exemplo a seguir, a transformação declarações extraiu o elemento `emailAddress` dos dados JSON: `{"emailAddress": "someone@example.com", "displayName": "Someone"}`

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
  - **inputJson**: {"EmailAddress": "someone@example.com", "DisplayName": "alguém"}
- Parâmetro de entrada:
    - **claimToExtract**: EmailAddress
- Declarações de saída:
  - **extractedClaim**: someone@example.com


## <a name="getclaimsfromjsonarray"></a>GetClaimsFromJsonArray

Obter uma lista de elementos especificados de dados JSON.

| Item | TransformationClaimType | Tipo de Dados | Notas |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | jsonSourceClaim | string | Os ClaimTypes que são usados pela transformação de declarações para obter as declarações. |
| InputParameter | errorOnMissingClaims | boolean | Especifica se um erro deve ser gerado se uma das declarações estiver ausente. |
| InputParameter | includeEmptyClaims | string | Especifique se deseja incluir declarações vazias. |
| InputParameter | jsonSourceKeyName | string | Nome da chave do elemento |
| InputParameter | jsonSourceValueName | string | Nome do valor do elemento |
| OutputClaim | Coleção | Cadeia de caracteres, int, booliano e DateTime |Lista de declarações a serem extraídas. O nome da declaração deve ser igual ao especificado na declaração de entrada _jsonSourceClaim_ . |

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
  - **jsonSourceClaim**: [{"Key": "email", "value": "someone@example.com"}, {"Key": "DisplayName", "value": "alguém"}, {"Key": "membershipNum", "value": 6353399}, {"Key": "active", "value": true}, {"Key": "BirthDate", "value": "1980-09-23T00:00:00Z"}]
- Parâmetros de entrada:
    - **errorOnMissingClaims**: false
    - **includeEmptyClaims**: false
    - **jsonSourceKeyName**: key
    - **jsonSourceValueName**: value
- Declarações de saída:
  - **email**: "someone@example.com"
  - **DisplayName**: "alguém"
  - **membershipNum**: 6353399
  - **ativo**: verdadeiro
  - **DataDeNascimento**: 1980-09-23T00:00:00Z

## <a name="getnumericclaimfromjson"></a>GetNumericClaimFromJson

Obtém um elemento numérico (longo) especificado de um dado JSON.

| Item | TransformationClaimType | Tipo de Dados | Notas |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputJson | string | Os ClaimTypes que são usados pela transformação de declarações para obter a declaração. |
| InputParameter | claimToExtract | string | O nome do elemento JSON a ser extraído. |
| OutputClaim | extractedClaim | longa | O ClaimType que é produzido após esse ClaimsTransformation foi invocado, o valor do elemento especificado nos parâmetros de entrada _claimToExtract_ . |

No exemplo a seguir, a transformação declarações extrai o elemento `id` dos dados JSON.

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
  - **inputJson**: {"EmailAddress": "someone@example.com", "DisplayName": "alguém", "ID": 6353399}
- Parâmetros de entrada
    - **claimToExtract**: ID
- Declarações de saída:
    - **extractedClaim**: 6353399

## <a name="getsinglevaluefromjsonarray"></a>GetSingleValueFromJsonArray

Obtém o primeiro elemento de uma matriz de dados JSON.

| Item | TransformationClaimType | Tipo de Dados | Notas |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputJsonClaim | string | Os ClaimTypes que são usados pela transformação de declarações para obter o item da matriz JSON. |
| OutputClaim | extractedClaim | string | O ClaimType que é produzido após esse ClaimsTransformation foi invocado, o primeiro elemento na matriz JSON. |

No exemplo a seguir, a transformação declarações extrai o primeiro elemento (endereço de email) da matriz JSON `["someone@example.com", "Someone", 6353399]`.

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
  - **extractedClaim**: someone@example.com

## <a name="xmlstringtojsonstring"></a>XmlStringToJsonString

Converte dados XML em formato JSON.

| Item | TransformationClaimType | Tipo de Dados | Notas |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | xml | string | Os ClaimTypes que são usados pela transformação de declarações para converter os dados de XML para o formato JSON. |
| OutputClaim | json | string | O ClaimType que é produzido após esse ClaimsTransformation foi invocado, os dados no formato JSON. |

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
