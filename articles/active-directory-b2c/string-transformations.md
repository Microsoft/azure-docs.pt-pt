---
title: Exemplos de transformação de declarações de cadeia de caracteres para o esquema de estrutura de experiência de identidade de Azure Active Directory B2C | Microsoft Docs
description: Exemplos de transformação de declarações de cadeia de caracteres para o esquema de estrutura de experiência de identidade de Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 77f0b196777ae0f2ff0b870eac0a01b11854190b
ms.sourcegitcommit: f2d9d5133ec616857fb5adfb223df01ff0c96d0a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/03/2019
ms.locfileid: "71936816"
---
# <a name="string-claims-transformations"></a>Transformações de declarações de cadeia de caracteres

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Este artigo fornece exemplos de como usar as transformações de declarações de cadeia de caracteres do esquema de estrutura de experiência de identidade em Azure Active Directory B2C (Azure AD B2C). Para obter mais informações, consulte [ClaimsTransformations](claimstransformations.md).

## <a name="assertstringclaimsareequal"></a>AssertStringClaimsAreEqual

Compare duas declarações e lance uma exceção se elas não forem iguais de acordo com a comparação especificada inputClaim1, inputClaim2 e stringComparison.

| Item | TransformationClaimType | Tipo de Dados | Notas |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim1 | Cadeia de caracteres | Tipo da primeira declaração, que deve ser comparada. |
| InputClaim | inputClaim2 | Cadeia de caracteres | Tipo da segunda declaração, que deve ser comparada. |
| InputParameter | stringComparison | Cadeia de caracteres | comparação de cadeia de caracteres, um dos valores: Ordinal, OrdinalIgnoreCase. |

A transformação declarações **AssertStringClaimsAreEqual** é sempre executada de um [perfil técnico de validação](validation-technical-profile.md) que é chamado por um [perfil técnico autodeclarado](self-asserted-technical-profile.md). Os metadados do perfil técnico autodeclarado **UserMessageIfClaimsTransformationStringsAreNotEqual** controlam a mensagem de erro apresentada ao usuário.

![Execução de AssertStringClaimsAreEqual](./media/string-transformations/assert-execution.png)

Você pode usar essa transformação de declarações para certificar-se de que duas ClaimTypes tenham o mesmo valor. Caso contrário, uma mensagem de erro será lançada. O exemplo a seguir verifica se o ClaimType **strongAuthenticationEmailAddress** é igual a declarar de **email** . Caso contrário, uma mensagem de erro será gerada.

```XML
<ClaimsTransformation Id="AssertEmailAndStrongAuthenticationEmailAddressAreEqual" TransformationMethod="AssertStringClaimsAreEqual">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="strongAuthenticationEmailAddress" TransformationClaimType="inputClaim1" />
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="inputClaim2" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="stringComparison" DataType="string" Value="ordinalIgnoreCase" />
  </InputParameters>
</ClaimsTransformation>
```


O perfil técnico **logon-não interativo** de validação chama a transformação declarações **AssertEmailAndStrongAuthenticationEmailAddressAreEqual** .
```XML
<TechnicalProfile Id="login-NonInteractive">
  ...
  <OutputClaimsTransformations>
    <OutputClaimsTransformation ReferenceId="AssertEmailAndStrongAuthenticationEmailAddressAreEqual" />
  </OutputClaimsTransformations>
</TechnicalProfile>
```

O perfil técnico autodeclarado chama o logon de validação **–** perfil técnico não interativo.

```XML
<TechnicalProfile Id="SelfAsserted-LocalAccountSignin-Email">
  <Metadata>
    <Item Key="UserMessageIfClaimsTransformationStringsAreNotEqual">Custom error message the email addresses you provided are not the same.</Item>
  </Metadata>
  <ValidationTechnicalProfiles>
    <ValidationTechnicalProfile ReferenceId="login-NonInteractive" />
  </ValidationTechnicalProfiles>
</TechnicalProfile>
```

### <a name="example"></a>Exemplo

- Declarações de entrada:
  - **inputClaim1**:someone@contoso.com
  - **inputClaim2**:someone@outlook.com
    - Parâmetros de entrada:
  - **stringComparison**: ordinalIgnoreCase
