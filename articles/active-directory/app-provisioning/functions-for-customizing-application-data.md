---
title: Expressões de escrita para mapeamento de atributos em Anúncio azure
description: Saiba como utilizar os mapeamentos de expressão para transformar valores de atributo num formato aceitável durante o aprovisionamento automatizado de objetos de aplicação SaaS no Azure Active Directory.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/05/2020
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: dd78c78a711b64c58290f09eb2ee52263375002f
ms.sourcegitcommit: 3c8fbce6989174b6c3cdbb6fea38974b46197ebe
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/21/2020
ms.locfileid: "77522514"
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
| **fonte** |Necessário |Cadeia |Normalmente, o nome do atributo de objeto de origem. |
| **sufixo** |Necessário |Cadeia |A cadeia a acrescentar ao final do valor de origem. |

---
### <a name="bitand"></a>Bitand
**Função:**<br> BitAnd (valor 1, valor 2)

**Descrição:**<br> Esta função converte ambos os parâmetros para a representação binária e define um pouco para:

0 - se uma ou ambas as partes correspondentes no valor 1 e no valor2 forem 0                                                  
1 - se ambas as partes correspondentes forem 1.                                    

Por outras palavras, retorna a 0 em todos os casos, exceto quando os bits correspondentes de ambos os parâmetros são 1.

**Parâmetros:**<br> 

| Nome | Obrigatório / repetidos | Tipo | Notas |
| --- | --- | --- | --- |
| **valor1** |Necessário |num |Valor numérico que deve ser E'ed com valor2|
| **valor2** |Necessário |num |Valor numérico que deve ser E'ed com valor1|

**Exemplo:**<br>
BitAnd (&HF, &HF7)                                                                                
11110111 E 00000111 = 00000111 por isso bitAnd devolve 7, o valor binário de 000001111

---
### <a name="cbool"></a>CBool
**Função:**<br> CBool (Expressão)

**Descrição:**<br> CBool devolve uma booleana com base na expressão avaliada. Se a expressão avaliar para um valor não zero, então CBool devolve True, caso contrário devolve Falso.

**Parâmetros:**<br> 

| Nome | Obrigatório / repetidos | Tipo | Notas |
| --- | --- | --- | --- |
| **expressão** |Necessário | expression | Qualquer expressão válida |

**Exemplo:**<br>
CBool =[atributo2])                                                                    
Devoluções Verdadeiras se ambos os atributos tiverem o mesmo valor.

---
### <a name="coalesce"></a>coalesce
**Função:**<br> Coalesce (fonte1, fonte2, ..., valor padrão)

**Descrição:**<br> Devolve o primeiro valor de origem que não é NULO. Se todos os argumentos forem NULOs e o defaultValue estiver presente, o valor padrãoValue será devolvido. Se todos os argumentos forem NULOS e o defaultValue não estiver presente, coalesce devolve NULL.

**Parâmetros:**<br> 

| Nome | Obrigatório / repetidos | Tipo | Notas |
| --- | --- | --- | --- |
| **fonte1 ... fonteN** | Necessário | Cadeia |Obrigatório, número variável de vezes. Normalmente, o nome do atributo de objeto de origem. |
| **defaultValor** | Opcional | Cadeia | Valor predefinido a utilizar quando todos os valores de origem são NULOS. Pode ser uma cadeia de caracteres vazia ("").

---
### <a name="converttobase64"></a>ConvertToBase64
**Função:**<br> ConvertToBase64 (fonte)

**Descrição:**<br> A função ConvertToBase64 converte uma cadeia para uma cadeia Unicode base64.

**Parâmetros:**<br> 

| Nome | Obrigatório / repetidos | Tipo | Notas |
| --- | --- | --- | --- |
| **fonte** |Necessário |Cadeia |Corda a ser convertida para base 64|

**Exemplo:**<br>
ConvertToBase64 ("Olá mundo!")                                                                                                        
Devoluções "SABlAGwAbABvACAAdwBvAHIAbABkACEA"

