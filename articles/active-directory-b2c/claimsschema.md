---
title: ReclamaçõesSchema - Azure Ative Directory B2C / Microsoft Docs
description: Especificar o elemento ClaimsSchema de uma política personalizada no Azure Ative Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/05/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: aadb75d7257470cf4288c6123263f3d2dfe14d21
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/28/2020
ms.locfileid: "92781724"
---
# <a name="claimsschema"></a>ClaimsSchema

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

O elemento **ClaimsSchema** define os tipos de reclamação que podem ser referenciados como parte da política. O esquema de reclamações é o lugar onde declaras as tuas reivindicações. Uma reclamação pode ser o primeiro nome, apelido, nome de exibição, número de telefone e muito mais. SinistrosO elemento do Caixa contém lista de elementos **ClaimType.** O elemento **ClaimType** contém o atributo **Id,** que é o nome de reclamação.

```xml
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

O elemento **ClaimType** contém o seguinte atributo:

| Atributo | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| Id | Sim | Um identificador que é usado para o tipo de reclamação. Outros elementos podem usar este identificador na apólice. |

O elemento **ClaimType** contém os seguintes elementos:

| Elemento | Ocorrências | Descrição |
| ------- | ----------- | ----------- |
| DisplayName | 1:1 | O título que é apresentado aos utilizadores em vários ecrãs. O valor pode ser [localizado.](localization.md) |
| DataType | 1:1 | O tipo de reclamação. |
| DefaultPartnerClaimTypes | 0:1 | Os tipos de reclamação por defeito do parceiro para utilizar para um protocolo especificado. O valor pode ser substituído no **PartnerClaimType** especificado nos elementos **InputClaim** ou **OutputClaim.** Utilize este elemento para especificar o nome predefinido de um protocolo.  |
| Máscara | 0:1 | Uma cadeia opcional de caracteres mascarados que pode ser aplicada ao exibir a alegação. Por exemplo, o número de telefone 324-232-4343 pode ser mascarado como XXX-XXX-4343. |
| UserHelpText | 0:1 | Uma descrição do tipo de reclamação que pode ser útil para os utilizadores entenderem o seu propósito. O valor pode ser [localizado.](localization.md) |
| UserInputType | 0:1 | O tipo de controlo de entrada que deve estar disponível para o utilizador ao introduzir manualmente os dados de reclamação para o tipo de reclamação. Consulte os tipos de entrada do utilizador definidos mais tarde nesta página. |
| AdminHelpText | 0:1 | Uma descrição do tipo de reclamação que pode ser útil para os administradores entenderem o seu propósito. |
| Restrição | 0:1 | As restrições de valor para esta alegação, tais como uma expressão regular (Regex) ou uma lista de valores aceitáveis. O valor pode ser [localizado.](localization.md) |
Pré-arquivação deValidação| 0:1 | Uma referência a um elemento **PredicateValidationsInput.** Os elementos **PredicateValidationReference** permitem-lhe realizar um processo de validação para garantir que apenas os dados devidamente formados são introduzidos. Para mais informações, consulte [Predicates.](predicates.md) |



### <a name="datatype"></a>DataType

O elemento **DataType** suporta os seguintes valores:

| Tipo | Descrição |
| ------- | ----------- |
|boolean|Representa um valor Boolean `true` `false` (ou)|
|date| Representa um instante no tempo, tipicamente expresso como uma data de um dia. O valor da data segue-se à convenção ISO 8601.|
|dataTime|Representa um instante no tempo, tipicamente expresso como data e hora do dia. O valor da data segue-se à convenção ISO 8601.|
|duration|Representa um intervalo de tempo em anos, meses, dias, horas, minutos e segundos. O formato de é `PnYnMnDTnHnMnS` , onde `P` indica positivo, ou por valor `N` negativo. `nY` é o número de anos seguido de um literal `Y` . `nMo` é o número de meses seguido de um literal `Mo` . `nD` é o número de dias seguidos por um literal `D` . Exemplos: `P21Y` representa 21 anos. `P1Y2Mo` representa um ano, e dois meses. `P1Y2Mo5D` representa um ano, dois meses e cinco dias.  `P1Y2M5DT8H5M620S` representa um ano, dois meses, cinco dias, oito horas, cinco minutos e vinte segundos.  |
|número de telefone|Representa um número de telefone. |
|int| Representa o número entre -2.147.483.648 e 2.147.483.647|
|long| Representa o número entre -9.223.372.036.854.775.808 a 9.223.372.036.854.775.807 |
|cadeia| Representa o texto como uma sequência de unidades de código UTF-16.|
|stringCollection|Representa uma coleção `string` de.|
|userDentity| Representa uma identidade de utilizador.|
|userIdentityCollection|Representa uma coleção `userIdentity` de.|

### <a name="defaultpartnerclaimtypes"></a>DefaultPartnerClaimTypes

Os **DefaultPartnerClaimTypes** podem conter o seguinte elemento:

| Elemento | Ocorrências | Descrição |
| ------- | ----------- | ----------- |
| Protocolo | 1:n | Lista de protocolos com o nome do tipo de reclamação do parceiro predefinido. |

O elemento **Protocolo** contém os seguintes atributos:

| Atributo | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| Nome | Sim | O nome de um protocolo válido suportado pelo Azure AD B2C. Os valores possíveis são: OAuth1, OAuth2, SAML2, OpenIdConnect. |
| PartnerClaimType | Sim | O nome do tipo reivindicação a ser usado. |

No exemplo seguinte, quando o Quadro de Experiência de Identidade interage com um fornecedor de identidade SAML2 ou com pedido de parte, a alegação de **apelido** é mapeada `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname` para, com OpenIdConnect e OAuth2, a alegação está mapeada para `family_name` .

```xml
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

