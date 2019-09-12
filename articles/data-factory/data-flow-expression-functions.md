---
title: Funções de expressão no recurso de fluxo de dados de mapeamento do Azure Data Factory
description: Saiba mais sobre as funções de expressão no fluxo de dados de mapeamento.
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/15/2019
ms.openlocfilehash: f13d156ea34fc1c909704a18f645be62a6e09f90
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/11/2019
ms.locfileid: "70881330"
---
# <a name="data-transformation-expressions-in-mapping-data-flow"></a>Expressões de transformação de dados no fluxo de dados de mapeamento 

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

## <a name="expression-functions"></a>Funções de expressão

No Data Factory, use a linguagem de expressão do recurso de fluxo de dados de mapeamento para configurar as transformações de dados.

___
### <code>abs</code>
<code><b>abs(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
Módulo positivo do par de números.
* ``abs(-20) -> 20``  
* ``abs(10) -> 10``  
___
### <code>acos</code>
<code><b>acos(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Calcula um valor inverso do cosseno* ``acos(1) -> 0.0``  
___
### <code>add</code>
<code><b>add(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
Adiciona um par de cadeias de caracteres ou números. Adiciona uma data a um número de dias. Acrescenta uma matriz de tipo semelhante para outra. Mesmo que o operador +* ``add(10, 20) -> 30``  
* ``10 + 20 -> 30``  
* ``add('ice', 'cream') -> 'icecream'``  
* ``'ice' + 'cream' + ' cone' -> 'icecream cone'``  
* ``add(toDate('2012-12-12'), 3) -> 2012-12-15 (date value)``  
* ``toDate('2012-12-12') + 3 -> 2012-12-15 (date value)``  
* ``[10, 20] + [30, 40] => [10, 20, 30, 40]``  
___
### <code>addDays</code>
<code><b>addDays(<i>&lt;date/timestamp&gt;</i> : datetime, <i>&lt;days to add&gt;</i> : integral) => datetime</b></code><br/><br/>
Adicione dias a uma data ou carimbo de hora. O mesmo que o operador + para data* ``addDays(toDate('2016-08-08'), 1) -> 2016-08-09``
___
### <code>addMonths</code>
<code><b>addMonths(<i>&lt;date/timestamp&gt;</i> : datetime, <i>&lt;months to add&gt;</i> : integral) => datetime</b></code><br/><br/>
Adicionar meses a uma data ou timestamp* ``addMonths(toDate('2016-08-31'), 1) -> 2016-09-30``
* ``addMonths(toTimestamp('2016-09-30 10:10:10'), -1) -> 2016-08-31 10:10:10``
___
### <code>and</code>
<code><b>and(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : boolean) => boolean</b></code><br/><br/>
Operador AND lógico. O mesmo que & &* ``and(true, false) -> false``
* ``true && false -> false``
___
### <code>asin</code>
<code><b>asin(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Calcula um valor inverso do seno* ``asin(0) -> 0.0``
___
### <code>atan</code>
<code><b>atan(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Calcula um valor tangente inverso* ``atan(0) -> 0.0``
___
### <code>atan2</code>
<code><b>atan2(<i>&lt;value1&gt;</i> : number, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Retorna o ângulo em radianos entre o eixo x positivo de um plano e o ponto fornecido pelas coordenadas* ``atan2(0, 0) -> 0.0``
___
### <code>avg</code>
<code><b>avg(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
Obtém a média de valores de uma coluna* ``avg(sales) -> 7523420.234``
___
### <code>avgIf</code>
<code><b>avgIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => number</b></code><br/><br/>
Com base em um critério Obtém a média de valores de uma coluna* ``avgIf(region == 'West', sales) -> 7523420.234``
___
### <code>byName</code>
<code><b>byName(<i>&lt;column name&gt;</i> : string) => any</b></code><br/><br/>
Seleciona um valor de coluna por nome no fluxo. Se houver várias correspondências, a primeira correspondência será retornada. Se nenhuma correspondência retornar um valor nulo. O valor retornado deve ser do tipo convertido por uma das funções de conversão de tipo (TO_DATE, TO_STRING...).  Nomes de coluna conhecidos em tempo de design devem ser abordados apenas por seu nome. Não há suporte para entradas computadas, mas você pode usar substituições de parâmetro* ``toString(byName('parent')) -> appa``
* ``toLong(byName('income')) -> 9000000000009``
* ``toBoolean(byName('foster')) -> false``
* ``toLong(byName($debtCol)) -> 123456890``
* ``birthDate -> 12/31/2050``
* ``toString(byName('Bogus Column')) -> NULL``
___
### <code>byPosition</code>
<code><b>byPosition(<i>&lt;position&gt;</i> : integer) => any</b></code><br/><br/>
Seleciona um valor de coluna por sua posição relativa (1 com base) no fluxo. Se a posição estiver fora dos limites, ela retornará um valor nulo. O valor retornado deve ser do tipo convertido por uma das funções de conversão de tipo (TO_DATE, TO_STRING...) Não há suporte para entradas computadas, mas você pode usar substituições de parâmetro* ``toString(byPosition(1)) -> amma``
* ``toDecimal(byPosition(2), 10, 2) -> 199990.99``
* ``toBoolean(byName(4)) -> false``
* ``toString(byName($colName)) -> family``
* ``toString(byPosition(1234)) -> NULL``
___
### <code>case</code>
<code><b>case(<i>&lt;condition&gt;</i> : boolean, <i>&lt;true_expression&gt;</i> : any, <i>&lt;false_expression&gt;</i> : any, ...) => any</b></code><br/><br/>
Com base em condições alternativas, aplica-se um valor ou outro. Se o número de entradas for par, o outro será nulo para a última condição* ``case(custType == 'Premium', 10, 4.5)``
* ``case(custType == 'Premium', price*0.95, custType == 'Elite',   price*0.9, price*2)``
* ``case(dayOfWeek(saleDate) == 1, 'Sunday', dayOfWeek(saleDate) == 6, 'Saturday')``
___
### <code>cbrt</code>
<code><b>cbrt(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Calcular a raiz do cubo de um número* ``cbrt(8) -> 2.0``
___
### <code>ceil</code>
<code><b>ceil(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
Retorna o menor inteiro que não é menor que o número* ``ceil(-0.1) -> 0``
___
### <code>coalesce</code>
<code><b>coalesce(<i>&lt;value1&gt;</i> : any, ...) => any</b></code><br/><br/>
Retorna o primeiro valor não nulo de um conjunto de entradas. Todas as entradas devem ser do mesmo tipo* ``coalesce(10, 20) -> 10``
* ``coalesce(toString(null), toString(null), 'dumbo', 'bo', 'go') -> 'dumbo'``
___
### <code>compare</code>
<code><b>compare(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => integer</b></code><br/><br/>
Compara dois valores do mesmo tipo. Retorna um inteiro negativo se value1 < value2, 0 se value1 = = value2, valor positivo se value1 > value2* ``(compare(12, 24) < 1) -> true``
* ``(compare('dumbo', 'dum') > 0) -> true``
___
### <code>concat</code>
<code><b>concat(<i>&lt;this&gt;</i> : string, <i>&lt;that&gt;</i> : string, ...) => string</b></code><br/><br/>
Concatena um número variável de cadeias de caracteres em conjunto. O mesmo que o operador + com cadeias de caracteres* ``concat('Awesome', 'Cool', 'Product') -> 'AwesomeCoolProduct'``
* ``'Awesome' + 'Cool' + 'Product' -> 'AwesomeCoolProduct'``
* ``concat(addrLine1, ' ', addrLine2, ' ', city, ' ', state, ' ', zip)``
* ``addrLine1 + ' ' + addrLine2 + ' ' + city + ' ' + state + ' ' + zip``
___
### <code>concatWS</code>
<code><b>concatWS(<i>&lt;separator&gt;</i> : string, <i>&lt;this&gt;</i> : string, <i>&lt;that&gt;</i> : string, ...) => string</b></code><br/><br/>
Concatena um número variável de cadeias de caracteres junto com um separador. O primeiro parâmetro é o separador* ``concatWS(' ', 'Awesome', 'Cool', 'Product') -> 'Awesome Cool Product'``
* ``concatWS(' ' , addrLine1, addrLine2, city, state, zip) ->``
* ``concatWS(',' , toString(order_total), toString(order_discount))``
___
### <code>cos</code>
<code><b>cos(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Calcula um valor cosseno* ``cos(10) -> -0.83907152907``
___
### <code>cosh</code>
<code><b>cosh(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Calcula um cosseno hiperbólico de um valor* ``cosh(0) -> 1.0``
___
### <code>count</code>
<code><b>count([<i>&lt;value1&gt;</i> : any]) => long</b></code><br/><br/>
Obtém a contagem de valores agregados. Se a coluna (s) opcional for especificada, ela ignorará valores nulos na contagem* ``count(custId) -> 100``
* ``count(custId, custName) -> 50``
* ``count() -> 125``
* ``count(iif(isNull(custId), 1, NULL)) -> 5``
___
### <code>countDistinct</code>
<code><b>countDistinct(<i>&lt;value1&gt;</i> : any, [<i>&lt;value2&gt;</i> : any], ...) => long</b></code><br/><br/>
Obtém a contagem agregada de valores distintos de um conjunto de colunas* ``countDistinct(custId, custName) -> 60``
___
### <code>countIf</code>
<code><b>countIf(<i>&lt;value1&gt;</i> : boolean, [<i>&lt;value2&gt;</i> : any]) => long</b></code><br/><br/>
Com base em um critério, obtém a contagem agregada de valores. Se a coluna opcional for especificada, ela ignorará valores nulos na contagem* ``countIf(state == 'CA' && commission < 10000, name) -> 100``
___
### <code>covariancePopulation</code>
<code><b>covariancePopulation(<i>&lt;value1&gt;</i> : number, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Obtém a covariância de população entre duas colunas* ``covariancePopulation(sales, profit) -> 122.12``
___
### <code>covariancePopulationIf</code>
<code><b>covariancePopulationIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number, <i>&lt;value3&gt;</i> : number) => double</b></code><br/><br/>
Com base em critérios, obtém a covariância de população de duas colunas* ``covariancePopulationIf(region == 'West', sales) -> 122.12``
___
### <code>covarianceSample</code>
<code><b>covarianceSample(<i>&lt;value1&gt;</i> : number, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Obtém a covariância de exemplo de duas colunas* ``covarianceSample(sales, profit) -> 122.12``
___
### <code>covarianceSampleIf</code>
<code><b>covarianceSampleIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number, <i>&lt;value3&gt;</i> : number) => double</b></code><br/><br/>
Com base em critérios, obtém a covariância de exemplo de duas colunas* ``covarianceSampleIf(region == 'West', sales, profit) -> 122.12``
___
### <code>crc32</code>
<code><b>crc32(<i>&lt;value1&gt;</i> : any, ...) => long</b></code><br/><br/>
Calcula o hash CRC32 do conjunto de colunas de tipos de dados primitivos variados de acordo com um comprimento de bit que só pode ser de valores 0 (256), 224, 256, 384, 512. Ele pode ser usado para calcular uma impressão digital de uma linha* ``crc32(256, 'gunchus', 8.2, 'bojjus', true, toDate('2010-4-4')) -> 3630253689``
___
### <code>cumeDist</code>
<code><b>cumeDist() => integer</b></code><br/><br/>
A função CumeDist computa a posição de um valor em relação a todos os valores na partição. O resultado é o número de linhas precedentes ou iguais à linha atual na ordenação da partição dividida pelo número total de linhas na partição da janela. Os valores de ligação na ordenação serão avaliados como a mesma posição.
* ``cumeDist() -> 1``
___
### <code>currentDate</code>
<code><b>currentDate([<i>&lt;value1&gt;</i> : string]) => date</b></code><br/><br/>
Obtém a data atual em que esse trabalho começa a ser executado. Você pode passar um fuso horário opcional na forma de ' GMT ', ' PST ', ' UTC ', ' América/Cayman '. O fuso horário local é usado como o padrão.
* ``currentDate() -> 12-12-2030``
* ``currentDate('PST') -> 12-31-2050``
___
### <code>currentTimestamp</code>
<code><b>currentTimestamp() => timestamp</b></code><br/><br/>
Obtém o carimbo de data/hora atual quando o trabalho começa a ser executado com o fuso horário local* ``currentTimestamp() -> 12-12-2030T12:12:12``
___
### <code>currentUTC</code>
<code><b>currentUTC([<i>&lt;value1&gt;</i> : string]) => timestamp</b></code><br/><br/>
Obtém o carimbo de data/hora atual como UTC. Você pode passar um fuso horário opcional na forma de ' GMT ', ' PST ', ' UTC ', ' América/Cayman '. Ele é padronizado para o fuso horário atual* ``currentUTC() -> 12-12-2030T19:18:12``
* ``currentUTC('Asia/Seoul') -> 12-13-2030T11:18:12``
___
### <code>dayOfMonth</code>
<code><b>dayOfMonth(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
Obtém o dia do mês de acordo com uma data* ``dayOfMonth(toDate('2018-06-08')) -> 08``
___
### <code>dayOfWeek</code>
<code><b>dayOfWeek(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
Obtém o dia da semana de acordo com uma data. 1-domingo, 2-segunda-feira..., 7-sábado* ``dayOfWeek(toDate('2018-06-08')) -> 7``
___
### <code>dayOfYear</code>
<code><b>dayOfYear(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
Obtém o dia do ano de acordo com uma data* ``dayOfYear(toDate('2016-04-09')) -> 100``
___
### <code>degrees</code>
<code><b>degrees(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Converte radianos em graus* ``degrees(3.141592653589793) -> 180``
___
### <code>denseRank</code>
<code><b>denseRank(<i>&lt;value1&gt;</i> : any, ...) => integer</b></code><br/><br/>
Computa a classificação de um valor em um grupo de valores. O resultado é um mais o número de linhas precedentes ou iguais à linha atual na ordenação da partição. Os valores não produzirão lacunas na sequência. A classificação densa funciona mesmo quando os dados não são classificados e procura alterações nos valores* ``denseRank(salesQtr, salesAmt) -> 1``
___
### <code>divide</code>
<code><b>divide(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
Divide o par de números. Mesmo que o operador/* ``divide(20, 10) -> 2``
* ``20 / 10 -> 2``
___
### <code>endsWith</code>
<code><b>endsWith(<i>&lt;string&gt;</i> : string, <i>&lt;substring to check&gt;</i> : string) => boolean</b></code><br/><br/>
Verifica se a cadeia de caracteres termina com a cadeia de caracteres fornecida* ``endsWith('great', 'eat') -> true``
___
### <code>equals</code>
<code><b>equals(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code><br/><br/>
Operador de comparação de Equals. Mesmo que o operador = =* ``equals(12, 24) -> false``
* ``12==24 -> false``
* ``'bad'=='bad' -> true``
* ``'good'== NULL -> false``
* ``NULL===NULL -> false``
___
### <code>equalsIgnoreCase</code>
<code><b>equalsIgnoreCase(<i>&lt;value1&gt;</i> : string, <i>&lt;value2&gt;</i> : string) => boolean</b></code><br/><br/>
Operador EQUAL de comparação ignorando maiúsculas e minúsculas. Mesmo que < = operador >* ``'abc'<==>'abc' -> true``
* ``equalsIgnoreCase('abc', 'Abc') -> true``
___
### <code>factorial</code>
<code><b>factorial(<i>&lt;value1&gt;</i> : number) => long</b></code><br/><br/>
Calcular o fatorial de um número* ``factorial(5) -> 120``
___
### <code>false</code>
<code><b>false() => boolean</b></code><br/><br/>
Sempre retorna um valor falso. Use a sintaxe da função (false ()) se houver uma coluna chamada ' false '* ``isDiscounted == false()``
* ``isDiscounted() == false``
___
### <code>first</code>
<code><b>first(<i>&lt;value1&gt;</i> : any, [<i>&lt;value2&gt;</i> : boolean]) => any</b></code><br/><br/>
Obtém o primeiro valor de um grupo de colunas. Se o segundo parâmetro ignoreNulls for omitido, presume-se false* ``first(sales) -> 12233.23``
* ``first(sales, false) -> NULL``
___
### <code>floor</code>
<code><b>floor(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
Retorna o maior inteiro que não é maior que o número* ``floor(-0.1) -> -1``
___
### <code>fromUTC</code>
<code><b>fromUTC(<i>&lt;value1&gt;</i> : timestamp, [<i>&lt;value2&gt;</i> : string]) => timestamp</b></code><br/><br/>
Converte para o carimbo de data/hora do UTC. Opcionalmente, você pode passar o fuso horário na forma de ' GMT ', ' PST ', ' UTC ', ' América/Cayman '. Ele é padronizado para o fuso horário atual* ``fromUTC(currentTimeStamp()) -> 12-12-2030T19:18:12``
* ``fromUTC(currentTimeStamp(), 'Asia/Seoul') -> 12-13-2030T11:18:12``
___
### <code>greater</code>
<code><b>greater(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code><br/><br/>
Operador maior de comparação. O mesmo que > operador* ``greater(12, 24) -> false``
* ``'abcd' > 'abc' -> true``
___
### <code>greaterOrEqual</code>
<code><b>greaterOrEqual(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code><br/><br/>
Comparação de operador maior ou igual a. Mesmo que > = Operator* ``greaterOrEqual(12, 12) -> false``
* ``'abcd' >= 'abc' -> true``
___
### <code>greatest</code>
<code><b>greatest(<i>&lt;value1&gt;</i> : any, ...) => any</b></code><br/><br/>
Retorna o valor máximo entre a lista de valores como entrada. Retornará NULL se todas as entradas forem nulas* ``greatest(10, 30, 15, 20) -> 30``
* ``greatest(toDate('12/12/2010'), toDate('12/12/2011'), toDate('12/12/2000')) -> '12/12/2011'``
___
### <code>hour</code>
<code><b>hour(<i>&lt;value1&gt;</i> : timestamp, [<i>&lt;value2&gt;</i> : string]) => integer</b></code><br/><br/>
Obtém o valor de hora de um timestamp. Você pode passar um fuso horário opcional na forma de ' GMT ', ' PST ', ' UTC ', ' América/Cayman '. O fuso horário local é usado como o padrão.
* ``hour(toTimestamp('2009-07-30T12:58:59')) -> 12``
* ``hour(toTimestamp('2009-07-30T12:58:59'), 'PST') -> 12``
___
### <code>iif</code>
<code><b>iif(<i>&lt;condition&gt;</i> : boolean, <i>&lt;true_expression&gt;</i> : any, [<i>&lt;false_expression&gt;</i> : any]) => any</b></code><br/><br/>
Com base em uma condição, aplica-se um valor ou outro. Se outro não for especificado, ele será considerado nulo. Ambos os valores devem ser compatíveis (Numeric, String...)* ``iif(custType == 'Premium', 10, 4.5)``
* ``iif(amount > 100, 'High')``
* ``iif(dayOfWeek(saleDate) == 6, 'Weekend', 'Weekday')``
___
### <code>in</code>
<code><b>in(<i>&lt;array of items&gt;</i> : array, <i>&lt;item to find&gt;</i> : any) => boolean</b></code><br/><br/>
Verifica se um item está na matriz* ``in([10, 20, 30], 10) -> true``
* ``in(['good', 'kid'], 'bad') -> false``
___
### <code>initCap</code>
<code><b>initCap(<i>&lt;value1&gt;</i> : string) => string</b></code><br/><br/>
Converte a primeira letra de cada palavra em maiúsculas. As palavras são identificadas como separadas por espaço em branco* ``initCap('cool iceCREAM') -> 'Cool IceCREAM'``
___
### <code>instr</code>
<code><b>instr(<i>&lt;string&gt;</i> : string, <i>&lt;substring to find&gt;</i> : string) => integer</b></code><br/><br/>
Localiza a posição (1 com base) da subcadeia de caracteres em uma cadeia de caracteres. 0 será retornado se não for encontrado* ``instr('great', 'eat') -> 3``
* ``instr('microsoft', 'o') -> 7``
* ``instr('good', 'bad') -> 0``
___
### <code>isDelete</code>
<code><b>isDelete([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
Verifica se a linha está marcada para exclusão. Para transformações que levam mais de um fluxo de entrada, você pode passar o índice (baseado em 1) do fluxo. O valor padrão para o índice de fluxo é 1* ``isDelete() -> true``
* ``isDelete(1) -> false``
___
### <code>isError</code>
<code><b>isError([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
Verifica se a linha está marcada como erro. Para transformações que levam mais de um fluxo de entrada, você pode passar o índice (baseado em 1) do fluxo. O valor padrão para o índice de fluxo é 1* ``isError() -> true``
* ``isError(1) -> false``
___
### <code>isIgnore</code>
<code><b>isIgnore([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
Verifica se a linha está marcada para ser ignorada. Para transformações que levam mais de um fluxo de entrada, você pode passar o índice (baseado em 1) do fluxo. O valor padrão para o índice de fluxo é 1* ``isIgnore() -> true``
* ``isIgnore(1) -> false``
___
### <code>isInsert</code>
<code><b>isInsert([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
Verifica se a linha está marcada para inserção. Para transformações que levam mais de um fluxo de entrada, você pode passar o índice (baseado em 1) do fluxo. O valor padrão para o índice de fluxo é 1* ``isInsert() -> true``
* ``isInsert(1) -> false``
___
### <code>isMatch</code>
<code><b>isMatch([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
Verifica se a linha é correspondida na pesquisa. Para transformações que levam mais de um fluxo de entrada, você pode passar o índice (baseado em 1) do fluxo. O valor padrão para o índice de fluxo é 1* ``isMatch() -> true``
* ``isMatch(1) -> false``
___
### <code>isNull</code>
<code><b>isNull(<i>&lt;value1&gt;</i> : any) => boolean</b></code><br/><br/>
Verifica se o valor é nulo* ``isNull(NULL()) -> true``
* ``isNull('') -> false'``
___
### <code>isUpdate</code>
<code><b>isUpdate([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
Verifica se a linha está marcada para atualização. Para transformações que levam mais de um fluxo de entrada, você pode passar o índice (baseado em 1) do fluxo. O valor padrão para o índice de fluxo é 1* ``isUpdate() -> true``
* ``isUpdate(1) -> false``
___
### <code>kurtosis</code>
<code><b>kurtosis(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Obtém a curtose de uma coluna* ``kurtosis(sales) -> 122.12``
___
### <code>kurtosisIf</code>
<code><b>kurtosisIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Com base em critérios, obtém a curtose de uma coluna* ``kurtosisIf(region == 'West', sales) -> 122.12``
___
### <code>lag</code>
<code><b>lag(<i>&lt;value&gt;</i> : any, [<i>&lt;number of rows to look before&gt;</i> : number], [<i>&lt;default value&gt;</i> : any]) => any</b></code><br/><br/>
Obtém o valor do primeiro parâmetro avaliado n linhas antes da linha atual. O segundo parâmetro é o número de linhas a serem examinadas e o valor padrão é 1. Se não houver tantas linhas, um valor de NULL será retornado, a menos que um valor padrão seja especificado* ``lag(amount, 2) -> 60``
* ``lag(amount, 2000, 100) -> 100``
___
### <code>last</code>
<code><b>last(<i>&lt;value1&gt;</i> : any, [<i>&lt;value2&gt;</i> : boolean]) => any</b></code><br/><br/>
Obtém o último valor de um grupo de colunas. Se o segundo parâmetro ignoreNulls for omitido, presume-se false* ``last(sales) -> 523.12``
* ``last(sales, false) -> NULL``
___
### <code>lastDayOfMonth</code>
<code><b>lastDayOfMonth(<i>&lt;value1&gt;</i> : datetime) => date</b></code><br/><br/>
Obtém a última data do mês em que uma data foi determinada* ``lastDayOfMonth(toDate('2009-01-12')) -> 2009-01-31``
___
### <code>lead</code>
<code><b>lead(<i>&lt;value&gt;</i> : any, [<i>&lt;number of rows to look after&gt;</i> : number], [<i>&lt;default value&gt;</i> : any]) => any</b></code><br/><br/>
Obtém o valor do primeiro parâmetro avaliado n linhas após a linha atual. O segundo parâmetro é o número de linhas a serem examinadas e o valor padrão é 1. Se não houver tantas linhas, um valor de NULL será retornado, a menos que um valor padrão seja especificado* ``lead(amount, 2) -> 60``
* ``lead(amount, 2000, 100) -> 100``
___
### <code>least</code>
<code><b>least(<i>&lt;value1&gt;</i> : any, ...) => any</b></code><br/><br/>
Comparação menor ou igual a operador. Mesmo que < = Operator* ``least(10, 30, 15, 20) -> 10``
* ``least(toDate('12/12/2010'), toDate('12/12/2011'), toDate('12/12/2000')) -> '12/12/2000'``
___
### <code>left</code>
<code><b>left(<i>&lt;string to subset&gt;</i> : string, <i>&lt;number of characters&gt;</i> : integral) => string</b></code><br/><br/>
Extrai uma subcadeia de caracteres de início no índice 1 com número de caracteres. Mesmo que substring (Str, 1, n)* ``left('bojjus', 2) -> 'bo'``
* ``left('bojjus', 20) -> 'bojjus'``
___
### <code>length</code>
<code><b>length(<i>&lt;value1&gt;</i> : string) => integer</b></code><br/><br/>
Retorna o comprimento da cadeia de caracteres* ``length('kiddo') -> 5``
___
### <code>lesser</code>
<code><b>lesser(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code><br/><br/>
Operador de comparação less. O mesmo que < operador* ``lesser(12 < 24) -> true``
* ``'abcd' < 'abc' -> false``
___
### <code>lesserOrEqual</code>
<code><b>lesserOrEqual(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code><br/><br/>
Comparação menor ou igual a operador. Mesmo que < = Operator* ``lesserOrEqual(12, 12) -> true``
* ``'abcd' <= 'abc' -> false``
___
### <code>levenshtein</code>
<code><b>levenshtein(<i>&lt;from string&gt;</i> : string, <i>&lt;to string&gt;</i> : string) => integer</b></code><br/><br/>
Obtém a distância Levenshtein entre duas cadeias de caracteres* ``levenshtein('boys', 'girls') -> 4``
___
### <code>like</code>
<code><b>like(<i>&lt;string&gt;</i> : string, <i>&lt;pattern match&gt;</i> : string) => boolean</b></code><br/><br/>
O padrão é uma cadeia de caracteres que corresponde literalmente. As exceções são os seguintes símbolos especiais: _ corresponde a um caractere na entrada (semelhante a. em expressões regulares de POSIX),% corresponde a zero ou mais caracteres na entrada (semelhante a. * em expressões regulares de POSIX).
O caractere de escape é ' '. Se um caractere de escape precede um símbolo especial ou outro caractere de escape, o caractere a seguir será correspondido literalmente. É inválido escapar de qualquer outro caractere.
* ``like('icecream', 'ice%') -> true``
___
### <code>locate</code>
<code><b>locate(<i>&lt;substring to find&gt;</i> : string, <i>&lt;string&gt;</i> : string, [<i>&lt;from index - 1-based&gt;</i> : integral]) => integer</b></code><br/><br/>
Localiza a posição (1 com base) da subcadeia de caracteres em uma cadeia de caracteres que inicia uma determinada posição. Se a posição for omitida, ela será considerada do início da cadeia de caracteres. 0 será retornado se não for encontrado* ``locate('eat', 'great') -> 3``
* ``locate('o', 'microsoft', 6) -> 7``
* ``locate('bad', 'good') -> 0``
___
### <code>log</code>
<code><b>log(<i>&lt;value1&gt;</i> : number, [<i>&lt;value2&gt;</i> : number]) => double</b></code><br/><br/>
Calcula o valor do log. Uma base opcional pode ser fornecida mais um número Euler, se usado* ``log(100, 10) -> 2``
___
### <code>log10</code>
<code><b>log10(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Calcula o valor do log com base em 10 base* ``log10(100) -> 2``
___
### <code>lower</code>
<code><b>lower(<i>&lt;value1&gt;</i> : string) => string</b></code><br/><br/>
Minúsculas uma cadeia de caracteres* ``lower('GunChus') -> 'gunchus'``
___
### <code>lpad</code>
<code><b>lpad(<i>&lt;string to pad&gt;</i> : string, <i>&lt;final padded length&gt;</i> : integral, <i>&lt;padding&gt;</i> : string) => string</b></code><br/><br/>
A esquerda pads a cadeia de caracteres pelo preenchimento fornecido até que seja de um determinado comprimento. Se a cadeia de caracteres for igual ou maior que o comprimento, ela será considerada um ' ' LPAD * ``lpad('great', 10, '-') -> '___--great'`` ' de não op 
* ``lpad('great', 4, '-') -> 'great'`` 
* (' Great ', 8, ' < > ')-> ' <<great'``
___
### <code>> LTrim</code>
<code><b>ltrim(<i>&lt;string to trim&gt;</i> : string, <i>&lt;trim characters&gt;</i> : string) => string</b></code><br/><br/>
A esquerda corta uma cadeia de caracteres da entrelinha. Se o segundo parâmetro não for especificado, ele cortará o espaço em branco. Caso contrário, ele apara qualquer caractere especificado no segundo parâmetro* ``ltrim('!--!wor!ld!', '-!') -> 'wor!ld!'``
___
### <code>max</code>
<code><b>max(<i>&lt;value1&gt;</i> : any) => any</b></code><br/><br/>
Obtém o valor máximo de uma coluna* ``MAX(sales) -> 12312131.12``
___
### <code>maxIf</code>
<code><b>maxIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
Com base em um critério, obtém o valor máximo de uma coluna* ``maxIf(region == 'West', sales) -> 99999.56``
___
### <code>md5</code>
<code><b>md5(<i>&lt;value1&gt;</i> : any, ...) => string</b></code><br/><br/>
Calcula o resumo MD5 do conjunto de colunas de tipos de datavariantes primitivos e retorna uma cadeia de caracteres hexadecimal de 32 caracteres. Ele pode ser usado para calcular uma impressão digital de uma linha* ``md5(5, 'gunchus', 8.2, 'bojjus', true, toDate('2010-4-4')) -> 'c1527622a922c83665e49835e46350fe'``
___
### <code>mean</code>
<code><b>mean(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
Obtém a média de valores de uma coluna. Igual ao AVG* ``mean(sales) -> 7523420.234``
___
### <code>meanIf</code>
<code><b>meanIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => number</b></code><br/><br/>
Com base em um critério, obtém a média de valores de uma coluna. O mesmo que avgIf* ``meanIf(region == 'West', sales) -> 7523420.234``
___
### <code>millisecond</code>
<code><b>millisecond(<i>&lt;value1&gt;</i> : timestamp, [<i>&lt;value2&gt;</i> : string]) => integer</b></code><br/><br/>
Obtém o valor de milissegundo de uma data. Você pode passar um fuso horário opcional na forma de ' GMT ', ' PST ', ' UTC ', ' América/Cayman '. O fuso horário local é usado como o padrão.
* ``millisecond(toTimestamp('2009-07-30 12:58:59.871', 'yyyy-MM-dd HH:mm:ss.SSS')) -> 871``
___
### <code>min</code>
<code><b>min(<i>&lt;value1&gt;</i> : any) => any</b></code><br/><br/>
Obtém o valor mínimo de uma coluna* ``min(sales) -> 00.01``
* ``min(orderDate) -> 12/12/2000``
___
### <code>minIf</code>
<code><b>minIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
Com base em um critério, obtém o valor mínimo de uma coluna* ``minIf(region == 'West', sales) -> 00.01``
___
### <code>minus</code>
<code><b>minus(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
Subtrai números. Subtraia de um número de data de dias. Mesmo que o operador-* ``minus(20, 10) -> 10``
* ``20 - 10 -> 10``
* ``minus(toDate('2012-12-15'), 3) -> 2012-12-12 (date value)``
* ``toDate('2012-12-15') - 3 -> 2012-12-13 (date value)``
___
### <code>minute</code>
<code><b>minute(<i>&lt;value1&gt;</i> : timestamp, [<i>&lt;value2&gt;</i> : string]) => integer</b></code><br/><br/>
Obtém o valor de minuto de um carimbo de data/hora. Você pode passar um fuso horário opcional na forma de ' GMT ', ' PST ', ' UTC ', ' América/Cayman '. O fuso horário local é usado como o padrão.
* ``minute(toTimestamp('2009-07-30T12:58:59')) -> 58``
* ``minute(toTimestamp('2009-07-30T12:58:59', 'PST')) -> 58``
___
### <code>mod</code>
<code><b>mod(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
Módulo de um par de números. O mesmo que o operador%* ``mod(20, 8) -> 4``
* ``20 % 8 -> 4``
___
### <code>month</code>
<code><b>month(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
Obtém o valor de mês de uma data ou timestamp* ``month(toDate('2012-8-8')) -> 8``
___
### <code>monthsBetween</code>
<code><b>monthsBetween(<i>&lt;from date/timestamp&gt;</i> : datetime, <i>&lt;to date/timestamp&gt;</i> : datetime, [<i>&lt;time zone&gt;</i> : boolean], [<i>&lt;value4&gt;</i> : string]) => double</b></code><br/><br/>
Obtém o número de meses entre dois datesYou pode passar um fuso horário opcional na forma de ' GMT ', ' PST ', ' UTC ', ' América/Cayman '. O fuso horário local é usado como o padrão.
* ``monthsBetween(toDate('1997-02-28 10:30:00'), toDate('1996-10-30')) -> 3.94959677``
___
### <code>multiply</code>
<code><b>multiply(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
Multiplica o par de números. Mesmo que o operador ** ``multiply(20, 10) -> 200``
* ``20 * 10 -> 200``
___
### <code>nTile</code>
<code><b>nTile([<i>&lt;value1&gt;</i> : integer]) => integer</b></code><br/><br/>
A função NTile divide as linhas de cada partição de janela em `n` buckets que variam de 1 para no máximo `n`. Os valores de Bucket serão diferentes por no máximo 1. Se o número de linhas na partição não dividir uniformemente o número de buckets, os valores restantes serão distribuídos um por Bucket, começando com o primeiro Bucket. A função NTile é útil para o cálculo de tertiles, quartils, decis e outras estatísticas de resumo comuns. A função calcula duas variáveis durante a inicialização: O tamanho de um Bucket regular terá uma linha extra adicionada a ele. Ambas as variáveis são baseadas no tamanho da partição atual. Durante o processo de cálculo, a função controla o número da linha atual, o número do Bucket atual e o número da linha na qual o Bucket será alterado (bucketThreshold). Quando o número da linha atual atingir o limite de Bucket, o valor do Bucket será aumentado em um e o limite será aumentado pelo tamanho do Bucket (mais um extra, se o Bucket atual for preenchido).
* ``nTile() -> 1``
* ``nTile(numOfBuckets) -> 1``
___
### <code>negate</code>
<code><b>negate(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
Nega um número. Transforma números positivos em negativos e vice-versa* ``negate(13) -> -13``
___
### <code>nextSequence</code>
<code><b>nextSequence() => long</b></code><br/><br/>
Retorna a próxima sequência exclusiva. O número é consecutivo apenas dentro de uma partição e é prefixado pela PartitionID* ``nextSequence() -> 12313112``
___
### <code>normalize</code>
<code><b>normalize(<i>&lt;String to normalize&gt;</i> : string) => string</b></code><br/><br/>
Normalizar o valor da cadeia para separar caracteres Unicode acentuados* ``normalize('boys') -> 'boys'``
___
### <code>not</code>
<code><b>not(<i>&lt;value1&gt;</i> : boolean) => boolean</b></code><br/><br/>
Operador de negação lógica* ``not(true) -> false``
* ``not(premium)``
___
### <code>notEquals</code>
<code><b>notEquals(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code><br/><br/>
Operador de comparação não é igual a. Mesmo que o operador! =* ``12!=24 -> true``
* ``'abc'!='abc' -> false``
___
### <code>null</code>
<code><b>null() => null</b></code><br/><br/>
Retorna um valor nulo. Use a sintaxe da função (NULL ()) se houver uma coluna chamada ' NULL '. Qualquer operação que usa resultará em um valor nulo* ``custId = NULL (for derived field)``
* ``custId == NULL -> NULL``
* ``'nothing' + NULL -> NULL``
* ``10 * NULL -> NULL'``
* ``NULL == '' -> NULL'``
___
### <code>or</code>
<code><b>or(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : boolean) => boolean</b></code><br/><br/>
Operador OR lógico. Mesmo que | |* ``or(true, false) -> true``
* ``true || false -> true``
___
### <code>pMod</code>
<code><b>pMod(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
Módulo positivo do par de números.
* ``pmod(-20, 8) -> 4``
___
### <code>power</code>
<code><b>power(<i>&lt;value1&gt;</i> : number, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Eleva um número à potência de outro* ``power(10, 2) -> 100``
___
### <code>rank</code>
<code><b>rank(<i>&lt;value1&gt;</i> : any, ...) => integer</b></code><br/><br/>
Computa a classificação de um valor em um grupo de valores. O resultado é um mais o número de linhas precedentes ou iguais à linha atual na ordenação da partição. Os valores produzirão lacunas na sequência. A classificação funciona mesmo quando os dados não são classificados e procura alterações nos valores* ``rank(salesQtr, salesAmt) -> 1``
___
### <code>regexExtract</code>
<code><b>regexExtract(<i>&lt;string&gt;</i> : string, <i>&lt;regex to find&gt;</i> : string, [<i>&lt;match group 1-based index&gt;</i> : integral]) => string</b></code><br/><br/>
Extraia uma subcadeia de caracteres correspondente para um determinado padrão de Regex. O último parâmetro identifica o grupo de correspondência e é padronizado como 1 se omitido. Use '<regex>' (aspas de fundo) para corresponder a uma cadeia de caracteres sem saída* ``regexExtract('Cost is between 600 and 800 dollars', '(\\d+) and (\\d+)', 2) -> '800'``
* ``regexExtract('Cost is between 600 and 800 dollars', `(\d+) and (\d+)`, 2) -> '800'``
___
### <code>regexMatch</code>
<code><b>regexMatch(<i>&lt;string&gt;</i> : string, <i>&lt;regex to match&gt;</i> : string) => boolean</b></code><br/><br/>
Verifica se a cadeia de caracteres corresponde ao padrão Regex fornecido. Use '<regex>' (aspas de fundo) para corresponder a uma cadeia de caracteres sem saída* ``regexMatch('200.50', '(\\d+).(\\d+)') -> true``
* ``regexMatch('200.50', `(\d+).(\d+)`) -> true``
___
### <code>regexReplace</code>
<code><b>regexReplace(<i>&lt;string&gt;</i> : string, <i>&lt;regex to find&gt;</i> : string, <i>&lt;substring to replace&gt;</i> : string) => string</b></code><br/><br/>
Substituir todas as ocorrências de um padrão de Regex por outra subcadeia de caracteres na cadeia<regex>determinada use ' ' (aspas de fundo) para corresponder a uma cadeia de caracteres sem saída* ``regexReplace('100 and 200', '(\\d+)', 'bojjus') -> 'bojjus and bojjus'``
* ``regexReplace('100 and 200', `(\d+)`, 'gunchus') -> 'gunchus and gunchus'``
___
### <code>regexSplit</code>
<code><b>regexSplit(<i>&lt;string to split&gt;</i> : string, <i>&lt;regex expression&gt;</i> : string) => array</b></code><br/><br/>
Divide uma cadeia de caracteres com base em um delimitador com base em Regex e retorna uma matriz de cadeias de caracteres* ``regexSplit('oneAtwoBthreeC', '[CAB]') -> ['one', 'two', 'three']``
* ``regexSplit('oneAtwoBthreeC', '[CAB]')[1] -> 'one'``
* ``regexSplit('oneAtwoBthreeC', '[CAB]')[0] -> NULL``
* ``regexSplit('oneAtwoBthreeC', '[CAB]')[20] -> NULL``
___
### <code>replace</code>
<code><b>replace(<i>&lt;string&gt;</i> : string, <i>&lt;substring to find&gt;</i> : string, <i>&lt;substring to replace&gt;</i> : string) => string</b></code><br/><br/>
Substituir todas as ocorrências de uma subcadeia de caracteres por outra subcadeia de caracteres na cadeia de caracteres especificada* ``replace('doggie dog', 'dog', 'cat') -> 'catgie cat'``
* ``replace('doggie dog', 'dog', '') -> 'gie'``
___
### <code>reverse</code>
<code><b>reverse(<i>&lt;value1&gt;</i> : string) => string</b></code><br/><br/>
Inverte uma cadeia de caracteres* ``reverse('gunchus') -> 'suhcnug'``
___
### <code>right</code>
<code><b>right(<i>&lt;string to subset&gt;</i> : string, <i>&lt;number of characters&gt;</i> : integral) => string</b></code><br/><br/>
Extrai uma Subcadeia com o número de caracteres à direita. Igual a subcadeia de caracteres (Str, LENGTH (Str)-n, n)* ``right('bojjus', 2) -> 'us'``
* ``right('bojjus', 20) -> 'bojjus'``
___
### <code>rlike</code>
<code><b>rlike(<i>&lt;string&gt;</i> : string, <i>&lt;pattern match&gt;</i> : string) => boolean</b></code><br/><br/>
Verifica se a cadeia de caracteres corresponde ao padrão Regex fornecido* ``rlike('200.50', '(\d+).(\d+)') -> true``
___
### <code>round</code>
<code><b>round(<i>&lt;number&gt;</i> : number, [<i>&lt;scale to round&gt;</i> : number], [<i>&lt;rounding option&gt;</i> : integral]) => double</b></code><br/><br/>
Arredonda um número de acordo com uma escala opcional e um modo de arredondamento opcional. Se a escala for omitida, o padrão será 0.  Se o modo for omitido, ele será padronizado como ROUND_HALF_UP (5). Os valores para arredondamento incluem 1-ROUND_UP 2-ROUND_DOWN 3-ROUND_CEILING 4-ROUND_FLOOR 5-ROUND_HALF_UP 6-ROUND_HALF_DOWN 7-ROUND_HALF_EVEN 8-ROUND_UNNECESSARY* ``round(100.123) -> 100.0``
* ``round(2.5, 0) -> 3.0``
* ``round(5.3999999999999995, 2, 7) -> 5.40``
___
### <code>rowNumber</code>
<code><b>rowNumber() => integer</b></code><br/><br/>
Atribui uma numeração de linha sequencial para linhas em uma janela que começa com 1* ``rowNumber() -> 1``
___
### <code>rpad</code>
<code><b>rpad(<i>&lt;string to pad&gt;</i> : string, <i>&lt;final padded length&gt;</i> : integral, <i>&lt;padding&gt;</i> : string) => string</b></code><br/><br/>
A direita pads a cadeia de caracteres pelo preenchimento fornecido até que seja de um determinado comprimento. Se a cadeia de caracteres for igual ou maior que o comprimento, ela será considerada um RTRIM não op * ``rpad('great', 10, '-') -> 'great___--'`` 
* ``rpad('great', 4, '-') -> 'great'`` 
* ``rpad('great', 8, '<>') -> 'great<><'`` 
___
### <code>rtrim</code></code>
<code><b>rtrim(<i>&lt;string to trim&gt;</i> : string, <i>&lt;trim characters&gt;</i> : string) => string</b></code><br/><br/>
A direita corta uma cadeia de caracteres à esquerda. Se o segundo parâmetro não for especificado, ele cortará o espaço em branco. Caso contrário, ele apara qualquer caractere especificado no segundo parâmetro* ``rtrim('!--!wor!ld!', '-!') -> '!--!wor!ld'``
___
### <code>second</code>
<code><b>second(<i>&lt;value1&gt;</i> : timestamp, [<i>&lt;value2&gt;</i> : string]) => integer</b></code><br/><br/>
Obtém o segundo valor de uma data. Você pode passar um fuso horário opcional na forma de ' GMT ', ' PST ', ' UTC ', ' América/Cayman '. O fuso horário local é usado como o padrão.
* ``second(toTimestamp('2009-07-30T12:58:59')) -> 59``
___
### <code>sha1</code>
<code><b>sha1(<i>&lt;value1&gt;</i> : any, ...) => string</b></code><br/><br/>
Calcula o resumo SHA-1 do conjunto de colunas de tipos de dataprimitivos variantes e retorna uma cadeia de caracteres hexadecimal de 40 caracteres. Ele pode ser usado para calcular uma impressão digital de uma linha* ``sha1(5, 'gunchus', 8.2, 'bojjus', true, toDate('2010-4-4')) -> '63849fd2abb65fbc626c60b1f827bd05573f0cea'``
___
### <code>sha2</code>
<code><b>sha2(<i>&lt;value1&gt;</i> : integer, <i>&lt;value2&gt;</i> : any, ...) => string</b></code><br/><br/>
Calcula o resumo SHA-2 do conjunto de colunas de tipos de dados primitivos variados de acordo com um comprimento de bit que só pode ser de valores 0 (256), 224, 256, 384, 512. Ele pode ser usado para calcular uma impressão digital de uma linha* ``sha2(256, 'gunchus', 8.2, 'bojjus', true, toDate('2010-4-4')) -> 'd3b2bff62c3a00e9370b1ac85e428e661a7df73959fa1a96ae136599e9ee20fd'``
___
### <code>sin</code>
<code><b>sin(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Calcula um valor do seno* ``sin(2) -> 0.90929742682``
___
### <code>sinh</code>
<code><b>sinh(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Calcula um valor seno hiperbólico* ``sinh(0) -> 0.0``
___
### <code>skewness</code>
<code><b>skewness(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Obtém a distorção de uma coluna* ``skewness(sales) -> 122.12``
___
### <code>skewnessIf</code>
<code><b>skewnessIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Com base em critérios, obtém a distorção de uma coluna* ``skewnessIf(region == 'West', sales) -> 122.12``
___
### <code>slice</code>
<code><b>slice(<i>&lt;array to slice&gt;</i> : array, <i>&lt;from 1-based index&gt;</i> : integral, [<i>&lt;number of items&gt;</i> : integral]) => array</b></code><br/><br/>
Extrai um subconjunto de uma matriz de uma posição. A posição é baseada em 1. Se o comprimento for omitido, o padrão será o fim da cadeia de caracteres* ``slice([10, 20, 30, 40], 1, 2) -> [10, 20]``
* ``slice([10, 20, 30, 40], 2) -> [20, 30, 40]``
* ``slice([10, 20, 30, 40], 2)[1] -> 20``
* ``slice([10, 20, 30, 40], 2)[0] -> NULL``
* ``slice([10, 20, 30, 40], 2)[20] -> NULL``
* ``slice([10, 20, 30, 40], 8) -> []``
___
### <code>soundex</code>
<code><b>soundex(<i>&lt;value1&gt;</i> : string) => string</b></code><br/><br/>
Obtém o código SOUNDEX para a cadeia de caracteres* ``soundex('genius') -> 'G520'``
___
### <code>split</code>
<code><b>split(<i>&lt;string to split&gt;</i> : string, <i>&lt;split characters&gt;</i> : string) => array</b></code><br/><br/>
Divide uma cadeia de caracteres com base em um delimitador e retorna uma matriz de cadeias de caracteres* ``split('100,200,300', ',') -> ['100', '200', '300']``
* ``split('100,200,300', '|') -> ['100,200,300']``
* ``split('100, 200, 300', ', ') -> ['100', '200', '300']``
* ``split('100, 200, 300', ', ')[1] -> '100'``
* ``split('100, 200, 300', ', ')[0] -> NULL``
* ``split('100, 200, 300', ', ')[20] -> NULL``
* ``split('100200300', ',') -> ['100200300']``
___
### <code>sqrt</code>
<code><b>sqrt(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Calcula a raiz quadrada de um número* ``sqrt(9) -> 3``
___
### <code>startsWith</code>
<code><b>startsWith(<i>&lt;string&gt;</i> : string, <i>&lt;substring to check&gt;</i> : string) => boolean</b></code><br/><br/>
Verifica se a cadeia de caracteres começa com a cadeia de caracteres fornecida* ``startsWith('great', 'gr') -> true``
___
### <code>stddev</code>
<code><b>stddev(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Obtém o desvio padrão de uma coluna* ``stdDev(sales) -> 122.12``
___
### <code>stddevIf</code>
<code><b>stddevIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Com base em critérios, obtém o desvio padrão de uma coluna* ``stddevIf(region == 'West', sales) -> 122.12``
___
### <code>stddevPopulation</code>
<code><b>stddevPopulation(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Obtém o desvio padrão da população de uma coluna* ``stddevPopulation(sales) -> 122.12``
___
### <code>stddevPopulationIf</code>
<code><b>stddevPopulationIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Com base em critérios, obtém o desvio padrão da população de uma coluna* ``stddevPopulationIf(region == 'West', sales) -> 122.12``
___
### <code>stddevSample</code>
<code><b>stddevSample(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Obtém o desvio padrão da amostra de uma coluna* ``stddevSample(sales) -> 122.12``
___
### <code>stddevSampleIf</code>
<code><b>stddevSampleIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Com base em critérios, obtém o desvio padrão da amostra de uma coluna* ``stddevSampleIf(region == 'West', sales) -> 122.12``
___
### <code>subDays</code>
<code><b>subDays(<i>&lt;date/timestamp&gt;</i> : datetime, <i>&lt;days to subtract&gt;</i> : integral) => datetime</b></code><br/><br/>
Subtraia meses de uma data. O mesmo que o-Operator para data* ``subDays(toDate('2016-08-08'), 1) -> 2016-08-09``
___
### <code>subMonths</code>
<code><b>subMonths(<i>&lt;date/timestamp&gt;</i> : datetime, <i>&lt;months to subtract&gt;</i> : integral) => datetime</b></code><br/><br/>
Subtrair meses de uma data ou timestamp* ``subMonths(toDate('2016-09-30'), 1) -> 2016-08-31``
___
### <code>substring</code>
<code><b>substring(<i>&lt;string to subset&gt;</i> : string, <i>&lt;from 1-based index&gt;</i> : integral, [<i>&lt;number of characters&gt;</i> : integral]) => string</b></code><br/><br/>
Extrai uma subcadeia de caracteres de um determinado comprimento de uma posição. A posição é baseada em 1. Se o comprimento for omitido, o padrão será o fim da cadeia de caracteres* ``substring('Cat in the hat', 5, 2) -> 'in'``
* ``substring('Cat in the hat', 5, 100) -> 'in the hat'``
* ``substring('Cat in the hat', 5) -> 'in the hat'``
* ``substring('Cat in the hat', 100, 100) -> ''``
___
### <code>sum</code>
<code><b>sum(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
Obtém a soma agregada de uma coluna numérica* ``sum(col) -> value``
___
### <code>sumDistinct</code>
<code><b>sumDistinct(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
Obtém a soma agregada de valores distintos de uma coluna numérica* ``sumDistinct(col) -> value``
___
### <code>sumDistinctIf</code>
<code><b>sumDistinctIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => number</b></code><br/><br/>
Com base em critérios, obtém a soma agregada de uma coluna numérica. A condição pode ser baseada em qualquer coluna* ``sumDistinctIf(state == 'CA' && commission < 10000, sales) -> value``
* ``sumDistinctIf(true, sales) -> SUM(sales)``
___
### <code>sumIf</code>
<code><b>sumIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => number</b></code><br/><br/>
Com base em critérios, obtém a soma agregada de uma coluna numérica. A condição pode ser baseada em qualquer coluna* ``sumIf(state == 'CA' && commission < 10000, sales) -> value``
* ``sumIf(true, sales) -> SUM(sales)``
___
### <code>tan</code>
<code><b>tan(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Calcula um valor tangente* ``tan(0) -> 0.0``
___
### <code>tanh</code>
<code><b>tanh(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Calcula um valor tangente hiperbólico* ``tanh(0) -> 0.0``
___
### <code>toBoolean</code>
<code><b>toBoolean(<i>&lt;value1&gt;</i> : string) => boolean</b></code><br/><br/>
Converte um valor de (' T', ' true ', ' y ', ' Yes ', ' 1 ') para true e (' f ', ' false ', ' n', ' no ', ' 0 ') como false e NULL para qualquer outro valor* ``toBoolean('true') -> true``
* ``toBoolean('n') -> false``
* ``toBoolean('truthy') -> NULL``
___
### <code>toDate</code>
<code><b>toDate(<i>&lt;string&gt;</i> : any, [<i>&lt;date format&gt;</i> : string]) => date</b></code><br/><br/>
Converte uma cadeia de caracteres em uma data de acordo com um formato de data opcional. Consulte Java SimpleDateFormat para todos os formatos possíveis. Se o formato de data for omitido, as combinações dos itens a seguir serão aceitas. [yyyy, aaaa-[M] M, aaaa-[M] M-[d] d, YYYY-[M] M-[d] d, YYYY-[M] M-[d] d, YYYY-[M] M-[d] dT *]* ``toDate('2012-8-8') -> 2012-8-8``
* ``toDate('12/12/2012', 'MM/dd/yyyy') -> 2012-12-12``
___
### <code>toDecimal</code>
<code><b>toDecimal(<i>&lt;value&gt;</i> : any, [<i>&lt;precision&gt;</i> : integral], [<i>&lt;scale&gt;</i> : integral], [<i>&lt;format&gt;</i> : string], [<i>&lt;locale&gt;</i> : string]) => decimal(10,0)</b></code><br/><br/>
Converte qualquer número ou cadeia de caracteres em um valor decimal. Se a precisão e a escala não forem especificadas, o padrão será (10, 2). Um formato decimal do Java opcional pode ser usado para a conversão. Um formato de localidade opcional na forma de linguagem BCP47, como en-US, de, zh-CN* ``toDecimal(123.45) -> 123.45``
* ``toDecimal('123.45', 8, 4) -> 123.4500``
* ``toDecimal('$123.45', 8, 4,'$###.00') -> 123.4500``
* ``toDecimal('Ç123,45', 10, 2, 'Ç###,##', 'de') -> 123.45``
___
### <code>toDouble</code>
<code><b>toDouble(<i>&lt;value&gt;</i> : any, [<i>&lt;format&gt;</i> : string], [<i>&lt;locale&gt;</i> : string]) => double</b></code><br/><br/>
Converte qualquer número ou cadeia de caracteres em um valor duplo. Um formato decimal do Java opcional pode ser usado para a conversão. Um formato de localidade opcional na forma de linguagem BCP47, como en-US, de, zh-CN* ``toDouble(123.45) -> 123.45``
* ``toDouble('123.45') -> 123.45``
* ``toDouble('$123.45', '$###.00') -> 123.45``
* ``toDouble('Ç123,45', 'Ç###,##', 'de') -> 123.45``
___
### <code>toFloat</code>
<code><b>toFloat(<i>&lt;value&gt;</i> : any, [<i>&lt;format&gt;</i> : string], [<i>&lt;locale&gt;</i> : string]) => float</b></code><br/><br/>
Converte qualquer número ou cadeia de caracteres em um valor float. Um formato decimal do Java opcional pode ser usado para a conversão. Trunca qualquer duplo* ``toFloat(123.45) -> 123.45``
* ``toFloat('123.45') -> 123.45``
* ``toFloat('$123.45', '$###.00') -> 123.45``
___
### <code>toInteger</code>
<code><b>toInteger(<i>&lt;value&gt;</i> : any, [<i>&lt;format&gt;</i> : string], [<i>&lt;locale&gt;</i> : string]) => integer</b></code><br/><br/>
Converte qualquer número ou cadeia de caracteres em um valor inteiro. Um formato decimal do Java opcional pode ser usado para a conversão. Trunca qualquer longo, float, Double* ``toInteger(123) -> 123``
* ``toInteger('123') -> 123``
* ``toInteger('$123', '$###') -> 123``
___
### <code>toLong</code>
<code><b>toLong(<i>&lt;value&gt;</i> : any, [<i>&lt;format&gt;</i> : string], [<i>&lt;locale&gt;</i> : string]) => long</b></code><br/><br/>
Converte qualquer número ou cadeia de caracteres em um valor longo. Um formato decimal do Java opcional pode ser usado para a conversão. Trunca qualquer float, Double* ``toLong(123) -> 123``
* ``toLong('123') -> 123``
* ``toLong('$123', '$###') -> 123``
___
### <code>toShort</code>
<code><b>toShort(<i>&lt;value&gt;</i> : any, [<i>&lt;format&gt;</i> : string], [<i>&lt;locale&gt;</i> : string]) => short</b></code><br/><br/>
Converte qualquer número ou cadeia de caracteres em um valor curto. Um formato decimal do Java opcional pode ser usado para a conversão. Trunca qualquer inteiro, longo, flutuante, duplo* ``toShort(123) -> 123``
* ``toShort('123') -> 123``
* ``toShort('$123', '$###') -> 123``
___
### <code>toString</code>
<code><b>toString(<i>&lt;value&gt;</i> : any, [<i>&lt;number format/date format&gt;</i> : string]) => string</b></code><br/><br/>
Converte um tipo de dados primitivo em uma cadeia de caracteres. Para números e datas, um formato pode ser especificado. Se não for especificado, o padrão do sistema será escolhido. O formato decimal do Java é usado para números. Consulte Java SimpleDateFormat para todos os formatos de data possíveis; o formato padrão é AAAA-MM-DD* ``toString(10) -> '10'``
* ``toString('engineer') -> 'engineer'``
* ``toString(123456.789, '##,###.##') -> '123,456.79'``
* ``toString(123.78, '000000.000') -> '000123.780'``
* ``toString(12345, '##0.#####E0') -> '12.345E3'``
* ``toString(toDate('2018-12-31')) -> '2018-12-31'``
* ``toString(toDate('2018-12-31'), 'MM/dd/yy') -> '12/31/18'``
* ``toString(4 == 20) -> 'false'``
___
### <code>toTimestamp</code>
<code><b>toTimestamp(<i>&lt;string&gt;</i> : any, [<i>&lt;timestamp format&gt;</i> : string], [<i>&lt;time zone&gt;</i> : string]) => timestamp</b></code><br/><br/>
Converte uma cadeia de caracteres em uma data dado um formato de carimbo de data/hora opcional. Consulte Java SimpleDateFormat para todos os formatos possíveis. Se o carimbo de data/hora for omitido, o padrão aaaa-[M] M-[d] d hh: mm: SS [. f...] será usado* ``toTimestamp('2016-12-31 00:12:00') -> 2012-8-8T00:12:00``
* ``toTimestamp('2016/12/31T00:12:00', 'MM/dd/yyyyThh:mm:ss') -> 2012-12-12T00:12:00``
___
### <code>toUTC</code>
<code><b>toUTC(<i>&lt;value1&gt;</i> : timestamp, [<i>&lt;value2&gt;</i> : string]) => timestamp</b></code><br/><br/>
Converte o carimbo de data/hora em UTC. Você pode passar um fuso horário opcional na forma de ' GMT ', ' PST ', ' UTC ', ' América/Cayman '. Ele é padronizado para o fuso horário atual* ``toUTC(currentTimeStamp()) -> 12-12-2030T19:18:12``
* ``toUTC(currentTimeStamp(), 'Asia/Seoul') -> 12-13-2030T11:18:12``
___
### <code>translate</code>
<code><b>translate(<i>&lt;string to translate&gt;</i> : string, <i>&lt;lookup characters&gt;</i> : string, <i>&lt;replace characters&gt;</i> : string) => string</b></code><br/><br/>
Substituir um conjunto de caracteres por outro conjunto de caracteres na cadeia de caracteres. Os caracteres têm de 1 a 1 substituição* ``translate('(Hello)', '()', '[]') -> '[Hello]'``
* ``translate('(Hello)', '()', '[') -> '[Hello'``
___
### <code>trim</code>
<code><b>trim(<i>&lt;string to trim&gt;</i> : string, [<i>&lt;trim characters&gt;</i> : string]) => string</b></code><br/><br/>
Corta uma cadeia de caracteres de caractere à esquerda e à direita. Se o segundo parâmetro não for especificado, ele cortará o espaço em branco. Caso contrário, ele apara qualquer caractere especificado no segundo parâmetro* ``trim('!--!wor!ld!', '-!') -> 'wor!ld'``
___
### <code>true</code>
<code><b>true() => boolean</b></code><br/><br/>
Sempre retorna um valor verdadeiro. Use a sintaxe da função (true ()) se houver uma coluna chamada ' true '* ``isDiscounted == true()``
* ``isDiscounted() == true``
___
### <code>typeMatch</code>
<code><b>typeMatch(<i>&lt;type&gt;</i> : string, <i>&lt;base type&gt;</i> : string) => boolean</b></code><br/><br/>
Corresponde ao tipo da coluna. Só pode ser usado em expressões de padrão. número correspondente curto, inteiro, longo, duplo, float ou Decimal, integral corresponde a curto, inteiro, longo, fração de correspondências Double, float, decimal e DateTime corresponde a data ou ao tipo TIMESTAMP* ``typeMatch(type, 'number') -> true``
* ``typeMatch('date', 'number') -> false``
___
### <code>upper</code>
<code><b>upper(<i>&lt;value1&gt;</i> : string) => string</b></code><br/><br/>
Maiúsculas uma cadeia de caracteres* ``upper('bojjus') -> 'BOJJUS'``
___
### <code>variance</code>
<code><b>variance(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Obtém a variância de uma coluna* ``variance(sales) -> 122.12``
___
### <code>varianceIf</code>
<code><b>varianceIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Com base em um critério, obtém a variância de uma coluna* ``varianceIf(region == 'West', sales) -> 122.12``
___
### <code>variancePopulation</code>
<code><b>variancePopulation(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Obtém a variação da população de uma coluna* ``variancePopulation(sales) -> 122.12``
___
### <code>variancePopulationIf</code>
<code><b>variancePopulationIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Com base em critérios, obtém a variação da população de uma coluna* ``variancePopulationIf(region == 'West', sales) -> 122.12``
___
### <code>varianceSample</code>
<code><b>varianceSample(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Obtém a variância não polarizada de uma coluna* ``varianceSample(sales) -> 122.12``
___
### <code>varianceSampleIf</code>
<code><b>varianceSampleIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Com base em critérios, obtém a variação não polarizada de uma coluna* ``varianceSampleIf(region == 'West', sales) -> 122.12``
___
### <code>weekOfYear</code>
<code><b>weekOfYear(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
Obtém a semana do ano de acordo com uma data* ``weekOfYear(toDate('2008-02-20')) -> 8``
___
### <code>xor</code>
<code><b>xor(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : boolean) => boolean</b></code><br/><br/>
Operador XOR lógico. Mesmo que o operador ^* ``xor(true, false) -> true``
* ``xor(true, true) -> false``
* ``true ^ false -> true``
___
### <code>year</code>
<code><b>year(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
Obtém o valor de ano de uma data* ``year(toDate('2012-8-8')) -> 2012``

## <a name="next-steps"></a>Passos seguintes

[Saiba como usar o Expression Builder](concepts-data-flow-expression-builder.md).
