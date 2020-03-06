---
title: ClaimsSchema - Diretório Ativo Azure B2C  Microsoft Docs
description: Especifique o elemento ClaimsSchema de uma política personalizada no Diretório Ativo Azure B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/05/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 4c3b3318e941723ec333597c7e4b3e48710152d1
ms.sourcegitcommit: 05b36f7e0e4ba1a821bacce53a1e3df7e510c53a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2020
ms.locfileid: "78397791"
---
# <a name="claimsschema"></a>ClaimsSchema

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

O elemento **ClaimsSchema** define os tipos de reclamação que podem ser referenciados como parte da política. Alega que o esquema é o lugar onde declaras as tuas alegações. Uma reclamação pode ser o primeiro nome, apelido, nome de exibição, número de telefone e muito mais. O elemento ClaimsSchema contém uma lista de elementos **Do ClaimType.** O elemento **ClaimType** contém o atributo **ID,** que é o nome da reclamação.

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

## <a name="claimtype"></a>Tipo de reclamação

O elemento **ClaimType** contém o seguinte atributo:

| Atributo | Necessário | Descrição |
| --------- | -------- | ----------- |
| Id | Sim | Um identificador que é usado para o tipo de reclamação. Outros elementos podem utilizar este identificador na política. |

O elemento **ClaimType** contém os seguintes elementos:

| Elemento | Ocorrências | Descrição |
| ------- | ----------- | ----------- |
| DisplayName | 1:1 | O título que é exibido aos utilizadores em vários ecrãs. O valor pode ser [localizado.](localization.md) |
| Tipo de dados | 1:1 | O tipo de reclamação. |
| DefaultPartnerClaimTypes | 0:1 | Os tipos de reclamação por defeito do parceiro para utilizar para um protocolo especificado. O valor pode ser substituído no **PartnerClaimType** especificado nos elementos **'Claimclaim'** ou **OutputClaim.** Utilize este elemento para especificar o nome predefinido para um protocolo.  |
| Máscara | 0:1 | Uma cadeia opcional de caracteres mascarados que podem ser aplicados ao exibir a alegação. Por exemplo, o número de telefone 324-232-4343 pode ser mascarado como XXX-XXX-4343. |
| UserHelpText | 0:1 | Uma descrição do tipo de reclamação que pode ser útil para os utilizadores entenderem o seu propósito. O valor pode ser [localizado.](localization.md) |
| UserInputType | 0:1 | O tipo de controlo de entrada que deve estar disponível para o utilizador ao introduzir manualmente os dados de reclamação para o tipo de reclamação. Consulte os tipos de entrada do utilizador definidos mais tarde nesta página. |
| AdminHelpText | 0:1 | Uma descrição do tipo de reclamação que pode ser útil para os administradores entenderem o seu propósito. |
| Restrição | 0:1 | As restrições de valor para esta reclamação, tais como uma expressão regular (Regex) ou uma lista de valores aceitáveis. O valor pode ser [localizado.](localization.md) |
PredicateValidationReference| 0:1 | Uma referência a um elemento **PredicadoValidaçõesInputput.** Os elementos **Predicados ValidaçãoDereferência** permitem-lhe realizar um processo de validação para garantir que apenas os dados devidamente formados são introduzidos. Para mais informações, consulte [Predicados](predicates.md). |



### <a name="datatype"></a>Tipo de dados

O elemento **DataType** suporta os seguintes valores:

| Tipo | Descrição |
| ------- | ----------- |
|boolean|Representa um valor Boolean (`true` ou `false`).|
|date| Representa um instante no tempo, tipicamente expresso como uma data de um dia. O valor da data segue a convenção ISO 8601.|
|DateTime|Representa um instante no tempo, tipicamente expresso como uma data e hora do dia. O valor da data segue a convenção ISO 8601.|
|duration|Representa um intervalo de tempo em anos, meses, dias, horas, minutos e segundos. O formato é `PnYnMnDTnHnMnS`, onde `P` indica positivo, ou `N` para valor negativo. `nY` é o número de anos seguidos de uma `Y`literal. `nMo` é o número de meses seguido de uma `Mo`literal. `nD` é o número de dias seguidos de uma `D`literal. Exemplos: `P21Y` representa 21 anos. `P1Y2Mo` representa um ano e dois meses. `P1Y2Mo5D` representa um ano, dois meses e cinco dias.  `P1Y2M5DT8H5M620S` representa um ano, dois meses, cinco dias, oito horas, cinco minutos e vinte segundos.  |
|phoneNumber|Representa um número de telefone. |
|int| Representa o número entre -2.147.483.648 e 2.147.483.647|
|long| Representa o número entre -9.223.372.036.854.775.808 para 9.223.372.036.854.775.807 |
|string| Representa o texto como uma sequência de unidades de código UTF-16.|
|stringCollection|Representa uma coleção de `string`.|
|userIdentidade| Representa uma identidade de utilizador.|
|userIdentityCollection|Representa uma coleção de `userIdentity`.|

