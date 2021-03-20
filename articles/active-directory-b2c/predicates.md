---
title: Predicados e PredicateValidations
titleSuffix: Azure AD B2C
description: Evite que os dados mal formados sejam adicionados ao seu inquilino Azure AD B2C utilizando políticas personalizadas no Azure Ative Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/30/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 46f04c55b40d4f1bdbbf5fd55eb648d1d3294056
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "97108421"
---
# <a name="predicates-and-predicatevalidations"></a>Predicados e PredicateValidations

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Os elementos **Predicates** e **PredicateValidations** permitem-lhe realizar um processo de validação para garantir que apenas os dados devidamente formados são introduzidos no seu inquilino Azure Ative Directory B2C (Azure AD B2C).

O diagrama a seguir mostra a relação entre os elementos:

![Diagrama mostrando predicados e relacionamento de validações predicate](./media/predicates/predicates.png)

## <a name="predicates"></a>Predicados

O elemento **Predicado** define uma validação básica para verificar o valor de um tipo de reclamação e devoluções `true` ou `false` . A validação é feita utilizando um elemento **método** especificado e um conjunto de elementos **parâmetros** relevantes para o método. Por exemplo, um predicado pode verificar se o comprimento de um valor de reclamação de cadeia está dentro do intervalo de parâmetros mínimos e máximos especificados, ou se um valor de reclamação de cadeia contém um conjunto de caracteres. O elemento **UserHelpText** fornece uma mensagem de erro para os utilizadores se a verificação falhar. O valor do elemento **UserHelpText** pode ser localizado através da [personalização da linguagem.](localization.md)

O elemento **Predicates** deve aparecer diretamente seguindo o elemento **ClaimsSchema** dentro do elemento [Blocos de Construção.](buildingblocks.md)

O elemento **Predicates** contém o seguinte elemento:

| Elemento | Ocorrências | Description |
| ------- | ----------- | ----------- |
| Predicado | 1:n | Uma lista de predicados. |

O elemento **Predicado** contém os seguintes atributos:

| Atributo | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| Id | Yes | Um identificador que é usado para o predicado. Outros elementos podem usar este identificador na apólice. |
| Método | Yes | O tipo de método a utilizar para validação. Valores possíveis: [IsLengthRange](#islengthrange), [MatchRegex,](#matchesregex) [IncludesCharacters,](#includescharacters)ou [IsDateRange](#isdaterange).  |
| HelpText | No | Uma mensagem de erro para os utilizadores se a verificação falhar. Esta cadeia pode ser localizada usando a personalização do [idioma](localization.md) |

O elemento **Predicado** contém os seguintes elementos:

| Elemento | Ocorrências | Description |
| ------- | ----------- | ----------- |
| UserHelpText | 0:1 | (Precatado) Uma mensagem de erro para os utilizadores se a verificação falhar. |
| Parâmetros | 1:1 | Os parâmetros para o tipo de método da validação da corda. |

O elemento **Parâmetros** contém os seguintes elementos:

| Elemento | Ocorrências | Description |
| ------- | ----------- | ----------- |
| Parâmetro | 1:n | Os parâmetros para o tipo de método da validação da corda. |

O elemento **parâmetro** contém os seguintes atributos:

| Elemento | Ocorrências | Description |
| ------- | ----------- | ----------- |
| Id | 1:1 | O identificador do parâmetro. |

### <a name="predicate-methods"></a>Métodos predicados

#### <a name="islengthrange"></a>IsLengthRange

O método IsLengthRange verifica se o comprimento de um valor de pedido de cadeia está dentro do intervalo de parâmetros mínimos e máximos especificados. O elemento predicado suporta os seguintes parâmetros:

| Parâmetro | Obrigatório | Descrição |
| ------- | ----------- | ----------- |
| Máximo | Yes | O número máximo de caracteres que podem ser introduzidos. |
| Mínimo | Yes | O número mínimo de caracteres que devem ser introduzidos. |


O exemplo a seguir mostra um método IsLengthRange com os parâmetros `Minimum` e `Maximum` que especificam a gama de comprimentos da cadeia:

```xml
<Predicate Id="IsLengthBetween8And64" Method="IsLengthRange" HelpText="The password must be between 8 and 64 characters.">
  <Parameters>
    <Parameter Id="Minimum">8</Parameter>
    <Parameter Id="Maximum">64</Parameter>
  </Parameters>
</Predicate>
```

#### <a name="matchesregex"></a>MatchRegex

O método MatchRegex verifica se um valor de reclamação de corda corresponde a uma expressão regular. O elemento predicado suporta os seguintes parâmetros:

| Parâmetro | Obrigatório | Descrição |
| ------- | ----------- | ----------- |
| Expressão Regular | Yes | O padrão de expressão regular para combinar. |

O exemplo a seguir mostra um `MatchesRegex` método com o parâmetro que especifica uma expressão `RegularExpression` regular:

```xml
<Predicate Id="PIN" Method="MatchesRegex" HelpText="The password must be numbers only.">
  <Parameters>
    <Parameter Id="RegularExpression">^[0-9]+$</Parameter>
  </Parameters>
</Predicate>
```

#### <a name="includescharacters"></a>IncluiCharacters

O método IncludesCharacters verifica se um valor de reclamação de cadeia contém um conjunto de caracteres. O elemento predicado suporta os seguintes parâmetros:

| Parâmetro | Obrigatório | Descrição |
| ------- | ----------- | ----------- |
| Conjunto de Caracteres | Yes | O conjunto de caracteres que podem ser introduzidos. Por exemplo, caracteres  `a-z` minúsculos, caracteres `A-Z` maiúsculas, `0-9` dígitos, ou uma lista de símbolos, tais como `@#$%^&amp;*\-_+=[]{}|\\:',?/~"();!` . |

O exemplo a seguir mostra um `IncludesCharacters` método com o parâmetro que especifica o conjunto de `CharacterSet` caracteres:

```xml
<Predicate Id="Lowercase" Method="IncludesCharacters" HelpText="a lowercase letter">
  <Parameters>
    <Parameter Id="CharacterSet">a-z</Parameter>
  </Parameters>
</Predicate>
```

#### <a name="isdaterange"></a>IsDateRange

O método IsDateRange verifica se um valor de reclamação de data está entre um intervalo de parâmetros mínimos e máximos especificados. O elemento predicado suporta os seguintes parâmetros:

| Parâmetro | Obrigatório | Descrição |
| ------- | ----------- | ----------- |
| Máximo | Yes | A maior data possível que pode ser inscrita. O formato da data segue a `yyyy-mm-dd` convenção, ou `Today` . |
| Mínimo | Yes | A menor data possível que pode ser inscrita. O formato da data segue a `yyyy-mm-dd` convenção, ou `Today` .|

O exemplo a seguir mostra um `IsDateRange` método com os parâmetros e que `Minimum` `Maximum` especificam a gama de datas com um formato de `yyyy-mm-dd` e `Today` .

```xml
<Predicate Id="DateRange" Method="IsDateRange" HelpText="The date must be between 1970-01-01 and today.">
  <Parameters>
    <Parameter Id="Minimum">1970-01-01</Parameter>
    <Parameter Id="Maximum">Today</Parameter>
  </Parameters>
</Predicate>
```

## <a name="predicatevalidations"></a>PredicateValidations

Enquanto os predicados definem a validação para verificar contra um tipo de reclamação, o **grupo PredicateValidations** agrupa um conjunto de predicados para formar uma validação de entrada de utilizador que pode ser aplicada a um tipo de reclamação. Cada elemento **PredicateValidation** contém um conjunto de elementos **do Grupo Predicate** que contêm um conjunto de elementos **PredicateReference** que apontam para um **Predicate**. Para passar a validação, o valor da reclamação deve passar todos os testes de qualquer predicado em todo o **Grupo Predicate** com o seu conjunto de elementos **PredicateReference.**

O elemento **PredicateValidations** deve aparecer diretamente seguindo o elemento **Predicates** dentro do elemento [Blocos de Construção.](buildingblocks.md)

```xml
<PredicateValidations>
  <PredicateValidation Id="">
    <PredicateGroups>
      <PredicateGroup Id="">
        <UserHelpText></UserHelpText>
        <PredicateReferences MatchAtLeast="">
          <PredicateReference Id="" />
          ...
        </PredicateReferences>
      </PredicateGroup>
      ...
    </PredicateGroups>
  </PredicateValidation>
...
</PredicateValidations>
```

O elemento **PredicateValidations** contém o seguinte elemento:

| Elemento | Ocorrências | Description |
| ------- | ----------- | ----------- |
| Pré-validade | 1:n | Uma lista de validação predicado. |

O elemento **PredicateValidation** contém o seguinte atributo:

| Atributo | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| Id | Yes | Um identificador que é usado para validação predicado. O elemento **ClaimType** pode utilizar este identificador na apólice. |

O elemento **PredicateValidation** contém o seguinte elemento:

| Elemento | Ocorrências | Description |
| ------- | ----------- | ----------- |
| Grupos Predicados | 1:n | Uma lista de grupos predicados. |

O elemento **PredicateGroups** contém o seguinte elemento:

| Elemento | Ocorrências | Description |
| ------- | ----------- | ----------- |
| Grupo Predicate | 1:n | Uma lista de predicados. |

O elemento **PredicateGroup** contém o seguinte atributo:

| Atributo | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| Id | Yes | Um identificador que é usado para o grupo predicado.  |

O elemento **PredicateGroup** contém os seguintes elementos:

| Elemento | Ocorrências | Description |
| ------- | ----------- | ----------- |
| UserHelpText | 0:1 |  Uma descrição do predicado que pode ser útil para os utilizadores saberem que valor devem escrever. |
| PredicateReferências | 1:n | Uma lista de referências predicados. |

O elemento **PredicateReferences** contém os seguintes atributos:

| Atributo | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| MatchAtLeast | No | Especifica que o valor deve corresponder pelo menos às muitas definições predicados para a entrada ser aceite. Se não for especificado, o valor deve corresponder a todas as definições predicados. |

O elemento **PredicateReferences** contém os seguintes elementos:

| Elemento | Ocorrências | Description |
| ------- | ----------- | ----------- |
| Pré-referência | 1:n | Uma referência a um predicado. |

O elemento **PredicateReference** contém os seguintes atributos:

| Atributo | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| Id | Yes | Um identificador que é usado para validação predicado.  |


## <a name="configure-password-complexity"></a>Configure a complexidade da palavra-passe

Com **Predicates** e **PredicateValidationsInte** pode controlar os requisitos de complexidade das palavras-passe fornecidas por um utilizador ao criar uma conta. Por padrão, o Azure AD B2C utiliza senhas fortes. O Azure AD B2C também suporta opções de configuração para controlar a complexidade das palavras-passe que os clientes podem usar. Pode definir a complexidade da palavra-passe utilizando estes elementos predicados:

- **IsLengthBetween8And64** usando o `IsLengthRange` método, valida que a palavra-passe deve estar entre 8 e 64 caracteres.
- **Minúscula** utilizando o `IncludesCharacters` método, valida que a palavra-passe contém uma letra minúscula.
- **A maiúscula** utilizando o `IncludesCharacters` método, valida que a palavra-passe contém uma letra maiúscula.
- **Número** utilizando o `IncludesCharacters` método, valida que a palavra-passe contém um dígito.
- **O símbolo** utilizando o `IncludesCharacters` método, valida que a palavra-passe contém um dos vários caracteres de símbolos.
- **PIN** utilizando o `MatchesRegex` método, valida que a palavra-passe contém apenas números.
- **Permitido AADCharacters** usando o `MatchesRegex` método, valida que a palavra-passe apenas foi fornecida de caráter inválido.
- **O Whitespace não permitido** usando o `MatchesRegex` método, valida que a palavra-passe não começa ou termina com um personagem de espaço branco.

```xml
<Predicates>
  <Predicate Id="IsLengthBetween8And64" Method="IsLengthRange" HelpText="The password must be between 8 and 64 characters.">
    <Parameters>
      <Parameter Id="Minimum">8</Parameter>
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

  <Predicate Id="Number" Method="IncludesCharacters" HelpText="a digit">
    <Parameters>
      <Parameter Id="CharacterSet">0-9</Parameter>
    </Parameters>
  </Predicate>

  <Predicate Id="Symbol" Method="IncludesCharacters" HelpText="a symbol">
    <Parameters>
      <Parameter Id="CharacterSet">@#$%^&amp;*\-_+=[]{}|\\:',.?/`~"();!</Parameter>
    </Parameters>
  </Predicate>

  <Predicate Id="PIN" Method="MatchesRegex" HelpText="The password must be numbers only.">
    <Parameters>
      <Parameter Id="RegularExpression">^[0-9]+$</Parameter>
    </Parameters>
  </Predicate>

  <Predicate Id="AllowedAADCharacters" Method="MatchesRegex" HelpText="An invalid character was provided.">
    <Parameters>
      <Parameter Id="RegularExpression">(^([0-9A-Za-z\d@#$%^&amp;*\-_+=[\]{}|\\:',?/`~"();! ]|(\.(?!@)))+$)|(^$)</Parameter>
    </Parameters>
  </Predicate>

  <Predicate Id="DisallowedWhitespace" Method="MatchesRegex" HelpText="The password must not begin or end with a whitespace character.">
    <Parameters>
      <Parameter Id="RegularExpression">(^\S.*\S$)|(^\S+$)|(^$)</Parameter>
    </Parameters>
  </Predicate>
```

Depois de definir as validações básicas, pode combiná-las e criar um conjunto de políticas de senha que pode utilizar na sua política:

- **SimplePassword** valida o EspaçoWhite, AllowedAADCharacters e IsLengthBetween8And64
- **O StrongPassword** valida o EspaçoWhite, AllowedAADCharacters, IsLengthBetween8And64. O último grupo `CharacterClasses` executa um conjunto adicional de predicados com definido para `MatchAtLeast` 3. A palavra-passe do utilizador deve ter entre 8 e 16 caracteres e três dos seguintes caracteres: Minúscula, Maiúscula, Número ou Símbolo.
- **O CustomPassword** valida apenas o EspaçoWhite, PermitidoS AADCharacters. Assim, o utilizador pode fornecer qualquer palavra-passe com qualquer comprimento, desde que os caracteres sejam válidos.

```xml
<PredicateValidations>
  <PredicateValidation Id="SimplePassword">
    <PredicateGroups>
      <PredicateGroup Id="DisallowedWhitespaceGroup">
        <PredicateReferences>
          <PredicateReference Id="DisallowedWhitespace" />
        </PredicateReferences>
      </PredicateGroup>
      <PredicateGroup Id="AllowedAADCharactersGroup">
        <PredicateReferences>
          <PredicateReference Id="AllowedAADCharacters" />
        </PredicateReferences>
      </PredicateGroup>
      <PredicateGroup Id="LengthGroup">
        <PredicateReferences>
          <PredicateReference Id="IsLengthBetween8And64" />
        </PredicateReferences>
      </PredicateGroup>
    </PredicateGroups>
  </PredicateValidation>

  <PredicateValidation Id="StrongPassword">
    <PredicateGroups>
      <PredicateGroup Id="DisallowedWhitespaceGroup">
        <PredicateReferences>
          <PredicateReference Id="DisallowedWhitespace" />
       </PredicateReferences>
      </PredicateGroup>
      <PredicateGroup Id="AllowedAADCharactersGroup">
        <PredicateReferences>
          <PredicateReference Id="AllowedAADCharacters" />
        </PredicateReferences>
      </PredicateGroup>
      <PredicateGroup Id="LengthGroup">
        <PredicateReferences>
          <PredicateReference Id="IsLengthBetween8And64" />
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

  <PredicateValidation Id="CustomPassword">
    <PredicateGroups>
      <PredicateGroup Id="DisallowedWhitespaceGroup">
        <PredicateReferences>
          <PredicateReference Id="DisallowedWhitespace" />
        </PredicateReferences>
      </PredicateGroup>
      <PredicateGroup Id="AllowedAADCharactersGroup">
        <PredicateReferences>
          <PredicateReference Id="AllowedAADCharacters" />
        </PredicateReferences>
      </PredicateGroup>
    </PredicateGroups>
  </PredicateValidation>
</PredicateValidations>
```

No seu tipo de reclamação, adicione o elemento **PredicateValidationReference** e especifique o identificador como uma das validações predicados, tais como SimplePassword, StrongPassword ou CustomPassword.

```xml
<ClaimType Id="password">
  <DisplayName>Password</DisplayName>
  <DataType>string</DataType>
  <AdminHelpText>Enter password</AdminHelpText>
  <UserHelpText>Enter password</UserHelpText>
  <UserInputType>Password</UserInputType>
  <PredicateValidationReference Id="StrongPassword" />
</ClaimType>
```

O seguinte mostra como os elementos são organizados quando a Azure AD B2C exibe a mensagem de erro:

![Diagrama de Exemplo de complexidade da palavra-passe Predicado e PredicateGroup](./media/predicates/predicates-pass.png)

## <a name="configure-a-date-range"></a>Configurar um intervalo de datas

Com os **elementos Predicates** e **PredicateValidations** pode controlar os valores mínimos e máximos de data do **UserInputType** utilizando um `DateTimeDropdown` . Para isso, crie um **Predicado** com o `IsDateRange` método e forneça os parâmetros mínimos e máximos.

```xml
<Predicates>
  <Predicate Id="DateRange" Method="IsDateRange" HelpText="The date must be between 01-01-1980 and today.">
    <Parameters>
      <Parameter Id="Minimum">1980-01-01</Parameter>
      <Parameter Id="Maximum">Today</Parameter>
    </Parameters>
  </Predicate>
</Predicates>
```

Adicione uma **PrécateValidação** com uma referência ao `DateRange` predicado.

```xml
<PredicateValidations>
  <PredicateValidation Id="CustomDateRange">
    <PredicateGroups>
      <PredicateGroup Id="DateRangeGroup">
        <PredicateReferences>
          <PredicateReference Id="DateRange" />
        </PredicateReferences>
      </PredicateGroup>
    </PredicateGroups>
  </PredicateValidation>
</PredicateValidations>
```

No seu tipo de reclamação, adicione o elemento **PredicateValidationReference** e especifique o identificador como `CustomDateRange` .

```xml
<ClaimType Id="dateOfBirth">
  <DisplayName>Date of Birth</DisplayName>
  <DataType>date</DataType>
  <AdminHelpText>The user's date of birth.</AdminHelpText>
  <UserHelpText>Your date of birth.</UserHelpText>
  <UserInputType>DateTimeDropdown</UserInputType>
  <PredicateValidationReference Id="CustomDateRange" />
</ClaimType>
 ```

## <a name="next-steps"></a>Passos seguintes

- Aprenda a configurar a [complexidade da palavra-passe utilizando políticas personalizadas no Azure Ative Directory B2C](password-complexity.md) utilizando validações predicados.