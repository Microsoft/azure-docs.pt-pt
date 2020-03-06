---
title: Azure AD Connect expressões de fornecimento de nuvem e referência de função
description: Referência
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
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78298620"
---
# <a name="writing-expressions-for-attribute-mappings-in-azure-active-directory"></a>Expressões de escrita para mapeamento de atributos no Diretório Ativo Azure
Ao configurar o fornecimento de nuvens, um dos tipos de mapeamento de atributos que pode especificar é um mapeamento de expressão. 

O mapeamento de expressão permite-lhe personalizar atributos usando uma expressão semelhante a um script.  Isto permite-lhe transformar os dados no local num valor novo ou diferente.  Por exemplo, pode querer combinar dois atributos num único atributo porque este único atributo é usado por uma das suas aplicações na nuvem.

O seguinte documento cobrirá as expressões semelhantes ao script que são usadas para transformar os dados.  Isto é apenas uma parte do processo.  Em seguida, você precisará usar esta expressão e colocá-la em um pedido web para o seu inquilino.  Para mais informações sobre isso ver [Transformações](how-to-transformation.md)

## <a name="syntax-overview"></a>Visão geral da sintaxe
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
| Lista de funções | Descrição |
|-----|----|
|[Apêndice](#append)|Assume um valor de cadeia de caracteres de origem e acrescenta o sufixo ao final do mesmo.|
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
|[FormatoDateTime](#formatdatetime) |Precisa de uma cadeia de data de um formato e os converte num formato diferente.| 
|[GUIA](#guid)|A função Guid gera um novo GUID aleatório.|           
|[IIF](#iif)|A função IIF devolve um conjunto de valores possíveis com base numa condição especificada.|
|[Instr](#instr)|A função InStr encontra a primeira ocorrência de uma subcadeia numa corda.|
|[Isnull](#isnull)|Se a expressão avaliar a Nula, então a função IsNull retorna verdadeira.|
|[IsnullorEmpty](#isnullorempty)|Se a expressão for nula ou uma corda vazia, então a função IsNullOrEmpty retorna verdadeira.|         
|[IsPresent](#ispresent)|Se a expressão avaliar para uma cadeia que não é Nula e não está vazia, então a função IsPresent retorna verdadeira.|    
|[IsString](#isstring)|Se a expressão pode ser avaliada para um tipo de corda, então a função IsString avalia para True.|
|[Item](#item)|A função Item devolve um item de uma cadeia/atributo de vários valores.|
|[Associar](#join) |A adesão() é semelhante ao apêndice, exceto que pode combinar múltiplos valores de cadeia de **origem** numa única corda, e cada valor será separado por uma corda **de separador.**| 
|[Esquerda](#left)|A função Esquerda devolve um número especificado de caracteres da esquerda de uma corda.|
|[Meados](#mid) |Devolve uma subcadeia do valor de origem. Uma subcadeia é uma cadeia que contém apenas alguns dos caracteres da cadeia de origem.|
|[Normalizar Os críticos da Diadia](#normalizediacritics)|Requer um argumento de cadeia de caracteres. Devolve a cadeia de caracteres, mas com nenhum dos caracteres diacritical substituídos por carateres não diacritical equivalentes.|
|[Não.](#not) |Inverte o valor booleano da **fonte.** Se o valor **de origem** for "*Verdadeiro",* devolve "*Falso*". Caso contrário, retorna "*Verdadeiro*".| 
|[Remover Duplicatos](#removeduplicates)|A função RemoveDuplicates pega numa cadeia de vários valores e certifica-se de que cada valor é único.| 
|[Substituir](#replace) |Substitui os valores dentro de uma cadeia de caracteres. | 
|[SelecioneUniqueValue](#selectuniquevalue)|Requer um mínimo de dois argumentos, que são definidas usando expressões de regras de geração de valor exclusivo. A função avalia cada regra e, em seguida, verifica o valor gerado para exclusividade na aplicação/diretório de destino.| 
|[Atribuição singleAppRole](#singleapproleassignment)|Devolve uma única appRoleAssignment da lista de todas as aplicaçõesAtribuídas atribuídas a um utilizador para uma determinada aplicação.| 
|[Divisão](#split)|Divide uma cadeia numa matriz de vários valores, utilizando o carácter delimitador especificado.|
|[StringFromSID](#stringfromsid)|A função StringFromSid converte um matriz de byte contendo um identificador de segurança numa corda.| 
|[StripSpaces](#stripspaces) |Remove todo o espaço ("") carateres da cadeia de caracteres de origem.| 
|[Mudar](#switch)|Quando o valor **de origem** corresponde a uma **chave,** devolve **valor** para essa **tecla**. | 
|[ToLower](#tolower)|Pega num valor de cadeia de *origem* e converte-o para minúscula utilizando as regras de cultura especificadas.| 
|[Toia](#toupper)|Pega num valor de cadeia de *origem* e converte-o em maiúsculas usando as regras de cultura especificadas.|
|[Aparar](#trim)|A função Trim remove espaços brancos de condução e de saque de uma corda.|
|[Palavra](#word)|A função Word devolve uma palavra contida numa corda, baseada em parâmetros que descrevem os delimitadores a usar e o número de palavra sabotado.|

---
### <a name="append"></a>Acrescentar
**Função:**<br> Append(Source, suffix)

**Descrição:**<br> Assume um valor de cadeia de caracteres de origem e acrescenta o sufixo ao final do mesmo.

**Parâmetros:**<br> 

   | Nome | Obrigatório / repetidos | Tipo | Notas |
   | --- | --- | --- | --- |
   | **fonte** |Necessário |String |Normalmente, o nome do atributo de objeto de origem. |
   | **sufixo** |Necessário |String |A cadeia a acrescentar ao final do valor de origem. |

---
### <a name="bitand"></a>Bitand
**Descrição:**  
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
**Descrição:**  
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
**Descrição:**  
A função ConvertFromBase64 converte o valor codificado de base 64 especificado para uma cadeia regular.

**Sintaxe:**  
`str ConvertFromBase64(str source)` - assume Unicode para codificação  
`str ConvertFromBase64(str source, enum Encoding)`

* fonte: Cadeia codificada Base64  
* Codificação: Unicode, ASCII, UTF8

**Exemplo**  
`ConvertFromBase64("SABlAGwAbABvACAAdwBvAHIAbABkACEA")`  
`ConvertFromBase64("SGVsbG8gd29ybGQh", UTF8)`

Ambos os exemplos voltam "*Olá mundo!* "

---
### <a name="converttobase64"></a>ConvertToBase64
**Descrição:**  
A função ConvertToBase64 converte uma cadeia para uma cadeia Unicode base64.  
Converte o valor de um conjunto de inteiros para a sua representação de cordas equivalente que é codificada com base-64 dígitos.

**Sintaxe:**  
`str ConvertToBase64(str source)`

**Exemplo:**  
`ConvertToBase64("Hello world!")`  
Devoluções "SABlAGwAbABvACAAdwBvAHIAbABkACEA"

---
### <a name="converttoutf8hex"></a>ConvertToUTF8Hex
**Descrição:**  
A função ConvertToUTF8Hex converte uma cadeia para um valor codificado uTF8 Hex.

**Sintaxe:**  
`str ConvertToUTF8Hex(str source)`

**Observações:**  
O formato de saída desta função é utilizado pelo Azure Ative Directory como formato de atribuição dN.

**Exemplo:**  
`ConvertToUTF8Hex("Hello world!")`  
Returns 48656C6C6F20776F726C6421

---
### <a name="count"></a>Contagem
**Descrição:**  
A função Count devolve o número de elementos num atributo multivalorizado

**Sintaxe:**  
`num Count(mvstr attribute)`

---
### <a name="cstr"></a>CStr
**Descrição:**  
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
### <a name="datefromnum"></a>DateFromNum
**Descrição:**  
A função DateFromNum converte um valor no formato de data da AD para um tipo DateTime.

**Sintaxe:**  
`dt DateFromNum(num value)`

**Exemplo:**  
`DateFromNum([lastLogonTimestamp])`  
`DateFromNum(129699324000000000)`  
Devoluções DataTime representando 2012-01-01 23:00:00

---
### <a name="dncomponent"></a>Componente DNComponente
**Descrição:**  
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
**Descrição:**  
A função Error é utilizada para devolver um erro personalizado.

**Sintaxe:**  
`void Error(str ErrorMessage)`

**Exemplo:**  
`IIF(IsPresent([accountName]),[accountName],Error("AccountName is required"))`  
Se a conta de atributonome não estiver presente, deite um erro no objeto.

---
### <a name="formatdatetime"></a>FormatDateTime
**Função:**<br> FormatDateTime (origem, inputFormat, outputFormat)

**Descrição:**<br> Precisa de uma cadeia de data de um formato e os converte num formato diferente.

**Parâmetros:**<br> 

   | Nome | Obrigatório / repetidos | Tipo | Notas |
   | --- | --- | --- | --- |
   | **fonte** |Necessário |String |Normalmente, o nome do atributo de objeto de origem. |
   | **inputFormat** |Necessário |String |Formato esperado do valor de origem. Para formatos suportados, consulte [https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx). |
   | **outputFormat** |Necessário |String |Formato da data de saída. |

---
### <a name="guid"></a>Guid
**Descrição:**  
A função Guid gera um novo GUID aleatório

**Sintaxe:**  
`str Guid()`

---
### <a name="iif"></a>IIF
**Descrição:**  
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
**Descrição:**  
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
### <a name="isnull"></a>IsNull
**Descrição:**  
Se a expressão avaliar a Nula, então a função IsNull retorna verdadeira.

**Sintaxe:**  
`bool IsNull(var Expression)`

**Observações:**  
Para um atributo, um Nulo é expresso pela ausência do atributo.

**Exemplo:**  
`IsNull([displayName])`  
Devoluções Verdadeiras se o atributo não estiver presente no CS ou MV.

---
### <a name="isnullorempty"></a>IsNullOrEmpty
**Descrição:**  
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
**Descrição:**  
Se a expressão avaliar para uma cadeia que não é Nula e não está vazia, então a função IsPresent retorna verdadeira.

**Sintaxe:**  
`bool IsPresent(var expression)`

**Observações:**  
O inverso desta função chama-se IsNullOrEmpty.

**Exemplo:**  
`Switch(IsPresent([directManager]),[directManager], IsPresent([skiplevelManager]),[skiplevelManager], IsPresent([director]),[director])`

---
### <a name="item"></a>Item
**Descrição:**  
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
**Descrição:**  
Se a expressão pode ser avaliada para um tipo de corda, então a função IsString avalia para True.

**Sintaxe:**  
`bool IsString(var expression)`

**Observações:**  
Usado para determinar se o CStr() pode ser bem sucedido para analisar a expressão.

---
### <a name="join"></a>Associar
**Função:**<br> Junte-se (separador, source1 source2,...)

**Descrição:**<br> A adesão() é semelhante ao apêndice, exceto que pode combinar múltiplos valores de cadeia de **origem** numa única corda, e cada valor será separado por uma corda **de separador.**

Se um dos valores de origem for um atributo de vários valores, então cada valor nesse atributo será unido, separado pelo valor do separador.

**Parâmetros:**<br> 

   | Nome | Obrigatório / repetidos | Tipo | Notas |
   | --- | --- | --- | --- |
   | **separador** |Necessário |String |Cadeia utilizada para separar os valores de origem quando eles são concatenados numa cadeia de caracteres. Pode ser "" se nenhum separador é necessário. |
   | **fonte1 ... fonteN** |Variável-número de vezes, necessário |String |Cadeia de valores a ser unidas. |

---
### <a name="left"></a>Esquerda
**Descrição:**  
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
Devoluções `Joh`.

---
### <a name="mid"></a>Mid
**Função:**<br> Mid (origem, início, comprimento)

**Descrição:**<br> Devolve uma subcadeia do valor de origem. Uma subcadeia é uma cadeia que contém apenas alguns dos caracteres da cadeia de origem.

**Parâmetros:**<br> 

   | Nome | Obrigatório / repetidos | Tipo | Notas |
   | --- | --- | --- | --- |
   | **fonte** |Necessário |String |Normalmente, o nome do atributo. |
   | **start** |Necessário |número inteiro |Índice na cadeia **de origem** onde deve iniciar a subcadeia. Primeiro caractere na cadeia de caracteres terão o índice de 1, o segundo caráter terão índice 2 e assim por diante. |
   | **comprimento** |Necessário |número inteiro |Comprimento da subcadeia. Se o comprimento terminar fora da cadeia de **origem,** a função devolverá o substring do índice de **arranque** até ao fim da cadeia de **origem.** |

---
### <a name="normalizediacritics"></a>NormalizeDiacritics
**Função:**<br> NormalizeDiacritics(source)

**Descrição:**<br> Requer um argumento de cadeia de caracteres. Devolve a cadeia de caracteres, mas com nenhum dos caracteres diacritical substituídos por carateres não diacritical equivalentes. Normalmente, utilizada para converter os nomes de primeiro e último carateres diacritical (marcas de acento sem) em valores legais que podem ser utilizadas em vários identificadores de utilizador, tais como nomes de principal de utilizador, nomes de conta SAM e endereços de e-mail.

**Parâmetros:**<br> 

   | Nome | Obrigatório / repetidos | Tipo | Notas |
   | --- | --- | --- | --- |
   | **fonte** |Necessário |String | Normalmente um primeiro nome ou apelido. |

---
### <a name="not"></a>Não
**Função:**<br> Not(Source)

**Descrição:**<br> Inverte o valor booleano da **fonte.** Se o valor **de origem** for "*Verdadeiro",* devolve "*Falso*". Caso contrário, retorna "*Verdadeiro*".

**Parâmetros:**<br> 

   | Nome | Obrigatório / repetidos | Tipo | Notas |
   | --- | --- | --- | --- |
   | **fonte** |Necessário |Booleano da cadeia |Os valores **de origem** esperados são "Verdadeiros" ou "Falsos". |

---
### <a name="removeduplicates"></a>RemoveDuplicates
**Descrição:**  
A função RemoveDuplicates pega numa cadeia de vários valores e certifica-se de que cada valor é único.

**Sintaxe:**  
`mvstr RemoveDuplicates(mvstr attribute)`

**Exemplo:**  
`RemoveDuplicates([proxyAddresses])`  
Devolve um atributo proxyAddress sanitizado onde todos os valores duplicados foram removidos.

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
   | **fonte** |Necessário |String |Normalmente, o nome do atributo do objeto **de origem.** |
   | **oldValue** |Opcional |String |Valor a substituir na **fonte** ou **no modelo.** |
   | **regexPattern** |Opcional |String |Padrão Regex para o valor a substituir na **fonte**. Ou, quando é utilizado **o nome propertyname** substituto, padrão para extrair valor da **substituiçãoPropertyName**. |
   | **regexGroupName** |Opcional |String |Nome do grupo dentro do **regexPattern**. Só quando for utilizado **o substitutoPropertyName,** extrairemos valor deste grupo como **substitutoValor** da **substituiçãoPropertyName**. |
   | **substituiçãoValor** |Opcional |String |Novo valor para substituir um antigo com. |
   | **substituiçãoNome atribuído** |Opcional |String |Nome do atributo a utilizar para valor de substituição |
   | **modelo** |Opcional |String |Quando o valor do **modelo** for fornecido, procuraremos **por oldValue** dentro do modelo e substituí-lo-emos pelo valor **de origem.** |

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
   | **únicoValueRule1 ... únicoValueRuleN** |Pelo menos 2 são vinculados a necessário, não superior |String | Lista de regras únicas de geração de valor para avaliar. |


---
### <a name="singleapproleassignment"></a>SingleAppRoleAssignment
**Função:**<br> SingleAppRoleAssignment([appRoleAssignments])

**Descrição:**<br> Devolve uma única appRoleAssignment da lista de todas as aplicaçõesAtribuídas atribuídas a um utilizador para uma determinada aplicação. Esta função é necessária para converter o objeto de appRoleAssignments numa única cadeia de nomes. Note que a melhor prática é garantir que apenas uma appRoleAssignment é atribuída a um utilizador de cada vez, e se várias funções forem atribuídas, a cadeia de funções devolvida pode não ser previsível. 

**Parâmetros:**<br> 

  | Nome | Obrigatório / repetidos | Tipo | Notas |
  |--- | --- | --- | --- |
  | **[appRoleAssignments]** |Necessário |String |**[appRoleAssignments]** objeto. |

---
### <a name="split"></a>Dividir
**Função:**<br> Divisão (fonte, delimitador)

**Descrição:**<br> Divide uma cadeia numa matriz de vários valores, utilizando o carácter delimitador especificado.

**Parâmetros:**<br> 

   | Nome | Obrigatório / repetidos | Tipo | Notas |
   | --- | --- | --- | --- |
   | **fonte** |Necessário |String |valor **de origem** para atualização. |
   | **delimitador** |Necessário |String |Especifica o personagem que será usado para dividir a corda (exemplo: "") |

---
### <a name="stringfromsid"></a>StringFromSid
**Descrição:**  
A função StringFromSid converte um matriz de byte contendo um identificador de segurança numa corda.

**Sintaxe:**  
`str StringFromSid(bin ObjectSID)`  

---
### <a name="stripspaces"></a>StripSpaces
**Função:**<br> StripSpaces(source)

**Descrição:**<br> Remove todo o espaço ("") carateres da cadeia de caracteres de origem.

**Parâmetros:**<br> 

   | Nome | Obrigatório / repetidos | Tipo | Notas |
   | --- | --- | --- | --- |
   | **fonte** |Necessário |String |valor **de origem** para atualização. |

---
### <a name="switch"></a>Comutador
**Função:**<br> Comutador (origem, defaultValue, chave1, value1, chave2, value2...)

**Descrição:**<br> Quando o valor **de origem** corresponde a uma **chave,** devolve **valor** para essa **tecla**. Se o valor de **origem** não corresponder a nenhuma tecla, devolve **o predefiniçãoValor**.  **Os** parâmetros de chave e **valor** devem sempre vir em pares. A função espera sempre um número par de parâmetros.

**Parâmetros:**<br> 

   | Nome | Obrigatório / repetidos | Tipo | Notas |
   | --- | --- | --- | --- |
   | **fonte** |Necessário |String |**Valor de origem** para verificar. |
   | **defaultValor** |Opcional |String |Valor predefinido a utilizar quando a origem não corresponde a quaisquer chaves. Pode ser uma cadeia de caracteres vazia (""). |
   | **chave** |Necessário |String |**Chave** para comparar o valor **de origem** com. |
   | **valor** |Necessário |String |Valor de substituição da **fonte** que combina com a chave. |

---
### <a name="tolower"></a>toLower
**Função:**<br> ToLower (fonte, cultura)

**Descrição:**<br> Pega num valor de cadeia de *origem* e converte-o para minúscula utilizando as regras de cultura especificadas. Se não houver informações de *cultura* especificadas, então utilizará a cultura Invariante.

**Parâmetros:**<br> 

   | Nome | Obrigatório / repetidos | Tipo | Notas |
   | --- | --- | --- | --- |
   | **fonte** |Necessário |String |Normalmente, o nome do atributo de objeto de origem |
   | **cultura** |Opcional |String |O formato para o nome da cultura baseado no RFC 4646 é *languagecode2-country/regioncode2*, onde o *código linguístico2* é o código linguístico de duas letras e o *código de país/região2* é o código de subcultura de duas letras. Exemplos incluem ja-JP para japonês (Japão) e en-US para inglês (Estados Unidos). Nos casos em que não esteja disponível um código linguístico de duas letras, é utilizado um código de três letras derivado da ISO 639-2.|

---

### <a name="toupper"></a>ToUpper
**Função:**<br> ToUpper (fonte, cultura)

**Descrição:**<br> Pega num valor de cadeia de *origem* e converte-o em maiúsculas usando as regras de cultura especificadas. Se não houver informações de *cultura* especificadas, então utilizará a cultura Invariante.

**Parâmetros:**<br> 

  | Nome | Obrigatório / repetidos | Tipo | Notas |
  | --- | --- | --- | --- |
  | **fonte** |Necessário |String |Normalmente, o nome do atributo de objeto de origem. |
  | **cultura** |Opcional |String |O formato para o nome da cultura baseado no RFC 4646 é *languagecode2-country/regioncode2*, onde o *código linguístico2* é o código linguístico de duas letras e o *código de país/região2* é o código de subcultura de duas letras. Exemplos incluem ja-JP para japonês (Japão) e en-US para inglês (Estados Unidos). Nos casos em que não esteja disponível um código linguístico de duas letras, é utilizado um código de três letras derivado da ISO 639-2.|

---

### <a name="trim"></a>aparar
**Descrição:**  
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
**Descrição:**  
A função Word devolve uma palavra contida numa corda, baseada em parâmetros que descrevem os delimitadores a usar e o número de palavra sabotado.

**Sintaxe:**  
`str Word(str string, num WordNumber, str delimiters)`

* corda: a corda para devolver uma palavra de.
* Número de palavras: um número que identifica o número de palavra seletiva deve ser retornado.
* delimitadores: uma corda que representa o ou os delimitadores que devem ser utilizados para identificar palavras

**Observações:**  
Cada cadeia de caracteres em cadeia separada si uma das personagens em delimitadores é identificada como palavras:

* Se o número e 1, devolver a corda vazia.
* Se a corda for nula, devolve corda vazia.

Se a corda contiver menos de palavras numéricas, ou a corda não contiver palavras identificadas pelos delimitadores, uma corda vazia é devolvida.

**Exemplo:**  
`Word("The quick brown fox",3," ")`  
Devoluções "marrom"

`Word("This,string!has&many separators",3,",!&#")`  
Voltaria "tem"

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


## <a name="next-steps"></a>Passos seguintes 

- [O que é o provisionamento?](what-is-provisioning.md)
- [O que é o fornecimento de nuvem Azure AD Connect?](what-is-cloud-provisioning.md)
