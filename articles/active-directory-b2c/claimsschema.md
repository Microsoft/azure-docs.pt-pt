---
title: ClaimsSchema - Azure Active Directory B2C | Documentos da Microsoft
description: Especifique o elemento de ClaimsSchema de uma política personalizada no Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 81df3532ee466f32ee42f0010de9aa74dbeb6d85
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2019
ms.locfileid: "64721744"
---
# <a name="claimsschema"></a>ClaimsSchema

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

O **ClaimsSchema** elemento define os tipos de afirmações que podem ser referenciados como parte da política. Esquema de declarações é o lugar onde declarar as afirmações. Uma afirmação pode ser o nome próprio, último nome, nome a apresentar, número de telefone e muito mais. Elemento de ClaimsSchema contém a lista de **ClaimType** elementos. O **ClaimType** elemento contém o **Id** atributo, o que é o nome de afirmação. 

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

O **ClaimType** elemento contém o atributo seguinte:

| Atributo | Necessário | Descrição |
| --------- | -------- | ----------- |
| Id | Sim | Um identificador que é utilizado para o tipo de afirmação. Outros elementos podem utilizar este identificador na política. |

O **ClaimType** elemento contém os seguintes elementos:

| Elemento | Ocorrências | Descrição |
| ------- | ----------- | ----------- |
| Nome a Apresentar | 0:1 | O título apresentado aos utilizadores nos vários ecrãs. O valor pode ser [localizada](localization.md). |
| Tipo de dados | 0:1 | O tipo de afirmação. Os tipos de dados de boolean, date, dateTime, int, long, string, stringCollection, alternativeSecurityIdCollection pode ser utilizado. |
| DefaultPartnerClaimTypes | 0:1 | A predefinição de parceiro de afirmação tipos a utilizar para um protocolo especificado. O valor pode ser substituído no **PartnerClaimType** especificado na **InputClaim** ou **OutputClaim** elementos. Utilize este elemento para especificar o nome predefinido para um protocolo.  |
| Máscara | 0:1 | Uma cadeia de caracteres opcional de máscara de caracteres que podem ser aplicadas ao exibir a afirmação. Por exemplo, o 324-232-4343 número telefone pode ser mascarado como XXX-XXX-4343. |
| UserHelpText | 0:1 | Uma descrição do tipo de afirmação que pode ser útil para os utilizadores a compreender a sua finalidade. O valor pode ser [localizada](localization.md). |
| UserInputType | 0:1 | O tipo de controlo de entrada que deve estar disponível para o utilizador ao introduzir manualmente os dados da declaração para o tipo de afirmação. Ver os tipos de entrada de utilizador definidos mais tarde nesta página. |
| Restrição | 0:1 | As restrições de valor por essa declaração, como uma expressão regular (Regex) ou uma lista de valores aceitáveis. O valor pode ser [localizada](localization.md). |
PredicateValidationReference| 0:1 | Uma referência a um **PredicateValidationsInput** elemento. O **PredicateValidationReference** elementos permitem-lhe efetuar um processo de validação para garantir que apenas os dados corretamente formados são introduzidos. Para obter mais informações, consulte [predicados](predicates.md). |

### <a name="defaultpartnerclaimtypes"></a>DefaultPartnerClaimTypes

O **DefaultPartnerClaimTypes** pode conter o seguinte elemento:

| Elemento | Ocorrências | Descrição |
| ------- | ----------- | ----------- |
| Protocolo | 0:n | Nome do tipo de afirmação de lista de protocolos com o respetivo parceiro de predefinição. |

O **protocolo** elemento contém os seguintes atributos:

| Atributo | Necessário | Descrição |
| --------- | -------- | ----------- |
| Name | Sim | O nome de um protocolo válido suportado pelo Azure AD B2C. Os valores possíveis são:  OAuth1, OAuth2, SAML2, OpenIdConnect, WsFed ou WsTrust. |
| PartnerClaimType | Sim | O nome do tipo de afirmação a ser utilizado. |

No exemplo seguinte, quando o Framework de experiência de identidade interage com um fornecedor de identidade SAML2 ou a aplicação da entidade confiadora de terceiros, o **Apelido** afirmação é mapeada para `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`, com OpenIdConnect e OAuth2, a afirmação é mapeado para `family_name`.

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

Como resultado, o token JWT emitido pelo Azure AD B2C, emite a `family_name` em vez do nome de ClaimType **Apelido**.
 
```JSON
{
  "sub": "6fbbd70d-262b-4b50-804c-257ae1706ef2",
  "auth_time": 1535013501,
  "given_name": "David",
  "family_name": "Williams",
  "name": "David Williams",
}
```

