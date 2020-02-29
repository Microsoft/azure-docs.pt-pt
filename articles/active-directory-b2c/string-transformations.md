---
title: Exemplos de transformação de reivindicações de cadeias para políticas personalizadas
titleSuffix: Azure AD B2C
description: Os exemplos de transformação de string claims para o quadro de experiência de identidade (IEF) do Azure Ative Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 02/24/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 678385d9ed16a9821fc61be476e7eb9eaf6fd4f1
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/29/2020
ms.locfileid: "78183708"
---
# <a name="string-claims-transformations"></a>Transformações de sinistros de cordas

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Este artigo fornece exemplos para a utilização das transformações de sinistros de cadeia do quadro de experiência de identidade no Azure Ative Directory B2C (Azure AD B2C). Para mais informações, consulte [ClaimsTransformations](claimstransformations.md).

## <a name="assertstringclaimsareequal"></a>AssertStringClaimsAreEqual

Compare duas reclamações e atire uma exceção se não forem iguais de acordo com a comparação especificada InputClaim1, inputClaim2 e stringCompare.

| Item | TransformationClaimType | Tipo de Dados | Notas |
| ---- | ----------------------- | --------- | ----- |
| inputClaim | inputClaim1 | string | O tipo de primeira reivindicação, que deve ser comparado. |
| inputClaim | inputClaim2 | string | O segundo tipo de reclamação, que deve ser comparado. |
| EntradaParametro | stringCom | string | comparação de cordas, um dos valores: Ordinal, OrdinalIgnoreCase. |

A transformação de reivindicações **AssertStringClaimsAreEqual** é sempre executada a partir de um perfil técnico de [validação](validation-technical-profile.md) que é chamado por um [perfil técnico autoafirmado](self-asserted-technical-profile.md), ou um [DisplayConrtol](display-controls.md). A `UserMessageIfClaimsTransformationStringsAreNotEqual` metadados de um perfil técnico autoafirmado controla a mensagem de erro que é apresentada ao utilizador.


![AssertstringClaimsAreIgual execução](./media/string-transformations/assert-execution.png)

Pode utilizar esta transformação de sinistros para garantir que dois Tipos de Reclamação têm o mesmo valor. Caso contrário, é lançada uma mensagem de erro. O exemplo seguinte verifica se o **strongAutenticaationEmailAddress** ClaimType é igual ao **email** ClaimType. Caso contrário, é lançada uma mensagem de erro.

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

- Créditos de entrada:
  - **inputClaim1**: someone@contoso.com
  - **inputClaim2**: someone@outlook.com
- Parâmetros de entrada:
  - **stringComcom :** ordinalIgnoreCase
- Resultado: Erro lançado

## <a name="changecase"></a>ChangeCase

Altera o caso da reclamação fornecida para maiúsculas inferiores ou superiores, dependendo do operador.

| Item | TransformationClaimType | Tipo de Dados | Notas |
| ---- | ----------------------- | --------- | ----- |
| inputClaim | inputClaim1 | string | O ClaimType a ser alterado. |
| EntradaParametro | toCase | string | Um dos seguintes valores: `LOWER` ou `UPPER`. |
| OutputClaim | outputClaim | string | O ClaimType que é produzido após esta transformação de sinistros foi invocado. |

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

- Créditos de entrada:
  - **e-mail**: SomeOne@contoso.com
- Parâmetros de entrada:
    - **toCase**: LOWER
- Alegações de saída:
  - **e-mail**: someone@contoso.com

## <a name="createstringclaim"></a>CreateStringClaim

Cria uma reivindicação de cadeia do parâmetro de entrada fornecido na transformação.

