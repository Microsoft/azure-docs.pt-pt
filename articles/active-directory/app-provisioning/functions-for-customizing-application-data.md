---
title: Escreva expressões para mapeamentos de atributos no Azure Ative Directory
description: Aprenda a usar mapeamentos de expressão para transformar valores de atributos num formato aceitável durante o fornecimento automatizado de objetos de aplicações SaaS no Azure Ative Directory.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: how-to
ms.date: 02/05/2020
ms.author: kenwith
ms.openlocfilehash: d454ab3ad382c6237ab9f8c215473801285ba3c9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88235677"
---
# <a name="how-to-write-expressions-for-attribute-mappings-in-azure-ad"></a>Como fazer: Escrever expressões para mapeamentos de atributos em Azure AD

Ao configurar o fornecimento de uma aplicação SaaS, um dos tipos de mapeamentos de atributos que pode especificar é um mapeamento de expressão. Para estes, tem de escrever uma expressão semelhante a um script que lhe permita transformar os dados dos seus utilizadores em formatos mais aceitáveis para a aplicação SaaS.

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
* A sintaxe é sensível ao caso, que deve ser considerada enquanto os datilografa como cordas numa função vs cópia colando-as diretamente a partir daqui. 

## <a name="list-of-functions"></a>Lista de Funções

