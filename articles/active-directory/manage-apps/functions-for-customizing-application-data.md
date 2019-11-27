---
title: Gravando expressões para mapeamentos de atributo no Azure AD
description: Saiba como utilizar os mapeamentos de expressão para transformar valores de atributo num formato aceitável durante o aprovisionamento automatizado de objetos de aplicação SaaS no Azure Active Directory.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/31/2019
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5f1880a79f7fdb27b407ecb7ed1b761493fe850d
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74274013"
---
# <a name="writing-expressions-for-attribute-mappings-in-azure-active-directory"></a>Escrever expressões para mapeamentos de atributos no Azure Active Directory
Quando configurar o aprovisionamento a uma aplicação SaaS, um dos tipos de mapeamentos de atributos que pode especificar é um mapeamento de expressão. Para eles, deve escrever uma expressão de tipo de script que permite transformar os dados dos seus utilizadores em formatos que são mais aceitáveis para a aplicação SaaS.

## <a name="syntax-overview"></a>Descrição geral da sintaxe
A sintaxe para expressões para mapeamentos de atributos é que sobrou do Visual Basic para funções de Applications (VBA).

* Toda a expressão tem de ser definida em termos de funções, que são compostas por um nome, seguido de argumentos parênteses: <br>
  *FunctionName (`<<argument 1>>`,`<<argument N>>`)*
* Pode aninhar funções dentro de uns dos outros. Por exemplo: <br> *FunctionOne (FunctionTwo (`<<argument1>>`))*
* Pode passar três tipos de argumentos diferentes em funções:
  
  1. Atributos, que devem estar entre parênteses Retos. Por exemplo: [attributeName]
  2. Constantes de cadeia de caracteres, têm de estar entre aspas duplas. Por exemplo: "United States"
  3. Outras funções. Por exemplo: FunctionOne (`<<argument1>>`, FunctionTwo (`<<argument2>>`))
