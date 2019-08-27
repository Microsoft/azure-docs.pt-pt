---
title: Azure Active Directory B2C de localização
description: Especifique o elemento de localização de uma política personalizada em Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 08/27/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: ec9b4e7ce761d524d047f4d12cab9e5b782e6032
ms.sourcegitcommit: 94ee81a728f1d55d71827ea356ed9847943f7397
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/26/2019
ms.locfileid: "70033452"
---
# <a name="localization"></a>Localização

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

O elemento de **localização** permite que você ofereça suporte a várias localidades ou idiomas na política para os percursos do usuário. O suporte à localização em políticas permite que você:

- Configure a lista explícita dos idiomas com suporte em uma política e escolha um idioma padrão.
- Forneça cadeias de caracteres e coleções específicas a um idioma.

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

O elemento de **localização** contém os seguintes atributos:

| Atributo | Requerido | Descrição |
| --------- | -------- | ----------- |
| Enabled | Não | Valores possíveis: `true` ou `false`. |

O elemento de **localização** contém os seguintes elementos XML

| Elemento | Ocorrências | Descrição |
| ------- | ----------- | ----------- |
| SupportedLanguages | 1: n | Lista de idiomas com suporte. |
| LocalizedResources | 0: n | Lista de recursos localizados. |

## <a name="supportedlanguages"></a>SupportedLanguages

O elemento **SupportedLanguages** contém os seguintes atributos:

| Atributo | Requerido | Descrição |
| --------- | -------- | ----------- |
| DefaultLanguage | Sim | O idioma a ser usado como o padrão para recursos localizados. |
| MergeBehavior | Não | Valores de enumeração de valores que são mesclados junto com qualquer ClaimType presente em uma política pai com o mesmo identificador. Use esse atributo quando você substituir uma declaração especificada na política de base. Valores possíveis: `Append`, `Prepend`ou `ReplaceAll`. O `Append` valor especifica que a coleção de dados presentes deve ser anexada ao final da coleção especificada na política pai. O `Prepend` valor especifica que a coleção de dados presentes deve ser adicionada antes da coleção especificada na política pai. O `ReplaceAll` valor especifica que a coleção de dados definida na política pai deve ser ignorada, usando em vez disso os dados definidos na política atual. |

### <a name="supportedlanguages"></a>SupportedLanguages

O elemento **SupportedLanguages** contém os seguintes elementos:

| Elemento | Ocorrências | Descrição |
| ------- | ----------- | ----------- |
| SupportedLanguage | 1: n | Exibe o conteúdo que está em conformidade com uma marca de idioma por RFC 5646 – marcas para identificar idiomas. |

## <a name="localizedresources"></a>LocalizedResources

O elemento **LocalizedResources** contém os seguintes atributos:

| Atributo | Requerido | Descrição |
| --------- | -------- | ----------- |
| ID | Sim | Um identificador que é usado para identificar exclusivamente os recursos localizados. |

O elemento **LocalizedResources** contém os seguintes elementos:

| Elemento | Ocorrências | Descrição |
| ------- | ----------- | ----------- |
| LocalizedCollections | 0: n | Define coleções inteiras em várias culturas. Uma coleção pode ter um número diferente de itens e diferentes cadeias de caracteres para várias culturas. Exemplos de coleções incluem as enumerações que aparecem em tipos de declaração. Por exemplo, uma lista de país/região é mostrada para o usuário em uma lista suspensa. |
| LocalizedStrings | 0: n | Define todas as cadeias de caracteres, exceto aquelas que aparecem em coleções, em várias culturas. |

### <a name="localizedcollections"></a>LocalizedCollections

O elemento **LocalizedCollections** contém os seguintes elementos:

| Elemento | Ocorrências | Descrição |
| ------- | ----------- | ----------- |
| Localizadacollection | 1: n | Lista de idiomas com suporte. |

#### <a name="localizedcollection"></a>Localizadacollection

O elemento **localizado** contém os seguintes atributos:

| Atributo | Requerido | Descrição |
| --------- | -------- | ----------- |
| ElementType | Sim | Faz referência a um elemento ClaimType ou um elemento de interface do usuário no arquivo de política. |
| ElementId | Sim | Uma cadeia de caracteres que contém uma referência a um tipo de declaração já definida na seção ClaimsSchema que será usada se **ElementType** estiver definida como um ClaimType. |
| TargetCollection | Sim | A coleção de destino. |

O elemento **localizado** contém os seguintes elementos:

| Elemento | Ocorrências | Descrição |
| ------- | ----------- | ----------- |
| Item | 0: n | Define uma opção disponível para o usuário selecionar para uma declaração na interface do usuário, como um valor em uma lista suspensa. |

O elemento **Item** contém os seguintes atributos:

| Atributo | Requerido | Descrição |
| --------- | -------- | ----------- |
| Text | Sim | A cadeia de caracteres de exibição amigável que deve ser mostrada para o usuário na interface do usuário para essa opção. |
| Value | Sim | O valor de declaração da cadeia de caracteres associado à seleção dessa opção. |
| SelectByDefault | Não | Indica se essa opção deve ou não ser selecionada por padrão na interface do usuário. Valores possíveis: VERDADEIRO ou FALSO. |

O exemplo a seguir mostra o uso do elemento **LocalizedCollections** . Ele contém dois elementos localizadas, um para inglês e outro para espanhol. Ambas definem a coleção de **restrições** da `Gender` declaração com uma lista de itens para inglês e espanhol.

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

O elemento **LocalizedStrings** contém os seguintes elementos:

| Elemento | Ocorrências | Descrição |
| ------- | ----------- | ----------- |
| Localizadastring | 1: n | Uma cadeia de caracteres localizada. |

O elemento localizadastring contém os seguintes atributos:

| Atributo | Requerido | Descrição |
| --------- | -------- | ----------- |
| ElementType | Sim | Uma referência a um elemento de tipo de declaração ou a um elemento de interface do usuário na política. Valores possíveis: `ClaimType` `UxElement` ,`Predicate`,, ou. `ErrorMessage` O `ClaimType` valor é usado para localizar um dos atributos de declaração, conforme especificado na stringid. O `UxElement` valor é usado para localizar um dos elementos da interface do usuário, conforme especificado na stringid. O `ErrorMessage` valor é usado para localizar uma das mensagens de erro do sistema, conforme especificado na stringid. O `Predicate` valor é usado para localizar uma das mensagens [](predicates.md) de erro de predicado, conforme especificado na stringid. O `InputValidation` valor é usado para localizar uma das mensagens de erro do grupo [PredicateValidation](predicates.md) conforme especificado na stringid. |
| ElementId | Sim | Se **ElementType** for definido como `ClaimType`, `Predicate`ou `InputValidation`, esse elemento conterá uma referência a um tipo de declaração já definido na seção ClaimsSchema. |
| StringId | Sim | Se **ElementType** for definido como `ClaimType`, esse elemento conterá uma referência a um atributo de um tipo de declaração. Valores possíveis: `DisplayName`, `AdminHelpText`ou `PatternHelpText`. O `DisplayName` valor é usado para definir o nome de exibição da declaração. O `AdminHelpText` valor é usado para definir o nome do texto de ajuda do usuário da declaração. O `PatternHelpText` valor é usado para definir o texto de ajuda padrão de declaração. Se **ElementType** for definido como `UxElement`, esse elemento conterá uma referência a um atributo de um elemento de interface do usuário. Se **ElementType** for definido como `ErrorMessage`, esse elemento especificará o identificador de uma mensagem de erro. Consulte [IDs de cadeia de caracteres de localização](localization-string-ids.md) para obter `UxElement` uma lista completa dos identificadores.|


O exemplo a seguir mostra uma página de inscrição localizada. Os três primeiros valores de localizadastring definem o atributo de declaração. O terceiro altera o valor do botão continuar. O último altera a mensagem de erro.

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

O exemplo a seguir mostra uma localização de userhelptext do predicado com ID. `IsLengthBetween8And64` E um userhelptext localizado de **Predicate** com `CharacterClasses` ID de **PredicateValidation** com ID `StrongPassword`.

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