[Apêndice](#append) &nbsp; &nbsp; &nbsp; &nbsp; [BitAnd](#bitand) &nbsp; &nbsp; BitAnd &nbsp; &nbsp; [CBool](#cbool) &nbsp; &nbsp; &nbsp; &nbsp; [Coalesce](#coalesce) &nbsp; &nbsp; &nbsp; &nbsp; [ConvertToBase64](#converttobase64) &nbsp; &nbsp; &nbsp; &nbsp; [ConvertToUTF8Hex](#converttoutf8hex) &nbsp; &nbsp; &nbsp; &nbsp; [Count](#count) &nbsp; &nbsp; Contagem &nbsp; &nbsp; [CStr](#cstr) &nbsp; &nbsp; CStr &nbsp; &nbsp; [DataFromNum](#datefromnum) &nbsp; [FormatDatime](#formatdatetime) &nbsp; &nbsp; &nbsp; &nbsp; [Guid](#guid) &nbsp; &nbsp; Guiar &nbsp; &nbsp; [IIF](#iif) &nbsp; &nbsp; IIF &nbsp; &nbsp; [InStr](#instr) &nbsp; &nbsp; InStr &nbsp; &nbsp; [Isnull](#isnull) &nbsp; &nbsp; &nbsp; &nbsp; [IsNullOrEmpty](#isnullorempty) &nbsp; &nbsp; &nbsp; &nbsp; [IsPresent](#ispresent) &nbsp; &nbsp; IsPresent &nbsp; &nbsp; [IsString](#isstring) &nbsp; &nbsp; &nbsp; &nbsp; [Item](#item) &nbsp; &nbsp; Item &nbsp; &nbsp; [Junte-se](#join) &nbsp; &nbsp; &nbsp; &nbsp; [Left](#left) &nbsp; &nbsp; Esquerda &nbsp; &nbsp; [Mid](#mid) &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; [NormalizeDiacritics](#normalizediacritics) [Not](#not) &nbsp; &nbsp; &nbsp; &nbsp; [RemoveDuplicates](#removeduplicates) &nbsp; &nbsp; &nbsp; &nbsp; [Replace](#replace) &nbsp; &nbsp; &nbsp; &nbsp; [SelectUniqueValue](#selectuniquevalue) &nbsp; &nbsp; &nbsp; &nbsp; [SingleAppRoleAssignment](#singleapproleassignment) &nbsp; &nbsp; &nbsp; &nbsp; [Split](#split) &nbsp; &nbsp; &nbsp; &nbsp; [StripSpaces](#stripspaces) &nbsp; &nbsp; &nbsp; &nbsp; [Switch](#switch) &nbsp; &nbsp; &nbsp; &nbsp; [ToLower](#tolower) &nbsp; &nbsp; &nbsp; &nbsp; [ToUpper](#toupper) &nbsp; &nbsp; &nbsp; &nbsp; [Word](#word)

---
### <a name="append"></a>Acrescentar

**Função:**<br> Apêndice (fonte, sufixo)

**Description:**<br> Pega num valor de cadeia de origem e apê o sufixo até ao fim.

**Parâmetros:**<br> 

| Nome | Obrigatório/ Repetição | Tipo | Notas |
| --- | --- | --- | --- |
| **fonte** |Necessário |Cadeia |Normalmente o nome do atributo do objeto de origem. |
| **sufixo** |Necessário |Cadeia |A corda que quer anexar ao fim do valor de origem. |

---
### <a name="bitand"></a>BitAnd
**Função:**<br> BitAnd (valor1, valor2)

**Description:**<br> Esta função converte ambos os parâmetros para a representação binária e define um pouco para:

0 - se uma ou ambas as bits correspondentes no valor1 e no valor2 forem 0                                                  
1 - se ambas as partes correspondentes forem 1.                                    

Por outras palavras, devolve 0 em todos os casos, exceto quando os pedaços correspondentes de ambos os parâmetros são 1.

**Parâmetros:**<br> 

| Nome | Obrigatório/ Repetição | Tipo | Notas |
| --- | --- | --- | --- |
| **valor1** |Necessário |num |Valor numérico que deve ser e'ed com valor2|
| **valor2** |Necessário |num |Valor numérico que deve ser e'ed com valor1|

**Exemplo:**<br>
BitAnd (&HF, &HF7)                                                                                
11110111 E 00000011 = 00000111 para que o BitAnd devolva 7, o valor binário de 00000111

---
### <a name="cbool"></a>CBool
**Função:**<br> CBool(Expressão)

**Description:**<br> CBool devolve um boolean com base na expressão avaliada. Se a expressão avalia um valor não-zero, então cBool devolve True, caso contrário retorna Falso..

**Parâmetros:**<br> 

| Nome | Obrigatório/ Repetição | Tipo | Notas |
| --- | --- | --- | --- |
| **expression** |Necessário | expression | Qualquer expressão válida |

**Exemplo:**<br>
CBool([atributo1] = [atributo2])                                                                    
Devoluções Verdadeiras se ambos os atributos tiverem o mesmo valor.

---
### <a name="coalesce"></a>Coalesce
**Função:**<br> Coalesce (fonte1, fonte2, ..., defaultValue)

**Description:**<br> Devolve o primeiro valor de origem que não é NULO. Se todos os argumentos forem NULOS e o padrãoValue estiver presente, o padrãoValue será devolvido. Se todos os argumentos forem NULOS e o incumprimentoValue não estiver presente, a Coalesce devolve a NU.

**Parâmetros:**<br> 

| Nome | Obrigatório/ Repetição | Tipo | Notas |
| --- | --- | --- | --- |
| **fonte1 ... fonteN** | Necessário | Cadeia |Obrigatório, número variável de vezes. Normalmente o nome do atributo do objeto de origem. |
| **padrãoValue** | Opcional | Cadeia | Valor predefinido a ser utilizado quando todos os valores de origem são NULOS. Pode ser corda vazia ("").

---
### <a name="converttobase64"></a>ConvertToBase64
**Função:**<br> ConvertToBase64 (fonte)

**Description:**<br> A função ConvertToBase64 converte uma corda numa cadeia base Unicode64.

**Parâmetros:**<br> 

| Nome | Obrigatório/ Repetição | Tipo | Notas |
| --- | --- | --- | --- |
| **fonte** |Necessário |Cadeia |Corda a converter para base 64|

**Exemplo:**<br>
ConvertToBase64 ("Olá mundo!")                                                                                                        
Devoluções "SABlAGwAbABvACAAdwBvAHIAbABKACEA"

---
### <a name="converttoutf8hex"></a>ConvertToUTF8Hex
**Função:**<br> ConvertToUTF8Hex (fonte)

**Description:**<br> A função ConvertToUTF8Hex converte uma cadeia para um valor codificado utf8 Hex.

**Parâmetros:**<br> 

| Nome | Obrigatório/ Repetição | Tipo | Notas |
| --- | --- | --- | --- |
| **fonte** |Necessário |Cadeia |Corda a converter para UTF8 Hex|

**Exemplo:**<br>
ConvertToUTF8Hex ("Olá mundo!")                                                                                                         
Devoluções 48656C6C6F20776F726C6421

---
### <a name="count"></a>Contagem
**Função:**<br> Contagem (atributo)

**Description:**<br> A função Contagem devolve o número de elementos num atributo multi-valor

**Parâmetros:**<br> 

| Nome | Obrigatório/ Repetição | Tipo | Notas |
| --- | --- | --- | --- |
| **atributo** |Necessário |atributo |Atributo multi-valor que terá elementos contados|

---
### <a name="cstr"></a>CStr
**Função:**<br> CStr (valor)

**Description:**<br> A função CStr converte um valor para um tipo de dados de cadeia.

**Parâmetros:**<br> 

| Nome | Obrigatório/ Repetição | Tipo | Notas |
| --- | --- | --- | --- |
| **value** |Necessário | numérico, referência ou booleano | Pode ser um valor numérico, atributo de referência, ou Boolean. |

**Exemplo:**<br>
CStr([dn])                                                            
Devoluções "cn=Joe,dc=contoso,dc=com"

---
### <a name="datefromnum"></a>DataFromNum
**Função:**<br> DataFromNum (valor)

**Description:**<br> A função DataFromNum converte um valor no formato de data da AD para um tipo DateTime.

**Parâmetros:**<br> 

| Nome | Obrigatório/ Repetição | Tipo | Notas |
| --- | --- | --- | --- |
| **value** |Necessário | Date | Data AD a converter para o tipo DateTime |

**Exemplo:**<br>
DataFromNum ([lastLogonTimestamp])                                                                                                   
DataSFromNum(1296993240000000000)                                                            
Devoluções de uma DataTime representando 2012-01-01 23:00:00

---
### <a name="formatdatetime"></a>FormatDatime
**Função:**<br> FormatoDateTime (fonte, inputFormat, outputFormat)

**Description:**<br> Pega numa cadeia de datas de um formato e converte-a num formato diferente.

**Parâmetros:**<br> 

| Nome | Obrigatório/ Repetição | Tipo | Notas |
| --- | --- | --- | --- |
| **fonte** |Necessário |Cadeia |Normalmente o nome do atributo do objeto de origem. |
| **inputFormat** |Necessário |Cadeia |Formato esperado do valor da origem. Para formatos suportados, consulte [/dotnet/standard/base-tipos/custom-date-and-time-format-strings](/dotnet/standard/base-types/custom-date-and-time-format-strings). |
| **outputFormat** |Necessário |Cadeia |Formato da data de saída. |

---
### <a name="guid"></a>GUID
**Função:**<br> Guid()

**Description:**<br> A função Guid gera um novo GUID aleatório

---
### <a name="iif"></a>IIF
**Função:**<br> IIF (condição,valueIfTrue,valueIfFalse)

**Description:**<br> A função IIF devolve um de um conjunto de valores possíveis com base numa condição especificada.

**Parâmetros:**<br> 

| Nome | Obrigatório/ Repetição | Tipo | Notas |
| --- | --- | --- | --- |
| **condição** |Necessário |Variável ou Expressão |Qualquer valor ou expressão que possa ser avaliado para verdadeiro ou falso. |
| **valorIfTrue** |Necessário |Variável ou Corda | Se a condição avaliar a verdade, o valor devolvido. |
| **valorIfFalse** |Necessário |Variável ou Corda |Se a condição avaliar o falso, o valor devolvido.|

**Exemplo:**<br>
IIF([país]="USA",[país],[departamento])

---
### <a name="instr"></a>InStr
**Função:**<br> InStr (valor1,valor2,start,compareType)

**Description:**<br> A função InStr encontra a primeira ocorrência de um sublaming numa corda

**Parâmetros:**<br> 

| Nome | Obrigatório/ Repetição | Tipo | Notas |
| --- | --- | --- | --- |
| **valor1** |Necessário |Cadeia |Corda a ser revistada |
| **valor2** |Necessário |Cadeia |Corda a ser encontrada |
| **iniciar** |Opcional |Número inteiro |Posição inicial para encontrar o sub-cordão|
| **compararType** |Opcional |Enumeração |Pode ser vbTextCompare ou vbBinaryCompare |

**Exemplo:**<br>
InStr ("A raposa marrom rápida", "rápido")                                                                             
Evalências a 5

InStr("repEated", "e"3,vbBinaryCompare)                                                                                  
Avalia a 7

---
### <a name="isnull"></a>Isnull
**Função:**<br> Isnull(Expressão)

**Description:**<br> Se a expressão avaliar a Nulo, então a função IsNull retorna verdadeiramente. Para um atributo, um Nulo é expresso pela ausência do atributo.

**Parâmetros:**<br> 

| Nome | Obrigatório/ Repetição | Tipo | Notas |
| --- | --- | --- | --- |
| **expression** |Necessário |expression |Expressão a avaliar |

**Exemplo:**<br>
IsNull ([displayName])                                                                                                
Devoluções Verdadeiras se o atributo não estiver presente

---
### <a name="isnullorempty"></a>IsnullorEmpty
**Função:**<br> IsNullOrEmpty (Expressão)

**Description:**<br> Se a expressão for nula ou uma corda vazia, então a função IsNullOrEmpty retorna verdadeiramente. Para um atributo, isto avaliaria a True se o atributo está ausente ou está presente, mas é uma corda vazia.
O inverso desta função chama-se IsPresent.

**Parâmetros:**<br> 

| Nome | Obrigatório/ Repetição | Tipo | Notas |
| --- | --- | --- | --- |
| **expression** |Necessário |expression |Expressão a avaliar |

**Exemplo:**<br>
IsNullOrEmpty ([displayName])                                               
Devoluções Verdadeiras se o atributo não estiver presente ou for uma corda vazia

---
### <a name="ispresent"></a>IsPresent
**Função:**<br> IsPresent (Expressão)

**Description:**<br> Se a expressão avaliar a uma cadeia que não é nula e não está vazia, então a função IsPresent retorna verdadeiramente. O inverso desta função chama-se IsNullOrEmpty.

**Parâmetros:**<br> 

| Nome | Obrigatório/ Repetição | Tipo | Notas |
| --- | --- | --- | --- |
| **expression** |Necessário |expression |Expressão a avaliar |

**Exemplo:**<br>
Switch(IsPresent[directManager][directManager], IsPresent[skiplevelManager],[skiplevelManager], IsPresent[diretor])

---
### <a name="isstring"></a>IsString
**Função:**<br> IsString (Expressão)

**Description:**<br> Se a expressão pode ser avaliada para um tipo de corda, então a função IsString avalia para True.

**Parâmetros:**<br> 

| Nome | Obrigatório/ Repetição | Tipo | Notas |
| --- | --- | --- | --- |
| **expression** |Necessário |expression |Expressão a avaliar |

---
### <a name="item"></a>Item
**Função:**<br> Item (atributo, índice)

**Description:**<br> A função Item devolve um item de uma cadeia/atributo multi-valor.

**Parâmetros:**<br> 

| Nome | Obrigatório/ Repetição | Tipo | Notas |
| --- | --- | --- | --- |
| **atributo** |Necessário |Atributo |Atributo multi-valor a ser pesquisado |
| **índice** |Necessário |Número inteiro | Índice para um item na cadeia multi-valorizada|

**Exemplo:**<br>
Item ([proxyAddresses], 1)

---
### <a name="join"></a>Associar
**Função:**<br> Junte-se(separador, fonte1, fonte2, ...)

**Description:**<br> A junção é semelhante ao Apêndice,), exceto que pode combinar múltiplos valores de cadeia de **origem** numa única corda, e cada valor será separado por uma corda **separador.**