Como resultado, o símbolo JWT emitido por Azure AD B2C, emite o `family_name` apelido em vez do **nome** ClaimType .

```json
{
  "sub": "6fbbd70d-262b-4b50-804c-257ae1706ef2",
  "auth_time": 1535013501,
  "given_name": "David",
  "family_name": "Williams",
  "name": "David Williams",
}
```

### <a name="mask"></a>Máscara

O elemento **Máscara** contém os seguintes atributos:

| Atributo | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| `Type` | Sim | O tipo da máscara de reivindicação. Valores possíveis: `Simple` ou `Regex` . O `Simple` valor indica que uma simples máscara de texto é aplicada na parte principal de uma alegação de corda. O `Regex` valor indica que uma expressão regular é aplicada à alegação de corda como um todo.  Se o `Regex` valor for especificado, um atributo opcional também deve ser definido com a expressão regular a usar. |
| `Regex` | Não | Se **`Type`** estiver `Regex` definido, especifique a expressão regular a utilizar.

O exemplo a seguir configura uma reivindicação **Do Número de Telefone** com a `Simple` máscara:

```xml
<ClaimType Id="PhoneNumber">
  <DisplayName>Phone Number</DisplayName>
  <DataType>string</DataType>
  <Mask Type="Simple">XXX-XXX-</Mask>
  <UserHelpText>Your telephone number.</UserHelpText>
</ClaimType>
```

O Quadro de Experiência de Identidade torna o número de telefone enquanto esconde os primeiros seis dígitos:

![Reclamação de número de telefone mostrada no navegador com os primeiros seis dígitos mascarados por Xs](./media/claimsschema/mask.png)

O exemplo a seguir configura uma reivindicação **AlternateEmail** com a `Regex` máscara:

```xml
<ClaimType Id="AlternateEmail">
  <DisplayName>Please verify the secondary email linked to your account</DisplayName>
  <DataType>string</DataType>
  <Mask Type="Regex" Regex="(?&lt;=.).(?=.*@)">*</Mask>
  <UserInputType>Readonly</UserInputType>
</ClaimType>
```

