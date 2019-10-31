---
title: Predicados e PredicateValidations-Azure Active Directory B2C | Microsoft Docs
description: Exemplos de transformação de declarações de conta social para o esquema de estrutura de experiência de identidade de Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 10/28/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: a1f08589ae28b3e19d2a4fdb3e3862e127a810cc
ms.sourcegitcommit: b45ee7acf4f26ef2c09300ff2dba2eaa90e09bc7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73099709"
---
# <a name="predicates-and-predicatevalidations"></a>Predicados e PredicateValidations

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Os elementos **predicados** e **PredicateValidations** permitem que você execute um processo de validação para garantir que apenas dados formados corretamente sejam inseridos em seu locatário de Azure Active Directory B2C (Azure ad B2C).

O diagrama a seguir mostra a relação entre os elementos:

![Diagrama mostrando os predicados e a relação de validações de predicados](./media/predicates/predicates.png)

## <a name="predicates"></a>Predicados

O elemento **Predicate** define uma validação básica para verificar o valor de um tipo de declaração e retorna `true` ou `false`. A validação é feita usando um elemento de **método** especificado e um conjunto de elementos de **parâmetro** relevantes para o método. Por exemplo, um predicado pode verificar se o comprimento de um valor de declaração de cadeia de caracteres está dentro do intervalo de parâmetros mínimos e máximos especificados ou se um valor de declaração de cadeia de caracteres contém um conjunto de caracteres. O elemento **userhelptext** fornecerá uma mensagem de erro para os usuários se a verificação falhar. O valor do elemento **userhelptext** pode ser localizado usando a [personalização de idioma](localization.md).

O elemento **predicados** deve aparecer diretamente após o elemento **ClaimsSchema** dentro do elemento [BuildingBlocks](buildingblocks.md) .

O elemento **predicados** contém o seguinte elemento:

| Elemento | Ocorrências | Descrição |
| ------- | ----------- | ----------- |
| Predicado | 1: n | Uma lista de predicados. |

O elemento **Predicate** contém os seguintes atributos:

| Atributo | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| Id | Sim | Um identificador que é usado para o predicado. Outros elementos podem usar esse identificador na política. |
| Método | Sim | O tipo de método a ser usado para validação. Valores possíveis: **IsLengthRange**, **MatchesRegex**, **IncludesCharacters**ou **IsDateRange**. O valor **IsLengthRange** verifica se o comprimento de um valor de declaração de cadeia de caracteres está dentro do intervalo de parâmetros mínimo e máximo especificados. O valor **MatchesRegex** verifica se um valor de declaração de cadeia de caracteres corresponde a uma expressão regular. O valor **IncludesCharacters** verifica se um valor de declaração de cadeia de caracteres contém um conjunto de caracteres. O valor **IsDateRange** verifica se um valor de declaração de data está entre um intervalo de parâmetros mínimo e máximo especificados. |

O elemento **Predicate** contém os seguintes elementos:

| Elemento | Ocorrências | Descrição |
| ------- | ----------- | ----------- |
| Userhelptext | 1:1 | Uma mensagem de erro para os usuários se a verificação falhar. Essa cadeia de caracteres pode ser localizada usando a [personalização da linguagem](localization.md) |
| Parâmetros | 1:1 | Os parâmetros para o tipo de método da validação da cadeia de caracteres. |

O elemento **Parameters** contém os seguintes elementos:

| Elemento | Ocorrências | Descrição |
| ------- | ----------- | ----------- |
| Parâmetro | 1: n | Os parâmetros para o tipo de método da validação da cadeia de caracteres. |

O elemento **Parameter** contém os seguintes atributos:

| Elemento | Ocorrências | Descrição |
| ------- | ----------- | ----------- |
| Id | 1:1 | O identificador do parâmetro. |

O exemplo a seguir mostra um método `IsLengthRange` com os parâmetros `Minimum` e `Maximum` que especificam o intervalo de comprimento da cadeia de caracteres:

```XML
<Predicate Id="IsLengthBetween8And64" Method="IsLengthRange">
  <UserHelpText>The password must be between 8 and 64 characters.</UserHelpText>
    <Parameters>
      <Parameter Id="Minimum">8</Parameter>
      <Parameter Id="Maximum">64</Parameter>
  </Parameters>
</Predicate>
```

O exemplo a seguir mostra um método `MatchesRegex` com o parâmetro `RegularExpression` que especifica uma expressão regular:

```XML
<Predicate Id="PIN" Method="MatchesRegex">
  <UserHelpText>The password must be numbers only.</UserHelpText>
  <Parameters>
    <Parameter Id="RegularExpression">^[0-9]+$</Parameter>
  </Parameters>
</Predicate>
```

O exemplo a seguir mostra um método `IncludesCharacters` com o parâmetro `CharacterSet` que especifica o conjunto de caracteres:

