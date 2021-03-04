---
title: JSON reivindica exemplos de transformação para políticas personalizadas
titleSuffix: Azure AD B2C
description: A JSON reivindica exemplos de transformação para o esquema do Azure Ative Directory B2C .
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/04/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: c5c8e21f2ce3f6907547bf1b2fe4681eb937864b
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2021
ms.locfileid: "102119880"
---
# <a name="json-claims-transformations"></a>JSON reivindica transformações

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Este artigo fornece exemplos para a utilização do esquema de identificação do quadro de experiência de identidade em Azure Ative Directory B2C (Azure AD B2C). Para obter mais informações, consulte [SinistrosTransformações](claimstransformations.md).

## <a name="generatejson"></a>GerarJson

Utilize valores de reclamação ou constantes para gerar uma cadeia JSON. A linha de caminho que segue a notação do ponto é usada para indicar onde inserir os dados numa cadeia JSON. Após a divisão por pontos, quaisquer inteiros são interpretados como o índice de uma matriz JSON e os não-inteiros são interpretados como o índice de um objeto JSON.

| Item | TransformaçãoClaimType | Tipo de Dados | Notas |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | Qualquer corda que segue notação de ponto | string | O JsonPath do JSON onde o valor da reclamação será inserido. |
| InputParameter | Qualquer corda que segue notação de ponto | string | O JsonPath do JSON onde o valor constante das cordas será inserido. |
| OutputClaim | outputClaim | string | A cadeia JSON gerada. |

### <a name="example-1"></a>Exemplo 1

O exemplo a seguir gera uma cadeia JSON com base no valor de reclamação de "email" e "otp" bem como em cadeias constantes.

```xml
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

As seguintes alegações de transformação de resultados uma alegação de cadeia JSON que será o corpo do pedido enviado à SendGrid (um fornecedor de e-mail de terceiros). A estrutura do objeto JSON é definida pelos IDs na notação de pontos dos InputParameters e pelos TransformationClaimTypes dos InputClaims. Números na notação de pontos implicam matrizes. Os valores provêm dos valores do InputClaims e das propriedades "Valor" dos InputParameters.

- Reclamações de entrada:
  - **e-mail**, transformação reivindicação  **personalizações tipo.0.to.0.email**: someone@example.com "
  - **otp**, tipo de reivindicação de transformação **personalizations.0.dynamic_template_data.otp** "346349"
- Parâmetro de entrada:
  - **template_id**: "d-4c56ffb40fa648b1aaaa6822283f94f60"
  - **a partir de.email:** service@contoso.com "
  - **personalizações.0.subject** "Código de verificação de conta Contoso"
- Reclamação de saída:
  - **requestBody**: Valor JSON

```json
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

### <a name="example-2"></a>Exemplo 2

O exemplo a seguir gera uma cadeia JSON baseada nos valores de reivindicação, bem como em cadeias constantes.

```xml
<ClaimsTransformation Id="GenerateRequestBody" TransformationMethod="GenerateJson">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="customerEntity.email" />
    <InputClaim ClaimTypeReferenceId="objectId" TransformationClaimType="customerEntity.userObjectId" />
    <InputClaim ClaimTypeReferenceId="givenName" TransformationClaimType="customerEntity.firstName" />
    <InputClaim ClaimTypeReferenceId="surname" TransformationClaimType="customerEntity.lastName" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="customerEntity.role.name" DataType="string" Value="Administrator"/>
    <InputParameter Id="customerEntity.role.id" DataType="long" Value="1"/>
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="requestBody" TransformationClaimType="outputClaim"/>
  </OutputClaims>
</ClaimsTransformation>
```

As seguintes alegações de transformação de resultados uma alegação de cadeia JSON que será o corpo do pedido enviado a uma API REST. A estrutura do objeto JSON é definida pelos IDs na notação de pontos dos InputParameters e pelos TransformationClaimTypes dos InputClaims. Os valores provêm dos valores do InputClaims e das propriedades "Valor" dos InputParameters.

