---
title: Azure AD Connect expressões de fornecimento de nuvem e referência de função
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
ms.openlocfilehash: 51c14fd7f427c29c47521a7355309e62ab2254ca
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "78298620"
---
# <a name="writing-expressions-for-attribute-mappings-in-azure-active-directory"></a>Expressões de escrita para mapeamento de atributos no Diretório Ativo Azure
Ao configurar o fornecimento de nuvens, um dos tipos de mapeamento de atributos que pode especificar é um mapeamento de expressão. 

O mapeamento de expressão permite-lhe personalizar atributos usando uma expressão semelhante a um script.  Isto permite-lhe transformar os dados no local num valor novo ou diferente.  Por exemplo, pode querer combinar dois atributos num único atributo porque este único atributo é usado por uma das suas aplicações na nuvem.

O seguinte documento cobrirá as expressões semelhantes ao script que são usadas para transformar os dados.  Isto é apenas uma parte do processo.  Em seguida, você precisará usar esta expressão e colocá-la em um pedido web para o seu inquilino.  Para mais informações sobre isso ver [Transformações](how-to-transformation.md)

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

## <a name="list-of-functions"></a>Lista de funções
| Lista de funções | Descrição |
|-----|----|
|[Acrescentar](#append)|Pega num valor de cadeia de origem e afixa o sufixo até ao fim.|
|[Bitand](#bitand)|A função BitAnd define bits especificados num valor.|
|[CBool](#cbool)|A função CBool devolve uma Boolean com base na expressão avaliada|
|[ConvertFromBase64](#convertfrombase64)|A função ConvertFromBase64 converte o valor codificado de base 64 especificado para uma cadeia regular.|
|[ConverttoBase64](#converttobase64)|A função ConvertToBase64 converte uma cadeia para uma cadeia Unicode base64. |
|[ConvertToUTF8Hex](#converttoutf8hex)|A função ConvertToUTF8Hex converte uma cadeia para um valor codificado uTF8 Hex.|
|[Contagem](#count)|A função Count devolve o número de elementos num atributo multivalorizado|
|[Rio Cstr](#cstr)|A função CStr converte-se num tipo de dados de cadeia.|
|[DataFromnum](#datefromnum)|A função DateFromNum converte um valor no formato de data da AD para um tipo DateTime.|
|[Componente DNComponente](#dncomponent)|A função DNComponent devolve o valor de um componente DN especificado que vai da esquerda.|
|[Erro](#error)|A função Error é utilizada para devolver um erro personalizado.|
|[FormatoDateTime](#formatdatetime) |Pega numa corda de data de um formato e converte-a num formato diferente.| 
|[GUIA](#guid)|A função Guid gera um novo GUID aleatório.|           
|[IIF](#iif)|A função IIF devolve um conjunto de valores possíveis com base numa condição especificada.|
|[Instr](#instr)|A função InStr encontra a primeira ocorrência de uma subcadeia numa corda.|
|[Isnull](#isnull)|Se a expressão avaliar a Nula, então a função IsNull retorna verdadeira.|
|[IsnullorEmpty](#isnullorempty)|Se a expressão for nula ou uma corda vazia, então a função IsNullOrEmpty retorna verdadeira.|         
|[IsPresent](#ispresent)|Se a expressão avaliar para uma cadeia que não é Nula e não está vazia, então a função IsPresent retorna verdadeira.|    
|[IsString](#isstring)|Se a expressão pode ser avaliada para um tipo de corda, então a função IsString avalia para True.|
|[Item](#item)|A função Item devolve um item de uma cadeia/atributo de vários valores.|
|[Aderir](#join) |A adesão() é semelhante ao apêndice, exceto que pode combinar múltiplos valores de cadeia de **origem** numa única corda, e cada valor será separado por uma corda **de separador.**| 
|[Esquerda](#left)|A função Esquerda devolve um número especificado de caracteres da esquerda de uma corda.|
|[Meados](#mid) |Devolve uma subcadeia do valor de origem. Uma subcadeia é uma cadeia que contém apenas alguns dos caracteres da cadeia de origem.|
|[Normalizar Os críticos da Diadia](#normalizediacritics)|Requer um argumento de corda. Devolve a corda, mas com quaisquer caracteres diacríticos substituídos por caracteres não diacríticos equivalentes.|
|[Não.](#not) |Inverte o valor booleano da **fonte.** Se o valor **de origem** for "*Verdadeiro",* devolve "*Falso*". Caso contrário, retorna "*Verdadeiro*".| 
|[Remover Duplicatos](#removeduplicates)|A função RemoveDuplicates pega numa cadeia de vários valores e certifica-se de que cada valor é único.| 
|[Substituir](#replace) |Substitui valores dentro de uma corda. | 
|[SelecioneUniqueValue](#selectuniquevalue)|Requer um mínimo de dois argumentos, que são regras únicas de geração de valor definidas usando expressões. A função avalia cada regra e, em seguida, verifica o valor gerado para a singularidade na aplicação/diretório alvo.| 
|[Atribuição singleAppRole](#singleapproleassignment)|Devolve uma única appRoleAssignment da lista de todas as aplicaçõesAtribuídas atribuídas a um utilizador para uma determinada aplicação.| 
|[Divisão](#split)|Divide uma cadeia numa matriz de vários valores, utilizando o carácter delimitador especificado.|
|[StringFromSID](#stringfromsid)|A função StringFromSid converte um matriz de byte contendo um identificador de segurança numa corda.| 
|[StripSpaces](#stripspaces) |Remove todos os caracteres de espaço (" "") da cadeia de origem.| 
|[Comutador](#switch)|Quando o valor **de origem** corresponde a uma **chave,** devolve **valor** para essa **tecla**. | 
|[ToLower](#tolower)|Pega num valor de cadeia de *origem* e converte-o para minúscula utilizando as regras de cultura especificadas.| 
|[Toia](#toupper)|Pega num valor de cadeia de *origem* e converte-o em maiúsculas usando as regras de cultura especificadas.|
|[Aparar](#trim)|A função Trim remove espaços brancos de condução e de saque de uma corda.|
|[Word](#word)|A função Word devolve uma palavra contida numa corda, baseada em parâmetros que descrevem os delimitadores a usar e o número de palavra sabotado.|

---
### <a name="append"></a>Acrescentar
**Função:**<br> Apêndice (fonte, sufixo)

**Description:**<br> Pega num valor de cadeia de origem e afixa o sufixo até ao fim.

**Parâmetros:**<br> 

   | Nome | Obrigatório/ Repetição | Tipo | Notas |
   | --- | --- | --- | --- |
   | **fonte** |Necessário |String |Normalmente, o nome do atributo do objeto de origem. |
   | **sufixo** |Necessário |String |A corda que quer anexar até ao fim do valor de origem. |

---
### <a name="bitand"></a>Bitand
**Description:**  
A função BitAnd define bits especificados num valor.

**Sintaxe:**  
`num BitAnd(num value1, num value2)`

* valor1, valor2: valores numéricos que devem ser E'ed juntos

**Observações:**  
Esta função converte ambos os parâmetros para a representação binária e define um pouco para:

* 0 - se uma ou ambas as partes correspondentes no *valor 1* e no *valor2* forem 0
* 1 - se ambas as partes correspondentes forem 1.

Por outras palavras, retorna a 0 em todos os casos, exceto quando os bits correspondentes de ambos os parâmetros são 1.

**Exemplo:**  
 
 `BitAnd(&HF, &HF7)`</br>
 Devoluções 7 porque hexadecimal "F" e "F7" avaliam a este valor.

---

### <a name="cbool"></a>CBool
**Description:**  
A função CBool devolve uma Boolean com base na expressão avaliada

**Sintaxe:**  
`bool CBool(exp Expression)`

**Observações:**  
Se a expressão avaliar para um valor não zero, então CBool devolve True, caso contrário devolve Falso.

**Exemplo:**  
`CBool([attrib1] = [attrib2])`  

Devoluções Verdadeiras se ambos os atributos tiverem o mesmo valor.

---
### <a name="convertfrombase64"></a>ConvertFromBase64
**Description:**  
A função ConvertFromBase64 converte o valor codificado de base 64 especificado para uma cadeia regular.

**Sintaxe:**  
`str ConvertFromBase64(str source)`- assume unicode para codificação  
`str ConvertFromBase64(str source, enum Encoding)`

* fonte: Cadeia codificada Base64  
* Codificação: Unicode, ASCII, UTF8

**Exemplo**  
`ConvertFromBase64("SABlAGwAbABvACAAdwBvAHIAbABkACEA")`  
`ConvertFromBase64("SGVsbG8gd29ybGQh", UTF8)`

Ambos os exemplos voltam "*Olá mundo!*"

---
### <a name="converttobase64"></a>ConverttoBase64
**Description:**  
A função ConvertToBase64 converte uma cadeia para uma cadeia Unicode base64.  
Converte o valor de um conjunto de inteiros para a sua representação de cordas equivalente que é codificada com base-64 dígitos.

**Sintaxe:**  
`str ConvertToBase64(str source)`

**Exemplo:**  
`ConvertToBase64("Hello world!")`  
Devoluções "SABlAGwAbABvACAAdwBvAHIAbABkACEA"

---
### <a name="converttoutf8hex"></a>ConvertToUTF8Hex
**Description:**  
A função ConvertToUTF8Hex converte uma cadeia para um valor codificado uTF8 Hex.

**Sintaxe:**  
`str ConvertToUTF8Hex(str source)`

**Observações:**  
O formato de saída desta função é utilizado pelo Azure Ative Directory como formato de atribuição dN.

**Exemplo:**  
`ConvertToUTF8Hex("Hello world!")`  
Devoluções 48656C6C6F20776F726C6421

---
### <a name="count"></a>Contagem
**Description:**  
A função Count devolve o número de elementos num atributo multivalorizado

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
### <a name="datefromnum"></a>DataFromnum
**Description:**  
A função DateFromNum converte um valor no formato de data da AD para um tipo DateTime.

**Sintaxe:**  
`dt DateFromNum(num value)`

**Exemplo:**  
`DateFromNum([lastLogonTimestamp])`  
`DateFromNum(129699324000000000)`  
Devoluções DataTime representando 2012-01-01 23:00:00

---
### <a name="dncomponent"></a>Componente DNComponente
**Description:**  
A função DNComponent devolve o valor de um componente DN especificado que vai da esquerda.

**Sintaxe:**  
`str DNComponent(ref dn, num ComponentNumber)`

* dN: o atributo de referência para interpretar
* ComponentNumber: O componente no DN para devolver

**Exemplo:**  
`DNComponent(CRef([dn]),1)`  
Se o DN é "cn=Joe,ou=...", devolve Joe

---
### <a name="error"></a>Erro
**Description:**  
A função Error é utilizada para devolver um erro personalizado.

**Sintaxe:**  
`void Error(str ErrorMessage)`

**Exemplo:**  
`IIF(IsPresent([accountName]),[accountName],Error("AccountName is required"))`  
Se a conta de atributonome não estiver presente, deite um erro no objeto.

---
### <a name="formatdatetime"></a>FormatoDateTime
**Função:**<br> FormatoDateTime (fonte, inputFormat, outputFormat)

**Description:**<br> Pega numa corda de data de um formato e converte-a num formato diferente.

**Parâmetros:**<br> 

   | Nome | Obrigatório/ Repetição | Tipo | Notas |
   | --- | --- | --- | --- |
   | **fonte** |Necessário |String |Normalmente, o nome do atributo do objeto de origem. |
   | **inputFormat** |Necessário |String |Formato esperado do valor de origem. Para formatos suportados, consulte [https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx). |
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
A função IIF devolve um conjunto de valores possíveis com base numa condição especificada.

**Sintaxe:**  
`var IIF(exp condition, var valueIfTrue, var valueIfFalse)`

* condição: qualquer valor ou expressão que possa ser avaliado a verdade ou falsa.
* valorIfTrue: Se a condição avaliar verdadeiramente, o valor devolvido.
* valorIfFalso: Se a condição avaliar a falso, o valor devolvido.

**Exemplo:**  
`IIF([employeeType]="Intern","t-" & [alias],[alias])`  
 Se o utilizador for estagiário, devolve o pseudónimo de um utilizador com "t-" adicionado ao início do mesmo, então devolve o pseudónimo do utilizador tal como está.

---
### <a name="instr"></a>Instr
**Description:**  
A função InStr encontra a primeira ocorrência de uma substring numa corda

**Sintaxe:**  

`num InStr(str stringcheck, str stringmatch)`  
`num InStr(str stringcheck, str stringmatch, num start)`  
`num InStr(str stringcheck, str stringmatch, num start , enum compare)`

* stringcheck: string a ser pesquisado
* stringmatch: corda a ser encontrada
* início: posição inicial para encontrar a subcadeia
* comparar: vbTextCompare ou vbBinaryCompare

**Observações:**  
Devolve a posição onde o substring foi encontrado ou 0 se não for encontrado.

**Exemplo:**  
`InStr("The quick brown fox","quick")`  
Valores e valores para 5

`InStr("repEated","e",3,vbBinaryCompare)`  
Avalia para 7

---
### <a name="isnull"></a>Isnull
**Description:**  
Se a expressão avaliar a Nula, então a função IsNull retorna verdadeira.

**Sintaxe:**  
`bool IsNull(var Expression)`

**Observações:**  
Para um atributo, um Nulo é expresso pela ausência do atributo.

**Exemplo:**  
`IsNull([displayName])`  
Devoluções Verdadeiras se o atributo não estiver presente no CS ou MV.

---
### <a name="isnullorempty"></a>IsnullorEmpty
**Description:**  
Se a expressão for nula ou uma corda vazia, então a função IsNullOrEmpty retorna verdadeira.

**Sintaxe:**  
`bool IsNullOrEmpty(var Expression)`

**Observações:**  
Para um atributo, isto avaliaria a True se o atributo está ausente ou está presente, mas é uma corda vazia.  
O inverso desta função chama-se IsPresent.

**Exemplo:**  
`IsNullOrEmpty([displayName])`  
Devoluções Verdadeirase se o atributo não estiver presente ou se for uma corda vazia no CS ou MV.

---
### <a name="ispresent"></a>IsPresent
**Description:**  
Se a expressão avaliar para uma cadeia que não é Nula e não está vazia, então a função IsPresent retorna verdadeira.

**Sintaxe:**  
`bool IsPresent(var expression)`

**Observações:**  
O inverso desta função chama-se IsNullOrEmpty.

**Exemplo:**  
`Switch(IsPresent([directManager]),[directManager], IsPresent([skiplevelManager]),[skiplevelManager], IsPresent([director]),[director])`

---
### <a name="item"></a>Item
**Description:**  
A função Item devolve um item de uma cadeia/atributo de vários valores.

**Sintaxe:**  
`var Item(mvstr attribute, num index)`

* atributo: atributo multivalorizado
* índice: índice para um item na cadeia multi-valorizada.

**Observações:**  
A função item é útil juntamente com a função Contém, uma vez que esta última função devolve o índice a um item no atributo multivalorizado.

Um erro se o índice estiver fora dos limites.

**Exemplo:**  
`Mid(Item([proxyAddresses],Contains([proxyAddresses], "SMTP:")),6)`  
Devolve o endereço de e-mail principal.

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
**Função:**<br> Aderir (separador, fonte1, fonte2, ...)

**Description:**<br> A adesão() é semelhante ao apêndice, exceto que pode combinar múltiplos valores de cadeia de **origem** numa única corda, e cada valor será separado por uma corda **de separador.**

Se um dos valores de origem for um atributo de vários valores, então cada valor nesse atributo será unido, separado pelo valor do separador.

**Parâmetros:**<br> 

   | Nome | Obrigatório/ Repetição | Tipo | Notas |
   | --- | --- | --- | --- |
   | **separador** |Necessário |String |Cordas usadas para separar os valores de origem quando são concatenadas numa só corda. Pode ser "" se não for necessário separador. |
   | **fonte1 ... fonteN** |Obrigatório, número variável de vezes |String |Valores de cordas a unir. |

---
### <a name="left"></a>Esquerda
**Description:**  
A função Esquerda devolve um número especificado de caracteres da esquerda de uma corda.

**Sintaxe:**  
`str Left(str string, num NumChars)`

* cadeia: a corda para devolver os caracteres de
* NumChars: um número que identifica o número de caracteres a regressar do início (à esquerda) da corda

**Observações:**  
Uma corda contendo os primeiros personagens de numchars em cadeia:

* Se numChars = 0, volte a ficar vazio.
* Se numChars < 0, volte a ser corda de entrada.
* Se a corda for nula, volte a ficar vazia.

Se a corda contiver menos caracteres do que o número especificado em numChars, uma corda idêntica à corda (isto é, contendo todos os caracteres no parâmetro 1) é devolvida.

**Exemplo:**  
`Left("John Doe", 3)`  
Devoluções. `Joh`

---
### <a name="mid"></a>Mid
**Função:**<br> Médio (fonte, início, comprimento)

**Description:**<br> Devolve uma subcadeia do valor de origem. Uma subcadeia é uma cadeia que contém apenas alguns dos caracteres da cadeia de origem.

**Parâmetros:**<br> 

   | Nome | Obrigatório/ Repetição | Tipo | Notas |
   | --- | --- | --- | --- |
   | **fonte** |Necessário |String |Normalmente o nome do atributo. |
   | **começar** |Necessário |número inteiro |Índice na cadeia **de origem** onde deve iniciar a subcadeia. O primeiro personagem na cadeia terá índice de 1, segundo personagem terá índice 2, e assim por diante. |
   | **comprimento** |Necessário |número inteiro |Comprimento da subcorda. Se o comprimento terminar fora da cadeia de **origem,** a função devolverá o substring do índice de **arranque** até ao fim da cadeia de **origem.** |

---
### <a name="normalizediacritics"></a>Normalizar Os críticos da Diadia
**Função:**<br> NormalizarOsDiacritics (fonte)

**Description:**<br> Requer um argumento de corda. Devolve a corda, mas com quaisquer caracteres diacríticos substituídos por caracteres não diacríticos equivalentes. Normalmente usado para converter os primeiros nomes e apelidos que contêm caracteres diacríticos (marcas de sotaque) em valores legais que podem ser usados em vários identificadores de utilizador, tais como nomes principais do utilizador, nomes de contas SAM e endereços de e-mail.

**Parâmetros:**<br> 

   | Nome | Obrigatório/ Repetição | Tipo | Notas |
   | --- | --- | --- | --- |
   | **fonte** |Necessário |String | Normalmente um primeiro nome ou apelido. |

---
### <a name="not"></a>Não
**Função:**<br> Não (fonte)

**Description:**<br> Inverte o valor booleano da **fonte.** Se o valor **de origem** for "*Verdadeiro",* devolve "*Falso*". Caso contrário, retorna "*Verdadeiro*".

**Parâmetros:**<br> 

   | Nome | Obrigatório/ Repetição | Tipo | Notas |
   | --- | --- | --- | --- |
   | **fonte** |Necessário |Corda Booleana |Os valores **de origem** esperados são "Verdadeiros" ou "Falsos". |

---
### <a name="removeduplicates"></a>Remover Duplicatos
**Description:**  
A função RemoveDuplicates pega numa cadeia de vários valores e certifica-se de que cada valor é único.

**Sintaxe:**  
`mvstr RemoveDuplicates(mvstr attribute)`

**Exemplo:**  
`RemoveDuplicates([proxyAddresses])`  
Devolve um atributo proxyAddress sanitizado onde todos os valores duplicados foram removidos.

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

   | Nome | Obrigatório/ Repetição | Tipo | Notas |
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

> [!NOTE]
> - Esta é uma função de alto nível, não pode ser aninhada.
> - Esta função não pode ser aplicada a atributos que tenham uma precedência correspondente.  
> - Esta função destina-se apenas a ser utilizada para criações de entrada. Ao utilizá-lo com um atributo, detete a propriedade **De Mapeamento aplicar** apenas durante a **criação de objetos**.
> - Esta função é atualmente suportada apenas para "Workday to Ative Directory User Provisioning". Não pode ser utilizado com outros pedidos de provisionamento. 


**Parâmetros:**<br> 

   | Nome | Obrigatório/ Repetição | Tipo | Notas |
   | --- | --- | --- | --- |
   | **únicoValueRule1 ... únicoValueRuleN** |Pelo menos 2 são necessários, sem limite superior |String | Lista de regras únicas de geração de valor para avaliar. |


---
### <a name="singleapproleassignment"></a>Atribuição singleAppRole
**Função:**<br> Atribuição de funções simples ([atribuição de aplicações])

**Description:**<br> Devolve uma única appRoleAssignment da lista de todas as aplicaçõesAtribuídas atribuídas a um utilizador para uma determinada aplicação. Esta função é necessária para converter o objeto de appRoleAssignments numa única cadeia de nomes. Note que a melhor prática é garantir que apenas uma appRoleAssignment é atribuída a um utilizador de cada vez, e se várias funções forem atribuídas, a cadeia de funções devolvida pode não ser previsível. 

**Parâmetros:**<br> 

  | Nome | Obrigatório/ Repetição | Tipo | Notas |
  |--- | --- | --- | --- |
  | **[appRoleAssignments]** |Necessário |String |**[appRoleAssignments]** objeto. |

---
### <a name="split"></a>Dividir
**Função:**<br> Divisão (fonte, delimitador)

**Description:**<br> Divide uma cadeia numa matriz de vários valores, utilizando o carácter delimitador especificado.

**Parâmetros:**<br> 

   | Nome | Obrigatório/ Repetição | Tipo | Notas |
   | --- | --- | --- | --- |
   | **fonte** |Necessário |String |valor **de origem** para atualização. |
   | **delimitador** |Necessário |String |Especifica o personagem que será usado para dividir a corda (exemplo: "") |

---
### <a name="stringfromsid"></a>StringFromSid
**Description:**  
A função StringFromSid converte um matriz de byte contendo um identificador de segurança numa corda.

**Sintaxe:**  
`str StringFromSid(bin ObjectSID)`  

---
### <a name="stripspaces"></a>StripSpaces
**Função:**<br> StripSpaces (fonte)

**Description:**<br> Remove todos os caracteres de espaço (" "") da cadeia de origem.

**Parâmetros:**<br> 

   | Nome | Obrigatório/ Repetição | Tipo | Notas |
   | --- | --- | --- | --- |
   | **fonte** |Necessário |String |valor **de origem** para atualização. |

---
### <a name="switch"></a>Comutador
**Função:**<br> Switch (fonte, defaultValue, key1, value1, key2, value2, ...)

**Description:**<br> Quando o valor **de origem** corresponde a uma **chave,** devolve **valor** para essa **tecla**. Se o valor de **origem** não corresponder a nenhuma tecla, devolve **o predefiniçãoValor**.  **Os** parâmetros de chave e **valor** devem sempre vir em pares. A função espera sempre um número par de parâmetros.

**Parâmetros:**<br> 

   | Nome | Obrigatório/ Repetição | Tipo | Notas |
   | --- | --- | --- | --- |
   | **fonte** |Necessário |String |**Valor de origem** para verificar. |
   | **defaultValor** |Opcional |String |Valor predefinido a ser usado quando a fonte não corresponde a nenhuma tecla. Pode ser corda vazia (""). |
   | **chave** |Necessário |String |**Chave** para comparar o valor **de origem** com. |
   | **valor** |Necessário |String |Valor de substituição da **fonte** que combina com a chave. |

---
### <a name="tolower"></a>ToLower
**Função:**<br> ToLower (fonte, cultura)

**Description:**<br> Pega num valor de cadeia de *origem* e converte-o para minúscula utilizando as regras de cultura especificadas. Se não houver informações de *cultura* especificadas, então utilizará a cultura Invariante.

**Parâmetros:**<br> 

   | Nome | Obrigatório/ Repetição | Tipo | Notas |
   | --- | --- | --- | --- |
   | **fonte** |Necessário |String |Normalmente nome do atributo do objeto de origem |
   | **cultura** |Opcional |String |O formato para o nome da cultura baseado no RFC 4646 é *languagecode2-country/regioncode2*, onde o *código linguístico2* é o código linguístico de duas letras e o *código de país/região2* é o código de subcultura de duas letras. Exemplos incluem ja-JP para japonês (Japão) e en-US para inglês (Estados Unidos). Nos casos em que não esteja disponível um código linguístico de duas letras, é utilizado um código de três letras derivado da ISO 639-2.|

---

### <a name="toupper"></a>Toia
**Função:**<br> ToUpper (fonte, cultura)

**Description:**<br> Pega num valor de cadeia de *origem* e converte-o em maiúsculas usando as regras de cultura especificadas. Se não houver informações de *cultura* especificadas, então utilizará a cultura Invariante.

**Parâmetros:**<br> 

  | Nome | Obrigatório/ Repetição | Tipo | Notas |
  | --- | --- | --- | --- |
  | **fonte** |Necessário |String |Normalmente, o nome do atributo do objeto de origem. |
  | **cultura** |Opcional |String |O formato para o nome da cultura baseado no RFC 4646 é *languagecode2-country/regioncode2*, onde o *código linguístico2* é o código linguístico de duas letras e o *código de país/região2* é o código de subcultura de duas letras. Exemplos incluem ja-JP para japonês (Japão) e en-US para inglês (Estados Unidos). Nos casos em que não esteja disponível um código linguístico de duas letras, é utilizado um código de três letras derivado da ISO 639-2.|

---

### <a name="trim"></a>Trim
**Description:**  
A função Trim remove espaços brancos de condução e de saque de uma corda.

**Sintaxe:**  
`str Trim(str value)`  

**Exemplo:**  
`Trim(" Test ")`  
Devolve "Teste".

`Trim([proxyAddresses])`  
Remove espaços de liderança e de trailing para cada valor no atributo proxyAddress.

---
### <a name="word"></a>Word
**Description:**  
A função Word devolve uma palavra contida numa corda, baseada em parâmetros que descrevem os delimitadores a usar e o número de palavra sabotado.

**Sintaxe:**  
`str Word(str string, num WordNumber, str delimiters)`

* corda: a corda para devolver uma palavra de.
* Número de palavras: um número que identifica o número de palavra seletiva deve ser retornado.
* delimitadores: uma corda que representa o ou os delimitadores que devem ser utilizados para identificar palavras

**Observações:**  
Cada cadeia de caracteres em cadeia separada si uma das personagens em delimitadores é identificada como palavras:

* Se o número < 1, devolve corda vazia.
* Se a corda for nula, devolve corda vazia.

Se a corda contiver menos de palavras numéricas, ou a corda não contiver palavras identificadas pelos delimitadores, uma corda vazia é devolvida.

**Exemplo:**  
`Word("The quick brown fox",3," ")`  
Devoluções "marrom"

`Word("This,string!has&many separators",3,",!&#")`  
Voltaria "tem"

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


## <a name="next-steps"></a>Passos seguintes 

- [O que é o aprovisionamento?](what-is-provisioning.md)
- [O que é o aprovisionamento na cloud do Azure AD Connect?](what-is-cloud-provisioning.md)
