---
title: Predicados e PredicateValidations - Azure Active Directory B2C | Documentos da Microsoft
description: Conta de rede social de exemplos de transformação de afirmações para a identidade experiência de estrutura de esquema do Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 360fd8e7ab0f7a85dbeed2bdbc7da379cbcfe91a
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/23/2019
ms.locfileid: "56737030"
---
# <a name="predicates-and-predicatevalidations"></a>Predicados e PredicateValidations

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

O **predicados** e **PredicateValidations** elementos permitem-lhe efetuar um processo de validação para garantir que apenas os dados corretamente formados são inseridos no seu inquilino do Azure Active Directory (Azure AD) B2C .  

O diagrama seguinte mostra a relação entre os elementos:  

![Predicados](./media/predicates/predicates.png)

## <a name="predicates"></a>Predicados  

O **predicado** elemento define uma validação básica para verificar o valor de um tipo de afirmação e retorna `true` ou `false`. A validação é efetuada com uma determinada **método** elemento e um conjunto de **parâmetro** elementos relevantes para o método. Por exemplo, um predicado pode verificar se o comprimento de um valor de afirmação de cadeia de caracteres é dentro do intervalo de parâmetros mínimos e máximo especificados ou se um valor de afirmação de cadeia de caracteres contém um conjunto de carateres. O **UserHelpText** elemento fornece uma mensagem de erro para os usuários se falhar a verificação. O valor de **UserHelpText** elemento pode ser localizado utilizando [personalização de idioma](localization.md).

O **predicados** elemento contém o seguinte elemento:

| Elemento | Ocorrências | Descrição |
| ------- | ----------- | ----------- |
| Predicado | 1:n | Uma lista de predicados. | 

O **predicado** elemento contém os seguintes atributos:

| Atributo | Necessário | Descrição |
| --------- | -------- | ----------- |
| Id | Sim | Um identificador que é utilizado para o predicado. Outros elementos podem utilizar este identificador na política. |
| Método | Sim | O tipo de método a utilizar para a validação. Valores possíveis: **IsLengthRange**, **MatchesRegex**, **IncludesCharacters**, ou **IsDateRange**. O **IsLengthRange** valor verifica se o comprimento de um valor de afirmação de cadeia de caracteres dentro do intervalo de parâmetros mínimos e máximo especificados. O **MatchesRegex** valor verifica se um valor de afirmação de cadeia de caracteres corresponde a uma expressão regular. O **IncludesCharacters** valor verifica se um valor de afirmação de cadeia de caracteres contém um conjunto de carateres. O **IsDateRange** valor verifica se a um valor de afirmação de data entre uma variedade de parâmetros mínimos e máximo especificado. |

O **predicado** elemento contém os seguintes elementos:

| Elemento | Ocorrências | Descrição |
| ------- | ----------- | ----------- |
| UserHelpText | 1:1 | Uma mensagem de erro para os utilizadores se falhar a verificação. Essa cadeia de caracteres pode ser localizada usando o [personalização de idioma](localization.md) |
| Parâmetros | 1:1 | Os parâmetros para o tipo de método de validação da cadeia de caracteres. | 

O **parâmetros** elemento contém os seguintes elementos:

| Elemento | Ocorrências | Descrição |
| ------- | ----------- | ----------- |
| Parâmetro | 1:n | Os parâmetros para o tipo de método de validação da cadeia de caracteres. | 

O **parâmetro** elemento contém os seguintes atributos:

| Elemento | Ocorrências | Descrição |
| ------- | ----------- | ----------- |
| Id | 1:1 | O identificador do parâmetro. |

A exemplo a seguir mostra um `IsLengthRange` método com os parâmetros `Minimum` e `Maximum` que especifique o intervalo de comprimento da cadeia de caracteres:

```XML
<Predicate Id="IsLengthBetween8And64" Method="IsLengthRange">
  <UserHelpText>The password must be between 8 and 64 characters.</UserHelpText>
    <Parameters>
      <Parameter Id="Minimum">8</Parameter>
      <Parameter Id="Maximum">64</Parameter>
  </Parameters>
</Predicate>
```