Se um dos valores de origem for um atributo de vários valores, então todos os valores nesse atributo serão unidos, separados pelo valor separador.

**Parâmetros:**<br> 

| Nome | Obrigatório/ Repetição | Tipo | Notas |
| --- | --- | --- | --- |
| **separador** |Necessário |Cadeia |A corda usada para separar os valores de origem quando são concatenadas numa única corda. Pode ser "" se não for necessário um separador. |
| **fonte1 ... fonteN** |Obrigatório, número variável de vezes |Cadeia |Valores de cordas a serem unidos. |

---
### <a name="left"></a>Esquerda
**Função:**<br> Esquerda (String,NumChars)

**Description:**<br> A função Esquerda retorna um número especificado de caracteres à esquerda de uma corda. Se numChars = 0, devolva a corda vazia.
Se os numChars < 0, devolva a cadeia de entrada.
Se a corda for nula, devolva a corda vazia.
Se a cadeia contiver menos caracteres do que o número especificado em numChars, é devolvida uma corda idêntica à corda (isto é, contendo todos os caracteres no parâmetro 1).

**Parâmetros:**<br> 

| Nome | Obrigatório/ Repetição | Tipo | Notas |
| --- | --- | --- | --- |
| **String** |Necessário |Atributo | A corda para devolver os personagens de |
| **NumChars** |Necessário |Número inteiro | Um número que identifica o número de caracteres para voltar desde o início (à esquerda) da corda|