### <a name="defaultpartnerclaimtypes"></a>DefaultPartnerClaimTypes

Os Tipos de **Reclamação por Predefinição** podem conter o seguinte elemento:

| Elemento | Ocorrências | Descrição |
| ------- | ----------- | ----------- |
| Protocolo | 1:n | Lista de protocolos com o nome do tipo de reclamação do parceiro predefinido. |

O elemento **Protocolo** contém os seguintes atributos:

| Atributo | Necessário | Descrição |
| --------- | -------- | ----------- |
| Nome | Sim | O nome de um protocolo válido suportado pelo Azure AD B2C. Os valores possíveis são: OAuth1, OAuth2, SAML2, OpenIdConnect. |
| Tipo de reclamação de parceiros | Sim | O nome do tipo de reclamação a utilizar. |

No exemplo seguinte, quando o Quadro de Experiência de Identidade interage com um fornecedor de identidade SAML2 ou uma aplicação de parte dependente, a alegação de **sobrenome** é mapeada para `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`, com openIdConnect e OAuth2, a alegação é mapeada para `family_name`.

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

Como resultado, o símbolo JWT emitido pelo Azure AD B2C, emite o `family_name` em vez do **apelido**de nome ClaimType .

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

O elemento **Máscara** contém os seguintes atributos:

| Atributo | Necessário | Descrição |
| --------- | -------- | ----------- |
| `Type` | Sim | O tipo de máscara de reivindicação. Valores possíveis: `Simple` ou `Regex`. O valor `Simple` indica que uma simples máscara de texto é aplicada à parte principal de uma reivindicação de cordas. O valor `Regex` indica que uma expressão regular é aplicada à reivindicação da cadeia no seu conjunto.  Se o valor `Regex` for especificado, um atributo opcional também deve ser definido com a expressão regular a utilizar. |
| `Regex` | Não | Se **`Type`** estiver programado para `Regex`, especifique a expressão regular a utilizar.

O exemplo seguinte configura uma alegação **de Número de Telefone** com a máscara `Simple`:

```XML
<ClaimType Id="PhoneNumber">
  <DisplayName>Phone Number</DisplayName>
  <DataType>string</DataType>
  <Mask Type="Simple">XXX-XXX-</Mask>
  <UserHelpText>Your telephone number.</UserHelpText>
</ClaimType>
```

O Quadro de Experiência de Identidade torna o número de telefone enquanto esconde os primeiros seis dígitos:

![Pedido de número de telefone mostrado no navegador com os primeiros seis dígitos mascarados por Xs](./media/claimsschema/mask.png)

O exemplo seguinte configura uma alegação **de Email Alternativo** com a máscara `Regex`:

```XML
<ClaimType Id="AlternateEmail">
  <DisplayName>Please verify the secondary email linked to your account</DisplayName>
  <DataType>string</DataType>
  <Mask Type="Regex" Regex="(?&lt;=.).(?=.*@)">*</Mask>
  <UserInputType>Readonly</UserInputType>
</ClaimType>
```

O Quadro de Experiência de Identidade torna apenas a primeira letra do endereço de e-mail e o nome de domínio do e-mail:

![Pedido de e-mail mostrado no navegador com caracteres mascarados por asteriscos](./media/claimsschema/mask-regex.png)


### <a name="restriction"></a>Restrição

O elemento **restrição** pode conter o seguinte atributo:

| Atributo | Necessário | Descrição |
| --------- | -------- | ----------- |
| Função Função Behavior | Não | O método utilizado para fundir valores de enumeração com um ClaimType numa política-mãe com o mesmo identificador. Use este atributo quando substituir uma reclamação especificada na política de base. Valores possíveis: `Append`, `Prepend`ou `ReplaceAll`. O valor `Append` é uma recolha de dados que deve ser anexado ao fim da recolha especificada na política-mãe. O valor `Prepend` é uma recolha de dados que deve ser adicionado antes da recolha especificada na política-mãe. O valor `ReplaceAll` é uma recolha de dados especificados na política-mãe que deve ser ignorada. |

