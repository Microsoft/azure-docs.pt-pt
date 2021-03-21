---
title: Localização - Azure Ative Directory B2C
description: Especificar o elemento de localização de uma política personalizada no Azure Ative Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/08/2021
ms.author: mimart
ms.subservice: B2C
ms.custom: b2c-support
ms.openlocfilehash: 3a5afcd8c0ef0c31353cd2369ead332675c9877f
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102453126"
---
# <a name="localization-element"></a>Elemento de localização

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

O elemento **Localização** permite-lhe suportar vários locais ou idiomas na política para as viagens do utilizador. O apoio à localização nas políticas permite-lhe:

- Crie a lista explícita das línguas apoiadas numa política e escolha uma língua predefinida.
- Fornecer cordas e coleções específicas da linguagem.

```xml
<Localization Enabled="true">
  <SupportedLanguages DefaultLanguage="en" MergeBehavior="ReplaceAll">
    <SupportedLanguage>en</SupportedLanguage>
    <SupportedLanguage>es</SupportedLanguage>
  </SupportedLanguages>
  <LocalizedResources Id="api.localaccountsignup.en">
  <LocalizedResources Id="api.localaccountsignup.es">
  ...
```

O elemento **de localização** contém os seguintes atributos:

| Atributo | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| Ativado | No | Valores possíveis: `true` ou `false` . |

O elemento **de localização** contém elementos XML

| Elemento | Ocorrências | Description |
| ------- | ----------- | ----------- |
| Apoio deGuages | 1:n | Lista de línguas apoiadas. |
| Recursos Localizados | 0:n | Lista de recursos localizados. |

## <a name="supportedlanguages"></a>Apoio deGuages

O elemento **SuportadoLanguages** contém os seguintes atributos:

| Atributo | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| DefaultLanguage | Yes | A linguagem a ser usada como padrão para recursos localizados. |
| FusõesBehavior | No | Uma enumeração de valores que são fundidos juntamente com qualquer ClaimType presente numa política-mãe com o mesmo identificador. Utilize este atributo quando substituir uma reclamação especificada na política base. Valores possíveis: `Append` `Prepend` , ou . `ReplaceAll` . O `Append` valor especifica que a recolha de dados presentes deve ser anexada ao fim da recolha especificada na política-mãe. O `Prepend` valor especifica que a recolha de dados presentes deve ser adicionada antes da recolha especificada na política-mãe. O `ReplaceAll` valor especifica que a recolha de dados definidos na política-mãe deve ser ignorada, utilizando em vez dos dados definidos na política atual. |

### <a name="supportedlanguages"></a>Apoio deGuages

O elemento **Suportado Deguages** contém os seguintes elementos:

| Elemento | Ocorrências | Description |
| ------- | ----------- | ----------- |
| Apoio àguagem | 1:n | Exibe conteúdo satisfatório em conformidade com uma etiqueta linguística por RFC 5646 - Tags para Identificar Línguas. |

## <a name="localizedresources"></a>Recursos Localizados

O elemento **Imagens Locais** contém os seguintes atributos:

| Atributo | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| Id | Yes | Um identificador que é usado para identificar recursos localizados de forma única. |

O elemento **LocaledResources** contém os seguintes elementos:

| Elemento | Ocorrências | Description |
| ------- | ----------- | ----------- |
| Collections localizadas | 0:n | Define coleções inteiras em várias culturas. Uma coleção pode ter um número diferente de itens e diferentes cordas para várias culturas. Exemplos de coleções incluem as enumerações que aparecem nos tipos de reclamações. Por exemplo, uma lista de país/região é mostrada ao utilizador numa lista de abandono. |
| Padrões Localizados | 0:n | Define todas as cordas, exceto as cordas que aparecem em coleções, em várias culturas. |

### <a name="localizedcollections"></a>Collections localizadas

O elemento **LocalizedCollections** contém os seguintes elementos:

| Elemento | Ocorrências | Description |
| ------- | ----------- | ----------- |
| Collecto Localizado | 1:n | Lista de línguas apoiadas. |

