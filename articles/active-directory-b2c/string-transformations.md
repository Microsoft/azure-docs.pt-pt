---
title: String reclama exemplos de transformação para políticas personalizadas
titleSuffix: Azure AD B2C
description: String alega exemplos de transformação para o esquema do Quadro de Experiência de Identidade (IEF) do Azure Ative Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/08/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 85574b7d33af6d9abfe25f5af4d811255f08ce4b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102452242"
---
# <a name="string-claims-transformations"></a>Cadeia reivindica transformações

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Este artigo fornece exemplos para a utilização das transformações de alegações de cadeia do esquema do Quadro de Experiência de Identidade em Azure Ative Directory B2C (Azure AD B2C). Para obter mais informações, consulte [SinistrosTransformações](claimstransformations.md).

## <a name="assertstringclaimsareequal"></a>AssertStringClaimsAreEqual

Compare duas reclamações e atire uma exceção se não forem iguais de acordo com a entrada de comparação especificadaClaim1, inputClaim2 e stringComparison.

| Item | TransformaçãoClaimType | Tipo de Dados | Notas |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim1 | string | O tipo da primeira reivindicação, que deve ser comparado. |
| InputClaim | inputClaim2 | string | O tipo da segunda reivindicação, que deve ser comparado. |
| InputParameter | cordaComparison | string | comparação de cordas, um dos valores: Ordinal, OrdinalIgnoreCase. |

