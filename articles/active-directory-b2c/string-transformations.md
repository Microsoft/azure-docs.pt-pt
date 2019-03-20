---
title: Exemplos de transformação de afirmações de cadeia de caracteres para a identidade experiência de estrutura de esquema do Azure Active Directory B2C | Documentos da Microsoft
description: Cadeia de caracteres de exemplos de transformação de afirmações para a identidade experiência de estrutura de esquema do Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 32acee78b1aadbe5a461de6ea4475cf28503c325
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58113032"
---
# <a name="string-claims-transformations"></a>Transformações de afirmações de cadeia de caracteres

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Este artigo fornece exemplos para usar as transformações de afirmações de cadeia de caracteres do esquema de arquitetura de experiências de identidade no Azure Active Directory (Azure AD) B2C. Para obter mais informações, consulte [ClaimsTransformations](claimstransformations.md).

## <a name="assertstringclaimsareequal"></a>AssertStringClaimsAreEqual 

Comparar duas afirmações e lançar uma exceção se não forem iguais, de acordo com o inputClaim1 de comparação especificada, inputClaim2 e stringComparison.

| Item | TransformationClaimType | Tipo de Dados | Notas |
| ---- | ----------------------- | --------- | ----- |
| inputClaim | inputClaim1 | string | Tipo de primeira afirmação, que é ser comparadas. |
| inputClaim | inputClaim2 | string | Segundo o tipo da afirmação, que está a ser comparado. |
| InputParameter | stringComparison | string | comparação de cadeia de caracteres, um dos valores: Ordinal, OrdinalIgnoreCase. |

O **AssertStringClaimsAreEqual** transformação de declarações é sempre executada a partir de um [perfil técnico de validação](validation-technical-profile.md) que é chamado por um [autodeclarativas perfil técnico](self-asserted-technical-profile.md). O **UserMessageIfClaimsTransformationStringsAreNotEqual** metadados de declaração própria perfil técnico controla a mensagem de erro que é apresentada ao utilizador.

![AssertStringClaimsAreEqual execution](./media/string-transformations/assert-execution.png)

Pode usar essa transformação para se certificar de afirmações, dois ClaimTypes têm o mesmo valor. Caso contrário, será gerada uma mensagem de erro. O exemplo a seguir verifica se o **strongAuthenticationEmailAddress** ClaimType é igual a **e-mail** ClaimType. Caso contrário, será gerada uma mensagem de erro. 

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


O **início de sessão NonInteractive** chamadas de perfil técnico de validação a **AssertEmailAndStrongAuthenticationEmailAddressAreEqual** transformação de afirmações.
```XML
<TechnicalProfile Id="login-NonInteractive">
  ...
  <OutputClaimsTransformations>
    <OutputClaimsTransformation ReferenceId="AssertEmailAndStrongAuthenticationEmailAddressAreEqual" />
  </OutputClaimsTransformations>
</TechnicalProfile>
```

O perfil técnico de declaração própria chama a validação **NonInteractive de início de sessão** perfil técnico.

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

- Afirmações de entrada:
  - **inputClaim1**: someone@contoso.com
  - **inputClaim2**: someone@outlook.com
    - Parâmetros de entrada:
  - **stringComparison**:  ordinalIgnoreCase
- Resultado: Erro gerado

## <a name="changecase"></a>ChangeCase 

Altera o caso da afirmação fornecido para reduzir ou maiúsculas consoante o operador.

| Item | TransformationClaimType | Tipo de Dados | Notas |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim1 | string | O ClaimType que ser alterado. |
| InputParameter | toCase | string | Um dos seguintes valores: `LOWER` ou `UPPER`. |
| OutputClaim | outputClaim | string | Po vyvolání o ClaimType produzido depois de esta transformação de afirmações. |

Utilize esta transformação de afirmações para alterar qualquer cadeia de caracteres ClaimType para reduzir ou maiúsculas.  

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

- Afirmações de entrada:
  - **e-mail**: SomeOne@contoso.com
- Parâmetros de entrada:
    - **toCase**: LOWER
- Afirmações de saída:
  - **e-mail**: someone@contoso.com

## <a name="createstringclaim"></a>CreateStringClaim 

Cria uma afirmação de cadeia de caracteres de parâmetro de entrada fornecido na política.

