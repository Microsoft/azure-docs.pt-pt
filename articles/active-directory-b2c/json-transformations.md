---
title: JSON reclama exemplos de transformação para políticas personalizadas
titleSuffix: Azure AD B2C
description: A JSON reclama exemplos de transformação para o quadro de experiência de identidade (IEF) do Azure Ative Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 04/21/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: b42c2a414333e7ed262441321a808fc45425fc3b
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/22/2020
ms.locfileid: "81756752"
---
# <a name="json-claims-transformations"></a>JSON reclama transformações

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Este artigo fornece exemplos para a utilização do JSON alega transformações do quadro de experiência de identidade em Azure Ative Directory B2C (Azure AD B2C). Para mais informações, consulte [ClaimsTransformations](claimstransformations.md).

## <a name="generatejson"></a>GeraçãoJson

Utilize valores de reivindicação ou constantes para gerar uma cadeia JSON. A cadeia de caminhos que seguem a notação do ponto é utilizada para indicar onde inserir os dados numa cadeia JSON. Após a divisão por pontos, quaisquer inteiros são interpretados como o índice de uma matriz JSON e os não-inteiros são interpretados como o índice de um objeto JSON.

| Item | Tipo de reclamação de transformação | Tipo de Dados | Notas |
| ---- | ----------------------- | --------- | ----- |
| Pedido de crédito | Qualquer notação de corda que se seguisse | string | O JsonPath da JSON onde o valor da reclamação será inserido. |
| EntradaParametro | Qualquer notação de corda que se seguisse | string | O JsonPath da JSON onde o valor constante da cadeia será inserido. |
| Pedido de saída | saídaReclamada | string | A cadeia JSON gerada. |

O exemplo seguinte gera uma cadeia JSON baseada no valor de reclamação de "e-mail" e "otp" bem como cordas constantes.

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

As seguintes receitas de transformação de sinistros uma reivindicação de cadeia JSON que será o corpo do pedido enviado à SendGrid (um fornecedor de e-mail de terceiros). A estrutura do objeto JSON é definida pelos IDs na notação de pontos dos InputParameters e pelos Tipos de Reclamações de Transformação das InputClaims. Os números na notação do ponto implicam matrizes. Os valores provêm dos valores dos InputClaims e das propriedades "Valor" dos InputParameters.

- Créditos de entrada:
  - **e-mail**, transformação reivindicação tipo **personalizações.0.to.0.email**: "someone@example.com
  - **otp**, transformação reivindicações tipo **personalizações.0.dynamic_template_data.otp** "346349"
- Parâmetro de entrada:
  - **template_id**: "d-4c56ffb40fa648b1aa682283df94f60"
  - **de.email**:service@contoso.com"
  - **personalizações.0.sujeito** "Código de verificação de email saquetado"
- Reivindicação de saída:
  - **requestBody**: Valor JSON

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

Obtenha um elemento especificado a partir de um dado JSON.

| Item | Tipo de reclamação de transformação | Tipo de Dados | Notas |
| ---- | ----------------------- | --------- | ----- |
| Pedido de crédito | inputJson | string | Os Tipos de Reclamação que são utilizados pela transformação de sinistros para obter o item. |
| EntradaParametro | reivindicaçãoToExtrair | string | o nome do elemento JSON a extrair. |
| Pedido de saída | reclamação extraída | string | O ClaimType que é produzido após esta transformação de sinistros foi invocado, o valor do elemento especificado no parâmetro de entrada _claimToExtract._ |

No exemplo seguinte, a transformação `emailAddress` de sinistros extraiu o elemento dos dados da JSON:`{"emailAddress": "someone@example.com", "displayName": "Someone"}`

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

- Créditos de entrada:
  - **inputJson**: {"emailAddress": "someone@example.com", "displayName": "Someone"}
- Parâmetro de entrada:
    - **reivindicaçãoToExtrair**: endereço de e-mail
- Alegações de saída:
  - **extractidaReivindica:**someone@example.com


## <a name="getclaimsfromjsonarray"></a>GetClaimsFromJsonArray

Obtenha uma lista de elementos especificados a partir de dados da Json.

| Item | Tipo de reclamação de transformação | Tipo de Dados | Notas |
| ---- | ----------------------- | --------- | ----- |
| Pedido de crédito | jsonSourceClaim | string | Os Tipos de Reclamação que são utilizados pela transformação de sinistros para obter as reclamações. |
| EntradaParametro | errorOnMissingClaims | boolean | Especifica se deve lançar um erro se falta uma das reclamações. |
| EntradaParametro | incluir EmptyClaims | string | Especifique se deve incluir reclamações vazias. |
| EntradaParametro | jsonSourceKeyName | string | Nome da chave do elemento |
| EntradaParametro | jsonSourceValueName | string | Nome do valor do elemento |
| Pedido de saída | Coleção | corda, int, boolean, e data |Lista de alegações a extrair. O nome da reclamação deve ser igual ao especificado na alegação de entrada _jsonSourceClaim._ |

