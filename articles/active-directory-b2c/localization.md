---
title: Localização - Diretório Ativo Azure B2C
description: Especifique o elemento de localização de uma política personalizada no Diretório Ativo Azure B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 04/20/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 94ff7ddda41f2df2634d927a7dbf8a5a0d4fc1d8
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/21/2020
ms.locfileid: "81681420"
---
# <a name="localization"></a>Localização

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

O elemento **Localização** permite-lhe apoiar vários locais ou idiomas na política para as viagens do utilizador. O apoio à localização em políticas permite-lhe:

- Configurar a lista explícita das línguas suportadas numa política e escolher uma linguagem padrão.
- Forneça cordas e coleções específicas da linguagem.

```XML
<Localization Enabled="true">
  <SupportedLanguages DefaultLanguage="en" MergeBehavior="ReplaceAll">
    <SupportedLanguage>en</SupportedLanguage>
    <SupportedLanguage>es</SupportedLanguage>
  </SupportedLanguages>
  <LocalizedResources Id="api.localaccountsignup.en">
  <LocalizedResources Id="api.localaccountsignup.es">
  ...
```

O elemento **Localização** contém os seguintes atributos:

| Atributo | Necessário | Descrição |
| --------- | -------- | ----------- |
| Ativado | Não | Valores `true` possíveis: ou `false`. |

O elemento **de localização** contém elementos XML que seguem

| Elemento | Ocorrências | Descrição |
| ------- | ----------- | ----------- |
| Línguas suportadas | 1:n | Lista de línguas apoiadas. |
| Recursos Localizados | 0:n | Lista de recursos localizados. |

## <a name="supportedlanguages"></a>Línguas suportadas

O elemento **SupportEdLanguages** contém os seguintes atributos:

| Atributo | Necessário | Descrição |
| --------- | -------- | ----------- |
| DefaultLanguage | Sim | A linguagem a ser usada como padrão para recursos localizados. |
| Função Função Behavior | Não | Uma enumeração dos valores que são fundidos juntamente com qualquer ClaimType presente numa política-mãe com o mesmo identificador. Use este atributo quando substituir uma reclamação especificada na política de base. Valores `Append`possíveis: , `Prepend`ou `ReplaceAll`. O `Append` valor especifica que a recolha de dados presentes deve ser anexada ao fim da recolha especificada na política-mãe. O `Prepend` valor especifica que a recolha dos dados presentes deve ser adicionada antes da recolha especificada na política-mãe. O `ReplaceAll` valor especifica que a recolha de dados definida na política-mãe deve ser ignorada, utilizando, em vez disso, os dados definidos na política atual. |

### <a name="supportedlanguages"></a>Línguas suportadas

O elemento **SupportEdLanguages** contém os seguintes elementos:

| Elemento | Ocorrências | Descrição |
| ------- | ----------- | ----------- |
| Linguagem apoiada | 1:n | Exibe conteúdo em conformidade com uma etiqueta linguística por RFC 5646 - Tags para Identificar Idiomas. |

## <a name="localizedresources"></a>Recursos Localizados

O elemento **Recursos Localizados** contém os seguintes atributos:

| Atributo | Necessário | Descrição |
| --------- | -------- | ----------- |
| Id | Sim | Um identificador que é usado para identificar recursos localizados de forma única. |

O elemento **Recursos Localizados** contém os seguintes elementos:

| Elemento | Ocorrências | Descrição |
| ------- | ----------- | ----------- |
| Coleções Localizadas | 0:n | Define coleções inteiras em várias culturas. Uma coleção pode ter um número diferente de itens e diferentes cordas para várias culturas. Exemplos de coleções incluem as enumerações que aparecem nos tipos de reclamação. Por exemplo, uma lista país/região é mostrada ao utilizador numa lista de abandono. |
| Strings localizados | 0:n | Define todas as cordas, exceto as cordas que aparecem em coleções, em várias culturas. |

### <a name="localizedcollections"></a>Coleções Localizadas

O elemento **Coleções Localizadas** contém os seguintes elementos:

| Elemento | Ocorrências | Descrição |
| ------- | ----------- | ----------- |
| Coleção Localizada | 1:n | Lista de línguas apoiadas. |

#### <a name="localizedcollection"></a>Coleção Localizada