### <a name="mask"></a>Máscara

O **máscara** elemento contém os seguintes atributos:

| Atributo | Necessário | Descrição |
| --------- | -------- | ----------- |
| `Type` | Sim | O tipo de máscara de afirmação. Valores possíveis: `Simple` ou `Regex`. O `Simple` valor indica que uma máscara de texto simples é aplicada à parte de uma afirmação de cadeia de caracteres à esquerda. O `Regex` valor indica que uma expressão regular é aplicada às afirmações de cadeia de caracteres como todo.  Se o `Regex` valor for especificado, um atributo opcional também tem de ser definido com a expressão regular a utilizar. |
| `Regex` | Não | Se **`Type`** está definido como `Regex`, especifique a expressão regular a utilizar.

O exemplo seguinte configura um **PhoneNumber** a afirmação com o `Simple` máscara:

```XML
<ClaimType Id="PhoneNumber">
  <DisplayName>Phone Number</DisplayName>
  <DataType>string</DataType>
  <Mask Type="Simple">XXX-XXX-</Mask>  
  <UserHelpText>Your telephone number.</UserHelpText>
</ClaimType>
```

O Framework de experiência de identidade renderiza o número de telefone e ocultar os primeiro de seis dígitos:

![Usando o tipo com a máscara de afirmação](./media/claimsschema/mask.png)

O exemplo seguinte configura um **AlternateEmail** a afirmação com o `Regex` máscara:

```XML
<ClaimType Id="AlternateEmail">
  <DisplayName>Please verify the secondary email linked to your account</DisplayName>
  <DataType>string</DataType>
  <Mask Type="Regex" Regex="(?&lt;=.).(?=.*@)">*</Mask>
  <UserInputType>Readonly</UserInputType>
</ClaimType>
```

O Framework de experiência de identidade renderiza apenas a primeira letra do endereço de e-mail e o nome de domínio de e-mail:

![Usando o tipo com a máscara de afirmação](./media/claimsschema/mask-regex.png)


### <a name="restriction"></a>Restrição

O **restrição** elemento pode conter o atributo seguinte:

| Atributo | Necessário | Descrição |
| --------- | -------- | ----------- |
| MergeBehavior | Não | O método usado para mesclar os valores de enumeração com um ClaimType numa política de principal com o mesmo identificador. Use este atributo quando substituir uma afirmação especificada na política de base. Valores possíveis: `Append`, `Prepend`, ou `ReplaceAll`. O `Append` valor é uma coleção de dados que devem ser acrescentados ao fim da coleção especificada na política de principal. O `Prepend` valor é uma coleção de dados que devem ser adicionados antes da coleção especificada na política de principal. O `ReplaceAll` valor é uma coleção de dados especificadas na política de principal que deve ser ignorada. |

O **restrição** elemento contém os seguintes elementos:

| Elemento | Ocorrências | Descrição |
| ------- | ----------- | ----------- |
| Enumeração | 1:n | As opções disponíveis na interface do usuário para o utilizador selecionar para uma afirmação, por exemplo, um valor numa lista pendente. |
| Padrão | 1:1 | A expressão regular a utilizar. |

### <a name="enumeration"></a>Enumeração

O **enumeração** elemento contém os seguintes atributos:

| Atributo | Necessário | Descrição |
| --------- | -------- | ----------- |
| Text | Sim | A cadeia de apresentação que é mostrada ao usuário na interface do usuário para esta opção. |
|Value | Sim | O valor de afirmação que está associado a seleção desta opção. |
| SelectByDefault | Não | Indica se é ou não essa opção deve ser selecionada por predefinição na interface do Usuário. Valores possíveis: VERDADEIRO ou FALSO. |

O exemplo seguinte configura um **Cidade** afirmação de lista suspensa com um valor predefinido definido como `New York`:

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
Lista de cidade de lista pendente com o valor predefinido definido como nova York:

![Lista de cidade pendente](./media/claimsschema/dropdownsingleselect.png)


### <a name="pattern"></a>Padrão

O **padrão** elemento pode conter os seguintes atributos:

| Atributo | Necessário | Descrição |
| --------- | -------- | ----------- |
| RegularExpression | Sim | A expressão regular que afirmações deste tipo tem de corresponder para que seja válido. |
| HelpText | Não | O padrão ou uma expressão regular para esta afirmação. |

O exemplo seguinte configura uma **e-mail** afirmação com o texto de ajuda e validação de entrada no expressão regular:

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

O Framework de experiência de identidade processa a declaração de endereço de e-mail com a validação de entrada de formato de e-mail:

![Usando o tipo com padrão de afirmação](./media/claimsschema/pattern.png)

