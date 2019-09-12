---
title: ClaimsSchema-Azure Active Directory B2C | Microsoft Docs
description: Especifique o elemento ClaimsSchema de uma política personalizada em Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 132dd91ba121fc5939a0f30194fe4abdd3755414
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/12/2019
ms.locfileid: "67847040"
---
# <a name="claimsschema"></a>ClaimsSchema

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

O elemento **ClaimsSchema** define os tipos de declaração que podem ser referenciados como parte da política. Esquema de declarações é o local onde você declara suas declarações. Uma declaração pode ser nome, sobrenome, nome de exibição, número de telefone e muito mais. O elemento ClaimsSchema contém a lista de elementos ClaimType. O elemento ClaimType contém o atributo **ID** , que é o nome da declaração.

```XML
<BuildingBlocks>
  <ClaimsSchema>
    <ClaimType Id="Id">
      <DisplayName>Surname</DisplayName>
      <DataType>string</DataType>
      <DefaultPartnerClaimTypes>
        <Protocol Name="OAuth2" PartnerClaimType="family_name" />
        <Protocol Name="OpenIdConnect" PartnerClaimType="family_name" />
        <Protocol Name="SAML2" PartnerClaimType="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname" />
      </DefaultPartnerClaimTypes>
      <UserHelpText>Your surname (also known as family name or last name).</UserHelpText>
      <UserInputType>TextBox</UserInputType>
```

## <a name="claimtype"></a>ClaimType

O elemento ClaimType contém o seguinte atributo:

| Atributo | Necessário | Descrição |
| --------- | -------- | ----------- |
| ID | Sim | Um identificador que é usado para o tipo de declaração. Outros elementos podem usar esse identificador na política. |

O elemento ClaimType contém os seguintes elementos:

| Elemento | Ocorrências | Descrição |
| ------- | ----------- | ----------- |
| DisplayName | 0:1 | O título que é exibido para os usuários em várias telas. O valor pode ser [localizado](localization.md). |
| Tipo de dados | 0:1 | O tipo da declaração. Os tipos de dados Boolean, Date, dateTime, int, Long, String, StringCollection, alternativeSecurityIdCollection podem ser usados. |
| DefaultPartnerClaimTypes | 0:1 | Os tipos de declaração padrão do parceiro a serem usados para um protocolo especificado. O valor pode ser substituído no **PartnerClaimType** especificado nos elementos **InputClaim** ou **OutputClaim** . Use este elemento para especificar o nome padrão para um protocolo.  |
| Mascara | 0:1 | Uma cadeia opcional de caracteres de mascaramento que podem ser aplicados ao exibir a declaração. Por exemplo, o número de telefone 324-232-4343 pode ser mascarado como XXX-XXX-4343. |
| UserHelpText | 0:1 | Uma descrição do tipo de declaração que pode ser útil para os usuários entenderem sua finalidade. O valor pode ser [localizado](localization.md). |
| UserInputType | 0:1 | O tipo de controle de entrada que deve estar disponível para o usuário ao inserir manualmente os dados de declaração para o tipo de declaração. Consulte os tipos de entrada de usuário definidos mais adiante nesta página. |
| Restrição | 0:1 | As restrições de valor para essa declaração, como uma expressão regular (Regex) ou uma lista de valores aceitáveis. O valor pode ser [localizado](localization.md). |
PredicateValidationReference| 0:1 | Uma referência a um elemento **PredicateValidationsInput** . Os elementos **PredicateValidationReference** permitem que você execute um processo de validação para garantir que apenas os dados formados corretamente sejam inseridos. Para obter mais informações, [](predicates.md)consulte predicados. |

### <a name="defaultpartnerclaimtypes"></a>DefaultPartnerClaimTypes

O **DefaultPartnerClaimTypes** pode conter o seguinte elemento:

| Elemento | Ocorrências | Descrição |
| ------- | ----------- | ----------- |
| Protocol | 0: n | Lista de protocolos com seu nome de tipo de declaração de parceiro padrão. |

O elemento **Protocol** contém os seguintes atributos:

| Atributo | Necessário | Descrição |
| --------- | -------- | ----------- |
| Name | Sim | O nome de um protocolo válido com suporte pelo Azure AD B2C. Os valores possíveis são:  OAuth1, OAuth2, SAML2, OpenIdConnect, WsFed ou WsTrust. |
| PartnerClaimType | Sim | O nome do tipo de declaração a ser usado. |

No exemplo a seguir, quando a estrutura de experiência de identidade interage com um provedor de identidade SAML2 ou um aplicativo de terceira parte confiável, a Declaração `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`de sobrenome é mapeada para, com OpenIdConnect e OAuth2, `family_name` a declaração é mapeada para .