**Exemplo:**<br>
Esquerda ("João Ninguém", 3)                                                            
Devoluções "Joh"

---
### <a name="mid"></a>Mid
**Função:**<br> Médio (fonte, início, comprimento)

**Description:**<br> Devolve um sub-cordão do valor de origem. Um sub-cordão é uma cadeia que contém apenas alguns dos caracteres da cadeia de origem.

**Parâmetros:**<br> 

| Nome | Obrigatório/ Repetição | Tipo | Notas |
| --- | --- | --- | --- |
| **fonte** |Necessário |Cadeia |Normalmente o nome do atributo. |
| **iniciar** |Necessário |número inteiro |Índice na cadeia **de origem** onde o sublpeso deve começar. O primeiro personagem da cadeia terá índice de 1, o segundo personagem terá índice 2, e assim por diante. |
| **length** |Necessário |número inteiro |Comprimento do sub-esforço. Se o comprimento terminar fora da cadeia **de origem,** a função retornará o sublamamento do índice inicial até ao fim da cadeia de **start** **origem.** |

---
### <a name="normalizediacritics"></a>NormalizarDiacráticos
**Função:**<br> Normalizar OsDiacritics(fonte)

**Description:**<br> Requer um argumento de corda. Devolve a corda, mas com caracteres diacríticos substituídos por caracteres não diacríticos equivalentes. Normalmente usado para converter nomes e apelidos contendo caracteres diacríticos (marcas de sotaque) em valores legais que podem ser usados em vários identificadores de utilizador, tais como nomes principais de utilizador, nomes de contas SAM e endereços de e-mail.

