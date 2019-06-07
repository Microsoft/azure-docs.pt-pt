---
title: Localização – o Azure Active Directory B2C | Documentos da Microsoft
description: Especifique o elemento de localização de uma política personalizada no Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: a02983c5019870e8b17db48184b2f238a82f8a40
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/04/2019
ms.locfileid: "66510584"
---
# <a name="localization"></a>Localização

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

O **localização** elemento permite-lhe dar suporte a várias localidades ou idiomas na política para as jornadas de utilizador. O suporte de localização em políticas permite-lhe:

- Configurar a lista explícita dos idiomas com suporte de uma política e escolha um idioma padrão.
- Forneça cadeias de caracteres do idioma específico e coleções.

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

O **localização** elemento contém os seguintes atributos:

| Atributo | Necessário | Descrição |
| --------- | -------- | ----------- |
| Enabled | Não | Valores possíveis: `true` ou `false`. |

O **localização** elemento contém seguintes elementos XML

| Elemento | Ocorrências | Descrição |
| ------- | ----------- | ----------- |
| SupportedLanguages | 1:n | Lista de idiomas suportados. | 
| LocalizedResources | 0:n | Lista de recursos localizados. |

## <a name="supportedlanguages"></a>SupportedLanguages

O **SupportedLanguages** elemento contém os seguintes atributos:

| Atributo | Necessário | Descrição |
| --------- | -------- | ----------- |
| DefaultLanguage | Sim | O idioma a ser utilizada como predefinição para recursos localizados. |
| MergeBehavior | Não | Um valores de enumeração de valores que são mesclados em conjunto com qualquer ClaimType presente uma política de principal com o mesmo identificador. Use este atributo quando substituir uma afirmação especificada na política base. Valores possíveis: `Append`, `Prepend`, ou `ReplaceAll`. O `Append` valor Especifica que a recolha de dados presentes deve ser acrescentada ao fim da coleção especificada na política de principal. O `Prepend` valor Especifica que a recolha de dados presentes deve ser adicionada antes da coleção especificada na política do principal. O `ReplaceAll` valor Especifica que a recolha de dados definidos na política de principal deve ser ignorada, usando, em vez disso, os dados definidos na política atual. |

### <a name="supportedlanguages"></a>SupportedLanguages

O **SupportedLanguages** elemento contém os seguintes elementos:

| Elemento | Ocorrências | Descrição |
| ------- | ----------- | ----------- |
| SupportedLanguage | 1:n | Apresenta o conteúdo que está em conformidade com a uma etiqueta de idioma por RFC 5646 - etiquetas para identificar idiomas. | 

## <a name="localizedresources"></a>LocalizedResources

O **LocalizedResources** elemento contém os seguintes atributos:

| Atributo | Necessário | Descrição |
| --------- | -------- | ----------- |
| Id | Sim | Um identificador que é utilizado para identificar exclusivamente os recursos localizados. |

O **LocalizedResources** elemento contém os seguintes elementos:

| Elemento | Ocorrências | Descrição |
| ------- | ----------- | ----------- |
| LocalizedCollections | 0:n | Define coleções inteiras em várias culturas. Uma coleção pode ter um número diferente de itens e as cadeias de caracteres diferentes para várias culturas. Exemplos de coleções incluem as enumerações que aparecem em tipos de afirmação. Por exemplo, é apresentada uma lista de país/região para o usuário numa lista pendente. |
| LocalizedStrings | 0:n | Define todas as cadeias de caracteres, exceto essas cadeias de caracteres que aparecem em coleções, em várias culturas. |

### <a name="localizedcollections"></a>LocalizedCollections

O **LocalizedCollections** elemento contém os seguintes elementos:

| Elemento | Ocorrências | Descrição |
| ------- | ----------- | ----------- |
| LocalizedCollection | 1:n | Lista de idiomas suportados. |

#### <a name="localizedcollection"></a>LocalizedCollection

O **LocalizedCollection** elemento contém os seguintes atributos:

| Atributo | Necessário | Descrição |
| --------- | -------- | ----------- |
| ElementType | Sim | Referencia um elemento de ClaimType ou um elemento de interface de utilizador no ficheiro de política. |
| ElementId | Sim | Uma cadeia que contém uma referência a um tipo de afirmação já definidos na secção ClaimsSchema que é utilizada se **ElementType** está definido como um ClaimType. |
| TargetCollection | Sim | A coleção de destino. |

O **LocalizedCollection** elemento contém os seguintes elementos:

| Elemento | Ocorrências | Descrição |
| ------- | ----------- | ----------- |
| Item | 0:n | Define uma opção disponível para o utilizador selecionar para uma afirmação na interface do usuário, como um valor numa lista pendente. |

O **Item** elemento contém os seguintes atributos:

| Atributo | Necessário | Descrição |
| --------- | -------- | ----------- |
| Text | Sim | A cadeia de apresentação amigável de utilizador que deve ser apresentada ao usuário na interface do usuário para esta opção. |
| Value | Sim | A cadeia de caracteres de afirmação valor associado a seleção desta opção. |

O exemplo seguinte mostra a utilização do **LocalizedCollections** elemento. Ele contém dois **LocalizedCollection** elementos, um para inglês e outro para espanhol. Ambos definidos os **restrição** coleção da afirmação `Gender` com uma lista de itens para inglês e espanhol.

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

### <a name="localizedstrings"></a>LocalizedStrings

O **LocalizedStrings** elemento contém os seguintes elementos:

| Elemento | Ocorrências | Descrição |
| ------- | ----------- | ----------- |
| LocalizedString | 1:n | Uma cadeia de caracteres localizada. |

O **LocalizedString** elemento contém os seguintes atributos:

| Atributo | Necessário | Descrição |
| --------- | -------- | ----------- |
| ElementType | Sim | Uma referência a um elemento do tipo de afirmação ou um elemento de interface do usuário na política. Valores possíveis: `ClaimType`, `UxElement`, `ErrorMessage`, `Predicate`, ou. O `ClaimType` valor é utilizado para localizar um dos atributos de afirmação, conforme especificado no StringId. O `UxElement` valor é utilizado para localizar um dos elementos de interface de utilizador conforme especificado no StringId. O `ErrorMessage` valor é utilizado para localizar uma das mensagens de erro de sistema conforme especificado no StringId. O `Predicate` valor é utilizado para localizar um da [predicado](predicates.md) mensagens de erro, conforme especificado no StringId. O `InputValidation` valor é utilizado para localizar um da [PredicateValidation](predicates.md) mensagens de erro, conforme especificado no StringId de grupo. |
| ElementId | Sim | Se **ElementType** está definida como `ClaimType`, `Predicate`, ou `InputValidation`, esse elemento contém uma referência a um tipo de afirmação já definido na secção ClaimsSchema. | 
| StringId | Sim | Se **ElementType** está definida como `ClaimType`, esse elemento contém uma referência a um atributo de um tipo de afirmação. Valores possíveis: `DisplayName`, `AdminHelpText`, ou `PatternHelpText`. O `DisplayName` valor é usado para definir o nome de exibição de afirmação. O `AdminHelpText` valor é utilizado para definir o nome de texto de ajuda do utilizador de afirmação. O `PatternHelpText` valor é utilizado para definir o texto de ajuda do padrão de afirmação. Se **ElementType** está definida como `UxElement`, esse elemento contém uma referência a um atributo de um elemento de interface do usuário. Se **ElementType** está definida como `ErrorMessage`, esse elemento Especifica o identificador de uma mensagem de erro. Ver [cadeia de caracteres de localização IDs](localization-string-ids.md) para obter uma lista completa da `UxElement` identificadores.|


O exemplo seguinte mostra uma página de inscrição localizada. Os três primeiros **LocalizedString** valores definir o atributo de afirmação. A terceira altera o valor do botão continuar. A última que foi alterado a mensagem de erro.

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

O exemplo seguinte mostra um localizada a **UserHelpText** dos **predicado** com o Id `IsLengthBetween8And64`. E um localizada **UserHelpText** dos **PredicateGroup** com o Id `CharacterClasses` de **PredicateValidation** com o Id `StrongPassword`.

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