O Quadro de Experiência de Identidade torna apenas a primeira letra do endereço de e-mail e o nome de domínio de e-mail:

![Alegação de e-mail mostrada no navegador com caracteres mascarados por asteriscos](./media/claimsschema/mask-regex.png)


### <a name="restriction"></a>Restrição

O elemento **restrição** pode conter o seguinte atributo:

| Atributo | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| FusõesBehavior | Não | O método utilizado para fundir valores de enumeração com um ClaimType numa política-mãe com o mesmo identificador. Utilize este atributo quando substituir uma reclamação especificada na política base. Valores possíveis: `Append` `Prepend` , ou . `ReplaceAll` . O `Append` valor é uma recolha de dados que deve ser anexado ao fim da recolha especificada na política-mãe. O `Prepend` valor é uma recolha de dados que deve ser adicionado antes da recolha especificada na política-mãe. O `ReplaceAll` valor é uma recolha de dados especificados na política-mãe que deve ser ignorada. |

O elemento **restrição** contém os seguintes elementos:

| Elemento | Ocorrências | Descrição |
| ------- | ----------- | ----------- |
| Enumeração | 1:n | As opções disponíveis na interface do utilizador para o utilizador selecionar para uma reclamação, como um valor numa queda. |
| Padrão | 1:1 | A expressão regular a usar. |

#### <a name="enumeration"></a>Enumeração

