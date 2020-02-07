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
ms.date: 02/05/2020
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: a01f7f48dd93983edf4be4b797f62afede273c66
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/07/2020
ms.locfileid: "77066671"
---
# <a name="writing-expressions-for-attribute-mappings-in-azure-active-directory"></a>Escrever Expressões para Mapeamentos de Atributos no Azure Active Directory
Quando configurar o aprovisionamento a uma aplicação SaaS, um dos tipos de mapeamentos de atributos que pode especificar é um mapeamento de expressão. Para eles, deve escrever uma expressão de tipo de script que permite transformar os dados dos seus utilizadores em formatos que são mais aceitáveis para a aplicação SaaS.

## <a name="syntax-overview"></a>Descrição geral da sintaxe
A sintaxe para expressões para mapeamentos de atributos é que sobrou do Visual Basic para funções de Applications (VBA).

* Toda a expressão tem de ser definida em termos de funções, que são compostas por um nome, seguido de argumentos parênteses: <br>
  *Nome de funções,`<<argument 1>>`,`<<argument N>>`*
* Pode aninhar funções dentro de uns dos outros. Por exemplo: <br> *FunctionOne (FunçãoTwo(`<<argument1>>`))*
* Pode passar três tipos de argumentos diferentes em funções:
  
  1. Atributos, que devem estar entre parênteses Retos. Por exemplo: [attributeName]
  2. Constantes de cadeia de caracteres, têm de estar entre aspas duplas. Por exemplo: "United States"
  3. Outras funções. Por exemplo: FunctionOne(`<<argument1>>`, FunctionTwo(`<<argument2>>`))