- Disso Erro gerado

## <a name="changecase"></a>ChangeCase

Altera o caso da declaração fornecida para letras minúsculas ou maiúsculas, dependendo do operador.

| Item | TransformationClaimType | Tipo de Dados | Notas |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim1 | Cadeia de caracteres | O ClaimType que foi alterado. |
| InputParameter | tocase | Cadeia de caracteres | Um dos seguintes valores: `LOWER` ou. `UPPER` |
| OutputClaim | outputClaim | Cadeia de caracteres | O ClaimType que é produzido depois que essa transformação de declarações foi invocada. |

Use essa transformação de declaração para alterar qualquer declaração de cadeia de caracteres para letras minúsculas ou maiúsculas.

```XML
<ClaimsTransformation Id="ChangeToLower" TransformationMethod="ChangeCase">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="inputClaim1" />
  </InputClaims>
<InputParameters>
  <InputParameter Id="toCase" DataType="string" Value="LOWER" />
</InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="email" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Exemplo

- Declarações de entrada:
  - **email**:SomeOne@contoso.com
- Parâmetros de entrada:
    - **tocase**: LOWER
- Declarações de saída:
  - **email**:someone@contoso.com

## <a name="createstringclaim"></a>CreateStringClaim

Cria uma declaração de cadeia de caracteres do parâmetro de entrada fornecido na política.

| Item | TransformationClaimType | Tipo de Dados | Notas |
|----- | ----------------------- | --------- | ----- |
| InputParameter | value | Cadeia de caracteres | A cadeia de caracteres a ser definida |
| OutputClaim | createdClaim | Cadeia de caracteres | O ClaimType que é produzido após essa transformação de declarações foi invocado, com o valor especificado no parâmetro de entrada. |

Use essa transformação de declarações para definir um valor de ClaimType de cadeia de caracteres.

```XML
<ClaimsTransformation Id="CreateTermsOfService" TransformationMethod="CreateStringClaim">
  <InputParameters>
    <InputParameter Id="value" DataType="string" Value="Contoso terms of service..." />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="TOS" TransformationClaimType="createdClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Exemplo

- Parâmetro de entrada:
    - **valor**: Termos de serviço da contoso...
- Declarações de saída:
    - **createdClaim**: O ClaimType do TOS contém os "termos de serviço contoso..." valor.

## <a name="compareclaims"></a>CompareClaims

Determine se uma declaração de cadeia de caracteres é igual a outra. O resultado é um novo ClaimType booliano com um valor `true` de `false`ou.

| Item | TransformationClaimType | Tipo de Dados | Notas |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim1 | Cadeia de caracteres | Primeiro tipo de declaração, que deve ser comparado. |
| InputClaim | inputClaim2 | Cadeia de caracteres | Segundo tipo de declaração, que deve ser comparada. |
| InputParameter | operator | Cadeia de caracteres | Valores possíveis: `EQUAL` ou `NOT EQUAL`. |
| InputParameter | ignoreCase | boolean | Especifica se essa comparação deve ignorar o caso das cadeias de caracteres sendo comparadas. |
| OutputClaim | outputClaim | boolean | O ClaimType que é produzido depois que essa transformação de declarações foi invocada. |

Use essa transformação de declarações para verificar se uma declaração é igual a outra declaração. Por exemplo, a transformação declarações a seguir verifica se o valor da declaração de **email** é igual à Declaração **. email verificada** .

