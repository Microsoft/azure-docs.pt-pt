---
title: Escreva expressões para mapeamentos de atributos no Diretório Ativo Azure
description: Aprenda a usar mapeamentos de expressão para transformar valores de atributos num formato aceitável durante o fornecimento automatizado de objetos de aplicações SaaS no Diretório Ativo Azure.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: conceptual
ms.date: 02/05/2020
ms.author: mimart
ms.openlocfilehash: c8573f9151ac59178b19bbf354da43990405b3e0
ms.sourcegitcommit: 3abadafcff7f28a83a3462b7630ee3d1e3189a0e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/30/2020
ms.locfileid: "82593697"
---
# <a name="how-to-write-expressions-for-attribute-mappings-in-azure-ad"></a>Como: Escrever expressões para mapeamento de atributos em Azure AD

Ao configurar o fornecimento a uma aplicação SaaS, um dos tipos de mapeamento de atributos que pode especificar é um mapeamento de expressão. Para estes, deve escrever uma expressão semelhante a um guião que lhe permita transformar os dados dos seus utilizadores em formatos mais aceitáveis para a aplicação SaaS.

## <a name="syntax-overview"></a>Visão geral da sintaxe

A sintaxe para expressões para mapeamento de atributos faz lembrar as funções de Base Visual para Aplicações (VBA).

* Toda a expressão deve ser definida em termos de funções, que consistem num nome seguido de argumentos em parênteses: <br>
  *Nome de`<<argument 1>>``<<argument N>>`funções,*
* Podem nidificar funções dentro umas das outras. Por exemplo: <br> *Functionone (FunçãoDois)`<<argument1>>`*
* Pode passar três tipos diferentes de argumentos em funções:
  
  1. Atributos, que devem ser fechados em suportes quadrados. Por exemplo: [nome de atributo]
  2. Constantes de cordas, que devem ser fechadas em citações duplas. Por exemplo: "Estados Unidos"
  3. Outras Funções. Por exemplo: FunctionOne,`<<argument1>>`FunctionTwo.`<<argument2>>`