* Para constantes de cadeia de caracteres, se precisar de uma barra invertida (\) ou aspas (") na cadeia de caracteres, ele deve ser escrito com o símbolo de barra invertida (\). Por exemplo: "nome da empresa: \\" contoso\\""

## <a name="list-of-functions"></a>Lista de funções
[Append](#append) &nbsp;&nbsp;&nbsp;&nbsp; [FormatDateTime](#formatdatetime) &nbsp;&nbsp;&nbsp;&nbsp; [ingressar](#join) &nbsp;&nbsp;&nbsp;[&nbsp; Mid](#mid) &nbsp;&nbsp;&nbsp;&nbsp; &nbsp;&nbsp;&nbsp;[&nbsp; NormalizeDiacritics](#normalizediacritics) [não](#not) &nbsp;&nbsp;&nbsp;&nbsp; [substituir](#replace) &nbsp;&nbsp;&nbsp;&nbsp; [SelectUniqueValue](#selectuniquevalue)&nbsp;&nbsp;&nbsp;&nbsp; [SingleAppRoleAssignment](#singleapproleassignment)&nbsp;&nbsp;&nbsp;&nbsp; [Split](#split)&nbsp;&nbsp;&nbsp;&nbsp;[StripSpaces](#stripspaces) &nbsp;&nbsp;&nbsp;&nbsp; [Switch](#switch)&nbsp;&nbsp;&nbsp;&nbsp; [ToLower](#tolower)&nbsp;&nbsp;&nbsp;&nbsp; [ToUpper](#toupper)

---
### <a name="append"></a>Acrescentar
**Funcionamento**<br> Append(Source, suffix)

**Ndescrição**<br> Assume um valor de cadeia de caracteres de origem e acrescenta o sufixo ao final do mesmo.

**Parâmetro**<br> 

| Nome | Obrigatório / repetidos | Tipo | Notas |
| --- | --- | --- | --- |
| **original** |Necessário |Cadeia |Normalmente, o nome do atributo de objeto de origem. |
| **sufixo** |Necessário |Cadeia |A cadeia a acrescentar ao final do valor de origem. |

---
### <a name="formatdatetime"></a>FormatDateTime
**Funcionamento**<br> FormatDateTime (origem, inputFormat, outputFormat)

**Ndescrição**<br> Precisa de uma cadeia de data de um formato e os converte num formato diferente.

**Parâmetro**<br> 

| Nome | Obrigatório / repetidos | Tipo | Notas |
| --- | --- | --- | --- |
| **original** |Necessário |Cadeia |Normalmente, o nome do atributo de objeto de origem. |
| **inputFormat** |Necessário |Cadeia |Formato esperado do valor de origem. Para formatos com suporte, consulte [https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx). |
| **outputFormat** |Necessário |Cadeia |Formato da data de saída. |

---
### <a name="join"></a>Associar
**Funcionamento**<br> Junte-se (separador, source1 source2,...)

**Ndescrição**<br> Join () é semelhante a Append (), exceto pelo fato de que ele pode combinar vários valores de cadeia de caracteres de **origem** em uma única cadeia de caracteres, e cada valor será separado por uma cadeia de caracteres **separadora** .

Se um dos valores de origem for um atributo com vários valores, cada valor nesse atributo será Unido, separado pelo valor do separador.

**Parâmetro**<br> 

| Nome | Obrigatório / repetidos | Tipo | Notas |
| --- | --- | --- | --- |
| **caractere** |Necessário |Cadeia |Cadeia utilizada para separar os valores de origem quando eles são concatenados numa cadeia de caracteres. Pode ser "" se nenhum separador é necessário. |
| **origem1... origemn** |Variável-número de vezes, necessário |Cadeia |Cadeia de valores a ser unidas. |

---
### <a name="mid"></a>Mid
**Funcionamento**<br> Mid (origem, início, comprimento)

**Ndescrição**<br> Devolve uma subcadeia do valor de origem. Uma subcadeia é uma cadeia que contém apenas alguns dos caracteres da cadeia de origem.

**Parâmetro**<br> 

| Nome | Obrigatório / repetidos | Tipo | Notas |
| --- | --- | --- | --- |
| **original** |Necessário |Cadeia |Normalmente, o nome do atributo. |
| **start** |Necessário |número inteiro |Índice na cadeia de caracteres de **origem** em que a subcadeia de caracteres deve iniciar. Primeiro caractere na cadeia de caracteres terão o índice de 1, o segundo caráter terão índice 2 e assim por diante. |
| **muito** |Necessário |número inteiro |Comprimento da subcadeia. Se Length terminar fora da cadeia de caracteres de **origem** , a função retornará substring do índice **inicial** até o fim da cadeia de caracteres de **origem** . |

---
### <a name="normalizediacritics"></a>NormalizeDiacritics
**Funcionamento**<br> NormalizeDiacritics(source)

**Ndescrição**<br> Requer um argumento de cadeia de caracteres. Devolve a cadeia de caracteres, mas com nenhum dos caracteres diacritical substituídos por carateres não diacritical equivalentes. Normalmente, utilizada para converter os nomes de primeiro e último carateres diacritical (marcas de acento sem) em valores legais que podem ser utilizadas em vários identificadores de utilizador, tais como nomes de principal de utilizador, nomes de conta SAM e endereços de e-mail.

**Parâmetro**<br> 

| Nome | Obrigatório / repetidos | Tipo | Notas |
| --- | --- | --- | --- |
| **original** |Necessário |Cadeia | Geralmente um atributo de nome ou sobrenome. |

---
### <a name="not"></a>Não
**Funcionamento**<br> Not(Source)

**Ndescrição**<br> Inverte o valor booliano da **origem**. Se o valor de **Source** for "*true*", retornará "*false*". Caso contrário, retorna "*true*".

**Parâmetro**<br> 

| Nome | Obrigatório / repetidos | Tipo | Notas |
| --- | --- | --- | --- |
| **original** |Necessário |Booleano da cadeia |Os valores de **origem** esperados são "true" ou "false". |

---
### <a name="replace"></a>Substituir
**Funcionamento**<br> Substituir (origem, oldValue, regexPattern, regexGroupName, replacementValue, replacementAttributeName, modelo)

**Ndescrição**<br>
Substitui os valores dentro de uma cadeia de caracteres. Ele funciona de forma diferente consoante os parâmetros fornecidos:

* Quando **OldValue** e **replacevalue** são fornecidos:
  
  * Substitui todas as ocorrências de **OldValue** na **fonte** por **replacevalue**
* Quando **OldValue** e o **modelo** são fornecidos:
  
  * Substitui todas as ocorrências de **OldValue** no **modelo** pelo valor de **origem**
* Quando **regexPattern** e **replacevalue** são fornecidos:

  * A função aplica o **regexPattern** à cadeia de caracteres de **origem** e você pode usar os nomes de grupo Regex para construir a cadeia de caracteres para **replacevalue**
* Quando **regexPattern**, **regexGroupName**, **replacevalue** são fornecidos:
  
  * A função aplica o **regexPattern** à cadeia de caracteres de **origem** e substitui todos os valores correspondentes a **regexGroupName** com **replacevalue**
* Quando **regexPattern**, **regexGroupName**, **replacementAttributeName** são fornecidos:
  
  * Se **Source** não tiver nenhum valor, **Source** será retornado
  * Se **Source** tiver um valor, a função aplicará o **regexPattern** à cadeia de caracteres de **origem** e substituirá todos os valores correspondentes a **regexGroupName** pelo valor associado a **replacementAttributeName**

**Parâmetro**<br> 

| Nome | Obrigatório / repetidos | Tipo | Notas |
| --- | --- | --- | --- |
| **original** |Necessário |Cadeia |Normalmente o nome do atributo do objeto de **origem** . |
| **oldValue** |Opcional |Cadeia |Valor a ser substituído na **origem** ou no **modelo**. |
| **regexPattern** |Opcional |Cadeia |Padrão Regex para o valor a ser substituído na **origem**. Ou, quando **replacementPropertyName** é usado, Pattern para extrair o valor de **replacementPropertyName**. |
| **regexGroupName** |Opcional |Cadeia |Nome do grupo dentro de **regexPattern**. Somente quando **replacementPropertyName** for usado, Extraíremos o valor desse grupo como **replacevalue** de **replacementPropertyName**. |
| **replacementValue** |Opcional |Cadeia |Novo valor para substituir um antigo com. |
| **replacementAttributeName** |Opcional |Cadeia |Nome do atributo a ser usado para o valor de substituição |
| **modelos** |Opcional |Cadeia |Quando o valor do **modelo** for fornecido, procuraremos **OldValue** dentro do modelo e o substituíremos pelo valor de **origem** . |

---
### <a name="selectuniquevalue"></a>SelectUniqueValue
**Funcionamento**<br> SelectUniqueValue (uniqueValueRule1, uniqueValueRule2, uniqueValueRule3,...)

**Ndescrição**<br> Requer um mínimo de dois argumentos, que são definidas usando expressões de regras de geração de valor exclusivo. A função avalia cada regra e, em seguida, verifica o valor gerado para exclusividade na aplicação/diretório de destino. O primeiro valor exclusivo encontrado vai ser devolvido o. Se todos os valores ainda existirem no destino, a entrada irá obter colocadas em caução em e o motivo é registrado em log nos registos de auditoria. Não há nenhum limite superior ao número de argumentos que pode ser fornecido.

> [!NOTE]
> - Esta é uma função de nível superior, ele não é possível aninhar.
> - Esta função não pode ser aplicada a atributos que têm uma precedência correspondente.  
> - Esta função apenas se destina a ser utilizado para criações de entrada. Ao usá-lo com um atributo, defina a propriedade **aplicar mapeamento** para **somente durante a criação do objeto**.
> - Atualmente, essa função só tem suporte para "WORKDAY para Active Directory provisionamento de usuário". Ele não pode ser usado com outros aplicativos de provisionamento. 


**Parâmetro**<br> 

| Nome | Obrigatório / repetidos | Tipo | Notas |
| --- | --- | --- | --- |
| **uniqueValueRule1 ... uniqueValueRuleN** |Pelo menos 2 são vinculados a necessário, não superior |Cadeia | Lista de regras de geração de valor exclusivo a serem avaliadas. |


---
### <a name="singleapproleassignment"></a>SingleAppRoleAssignment
**Funcionamento**<br> SingleAppRoleAssignment([appRoleAssignments])

**Ndescrição**<br> Retorna um único appRoleAssignment da lista de todos os appRoleAssignments atribuídos a um usuário para um determinado aplicativo. Essa função é necessária para converter o objeto appRoleAssignments em uma única cadeia de caracteres de nome de função. Observe que a prática recomendada é garantir que apenas um appRoleAssignment seja atribuído a um usuário por vez, e se várias funções forem atribuídas, a cadeia de caracteres de função retornada poderá não ser previsível. 

**Parâmetro**<br> 

| Nome | Obrigatório / repetidos | Tipo | Notas |
| --- | --- | --- | --- |
| **AppRoleAssignments** |Necessário |Cadeia |objeto **[appRoleAssignments]** . |

---
### <a name="split"></a>Dividir
**Funcionamento**<br> Divisão (origem, delimitador)

**Ndescrição**<br> Divide uma cadeia de caracteres em uma matriz de valores múltiplos, usando o caractere delimitador especificado.

**Parâmetro**<br> 

| Nome | Obrigatório / repetidos | Tipo | Notas |
| --- | --- | --- | --- |
| **original** |Necessário |Cadeia |valor de **origem** a ser atualizado. |
| **delimitador** |Necessário |Cadeia |Especifica o caractere que será usado para dividir a cadeia de caracteres (exemplo: ",") |

---
### <a name="stripspaces"></a>StripSpaces
**Funcionamento**<br> StripSpaces(source)

**Ndescrição**<br> Remove todo o espaço ("") carateres da cadeia de caracteres de origem.

**Parâmetro**<br> 

| Nome | Obrigatório / repetidos | Tipo | Notas |
| --- | --- | --- | --- |
| **original** |Necessário |Cadeia |valor de **origem** a ser atualizado. |

---
### <a name="switch"></a>Comutador
**Funcionamento**<br> Comutador (origem, defaultValue, chave1, value1, chave2, value2...)

**Ndescrição**<br> Quando o valor de **origem** corresponde a uma **chave**, retorna um **valor** para essa **chave**. Se o valor de **Source** não corresponder a nenhuma chave, retornará **DefaultValue**.  Os parâmetros de **chave** e **valor** sempre devem vir em pares. A função espera sempre um número par de parâmetros.

**Parâmetro**<br> 

| Nome | Obrigatório / repetidos | Tipo | Notas |
| --- | --- | --- | --- |
| **original** |Necessário |Cadeia |Valor de **origem** a ser atualizado. |
| **ValorPadrão** |Opcional |Cadeia |Valor predefinido a utilizar quando a origem não corresponde a quaisquer chaves. Pode ser uma cadeia de caracteres vazia (""). |
| **chave** |Necessário |Cadeia |**Chave** para comparar o valor de **origem** com. |
| **valor** |Necessário |Cadeia |Valor de substituição para a **origem** correspondente à chave. |

---
### <a name="tolower"></a>toLower
**Funcionamento**<br> ToLower (origem, cultura)

**Ndescrição**<br> Usa um valor de cadeia de caracteres de *origem* e o converte em letras minúsculas usando as regras de cultura especificadas. Se não houver nenhuma informação de *cultura* especificada, ela usará cultura invariável.

**Parâmetro**<br> 

| Nome | Obrigatório / repetidos | Tipo | Notas |
| --- | --- | --- | --- |
| **original** |Necessário |Cadeia |Normalmente, o nome do atributo de objeto de origem |
| **UICulture** |Opcional |Cadeia |O formato do nome da cultura baseado em RFC 4646 é *languagecode2-Country/regioncode2*, em que *languagecode2* é o código de idioma de duas letras e *Country/regioncode2* é o código de subcultura de duas letras. Os exemplos incluem ja-JP para japonês (Japão) e en-US para inglês (Estados Unidos). Nos casos em que um código de idioma de duas letras não está disponível, um código de três letras derivado de ISO 639-2 é usado.|

---
### <a name="toupper"></a>ToUpper
**Funcionamento**<br> ToUpper (origem, cultura)

**Ndescrição**<br> Usa um valor de cadeia de caracteres de *origem* e converte-o em maiúsculas usando as regras de cultura especificadas. Se não houver nenhuma informação de *cultura* especificada, ela usará cultura invariável.

**Parâmetro**<br> 

| Nome | Obrigatório / repetidos | Tipo | Notas |
| --- | --- | --- | --- |
| **original** |Necessário |Cadeia |Normalmente, o nome do atributo de objeto de origem. |
| **UICulture** |Opcional |Cadeia |O formato do nome da cultura baseado em RFC 4646 é *languagecode2-Country/regioncode2*, em que *languagecode2* é o código de idioma de duas letras e *Country/regioncode2* é o código de subcultura de duas letras. Os exemplos incluem ja-JP para japonês (Japão) e en-US para inglês (Estados Unidos). Nos casos em que um código de idioma de duas letras não está disponível, um código de três letras derivado de ISO 639-2 é usado.|

## <a name="examples"></a>Exemplos
### <a name="strip-known-domain-name"></a>Nome de domínio conhecidos de faixa
É necessário um nome de domínio conhecidos do e-mail de um utilizador para obter um nome de utilizador de faixa. <br>
Por exemplo, se o domínio "contoso.com", em seguida, poderia usar a seguinte expressão:

**Expressão** <br>
`Replace([mail], "@contoso.com", , ,"", ,)`

**Entrada/saída de exemplo:** <br>

* **Entrada** (email): "john.doe@contoso.com"
* **Saída**: "João. Doe"

### <a name="append-constant-suffix-to-user-name"></a>Acrescentar constante sufixo ao nome de utilizador
Se estiver a utilizar uma área de segurança do Salesforce, precisará de acrescentar um sufixo adicional a todos os seus nomes de utilizador antes de sincronizá-los.

**Expressão** <br>
`Append([userPrincipalName], ".test")`

**Entrada/saída de exemplo:** <br>

* **Entrada**: (userPrincipalName): "John.Doe@contoso.com"
* **Saída**: "John.Doe@contoso.com.test"

### <a name="generate-user-alias-by-concatenating-parts-of-first-and-last-name"></a>Gerar o alias do utilizador, concatenando partes do nome próprio e apelido
Terá de gerar um utilizador alias ao colocar os primeiros 3 letras do nome próprio do utilizador e os primeiros 5 letras do apelido do utilizador.

**Expressão** <br>
`Append(Mid([givenName], 1, 3), Mid([surname], 1, 5))`

**Entrada/saída de exemplo:** <br>

* **Entrada** (fornecida): "João"
* **Entrada** (sobrenome): "Doe"
* **Saída**: "davibarros"

### <a name="remove-diacritics-from-a-string"></a>Remover Diacríticos de uma cadeia de caracteres
É necessário substituir caracteres que contém marcas de acento sem com caracteres equivalentes que não contenham marcas de acento sem.

**Expressão** <br>
NormalizeDiacritics([givenName])

**Entrada/saída de exemplo:** <br>

* **Entrada** (fornecida): "Zoë"
* **Saída**: "Zoe"

### <a name="split-a-string-into-a-multi-valued-array"></a>Dividir uma cadeia de caracteres em uma matriz de vários valores
Você precisa usar uma lista delimitada por vírgulas de cadeias de caracteres e dividi-las em uma matriz que pode ser conectada a um atributo de vários valores, como o atributo PermissionSets do Salesforce. Neste exemplo, uma lista de conjuntos de permissões foi populada no extensionAttribute5 no Azure AD.

**Expressão** <br>
Split ([extensionAttribute5], ",")

**Entrada/saída de exemplo:** <br>

* **Entrada** (extensionAttribute5): "PermissionSetOne, PermisionSetTwo"
* **Saída**: ["PermissionSetOne", "PermissionSetTwo"]

### <a name="output-date-as-a-string-in-a-certain-format"></a>Data de saída como uma cadeia de caracteres num determinado formato
Pretende enviar as datas para uma aplicação SaaS num determinado formato. <br>
Por exemplo, que pretende formatar datas do ServiceNow.

**Expressão** <br>

`FormatDateTime([extensionAttribute1], "yyyyMMddHHmmss.fZ", "yyyy-MM-dd")`

**Entrada/saída de exemplo:**

* **Entrada** (extensionAttribute1): "20150123105347.1 z"
* **Saída**: "2015-01-23"

### <a name="replace-a-value-based-on-predefined-set-of-options"></a>Substituir um valor com base num conjunto predefinido de opções

Tem de definir o fuso horário do utilizador com base no código de estado armazenado no Azure AD. <br>
Se o código de estado não corresponder a qualquer uma das opções predefinidas, utilize o valor predefinido de "Austrália/Sydney".

**Expressão** <br>
`Switch([state], "Australia/Sydney", "NSW", "Australia/Sydney","QLD", "Australia/Brisbane", "SA", "Australia/Adelaide")`

**Entrada/saída de exemplo:**

* **Entrada** (estado): "QLD"
* **Saída**: "Austrália/Brisbane"

### <a name="replace-characters-using-a-regular-expression"></a>Substituir caracteres usando uma expressão regular
Você precisa localizar caracteres que correspondam a um valor de expressão regular e removê-los.

**Expressão** <br>

Replace ([mailNickname],, "[a-zA-Z_] *",, "",,)

**Entrada/saída de exemplo:**

* **Entrada** (mailNickname: "john_doe72"
* **Saída**: "72"

### <a name="convert-generated-userprincipalname-upn-value-to-lower-case"></a>Converter o valor de userPrincipalName (UPN) gerado em letras minúsculas
No exemplo a seguir, o valor UPN é gerado pela concatenação dos campos de origem nomepreferido e PreferredLastName e a função ToLower opera na cadeia de caracteres gerada para converter todos os caracteres em letras minúsculas. 

`ToLower(Join("@", NormalizeDiacritics(StripSpaces(Join(".",  [PreferredFirstName], [PreferredLastName]))), "contoso.com"))`

**Entrada/saída de exemplo:**

* **Entrada** (nomepreferido): "John"
* **Entrada** (PreferredLastName): "Smith"
* **Saída**: "john.smith@contoso.com"

### <a name="generate-unique-value-for-userprincipalname-upn-attribute"></a>Gerar um valor exclusivo para o atributo userPrincipalName (UPN)
Com base do usuário nome próprio, segundo nome e sobrenome, terá de gerar um valor para o atributo UPN e verificar seu exclusividade no diretório de destino AD antes de atribuir o valor para o atributo UPN.

**Expressão** <br>

    SelectUniqueValue( 
        Join("@", NormalizeDiacritics(StripSpaces(Join(".",  [PreferredFirstName], [PreferredLastName]))), "contoso.com"), 
        Join("@", NormalizeDiacritics(StripSpaces(Join(".",  Mid([PreferredFirstName], 1, 1), [PreferredLastName]))), "contoso.com"),
        Join("@", NormalizeDiacritics(StripSpaces(Join(".",  Mid([PreferredFirstName], 1, 2), [PreferredLastName]))), "contoso.com")
    )

**Entrada/saída de exemplo:**

* **Entrada** (nomepreferido): "John"
* **Entrada** (PreferredLastName): "Smith"
* **Saída**: "John.Smith@contoso.com" se o valor UPN de John.Smith@contoso.com ainda não existir no diretório
* **Saída**: "J.Smith@contoso.com" se o valor UPN de John.Smith@contoso.com já existir no diretório
* **Saída**: "Jo.Smith@contoso.com" se os dois valores UPN acima já existirem no diretório

## <a name="related-articles"></a>Artigos relacionados
* [Automatizar o provisionamento/desprovisionamento de usuários para aplicativos SaaS](user-provisioning.md)
* [Personalizando mapeamentos de atributo para provisionamento de usuário](customize-application-attributes.md)
* [Filtros de escopo para provisionamento de usuário](define-conditional-rules-for-provisioning-user-accounts.md)
* [Utilizar o SCIM para ativar o aprovisionamento automático de utilizadores e grupos do Azure Active Directory a aplicações](use-scim-to-provision-users-and-groups.md)
* [Notificações de provisionamento de conta](user-provisioning.md)
* [Lista de tutoriais sobre como integrar aplicativos SaaS](../saas-apps/tutorial-list.md)
