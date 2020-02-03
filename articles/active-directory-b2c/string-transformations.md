---
title: Exemplos de transformação de reivindicações de cadeias para políticas personalizadas
titleSuffix: Azure AD B2C
description: Os exemplos de transformação de string claims para o quadro de experiência de identidade (IEF) do Azure Ative Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 72b3349e0ad4fd86b91a7a02f70b2bcf1efbc271
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/24/2020
ms.locfileid: "76712846"
---
# <a name="string-claims-transformations"></a>Transformações de sinistros de cordas

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Este artigo fornece exemplos para a utilização das transformações de sinistros de cadeia do quadro de experiência de identidade no Azure Ative Directory B2C (Azure AD B2C). Para mais informações, consulte [ClaimsTransformations](claimstransformations.md).

## <a name="assertstringclaimsareequal"></a>AssertStringClaimsAreEqual

Compare duas reclamações e atire uma exceção se não forem iguais de acordo com a comparação especificada InputClaim1, inputClaim2 e stringCompare.

| Item | TransformationClaimType | Tipo de Dados | Notas |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim1 | Cadeia de caracteres | O tipo de primeira reivindicação, que deve ser comparado. |
| InputClaim | inputClaim2 | Cadeia de caracteres | O segundo tipo de reclamação, que deve ser comparado. |
| InputParameter | stringCom | Cadeia de caracteres | comparação de cordas, um dos valores: Ordinal, OrdinalIgnoreCase. |

A transformação de **afirmações assertstringClaimsAreEqual** é sempre executada a partir de um perfil técnico de [validação](validation-technical-profile.md) que é chamado por um [perfil técnico autoafirmado](self-asserted-technical-profile.md). O **UserMessageIfClaimsTransformationStringsAreNotEqual** autoafirmado de dados de perfil técnico controla a mensagem de erro que é apresentada ao utilizador.

![AssertstringClaimsAreIgual execução](./media/string-transformations/assert-execution.png)

Pode utilizar esta transformação de sinistros para garantir que dois Tipos de Reclamação têm o mesmo valor. Caso contrário, uma mensagem de erro será lançada. O exemplo seguinte verifica se o **strongAutenticaationEmailAddress** ClaimType é igual ao **email** ClaimType. Caso contrário, é lançada uma mensagem de erro.

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


O perfil técnico de validação **login-nonInteractive** chama a **transformação de afirmações De EemailAndStrongAuthenticationEmailToAreEqual.**
```XML
<TechnicalProfile Id="login-NonInteractive">
  ...
  <OutputClaimsTransformations>
    <OutputClaimsTransformation ReferenceId="AssertEmailAndStrongAuthenticationEmailAddressAreEqual" />
  </OutputClaimsTransformations>
</TechnicalProfile>
```

O perfil técnico autoafirmado chama o perfil técnico **login-nonInteractive** de validação.

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
  - **inputClaim1**: someone@contoso.com
  - **inputClaim2**: someone@outlook.com
    - Parâmetros de entrada:
  - **stringComcom :** ordinalIgnoreCase
- Resultado: Erro lançado

## <a name="changecase"></a>ChangeCase

Altera o caso da reclamação fornecida para maiúsculas inferiores ou superiores, dependendo do operador.

| Item | TransformationClaimType | Tipo de Dados | Notas |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim1 | Cadeia de caracteres | O ClaimType que será alterado. |
| InputParameter | toCase | Cadeia de caracteres | Um dos seguintes valores: `LOWER` ou `UPPER`. |
| OutputClaim | outputClaim | Cadeia de caracteres | O ClaimType que é produzido após esta transformação de sinistros foi invocado. |

Utilize esta transformação de reclamação para alterar qualquer string ClaimType para uma caixa inferior ou superior.

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
  - **e-mail**: SomeOne@contoso.com
- Parâmetros de entrada:
    - **toCase**: LOWER
- Declarações de saída:
  - **e-mail**: someone@contoso.com

## <a name="createstringclaim"></a>CreateStringClaim

Cria uma reivindicação de cadeia do parâmetro de entrada fornecido na política.

