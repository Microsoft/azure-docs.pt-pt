---
title: Funções de expressão no recurso de fluxo de dados de mapeamento do Azure Data Factory
description: Saiba mais sobre as funções de expressão no fluxo de dados de mapeamento.
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/15/2019
ms.openlocfilehash: 27d968aa5202fbeb38be9a2416514d2185c1d8b9
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/16/2019
ms.locfileid: "72436735"
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
Calcula um valor inverso do cosseno * ``acos(1) -> 0.0`` @ no__t-1 @ no__t-2<br/><br/>
Adiciona um par de cadeias de caracteres ou números. Adiciona uma data a um número de dias. Adiciona uma duração a um carimbo de data/hora. Acrescenta uma matriz de tipo semelhante para outra. O mesmo que o operador + * ``add(10, 20) -> 30`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6 @ no__t-7 @ no__t-8 @ no__t-9<br/><br/>
Adicione dias a uma data ou carimbo de hora. O mesmo que o operador + para a data * ``addDays(toDate('2016-08-08'), 1) -> toDate('2016-08-09')`` @ no__t-1 @ no__t-2<br/><br/>
Adicione meses a uma data ou timestamp. Opcionalmente, você pode passar um fuso horário * ``addMonths(toDate('2016-08-31'), 1) -> toDate('2016-09-30')`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Operador AND lógico. O mesmo que & & * ``and(true, false) -> false`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Calcula um valor de seno inverso * ``asin(0) -> 0.0`` @ no__t-1 @ no__t-2<br/><br/>
Calcula um valor tangente inverso * ``atan(0) -> 0.0`` @ no__t-1 @ no__t-2<br/><br/>
Retorna o ângulo em radianos entre o eixo x positivo de um plano e o ponto fornecido pelas coordenadas * ``atan2(0, 0) -> 0.0`` @ no__t-1 @ no__t-2<br/><br/>
Obtém a média de valores de uma coluna * ``avg(sales)`` @ no__t-1 @ no__t-2<br/><br/>
Com base em um critério Obtém a média de valores de uma coluna * ``avgIf(region == 'West', sales)`` @ no__t-1 @ no__t-2<br/><br/>
Seleciona um valor de coluna por nome no fluxo. Você pode passar um nome de fluxo opcional como o segundo argumento. Se houver várias correspondências, a primeira correspondência será retornada. Se nenhuma correspondência retornar um valor nulo. O valor retornado deve ser do tipo convertido por uma das funções de conversão de tipo (TO_DATE, TO_STRING...).  Nomes de coluna conhecidos em tempo de design devem ser abordados apenas por seu nome. Não há suporte para entradas computadas, mas você pode usar substituições de parâmetro * ``toString(byName('parent'))`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6 @ no__t-7<br/><br/>
Seleciona um valor de coluna por sua posição relativa (1 com base) no fluxo. Se a posição estiver fora dos limites, ela retornará um valor nulo. O valor retornado deve ser do tipo convertido por uma das funções de conversão de tipo (TO_DATE, TO_STRING...) Não há suporte para entradas computadas, mas você pode usar substituições de parâmetro * ``toString(byPosition(1))`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6<br/><br/>
Com base em condições alternativas, aplica-se um valor ou outro. Se o número de entradas for par, o outro será padronizado como nulo para a última condição * ``case(10 + 20 == 30, 'dumbo', 'gumbo') -> 'dumbo'`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5<br/><br/>
Calcule a raiz do cubo de um número * ``cbrt(8) -> 2.0`` @ no__t-1 @ no__t-2<br/><br/>
Retorna o menor inteiro que não é menor que o número * ``ceil(-0.1) -> 0`` @ no__t-1 @ no__t-2<br/><br/>
Retorna o primeiro valor não nulo de um conjunto de entradas. Todas as entradas devem ser do mesmo tipo * ``coalesce(10, 20) -> 10`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Compara dois valores do mesmo tipo. Retorna um inteiro negativo se value1 < value2, 0 se value1 = = value2, valor positivo se value1 > value2 * ``(compare(12, 24) < 1) -> true`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Concatena um número variável de cadeias de caracteres em conjunto. O mesmo que o operador + com cadeias de caracteres * ``concat('dataflow', 'is', 'awesome') -> 'dataflowisawesome'`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4<br/><br/>
Concatena um número variável de cadeias de caracteres junto com um separador. O primeiro parâmetro é o separador * ``concatWS(' ', 'dataflow', 'is', 'awesome') -> 'dataflow is awesome'`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4<br/><br/>
Retornará true se qualquer elemento na matriz fornecida for avaliado como true no predicado fornecido. Contains espera uma referência a um elemento na função de predicado como #item * ``contains([1, 2, 3, 4], #item == 3) -> true`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Calcula um valor do cosseno * ``cos(10) -> -0.8390715290764524`` @ no__t-1 @ no__t-2<br/><br/>
Calcula um cosseno hiperbólico de um valor * ``cosh(0) -> 1.0`` @ no__t-1 @ no__t-2<br/><br/>
Obtém a contagem de valores agregados. Se a coluna (s) opcional for especificada, ela ignorará valores nulos na contagem * ``count(custId)`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5<br/><br/>
Obtém a contagem agregada de valores distintos de um conjunto de colunas * ``countDistinct(custId, custName)`` @ no__t-1 @ no__t-2<br/><br/>
Com base em um critério, obtém a contagem agregada de valores. Se a coluna opcional for especificada, ela ignorará valores nulos na contagem * ``countIf(state == 'CA' && commission < 10000, name)`` @ no__t-1 @ no__t-2<br/><br/>
Obtém a covariância de população entre duas colunas * ``covariancePopulation(sales, profit)`` @ no__t-1 @ no__t-2<br/><br/>
Com base em critérios, obtém a covariância de população de duas colunas * ``covariancePopulationIf(region == 'West', sales)`` @ no__t-1 @ no__t-2<br/><br/>
Obtém a covariância de exemplo de duas colunas * ``covarianceSample(sales, profit)`` @ no__t-1 @ no__t-2<br/><br/>
Com base em critérios, obtém a covariância de exemplo de duas colunas * ``covarianceSampleIf(region == 'West', sales, profit)`` @ no__t-1 @ no__t-2<br/><br/>
Calcula o hash CRC32 do conjunto de colunas de tipos de dados primitivos variados de acordo com um comprimento de bit que só pode ser de valores 0 (256), 224, 256, 384, 512. Ele pode ser usado para calcular uma impressão digital para uma linha * ``crc32(256, 'gunchus', 8.2, 'bojjus', true, toDate('2010-4-4')) -> 3630253689L`` @ no__t-1 @ no__t-2<br/><br/>
A função CumeDist computa a posição de um valor em relação a todos os valores na partição. O resultado é o número de linhas precedentes ou iguais à linha atual na ordenação da partição dividida pelo número total de linhas na partição da janela. Os valores de ligação na ordenação serão avaliados como a mesma posição.
* ``cumeDist()``
___
### <code>currentDate</code>
<code><b>currentDate([<i>&lt;value1&gt;</i> : string]) => date</b></code><br/><br/>
Obtém a data atual em que esse trabalho começa a ser executado. Você pode passar um fuso horário opcional na forma de ' GMT ', ' PST ', ' UTC ', ' América/Cayman '. O fuso horário local é usado como o padrão. Consulte SimpleDateFormat do Java para obter os formatos disponíveis. https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html * ``currentDate() == toDate('2250-12-31') -> false`` @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5<br/><br/>
Obtém o carimbo de data/hora atual quando o trabalho começa a ser executado com o fuso horário local * ``currentTimestamp() == toTimestamp('2250-12-31 12:12:12') -> false`` @ no__t-1 @ no__t-2<br/><br/>
Obtém o carimbo de data/hora atual como UTC. Se desejar que a hora atual seja interpretada em um fuso horário diferente do fuso horário do cluster, você poderá passar um fuso horário opcional na forma de ' GMT ', ' PST ', ' UTC ', ' América/Cayman '. Ele é padronizado para o fuso horário atual. Consulte SimpleDateFormat do Java para obter os formatos disponíveis. Use [SimpleDateFormat](https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html) para converter a hora UTC em um fuso horário diferente, use fromUTC ().
* ``currentUTC() == toTimestamp('2050-12-12 19:18:12') -> false``
* ``currentUTC() != toTimestamp('2050-12-12 19:18:12') -> true``
* ``fromUTC(currentUTC(), 'Asia/Seoul') != toTimestamp('2050-12-12 19:18:12') -> true``
___
### <code>dayOfMonth</code>
<code><b>dayOfMonth(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
Obtém o dia do mês de acordo com a data * ``dayOfMonth(toDate('2018-06-08')) -> 8`` @ no__t-1 @ no__t-2<br/><br/>
Obtém o dia da semana de acordo com uma data. 1-domingo, 2-segunda-feira..., 7-sábado * ``dayOfWeek(toDate('2018-06-08')) -> 6`` @ no__t-1 @ no__t-2<br/><br/>
Obtém o dia do ano de acordo com a data * ``dayOfYear(toDate('2016-04-09')) -> 100`` @ no__t-1 @ no__t-2<br/><br/>
Duração em milissegundos para o número de dias * ``days(2) -> 172800000L`` @ no__t-1 @ no__t-2<br/><br/>
Converte radianos em graus * ``degrees(3.141592653589793) -> 180`` @ no__t-1 @ no__t-2<br/><br/>
Computa a classificação de um valor em um grupo de valores. O resultado é um mais o número de linhas precedentes ou iguais à linha atual na ordenação da partição. Os valores não produzirão lacunas na sequência. A classificação densa funciona mesmo quando os dados não são classificados e procura alterações nos valores * ``denseRank(salesQtr, salesAmt)`` @ no__t-1 @ no__t-2<br/><br/>
Divide o par de números. O mesmo que o operador/* ``divide(20, 10) -> 2`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Verifica se a cadeia de caracteres termina com a cadeia de caracteres fornecida * ``endsWith('dumbo', 'mbo') -> true`` @ no__t-1 @ no__t-2<br/><br/>
Operador de comparação de Equals. O mesmo que = = operator * ``equals(12, 24) -> false`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6<br/><br/>
Operador EQUAL de comparação ignorando maiúsculas e minúsculas. O mesmo que < = operador > * ``'abc'<=>'Abc' -> true`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Calcular o fatorial de um número * ``factorial(5) -> 120`` @ no__t-1 @ no__t-2<br/><br/>
Sempre retorna um valor falso. Use a sintaxe da função (false ()) se houver uma coluna denominada ' false ' * ``(10 + 20 > 30) -> false`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Filtra elementos da matriz que não atendem ao predicado fornecido. O filtro espera uma referência a um elemento na função de predicado como #item * ``filter([1, 2, 3, 4], #item > 2) -> [3, 4]`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Obtém o primeiro valor de um grupo de colunas. Se o segundo parâmetro ignoreNulls for omitido, presume-se false * ``first(sales)`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Retorna o maior inteiro que não é maior que o número * ``floor(-0.1) -> -1`` @ no__t-1 @ no__t-2<br/><br/>
Converte para o carimbo de data/hora do UTC. Opcionalmente, você pode passar o fuso horário na forma de ' GMT ', ' PST ', ' UTC ', ' América/Cayman '. Ele é padronizado para os SimpleDateFormat atuais do timezoneRefer Java para os formatos disponíveis. https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html * ``fromUTC(currentTimeStamp()) == toTimestamp('2050-12-12 19:18:12') -> false`` @ no__t-2 @ no__t-3 @ no__t-4<br/><br/>
Operador maior de comparação. O mesmo que > Operator * ``greater(12, 24) -> false`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4<br/><br/>
Comparação de operador maior ou igual a. O mesmo que > = Operator * ``greaterOrEqual(12, 12) -> true`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Retorna o valor máximo entre a lista de valores como entrada ignorando valores nulos. Retornará NULL se todas as entradas forem nulas * ``greatest(10, 30, 15, 20) -> 30`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5<br/><br/>
Verifica o valor de uma coluna por nome no fluxo. Você pode passar um nome de fluxo opcional como o segundo argumento.  Nomes de coluna conhecidos em tempo de design devem ser abordados apenas por seu nome. Não há suporte para entradas computadas, mas você pode usar substituições de parâmetro * ``hasColumn('parent')`` @ no__t-1 @ no__t-2<br/><br/>
Obtém o valor de hora de um timestamp. Você pode passar um fuso horário opcional na forma de ' GMT ', ' PST ', ' UTC ', ' América/Cayman '. O fuso horário local é usado como o padrão. Consulte SimpleDateFormat do Java para obter os formatos disponíveis. https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html * ``hour(toTimestamp('2009-07-30 12:58:59')) -> 12`` @ no__t-2 @ no__t-3 @ no__t-4<br/><br/>
Duração em milissegundos para o número de horas * ``hours(2) -> 7200000L`` @ no__t-1 @ no__t-2<br/><br/>
Mapeia cada elemento da matriz para um novo elemento usando a expressão fornecida. O mapa espera uma referência a um elemento na função de expressão como #item e uma referência ao índice de elemento como #index * ``iMap([1, 2, 3, 4], #item + 2 + #index) -> [4, 6, 8, 10]`` @ no__t-1 @ no__t-2<br/><br/>
Com base em uma condição, aplica-se um valor ou outro. Se outro não for especificado, ele será considerado nulo. Ambos os valores devem ser compatíveis (Numeric, String...) * ``iif(10 + 20 == 30, 'dumbo', 'gumbo') -> 'dumbo'`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4<br/><br/>
Verifica se o valor não é nulo e retorna-o mais retorna a alternativa. Ele testa todas as entradas até encontrar o primeiro valor não nulo * ``iifNull(10, 20) -> 10`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4<br/><br/>
Verifica se um item está na matriz * ``in([10, 20, 30], 10) -> true`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Converte a primeira letra de cada palavra em maiúsculas. As palavras são identificadas como separadas por espaço em branco * ``initCap('cool iceCREAM') -> 'Cool Icecream'`` @ no__t-1 @ no__t-2<br/><br/>
Localiza a posição (1 com base) da subcadeia de caracteres em uma cadeia de caracteres. 0 será retornado se não for encontrado * ``instr('dumbo', 'mbo') -> 3`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4<br/><br/>
Verifica se a linha está marcada para exclusão. Para transformações que levam mais de um fluxo de entrada, você pode passar o índice (baseado em 1) do fluxo. O valor padrão para o índice de fluxo é 1 * ``isDelete()`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Verifica se a linha está marcada como erro. Para transformações que levam mais de um fluxo de entrada, você pode passar o índice (baseado em 1) do fluxo. O valor padrão para o índice de fluxo é 1 * ``isError()`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Verifica se a linha está marcada para ser ignorada. Para transformações que levam mais de um fluxo de entrada, você pode passar o índice (baseado em 1) do fluxo. O valor padrão para o índice de fluxo é 1 * ``isIgnore()`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Verifica se a linha está marcada para inserção. Para transformações que levam mais de um fluxo de entrada, você pode passar o índice (baseado em 1) do fluxo. O valor padrão para o índice de fluxo é 1 * ``isInsert()`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Verifica se a linha é correspondida na pesquisa. Para transformações que levam mais de um fluxo de entrada, você pode passar o índice (baseado em 1) do fluxo. O valor padrão para o índice de fluxo é 1 * ``isMatch()`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Verifica se o valor é nulo * ``isNull(NULL()) -> true`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Verifica se a linha está marcada para atualização. Para transformações que levam mais de um fluxo de entrada, você pode passar o índice (baseado em 1) do fluxo. O valor padrão para o índice de fluxo é 1 * ``isUpdate()`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Verifica se a linha está marcada para inserção. Para transformações que levam mais de um fluxo de entrada, você pode passar o índice (baseado em 1) do fluxo. O valor padrão para o índice de fluxo é 1 * ``isUpsert()`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Obtém a curtose de uma coluna * ``kurtosis(sales)`` @ no__t-1 @ no__t-2<br/><br/>
Com base em critérios, obtém a curtose de uma coluna * ``kurtosisIf(region == 'West', sales)`` @ no__t-1 @ no__t-2<br/><br/>
Obtém o valor do primeiro parâmetro avaliado n linhas antes da linha atual. O segundo parâmetro é o número de linhas a serem examinadas e o valor padrão é 1. Se não houver tantas linhas, um valor de NULL será retornado, a menos que um valor padrão seja especificado * ``lag(amount, 2)`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Obtém o último valor de um grupo de colunas. Se o segundo parâmetro ignoreNulls for omitido, presume-se false * ``last(sales)`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Obtém a última data do mês em que foi dada uma data * ``lastDayOfMonth(toDate('2009-01-12')) -> toDate('2009-01-31')`` @ no__t-1 @ no__t-2<br/><br/>
Obtém o valor do primeiro parâmetro avaliado n linhas após a linha atual. O segundo parâmetro é o número de linhas a serem examinadas e o valor padrão é 1. Se não houver tantas linhas, um valor de NULL será retornado, a menos que um valor padrão seja especificado * ``lead(amount, 2)`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Comparação menor ou igual a operador. O mesmo que < = Operator * ``least(10, 30, 15, 20) -> 10`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Extrai uma subcadeia de caracteres de início no índice 1 com número de caracteres. O mesmo que substring (Str, 1, n) * ``left('bojjus', 2) -> 'bo'`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Retorna o comprimento da cadeia de caracteres * ``length('dumbo') -> 5`` @ no__t-1 @ no__t-2<br/><br/>
Operador de comparação less. O mesmo que < Operator * ``lesser(12, 24) -> true`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4<br/><br/>
Comparação menor ou igual a operador. O mesmo que < = Operator * ``lesserOrEqual(12, 12) -> true`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Obtém a distância Levenshtein entre duas cadeias de caracteres * ``levenshtein('boys', 'girls') -> 4`` @ no__t-1 @ no__t-2<br/><br/>
O padrão é uma cadeia de caracteres que corresponde literalmente. As exceções são os seguintes símbolos especiais: _ corresponde a um caractere na entrada (semelhante a. em expressões regulares de POSIX),% corresponde a zero ou mais caracteres na entrada (semelhante a. * em expressões regulares de POSIX).
O caractere de escape é ' '. Se um caractere de escape precede um símbolo especial ou outro caractere de escape, o caractere a seguir será correspondido literalmente. É inválido escapar de qualquer outro caractere.
* ``like('icecream', 'ice%') -> true``
___
### <code>locate</code>
<code><b>locate(<i>&lt;substring to find&gt;</i> : string, <i>&lt;string&gt;</i> : string, [<i>&lt;from index - 1-based&gt;</i> : integral]) => integer</b></code><br/><br/>
Localiza a posição (1 com base) da subcadeia de caracteres em uma cadeia de caracteres que inicia uma determinada posição. Se a posição for omitida, ela será considerada do início da cadeia de caracteres. 0 será retornado se não for encontrado * ``locate('mbo', 'dumbo') -> 3`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4<br/><br/>
Calcula o valor do log. Uma base opcional pode ser fornecida mais um número Euler, se usado * ``log(100, 10) -> 2`` @ no__t-1 @ no__t-2<br/><br/>
Calcula o valor do log com base em 10 base * ``log10(100) -> 2`` @ no__t-1 @ no__t-2<br/><br/>
Minúsculas uma cadeia de caracteres * ``lower('GunChus') -> 'gunchus'`` @ no__t-1 @ no__t-2<br/><br/>
A esquerda pads a cadeia de caracteres pelo preenchimento fornecido até que seja de um determinado comprimento. Se a cadeia de caracteres for igual ou maior que o comprimento, ela será cortada para o comprimento * ``lpad('dumbo', 10, '-') -> '-----dumbo'`` @ no__t-1 @ no__t-2 ' ' LPAD (' Dumbo ', 8, ' < > ')-> ' < > <dumbo'``
___
### <code>ltrim @ no__t-4 @ no__t-5<br/><br/>
A esquerda corta uma cadeia de caracteres da entrelinha. Se o segundo parâmetro não for especificado, ele cortará o espaço em branco. Caso contrário, ele apara qualquer caractere especificado no segundo parâmetro * ``ltrim('  dumbo  ') -> 'dumbo  '`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Mapeia cada elemento da matriz para um novo elemento usando a expressão fornecida. O mapa espera uma referência a um elemento na função de expressão como #item * ``map([1, 2, 3, 4], #item + 2) -> [3, 4, 5, 6]`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Obtém o valor máximo de uma coluna * ``max(sales)`` @ no__t-1 @ no__t-2<br/><br/>
Com base em um critério, obtém o valor máximo de uma coluna * ``maxIf(region == 'West', sales)`` @ no__t-1 @ no__t-2<br/><br/>
Calcula o resumo MD5 do conjunto de colunas de tipos de datavariantes primitivos e retorna uma cadeia de caracteres hexadecimal de 32 caracteres. Ele pode ser usado para calcular uma impressão digital para uma linha * ``md5(5, 'gunchus', 8.2, 'bojjus', true, toDate('2010-4-4')) -> '4ce8a880bd621a1ffad0bca905e1bc5a'`` @ no__t-1 @ no__t-2<br/><br/>
Obtém a média de valores de uma coluna. O mesmo que AVG * ``mean(sales)`` @ no__t-1 @ no__t-2<br/><br/>
Com base em um critério, obtém a média de valores de uma coluna. O mesmo que avgIf * ``meanIf(region == 'West', sales)`` @ no__t-1 @ no__t-2<br/><br/>
Obtém o valor de milissegundo de uma data. Você pode passar um fuso horário opcional na forma de ' GMT ', ' PST ', ' UTC ', ' América/Cayman '. O fuso horário local é usado como o padrão. Consulte SimpleDateFormat do Java para obter os formatos disponíveis. https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html * ``millisecond(toTimestamp('2009-07-30 12:58:59.871', 'yyyy-MM-dd HH:mm:ss.SSS')) -> 871`` @ no__t-2 @ no__t-3<br/><br/>
Duração em milissegundos para o número de milissegundos * ``seconds(2) -> 2L`` @ no__t-1 @ no__t-2<br/><br/>
Obtém o valor mínimo de uma coluna * ``min(sales)`` @ no__t-1 @ no__t-2<br/><br/>
Com base em um critério, obtém o valor mínimo de uma coluna * ``minIf(region == 'West', sales)`` @ no__t-1 @ no__t-2<br/><br/>
Subtrai números. Subtraia de um número de data de dias. Duração de Substract de um carimbo de data/hora. Substract dois carimbos de data/hora para obter a diferença em milissegundos. O mesmo que o operador * ``minus(20, 10) -> 10`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6 @ no__t-7<br/><br/>
Obtém o valor de minuto de um carimbo de data/hora. Você pode passar um fuso horário opcional na forma de ' GMT ', ' PST ', ' UTC ', ' América/Cayman '. O fuso horário local é usado como o padrão. Consulte SimpleDateFormat do Java para obter os formatos disponíveis. https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html * ``minute(toTimestamp('2009-07-30 12:58:59')) -> 58`` @ no__t-2 @ no__t-3 @ no__t-4<br/><br/>
Duração em milissegundos para o número de minutos * ``minutes(2) -> 120000L`` @ no__t-1 @ no__t-2<br/><br/>
Módulo de um par de números. O mesmo que o operador% * ``mod(20, 8) -> 4`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Obtém o valor de mês de uma data ou timestamp * ``month(toDate('2012-8-8')) -> 8`` @ no__t-1 @ no__t-2<br/><br/>
Obtém o número de meses entre duas datas. Você pode arredondar o cálculo. Você pode passar um fuso horário opcional na forma de ' GMT ', ' PST ', ' UTC ', ' América/Cayman '. O fuso horário local é usado como o padrão. Consulte SimpleDateFormat do Java para obter os formatos disponíveis. https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html * ``monthsBetween(toTimestamp('1997-02-28 10:30:00'), toDate('1996-10-30')) -> 3.94959677`` @ no__t-2 @ no__t-3<br/><br/>
Multiplica o par de números. O mesmo que o operador * * ``multiply(20, 10) -> 200`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
A função NTile divide as linhas de cada partição de janela em buckets `n` variando de 1 para no máximo `n`. Os valores de Bucket serão diferentes por no máximo 1. Se o número de linhas na partição não dividir uniformemente o número de buckets, os valores restantes serão distribuídos um por Bucket, começando com o primeiro Bucket. A função NTile é útil para o cálculo de tertiles, quartils, decis e outras estatísticas de resumo comuns. A função calcula duas variáveis durante a inicialização: o tamanho de um Bucket regular terá uma linha extra adicionada a ele. Ambas as variáveis são baseadas no tamanho da partição atual. Durante o processo de cálculo, a função controla o número da linha atual, o número do Bucket atual e o número da linha na qual o Bucket será alterado (bucketThreshold). Quando o número da linha atual atingir o limite de Bucket, o valor do Bucket será aumentado em um e o limite será aumentado pelo tamanho do Bucket (mais um extra, se o Bucket atual for preenchido).
* ``nTile()``
* ``nTile(numOfBuckets)``
___
### <code>negate</code>
<code><b>negate(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
Nega um número. Transforma números positivos em negativo e vice-versa * ``negate(13) -> -13`` @ no__t-1 @ no__t-2<br/><br/>
Retorna a próxima sequência exclusiva. O número é consecutivo apenas dentro de uma partição e é prefixado pela PartitionID * ``nextSequence() == 12313112 -> false`` @ no__t-1 @ no__t-2<br/><br/>
Normalizar o valor da cadeia para separar caracteres Unicode acentuados * ``regexReplace(normalize('bo²s'), `\p{M}`, '') -> 'boys'`` @ no__t-1 @ no__t-2<br/><br/>
Operador de negação lógica * ``not(true) -> false`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Operador de comparação não é igual a. O mesmo que! = operator * ``12 != 24 -> true`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Retorna um valor nulo. Use a sintaxe da função (NULL ()) se houver uma coluna chamada ' NULL '. Qualquer operação que usa resultará em um @no__t nulo-0 @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6<br/><br/>
Operador OR lógico. Mesmo que | | * ``or(true, false) -> true`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Módulo positivo do par de números.
* ``pmod(-20, 8) -> 4``
___
### <code>partitionId</code>
<code><b>partitionId() => integer</b></code><br/><br/>
Retorna a ID de partição atual que a linha de entrada está em * ``partitionId()`` @ no__t-1 @ no__t-2<br/><br/>
Eleva um número à potência de outro * ``power(10, 2) -> 100`` @ no__t-1 @ no__t-2<br/><br/>
Computa a classificação de um valor em um grupo de valores. O resultado é um mais o número de linhas precedentes ou iguais à linha atual na ordenação da partição. Os valores produzirão lacunas na sequência. A classificação funciona mesmo quando os dados não são classificados e procura alterações nos valores * ``rank(salesQtr, salesAmt)`` @ no__t-1 @ no__t-2<br/><br/>
Acumula elementos em uma matriz. A redução espera uma referência a um acumulador e um elemento na primeira função de expressão como #acc e #item e espera que o valor resultante como #result seja usado na segunda função de expressão * ``reduce([1, 2, 3, 4], 0, #acc + #item, #result) -> 10`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4<br/><br/>
Extraia uma subcadeia de caracteres correspondente para um determinado padrão de Regex. O último parâmetro identifica o grupo de correspondência e é padronizado como 1 se omitido. Use ' <regex> ' (aspas de fundo) para corresponder a uma cadeia de caracteres sem saída * ``regexExtract('Cost is between 600 and 800 dollars', '(\\d+) and (\\d+)', 2) -> '800'`` @ no__t-2 @ no__t-3 @ no__t-4<br/><br/>
Verifica se a cadeia de caracteres corresponde ao padrão Regex fornecido. Use ' <regex> ' (aspas de fundo) para corresponder a uma cadeia de caracteres sem saída * ``regexMatch('200.50', '(\\d+).(\\d+)') -> true`` @ no__t-2 @ no__t-3 @ no__t-4<br/><br/>
Substitua todas as ocorrências de um padrão Regex por outra subcadeia na cadeia de caracteres fornecida use ' <regex> ' (aspas de fundo) para corresponder a uma cadeia de caracteres sem saída * ``regexReplace('100 and 200', '(\\d+)', 'bojjus') -> 'bojjus and bojjus'`` @ no__t-2 @ no__t-3 @ no__t-4<br/><br/>
Divide uma cadeia de caracteres com base em um delimitador com base em Regex e retorna uma matriz de cadeias de caracteres * ``regexSplit('bojjusAgunchusBdumbo', `[CAB]`) -> ['bojjus', 'gunchus', 'dumbo']`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5<br/><br/>
Substitua todas as ocorrências de uma subcadeia de caracteres por outra subcadeia de caracteres na cadeia de caracteres especificada. Se o último parâmetro for omitido, o padrão será a cadeia de caracteres vazia * ``replace('doggie dog', 'dog', 'cat') -> 'catgie cat'`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4<br/><br/>
Inverte uma cadeia de caracteres * ``reverse('gunchus') -> 'suhcnug'`` @ no__t-1 @ no__t-2<br/><br/>
Extrai uma Subcadeia com o número de caracteres à direita. Igual a subcadeia de caracteres (Str, LENGTH (Str)-n, n) * ``right('bojjus', 2) -> 'us'`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Verifica se a cadeia de caracteres corresponde ao padrão Regex fornecido * ``rlike('200.50', `(\d+).(\d+)`) -> true`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Arredonda um número de acordo com uma escala opcional e um modo de arredondamento opcional. Se a escala for omitida, o padrão será 0.  Se o modo for omitido, ele será padronizado como ROUND_HALF_UP (5). Os valores para arredondamento incluem 1-ROUND_UP 2-ROUND_DOWN 3-ROUND_CEILING 4-ROUND_FLOOR 5-ROUND_HALF_UP 6-ROUND_HALF_DOWN 7-ROUND_HALF_EVEN 8-ROUND_UNNECESSARY * ``round(100.123) -> 100.0`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4<br/><br/>
Atribui uma numeração de linha sequencial para linhas em uma janela que começa com 1 * ``rowNumber()`` @ no__t-1 @ no__t-2<br/><br/>
A direita pads a cadeia de caracteres pelo preenchimento fornecido até que seja de um determinado comprimento. Se a cadeia de caracteres for igual ou maior que o comprimento, ela será cortada para o comprimento * ``rpad('dumbo', 10, '-') -> 'dumbo-----'`` @ no__t-1 @ no__t-2 @ no__t-3rtrim @ no__t-4 @ no__t-5<br/><br/>
A direita corta uma cadeia de caracteres à esquerda. Se o segundo parâmetro não for especificado, ele cortará o espaço em branco. Caso contrário, ele apara qualquer caractere especificado no segundo parâmetro * ``rtrim('  dumbo  ') -> '  dumbo'`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Obtém o segundo valor de uma data. Você pode passar um fuso horário opcional na forma de ' GMT ', ' PST ', ' UTC ', ' América/Cayman '. O fuso horário local é usado como o padrão. Consulte SimpleDateFormat do Java para obter os formatos disponíveis. https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html * ``second(toTimestamp('2009-07-30 12:58:59')) -> 59`` @ no__t-2 @ no__t-3<br/><br/>
Duração em milissegundos para o número de segundos * ``seconds(2) -> 2000L`` @ no__t-1 @ no__t-2<br/><br/>
Calcula o resumo SHA-1 do conjunto de colunas de tipos de dataprimitivos variantes e retorna uma cadeia de caracteres hexadecimal de 40 caracteres. Ele pode ser usado para calcular uma impressão digital para uma linha * ``sha1(5, 'gunchus', 8.2, 'bojjus', true, toDate('2010-4-4')) -> '46d3b478e8ec4e1f3b453ac3d8e59d5854e282bb'`` @ no__t-1 @ no__t-2<br/><br/>
Calcula o resumo SHA-2 do conjunto de colunas de tipos de dados primitivos variados de acordo com um comprimento de bit que só pode ser de valores 0 (256), 224, 256, 384, 512. Ele pode ser usado para calcular uma impressão digital para uma linha * ``sha2(256, 'gunchus', 8.2, 'bojjus', true, toDate('2010-4-4')) -> 'afe8a553b1761c67d76f8c31ceef7f71b66a1ee6f4e6d3b5478bf68b47d06bd3'`` @ no__t-1 @ no__t-2<br/><br/>
Calcula um valor do seno * ``sin(2) -> 0.9092974268256817`` @ no__t-1 @ no__t-2<br/><br/>
Calcula um valor seno hiperbólico * ``sinh(0) -> 0.0`` @ no__t-1 @ no__t-2<br/><br/>
Obtém a distorção de uma coluna * ``skewness(sales)`` @ no__t-1 @ no__t-2<br/><br/>
Com base em critérios, obtém a distorção de uma coluna * ``skewnessIf(region == 'West', sales)`` @ no__t-1 @ no__t-2<br/><br/>
Extrai um subconjunto de uma matriz de uma posição. A posição é baseada em 1. Se o comprimento for omitido, o padrão será o término da cadeia de caracteres * ``slice([10, 20, 30, 40], 1, 2) -> [10, 20]`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6 @ no__t-7<br/><br/>
Classifica a matriz usando a função de predicado fornecida. Sort espera uma referência a dois elementos consecutivos na função Expression como #item1 e #item2 * ``sort([4, 8, 2, 3], compare(#item1, #item2)) -> [2, 3, 4, 8]`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Obtém o código SOUNDEX para a cadeia de caracteres * ``soundex('genius') -> 'G520'`` @ no__t-1 @ no__t-2<br/><br/>
Divide uma cadeia de caracteres com base em um delimitador e retorna uma matriz de cadeias de caracteres * ``split('bojjus,guchus,dumbo', ',') -> ['bojjus', 'guchus', 'dumbo']`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6 @ no__t-7 @ no__t-8<br/><br/>
Calcula a raiz quadrada de um número * ``sqrt(9) -> 3`` @ no__t-1 @ no__t-2<br/><br/>
Verifica se a cadeia de caracteres começa com a cadeia de caracteres fornecida * ``startsWith('dumbo', 'du') -> true`` @ no__t-1 @ no__t-2<br/><br/>
Obtém o desvio padrão de uma coluna * ``stdDev(sales)`` @ no__t-1 @ no__t-2<br/><br/>
Com base em critérios, obtém o desvio padrão de uma coluna * ``stddevIf(region == 'West', sales)`` @ no__t-1 @ no__t-2<br/><br/>
Obtém o desvio padrão da população de uma coluna * ``stddevPopulation(sales)`` @ no__t-1 @ no__t-2<br/><br/>
Com base em critérios, obtém o desvio padrão da população de uma coluna * ``stddevPopulationIf(region == 'West', sales)`` @ no__t-1 @ no__t-2<br/><br/>
Obtém o desvio padrão de uma coluna * ``stddevSample(sales)`` @ no__t-1 @ no__t-2<br/><br/>
Com base em critérios, obtém o desvio padrão de uma coluna * ``stddevSampleIf(region == 'West', sales)`` @ no__t-1 @ no__t-2<br/><br/>
Subtraia meses de uma data ou timestamp. O mesmo que o operador para a data * ``subDays(toDate('2016-08-08'), 1) -> toDate('2016-08-07')`` @ no__t-1 @ no__t-2<br/><br/>
Subtraia meses de uma data ou timestamp * ``subMonths(toDate('2016-09-30'), 1) -> toDate('2016-08-31')`` @ no__t-1 @ no__t-2<br/><br/>
Extrai uma subcadeia de caracteres de um determinado comprimento de uma posição. A posição é baseada em 1. Se o comprimento for omitido, o padrão será o fim da cadeia de caracteres * ``substring('Cat in the hat', 5, 2) -> 'in'`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5<br/><br/>
Obtém a soma agregada de uma coluna numérica * ``sum(col)`` @ no__t-1 @ no__t-2<br/><br/>
Obtém a soma agregada de valores distintos de uma coluna numérica * ``sumDistinct(col)`` @ no__t-1 @ no__t-2<br/><br/>
Com base em critérios, obtém a soma agregada de uma coluna numérica. A condição pode ser baseada em qualquer coluna * ``sumDistinctIf(state == 'CA' && commission < 10000, sales)`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Com base em critérios, obtém a soma agregada de uma coluna numérica. A condição pode ser baseada em qualquer coluna * ``sumIf(state == 'CA' && commission < 10000, sales)`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Calcula um valor tangente * ``tan(0) -> 0.0`` @ no__t-1 @ no__t-2<br/><br/>
Calcula um valor tangente hiperbólico * ``tanh(0) -> 0.0`` @ no__t-1 @ no__t-2<br/><br/>
Converte qualquer número de data/hora/timestamp/cadeia de caracteres em representação binária * ``toBinary(3) -> [0x11]`` @ no__t-1 @ no__t-2<br/><br/>
Converte um valor de (' T', ' true ', ' y ', ' Yes ', ' 1 ') para true e (' f ', ' false ', ' n', ' no ', ' 0 ') como false e NULL para qualquer outro valor * ``toBoolean('true') -> true`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4<br/><br/>
Converte a cadeia de caracteres de data de entrada em data usando um formato de data de entrada opcional. Consulte SimpleDateFormat do Java para obter os formatos disponíveis. Se o formato de data de entrada for omitido, o formato padrão será aaaa-[M] M-[d] d. Os formatos aceitos são: [aaaa, YYYY-[M] M, YYYY-[M] M-[d] d, YYYY-[M] M-[d] dT *] * ``toDate('2012-8-18') -> toDate('2012-08-18')`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Converte qualquer número ou cadeia de caracteres em um valor decimal. Se a precisão e a escala não forem especificadas, o padrão será (10, 2). Um formato decimal do Java opcional pode ser usado para a conversão. Um formato de localidade opcional na forma de linguagem BCP47, como en-US, de, zh-CN * ``toDecimal(123.45) -> 123.45`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5<br/><br/>
Converte qualquer número ou cadeia de caracteres em um valor duplo. Um formato decimal do Java opcional pode ser usado para a conversão. Um formato de localidade opcional na forma de linguagem BCP47, como en-US, de, zh-CN * ``toDouble(123.45) -> 123.45`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5<br/><br/>
Converte qualquer número ou cadeia de caracteres em um valor float. Um formato decimal do Java opcional pode ser usado para a conversão. Trunca qualquer @no__t duplo-0 @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4<br/><br/>
Converte qualquer número ou cadeia de caracteres em um valor inteiro. Um formato decimal do Java opcional pode ser usado para a conversão. Trunca um longo, float, duplo * ``toInteger(123) -> 123`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4<br/><br/>
Converte qualquer número ou cadeia de caracteres em um valor longo. Um formato decimal do Java opcional pode ser usado para a conversão. Trunca qualquer float, Double * ``toLong(123) -> 123`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4<br/><br/>
Converte qualquer número ou cadeia de caracteres em um valor curto. Um formato decimal do Java opcional pode ser usado para a conversão. Trunca qualquer inteiro, longo, float, duplo * ``toShort(123) -> 123`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4<br/><br/>
Converte um tipo de dados primitivo em uma cadeia de caracteres. Para números e datas, um formato pode ser especificado. Se não for especificado, o padrão do sistema será escolhido. O formato decimal do Java é usado para números. Consulte Java SimpleDateFormat para todos os formatos de data possíveis; o formato padrão é AAAA-MM-DD * ``toString(10) -> '10'`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6 @ no__t-7 @ no__t-8 @ no__t-9<br/><br/>
Converte uma cadeia de caracteres em um timestamp dado um formato de carimbo de data/hora opcional. Consulte Java SimpleDateFormat para todos os formatos possíveis. Se o carimbo de data/hora for omitido, o padrão. YYYY-[M] M-[d] d hh: mm: SS [. f...] será usado. Você pode passar um fuso horário opcional na forma de ' GMT ', ' PST ', ' UTC ', ' América/Cayman '. O carimbo de data/hora dá suporte à precisão de até milissegundos com o valor de SimpleDateFormat de 999Refer Java para formatos disponíveis. https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html * ``toTimestamp('2016-12-31 00:12:00') -> toTimestamp('2016-12-31 00:12:00')`` @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6<br/><br/>
Converte o carimbo de data/hora em UTC. Você pode passar um fuso horário opcional na forma de ' GMT ', ' PST ', ' UTC ', ' América/Cayman '. Ele é padronizado para os SimpleDateFormat atuais do timezoneRefer Java para os formatos disponíveis. https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html * ``toUTC(currentTimeStamp()) == toTimestamp('2050-12-12 19:18:12') -> false`` @ no__t-2 @ no__t-3 @ no__t-4<br/><br/>
Substituir um conjunto de caracteres por outro conjunto de caracteres na cadeia de caracteres. Os caracteres têm 1 a 1 substituição * ``translate('(bojjus)', '()', '[]') -> '[bojjus]'`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Corta uma cadeia de caracteres de caractere à esquerda e à direita. Se o segundo parâmetro não for especificado, ele cortará o espaço em branco. Caso contrário, ele apara qualquer caractere especificado no segundo parâmetro * ``trim('  dumbo  ') -> 'dumbo'`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Sempre retorna um valor verdadeiro. Use a sintaxe da função (true ()) se houver uma coluna chamada ' true ' * ``(10 + 20 == 30) -> true`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Corresponde ao tipo da coluna. Só pode ser usado em expressões de padrão. número correspondente curto, inteiro, longo, duplo, float ou Decimal, integral corresponde a curto, inteiro, longo, fração de correspondências Double, float, decimal e DateTime corresponde à data ou ao tipo TIMESTAMP * ``typeMatch(type, 'number')`` @ no__t-1 @ no__t-2 @ no__ t-3<br/><br/>
Maiúsculas uma cadeia de caracteres * ``upper('bojjus') -> 'BOJJUS'`` @ no__t-1 @ no__t-2<br/><br/>
Retorna o UUID gerado * ``uuid()`` @ no__t-1 @ no__t-2<br/><br/>
Obtém a variância de uma coluna * ``variance(sales)`` @ no__t-1 @ no__t-2<br/><br/>
Com base em critérios, obtém a variância de uma coluna * ``varianceIf(region == 'West', sales)`` @ no__t-1 @ no__t-2<br/><br/>
Obtém a variação da população de uma coluna * ``variancePopulation(sales)`` @ no__t-1 @ no__t-2<br/><br/>
Com base em critérios, obtém a variação da população de uma coluna * ``variancePopulationIf(region == 'West', sales)`` @ no__t-1 @ no__t-2<br/><br/>
Obtém a variância não polarizada de uma coluna * ``varianceSample(sales)`` @ no__t-1 @ no__t-2<br/><br/>
Com base em critérios, obtém a variação não polarizada de uma coluna * ``varianceSampleIf(region == 'West', sales)`` @ no__t-1 @ no__t-2<br/><br/>
Obtém a semana do ano de acordo com a data * ``weekOfYear(toDate('2008-02-20')) -> 8`` @ no__t-1 @ no__t-2<br/><br/>
Duração em milissegundos para o número de semanas * ``weeks(2) -> 1209600000L`` @ no__t-1 @ no__t-2<br/><br/>
Operador XOR lógico. O mesmo que ^ operator * ``xor(true, false) -> true`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4<br/><br/>
Obtém o valor de ano de uma data * ``year(toDate('2012-8-8')) -> 2012``

## <a name="next-steps"></a>Passos seguintes

[Saiba como usar o Expression Builder](concepts-data-flow-expression-builder.md).
