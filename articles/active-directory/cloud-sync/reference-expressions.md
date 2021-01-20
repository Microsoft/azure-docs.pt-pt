---
title: Azure AD Connect expressões de sincronização de nuvem e referência de função
description: referência
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 12/02/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 869d60d4ccb3bae58924fe4221f83b1b0125ae04
ms.sourcegitcommit: 8a74ab1beba4522367aef8cb39c92c1147d5ec13
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/20/2021
ms.locfileid: "98613537"
---
# <a name="writing-expressions-for-attribute-mappings-in-azure-active-directory"></a>Expressãos de escrita para mapeamentos de atributos no Azure Ative Directory
Quando configura sincronização de nuvem, um dos tipos de mapeamentos de atributos que pode especificar é um mapeamento de expressão. 

O mapeamento de expressão permite-lhe personalizar atributos usando uma expressão semelhante a um script.  Isto permite-lhe transformar os dados no local num valor novo ou diferente.  Por exemplo, pode querer combinar dois atributos num único atributo porque este único atributo é usado por uma das suas aplicações em nuvem.

O documento seguinte cobrirá as expressões semelhantes ao script que são usadas para transformar os dados.  Isto é apenas uma parte do processo.  Em seguida, você precisará usar esta expressão e colocá-la em um pedido web para o seu inquilino.  Para mais informações sobre as [transformações](how-to-transformation.md)

## <a name="syntax-overview"></a>Visão geral da sintaxe
A sintaxe para expressões para mapeamentos de atributos faz lembrar as funções De Bases Visuais para Aplicações (VBA).

* Toda a expressão deve ser definida em termos de funções, que consistem num nome seguido de argumentos em parênteses: <br>
  *Nome de `<<argument 1>>` função, `<<argument N>>`*
* Podem nidificar funções dentro umas das outras. Por exemplo: <br> *FunctionOne (FunctionTwo) `<<argument1>>`*
* Pode passar três tipos diferentes de argumentos em funções:
  
  1. Atributos, que devem ser fechados em suportes quadrados. Por exemplo: [atributoName]
  2. Constantes de cordas, que devem ser fechadas em ações duplas. Por exemplo: "Estados Unidos"
  3. Outras Funções. Por exemplo: FunctionOne( `<<argument1>>` FunctionTwo( `<<argument2>>` ))