| Item | TransformationClaimType | Tipo de Dados | Notas |
|----- | ----------------------- | --------- | ----- |
| InputParameter | Valor | Cadeia de caracteres | A corda a ser definida |
| OutputClaim | createdClaim | Cadeia de caracteres | O ClaimType que é produzido após esta transformação de sinistros foi invocado, com o valor especificado no parâmetro de entrada. |

Utilize esta transformação de reclamações para definir um valor de Reclamação de cadeia.

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
    - **valor**: Termos de serviço Contoso...
- Declarações de saída:
    - **criouReclamação**: O ToS ClaimType contém os "termos de serviço Contoso..." valor.

## <a name="compareclaims"></a>CompareClaims

Determine se uma reivindicação de corda é igual a outra. O resultado é um novo Beiporador booleano com um valor de `true` ou `false`.

| Item | TransformationClaimType | Tipo de Dados | Notas |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim1 | Cadeia de caracteres | Primeiro tipo de reclamação, que deve ser comparado. |
| InputClaim | inputClaim2 | Cadeia de caracteres | Segundo tipo de reclamação, que deve ser comparado. |
| InputParameter | operador | Cadeia de caracteres | Valores possíveis: `EQUAL` ou `NOT EQUAL`. |
| InputParameter | ignoreCase | valor booleano | Especifica se esta comparação deve ignorar o caso das cordas que estão a ser comparadas. |
| OutputClaim | outputClaim | valor booleano | O ClaimType que é produzido após esta transformação de sinistros foi invocado. |

Utilize esta transformação de sinistros para verificar se uma reclamação é igual a outra reclamação. Por exemplo, a seguinte transformação de reclamações verifica se o valor da reclamação de **e-mail** é igual à reclamação **Verified.Email.**

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
  - **inputClaim1**: someone@contoso.com
  - **inputClaim2**: someone@outlook.com
- Parâmetros de entrada:
    - **operador**: NÃO IGUAL
    - **ignoraCaso:** verdadeiro
- Declarações de saída:
    - **saídaReivindicação**: verdadeiro

## <a name="compareclaimtovalue"></a>CompareClaimToValue

Determina se um valor de reclamação é igual ao valor do parâmetro de entrada.

| Item | TransformationClaimType | Tipo de Dados | Notas |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim1 | Cadeia de caracteres | O tipo de reclamação, que deve ser comparado. |
| InputParameter | operador | Cadeia de caracteres | Valores possíveis: `EQUAL` ou `NOT EQUAL`. |
| InputParameter | compareTo | Cadeia de caracteres | comparação de cordas, um dos valores: Ordinal, OrdinalIgnoreCase. |
| InputParameter | ignoreCase | valor booleano | Especifica se esta comparação deve ignorar o caso das cordas que estão a ser comparadas. |
| OutputClaim | outputClaim | valor booleano | O ClaimType que é produzido após esta transformação de sinistros foi invocado. |

Pode utilizar esta transformação de sinistros para verificar se uma reclamação é igual a um valor que especificou. Por exemplo, a seguinte transformação de sinistros verifica se o valor da reclamação **termosOfUseConsentVersion** é igual a `v1`.

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
    - **comparação:** V1
    - **operador**: EQUAL
    - **ignoraCaso:** verdadeiro
- Declarações de saída:
    - **saídaReivindicação**: verdadeiro

## <a name="createrandomstring"></a>CreateRandomString

Cria uma cadeia aleatória usando o gerador de números aleatórios. Se o gerador de números aleatórios for de tipo `integer`, opcionalmente pode ser fornecido um parâmetro de semente e um número máximo. Um parâmetro de formato de corda opcional permite que a saída seja formatada utilizando-a, e um parâmetro de base opcional 64 especifica se a saída é base64 codificada randomGeneratorType [guia, inteiro] saídaClaim (String).

| Item | TransformationClaimType | Tipo de Dados | Notas |
| ---- | ----------------------- | --------- | ----- |
| InputParameter | randomGeneratorType | Cadeia de caracteres | Especifica o valor aleatório a gerar, `GUID` (ID único global) ou `INTEGER` (um número). |
| InputParameter | stringFormat | Cadeia de caracteres | [Opcional] Forforme o valor aleatório. |
| InputParameter | base64 | valor booleano | [Opcional] Converta o valor aleatório para base64. Se for aplicado o formato de cadeia, o valor após o formato de cordas é codificado para base64. |
| InputParameter | número máximo | int | [Opcional] Para `INTEGER` apenas generatorType aleatório. Especifique o número máximo. |
| InputParameter | semente  | int | [Opcional] Para `INTEGER` apenas generatorType aleatório. Especifique a semente para o valor aleatório. Nota: a mesma semente produz a mesma sequência de números aleatórios. |
| OutputClaim | outputClaim | Cadeia de caracteres | Os Tipos de Reclamação que serão produzidos após esta transformação de sinistros ter sido invocado. O valor aleatório. |