```XML
<ClaimType Id="surname">
  <DisplayName>Surname</DisplayName>
  <DataType>string</DataType>
  <DefaultPartnerClaimTypes>
    <Protocol Name="OAuth2" PartnerClaimType="family_name" />
    <Protocol Name="OpenIdConnect" PartnerClaimType="family_name" />
    <Protocol Name="SAML2" PartnerClaimType="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname" />
  </DefaultPartnerClaimTypes>
</ClaimType>
```

Como resultado, o token JWT emitido por Azure ad B2C emite `family_name` um nome em vez de DeclareType como **sobrenome**.

```JSON
{
  "sub": "6fbbd70d-262b-4b50-804c-257ae1706ef2",
  "auth_time": 1535013501,
  "given_name": "David",
  "family_name": "Williams",
  "name": "David Williams",
}
```

### <a name="mask"></a>Mascara

O elemento **Mask** contém os seguintes atributos:

| Atributo | Necessário | Descrição |
| --------- | -------- | ----------- |
| `Type` | Sim | O tipo da máscara de declaração. Valores possíveis: `Simple` ou `Regex`. O `Simple` valor indica que uma máscara de texto simples é aplicada à parte à esquerda de uma declaração de cadeia de caracteres. O `Regex` valor indica que uma expressão regular é aplicada à declaração de cadeia de caracteres como um todo.  Se o `Regex` valor for especificado, um atributo opcional também deverá ser definido com a expressão regular a ser usada. |
| `Regex` | Não | Se **`Type`** for definido como `Regex`, especifique a expressão regular a ser usada.

O exemplo a seguir configura uma declaração **PhoneNumber** com a `Simple` máscara:

```XML
<ClaimType Id="PhoneNumber">
  <DisplayName>Phone Number</DisplayName>
  <DataType>string</DataType>
  <Mask Type="Simple">XXX-XXX-</Mask>
  <UserHelpText>Your telephone number.</UserHelpText>
</ClaimType>
```

A estrutura de experiência de identidade renderiza o número de telefone enquanto oculta os seis primeiros dígitos:

![Declaração de número de telefone mostrada no navegador com os seis primeiros dígitos mascarados por XS](./media/claimsschema/mask.png)

O exemplo a seguir configura uma declaração **AlternateEmail** com a `Regex` máscara:

```XML
<ClaimType Id="AlternateEmail">
  <DisplayName>Please verify the secondary email linked to your account</DisplayName>
  <DataType>string</DataType>
  <Mask Type="Regex" Regex="(?&lt;=.).(?=.*@)">*</Mask>
  <UserInputType>Readonly</UserInputType>
</ClaimType>
```

A estrutura de experiência de identidade renderiza apenas a primeira letra do endereço de email e o nome de domínio de email:

![Declaração de email mostrada no navegador com caracteres mascarados por asteriscos](./media/claimsschema/mask-regex.png)


### <a name="restriction"></a>Restrição

O elemento **Restriction** pode conter o seguinte atributo:

| Atributo | Necessário | Descrição |
| --------- | -------- | ----------- |
| MergeBehavior | Não | O método usado para mesclar valores de enumeração com um ClaimType em uma política pai com o mesmo identificador. Use esse atributo quando você substituir uma declaração especificada na política de base. Valores possíveis: `Append`, `Prepend`ou `ReplaceAll`. O `Append` valor é uma coleção de dados que devem ser acrescentados ao final da coleção especificada na política pai. O `Prepend` valor é uma coleção de dados que deve ser adicionada antes da coleção especificada na política pai. O `ReplaceAll` valor é uma coleção de dados especificada na política pai que deve ser ignorada. |

O elemento **Restriction** contém os seguintes elementos:

| Elemento | Ocorrências | Descrição |
| ------- | ----------- | ----------- |
| Enumeração | 1: n | As opções disponíveis na interface do usuário para o usuário selecionar para uma declaração, como um valor em uma lista suspensa. |
| Padrão | 1:1 | A expressão regular a ser usada. |

### <a name="enumeration"></a>Enumeração

O elemento **Enumeration** contém os seguintes atributos:

| Atributo | Necessário | Descrição |
| --------- | -------- | ----------- |
| Text | Sim | A cadeia de caracteres de exibição mostrada para o usuário na interface do usuário para essa opção. |
|Value | Sim | O valor de declaração associado à seleção dessa opção. |
| SelectByDefault | Não | Indica se essa opção deve ou não ser selecionada por padrão na interface do usuário. Valores possíveis: VERDADEIRO ou FALSO. |

O exemplo a seguir configura uma declaração de lista suspensa **cidade** com um valor padrão definido como `New York`:

```XML
<ClaimType Id="city">
  <DisplayName>city where you work</DisplayName>
  <DataType>string</DataType>
  <UserInputType>DropdownSingleSelect</UserInputType>
  <Restriction>
    <Enumeration Text="Bellevue" Value="bellevue" SelectByDefault="false" />
    <Enumeration Text="Redmond" Value="redmond" SelectByDefault="false" />
    <Enumeration Text="New York" Value="new-york" SelectByDefault="true" />
  </Restriction>
</ClaimType>
```

Lista de cidade suspensa com um valor padrão definido como Nova York:

![Controle suspenso renderizado no navegador e mostrando o valor padrão](./media/claimsschema/dropdownsingleselect.png)

### <a name="pattern"></a>Padrão

O elemento **Pattern** pode conter os seguintes atributos:

| Atributo | Necessário | Descrição |
| --------- | -------- | ----------- |
| RegularExpression | Sim | A expressão regular que alega esse tipo deve corresponder para ser válida. |
| Texto | Não | O padrão ou a expressão regular para esta declaração. |

O exemplo a seguir configura uma declaração de **email** com validação de entrada de expressão regular e texto de ajuda:

```XML
<ClaimType Id="email">
  <DisplayName>Email Address</DisplayName>
  <DataType>string</DataType>
  <DefaultPartnerClaimTypes>
    <Protocol Name="OpenIdConnect" PartnerClaimType="email" />
  </DefaultPartnerClaimTypes>
  <UserHelpText>Email address that can be used to contact you.</UserHelpText>
  <UserInputType>TextBox</UserInputType>
  <Restriction>
    <Pattern RegularExpression="^[a-zA-Z0-9.!#$%&amp;'^_`{}~-]+@[a-zA-Z0-9-]+(?:\.[a-zA-Z0-9-]+)*$" HelpText="Please enter a valid email address." />
    </Restriction>
 </ClaimType>
```

A estrutura de experiência de identidade renderiza a declaração de endereço de email com a validação de entrada de formato de email:

![Caixa de texto mostrando a mensagem de erro disparada pela restrição Regex](./media/claimsschema/pattern.png)

## <a name="userinputtype"></a>UserInputType

O Azure AD B2C dá suporte a uma variedade de tipos de entrada de usuário, como uma caixa de texto, uma senha e uma lista suspensa que podem ser usados ao inserir manualmente os dados de declaração para o tipo de declaração. Você deve especificar o userinputtype ao coletar informações do usuário usando um [perfil técnico autodeclarado](self-asserted-technical-profile.md).

### <a name="textbox"></a>TextBox

O tipo de entrada de usuário **TextBox** é usado para fornecer uma caixa de texto de linha única.

![Caixa de texto mostrando as propriedades especificadas no tipo de declaração](./media/claimsschema/textbox.png)

```XML
<ClaimType Id="displayName">
  <DisplayName>Display Name</DisplayName>
  <DataType>string</DataType>
  <UserHelpText>Your display name.</UserHelpText>
  <UserInputType>TextBox</UserInputType>
</ClaimType>
```

### <a name="emailbox"></a>EmailBox

O tipo de entrada de usuário **EmailBox** é usado para fornecer um campo de entrada de email básico.

![EmailBox mostrando as propriedades especificadas no tipo de declaração](./media/claimsschema/emailbox.png)

```XML
<ClaimType Id="email">
  <DisplayName>Email Address</DisplayName>
  <DataType>string</DataType>
  <UserHelpText>Email address that can be used to contact you.</UserHelpText>
  <UserInputType>EmailBox</UserInputType>
  <Restriction>
    <Pattern RegularExpression="^[a-zA-Z0-9!#$%&amp;'+^_`{}~-]+(?:\.[a-zA-Z0-9!#$%&amp;'+^_`{}~-]+)*@(?:[a-zA-Z0-9](?:[a-zA-Z0-9-]*[a-zA-Z0-9])?\.)+[a-zA-Z0-9](?:[a-zA-Z0-9-]*[a-zA-Z0-9])?$" HelpText="Please enter a valid email address." />
  </Restriction>
</ClaimType>
```

### <a name="password"></a>Palavra-passe

O tipo de entrada usuário de **senha** é usado para registrar uma senha inserida pelo usuário.

![Usando tipo de declaração com senha](./media/claimsschema/password.png)

```XML
<ClaimType Id="password">
  <DisplayName>Password</DisplayName>
  <DataType>string</DataType>
  <UserHelpText>Enter password</UserHelpText>
  <UserInputType>Password</UserInputType>
</ClaimType>
```

### <a name="datetimedropdown"></a>DateTimeDropdown

O tipo de entrada de usuário **DateTimeDropdown** é usado para fornecer um conjunto de menus suspensos para selecionar um dia, mês e ano. Você pode usar predicados e elementos PredicateValidations para controlar os valores de data mínimo e máximo. Para obter mais informações, consulte a seção **configurar um intervalo de datas** de predicados [e PredicateValidations](predicates.md).

![Usando o tipo de declaração com datetimedropdown](./media/claimsschema/datetimedropdown.png)

```XML
<ClaimType Id="dateOfBirth">
  <DisplayName>Date Of Birth</DisplayName>
  <DataType>date</DataType>
  <UserHelpText>The date on which you were born.</UserHelpText>
  <UserInputType>DateTimeDropdown</UserInputType>