O elemento **Enumeração** define as opções disponíveis para o utilizador selecionar para uma reclamação na interface do utilizador, como um valor num `CheckboxMultiSelect` , ou `DropdownSingleSelect` `RadioSingleSelect` . Em alternativa, pode definir e localizado as opções disponíveis com o [elementoCollections Localizado.](localization.md#localizedcollections) Para procurar um item a partir de uma coleção de enumeração de **reivindicação,** use a transformação de reivindicações [getMappedValueFromLocalizedCollection.](string-transformations.md#getmappedvaluefromlocalizedcollection)

O elemento **Enumeração** contém os seguintes atributos:

| Atributo | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| Texto | Sim | A cadeia de visualização que é mostrada ao utilizador na interface do utilizador para esta opção. |
|Valor | Sim | O valor de reclamação que está associado à seleção desta opção. |
| SelecioneByDefault | Não | Indica se esta opção deve ou não ser selecionada por padrão na UI. Valores possíveis: Verdadeiros ou Falsos. |

O exemplo a seguir configura uma reivindicação da lista de abandono da **cidade** com um valor padrão definido `New York` para:

```xml
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

Lista de cidades de dropdown com um valor padrão definido para Nova Iorque:

![Controlo de dropdown renderizado no navegador e mostrando valor padrão](./media/claimsschema/dropdownsingleselect.png)

### <a name="pattern"></a>Padrão

O elemento **Padrão** pode conter os seguintes atributos:

| Atributo | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| Expressão Regular | Sim | A expressão regular que as alegações deste tipo devem corresponder para ser válida. |
| HelpText | Não | Uma mensagem de erro para os utilizadores se a verificação de expressão regular falhar. |

O exemplo a seguir configura uma reclamação **de e-mail** com validação regular de entrada de expressão e texto de ajuda:

```xml
<ClaimType Id="email">
  <DisplayName>Email Address</DisplayName>
  <DataType>string</DataType>
  <DefaultPartnerClaimTypes>
    <Protocol Name="OpenIdConnect" PartnerClaimType="email" />
  </DefaultPartnerClaimTypes>
  <UserHelpText>Email address that can be used to contact you.</UserHelpText>
  <UserInputType>TextBox</UserInputType>
  <Restriction>
    <Pattern RegularExpression="^[a-zA-Z0-9.+!#$%&amp;'^_`{}~-]+@[a-zA-Z0-9-]+(?:\.[a-zA-Z0-9-]+)*$" HelpText="Please enter a valid email address." />
    </Restriction>
 </ClaimType>
```

O Quadro de Experiência de Identidade torna a reclamação do endereço de e-mail com validação de entrada de formato de e-mail:

![TextBox mostrando mensagem de erro desencadeada pela restrição regex](./media/claimsschema/pattern.png)

### <a name="userinputtype"></a>UserInputType

O Azure AD B2C suporta uma variedade de tipos de entrada de utilizador, tais como uma caixa de texto, palavra-passe e lista de dropdown que podem ser usados ao introduzir manualmente dados de reclamação para o tipo de reclamação. Tem de especificar o **UserInputType** quando recolher informações do utilizador utilizando um [perfil técnico autoafirmado](self-asserted-technical-profile.md) e [controlos de exibição](display-controls.md).

O elemento **UserInputType** disponível nos tipos de entrada do utilizador:

| UserInputType | Tip de reclamação suportado | Descrição |
| --------- | -------- | ----------- |
|CheckboxMultiSelect| `string` |Caixa de entrega multi-selecção. O valor de reclamação está representado numa cadeia delimiter de vírgula dos valores selecionados. |
|DataTimeDropdown | `date`, `dateTime` |Drop-downs para selecionar um dia, mês e ano. |
|DropdownSingleSelect |`string` |Única caixa de entrega selecionada. O valor de reclamação é o valor selecionado.|
|Caixa de e-mail | `string` |Campo de entrada de e-mail. |
|Parágrafo | `boolean`, `date`, `dateTime`, `duration`, `int`, `long`, `string`|Um campo que mostra texto apenas numa etiqueta de parágrafo. |
|Palavra-passe | `string` |Caixa de texto de senha.|
|RadioSingleSelect |`string` | Coleção de botões de rádio. O valor de reclamação é o valor selecionado.|
|Readonly | `boolean`, `date`, `dateTime`, `duration`, `int`, `long`, `string`| Caixa de texto só de leitura. |
|TextBox |`boolean`, `int`, `string` |Caixa de texto de linha única. |


#### <a name="textbox"></a>TextBox

O tipo de entrada do utilizador **TextBox** é utilizado para fornecer uma caixa de texto de uma única linha.

![TextBox mostrando propriedades especificadas no tipo de reclamação](./media/claimsschema/textbox.png)

```xml
<ClaimType Id="displayName">
  <DisplayName>Display Name</DisplayName>
  <DataType>string</DataType>
  <UserHelpText>Your display name.</UserHelpText>
  <UserInputType>TextBox</UserInputType>
</ClaimType>
```

#### <a name="emailbox"></a>Caixa de e-mail

O tipo de entrada do utilizador **Do EmailBox** é utilizado para fornecer um campo básico de entrada de e-mail.

![EmailBox mostrando propriedades especificadas no tipo de reclamação](./media/claimsschema/emailbox.png)

```xml
<ClaimType Id="email">
  <DisplayName>Email Address</DisplayName>
  <DataType>string</DataType>
  <UserHelpText>Email address that can be used to contact you.</UserHelpText>
  <UserInputType>EmailBox</UserInputType>
  <Restriction>
    <Pattern RegularExpression="^[a-zA-Z0-9.+!#$%&amp;'+^_`{}~-]+(?:\.[a-zA-Z0-9!#$%&amp;'+^_`{}~-]+)*@(?:[a-zA-Z0-9](?:[a-zA-Z0-9-]*[a-zA-Z0-9])?\.)+[a-zA-Z0-9](?:[a-zA-Z0-9-]*[a-zA-Z0-9])?$" HelpText="Please enter a valid email address." />
  </Restriction>
</ClaimType>
```

#### <a name="password"></a>Palavra-passe

O tipo de entrada do utilizador **palavra-passe** é utilizado para registar uma palavra-passe inserida pelo utilizador.

![Utilização do tipo de reclamação com senha](./media/claimsschema/password.png)

```xml
<ClaimType Id="password">
  <DisplayName>Password</DisplayName>
  <DataType>string</DataType>
  <UserHelpText>Enter password</UserHelpText>
  <UserInputType>Password</UserInputType>
</ClaimType>
```

#### <a name="datetimedropdown"></a>DataTimeDropdown

O tipo de entrada do utilizador **DateTimeDropdown** é utilizado para fornecer um conjunto de drop-downs para selecionar um dia, mês e ano. Pode utilizar elementos Predicates e PredicateValidations para controlar os valores mínimos e máximos de data. Para obter mais informações, consulte a secção **de configuração de uma** gama de datas de [Predicates e PredicateValidations](predicates.md).

![Utilização do tipo de reclamação com datatimedropdown](./media/claimsschema/datetimedropdown.png)

```xml
<ClaimType Id="dateOfBirth">
  <DisplayName>Date Of Birth</DisplayName>
  <DataType>date</DataType>
  <UserHelpText>The date on which you were born.</UserHelpText>
  <UserInputType>DateTimeDropdown</UserInputType>
</ClaimType>
```

#### <a name="radiosingleselect"></a>RadioSingleSelect

O tipo de entrada do utilizador **RadioSingleSelect** é utilizado para fornecer uma coleção de botões de rádio que permite ao utilizador selecionar uma opção.

![Utilização do tipo de reclamação com radiodsingleselect](./media/claimsschema/radiosingleselect.png)

```xml
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

#### <a name="dropdownsingleselect"></a>DropdownSingleSelect

O tipo de entrada do utilizador **DropdownSingleSelect** é utilizado para fornecer uma caixa de entrega que permite ao utilizador selecionar uma opção.

![Utilização do tipo de reclamação com dropdownsingleselect](./media/claimsschema/dropdownsingleselect.png)

```xml
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

#### <a name="checkboxmultiselect"></a>CheckboxMultiSelect

O tipo de entrada do utilizador **CheckboxMultiSelect** é utilizado para fornecer uma coleção de caixas de verificação que permite ao utilizador selecionar várias opções.

![Utilização do tipo de reclamação com checkboxmultiselect](./media/claimsschema/checkboxmultiselect.png)

```xml
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

#### <a name="readonly"></a>Readonly

O tipo de entrada do utilizador **Readonly** é utilizado para fornecer um campo de leitura apenas para exibir a reclamação e o valor.

![Utilização do tipo de reclamação com readonly](./media/claimsschema/readonly.png)

```xml
<ClaimType Id="membershipNumber">
  <DisplayName>Membership number</DisplayName>
  <DataType>string</DataType>
  <UserHelpText>Your membership number (read only)</UserHelpText>
  <UserInputType>Readonly</UserInputType>
</ClaimType>
```


#### <a name="paragraph"></a>Parágrafo

O tipo de entrada do utilizador **parágrafo** é utilizado para fornecer um campo que mostra texto apenas numa etiqueta de parágrafo.  Por exemplo, &lt; p &gt; texto &lt; /p &gt; . Um **tipo** de entrada do utilizador do parágrafo de perfil `OutputClaim` técnico autoafirmado, deve definir o `Required` atributo `false` (predefinição).

![Utilização do tipo de reclamação com parágrafo](./media/claimsschema/paragraph.png)

```xml
<ClaimType Id="responseMsg">
  <DisplayName>Error message: </DisplayName>
  <DataType>string</DataType>
  <AdminHelpText>A claim responsible for holding response messages to send to the relying party</AdminHelpText>
  <UserHelpText>A claim responsible for holding response messages to send to the relying party</UserHelpText>
  <UserInputType>Paragraph</UserInputType>
  <Restriction>
    <Enumeration Text="B2C_V1_90001" Value="You cannot sign in because you are a minor" />
    <Enumeration Text="B2C_V1_90002" Value="This action can only be performed by gold members" />
    <Enumeration Text="B2C_V1_90003" Value="You have not been enabled for this operation" />
  </Restriction>
</ClaimType>
```