Seguir o exemplo gera uma identificação única global. Esta transformação de sinistros é usada para criar a UPN aleatória (nome principal do utilizador).

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
    - **saídaSReclamação**: bc8bedd2-aaa3-411e-bdee-2f1810b73dfc

Seguindo o exemplo gera um valor aleatório inteiro entre 0 e 1000. O valor é formatado para OTP_{valor aleatório}.

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
    - **número máximo:** 1000
    - **stringFormat**: OTP_{0}
    - **base64:** falso
- Declarações de saída:
    - **saídaReivindicação**: OTP_853


## <a name="formatstringclaim"></a>FormatStringClaim

Forme uma reclamação de acordo com a cadeia de formato fornecida. Esta transformação C# usa o método `String.Format`.

| Item | TransformationClaimType | Tipo de Dados | Notas |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | InputClaim |Cadeia de caracteres |O ClaimType que funciona como formato de cadeia {0} parâmetro. |
| InputParameter | stringFormat | Cadeia de caracteres | O formato de cordas, incluindo o parâmetro {0}. |
| OutputClaim | outputClaim | Cadeia de caracteres | O ClaimType que é produzido após esta transformação de sinistros foi invocado. |

Utilize esta transformação de reclamações para formatar qualquer corda com um parâmetro {0}. O exemplo seguinte cria um **nome principal**do utilizador . Todos os perfis técnicos do fornecedor de identidade social, como `Facebook-OAUTH` chama o **Nome CreateUserPrincipal** para gerar um **nome principal**de utilizador .

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
    - **stringFormat**: cpim_{0}@{RelyingPartyTenantId}
- Declarações de saída:
  - **saída :** cpim_5164db16-3eee-4629-bfda-dcc3326790e9@b2cdemo.onmicrosoft.com

## <a name="formatstringmultipleclaims"></a>FormatStringMultipleClaims

Formato duas reclamações de acordo com a cadeia de formato fornecida. Esta transformação C# utiliza o método **String.Format.**

| Item | TransformationClaimType | Tipo de Dados | Notas |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | InputClaim |Cadeia de caracteres | O ClaimType que funciona como formato de cadeia {0} parâmetro. |
| InputClaim | InputClaim | Cadeia de caracteres | O ClaimType que funciona como formato de cadeia {1} parâmetro. |
| InputParameter | stringFormat | Cadeia de caracteres | O formato de cordas, incluindo os parâmetros {0} e {1}. |
| OutputClaim | outputClaim | Cadeia de caracteres | O ClaimType que é produzido após esta transformação de sinistros foi invocado. |

Utilize esta transformação de sinistros para formatar qualquer cadeia com dois parâmetros, {0} e {1}. O exemplo seguinte cria um nome de **exibição** com o formato especificado:

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
    - **stringFormat**: {0} {1}
- Declarações de saída:
    - **saídaSClaim**: Joe Fernando

## <a name="getmappedvaluefromlocalizedcollection"></a>GetMappedValueFromLocalizedCollection

Procurando um item de uma coleção de **restrição** de reclamação.

| Item | TransformationClaimType | Tipo de Dados | Notas |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | mapFromClaim | Cadeia de caracteres | A alegação que contém o texto a ser analisado nas **restriçõesReclamações valueClaim** com a coleção **Restrição.**  |
| OutputClaim | restrictionValueClaim | Cadeia de caracteres | A alegação que contém a coleção **Restrição.** Após a invocação da transformação de sinistros, o valor desta reclamação contém o valor do item selecionado. |

O exemplo que se segue analisa a descrição da mensagem de erro com base na chave de erro. A **alegação respostaMsg** contém uma recolha de mensagens de erro para apresentar ao utilizador final ou para ser enviada para a parte que depende.