* Para as constantes de cordas, se precisar de um backslash ( \ ) ou de uma marca de citação ( " ) na corda, deve ser escapado com o símbolo de backslash (\). Por exemplo: "Nome \\da\\empresa: "Contoso".

## <a name="list-of-functions"></a>Lista de Funções

[Append](#append) &nbsp; [Join](#join) [Count](#count) &nbsp; &nbsp; [Guid](#guid) &nbsp; [Left](#left) [Not](#not) [Mid](#mid) [Replace](#replace) [BitAnd](#bitand) &nbsp; &nbsp; &nbsp; &nbsp; [CStr](#cstr) &nbsp; &nbsp; &nbsp; [InStr](#instr) &nbsp; [DateFromNum](#datefromnum) &nbsp; &nbsp; [IIF](#iif) &nbsp; [IsNull](#isnull) &nbsp; [Item](#item) &nbsp; [FormatDateTime](#formatdatetime) &nbsp; &nbsp; [CBool](#cbool) &nbsp; &nbsp; &nbsp; &nbsp; [Coalesce](#coalesce) &nbsp; &nbsp; &nbsp; [SelectUniqueValue](#selectuniquevalue) [IsString](#isstring) &nbsp; [IsNullOrEmpty](#isnullorempty) &nbsp; [RemoveDuplicates](#removeduplicates) [IsPresent](#ispresent) &nbsp; [NormalizeDiacritics](#normalizediacritics) [ConvertToBase64](#converttobase64) &nbsp; &nbsp; &nbsp; &nbsp; [ConvertToUTF8Hex](#converttoutf8hex) &nbsp; &nbsp; &nbsp; &nbsp; Apêndice BitAnd &nbsp; &nbsp; &nbsp; &nbsp; CBool &nbsp; &nbsp; &nbsp; Coalesce &nbsp; &nbsp; &nbsp; ConverttoBase64 &nbsp; &nbsp; &nbsp; ConvertToUTF8Hex &nbsp; &nbsp; Count &nbsp; CStr &nbsp; DateFromTime &nbsp; Guid &nbsp; &nbsp; IIF &nbsp; InStr &nbsp; IsNull &nbsp; OrEmpty &nbsp; IsPresent IsPresent IsString &nbsp; Item Join &nbsp; Left Mid &nbsp; NormalizeDiacritics Not RemoveDuplicates Replace SelectUniqueValue &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; [Switch](#switch) [Split](#split)&nbsp; [Word](#word) [StripSpaces](#stripspaces) &nbsp; [SingleAppRoleAssignment](#singleapproleassignment) &nbsp; &nbsp; &nbsp; [ToUpper](#toupper) [ToLower](#tolower) Singleapproleassignment &nbsp; &nbsp; split &nbsp; &nbsp; stripspaces&nbsp; switch&nbsp; tolower&nbsp; toupper word&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;&nbsp;&nbsp;&nbsp; &nbsp; &nbsp; &nbsp;

---
### <a name="append"></a>Acrescentar

**Função:**<br> Apêndice (fonte, sufixo)

**Description:**<br> Pega num valor de cadeia de origem e afixa o sufixo até ao fim.

**Parâmetros:**<br> 

| Name | Obrigatório/ Repetição | Tipo | Notas |
| --- | --- | --- | --- |
| **fonte** |Necessário |String |Normalmente, o nome do atributo do objeto de origem. |
| **sufixo** |Necessário |String |A corda que quer anexar até ao fim do valor de origem. |

---
### <a name="bitand"></a>Bitand
**Função:**<br> BitAnd (valor 1, valor 2)

**Description:**<br> Esta função converte ambos os parâmetros para a representação binária e define um pouco para:

0 - se uma ou ambas as partes correspondentes no valor 1 e no valor2 forem 0                                                  
1 - se ambas as partes correspondentes forem 1.                                    

Por outras palavras, retorna a 0 em todos os casos, exceto quando os bits correspondentes de ambos os parâmetros são 1.

**Parâmetros:**<br> 

| Name | Obrigatório/ Repetição | Tipo | Notas |
| --- | --- | --- | --- |
| **valor1** |Necessário |num |Valor numérico que deve ser E'ed com valor2|
| **valor2** |Necessário |num |Valor numérico que deve ser E'ed com valor1|

**Exemplo:**<br>
BitAnd (&HF, &HF7)                                                                                
11110111 E 00000111 = 00000111 por isso bitAnd devolve 7, o valor binário de 000001111

---
### <a name="cbool"></a>CBool
**Função:**<br> CBool (Expressão)

**Description:**<br> CBool devolve uma booleana com base na expressão avaliada. Se a expressão avaliar para um valor não zero, então CBool devolve True, caso contrário devolve Falso.

**Parâmetros:**<br> 

| Name | Obrigatório/ Repetição | Tipo | Notas |
| --- | --- | --- | --- |
| **expressão** |Necessário | expression | Qualquer expressão válida |

**Exemplo:**<br>
CBool =[atributo2])                                                                    
Devoluções Verdadeiras se ambos os atributos tiverem o mesmo valor.

---
### <a name="coalesce"></a>Coalesce
**Função:**<br> Coalesce (fonte1, fonte2, ..., valor padrão)

**Description:**<br> Devolve o primeiro valor de origem que não é NULO. Se todos os argumentos forem NULOs e o defaultValue estiver presente, o valor padrãoValue será devolvido. Se todos os argumentos forem NULOS e o defaultValue não estiver presente, coalesce devolve NULL.

**Parâmetros:**<br> 

| Name | Obrigatório/ Repetição | Tipo | Notas |
| --- | --- | --- | --- |
| **fonte1 ... fonteN** | Necessário | String |Obrigatório, número variável de vezes. Normalmente, o nome do atributo do objeto de origem. |
| **defaultValor** | Opcional | String | Valor predefinido a utilizar quando todos os valores de origem são NULOS. Pode ser corda vazia ("").

---
### <a name="converttobase64"></a>ConverttoBase64
**Função:**<br> ConvertToBase64 (fonte)

**Description:**<br> A função ConvertToBase64 converte uma cadeia para uma cadeia Unicode base64.

**Parâmetros:**<br> 

| Name | Obrigatório/ Repetição | Tipo | Notas |
| --- | --- | --- | --- |
| **fonte** |Necessário |String |Corda a ser convertida para base 64|

**Exemplo:**<br>
ConvertToBase64 ("Olá mundo!")                                                                                                        
Devoluções "SABlAGwAbABvACAAdwBvAHIAbABkACEA"

---
### <a name="converttoutf8hex"></a>ConvertToUTF8Hex
**Função:**<br> ConvertToUTF8Hex (fonte)

**Description:**<br> A função ConvertToUTF8Hex converte uma cadeia para um valor codificado uTF8 Hex.

**Parâmetros:**<br> 

| Name | Obrigatório/ Repetição | Tipo | Notas |
| --- | --- | --- | --- |
| **fonte** |Necessário |String |Corda a ser convertida em UTF8 Hex|

**Exemplo:**<br>
ConvertToUTF8Hex ("Olá mundo!")                                                                                                         
Devoluções 48656C6C6F20776F726C6421

---
### <a name="count"></a>Contagem
**Função:**<br> Contagem (atributo)

**Description:**<br> A função Count devolve o número de elementos num atributo multivalorizado

**Parâmetros:**<br> 

| Name | Obrigatório/ Repetição | Tipo | Notas |
| --- | --- | --- | --- |
| **atributo** |Necessário |atributo |Atributo multivalorizado que terá elementos contados|

---
### <a name="cstr"></a>CStr
**Função:**<br> CStr (valor)

**Description:**<br> A função CStr converte um valor para um tipo de dados de cordas.

**Parâmetros:**<br> 

| Name | Obrigatório/ Repetição | Tipo | Notas |
| --- | --- | --- | --- |
| **valor** |Necessário | numérico, referência ou booleano | Pode ser um valor numérico, atributo de referência, ou Boolean. |

**Exemplo:**<br>
CStr([dn])                                                            
Devoluções "cn=Joe,dc=contoso,dc=com"

---
### <a name="datefromnum"></a>DataFromnum
**Função:**<br> DataFromNum (valor)

**Description:**<br> A função DateFromNum converte um valor no formato de data da AD para um tipo DateTime.

**Parâmetros:**<br> 

| Name | Obrigatório/ Repetição | Tipo | Notas |
| --- | --- | --- | --- |
| **valor** |Necessário | Date | Data AD a converter para o tipo DateTime |

**Exemplo:**<br>
DateFromNum([lastLogonTimestamp])                                                                                                   
DataFromnum(129699324000000000000000000000000)                                                            
Devoluções DataTime representando 2012-01-01 23:00:00

---
### <a name="formatdatetime"></a>FormatoDateTime
**Função:**<br> FormatoDateTime (fonte, inputFormat, outputFormat)

**Description:**<br> Pega numa corda de data de um formato e converte-a num formato diferente.

**Parâmetros:**<br> 

| Name | Obrigatório/ Repetição | Tipo | Notas |
| --- | --- | --- | --- |
| **fonte** |Necessário |String |Normalmente, o nome do atributo do objeto de origem. |
| **inputFormat** |Necessário |String |Formato esperado do valor de origem. Para formatos suportados, consulte [https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx). |
| **outputFormat** |Necessário |String |Formato da data de saída. |

---
### <a name="guid"></a>GUID
**Função:**<br> Guia()

**Description:**<br> A função Guid gera um novo GUID aleatório

---
### <a name="iif"></a>IIF
**Função:**<br> IIF (condição,valueIfTrue,valueIfFalse)

**Description:**<br> A função IIF devolve um conjunto de valores possíveis com base numa condição especificada.

**Parâmetros:**<br> 

| Name | Obrigatório/ Repetição | Tipo | Notas |
| --- | --- | --- | --- |
| **condição** |Necessário |Variável ou Expressão |Qualquer valor ou expressão que possa ser avaliado a verdade ou falsa. |
| **valorIfTrue** |Necessário |Variável ou Corda | Se a condição avaliar a verdade, o valor devolvido. |
| **valorIfFalso** |Necessário |Variável ou Corda |Se a condição avaliar a falsa, o valor devolvido.|

**Exemplo:**<br>
IIF[[país]="EUA,"[country],[department])

---
### <a name="instr"></a>Instr
**Função:**<br> InStr (valor1,valor2,início,compareType)

**Description:**<br> A função InStr encontra a primeira ocorrência de uma substring numa corda

**Parâmetros:**<br> 

| Name | Obrigatório/ Repetição | Tipo | Notas |
| --- | --- | --- | --- |
| **valor1** |Necessário |String |Corda a ser revistada |
| **valor2** |Necessário |String |Corda a ser encontrada |
| **começar** |Opcional |Número inteiro |Posição inicial para encontrar a subcadeia|
| **compararType** |Opcional |Enum |Pode ser vbTextCompare ou vbBinaryCompare |

**Exemplo:**<br>
InStr("A raposa marrom rápida", "rápido")                                                                             
Valores e valores para 5

InStr ("repEated", "e,"3,vbBinaryCompare)                                                                                  
Avalia para 7

---
### <a name="isnull"></a>Isnull
**Função:**<br> Isnull (Expressão)

**Description:**<br> Se a expressão avaliar a Nula, então a função IsNull retorna verdadeira. Para um atributo, um Nulo é expresso pela ausência do atributo.

**Parâmetros:**<br> 

| Name | Obrigatório/ Repetição | Tipo | Notas |
| --- | --- | --- | --- |
| **expressão** |Necessário |expression |Expressão a avaliar |

**Exemplo:**<br>
IsNull([nome do ecrã])                                                                                                
Devoluções Verdadeirase se o atributo não estiver presente

---
### <a name="isnullorempty"></a>IsNullorEmpty
**Função:**<br> IsnullorEmpty (Expressão)

**Description:**<br> Se a expressão for nula ou uma corda vazia, então a função IsNullOrEmpty retorna verdadeira. Para um atributo, isto avaliaria a True se o atributo está ausente ou está presente, mas é uma corda vazia.
O inverso desta função chama-se IsPresent.

**Parâmetros:**<br> 

| Name | Obrigatório/ Repetição | Tipo | Notas |
| --- | --- | --- | --- |
| **expressão** |Necessário |expression |Expressão a avaliar |

**Exemplo:**<br>
IsNullOrEmpty([nome do ecrã])                                               
Devoluções Verdadeirase se o atributo não estiver presente ou se for uma corda vazia

---
### <a name="ispresent"></a>IsPresent
**Função:**<br> IsPresent (Expressão)

**Description:**<br> Se a expressão avaliar para uma cadeia que não é Nula e não está vazia, então a função IsPresent retorna verdadeira. O inverso desta função chama-se IsNullOrEmpty.

**Parâmetros:**<br> 

| Name | Obrigatório/ Repetição | Tipo | Notas |
| --- | --- | --- | --- |
| **expressão** |Necessário |expression |Expressão a avaliar |

**Exemplo:**<br>
Switch(IsPresent([directManager]),[directManager], IsPresent([skiplevelManager],[skiplevelManager], IsPresent[[diretor]]]]

---
### <a name="isstring"></a>IsString
**Função:**<br> Isstring (Expressão)

**Description:**<br> Se a expressão pode ser avaliada para um tipo de corda, então a função IsString avalia para True.

**Parâmetros:**<br> 

| Name | Obrigatório/ Repetição | Tipo | Notas |
| --- | --- | --- | --- |
| **expressão** |Necessário |expression |Expressão a avaliar |

---
### <a name="item"></a>Item
**Função:**<br> Item (atributo, índice)

**Description:**<br> A função Item devolve um item de uma cadeia/atributo de vários valores.

**Parâmetros:**<br> 

| Name | Obrigatório/ Repetição | Tipo | Notas |
| --- | --- | --- | --- |
| **atributo** |Necessário |Atributo |Atributo multivalorizado a ser pesquisado |
| **índice** |Necessário |Número inteiro | Index a um item na cadeia multi-valorizado|

**Exemplo:**<br>
Item([proxyAddresss], 1)

---
### <a name="join"></a>Associar
**Função:**<br> Aderir (separador, fonte1, fonte2, ...)

**Description:**<br> A adesão() é semelhante ao apêndice, exceto que pode combinar múltiplos valores de cadeia de **origem** numa única corda, e cada valor será separado por uma corda **de separador.**

Se um dos valores de origem for um atributo de vários valores, então cada valor nesse atributo será unido, separado pelo valor do separador.

**Parâmetros:**<br> 

| Name | Obrigatório/ Repetição | Tipo | Notas |
| --- | --- | --- | --- |
| **separador** |Necessário |String |Cordas usadas para separar os valores de origem quando são concatenadas numa só corda. Pode ser "" se não for necessário separador. |
| **fonte1 ... fonteN** |Obrigatório, número variável de vezes |String |Valores de cordas a unir. |

---
### <a name="left"></a>Esquerda
**Função:**<br> Esquerda (String,NumChars)

**Description:**<br> A função Esquerda devolve um número especificado de caracteres da esquerda de uma corda. Se numChars = 0, volte a ficar vazio.
Se numChars < 0, volte a ser corda de entrada.
Se a corda for nula, volte a ficar vazia.
Se a corda contiver menos caracteres do que o número especificado em numChars, uma corda idêntica à corda (isto é, contendo todos os caracteres no parâmetro 1) é devolvida.

**Parâmetros:**<br> 

| Name | Obrigatório/ Repetição | Tipo | Notas |
| --- | --- | --- | --- |
| **String** |Necessário |Atributo | A corda para devolver personagens de |
| **Numchars** |Necessário |Número inteiro | Um número que identifica o número de caracteres a regressar do início (à esquerda) da corda|

**Exemplo:**<br>
Esquerda("João Ninguém", 3)                                                            
Devoluções "Joh"

---
### <a name="mid"></a>Mid
**Função:**<br> Médio (fonte, início, comprimento)

**Description:**<br> Devolve uma subcadeia do valor de origem. Uma subcadeia é uma cadeia que contém apenas alguns dos caracteres da cadeia de origem.

**Parâmetros:**<br> 

| Name | Obrigatório/ Repetição | Tipo | Notas |
| --- | --- | --- | --- |
| **fonte** |Necessário |String |Normalmente o nome do atributo. |
| **começar** |Necessário |número inteiro |Índice na cadeia **de origem** onde deve iniciar a subcadeia. O primeiro personagem na cadeia terá índice de 1, segundo personagem terá índice 2, e assim por diante. |
| **comprimento** |Necessário |número inteiro |Comprimento da subcorda. Se o comprimento terminar fora da cadeia de **origem,** a função devolverá o substring do índice de **arranque** até ao fim da cadeia de **origem.** |

---
### <a name="normalizediacritics"></a>Normalizar Os críticos da Diadia
**Função:**<br> NormalizarOsDiacritics (fonte)

**Description:**<br> Requer um argumento de corda. Devolve a corda, mas com quaisquer caracteres diacríticos substituídos por caracteres não diacríticos equivalentes. Normalmente usado para converter os primeiros nomes e apelidos que contêm caracteres diacríticos (marcas de sotaque) em valores legais que podem ser usados em vários identificadores de utilizador, tais como nomes principais do utilizador, nomes de contas SAM e endereços de e-mail.

**Parâmetros:**<br> 

| Name | Obrigatório/ Repetição | Tipo | Notas |
| --- | --- | --- | --- |
| **fonte** |Necessário |String | Normalmente um primeiro nome ou apelido. |

---
### <a name="not"></a>Não
**Função:**<br> Não (fonte)

**Description:**<br> Inverte o valor booleano da **fonte.** Se o valor **de origem** for "*Verdadeiro",* devolve "*Falso*". Caso contrário, retorna "*Verdadeiro*".

**Parâmetros:**<br> 

| Name | Obrigatório/ Repetição | Tipo | Notas |
| --- | --- | --- | --- |
| **fonte** |Necessário |Corda Booleana |Os valores **de origem** esperados são "Verdadeiros" ou "Falsos". |

---
### <a name="numfromdate"></a>NumFromDate
**Função:**<br> NumFromDate (valor)

**Description:**<br> A função NumFromDate converte um valor DateTime para o formato Ative Directory que é necessário para definir atributos como [contasExpirações](https://docs.microsoft.com/windows/win32/adschema/a-accountexpires). Utilize esta função para converter os valores dateTime recebidos de aplicações de RH em nuvem como Workday e SuccessFactors para a sua representação aD equivalente. 

**Parâmetros:**<br> 

| Name | Obrigatório/ Repetição | Tipo | Notas |
| --- | --- | --- | --- |
| **valor** |Necessário | String | Linha de hora de data no formato suportado. Para formatos suportados, consulte https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx. |

**Exemplo:**<br>
* Exemplo do dia de trabalho <br>
  Assumindo que pretende mapear o atributo *ContractEndDate* do Dia de Trabalho, que está no formato *2020-12-31-08:00* para o campo de *contasExpires* em AD, aqui está como você pode usar esta função e alterar o tempo compensado para combinar com o seu local. 
  `NumFromDate(Join("", FormatDateTime([ContractEndDate], "yyyy-MM-ddzzz", "yyyy-MM-dd"), "T23:59:59-08:00"))`

* Exemplo de SuccessFactors <br>
  Assumindo que pretende mapear o final do *atributoData* from SuccessFactors que está no formato *M/d/yyyy hh:mm:st* para *contasCampo Expiraem* em AD, aqui está como você pode usar esta função e alterar o fuso horário compensado para combinar com o seu local.
  `NumFromDate(Join("",FormatDateTime([endDate],"M/d/yyyy hh:mm:ss tt","yyyy-MM-dd"),"T23:59:59-08:00"))`


---
### <a name="removeduplicates"></a>Remover Duplicatos
**Função:**<br> Remover Duplicates (atributo)

**Description:**<br> A função RemoveDuplicates pega numa cadeia de vários valores e certifica-se de que cada valor é único.

**Parâmetros:**<br> 

| Name | Obrigatório/ Repetição | Tipo | Notas |
| --- | --- | --- | --- |
| **atributo** |Necessário |Atributo multivalorizado |Atributo multivalorizado que terá duplicados removidos|

**Exemplo:**<br>
Remover Duplicatos([proxyAddresss])                                                                                                       
Devolve um atributo proxyAddress sanitizado onde todos os valores duplicados foram removidos

---
### <a name="replace"></a>Substituir
**Função:**<br> Substituir (fonte, oldValue, regexPattern, regexGroupName, substituiçãoValue, substituiçãoAttributeName, modelo)

**Description:**<br>
Substitui valores dentro de uma corda. Funciona de forma diferente dependendo dos parâmetros fornecidos:

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

| Name | Obrigatório/ Repetição | Tipo | Notas |
| --- | --- | --- | --- |
| **fonte** |Necessário |String |Normalmente, o nome do atributo do objeto **de origem.** |
| **oldValue** |Opcional |String |Valor a substituir na **fonte** ou **no modelo.** |
| **regexPattern** |Opcional |String |Padrão Regex para o valor a substituir na **fonte**. Ou, quando é utilizado **o nome propertyname** substituto, padrão para extrair valor da **substituiçãoPropertyName**. |
| **regexGroupName** |Opcional |String |Nome do grupo dentro do **regexPattern**. Só quando for utilizado **o substitutoPropertyName,** extrairemos valor deste grupo como **substitutoValor** da **substituiçãoPropertyName**. |
| **substituiçãoValor** |Opcional |String |Novo valor para substituir o antigo por. |
| **substituiçãoNome atribuído** |Opcional |String |Nome do atributo a utilizar para valor de substituição |
| **modelo** |Opcional |String |Quando o valor do **modelo** for fornecido, procuraremos **por oldValue** dentro do modelo e substituí-lo-emos pelo valor **de origem.** |

---
### <a name="selectuniquevalue"></a>SelecioneUniqueValue
**Função:**<br> SelectUniqueValue (uniqueValueRule1, uniqueValueRule2, uniqueValueRule3, ...)

**Description:**<br> Requer um mínimo de dois argumentos, que são regras únicas de geração de valor definidas usando expressões. A função avalia cada regra e, em seguida, verifica o valor gerado para a singularidade na aplicação/diretório alvo. O primeiro valor único encontrado será o devolvido. Se todos os valores já existirem no alvo, a entrada será depositada e a razão é registada nos registos de auditoria. Não há limite superior ao número de argumentos que podem ser fornecidos.


 - Esta é uma função de alto nível, não pode ser aninhada.
 - Esta função não pode ser aplicada a atributos que tenham uma precedência correspondente.   
 - Esta função destina-se apenas a ser utilizada para criações de entrada. Ao utilizá-lo com um atributo, detete a propriedade **De Mapeamento aplicar** apenas durante a **criação de objetos**.
 - Esta função é atualmente suportada apenas para "Workday to Ative Directory User Provisioning". Não pode ser utilizado com outros pedidos de provisionamento. 


**Parâmetros:**<br> 

| Name | Obrigatório/ Repetição | Tipo | Notas |
| --- | --- | --- | --- |
| **únicoValueRule1 ... únicoValueRuleN** |Pelo menos 2 são necessários, sem limite superior |String | Lista de regras únicas de geração de valor para avaliar. |


---
### <a name="singleapproleassignment"></a>Atribuição singleAppRole
**Função:**<br> Atribuição de funções simples ([atribuição de aplicações])

**Description:**<br> Devolve uma única appRoleAssignment da lista de todas as aplicaçõesAtribuídas atribuídas a um utilizador para uma determinada aplicação. Esta função é necessária para converter o objeto de appRoleAssignments numa única cadeia de nomes. Note que a melhor prática é garantir que apenas uma appRoleAssignment é atribuída a um utilizador de cada vez, e se várias funções forem atribuídas, a cadeia de funções devolvida pode não ser previsível. 

**Parâmetros:**<br> 

| Name | Obrigatório/ Repetição | Tipo | Notas |
| --- | --- | --- | --- |
| **[appRoleAssignments]** |Necessário |String |**[appRoleAssignments]** objeto. |

---
### <a name="split"></a>Dividir
**Função:**<br> Divisão (fonte, delimitador)

**Description:**<br> Divide uma cadeia numa matriz de vários valores, utilizando o carácter delimitador especificado.

**Parâmetros:**<br> 

| Name | Obrigatório/ Repetição | Tipo | Notas |
| --- | --- | --- | --- |
| **fonte** |Necessário |String |valor **de origem** para atualização. |
| **delimitador** |Necessário |String |Especifica o personagem que será usado para dividir a corda (exemplo: "") |

---
### <a name="stripspaces"></a>StripSpaces
**Função:**<br> StripSpaces (fonte)

**Description:**<br> Remove todos os caracteres de espaço (" "") da cadeia de origem.

**Parâmetros:**<br> 

| Name | Obrigatório/ Repetição | Tipo | Notas |
| --- | --- | --- | --- |
| **fonte** |Necessário |String |valor **de origem** para atualização. |

---
### <a name="switch"></a>Comutador
**Função:**<br> Switch (fonte, defaultValue, key1, value1, key2, value2, ...)

**Description:**<br> Quando o valor **de origem** corresponde a uma **chave,** devolve **valor** para essa **tecla**. Se o valor de **origem** não corresponder a nenhuma tecla, devolve **o predefiniçãoValor**.  **Os** parâmetros de chave e **valor** devem sempre vir em pares. A função espera sempre um número par de parâmetros. A função não deve ser utilizada para atributos referenciais como o gestor. 

**Parâmetros:**<br> 

| Name | Obrigatório/ Repetição | Tipo | Notas |
| --- | --- | --- | --- |
| **fonte** |Necessário |String |**Valor de origem** para atualização. |
| **defaultValor** |Opcional |String |Valor predefinido a ser usado quando a fonte não corresponde a nenhuma tecla. Pode ser corda vazia (""). |
| **chave** |Necessário |String |**Chave** para comparar o valor **de origem** com. |
| **valor** |Necessário |String |Valor de substituição da **fonte** que combina com a chave. |

---
### <a name="tolower"></a>ToLower
**Função:**<br> ToLower (fonte, cultura)

**Description:**<br> Pega num valor de cadeia de *origem* e converte-o para minúscula utilizando as regras de cultura especificadas. Se não houver informações de *cultura* especificadas, então utilizará a cultura Invariante.

**Parâmetros:**<br> 

| Name | Obrigatório/ Repetição | Tipo | Notas |
| --- | --- | --- | --- |
| **fonte** |Necessário |String |Normalmente nome do atributo do objeto de origem |
| **cultura** |Opcional |String |O formato para o nome da cultura baseado no RFC 4646 é *languagecode2-country/regioncode2*, onde o *código linguístico2* é o código linguístico de duas letras e o *código de país/região2* é o código de subcultura de duas letras. Exemplos incluem ja-JP para japonês (Japão) e en-US para inglês (Estados Unidos). Nos casos em que não esteja disponível um código linguístico de duas letras, é utilizado um código de três letras derivado da ISO 639-2.|

---
### <a name="toupper"></a>Toia
**Função:**<br> ToUpper (fonte, cultura)

**Description:**<br> Pega num valor de cadeia de *origem* e converte-o em maiúsculas usando as regras de cultura especificadas. Se não houver informações de *cultura* especificadas, então utilizará a cultura Invariante.

**Parâmetros:**<br> 

| Name | Obrigatório/ Repetição | Tipo | Notas |
| --- | --- | --- | --- |
| **fonte** |Necessário |String |Normalmente, o nome do atributo do objeto de origem. |
| **cultura** |Opcional |String |O formato para o nome da cultura baseado no RFC 4646 é *languagecode2-country/regioncode2*, onde o *código linguístico2* é o código linguístico de duas letras e o *código de país/região2* é o código de subcultura de duas letras. Exemplos incluem ja-JP para japonês (Japão) e en-US para inglês (Estados Unidos). Nos casos em que não esteja disponível um código linguístico de duas letras, é utilizado um código de três letras derivado da ISO 639-2.|

---
### <a name="word"></a>Word
**Função:**<br> Palavra (String,WordNumber,Delimiters)

**Description:**<br> A função Word devolve uma palavra contida numa corda, baseada em parâmetros que descrevem os delimitadores a usar e o número de palavra sabotado. Cada cadeia de caracteres em cadeia separada si uma das personagens em delimitadores é identificada como palavras:

Se o número < 1, devolve corda vazia.
Se a corda for nula, devolve corda vazia.
Se a corda contiver menos de palavras numéricas, ou a corda não contiver palavras identificadas pelos delimitadores, uma corda vazia é devolvida.

**Parâmetros:**<br> 

| Name | Obrigatório/ Repetição | Tipo | Notas |
| --- | --- | --- | --- |
| **String** |Necessário |Atributo multivalorizado |Corda para devolver uma palavra de.|
| **Número de palavras** |Necessário | Número inteiro | Número identificando qual número de palavra deve devolver|
| **delimitadores** |Necessário |String| Uma corda que representa o(s) delimitador que deve ser usado para identificar palavras|

**Exemplo:**<br>
Palavra("A raposa marrom rápida", 3, ")                                                                                       
Devoluções "marrom"

Palavra ("Isto,string!tem&muitos separadores", 3,","!&#")                                                                       
Devoluções "tem"

---

## <a name="examples"></a>Exemplos
### <a name="strip-known-domain-name"></a>Strip nome de domínio conhecido
É necessário retirar um nome de domínio conhecido do e-mail de um utilizador para obter um nome de utilizador. <br>
Por exemplo, se o domínio for "contoso.com", então pode utilizar a seguinte expressão:

**Expressão:** <br>
`Replace([mail], "@contoso.com", , ,"", ,)`

**Entrada/saída da amostra:** <br>

* **INPUT** (correio):john.doe@contoso.com" "
* **SAÍDA**: "john.doe"

### <a name="append-constant-suffix-to-user-name"></a>Apêndice constante sufixo ao nome do utilizador
Se estiver a utilizar uma Caixa de Areia Salesforce, poderá ter de anexar um sufixo adicional a todos os nomes dos seus utilizadores antes de os sincronizar.

**Expressão:** <br>
`Append([userPrincipalName], ".test")`

**Entrada/saída da amostra:** <br>

* **INPUT**: (userPrincipalName): " "John.Doe@contoso.com
* **SAÍDA**:John.Doe@contoso.com.test"

### <a name="generate-user-alias-by-concatenating-parts-of-first-and-last-name"></a>Gerar pseudónimo de utilizador concatenando partes do primeiro e último nome
É necessário gerar um pseudónimo de utilizador, tomando as primeiras 3 letras do primeiro nome do utilizador e as primeiras 5 letras do apelido do utilizador.

**Expressão:** <br>
`Append(Mid([givenName], 1, 3), Mid([surname], 1, 5))`

**Entrada/saída da amostra:** <br>

* **INPUT** (nome dado): "John"
* **INPUT** (sobrenome): "Doe"
* **SAÍDA**: "JohDoe"

### <a name="remove-diacritics-from-a-string"></a>Remova os diacríticos de uma corda
É necessário substituir caracteres que contenham marcas de sotaque por caracteres equivalentes que não contenham marcas de sotaque.

**Expressão:** <br>
NormalizarOsdiacritics([nome dado])

**Entrada/saída da amostra:** <br>

* **INPUT** (nome dado): "Zoë"
* **SAÍDA**: "Zoe"

### <a name="split-a-string-into-a-multi-valued-array"></a>Divida uma corda em uma matriz de vários valores
Você precisa pegar uma lista de cordas delimitada sma e dividi-las em uma matriz que pode ser ligada a um atributo de vários valores como o atributo de Permissões da Salesforce. Neste exemplo, uma lista de conjuntos de permissões foi povoada em extensãoAttribute5 em Azure AD.

**Expressão:** <br>
Divisão([extensãoAtribuído5], "")

**Entrada/saída da amostra:** <br>

* **INPUT** (extensãoAttribute5): "PermissionSetOne, PermisionSetTwo"
* **SAÍDA**: ["PermissionSetone", "PermissionSetTwo"]

### <a name="output-date-as-a-string-in-a-certain-format"></a>Data de saída como uma corda num determinado formato
Deseja enviar datas para uma aplicação SaaS num determinado formato. <br>
Por exemplo, pretende formatar datas para o ServiceNow.

**Expressão:** <br>

`FormatDateTime([extensionAttribute1], "yyyyMMddHHmmss.fZ", "yyyy-MM-dd")`

**Entrada/saída da amostra:**

* **INPUT** (extensãoAtribuído1): "20150123105347.1Z"
* **SAÍDA**: "2015-01-23"

### <a name="replace-a-value-based-on-predefined-set-of-options"></a>Substitua um valor com base no conjunto de opções predefinidas

É necessário definir o fuso horário do utilizador com base no código estatal armazenado em Azure AD. <br>
Se o código de Estado não corresponder a nenhuma das opções predefinidas, utilize o valor padrão de "Austrália/Sydney".

**Expressão:** <br>
`Switch([state], "Australia/Sydney", "NSW", "Australia/Sydney","QLD", "Australia/Brisbane", "SA", "Australia/Adelaide")`

**Entrada/saída da amostra:**

* **INPUT** (estado): "QLD"
* **SAÍDA**: "Austrália/Brisbane"

### <a name="replace-characters-using-a-regular-expression"></a>Substitua os caracteres usando uma expressão regular
Precisa encontrar caracteres que correspondam a um valor de expressão regular e removê-los.

**Expressão:** <br>

Substituir,[mailNickname], "[a-zA-Z_]*", "", "", ")

**Entrada/saída da amostra:**

* **INPUT** (mailNickname: "john_doe72"
* **SAÍDA**: "72"

### <a name="convert-generated-userprincipalname-upn-value-to-lower-case"></a>Converter valor do userPrincipalName (UPN) gerado para minúscula
No exemplo abaixo, o valor UPN é gerado concatenando os campos de origem PreferredFirstName e PreferredLastName e a função ToLower funciona na cadeia gerada para converter todos os caracteres em minúsculas. 

`ToLower(Join("@", NormalizeDiacritics(StripSpaces(Join(".",  [PreferredFirstName], [PreferredLastName]))), "contoso.com"))`

**Entrada/saída da amostra:**

* **ENTRADA** (PreferredFirstName): "John"
* **INPUT** (PreferredLastName): "Smith"
* **SAÍDA**:john.smith@contoso.com"

### <a name="generate-unique-value-for-userprincipalname-upn-attribute"></a>Gerar valor único para o atributo do userPrincipalName (UPN)
Com base no primeiro nome, nome do meio e apelido do utilizador, é necessário gerar um valor para o atributo upn e verificar a sua singularidade no diretório de Anúncios-alvo antes de atribuir o valor ao atributo UPN.

**Expressão:** <br>

    SelectUniqueValue( 
        Join("@", NormalizeDiacritics(StripSpaces(Join(".",  [PreferredFirstName], [PreferredLastName]))), "contoso.com"), 
        Join("@", NormalizeDiacritics(StripSpaces(Join(".",  Mid([PreferredFirstName], 1, 1), [PreferredLastName]))), "contoso.com"),
        Join("@", NormalizeDiacritics(StripSpaces(Join(".",  Mid([PreferredFirstName], 1, 2), [PreferredLastName]))), "contoso.com")
    )

**Entrada/saída da amostra:**

* **ENTRADA** (PreferredFirstName): "John"
* **INPUT** (PreferredLastName): "Smith"
* **SAÍDA**:John.Smith@contoso.com" se o John.Smith@contoso.com valor UPN não existir já no diretório
* **SAÍDA**:J.Smith@contoso.com" " se John.Smith@contoso.com o valor UPN já existe no diretório
* **SAÍDA**:Jo.Smith@contoso.com" " se os dois valores UPN acima já existirem no diretório

### <a name="flow-mail-value-if-not-null-otherwise-flow-userprincipalname"></a>Valor de correio de fluxo se não NULO, caso contrário, fluxo userPrincipalName
Deseja fluir o atributo do correio se estiver presente. Caso contrário, deseja fluir o valor do utilizadorPrincipalName.

**Expressão:** <br>
`Coalesce([mail],[userPrincipalName])`

**Entrada/saída da amostra:** <br>

* **INPUT** (correio): NULO
* **INPUT** (userPrincipalName):John.Doe@contoso.com" "
* **SAÍDA**:John.Doe@contoso.com"

## <a name="related-articles"></a>Artigos Relacionados
* [Automatizar o fornecimento/deprovisionamento de utilizadores para apps SaaS](../app-provisioning/user-provisioning.md)
* [Personalização de mapeamentos de atributos para fornecimento de utilizadores](../app-provisioning/customize-application-attributes.md)
* [Filtros de deteção para fornecimento de utilizadores](define-conditional-rules-for-provisioning-user-accounts.md)
* [Utilizar o SCIM para ativar o aprovisionamento automático de utilizadores e grupos do Azure Active Directory a aplicações](../app-provisioning/use-scim-to-provision-users-and-groups.md)
* [Notificações do Aprovisionamento de Contas](../app-provisioning/user-provisioning.md)
* [Lista de Tutoriais sobre Como Integrar Aplicações SaaS](../saas-apps/tutorial-list.md)