```XML
<ClaimsTransformation Id="CheckEmail" TransformationMethod="CompareClaims">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="Email" TransformationClaimType="inputClaim1" />
    <InputClaim ClaimTypeReferenceId="Verified.Email" TransformationClaimType="inputClaim2" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="operator" DataType="string" Value="NOT EQUAL" />
    <InputParameter Id="ignoreCase" DataType="string" Value="true" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="SameEmailAddress" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Exemplo

- Declarações de entrada:
  - **inputClaim1**:someone@contoso.com
  - **inputClaim2**:someone@outlook.com
- Parâmetros de entrada:
    - **operador**:  DIFERENTE DE
    - **IgnoreCase**: verdadeiro
- Declarações de saída:
    - **outputClaim**: true

## <a name="compareclaimtovalue"></a>CompareClaimToValue

Determina se um valor de declaração é igual ao valor do parâmetro de entrada.

| Item | TransformationClaimType | Tipo de Dados | Notas |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim1 | Cadeia de caracteres | O tipo da declaração, que deve ser comparada. |
| InputParameter | operator | Cadeia de caracteres | Valores possíveis: `EQUAL` ou `NOT EQUAL`. |
| InputParameter | compareTo | Cadeia de caracteres | comparação de cadeia de caracteres, um dos valores: Ordinal, OrdinalIgnoreCase. |
| InputParameter | ignoreCase | boolean | Especifica se essa comparação deve ignorar o caso das cadeias de caracteres sendo comparadas. |
| OutputClaim | outputClaim | boolean | O ClaimType que é produzido depois que essa transformação de declarações foi invocada. |

Você pode usar essa transformação de declarações para verificar se uma declaração é igual a um valor especificado. Por exemplo, a transformação declarações a seguir verifica se o valor da Declaração **termsOfUseConsentVersion** é igual a `v1`.

```XML
<ClaimsTransformation Id="IsTermsOfUseConsentRequiredForVersion" TransformationMethod="CompareClaimToValue">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="termsOfUseConsentVersion" TransformationClaimType="inputClaim1" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="compareTo" DataType="string" Value="V1" />
    <InputParameter Id="operator" DataType="string" Value="not equal" />
    <InputParameter Id="ignoreCase" DataType="string" Value="true" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="termsOfUseConsentRequired" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Exemplo
- Declarações de entrada:
    - **inputClaim1**: v1
- Parâmetros de entrada:
    - **compareTo**: V1
    - **operador**: EQUAL
    - **IgnoreCase**: verdadeiro
- Declarações de saída:
    - **outputClaim**: true

## <a name="createrandomstring"></a>CreateRandomString

Cria uma cadeia de caracteres aleatória usando o gerador de números aleatórios. Se o gerador de número aleatório for do `integer`tipo, opcionalmente, um parâmetro semente e um número máximo poderão ser fornecidos. Um parâmetro de formato de cadeia de caracteres opcional permite que a saída seja formatada usando-o e um parâmetro opcional Base64 especifica se a saída é codificada na base64 randomGeneratorType [GUID, Integer] outputClaim (String).

| Item | TransformationClaimType | Tipo de Dados | Notas |
| ---- | ----------------------- | --------- | ----- |
| InputParameter | randomGeneratorType | Cadeia de caracteres | Especifica o valor aleatório a ser gerado, `GUID` (ID global exclusiva) ou `INTEGER` (um número). |
| InputParameter | stringFormat | Cadeia de caracteres | Adicional Formate o valor aleatório. |
| InputParameter | base64 | boolean | Adicional Converta o valor aleatório em base64. Se o formato da cadeia de caracteres for aplicado, o valor após o formato da cadeia de caracteres será codificado em base64. |
| InputParameter | maximumNumber | int | Adicional Somente para `INTEGER` randomGeneratorType. Especifique o número máximo. |
| InputParameter | semente  | int | Adicional Somente para `INTEGER` randomGeneratorType. Especifique a semente para o valor aleatório. Observação: a mesma semente produz a mesma sequência de números aleatórios. |
| OutputClaim | outputClaim | Cadeia de caracteres | O ClaimTypes que será produzido depois que essa transformação de declarações for invocada. O valor aleatório. |

O exemplo a seguir gera uma ID exclusiva global. Essa transformação de declarações é usada para criar o UPN aleatório (nome do princípio do usuário).

```XML
<ClaimsTransformation Id="CreateRandomUPNUserName" TransformationMethod="CreateRandomString">
  <InputParameters>
    <InputParameter Id="randomGeneratorType" DataType="string" Value="GUID" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="upnUserName" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```
### <a name="example"></a>Exemplo

- Parâmetros de entrada:
    - **randomGeneratorType**: GUID
- Declarações de saída:
    - **outputClaim**: bc8bedd2-aaa3-411e-bdee-2f1810b73dfc

O exemplo a seguir gera um valor aleatório inteiro entre 0 e 1000. O valor é formatado para OTP_ {valor aleatório}.

