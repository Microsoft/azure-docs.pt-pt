---
title: Azure AD Connectndo expressões de provisionamento de nuvem e referência de função
description: referência
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 12/02/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7d250377e15b957c10322dbba9ca587dd58944ad
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74793543"
---
# <a name="writing-expressions-for-attribute-mappings-in-azure-active-directory"></a>Gravando expressões para mapeamentos de atributo no Azure Active Directory
Quando você configura o provisionamento de nuvem, um dos tipos de mapeamentos de atributo que você pode especificar é um mapeamento de expressão. 

O mapeamento de expressão permite que você personalize atributos usando uma expressão do tipo script.  Isso permite transformar os dados locais em um valor novo ou diferente.  Por exemplo, talvez você queira combinar dois atributos em um único atributo porque esse único atributo é usado por um dos seus aplicativos de nuvem.

O documento a seguir abordará as expressões do script que são usadas para transformar os dados.  Isso é apenas parte do processo.  Em seguida, você precisará usar essa expressão e colocá-la em uma solicitação da Web para seu locatário.  Para obter mais informações sobre isso, consulte [transformações](how-to-transformation.md)

## <a name="syntax-overview"></a>Visão geral da sintaxe
A sintaxe de expressões para mapeamentos de atributo é que sobrou de funções de Visual Basic for Applications (VBA).

* A expressão inteira deve ser definida em termos de funções, que consistem em um nome seguido por argumentos entre parênteses: <br>
  *FunctionName (`<<argument 1>>`,`<<argument N>>`)*
* Você pode aninhar funções entre si. Por exemplo: <br> *FunctionOne (FunctionTwo (`<<argument1>>`))*
* Você pode passar três tipos diferentes de argumentos em funções:
  
  1. Atributos, que devem ser colocados entre colchetes. Por exemplo: [attributeName]
  2. Constantes de cadeia de caracteres, que devem ser colocadas entre aspas duplas. Por exemplo: "Estados Unidos"
  3. Outras funções. Por exemplo: FunctionOne (`<<argument1>>`, FunctionTwo (`<<argument2>>`))