| Item | TransformationClaimType | Tipo de Dados | Notas |
|----- | ----------------------- | --------- | ----- |
| InputParameter | valor | string | A cadeia a ser definido |
| OutputClaim | createdClaim | string | O que é produzido depois de esta transformação de afirmações ClaimType ser invocado, com o valor especificado no parâmetro de entrada. |

Utilize que este afirmações de transformação para definir uma cadeia de valor de ClaimType.

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
    - **Valor**: Contoso termos de serviço...
- Afirmações de saída:
    - **createdClaim**: Os guias de instruções ClaimType contém o valor de "Contoso termos de serviço...".

## <a name="compareclaims"></a>CompareClaims

Determine se uma afirmação de cadeia de caracteres é igual para outro. O resultado é um novo ClaimType booleano com um valor de `true` ou `false`.

| Item | TransformationClaimType | Tipo de Dados | Notas |
| ---- | ----------------------- | --------- | ----- |
| inputClaim | inputClaim1 | string | Tipo, o que está a ser comparada de afirmação pela primeira vez. |
| inputClaim | inputClaim2 | string | Tipo, o que está a ser comparada de afirmação em segundo lugar. |
| InputParameter | Operador | string | Valores possíveis: `EQUAL` ou `NOT EQUAL`. |
| InputParameter | ignoreCase | boolean | Especifica se esta comparação deve ignorar o caso das cadeias de caracteres está a ser comparado. |
| OutputClaim | outputClaim | boolean | Po vyvolání o ClaimType produzido depois de esta transformação de afirmações. |

Utilize que este afirmações de transformação para verificar se uma afirmação é igual a outra declaração. Por exemplo, as seguintes afirmações verificações de transformação, se o valor do **e-mail** afirmação é igual para o **Verified.Email** de afirmação.

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

- Afirmações de entrada:
  - **inputClaim1**: someone@contoso.com
  - **inputClaim2**: someone@outlook.com
- Parâmetros de entrada:
    - **operator**:  NOT EQUAL
    - **ignoreCase**: VERDADEIRO
- Afirmações de saída:
    - **outputClaim**: true

## <a name="compareclaimtovalue"></a>CompareClaimToValue

Determina se um valor de afirmação é igual ao valor de parâmetro de entrada.

| Item | TransformationClaimType | Tipo de Dados | Notas |
| ---- | ----------------------- | --------- | ----- |
| inputClaim | inputClaim1 | string | Tipo da afirmação, que é ser comparadas. |
| InputParameter | Operador | string | Valores possíveis: `EQUAL` ou `NOT EQUAL`. |
| InputParameter | compareTo | string | comparação de cadeia de caracteres, um dos valores: Ordinal, OrdinalIgnoreCase. |
| InputParameter | ignoreCase | boolean | Especifica se esta comparação deve ignorar o caso das cadeias de caracteres está a ser comparado. |
| OutputClaim | outputClaim | boolean | Po vyvolání o ClaimType produzido depois de esta transformação de afirmações. |

Pode utilizar este afirmações de transformação para verificar se uma afirmação é igual a um valor que especificou. Por exemplo, as seguintes afirmações verificações de transformação, se o valor do **termsOfUseConsentVersion** afirmação é igual a `v1`.

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
- Afirmações de entrada:
    - **inputClaim1**: v1
- Parâmetros de entrada:
    - **compareTo**: V1
    - **operator**: EQUAL 
    - **ignoreCase**: VERDADEIRO
- Afirmações de saída:
    - **outputClaim**: true

## <a name="createrandomstring"></a>CreateRandomString

Cria uma cadeia de caracteres aleatória usando o gerador de números aleatórios. Se o gerador de números aleatórios é do tipo `integer`, opcionalmente, podem ser fornecidos um parâmetro de seed e um número máximo. Um parâmetro de formato de cadeia de caracteres opcional permite a saída para ter o formato usá-lo e um parâmetro de base64 opcional especifica se a saída é codificado em base64 randomGeneratorType [guid, número inteiro] outputClaim (cadeia).