---
### <a name="converttoutf8hex"></a>ConvertToUTF8Hex
**Função:**<br> ConvertToUTF8Hex (fonte)

**Descrição:**<br> A função ConvertToUTF8Hex converte uma cadeia para um valor codificado uTF8 Hex.

**Parâmetros:**<br> 

| Nome | Obrigatório / repetidos | Tipo | Notas |
| --- | --- | --- | --- |
| **fonte** |Necessário |Cadeia |Corda a ser convertida em UTF8 Hex|

**Exemplo:**<br>
ConvertToUTF8Hex ("Olá mundo!")                                                                                                         
Returns 48656C6C6F20776F726C6421

---
### <a name="count"></a>Contagem
**Função:**<br> Contagem (atributo)

**Descrição:**<br> A função Count devolve o número de elementos num atributo multivalorizado

**Parâmetros:**<br> 

| Nome | Obrigatório / repetidos | Tipo | Notas |
| --- | --- | --- | --- |
| **atributo** |Necessário |atributo |Atributo multivalorizado que terá elementos contados|

---
### <a name="cstr"></a>CStr
**Função:**<br> CStr (valor)

**Descrição:**<br> A função CStr converte um valor para um tipo de dados de cordas.

**Parâmetros:**<br> 

| Nome | Obrigatório / repetidos | Tipo | Notas |
| --- | --- | --- | --- |
| **valor** |Necessário | numérico, referência ou booleano | Pode ser um valor numérico, atributo de referência, ou Boolean. |

**Exemplo:**<br>
CStr([dn])                                                            
Devoluções "cn=Joe,dc=contoso,dc=com"

---
### <a name="datefromnum"></a>DateFromNum
**Função:**<br> DataFromNum (valor)

**Descrição:**<br> A função DateFromNum converte um valor no formato de data da AD para um tipo DateTime.

**Parâmetros:**<br> 

| Nome | Obrigatório / repetidos | Tipo | Notas |
| --- | --- | --- | --- |
| **valor** |Necessário | Data | Data AD a converter para o tipo DateTime |

**Exemplo:**<br>
DateFromNum([lastLogonTimestamp])                                                                                                   
DataFromnum(129699324000000000000000000000000)                                                            
Devoluções DataTime representando 2012-01-01 23:00:00

---
### <a name="formatdatetime"></a>FormatDateTime
**Função:**<br> FormatDateTime (origem, inputFormat, outputFormat)

**Descrição:**<br> Precisa de uma cadeia de data de um formato e os converte num formato diferente.

**Parâmetros:**<br> 

| Nome | Obrigatório / repetidos | Tipo | Notas |
| --- | --- | --- | --- |
| **fonte** |Necessário |Cadeia |Normalmente, o nome do atributo de objeto de origem. |
| **inputFormat** |Necessário |Cadeia |Formato esperado do valor de origem. Para formatos suportados, consulte [https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx). |
| **outputFormat** |Necessário |Cadeia |Formato da data de saída. |

---
### <a name="guid"></a>Guid
**Função:**<br> Guia()

**Descrição:**<br> A função Guid gera um novo GUID aleatório

---
### <a name="instr"></a>Instr
**Função:**<br> InStr (valor1,valor2,início,compareType)

**Descrição:**<br> A função InStr encontra a primeira ocorrência de uma substring numa corda

**Parâmetros:**<br> 

| Nome | Obrigatório / repetidos | Tipo | Notas |
| --- | --- | --- | --- |
| **valor1** |Necessário |Cadeia |Corda a ser revistada |
| **valor2** |Necessário |Cadeia |Corda a ser encontrada |
| **start** |Opcional |Número inteiro |Posição inicial para encontrar a subcadeia|
| **compararType** |Opcional |Enum |Pode ser vbTextCompare ou vbBinaryCompare |

**Exemplo:**<br>
InStr("A raposa marrom rápida", "rápido")                                                                             
Valores e valores para 5