A **assertStringClaimsAreEqual** alega que a transformação é sempre executada a partir de um perfil técnico de [validação](validation-technical-profile.md) que é chamado por um [perfil técnico autoafirmado,](self-asserted-technical-profile.md)ou um [DisplayConrtol](display-controls.md). Os `UserMessageIfClaimsTransformationStringsAreNotEqual` metadados de um perfil técnico autoafirmado controlam a mensagem de erro que é apresentada ao utilizador. As mensagens de erro podem ser [localizadas](localization-string-ids.md#claims-transformations-error-messages).


![Execução assertStringClaimsAreEqual](./media/string-transformations/assert-execution.png)

Pode utilizar esta transformação de sinistros para se certificar de que dois ClaimTypes têm o mesmo valor. Caso contrário, é lançada uma mensagem de erro. O exemplo seguinte verifica que o **strongAuthenticationEmailAddress** ClaimType é igual ao **Email** ClaimType. Caso contrário, é lançada uma mensagem de erro.

```xml
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


O perfil técnico de validação **login-NonInteractive** chama a transformação de alegações **assertEmailAndStrongAuthenticationEmailAddressAreEqual.**
```xml
<TechnicalProfile Id="login-NonInteractive">
  ...
  <OutputClaimsTransformations>
    <OutputClaimsTransformation ReferenceId="AssertEmailAndStrongAuthenticationEmailAddressAreEqual" />
  </OutputClaimsTransformations>
</TechnicalProfile>
```

O perfil técnico autoafirmado chama o perfil técnico **de login-NonInteractive de** validação.

```xml
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

- Reclamações de entrada:
  - **inputClaim1**: someone@contoso.com
  - **inputClaim2**: someone@outlook.com
- Parâmetros de entrada:
  - **stringComparison**: ordinalIgnoreCase
- Resultado: Erro lançado

## <a name="changecase"></a>ChangeCase

Altera o caso do pedido fornecido para maiússão ou maiúsão, dependendo do operador.

| Item | TransformaçãoClaimType | Tipo de Dados | Notas |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim1 | string | O ClaimType a ser alterado. |
| InputParameter | toCase | string | Um dos seguintes valores: `LOWER` ou `UPPER` . |
| OutputClaim | outputClaim | string | O ClaimType que é produzido após esta transformação de alegações foi invocado. |

Utilize esta transformação de reclamação para alterar qualquer string ClaimType para maiússão ou maiúsão.

```xml
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

- Reclamações de entrada:
  - **E-mail:**SomeOne@contoso.com
- Parâmetros de entrada:
    - **toCase**: LOWER
- Reclamações de saída:
  - **E-mail:**someone@contoso.com

## <a name="createstringclaim"></a>CreateStringClaim

Cria uma reivindicação de cadeia a partir do parâmetro de entrada fornecido na transformação.

| Item | TransformaçãoClaimType | Tipo de Dados | Notas |
|----- | ----------------------- | --------- | ----- |
| InputParameter | valor | string | A corda a ser definida. Este parâmetro de entrada suporta [expressões de transformação de alegações de cordas.](string-transformations.md#string-claim-transformations-expressions) |
| OutputClaim | criadoClaim | string | O ClaimType que é produzido após esta transformação de sinistros foi invocado, com o valor especificado no parâmetro de entrada. |

Utilize esta transformação de sinistros para definir um valor de claimType de cadeia.

```xml
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
    - **valor**: Contoso termos de serviço...
- Reclamações de saída:
    - **createdClaim**: O TOS ClaimType contém os "termos de serviço contoso..." valor.

## <a name="copyclaimifpredicatematch"></a>CopyClaimIfPredicateMatch

Copiar o valor de uma reclamação a outra se o valor da reclamação de entrada corresponder ao predicado pedido de saída. 

| Item | TransformaçãoClaimType | Tipo de Dados | Notas |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim | string | O tipo de reclamação, que deve ser copiado. |
| OutputClaim | outputClaim | string | O tipo de reclamação que é produzido após esta transformação de alegações foi invocado. O valor da reclamação de entrada é verificado contra este predicado pedido. |

O exemplo a seguir copia o valor de reclamação signInName para a reclamação do telefoneNumber, apenas se o signInName for um número de telefone. Para obter a amostra completa, consulte o número de telefone ou a política do pacote de arranque [de entrada de e-mail.](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/blob/master/scenarios/phone-number-passwordless/Phone_Email_Base.xml)

```xml
<ClaimsTransformation Id="SetPhoneNumberIfPredicateMatch" TransformationMethod="CopyClaimIfPredicateMatch">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="signInName" TransformationClaimType="inputClaim" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="phoneNumber" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example-1"></a>Exemplo 1

- Reclamações de entrada:
    - **inputClaim:**bob@contoso.com
- Reclamações de saída:
    - **outputClaim**: A reclamação de saída não será alterada do seu valor original.

### <a name="example-2"></a>Exemplo 2

- Reclamações de entrada:
    - **inputClaim**: +11234567890
- Reclamações de saída:
    - **outputClaim**: +11234567890

## <a name="compareclaims"></a>CompareClaims

Determinar se uma alegação de corda é igual a outra. O resultado é um novo Boolean ClaimType com um valor de `true` ou `false` .

| Item | TransformaçãoClaimType | Tipo de Dados | Notas |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim1 | string | Primeiro tipo de reclamação, que deve ser comparado. |
| InputClaim | inputClaim2 | string | Segundo tipo de reclamação, que deve ser comparado. |
| InputParameter | operador | string | Valores possíveis: `EQUAL` ou `NOT EQUAL` . |
| InputParameter | ignoreCase | boolean | Especifica se esta comparação deve ignorar o caso das cordas que estão a ser comparadas. |
| OutputClaim | outputClaim | boolean | O ClaimType que é produzido após esta transformação de alegações foi invocado. |

Utilize esta transformação de sinistros para verificar se uma reclamação é igual a outra reclamação. Por exemplo, a transformação de reclamações seguinte verifica se o valor da reclamação de **e-mail** é igual ao **Verified.Email** reclamação.

```xml
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

- Reclamações de entrada:
  - **inputClaim1**: someone@contoso.com
  - **inputClaim2**: someone@outlook.com
- Parâmetros de entrada:
    - **operador**: NÃO IGUAL
    - **ignoreCase**: verdadeiro
- Reclamações de saída:
    - **outputClaim**: verdadeiro

## <a name="compareclaimtovalue"></a>CompareClaimToValue

Determina se um valor de reclamação é igual ao valor do parâmetro de entrada.

| Item | TransformaçãoClaimType | Tipo de Dados | Notas |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim1 | string | O tipo da reclamação, que deve ser comparado. |
| InputParameter | operador | string | Valores possíveis: `EQUAL` ou `NOT EQUAL` . |
| InputParameter | compararTo | string | comparação de cordas, um dos valores: Ordinal, OrdinalIgnoreCase. |
| InputParameter | ignoreCase | boolean | Especifica se esta comparação deve ignorar o caso das cordas que estão a ser comparadas. |
| OutputClaim | outputClaim | boolean | O ClaimType que é produzido após esta transformação de alegações foi invocado. |

Pode utilizar esta transformação de reclamações para verificar se uma reclamação é igual a um valor especificado. Por exemplo, a transformação de sinistros a seguir verifica se o valor dos **termosOfUseConsentVersion** é igual a `v1` .

```xml
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
- Reclamações de entrada:
    - **inputClaim1**: v1
- Parâmetros de entrada:
    - **comparar:** V1
    - **operador**: EQUAL
    - **ignoreCase**: verdadeiro
- Reclamações de saída:
    - **outputClaim**: verdadeiro

## <a name="createrandomstring"></a>CreateRandomString

Cria uma cadeia aleatória utilizando o gerador de números aleatórios. Se o gerador de números aleatórios for do `integer` tipo, opcionalmente pode ser fornecido um parâmetro de semente e um número máximo. Um parâmetro de formato de corda opcional permite que a saída seja formatada usando-a, e um parâmetro de base opcional 64 especifica se a saída é base64 codificada randomGeneratorType [guid, inteiro] outputClaim (String).

| Item | TransformaçãoClaimType | Tipo de Dados | Notas |
| ---- | ----------------------- | --------- | ----- |
| InputParameter | RandomGeneratorType | string | Especifica o valor aleatório a gerar `GUID` (ID exclusivo global) ou `INTEGER` (um número). |
| InputParameter | stringFormat | string | [Opcional] Formate o valor aleatório. |
| InputParameter | base64 | boolean | [Opcional] Converta o valor aleatório na base64. Se o formato de cadeia for aplicado, o valor após o formato de corda é codificado para base64. |
| InputParameter | número máximo | int | [Opcional] Apenas para `INTEGER` o Tipo deGerador aleatório. Especifique o número máximo. |
| InputParameter | sementes  | int | [Opcional] Apenas para `INTEGER` o Tipo deGerador aleatório. Especifique a semente pelo valor aleatório. Nota: a mesma semente produz a mesma sequência de números aleatórios. |
| OutputClaim | outputClaim | string | Os ClaimTypes que serão produzidos após esta transformação de alegações foi invocado. O valor aleatório. |

Seguir o exemplo gera um ID único global. Esta transformação de sinistros é usada para criar a UPN aleatória (nome de princípio do utilizador).

```xml
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
    - **RandomGeneratorType**: GUID
- Reclamações de saída:
    - **outputClaim**: bc8bedd2-aaa3-411e-bdee-2f1810b73dfc

O exemplo seguinte gera um valor inteiro aleatório entre 0 e 1000. O valor é formatado para OTP_{valor aleatório}.

```xml
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
    - **RandomGeneratorType**: INTEIROGER
    - **número máximo:** 1000
    - **stringFormat**: OTP_{0}
    - **base64**: falso
- Reclamações de saída:
    - **outputClaim**: OTP_853


## <a name="formatlocalizedstring"></a>FormatLocalizedString

Formato múltiplas reclamações de acordo com uma cadeia de formato localizada fornecida. Esta transformação utiliza o `String.Format` método C#.


| Item | TransformaçãoClaimType | Tipo de Dados | Notas |
| ---- | ----------------------- | --------- | ----- |
| InputClaims |  |string | A coleção de alegações de entrada que funciona como formato de {0} corda, {1} {2} parâmetros. |
| InputParameter | stringFormatId | string |  O `StringId` de uma corda [localizada.](localization.md)   |
| OutputClaim | outputClaim | string | O ClaimType que é produzido após esta transformação de alegações foi invocado. |

> [!NOTE]
> O tamanho máximo permitido em formato de corda é de 4000.

Para utilizar a transformação de reclamações FormatLocalizedString:

1. Defina uma [corda de localização](localization.md)e associe-a a um [perfil auto-afirmado-técnico.](self-asserted-technical-profile.md)
1. O `ElementType` elemento deve ser definido para `LocalizedString` `FormatLocalizedStringTransformationClaimType` .
1. É `StringId` um identificador único que define, e usá-lo mais tarde na sua transformação de reivindicações. `stringFormatId`
1. Na transformação de sinistros, especifique a lista de reclamações a definir com a cadeia localizada. Em seguida, coloque `stringFormatId` o elemento de corda `StringId` localizado. 
1. Num [perfil técnico autoafirmado,](self-asserted-technical-profile.md)ou numa entrada de [controlo de ecrã](display-controls.md) ou transformação de reivindicações de resultados, faça uma referência à transformação das suas reclamações.


O exemplo a seguir gera uma mensagem de erro quando uma conta já está no diretório. O exemplo define as cordas localizadas para inglês (padrão) e espanhol.

```xml
<Localization Enabled="true">
  <SupportedLanguages DefaultLanguage="en" MergeBehavior="Append">
    <SupportedLanguage>en</SupportedLanguage>
    <SupportedLanguage>es</SupportedLanguage>
   </SupportedLanguages>

  <LocalizedResources Id="api.localaccountsignup.en">
    <LocalizedStrings>
      <LocalizedString ElementType="FormatLocalizedStringTransformationClaimType" StringId="ResponseMessge_EmailExists">The email '{0}' is already an account in this organization. Click Next to sign in with that account.</LocalizedString>
      </LocalizedStrings>
    </LocalizedResources>
  <LocalizedResources Id="api.localaccountsignup.es">
    <LocalizedStrings>
      <LocalizedString ElementType="FormatLocalizedStringTransformationClaimType" StringId="ResponseMessge_EmailExists">Este correo electrónico "{0}" ya es una cuenta de esta organización. Haga clic en Siguiente para iniciar sesión con esa cuenta.</LocalizedString>
    </LocalizedStrings>
  </LocalizedResources>
</Localization>
```

A transformação de alegações cria uma mensagem de resposta baseada na cadeia localizada. A mensagem contém o endereço de e-mail do utilizador incorporado na picada localizada *ResponseMessge_EmailExists*.

```xml
<ClaimsTransformation Id="SetResponseMessageForEmailAlreadyExists" TransformationMethod="FormatLocalizedString">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="email" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="stringFormatId" DataType="string" Value="ResponseMessge_EmailExists" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="responseMsg" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Exemplo

- Reclamações de entrada:
    - **inputClaim:**sarah@contoso.com
- Parâmetros de entrada:
    - **stringFormat**: ResponseMessge_EmailExists
- Reclamações de saída:
  - **outputClaim**: O e-mail sarah@contoso.com ' ' já é uma conta nesta organização. Clique ao lado para iniciar scontabilidade com a conta.


## <a name="formatstringclaim"></a>FormatStringClaim

Formatar uma reclamação de acordo com a cadeia de formato fornecida. Esta transformação utiliza o `String.Format` método C#.

| Item | TransformaçãoClaimType | Tipo de Dados | Notas |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim |string |O ClaimType que funciona como parâmetro de formato de {0} corda. |
| InputParameter | stringFormat | string | O formato de corda, incluindo o {0}  parâmetro. Este parâmetro de entrada suporta [expressões de transformação de alegações de cordas.](string-transformations.md#string-claim-transformations-expressions)  |
| OutputClaim | outputClaim | string | O ClaimType que é produzido após esta transformação de alegações foi invocado. |

> [!NOTE]
> O tamanho máximo permitido em formato de corda é de 4000.

Utilize esta transformação de sinistros para formatar qualquer cadeia com um parâmetro {0} . O exemplo a seguir cria um **nome de utilizadorPrincipalName**. Todos os perfis técnicos do fornecedor de identidade social, tais como `Facebook-OAUTH` chamadas **de Nome CreateUserPrincipal** para gerar um **nome de utilizadorPrincipalName**.

```xml
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

- Reclamações de entrada:
    - **inputClaim**: 5164db16-3eee-4629-bfda-dcc3326790e9
- Parâmetros de entrada:
    - **stringFormat**: cpim_ {0} @{RelyingPartyTenantId}
- Reclamações de saída:
  - **outputClaim:**cpim_5164db16-3eee-4629-bfda-dcc3326790e9@b2cdemo.onmicrosoft.com

## <a name="formatstringmultipleclaims"></a>FormatStringMultipleClaims

Formato duas reclamações de acordo com a cadeia de formato fornecida. Esta transformação utiliza o `String.Format` método C#.

| Item | TransformaçãoClaimType | Tipo de Dados | Notas |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim |string | O ClaimType que funciona como parâmetro de formato de {0} corda. |
| InputClaim | inputClaim | string | O ClaimType que funciona como parâmetro de formato de {1} corda. |
| InputParameter | stringFormat | string | O formato de corda, incluindo os {0} {1} parâmetros e parâmetros. Este parâmetro de entrada suporta [expressões de transformação de alegações de cordas.](string-transformations.md#string-claim-transformations-expressions)   |
| OutputClaim | outputClaim | string | O ClaimType que é produzido após esta transformação de alegações foi invocado. |

> [!NOTE]
> O tamanho máximo permitido em formato de corda é de 4000.

Utilize esta transformação de alegações para formatar qualquer cadeia com dois parâmetros, {0} e {1} . O exemplo a seguir cria um **nome de exibição** com o formato especificado:

```xml
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

- Reclamações de entrada:
    - **inputClaim1**: Joe
    - **inputClaim2**: Fernando
- Parâmetros de entrada:
    - **stringFormat:** {0}{1}
- Reclamações de saída:
    - **outputClaim**: Joe Fernando

## <a name="getlocalizedstringstransformation"></a>GetLocalizedStringsTransformação

Copia cordas localizadas em reclamações.

| Item | TransformaçãoClaimType | Tipo de Dados | Notas |
| ---- | ----------------------- | --------- | ----- |
| OutputClaim | O nome da cadeia localizada | string | Lista de tipos de reclamações que são produzidos após esta transformação de sinistros foi invocada. |

Para utilizar a transformação de pedidos de gestão getLocalizedStrings:

1. Defina uma [corda de localização](localization.md) e associe-a a um [perfil auto-afirmado-técnico](self-asserted-technical-profile.md).
1. O `ElementType` elemento deve ser definido para `LocalizedString` `GetLocalizedStringsTransformationClaimType` .
1. É `StringId` um identificador único que define, e usá-lo mais tarde na sua transformação de reivindicações.
1. Na transformação de sinistros, especifique a lista de reclamações a definir com a cadeia localizada. `ClaimTypeReferenceId`Trata-se de uma referência a umTip de Reclamação já definido na secção ClaimsSchema na apólice. `TransformationClaimType`É o nome da corda localizada, tal como definido no `StringId` `LocalizedString` elemento.
1. Num [perfil técnico autoafirmado,](self-asserted-technical-profile.md)ou numa entrada de [controlo de ecrã](display-controls.md) ou transformação de reivindicações de resultados, faça uma referência à transformação das suas reclamações.

![GetLocalizedStringsTransformação](./media/string-transformations/get-localized-strings-transformation.png)

O exemplo a seguir procura o sujeito de e-mail, o corpo, a sua mensagem de código, e a assinatura do e-mail, a partir de cordas localizadas. Estas alegações mais tarde usadas pelo modelo de verificação de e-mail personalizado.

Defina as cordas localizadas para inglês (padrão) e espanhol.

```xml
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

A transformação de sinistros define o valor do *tipo* de reclamação com o valor do `StringId` *email_subject*.

```xml
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

- Reclamações de saída:
  - **assunto**: Código de verificação de email da conta Contoso
  - **mensagem**: Obrigado por verificar a sua conta!
  - **codeIntro**: O seu código é
  - **assinatura**: Sinceramente


## <a name="getmappedvaluefromlocalizedcollection"></a>GetMappedValueFromLocalizedCollection

Procurando um item de uma coleção de **restrição** de reclamação.

| Item | TransformaçãoClaimType | Tipo de Dados | Notas |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | mapaFromClaim | string | A alegação que contém o texto a ser analisado na **restriçãoValueClaim** reclama com a coleção **Restrição.**  |
| OutputClaim | restriçãoValueClaim | string | A alegação que contém a coleção **Restrição.** Após a transformação das reclamações ter sido invocada, o valor desta reclamação contém o valor do item selecionado. |

O exemplo a seguir analisa a descrição da mensagem de erro com base na tecla de erro. A **alegação responseMsg** contém uma recolha de mensagens de erro para apresentar ao utilizador final ou para ser enviada para a parte que conta.

```xml
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
A transformação de sinistros procura o texto do item e devolve o seu valor. Se a restrição for localizada `<LocalizedCollection>` utilizando, a transformação de sinistros devolve o valor localizado.

```xml
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

- Reclamações de entrada:
    - **mapFromClaim**: B2C_V1_90001
- Reclamações de saída:
    - **restriçãoValueClaim**: Não pode entrar porque é menor de idade.

## <a name="lookupvalue"></a>LookupValue

Procure um valor de reclamação a partir de uma lista de valores com base no valor de outra reclamação.

| Item | TransformaçãoClaimType | Tipo de Dados | Notas |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputParameterId | string | A alegação que contém o valor de procura |
| InputParameter | |string | Coleção de entradasParametros. |
| InputParameter | errorOnFailedLookup | boolean | Controlando se um erro é devolvido quando não há procura correspondente. |
| OutputClaim | inputParameterId | string | Os ClaimTypes que serão produzidos após esta transformação de alegações foi invocado. O valor da `Id` correspondência. |

O exemplo a seguir procura o nome de domínio numa das coleções de inputParameters. A transformação de sinistros procura o nome de domínio no identificador e devolve o seu valor (um ID de aplicação).

```xml
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

- Reclamações de entrada:
    - **inputParameterId**: test.com
- Parâmetros de entrada:
    - **contoso.com**: 13c15f79-8fb1-4e29-a6c9-be0d36ff19f1
    - **microsoft.com**: 0213308f-17cb-4398-b97e-01da7bd4804e
    - **test.com**: c7026f88-4299-4cdb-965d-3f166464b8a9
    - **errorOnFailedLookup**: falso
- Reclamações de saída:
    - **outputClaim**: c7026f88-4299-4cdb-965d-3f166464b8a9

Quando `errorOnFailedLookup` o parâmetro de entrada é definido para , a `true` transformação de **pedidos de procuração** é sempre executada a partir de um perfil técnico de [validação](validation-technical-profile.md) que é chamado por um [perfil técnico autoafirmado](self-asserted-technical-profile.md), ou um [DisplayConrtol](display-controls.md). Os `LookupNotFound` metadados de um perfil técnico autoafirmado controlam a mensagem de erro que é apresentada ao utilizador.

![Execução assertStringClaimsAreEqual](./media/string-transformations/assert-execution.png)

O exemplo a seguir procura o nome de domínio numa das coleções de inputParameters. A transformação de sinistros procura o nome de domínio no identificador e devolve o seu valor (um ID de aplicação), ou levanta uma mensagem de erro.

```xml
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

- Reclamações de entrada:
    - **inputParameterId**: live.com
- Parâmetros de entrada:
    - **contoso.com**: 13c15f79-8fb1-4e29-a6c9-be0d36ff19f1
    - **microsoft.com**: 0213308f-17cb-4398-b97e-01da7bd4804e
    - **test.com**: c7026f88-4299-4cdb-965d-3f166464b8a9
    - **errorOnFailedLookup**: verdadeiro
- Erro:
    - Nenhuma correspondência encontrada para o valor de reclamação de entrada na lista de ids de parâmetros de entrada e erroOnFailedLookup é verdade.


## <a name="nullclaim"></a>NullClaim

Limpe o valor de uma determinada reclamação.

| Item | TransformaçãoClaimType | Tipo de Dados | Notas |
| ---- | ----------------------- | --------- | ----- |
| OutputClaim | claim_to_null | string | O valor da reclamação está definido para NU. |

Utilize esta transformação de reclamação para remover dados desnecessários do saco de propriedades para que o cookie da sessão seja menor. O exemplo a seguir remove o valor do `TermsOfService` tipo de reclamação.

```xml
<ClaimsTransformation Id="SetTOSToNull" TransformationMethod="NullClaim">
  <OutputClaims>
  <OutputClaim ClaimTypeReferenceId="TermsOfService" TransformationClaimType="claim_to_null" />
  </OutputClaims>
</ClaimsTransformation>
```

- Reclamações de entrada:
    - **outputClaim**: Bem-vindo à App Contoso. Se continuar a navegar e a utilizar este website, está a concordar em cumprir e ficar vinculado pelos seguintes termos e condições...
- Reclamações de saída:
    - **outputClaim**: NU

## <a name="parsedomain"></a>ParseDomínio

Obtém a parte de domínio de um endereço de e-mail.

| Item | TransformaçãoClaimType | Tipo de Dados | Notas |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | e-mailAddress | string | O ClaimType que contém o endereço de e-mail. |
| OutputClaim | domínio | string | O ClaimType que é produzido após esta transformação de alegações foi invocado - o domínio. |

Utilize esta transformação de alegações para analisar o nome de domínio após o símbolo @ do utilizador. A transformação de alegações a seguir demonstra como analisar o nome de domínio a partir de uma reclamação de **e-mail.**

```xml
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

- Reclamações de entrada:
  - **e-mailAddress**: joe@outlook.com
- Reclamações de saída:
    - **domínio**: outlook.com

## <a name="setclaimsifregexmatch"></a>SetClaimsIfRegexMatch

Verifica se um parâmetro de reivindicação de cordas `claimToMatch` e de entrada são `matchTo` iguais, e define as alegações de saída com o valor presente no `outputClaimIfMatched` parâmetro de entrada, juntamente com a alegação de saída de resultados de comparação, que deve ser definida como `true` ou com base no resultado da `false` comparação.

| Item | TransformaçãoClaimType | Tipo de Dados | Notas |
| ---- | ----------------------- | --------- | ----- |
| inputClaim | claimToMatch | string | O tipo de reclamação, que deve ser comparado. |
| InputParameter | matchTo | string | A expressão regular para combinar. |
| InputParameter | outputClaimIfMatched | string | O valor a definir se as cordas forem iguais. |
| InputParameter | extractosGrupos | boolean | [Opcional] Especifica se a correspondência Regex deve extrair valores de grupos. Valores possíveis: `true` , ou `false` (predefinição). | 
| OutputClaim | outputClaim | string | Se a expressão regular corresponder, esta alegação de saída contém o valor do parâmetro de `outputClaimIfMatched` entrada. Ou nulo, se não corresponder. |
| OutputClaim | regexCompareResultClaim | boolean | O tipo de reclamação de resultado de correspondência de expressão regular, que deve ser definido como `true` ou com base no resultado da `false` correspondência. |
| OutputClaim| O nome da reclamação| string | Se o parâmetro de entrada do ExtractGroups for definido como verdadeiro, a lista dos tipos de reclamações que são produzidos após esta transformação de sinistros foi invocada. O nome da reivindicaçãoType deve corresponder ao nome do grupo Regex. | 

### <a name="example-1"></a>Exemplo 1

Verifique se o número de telefone fornecido é válido, com base no padrão de expressão regular do número de telefone.

```xml
<ClaimsTransformation Id="SetIsPhoneRegex" TransformationMethod="SetClaimsIfRegexMatch">
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

- Reclamações de entrada:
    - **claimToMatch**: "64854114520"
- Parâmetros de entrada:
    - **matchTo**: "^[0-9] {4,16} $"
    - **outputClaimIfMatched**: "isPhone"
- Reclamações de saída:
    - **outputClaim**: "isPhone"
    - **regexCompareResultClaim**: verdadeiro

### <a name="example-2"></a>Exemplo 2

Verifique se o endereço de e-mail fornecido é válido e devolva o pseudónimo do e-mail.

```xml
<ClaimsTransformation Id="GetAliasFromEmail" TransformationMethod="SetClaimsIfRegexMatch">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="claimToMatch" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="matchTo" DataType="string" Value="(?&lt;mailAlias&gt;.*)@(.*)$" />
    <InputParameter Id="outputClaimIfMatched" DataType="string" Value="isEmail" />
    <InputParameter Id="extractGroups" DataType="boolean" Value="true" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="validationResult" TransformationClaimType="outputClaim" />
    <OutputClaim ClaimTypeReferenceId="isEmailString" TransformationClaimType="regexCompareResultClaim" />
    <OutputClaim ClaimTypeReferenceId="mailAlias" />
  </OutputClaims>
</ClaimsTransformation>
```

- Reclamações de entrada:
    - **claimToMatch**: emily@contoso.com "
- Parâmetros de entrada:
    - **matchTo:**`(?&lt;mailAlias&gt;.*)@(.*)$`
    - **outputClaimIfMatched**: "isEmail"
    - **extractGroups**: verdadeiro
- Reclamações de saída:
    - **outputClaim**: "isEmail"
    - **regexCompareResultClaim**: verdadeiro
    - **mailAlias**: emily
    
## <a name="setclaimsifstringsareequal"></a>SetClaimsIfStringsAreEqual

Verifica se um parâmetro de reivindicação de cordas e `matchTo` de entrada são iguais, e define as alegações de saída com o valor presente nos `stringMatchMsg` parâmetros e `stringMatchMsgCode` entradas, juntamente com a alegação de saída de resultados de comparação, que deve ser definida como `true` ou com base no resultado da `false` comparação.

| Item | TransformaçãoClaimType | Tipo de Dados | Notas |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim | string | O tipo de reclamação, que deve ser comparado. |
| InputParameter | matchTo | string | A corda a ser comparada com `inputClaim` . |
| InputParameter | cordaComparison | string | Valores possíveis: `Ordinal` ou `OrdinalIgnoreCase` . |
| InputParameter | stringMatchMsg | string | Primeiro valor a ser definido se as cordas forem iguais. |
| InputParameter | stringMatchMsgCode | string | Segundo valor a definir se as cordas forem iguais. |
| OutputClaim | outputClaim1 | string | Se as cordas forem iguais, esta alegação de saída contém o valor do parâmetro de `stringMatchMsg` entrada. |
| OutputClaim | saídaClaim2 | string | Se as cordas forem iguais, esta alegação de saída contém o valor do parâmetro de `stringMatchMsgCode` entrada. |
| OutputClaim | stringCompareResultClaim | boolean | O tipo de reclamação de resultado de comparação, que deve ser definido como `true` ou baseado no resultado da `false` comparação. |

Pode utilizar esta transformação de reclamações para verificar se uma reclamação é igual ao valor especificado. Por exemplo, a transformação de sinistros a seguir verifica se o valor dos **termosOfUseConsentVersion** é igual a `v1` . Se sim, mude o valor para `v2` .

```xml
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

- Reclamações de entrada:
    - **inputClaim**: v1
- Parâmetros de entrada:
    - **matchTo**: V1
    - **stringComparison**: ordinalIgnoreCase
    - **stringMatchMsg**: B2C_V1_90005
    - **stringMatchMsgCode**: O TOS é atualizado para v2
- Reclamações de saída:
    - **outputClaim1**: B2C_V1_90005
    - **outputClaim2**: O TOS é atualizado para v2
    - **stringCompareResultClaim**: verdadeiro

## <a name="setclaimsifstringsmatch"></a>SetClaimsIfStringsMatch

Verifica se um parâmetro de reivindicação de cordas e `matchTo` de entrada são iguais, e define as alegações de saída com o valor presente no `outputClaimIfMatched` parâmetro de entrada, juntamente com a alegação de saída de resultados de comparação, que deve ser definida como `true` ou com base no resultado da `false` comparação.

| Item | TransformaçãoClaimType | Tipo de Dados | Notas |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | claimToMatch | string | O tipo de reclamação, que deve ser comparado. |
| InputParameter | matchTo | string | A corda a ser comparada com a entradaClaim. |
| InputParameter | cordaComparison | string | Valores possíveis: `Ordinal` ou `OrdinalIgnoreCase` . |
| InputParameter | outputClaimIfMatched | string | O valor a definir se as cordas forem iguais. |
| OutputClaim | outputClaim | string | Se as cordas forem iguais, esta alegação de saída contém o valor do parâmetro de `outputClaimIfMatched` entrada. Ou nulo, se as cordas não forem compatíveis. |
| OutputClaim | stringCompareResultClaim | boolean | O tipo de reclamação de resultado de comparação, que deve ser definido como `true` ou baseado no resultado da `false` comparação. |

Por exemplo, a transformação de reclamações a seguir verifica se o valor da reclamação do **AgeGroup** é igual a `Minor` . Se sim, devolva o valor a `B2C_V1_90001` .

```xml
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

- Reclamações de entrada:
    - **claimToMatch**: Menor
- Parâmetros de entrada:
    - **matchTo**: Menor
    - **stringComparison**: ordinalIgnoreCase
    - **outputClaimIfMatched**: B2C_V1_90001
- Reclamações de saída:
    - **isMinorResponseCode**: B2C_V1_90001
    - **isMinor**: verdadeiro


## <a name="stringcontains"></a>CordasContains

Determinar se um sub-adcção especificado ocorre dentro da reclamação de entrada. O resultado é um novo Boolean ClaimType com um valor de `true` ou `false` . `true` se o parâmetro de valor ocorrer dentro desta cadeia, caso contrário, `false` .

| Item | TransformaçãoClaimType | Tipo de Dados | Notas |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim | string | O tipo de reclamação, que deve ser revistado. |
|InputParameter|contains|string|O valor a procurar.|
|InputParameter|ignoreCase|string|Especifica se esta comparação deve ignorar o caso da cadeia ser comparada.|
| OutputClaim | outputClaim | string | O ClaimType que é produzido após esta ReclamaçãoTransformação foi invocado. Um indicador booleano se o sub-adiscão ocorrer dentro da reivindicação de entrada. |

Utilize esta transformação de sinistros para verificar se um tipo de alegação de corda contém um sub-adcção. Seguindo o exemplo, verifica se o `roles` tipo de reclamação de cordas contém o valor da **administração**.

```xml
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

- Reclamações de entrada:
    - **inputClaim**: "Administrador, Aprovador, Editor"
- Parâmetros de entrada:
    - **contém**: "administrador",
    - **ignoreCase**: verdadeiro
- Reclamações de saída:
    - **outputClaim**: verdadeiro

## <a name="stringsubstring"></a>StringSubstring

Extrai partes de um tipo de reivindicação de cordas, começando pelo personagem na posição especificada, e devolve o número especificado de caracteres.

| Item | TransformaçãoClaimType | Tipo de Dados | Notas |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim | string | O tipo de reclamação, que contém a corda. |
| InputParameter | startIndex | int | A posição de caracteres initenta baseado em zero de um sub-cordão neste caso. |
| InputParameter | length | int | O número de caracteres na sub-corda. |
| OutputClaim | outputClaim | boolean | Uma corda que é equivalente ao sub-comprimento que começa no startIndex neste caso, ou Empty if startIndex é igual ao comprimento deste caso e comprimento é zero. |

Por exemplo, obtenha o prefixo do número de telefone país/região.


```xml
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

- Reclamações de entrada:
    - **inputClaim**: "+1644114520"
- Parâmetros de entrada:
    - **startIndex**: 0
    - **comprimento**: 2
- Reclamações de saída:
    - **outputClaim**: "+1"

## <a name="stringreplace"></a>CadeiaReplace

Procura uma cadeia tipo de reclamação para obter um valor especificado e devolve uma nova cadeia tipo de reclamação na qual todas as ocorrências de uma cadeia especificada na cadeia atual são substituídas por outra cadeia especificada.

| Item | TransformaçãoClaimType | Tipo de Dados | Notas |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim | string | O tipo de reclamação, que contém a corda. |
| InputParameter | oldValue | string | A corda a ser revistada. |
| InputParameter | newValue | string | A cadeia para substituir todas as ocorrências de `oldValue` |
| OutputClaim | outputClaim | boolean | Uma corda que é equivalente à cadeia atual, exceto que todas as instâncias do velhoVal são substituídas por newValue. Se o velhoValue não for encontrado no caso atual, o método retorna a instância atual inalterada. |

Por exemplo, normalizar um número de telefone, removendo os `-` caracteres


```xml
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

- Reclamações de entrada:
    - **inputClaim**: "+164-411-452-054"
- Parâmetros de entrada:
    - **oldValue**: "-"
    - **newValue**: ""
- Reclamações de saída:
    - **outputClaim**: "+164411452054"

## <a name="stringjoin"></a>CordaJoin

Concatenats os elementos de um tipo de reivindicação de recolha de cordas especificado, utilizando o separador especificado entre cada elemento ou membro.

| Item | TransformaçãoClaimType | Tipo de Dados | Notas |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim | stringCollection | Uma coleção que contém as cordas para concatenar. |
| InputParameter | delimitador | string | A corda para usar como separador, como vírgula. `,` |
| OutputClaim | outputClaim | string | Uma corda que consiste nos membros da coleção de `inputClaim` cordas, delimitada pelo parâmetro de `delimiter` entrada. |

O exemplo a seguir pega numa coleção de cordas de papéis de utilizador, e converte-o numa cadeia delimiter de vírgula. Pode utilizar este método para armazenar uma coleção de cordas na conta de utilizador Azure AD. Mais tarde, quando ler a conta do diretório, use `StringSplit` o para converter a corda delimiter de vírgula de volta para a coleção de cordas.

```xml
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

- Reclamações de entrada:
  - **inputClaim**: [ "Administrador", "Autor", "Leitor" ]
- Parâmetros de entrada:
  - **delimiter:**""
- Reclamações de saída:
  - **outputClaim**: "Administrador,Autor,Leitor"


## <a name="stringsplit"></a>StringSplit

Devolve uma matriz de cordas que contém os sublutos neste caso que são delimitados por elementos de uma cadeia especificada.

| Item | TransformaçãoClaimType | Tipo de Dados | Notas |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim | string | Um tipo de alegação de corda que contém as cadeias sub para dividir. |
| InputParameter | delimitador | string | A corda para usar como separador, como vírgula. `,` |
| OutputClaim | outputClaim | stringCollection | Uma coleção de cordas cujos elementos contêm os sublutos desta corda que são delimitados pelo parâmetro de `delimiter` entrada. |

O exemplo a seguir pega numa cadeia de delimiter de vírgula de papéis de utilizador, e converte-o numa coleção de cordas.

```xml
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

- Reclamações de entrada:
  - **inputClaim**: "Administrador,Autor,Leitor"
- Parâmetros de entrada:
  - **delimiter:**""
- Reclamações de saída:
  - **outputClaim**: [ "Administrador", "Autor", "Leitor" ]

## <a name="string-claim-transformations-expressions"></a>Alegação de cordas transforma expressões
As expressões de transformações de reivindicação nas políticas personalizadas Azure AD B2C fornecem informações de contexto sobre o ID do inquilino e identificação de perfil técnico.

  | Expressão | Descrição | Exemplo |
 | ----- | ----------- | --------|
 | `{TechnicalProfileId}` | O nome de perfil técnicoid. | Facebook-OAUTH |
 | `{RelyingPartyTenantId}` | A identificação do inquilino da política do partido. | your-tenant.onmicrosoft.com |
 | `{TrustFrameworkTenantId}` | A identificação do inquilino do quadro de confiança. | your-tenant.onmicrosoft.com |