```XML
<ClaimType Id="responseMsg">
  <DisplayName>Error message: </DisplayName>
  <DataType>string</DataType>
  <UserInputType>Paragraph</UserInputType>
  <Restriction>
    <Enumeration Text="B2C_V1_90001" Value="You cannot sign in because you are a minor" />
    <Enumeration Text="B2C_V1_90002" Value="This action can only be performed by gold members" />
    <Enumeration Text="B2C_V1_90003" Value="You have not been enabled for this operation" />
  </Restriction>
</ClaimType>
```
A transformação de sinistros analisa o texto do item e devolve o seu valor. Se a restrição for localizada utilizando `<LocalizedCollection>`, a transformação de sinistros devolve o valor localizado.

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
    - **restriçõesValueClaim:** Não pode iniciar sessão por ser menor.

## <a name="lookupvalue"></a>LookupValue

Procure um valor de reclamação de uma lista de valores com base no valor de outra reclamação.

| Item | TransformationClaimType | Tipo de Dados | Notas |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputParameterId | Cadeia de caracteres | A alegação que contém o valor de procura |
| InputParameter | |Cadeia de caracteres | Coleção de inputParâmetros. |
| InputParameter | errorOnFailedLookup | valor booleano | Controlando se um erro é devolvido quando não há procuração correspondente. |
| OutputClaim | inputParameterId | Cadeia de caracteres | Os Tipos de Reclamação que serão produzidos após esta transformação de sinistros ter sido invocado. O valor do id correspondente. |

O exemplo seguinte procura o nome de domínio numa das coleções inputParameters. A transformação de sinistros procura o nome de domínio no identificador e devolve o seu valor (um ID de aplicação).

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
    - **entradaParameterId**: test.com
- Parâmetros de entrada:
    - **contoso.com**: 13c15f79-8fb1-4e29-a6c9-be0d36ff19f1
    - **microsoft.com**: 0213308f-17cb-4398-b97e-01da7bd4804e
    - **test.com**: c7026f88-4299-4cdb-965d-3f16644b8a9
    - **errorOnFailedLookup**: falso
- Declarações de saída:
    - **saídaSReclamação**: c7026f88-4299-4cdb-965d-3f16644b8a9

## <a name="nullclaim"></a>NullClaim

Limpe o valor de uma determinada reclamação.

| Item | TransformationClaimType | Tipo de Dados | Notas |
| ---- | ----------------------- | --------- | ----- |
| OutputClaim | claim_to_null | Cadeia de caracteres | A alegação do seu valor é NU. |

Utilize esta transformação de reclamação para remover dados desnecessários do saco de propriedade sinistros. Então, o cookie da sessão será menor. O exemplo seguinte remove o valor do tipo de reclamação `TermsOfService`.

```XML
<ClaimsTransformation Id="SetTOSToNull" TransformationMethod="NullClaim">
  <OutputClaims>
  <OutputClaim ClaimTypeReferenceId="TermsOfService" TransformationClaimType="claim_to_null" />
  </OutputClaims>
</ClaimsTransformation>
```

- Declarações de entrada:
    - **saídaReivindicação**: Bem-vindo à App Contoso. Se continuar a navegar e a utilizar este website, está a concordar em cumprir e ficar vinculado pelos seguintes termos e condições...
- Declarações de saída:
    - **saídaReclamação**: NULO

## <a name="parsedomain"></a>ParseDomain

Obtém a parte de domínio de um endereço de e-mail.

| Item | TransformationClaimType | Tipo de Dados | Notas |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | emailAddress | Cadeia de caracteres | O ClaimType que contém o endereço de e-mail. |
| OutputClaim | domínio | Cadeia de caracteres | O ClaimType que é produzido após esta transformação de sinistros foi invocado - o domínio. |

Utilize esta transformação de reclamações para analisar o nome de domínio após o símbolo @ do utilizador. Isto pode ser útil na remoção de informações pessoalmente identificáveis (PII) de dados de auditoria. A transformação de reclamações seguinte demonstra como analisar o nome de domínio a partir de uma reclamação de **e-mail.**

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
  - **e-mailEndereço**: joe@outlook.com
- Declarações de saída:
    - **domínio**: outlook.com