- Reclamações de entrada:
  - **e-mail**, pedido de transformação tipo  **cliente Entidade.email**: john.s@contoso.com "
  - **objectId**, pedido de reclamação de transformação **tipo cliente Entidade.userObjectIda** "01234567-89ab-cdef-0123-456789abcdef"
  - **dadoName**, design de reivindicação de transformação **tipo cliente Entidade.firstName** "John"
  - **sobrenome**, substituição tipo **cliente Entidade.lastName** "Smith"
- Parâmetro de entrada:
  - **customerEntity.role.name**: "Administrador"
  - **customerEntity.role.id** 1
- Reclamação de saída:
  - **requestBody**: Valor JSON

```json
{
   "customerEntity":{
      "email":"john.s@contoso.com",
      "userObjectId":"01234567-89ab-cdef-0123-456789abcdef",
      "firstName":"John",
      "lastName":"Smith",
      "role":{
         "name":"Administrator",
         "id": 1
      }
   }
}
```

## <a name="getclaimfromjson"></a>GetClaimFromJson

Obtenha um elemento especificado a partir de dados JSON.

| Item | TransformaçãoClaimType | Tipo de Dados | Notas |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputJson | string | Os ClaimTypes que são usados pela transformação de sinistros para obter o item. |
| InputParameter | claimToExtract | string | o nome do elemento JSON a extrair. |
| OutputClaim | extraídoClaim | string | O ClaimType que é produzido após esta transformação de sinistros foi invocado, o valor do elemento especificado no parâmetro de entrada _claimToExtract._ |

No exemplo seguinte, a transformação das sinistros extraiu o `emailAddress` elemento dos dados do JSON: `{"emailAddress": "someone@example.com", "displayName": "Someone"}`

```xml
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

- Reclamações de entrada:
  - **inputJson**: {"emailAddress": " someone@example.com " "displayName": "Someone"}
- Parâmetro de entrada:
    - **claimToExtract**: emailAddress
- Reclamações de saída:
  - **extraídoClaim:**someone@example.com


## <a name="getclaimsfromjsonarray"></a>GetClaimsFromJsonArray

Obtenha uma lista de elementos especificados a partir de dados Json.

| Item | TransformaçãoClaimType | Tipo de Dados | Notas |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | jsonSourceClaim | string | Os ClaimTypes que são usados pela transformação de sinistros para obter os créditos. |
| InputParameter | errorOnMissingClaims | boolean | Especifica se deve lançar um erro se faltar uma das reclamações. |
| InputParameter | incluemEmptyClaims | string | Especificar se deve incluir reclamações vazias. |
| InputParameter | jsonSourceKeyName | string | Nome chave do elemento |
| InputParameter | jsonSourceValueName | string | Nome do valor do elemento |
| OutputClaim | Coleção | corda, int, boolean, e data |Lista de reclamações a extrair. O nome da reclamação deve ser igual ao especificado na alegação de entrada _jsonSourceClaim._ |

No exemplo seguinte, a transformação de sinistros extrai as seguintes alegações: e-mail (string), displayName (string), membershipNum (int), ative (boolean) e birthdate (data) a partir dos dados do JSON.

```json
[{"key":"email","value":"someone@example.com"}, {"key":"displayName","value":"Someone"}, {"key":"membershipNum","value":6353399}, {"key":"active","value":true}, {"key":"birthdate","value":"1980-09-23T00:00:00Z"}]
```

```xml
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