```XML
<ClaimsTransformation Id="SetRandomNumber" TransformationMethod="CreateRandomString">
  <InputParameters>
    <InputParameter Id="randomGeneratorType" DataType="string" Value="INTEGER" />
    <InputParameter Id="maximumNumber" DataType="int" Value="1000" />
    <InputParameter Id="stringFormat" DataType="string" Value="OTP_{0}" />
    <InputParameter Id="base64" DataType="boolean" Value="false" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="randomNumber" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Exemplo

- Parâmetros de entrada:
    - **randomGeneratorType**: INTEGER
    - **maximumNumber**: 1000
    - **stringFormat**: OTP_{0}
    - **Base64**: falso
- Declarações de saída:
    - **outputClaim**: OTP_853


## <a name="formatstringclaim"></a>FormatStringClaim

Formate uma declaração de acordo com a cadeia de caracteres de formato fornecida. Essa transformação usa o C# `String.Format` método.

| Item | TransformationClaimType | Tipo de Dados | Notas |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | InputClaim |Cadeia de caracteres |O ClaimType que atua como parâmetro de {0} formato de cadeia de caracteres. |
| InputParameter | stringFormat | Cadeia de caracteres | O formato da cadeia de caracteres {0} , incluindo o parâmetro. |
| OutputClaim | outputClaim | Cadeia de caracteres | O ClaimType que é produzido depois que essa transformação de declarações foi invocada. |

Use essa transformação de declarações para formatar qualquer cadeia de caracteres {0}com um parâmetro. O exemplo a seguir cria um **userPrincipalName**. Todos os perfis técnicos do provedor de identidade social `Facebook-OAUTH` , como chamadas de **createuserprincipalname** para gerar um **userPrincipalName**.

```XML
<ClaimsTransformation Id="CreateUserPrincipalName" TransformationMethod="FormatStringClaim">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="upnUserName" TransformationClaimType="inputClaim" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="stringFormat" DataType="string" Value="cpim_{0}@{RelyingPartyTenantId}" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="userPrincipalName" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Exemplo

- Declarações de entrada:
    - **inputClaim**: 5164db16-3eee-4629-bfda-dcc3326790e9
- Parâmetros de entrada:
    - **StringFormat**: cpim_ @ no__t-1 @ {RelyingPartyTenantId}
- Declarações de saída:
  - **outputClaim**:cpim_5164db16-3eee-4629-bfda-dcc3326790e9@b2cdemo.onmicrosoft.com

## <a name="formatstringmultipleclaims"></a>FormatStringMultipleClaims

Formate duas declarações de acordo com a cadeia de caracteres de formato fornecida. Essa transformação usa o C# método **String. Format** .

| Item | TransformationClaimType | Tipo de Dados | Notas |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | InputClaim |Cadeia de caracteres | O ClaimType que atua como parâmetro de {0} formato de cadeia de caracteres. |
| InputClaim | InputClaim | Cadeia de caracteres | O ClaimType que atua como parâmetro de {1} formato de cadeia de caracteres. |
| InputParameter | stringFormat | Cadeia de caracteres | O formato da cadeia de caracteres {0} , {1} incluindo os parâmetros e. |
| OutputClaim | outputClaim | Cadeia de caracteres | O ClaimType que é produzido depois que essa transformação de declarações foi invocada. |

Use essa transformação de declarações para formatar qualquer cadeia de caracteres com {0} dois {1}parâmetros e. O exemplo a seguir cria um **DisplayName** com o formato especificado:

```XML
<ClaimsTransformation Id="CreateDisplayNameFromFirstNameAndLastName" TransformationMethod="FormatStringMultipleClaims">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="givenName" TransformationClaimType="inputClaim1" />
    <InputClaim ClaimTypeReferenceId="surName" TransformationClaimType="inputClaim2" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="stringFormat" DataType="string" Value="{0} {1}" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="displayName" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Exemplo

- Declarações de entrada:
    - **inputClaim1**: Joe
    - **inputClaim2**: Fernando
- Parâmetros de entrada:
    - **StringFormat**: {0}{1}
- Declarações de saída:
    - **outputClaim**: Joe Fernando

## <a name="getmappedvaluefromlocalizedcollection"></a>GetMappedValueFromLocalizedCollection

Pesquisando um item de uma coleção de **restrições** de declaração.

| Item | TransformationClaimType | Tipo de Dados | Notas |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | mapFromClaim | Cadeia de caracteres | A declaração que contém o texto a ser pesquisado nas declarações **restrictionValueClaim** com a coleção de **restrições** .  |
| OutputClaim | restrictionValueClaim | Cadeia de caracteres | A declaração que contém a coleção de **restrições** . Depois que a transformação de declarações for invocada, o valor dessa declaração conterá o valor do item selecionado. |

O exemplo a seguir pesquisa a descrição da mensagem de erro com base na chave de erro. A Declaração **responseMsg** contém uma coleção de mensagens de erro para apresentar ao usuário final ou para ser enviada para a terceira parte confiável.

```XML
<ClaimType Id="responseMsg">
  <DisplayName>Error message: </DisplayName>
  <DataType>string</DataType>
  <UserInputType>Paragraph</UserInputType>
  <Restriction>
    <Enumeration Text="B2C_V1_90001" Value="You cant sign in because you are a minor" />
    <Enumeration Text="B2C_V1_90002" Value="This action can only be performed by gold members" />
    <Enumeration Text="B2C_V1_90003" Value="You have not been enabled for this operation" />
  </Restriction>
</ClaimType>
```
A transformação declarações pesquisa o texto do item e retorna seu valor. Se a restrição for localizada usando `<LocalizedCollection>`, a transformação declarações retornará o valor localizado.

```XML
<ClaimsTransformation Id="GetResponseMsgMappedToResponseCode" TransformationMethod="GetMappedValueFromLocalizedCollection">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="responseCode" TransformationClaimType="mapFromClaim" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="responseMsg" TransformationClaimType="restrictionValueClaim" />        
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Exemplo

- Declarações de entrada:
    - **mapFromClaim**: B2C_V1_90001
- Declarações de saída:
    - **restrictionValueClaim**: Não é possível entrar porque você é um pequeno.

## <a name="lookupvalue"></a>LookupValue

Pesquisar um valor de declaração de uma lista de valores com base no valor de outra declaração.

| Item | TransformationClaimType | Tipo de Dados | Notas |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputParameterId | Cadeia de caracteres | A declaração que contém o valor de pesquisa |
| InputParameter | |Cadeia de caracteres | Coleção de InputParameters. |
| InputParameter | errorOnFailedLookup | boolean | Controlando se um erro é retornado quando nenhuma pesquisa correspondente. |
| OutputClaim | inputParameterId | Cadeia de caracteres | O ClaimTypes que será produzido depois que essa transformação de declarações for invocada. O valor da ID correspondente. |

O exemplo a seguir pesquisa o nome de domínio em uma das coleções InputParameters. A transformação declarações pesquisa o nome de domínio no identificador e retorna seu valor (uma ID de aplicativo).

```XML
 <ClaimsTransformation Id="DomainToClientId" TransformationMethod="LookupValue">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="domainName" TransformationClaimType="inputParameterId" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="contoso.com" DataType="string" Value="13c15f79-8fb1-4e29-a6c9-be0d36ff19f1" />
    <InputParameter Id="microsoft.com" DataType="string" Value="0213308f-17cb-4398-b97e-01da7bd4804e" />
    <InputParameter Id="test.com" DataType="string" Value="c7026f88-4299-4cdb-965d-3f166464b8a9" />
    <InputParameter Id="errorOnFailedLookup" DataType="boolean" Value="false" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="domainAppId" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Exemplo

- Declarações de entrada:
    - **inputParameterId**: test.com
- Parâmetros de entrada:
    - **contoso.com**: 13c15f79-8fb1-4e29-a6c9-be0d36ff19f1
    - **microsoft.com**: 0213308f-17cb-4398-b97e-01da7bd4804e
    - **Test.com**: c7026f88-4299-4cdb-965d-3f166464b8a9
    - **errorOnFailedLookup**: false
- Declarações de saída:
    - **outputClaim**:  c7026f88-4299-4cdb-965d-3f166464b8a9

## <a name="nullclaim"></a>NullClaim

Limpe o valor de uma determinada declaração.

| Item | TransformationClaimType | Tipo de Dados | Notas |
| ---- | ----------------------- | --------- | ----- |
| OutputClaim | claim_to_null | Cadeia de caracteres | A declaração seu valor como nulo. |

Use essa transformação de declaração para remover dados desnecessários do recipiente de propriedades de declarações. Portanto, o cookie de sessão será menor. O exemplo a seguir remove o valor do `TermsOfService` tipo de declaração.

```XML
<ClaimsTransformation Id="SetTOSToNull" TransformationMethod="NullClaim">
  <OutputClaims>
  <OutputClaim ClaimTypeReferenceId="TermsOfService" TransformationClaimType="claim_to_null" />
  </OutputClaims>