## <a name="userinputtype"></a>UserInputType

O Azure AD B2C suporta uma variedade de tipos de entrada de usuário, como uma lista de caixa de texto, a palavra-passe e a lista pendente que podem ser utilizados quando introduzir manualmente os dados da declaração para o tipo de afirmação. Tem de especificar o **UserInputType** quando recolher informações do usuário, utilizando um [autodeclarativas perfil técnico](self-asserted-technical-profile.md).

### <a name="textbox"></a>TextBox

O **caixa de texto** tipo de entrada do utilizador é utilizado para fornecer uma caixa de texto de linha única.

![Usando o tipo com a caixa de texto de afirmação](./media/claimsschema/textbox.png)

```XML
<ClaimType Id="displayName">
  <DisplayName>Display Name</DisplayName>
  <DataType>string</DataType>
  <UserHelpText>Your display name.</UserHelpText>
  <UserInputType>TextBox</UserInputType>
</ClaimType>
```

### <a name="emailbox"></a>EmailBox

O **EmailBox** tipo de entrada do utilizador é utilizado para fornecer um campo de entrada de e-mail básica.

![Usando o tipo com emailbox de afirmação](./media/claimsschema/emailbox.png)

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

O **palavra-passe** tipo de entrada do utilizador é utilizado para registar uma palavra-passe introduzida pelo utilizador.

![Usando o tipo com palavra-passe de afirmação](./media/claimsschema/password.png)

```XML
<ClaimType Id="password">
  <DisplayName>Password</DisplayName>
  <DataType>string</DataType>
  <UserHelpText>Enter password</UserHelpText>
  <UserInputType>Password</UserInputType>
</ClaimType>
```

### <a name="datetimedropdown"></a>DateTimeDropdown

O **DateTimeDropdown** tipo de entrada do utilizador é utilizado para fornecer um conjunto de listas pendentes para selecionar um dia, mês e ano. Pode utilizar os elementos de predicados e PredicateValidations para controlar os valores de data mínimo e máximo. Para obter mais informações, consulte a **configurar um intervalo de datas** secção [predicados e PredicateValidations](predicates.md).

![Usando o tipo com datetimedropdown de afirmação](./media/claimsschema/datetimedropdown.png)

```XML
<ClaimType Id="dateOfBirth">
  <DisplayName>Date Of Birth</DisplayName>
  <DataType>date</DataType>
  <UserHelpText>The date on which you were born.</UserHelpText>
  <UserInputType>DateTimeDropdown</UserInputType>
</ClaimType>
```

### <a name="radiosingleselect"></a>RadioSingleSelect

O **RadioSingleSelect** tipo de entrada do utilizador é utilizado para fornecer uma coleção de botões de opção que permita ao usuário selecionar uma opção.

![Usando o tipo com radiodsingleselect de afirmação](./media/claimsschema/radiosingleselect.png)

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

O **DropdownSingleSelect** tipo de entrada do utilizador é utilizado para fornecer uma caixa de lista pendente que permite ao usuário selecionar uma opção.

![Usando o tipo com dropdownsingleselect de afirmação](./media/claimsschema/dropdownsingleselect.png)

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

O **CheckboxMultiSelect** tipo de entrada do utilizador é utilizado para fornecer uma coleção de caixas de verificação que permite que o usuário marque várias opções.

![Usando o tipo com checkboxmultiselect de afirmação](./media/claimsschema/checkboxmultiselect.png)

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

### <a name="readonly"></a>só de leitura

O **só de leitura** tipo de entrada do utilizador é utilizado para fornecer um campo de só de leitura para apresentar a afirmação e o valor.

![Usando o tipo com só de leitura de afirmação](./media/claimsschema/readonly.png)

```XML
<ClaimType Id="membershipNumber">
  <DisplayName>Membership number</DisplayName>
  <DataType>string</DataType>
  <UserHelpText>Your membership number (read only)</UserHelpText>
  <UserInputType>Readonly</UserInputType>
</ClaimType>
```


### <a name="paragraph"></a>Parágrafo

O **parágrafo** tipo de entrada do utilizador é utilizado para fornecer um campo que mostra o texto apenas numa marca de parágrafo. Por exemplo, &lt;p&gt;texto&lt;p&gt;.

![Usando o tipo com o parágrafo de afirmação](./media/claimsschema/paragraph.png)

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

Para apresentar uma da **enumeração** valores num **responseMsg** de afirmação, utilize `GetMappedValueFromLocalizedCollection` ou `CreateStringClaim` transformação de afirmações. Para obter mais informações, consulte [transformações de afirmações de cadeia de caracteres](string-transformations.md) 