| Item | TransformationClaimType | Tipo de Dados | Notas |
|----- | ----------------------- | --------- | ----- |
| EntradaParametro | valor | string | A corda a ser definida. Este parâmetro de entrada suporta expressões de transformação de reivindicações de [cadeias.](string-transformations.md#string-claim-transformations-expressions) |
| OutputClaim | createdClaim | string | O ClaimType que é produzido após esta transformação de sinistros foi invocado, com o valor especificado no parâmetro de entrada. |

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
- Alegações de saída:
    - **criouReclamação**: O ToS ClaimType contém os "termos de serviço Contoso..." valor.

## <a name="compareclaims"></a>CompareClaims

Determine se uma reivindicação de corda é igual a outra. O resultado é um novo Beiporador booleano com um valor de `true` ou `false`.

| Item | TransformationClaimType | Tipo de Dados | Notas |
| ---- | ----------------------- | --------- | ----- |
| inputClaim | inputClaim1 | string | Primeiro tipo de reclamação, que deve ser comparado. |
| inputClaim | inputClaim2 | string | Segundo tipo de reclamação, que deve ser comparado. |
| EntradaParametro | operador | string | Valores possíveis: `EQUAL` ou `NOT EQUAL`. |
| EntradaParametro | ignoreCase | boolean | Especifica se esta comparação deve ignorar o caso das cordas que estão a ser comparadas. |
| OutputClaim | outputClaim | boolean | O ClaimType que é produzido após esta transformação de sinistros foi invocado. |

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

- Créditos de entrada:
  - **inputClaim1**: someone@contoso.com
  - **inputClaim2**: someone@outlook.com
- Parâmetros de entrada:
    - **operador**: NÃO IGUAL
    - **ignoraCaso:** verdadeiro
- Alegações de saída:
    - **saídaReivindicação**: verdadeiro

## <a name="compareclaimtovalue"></a>CompareClaimToValue

Determina se um valor de reclamação é igual ao valor do parâmetro de entrada.

| Item | TransformationClaimType | Tipo de Dados | Notas |
| ---- | ----------------------- | --------- | ----- |
| inputClaim | inputClaim1 | string | O tipo de reclamação, que deve ser comparado. |
| EntradaParametro | operador | string | Valores possíveis: `EQUAL` ou `NOT EQUAL`. |
| EntradaParametro | compareTo | string | comparação de cordas, um dos valores: Ordinal, OrdinalIgnoreCase. |
| EntradaParametro | ignoreCase | boolean | Especifica se esta comparação deve ignorar o caso das cordas que estão a ser comparadas. |
| OutputClaim | outputClaim | boolean | O ClaimType que é produzido após esta transformação de sinistros foi invocado. |

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
- Créditos de entrada:
    - **inputClaim1**: v1
- Parâmetros de entrada:
    - **comparação:** V1
    - **operador**: EQUAL
    - **ignoraCaso:** verdadeiro
- Alegações de saída:
    - **saídaReivindicação**: verdadeiro

## <a name="createrandomstring"></a>CreateRandomString

Cria uma cadeia aleatória usando o gerador de números aleatórios. Se o gerador de números aleatórios for de tipo `integer`, opcionalmente pode ser fornecido um parâmetro de semente e um número máximo. Um parâmetro de formato de corda opcional permite que a saída seja formatada utilizando-a, e um parâmetro de base opcional 64 especifica se a saída é base64 codificada randomGeneratorType [guia, inteiro] saídaClaim (String).

| Item | TransformationClaimType | Tipo de Dados | Notas |
| ---- | ----------------------- | --------- | ----- |
| EntradaParametro | randomGeneratorType | string | Especifica o valor aleatório a gerar, `GUID` (ID único global) ou `INTEGER` (um número). |
| EntradaParametro | stringFormat | string | [Opcional] Forforme o valor aleatório. |
| EntradaParametro | base64 | boolean | [Opcional] Converta o valor aleatório para base64. Se for aplicado o formato de cadeia, o valor após o formato de cordas é codificado para base64. |
| EntradaParametro | número máximo | int | [Opcional] Para `INTEGER` apenas generatorType aleatório. Especifique o número máximo. |
| EntradaParametro | semente  | int | [Opcional] Para `INTEGER` apenas generatorType aleatório. Especifique a semente para o valor aleatório. Nota: a mesma semente produz a mesma sequência de números aleatórios. |
| OutputClaim | outputClaim | string | Os Tipos de Reclamação que serão produzidos após esta transformação de sinistros ter sido invocado. O valor aleatório. |

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
- Alegações de saída:
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
- Alegações de saída:
    - **saídaReivindicação**: OTP_853


## <a name="formatstringclaim"></a>FormatStringClaim

Forme uma reclamação de acordo com a cadeia de formato fornecida. Esta transformação C# usa o método `String.Format`.

| Item | TransformationClaimType | Tipo de Dados | Notas |
| ---- | ----------------------- | --------- | ----- |
| inputClaim | inputClaim |string |O ClaimType que funciona como formato de cadeia {0} parâmetro. |
| EntradaParametro | stringFormat | string | O formato de cordas, incluindo o parâmetro {0}. Este parâmetro de entrada suporta expressões de transformação de reivindicações de [cadeias.](string-transformations.md#string-claim-transformations-expressions)  |
| OutputClaim | outputClaim | string | O ClaimType que é produzido após esta transformação de sinistros foi invocado. |

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

- Créditos de entrada:
    - **inputClaim**: 5164db16-3eee-4629-bfda-dcc3326790e9
- Parâmetros de entrada:
    - **stringFormat**: cpim_{0}@{RelyingPartyTenantId}
- Alegações de saída:
  - **saída :** cpim_5164db16-3eee-4629-bfda-dcc3326790e9@b2cdemo.onmicrosoft.com

## <a name="formatstringmultipleclaims"></a>FormatStringMultipleClaims

Formato duas reclamações de acordo com a cadeia de formato fornecida. Esta transformação C# usa o método `String.Format`.

| Item | TransformationClaimType | Tipo de Dados | Notas |
| ---- | ----------------------- | --------- | ----- |
| inputClaim | inputClaim |string | O ClaimType que funciona como formato de cadeia {0} parâmetro. |
| inputClaim | inputClaim | string | O ClaimType que funciona como formato de cadeia {1} parâmetro. |
| EntradaParametro | stringFormat | string | O formato de cordas, incluindo os parâmetros {0} e {1}. Este parâmetro de entrada suporta expressões de transformação de reivindicações de [cadeias.](string-transformations.md#string-claim-transformations-expressions)   |
| OutputClaim | outputClaim | string | O ClaimType que é produzido após esta transformação de sinistros foi invocado. |

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

- Créditos de entrada:
    - **inputClaim1**: Joe
    - **inputClaim2**: Fernando
- Parâmetros de entrada:
    - **stringFormat**: {0} {1}
- Alegações de saída:
    - **saídaSClaim**: Joe Fernando

## <a name="getlocalizedstringstransformation"></a>GetLocalizedStringsTransformation

Cópias localizadas cordas em reivindicações.

| Item | TransformationClaimType | Tipo de Dados | Notas |
| ---- | ----------------------- | --------- | ----- |
| OutputClaim | O nome da corda localizada | string | Lista de tipos de reclamações que são produzidos após esta transformação de sinistros ter sido invocada. |

Para utilizar a transformação de sinistros GetLocalizedStringsTransformation:

1. Defina uma cadeia de [localização](localization.md) e associe-a a um [perfil auto-afirmou-técnico.](self-asserted-technical-profile.md)
1. O `ElementType` do elemento `LocalizedString` deve `GetLocalizedStringsTransformationClaimType`.
1. O `StringId` é um identificador único que define, e usa-o mais tarde na transformação das suas reivindicações.
1. Na transformação de sinistros, especifique a lista de reclamações a definir com a cadeia localizada. O `ClaimTypeReferenceId` é uma referência a um ClaimType já definido na secção ClaimsSchema na política. O `TransformationClaimType` é o nome da cadeia localizada, tal como definido no `StringId` do elemento `LocalizedString`.
1. Num [perfil técnico autoafirmado](self-asserted-technical-profile.md), ou numa entrada de controlo de [visualização](display-controls.md) ou transformação de sinistros de saída, faça uma referência à transformação das suas reclamações.

![GetLocalizedStringsTransformation](./media/string-transformations/get-localized-strings-transformation.png)

O exemplo que se segue procura o assunto do e-mail, o corpo, a sua mensagem de código e a assinatura do e-mail, a partir de cordas localizadas. Estas alegações mais tarde utilizadas por modelo de verificação de e-mail personalizado.

Defina cordas localizadas para inglês (padrão) e espanhol.

```XML
<Localization Enabled="true">
  <SupportedLanguages DefaultLanguage="en" MergeBehavior="Append">
    <SupportedLanguage>en</SupportedLanguage>
    <SupportedLanguage>es</SupportedLanguage>
   </SupportedLanguages>

  <LocalizedResources Id="api.localaccountsignup.en">
    <LocalizedStrings>
      <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_subject">Contoso account email verification code</LocalizedString>
      <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_message">Thanks for verifying your account!</LocalizedString>
      <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_code">Your code is</LocalizedString>
      <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_signature">Sincerely</LocalizedString>
     </LocalizedStrings>
   </LocalizedResources>
   <LocalizedResources Id="api.localaccountsignup.es">
     <LocalizedStrings>
      <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_subject">Código de verificación del correo electrónico de la cuenta de Contoso</LocalizedString>
      <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_message">Gracias por comprobar la cuenta de </LocalizedString>
      <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_code">Su código es</LocalizedString>
      <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_signature">Atentamente</LocalizedString>
    </LocalizedStrings>
  </LocalizedResources>
</Localization>
```

A transformação de sinistros define o valor do *sujeito* do tipo de reclamação com o valor do *`StringId` email_subject*.

```XML
<ClaimsTransformation Id="GetLocalizedStringsForEmail" TransformationMethod="GetLocalizedStringsTransformation">
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="subject" TransformationClaimType="email_subject" />
    <OutputClaim ClaimTypeReferenceId="message" TransformationClaimType="email_message" />
    <OutputClaim ClaimTypeReferenceId="codeIntro" TransformationClaimType="email_code" />
    <OutputClaim ClaimTypeReferenceId="signature" TransformationClaimType="email_signature" />
   </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Exemplo

- Alegações de saída:
  - **assunto**: Código de verificação de email de conta Contoso
  - **mensagem**: Obrigado por verificar a sua conta!
  - **codeIntro**: O seu código é
  - **assinatura**: Sinceramente


## <a name="getmappedvaluefromlocalizedcollection"></a>GetMappedValueFromLocalizedCollection

Procurando um item de uma coleção de **restrição** de reclamação.

| Item | TransformationClaimType | Tipo de Dados | Notas |
| ---- | ----------------------- | --------- | ----- |
| inputClaim | mapFromClaim | string | A alegação que contém o texto a ser analisado nas **restriçõesReclamações valueClaim** com a coleção **Restrição.**  |
| OutputClaim | restrictionValueClaim | string | A alegação que contém a coleção **Restrição.** Após a invocação da transformação de sinistros, o valor desta reclamação contém o valor do item selecionado. |

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

- Créditos de entrada:
    - **mapFromClaim**: B2C_V1_90001
- Alegações de saída:
    - **restriçõesValueClaim:** Não pode iniciar sessão por ser menor.

## <a name="lookupvalue"></a>LookupValue

Procure um valor de reclamação de uma lista de valores com base no valor de outra reclamação.

| Item | TransformationClaimType | Tipo de Dados | Notas |
| ---- | ----------------------- | --------- | ----- |
| inputClaim | inputParameterId | string | A alegação que contém o valor de procura |
| EntradaParametro | |string | Coleção de inputParâmetros. |
| EntradaParametro | errorOnFailedLookup | boolean | Controlando se um erro é devolvido quando não há procuração correspondente. |
| OutputClaim | inputParameterId | string | Os Tipos de Reclamação que serão produzidos após esta transformação de sinistros ter sido invocado. O valor da `Id`correspondente. |

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

- Créditos de entrada:
    - **entradaParameterId**: test.com
- Parâmetros de entrada:
    - **contoso.com**: 13c15f79-8fb1-4e29-a6c9-be0d36ff19f1
    - **microsoft.com**: 0213308f-17cb-4398-b97e-01da7bd4804e
    - **test.com**: c7026f88-4299-4cdb-965d-3f16644b8a9
    - **errorOnFailedLookup**: falso
- Alegações de saída:
    - **saídaSReclamação**: c7026f88-4299-4cdb-965d-3f16644b8a9

Quando `errorOnFailedLookup` parâmetro de entrada está definido para `true`, a transformação de reivindicações **LookupValue** é sempre executada a partir de um perfil técnico de [validação](validation-technical-profile.md) que é chamado por um [perfil técnico autoafirmado](self-asserted-technical-profile.md), ou um [DisplayConrtol](display-controls.md). A `LookupNotFound` metadados de um perfil técnico autoafirmado controla a mensagem de erro que é apresentada ao utilizador.

![AssertstringClaimsAreIgual execução](./media/string-transformations/assert-execution.png)

O exemplo seguinte procura o nome de domínio numa das coleções inputParameters. A transformação de sinistros procura o nome de domínio no identificador e devolve o seu valor (um ID de aplicação), ou levanta uma mensagem de erro.

```XML
 <ClaimsTransformation Id="DomainToClientId" TransformationMethod="LookupValue">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="domainName" TransformationClaimType="inputParameterId" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="contoso.com" DataType="string" Value="13c15f79-8fb1-4e29-a6c9-be0d36ff19f1" />
    <InputParameter Id="microsoft.com" DataType="string" Value="0213308f-17cb-4398-b97e-01da7bd4804e" />
    <InputParameter Id="test.com" DataType="string" Value="c7026f88-4299-4cdb-965d-3f166464b8a9" />
    <InputParameter Id="errorOnFailedLookup" DataType="boolean" Value="true" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="domainAppId" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Exemplo

- Créditos de entrada:
    - **entradaParameterId**: live.com
- Parâmetros de entrada:
    - **contoso.com**: 13c15f79-8fb1-4e29-a6c9-be0d36ff19f1
    - **microsoft.com**: 0213308f-17cb-4398-b97e-01da7bd4804e
    - **test.com**: c7026f88-4299-4cdb-965d-3f16644b8a9
    - **errorOnFailedLookup**: verdadeiro
- Erro:
    - Nenhuma correspondência encontrada para o valor de reclamação de entrada na lista de ids de parâmetros de entrada e erroOnFailedLookup é verdadeira.


## <a name="nullclaim"></a>NullClaim

Limpe o valor de uma determinada reclamação.

| Item | TransformationClaimType | Tipo de Dados | Notas |
| ---- | ----------------------- | --------- | ----- |
| OutputClaim | claim_to_null | string | O valor da reclamação está definido para NULO. |

Utilize esta transformação de reclamação para remover dados desnecessários do saco de propriedade sinistros para que o cookie da sessão seja menor. O exemplo seguinte remove o valor do tipo de reclamação `TermsOfService`.

```XML
<ClaimsTransformation Id="SetTOSToNull" TransformationMethod="NullClaim">
  <OutputClaims>
  <OutputClaim ClaimTypeReferenceId="TermsOfService" TransformationClaimType="claim_to_null" />
  </OutputClaims>
</ClaimsTransformation>
```

- Créditos de entrada:
    - **saídaReivindicação**: Bem-vindo à App Contoso. Se continuar a navegar e a utilizar este website, está a concordar em cumprir e ficar vinculado pelos seguintes termos e condições...
- Alegações de saída:
    - **saídaReclamação**: NULO

## <a name="parsedomain"></a>ParseDomain

Obtém a parte de domínio de um endereço de e-mail.

| Item | TransformationClaimType | Tipo de Dados | Notas |
| ---- | ----------------------- | --------- | ----- |
| inputClaim | emailAddress | string | O ClaimType que contém o endereço de e-mail. |
| OutputClaim | domínio | string | O ClaimType que é produzido após esta transformação de sinistros foi invocado - o domínio. |

Utilize esta transformação de reclamações para analisar o nome de domínio após o símbolo @ do utilizador. A transformação de reclamações seguinte demonstra como analisar o nome de domínio a partir de uma reclamação de **e-mail.**

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

- Créditos de entrada:
  - **e-mailEndereço**: joe@outlook.com
- Alegações de saída:
    - **domínio**: outlook.com

## <a name="setclaimsifregexmatch"></a>SetClaimsIfRegexMatch

Verifica se uma reclamação de cadeia sou `claimToMatch` e `matchTo` parâmetro de entrada são iguais e define os pedidos de saída com o valor presente no parâmetro de entrada `outputClaimIfMatched`, juntamente com a alegação de saída de resultados comparada, que deve ser definida como `true` ou `false` com base no resultado da comparação.

| Item | TransformationClaimType | Tipo de Dados | Notas |
| ---- | ----------------------- | --------- | ----- |
| inputClaim | reivindicaçãoToMatch | string | O tipo de reclamação, que deve ser comparado. |
| EntradaParametro | matchTo | string | A expressão regular a condizer. |
| EntradaParametro | outputClaimIfMatched | string | O valor a definir se as cordas forem iguais. |
| OutputClaim | outputClaim | string | Se a expressão regular for compatível, esta alegação de saída contém o valor do parâmetro de entrada `outputClaimIfMatched`. Ou nulo, se não corresponder. |
| OutputClaim | regexCompareResultClaim | boolean | O tipo de resultado de correspondência de expressão regular, que deve ser definido como `true` ou `false` com base no resultado da correspondência. |

Por exemplo, verifica se o número de telefone fornecido é válido, com base no padrão de expressão regular do número de telefone.

```XML
<ClaimsTransformation Id="SetIsPhoneRegex" TransformationMethod="setClaimsIfRegexMatch">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="phone" TransformationClaimType="claimToMatch" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="matchTo" DataType="string" Value="^[0-9]{4,16}$" />
    <InputParameter Id="outputClaimIfMatched" DataType="string" Value="isPhone" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="validationResult" TransformationClaimType="outputClaim" />
    <OutputClaim ClaimTypeReferenceId="isPhoneBoolean" TransformationClaimType="regexCompareResultClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Exemplo

- Créditos de entrada:
    - **reivindicaçõesToMatch:** "64854114520"
- Parâmetros de entrada:
    - **matchTo**: "^[0-9]{4,16}$"
    - **outputClaimIfMatched**: "isPhone"
- Alegações de saída:
    - **outputClaim**: "isPhone"
    - **regexCompareResultClaim**: verdadeiro

## <a name="setclaimsifstringsareequal"></a>SetClaimsIfStringsAreEqual

Verifica se uma reclamação de cadeias e `matchTo` parâmetro de entrada são iguais e define os pedidos de saída com o valor presente nos parâmetros de entrada de `stringMatchMsg` e `stringMatchMsgCode`, juntamente com a alegação de saída de resultados comparada, que deve ser definida como `true` ou `false` com base no resultado da comparação.

| Item | TransformationClaimType | Tipo de Dados | Notas |
| ---- | ----------------------- | --------- | ----- |
| inputClaim | inputClaim | string | O tipo de reclamação, que deve ser comparado. |
| EntradaParametro | matchTo | string | A corda a ser comparada com `inputClaim`. |
| EntradaParametro | stringCom | string | Valores possíveis: `Ordinal` ou `OrdinalIgnoreCase`. |
| EntradaParametro | stringMatchMsg | string | Primeiro valor a ser definido se as cordas forem iguais. |
| EntradaParametro | stringMatchMsgCode | string | Segundo valor a definir se as cordas forem iguais. |
| OutputClaim | outputClaim1 | string | Se as cordas forem iguais, esta alegação de saída contém o valor de `stringMatchMsg` parâmetro de entrada. |
| OutputClaim | outputClaim2 | string | Se as cordas forem iguais, esta alegação de saída contém o valor de `stringMatchMsgCode` parâmetro de entrada. |
| OutputClaim | stringCompareResultClaim | boolean | O tipo de reclamação de resultado sumido, que deve ser definido como `true` ou `false` com base no resultado da comparação. |

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

- Créditos de entrada:
    - **inputClaim**: v1
- Parâmetros de entrada:
    - **matchTo**: V1
    - **stringComcom :** ordinalIgnoreCase
    - **stringMatchMsg**: B2C_V1_90005
    - **stringMatchMsgCode**: O TOS é atualizado para v2
- Alegações de saída:
    - **saídaClaim1**: B2C_V1_90005
    - **saídaClaim2**: O TOS é atualizado para v2
    - **stringCompareResultClaim**: verdadeiro

## <a name="setclaimsifstringsmatch"></a>SetClaimsIfStringsMatch

Verifica se uma reclamação de cadeia e `matchTo` parâmetro de entrada são iguais e define as reclamações de saída com o valor presente no parâmetro de entrada `outputClaimIfMatched`, juntamente com a alegação de saída de resultados comparada, que deve ser definida como `true` ou `false` com base no resultado da comparação.

| Item | TransformationClaimType | Tipo de Dados | Notas |
| ---- | ----------------------- | --------- | ----- |
| inputClaim | reivindicaçãoToMatch | string | O tipo de reclamação, que deve ser comparado. |
| EntradaParametro | matchTo | string | A corda a ser comparada com a inputClaim. |
| EntradaParametro | stringCom | string | Valores possíveis: `Ordinal` ou `OrdinalIgnoreCase`. |
| EntradaParametro | outputClaimIfMatched | string | O valor a definir se as cordas forem iguais. |
| OutputClaim | outputClaim | string | Se as cordas forem iguais, esta alegação de saída contém o valor de `outputClaimIfMatched` parâmetro de entrada. Ou nulo, se as cordas não estiverem à altura. |
| OutputClaim | stringCompareResultClaim | boolean | O tipo de reclamação de resultado sumido, que deve ser definido como `true` ou `false` com base no resultado da comparação. |

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

- Créditos de entrada:
    - **reivindicaçõesToMatch**: Minor
- Parâmetros de entrada:
    - **matchTo**: Menor
    - **stringComcom :** ordinalIgnoreCase
    - **outputClaimIfMatched**: B2C_V1_90001
- Alegações de saída:
    - **isMinorResponseCode**: B2C_V1_90001
    - **isMinor**: verdadeiro


## <a name="stringcontains"></a>StringContains

Determine se ocorre uma subcadeia especificada dentro da alegação de entrada. O resultado é um novo Beiporador booleano com um valor de `true` ou `false`. `true` se o parâmetro de valor ocorrer dentro desta corda, caso contrário, `false`.

| Item | TransformationClaimType | Tipo de Dados | Notas |
| ---- | ----------------------- | --------- | ----- |
| inputClaim | inputClaim | string | O tipo de reclamação, que deve ser revistado. |
|EntradaParametro|Contém|string|O valor para procurar.|
|EntradaParametro|ignoreCase|string|Especifica se esta comparação deve ignorar o caso da cadeia que está a ser comparada.|
| OutputClaim | outputClaim | string | O ClaimType que é produzido após esta Transformação de Reclamações foi invocado. Um indicador booleano se o substring ocorrer dentro da alegação de entrada. |

Utilize esta transformação de reclamações para verificar se um tipo de reclamação de cordas contém um substring. Seguindo o exemplo, verifica se o tipo de reclamação de cadeias `roles` contém o valor da **administração**.

```XML
<ClaimsTransformation Id="CheckIsAdmin" TransformationMethod="StringContains">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="roles" TransformationClaimType="inputClaim"/>
  </InputClaims>
  <InputParameters>
    <InputParameter  Id="contains" DataType="string" Value="admin"/>
    <InputParameter  Id="ignoreCase" DataType="string" Value="true"/>
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="isAdmin" TransformationClaimType="outputClaim"/>
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Exemplo

- Créditos de entrada:
    - **inputClaim**: "Administrador, Aprovador, Editor"
- Parâmetros de entrada:
    - **contém**: "administrador",
    - **ignoraCaso:** verdadeiro
- Alegações de saída:
    - **saídaReivindicação**: verdadeiro

## <a name="stringsubstring"></a>StringSubstring

Extrai partes de um tipo de reclamação de cordas, começando pelo personagem na posição especificada, e devolve o número especificado de caracteres.

| Item | TransformationClaimType | Tipo de Dados | Notas |
| ---- | ----------------------- | --------- | ----- |
| inputClaim | inputClaim | string | O tipo de reclamação, que contém a corda. |
| EntradaParametro | startIndex | int | A posição de personagem inicial de uma subcorda baseada em zero neste caso. |
| EntradaParametro | length | int | O número de caracteres na subcadeia. |
| OutputClaim | outputClaim | boolean | Uma cadeia equivalente à subcadeia de comprimento que começa no inícioIndex neste caso, ou Empty se o startIndex for igual ao comprimento desta instância e comprimento é zero. |

Por exemplo, obtenha o prefixo do país do número de telefone.


```XML
<ClaimsTransformation Id="GetPhonePrefix" TransformationMethod="StringSubstring">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="phoneNumber" TransformationClaimType="inputClaim" />
  </InputClaims>
<InputParameters>
  <InputParameter Id="startIndex" DataType="int" Value="0" />
  <InputParameter Id="length" DataType="int" Value="2" />
</InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="phonePrefix" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```
### <a name="example"></a>Exemplo

- Créditos de entrada:
    - **inputClaim:** "+1644114520"
- Parâmetros de entrada:
    - **inícioÍndice**: 0
    - **comprimento**: 2
- Alegações de saída:
    - **saídaSClaim**: "+1"

## <a name="stringreplace"></a>StringReplace

Procura uma cadeia de tipo de reclamação por um valor especificado e devolve uma nova cadeia de tipo de reclamação na qual todas as ocorrências de uma corda especificada na cadeia de corrente são substituídas por outra corda especificada.

| Item | TransformationClaimType | Tipo de Dados | Notas |
| ---- | ----------------------- | --------- | ----- |
| inputClaim | inputClaim | string | O tipo de reclamação, que contém a corda. |
| EntradaParametro | oldValue | string | A corda a ser revistada. |
| EntradaParametro | newValue | string | A corda para substituir todas as ocorrências de `oldValue` |
| OutputClaim | outputClaim | boolean | Uma cadeia equivalente à cadeia atual, exceto que todos os casos de oldValue são substituídos por newValue. Se o oldValue não for encontrado na instância atual, o método devolve a instância atual inalterada. |

Por exemplo, normalizar um número de telefone, removendo os caracteres `-`


```XML
<ClaimsTransformation Id="NormalizePhoneNumber" TransformationMethod="StringReplace">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="phoneNumber" TransformationClaimType="inputClaim" />
  </InputClaims>
<InputParameters>
  <InputParameter Id="oldValue" DataType="string" Value="-" />
  <InputParameter Id="newValue" DataType="string" Value="" />
</InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="phoneNumber" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```
### <a name="example"></a>Exemplo

- Créditos de entrada:
    - **inputClaim:** "+164-411-452-054"
- Parâmetros de entrada:
    - **oldValue**: "-"
    - **comprimento**: ""
- Alegações de saída:
    - **saídaSReclamação:** "+164411452054"

## <a name="stringjoin"></a>StringJoin

Concatena os elementos de um tipo de reivindicação de recolha de cordas especificado, utilizando o separador especificado entre cada elemento ou membro.

| Item | TransformationClaimType | Tipo de Dados | Notas |
| ---- | ----------------------- | --------- | ----- |
| inputClaim | inputClaim | stringCollection | Uma coleção que contém as cordas para concatenar. |
| EntradaParametro | delimitador | string | A corda para usar como separador, como vírpara `,`. |
| OutputClaim | outputClaim | string | Uma cadeia que consiste nos membros da coleção de cordas `inputClaim`, delimitada pelo parâmetro de entrada `delimiter`. |

O exemplo seguinte pega numa coleção de cordas de funções de utilizador e converte-a numa cadeia de delimitador de vírposta. Pode utilizar este método para armazenar uma coleção de cordas na conta de utilizador da AD Azure. Mais tarde, quando ler a conta do diretório, use o `StringSplit` para converter a corda de limitador da vírce de volta à coleção de cordas.

```XML
<ClaimsTransformation Id="ConvertRolesStringCollectionToCommaDelimiterString" TransformationMethod="StringJoin">
  <InputClaims>
   <InputClaim ClaimTypeReferenceId="roles" TransformationClaimType="inputClaim" />
  </InputClaims>
  <InputParameters>
    <InputParameter DataType="string" Id="delimiter" Value="," />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="rolesCommaDelimiterConverted" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Exemplo

- Créditos de entrada:
  - **inputClaim**: [ "Administrador", "Autor", "Reader" ]
- Parâmetros de entrada:
  - **delimitador**: ""
- Alegações de saída:
  - **saídaSClaim**: "Administrador, Autor, Leitor"


## <a name="stringsplit"></a>StringSplit

Devolve uma cadeia de cordas que contém as subcordas neste caso que são delimitadas por elementos de uma corda especificada.

| Item | TransformationClaimType | Tipo de Dados | Notas |
| ---- | ----------------------- | --------- | ----- |
| inputClaim | inputClaim | string | Um tipo de reivindicação de cordas que contém as cadeias sub para dividir. |
| EntradaParametro | delimitador | string | A corda para usar como separador, como vírpara `,`. |
| OutputClaim | outputClaim | stringCollection | Uma coleção de cordas cujos elementos contêm as subcordas desta cadeia que são delimitadas pelo parâmetro de entrada `delimiter`. |

O exemplo seguinte pega numa cadeia de funções de utilizador de comma e converte-a numa coleção de cordas.

```XML
<ClaimsTransformation Id="ConvertRolesToStringCollection" TransformationMethod="StringSplit">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="rolesCommaDelimiter" TransformationClaimType="inputClaim" />
  </InputClaims>
  <InputParameters>
  <InputParameter DataType="string" Id="delimiter" Value="," />
    </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="roles" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Exemplo

- Créditos de entrada:
  - **inputClaim**: "Administrador, Autor, Leitor"
- Parâmetros de entrada:
  - **delimitador**: ""
- Alegações de saída:
  - **saídasReivindicação**: [ "Administrador", "Autor", "Leitor" ]

## <a name="string-claim-transformations-expressions"></a>Expressões de transformações de reivindicação de cordas
As expressões de transformações de reivindicação em políticas personalizadas Azure AD B2C fornecem informações de contexto sobre o ID do inquilino e identificação de perfil técnico.

  | Expressão | Descrição | Exemplo |
 | ----- | ----------- | --------|
 | `{TechnicalProfileId}` | O perfil técnico Nome id. | Facebook-OAUTH |
 | `{RelyingPartyTenantId}` | A identificação do inquilino da política do partido. | your-tenant.onmicrosoft.com |
 | `{TrustFrameworkTenantId}` | A identificação do inquilino do quadro fiduciário. | your-tenant.onmicrosoft.com |