InStr ("repEated", "e,"3,vbBinaryCompare)                                                                                  
Avalia para 7

---
### <a name="isnull"></a>IsNull
**Função:**<br> Isnull (Expressão)

**Descrição:**<br> Se a expressão avaliar a Nula, então a função IsNull retorna verdadeira. Para um atributo, um Nulo é expresso pela ausência do atributo.

**Parâmetros:**<br> 

| Nome | Obrigatório / repetidos | Tipo | Notas |
| --- | --- | --- | --- |
| **expressão** |Necessário |expression |Expressão a avaliar |

**Exemplo:**<br>
IsNull([nome do ecrã])                                                                                                
Devoluções Verdadeirase se o atributo não estiver presente

---
### <a name="isnullorempty"></a>IsNullorEmpty
**Função:**<br> IsnullorEmpty (Expressão)

**Descrição:**<br> Se a expressão for nula ou uma corda vazia, então a função IsNullOrEmpty retorna verdadeira. Para um atributo, isto avaliaria a True se o atributo está ausente ou está presente, mas é uma corda vazia.
O inverso desta função chama-se IsPresent.

**Parâmetros:**<br> 

| Nome | Obrigatório / repetidos | Tipo | Notas |
| --- | --- | --- | --- |
| **expressão** |Necessário |expression |Expressão a avaliar |

**Exemplo:**<br>
IsNullOrEmpty([nome do ecrã])                                               
Devoluções Verdadeirase se o atributo não estiver presente ou se for uma corda vazia

---
### <a name="ispresent"></a>IsPresent
**Função:**<br> IsPresent (Expressão)

**Descrição:**<br> Se a expressão avaliar para uma cadeia que não é Nula e não está vazia, então a função IsPresent retorna verdadeira. O inverso desta função chama-se IsNullOrEmpty.

**Parâmetros:**<br> 

| Nome | Obrigatório / repetidos | Tipo | Notas |
| --- | --- | --- | --- |
| **expressão** |Necessário |expression |Expressão a avaliar |

**Exemplo:**<br>
Switch(IsPresent([directManager]),[directManager], IsPresent([skiplevelManager],[skiplevelManager], IsPresent[[diretor]]]]

---
### <a name="isstring"></a>IsString
**Função:**<br> Isstring (Expressão)

**Descrição:**<br> Se a expressão pode ser avaliada para um tipo de corda, então a função IsString avalia para True.

**Parâmetros:**<br> 

| Nome | Obrigatório / repetidos | Tipo | Notas |
| --- | --- | --- | --- |
| **expressão** |Necessário |expression |Expressão a avaliar |

---
### <a name="item"></a>Item
**Função:**<br> Item (atributo, índice)

**Descrição:**<br> A função Item devolve um item de uma cadeia/atributo de vários valores.

**Parâmetros:**<br> 

| Nome | Obrigatório / repetidos | Tipo | Notas |
| --- | --- | --- | --- |
| **atributo** |Necessário |Atributo |Atributo multivalorizado a ser pesquisado |
| **índice** |Necessário |Número inteiro | Index a um item na cadeia multi-valorizado|

**Exemplo:**<br>
Item([proxyAddresss], 1)

---
### <a name="join"></a>Associar
**Função:**<br> Junte-se (separador, source1 source2,...)

**Descrição:**<br> A adesão() é semelhante ao apêndice, exceto que pode combinar múltiplos valores de cadeia de **origem** numa única corda, e cada valor será separado por uma corda **de separador.**

Se um dos valores de origem for um atributo de vários valores, então cada valor nesse atributo será unido, separado pelo valor do separador.

**Parâmetros:**<br> 

| Nome | Obrigatório / repetidos | Tipo | Notas |
| --- | --- | --- | --- |
| **separador** |Necessário |Cadeia |Cadeia utilizada para separar os valores de origem quando eles são concatenados numa cadeia de caracteres. Pode ser "" se nenhum separador é necessário. |
| **fonte1 ... fonteN** |Variável-número de vezes, necessário |Cadeia |Cadeia de valores a ser unidas. |