</ClaimsTransformation>
```

- Declarações de entrada:
    - **outputClaim**: Bem-vindo ao aplicativo contoso. Se você continuar a procurar e usar este site, estará concordando em obedecer e esteja associado aos seguintes termos e condições...
- Declarações de saída:
    - **outputClaim**: NULL

## <a name="parsedomain"></a>ParseDomain

Obtém a parte do domínio de um endereço de email.

| Item | TransformationClaimType | Tipo de Dados | Notas |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | emailAddress | Cadeia de caracteres | O ClaimType que contém o endereço de email. |
| OutputClaim | domínio | Cadeia de caracteres | O ClaimType que é produzido depois que essa transformação de declarações foi invocada-o domínio. |

Use essa transformação de declarações para analisar o nome de domínio após o símbolo @ do usuário. Isso pode ser útil para remover informações de identificação pessoal (PII) dos dados de auditoria. A transformação de declarações a seguir demonstra como analisar o nome de domínio de uma declaração de **email** .

```XML
<ClaimsTransformation Id="SetDomainName" TransformationMethod="ParseDomain">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="emailAddress" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="domainName" TransformationClaimType="domain" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Exemplo

- Declarações de entrada:
  - **EmailAddress**:joe@outlook.com
- Declarações de saída:
    - **domínio**: Outlook.com

## <a name="setclaimsifstringsareequal"></a>SetClaimsIfStringsAreEqual

Verifica se uma declaração de cadeia `matchTo` de caracteres e um parâmetro de entrada são iguais e define as declarações de saída `stringMatchMsg` com `stringMatchMsgCode` o valor presente em e parâmetros de entrada, juntamente com a declaração comparar resultado de `true` saída, que deve ser definida como ou `false` com base no resultado da comparação.

| Item | TransformationClaimType | Tipo de Dados | Notas |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | InputClaim | Cadeia de caracteres | O tipo de declaração, que deve ser comparado. |
| InputParameter | matchTo | Cadeia de caracteres | A cadeia de caracteres com `inputClaim`a qual será comparada. |
| InputParameter | stringComparison | Cadeia de caracteres | Valores possíveis: `Ordinal` ou `OrdinalIgnoreCase`. |
| InputParameter | stringMatchMsg | Cadeia de caracteres | Primeiro valor a ser definido se as cadeias de caracteres forem iguais. |
| InputParameter | stringMatchMsgCode | Cadeia de caracteres | O segundo valor a ser definido se as cadeias de caracteres forem iguais. |
| OutputClaim | outputClaim1 | Cadeia de caracteres | Se as cadeias de caracteres forem iguais, essa declaração de `stringMatchMsg` saída conterá o valor do parâmetro de entrada. |
| OutputClaim | outputClaim2 | Cadeia de caracteres | Se as cadeias de caracteres forem iguais, essa declaração de `stringMatchMsgCode` saída conterá o valor do parâmetro de entrada. |
| OutputClaim | stringCompareResultClaim | boolean | O tipo de declaração de saída de resultado de comparação, que deve `true` ser `false` definido como ou com base no resultado da comparação. |

Você pode usar essa transformação de declarações para verificar se uma declaração é igual ao valor especificado. Por exemplo, a transformação declarações a seguir verifica se o valor da Declaração **termsOfUseConsentVersion** é igual a `v1`. Em caso afirmativo, altere o `v2`valor para.