**Parâmetros:**<br> 

| Nome | Obrigatório/ Repetição | Tipo | Notas |
| --- | --- | --- | --- |
| **fonte** |Necessário |Cadeia | Normalmente um primeiro nome ou atributo de apelido. |

---
### <a name="not"></a>Não
**Função:**<br> Não(fonte)

**Description:**<br> Inverte o valor booleano da **fonte**. Se o valor **da fonte** for "*Verdadeiro*", retorna "*Falso*". Caso contrário, retorna "*Verdadeiro*".

**Parâmetros:**<br> 

| Nome | Obrigatório/ Repetição | Tipo | Notas |
| --- | --- | --- | --- |
| **fonte** |Necessário |Corda Booleana |Os valores de **origem** esperados são "Verdadeiro" ou "Falso". |

---
### <a name="numfromdate"></a>NumFromDate
**Função:**<br> NumFromDate (valor)

**Description:**<br> A função NumFromDate converte um valor DateTime para formato Ative Directory que é necessário para definir atributos como [contaExpires](/windows/win32/adschema/a-accountexpires). Utilize esta função para converter os valores do DateTime recebidos de aplicações de RH em nuvem como Workday e SuccessFactors para a sua representação equivalente em AD. 

**Parâmetros:**<br> 

| Nome | Obrigatório/ Repetição | Tipo | Notas |
| --- | --- | --- | --- |
| **value** |Necessário | Cadeia | Cadeia de tempo de data no formato suportado. Para formatos suportados, consulte https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx . |

**Exemplo:**<br>
* Exemplo do dia de trabalho <br>
  Assumindo que pretende mapear o atributo *ContractEndDate* do Workday que está no formato *2020-12-31-08:00* para *contaREsté aD,* eis como pode usar esta função e alterar o offset de tempo para combinar com o local. 
  `NumFromDate(Join("", FormatDateTime([ContractEndDate], "yyyy-MM-ddzzz", "yyyy-MM-dd"), "T23:59:59-08:00"))`

* Sucesso Exemplo deFactfactors <br>
  Assumindo que pretende mapear o *atributo endDate* de SuccessFactors que está no formato *M/d/yyyy hh:mm:mmt* para *contaRExpires* campo em AD, eis como pode usar esta função e alterar o fuso horário para combinar com o seu local.
  `NumFromDate(Join("",FormatDateTime([endDate],"M/d/yyyy hh:mm:ss tt","yyyy-MM-dd"),"T23:59:59-08:00"))`


---
### <a name="removeduplicates"></a>Removeduplicates
**Função:**<br> RemoveDuplicates (atributo)

**Description:**<br> A função RemoveDuplicates requer uma corda multi-valorizada e certifique-se de que cada valor é único.

**Parâmetros:**<br> 

| Nome | Obrigatório/ Repetição | Tipo | Notas |
| --- | --- | --- | --- |
| **atributo** |Necessário |Atributo multi-valor |Atributo multi-valor que terá duplicados removidos|

**Exemplo:**<br>
Removeduplicates ([proxyAddresses])                                                                                                       
Devolve um atributo proxyAddress higienizado onde todos os valores duplicados foram removidos

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
| **fonte** |Necessário |Cadeia |Normalmente o nome do atributo do objeto **de origem.** |
| **oldValue** |Opcional |Cadeia |Valor a ser substituído na **fonte** ou **no modelo.** |
| **regexPattern** |Opcional |Cadeia |Padrão Regex para que o valor seja substituído na **fonte**. Ou, quando é utilizado **o Nome Deproperty substituto,** padrão para extrair valor do **substitutoPropertyName**. |
| **regexGroupName** |Opcional |Cadeia |Nome do grupo dentro **da regexPattern**. Só quando for utilizado  **o Nome Deperperty** Substituto, extrairemos valor deste grupo como **substitutoValue** da **substituiçãoPropertyName**. |
| **substituiçãoValue** |Opcional |Cadeia |Novo valor para substituir o antigo por. |
| **substituiçãoAttributeName** |Opcional |Cadeia |Nome do atributo a utilizar para valor de substituição |
| **modelo** |Opcional |Cadeia |Quando o valor **do modelo** for fornecido, procuraremos o **velhoValue** dentro do modelo e substituí-lo-emos pelo valor **de origem.** |

---
### <a name="selectuniquevalue"></a>SelecioneUniqueValue
**Função:**<br> SelectUniqueValue (uniqueValueRule1, uniqueValueRule2, uniqueValueRule3, ...)

**Description:**<br> Requer um mínimo de dois argumentos, que são regras únicas de geração de valor definidas usando expressões. A função avalia cada regra e, em seguida, verifica o valor gerado para a singularidade na aplicação/diretório-alvo. O primeiro valor único encontrado será o devolvido. Se todos os valores já existirem no alvo, a entrada será escrocada e a razão será registada nos registos de auditoria. Não há limite superior ao número de argumentos que podem ser apresentados.


 - Esta é uma função de alto nível, não pode ser aninhada.
 - Esta função não pode ser aplicada a atributos que tenham precedência correspondente.   
 - Esta função destina-se apenas a ser utilizada para criações de entrada. Ao usá-lo com um atributo, desaje a propriedade **Apply Mapping** para Apenas durante a **criação de objetos**.
 - Atualmente, esta função é suportada apenas para "Workday to Ative Directory User Provisioning". Não pode ser utilizado com outros pedidos de provisionamento. 


**Parâmetros:**<br> 