```XML
<Predicate Id="Lowercase" Method="IncludesCharacters">
  <UserHelpText>a lowercase letter</UserHelpText>
  <Parameters>
    <Parameter Id="CharacterSet">a-z</Parameter>
  </Parameters>
</Predicate>
```

O exemplo a seguir mostra um método `IsDateRange` com os parâmetros `Minimum` e `Maximum` que especificam o intervalo de datas com um formato de `yyyy-MM-dd` e `Today`.

```XML
<Predicate Id="DateRange" Method="IsDateRange" HelpText="The date must be between 1970-01-01 and today.">
  <Parameters>
    <Parameter Id="Minimum">1970-01-01</Parameter>
    <Parameter Id="Maximum">Today</Parameter>
  </Parameters>
</Predicate>
```

## <a name="predicatevalidations"></a>PredicateValidations

Embora os predicados definam a validação a ser verificada em relação a um tipo de declaração, o grupo **PredicateValidations** um conjunto de predicados para formar uma validação de entrada de usuário que pode ser aplicada a um tipo de declaração. Cada elemento **PredicateValidation** contém um conjunto de elementos do grupo de **predicados** que contêm um conjunto de elementos **PredicateReference** que aponta para um **predicado**. Para passar a validação, o valor da declaração deve passar todos os testes de qualquer predicado em todo o grupo de **predicado** com seu conjunto de elementos **PredicateReference** .

O elemento **PredicateValidations** deve aparecer diretamente após o elemento **predicados** dentro do elemento [BuildingBlocks](buildingblocks.md) .

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

O elemento **PredicateValidations** contém o seguinte elemento:

| Elemento | Ocorrências | Descrição |
| ------- | ----------- | ----------- |
| PredicateValidation | 1: n | Uma lista de validação de predicado. |

O elemento **PredicateValidation** contém o seguinte atributo:

| Atributo | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| Id | Sim | Um identificador que é usado para a validação de predicado. O elemento **ClaimType** pode usar esse identificador na política. |

O elemento **PredicateValidation** contém o seguinte elemento:

| Elemento | Ocorrências | Descrição |
| ------- | ----------- | ----------- |
| PredicateGroups | 1: n | Uma lista de grupos de predicados. |

O elemento **PredicateGroups** contém o seguinte elemento:

| Elemento | Ocorrências | Descrição |
| ------- | ----------- | ----------- |
| Condicado | 1: n | Uma lista de predicados. |

O elemento de **Predicate** contém o seguinte atributo:

| Atributo | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| Id | Sim | Um identificador que é usado para o grupo de predicado.  |

O elemento de **Predicate** contém os seguintes elementos:

| Elemento | Ocorrências | Descrição |
| ------- | ----------- | ----------- |
| Userhelptext | 1:1 |  Uma descrição do predicado que pode ser útil para os usuários saber qual valor eles devem digitar. |
| PredicateReferences | 1: n | Uma lista de referências de predicado. |

O elemento **PredicateReferences** contém os seguintes atributos:

| Atributo | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| MatchAtLeast | Não | Especifica que o valor deve corresponder a pelo menos que muitas definições de predicado para a entrada sejam aceitas. |

O elemento **PredicateReferences** contém os seguintes elementos:

| Elemento | Ocorrências | Descrição |
| ------- | ----------- | ----------- |
| PredicateReference | 1: n | Uma referência a um predicado. |

O elemento **PredicateReference** contém os seguintes atributos:

| Atributo | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| Id | Sim | Um identificador que é usado para a validação de predicado.  |


## <a name="configure-password-complexity"></a>Configurar a complexidade da senha

Com **predicados** e **PredicateValidationsInput** , você pode controlar os requisitos de complexidade para as senhas fornecidas por um usuário ao criar uma conta. Por padrão, Azure AD B2C usa senhas fortes. O Azure AD B2C também dá suporte a opções de configuração para controlar a complexidade das senhas que os clientes podem usar. Você pode definir a complexidade da senha usando estes elementos de predicado:

- **IsLengthBetween8And64** usando o método `IsLengthRange`, o valida que a senha deve ter entre 8 e 64 caracteres.
- Em **letras minúsculas** usando o método `IncludesCharacters`, o valida que a senha contém uma letra minúscula.
- **Maiúsculas** usando o método `IncludesCharacters`, valida que a senha contém uma letra maiúscula.
- **Número** usando o método `IncludesCharacters`, valida que a senha contém um dígito.
- **Símbolo** usando o método `IncludesCharacters`, valida que a senha contém um dos vários caracteres de símbolo.
- O **PIN** usando o método `MatchesRegex`, valida que a senha contém apenas números.
- **AllowedAADCharacters** usando o método `MatchesRegex`, o valida que a senha com apenas caracteres inválidos foi fornecida.
- **DisallowedWhitespace** usando o método `MatchesRegex`, valida que a senha não começa ou termina com um caractere de espaço em branco.