## <a name="set-up-localization"></a>Configurar localização

Este artigo mostra-lhe como suportar várias localidades ou idiomas na política para jornadas de utilizador. Localização requer três passos: configuração explícita lista dos idiomas com suporte, forneça cadeias de caracteres do idioma específico e coleções e editar ContentDefinition para a página.

### <a name="set-up-the-explicit-list-of-supported-languages"></a>Configurar a lista explícita de idiomas suportados

Sob o **BuildingBlocks** elemento, adicione o **localização** elemento com a lista de idiomas suportados. O exemplo seguinte mostra como definir o suporte de localização para inglês (predefinição) e espanhol:

```XML
<Localization Enabled="true">
  <SupportedLanguages DefaultLanguage="en" MergeBehavior="ReplaceAll">
    <SupportedLanguage>en</SupportedLanguage>
    <SupportedLanguage>es</SupportedLanguage>
  </SupportedLanguages>
</Localization>
```

### <a name="provide-language-specific-strings-and-collections"></a>Forneça cadeias de caracteres do idioma específico e coleções 

Adicione **LocalizedResources** elementos dentro do **localização** elemento após o fecho do **SupportedLanguages** elemento. Adiciona **LocalizedResources** elementos para cada página (definição de conteúdo) e qualquer linguagem que pretende suportar. Para personalizar a página de inscrição ou início de sessão unificada, páginas de inscrição e o multi-factor authentication (MFA) para inglês, espanhol e França, adicione as seguintes **LocalizedResources** elementos.  
- A unificação da página de inscrição ou início de sessão, inglês `<LocalizedResources Id="api.signuporsignin.en">`
- A unificação da página de inscrição ou início de sessão, espanhol `<LocalizedResources Id="api.signuporsignin.es">`
- A unificação da página de inscrição ou início de sessão, França `<LocalizedResources Id="api.signuporsignin.fr">` 
- Inscreva-se, em inglês `<LocalizedResources Id="api.localaccountsignup.en">`
- Inscreva-se, espanhol `<LocalizedResources Id="api.localaccountsignup.es">`
- Inscreva-se, França `<LocalizedResources Id="api.localaccountsignup.fr">`
- MFA, inglês `<LocalizedResources Id="api.phonefactor.en">`
- MFA, espanhol `<LocalizedResources Id="api.phonefactor.es">`
- MFA, França `<LocalizedResources Id="api.phonefactor.fr">`

Cada **LocalizedResources** elemento contém todo o necessário **LocalizedStrings** elementos com múltiplos **LocalizedString** elementos e  **LocalizedCollections** elementos com múltiplos **LocalizedCollection** elementos.  O exemplo seguinte adiciona a localização em inglês de página de inscrição: 

Nota: Este exemplo faz uma referência ao `Gender` e `City` tipos de afirmação. Para utilizar neste exemplo, certifique-se de que define essas declarações. Para obter mais informações, consulte [ClaimsSchema](claimsschema.md).

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

### <a name="edit-the-contentdefinition-for-the-page"></a>Editar ContentDefinition para a página 

Para cada página que deseja localizar, especifique os códigos de idioma devem ser procuradas na **ContentDefinition**.

No exemplo a seguir, inglês (en) e as cadeias de caracteres personalizadas Espanhol (es) são adicionadas à página de inscrição. O **LocalizedResourcesReferenceId** para cada **LocalizedResourcesReference** é o mesmo que a Localidade, mas pode usar qualquer cadeia de caracteres como o identificador. Para cada combinação de idioma e página, apontar para o correspondente **LocalizedResources** que criou anteriormente.

```XML
<ContentDefinition Id="api.localaccountsignup">
...
  <LocalizedResourcesReferences MergeBehavior="Prepend">
    <LocalizedResourcesReference Language="en" LocalizedResourcesReferenceId="api.localaccountsignup.en" />
    <LocalizedResourcesReference Language="es" LocalizedResourcesReferenceId="api.localaccountsignup.es" />
  </LocalizedResourcesReferences>
</ContentDefinition>
```

O exemplo seguinte mostra o XML final:

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




