---
title: Predicados e PredicadosValidações
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
ms.openlocfilehash: 887c9432f04cce775e045bb6da83f0af4a4a4bce
ms.sourcegitcommit: 632e7ed5449f85ca502ad216be8ec5dd7cd093cb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2020
ms.locfileid: "80396897"
---
# <a name="predicates-and-predicatevalidations"></a>Predicados e PredicadosValidações

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Os **elementos Predicados** e **Predicados Validações** permitem-lhe realizar um processo de validação para garantir que apenas os dados devidamente formados são introduzidos no seu inquilino Azure Ative Directory B2C (Azure AD B2C).

O diagrama seguinte mostra a relação entre os elementos:

![Diagrama mostrando predicados e predicados relacionamento de validações](./media/predicates/predicates.png)

## <a name="predicates"></a>Predicados

O elemento **Predicado** define uma validação básica para verificar `true` `false`o valor de um tipo de reclamação e devoluções ou . A validação é feita utilizando um elemento **método** especificado e um conjunto de elementos **parâmetros** relevantes para o método. Por exemplo, um predicado pode verificar se o comprimento de um valor de reclamação de cadeia está dentro do intervalo dos parâmetros mínimos e máximos especificados, ou se um valor de reclamação de cadeia contém um conjunto de caracteres. O elemento **UserHelpText** fornece uma mensagem de erro para os utilizadores se a verificação falhar. O valor do elemento **UserHelpText** pode ser localizado através da [personalização do idioma.](localization.md)

O elemento **Predicado** deve aparecer diretamente seguindo o elemento **ClaimsSchema** dentro do elemento [BuildingBlocks.](buildingblocks.md)

O elemento **Predicado** contém o seguinte elemento:

| Elemento | Ocorrências | Descrição |
| ------- | ----------- | ----------- |
| Predicado | 1:n | Uma lista de predicados. |

O elemento **Predicado** contém os seguintes atributos:

| Atributo | Necessário | Descrição |
| --------- | -------- | ----------- |
| Id | Sim | Um identificador que é usado para o predicado. Outros elementos podem utilizar este identificador na política. |
| Método | Sim | O tipo de método a utilizar para validação. Valores possíveis: [IsLengthRange,](#islengthrange) [MatchesRegex,](#matchesregex) [IncluiCharacters](#includescharacters), ou [IsDateRange](#isdaterange).  |
| Texto de Ajuda | Não | Uma mensagem de erro para os utilizadores se o cheque falhar. Esta cadeia pode ser localizada usando a [personalização](localization.md) da linguagem |

O elemento **Predicado** contém os seguintes elementos:

| Elemento | Ocorrências | Descrição |
| ------- | ----------- | ----------- |
| UserHelpText | 0:1 | (Depreciado) Uma mensagem de erro para os utilizadores se o cheque falhar. |
| Parâmetros | 1:1 | Os parâmetros para o tipo de método da validação da corda. |

O elemento **Parâmetros** contém os seguintes elementos:

| Elemento | Ocorrências | Descrição |
| ------- | ----------- | ----------- |
| Parâmetro | 1:n | Os parâmetros para o tipo de método da validação da corda. |

O elemento **Parâmetro** contém os seguintes atributos:

| Elemento | Ocorrências | Descrição |
| ------- | ----------- | ----------- |
| Id | 1:1 | O identificador do parâmetro. |

### <a name="predicate-methods"></a>Métodos predicados

#### <a name="islengthrange"></a>IsLengthRange

O método IsLengthRange verifica se o comprimento de um valor de reclamação de cadeia está dentro do intervalo dos parâmetros mínimos e máximos especificados. O elemento predicado suporta os seguintes parâmetros:

| Parâmetro | Necessário | Descrição |
| ------- | ----------- | ----------- |
| Máximo | Sim | O número máximo de caracteres que podem ser introduzidos. |
| Mínimo | Sim | O número mínimo de caracteres que devem ser inscritos. |


O exemplo seguinte mostra um método IsLengthRange com os parâmetros `Minimum` e `Maximum` que especifica a faixa de comprimento da cadeia:

```XML
<Predicate Id="IsLengthBetween8And64" Method="IsLengthRange" HelpText="The password must be between 8 and 64 characters.">
  <Parameters>
    <Parameter Id="Minimum">8</Parameter>
    <Parameter Id="Maximum">64</Parameter>
  </Parameters>
</Predicate>
```

#### <a name="matchesregex"></a>MatchRegex

O método MatchRegex verifica se um valor de reclamação de cordas corresponde a uma expressão regular. O elemento predicado suporta os seguintes parâmetros:

| Parâmetro | Necessário | Descrição |
| ------- | ----------- | ----------- |
| Expressão regular | Sim | O padrão de expressão regular para combinar. |

O exemplo que `MatchesRegex` se segue `RegularExpression` mostra um método com o parâmetro que especifica uma expressão regular:

```XML
<Predicate Id="PIN" Method="MatchesRegex" HelpText="The password must be numbers only.">
  <Parameters>
    <Parameter Id="RegularExpression">^[0-9]+$</Parameter>
  </Parameters>
</Predicate>
```

#### <a name="includescharacters"></a>Inclui personagens

O método IncludesCharacters verifica se um valor de reclamação de cordas contém um conjunto de caracteres. O elemento predicado suporta os seguintes parâmetros:

| Parâmetro | Necessário | Descrição |
| ------- | ----------- | ----------- |
| Conjunto de caracteres | Sim | O conjunto de caracteres que podem ser inseridos. Por exemplo, caracteres `a-z`minúsculos, `A-Z`caracteres `0-9`maiúsculos, dígitos, `@#$%^&amp;*\-_+=[]{}|\\:',?/~"();!`ou uma lista de símbolos, tais como . |

O exemplo seguinte `IncludesCharacters` mostra um `CharacterSet` método com o parâmetro que especifica o conjunto de caracteres:

```XML
<Predicate Id="Lowercase" Method="IncludesCharacters" HelpText="a lowercase letter">
  <Parameters>
    <Parameter Id="CharacterSet">a-z</Parameter>
  </Parameters>
</Predicate>
```

#### <a name="isdaterange"></a>IsDateRange

O método IsDateRange verifica se o valor da reclamação de data é entre um intervalo de parâmetros mínimos e máximos especificados. O elemento predicado suporta os seguintes parâmetros:

| Parâmetro | Necessário | Descrição |
| ------- | ----------- | ----------- |
| Máximo | Sim | A maior data possível que pode ser inserida. O formato da `yyyy-mm-dd` data `Today`segue a convenção, ou . |
| Mínimo | Sim | A menor data possível que pode ser inserida. O formato da `yyyy-mm-dd` data `Today`segue a convenção, ou .|

O exemplo seguinte `IsDateRange` mostra um `Minimum` método `Maximum` com os parâmetros e `yyyy-mm-dd` `Today`que especifica a gama de datas com um formato de e .

```XML
<Predicate Id="DateRange" Method="IsDateRange" HelpText="The date must be between 1970-01-01 and today.">
  <Parameters>
    <Parameter Id="Minimum">1970-01-01</Parameter>
    <Parameter Id="Maximum">Today</Parameter>
  </Parameters>
</Predicate>
```

## <a name="predicatevalidations"></a>PredicadasValidações

Enquanto os predicados definem a validação para verificar contra um tipo de reclamação, o grupo **PredicadoValidações** um conjunto de predicados para formar uma validação de entrada do utilizador que pode ser aplicada a um tipo de reclamação. Cada elemento **PredicadoValidação** contém um conjunto de elementos **PredicadosGroup** que contêm um conjunto de elementos **PredicadosReferência** que aponta para um **Predicado**. Para passar a validação, o valor da reclamação deve passar todos os testes de qualquer predicado sob todo o **PredicadoGroup** com o seu conjunto de elementos **PredicadosReference.**

O elemento **PredicadoValidações** deve aparecer diretamente seguindo o elemento **Predicado** dentro do elemento [BuildingBlocks.](buildingblocks.md)

```XML
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

O elemento **PredicadoValidações** contém o seguinte elemento:

| Elemento | Ocorrências | Descrição |
| ------- | ----------- | ----------- |
| PredicadoValidação | 1:n | Uma lista de validação predicada. |

O elemento **PredicadoValidação** contém o seguinte atributo:

| Atributo | Necessário | Descrição |
| --------- | -------- | ----------- |
| Id | Sim | Um identificador que é usado para a validação predicada. O elemento **ClaimType** pode utilizar este identificador na política. |

O elemento **PredicadoValidação** contém o seguinte elemento:

| Elemento | Ocorrências | Descrição |
| ------- | ----------- | ----------- |
| Grupos Predicados | 1:n | Uma lista de grupos predicados. |

O elemento **PredicadosGrupos** contém o seguinte elemento:

| Elemento | Ocorrências | Descrição |
| ------- | ----------- | ----------- |
| Grupo Predicado | 1:n | Uma lista de predicados. |

O elemento **PredicateGroup** contém o seguinte atributo:

| Atributo | Necessário | Descrição |
| --------- | -------- | ----------- |
| Id | Sim | Um identificador que é usado para o grupo predicado.  |

O elemento **PredicadoGroup** contém os seguintes elementos:

| Elemento | Ocorrências | Descrição |
| ------- | ----------- | ----------- |
| UserHelpText | 0:1 |  Uma descrição do predicado que pode ser útil para os utilizadores saberem qual o valor que devem escrever. |
| Referências Predicadas | 1:n | Uma lista de referências predicadas. |

O elemento **PredicadoReferências** contém os seguintes atributos:

| Atributo | Necessário | Descrição |
| --------- | -------- | ----------- |
| Matchatleast | Não | Especifica que o valor deve corresponder, pelo menos, a muitas definições predicadas para a entrada a aceitar. Se não especificado, o valor deve corresponder a todas as definições predicadas. |

O elemento **PredicadoReferências** contém os seguintes elementos:

| Elemento | Ocorrências | Descrição |
| ------- | ----------- | ----------- |
| Referência predicada | 1:n | Uma referência a um predicado. |

O elemento **PredicadoReference** contém os seguintes atributos:

| Atributo | Necessário | Descrição |
| --------- | -------- | ----------- |
| Id | Sim | Um identificador que é usado para a validação predicada.  |


## <a name="configure-password-complexity"></a>Configurar a complexidade da palavra-passe

Com **Predicados** e **PredicadosValidaçõesInputputpode** controlar os requisitos de complexidade das palavras-passe fornecidas por um utilizador ao criar uma conta. Por predefinição, o Azure AD B2C utiliza senhas fortes. O Azure AD B2C também suporta opções de configuração para controlar a complexidade das palavras-passe que os clientes podem usar. Pode definir a complexidade da palavra-passe utilizando estes elementos predicados:

- **IsLengthBetween8And64** utilizando `IsLengthRange` o método, valida que a palavra-passe deve estar entre 8 e 64 caracteres.
- **Minúscula** utilizando `IncludesCharacters` o método, valida que a palavra-passe contém uma letra minúscula.
- **Maiúscula** utilizando o `IncludesCharacters` método, valida que a palavra-passe contém uma letra maiúscula.
- **Número** utilizando `IncludesCharacters` o método, valida que a palavra-passe contém um dígito.
- **Símbolo** utilizando `IncludesCharacters` o método, valida que a palavra-passe contém um dos vários caracteres de símbolo.
- **PIN** utilizando `MatchesRegex` o método, valida que a palavra-passe contém apenas números.
- **AllowedAADCharacters** usando `MatchesRegex` o método, valida que a palavra-passe apenas caracteres inválidos foi fornecido.
- **O Espaço White** não `MatchesRegex` autorizado utilizando o método, valida que a palavra-passe não começa ou termina com um personagem de whitespace.

```XML
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

- **SimplePassword** valida o Espaço Branco Não Permitido, AllowedAADCharacters e IsLengthBetween8And64
- **StrongPassword** valida o Espaço Branco Autorizado, AllowedAADCharacters, IsLengthBetween8And64. O último `CharacterClasses` grupo executa um conjunto `MatchAtLeast` adicional de predicados com conjunto de 3. A palavra-passe do utilizador deve estar entre 8 e 16 caracteres, e três dos seguintes caracteres: Minúscula, Maiúscula, Número ou Símbolo.
- **CustomPassword** valida apenas Espaços Brancos Não Autorizados, AllowedAADCharacters. Assim, o utilizador pode fornecer qualquer palavra-passe com qualquer comprimento, desde que os caracteres sejam válidos.

```XML
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

No seu tipo de reclamação, adicione o elemento **PredicadoValidaçãoReference** e especifique o identificador como uma das validações predicadas, tais como SimplePassword, StrongPassword ou CustomPassword.

```XML
<ClaimType Id="password">
  <DisplayName>Password</DisplayName>
  <DataType>string</DataType>
  <AdminHelpText>Enter password</AdminHelpText>
  <UserHelpText>Enter password</UserHelpText>
  <UserInputType>Password</UserInputType>
  <PredicateValidationReference Id="StrongPassword" />
</ClaimType>
```

O seguinte mostra como os elementos são organizados quando o Azure AD B2C exibe a mensagem de erro:

![Diagrama de Predicado e PredicadoExemplo de complexidade da palavra-passe](./media/predicates/predicates-pass.png)

## <a name="configure-a-date-range"></a>Configurar uma gama de datas

Com os **elementos Predicados** e **PredicadosValidações** pode controlar os valores mínimos e `DateTimeDropdown`máximos de data do **UserInputType** utilizando a . Para tal, crie um **Predicado** com o `IsDateRange` método e forneça os parâmetros mínimos e máximos.

```XML
<Predicates>
  <Predicate Id="DateRange" Method="IsDateRange" HelpText="The date must be between 01-01-1980 and today.">
    <Parameters>
      <Parameter Id="Minimum">1980-01-01</Parameter>
      <Parameter Id="Maximum">Today</Parameter>
    </Parameters>
  </Predicate>
</Predicates>
```

Adicione uma **Validação Predicada** `DateRange` com uma referência ao predicado.

```XML
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

No seu tipo de reclamação, adicione o elemento `CustomDateRange` **PredicadoValidaçãoReference e** especifique o identificador como .

```XML
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

- Saiba como configurar a complexidade da [palavra-passe utilizando políticas personalizadas no Diretório Ativo Azure B2C](custom-policy-password-complexity.md) utilizando validações predicadas.