| Item | TransformationClaimType | Tipo de Dados | Notas |
| ---- | ----------------------- | --------- | ----- |
| InputParameter | randomGeneratorType | string | Especifica o valor aleatório sejam gerados `GUID` (ID exclusivo global) ou `INTEGER` (um número). |
| InputParameter | stringFormat | string | [Opcional] Formate o valor aleatório. |
| InputParameter | base64 | boolean | [Opcional] Converta o valor aleatório para base64. Se o formato de cadeia de caracteres é aplicado, o valor depois de formato de cadeia está codificado para base64. |
| InputParameter | maximumNumber | int | [Opcional] Para `INTEGER` randomGeneratorType apenas. Especifique o número máximo. |
| InputParameter | seed  | int | [Opcional] Para `INTEGER` randomGeneratorType apenas. Especifique a semente para o valor aleatório. Nota: a seed mesmo gera a mesma seqüência de números aleatórios. |
| OutputClaim | outputClaim | string | Os ClaimTypes que serão produzidos depois de esta transformação de afirmações ser invocado. O valor aleatório. |

Exemplo a seguir gera um ID exclusivo global. Este afirmações de transformação é utilizada para criar o aleatório UPN (nome do principal de utilizador).

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
- Afirmações de saída: 
    - **outputClaim**: bc8bedd2-aaa3-411e-bdee-2f1810b73dfc

Exemplo a seguir gera um valor aleatório de número inteiro entre 0 e 1000. O valor é formatado para OTP_ {valor aleatório}.

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
    - **randomGeneratorType**: NÚMERO INTEIRO
    - **maximumNumber**: 1000
    - **stringFormat**: OTP_{0}
    - **base64**: false
- Afirmações de saída: 
    - **outputClaim**: OTP_853


## <a name="formatstringclaim"></a>FormatStringClaim

Formato de uma afirmação, de acordo com a cadeia de formato fornecido. Essa transformação utiliza c# `String.Format` método.

| Item | TransformationClaimType | Tipo de Dados | Notas |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim |string |O ClaimType que age como formato de cadeia de caracteres {0} parâmetro. |
| InputParameter | stringFormat | string | O formato de cadeia de caracteres, incluindo o {0} parâmetro. |
| OutputClaim | outputClaim | string | Po vyvolání o ClaimType produzido depois de esta transformação de afirmações. |

Utilize este afirmações de transformação para qualquer cadeia de caracteres com um parâmetro de formato {0}. O exemplo seguinte cria um **userPrincipalName**. Todas as identidades sociais fornecedor perfis técnicos, como `Facebook-OAUTH` chamadas a **CreateUserPrincipalName** para gerar um **userPrincipalName**.   

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

- Afirmações de entrada:
    - **inputClaim**: 5164db16-3eee-4629-bfda-dcc3326790e9
- Parâmetros de entrada:
    - **stringFormat**: cpim_{0}@{RelyingPartyTenantId}
- Afirmações de saída:
  - **outputClaim**: cpim_5164db16-3eee-4629-bfda-dcc3326790e9@b2cdemo.onmicrosoft.com

## <a name="formatstringmultipleclaims"></a>FormatStringMultipleClaims

Formate duas afirmações, de acordo com a cadeia de formato fornecido. Essa transformação utiliza o c# **String. format** método.

| Item | TransformationClaimType | Tipo de Dados | Notas |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim |string | O ClaimType que age como formato de cadeia de caracteres {0} parâmetro. |
| InputClaim | inputClaim | string | O ClaimType que age como formato de cadeia de caracteres {1} parâmetro. |
| InputParameter | stringFormat | string | O formato de cadeia de caracteres, incluindo o {0} e {1} parâmetros. |
| OutputClaim | outputClaim | string | Po vyvolání o ClaimType produzido depois de esta transformação de afirmações. |

Utilize este afirmações de transformação para o formato com dois parâmetros, qualquer cadeia de caracteres {0} e {1}. O exemplo seguinte cria um **displayName** com o formato especificado:

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

- Afirmações de entrada:
    - **inputClaim1**: João
    - **inputClaim2**: Fernando
- Parâmetros de entrada:
    - **stringFormat**: {0} {1}
- Afirmações de saída:
    - **outputClaim**: Joe Fernando

## <a name="getmappedvaluefromlocalizedcollection"></a>GetMappedValueFromLocalizedCollection

Procurar um item de uma afirmação **restrição** coleção.