* Para constantes de cadeia de caracteres, se você precisar de uma barra invertida (\) ou aspas (") na cadeia de caracteres, ela deverá ter um escape com o símbolo de barra invertida (\). Por exemplo: "nome da empresa: \\" contoso\\""

## <a name="list-of-functions"></a>Lista de funções
| Lista de funções | Descrição |
|-----|----|
|[Anexar](#append)|Usa um valor de cadeia de caracteres de origem e acrescenta o sufixo ao final dele.|
|[BitAnd](#bitand)|A função BitAnd define os bits especificados em um valor.|
|[CBool](#cbool)|A função CBool retorna um booliano com base na expressão avaliada|
|[ConvertFromBase64](#convertfrombase64)|A função ConvertFromBase64 converte o valor codificado base64 especificado em uma cadeia de caracteres regular.|
|[ConvertToBase64](#converttobase64)|A função ConvertToBase64 converte uma cadeia de caracteres em uma cadeia de caracteres Base64 Unicode. |
|[ConvertToUTF8Hex](#converttoutf8hex)|A função ConvertToUTF8Hex converte uma cadeia de caracteres em um valor codificado hexadecimal UTF8.|
|[Contar](#count)|A função Count retorna o número de elementos em um atributo de valores múltiplos|
|[CStr](#cstr)|A função CStr converte em um tipo de dados de cadeia de caracteres.|
|[DateFromNum](#datefromnum)|A função DateFromNum converte um valor no formato de data do AD em um tipo DateTime.|
|[DNComponent](#dncomponent)|A função DNComponent retorna o valor de um componente DN especificado indo da esquerda.|
|[Ao](#error)|A função Error é usada para retornar um erro personalizado.|
|[FormatDateTime](#formatdatetime) |Usa uma cadeia de caracteres de data de um formato e a converte em um formato diferente.| 
|[VOLUME](#guid)|O GUID da função gera um novo GUID aleatório.|           
|[IIF](#iif)|A função IIF retorna um de um conjunto de valores possíveis com base em uma condição especificada.|
|[InStr](#instr)|A função InStr localiza a primeira ocorrência de uma subcadeia de caracteres em uma cadeia de caracteres.|
|[Énulo](#isnull)|Se a expressão for avaliada como NULL, a função IsNull retornará true.|
|[IsNullOrEmpty](#isnullorempty)|Se a expressão for nula ou uma cadeia de caracteres vazia, a função IsNullOrEmpty retornará true.|         
|[IsPresent](#ispresent)|Se a expressão for avaliada como uma cadeia de caracteres que não é nula e não estiver vazia, a função IsPresent retornará true.|    
|[IsString](#isstring)|Se a expressão puder ser avaliada como um tipo de cadeia de caracteres, a função isString será avaliada como true.|
|[Item](#item)|A função Item retorna um item de um atributo/cadeia de caracteres de valores múltiplos.|
|[Associar](#join) |Join () é semelhante a Append (), exceto pelo fato de que ele pode combinar vários valores de cadeia de caracteres de **origem** em uma única cadeia de caracteres, e cada valor será separado por uma cadeia de caracteres **separadora** .| 
|[Mantida](#left)|A função Left retorna um número especificado de caracteres a partir da esquerda de uma cadeia de caracteres.|
|[Meio](#mid) |Retorna uma subcadeia de caracteres do valor de origem. Uma subcadeia de caracteres é uma cadeia de caracteres que contém apenas alguns dos caracteres da cadeia de caracteres de origem.|
|[NormalizeDiacritics](#normalizediacritics)|Requer um argumento de cadeia de caracteres. Retorna a cadeia de caracteres, mas com caracteres diacríticos substituídos por caracteres não diacríticos equivalentes.|
|[Válido](#not) |Inverte o valor booliano da **origem**. Se o valor de **Source** for "*true*", retornará "*false*". Caso contrário, retorna "*true*".| 
|[RemoveDuplicates](#removeduplicates)|A função RemoveDuplicates usa uma cadeia de caracteres com valores múltiplos e garante que cada valor seja exclusivo.| 
|[Substitua](#replace) |Substitui valores em uma cadeia de caracteres. | 
|[SelectUniqueValue](#selectuniquevalue)|Requer um mínimo de dois argumentos, que são regras exclusivas de geração de valor definidas usando expressões. A função avalia cada regra e, em seguida, verifica o valor gerado para exclusividade no diretório/aplicativo de destino.| 
|[SingleAppRoleAssignment](#singleapproleassignment)|Retorna um único appRoleAssignment da lista de todos os appRoleAssignments atribuídos a um usuário para um determinado aplicativo.| 
|[Divisão](#split)|Divide uma cadeia de caracteres em uma matriz de valores múltiplos, usando o caractere delimitador especificado.|
|[StringFromSID](#stringfromsid)|A função StringFromSid converte uma matriz de bytes que contém um identificador de segurança em uma cadeia de caracteres.| 
|[StripSpaces](#stripspaces) |Remove todos os caracteres de espaço ("") da cadeia de caracteres de origem.| 
|[Comutador](#switch)|Quando o valor de **origem** corresponde a uma **chave**, retorna um **valor** para essa **chave**. | 
|[ToLower](#tolower)|Usa um valor de cadeia de caracteres de *origem* e o converte em letras minúsculas usando as regras de cultura especificadas.| 
|[ToUpper](#toupper)|Usa um valor de cadeia de caracteres de *origem* e converte-o em maiúsculas usando as regras de cultura especificadas.|
|[Trim](#trim)|A função Trim remove espaços em branco à esquerda e à direita de uma cadeia de caracteres.|
|[Textos](#word)|A função Word retorna uma palavra contida em uma cadeia de caracteres, com base nos parâmetros que descrevem os delimitadores a serem usados e o número da palavra a ser retornado.|

---
### <a name="append"></a>Acrescentar
**Funcionamento**<br> Append (origem, sufixo)

**Ndescrição**<br> Usa um valor de cadeia de caracteres de origem e acrescenta o sufixo ao final dele.

**Parâmetro**<br> 

   | Nome | Obrigatório/repetitivo | Tipo | Notas |
   | --- | --- | --- | --- |
   | **original** |Obrigatório |String |Normalmente o nome do atributo do objeto de origem. |
   | **sufixo** |Obrigatório |String |A cadeia de caracteres que você deseja acrescentar ao final do valor de origem. |

---
### <a name="bitand"></a>BitAnd
**Ndescrição**  
A função BitAnd define os bits especificados em um valor.

**Sintaxe**  
`num BitAnd(num value1, num value2)`

* value1, value2: valores numéricos que devem ser AND'ed juntos

**Comentários**  
Essa função converte ambos os parâmetros na representação binária e define um bit para:

* 0-se um ou ambos os bits correspondentes em *value1* e *value2* forem 0
* 1-se ambos os bits correspondentes forem 1.

Em outras palavras, ele retorna 0 em todos os casos, exceto quando os bits correspondentes de ambos os parâmetros são 1.

**Exemplo:**  
 
 `BitAnd(&HF, &HF7)`</br>
 Retorna 7 porque o hexadecimal "F" e "F7" são avaliados para esse valor.

---

### <a name="cbool"></a>CBool
**Ndescrição**  
A função CBool retorna um booliano com base na expressão avaliada

**Sintaxe**  
`bool CBool(exp Expression)`

**Comentários**  
Se a expressão for avaliada como um valor diferente de zero, CBool retornará true, caso contrário retornará false.

**Exemplo:**  
`CBool([attrib1] = [attrib2])`  

Retorna true se ambos os atributos têm o mesmo valor.

---
### <a name="convertfrombase64"></a>ConvertFromBase64
**Ndescrição**  
A função ConvertFromBase64 converte o valor codificado base64 especificado em uma cadeia de caracteres regular.

**Sintaxe**  
`str ConvertFromBase64(str source)`-presume Unicode para codificação  
`str ConvertFromBase64(str source, enum Encoding)`

* Fonte: cadeia de caracteres codificada em base64  
* Codificação: Unicode, ASCII, UTF8

**Exemplo**  
`ConvertFromBase64("SABlAGwAbABvACAAdwBvAHIAbABkACEA")`  
`ConvertFromBase64("SGVsbG8gd29ybGQh", UTF8)`

Ambos os exemplos retornam "*Olá mundo!* "

---
### <a name="converttobase64"></a>ConvertToBase64
**Ndescrição**  
A função ConvertToBase64 converte uma cadeia de caracteres em uma cadeia de caracteres Base64 Unicode.  
Converte o valor de uma matriz de inteiros em sua representação de cadeia de caracteres equivalente que é codificada com dígitos de base 64.

**Sintaxe**  
`str ConvertToBase64(str source)`

**Exemplo:**  
`ConvertToBase64("Hello world!")`  
Retorna "SABlAGwAbABvACAAdwBvAHIAbABkACEA"

---
### <a name="converttoutf8hex"></a>ConvertToUTF8Hex
**Ndescrição**  
A função ConvertToUTF8Hex converte uma cadeia de caracteres em um valor codificado hexadecimal UTF8.

**Sintaxe**  
`str ConvertToUTF8Hex(str source)`

**Comentários**  
O formato de saída dessa função é usado pelo Azure Active Directory como formato de atributo DN.

**Exemplo:**  
`ConvertToUTF8Hex("Hello world!")`  
Retorna 48656C6C6F20776F726C6421

---
### <a name="count"></a>Contagem
**Ndescrição**  
A função Count retorna o número de elementos em um atributo de valores múltiplos

**Sintaxe**  
`num Count(mvstr attribute)`

---
### <a name="cstr"></a>CStr
**Ndescrição**  
A função CStr converte em um tipo de dados de cadeia de caracteres.

**Sintaxe**  
`str CStr(num value)`  
`str CStr(ref value)`  
`str CStr(bool value)`  

* value: pode ser um valor numérico, um atributo de referência ou um booliano.

**Exemplo:**  
`CStr([dn])`  
Poderia retornar "CN = Joe, DC = contoso, DC = com"

---
### <a name="datefromnum"></a>DateFromNum
**Ndescrição**  
A função DateFromNum converte um valor no formato de data do AD em um tipo DateTime.

**Sintaxe**  
`dt DateFromNum(num value)`

**Exemplo:**  
`DateFromNum([lastLogonTimestamp])`  
`DateFromNum(129699324000000000)`  
Retorna um DateTime que representa 2012-01-01 23:00:00

---
### <a name="dncomponent"></a>DNComponent
**Ndescrição**  
A função DNComponent retorna o valor de um componente DN especificado indo da esquerda.

**Sintaxe**  
`str DNComponent(ref dn, num ComponentNumber)`

* DN: o atributo de referência a ser interpretado
* ComponentNumber: o componente no DN a ser retornado

**Exemplo:**  
`DNComponent(CRef([dn]),1)`  
Se DN for "CN = Joe, ou =...", ele retornará Joe

---
### <a name="error"></a>Erro
**Ndescrição**  
A função Error é usada para retornar um erro personalizado.

**Sintaxe**  
`void Error(str ErrorMessage)`

**Exemplo:**  
`IIF(IsPresent([accountName]),[accountName],Error("AccountName is required"))`  
Se o atributo accountName não estiver presente, acione um erro no objeto.

---
### <a name="formatdatetime"></a>formatDateTime
**Funcionamento**<br> FormatDateTime (origem, inputFormat, outputFormat)

**Ndescrição**<br> Usa uma cadeia de caracteres de data de um formato e a converte em um formato diferente.

**Parâmetro**<br> 

   | Nome | Obrigatório/repetitivo | Tipo | Notas |
   | --- | --- | --- | --- |
   | **original** |Obrigatório |String |Normalmente o nome do atributo do objeto de origem. |
   | **inputFormat** |Obrigatório |String |Formato esperado do valor de origem. Para formatos com suporte, consulte [https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx). |
   | **outputFormat** |Obrigatório |String |Formato da data de saída. |

---
### <a name="guid"></a>GUID
**Ndescrição**  
O GUID da função gera um novo GUID aleatório

**Sintaxe**  
`str Guid()`

---
### <a name="iif"></a>IIF
**Ndescrição**  
A função IIF retorna um de um conjunto de valores possíveis com base em uma condição especificada.

**Sintaxe**  
`var IIF(exp condition, var valueIfTrue, var valueIfFalse)`

* Condition: qualquer valor ou expressão que possa ser avaliada como true ou false.
* valueIfTrue: se a condição for avaliada como true, o valor retornado.
* valueIfFalse: se a condição for avaliada como false, o valor retornado.

**Exemplo:**  
`IIF([employeeType]="Intern","t-" & [alias],[alias])`  
 Se o usuário for um estagiário, retorna o alias de um usuário com "t-" adicionado ao início dele, caso contrário retorna o alias do usuário como está.

---
### <a name="instr"></a>InStr
**Ndescrição**  
A função InStr localiza a primeira ocorrência de uma subcadeia de caracteres em uma cadeia de caracteres

**Sintaxe**  

`num InStr(str stringcheck, str stringmatch)`  
`num InStr(str stringcheck, str stringmatch, num start)`  
`num InStr(str stringcheck, str stringmatch, num start , enum compare)`

* StringCheck: cadeia de caracteres a ser pesquisada
* StringMatch: cadeia de caracteres a ser encontrada
* Start: posição inicial para localizar a subcadeia de caracteres
* comparar: vbTextCompare ou vbBinaryCompare

**Comentários**  
Retorna a posição onde a subcadeia de caracteres foi encontrada ou 0 se não for encontrada.

**Exemplo:**  
`InStr("The quick brown fox","quick")`  
Avalia para 5

`InStr("repEated","e",3,vbBinaryCompare)`  
Avalia para 7

---
### <a name="isnull"></a>Énulo
**Ndescrição**  
Se a expressão for avaliada como NULL, a função IsNull retornará true.

**Sintaxe**  
`bool IsNull(var Expression)`

**Comentários**  
Para um atributo, um NULL é expresso pela ausência do atributo.

**Exemplo:**  
`IsNull([displayName])`  
Retornará true se o atributo não estiver presente no CS ou no MV.

---
### <a name="isnullorempty"></a>IsNullOrEmpty
**Ndescrição**  
Se a expressão for nula ou uma cadeia de caracteres vazia, a função IsNullOrEmpty retornará true.

**Sintaxe**  
`bool IsNullOrEmpty(var Expression)`

**Comentários**  
Para um atributo, isso será avaliado como true se o atributo estiver ausente ou estiver presente, mas for uma cadeia de caracteres vazia.  
O inverso dessa função é chamado ispresente.

**Exemplo:**  
`IsNullOrEmpty([displayName])`  
Retornará true se o atributo não estiver presente ou for uma cadeia de caracteres vazia no CS ou no MV.

---
### <a name="ispresent"></a>IsPresent
**Ndescrição**  
Se a expressão for avaliada como uma cadeia de caracteres que não é nula e não estiver vazia, a função IsPresent retornará true.

**Sintaxe**  
`bool IsPresent(var expression)`

**Comentários**  
O inverso dessa função é chamado de IsNullOrEmpty.

**Exemplo:**  
`Switch(IsPresent([directManager]),[directManager], IsPresent([skiplevelManager]),[skiplevelManager], IsPresent([director]),[director])`

---
### <a name="item"></a>Item
**Ndescrição**  
A função Item retorna um item de um atributo/cadeia de caracteres de valores múltiplos.

**Sintaxe**  
`var Item(mvstr attribute, num index)`

* atributo: atributo de valores múltiplos
* índice: índice para um item na cadeia de caracteres de valores múltiplos.

**Comentários**  
A função item é útil junto com a função Contains, uma vez que a última função retorna o índice para um item no atributo de valores múltiplos.

Gera um erro se o índice está fora dos limites.

**Exemplo:**  
`Mid(Item([proxyAddresses],Contains([proxyAddresses], "SMTP:")),6)`  
Retorna o endereço de email principal.

---
### <a name="isstring"></a>IsString
**Ndescrição**  
Se a expressão puder ser avaliada como um tipo de cadeia de caracteres, a função isString será avaliada como true.

**Sintaxe**  
`bool IsString(var expression)`

**Comentários**  
Usado para determinar se CStr () pode ser bem-sucedido para analisar a expressão.

---
### <a name="join"></a>Associar
**Funcionamento**<br> Junção (separador, origem1, origem2,...)

**Ndescrição**<br> Join () é semelhante a Append (), exceto pelo fato de que ele pode combinar vários valores de cadeia de caracteres de **origem** em uma única cadeia de caracteres, e cada valor será separado por uma cadeia de caracteres **separadora** .

Se um dos valores de origem for um atributo com vários valores, cada valor nesse atributo será Unido, separado pelo valor do separador.

**Parâmetro**<br> 

   | Nome | Obrigatório/repetitivo | Tipo | Notas |
   | --- | --- | --- | --- |
   | **caractere** |Obrigatório |String |Cadeia de caracteres usada para separar valores de origem quando eles são concatenados em uma cadeia de caracteres. Pode ser "" se nenhum separador for necessário. |
   | **origem1... origemn** |Obrigatório, número da variável de vezes |String |Valores de cadeia de caracteres a serem Unidos. |

---
### <a name="left"></a>Mantida
**Ndescrição**  
A função Left retorna um número especificado de caracteres a partir da esquerda de uma cadeia de caracteres.

**Sintaxe**  
`str Left(str string, num NumChars)`

* String: a cadeia de caracteres da qual retornar os caracteres
* NumChars: um número que identifica o número de caracteres a serem retornados do início (à esquerda) da cadeia de caracteres

**Comentários**  
Uma cadeia de caracteres que contém os primeiros caracteres numChars na cadeia de caracteres:

* Se numChars = 0, retornará uma cadeia de caracteres vazia.
* Se numChars < 0, retornará a cadeia de caracteres de entrada.
* Se a cadeia de caracteres for nula, retornará uma cadeia de caracteres vazia.

Se a cadeia de caracteres contiver menos caracteres do que o número especificado em numChars, uma cadeia de caracteres idêntica à cadeia de caracteres (ou seja, contendo todos os caracteres no parâmetro 1) será retornada.

**Exemplo:**  
`Left("John Doe", 3)`  
Retorna `Joh`.

---
### <a name="mid"></a>Meio
**Funcionamento**<br> Mid (origem, início, comprimento)

**Ndescrição**<br> Retorna uma subcadeia de caracteres do valor de origem. Uma subcadeia de caracteres é uma cadeia de caracteres que contém apenas alguns dos caracteres da cadeia de caracteres de origem.

**Parâmetro**<br> 

   | Nome | Obrigatório/repetitivo | Tipo | Notas |
   | --- | --- | --- | --- |
   | **original** |Obrigatório |String |Normalmente o nome do atributo. |
   | **start** |Obrigatório |número inteiro |Índice na cadeia de caracteres de **origem** em que a subcadeia de caracteres deve iniciar. O primeiro caractere na cadeia de caracteres terá o índice de 1, o segundo caractere terá o índice 2 e assim por diante. |
   | **muito** |Obrigatório |número inteiro |Comprimento da subcadeia de caracteres. Se Length terminar fora da cadeia de caracteres de **origem** , a função retornará substring do índice **inicial** até o fim da cadeia de caracteres de **origem** . |

---
### <a name="normalizediacritics"></a>NormalizeDiacritics
**Funcionamento**<br> NormalizeDiacritics (origem)

**Ndescrição**<br> Requer um argumento de cadeia de caracteres. Retorna a cadeia de caracteres, mas com caracteres diacríticos substituídos por caracteres não diacríticos equivalentes. Normalmente usado para converter nomes e sobrenomes que contêm caracteres diacríticos (acentos) em valores válidos que podem ser usados em vários identificadores de usuário, como nomes de entidade de usuário, nomes de conta SAM e endereços de email.

**Parâmetro**<br> 

   | Nome | Obrigatório/repetitivo | Tipo | Notas |
   | --- | --- | --- | --- |
   | **original** |Obrigatório |String | Geralmente um atributo de nome ou sobrenome. |

---
### <a name="not"></a>Não
**Funcionamento**<br> Não (origem)

**Ndescrição**<br> Inverte o valor booliano da **origem**. Se o valor de **Source** for "*true*", retornará "*false*". Caso contrário, retorna "*true*".

**Parâmetro**<br> 

   | Nome | Obrigatório/repetitivo | Tipo | Notas |
   | --- | --- | --- | --- |
   | **original** |Obrigatório |Cadeia de caracteres booliana |Os valores de **origem** esperados são "true" ou "false". |

---
### <a name="removeduplicates"></a>RemoveDuplicates
**Ndescrição**  
A função RemoveDuplicates usa uma cadeia de caracteres com valores múltiplos e garante que cada valor seja exclusivo.

**Sintaxe**  
`mvstr RemoveDuplicates(mvstr attribute)`

**Exemplo:**  
`RemoveDuplicates([proxyAddresses])`  
Retorna um atributo proxyAddress limpo em que todos os valores duplicados foram removidos.

---
### <a name="replace"></a>Substituir
**Funcionamento**<br> Replace (origem, oldValue, regexPattern, regexGroupName, replacevalue, replacementAttributeName, modelo)

**Ndescrição**<br>
Substitui valores em uma cadeia de caracteres. Ele funciona de maneira diferente, dependendo dos parâmetros fornecidos:

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

   | Nome | Obrigatório/repetitivo | Tipo | Notas |
   | --- | --- | --- | --- |
   | **original** |Obrigatório |String |Normalmente o nome do atributo do objeto de **origem** . |
   | **oldValue** |Opcional |String |Valor a ser substituído na **origem** ou no **modelo**. |
   | **regexPattern** |Opcional |String |Padrão Regex para o valor a ser substituído na **origem**. Ou, quando **replacementPropertyName** é usado, Pattern para extrair o valor de **replacementPropertyName**. |
   | **regexGroupName** |Opcional |String |Nome do grupo dentro de **regexPattern**. Somente quando **replacementPropertyName** for usado, Extraíremos o valor desse grupo como **replacevalue** de **replacementPropertyName**. |
   | **replacementValue** |Opcional |String |Novo valor para substituir um antigo por. |
   | **replacementAttributeName** |Opcional |String |Nome do atributo a ser usado para o valor de substituição |
   | **modelos** |Opcional |String |Quando o valor do **modelo** for fornecido, procuraremos **OldValue** dentro do modelo e o substituíremos pelo valor de **origem** . |

---
### <a name="selectuniquevalue"></a>SelectUniqueValue
**Funcionamento**<br> SelectUniqueValue(uniqueValueRule1, uniqueValueRule2, uniqueValueRule3, ...)

**Ndescrição**<br> Requer um mínimo de dois argumentos, que são regras exclusivas de geração de valor definidas usando expressões. A função avalia cada regra e, em seguida, verifica o valor gerado para exclusividade no diretório/aplicativo de destino. O primeiro valor exclusivo encontrado será aquele retornado. Se todos os valores já existirem no destino, a entrada será mais caução e o motivo é registrado nos logs de auditoria. Não há nenhum limite superior para o número de argumentos que podem ser fornecidos.

> [!NOTE]
> - Essa é uma função de nível superior, não pode ser aninhada.
> - Esta função não pode ser aplicada a atributos que têm uma precedência correspondente.  
> - Essa função destina-se apenas a ser usada para criações de entrada. Ao usá-lo com um atributo, defina a propriedade **aplicar mapeamento** para **somente durante a criação do objeto**.
> - Atualmente, essa função só tem suporte para "WORKDAY para Active Directory provisionamento de usuário". Ele não pode ser usado com outros aplicativos de provisionamento. 


**Parâmetro**<br> 

   | Nome | Obrigatório/repetitivo | Tipo | Notas |
   | --- | --- | --- | --- |
   | **uniqueValueRule1 ... uniqueValueRuleN** |Pelo menos 2 são necessários, sem limite superior |String | Lista de regras de geração de valor exclusivo a serem avaliadas. |


---
### <a name="singleapproleassignment"></a>SingleAppRoleAssignment
**Funcionamento**<br> SingleAppRoleAssignment ([appRoleAssignments])

**Ndescrição**<br> Retorna um único appRoleAssignment da lista de todos os appRoleAssignments atribuídos a um usuário para um determinado aplicativo. Essa função é necessária para converter o objeto appRoleAssignments em uma única cadeia de caracteres de nome de função. Observe que a prática recomendada é garantir que apenas um appRoleAssignment seja atribuído a um usuário por vez, e se várias funções forem atribuídas, a cadeia de caracteres de função retornada poderá não ser previsível. 

**Parâmetro**<br> 

  | Nome | Obrigatório/repetitivo | Tipo | Notas |
  |--- | --- | --- | --- |
  | **AppRoleAssignments** |Obrigatório |String |objeto **[appRoleAssignments]** . |

---
### <a name="split"></a>Dividir
**Funcionamento**<br> Divisão (origem, delimitador)

**Ndescrição**<br> Divide uma cadeia de caracteres em uma matriz de valores múltiplos, usando o caractere delimitador especificado.

**Parâmetro**<br> 

   | Nome | Obrigatório/repetitivo | Tipo | Notas |
   | --- | --- | --- | --- |
   | **original** |Obrigatório |String |valor de **origem** a ser atualizado. |
   | **delimitador** |Obrigatório |String |Especifica o caractere que será usado para dividir a cadeia de caracteres (exemplo: ",") |

---
### <a name="stringfromsid"></a>StringFromSid
**Ndescrição**  
A função StringFromSid converte uma matriz de bytes que contém um identificador de segurança em uma cadeia de caracteres.

**Sintaxe**  
`str StringFromSid(bin ObjectSID)`  

---
### <a name="stripspaces"></a>StripSpaces
**Funcionamento**<br> StripSpaces (origem)

**Ndescrição**<br> Remove todos os caracteres de espaço ("") da cadeia de caracteres de origem.

**Parâmetro**<br> 

   | Nome | Obrigatório/repetitivo | Tipo | Notas |
   | --- | --- | --- | --- |
   | **original** |Obrigatório |String |valor de **origem** a ser atualizado. |

---
### <a name="switch"></a>Comutador
**Funcionamento**<br> Switch (Source, defaultValue, key1, value1, Key2, value2,...)

**Ndescrição**<br> Quando o valor de **origem** corresponde a uma **chave**, retorna um **valor** para essa **chave**. Se o valor de **Source** não corresponder a nenhuma chave, retornará **DefaultValue**.  Os parâmetros de **chave** e **valor** sempre devem vir em pares. A função sempre espera um número par de parâmetros.

**Parâmetro**<br> 

   | Nome | Obrigatório/repetitivo | Tipo | Notas |
   | --- | --- | --- | --- |
   | **original** |Obrigatório |String |Valor de **origem** a ser atualizado. |
   | **ValorPadrão** |Opcional |String |Valor padrão a ser usado quando a origem não corresponde a nenhuma chave. Pode ser uma cadeia de caracteres vazia (""). |
   | **chave** |Obrigatório |String |**Chave** para comparar o valor de **origem** com. |
   | **valor** |Obrigatório |String |Valor de substituição para a **origem** correspondente à chave. |

---
### <a name="tolower"></a>toLower
**Funcionamento**<br> ToLower (origem, cultura)

**Ndescrição**<br> Usa um valor de cadeia de caracteres de *origem* e o converte em letras minúsculas usando as regras de cultura especificadas. Se não houver nenhuma informação de *cultura* especificada, ela usará cultura invariável.

**Parâmetro**<br> 

   | Nome | Obrigatório/repetitivo | Tipo | Notas |
   | --- | --- | --- | --- |
   | **original** |Obrigatório |String |Geralmente o nome do atributo do objeto de origem |
   | **UICulture** |Opcional |String |O formato do nome da cultura baseado em RFC 4646 é *languagecode2-Country/regioncode2*, em que *languagecode2* é o código de idioma de duas letras e *Country/regioncode2* é o código de subcultura de duas letras. Os exemplos incluem ja-JP para japonês (Japão) e en-US para inglês (Estados Unidos). Nos casos em que um código de idioma de duas letras não está disponível, um código de três letras derivado de ISO 639-2 é usado.|

---

### <a name="toupper"></a>toUpper
**Funcionamento**<br> ToUpper (origem, cultura)

**Ndescrição**<br> Usa um valor de cadeia de caracteres de *origem* e converte-o em maiúsculas usando as regras de cultura especificadas. Se não houver nenhuma informação de *cultura* especificada, ela usará cultura invariável.

**Parâmetro**<br> 

  | Nome | Obrigatório/repetitivo | Tipo | Notas |
  | --- | --- | --- | --- |
  | **original** |Obrigatório |String |Normalmente o nome do atributo do objeto de origem. |
  | **UICulture** |Opcional |String |O formato do nome da cultura baseado em RFC 4646 é *languagecode2-Country/regioncode2*, em que *languagecode2* é o código de idioma de duas letras e *Country/regioncode2* é o código de subcultura de duas letras. Os exemplos incluem ja-JP para japonês (Japão) e en-US para inglês (Estados Unidos). Nos casos em que um código de idioma de duas letras não está disponível, um código de três letras derivado de ISO 639-2 é usado.|

---

### <a name="trim"></a>Trim
**Ndescrição**  
A função Trim remove espaços em branco à esquerda e à direita de uma cadeia de caracteres.

**Sintaxe**  
`str Trim(str value)`  

**Exemplo:**  
`Trim(" Test ")`  
Retorna "Test".

`Trim([proxyAddresses])`  
Remove espaços à esquerda e à direita para cada valor no atributo proxyAddress.

---
### <a name="word"></a>Word
**Ndescrição**  
A função Word retorna uma palavra contida em uma cadeia de caracteres, com base nos parâmetros que descrevem os delimitadores a serem usados e o número da palavra a ser retornado.

**Sintaxe**  
`str Word(str string, num WordNumber, str delimiters)`

* String: a cadeia de caracteres da qual retornar uma palavra.
* WordNumber: um número que identifica qual número de palavras deve retornar.
* delimitadores: uma cadeia de caracteres que representa os delimitadores que devem ser usados para identificar palavras

**Comentários**  
Cada cadeia de caracteres em seqüência separada por um dos caracteres nos delimitadores é identificada como palavras:

* Se o número < 1, retorna uma cadeia de caracteres vazia.
* Se a cadeia de caracteres for nula, retornará uma cadeia de caracteres vazia.

Se a cadeia de caracteres contiver menos que palavras de número ou a cadeia de caracteres não contiver palavras identificadas por delimitadores, uma cadeia de caracteres vazia será retornada.

**Exemplo:**  
`Word("The quick brown fox",3," ")`  
Retorna "Brown"

`Word("This,string!has&many separators",3,",!&#")`  
Retornaria "tem"

## <a name="examples"></a>Exemplos
### <a name="strip-known-domain-name"></a>Remover nome de domínio conhecido
Você precisa remover um nome de domínio conhecido do email de um usuário para obter um nome de usuário. <br>
Por exemplo, se o domínio for "contoso.com", você poderá usar a seguinte expressão:

**Expressão** <br>
`Replace([mail], "@contoso.com", , ,"", ,)`

**Entrada/saída de exemplo:** <br>

* **Entrada** (email): "john.doe@contoso.com"
* **Saída**: "João. Doe"

### <a name="append-constant-suffix-to-user-name"></a>Acrescentar sufixo constante ao nome de usuário
Se você estiver usando uma área restrita Salesforce, talvez seja necessário acrescentar um sufixo adicional a todos os nomes de usuário antes de sincronizá-los.

**Expressão** <br>
`Append([userPrincipalName], ".test")`

**Entrada/saída de exemplo:** <br>

* **Entrada**: (userPrincipalName): "John.Doe@contoso.com"
* **Saída**: "John.Doe@contoso.com.test"

### <a name="generate-user-alias-by-concatenating-parts-of-first-and-last-name"></a>Gerar alias de usuário concatenando partes do nome e sobrenome
Você precisa gerar um alias de usuário, colocando as três primeiras letras do primeiro nome do usuário e as 5 primeiras letras do sobrenome do usuário.

**Expressão** <br>
`Append(Mid([givenName], 1, 3), Mid([surname], 1, 5))`

**Entrada/saída de exemplo:** <br>

* **Entrada** (fornecida): "João"
* **Entrada** (sobrenome): "Doe"
* **Saída**: "davibarros"

### <a name="remove-diacritics-from-a-string"></a>Remover diacríticos de uma cadeia de caracteres
Você precisa substituir caracteres que contenham acentos com caracteres equivalentes que não contenham marcas de acentuação.

**Expressão** <br>
NormalizeDiacritics ([excertoname])

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

### <a name="output-date-as-a-string-in-a-certain-format"></a>Data de saída como uma cadeia de caracteres em um determinado formato
Você deseja enviar datas para um aplicativo SaaS em um determinado formato. <br>
Por exemplo, você deseja formatar datas para o ServiceNow.

**Expressão** <br>

`FormatDateTime([extensionAttribute1], "yyyyMMddHHmmss.fZ", "yyyy-MM-dd")`

**Entrada/saída de exemplo:**

* **Entrada** (extensionAttribute1): "20150123105347.1 z"
* **Saída**: "2015-01-23"

### <a name="replace-a-value-based-on-predefined-set-of-options"></a>Substituir um valor com base no conjunto predefinido de opções

Você precisa definir o fuso horário do usuário com base no código de estado armazenado no Azure AD. <br>
Se o código de estado não corresponder a nenhuma das opções predefinidas, use o valor padrão de "Austrália/Sydney".

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

### <a name="generate-unique-value-for-userprincipalname-upn-attribute"></a>Gerar valor exclusivo para o atributo userPrincipalName (UPN)
Com base no nome do usuário, no nome do meio e no sobrenome, você precisa gerar um valor para o atributo UPN e verificar sua exclusividade no diretório do AD de destino antes de atribuir o valor ao atributo UPN.

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


## <a name="next-steps"></a>Passos seguintes 

- [O que é provisionamento?](what-is-provisioning.md)
- [O que é Azure AD Connect provisionamento de nuvem?](what-is-cloud-provisioning.md)