---
### <a name="left"></a>Esquerda
**Função:**<br> Esquerda (String,NumChars)

**Descrição:**<br> A função Esquerda devolve um número especificado de caracteres da esquerda de uma corda. Se numChars = 0, volte a ficar vazio.
Se numChars < 0, volte a ser corda de entrada.
Se a corda for nula, volte a ficar vazia.
Se a corda contiver menos caracteres do que o número especificado em numChars, uma corda idêntica à corda (isto é, contendo todos os caracteres no parâmetro 1) é devolvida.

**Parâmetros:**<br> 

| Nome | Obrigatório / repetidos | Tipo | Notas |
| --- | --- | --- | --- |
| **Corda** |Necessário |Atributo | A corda para devolver personagens de |
| **Numchars** |Necessário |Número inteiro | Um número que identifica o número de caracteres a regressar do início (à esquerda) da corda|

**Exemplo:**<br>
Esquerda("João Ninguém", 3)                                                            
Devoluções "Joh"

---
### <a name="mid"></a>Mid
**Função:**<br> Mid (origem, início, comprimento)

**Descrição:**<br> Devolve uma subcadeia do valor de origem. Uma subcadeia é uma cadeia que contém apenas alguns dos caracteres da cadeia de origem.

**Parâmetros:**<br> 

| Nome | Obrigatório / repetidos | Tipo | Notas |
| --- | --- | --- | --- |
| **fonte** |Necessário |Cadeia |Normalmente, o nome do atributo. |
| **start** |Necessário |número inteiro |Índice na cadeia **de origem** onde deve iniciar a subcadeia. Primeiro caractere na cadeia de caracteres terão o índice de 1, o segundo caráter terão índice 2 e assim por diante. |
| **comprimento** |Necessário |número inteiro |Comprimento da subcadeia. Se o comprimento terminar fora da cadeia de **origem,** a função devolverá o substring do índice de **arranque** até ao fim da cadeia de **origem.** |

---
### <a name="normalizediacritics"></a>NormalizeDiacritics
**Função:**<br> NormalizeDiacritics(source)

**Descrição:**<br> Requer um argumento de cadeia de caracteres. Devolve a cadeia de caracteres, mas com nenhum dos caracteres diacritical substituídos por carateres não diacritical equivalentes. Normalmente, utilizada para converter os nomes de primeiro e último carateres diacritical (marcas de acento sem) em valores legais que podem ser utilizadas em vários identificadores de utilizador, tais como nomes de principal de utilizador, nomes de conta SAM e endereços de e-mail.

**Parâmetros:**<br> 

| Nome | Obrigatório / repetidos | Tipo | Notas |
| --- | --- | --- | --- |
| **fonte** |Necessário |Cadeia | Normalmente um primeiro nome ou apelido. |

---
### <a name="not"></a>Não
**Função:**<br> Not(Source)

**Descrição:**<br> Inverte o valor booleano da **fonte.** Se o valor **de origem** for "*Verdadeiro",* devolve "*Falso*". Caso contrário, retorna "*Verdadeiro*".

**Parâmetros:**<br> 

| Nome | Obrigatório / repetidos | Tipo | Notas |
| --- | --- | --- | --- |
| **fonte** |Necessário |Booleano da cadeia |Os valores **de origem** esperados são "Verdadeiros" ou "Falsos". |

---
### <a name="numfromdate"></a>NumFromDate
**Função:**<br> NumFromDate (valor)