| Nome | Obrigatório/ Repetição | Tipo | Notas |
| --- | --- | --- | --- |
| **únicoValueRule1 ... únicoValueRuleN** |Pelo menos 2 são necessários, sem limite superior |Cadeia | Lista de regras únicas de geração de valor para avaliar. |


---
### <a name="singleapproleassignment"></a>Assinatura SingleAppRoleAssignment
**Função:**<br> SingleAppRoleAssignment ([appRoleAssignments])

**Description:**<br> Devolve um único apRoleAssignment da lista de todos os pedidos de apreciados atribuídos a um utilizador para uma determinada aplicação. Esta função é necessária para converter o objeto appRoleAssignments numa única cadeia de nomes de função. Note que a melhor prática é garantir que apenas uma assinatura de appRoleAssignment é atribuída a um utilizador de cada vez, e se várias funções forem atribuídas a cadeia de função devolvida pode não ser previsível. 

**Parâmetros:**<br> 

| Nome | Obrigatório/ Repetição | Tipo | Notas |
| --- | --- | --- | --- |
| **[appRoleAssignments]** |Necessário |Cadeia |**[appRoleAssignments]** objeto. |

---
### <a name="split"></a>Dividir
**Função:**<br> Divisão (fonte, delimiter)

**Description:**<br> Divide uma corda numa matriz multi-valorizada, utilizando o carácter delimiter especificado.

**Parâmetros:**<br> 

| Nome | Obrigatório/ Repetição | Tipo | Notas |
| --- | --- | --- | --- |
| **fonte** |Necessário |Cadeia |valor **de origem** para atualizar. |
| **delimitador** |Necessário |Cadeia |Especifica o carácter que será usado para dividir a corda (exemplo: "") |

---
### <a name="stripspaces"></a>StripSpaces
**Função:**<br> StripSpaces (fonte)

**Description:**<br> Remove todos os caracteres do espaço (" ") da cadeia de origem.

**Parâmetros:**<br> 

| Nome | Obrigatório/ Repetição | Tipo | Notas |
| --- | --- | --- | --- |
| **fonte** |Necessário |Cadeia |valor **de origem** para atualizar. |

---
### <a name="switch"></a>Comutador
**Função:**<br> Switch (fonte, defaultValue, key1, valor1, key2, valor2, ...)

**Description:**<br> Quando o valor **de origem** corresponde a uma **chave,** devolve **valor** a essa **chave**. Se o valor **da fonte** não corresponder a nenhuma tecla, retorna o **padrãoValue**.  **Os** parâmetros de chave e **valor** devem sempre vir em pares. A função espera sempre um número par de parâmetros. A função não deve ser utilizada para atributos referenciais, como o gestor. 

**Parâmetros:**<br> 

| Nome | Obrigatório/ Repetição | Tipo | Notas |
| --- | --- | --- | --- |
| **fonte** |Necessário |Cadeia |**Valor de origem** para atualizar. |
| **padrãoValue** |Opcional |Cadeia |Valor predefinido a ser utilizado quando a fonte não corresponde a nenhuma tecla. Pode ser corda vazia (""). |
| **chave** |Necessário |Cadeia |**Chave** para comparar o valor **da fonte** com. |
| **value** |Necessário |Cadeia |Valor de substituição da **fonte** correspondente à chave. |

---
### <a name="tolower"></a>ToLower
**Função:**<br> ToLower (fonte, cultura)

**Description:**<br> Pega num valor de cadeia *de origem* e converte-o em minúsculas usando as regras de cultura especificadas. Se não houver informação sobre *cultura* especificada, então usará a cultura Invariante.

**Parâmetros:**<br> 

| Nome | Obrigatório/ Repetição | Tipo | Notas |
| --- | --- | --- | --- |
| **fonte** |Necessário |Cadeia |Normalmente o nome do atributo a partir do objeto de origem |
| **cultura** |Opcional |Cadeia |O formato para o nome da cultura baseado no RFC 4646 é *languagecode2-country/regioncode2,* onde o *languagecode2* é o código linguístico de duas letras e *o código de duas letras* e o código de duas letras e o código de duas letras. Exemplos incluem ja-JP para japonês (Japão) e en-US para inglês (Estados Unidos). Nos casos em que não esteja disponível um código linguístico de duas letras, é utilizado um código de três letras derivado da ISO 639-2.|