O elemento **Coleção Localizada** contém os seguintes atributos:

| Atributo | Necessário | Descrição |
| --------- | -------- | ----------- |
| ElementType | Sim | Refere um elemento ClaimType ou um elemento de interface do utilizador no ficheiro de política. |
| Elementide | Sim | Uma cadeia que contém uma referência a um tipo de reclamação já definido na secção ClaimsSchema que é usada se o **ElementType** for definido para um ClaimType. |
| Coleção-alvo | Sim | A coleção do alvo. |

O elemento **Coleção Localizada** contém os seguintes elementos:

| Elemento | Ocorrências | Descrição |
| ------- | ----------- | ----------- |
| Item | 0:n | Define uma opção disponível para o utilizador selecionar para uma reclamação na interface do utilizador, como um valor numa queda. |

O elemento **Item** contém os seguintes atributos:

| Atributo | Necessário | Descrição |
| --------- | -------- | ----------- |
| Texto | Sim | A cadeia de visualização fácil de utilizar que deve ser mostrada ao utilizador na interface do utilizador para esta opção. |
| Valor | Sim | O valor da reclamação de cadeia associado à seleção desta opção. |
| SelectByDefault | Não | Indica se esta opção deve ou não ser selecionada por defeito na UI. Valores possíveis: Verdadeiro ou Falso. |

O exemplo seguinte mostra a utilização do elemento **Coleções Localizadas.** Contém dois elementos **LocalizadoSCollection,** um para inglês e outro para espanhol. Ambos definiram a coleção `Gender` **restrição** da reclamação com uma lista de itens para inglês e espanhol.

```XML
<LocalizedResources Id="api.selfasserted.en">
 <LocalizedCollections>
   <LocalizedCollection ElementType="ClaimType" ElementId="Gender" TargetCollection="Restriction">
      <Item Text="Female" Value="F" />
      <Item Text="Male" Value="M" />
    </LocalizedCollection>
</LocalizedCollections>

<LocalizedResources Id="api.selfasserted.es">
 <LocalizedCollections>
   <LocalizedCollection ElementType="ClaimType" ElementId="Gender" TargetCollection="Restriction">
      <Item Text="Femenino" Value="F" />
      <Item Text="Masculino" Value="M" />
    </LocalizedCollection>
</LocalizedCollections>
```

### <a name="localizedstrings"></a>Strings localizados

O elemento **"Strings" localizado** contém os seguintes elementos:

| Elemento | Ocorrências | Descrição |
| ------- | ----------- | ----------- |
| String localizado | 1:n | Uma corda localizada. |

O elemento **LocalizedString** contém os seguintes atributos:

| Atributo | Necessário | Descrição |
| --------- | -------- | ----------- |
| ElementType | Sim | Valores possíveis: [ClaimsProvider](#claimsprovider), [ClaimType](#claimtype), [ErrorMessage](#errormessage), [GetLocalizedStringsTransformationClaimType](#getlocalizedstringstransformationclaimtype), [Predicado](#predicate), [Validação de Entrada](#inputvalidation), ou [UxElement](#uxelement).   | 
| Elementide | Sim | Se o **ElementType** `Predicate`estiver `InputValidation`definido para, `ClaimType`ou , este elemento contém uma referência a um tipo de reclamação já definido na secção ClaimsSchema. |
| Cadeia | Sim | Se o **ElementType** estiver `ClaimType`definido, este elemento contém uma referência a um atributo de um tipo de reclamação. Valores `DisplayName`possíveis: , `AdminHelpText`ou `PatternHelpText`. O `DisplayName` valor é utilizado para definir o nome do display de reclamação. O `AdminHelpText` valor é utilizado para definir o nome de texto de ajuda do utilizador da reclamação. O `PatternHelpText` valor é usado para definir o texto de ajuda do padrão de reclamação. Se o **ElementType** estiver `UxElement`definido, este elemento contém uma referência a um atributo de um elemento de interface do utilizador. Se o **ElementType** estiver `ErrorMessage`definido, este elemento especifica o identificador de uma mensagem de erro. Consulte [as identificações](localization-string-ids.md) de cordas de `UxElement` localização para obter uma lista completa dos identificadores.|

## <a name="elementtype"></a>ElementType

O ElementType refere-se a um tipo de reclamação, a uma transformação de reclamações ou a um elemento de interface do utilizador na política a ser localizada.

| Elemento para localizar | ElementType | Elementide |Cadeia |
| --------- | -------- | ----------- |----------- |
| Nome do fornecedor de identidade |`ClaimsProvider`| | A identificação do elemento ClaimsExchange|
| Atributos do tipo de reclamação|`ClaimType`|Nome do tipo de reclamação| O atributo da reivindicação a ser localizada. Valores `AdminHelpText`possíveis: , `DisplayName`, `PatternHelpText`e `UserHelpText`.|
|Mensagem de erro|`ErrorMessage`||A identificação da mensagem de erro |
|Cópias de cordas localizadas em sinistros|`GetLocalizedStringsTra nsformationClaimType`||O nome da reivindicação de saída|
|Mensagem de utilizador predicada|`Predicate`|O nome do predicado| O atributo do predicado a ser localizado. Valores `HelpText`possíveis: .|
|Mensagem de utilizador do grupo predicado|`InputValidation`|Identificação do elemento Predicado Validação.|A identificação do elemento PredicadoGroup. O grupo predicado deve ser uma criança do elemento de validação predicado, tal como definido no ElementId.|
|Elementos da interface de utilizador |`UxElement` | | A identificação do elemento interface do utilizador a ser localizada.|

## <a name="examples"></a>Exemplos

### <a name="claimsprovider"></a>ReclamaçõesProvedor

O valor reclamação é utilizado para localizar um dos fornecedores de reclamações a exibir o nome. 

```xml
<OrchestrationStep Order="2" Type="ClaimsExchange">
  ...
  <ClaimsExchanges>
    <ClaimsExchange Id="FacebookExchange" TechnicalProfileReferenceId="Facebook-OAUTH" />
    <ClaimsExchange Id="GoogleExchange" TechnicalProfileReferenceId="Google-OAUTH" />
    <ClaimsExchange Id="LinkedInExchange" TechnicalProfileReferenceId="LinkedIn-OAUTH" />
  </ClaimsExchanges>
</OrchestrationStep>

```

O exemplo que se segue mostra como localizar o nome de exibição dos fornecedores de reclamações.

```xml
<LocalizedString ElementType="ClaimsProvider" StringId="FacebookExchange">Facebook</LocalizedString>
<LocalizedString ElementType="ClaimsProvider" StringId="GoogleExchange">Google</LocalizedString>
<LocalizedString ElementType="ClaimsProvider" StringId="LinkedInExchange">LinkedIn</LocalizedString>
```

### <a name="claimtype"></a>Tipo de reclamação

O valor Do ClaimType é utilizado para localizar um dos atributos de reclamação. 

```xml
<ClaimType Id="email">
  <DisplayName>Email Address</DisplayName>
  <DataType>string</DataType>
  <UserHelpText>Email address that can be used to contact you.</UserHelpText>
  <UserInputType>TextBox</UserInputType>
</ClaimType>
```

O exemplo seguinte mostra como localizar os atributos DisplayName, UserHelpText e PatternHelpText do tipo de pedido de e-mail.

```XML
<LocalizedString ElementType="ClaimType" ElementId="email" StringId="DisplayName">Email</LocalizedString>
<LocalizedString ElementType="ClaimType" ElementId="email" StringId="UserHelpText">Please enter your email</LocalizedString>
<LocalizedString ElementType="ClaimType" ElementId="email" StringId="PatternHelpText">Please enter a valid email address</LocalizedString>
```

### <a name="errormessage"></a>Mensagem de erro

O valor ErrorMessage é utilizado para localizar uma das mensagens de erro do sistema. 

```xml
<TechnicalProfile Id="AAD-UserWriteUsingAlternativeSecurityId">
  <Metadata>
    <Item Key="RaiseErrorIfClaimsPrincipalAlreadyExists">true</Item>
    <Item Key="UserMessageIfClaimsPrincipalAlreadyExists">You are already registered, please press the back button and sign in instead.</Item>
  </Metadata>
  ...
</TechnicalProfile>
```

O exemplo que se segue mostra como localizar a mensagem de erro UserMessageIfClaimsPrincipalAlreadyExiste uma mensagem de erro.


```XML
<LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimsPrincipalAlreadyExists">The account you are trying to create already exists, please sign-in.</LocalizedString>
```

### <a name="getlocalizedstringstransformationclaimtype"></a>GetLocalizedStringsTransformationClaimType

O valor GetLocalizedStringsTransformationClaimType é usado para copiar cordas localizadas em sinistros. Para mais informações, consulte [GetLocalizedStringsTransformation reivindica a transformação](string-transformations.md#getlocalizedstringstransformation)


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

O exemplo seguinte mostra como localizar as alegações de saída da GetLocalizedStringsTransformation reivindica a transformação.

```xml
<LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_subject">Contoso account email verification code</LocalizedString>
<LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_message">Thanks for verifying your account!</LocalizedString>
<LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_code">Your code is</LocalizedString>
<LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_signature">Sincerely</LocalizedString>
```

### <a name="predicate"></a>Predicado

O valor predicado é utilizado para localizar uma das mensagens de erro [predicadas.](predicates.md) 

```xml
<Predicates>
  <Predicate Id="LengthRange" Method="IsLengthRange"  HelpText="The password must be between 6 and 64 characters.">
    <Parameters>
      <Parameter Id="Minimum">6</Parameter>
      <Parameter Id="Maximum">64</Parameter>
    </Parameters>
  </Predicate>
  <Predicate Id="Lowercase" Method="IncludesCharacters" HelpText="a lowercase letter">
    <Parameters>
      <Parameter Id="CharacterSet">a-z</Parameter>
    </Parameters>
  </Predicate>
  <Predicate Id="Uppercase" Method="IncludesCharacters" HelpText="an uppercase letter">
    <Parameters>
      <Parameter Id="CharacterSet">A-Z</Parameter>
    </Parameters>
  </Predicate>
</Predicates>
```

O exemplo que se segue mostra como localizar predicados ajudam o texto.

```xml
<LocalizedString ElementType="Predicate" ElementId="LengthRange" StringId="HelpText">The password must be between 6 and 64 characters.</LocalizedString>
<LocalizedString ElementType="Predicate" ElementId="Lowercase" StringId="HelpText">a lowercase letter</LocalizedString>
<LocalizedString ElementType="Predicate" ElementId="Uppercase" StringId="HelpText">an uppercase letter</LocalizedString>
```

### <a name="inputvalidation"></a>Validação de Entrada

O valor de validação de entrada é utilizado para localizar uma das mensagens de erro do grupo [PredicateValidação.](predicates.md) 

```xml
<PredicateValidations>
  <PredicateValidation Id="CustomPassword">
    <PredicateGroups>
      <PredicateGroup Id="LengthGroup">
        <PredicateReferences MatchAtLeast="1">
          <PredicateReference Id="LengthRange" />
        </PredicateReferences>
      </PredicateGroup>
      <PredicateGroup Id="CharacterClasses">
        <UserHelpText>The password must have at least 3 of the following:</UserHelpText>
        <PredicateReferences MatchAtLeast="3">
          <PredicateReference Id="Lowercase" />
          <PredicateReference Id="Uppercase" />
          <PredicateReference Id="Number" />
          <PredicateReference Id="Symbol" />
        </PredicateReferences>
      </PredicateGroup>
    </PredicateGroups>
  </PredicateValidation>
</PredicateValidations>
```

O exemplo que se segue mostra como localizar um grupo de validação predicado ajuda texto.

```XML
<LocalizedString ElementType="InputValidation" ElementId="CustomPassword" StringId="CharacterClasses">The password must have at least 3 of the following:</LocalizedString>
```

### <a name="uxelement"></a>UxElement

O valor UxElement é utilizado para localizar um dos elementos da interface do utilizador. O exemplo que se segue mostra como localizar os botões de continuação e cancelamento.

```XML
<LocalizedString ElementType="UxElement" StringId="button_continue">Create new account</LocalizedString>
<LocalizedString ElementType="UxElement" StringId="button_cancel">Cancel</LocalizedString>
```

## <a name="next-steps"></a>Passos seguintes

Consulte os seguintes artigos para exemplos de localização:

- [Personalização linguística com política personalizada no Diretório Ativo Azure B2C](custom-policy-localization.md)
- [Personalização linguística com fluxos de utilizadores no Diretório Ativo Azure B2C](user-flow-language-customization.md)