| Item | TransformationClaimType | Tipo de Dados | Notas |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | mapFromClaim | string | A afirmação que contém o texto a ser pesquisada no **restrictionValueClaim** afirmações com o **restrição** coleção.  |
| OutputClaim | restrictionValueClaim | string | A afirmação que contém o **restrição** coleção. Po vyvolání a transformação de afirmações, o valor desta afirmação contém o valor do item selecionado. |

O exemplo seguinte procura a descrição da mensagem de erro com base na chave de erro. O **responseMsg** afirmação contém uma coleção de mensagens de erro para apresentar ao usuário final ou para ser enviado à entidade confiadora.

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
A transformação de declarações procura o texto do item e retorna seu valor. Se a restrição for localizada utilizando `<LocalizedCollection>`, a transformação de afirmações devolve o valor localizado.

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

- Afirmações de entrada:
    - **mapFromClaim**: B2C_V1_90001
- Afirmações de saída:
    - **restrictionValueClaim**: Não pode iniciar sessão uma vez que é menor.

## <a name="lookupvalue"></a>LookupValue

Procure um valor de afirmação de uma lista de valores com base no valor de afirmação de outro.

| Item | TransformationClaimType | Tipo de Dados | Notas |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputParameterId | string | A afirmação que contém o valor de pesquisa |
| InputParameter | |string | Coleção de inputParameters. |
| InputParameter | errorOnFailedLookup | boolean | Controlar se um erro é retornado quando nenhuma pesquisa correspondente. |
| OutputClaim | inputParameterId | string | Os ClaimTypes que serão produzidos depois de esta transformação de afirmações ser invocado. O valor de ID correspondente. |

O exemplo seguinte procura o nome de domínio em uma das coleções inputParameters. A transformação de declarações procura o nome de domínio no identificador de e retorna seu valor (uma ID da aplicação).

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

- Afirmações de entrada:
    - **inputParameterId**: test.com
- Parâmetros de entrada:
    - **contoso.com**: 13c15f79-8fb1-4e29-a6c9-be0d36ff19f1
    - **microsoft.com**: 0213308f-17cb-4398-b97e-01da7bd4804e
    - **test.com**: c7026f88-4299-4cdb-965d-3f166464b8a9
    - **errorOnFailedLookup**: Falso
- Afirmações de saída:
    - **outputClaim**:  c7026f88-4299-4cdb-965d-3f166464b8a9

## <a name="nullclaim"></a>NullClaim

Limpe o valor de uma afirmação de determinado.

| Item | TransformationClaimType | Tipo de Dados | Notas |
| ---- | ----------------------- | --------- | ----- |
| OutputClaim | claim_to_null | string | A afirmação de seu valor para ser NULL. |

Use essa transformação de afirmação para remover dados desnecessários da matriz de propriedades de afirmações. Então, o cookie de sessão serão menor. O exemplo seguinte remove o valor da `TermsOfService` tipo de afirmação.

```XML
<ClaimsTransformation Id="SetTOSToNull" TransformationMethod="NullClaim">
  <OutputClaims>
  <OutputClaim ClaimTypeReferenceId="TermsOfService" TransformationClaimType="claim_to_null" />
  </OutputClaims>
</ClaimsTransformation>
```

- Afirmações de entrada:
    - **outputClaim**: Bem-vindo à aplicação da Contoso. Se continuar a procurar e utilizar este Web site, aceita cumprir e cumprir os seguintes termos e condições...
- Afirmações de saída:
    - **outputClaim**: NULL

## <a name="parsedomain"></a>ParseDomain

Obtém a parte do domínio de um endereço de e-mail.

| Item | TransformationClaimType | Tipo de Dados | Notas |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | emailAddress | string | ClaimType que contém o endereço de e-mail. |
| OutputClaim | domínio | string | O que é produzido depois de esta transformação de afirmações ClaimType ser invocado - o domínio. |

Utilize este afirmações de transformação para analisar o nome de domínio após o símbolo do utilizador @. Isso pode ser útil para remover informações de identificação pessoal (PII) de dados de auditoria. A transformação de declarações seguinte demonstra como analisar o nome de domínio de um **e-mail** de afirmação.

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

- Afirmações de entrada:
  - **endereço de correio eletrónico**: joe@outlook.com
- Afirmações de saída:
    - **domain**: outlook.com

## <a name="setclaimsifstringsareequal"></a>SetClaimsIfStringsAreEqual