A exemplo a seguir mostra um `MatchesRegex` método com o parâmetro `RegularExpression` que especifica uma expressão regular:

```XML
<Predicate Id="PIN" Method="MatchesRegex">
  <UserHelpText>The password must be numbers only.</UserHelpText>
  <Parameters>
    <Parameter Id="RegularExpression">^[0-9]+$</Parameter>
  </Parameters>
</Predicate>
```

A exemplo a seguir mostra um `IncludesCharacters` método com o parâmetro `CharacterSet` que especifica o conjunto de caracteres:

```XML
<Predicate Id="Lowercase" Method="IncludesCharacters">
  <UserHelpText>a lowercase letter</UserHelpText>
  <Parameters>
    <Parameter Id="CharacterSet">a-z</Parameter>
  </Parameters>
</Predicate>
```

A exemplo a seguir mostra um `IsDateRange` método com os parâmetros `Minimum` e `Maximum` que especifique o intervalo de datas com um formato de `yyyy-MM-dd` e `Today`.

```XML
<Predicate Id="DateRange" Method="IsDateRange" HelpText="The date must be between 1970-01-01 and today.">
  <Parameters>
    <Parameter Id="Minimum">1970-01-01</Parameter>
    <Parameter Id="Maximum">Today</Parameter>
  </Parameters>
</Predicate>
```

## <a name="predicatevalidations"></a>PredicateValidations 

Embora os predicados de definem a validação para verificar em relação a um tipo de afirmação, o **PredicateValidations** agrupar um conjunto de predicados para formar uma validação de entrada do usuário que pode ser aplicada a um tipo de afirmação. Cada **PredicateValidation** elemento contém um conjunto de **PredicateGroup** elementos que contêm um conjunto de **PredicateReference** elementos que aponta para um **Predicado**. Para passar a validação, o valor da afirmação deve passar todos os testes de algum predicado sob todos os **PredicateGroup** com o seu conjunto de **PredicateReference** elementos.

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

O **PredicateValidations** elemento contém o seguinte elemento:

| Elemento | Ocorrências | Descrição |
| ------- | ----------- | ----------- |
| PredicateValidation | 1:n | Uma lista de validação de predicado. | 

O **PredicateValidation** elemento contém o atributo seguinte:

| Atributo | Necessário | Descrição |
| --------- | -------- | ----------- |
| Id | Sim | Um identificador que é utilizado para a validação de predicado. O **ClaimType** elemento pode utilizar este identificador na política. |

O **PredicateValidation** elemento contém o seguinte elemento:

| Elemento | Ocorrências | Descrição |
| ------- | ----------- | ----------- |
| PredicateGroups | 1:n | Uma lista de grupos de predicado. | 

O **PredicateGroups** elemento contém o seguinte elemento:

| Elemento | Ocorrências | Descrição |
| ------- | ----------- | ----------- |
| PredicateGroup | 1:n | Uma lista de predicados. | 

O **PredicateGroup** elemento contém o atributo seguinte:

| Atributo | Necessário | Descrição |
| --------- | -------- | ----------- |
| Id | Sim | Um identificador que é utilizado para o grupo de predicado.  |

O **PredicateGroup** elemento contém os seguintes elementos:

| Elemento | Ocorrências | Descrição |
| ------- | ----------- | ----------- |
| UserHelpText | 1:1 |  Uma descrição do predicado que pode ser útil para os utilizadores saber o valor devem escrever. | 
| PredicateReferences | 1:n | Uma lista de referências de predicado. | 

O **PredicateReferences** elemento contém os seguintes atributos:

| Atributo | Necessário | Descrição |
| --------- | -------- | ----------- |
| MatchAtLeast | Não | Especifica que o valor tem de corresponder, pelo menos, muitos predicado de definições para a entrada de ser aceites. |

