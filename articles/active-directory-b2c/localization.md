---
title: Localização - Diretório Ativo Azure B2C
description: Especifique o elemento de localização de uma política personalizada no Diretório Ativo Azure B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/09/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 5dcbc2ee35620d0a29c495b24bd3756769095a17
ms.sourcegitcommit: 3616b42a0d6bbc31b965995d861930e53d2cf0d3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/09/2020
ms.locfileid: "78933004"
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
| Ativado | Não | Valores possíveis: `true` ou `false`. |

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
| Função Função Behavior | Não | Uma enumeração dos valores que são fundidos juntamente com qualquer ClaimType presente numa política-mãe com o mesmo identificador. Use este atributo quando substituir uma reclamação especificada na política de base. Valores possíveis: `Append`, `Prepend`ou `ReplaceAll`. O valor `Append` especifica que a recolha de dados presentes deve ser anexada ao fim da recolha especificada na política-mãe. O valor `Prepend` especifica que a recolha dos dados presentes deve ser adicionada antes da recolha especificada na política-mãe. O valor `ReplaceAll` especifica que a recolha de dados definida na política-mãe deve ser ignorada, utilizando, em vez disso, os dados definidos na política atual. |

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
| ElementId | Sim | Uma cadeia que contém uma referência a um tipo de reclamação já definido na secção ClaimsSchema que é usada se o **ElementType** for definido para um ClaimType. |
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

