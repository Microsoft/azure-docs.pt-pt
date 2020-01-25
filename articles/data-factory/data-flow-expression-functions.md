---
title: Funções de expressão no fluxo de dados de mapeamento
description: Saiba mais sobre as funções de expressão no fluxo de dados de mapeamento.
author: kromerm
ms.author: makromer
manager: anandsub
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 02/15/2019
ms.openlocfilehash: 68771ee3d2ae2d43245bd217bedcf59b987786f1
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/24/2020
ms.locfileid: "76716731"
---
# <a name="data-transformation-expressions-in-mapping-data-flow"></a>Expressões de transformação de dados no fluxo de dados de mapeamento 

## <a name="expression-functions"></a>Funções de expressão

No Data Factory, use a linguagem de expressão do recurso de fluxo de dados de mapeamento para configurar as transformações de dados.
___
### <code>abs</code>
<code><b>abs(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
Valor absoluto de um número.
* ``abs(-20) -> 20``
* ``abs(10) -> 10``
___
### <code>acos</code>
<code><b>acos(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Calcula um valor inverso cosseno * ``acos(1) -> 0.0``
___
### <code>add</code>
<code><b>add(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
Adiciona um par de cadeias de caracteres ou números. Adiciona uma data a um número de dias. Adiciona uma duração a um carimbo de data/hora. Acrescenta uma matriz de tipo semelhante para outra. O mesmo que o 
* ``10 + 20 -> 30``* ``add(10, 20) -> 30``+ 
* ``add('ice', 'cream') -> 'icecream'``
* ``[10, 20] + [30, 40] -> [10, 20, 30, 40]``
* ``add(toDate('2012-12-12'), 3) -> toDate('2012-12-15')``
* ``'ice' + 'cream' + ' cone' -> 'icecream cone'``
* ``toDate('2012-12-12') + 3 -> toDate('2012-12-15')``  
* ``toTimestamp('2019-02-03 05:19:28.871', 'yyyy-MM-dd HH:mm:ss.SSS') + (days(1) + hours(2) - seconds(10)) -> toTimestamp('2019-02-04 07:19:18.871', 'yyyy-MM-dd HH:mm:ss.SSS')``
___
### <code>addDays</code>
<code><b>addDays(<i>&lt;date/timestamp&gt;</i> : datetime, <i>&lt;days to add&gt;</i> : integral) => datetime</b></code><br/><br/>
Adicione dias a uma data ou carimbo de hora. O mesmo que o + operador para data * ``addDays(toDate('2016-08-08'), 1) -> toDate('2016-08-09')``
___
### <code>addMonths</code>
<code><b>addMonths(<i>&lt;date/timestamp&gt;</i> : datetime, <i>&lt;months to add&gt;</i> : integral, [<i>&lt;value3&gt;</i> : string]) => datetime</b></code><br/><br/>
Adicione meses a uma data ou timestamp. Você pode opcionalmente passar um * ``addMonths(toDate('2016-08-31'), 1) -> toDate('2016-09-30')``de tempo 
* ``addMonths(toTimestamp('2016-09-30 10:10:10'), -1) -> toTimestamp('2016-08-31 10:10:10')``
___
### <code>and</code>
<code><b>and(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : boolean) => boolean</b></code><br/><br/>
Operador AND lógico. O mesmo que * ``and(true, false) -> false``
* ``true && false -> false``
___
### <code>asin</code>
<code><b>asin(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Calcula um valor sino inverso * ``asin(0) -> 0.0``
___
### <code>atan</code>
<code><b>atan(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Calcula um valor tanggente inverso * ``atan(0) -> 0.0``
___
### <code>atan2</code>
<code><b>atan2(<i>&lt;value1&gt;</i> : number, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Devolve o ângulo em radians entre o eixo x positivo de um plano e o ponto dado pelas coordenadas * ``atan2(0, 0) -> 0.0``
___
### <code>byName</code>
<code><b>byName(<i>&lt;column name&gt;</i> : string, [<i>&lt;stream name&gt;</i> : string]) => any</b></code><br/><br/>
Seleciona um valor de coluna por nome no fluxo. Você pode passar um nome de fluxo opcional como o segundo argumento. Se houver várias correspondências, a primeira correspondência será retornada. Se nenhuma correspondência retornar um valor nulo. O valor devolvido tem de ser convertido por uma das funções de conversão do tipo (TO_DATE, TO_STRING...).  Os nomes das colunas conhecidos na hora do design devem ser abordados apenas pelo seu nome. As inputs computadas não são suportadas, mas pode utilizar substituições de parâmetros * ``toString(byName('parent'))``
* ``toLong(byName('income'))``
* ``toBoolean(byName('foster'))``
* ``toLong(byName($debtCol))`` 
* ``toString(byName('Bogus Column'))``
* ``toString(byName('Bogus Column', 'DeriveStream'))`` 
___
### <code>byPosition</code> 
<code><b>byPosition(<i>&lt;position&gt;</i> : integer) => any</b></code><br/><br/>
Seleciona um valor de coluna por sua posição relativa (1 com base) no fluxo. Se a posição estiver fora dos limites, ela retornará um valor nulo. O valor devolvido tem de ser convertido por uma das funções de conversão do tipo (TO_DATE, TO_STRING...) As inputs computadas não são suportadas, mas pode utilizar substituições de parâmetros * ``toString(byPosition(1))``
* ``toDecimal(byPosition(2), 10, 2)``
* ``toBoolean(byName(4))``
* ``toString(byName($colName))`` 
* ``toString(byPosition(1234))``
___
### <code>case</code>
<code><b>case(<i>&lt;condition&gt;</i> : boolean, <i>&lt;true_expression&gt;</i> : any, <i>&lt;false_expression&gt;</i> : any, ...) => any</b></code><br/><br/>
Com base em condições alternativas, aplica-se um valor ou outro. Se o número de inputs for igual, o outro é indefinido para nulo para última condição * ``case(10 + 20 == 30, 'dumbo', 'gumbo') -> 'dumbo'``
* ``case(10 + 20 == 25, 'bojjus', 'do' < 'go', 'gunchus') -> 'gunchus'``
* ``isNull(case(10 + 20 == 25, 'bojjus', 'do' > 'go', 'gunchus')) -> true``
* ``case(10 + 20 == 25, 'bojjus', 'do' > 'go', 'gunchus', 'dumbo') -> 'dumbo'``
___
### <code>cbrt</code>
<code><b>cbrt(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Calcular a raiz do cubo de um número * ``cbrt(8) -> 2.0``
___
### <code>ceil</code>
<code><b>ceil(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
Devolve o menor número não menor que o número * ``ceil(-0.1) -> 0``
___
### <code>coalesce</code>
<code><b>coalesce(<i>&lt;value1&gt;</i> : any, ...) => any</b></code><br/><br/>
Retorna o primeiro valor não nulo de um conjunto de entradas. Todas as inputs devem ser do mesmo tipo * ``coalesce(10, 20) -> 10``
* ``coalesce(toString(null), toString(null), 'dumbo', 'bo', 'go') -> 'dumbo'``
___
### <code>compare</code>
<code><b>compare(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => integer</b></code><br/><br/>
Compara dois valores do mesmo tipo. Retornos notager negativo se valor1 < valor2, 0 se valor1 == valor2, valor positivo se valor1 > valor2 * ``(compare(12, 24) < 1) -> true``
* ``(compare('dumbo', 'dum') > 0) -> true``
___
### <code>concat</code>
<code><b>concat(<i>&lt;this&gt;</i> : string, <i>&lt;that&gt;</i> : string, ...) => string</b></code><br/><br/>
Concatena um número variável de cadeias de caracteres em conjunto. O mesmo que o operador + com cordas * ``concat('dataflow', 'is', 'awesome') -> 'dataflowisawesome'``
* ``'dataflow' + 'is' + 'awesome' -> 'dataflowisawesome'``
* ``isNull('sql' + null) -> true``
___
### <code>concatWS</code> 
<code><b>concatWS(<i>&lt;separator&gt;</i> : string, <i>&lt;this&gt;</i> : string, <i>&lt;that&gt;</i> : string, ...) => string</b></code><br/><br/>
Concatena um número variável de cadeias de caracteres junto com um separador. O primeiro parâmetro é o separador * ``concatWS(' ', 'dataflow', 'is', 'awesome') -> 'dataflow is awesome'``
* ``isNull(concatWS(null, 'dataflow', 'is', 'awesome')) -> true``
* ``concatWS(' is ', 'dataflow', 'awesome') -> 'dataflow is awesome'``
___
### <code>contains</code>
<code><b>contains(<i>&lt;value1&gt;</i> : array, <i>&lt;value2&gt;</i> : unaryfunction) => boolean</b></code><br/><br/>
Retornará true se qualquer elemento na matriz fornecida for avaliado como true no predicado fornecido. Contém espera uma referência a um elemento na função predicada como #item * ``contains([1, 2, 3, 4], #item == 3) -> true``
* ``contains([1, 2, 3, 4], #item > 5) -> false``
___
### <code>cos</code>
<code><b>cos(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Calcula um valor de cosseno * ``cos(10) -> -0.8390715290764524``
___
### <code>cosh</code>
<code><b>cosh(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Calcula uma cosina hiperbólica de valor * ``cosh(0) -> 1.0``
___
### <code>crc32</code>
<code><b>crc32(<i>&lt;value1&gt;</i> : any, ...) => long</b></code><br/><br/>
Calcula o hash CRC32 do conjunto de colunas de tipos de dados primitivos variados de acordo com um comprimento de bit que só pode ser de valores 0 (256), 224, 256, 384, 512. Pode ser usado para calcular uma impressão digital para uma linha * ``crc32(256, 'gunchus', 8.2, 'bojjus', true, toDate('2010-4-4')) -> 3630253689L``
___
### <code>currentDate</code>
<code><b>currentDate([<i>&lt;value1&gt;</i> : string]) => date</b></code><br/><br/>
Obtém a data atual em que esse trabalho começa a ser executado. Você pode passar um fuso horário opcional na forma de ' GMT ', ' PST ', ' UTC ', ' América/Cayman '. O fuso horário local é usado como o padrão. Consulte SimpleDateFormat do Java para obter os formatos disponíveis. 
* ``currentDate('PST')  == toDate('2250-12-31') -> false``https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html * ``currentDate() == toDate('2250-12-31') -> false`` 
<code><b>currentTimestamp() => timestamp</b></code> de 
___
### <code>currentTimestamp</code>
* ``currentDate('America/New_York')  == toDate('2250-12-31') -> false``<br/><br/>
Obtém a marca ção atual quando o trabalho começa a funcionar com o fuso horário local * ``currentTimestamp() == toTimestamp('2250-12-31 12:12:12') -> false``
___
### <code>currentUTC</code>
<code><b>currentUTC([<i>&lt;value1&gt;</i> : string]) => timestamp</b></code><br/><br/>
Obtém o carimbo de data/hora atual como UTC. Se desejar que a hora atual seja interpretada em um fuso horário diferente do fuso horário do cluster, você poderá passar um fuso horário opcional na forma de ' GMT ', ' PST ', ' UTC ', ' América/Cayman '. Ele é padronizado para o fuso horário atual. Consulte SimpleDateFormat do Java para obter os formatos disponíveis. [https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html](https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html). Para converter o tempo UTC para uma utilização diferente do fuso horário da UTC() * ``currentUTC() == toTimestamp('2050-12-12 19:18:12') -> false``
* ``currentUTC() != toTimestamp('2050-12-12 19:18:12') -> true``
* ``fromUTC(currentUTC(), 'Asia/Seoul') != toTimestamp('2050-12-12 19:18:12') -> true``
___
### <code>dayOfMonth</code> 
<code><b>dayOfMonth(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
Recebe o dia do mês dado uma data * ``dayOfMonth(toDate('2018-06-08')) -> 8``
___
### <code>dayOfWeek</code>
<code><b>dayOfWeek(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
Obtém o dia da semana de acordo com uma data. 1 - Domingo, 2 - Segunda-feira ..., 7 - Sábado * ``dayOfWeek(toDate('2018-06-08')) -> 6``
___
### <code>dayOfYear</code>
<code><b>dayOfYear(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
Recebe o dia do ano com uma data * ``dayOfYear(toDate('2016-04-09')) -> 100``
___
### <code>days</code>
<code><b>days(<i>&lt;value1&gt;</i> : integer) => long</b></code><br/><br/>
Duração em milissegundos durante o número de dias * ``days(2) -> 172800000L``
___
### <code>degrees</code>
<code><b>degrees(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Converte radianos em graus * ``degrees(3.141592653589793) -> 180``
___
### <code>divide</code>
<code><b>divide(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
Divide o par de números. O mesmo que o * ``divide(20, 10) -> 2``/operador 
* ``20 / 10 -> 2``
___
### <code>endsWith</code>
<code><b>endsWith(<i>&lt;string&gt;</i> : string, <i>&lt;substring to check&gt;</i> : string) => boolean</b></code><br/><br/>
Verifica se a corda termina com a cadeia fornecida * ``endsWith('dumbo', 'mbo') -> true``
___
### <code>equals</code>
<code><b>equals(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code><br/><br/>
Operador de comparação de Equals. O mesmo que == operador * ``equals(12, 24) -> false``
* ``12 == 24 -> false`` 
* ``'bad' == 'bad' -> true``
* ``isNull('good' == toString(null)) -> true``
___
### <code>equalsIgnoreCase</code>
* ``isNull(null == null) -> true``
<code><b>equalsIgnoreCase(<i>&lt;value1&gt;</i> : string, <i>&lt;value2&gt;</i> : string) => boolean</b></code><br/><br/>
Operador EQUAL de comparação ignorando maiúsculas e minúsculas. O mesmo que <=> operador * ``'abc'<=>'Abc' -> true``
* ``equalsIgnoreCase('abc', 'Abc') -> true``
___
### <code>factorial</code>
<code><b>factorial(<i>&lt;value1&gt;</i> : number) => long</b></code><br/><br/>
Calcular o fatorial de um número * ``factorial(5) -> 120``
___
### <code>false</code>
<code><b>false() => boolean</b></code><br/><br/>
Sempre retorna um valor falso. Utilize a sintaxe da função (falsa)se existir uma coluna chamada "falsa" * ``(10 + 20 > 30) -> false``
* ``(10 + 20 > 30) -> false()``
___
### <code>filter</code>
<code><b>filter(<i>&lt;value1&gt;</i> : array, <i>&lt;value2&gt;</i> : unaryfunction) => array</b></code><br/><br/>
Filtra elementos da matriz que não atendem ao predicado fornecido. O filtro espera uma referência a um elemento na função predicada como #item * ``filter([1, 2, 3, 4], #item > 2) -> [3, 4]``
* ``filter(['a', 'b', 'c', 'd'], #item == 'a' || #item == 'b') -> ['a', 'b']``
___
### <code>floor</code>
<code><b>floor(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
Devolve o maior inteiro não superior ao número * ``floor(-0.1) -> -1``
___
### <code>fromBase64</code>
<code><b>fromBase64(<i>&lt;value1&gt;</i> : string) => string</b></code><br/><br/>
Codifica a corda dada na base64 * ``fromBase64('Z3VuY2h1cw==') -> 'gunchus'``
___
### <code>fromUTC</code>
<code><b>fromUTC(<i>&lt;value1&gt;</i> : timestamp, [<i>&lt;value2&gt;</i> : string]) => timestamp</b></code><br/><br/>
Converte para o carimbo de data/hora do UTC. Opcionalmente, você pode passar o fuso horário na forma de ' GMT ', ' PST ', ' UTC ', ' América/Cayman '. Ele é padronizado para os SimpleDateFormat atuais do timezoneRefer Java para os formatos disponíveis. https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html * ``fromUTC(currentTimeStamp()) == toTimestamp('2050-12-12 19:18:12') -> false``
* ``fromUTC(currentTimeStamp(), 'Asia/Seoul') != toTimestamp('2050-12-12 19:18:12') -> true``
___
### <code>greater</code>
<code><b>greater(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code><br/><br/>
Operador maior de comparação. O mesmo que o operador * ``greater(12, 24) -> false``
* ``('dumbo' > 'dum') -> true``
* ``(toTimestamp('2019-02-05 08:21:34.890', 'yyyy-MM-dd HH:mm:ss.SSS') > toTimestamp('2019-02-03 05:19:28.871', 'yyyy-MM-dd HH:mm:ss.SSS')) -> true``
<code><b>greaterOrEqual(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code> 
___
### <code>greaterOrEqual</code><br/><br/>
Comparação de operador maior ou igual a. O mesmo que o operador * ``greaterOrEqual(12, 12) -> true``
* ``('dumbo' >= 'dum') -> true``
___
### <code>greatest</code>
<code><b>greatest(<i>&lt;value1&gt;</i> : any, ...) => any</b></code><br/><br/>
Retorna o valor máximo entre a lista de valores como entrada ignorando valores nulos. Devoluções nulas se todas as inputs forem nulas * ``greatest(10, 30, 15, 20) -> 30``
* ``greatest(10, toInteger(null), 20) -> 20``
* ``greatest(toDate('2010-12-12'), toDate('2011-12-12'), toDate('2000-12-12')) -> toDate('2011-12-12')``
* ``greatest(toTimestamp('2019-02-03 05:19:28.871', 'yyyy-MM-dd HH:mm:ss.SSS'), toTimestamp('2019-02-05 08:21:34.890', 'yyyy-MM-dd HH:mm:ss.SSS')) -> toTimestamp('2019-02-05 08:21:34.890', 'yyyy-MM-dd HH:mm:ss.SSS')``
___
### <code>hasColumn</code>
<code><b>hasColumn(<i>&lt;column name&gt;</i> : string, [<i>&lt;stream name&gt;</i> : string]) => boolean</b></code><br/><br/>
Verifica o valor de uma coluna por nome no fluxo. Você pode passar um nome de fluxo opcional como o segundo argumento.  Nomes de coluna conhecidos em tempo de design devem ser abordados apenas por seu nome. As inputs computadas não são suportadas, mas pode utilizar substituições de parâmetros * ``hasColumn('parent')``
___
### <code>hour</code>
<code><b>hour(<i>&lt;value1&gt;</i> : timestamp, [<i>&lt;value2&gt;</i> : string]) => integer</b></code><br/><br/>
Obtém o valor de hora de um timestamp. Você pode passar um fuso horário opcional na forma de ' GMT ', ' PST ', ' UTC ', ' América/Cayman '. O fuso horário local é usado como o padrão. Consulte SimpleDateFormat do Java para obter os formatos disponíveis. https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html * ``hour(toTimestamp('2009-07-30 12:58:59')) -> 12``
* ``hour(toTimestamp('2009-07-30 12:58:59'), 'PST') -> 12``
___
### <code>hours</code>
<code><b>hours(<i>&lt;value1&gt;</i> : integer) => long</b></code><br/><br/>
Duração em milissegundos durante o número de horas * ``hours(2) -> 7200000L``
___
### <code>iif</code>
<code><b>iif(<i>&lt;condition&gt;</i> : boolean, <i>&lt;true_expression&gt;</i> : any, [<i>&lt;false_expression&gt;</i> : any]) => any</b></code><br/><br/>
Com base em uma condição, aplica-se um valor ou outro. Se outro não for especificado, ele será considerado nulo. Ambos os valores devem ser compatíveis (numéricos, cordas...) * ``iif(10 + 20 == 30, 'dumbo', 'gumbo') -> 'dumbo'``
* ``iif(10 > 30, 'dumbo', 'gumbo') -> 'gumbo'``
* ``iif(month(toDate('2018-12-01')) == 12, 345.12, 102.67) -> 345.12``
<code><b>iifNull(<i>&lt;value1&gt;</i> : any, [<i>&lt;value2&gt;</i> : any], ...) => any</b></code> 
___
### <code>iifNull</code><br/><br/>
Verifica se o valor não é nulo e retorna-o mais retorna a alternativa. Testa todas as inputs até encontrar o primeiro valor não nulo * ``iifNull(10, 20) -> 10``
* ``iifNull(null, 20, 40) -> 20``
* ``iifNull('bojjus', 'bo', 'dumbo') -> 'dumbo'``
___
### <code>in</code>
<code><b>in(<i>&lt;array of items&gt;</i> : array, <i>&lt;item to find&gt;</i> : any) => boolean</b></code><br/><br/>
Verifica se um item está na matriz * ``in([10, 20, 30], 10) -> true``
* ``in(['good', 'kid'], 'bad') -> false``
___
### <code>initCap</code>
<code><b>initCap(<i>&lt;value1&gt;</i> : string) => string</b></code><br/><br/>
Converte a primeira letra de cada palavra em maiúsculas. As palavras são identificadas como separadas por * ``initCap('cool iceCREAM') -> 'Cool Icecream'``
___
### <code>instr</code>
<code><b>instr(<i>&lt;string&gt;</i> : string, <i>&lt;substring to find&gt;</i> : string) => integer</b></code><br/><br/>
Localiza a posição (1 com base) da subcadeia de caracteres em uma cadeia de caracteres. 0 é devolvido se não for encontrado * ``instr('dumbo', 'mbo') -> 3``
* ``instr('microsoft', 'o') -> 5``
* ``instr('good', 'bad') -> 0``
<code><b>isDelete([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code> 
___
### <code>isDelete</code><br/><br/>
Verifica se a linha está marcada para exclusão. Para transformações que levam mais de um fluxo de entrada, você pode passar o índice (baseado em 1) do fluxo. O índice de fluxo deve ser 1 ou 2 e o valor predefinido é de 1 * ``isDelete()``
* ``isDelete(1)``
___
### <code>isError</code>
<code><b>isError([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
Verifica se a linha está marcada como erro. Para transformações que levam mais de um fluxo de entrada, você pode passar o índice (baseado em 1) do fluxo. O índice de fluxo deve ser 1 ou 2 e o valor predefinido é de 1 * ``isError()``
* ``isError(1)``
___
### <code>isIgnore</code>
<code><b>isIgnore([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
Verifica se a linha está marcada para ser ignorada. Para transformações que levam mais de um fluxo de entrada, você pode passar o índice (baseado em 1) do fluxo. O índice de fluxo deve ser 1 ou 2 e o valor predefinido é de 1 * ``isIgnore()``
* ``isIgnore(1)``
___
### <code>isInsert</code>
<code><b>isInsert([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
Verifica se a linha está marcada para inserção. Para transformações que levam mais de um fluxo de entrada, você pode passar o índice (baseado em 1) do fluxo. O índice de fluxo deve ser 1 ou 2 e o valor predefinido é de 1 * ``isInsert()``
* ``isInsert(1)``
___
### <code>isMatch</code>
<code><b>isMatch([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
Verifica se a linha é correspondida na pesquisa. Para transformações que levam mais de um fluxo de entrada, você pode passar o índice (baseado em 1) do fluxo. O índice de fluxo deve ser 1 ou 2 e o valor predefinido é de 1 * ``isMatch()``
* ``isMatch(1)``
___
### <code>isNull</code>
<code><b>isNull(<i>&lt;value1&gt;</i> : any) => boolean</b></code><br/><br/>
Verifica se o valor é nulo * ``isNull(NULL()) -> true``
* ``isNull('') -> false``
___
### <code>isUpdate</code>
<code><b>isUpdate([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
Verifica se a linha está marcada para atualização. Para transformações que levam mais de um fluxo de entrada, você pode passar o índice (baseado em 1) do fluxo. O índice de fluxo deve ser 1 ou 2 e o valor predefinido é de 1 * ``isUpdate()``
* ``isUpdate(1)``
___
### <code>isUpsert</code>
<code><b>isUpsert([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
Verifica se a linha está marcada para inserção. Para transformações que levam mais de um fluxo de entrada, você pode passar o índice (baseado em 1) do fluxo. O índice de fluxo deve ser 1 ou 2 e o valor predefinido é de 1 * ``isUpsert()``
* ``isUpsert(1)``
___
### <code>lastDayOfMonth</code>
<code><b>lastDayOfMonth(<i>&lt;value1&gt;</i> : datetime) => date</b></code><br/><br/>
Tem a última data do mês dada uma data * ``lastDayOfMonth(toDate('2009-01-12')) -> toDate('2009-01-31')``
___
### <code>least</code>
<code><b>least(<i>&lt;value1&gt;</i> : any, ...) => any</b></code><br/><br/>
Comparação menor ou igual a operador. O mesmo que o operador * ``least(10, 30, 15, 20) -> 10``
* ``least(toDate('2010-12-12'), toDate('2011-12-12'), toDate('2000-12-12')) -> toDate('2000-12-12')``
___
### <code>left</code>
<code><b>left(<i>&lt;string to subset&gt;</i> : string, <i>&lt;number of characters&gt;</i> : integral) => string</b></code><br/><br/>
Extrai uma subcadeia de caracteres de início no índice 1 com número de caracteres. O mesmo que substring(str, 1, n) * ``left('bojjus', 2) -> 'bo'``
* ``left('bojjus', 20) -> 'bojjus'``
___
### <code>length</code>
<code><b>length(<i>&lt;value1&gt;</i> : string) => integer</b></code><br/><br/>
Devolve o comprimento da cadeia * ``length('dumbo') -> 5``
___
### <code>lesser</code>
<code><b>lesser(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code><br/><br/>
Operador de comparação less. O mesmo que o operador * ``lesser(12, 24) -> true``
* ``('abcd' < 'abc') -> false``
* ``(toTimestamp('2019-02-03 05:19:28.871', 'yyyy-MM-dd HH:mm:ss.SSS') < toTimestamp('2019-02-05 08:21:34.890', 'yyyy-MM-dd HH:mm:ss.SSS')) -> true`` 
<code><b>lesserOrEqual(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code> 
___
### <code>lesserOrEqual</code><br/><br/>
Comparação menor ou igual a operador. O mesmo que o operador * ``lesserOrEqual(12, 12) -> true``
* ``('dumbo' <= 'dum') -> false``
___
### <code>levenshtein</code>
<code><b>levenshtein(<i>&lt;from string&gt;</i> : string, <i>&lt;to string&gt;</i> : string) => integer</b></code><br/><br/>
Obtém a distância de levenshtein entre duas cordas * ``levenshtein('boys', 'girls') -> 4``
___
### <code>like</code>
<code><b>like(<i>&lt;string&gt;</i> : string, <i>&lt;pattern match&gt;</i> : string) => boolean</b></code><br/><br/>
O padrão é uma cadeia de caracteres que corresponde literalmente. As exceções são os seguintes símbolos especiais: _ corresponde a qualquer personagem na entrada (semelhante a . em posix expressões regulares) % corresponde a zero ou mais caracteres na entrada (semelhante a .* em expressões regulares posix).
O caractere de escape é ' '. Se um caractere de escape precede um símbolo especial ou outro caractere de escape, o caractere a seguir será correspondido literalmente. É inválido escapar de qualquer outro caractere.
* ``like('icecream', 'ice%') -> true``
___
### <code>locate</code>
<code><b>locate(<i>&lt;substring to find&gt;</i> : string, <i>&lt;string&gt;</i> : string, [<i>&lt;from index - 1-based&gt;</i> : integral]) => integer</b></code><br/><br/>
Localiza a posição (1 com base) da subcadeia de caracteres em uma cadeia de caracteres que inicia uma determinada posição. Se a posição for omitida, ela será considerada do início da cadeia de caracteres. 0 é devolvido se não for encontrado * ``locate('mbo', 'dumbo') -> 3``
* ``locate('o', 'microsoft', 6) -> 7``
* ``locate('bad', 'good') -> 0``
<code><b>log(<i>&lt;value1&gt;</i> : number, [<i>&lt;value2&gt;</i> : number]) => double</b></code> 
___
### <code>log</code><br/><br/>
Calcula o valor do log. Uma base opcional pode ser fornecida e outro número de euler se usado * ``log(100, 10) -> 2``
___
### <code>log10</code>
<code><b>log10(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Calcula o valor do registo com base em 10 * ``log10(100) -> 2``base 
___
### <code>lower</code>
<code><b>lower(<i>&lt;value1&gt;</i> : string) => string</b></code><br/><br/>
Diminui uma * ``lower('GunChus') -> 'gunchus'``
<code><b>lpad(<i>&lt;string to pad&gt;</i> : string, <i>&lt;final padded length&gt;</i> : integral, <i>&lt;padding&gt;</i> : string) => string</b></code> de 
___
### <code>lpad</code>de cadeias<br/><br/>
A esquerda pads a cadeia de caracteres pelo preenchimento fornecido até que seja de um determinado comprimento. Se a corda for igual ou superior ao comprimento, então é aparada ao comprimento * ``lpad('dumbo', 10, '-') -> '-----dumbo'``
* ``lpad('dumbo', 4, '-') -> 'dumb'``
* ''dumbo', 8, '<>') -> '<><dumbo'``
___
### <code>ltrim</code>
<code><b>ltrim(<i>&lt;string to trim&gt;</i> : string, [<i>&lt;trim characters&gt;</i> : string]) => string</b></code><br/><br/>
A esquerda corta uma cadeia de caracteres da entrelinha. Se o segundo parâmetro não for especificado, ele cortará o espaço em branco. Caso contrário, apara qualquer personagem especificado no segundo parâmetro * ``ltrim('  dumbo  ') -> 'dumbo  '``
* ``ltrim('!--!du!mbo!', '-!') -> 'du!mbo!'``
___
### <code>map</code>
<code><b>map(<i>&lt;value1&gt;</i> : array, <i>&lt;value2&gt;</i> : unaryfunction) => any</b></code><br/><br/>
Mapeia cada elemento da matriz para um novo elemento usando a expressão fornecida. O mapa espera uma referência a um elemento na função de expressão como #item * ``map([1, 2, 3, 4], #item + 2) -> [3, 4, 5, 6]``
* ``map(['a', 'b', 'c', 'd'], #item + '_processed') -> ['a_processed', 'b_processed', 'c_processed', 'd_processed']``
___
### <code>mapIndex</code>
<code><b>mapIndex(<i>&lt;value1&gt;</i> : array, <i>&lt;value2&gt;</i> : binaryfunction) => any</b></code><br/><br/>
Mapeia cada elemento da matriz para um novo elemento usando a expressão fornecida. O mapa espera uma referência a um elemento na função de expressão como #item e uma referência ao índice de elementos como #index * ``mapIndex([1, 2, 3, 4], #item + 2 + #index) -> [4, 6, 8, 10]``
___
### <code>md5</code>
<code><b>md5(<i>&lt;value1&gt;</i> : any, ...) => string</b></code><br/><br/>
Calcula o resumo MD5 do conjunto de colunas de tipos de datavariantes primitivos e retorna uma cadeia de caracteres hexadecimal de 32 caracteres. Pode ser usado para calcular uma impressão digital para uma linha * ``md5(5, 'gunchus', 8.2, 'bojjus', true, toDate('2010-4-4')) -> '4ce8a880bd621a1ffad0bca905e1bc5a'``
___
### <code>millisecond</code>
<code><b>millisecond(<i>&lt;value1&gt;</i> : timestamp, [<i>&lt;value2&gt;</i> : string]) => integer</b></code><br/><br/>
Obtém o valor de milissegundo de uma data. Você pode passar um fuso horário opcional na forma de ' GMT ', ' PST ', ' UTC ', ' América/Cayman '. O fuso horário local é usado como o padrão. Consulte SimpleDateFormat do Java para obter os formatos disponíveis. https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html * ``millisecond(toTimestamp('2009-07-30 12:58:59.871', 'yyyy-MM-dd HH:mm:ss.SSS')) -> 871``
___
### <code>milliseconds</code>
<code><b>milliseconds(<i>&lt;value1&gt;</i> : integer) => long</b></code><br/><br/>
Duração em milissegundos para o número de milissegundos * ``milliseconds(2) -> 2L``
___
### <code>minus</code>
<code><b>minus(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
Subtrai números. Subtraia de um número de data de dias. Duração de Substract de um carimbo de data/hora. Substract dois carimbos de data/hora para obter a diferença em milissegundos. O mesmo que o operador * ``minus(20, 10) -> 10``
* ``20 - 10 -> 10``
* ``minus(toDate('2012-12-15'), 3) -> toDate('2012-12-12')``
* ``toDate('2012-12-15') - 3 -> toDate('2012-12-12')``
* ``toTimestamp('2019-02-03 05:19:28.871', 'yyyy-MM-dd HH:mm:ss.SSS') + (days(1) + hours(2) - seconds(10)) -> toTimestamp('2019-02-04 07:19:18.871', 'yyyy-MM-dd HH:mm:ss.SSS')``
<code><b>minute(<i>&lt;value1&gt;</i> : timestamp, [<i>&lt;value2&gt;</i> : string]) => integer</b></code> 
___
### <code>minute</code>
* ``toTimestamp('2019-02-03 05:21:34.851', 'yyyy-MM-dd HH:mm:ss.SSS') - toTimestamp('2019-02-03 05:21:36.923', 'yyyy-MM-dd HH:mm:ss.SSS') -> -2072``<br/><br/>
Obtém o valor de minuto de um carimbo de data/hora. Você pode passar um fuso horário opcional na forma de ' GMT ', ' PST ', ' UTC ', ' América/Cayman '. O fuso horário local é usado como o padrão. Consulte SimpleDateFormat do Java para obter os formatos disponíveis. https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html * ``minute(toTimestamp('2009-07-30 12:58:59')) -> 58``
* ``minute(toTimestamp('2009-07-30 12:58:59'), 'PST') -> 58``
___
### <code>minutes</code>
<code><b>minutes(<i>&lt;value1&gt;</i> : integer) => long</b></code><br/><br/>
Duração em milissegundos durante o número de minutos * ``minutes(2) -> 120000L``
___
### <code>mod</code>
<code><b>mod(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
Módulo de um par de números. O mesmo que o * ``mod(20, 8) -> 4``%  
* ``20 % 8 -> 4``
___
### <code>month</code>
<code><b>month(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
Obtém o valor mensal de uma data ou carimbo de tempo * ``month(toDate('2012-8-8')) -> 8``
___
### <code>monthsBetween</code>
<code><b>monthsBetween(<i>&lt;from date/timestamp&gt;</i> : datetime, <i>&lt;to date/timestamp&gt;</i> : datetime, [<i>&lt;roundoff&gt;</i> : boolean], [<i>&lt;time zone&gt;</i> : string]) => double</b></code><br/><br/>
Obtém o número de meses entre duas datas. Você pode arredondar o cálculo. Você pode passar um fuso horário opcional na forma de ' GMT ', ' PST ', ' UTC ', ' América/Cayman '. O fuso horário local é usado como o padrão. Consulte SimpleDateFormat do Java para obter os formatos disponíveis. https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html * ``monthsBetween(toTimestamp('1997-02-28 10:30:00'), toDate('1996-10-30')) -> 3.94959677``
___
### <code>multiply</code>
<code><b>multiply(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
Multiplica o par de números. O mesmo que o operador * ``multiply(20, 10) -> 200``
* ``20 * 10 -> 200`` 
___
### <code>negate</code>
<code><b>negate(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
Nega um número. Transforma os números positivos em negativo e vice-versa * ``negate(13) -> -13``
___
### <code>nextSequence</code>
<code><b>nextSequence() => long</b></code><br/><br/>
Retorna a próxima sequência exclusiva. O número é consecutivo apenas dentro de uma partição e é prefixado pela partiçãoId * ``nextSequence() == 12313112 -> false``
___
### <code>normalize</code>
<code><b>normalize(<i>&lt;String to normalize&gt;</i> : string) => string</b></code><br/><br/>
Normalizar o valor da cadeia para separar caracteres de unicódigo acentuados * ``regexReplace(normalize('bo²s'), `\p{M}`, '') -> 'boys'``
___
### <code>not</code>
<code><b>not(<i>&lt;value1&gt;</i> : boolean) => boolean</b></code><br/><br/>
Operador de negação lógica * ``not(true) -> false``
<code><b>notEquals(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code> de 
___
### <code>notEquals</code>
* ``not(10 == 20) -> true``<br/><br/>
Operador de comparação não é igual a. O mesmo que != operador * ``12 != 24 -> true``
* ``'bojjus' != 'bo' + 'jjus' -> false``
___
### <code>notNull</code>
<code><b>notNull(<i>&lt;value1&gt;</i> : any) => boolean</b></code><br/><br/>
Verifica se o valor não for nulo * ``notNull(NULL()) -> false``
* ``notNull('') -> true``
___
### <code>null</code>
<code><b>null() => null</b></code><br/><br/>
Retorna um valor nulo. Use a sintaxe da função (NULL ()) se houver uma coluna chamada ' NULL '. Qualquer operação que utilize resultará num * ``isNull('dumbo' + null) -> true``nulo 
* ``isNull(10 * null) -> true``
* ``isNull('') -> false`` 
* ``isNull(10 + 20) -> false``
* ``isNull(10/0) -> true``
___
### <code>or</code> 
<code><b>or(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : boolean) => boolean</b></code><br/><br/>
Operador lógico ou lógico. O mesmo que [] * ``or(true, false) -> true``
* ``true || false -> true``
<code><b>pMod(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code> 
___
### <code>pMod</code><br/><br/>
Módulo positivo do par de números.
* ``pmod(-20, 8) -> 4``
___
### <code>partitionId</code>
<code><b>partitionId() => integer</b></code><br/><br/>
Devolve o id de partição atual a linha de entrada está em * ``partitionId()``
___
### <code>power</code>
<code><b>power(<i>&lt;value1&gt;</i> : number, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Eleva um número ao poder de outro * ``power(10, 2) -> 100``
___
### <code>reduce</code>
<code><b>reduce(<i>&lt;value1&gt;</i> : array, <i>&lt;value2&gt;</i> : any, <i>&lt;value3&gt;</i> : binaryfunction, <i>&lt;value4&gt;</i> : unaryfunction) => any</b></code><br/><br/>
Acumula elementos em uma matriz. A Redução espera uma referência a um acumulador e a um elemento na primeira função de expressão como #acc e #item e espera que o valor resultante como #result seja utilizado na função de segunda expressão * ``toString(reduce(['1', '2', '3', '4'], '0', #acc + #item, #result)) -> '01234'``
___
### <code>regexExtract</code>
<code><b>regexExtract(<i>&lt;string&gt;</i> : string, <i>&lt;regex to find&gt;</i> : string, [<i>&lt;match group 1-based index&gt;</i> : integral]) => string</b></code><br/><br/>
Extraia uma subcadeia de caracteres correspondente para um determinado padrão de Regex. O último parâmetro identifica o grupo de correspondência e é padronizado como 1 se omitido. Use<regex>'(citação traseira) para combinar com uma corda sem escapar * ``regexExtract('Cost is between 600 and 800 dollars', '(\\d+) and (\\d+)', 2) -> '800'``
* ``regexExtract('Cost is between 600 and 800 dollars', `(\d+) and (\d+)`, 2) -> '800'``
___
### <code>regexMatch</code>
<code><b>regexMatch(<i>&lt;string&gt;</i> : string, <i>&lt;regex to match&gt;</i> : string) => boolean</b></code><br/><br/>
Verifica se a cadeia de caracteres corresponde ao padrão Regex fornecido. Use<regex>'(citação traseira) para combinar com uma corda sem escapar * ``regexMatch('200.50', '(\\d+).(\\d+)') -> true``
* ``regexMatch('200.50', `(\d+).(\d+)`) -> true``
___
### <code>regexReplace</code>
<code><b>regexReplace(<i>&lt;string&gt;</i> : string, <i>&lt;regex to find&gt;</i> : string, <i>&lt;substring to replace&gt;</i> : string) => string</b></code><br/><br/>
Substitua todas as ocorrências de um padrão regex por outra substring na dada corda Use<regex>'(citação traseira) para combinar com uma corda sem escapar * ``regexReplace('100 and 200', '(\\d+)', 'bojjus') -> 'bojjus and bojjus'``
* ``regexReplace('100 and 200', `(\d+)`, 'gunchus') -> 'gunchus and gunchus'``
___
### <code>regexSplit</code>
<code><b>regexSplit(<i>&lt;string to split&gt;</i> : string, <i>&lt;regex expression&gt;</i> : string) => array</b></code><br/><br/>
Divide uma corda baseada num delimitador baseado no regex e devolve uma série de cordas * ``regexSplit('bojjusAgunchusBdumbo', `[CAB]`) -> ['bojjus', 'gunchus', 'dumbo']``
* ``regexSplit('bojjusAgunchusBdumboC', `[CAB]`) -> ['bojjus', 'gunchus', 'dumbo', '']``
* ``(regexSplit('bojjusAgunchusBdumboC', `[CAB]`)[1]) -> 'bojjus'``
* ``isNull(regexSplit('bojjusAgunchusBdumboC', `[CAB]`)[20]) -> true``
___
### <code>replace</code>
<code><b>replace(<i>&lt;string&gt;</i> : string, <i>&lt;substring to find&gt;</i> : string, [<i>&lt;substring to replace&gt;</i> : string]) => string</b></code><br/><br/>
Substitua todas as ocorrências de uma subcadeia de caracteres por outra subcadeia de caracteres na cadeia de caracteres especificada. Se o último parâmetro for omitido, é por defeito o esvaziamento de * ``replace('doggie dog', 'dog', 'cat') -> 'catgie cat'``
* ``replace('doggie dog', 'dog', '') -> 'gie '``
* ``replace('doggie dog', 'dog') -> 'gie '``
___
### <code>reverse</code>
<code><b>reverse(<i>&lt;value1&gt;</i> : string) => string</b></code><br/><br/>
Inverte uma cadeia * ``reverse('gunchus') -> 'suhcnug'``
___
### <code>right</code>
<code><b>right(<i>&lt;string to subset&gt;</i> : string, <i>&lt;number of characters&gt;</i> : integral) => string</b></code><br/><br/>
Extrai uma Subcadeia com o número de caracteres à direita. O mesmo que substring(str, LENGTH(str) - n, n) * ``right('bojjus', 2) -> 'us'``
* ``right('bojjus', 20) -> 'bojjus'``
___
### <code>rlike</code>
<code><b>rlike(<i>&lt;string&gt;</i> : string, <i>&lt;pattern match&gt;</i> : string) => boolean</b></code><br/><br/>
Verifica se a corda corresponde ao padrão regex dado * ``rlike('200.50', `(\d+).(\d+)`) -> true``
* ``rlike('bogus', `M[0-9]+.*`) -> false``
___
### <code>round</code>
<code><b>round(<i>&lt;number&gt;</i> : number, [<i>&lt;scale to round&gt;</i> : number], [<i>&lt;rounding option&gt;</i> : integral]) => double</b></code><br/><br/>
Arredonda um número de acordo com uma escala opcional e um modo de arredondamento opcional. Se a escala for omitida, o padrão será 0.  Se o modo for omitido, está em incumprimento ROUND_HALF_UP(5). Os valores para arredondamento incluem 1 - ROUND_UP 2 - ROUND_DOWN 3 - ROUND_CEILING 4 - ROUND_FLOOR 5 - ROUND_HALF_UP 6 - ROUND_HALF_DOWN 7 - ROUND_HALF_EVEN 8 - ROUND_UNNECESSARY * ``round(100.123) -> 100.0``
* ``round(2.5, 0) -> 3.0`` 
___
### <code>rpad</code>
* ``round(5.3999999999999995, 2, 7) -> 5.40`` 
<code><b>rpad(<i>&lt;string to pad&gt;</i> : string, <i>&lt;final padded length&gt;</i> : integral, <i>&lt;padding&gt;</i> : string) => string</b></code><br/><br/>
A direita pads a cadeia de caracteres pelo preenchimento fornecido até que seja de um determinado comprimento. Se a corda for igual ou superior ao comprimento, então é aparada ao comprimento * ``rpad('dumbo', 10, '-') -> 'dumbo-----'``
* ``rpad('dumbo', 4, '-') -> 'dumb'``
* ``rpad('dumbo', 8, '<>') -> 'dumbo<><'``
___
### <code>rtrim</code>rtrim</code>
<code><b>rtrim(<i>&lt;string to trim&gt;</i> : string, [<i>&lt;trim characters&gt;</i> : string]) => string</b></code><br/><br/>
A direita corta uma cadeia de caracteres à esquerda. Se o segundo parâmetro não for especificado, ele cortará o espaço em branco. Caso contrário, apara qualquer personagem especificado no segundo parâmetro * ``rtrim('  dumbo  ') -> '  dumbo'``
* ``rtrim('!--!du!mbo!', '-!') -> '!--!du!mbo'``
___
### <code>second</code>
<code><b>second(<i>&lt;value1&gt;</i> : timestamp, [<i>&lt;value2&gt;</i> : string]) => integer</b></code><br/><br/>
Obtém o segundo valor de uma data. Você pode passar um fuso horário opcional na forma de ' GMT ', ' PST ', ' UTC ', ' América/Cayman '. O fuso horário local é usado como o padrão. Consulte SimpleDateFormat do Java para obter os formatos disponíveis. https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html * ``second(toTimestamp('2009-07-30 12:58:59')) -> 59``
___
### <code>seconds</code>
<code><b>seconds(<i>&lt;value1&gt;</i> : integer) => long</b></code><br/><br/>
Duração em milissegundos durante o número de segundos * ``seconds(2) -> 2000L``
___
### <code>sha1</code>
<code><b>sha1(<i>&lt;value1&gt;</i> : any, ...) => string</b></code><br/><br/>
Calcula o resumo SHA-1 do conjunto de colunas de tipos de dataprimitivos variantes e retorna uma cadeia de caracteres hexadecimal de 40 caracteres. Pode ser usado para calcular uma impressão digital para uma linha * ``sha1(5, 'gunchus', 8.2, 'bojjus', true, toDate('2010-4-4')) -> '46d3b478e8ec4e1f3b453ac3d8e59d5854e282bb'``
___
### <code>sha2</code>
<code><b>sha2(<i>&lt;value1&gt;</i> : integer, <i>&lt;value2&gt;</i> : any, ...) => string</b></code><br/><br/>
Calcula o resumo SHA-2 do conjunto de colunas de tipos de dados primitivos variados de acordo com um comprimento de bit que só pode ser de valores 0 (256), 224, 256, 384, 512. Pode ser usado para calcular uma impressão digital para uma linha * ``sha2(256, 'gunchus', 8.2, 'bojjus', true, toDate('2010-4-4')) -> 'afe8a553b1761c67d76f8c31ceef7f71b66a1ee6f4e6d3b5478bf68b47d06bd3'``
___
### <code>sin</code>
<code><b>sin(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Calcula um valor sine * ``sin(2) -> 0.9092974268256817``
___
### <code>sinh</code>
<code><b>sinh(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Calcula um valor seno hiperbólico * ``sinh(0) -> 0.0``
___
### <code>slice</code>
<code><b>slice(<i>&lt;array to slice&gt;</i> : array, <i>&lt;from 1-based index&gt;</i> : integral, [<i>&lt;number of items&gt;</i> : integral]) => array</b></code><br/><br/>
Extrai um subconjunto de uma matriz de uma posição. A posição é baseada em 1. Se o comprimento for omitido, é indefinido o fim da sequência * ``slice([10, 20, 30, 40], 1, 2) -> [10, 20]``
* ``slice([10, 20, 30, 40], 2) -> [20, 30, 40]``
* ``slice([10, 20, 30, 40], 2)[1] -> 20``
* ``isNull(slice([10, 20, 30, 40], 2)[0]) -> true`` 
* ``isNull(slice([10, 20, 30, 40], 2)[20]) -> true``
* ``slice(['a', 'b', 'c', 'd'], 8) -> []``  
___
### <code>sort</code>
<code><b>sort(<i>&lt;value1&gt;</i> : array, <i>&lt;value2&gt;</i> : binaryfunction) => array</b></code><br/><br/>
Classifica a matriz usando a função de predicado fornecida. Sort espera uma referência a dois elementos consecutivos na função de expressão como #item1 e #item2 * ``sort([4, 8, 2, 3], compare(#item1, #item2)) -> [2, 3, 4, 8]``
* ``sort(['a3', 'b2', 'c1'], iif(right(#item1, 1) >= right(#item2, 1), 1, -1)) -> ['c1', 'b2', 'a3']``
___
### <code>soundex</code>
<code><b>soundex(<i>&lt;value1&gt;</i> : string) => string</b></code><br/><br/>
Obtém o código soundex para a * ``soundex('genius') -> 'G520'``
___
### <code>split</code>
<code><b>split(<i>&lt;string to split&gt;</i> : string, <i>&lt;split characters&gt;</i> : string) => array</b></code><br/><br/>
Divide uma corda baseada num delimitador e devolve uma série de cordas * ``split('bojjus,guchus,dumbo', ',') -> ['bojjus', 'guchus', 'dumbo']``
* ``split('bojjus,guchus,dumbo', '|') -> ['bojjus,guchus,dumbo']``
* ``split('bojjus, guchus, dumbo', ', ') -> ['bojjus', 'guchus', 'dumbo']`` 
* ``split('bojjus, guchus, dumbo', ', ')[1] -> 'bojjus'``
* ``isNull(split('bojjus, guchus, dumbo', ', ')[0]) -> true``
* ``isNull(split('bojjus, guchus, dumbo', ', ')[20]) -> true``
* ``split('bojjusguchusdumbo', ',') -> ['bojjusguchusdumbo']``
___
### <code>sqrt</code>
<code><b>sqrt(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Calcula a raiz quadrada de um número * ``sqrt(9) -> 3``
___
### <code>startsWith</code>
<code><b>startsWith(<i>&lt;string&gt;</i> : string, <i>&lt;substring to check&gt;</i> : string) => boolean</b></code><br/><br/>
Verifique se a corda começa com a cadeia fornecida * ``startsWith('dumbo', 'du') -> true``
___
### <code>subDays</code>
<code><b>subDays(<i>&lt;date/timestamp&gt;</i> : datetime, <i>&lt;days to subtract&gt;</i> : integral) => datetime</b></code><br/><br/>
Subtraia meses de uma data ou timestamp. O mesmo que o operador da data * ``subDays(toDate('2016-08-08'), 1) -> toDate('2016-08-07')``
___
### <code>subMonths</code>
<code><b>subMonths(<i>&lt;date/timestamp&gt;</i> : datetime, <i>&lt;months to subtract&gt;</i> : integral) => datetime</b></code><br/><br/>
Subtrair meses a partir de uma data ou carimbo de tempo * ``subMonths(toDate('2016-09-30'), 1) -> toDate('2016-08-31')``
___
### <code>substring</code>
<code><b>substring(<i>&lt;string to subset&gt;</i> : string, <i>&lt;from 1-based index&gt;</i> : integral, [<i>&lt;number of characters&gt;</i> : integral]) => string</b></code><br/><br/>
Extrai uma subcadeia de caracteres de um determinado comprimento de uma posição. A posição é baseada em 1. Se o comprimento for omitido, é indefinido para o fim da cadeia * ``substring('Cat in the hat', 5, 2) -> 'in'``
* ``substring('Cat in the hat', 5, 100) -> 'in the hat'``
* ``substring('Cat in the hat', 5) -> 'in the hat'``
* ``substring('Cat in the hat', 100, 100) -> ''`` 
___
### <code>tan</code>
<code><b>tan(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Calcula um valor tanggente * ``tan(0) -> 0.0``
___
### <code>tanh</code>
<code><b>tanh(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Calcula um valor tangente hiperbólico * ``tanh(0) -> 0.0``
___
### <code>toBase64</code>
<code><b>toBase64(<i>&lt;value1&gt;</i> : string) => string</b></code><br/><br/>
Codifica a corda dada na base64 * ``toBase64('bojjus') -> 'Ym9qanVz'``
___
### <code>toBinary</code>
<code><b>toBinary(<i>&lt;value1&gt;</i> : any) => binary</b></code><br/><br/>
Converte qualquer carimbo/corda numérico/data/prazo para representação binária * ``toBinary(3) -> [0x11]``
___
### <code>toBoolean</code>
<code><b>toBoolean(<i>&lt;value1&gt;</i> : string) => boolean</b></code><br/><br/>
Converte um valor de ('t', 'true', 'y', 'sim', '1') para verdadeiro e ('f', 'falso', 'n', 'não', '0') a falso e nulo por qualquer outro valor * ``toBoolean('true') -> true``
* ``toBoolean('n') -> false``
* ``isNull(toBoolean('truthy')) -> true``
___
### <code>toDate</code>
<code><b>toDate(<i>&lt;string&gt;</i> : any, [<i>&lt;date format&gt;</i> : string]) => date</b></code><br/><br/>
Converte a cadeia de caracteres de data de entrada em data usando um formato de data de entrada opcional. Consulte SimpleDateFormat do Java para obter os formatos disponíveis. Se o formato da data de entrada for omitido, o formato predefinido é yyyy-[M]M-[d]d. Os formatos aceites são "yyyy,yyy-[M]M, yyyy-[M]M-[d]d, yyyy-[M]M-[dT* ] * ``toDate('2012-8-18') -> toDate('2012-08-18')``
* ``toDate('12/18/2012', 'MM/dd/yyyy') -> toDate('2012-12-18')``
___
### <code>toDecimal</code>
<code><b>toDecimal(<i>&lt;value&gt;</i> : any, [<i>&lt;precision&gt;</i> : integral], [<i>&lt;scale&gt;</i> : integral], [<i>&lt;format&gt;</i> : string], [<i>&lt;locale&gt;</i> : string]) => decimal(10,0)</b></code><br/><br/>
Converte qualquer número ou cadeia de caracteres em um valor decimal. Se a precisão e a escala não forem especificadas, o padrão será (10, 2). Um formato decimal do Java opcional pode ser usado para a conversão. Um formato local opcional sob a forma de linguagem BCP47 como en-US, de, zh-CN * ``toDecimal(123.45) -> 123.45``
* ``toDecimal('123.45', 8, 4) -> 123.4500``
* ``toDecimal('Ç123,45', 10, 2, 'Ç###,##', 'de') -> 123.45``
* ``toDecimal('$123.45', 8, 4,'$###.00') -> 123.4500``
___
### <code>toDouble</code> 
<code><b>toDouble(<i>&lt;value&gt;</i> : any, [<i>&lt;format&gt;</i> : string], [<i>&lt;locale&gt;</i> : string]) => double</b></code><br/><br/>
Converte qualquer número ou cadeia de caracteres em um valor duplo. Um formato decimal do Java opcional pode ser usado para a conversão. Um formato local opcional sob a forma de linguagem BCP47 como en-US, de, zh-CN * ``toDouble(123.45) -> 123.45``
* ``toDouble('123.45') -> 123.45``
* ``toDouble('Ç123,45', 'Ç###,##', 'de') -> 123.45``
* ``toDouble('$123.45', '$###.00') -> 123.45``
___
### <code>toFloat</code> 
<code><b>toFloat(<i>&lt;value&gt;</i> : any, [<i>&lt;format&gt;</i> : string], [<i>&lt;locale&gt;</i> : string]) => float</b></code><br/><br/>
Converte qualquer número ou cadeia de caracteres em um valor float. Um formato decimal do Java opcional pode ser usado para a conversão. Truncates qualquer * ``toFloat(123.45) -> 123.45f``dupla 
* ``toFloat('123.45') -> 123.45f``
<code><b>toInteger(<i>&lt;value&gt;</i> : any, [<i>&lt;format&gt;</i> : string], [<i>&lt;locale&gt;</i> : string]) => integer</b></code> 
___
### <code>toInteger</code>
* ``toFloat('$123.45', '$###.00') -> 123.45f``<br/><br/>
Converte qualquer número ou cadeia de caracteres em um valor inteiro. Um formato decimal do Java opcional pode ser usado para a conversão. Truncates qualquer 
<code><b>toLong(<i>&lt;value&gt;</i> : any, [<i>&lt;format&gt;</i> : string], [<i>&lt;locale&gt;</i> : string]) => long</b></code> de 
* ``toInteger('123') -> 123``* ``toInteger(123) -> 123``longo, flutuante e duplo 
* ``toInteger('$123', '$###') -> 123``
___
### <code>toLong</code><br/><br/>
Converte qualquer número ou cadeia de caracteres em um valor longo. Um formato decimal do Java opcional pode ser usado para a conversão. Truncates qualquer flutuador, * ``toLong(123) -> 123``
* ``toLong('$123', '$###') -> 123``
* ``toLong('123') -> 123``
___
### <code>toShort</code> 
<code><b>toShort(<i>&lt;value&gt;</i> : any, [<i>&lt;format&gt;</i> : string], [<i>&lt;locale&gt;</i> : string]) => short</b></code><br/><br/>
Converte qualquer número ou cadeia de caracteres em um valor curto. Um formato decimal do Java opcional pode ser usado para a conversão. Truncates qualquer inteiro, longo, flutuante, * ``toShort(123) -> 123``duplo 
* ``toShort('123') -> 123``
* ``toShort('$123', '$###') -> 123``
___
### <code>toString</code>
<code><b>toString(<i>&lt;value&gt;</i> : any, [<i>&lt;number format/date format&gt;</i> : string]) => string</b></code><br/><br/>
Converte um tipo de dados primitivo em uma cadeia de caracteres. Para números e datas, um formato pode ser especificado. Se não for especificado, o padrão do sistema será escolhido. O formato decimal do Java é usado para números. Consulte o Java SimpleDateFormat para todos os formatos de data possíveis; o formato predefinido é yyy-MM-dd * ``toString(10) -> '10'``
* ``toString('engineer') -> 'engineer'``
* ``toString(123456.789, '##,###.##') -> '123,456.79'``
* ``toString(123.78, '000000.000') -> '000123.780'`` 
* ``toString(12345, '##0.#####E0') -> '12.345E3'``
<code><b>toTimestamp(<i>&lt;string&gt;</i> : any, [<i>&lt;timestamp format&gt;</i> : string], [<i>&lt;time zone&gt;</i> : string]) => timestamp</b></code> 
* ``toString(4 == 20) -> 'false'``
* ``isNull(toString(toDate('2018-12-31', 'MM/dd/yy'))) -> true``
* ``toString(toDate('2018-12-31')) -> '2018-12-31'``
___
### <code>toTimestamp</code><br/><br/>
Converte uma cadeia de caracteres em um timestamp dado um formato de carimbo de data/hora opcional. Consulte Java SimpleDateFormat para todos os formatos possíveis. Se a marca de tempo for omitida, é utilizado o padrão predefinido.yyyy-[M]M-[d]d hh:mm:ss[.f...]. Você pode passar um fuso horário opcional sob a forma de 'GMT', 'PST', 'UTC', 'America/Cayman'. A timestamp suporta até uma precisão de milissegundocom valor de 999Consulte o SimpleDateFormat da Java para os formatos disponíveis. https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html * ``toTimestamp('2016-12-31 00:12:00') -> toTimestamp('2016-12-31 00:12:00')``
* ``millisecond(toTimestamp('2019-02-03 05:19:28.871', 'yyyy-MM-dd HH:mm:ss.SSS')) -> 871``
* ``toTimestamp('12/31/2016T00:12:00', 'MM/dd/yyyy\'T\'HH:mm:ss') -> toTimestamp('2016-12-31 00:12:00')``
* ``toTimestamp('2016-12-31T00:12:00', 'yyyy-MM-dd\'T\'HH:mm:ss', 'PST') -> toTimestamp('2016-12-31 00:12:00')``  
___
### <code>toUTC</code> 
<code><b>toUTC(<i>&lt;value1&gt;</i> : timestamp, [<i>&lt;value2&gt;</i> : string]) => timestamp</b></code><br/><br/>
Converte o carimbo de data/hora em UTC. Você pode passar um fuso horário opcional na forma de ' GMT ', ' PST ', ' UTC ', ' América/Cayman '. Ele é padronizado para os SimpleDateFormat atuais do timezoneRefer Java para os formatos disponíveis. https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html * ``toUTC(currentTimeStamp()) == toTimestamp('2050-12-12 19:18:12') -> false``
* ``toUTC(currentTimeStamp(), 'Asia/Seoul') != toTimestamp('2050-12-12 19:18:12') -> true``
___
### <code>translate</code>
<code><b>translate(<i>&lt;string to translate&gt;</i> : string, <i>&lt;lookup characters&gt;</i> : string, <i>&lt;replace characters&gt;</i> : string) => string</b></code><br/><br/>
Substituir um conjunto de caracteres por outro conjunto de caracteres na cadeia de caracteres. Os caracteres têm 1 a 1 substituto * ``translate('(bojjus)', '()', '[]') -> '[bojjus]'``
* ``translate('(gunchus)', '()', '[') -> '[gunchus'``
___
### <code>trim</code>
<code><b>trim(<i>&lt;string to trim&gt;</i> : string, [<i>&lt;trim characters&gt;</i> : string]) => string</b></code><br/><br/>
Corta uma cadeia de caracteres de caractere à esquerda e à direita. Se o segundo parâmetro não for especificado, ele cortará o espaço em branco. Caso contrário, apara qualquer personagem especificado no segundo parâmetro * ``trim('  dumbo  ') -> 'dumbo'``
* ``trim('!--!du!mbo!', '-!') -> 'du!mbo'``
___
### <code>true</code>
<code><b>true() => boolean</b></code><br/><br/>
Sempre retorna um valor verdadeiro. Utilize a sintaxe da função (verdadeira)se existir uma coluna chamada "verdadeira" * ``(10 + 20 == 30) -> true``
* ``(10 + 20 == 30) -> true()``
___
### <code>typeMatch</code>
<code><b>typeMatch(<i>&lt;type&gt;</i> : string, <i>&lt;base type&gt;</i> : string) => boolean</b></code><br/><br/>
Corresponde ao tipo da coluna. Só pode ser utilizado em expressões de padrão.número corresponde a curto, inteiro, longo, duplo, flutuante ou decimal, fósforos integrais curtos, inteiros, jogos longos, fracionados duplos, flutuantes, decimais e data-hora de partidas de data ou data de marca ção * ``typeMatch(type, 'number')``
* ``typeMatch('date', 'datetime')``
___
### <code>upper</code>
<code><b>upper(<i>&lt;value1&gt;</i> : string) => string</b></code><br/><br/>
Maiúsculas uma cadeia * ``upper('bojjus') -> 'BOJJUS'``
___
### <code>uuid</code>
<code><b>uuid() => string</b></code><br/><br/>
Devolve o * ``uuid()``uUID gerado 
___
### <code>weekOfYear</code>
<code><b>weekOfYear(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
Recebe a semana do ano dada uma data * ``weekOfYear(toDate('2008-02-20')) -> 8``
___
### <code>weeks</code>
<code><b>weeks(<i>&lt;value1&gt;</i> : integer) => long</b></code><br/><br/>
Duração em milissegundos durante o número de semanas * ``weeks(2) -> 1209600000L``
___
### <code>xor</code>
<code><b>xor(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : boolean) => boolean</b></code><br/><br/>
Operador XOR lógico. O mesmo que o operador ^ * ``xor(true, false) -> true``
* ``xor(true, true) -> false``
* ``true ^ false -> true``
___
### <code>year</code>
<code><b>year(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
Obtém o valor do ano de uma data * ``year(toDate('2012-8-8')) -> 2012``funções 
## Agregado As seguintes funções só estão disponíveis em transformações agregadas, pivô, despivote e janelas ___
### <code>avg</code>
<code><b>avg(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
Obtém a média de valores de uma coluna * ``avg(sales)``
___
### <code>avgIf</code>
<code><b>avgIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => number</b></code><br/><br/>
Com base num critério obtém a média dos valores de uma coluna * ``avgIf(region == 'West', sales)``
___
### <code>count</code>
<code><b>count([<i>&lt;value1&gt;</i> : any]) => long</b></code><br/><br/>
Obtém a contagem de valores agregados. Se as colunas opcionais forem especificadas, ignora os valores NULOs na contagem * ``count(custId)``
* ``count(custId, custName)``
* ``count()``
* ``count(iif(isNull(custId), 1, NULL))``
___
### <code>countDistinct</code>
<code><b>countDistinct(<i>&lt;value1&gt;</i> : any, [<i>&lt;value2&gt;</i> : any], ...) => long</b></code><br/><br/>
Obtém a contagem agregada de valores distintos de um conjunto de colunas * ``countDistinct(custId, custName)``
___
### <code>countIf</code>
<code><b>countIf(<i>&lt;value1&gt;</i> : boolean, [<i>&lt;value2&gt;</i> : any]) => long</b></code><br/><br/>
Com base em um critério, obtém a contagem agregada de valores. Se a coluna opcional for especificada, ignora os valores NULOs na contagem * ``countIf(state == 'CA' && commission < 10000, name)``
___
### <code>covariancePopulation</code>
<code><b>covariancePopulation(<i>&lt;value1&gt;</i> : number, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Obtém o entórico populacional entre duas colunas * ``covariancePopulation(sales, profit)``
___
### <code>covariancePopulationIf</code>
<code><b>covariancePopulationIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number, <i>&lt;value3&gt;</i> : number) => double</b></code><br/><br/>
Com base num critério, obtém a população de duas colunas * ``covariancePopulationIf(region == 'West', sales)``
___
### <code>covarianceSample</code>
<code><b>covarianceSample(<i>&lt;value1&gt;</i> : number, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Obtém a amostra de duas colunas * ``covarianceSample(sales, profit)``
___
### <code>covarianceSampleIf</code>
<code><b>covarianceSampleIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number, <i>&lt;value3&gt;</i> : number) => double</b></code><br/><br/>
Com base num critério, obtém a covariance da amostra de duas colunas * ``covarianceSampleIf(region == 'West', sales, profit)``
___
### <code>first</code>
<code><b>first(<i>&lt;value1&gt;</i> : any, [<i>&lt;value2&gt;</i> : boolean]) => any</b></code><br/><br/>
Obtém o primeiro valor de um grupo de colunas. Se o segundo parâmetro ignorar Nulls, presume-se que * ``first(sales)``
* ``first(sales, false)``falsas  
___
### <code>kurtosis</code>
<code><b>kurtosis(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Obtém a kurtose de uma coluna * ``kurtosis(sales)``
___
### <code>kurtosisIf</code>
<code><b>kurtosisIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Com base num critério, obtém a kurtose de uma coluna * ``kurtosisIf(region == 'West', sales)``
___
### <code>last</code>
<code><b>last(<i>&lt;value1&gt;</i> : any, [<i>&lt;value2&gt;</i> : boolean]) => any</b></code><br/><br/>
Obtém o último valor de um grupo de colunas. Se o segundo parâmetro ignorar Nulls, presume-se que * ``last(sales)``
* ``last(sales, false)``falsas  
___
### <code>max</code>
<code><b>max(<i>&lt;value1&gt;</i> : any) => any</b></code><br/><br/>
Obtém o valor máximo de uma coluna * ``max(sales)``
___
### <code>maxIf</code>
<code><b>maxIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
Com base num critério, obtém o valor máximo de uma coluna * ``maxIf(region == 'West', sales)``
___
### <code>mean</code>
<code><b>mean(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
Obtém a média de valores de uma coluna. O mesmo que a 
___
### <code>meanIf</code>* ``mean(sales)`` da AVG 
<code><b>meanIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => number</b></code><br/><br/>
Com base em um critério, obtém a média de valores de uma coluna. O mesmo que avgSe * ``meanIf(region == 'West', sales)``
___
### <code>min</code>
<code><b>min(<i>&lt;value1&gt;</i> : any) => any</b></code><br/><br/>
Obtém o valor mínimo de uma coluna * ``min(sales)``
___
### <code>minIf</code>
<code><b>minIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
Com base em critérios, obtém o valor mínimo de uma coluna * ``minIf(region == 'West', sales)``
___
### <code>skewness</code>
<code><b>skewness(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Obtém a distorção de uma coluna * ``skewness(sales)``
___
### <code>skewnessIf</code>
<code><b>skewnessIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Com base num critério, obtém a distorção de uma coluna * ``skewnessIf(region == 'West', sales)``
___
### <code>stddev</code>
<code><b>stddev(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Obtém o desvio padrão de uma coluna * ``stdDev(sales)``
___
### <code>stddevIf</code>
<code><b>stddevIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Com base num critério, obtém o desvio padrão de uma coluna * ``stddevIf(region == 'West', sales)``
___
### <code>stddevPopulation</code>
<code><b>stddevPopulation(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Obtém a população desvio padrão de uma coluna * ``stddevPopulation(sales)``
___
### <code>stddevPopulationIf</code>
<code><b>stddevPopulationIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Com base num critério, obtém a população de desvio padrão de uma coluna * ``stddevPopulationIf(region == 'West', sales)``
___
### <code>stddevSample</code>
<code><b>stddevSample(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Obtém a amostra de desvio padrão de uma coluna * ``stddevSample(sales)``
___
### <code>stddevSampleIf</code>
<code><b>stddevSampleIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Com base num critério, obtém o desvio padrão da amostra de uma coluna * ``stddevSampleIf(region == 'West', sales)``
___
### <code>sum</code>
<code><b>sum(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
Obtém a soma agregada de uma coluna numérica * ``sum(col)``
___
### <code>sumDistinct</code>
<code><b>sumDistinct(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
Obtém a soma agregada de valores distintos de uma coluna numérica * ``sumDistinct(col)``
___
### <code>sumDistinctIf</code>
<code><b>sumDistinctIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => number</b></code><br/><br/>
Com base em critérios, obtém a soma agregada de uma coluna numérica. A condição pode basear-se em qualquer coluna * ``sumDistinctIf(state == 'CA' && commission < 10000, sales)``
* ``sumDistinctIf(true, sales)``
___
### <code>sumIf</code>
<code><b>sumIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => number</b></code><br/><br/>
Com base em critérios, obtém a soma agregada de uma coluna numérica. A condição pode basear-se em qualquer coluna * ``sumIf(state == 'CA' && commission < 10000, sales)``
* ``sumIf(true, sales)``
___
### <code>variance</code>
<code><b>variance(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Obtém a variação de uma coluna * ``variance(sales)``
___
### <code>varianceIf</code>
<code><b>varianceIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Com base num critério, obtém a variação de uma coluna * ``varianceIf(region == 'West', sales)``
___
### <code>variancePopulation</code>
<code><b>variancePopulation(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Obtém a variação populacional de uma coluna * ``variancePopulation(sales)``
___
### <code>variancePopulationIf</code>
<code><b>variancePopulationIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Com base num critério, obtém a variação populacional de uma coluna * ``variancePopulationIf(region == 'West', sales)``
___
### <code>varianceSample</code>
<code><b>varianceSample(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Obtém a variância imparcial de uma coluna * ``varianceSample(sales)``
___
### <code>varianceSampleIf</code>
<code><b>varianceSampleIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Com base em critérios, obtém a variação imparcial de uma coluna * ``varianceSampleIf(region == 'West', sales)``
## funções da Janela As seguintes funções só estão disponíveis em transformações de janelas ___
### <code>cumeDist</code>
<code><b>cumeDist() => integer</b></code><br/><br/>
A função CumeDist computa a posição de um valor em relação a todos os valores na partição. O resultado é o número de linhas precedentes ou iguais à linha atual na ordenação da partição dividida pelo número total de linhas na partição da janela. Os valores de ligação na ordenação serão avaliados como a mesma posição.
* ``cumeDist()``
___
### <code>denseRank</code>
<code><b>denseRank(<i>&lt;value1&gt;</i> : any, ...) => integer</b></code><br/><br/>
Computa a classificação de um valor em um grupo de valores. O resultado é um mais o número de linhas precedentes ou iguais à linha atual na ordenação da partição. Os valores não produzirão lacunas na sequência. Dense Rank funciona mesmo quando os dados não são classificados e procura mudanças nos valores * ``denseRank(salesQtr, salesAmt)``
___
### <code>lag</code>
<code><b>lag(<i>&lt;value&gt;</i> : any, [<i>&lt;number of rows to look before&gt;</i> : number], [<i>&lt;default value&gt;</i> : any]) => any</b></code><br/><br/>
Obtém o valor do primeiro parâmetro avaliado n linhas antes da linha atual. O segundo parâmetro é o número de linhas a serem examinadas e o valor padrão é 1. Se não houver tantas linhas, um valor de nulidade é devolvido a menos que um valor predefinido seja especificado * ``lag(amount, 2)``
* ``lag(amount, 2000, 100)``
___
### <code>lead</code>
<code><b>lead(<i>&lt;value&gt;</i> : any, [<i>&lt;number of rows to look after&gt;</i> : number], [<i>&lt;default value&gt;</i> : any]) => any</b></code><br/><br/>
Obtém o valor do primeiro parâmetro avaliado n linhas após a linha atual. O segundo parâmetro é o número de linhas a serem examinadas e o valor padrão é 1. Se não houver tantas linhas, um valor de nulidade é devolvido a menos que um valor predefinido seja especificado * ``lead(amount, 2)``
* ``lead(amount, 2000, 100)``
___
### <code>nTile</code>
<code><b>nTile([<i>&lt;value1&gt;</i> : integer]) => integer</b></code><br/><br/>
A função NTile divide as linhas de cada partição de janela em `n` buckets variando de 1 para no máximo `n`. Os valores de Bucket serão diferentes por no máximo 1. Se o número de linhas na partição não dividir uniformemente o número de buckets, os valores restantes serão distribuídos um por Bucket, começando com o primeiro Bucket. A função NTile é útil para o cálculo de tertiles, quartils, decis e outras estatísticas de resumo comuns. A função calcula duas variáveis durante a inicialização: o tamanho de um Bucket regular terá uma linha extra adicionada a ele. Ambas as variáveis são baseadas no tamanho da partição atual. Durante o processo de cálculo, a função controla o número da linha atual, o número do Bucket atual e o número da linha na qual o Bucket será alterado (bucketThreshold). Quando o número da linha atual atingir o limite de Bucket, o valor do Bucket será aumentado em um e o limite será aumentado pelo tamanho do Bucket (mais um extra, se o Bucket atual for preenchido).
* ``nTile()``
* ``nTile(numOfBuckets)``
___
### <code>rank</code>
<code><b>rank(<i>&lt;value1&gt;</i> : any, ...) => integer</b></code><br/><br/>
Computa a classificação de um valor em um grupo de valores. O resultado é um mais o número de linhas precedentes ou iguais à linha atual na ordenação da partição. Os valores produzirão lacunas na sequência. O rank funciona mesmo quando os dados não são classificados e procura mudanças nos valores * ``rank(salesQtr, salesAmt)``
___
### <code>rowNumber</code>
<code><b>rowNumber() => integer</b></code><br/><br/>
Atribui uma numeração de linha sequencial para linhas em uma janela que começa com 1 * ``rowNumber()``

## <a name="next-steps"></a>Passos seguintes

[Saiba como usar o Expression Builder](concepts-data-flow-expression-builder.md).