No exemplo seguinte, a transformação de sinistros extrai as seguintes alegações: e-mail (string), displayName (string), membershipNum (int), ative (boolean) e data de nascimento (data de data) dos dados jSON.

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

- Créditos de entrada:
  - **jsonSourceClaim**: [{"key":"email","value":"someone@example.com"}, {"key":"displayName","value":"Someone"}, {"key":"membershipNum","value":6353399}, {"key":"ative","value": true}, {"key":"data de nascimento", "valor":"1980-09-23T00:00:00:00"}"}
- Parâmetros de entrada:
    - **errorOnMissingClaims**: falso
    - **incluir EmptyClaims**: falso
    - **jsonSourceKeyName**: chave
    - **jsonSourceValueName**: valor
- Alegações de saída:
  - **e-mail**: "someone@example.com
  - **nome do ecrã**: "Alguém"
  - **membrosNo:** 6353399
  - **ativo**: verdadeiro
  - **data**de nascimento : 1980-09-23T00:00:00Z

## <a name="getnumericclaimfromjson"></a>GetNumeric ClaimFromJson

Obtém um elemento numérico (longo) especificado a partir de um dado JSON.

| Item | Tipo de reclamação de transformação | Tipo de Dados | Notas |
| ---- | ----------------------- | --------- | ----- |
| Pedido de crédito | inputJson | string | Os Tipos de Reclamação que são utilizados pela transformação de sinistros para obter a reclamação. |
| EntradaParametro | reivindicaçãoToExtrair | string | O nome do elemento JSON para extrair. |
| Pedido de saída | reclamação extraída | longo | O ClaimType que é produzido após esta Transformação de Reclamações foi invocado, o valor do elemento especificado nos parâmetros de entrada _do claimToExtract._ |

No exemplo seguinte, a transformação `id` de sinistros extrai o elemento dos dados jSON.

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

- Créditos de entrada:
  - **inputJson**: {"emailAddress": "someone@example.com", "displayName": "Someone," "id" : 6353399}
- Parâmetros de entrada
    - **reivindicaçãoToExtrair**: id
- Alegações de saída:
    - **extractida:** 6353399

## <a name="getsingleitemfromjson"></a>GetSingleItemFromJson

Obtém o primeiro elemento a partir de um dado JSON.

| Item | Tipo de reclamação de transformação | Tipo de Dados | Notas |
| ---- | ----------------------- | --------- | ----- |
| Pedido de crédito | inputJson | string | Os Tipos de Reclamação que são utilizados pela transformação de sinistros para obter o item dos dados jSON. |
| Pedido de saída | key | string | A primeira chave de elementos no JSON. |
| Pedido de saída | valor | string | O primeiro valor de elemento no JSON. |

No exemplo seguinte, a transformação de sinistros extrai o primeiro elemento (nome dado) dos dados jSON.

```XML
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

- Créditos de entrada:
  - **inputJson**: {"givenName": "Emilty", "lastName": "Smith"}
- Alegações de saída:
  - **chave**: nome dado
  - **valor**: Emilty


## <a name="getsinglevaluefromjsonarray"></a>GetSingleValueFromJsonArray

Obtém o primeiro elemento de uma matriz de dados DaJSON.

| Item | Tipo de reclamação de transformação | Tipo de Dados | Notas |
| ---- | ----------------------- | --------- | ----- |
| Pedido de crédito | inputJsonClaim | string | Os Tipos de Reclamação que são utilizados pela transformação de sinistros para obter o item da matriz JSON. |
| Pedido de saída | reclamação extraída | string | O ClaimType que é produzido após esta Transformação de Reclamações foi invocado, o primeiro elemento na matriz JSON. |

No exemplo seguinte, a transformação de sinistros extrai o primeiro `["someone@example.com", "Someone", 6353399]`elemento (endereço de e-mail) da matriz JSON .

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

- Créditos de entrada:
  - **inputJsonClaim:**["someone@example.com", "Someone", 6353399]
- Alegações de saída:
  - **extractidaReivindica:**someone@example.com

## <a name="xmlstringtojsonstring"></a>XmlStringToJsonString

Converte os dados XML para o formato JSON.

| Item | Tipo de reclamação de transformação | Tipo de Dados | Notas |
| ---- | ----------------------- | --------- | ----- |
| Pedido de crédito | xml | string | Os Tipos de Reclamação que são utilizados pela transformação de sinistros para converter os dados do formato XML para jSON. |
| Pedido de saída | json | string | O ClaimType que é produzido após esta Transformação de Reclamações foi invocado, os dados em formato JSON. |

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

No exemplo seguinte, a transformação de sinistros converte os seguintes dados XML para o formato JSON.

#### <a name="example"></a>Exemplo
Pedido de entrada:

```XML
<user>
  <name>Someone</name>
  <email>someone@example.com</email>
</user>
```

Reivindicação de saída:

```JSON
{
  "user": {
    "name":"Someone",
    "email":"someone@example.com"
  }
}
```