```XML
<ClaimsTransformation Id="CheckTheTOS" TransformationMethod="SetClaimsIfStringsAreEqual">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="termsOfUseConsentVersion" TransformationClaimType="inputClaim" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="matchTo" DataType="string" Value="v1" />
    <InputParameter Id="stringComparison" DataType="string" Value="ordinalIgnoreCase" />
    <InputParameter Id="stringMatchMsg" DataType="string" Value="B2C_V1_90005" />
    <InputParameter Id="stringMatchMsgCode" DataType="string" Value="The TOS is upgraded to v2" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="termsOfUseConsentVersion" TransformationClaimType="outputClaim1" />
    <OutputClaim ClaimTypeReferenceId="termsOfUseConsentVersionUpgradeCode" TransformationClaimType="outputClaim2" />
    <OutputClaim ClaimTypeReferenceId="termsOfUseConsentVersionUpgradeResult" TransformationClaimType="stringCompareResultClaim" />
  </OutputClaims>
</ClaimsTransformation>
```
### <a name="example"></a>Exemplo

- Declarações de entrada:
    - **inputClaim**: v1
- Parâmetros de entrada:
    - **matchTo**: V1
    - **stringComparison**: ordinalIgnoreCase
    - **stringMatchMsg**:  B2C_V1_90005
    - **stringMatchMsgCode**:  O TOS é atualizado para a v2
- Declarações de saída:
    - **outputClaim1**: B2C_V1_90005
    - **outputClaim2**: O TOS é atualizado para a v2
    - **stringCompareResultClaim**: true

## <a name="setclaimsifstringsmatch"></a>SetClaimsIfStringsMatch

Verifica se uma declaração de cadeia `matchTo` de caracteres e um parâmetro de entrada são iguais e define as declarações de saída `outputClaimIfMatched` com o valor presente no parâmetro de entrada, juntamente com a declaração comparar resultado de `true` saída `false` , que deve ser definida como ou com base no resultado da comparação.

| Item | TransformationClaimType | Tipo de Dados | Notas |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | claimToMatch | Cadeia de caracteres | O tipo de declaração, que deve ser comparado. |
| InputParameter | matchTo | Cadeia de caracteres | A cadeia de caracteres a ser comparada com inputClaim. |
| InputParameter | stringComparison | Cadeia de caracteres | Valores possíveis: `Ordinal` ou `OrdinalIgnoreCase`. |
| InputParameter | outputClaimIfMatched | Cadeia de caracteres | O valor a ser definido se as cadeias de caracteres forem iguais. |
| OutputClaim | outputClaim | Cadeia de caracteres | Se as cadeias de caracteres forem iguais, essa declaração de `outputClaimIfMatched` saída conterá o valor do parâmetro de entrada. Ou NULL, se as cadeias de caracteres não forem correspondentes. |
| OutputClaim | stringCompareResultClaim | boolean | O tipo de declaração de saída de resultado de comparação, que deve `true` ser `false` definido como ou com base no resultado da comparação. |

Por exemplo, a transformação declarações a seguir verifica se o valor da Declaração **ageGroup** é igual `Minor`a. Em caso afirmativo, retorne `B2C_V1_90001`o valor para.

```XML
<ClaimsTransformation Id="SetIsMinor" TransformationMethod="SetClaimsIfStringsMatch">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="ageGroup" TransformationClaimType="claimToMatch" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="matchTo" DataType="string" Value="Minor" />
    <InputParameter Id="stringComparison" DataType="string" Value="ordinalIgnoreCase" />
    <InputParameter Id="outputClaimIfMatched" DataType="string" Value="B2C_V1_90001" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="isMinor" TransformationClaimType="outputClaim" />
    <OutputClaim ClaimTypeReferenceId="isMinorResponseCode" TransformationClaimType="stringCompareResultClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Exemplo

- Declarações de entrada:
    - **claimToMatch**: Pequenas
- Parâmetros de entrada:
    - **matchTo**: Pequenas
    - **stringComparison**: ordinalIgnoreCase
    - **outputClaimIfMatched**:  B2C_V1_90001
- Declarações de saída:
    - **isMinorResponseCode**: B2C_V1_90001
    - **isminor**: true