O exemplo seguinte mostra a utilização do elemento **Coleções Localizadas.** Contém dois elementos **LocalizadoSCollection,** um para inglês e outro para espanhol. Ambos definiram a coleção **restrição** da reclamação `Gender` com uma lista de itens para inglês e espanhol.

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
| ElementType | Sim | Uma referência a um elemento do tipo de reclamação ou a um elemento de interface do utilizador na política. Valores possíveis: `ClaimType`, `UxElement`, `ErrorMessage`, `Predicate`ou `GetLocalizedStringsTransformationClaimType`. O valor `ClaimType` é utilizado para localizar um dos atributos de reclamação, conforme especificado no StringId. O valor `UxElement` é utilizado para localizar um dos elementos da interface do utilizador, conforme especificado no StringId. O valor `ErrorMessage` é utilizado para localizar uma das mensagens de erro do sistema, conforme especificado no StringId. O valor `Predicate` é utilizado para localizar uma das mensagens de erro [predicadas,](predicates.md) conforme especificado no StringId. O valor `InputValidation` é utilizado para localizar uma das mensagens de erro do grupo [PredicateValidação,](predicates.md) conforme especificado no StringId. O valor `GetLocalizedStringsTransformationClaimType` é usado para copiar cordas localizadas em sinistros. Para mais informações, consulte [GetLocalizedStringsTransformation reivindica a transformação](string-transformations.md#getlocalizedstringstransformation)  | 
| ElementId | Sim | Se o **ElementType** for definido para `ClaimType`, `Predicate`, ou `InputValidation`, este elemento contém uma referência a um tipo de reclamação já definido na secção ClaimsSchema. |
| StringId | Sim | Se o **ElementType** estiver definido para `ClaimType`, este elemento contém uma referência a um atributo de um tipo de reclamação. Valores possíveis: `DisplayName`, `AdminHelpText`ou `PatternHelpText`. O valor `DisplayName` é utilizado para definir o nome do display de reclamação. O valor `AdminHelpText` é utilizado para definir o nome de texto de ajuda do utilizador da reclamação. O valor `PatternHelpText` é usado para definir o texto de ajuda do padrão de reclamação. Se o **ElementType** for definido para `UxElement`, este elemento contém uma referência a um atributo de um elemento de interface do utilizador. Se o **ElementType** estiver definido para `ErrorMessage`, este elemento especifica o identificador de uma mensagem de erro. Consulte [as identificações](localization-string-ids.md) de cordas de localização para obter uma lista completa dos identificadores `UxElement`.|


O exemplo seguinte mostra uma página de inscrição localizada. Os três primeiros valores **da Configuração da Cadeia Localizada** definiram o atributo de reclamação. O terceiro altera o valor do botão de continuação. O último muda a mensagem de erro.

```XML
<LocalizedResources Id="api.selfasserted.en">
  <LocalizedStrings>
    <LocalizedString ElementType="ClaimType" ElementId="email" StringId="DisplayName">Email</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="email" StringId="UserHelpText">Please enter your email</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="email" StringId="PatternHelpText">Please enter a valid email address</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="button_continue">Create new account</LocalizedString>
   <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimsPrincipalAlreadyExists">The account you are trying to create already exists, please sign-in.</LocalizedString>
  </LocalizedStrings>
</LocalizedResources>
```

O exemplo seguinte mostra um **UserHelpText** de **Predicado** localizado com id `IsLengthBetween8And64`. E um **UserHelpText** localizado do **PredicadoGroup** com id `CharacterClasses` de **PredicadoValidação** com id `StrongPassword`.

```XML
<PredicateValidation Id="StrongPassword">
  <PredicateGroups>
    ...
    <PredicateGroup Id="CharacterClasses">
    ...
    </PredicateGroup>
  </PredicateGroups>
</PredicateValidation>

...

<Predicate Id="IsLengthBetween8And64" Method="IsLengthRange">
  ...
</Predicate>
...


<LocalizedString ElementType="InputValidation" ElementId="StrongPassword" StringId="CharacterClasses">The password must have at least 3 of the following:</LocalizedString>

<LocalizedString ElementType="Predicate" ElementId="IsLengthBetween8And64" StringId="HelpText">The password must be between 8 and 64 characters.</LocalizedString>
```

## <a name="set-up-localization"></a>Configurar a localização

Este artigo mostra-lhe como apoiar vários locais ou idiomas na política para viagens de utilizador. A localização requer três passos: configurar a lista explícita das línguas suportadas, fornecer cordas e coleções específicas da linguagem e editar a Definição de Conteúdo para a página.

### <a name="set-up-the-explicit-list-of-supported-languages"></a>Configurar a lista explícita de línguas apoiadas

Sob o elemento **BuildingBlocks,** adicione o elemento **Localização** com a lista de idiomas suportados. O exemplo que se segue mostra como definir o suporte de localização tanto para inglês (padrão) como para espanhol:

```XML
<Localization Enabled="true">
  <SupportedLanguages DefaultLanguage="en" MergeBehavior="ReplaceAll">
    <SupportedLanguage>en</SupportedLanguage>
    <SupportedLanguage>es</SupportedLanguage>
  </SupportedLanguages>
</Localization>
```

### <a name="provide-language-specific-strings-and-collections"></a>Fornecer cordas e coleções específicas da linguagem

Adicione elementos **de Recursos Localizados** dentro do elemento **localização** após o encerramento do elemento **SupportEdLanguages.** Adiciona elementos **De Recursos Localizados** para cada página (definição de conteúdo) e qualquer idioma que queira suportar. Para personalizar as páginas unificadas de inscrição ou inscrição, inscrição e autenticação de vários fatores (MFA) para inglês, espanhol e França, adiciona os seguintes elementos De **Recursos Localizados.**

- Página unificada de inscrição ou inscrição, inglês `<LocalizedResources Id="api.signuporsignin.en">`
- Página unificada de inscrição ou inscrição, espanhol `<LocalizedResources Id="api.signuporsignin.es">`
- Página unificada de inscrição ou inscrição, França `<LocalizedResources Id="api.signuporsignin.fr">`
- Inscrições, `<LocalizedResources Id="api.localaccountsignup.en">` Inglesa
- Inscrições, `<LocalizedResources Id="api.localaccountsignup.es">` espanholas
- Inscrições, França `<LocalizedResources Id="api.localaccountsignup.fr">`
- MFA, `<LocalizedResources Id="api.phonefactor.en">` inglesa
- MFA, `<LocalizedResources Id="api.phonefactor.es">` espanhola
- MFA, França `<LocalizedResources Id="api.phonefactor.fr">`

Cada elemento **LocalizedResources** contém todos os elementos de **Strings Localizados** necessários com vários elementos de **String localizados** e elementos **Localizados Collections** com vários elementos localizados.  O exemplo seguinte adiciona a página de inscrição localização inglesa:

Nota: Este exemplo faz referência aos tipos de `Gender` e `City` reivindicação. Para utilizar este exemplo, certifique-se de definir essas reivindicações. Para mais informações, consulte [ClaimsSchema](claimsschema.md).

```XML
<LocalizedResources Id="api.localaccountsignup.en">

 <LocalizedCollections>
   <LocalizedCollection ElementType="ClaimType" ElementId="Gender" TargetCollection="Restriction">
      <Item Text="Female" Value="F" />
      <Item Text="Male" Value="M" />
    </LocalizedCollection>
   <LocalizedCollection ElementType="ClaimType" ElementId="City" TargetCollection="Restriction">
      <Item Text="New York" Value="NY" />
      <Item Text="Paris" Value="Paris" />
      <Item Text="London" Value="London" />
    </LocalizedCollection>
  </LocalizedCollections>

  <LocalizedStrings>
    <LocalizedString ElementType="ClaimType" ElementId="email" StringId="DisplayName">Email</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="email" StringId="UserHelpText">Please enter your email</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="email" StringId="PatternHelpText">Please enter a valid email address</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="button_continue">Create new account</LocalizedString>
   <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimsPrincipalAlreadyExists">The account you are trying to create already exists, please sign-in.</LocalizedString>
  </LocalizedStrings>
</LocalizedResources>
```

A localização da página de inscrição para espanhol.

```XML
<LocalizedResources Id="api.localaccountsignup.es">

 <LocalizedCollections>
   <LocalizedCollection ElementType="ClaimType" ElementId="Gender" TargetCollection="Restriction">
      <Item Text="Femenino" Value="F" />
      <Item Text="Masculino" Value="M" />
    </LocalizedCollection>
   <LocalizedCollection ElementType="ClaimType" ElementId="City" TargetCollection="Restriction">
      <Item Text="Nueva York" Value="NY" />
      <Item Text="París" Value="Paris" />
      <Item Text="Londres" Value="London" />
    </LocalizedCollection>
  </LocalizedCollections>

  <LocalizedStrings>
    <LocalizedString ElementType="ClaimType" ElementId="email" StringId="DisplayName">Dirección de correo electrónico</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="email" StringId="UserHelpText">Dirección de correo electrónico que puede usarse para ponerse en contacto con usted.</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="email" StringId="PatternHelpText">Introduzca una dirección de correo electrónico.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="button_continue">Crear</LocalizedString>
   <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimsPrincipalAlreadyExists">Ya existe un usuario con el id. especificado. Elija otro diferente.</LocalizedString>
  </LocalizedStrings>
</LocalizedResources>
```

### <a name="edit-the-contentdefinition-for-the-page"></a>Editar a Definição de Conteúdo para a página

Para cada página que pretende localizar, especifique os códigos linguísticos a procurar na **Definição**de Conteúdo .

No exemplo seguinte, as cordas personalizadas inglesas (en) e espanholas (es) são adicionadas à página de inscrição. O **LocalizedResourcesReferenceId** para cada Referência de **Recursos Localizados** é o mesmo que o local, mas pode utilizar qualquer cadeia como identificador. Para cada combinação de idiomas e páginas, aponta para os **Recursos Localizados correspondentes** que criou anteriormente.

```XML
<ContentDefinition Id="api.localaccountsignup">
...
  <LocalizedResourcesReferences MergeBehavior="Prepend">
    <LocalizedResourcesReference Language="en" LocalizedResourcesReferenceId="api.localaccountsignup.en" />
    <LocalizedResourcesReference Language="es" LocalizedResourcesReferenceId="api.localaccountsignup.es" />
  </LocalizedResourcesReferences>
</ContentDefinition>
```

O exemplo que se segue mostra o XML final:

```XML
<BuildingBlocks>
  <ContentDefinitions>
    <ContentDefinition Id="api.localaccountsignup">
      <!-- Other content definitions elements... -->
      <LocalizedResourcesReferences MergeBehavior="Prepend">
         <LocalizedResourcesReference Language="en" LocalizedResourcesReferenceId="api.localaccountsignup.en" />
         <LocalizedResourcesReference Language="es" LocalizedResourcesReferenceId="api.localaccountsignup.es" />
      </LocalizedResourcesReferences>
    </ContentDefinition>
    <!-- More content definitions... -->
  </ContentDefinitions>

  <Localization Enabled="true">

    <SupportedLanguages DefaultLanguage="en" MergeBehavior="ReplaceAll">
      <SupportedLanguage>en</SupportedLanguage>
      <SupportedLanguage>es</SupportedLanguage>
      <!-- More supported language... -->
    </SupportedLanguages>

    <LocalizedResources Id="api.localaccountsignup.en">
      <LocalizedCollections>
        <LocalizedCollection ElementType="ClaimType" ElementId="Gender" TargetCollection="Restriction">
          <Item Text="Female" Value="F" />
          <Item Text="Male" Value="M" />
          <!-- More items... -->
        </LocalizedCollection>
        <LocalizedCollection ElementType="ClaimType" ElementId="City" TargetCollection="Restriction">
          <Item Text="New York" Value="NY" />
          <Item Text="Paris" Value="Paris" />
          <Item Text="London" Value="London" />
        </LocalizedCollection>
        <!-- More localized collections... -->
      </LocalizedCollections>
      <LocalizedStrings>
        <LocalizedString ElementType="ClaimType" ElementId="email" StringId="DisplayName">Email</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="email" StringId="UserHelpText">Please enter your email</LocalizedString>
        <LocalizedString ElementType="ClaimType" ElementId="email" StringId="PatternHelpText">Please enter a valid email address</LocalizedString>
        <LocalizedString ElementType="UxElement" StringId="button_continue">Create new account</LocalizedString>
       <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimsPrincipalAlreadyExists">The account you are trying to create already exists, please sign-in.</LocalizedString>
        <!-- More localized strings... -->
      </LocalizedStrings>
    </LocalizedResources>

    <LocalizedResources Id="api.localaccountsignup.es">
      <LocalizedCollections>
       <LocalizedCollection ElementType="ClaimType" ElementId="Gender" TargetCollection="Restriction">
          <Item Text="Femenino" Value="F" />
          <Item Text="Masculino" Value="M" />
        </LocalizedCollection>
        <LocalizedCollection ElementType="ClaimType" ElementId="City" TargetCollection="Restriction">
          <Item Text="Nueva York" Value="NY" />
          <Item Text="París" Value="Paris" />
          <Item Text="Londres" Value="London" />
        </LocalizedCollection>
      </LocalizedCollections>
      <LocalizedStrings>
        <LocalizedString ElementType="ClaimType" ElementId="email" StringId="DisplayName">Dirección de correo electrónico</LocalizedString>
        <LocalizedString ElementType="ClaimType" ElementId="email" StringId="UserHelpText">Dirección de correo electrónico que puede usarse para ponerse en contacto con usted.</LocalizedString>
        <LocalizedString ElementType="ClaimType" ElementId="email" StringId="PatternHelpText">Introduzca una dirección de correo electrónico.</LocalizedString>
        <LocalizedString ElementType="UxElement" StringId="button_continue">Crear</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimsPrincipalAlreadyExists">Ya existe un usuario con el id. especificado. Elija otro diferente.</LocalizedString>
      </LocalizedStrings>
    </LocalizedResources>
    <!-- More localized resources... -->
  </Localization>
</BuildingBlocks>
```