O elemento **restrição** contém os seguintes elementos:

| Elemento | Ocorrências | Descrição |
| ------- | ----------- | ----------- |
| Enumeração | 1:n | As opções disponíveis na interface de utilizador para o utilizador selecionar para uma reclamação, como um valor em uma queda. |
| Padrão | 1:1 | A expressão regular a usar. |

#### <a name="enumeration"></a>Enumeração

O elemento **Enumeração** define as opções disponíveis para o utilizador selecionar para uma reclamação na interface do utilizador, como um valor num `CheckboxMultiSelect`, `DropdownSingleSelect`ou `RadioSingleSelect`. Em alternativa, pode definir e localizar as opções disponíveis com o elemento [LocalizedCollections.](localization.md#localizedcollections) Para procurar um item de uma coleção de **enumeração** de reclamações, use [GetMappedValueFromLocalizedCollection](string-transformations.md#getmappedvaluefromlocalizedcollection) reivindica a transformação de reivindicações.

O elemento **Enumeração** contém os seguintes atributos:

| Atributo | Necessário | Descrição |
| --------- | -------- | ----------- |
| Texto | Sim | A cadeia de visualização que é mostrada ao utilizador na interface do utilizador para esta opção. |
|Valor | Sim | O valor de reclamação que está associado à seleção desta opção. |
| SelectByDefault | Não | Indica se esta opção deve ou não ser selecionada por defeito na UI. Valores possíveis: Verdadeiro ou Falso. |

O exemplo seguinte configura uma reivindicação de lista de abandono da **cidade** com um valor predefinido definido para `New York`:

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

Lista de cidades dropdown com um valor padrão definido para Nova Iorque:

![Controlo de dropdown renderizado no navegador e mostrando valor padrão](./media/claimsschema/dropdownsingleselect.png)

### <a name="pattern"></a>Padrão

O elemento **Padrão** pode conter os seguintes atributos:

| Atributo | Necessário | Descrição |
| --------- | -------- | ----------- |
| Expressão regular | Sim | A expressão regular que reclama deste tipo deve coincidir para ser válida. |
| Texto de Ajuda | Não | Uma mensagem de erro para os utilizadores se a verificação de expressão regular falhar. |

O exemplo seguinte configura uma reclamação de **e-mail** com validação regular de entrada de expressão e texto de ajuda:

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

O Quadro de Experiência de Identidade torna a reclamação de endereço de e-mail com validação de entrada de formato de e-mail:

![TextBox mostrando mensagem de erro desencadeada pela restrição regex](./media/claimsschema/pattern.png)

### <a name="userinputtype"></a>UserInputType

O Azure AD B2C suporta uma variedade de tipos de entrada do utilizador, tais como uma caixa de texto, palavra-passe e lista de dropdown que podem ser usadas ao introduzir manualmente dados de reclamação para o tipo de reclamação. Deve especificar o **UserInputType** quando recolher informações do utilizador utilizando um [perfil técnico autoafirmado](self-asserted-technical-profile.md) e controlos de [visualização](display-controls.md).

Os tipos de entrada do utilizador do elemento **UserInputType** disponíveis:

| UserInputType | Tipo de reclamação suportado | Descrição |
| --------- | -------- | ----------- |
|Caixa de verificaçãoMultiSelect| `string` |Multi select drop-down box. O valor da reclamação está representado numa cadeia de limites de vírina dos valores selecionados. |
|DateTimeDropdown | `date`, `dateTime` |Drop-downs para selecionar um dia, mês e ano. |
|DropdownSingleSelect |`string` |Única caixa de entrega selecionada. O valor da reclamação é o valor selecionado.|
|EmailBox | `string` |Campo de entrada de e-mail. |
|Parágrafo | `boolean`, `date`, `dateTime`, `duration``int`, `long`, `string`|Um campo que mostra texto apenas numa etiqueta de parágrafo. |
|Palavra-passe | `string` |Caixa de texto de senha.|
|RadioSingleSelect |`string` | Coleção de botões de rádio. O valor da reclamação é o valor selecionado.|
|Leitura apenas | `boolean`, `date`, `dateTime`, `duration``int`, `long`, `string`| Caixa de texto só de leitura. |
|TextBox |`boolean`, `int`, `string` |Caixa de texto de linha única. |


#### <a name="textbox"></a>TextBox

O tipo de entrada do utilizador **TextBox** é utilizado para fornecer uma caixa de texto de uma única linha.

![TextBox mostrando propriedades especificadas no tipo de reclamação](./media/claimsschema/textbox.png)

```XML
<ClaimType Id="displayName">
  <DisplayName>Display Name</DisplayName>
  <DataType>string</DataType>
  <UserHelpText>Your display name.</UserHelpText>
  <UserInputType>TextBox</UserInputType>
</ClaimType>
```

#### <a name="emailbox"></a>EmailBox

O tipo de entrada do utilizador **EmailBox** é utilizado para fornecer um campo de entrada de e-mail básico.

![EmailBox mostrando propriedades especificadas no tipo de reclamação](./media/claimsschema/emailbox.png)

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

#### <a name="password"></a>Palavra-passe

O tipo de entrada do utilizador **password** é utilizado para registar uma palavra-passe introduzida pelo utilizador.

![Utilização do tipo de reclamação com senha](./media/claimsschema/password.png)

```XML
<ClaimType Id="password">
  <DisplayName>Password</DisplayName>
  <DataType>string</DataType>
  <UserHelpText>Enter password</UserHelpText>
  <UserInputType>Password</UserInputType>
</ClaimType>
```

#### <a name="datetimedropdown"></a>DateTimeDropdown

O tipo de entrada do utilizador **DateTimeDropdown** é utilizado para fornecer um conjunto de drop-downs para selecionar um dia, mês e ano. Pode utilizar elementos Predicados e Predicados Para controlar os valores mínimos e máximos da data. Para mais informações, consulte a secção de intervalo de **datas** de [Predicados e Predicadas](predicates.md).

![Utilização do tipo de reclamação com data-dropdown](./media/claimsschema/datetimedropdown.png)

```XML
<ClaimType Id="dateOfBirth">
  <DisplayName>Date Of Birth</DisplayName>
  <DataType>date</DataType>
  <UserHelpText>The date on which you were born.</UserHelpText>
  <UserInputType>DateTimeDropdown</UserInputType>
</ClaimType>
```

#### <a name="radiosingleselect"></a>RadioSingleSelect

O tipo de entrada do utilizador **RadioSingleSelect** é utilizado para fornecer uma recolha de botões de rádio que permite ao utilizador selecionar uma opção.

![Utilização do tipo de reclamação com radiodsingleselect](./media/claimsschema/radiosingleselect.png)

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

#### <a name="dropdownsingleselect"></a>DropdownSingleSelect

O tipo de entrada do utilizador **DropdownSingleSelect** é utilizado para fornecer uma caixa de drop-down que permite ao utilizador selecionar uma opção.

![Utilização do tipo de reclamação com dropdownsingleselect](./media/claimsschema/dropdownsingleselect.png)

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

#### <a name="checkboxmultiselect"></a>Caixa de verificaçãoMultiSelect

O tipo de entrada do utilizador **CheckboxMultiSelect** é utilizado para fornecer uma recolha de caixas de verificação que permite ao utilizador selecionar várias opções.

![Utilização do tipo de reclamação com checkboxmultiselect](./media/claimsschema/checkboxmultiselect.png)

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

#### <a name="readonly"></a>Leitura apenas

O tipo de entrada do utilizador **Readonly** é utilizado para fornecer um campo de leitura apenas para exibir a reclamação e o valor.

![Utilização do tipo de reclamação com leitura apenas](./media/claimsschema/readonly.png)

```XML
<ClaimType Id="membershipNumber">
  <DisplayName>Membership number</DisplayName>
  <DataType>string</DataType>
  <UserHelpText>Your membership number (read only)</UserHelpText>
  <UserInputType>Readonly</UserInputType>
</ClaimType>
```


#### <a name="paragraph"></a>Parágrafo

O tipo de entrada do **utilizador do parágrafo** é utilizado para fornecer um campo que apresente texto apenas numa etiqueta de parágrafo.  Por exemplo, &lt;p&gt;texto&lt;/p&gt;. Um tipo de entrada do utilizador **parágrafo** `OutputClaim` de perfil técnico autoafirmado, deve definir o `false` de atributo `Required` (predefinido).

![Utilização do tipo de reclamação com parágrafo](./media/claimsschema/paragraph.png)

```XML
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