```XML
<Predicates>
  <Predicate Id="IsLengthBetween8And64" Method="IsLengthRange">
    <UserHelpText>The password must be between 8 and 64 characters.</UserHelpText>
    <Parameters>
      <Parameter Id="Minimum">8</Parameter>
      <Parameter Id="Maximum">64</Parameter>
    </Parameters>
  </Predicate>

  <Predicate Id="Lowercase" Method="IncludesCharacters">
    <UserHelpText>a lowercase letter</UserHelpText>
    <Parameters>
      <Parameter Id="CharacterSet">a-z</Parameter>
    </Parameters>
  </Predicate>

  <Predicate Id="Uppercase" Method="IncludesCharacters">
    <UserHelpText>an uppercase letter</UserHelpText>
    <Parameters>
      <Parameter Id="CharacterSet">A-Z</Parameter>
    </Parameters>
  </Predicate>

  <Predicate Id="Number" Method="IncludesCharacters">
    <UserHelpText>a digit</UserHelpText>
    <Parameters>
      <Parameter Id="CharacterSet">0-9</Parameter>
    </Parameters>
  </Predicate>

  <Predicate Id="Symbol" Method="IncludesCharacters">
    <UserHelpText>a symbol</UserHelpText>
    <Parameters>
      <Parameter Id="CharacterSet">@#$%^&amp;*\-_+=[]{}|\\:',.?/`~"();!</Parameter>
    </Parameters>
  </Predicate>

  <Predicate Id="PIN" Method="MatchesRegex">
    <UserHelpText>The password must be numbers only.</UserHelpText>
    <Parameters>
      <Parameter Id="RegularExpression">^[0-9]+$</Parameter>
    </Parameters>
  </Predicate>

  <Predicate Id="AllowedAADCharacters" Method="MatchesRegex">
    <UserHelpText>An invalid character was provided.</UserHelpText>
    <Parameters>
      <Parameter Id="RegularExpression">(^([0-9A-Za-z\d@#$%^&amp;*\-_+=[\]{}|\\:',?/`~"();! ]|(\.(?!@)))+$)|(^$)</Parameter>
    </Parameters>
  </Predicate>

  <Predicate Id="DisallowedWhitespace" Method="MatchesRegex">
    <UserHelpText>The password must not begin or end with a whitespace character.</UserHelpText>
    <Parameters>
      <Parameter Id="RegularExpression">(^\S.*\S$)|(^\S+$)|(^$)</Parameter>
    </Parameters>
  </Predicate>
```

Depois de definir as validações básicas, você pode combiná-las e criar um conjunto de políticas de senha que você pode usar em sua política:

- **SimplePassword** valida o DisallowedWhitespace, o AllowedAADCharacters e o IsLengthBetween8And64
- **Senha forte** valida DisallowedWhitespace, AllowedAADCharacters, IsLengthBetween8And64. O último grupo `CharacterClasses` executa um conjunto adicional de predicados com `MatchAtLeast` definido como 3. A senha do usuário deve ter entre 8 e 16 caracteres e três dos seguintes caracteres: letras minúsculas, maiúsculas, números ou símbolos.
- **CustomPassword** valida apenas DisallowedWhitespace, AllowedAADCharacters. Portanto, o usuário pode fornecer qualquer senha com qualquer comprimento, desde que os caracteres sejam válidos.

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

Em seu tipo de declaração, adicione o elemento **PredicateValidationReference** e especifique o identificador como uma das validações de predicado, como SimplePassword, senha forte ou CustomPassword.

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

O seguinte mostra como os elementos são organizados quando Azure AD B2C exibe a mensagem de erro:

![Diagrama de exemplo de predicado e complexidade de senha de subdicado](./media/predicates/predicates-pass.png)

## <a name="configure-a-date-range"></a>Configurar um intervalo de datas

Com os elementos **predicados** e **PredicateValidations** , você pode controlar os valores de data mínimo e máximo de **userinputtype** usando um `DateTimeDropdown`. Para fazer isso, crie um **predicado** com o método `IsDateRange` e forneça os parâmetros mínimo e máximo.

```XML
<Predicates>
  <Predicate Id="DateRange" Method="IsDateRange">
    <UserHelpText>The date must be between 01-01-1980 and today.</UserHelpText>
    <Parameters>
      <Parameter Id="Minimum">1980-01-01</Parameter>
      <Parameter Id="Maximum">Today</Parameter>
    </Parameters>
  </Predicate>
</Predicates>
```

Adicione um **PredicateValidation** com uma referência ao predicado `DateRange`.

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

Em seu tipo de declaração, adicione o elemento **PredicateValidationReference** e especifique o identificador como `CustomDateRange`.

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