- Reclamações de entrada:
  - **jsonSourceClaim**: [{"key":"email","value":""}, someone@example.com {"key":"displayName","value":"Someone"}, {"key":"membershipNum","value":6353399}, {"key":"ative","value": true}, {"key":"birthdate","value":"1980-09-23T00:00:00:00
- Parâmetros de entrada:
    - **errorOnMissingClaims**: falso
    - **incluemEmptyClaims**: falso
    - **jsonSourceKeyName**: chave
    - **jsonSourceValueName**: valor
- Reclamações de saída:
  - **e-mail:** someone@example.com "
  - **displayName**: "Alguém"
  - **membrosNum**: 6353399
  - **ativo:** verdadeiro
  - **data de nascimento**: 1980-09-23T00:00:00Z

## <a name="getnumericclaimfromjson"></a>GetNumericClaimFromJson

Obtém um elemento numérico especificado (longo) a partir de dados JSON.

| Item | TransformaçãoClaimType | Tipo de Dados | Notas |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputJson | string | Os ClaimTypes que são usados pela transformação de sinistros para obter a reclamação. |
| InputParameter | claimToExtract | string | O nome do elemento JSON para extrair. |
| OutputClaim | extraídoClaim | long | O ClaimType que é produzido após a invocação desta ReclamaçãoTransformação, o valor do elemento especificado nos parâmetros de entrada _claimToExtract._ |

No exemplo seguinte, a transformação de sinistros extrai o `id` elemento dos dados do JSON.

```json
{
    "emailAddress": "someone@example.com",
    "displayName": "Someone",
    "id" : 6353399
}
```

```xml
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

- Reclamações de entrada:
  - **inputJson**: {"emailAddress": " someone@example.com " "displayName": "Someone", "id" : 6353399}
- Parâmetros de entrada
    - **claimToExtract**: id
- Reclamações de saída:
    - **extraídoClaim**: 6353399

## <a name="getsingleitemfromjson"></a>GetSingleItemFromJson

Obtém o primeiro elemento a partir de dados JSON.

| Item | TransformaçãoClaimType | Tipo de Dados | Notas |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputJson | string | Os ClaimTypes que são utilizados pela transformação de sinistros para obter o item a partir dos dados JSON. |
| OutputClaim | key | string | A chave do primeiro elemento no JSON. |
| OutputClaim | valor | string | O primeiro elemento valor no JSON. |

No exemplo seguinte, a transformação de sinistros extrai o primeiro elemento (nome dado) dos dados do JSON.

```xml
<ClaimsTransformation Id="GetGivenNameFromResponse" TransformationMethod="GetSingleItemFromJson">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="json" TransformationClaimType="inputJson" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="givenNameKey" TransformationClaimType="key" />
    <OutputClaim ClaimTypeReferenceId="givenName" TransformationClaimType="value" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Exemplo

- Reclamações de entrada:
  - **inputJson**: {"givenName": "Emilty", "lastName": "Smith"}
- Reclamações de saída:
  - **chave**: dado Nome
  - **valor**: Emilty


## <a name="getsinglevaluefromjsonarray"></a>GetSingleValueFromJsonArray

Obtém o primeiro elemento de uma matriz de dados JSON.

| Item | TransformaçãoClaimType | Tipo de Dados | Notas |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputJsonClaim | string | Os ClaimTypes que são usados pela transformação de sinistros para obter o item da matriz JSON. |
| OutputClaim | extraídoClaim | string | O ClaimType que é produzido após esta ReclamaçãoTransformação foi invocado, o primeiro elemento na matriz JSON. |

No exemplo seguinte, a transformação de sinistros extrai o primeiro elemento (endereço de e-mail) da matriz JSON  `["someone@example.com", "Someone", 6353399]` .

```xml
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

- Reclamações de entrada:
  - **inputJsonClaim:** someone@example.com "" "Someone", 6353399]
- Reclamações de saída:
  - **extraídoClaim:**someone@example.com

## <a name="xmlstringtojsonstring"></a>XmlStringToJsonString

Converte dados XML no formato JSON.

| Item | TransformaçãoClaimType | Tipo de Dados | Notas |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | xml | string | Os ClaimTypes que são utilizados pela transformação de sinistros para converter os dados de XML para o formato JSON. |
| OutputClaim | json | string | O ClaimType que é produzido após esta ReclamaçãoTransformação foi invocado, os dados em formato JSON. |

```xml
<ClaimsTransformation Id="ConvertXmlToJson" TransformationMethod="XmlStringToJsonString">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="intpuXML" TransformationClaimType="xml" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="outputJson" TransformationClaimType="json" />
  </OutputClaims>
</ClaimsTransformation>
```

No exemplo seguinte, a transformação de sinistros converte os seguintes dados XML para o formato JSON.

#### <a name="example"></a>Exemplo
Reclamação de entrada:

```xml
<user>
  <name>Someone</name>
  <email>someone@example.com</email>
</user>
```

Reclamação de saída:

```json
{
  "user": {
    "name":"Someone",
    "email":"someone@example.com"
  }
}
```