## <a name="setclaimsifstringsareequal"></a>SetClaimsIfStringsAreEqual

Verifica se uma reclamação de cadeias e `matchTo` parâmetro de entrada são iguais e define os pedidos de saída com o valor presente nos parâmetros de entrada de `stringMatchMsg` e `stringMatchMsgCode`, juntamente com a alegação de saída de resultados comparada, que deve ser definida como `true` ou `false` com base no resultado da comparação.

| Item | TransformationClaimType | Tipo de Dados | Notas |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | InputClaim | Cadeia de caracteres | O tipo de reclamação, que deve ser comparado. |
| InputParameter | matchTo | Cadeia de caracteres | A corda a ser comparada com `inputClaim`. |
| InputParameter | stringCom | Cadeia de caracteres | Valores possíveis: `Ordinal` ou `OrdinalIgnoreCase`. |
| InputParameter | stringMatchMsg | Cadeia de caracteres | Primeiro valor a ser definido se as cordas forem iguais. |
| InputParameter | stringMatchMsgCode | Cadeia de caracteres | Segundo valor a definir se as cordas forem iguais. |
| OutputClaim | outputClaim1 | Cadeia de caracteres | Se as cordas forem iguais, esta alegação de saída contém o valor de `stringMatchMsg` parâmetro de entrada. |
| OutputClaim | outputClaim2 | Cadeia de caracteres | Se as cordas forem iguais, esta alegação de saída contém o valor de `stringMatchMsgCode` parâmetro de entrada. |
| OutputClaim | stringCompareResultClaim | valor booleano | O tipo de reclamação de resultado sumido, que deve ser definido como `true` ou `false` com base no resultado da comparação. |

Pode utilizar esta transformação de reclamações para verificar se uma reclamação é igual ao valor que especifica. Por exemplo, a seguinte transformação de sinistros verifica se o valor da reclamação **termosOfUseConsentVersion** é igual a `v1`. Se sim, mude o valor para `v2`.

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
    - **stringComcom :** ordinalIgnoreCase
    - **stringMatchMsg**: B2C_V1_90005
    - **stringMatchMsgCode**: O TOS é atualizado para v2
- Declarações de saída:
    - **saídaClaim1**: B2C_V1_90005
    - **saídaClaim2**: O TOS é atualizado para v2
    - **stringCompareResultClaim**: verdadeiro

## <a name="setclaimsifstringsmatch"></a>SetClaimsIfStringsMatch

Verifica se uma reclamação de cadeia e `matchTo` parâmetro de entrada são iguais e define as reclamações de saída com o valor presente no parâmetro de entrada `outputClaimIfMatched`, juntamente com a alegação de saída de resultados comparada, que deve ser definida como `true` ou `false` com base no resultado da comparação.

| Item | TransformationClaimType | Tipo de Dados | Notas |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | reivindicaçãoToMatch | Cadeia de caracteres | O tipo de reclamação, que deve ser comparado. |
| InputParameter | matchTo | Cadeia de caracteres | A corda a ser comparada com a inputClaim. |
| InputParameter | stringCom | Cadeia de caracteres | Valores possíveis: `Ordinal` ou `OrdinalIgnoreCase`. |
| InputParameter | outputClaimIfMatched | Cadeia de caracteres | O valor a definir se as cordas forem iguais. |
| OutputClaim | outputClaim | Cadeia de caracteres | Se as cordas forem iguais, esta alegação de saída contém o valor de `outputClaimIfMatched` parâmetro de entrada. Ou nulo, se as cordas não estiverem à altura. |
| OutputClaim | stringCompareResultClaim | valor booleano | O tipo de reclamação de resultado sumido, que deve ser definido como `true` ou `false` com base no resultado da comparação. |

Por exemplo, verifica-se as seguintes verificações de transformação de sinistros se o valor da reivindicação do **AgeGroup** for igual a `Minor`. Se sim, devolva o valor a `B2C_V1_90001`.

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
    - **reivindicaçõesToMatch**: Minor
- Parâmetros de entrada:
    - **matchTo**: Menor
    - **stringComcom :** ordinalIgnoreCase
    - **outputClaimIfMatched**: B2C_V1_90001
- Declarações de saída:
    - **isMinorResponseCode**: B2C_V1_90001
    - **isMinor**: verdadeiro