---
### <a name="toupper"></a>Toupper
**Função:**<br> ToUpper (fonte, cultura)

**Description:**<br> Pega num valor de cadeia *de origem* e converte-o em maiúsão utilizando as regras de cultura especificadas. Se não houver informação sobre *cultura* especificada, então usará a cultura Invariante.

**Parâmetros:**<br> 

| Nome | Obrigatório/ Repetição | Tipo | Notas |
| --- | --- | --- | --- |
| **fonte** |Necessário |Cadeia |Normalmente o nome do atributo do objeto de origem. |
| **cultura** |Opcional |Cadeia |O formato para o nome da cultura baseado no RFC 4646 é *languagecode2-country/regioncode2,* onde o *languagecode2* é o código linguístico de duas letras e *o código de duas letras* e o código de duas letras e o código de duas letras. Exemplos incluem ja-JP para japonês (Japão) e en-US para inglês (Estados Unidos). Nos casos em que não esteja disponível um código linguístico de duas letras, é utilizado um código de três letras derivado da ISO 639-2.|

---
### <a name="word"></a>Word
**Função:**<br> Palavra (String,WordNumber,Delimiters)

**Description:**<br> A função Word devolve uma palavra contida dentro de uma cadeia, com base em parâmetros que descrevem os delimitadores a utilizar e o número da palavra para devolver. Cada cadeia de caracteres em corda separada por um dos caracteres em delimiters são identificadas como palavras:

Se o número < 1, retorna a corda vazia.
Se a corda for nula, devolve a corda vazia.
Se a corda contiver menos de palavras numeradas, ou a corda não conter quaisquer palavras identificadas por delimiters, uma corda vazia é devolvida.

**Parâmetros:**<br> 

| Nome | Obrigatório/ Repetição | Tipo | Notas |
| --- | --- | --- | --- |
| **String** |Necessário |Atributo multi-valor |String para devolver uma palavra de.|
| **WordNumber** |Necessário | Número inteiro | Número que identifica qual número de palavra deve devolver|
| **delimiters** |Necessário |Cadeia| Uma corda que representa o(s)limiter(s) que deve ser usado para identificar palavras|

**Exemplo:**<br>
Palavra ("A raposa marrom rápida",3,")                                                                                       
Devoluções "castanhas"

Word ("This,string!has&many separadores","3,"!&#")                                                                       
Devoluções "tem"

---

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

* **ENTRADA** (extensãoAttribute5): "PermissionSetOne, PermisionSetTwo"
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

### <a name="flow-mail-value-if-not-null-otherwise-flow-userprincipalname"></a>Valor do correio de fluxo se não NULL, de outra forma fluxo utilizadorName
Deseja fluir o atributo de correio se estiver presente. Caso contrário, deseja fluir o valor do nome do utilizadorPrincipal.

**Expressão:** <br>
`Coalesce([mail],[userPrincipalName])`

**Entrada/saída da amostra:** <br>

* **ENTRADA** (correio): NULO
* **ENTRADA** (userPrincipalName): John.Doe@contoso.com " "
* **OUTPUT**: " John.Doe@contoso.com "

## <a name="related-articles"></a>Artigos Relacionados
* [Automatizar o Provisionamento/Desprovisionamento do Utilizador para aplicações SaaS](../app-provisioning/user-provisioning.md)
* [Personalizar mapeamentos de atributos para provisionamento de utilizadores](../app-provisioning/customize-application-attributes.md)
* [Filtros de deteção para o provisionamento do utilizador](define-conditional-rules-for-provisioning-user-accounts.md)
* [Utilizar o SCIM para ativar o aprovisionamento automático de utilizadores e grupos do Azure Active Directory a aplicações](../app-provisioning/use-scim-to-provision-users-and-groups.md)
* [Notificações do Aprovisionamento de Contas](../app-provisioning/user-provisioning.md)
* [Lista de Tutoriais sobre Como Integrar Aplicações SaaS](../saas-apps/tutorial-list.md)