O **PredicateReferences** elemento contém os seguintes elementos:

| Elemento | Ocorrências | Descrição |
| ------- | ----------- | ----------- |
| PredicateReference | 1:n | Uma referência a um predicado. | 

O **PredicateReference** elemento contém os seguintes atributos:

| Atributo | Necessário | Descrição |
| --------- | -------- | ----------- |
| Id | Sim | Um identificador que é utilizado para a validação de predicado.  |


## <a name="configure-password-complexity"></a>Configurar a complexidade de palavra-passe

Com o **predicados** e **PredicateValidationsInput** pode controlar os requisitos de complexidade de palavras-passe fornecidas por um utilizador quando criar uma conta. Por predefinição, o Azure AD B2C utiliza senhas fortes. O Azure AD B2C também oferece suporte a opções de configuração para controlar a complexidade de palavras-passe que os clientes podem utilizar. Pode definir a complexidade de palavra-passe utilizando estes elementos de predicado: 

- **IsLengthBetween8And64** usando o `IsLengthRange` método, valida que a palavra-passe tem de ter entre 8 e 64 carateres.
- **Em minúsculas** usando o `IncludesCharacters` método, valida que a palavra-passe contém uma letra minúscula.
- **Letras maiúsculas** usando o `IncludesCharacters` método, valida que a palavra-passe contém uma letra maiúscula.
- **Número** usando o `IncludesCharacters` método, valida que a palavra-passe contém um dígito.
- **Symbol** usando o `IncludesCharacters` método, valida que a palavra-passe contém um dos seguintes símbolos `@#$%^&*\-_+=[]{}|\:',?/~"();!`
- **PIN** usando o `MatchesRegex` método, valida que a palavra-passe contém apenas números.
- **AllowedAADCharacters** usando o `MatchesRegex` método, valida que o caráter de apenas inválido de palavra-passe foi fornecido.
- **DisallowedWhitespace** usando o `MatchesRegex` método, valida que a palavra-passe não começar ou terminar com um caráter de espaço em branco.

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
      <Parameter Id="CharacterSet">@#$%^&amp;*\-_+=[]{}|\:',?/`~"();!</Parameter>
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

Depois de definir as validações básicas, pode combiná-los em conjunto e criar um conjunto de políticas de palavra-passe que podem ser utilizados na sua política:

- **SimplePassword** valida o DisallowedWhitespace AllowedAADCharacters e IsLengthBetween8And64
- **StrongPassword** valida o IsLengthBetween8And64 DisallowedWhitespace, AllowedAADCharacters,. O último grupo `CharacterClasses` executa um conjunto adicional de predicados com `MatchAtLeast` definido como 3. A palavra-passe do utilizador tem de ter entre 8 e 16 carateres e três dos seguintes carateres: Em minúsculas, letras maiúsculas, número ou símbolo.
- **CustomPassword** valida apenas DisallowedWhitespace, AllowedAADCharacters. Então, o usuário pode fornecer qualquer palavra-passe com qualquer comprimento, desde que os caracteres são válidos.

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

No seu tipo de afirmação, adicione a **PredicateValidationReference** elemento e especifique o identificador como um das validações predicadas, como SimplePassword, StrongPassword ou CustomPassword.

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

O código a seguir mostra como os elementos são organizados quando o Azure AD B2C apresenta a mensagem de erro:

![Processo de predicado](./media/predicates/predicates-pass.png)

## <a name="configure-a-date-range"></a>Configurar um intervalo de datas

Com o **predicados** e **PredicateValidations** elementos pode controlar os valores de data mínimo e máximo do **UserInputType** utilizando um `DateTimeDropdown`. Para tal, crie uma **predicado** com o `IsDateRange` método e forneça os parâmetros mínimos e máximos.

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

Adicionar uma **PredicateValidation** com uma referência para o `DateRange` predicado.

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

No seu tipo de afirmação, adicione **PredicateValidationReference** elemento e especifique o identificador como `CustomDateRange`. 
    
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