* Para constantes de cadeia de caracteres, se precisar de uma barra invertida (\) ou aspas (") na cadeia de caracteres, ele deve ser escrito com o símbolo de barra invertida (\). Por exemplo: "Nome da empresa: \\"Contoso\\".

## <a name="list-of-functions"></a>Lista de funções
[Append](#append) &nbsp;&nbsp;&nbsp;&nbsp; [BitAnd](#bitand) &nbsp;&nbsp;&nbsp;&nbsp; [CBool](#cbool) &nbsp;&nbsp;&nbsp;&nbsp; [Coalesce](#coalesce) &nbsp;&nbsp;&nbsp;&nbsp; [ConvertToBase64](#converttobase64) &nbsp;&nbsp;&nbsp;&nbsp; [ConvertToUTF8Hex](#converttoutf8hex) &nbsp;&nbsp;&nbsp;&nbsp; [Count](#count) &nbsp;&nbsp;&nbsp;&nbsp; [CStr](#cstr) &nbsp;&nbsp;&nbsp;&nbsp; [DateFromNum](#datefromnum) &nbsp;[FormatDateTime](#formatdatetime) &nbsp;&nbsp;&nbsp;&nbsp; [Guid](#guid) &nbsp;&nbsp;&nbsp;&nbsp; [InStr](#instr) &nbsp;&nbsp;&nbsp;&nbsp; [IsNull](#isnull) &nbsp;&nbsp;&nbsp;&nbsp; [IsNullOrEmpty](#isnullorempty) &nbsp;&nbsp;&nbsp;&nbsp; [IsPresent](#ispresent) &nbsp;&nbsp;&nbsp;&nbsp; [IsString](#isstring) &nbsp;&nbsp;&nbsp;&nbsp; [Item](#item) &nbsp;&nbsp;&nbsp;&nbsp; [Join](#join) &nbsp;&nbsp;&nbsp;&nbsp; [Left](#left) &nbsp;&nbsp;&nbsp;&nbsp; [Mid](#mid) &nbsp;&nbsp; &nbsp;&nbsp;&nbsp;&nbsp; [NormalizeDiacritics](#normalizediacritics) [Not](#not) &nbsp;&nbsp;&nbsp;&nbsp; [NumFromDate](#numfromdate) &nbsp;&nbsp;&nbsp;&nbsp;[RemoveDuplicates](#removeduplicates) &nbsp;&nbsp;&nbsp;&nbsp; [Replace](#replace) &nbsp;&nbsp;&nbsp;&nbsp; [SelectUniqueValue](#selectuniquevalue)&nbsp;&nbsp;&nbsp;&nbsp; [SingleAppRoleAssignment](#singleapproleassignment)&nbsp;&nbsp;&nbsp;&nbsp; [Split](#split)&nbsp;&nbsp;&nbsp;&nbsp;[StripSpaces](#stripspaces) &nbsp;&nbsp;&nbsp;&nbsp; [Switch](#switch)&nbsp;&nbsp;&nbsp;&nbsp; [ToLower](#tolower)&nbsp;&nbsp;&nbsp;&nbsp; [ToUpper](#toupper)&nbsp;&nbsp;&nbsp;&nbsp; [Word](#word)

---
### <a name="append"></a>Acrescentar
**Função:**<br> Append(Source, suffix)

**Descrição:**<br> Assume um valor de cadeia de caracteres de origem e acrescenta o sufixo ao final do mesmo.

**Parâmetros:**<br> 

| Nome | Obrigatório / repetidos | Tipo | Notas |
| --- | --- | --- | --- |
| **fonte** |Required |String |Normalmente, o nome do atributo de objeto de origem. |
| **sufixo** |Required |String |A cadeia a acrescentar ao final do valor de origem. |

---
### <a name="bitand"></a>BitAnd
**Função:**<br> BitAnd (value1, value2)

**Descrição:**<br> Essa função converte ambos os parâmetros na representação binária e define um bit para:

0-se um ou ambos os bits correspondentes em value1 e value2 forem 0                                                  
1-se ambos os bits correspondentes forem 1.                                    

Em outras palavras, ele retorna 0 em todos os casos, exceto quando os bits correspondentes de ambos os parâmetros são 1.

**Parâmetros:**<br> 

| Nome | Obrigatório / repetidos | Tipo | Notas |
| --- | --- | --- | --- |
| **valor1** |Required |num |Valor numérico que deve ser AND'eddo com value2|
| **valor2** |Required |num |Valor numérico que deve ser AND'ed com value1|

**Exemplo:**<br>
BitAnd (& HF, & HF7)                                                                                
11110111 e 00000111 = 00000111 para que BitAnd retorne 7, o valor binário de 00000111

---
### <a name="cbool"></a>CBool
**Função:**<br> CBool (expressão)

**Descrição:**<br> CBool retorna um booliano com base na expressão avaliada. Se a expressão for avaliada como um valor diferente de zero, CBool retornará true, caso contrário retornará false..

**Parâmetros:**<br> 

| Nome | Obrigatório / repetidos | Tipo | Notas |
| --- | --- | --- | --- |
| **expressão** |Required | expression | Qualquer expressão válida |

**Exemplo:**<br>
CBool =[atributo2])                                                                    
Retorna true se ambos os atributos têm o mesmo valor.

---
### <a name="coalesce"></a>COALESCE
**Função:**<br> Adesão (origem1, origem2,..., defaultValue)

**Descrição:**<br> Retorna o primeiro valor de origem que não é nulo. Se todos os argumentos forem nulos e defaultValue estiver presente, o defaultValue será retornado. Se todos os argumentos forem nulos e defaultValue não estiver presente, o adesão retornará nulo.

**Parâmetros:**<br> 

| Nome | Obrigatório / repetidos | Tipo | Notas |
| --- | --- | --- | --- |
| **fonte1 ... fonteN** | Required | String |Obrigatório, número de vezes variável. Normalmente, o nome do atributo de objeto de origem. |
| **defaultValor** | Opcional | String | Valor padrão a ser usado quando todos os valores de origem forem nulos. Pode ser uma cadeia de caracteres vazia ("").

---
### <a name="converttobase64"></a>ConvertToBase64
**Função:**<br> ConvertToBase64 (origem)

**Descrição:**<br> A função ConvertToBase64 converte uma cadeia de caracteres em uma cadeia de caracteres Base64 Unicode.

**Parâmetros:**<br> 

| Nome | Obrigatório / repetidos | Tipo | Notas |
| --- | --- | --- | --- |
| **fonte** |Required |String |Cadeia de caracteres a ser convertida em base 64|

**Exemplo:**<br>
ConvertToBase64 ("Olá mundo!")                                                                                                        
Retorna "SABlAGwAbABvACAAdwBvAHIAbABkACEA"

---
### <a name="converttoutf8hex"></a>ConvertToUTF8Hex
**Função:**<br> ConvertToUTF8Hex (origem)

**Descrição:**<br> A função ConvertToUTF8Hex converte uma cadeia de caracteres em um valor codificado hexadecimal UTF8.

**Parâmetros:**<br> 

| Nome | Obrigatório / repetidos | Tipo | Notas |
| --- | --- | --- | --- |
| **fonte** |Required |String |Cadeia de caracteres a ser convertida em UTF8 hex|

**Exemplo:**<br>
ConvertToUTF8Hex ("Olá mundo!")                                                                                                         
Returns 48656C6C6F20776F726C6421

---
### <a name="count"></a>Contagem
**Função:**<br> Contagem (atributo)

**Descrição:**<br> A função Count retorna o número de elementos em um atributo de valores múltiplos

**Parâmetros:**<br> 

| Nome | Obrigatório / repetidos | Tipo | Notas |
| --- | --- | --- | --- |
| **atributo** |Required |atributo |Atributo com valores múltiplos que terá elementos contados|

---
### <a name="cstr"></a>CStr
**Função:**<br> CStr (valor)

**Descrição:**<br> A função CStr converte um valor em um tipo de dados de cadeia de caracteres.

**Parâmetros:**<br> 

| Nome | Obrigatório / repetidos | Tipo | Notas |
| --- | --- | --- | --- |
| **valor** |Required | Numeric, Reference ou Boolean | Pode ser um valor numérico, um atributo de referência ou um booliano. |

**Exemplo:**<br>
CStr([dn])                                                            
Retorna "CN = Joe, DC = contoso, DC = com"

---
### <a name="datefromnum"></a>DateFromNum
**Função:**<br> DateFromNum (valor)

**Descrição:**<br> A função DateFromNum converte um valor no formato de data do AD em um tipo DateTime.

**Parâmetros:**<br> 

| Nome | Obrigatório / repetidos | Tipo | Notas |
| --- | --- | --- | --- |
| **valor** |Required | Data | A data do AD a ser convertida no tipo DateTime |

**Exemplo:**<br>
DateFromNum([lastLogonTimestamp])                                                                                                   
DateFromNum (129699324000000000)                                                            
Retorna um DateTime que representa 2012-01-01 23:00:00

---
### <a name="formatdatetime"></a>FormatDateTime
**Função:**<br> FormatDateTime (origem, inputFormat, outputFormat)

**Descrição:**<br> Precisa de uma cadeia de data de um formato e os converte num formato diferente.

**Parâmetros:**<br> 

| Nome | Obrigatório / repetidos | Tipo | Notas |
| --- | --- | --- | --- |
| **fonte** |Required |String |Normalmente, o nome do atributo de objeto de origem. |
| **inputFormat** |Required |String |Formato esperado do valor de origem. Para formatos suportados, consulte [https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx). |
| **outputFormat** |Required |String |Formato da data de saída. |

---
### <a name="guid"></a>Guid
**Função:**<br> GUID ()

**Descrição:**<br> O GUID da função gera um novo GUID aleatório

---
### <a name="instr"></a>InStr
**Função:**<br> InStr (value1, value2, Start, comparetype)

**Descrição:**<br> A função InStr localiza a primeira ocorrência de uma subcadeia de caracteres em uma cadeia de caracteres

**Parâmetros:**<br> 

| Nome | Obrigatório / repetidos | Tipo | Notas |
| --- | --- | --- | --- |
| **valor1** |Required |String |Cadeia de caracteres a ser pesquisada |
| **valor2** |Required |String |Cadeia de caracteres a ser encontrada |
| **start** |Opcional |Número inteiro |Posição inicial para localizar a subcadeia de caracteres|
| **compararType** |Opcional |Enum |Pode ser vbTextCompare ou vbBinaryCompare |

**Exemplo:**<br>
InStr ("a rápida raposa marrom", "rápido")                                                                             
Avalia para 5

InStr ("repetido", "e", 3, vbBinaryCompare)                                                                                  
Avalia para 7

---
### <a name="isnull"></a>IsNull
**Função:**<br> IsNull (expressão)

**Descrição:**<br> Se a expressão for avaliada como NULL, a função IsNull retornará true. Para um atributo, um NULL é expresso pela ausência do atributo.

**Parâmetros:**<br> 

| Nome | Obrigatório / repetidos | Tipo | Notas |
| --- | --- | --- | --- |
| **expressão** |Required |expression |Expressão a ser avaliada |

**Exemplo:**<br>
IsNull([nome do ecrã])                                                                                                
Retornará true se o atributo não estiver presente

---
### <a name="isnullorempty"></a>IsNullorEmpty
**Função:**<br> IsNullOrEmpty (expressão)

**Descrição:**<br> Se a expressão for nula ou uma cadeia de caracteres vazia, a função IsNullOrEmpty retornará true. Para um atributo, isso será avaliado como true se o atributo estiver ausente ou estiver presente, mas for uma cadeia de caracteres vazia.
O inverso dessa função é chamado ispresente.

**Parâmetros:**<br> 

| Nome | Obrigatório / repetidos | Tipo | Notas |
| --- | --- | --- | --- |
| **expressão** |Required |expression |Expressão a ser avaliada |

**Exemplo:**<br>
IsNullOrEmpty([nome do ecrã])                                               
Retornará true se o atributo não estiver presente ou for uma cadeia de caracteres vazia

---
### <a name="ispresent"></a>IsPresent
**Função:**<br> IsPresent (Expressão)

**Descrição:**<br> Se a expressão for avaliada como uma cadeia de caracteres que não é nula e não estiver vazia, a função IsPresent retornará true. O inverso dessa função é chamado de IsNullOrEmpty.

**Parâmetros:**<br> 

| Nome | Obrigatório / repetidos | Tipo | Notas |
| --- | --- | --- | --- |
| **expressão** |Required |expression |Expressão a ser avaliada |

**Exemplo:**<br>
Switch(IsPresent([directManager]),[directManager], IsPresent([skiplevelManager],[skiplevelManager], IsPresent[[diretor]

---
### <a name="isstring"></a>IsString
**Função:**<br> IsString (expressão)

**Descrição:**<br> Se a expressão puder ser avaliada como um tipo de cadeia de caracteres, a função isString será avaliada como true.

**Parâmetros:**<br> 

| Nome | Obrigatório / repetidos | Tipo | Notas |
| --- | --- | --- | --- |
| **expressão** |Required |expression |Expressão a ser avaliada |

---
### <a name="item"></a>Item
**Função:**<br> Item (atributo, índice)

**Descrição:**<br> A função Item retorna um item de um atributo/cadeia de caracteres de valores múltiplos.

**Parâmetros:**<br> 

| Nome | Obrigatório / repetidos | Tipo | Notas |
| --- | --- | --- | --- |
| **atributo** |Required |Atributo |Atributo de valores múltiplos a ser pesquisado |
| **índice** |Required |Número inteiro | Índice para um item na cadeia de caracteres de valores múltiplos|

**Exemplo:**<br>
Item([proxyAddresss], 1)

---
### <a name="join"></a>Associar
**Função:**<br> Junte-se (separador, source1 source2,...)

**Descrição:**<br> A adesão() é semelhante ao apêndice, exceto que pode combinar múltiplos valores de cadeia de **origem** numa única corda, e cada valor será separado por uma corda **de separador.**

Se um dos valores de origem for um atributo com vários valores, cada valor nesse atributo será Unido, separado pelo valor do separador.

**Parâmetros:**<br> 

| Nome | Obrigatório / repetidos | Tipo | Notas |
| --- | --- | --- | --- |
| **separador** |Required |String |Cadeia utilizada para separar os valores de origem quando eles são concatenados numa cadeia de caracteres. Pode ser "" se nenhum separador é necessário. |
| **fonte1 ... fonteN** |Variável-número de vezes, necessário |String |Cadeia de valores a ser unidas. |

---
### <a name="left"></a>Esquerda
**Função:**<br> Left (cadeia de caracteres, NumChars)

**Descrição:**<br> A função Left retorna um número especificado de caracteres a partir da esquerda de uma cadeia de caracteres. Se numChars = 0, retornará uma cadeia de caracteres vazia.
Se numChars < 0, retornará a cadeia de caracteres de entrada.
Se a cadeia de caracteres for nula, retornará uma cadeia de caracteres vazia.
Se a cadeia de caracteres contiver menos caracteres do que o número especificado em numChars, uma cadeia de caracteres idêntica à cadeia de caracteres (ou seja, contendo todos os caracteres no parâmetro 1) será retornada.

**Parâmetros:**<br> 

| Nome | Obrigatório / repetidos | Tipo | Notas |
| --- | --- | --- | --- |
| **Corda** |Required |Atributo | A cadeia de caracteres da qual retornar os caracteres |
| **Numchars** |Required |Número inteiro | Um número que identifica o número de caracteres a serem retornados do início (à esquerda) da cadeia de caracteres|

**Exemplo:**<br>
Esquerda ("João da Silva", 3)                                                            
Retorna "Joh"

---
### <a name="mid"></a>Mid
**Função:**<br> Mid (origem, início, comprimento)

**Descrição:**<br> Devolve uma subcadeia do valor de origem. Uma subcadeia é uma cadeia que contém apenas alguns dos caracteres da cadeia de origem.

**Parâmetros:**<br> 

| Nome | Obrigatório / repetidos | Tipo | Notas |
| --- | --- | --- | --- |
| **fonte** |Required |String |Normalmente, o nome do atributo. |
| **start** |Required |número inteiro |Índice na cadeia **de origem** onde deve iniciar a subcadeia. Primeiro caractere na cadeia de caracteres terão o índice de 1, o segundo caráter terão índice 2 e assim por diante. |
| **comprimento** |Required |número inteiro |Comprimento da subcadeia. Se o comprimento terminar fora da cadeia de **origem,** a função devolverá o substring do índice de **arranque** até ao fim da cadeia de **origem.** |

---
### <a name="normalizediacritics"></a>NormalizeDiacritics
**Função:**<br> NormalizeDiacritics(source)

**Descrição:**<br> Requer um argumento de cadeia de caracteres. Devolve a cadeia de caracteres, mas com nenhum dos caracteres diacritical substituídos por carateres não diacritical equivalentes. Normalmente, utilizada para converter os nomes de primeiro e último carateres diacritical (marcas de acento sem) em valores legais que podem ser utilizadas em vários identificadores de utilizador, tais como nomes de principal de utilizador, nomes de conta SAM e endereços de e-mail.

**Parâmetros:**<br> 

| Nome | Obrigatório / repetidos | Tipo | Notas |
| --- | --- | --- | --- |
| **fonte** |Required |String | Geralmente um atributo de nome ou sobrenome. |

---
### <a name="not"></a>Não
**Função:**<br> Not(Source)

**Descrição:**<br> Inverte o valor booleano da **fonte.** Se o valor **de origem** for "*Verdadeiro",* devolve "*Falso*". Caso contrário, retorna "*Verdadeiro*".

**Parâmetros:**<br> 

| Nome | Obrigatório / repetidos | Tipo | Notas |
| --- | --- | --- | --- |
| **fonte** |Required |Booleano da cadeia |Os valores **de origem** esperados são "Verdadeiros" ou "Falsos". |

---
### <a name="numfromdate"></a>NumFromDate
**Função:**<br> NumFromDate (valor)

**Descrição:**<br> A função NumFromDate converte um valor DateTime para o formato Ative Directory que é necessário para definir atributos como [contasExpirações](https://docs.microsoft.com/windows/win32/adschema/a-accountexpires). Utilize esta função para converter os valores dateTime recebidos de aplicações de RH em nuvem como Workday e SuccessFactors para a sua representação aD equivalente. 

**Parâmetros:**<br> 

| Nome | Obrigatório / repetidos | Tipo | Notas |
| --- | --- | --- | --- |
| **valor** |Required | String | Linha de hora de data no formato suportado. Para formatos suportados, consulte https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx. |

**Exemplo:**<br>
* Exemplo do dia de trabalho <br>
  Assumindo que pretende mapear o atributo *ContractEndDate* do Dia de Trabalho, que está no formato *2020-12-31-08:00* para o campo de *contasExpires* em AD, aqui está como você pode usar esta função e alterar o tempo compensado para combinar com o seu local. 
  `NumFromDate(Join("", FormatDateTime([ContractEndDate], "yyyy-MM-ddzzz", "yyyy-MM-dd"), "T23:59:59-08:00"))`

* Exemplo de SuccessFactors <br>
  Assumindo que pretende mapear o final do *atributoData* from SuccessFactors que está no formato *M/d/yyyy hh:mm:st* para *contasCampo Expiraem* em AD, aqui está como você pode usar esta função e alterar o fuso horário compensado para combinar com o seu local.
  `NumFromDate(Join("",FormatDateTime([endDate],"M/d/yyyy hh:mm:ss tt","yyyy-MM-dd"),"T23:59:59-08:00"))`


---
### <a name="removeduplicates"></a>RemoveDuplicates
**Função:**<br> RemoveDuplicates (atributo)

**Descrição:**<br> A função RemoveDuplicates usa uma cadeia de caracteres com valores múltiplos e garante que cada valor seja exclusivo.

**Parâmetros:**<br> 

| Nome | Obrigatório / repetidos | Tipo | Notas |
| --- | --- | --- | --- |
| **atributo** |Required |Atributo de valores múltiplos |Atributo com valores múltiplos que terá duplicatas removidas|

**Exemplo:**<br>
Remover Duplicatos([proxyAddresss])                                                                                                       
Retorna um atributo proxyAddress limpo em que todos os valores duplicados foram removidos

---
### <a name="replace"></a>Substituir
**Função:**<br> Substituir (origem, oldValue, regexPattern, regexGroupName, replacementValue, replacementAttributeName, modelo)

**Descrição:**<br>
Substitui os valores dentro de uma cadeia de caracteres. Ele funciona de forma diferente consoante os parâmetros fornecidos:

* Quando for em **idade,** valor e **substituiçãoSão:**
  
  * Substitui todas as ocorrências do **oldValue** na **fonte** por **substituiçãoValor**
* Quando forem fornecidos **valores antigos** e **modelo:**
  
  * Substitui todas as ocorrências do **antigoValor** no **modelo** pelo valor **de origem**
* Quando **regexPattern** e **substituiçãoValor** são fornecidos:

  * A função aplica o **regexPattern** à cadeia de **origem** e pode utilizar os nomes do grupo regex para construir a cadeia para **substituiçãoValue**
* Quando **regexPattern**, **regexGroupName**, **substituiçãoValue** são fornecidos:
  
  * A função aplica o **regexPattern** à cadeia de **origem** e substitui todos os valores que correspondem a **regexGroupName** por **substituiçãoValue**
* Quando **regexPattern**, **regexGroupName**, **substituiçãoAtribuídoNome** é fornecido:
  
  * Se **a fonte** não tiver valor, a **fonte** é devolvida
  * Se **a fonte** tiver um valor, a função aplica o **regexPattern** à cadeia de **origem** e substitui todos os valores correspondentes ao **RegexGroupName** pelo valor associado à **substituiçãoAttributeName**

**Parâmetros:**<br> 

| Nome | Obrigatório / repetidos | Tipo | Notas |
| --- | --- | --- | --- |
| **fonte** |Required |String |Normalmente, o nome do atributo do objeto **de origem.** |
| **oldValue** |Opcional |String |Valor a substituir na **fonte** ou **no modelo.** |
| **regexPattern** |Opcional |String |Padrão Regex para o valor a substituir na **fonte**. Ou, quando é utilizado **o nome propertyname** substituto, padrão para extrair valor da **substituiçãoPropertyName**. |
| **regexGroupName** |Opcional |String |Nome do grupo dentro do **regexPattern**. Só quando for utilizado **o substitutoPropertyName,** extrairemos valor deste grupo como **substitutoValor** da **substituiçãoPropertyName**. |
| **substituiçãoValor** |Opcional |String |Novo valor para substituir um antigo com. |
| **substituiçãoNome atribuído** |Opcional |String |Nome do atributo a ser usado para o valor de substituição |
| **modelo** |Opcional |String |Quando o valor do **modelo** for fornecido, procuraremos **por oldValue** dentro do modelo e substituí-lo-emos pelo valor **de origem.** |

---
### <a name="selectuniquevalue"></a>SelectUniqueValue
**Função:**<br> SelectUniqueValue (uniqueValueRule1, uniqueValueRule2, uniqueValueRule3,...)

**Descrição:**<br> Requer um mínimo de dois argumentos, que são definidas usando expressões de regras de geração de valor exclusivo. A função avalia cada regra e, em seguida, verifica o valor gerado para exclusividade na aplicação/diretório de destino. O primeiro valor exclusivo encontrado vai ser devolvido o. Se todos os valores ainda existirem no destino, a entrada irá obter colocadas em caução em e o motivo é registrado em log nos registos de auditoria. Não há nenhum limite superior ao número de argumentos que pode ser fornecido.

> [!NOTE]
> - Esta é uma função de nível superior, ele não é possível aninhar.
> - Esta função não pode ser aplicada a atributos que têm uma precedência correspondente.  
> - Esta função apenas se destina a ser utilizado para criações de entrada. Ao utilizá-lo com um atributo, detete a propriedade **De Mapeamento aplicar** apenas durante a **criação de objetos**.
> - Atualmente, essa função só tem suporte para "WORKDAY para Active Directory provisionamento de usuário". Ele não pode ser usado com outros aplicativos de provisionamento. 


**Parâmetros:**<br> 

| Nome | Obrigatório / repetidos | Tipo | Notas |
| --- | --- | --- | --- |
| **únicoValueRule1 ... únicoValueRuleN** |Pelo menos 2 são vinculados a necessário, não superior |String | Lista de regras de geração de valor exclusivo a serem avaliadas. |


---
### <a name="singleapproleassignment"></a>SingleAppRoleAssignment
**Função:**<br> SingleAppRoleAssignment([appRoleAssignments])

**Descrição:**<br> Retorna um único appRoleAssignment da lista de todos os appRoleAssignments atribuídos a um usuário para um determinado aplicativo. Essa função é necessária para converter o objeto appRoleAssignments em uma única cadeia de caracteres de nome de função. Observe que a prática recomendada é garantir que apenas um appRoleAssignment seja atribuído a um usuário por vez, e se várias funções forem atribuídas, a cadeia de caracteres de função retornada poderá não ser previsível. 

**Parâmetros:**<br> 

| Nome | Obrigatório / repetidos | Tipo | Notas |
| --- | --- | --- | --- |
| **[appRoleAssignments]** |Required |String |**[appRoleAssignments]** objeto. |

---
### <a name="split"></a>Dividir
**Função:**<br> Divisão (origem, delimitador)

**Descrição:**<br> Divide uma cadeia de caracteres em uma matriz de valores múltiplos, usando o caractere delimitador especificado.

**Parâmetros:**<br> 

| Nome | Obrigatório / repetidos | Tipo | Notas |
| --- | --- | --- | --- |
| **fonte** |Required |String |valor **de origem** para atualização. |
| **delimitador** |Required |String |Especifica o caractere que será usado para dividir a cadeia de caracteres (exemplo: ",") |

---
### <a name="stripspaces"></a>StripSpaces
**Função:**<br> StripSpaces(source)

**Descrição:**<br> Remove todo o espaço ("") carateres da cadeia de caracteres de origem.

**Parâmetros:**<br> 

| Nome | Obrigatório / repetidos | Tipo | Notas |
| --- | --- | --- | --- |
| **fonte** |Required |String |valor **de origem** para atualização. |

---
### <a name="switch"></a>Comutador
**Função:**<br> Comutador (origem, defaultValue, chave1, value1, chave2, value2...)

**Descrição:**<br> Quando o valor **de origem** corresponde a uma **chave,** devolve **valor** para essa **tecla**. Se o valor de **origem** não corresponder a nenhuma tecla, devolve **o predefiniçãoValor**.  **Os** parâmetros de chave e **valor** devem sempre vir em pares. A função espera sempre um número par de parâmetros. A função não deve ser usada para atributos referenciais como Manager. 

**Parâmetros:**<br> 

| Nome | Obrigatório / repetidos | Tipo | Notas |
| --- | --- | --- | --- |
| **fonte** |Required |String |**Valor de origem** para atualização. |
| **defaultValor** |Opcional |String |Valor predefinido a utilizar quando a origem não corresponde a quaisquer chaves. Pode ser uma cadeia de caracteres vazia (""). |
| **chave** |Required |String |**Chave** para comparar o valor **de origem** com. |
| **valor** |Required |String |Valor de substituição da **fonte** que combina com a chave. |

---
### <a name="tolower"></a>toLower
**Função:**<br> ToLower (origem, cultura)

**Descrição:**<br> Pega num valor de cadeia de *origem* e converte-o para minúscula utilizando as regras de cultura especificadas. Se não houver informações de *cultura* especificadas, então utilizará a cultura Invariante.

**Parâmetros:**<br> 

| Nome | Obrigatório / repetidos | Tipo | Notas |
| --- | --- | --- | --- |
| **fonte** |Required |String |Normalmente, o nome do atributo de objeto de origem |
| **cultura** |Opcional |String |O formato para o nome da cultura baseado no RFC 4646 é *languagecode2-country/regioncode2*, onde o *código linguístico2* é o código linguístico de duas letras e o *código de país/região2* é o código de subcultura de duas letras. Os exemplos incluem ja-JP para japonês (Japão) e en-US para inglês (Estados Unidos). Nos casos em que um código de idioma de duas letras não está disponível, um código de três letras derivado de ISO 639-2 é usado.|

---
### <a name="toupper"></a>ToUpper
**Função:**<br> ToUpper (origem, cultura)

**Descrição:**<br> Pega num valor de cadeia de *origem* e converte-o em maiúsculas usando as regras de cultura especificadas. Se não houver informações de *cultura* especificadas, então utilizará a cultura Invariante.

**Parâmetros:**<br> 

| Nome | Obrigatório / repetidos | Tipo | Notas |
| --- | --- | --- | --- |
| **fonte** |Required |String |Normalmente, o nome do atributo de objeto de origem. |
| **cultura** |Opcional |String |O formato para o nome da cultura baseado no RFC 4646 é *languagecode2-country/regioncode2*, onde o *código linguístico2* é o código linguístico de duas letras e o *código de país/região2* é o código de subcultura de duas letras. Os exemplos incluem ja-JP para japonês (Japão) e en-US para inglês (Estados Unidos). Nos casos em que um código de idioma de duas letras não está disponível, um código de três letras derivado de ISO 639-2 é usado.|

---
### <a name="word"></a>Word
**Função:**<br> Word (cadeia de caracteres, WordNumber, delimitadores)

**Descrição:**<br> A função Word retorna uma palavra contida em uma cadeia de caracteres, com base nos parâmetros que descrevem os delimitadores a serem usados e o número da palavra a ser retornado. Cada cadeia de caracteres em seqüência separada por um dos caracteres nos delimitadores é identificada como palavras:

Se o número < 1, retorna uma cadeia de caracteres vazia.
Se a cadeia de caracteres for nula, retornará uma cadeia de caracteres vazia.
Se a cadeia de caracteres contiver menos que palavras de número ou a cadeia de caracteres não contiver palavras identificadas por delimitadores, uma cadeia de caracteres vazia será retornada.

**Parâmetros:**<br> 

| Nome | Obrigatório / repetidos | Tipo | Notas |
| --- | --- | --- | --- |
| **Corda** |Required |Atributo de valores múltiplos |Cadeia de caracteres da qual retornar uma palavra.|
| **Número de palavras** |Required | Número inteiro | Número que identifica qual número de palavras deve retornar|
| **delimitadores** |Required |String| Uma cadeia de caracteres que representa os delimitadores que devem ser usados para identificar palavras|

**Exemplo:**<br>
Word ("The Quick Brown raposa", 3, "")                                                                                       
Retorna "Brown"

Word ("this, String! tem & muitos separadores", 3, ",! & #")                                                                       
Retorna "tem"

---

## <a name="examples"></a>Exemplos
### <a name="strip-known-domain-name"></a>Nome de domínio conhecidos de faixa
É necessário um nome de domínio conhecidos do e-mail de um utilizador para obter um nome de utilizador de faixa. <br>
Por exemplo, se o domínio "contoso.com", em seguida, poderia usar a seguinte expressão:

**Expressão:** <br>
`Replace([mail], "@contoso.com", , ,"", ,)`

**Entrada/saída da amostra:** <br>

* **INPUT** (correio): "john.doe@contoso.com"
* **SAÍDA**: "john.doe"

### <a name="append-constant-suffix-to-user-name"></a>Acrescentar constante sufixo ao nome de utilizador
Se estiver a utilizar uma área de segurança do Salesforce, precisará de acrescentar um sufixo adicional a todos os seus nomes de utilizador antes de sincronizá-los.

**Expressão:** <br>
`Append([userPrincipalName], ".test")`

**Entrada/saída da amostra:** <br>

* **INPUT**: (userPrincipalName): "John.Doe@contoso.com"
* **SAÍDA**: "John.Doe@contoso.com.test"

### <a name="generate-user-alias-by-concatenating-parts-of-first-and-last-name"></a>Gerar o alias do utilizador, concatenando partes do nome próprio e apelido
Terá de gerar um utilizador alias ao colocar os primeiros 3 letras do nome próprio do utilizador e os primeiros 5 letras do apelido do utilizador.

**Expressão:** <br>
`Append(Mid([givenName], 1, 3), Mid([surname], 1, 5))`

**Entrada/saída da amostra:** <br>

* **INPUT** (nome dado): "John"
* **INPUT** (sobrenome): "Doe"
* **SAÍDA**: "JohDoe"

### <a name="remove-diacritics-from-a-string"></a>Remover Diacríticos de uma cadeia de caracteres
É necessário substituir caracteres que contém marcas de acento sem com caracteres equivalentes que não contenham marcas de acento sem.

**Expressão:** <br>
NormalizeDiacritics([givenName])

**Entrada/saída da amostra:** <br>

* **INPUT** (nome dado): "Zoë"
* **SAÍDA**: "Zoe"

### <a name="split-a-string-into-a-multi-valued-array"></a>Dividir uma cadeia de caracteres em uma matriz de vários valores
Você precisa usar uma lista delimitada por vírgulas de cadeias de caracteres e dividi-las em uma matriz que pode ser conectada a um atributo de vários valores, como o atributo PermissionSets do Salesforce. Neste exemplo, uma lista de conjuntos de permissões foi populada no extensionAttribute5 no Azure AD.

**Expressão:** <br>
Divisão([extensãoAtribuído5], "")

**Entrada/saída da amostra:** <br>

* **INPUT** (extensãoAttribute5): "PermissionSetOne, PermisionSetTwo"
* **SAÍDA**: ["PermissionSetone", "PermissionSetTwo"]

### <a name="output-date-as-a-string-in-a-certain-format"></a>Data de saída como uma cadeia de caracteres num determinado formato
Pretende enviar as datas para uma aplicação SaaS num determinado formato. <br>
Por exemplo, que pretende formatar datas do ServiceNow.

**Expressão:** <br>

`FormatDateTime([extensionAttribute1], "yyyyMMddHHmmss.fZ", "yyyy-MM-dd")`

**Entrada/saída da amostra:**

* **INPUT** (extensãoAtribuído1): "20150123105347.1Z"
* **SAÍDA**: "2015-01-23"

### <a name="replace-a-value-based-on-predefined-set-of-options"></a>Substituir um valor com base num conjunto predefinido de opções

Tem de definir o fuso horário do utilizador com base no código de estado armazenado no Azure AD. <br>
Se o código de estado não corresponder a qualquer uma das opções predefinidas, utilize o valor predefinido de "Austrália/Sydney".

**Expressão:** <br>
`Switch([state], "Australia/Sydney", "NSW", "Australia/Sydney","QLD", "Australia/Brisbane", "SA", "Australia/Adelaide")`

**Entrada/saída da amostra:**

* **INPUT** (estado): "QLD"
* **SAÍDA**: "Austrália/Brisbane"

### <a name="replace-characters-using-a-regular-expression"></a>Substituir caracteres usando uma expressão regular
Você precisa localizar caracteres que correspondam a um valor de expressão regular e removê-los.

**Expressão:** <br>

Substituir,[mailNickname], "[a-zA-Z_]*", "", "", ")

**Entrada/saída da amostra:**

* **INPUT** (mailNickname: "john_doe72"
* **SAÍDA**: "72"

### <a name="convert-generated-userprincipalname-upn-value-to-lower-case"></a>Converter o valor de userPrincipalName (UPN) gerado em letras minúsculas
No exemplo a seguir, o valor UPN é gerado pela concatenação dos campos de origem nomepreferido e PreferredLastName e a função ToLower opera na cadeia de caracteres gerada para converter todos os caracteres em letras minúsculas. 

`ToLower(Join("@", NormalizeDiacritics(StripSpaces(Join(".",  [PreferredFirstName], [PreferredLastName]))), "contoso.com"))`

**Entrada/saída da amostra:**

* **ENTRADA** (PreferredFirstName): "John"
* **INPUT** (PreferredLastName): "Smith"
* **SAÍDA**: "john.smith@contoso.com"

### <a name="generate-unique-value-for-userprincipalname-upn-attribute"></a>Gerar um valor exclusivo para o atributo userPrincipalName (UPN)
Com base do usuário nome próprio, segundo nome e sobrenome, terá de gerar um valor para o atributo UPN e verificar seu exclusividade no diretório de destino AD antes de atribuir o valor para o atributo UPN.

**Expressão:** <br>

    SelectUniqueValue( 
        Join("@", NormalizeDiacritics(StripSpaces(Join(".",  [PreferredFirstName], [PreferredLastName]))), "contoso.com"), 
        Join("@", NormalizeDiacritics(StripSpaces(Join(".",  Mid([PreferredFirstName], 1, 1), [PreferredLastName]))), "contoso.com"),
        Join("@", NormalizeDiacritics(StripSpaces(Join(".",  Mid([PreferredFirstName], 1, 2), [PreferredLastName]))), "contoso.com")
    )

**Entrada/saída da amostra:**

* **ENTRADA** (PreferredFirstName): "John"
* **INPUT** (PreferredLastName): "Smith"
* **SAÍDA**: "John.Smith@contoso.com" se o valor upn de John.Smith@contoso.com já não existe no diretório
* **SAÍDA**: "J.Smith@contoso.com" se o valor UPN de John.Smith@contoso.com já existe no diretório
* **SAÍDA**: "Jo.Smith@contoso.com" se já existirem os dois valores UPN acima referidos no diretório

### <a name="flow-mail-value-if-not-null-otherwise-flow-userprincipalname"></a>O valor do fluxo de email se não for nulo; caso contrário, flua userPrincipalName
Você deseja fluir o atributo mail se ele estiver presente. Se não for, você deseja fluir o valor de userPrincipalName em vez disso.

**Expressão:** <br>
`Coalesce([mail],[userPrincipalName])`

**Entrada/saída da amostra:** <br>

* **INPUT** (correio): NULO
* **INPUT** (userPrincipalName): "John.Doe@contoso.com"
* **SAÍDA**: "John.Doe@contoso.com"

## <a name="related-articles"></a>Artigos relacionados
* [Automatizar o fornecimento/deprovisionamento de utilizadores para apps SaaS](../app-provisioning/user-provisioning.md)
* [Personalização de mapeamentos de atributos para fornecimento de utilizadores](../app-provisioning/customize-application-attributes.md)
* [Filtros de deteção para fornecimento de utilizadores](define-conditional-rules-for-provisioning-user-accounts.md)
* [Utilizar o SCIM para ativar o aprovisionamento automático de utilizadores e grupos do Azure Active Directory a aplicações](../app-provisioning/use-scim-to-provision-users-and-groups.md)
* [Notificações de Provisionamento de Contas](../app-provisioning/user-provisioning.md)
* [Lista de Tutoriais sobre Como Integrar Apps SaaS](../saas-apps/tutorial-list.md)