Verifica se uma cadeia de caracteres de afirmação e `matchTo` parâmetro de entrada são iguais e conjuntos de afirmações de saída com o valor presente na `stringMatchMsg` e `stringMatchMsgCode` parâmetros de entrada, juntamente com afirmações de saída resultado de comparação, que deve ser definido como `true` ou `false` com base no resultado da comparação.

| Item | TransformationClaimType | Tipo de Dados | Notas |
| ---- | ----------------------- | --------- | ----- |
| inputClaim | inputClaim | string | O tipo de afirmação, que está a ser comparado. |
| InputParameter | matchTo | string | A cadeia de caracteres a ser comparada ao `inputClaim`. |
| InputParameter | stringComparison | string | Valores possíveis: `Ordinal` ou `OrdinalIgnoreCase`. |
| InputParameter | stringMatchMsg | string | Primeiro valor a ser definido se as cadeias de caracteres são iguais. |
| InputParameter | stringMatchMsgCode | string | Segundo valor a ser definido se as cadeias de caracteres são iguais. |
| OutputClaim | outputClaim1 | string | Se as cadeias de caracteres é igual a, esta afirmação de saída contém o valor da `stringMatchMsg` parâmetro de entrada. |
| OutputClaim | outputClaim2 | string | Se as cadeias de caracteres é igual a, esta afirmação de saída contém o valor da `stringMatchMsgCode` parâmetro de entrada. |
| OutputClaim | stringCompareResultClaim | boolean | A saída do resultado de comparação de afirmação de tipo, que deve ser definido como `true` ou `false` com base no resultado da comparação. |

Pode utilizar este afirmações de transformação para verificar se uma afirmação é igual ao valor que especificou. Por exemplo, as seguintes afirmações verificações de transformação, se o valor do **termsOfUseConsentVersion** afirmação é igual a `v1`. Em caso afirmativo, altere o valor para `v2`. 

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

- Afirmações de entrada:
    - **inputClaim**: v1
- Parâmetros de entrada:
    - **matchTo**: V1
    - **stringComparison**: ordinalIgnoreCase 
    - **stringMatchMsg**:  B2C_V1_90005
    - **stringMatchMsgCode**:  Os guias de instruções é atualizado para v2
- Afirmações de saída:
    - **outputClaim1**: B2C_V1_90005
    - **outputClaim2**: Os guias de instruções é atualizado para v2
    - **stringCompareResultClaim**: true

## <a name="setclaimsifstringsmatch"></a>SetClaimsIfStringsMatch

Verifica se uma cadeia de caracteres de afirmação e `matchTo` parâmetro de entrada são iguais e conjuntos de afirmações de saída com o valor presente na `outputClaimIfMatched` parâmetro de entrada, juntamente com afirmações de saída resultado de comparação, que deve ser definido como `true` ou `false` com base no resultado da comparação.

| Item | TransformationClaimType | Tipo de Dados | Notas |
| ---- | ----------------------- | --------- | ----- |
| inputClaim | claimToMatch | string | O tipo de afirmação, que está a ser comparado. |
| InputParameter | matchTo | string | A cadeia a ser comparada ao inputClaim. |
| InputParameter | stringComparison | string | Valores possíveis: `Ordinal` ou `OrdinalIgnoreCase`. |
| InputParameter | outputClaimIfMatched | string | O valor a ser definido se as cadeias de caracteres são iguais. |
| OutputClaim | outputClaim | string | Se as cadeias de caracteres é igual a, esta afirmação de saída contém o valor da `outputClaimIfMatched` parâmetro de entrada. Ou null, se as cadeias de caracteres não correspondem. |
| OutputClaim | stringCompareResultClaim | boolean | A saída do resultado de comparação de afirmação de tipo, que deve ser definido como `true` ou `false` com base no resultado da comparação. |

Por exemplo, as seguintes afirmações verificações de transformação, se o valor de **ageGroup** afirmação é igual a `Minor`. Se Sim, devolver o valor a `B2C_V1_90001`. 

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

- Afirmações de entrada:
    - **claimToMatch**: Menor
- Parâmetros de entrada:
    - **matchTo**: Menor
    - **stringComparison**: ordinalIgnoreCase 
    - **outputClaimIfMatched**:  B2C_V1_90001
- Afirmações de saída:
    - **isMinorResponseCode**: B2C_V1_90001
    - **isMinor**: true