* Para as constantes de corda, se necessitar de uma inclinação traseira (\ ) ou marca de citação (" ) na corda, deve ser escapado com o símbolo de backslash (\ ). Por exemplo: "Nome da empresa: \\ \\ "Contoso".

## <a name="list-of-functions"></a>Lista de funções
| Lista de funções | Descrição |
|-----|----|
|[Append](#append)|Pega num valor de cadeia de origem e apê o sufixo até ao fim.|
|[BitAnd](#bitand)|A função BitAnd define bits especificados num valor.|
|[CBool](#cbool)|A função CBool devolve um Boolean com base na expressão avaliada|
|[ConverterFromBase64](#convertfrombase64)|A função ConvertFromBase64 converte o valor codificado base 64 especificado para uma cadeia regular.|
|[ConvertToBase64](#converttobase64)|A função ConvertToBase64 converte uma corda numa cadeia base Unicode64. |
|[ConvertToUTF8Hex](#converttoutf8hex)|A função ConvertToUTF8Hex converte uma cadeia para um valor codificado utf8 Hex.|
|[de palavras](#count)|A função Contagem devolve o número de elementos num atributo multi-valor|
|[Cstr](#cstr)|A função CStr converte-se num tipo de dados de cadeia.|
|[DataFromNum](#datefromnum)|A função DataFromNum converte um valor no formato de data da AD para um tipo DateTime.|
|[DNComponente](#dncomponent)|A função DNComponent devolve o valor de um componente DN especificado que vai da esquerda.|
|[Erro](#error)|A função Erro é utilizada para retornar um erro personalizado.|
|[FormatDatime](#formatdatetime) |Pega numa cadeia de datas de um formato e converte-a num formato diferente.| 
|[GUIADOR](#guid)|A função Guid gera um novo GUID aleatório.|           
|[IIF](#iif)|A função IIF devolve um de um conjunto de valores possíveis com base numa condição especificada.|
|[InStr](#instr)|A função InStr encontra a primeira ocorrência de um sublamamento numa corda.|
|[Isnull](#isnull)|Se a expressão avaliar a Nulo, então a função IsNull retorna verdadeiramente.|
|[IsNullOrEmpty](#isnullorempty)|Se a expressão for nula ou uma corda vazia, então a função IsNullOrEmpty retorna verdadeiramente.|         
|[IsPresent](#ispresent)|Se a expressão avaliar a uma cadeia que não é nula e não está vazia, então a função IsPresent retorna verdadeiramente.|    
|[IsString](#isstring)|Se a expressão pode ser avaliada para um tipo de corda, então a função IsString avalia para True.|
|[Item](#item)|A função Item devolve um item de uma cadeia/atributo multi-valor.|
|[Join](#join) |A junção é semelhante ao Apêndice,), exceto que pode combinar múltiplos valores de cadeia de **origem** numa única corda, e cada valor será separado por uma corda **separador.**| 
|[Esquerda](#left)|A função Esquerda retorna um número especificado de caracteres à esquerda de uma corda.|
|[Meio](#mid) |Devolve um sub-cordão do valor de origem. Um sub-cordão é uma cadeia que contém apenas alguns dos caracteres da cadeia de origem.|
|[NormalizarDiacráticos](#normalizediacritics)|Requer um argumento de corda. Devolve a corda, mas com caracteres diacríticos substituídos por caracteres não diacríticos equivalentes.|
|[Não](#not) |Inverte o valor booleano da **fonte**. Se o valor **da fonte** for "*Verdadeiro*", retorna "*Falso*". Caso contrário, retorna "*Verdadeiro*".| 
|[Removeduplicates](#removeduplicates)|A função RemoveDuplicates requer uma corda multi-valorizada e certifique-se de que cada valor é único.| 
|[Substituir](#replace) |Substitui valores dentro de uma corda. | 
|[SelecioneUniqueValue](#selectuniquevalue)|Requer um mínimo de dois argumentos, que são regras únicas de geração de valor definidas usando expressões. A função avalia cada regra e, em seguida, verifica o valor gerado para a singularidade na aplicação/diretório-alvo.| 
|[Assinatura SingleAppRoleAssignment](#singleapproleassignment)|Devolve um único apRoleAssignment da lista de todos os pedidos de apreciados atribuídos a um utilizador para uma determinada aplicação.| 
|[Divisão](#split)|Divide uma corda numa matriz multi-valorizada, utilizando o carácter delimiter especificado.|
|[StringFromSID](#stringfromsid)|A função StringFromSid converte uma matriz de byte contendo um identificador de segurança a uma corda.| 
|[StripSpaces](#stripspaces) |Remove todos os caracteres do espaço (" ") da cadeia de origem.| 
|[Comutador](#switch)|Quando o valor **de origem** corresponde a uma **chave,** devolve **valor** a essa **chave**. | 
|[ToLower](#tolower)|Pega num valor de cadeia *de origem* e converte-o em minúsculas usando as regras de cultura especificadas.| 
|[Toupper](#toupper)|Pega num valor de cadeia *de origem* e converte-o em maiúsão utilizando as regras de cultura especificadas.|
|[Guarnição](#trim)|A função Trim remove espaços brancos de condução e de fuga de uma corda.|
|[Word](#word)|A função Word devolve uma palavra contida dentro de uma cadeia, com base em parâmetros que descrevem os delimitadores a utilizar e o número da palavra para devolver.|

---
### <a name="append"></a>Acrescentar
**Função:**<br> Apêndice (fonte, sufixo)

**Description:**<br> Pega num valor de cadeia de origem e apê o sufixo até ao fim.

**Parâmetros:**<br> 

   | Nome | Obrigatório/ Repetição | Tipo | Notas |
   | --- | --- | --- | --- |
   | **fonte** |Necessário |String |Normalmente o nome do atributo do objeto de origem. |
   | **sufixo** |Necessário |String |A corda que quer anexar ao fim do valor de origem. |

---
### <a name="bitand"></a>BitAnd
**Description:**  
A função BitAnd define bits especificados num valor.

**Sintaxe:**  
`num BitAnd(num value1, num value2)`

* valor1, valor2: valores numéricos que devem ser e'ed juntos

**Observações:**  
Esta função converte ambos os parâmetros para a representação binária e define um pouco para:

* 0 - se uma ou ambas as bits correspondentes no *valor1* e *no valor2* forem 0
* 1 - se ambas as partes correspondentes forem 1.

Por outras palavras, devolve 0 em todos os casos, exceto quando os pedaços correspondentes de ambos os parâmetros são 1.

**Exemplo:**  
 
 `BitAnd(&HF, &HF7)`</br>
 Devoluções 7 porque hexadecimal "F" e "F7" avaliam este valor.

---

### <a name="cbool"></a>CBool
**Description:**  
A função CBool devolve um Boolean com base na expressão avaliada

**Sintaxe:**  
`bool CBool(exp Expression)`

**Observações:**  
Se a expressão avalia um valor não-zero, então o CBool devolve True, caso contrário retorna Falso.

**Exemplo:**  
`CBool([attrib1] = [attrib2])`  

Devoluções Verdadeiras se ambos os atributos tiverem o mesmo valor.

---
### <a name="convertfrombase64"></a>ConverterFromBase64
**Description:**  
A função ConvertFromBase64 converte o valor codificado base 64 especificado para uma cadeia regular.

**Sintaxe:**  
`str ConvertFromBase64(str source)` - assume o Unicode para codificação  
`str ConvertFromBase64(str source, enum Encoding)`

* fonte: Base64 cadeia codificada  
* Codificação: Unicode, ASCII, UTF8

**Exemplo**  
`ConvertFromBase64("SABlAGwAbABvACAAdwBvAHIAbABkACEA")`  
`ConvertFromBase64("SGVsbG8gd29ybGQh", UTF8)`

Ambos os exemplos regressam "*Olá mundo!*"

---
### <a name="converttobase64"></a>ConvertToBase64
**Description:**  
A função ConvertToBase64 converte uma corda numa cadeia base Unicode64.  
Converte o valor de uma matriz de inteiros à sua representação de cordas equivalente que é codificada com dígitos base-64.

**Sintaxe:**  
`str ConvertToBase64(str source)`

**Exemplo:**  
`ConvertToBase64("Hello world!")`  
Devoluções "SABlAGwAbABvACAAdwBvAHIAbABKACEA"

---
### <a name="converttoutf8hex"></a>ConvertToUTF8Hex
**Description:**  
A função ConvertToUTF8Hex converte uma cadeia para um valor codificado utf8 Hex.

**Sintaxe:**  
`str ConvertToUTF8Hex(str source)`

**Observações:**  
O formato de saída desta função é utilizado pelo Azure Ative Directory como formato de atributo DN.

**Exemplo:**  
`ConvertToUTF8Hex("Hello world!")`  
Devoluções 48656C6C6F20776F726C6421

---
### <a name="count"></a>de palavras
**Description:**  
A função Contagem devolve o número de elementos num atributo multi-valor

**Sintaxe:**  
`num Count(mvstr attribute)`

---
### <a name="cstr"></a>CStr
**Description:**  
A função CStr converte-se num tipo de dados de cadeia.

**Sintaxe:**  
`str CStr(num value)`  
`str CStr(ref value)`  
`str CStr(bool value)`  

* valor: Pode ser um valor numérico, atributo de referência ou Boolean.

**Exemplo:**  
`CStr([dn])`  
Poderia devolver "cn=Joe,dc=contoso,dc=com"

---
### <a name="datefromnum"></a>DataFromNum
**Description:**  
A função DataFromNum converte um valor no formato de data da AD para um tipo DateTime.

**Sintaxe:**  
`dt DateFromNum(num value)`

**Exemplo:**  
`DateFromNum([lastLogonTimestamp])`  
`DateFromNum(129699324000000000)`  
Devoluções de uma DataTime representando 2012-01-01 23:00:00

---
### <a name="dncomponent"></a>DNComponente
**Description:**  
A função DNComponent devolve o valor de um componente DN especificado que vai da esquerda.

**Sintaxe:**  
`str DNComponent(ref dn, num ComponentNumber)`

* dn: o atributo de referência para interpretar
* ComponentNumber: O componente no DN para devolver

**Exemplo:**  
`DNComponent(CRef([dn]),1)`  
Se dn é "cn=Joe,ou=...", devolve Joe

---
### <a name="error"></a>Erro
**Description:**  
A função Erro é utilizada para retornar um erro personalizado.

**Sintaxe:**  
`void Error(str ErrorMessage)`

**Exemplo:**  
`IIF(IsPresent([accountName]),[accountName],Error("AccountName is required"))`  
Se a conta de atributosName não estiver presente, atire um erro no objeto.

---
### <a name="formatdatetime"></a>FormatDatime
**Função:**<br> FormatoDateTime (fonte, inputFormat, outputFormat)

**Description:**<br> Pega numa cadeia de datas de um formato e converte-a num formato diferente.

**Parâmetros:**<br> 

   | Nome | Obrigatório/ Repetição | Tipo | Notas |
   | --- | --- | --- | --- |
   | **fonte** |Necessário |String |Normalmente o nome do atributo do objeto de origem. |
   | **inputFormat** |Necessário |String |Formato esperado do valor da origem. Para formatos suportados, consulte [/dotnet/standard/base-tipos/custom-date-and-time-format-strings](/dotnet/standard/base-types/custom-date-and-time-format-strings). |
   | **outputFormat** |Necessário |String |Formato da data de saída. |

---
### <a name="guid"></a>GUID
**Description:**  
A função Guid gera um novo GUID aleatório

**Sintaxe:**  
`str Guid()`

---
### <a name="iif"></a>IIF
**Description:**  
A função IIF devolve um de um conjunto de valores possíveis com base numa condição especificada.

**Sintaxe:**  
`var IIF(exp condition, var valueIfTrue, var valueIfFalse)`

* condição: qualquer valor ou expressão que possa ser avaliado para verdadeiro ou falso.
* valueIfTrue: Se a condição avaliar a verdade, o valor devolvido.
* valueIfFalse: Se a condição avaliar a falsa, o valor devolvido.

**Exemplo:**  
`IIF([employeeType]="Intern","t-" & [alias],[alias])`  
 Se o utilizador for um estagiário, devolve o pseudónimo de um utilizador com "t-" adicionado ao início do mesmo, caso contrário, devolve o pseudónimo do utilizador como está.

---
### <a name="instr"></a>InStr
**Description:**  
A função InStr encontra a primeira ocorrência de um sublaming numa corda

**Sintaxe:**  

`num InStr(str stringcheck, str stringmatch)`  
`num InStr(str stringcheck, str stringmatch, num start)`  
`num InStr(str stringcheck, str stringmatch, num start, enum compare)`

* stringcheck: cadeia a ser pesquisada
* stringmatch: corda a ser encontrada
* início: posição inicial para encontrar o sub-cording
* comparar: vbTextCompare ou vbBinaryCompare

**Observações:**  
Devolve a posição onde o sublamento foi encontrado ou 0 se não for encontrado.

**Exemplo:**  
`InStr("The quick brown fox","quick")`  
Evalências a 5

`InStr("repEated","e",3,vbBinaryCompare)`  
Avalia a 7

---
### <a name="isnull"></a>Isnull
**Description:**  
Se a expressão avaliar a Nulo, então a função IsNull retorna verdadeiramente.

**Sintaxe:**  
`bool IsNull(var Expression)`

**Observações:**  
Para um atributo, um Nulo é expresso pela ausência do atributo.

**Exemplo:**  
`IsNull([displayName])`  
Devoluções Verdadeiras se o atributo não estiver presente no CS ou MV.

---
### <a name="isnullorempty"></a>IsNullOrEmpty
**Description:**  
Se a expressão for nula ou uma corda vazia, então a função IsNullOrEmpty retorna verdadeiramente.

**Sintaxe:**  
`bool IsNullOrEmpty(var Expression)`

**Observações:**  
Para um atributo, isto avaliaria a True se o atributo está ausente ou está presente, mas é uma corda vazia.  
O inverso desta função chama-se IsPresent.

**Exemplo:**  
`IsNullOrEmpty([displayName])`  
Devoluções Verdadeiras se o atributo não estiver presente ou se for uma corda vazia no CS ou MV.

---
### <a name="ispresent"></a>IsPresent
**Description:**  
Se a expressão avaliar a uma cadeia que não é nula e não está vazia, então a função IsPresent retorna verdadeiramente.

**Sintaxe:**  
`bool IsPresent(var expression)`

**Observações:**  
O inverso desta função chama-se IsNullOrEmpty.

**Exemplo:**  
`Switch(IsPresent([directManager]),[directManager], IsPresent([skiplevelManager]),[skiplevelManager], IsPresent([director]),[director])`

---
### <a name="item"></a>Item
**Description:**  
A função Item devolve um item de uma cadeia/atributo multi-valor.

**Sintaxe:**  
`var Item(mvstr attribute, num index)`

* atributo: atributo multi-valor
* índice: índice para um item na cadeia multi-valor.

**Observações:**  
A função 'Item' é útil juntamente com a função Conter, uma vez que esta última função devolve o índice a um item no atributo multi-valor.

Lança um erro se o índice estiver fora dos limites.

**Exemplo:**  
`Mid(Item([proxyAddresses],Contains([proxyAddresses], "SMTP:")),6)`  
Devolve o endereço de e-mail primário.

---
### <a name="isstring"></a>IsString
**Description:**  
Se a expressão pode ser avaliada para um tipo de corda, então a função IsString avalia para True.

**Sintaxe:**  
`bool IsString(var expression)`

**Observações:**  
Usado para determinar se o CStr() pode ser bem sucedido para analisar a expressão.

---
### <a name="join"></a>Associar
**Função:**<br> Junte-se(separador, fonte1, fonte2, ...)

**Description:**<br> A junção é semelhante ao Apêndice,), exceto que pode combinar múltiplos valores de cadeia de **origem** numa única corda, e cada valor será separado por uma corda **separador.**

Se um dos valores de origem for um atributo de vários valores, então todos os valores nesse atributo serão unidos, separados pelo valor separador.

**Parâmetros:**<br> 

   | Nome | Obrigatório/ Repetição | Tipo | Notas |
   | --- | --- | --- | --- |
   | **separador** |Necessário |String |A corda usada para separar os valores de origem quando são concatenadas numa única corda. Pode ser "" se não for necessário um separador. |
   | **fonte1 ... fonteN** |Obrigatório, número variável de vezes |String |Valores de cordas a serem unidos. |

---
### <a name="left"></a>Esquerda
**Description:**  
A função Esquerda retorna um número especificado de caracteres à esquerda de uma corda.

**Sintaxe:**  
`str Left(str string, num NumChars)`

* corda: a cadeia para devolver os caracteres de
* NumChars: um número que identifica o número de caracteres para regressar desde o início (à esquerda) da cadeia

**Observações:**  
Uma corda contendo os primeiros caracteres numChars em corda:

* Se numChars = 0, devolva a corda vazia.
* Se os numChars < 0, devolva a cadeia de entrada.
* Se a corda for nula, devolva a corda vazia.

Se a cadeia contiver menos caracteres do que o número especificado em numChars, é devolvida uma corda idêntica à corda (isto é, contendo todos os caracteres no parâmetro 1).

**Exemplo:**  
`Left("John Doe", 3)`  
Devoluções `Joh` .

---
### <a name="mid"></a>Mid
**Função:**<br> Médio (fonte, início, comprimento)

**Description:**<br> Devolve um sub-cordão do valor de origem. Um sub-cordão é uma cadeia que contém apenas alguns dos caracteres da cadeia de origem.

**Parâmetros:**<br> 

   | Nome | Obrigatório/ Repetição | Tipo | Notas |
   | --- | --- | --- | --- |
   | **fonte** |Necessário |String |Normalmente o nome do atributo. |
   | **começar** |Necessário |número inteiro |Índice na cadeia **de origem** onde o sublpeso deve começar. O primeiro personagem da cadeia terá índice de 1, o segundo personagem terá índice 2, e assim por diante. |
   | **length** |Necessário |número inteiro |Comprimento do sub-esforço. Se o comprimento terminar fora da cadeia **de origem,** a função retornará o sublamamento do índice inicial até ao fim da cadeia de  **origem.** |

---
### <a name="normalizediacritics"></a>NormalizarDiacráticos
**Função:**<br> Normalizar OsDiacritics(fonte)

**Description:**<br> Requer um argumento de corda. Devolve a corda, mas com caracteres diacríticos substituídos por caracteres não diacríticos equivalentes. Normalmente usado para converter nomes e apelidos contendo caracteres diacríticos (marcas de sotaque) em valores legais que podem ser usados em vários identificadores de utilizador, tais como nomes principais de utilizador, nomes de contas SAM e endereços de e-mail.

**Parâmetros:**<br> 

   | Nome | Obrigatório/ Repetição | Tipo | Notas |
   | --- | --- | --- | --- |
   | **fonte** |Necessário |String | Normalmente um primeiro nome ou atributo de apelido. |

---
### <a name="not"></a>Não
**Função:**<br> Não(fonte)

**Description:**<br> Inverte o valor booleano da **fonte**. Se o valor **da fonte** for "*Verdadeiro*", retorna "*Falso*". Caso contrário, retorna "*Verdadeiro*".

**Parâmetros:**<br> 

   | Nome | Obrigatório/ Repetição | Tipo | Notas |
   | --- | --- | --- | --- |
   | **fonte** |Necessário |Corda Booleana |Os valores de **origem** esperados são "Verdadeiro" ou "Falso". |

---
### <a name="removeduplicates"></a>Removeduplicates
**Description:**  
A função RemoveDuplicates requer uma corda multi-valorizada e certifique-se de que cada valor é único.

**Sintaxe:**  
`mvstr RemoveDuplicates(mvstr attribute)`

**Exemplo:**  
`RemoveDuplicates([proxyAddresses])`  
Devolve um atributo proxyAddress higienizado onde todos os valores duplicados foram removidos.

---
### <a name="replace"></a>Substituir
**Função:**<br> Substituir (fonte, oldValue, regexPattern, regexGroupName, replacementValue, replacementAttributeName, modelo)

**Description:**<br>
Substitui valores dentro de uma corda. Funciona de forma diferente dependendo dos parâmetros fornecidos:

* Quando **o antigoValue** e **a substituiçãoValue** forem fornecidos:
  
  * Substitui todas as ocorrências de **oldValue** na **fonte**  por **substituiçãoValue**
* Quando **o velhoValue** e **o modelo** são fornecidos:
  
  * Substitui todas as ocorrências do **antigo Valor** no **modelo** pelo valor de **origem**
* Quando **regexPattern** e **substituiçãoValue** são fornecidos:

  * A função aplica o **regexPattern** à cadeia **de origem** e pode usar os nomes do grupo Regex para construir a cadeia para **substituiçãoValue**
* Quando **regexPattern,** **regexGroupName,** **substituiçãoValue** são fornecidos:
  
  * A função aplica o **regexPattern** à cadeia **de origem** e substitui todos os valores correspondentes **ao RegexGroupName** com **substituiçãoValue**
* Quando **regexPattern,** **regexGroupName,** **substituiçãoAttributeName:**
  
  * Se **a fonte** não tiver valor, a **fonte** é devolvida
  * Se **a fonte** tiver um valor, a função aplica o **regexPattern** à cadeia **de origem** e substitui todos os valores correspondentes ao **RegexGroupName** com o valor associado à **substituiçãoAttributeName**

**Parâmetros:**<br> 

   | Nome | Obrigatório/ Repetição | Tipo | Notas |
   | --- | --- | --- | --- |
   | **fonte** |Necessário |String |Normalmente o nome do atributo do objeto **de origem.** |
   | **oldValue** |Opcional |String |Valor a ser substituído na **fonte** ou **no modelo.** |
   | **regexPattern** |Opcional |String |Padrão Regex para que o valor seja substituído na **fonte**. Ou, quando é utilizado **o Nome Deproperty substituto,** padrão para extrair valor do **substitutoPropertyName**. |
   | **regexGroupName** |Opcional |String |Nome do grupo dentro **da regexPattern**. Só quando for utilizado  **o Nome Deperperty** Substituto, extrairemos valor deste grupo como **substitutoValue** da **substituiçãoPropertyName**. |
   | **substituiçãoValue** |Opcional |String |Novo valor para substituir o antigo por. |
   | **substituiçãoAttributeName** |Opcional |String |Nome do atributo a utilizar para valor de substituição |
   | **modelo** |Opcional |String |Quando o valor **do modelo** for fornecido, procuraremos o **velhoValue** dentro do modelo e substituí-lo-emos pelo valor **de origem.** |

---
### <a name="selectuniquevalue"></a>SelecioneUniqueValue
**Função:**<br> SelectUniqueValue (uniqueValueRule1, uniqueValueRule2, uniqueValueRule3, ...)

**Description:**<br> Requer um mínimo de dois argumentos, que são regras únicas de geração de valor definidas usando expressões. A função avalia cada regra e, em seguida, verifica o valor gerado para a singularidade na aplicação/diretório-alvo. O primeiro valor único encontrado será o devolvido. Se todos os valores já existirem no alvo, a entrada será escrocada e a razão será registada nos registos de auditoria. Não há limite superior ao número de argumentos que podem ser apresentados.

> [!NOTE]
> - Esta é uma função de alto nível, não pode ser aninhada.
> - Esta função não pode ser aplicada a atributos que tenham precedência correspondente.  
> - Esta função destina-se apenas a ser utilizada para criações de entrada. Ao usá-lo com um atributo, desaje a propriedade **Apply Mapping** para Apenas durante a **criação de objetos**.
> - Atualmente, esta função é suportada apenas para "Workday to Ative Directory User Provisioning". Não pode ser utilizado com outros pedidos de provisionamento. 


**Parâmetros:**<br> 

   | Nome | Obrigatório/ Repetição | Tipo | Notas |
   | --- | --- | --- | --- |
   | **únicoValueRule1 ... únicoValueRuleN** |Pelo menos 2 são necessários, sem limite superior |String | Lista de regras únicas de geração de valor para avaliar. |


---
### <a name="singleapproleassignment"></a>Assinatura SingleAppRoleAssignment
**Função:**<br> SingleAppRoleAssignment ([appRoleAssignments])

**Description:**<br> Devolve um único apRoleAssignment da lista de todos os pedidos de apreciados atribuídos a um utilizador para uma determinada aplicação. Esta função é necessária para converter o objeto appRoleAssignments numa única cadeia de nomes de função. Note que a melhor prática é garantir que apenas uma assinatura de appRoleAssignment é atribuída a um utilizador de cada vez, e se várias funções forem atribuídas a cadeia de função devolvida pode não ser previsível. 

**Parâmetros:**<br> 

  | Nome | Obrigatório/ Repetição | Tipo | Notas |
  |--- | --- | --- | --- |
  | **[appRoleAssignments]** |Necessário |String |**[appRoleAssignments]** objeto. |

---
### <a name="split"></a>Dividir
**Função:**<br> Divisão (fonte, delimiter)

**Description:**<br> Divide uma corda numa matriz multi-valorizada, utilizando o carácter delimiter especificado.

**Parâmetros:**<br> 

   | Nome | Obrigatório/ Repetição | Tipo | Notas |
   | --- | --- | --- | --- |
   | **fonte** |Necessário |String |valor **de origem** para atualizar. |
   | **delimiter** |Necessário |String |Especifica o carácter que será usado para dividir a corda (exemplo: "") |

---
### <a name="stringfromsid"></a>StringFromSid
**Description:**  
A função StringFromSid converte uma matriz de byte contendo um identificador de segurança a uma corda.

**Sintaxe:**  
`str StringFromSid(bin ObjectSID)`  

---
### <a name="stripspaces"></a>StripSpaces
**Função:**<br> StripSpaces (fonte)

**Description:**<br> Remove todos os caracteres do espaço (" ") da cadeia de origem.

**Parâmetros:**<br> 

   | Nome | Obrigatório/ Repetição | Tipo | Notas |
   | --- | --- | --- | --- |
   | **fonte** |Necessário |String |valor **de origem** para atualizar. |

---
### <a name="switch"></a>Comutador
**Função:**<br> Switch (fonte, defaultValue, key1, valor1, key2, valor2, ...)

**Description:**<br> Quando o valor **de origem** corresponde a uma **chave,** devolve **valor** a essa **chave**. Se o valor **da fonte** não corresponder a nenhuma tecla, retorna o **padrãoValue**.  **Os** parâmetros de chave e **valor** devem sempre vir em pares. A função espera sempre um número par de parâmetros.

**Parâmetros:**<br> 

   | Nome | Obrigatório/ Repetição | Tipo | Notas |
   | --- | --- | --- | --- |
   | **fonte** |Necessário |String |**Valor de origem** para verificar. |
   | **padrãoValue** |Opcional |String |Valor predefinido a ser utilizado quando a fonte não corresponde a nenhuma tecla. Pode ser corda vazia (""). |
   | **chave** |Necessário |String |**Chave** para comparar o valor **da fonte** com. |
   | **value** |Necessário |String |Valor de substituição da **fonte** correspondente à chave. |

---
### <a name="tolower"></a>ToLower
**Função:**<br> ToLower (fonte, cultura)

**Description:**<br> Pega num valor de cadeia *de origem* e converte-o em minúsculas usando as regras de cultura especificadas. Se não houver informação sobre *cultura* especificada, então usará a cultura Invariante.

**Parâmetros:**<br> 

   | Nome | Obrigatório/ Repetição | Tipo | Notas |
   | --- | --- | --- | --- |
   | **fonte** |Necessário |String |Normalmente o nome do atributo a partir do objeto de origem |
   | **cultura** |Opcional |String |O formato para o nome da cultura baseado no RFC 4646 é *languagecode2-country/regioncode2,* onde o *languagecode2* é o código linguístico de duas letras e *o código de duas letras* e o código de duas letras e o código de duas letras. Exemplos incluem ja-JP para japonês (Japão) e en-US para inglês (Estados Unidos). Nos casos em que não esteja disponível um código linguístico de duas letras, é utilizado um código de três letras derivado da ISO 639-2.|

---

### <a name="toupper"></a>Toupper
**Função:**<br> ToUpper (fonte, cultura)

**Description:**<br> Pega num valor de cadeia *de origem* e converte-o em maiúsão utilizando as regras de cultura especificadas. Se não houver informação sobre *cultura* especificada, então usará a cultura Invariante.

**Parâmetros:**<br> 

  | Nome | Obrigatório/ Repetição | Tipo | Notas |
  | --- | --- | --- | --- |
  | **fonte** |Necessário |String |Normalmente o nome do atributo do objeto de origem. |
  | **cultura** |Opcional |String |O formato para o nome da cultura baseado no RFC 4646 é *languagecode2-country/regioncode2,* onde o *languagecode2* é o código linguístico de duas letras e *o código de duas letras* e o código de duas letras e o código de duas letras. Exemplos incluem ja-JP para japonês (Japão) e en-US para inglês (Estados Unidos). Nos casos em que não esteja disponível um código linguístico de duas letras, é utilizado um código de três letras derivado da ISO 639-2.|

---

### <a name="trim"></a>Trim
**Description:**  
A função Trim remove espaços brancos de condução e de fuga de uma corda.

**Sintaxe:**  
`str Trim(str value)`  

**Exemplo:**  
`Trim(" Test ")`  
Devolve "Teste".

`Trim([proxyAddresses])`  
Remove espaços de liderança e de fuga para cada valor no atributo ProxyAddress.

---
### <a name="word"></a>Word
**Description:**  
A função Word devolve uma palavra contida dentro de uma cadeia, com base em parâmetros que descrevem os delimitadores a utilizar e o número da palavra para devolver.

**Sintaxe:**  
`str Word(str string, num WordNumber, str delimiters)`

* corda: a corda para devolver uma palavra de.
* WordNumber: um número que identifica qual o número de palavra que deve ser retornante.
* delimiters: uma corda que representa o(s) delimiter(s) que deve ser usado para identificar palavras

**Observações:**  
Cada cadeia de caracteres em corda separada por um dos caracteres em delimiters são identificadas como palavras:

* Se o número < 1, retorna a corda vazia.
* Se a corda for nula, devolve a corda vazia.

Se a corda contiver menos de palavras numeradas, ou a corda não conter quaisquer palavras identificadas por delimiters, uma corda vazia é devolvida.

**Exemplo:**  
`Word("The quick brown fox",3," ")`  
Devoluções "castanhas"

`Word("This,string!has&many separators",3,",!&#")`  
Voltaria "tem"

## <a name="examples"></a>Exemplos
### <a name="strip-known-domain-name"></a>Strip nome de domínio conhecido
É necessário retirar um nome de domínio conhecido do e-mail de um utilizador para obter um nome de utilizador. <br>
Por exemplo, se o domínio for "contoso.com", então pode utilizar a seguinte expressão:

**Expressão:** <br>
`Replace([mail], "@contoso.com", , ,"", ,)`

**Entrada/saída da amostra:** <br>

* **ENTRADA** (correio): john.doe@contoso.com "
* **OUTPUT**: "john.doe"

### <a name="append-constant-suffix-to-user-name"></a>Sufixo constante do apêndice ao nome de utilizador
Se estiver a utilizar uma Caixa de Areia Salesforce, poderá ter de anexar um sufixo adicional a todos os seus nomes de utilizador antes de os sincronizar.

**Expressão:** <br>
`Append([userPrincipalName], ".test")`

**Entrada/saída da amostra:** <br>

* **ENTRADA**: (userPrincipalName): John.Doe@contoso.com " "
* **OUTPUT**: " John.Doe@contoso.com.test "

### <a name="generate-user-alias-by-concatenating-parts-of-first-and-last-name"></a>Gerar pseudónimos de utilizador ao concatenar partes do primeiro e último nome
É necessário gerar um pseudónimo de utilizador tomando as primeiras 3 letras do primeiro nome do utilizador e as primeiras 5 letras do apelido do utilizador.

**Expressão:** <br>
`Append(Mid([givenName], 1, 3), Mid([surname], 1, 5))`

**Entrada/saída da amostra:** <br>

* **INPUT** (dado Nome): "João"
* **ENTRADA** (sobrenome): "Doe"
* **OUTPUT**: "JohDoe"

### <a name="remove-diacritics-from-a-string"></a>Remova diacríticos de uma corda
É necessário substituir caracteres que contenham marcas de sotaque por caracteres equivalentes que não contenham marcas de sotaque.

**Expressão:** <br>
Normalizar OsDiacritics ([dado o nome])

**Entrada/saída da amostra:** <br>

* **INPUT** (dado Natal): "Zoë"
* **OUTPUT**: "Zoe"

### <a name="split-a-string-into-a-multi-valued-array"></a>Divida uma corda numa matriz multi-valorizada
É necessário pegar numa lista de cordas delimitada por vírgulas e dividi-las numa matriz que pode ser ligada a um atributo de vários valores como o atributo Permissões de Permissões da Salesforce. Neste exemplo, uma lista de conjuntos de permissões foi povoada em extensãoAtribute5 em Azure AD.

**Expressão:** <br>
Split ([extensãoAttribute5], "")

**Entrada/saída da amostra:** <br>

* **ENTRADA** (extensãoAttribute5): "PermissionSetOne, PermissionSetTwo"
* **OUTPUT**: ["PermissionSetOne", "PermissionSetTwo"]

### <a name="output-date-as-a-string-in-a-certain-format"></a>Data de saída como uma corda em um determinado formato
Pretende enviar datas para uma aplicação SaaS num determinado formato. <br>
Por exemplo, pretende-se que as datas de formato para o ServiceNow.

**Expressão:** <br>

`FormatDateTime([extensionAttribute1], "yyyyMMddHHmmss.fZ", "yyyy-MM-dd")`

**Entrada/saída da amostra:**

* **ENTRADA** (extensãoAttribute1): "20150123105347.1Z"
* **SAÍDA**: "2015-01-23"

### <a name="replace-a-value-based-on-predefined-set-of-options"></a>Substitua um valor baseado no conjunto de opções predefinido

É necessário definir o fuso horário do utilizador com base no código de estado armazenado no Azure AD. <br>
Se o código de estado não corresponder a nenhuma das opções predefinidas, utilize o valor predefinido de "Austrália/Sydney".

**Expressão:** <br>
`Switch([state], "Australia/Sydney", "NSW", "Australia/Sydney","QLD", "Australia/Brisbane", "SA", "Australia/Adelaide")`

**Entrada/saída da amostra:**

* **ENTRADA** (estado): "QLD"
* **OUTPUT**: "Australia/Brisbane"

### <a name="replace-characters-using-a-regular-expression"></a>Substitua os caracteres usando uma expressão regular
É necessário encontrar caracteres que correspondam a um valor de expressão regular e removam-nos.

**Expressão:** <br>

Substituir([mailNickname], "[a-zA-Z_]*", ","

**Entrada/saída da amostra:**

* **ENTRADA** (mailDámeo: "john_doe72"
* **SAÍDA**: "72"

### <a name="convert-generated-userprincipalname-upn-value-to-lower-case"></a>Converter o valor do utilizador geradoPrincipalName (UPN) para minúsculas
No exemplo abaixo, o valor UPN é gerado através da concatenação dos campos de origem PreferredFirstName e PreferredLastName e a função ToLower opera na cadeia gerada para converter todos os caracteres em minúsculas. 

`ToLower(Join("@", NormalizeDiacritics(StripSpaces(Join(".",  [PreferredFirstName], [PreferredLastName]))), "contoso.com"))`

**Entrada/saída da amostra:**

* **INPUT** (Nome Preferencial De Primeira): "John"
* **INPUT** (Nome Preferido do Natal): "Smith"
* **OUTPUT**: " john.smith@contoso.com "

### <a name="generate-unique-value-for-userprincipalname-upn-attribute"></a>Gerar valor único para o atributo UserPrincipalName (UPN)
Com base no nome do utilizador, nome do meio e apelido, é necessário gerar um valor para o atributo UPN e verificar a sua singularidade no diretório de AD alvo antes de atribuir o valor ao atributo UPN.

**Expressão:** <br>

```ad-attr-mapping-expr
    SelectUniqueValue( 
        Join("@", NormalizeDiacritics(StripSpaces(Join(".",  [PreferredFirstName], [PreferredLastName]))), "contoso.com"), 
        Join("@", NormalizeDiacritics(StripSpaces(Join(".",  Mid([PreferredFirstName], 1, 1), [PreferredLastName]))), "contoso.com"),
        Join("@", NormalizeDiacritics(StripSpaces(Join(".",  Mid([PreferredFirstName], 1, 2), [PreferredLastName]))), "contoso.com")
    )
```

**Entrada/saída da amostra:**

* **INPUT** (Nome Preferencial De Primeira): "John"
* **INPUT** (Nome Preferido do Natal): "Smith"
* **OUTPUT**: " John.Smith@contoso.com " se o valor upn de já não existe no John.Smith@contoso.com diretório
* **OUTPUT**: " J.Smith@contoso.com " se o valor UPN já existe no John.Smith@contoso.com diretório
* **OUTPUT**: " Jo.Smith@contoso.com " se os dois valores acima da UPN já existem no diretório


## <a name="next-steps"></a>Passos seguintes 

- [O que é o aprovisionamento?](what-is-provisioning.md)
- [O que é a sincronização de nuvem AZure AD Connect?](what-is-cloud-sync.md)