#### <a name="localizedcollection"></a>Collecto Localizado

O elemento **Decolecção Localizada** contém os seguintes atributos:

| Atributo | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| ElementoType | Yes | Refere um elemento ClaimType ou um elemento de interface do utilizador no ficheiro de política. |
| ElementId | Yes | Uma cadeia que contém uma referência a um tipo de reclamação já definido na secção ClaimsSchema que é usada se **o ElementType** estiver definido para um ClaimType. |
| TargetCollection | Yes | A coleção de alvos. |

O elemento **Decolecção Localizada** contém os seguintes elementos:

| Elemento | Ocorrências | Description |
| ------- | ----------- | ----------- |
| Item | 0:n | Define uma opção disponível para o utilizador selecionar para uma reclamação na interface do utilizador, como um valor numa queda. |

O elemento **Item** contém os seguintes atributos:

| Atributo | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| Texto | Yes | A cadeia de visualização fácil de utilizar que deve ser mostrada ao utilizador na interface do utilizador para esta opção. |
| Valor | Yes | O valor de reivindicação de cadeia associado à seleção desta opção. |
| SelecioneByDefault | No | Indica se esta opção deve ou não ser selecionada por padrão na UI. Valores possíveis: Verdadeiros ou Falsos. |

O exemplo a seguir mostra a utilização do elemento **Decollections Localizado.** Contém dois elementos **decolecção localizada,** um para inglês e outro para espanhol. Ambos definiram a coleção **de restrição** da reclamação `Gender` com uma lista de itens para inglês e espanhol.

```xml
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

### <a name="localizedstrings"></a>Padrões Localizados

O elemento **LocalizadoStrings** contém os seguintes elementos:

| Elemento | Ocorrências | Description |
| ------- | ----------- | ----------- |
| Estamos localizados | 1:n | Uma corda localizada. |

O elemento **LocalizadoString** contém os seguintes atributos:

| Atributo | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| ElementoType | Yes | Valores possíveis: [ClaimsProvider](#claimsprovider), [ClaimType](#claimtype), [ErrorMessage](#errormessage), [GetLocalizedStringsTransformationClaimType](#getlocalizedstringstransformationclaimtype), [FormatLocalizedStringTransformationClaimType](#formatlocalizedstringtransformationclaimtype), [Predicate,](#predicate) [InputValidation](#inputvalidation)ou [UxElement](#uxelement).   | 
| ElementId | Yes | Se **o ElementType** estiver definido para `ClaimType` , ou , este elemento contém uma referência a um tipo de `Predicate` `InputValidation` reclamação já definido na secção ClaimsSchema. |
| StringId | Yes | Se **o ElementType** estiver definido `ClaimType` para, este elemento contém uma referência a um atributo de um tipo de reclamação. Valores possíveis: `DisplayName` `AdminHelpText` , ou . `PatternHelpText` . O `DisplayName` valor é utilizado para definir o nome de exibição de reclamação. O `AdminHelpText` valor é utilizado para definir o nome de texto de ajuda do utilizador de reclamação. O `PatternHelpText` valor é usado para definir o texto de ajuda do padrão de reclamação. Se **o ElementType** estiver definido `UxElement` para, este elemento contém uma referência a um atributo de um elemento de interface do utilizador. Se **o ElementType** estiver definido para `ErrorMessage` , este elemento especifica o identificador de uma mensagem de erro. Consulte [os IDs de cadeia de localização](localization-string-ids.md) para obter uma lista completa dos `UxElement` identificadores.|

## <a name="elementtype"></a>ElementoType

O ElementType refere-se a um tipo de reclamação, a uma transformação de reclamação ou a um elemento de interface de utilizador na política a ser localizada.

| Elemento para localizar | ElementoType | ElementId |StringId |
| --------- | -------- | ----------- |----------- |
| Nome do fornecedor de identidade |`ClaimsProvider`| | O ID do elemento ClaimsExchange|
| Atributos do tipo de reclamação|`ClaimType`|Nome do tipo de reclamação| O atributo da reivindicação a ser localizado. Valores possíveis: `AdminHelpText` `DisplayName` , , e `PatternHelpText` `UserHelpText` .|
|Mensagem de erro|`ErrorMessage`||O ID da mensagem de erro |
|Copia cordas localizadas em reclamações|`GetLocalizedStringsTra nsformationClaimType`||O nome da reivindicação de saída|
|Mensagem de utilizador predicado|`Predicate`|O nome do predicado| O atributo do predicado a ser localizado. Valores possíveis: `HelpText` .|
|Mensagem de utilizador do grupo predicado|`InputValidation`|A identificação do elemento PredicateValidation.|O ID do elemento Grupo Predicate. O grupo predicado deve ser uma criança do elemento de validação predicado, tal como definido no ElementId.|
|Elementos da interface de utilizador |`UxElement` | | O ID do elemento de interface do utilizador a ser localizado.|
|[Controlo de exibição](display-controls.md) |`DisplayControl` |A identificação do controlo de visualização. | O ID do elemento de interface do utilizador a ser localizado.|

## <a name="examples"></a>Exemplos

### <a name="claimsprovider"></a>Provívido de Reclamações

O valor ClaimsProvider é utilizado para localizar um dos fornecedores de reclamações. 

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

O exemplo a seguir mostra como localizar o nome de exibição dos fornecedores de reclamações.

```xml
<LocalizedString ElementType="ClaimsProvider" StringId="FacebookExchange">Facebook</LocalizedString>
<LocalizedString ElementType="ClaimsProvider" StringId="GoogleExchange">Google</LocalizedString>
<LocalizedString ElementType="ClaimsProvider" StringId="LinkedInExchange">LinkedIn</LocalizedString>
```

### <a name="claimtype"></a>ClaimType

O valor ClaimType é utilizado para localizar um dos atributos de reclamação. 

```xml
<ClaimType Id="email">
  <DisplayName>Email Address</DisplayName>
  <DataType>string</DataType>
  <UserHelpText>Email address that can be used to contact you.</UserHelpText>
  <UserInputType>TextBox</UserInputType>