**Descrição:**<br> A função NumFromDate converte um valor DateTime para o formato Ative Directory que é necessário para definir atributos como [contasExpirações](https://docs.microsoft.com/windows/win32/adschema/a-accountexpires). Utilize esta função para converter os valores dateTime recebidos de aplicações de RH em nuvem como Workday e SuccessFactors para a sua representação aD equivalente. 

**Parâmetros:**<br> 

| Nome | Obrigatório / repetidos | Tipo | Notas |
| --- | --- | --- | --- |
| **valor** |Necessário | Cadeia | Linha de hora de data no formato suportado. Para formatos suportados, consulte https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx. |

**Exemplo:**<br>
* Exemplo do dia de trabalho <br>
  Assumindo que pretende mapear o atributo *ContractEndDate* do Dia de Trabalho, que está no formato *2020-12-31-08:00* para o campo de *contasExpires* em AD, aqui está como você pode usar esta função e alterar o tempo compensado para combinar com o seu local. 
  `NumFromDate(Join("", FormatDateTime([ContractEndDate], "yyyy-MM-ddzzz", "yyyy-MM-dd"), "T23:59:59-08:00"))`

* Exemplo de SuccessFactors <br>
  Assumindo que pretende mapear o final do *atributoData* from SuccessFactors que está no formato *M/d/yyyy hh:mm:st* para *contasCampo Expiraem* em AD, aqui está como você pode usar esta função e alterar o fuso horário compensado para combinar com o seu local.
  `NumFromDate(Join("",FormatDateTime([endDate],"M/d/yyyy hh:mm:ss tt","yyyy-MM-dd"),"T23:59:59-08:00"))`


---
### <a name="removeduplicates"></a>RemoveDuplicates
**Função:**<br> Remover Duplicates (atributo)

**Descrição:**<br> A função RemoveDuplicates pega numa cadeia de vários valores e certifica-se de que cada valor é único.

**Parâmetros:**<br> 

| Nome | Obrigatório / repetidos | Tipo | Notas |
| --- | --- | --- | --- |
| **atributo** |Necessário |Atributo multivalorizado |Atributo multivalorizado que terá duplicados removidos|

**Exemplo:**<br>
Remover Duplicatos([proxyAddresss])                                                                                                       
Devolve um atributo proxyAddress sanitizado onde todos os valores duplicados foram removidos

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
| **fonte** |Necessário |Cadeia |Normalmente, o nome do atributo do objeto **de origem.** |
| **oldValue** |Opcional |Cadeia |Valor a substituir na **fonte** ou **no modelo.** |
| **regexPattern** |Opcional |Cadeia |Padrão Regex para o valor a substituir na **fonte**. Ou, quando é utilizado **o nome propertyname** substituto, padrão para extrair valor da **substituiçãoPropertyName**. |
| **regexGroupName** |Opcional |Cadeia |Nome do grupo dentro do **regexPattern**. Só quando for utilizado **o substitutoPropertyName,** extrairemos valor deste grupo como **substitutoValor** da **substituiçãoPropertyName**. |
| **substituiçãoValor** |Opcional |Cadeia |Novo valor para substituir um antigo com. |
| **substituiçãoNome atribuído** |Opcional |Cadeia |Nome do atributo a utilizar para valor de substituição |
| **modelo** |Opcional |Cadeia |Quando o valor do **modelo** for fornecido, procuraremos **por oldValue** dentro do modelo e substituí-lo-emos pelo valor **de origem.** |

---
### <a name="selectuniquevalue"></a>SelectUniqueValue
**Função:**<br> SelectUniqueValue (uniqueValueRule1, uniqueValueRule2, uniqueValueRule3,...)

**Descrição:**<br> Requer um mínimo de dois argumentos, que são definidas usando expressões de regras de geração de valor exclusivo. A função avalia cada regra e, em seguida, verifica o valor gerado para exclusividade na aplicação/diretório de destino. O primeiro valor exclusivo encontrado vai ser devolvido o. Se todos os valores ainda existirem no destino, a entrada irá obter colocadas em caução em e o motivo é registrado em log nos registos de auditoria. Não há nenhum limite superior ao número de argumentos que pode ser fornecido.

> [!NOTE]
> - Esta é uma função de nível superior, ele não é possível aninhar.
> - Esta função não pode ser aplicada a atributos que tenham uma precedência correspondente.  
> - Esta função apenas se destina a ser utilizado para criações de entrada. Ao utilizá-lo com um atributo, detete a propriedade **De Mapeamento aplicar** apenas durante a **criação de objetos**.
> - Esta função é atualmente suportada apenas para "Workday to Ative Directory User Provisioning". Não pode ser utilizado com outros pedidos de provisionamento. 


**Parâmetros:**<br> 

| Nome | Obrigatório / repetidos | Tipo | Notas |
| --- | --- | --- | --- |
| **únicoValueRule1 ... únicoValueRuleN** |Pelo menos 2 são vinculados a necessário, não superior |Cadeia | Lista de regras únicas de geração de valor para avaliar. |


---
### <a name="singleapproleassignment"></a>SingleAppRoleAssignment
**Função:**<br> SingleAppRoleAssignment([appRoleAssignments])

**Descrição:**<br> Devolve uma única appRoleAssignment da lista de todas as aplicaçõesAtribuídas atribuídas a um utilizador para uma determinada aplicação. Esta função é necessária para converter o objeto de appRoleAssignments numa única cadeia de nomes. Note que a melhor prática é garantir que apenas uma appRoleAssignment é atribuída a um utilizador de cada vez, e se várias funções forem atribuídas, a cadeia de funções devolvida pode não ser previsível. 

**Parâmetros:**<br> 

| Nome | Obrigatório / repetidos | Tipo | Notas |
| --- | --- | --- | --- |
| **[appRoleAssignments]** |Necessário |Cadeia |**[appRoleAssignments]** objeto. |

---
### <a name="split"></a>Dividir
**Função:**<br> Divisão (fonte, delimitador)

**Descrição:**<br> Divide uma cadeia numa matriz de vários valores, utilizando o carácter delimitador especificado.

**Parâmetros:**<br> 

| Nome | Obrigatório / repetidos | Tipo | Notas |
| --- | --- | --- | --- |
| **fonte** |Necessário |Cadeia |valor **de origem** para atualização. |
| **delimitador** |Necessário |Cadeia |Especifica o personagem que será usado para dividir a corda (exemplo: "") |

---
### <a name="stripspaces"></a>StripSpaces
**Função:**<br> StripSpaces(source)

**Descrição:**<br> Remove todo o espaço ("") carateres da cadeia de caracteres de origem.

**Parâmetros:**<br> 

| Nome | Obrigatório / repetidos | Tipo | Notas |
| --- | --- | --- | --- |
| **fonte** |Necessário |Cadeia |valor **de origem** para atualização. |

---
### <a name="switch"></a>Comutador
**Função:**<br> Comutador (origem, defaultValue, chave1, value1, chave2, value2...)

**Descrição:**<br> Quando o valor **de origem** corresponde a uma **chave,** devolve **valor** para essa **tecla**. Se o valor de **origem** não corresponder a nenhuma tecla, devolve **o predefiniçãoValor**.  **Os** parâmetros de chave e **valor** devem sempre vir em pares. A função espera sempre um número par de parâmetros. A função não deve ser utilizada para atributos referenciais como o gestor. 

**Parâmetros:**<br> 

| Nome | Obrigatório / repetidos | Tipo | Notas |
| --- | --- | --- | --- |
| **fonte** |Necessário |Cadeia |**Valor de origem** para atualização. |
| **defaultValor** |Opcional |Cadeia |Valor predefinido a utilizar quando a origem não corresponde a quaisquer chaves. Pode ser uma cadeia de caracteres vazia (""). |
| **chave** |Necessário |Cadeia |**Chave** para comparar o valor **de origem** com. |
| **valor** |Necessário |Cadeia |Valor de substituição da **fonte** que combina com a chave. |

---
### <a name="tolower"></a>toLower
**Função:**<br> ToLower (fonte, cultura)

**Descrição:**<br> Pega num valor de cadeia de *origem* e converte-o para minúscula utilizando as regras de cultura especificadas. Se não houver informações de *cultura* especificadas, então utilizará a cultura Invariante.

**Parâmetros:**<br> 

| Nome | Obrigatório / repetidos | Tipo | Notas |
| --- | --- | --- | --- |
| **fonte** |Necessário |Cadeia |Normalmente, o nome do atributo de objeto de origem |
| **cultura** |Opcional |Cadeia |O formato para o nome da cultura baseado no RFC 4646 é *languagecode2-country/regioncode2*, onde o *código linguístico2* é o código linguístico de duas letras e o *código de país/região2* é o código de subcultura de duas letras. Exemplos incluem ja-JP para japonês (Japão) e en-US para inglês (Estados Unidos). Nos casos em que não esteja disponível um código linguístico de duas letras, é utilizado um código de três letras derivado da ISO 639-2.|

---
### <a name="toupper"></a>ToUpper
**Função:**<br> ToUpper (fonte, cultura)

**Descrição:**<br> Pega num valor de cadeia de *origem* e converte-o em maiúsculas usando as regras de cultura especificadas. Se não houver informações de *cultura* especificadas, então utilizará a cultura Invariante.

**Parâmetros:**<br> 

| Nome | Obrigatório / repetidos | Tipo | Notas |
| --- | --- | --- | --- |
| **fonte** |Necessário |Cadeia |Normalmente, o nome do atributo de objeto de origem. |
| **cultura** |Opcional |Cadeia |O formato para o nome da cultura baseado no RFC 4646 é *languagecode2-country/regioncode2*, onde o *código linguístico2* é o código linguístico de duas letras e o *código de país/região2* é o código de subcultura de duas letras. Exemplos incluem ja-JP para japonês (Japão) e en-US para inglês (Estados Unidos). Nos casos em que não esteja disponível um código linguístico de duas letras, é utilizado um código de três letras derivado da ISO 639-2.|

---
### <a name="word"></a>Word
**Função:**<br> Palavra (String,WordNumber,Delimiters)

**Descrição:**<br> A função Word devolve uma palavra contida numa corda, baseada em parâmetros que descrevem os delimitadores a usar e o número de palavra sabotado. Cada cadeia de caracteres em cadeia separada si uma das personagens em delimitadores é identificada como palavras:

Se o número e 1, devolver a corda vazia.
Se a corda for nula, devolve corda vazia.
Se a corda contiver menos de palavras numéricas, ou a corda não contiver palavras identificadas pelos delimitadores, uma corda vazia é devolvida.

**Parâmetros:**<br> 

| Nome | Obrigatório / repetidos | Tipo | Notas |
| --- | --- | --- | --- |
| **Corda** |Necessário |Atributo multivalorizado |Corda para devolver uma palavra de.|
| **Número de palavras** |Necessário | Número inteiro | Número identificando qual número de palavra deve devolver|
| **delimitadores** |Necessário |Cadeia| Uma corda que representa o(s) delimitador que deve ser usado para identificar palavras|

**Exemplo:**<br>
Palavra("A raposa marrom rápida", 3, ")                                                                                       
Devoluções "marrom"

Palavra ("Isto,string!has&many separadores",3,"!&#")                                                                       
Devoluções "tem"

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

### <a name="split-a-string-into-a-multi-valued-array"></a>Divida uma corda em uma matriz de vários valores
Você precisa pegar uma lista de cordas delimitada sma e dividi-las em uma matriz que pode ser ligada a um atributo de vários valores como o atributo de Permissões da Salesforce. Neste exemplo, uma lista de conjuntos de permissões foi povoada em extensãoAttribute5 em Azure AD.

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

### <a name="flow-mail-value-if-not-null-otherwise-flow-userprincipalname"></a>Valor de correio de fluxo se não NULO, caso contrário, fluxo userPrincipalName
Deseja fluir o atributo do correio se estiver presente. Caso contrário, deseja fluir o valor do utilizadorPrincipalName.

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