</ClaimType>
```

### <a name="radiosingleselect"></a>RadioSingleSelect

O tipo de entrada de usuário **RadioSingleSelect** é usado para fornecer uma coleção de botões de opção que permite ao usuário selecionar uma opção.

![Usando o tipo de declaração com radiodsingleselect](./media/claimsschema/radiosingleselect.png)

```XML
<ClaimType Id="color">
  <DisplayName>Preferred color</DisplayName>
  <DataType>string</DataType>
  <UserInputType>RadioSingleSelect</UserInputType>
  <Restriction>
    <Enumeration Text="Blue" Value="Blue" SelectByDefault="false" />
    <Enumeration Text="Green " Value="Green" SelectByDefault="false" />
    <Enumeration Text="Orange" Value="Orange" SelectByDefault="true" />
  </Restriction>
</ClaimType>
```

### <a name="dropdownsingleselect"></a>DropdownSingleSelect

O tipo de entrada de usuário **DropdownSingleSelect** é usado para fornecer uma caixa suspensa que permite ao usuário selecionar uma opção.

![Usando o tipo de declaração com dropdownsingleselect](./media/claimsschema/dropdownsingleselect.png)

```XML
<ClaimType Id="city">
  <DisplayName>City where you work</DisplayName>
  <DataType>string</DataType>
  <UserInputType>DropdownSingleSelect</UserInputType>
  <Restriction>
    <Enumeration Text="Bellevue" Value="bellevue" SelectByDefault="false" />
    <Enumeration Text="Redmond" Value="redmond" SelectByDefault="false" />
    <Enumeration Text="New York" Value="new-york" SelectByDefault="true" />
  </Restriction>
</ClaimType>
```

### <a name="checkboxmultiselect"></a>CheckboxMultiSelect

O tipo de entrada de usuário **CheckboxMultiSelect** é usado para fornecer uma coleção de caixas de seleção que permite ao usuário selecionar várias opções.

![Usando o tipo de declaração com checkboxmultiselect](./media/claimsschema/checkboxmultiselect.png)

```XML
<ClaimType Id="languages">
  <DisplayName>Languages you speak</DisplayName>
  <DataType>string</DataType>
  <UserInputType>CheckboxMultiSelect</UserInputType>
  <Restriction>
    <Enumeration Text="English" Value="English" SelectByDefault="true" />
    <Enumeration Text="France " Value="France" SelectByDefault="false" />
    <Enumeration Text="Spanish" Value="Spanish" SelectByDefault="false" />
  </Restriction>
</ClaimType>
```

### <a name="readonly"></a>Leitura

O tipo de entrada de usuário **ReadOnly** é usado para fornecer um campo ReadOnly para exibir a declaração e o valor.

![Usando tipo de declaração com ReadOnly](./media/claimsschema/readonly.png)

```XML
<ClaimType Id="membershipNumber">
  <DisplayName>Membership number</DisplayName>
  <DataType>string</DataType>
  <UserHelpText>Your membership number (read only)</UserHelpText>
  <UserInputType>Readonly</UserInputType>
</ClaimType>
```


### <a name="paragraph"></a>Retorno

O tipo de entrada de usuário de **parágrafo** é usado para fornecer um campo que mostra texto somente em uma marca de parágrafo. Por exemplo, &lt;p&gt;Text&lt;/p&gt;.

![Usando o tipo de declaração com o parágrafo](./media/claimsschema/paragraph.png)

```XML
<ClaimType Id="responseMsg">
  <DisplayName>Error message: </DisplayName>
  <DataType>string</DataType>
  <AdminHelpText>A claim responsible for holding response messages to send to the relying party</AdminHelpText>
  <UserHelpText>A claim responsible for holding response messages to send to the relying party</UserHelpText>
  <UserInputType>Paragraph</UserInputType>
  <Restriction>
    <Enumeration Text="B2C_V1_90001" Value="You cant sign in because you are a minor" />
    <Enumeration Text="B2C_V1_90002" Value="This action can only be performed by gold members" />
    <Enumeration Text="B2C_V1_90003" Value="You have not been enabled for this operation" />
  </Restriction>
</ClaimType>
```

Para exibir um dos valores de **Enumeração** em uma declaração **responseMsg** , use `GetMappedValueFromLocalizedCollection` ou `CreateStringClaim` transformação de declarações. Para obter mais informações, consulte transformações de [declarações de cadeia de caracteres](string-transformations.md)