Este artigo mostra como dar suporte a várias localidades ou idiomas na política para viagens do usuário. A localização requer três etapas: configurar a lista explícita dos idiomas com suporte, fornecer cadeias de caracteres e coleções específicas do idioma e editar o ContentDefinition da página.

### <a name="set-up-the-explicit-list-of-supported-languages"></a>Configurar a lista explícita de idiomas com suporte

No elemento **BuildingBlocks** , adicione o elemento de **localização** com a lista de idiomas com suporte. O exemplo a seguir mostra como definir o suporte à localização para inglês (padrão) e espanhol:

```XML
<Localization Enabled="true">
  <SupportedLanguages DefaultLanguage="en" MergeBehavior="ReplaceAll">
    <SupportedLanguage>en</SupportedLanguage>
    <SupportedLanguage>es</SupportedLanguage>
  </SupportedLanguages>
</Localization>
```

### <a name="provide-language-specific-strings-and-collections"></a>Fornecer cadeias de caracteres e coleções específicas a um idioma

Adicione elementos **LocalizedResources** dentro do elemento de **localização** após o fechamento do elemento **SupportedLanguages** . Você adiciona elementos **LocalizedResources** para cada página (definição de conteúdo) e qualquer idioma ao qual você deseja dar suporte. Para personalizar a página de inscrição ou entrada unificada, as páginas de entrada e autenticação multifator (MFA) para inglês, espanhol e França, você adiciona os seguintes elementos **LocalizedResources** .

- Página de inscrição ou entrada unificada, inglês`<LocalizedResources Id="api.signuporsignin.en">`
- Página de inscrição ou entrada unificada, espanhol`<LocalizedResources Id="api.signuporsignin.es">`
- Página de inscrição ou entrada unificada, França`<LocalizedResources Id="api.signuporsignin.fr">`
- Inscreva-se, inglês`<LocalizedResources Id="api.localaccountsignup.en">`
- Inscreva-se, espanhol`<LocalizedResources Id="api.localaccountsignup.es">`
- Inscreva-se, França`<LocalizedResources Id="api.localaccountsignup.fr">`
- MFA, inglês`<LocalizedResources Id="api.phonefactor.en">`
- MFA, espanhol`<LocalizedResources Id="api.phonefactor.es">`
- MFA, França`<LocalizedResources Id="api.phonefactor.fr">`

Cada elemento **LocalizedResources** contém todos os elementos **LocalizedStrings** necessários com vários elementos **localizadas** e elementos **LocalizedCollections** com várias **localizadas** elementos.  O exemplo a seguir adiciona a página de inscrição em inglês:

Nota: Este exemplo faz uma referência aos `Gender` tipos `City` de declaração e. Para usar este exemplo, certifique-se de definir essas declarações. Para obter mais informações, consulte [ClaimsSchema](claimsschema.md).

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

### <a name="edit-the-contentdefinition-for-the-page"></a>Editar o ContentDefinition da página

Para cada página que você deseja localizar, especifique os códigos de idioma a serem procurados no **ContentDefinition**.

No exemplo a seguir, as cadeias de caracteres personalizadas em inglês (EN) e espanhol (es) são adicionadas à página de inscrição. O **LocalizedResourcesReferenceId** para cada **LocalizedResourcesReference** é o mesmo que sua localidade, mas você pode usar qualquer cadeia de caracteres como o identificador. Para cada combinação de idioma e página, aponte para o **LocalizedResources** correspondente que você criou anteriormente.

```XML
<ContentDefinition Id="api.localaccountsignup">
...
  <LocalizedResourcesReferences MergeBehavior="Prepend">
    <LocalizedResourcesReference Language="en" LocalizedResourcesReferenceId="api.localaccountsignup.en" />
    <LocalizedResourcesReference Language="es" LocalizedResourcesReferenceId="api.localaccountsignup.es" />
  </LocalizedResourcesReferences>
</ContentDefinition>
```

O exemplo a seguir mostra o XML final:

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