</ClaimType>
```

O exemplo a seguir mostra como localizar os atributos DisplayName, UserHelpText e PatternHelpText do tipo de reclamação de e-mail.

```xml
<LocalizedString ElementType="ClaimType" ElementId="email" StringId="DisplayName">Email</LocalizedString>
<LocalizedString ElementType="ClaimType" ElementId="email" StringId="UserHelpText">Please enter your email</LocalizedString>
<LocalizedString ElementType="ClaimType" ElementId="email" StringId="PatternHelpText">Please enter a valid email address</LocalizedString>
```

### <a name="errormessage"></a>ErroS

O valor ErrorMessage é utilizado para localização de uma das mensagens de erro do sistema. 

```xml
<TechnicalProfile Id="AAD-UserWriteUsingAlternativeSecurityId">
  <Metadata>
    <Item Key="RaiseErrorIfClaimsPrincipalAlreadyExists">true</Item>
    <Item Key="UserMessageIfClaimsPrincipalAlreadyExists">You are already registered, please press the back button and sign in instead.</Item>
  </Metadata>
  ...
</TechnicalProfile>
```

O exemplo a seguir mostra como rastrear a mensagem de erro do UserMessageIfClaimsPrincipalAlreadyExists.


```xml
<LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimsPrincipalAlreadyExists">The account you are trying to create already exists, please sign-in.</LocalizedString>
```

### <a name="formatlocalizedstringtransformationclaimtype"></a>FormatoLocalizedStringTransformationClaimType

O valor FormatLocalizedStringTransformationClaimType é utilizado para formatar reclamações numa cadeia localizada. Para mais informações, consulte [FormatLocalizedString transformação de reclamações](string-transformations.md#formatlocalizedstring)


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

O exemplo a seguir mostra como localização do formato de cadeia do FormatLocalizedStringTransformationClaimType alega transformação.

```xml
<LocalizedString ElementType="FormatLocalizedStringTransformationClaimType" StringId="ResponseMessge_EmailExists">The email '{0}' is already an account in this organization. Click Next to sign in with that account.</LocalizedString>
```

### <a name="getlocalizedstringstransformationclaimtype"></a>GetLocalizedStringsTransformationClaimType

O valor GetLocalizedStringsTransformationClaimType é utilizado para copiar cordas localizadas em sinistros. Para obter mais informações, consulte [a transformação de reclamações de pedidos de gestão getLocalizedStrings](string-transformations.md#getlocalizedstringstransformation)


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

O exemplo a seguir mostra como localização de alegações de produção da transformação de sinistros GetLocalizedStringsTransformation.

```xml
<LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_subject">Contoso account email verification code</LocalizedString>
<LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_message">Thanks for verifying your account!</LocalizedString>
<LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_code">Your code is</LocalizedString>
<LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_signature">Sincerely</LocalizedString>
```

### <a name="predicate"></a>Predicado

O valor Predicado é utilizado para localização de uma das mensagens de erro [predicados.](predicates.md) 

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

O exemplo a seguir mostra como localizar os predicados ajudam o texto.

```xml
<LocalizedString ElementType="Predicate" ElementId="LengthRange" StringId="HelpText">The password must be between 6 and 64 characters.</LocalizedString>
<LocalizedString ElementType="Predicate" ElementId="Lowercase" StringId="HelpText">a lowercase letter</LocalizedString>
<LocalizedString ElementType="Predicate" ElementId="Uppercase" StringId="HelpText">an uppercase letter</LocalizedString>
```

### <a name="inputvalidation"></a>InputValidation

O valor InputValidation é utilizado para localização de uma das mensagens de erro do grupo [PredicateValidation.](predicates.md) 

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

O exemplo a seguir mostra como localização de um grupo de validação predicado ajuda texto.

```xml
<LocalizedString ElementType="InputValidation" ElementId="CustomPassword" StringId="CharacterClasses">The password must have at least 3 of the following:</LocalizedString>
```

### <a name="uxelement"></a>UxElement

O valor UxElement é utilizado para localizar um dos elementos de interface do utilizador. O exemplo a seguir mostra como localizar os botões de continuar e cancelar.

```xml
<LocalizedString ElementType="UxElement" StringId="button_continue">Create new account</LocalizedString>
<LocalizedString ElementType="UxElement" StringId="button_cancel">Cancel</LocalizedString>
```

### <a name="displaycontrol"></a>DisplayControl

O valor DisplayControl é utilizado para localizar um dos elementos de interface do utilizador do controlo do [ecrã.](display-controls.md) Quando ativado, o controlo de visualização localizadoStrings assume a **precedência*** sobre alguns dos _ *UxElement** StringIDs como **ver_but_send**, **ver_but_edit,** **ver_but_resend** e **ver_but_verify**. O exemplo a seguir mostra como localizar os botões de envio e verificação. 

```xml
<LocalizedString ElementType="DisplayControl" ElementId="emailVerificationControl" StringId="but_send_code">Send verification code</LocalizedString>
<LocalizedString ElementType="DisplayControl" ElementId="emailVerificationControl" StringId="but_verify_code">Verify code</LocalizedString>
```

Na secção de metadados de um perfil técnico autoafirmado, a definição de conteúdo referenciada necessita de ter DataUri definido para a versão de layout da [página](page-layout.md) 2.1.0 ou superior. Por exemplo:

```xml
<ContentDefinition Id="api.selfasserted">
  <DataUri>urn:com:microsoft:aad:b2c:elements:selfasserted:2.1.0</DataUri>
  ...
```

## <a name="next-steps"></a>Passos seguintes

Consulte os seguintes artigos para exemplos de localização:

- [Personalização linguística com política personalizada no Azure Ative Directory B2C](language-customization.md)
- [Personalização linguística com fluxos de utilizadores no Azure Ative Directory B2C](language-customization.md)
