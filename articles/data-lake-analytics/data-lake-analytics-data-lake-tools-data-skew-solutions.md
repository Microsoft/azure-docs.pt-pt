---
title: Resolver o skew de dados - Ferramentas do Lago de Dados Azure para estúdio visual
description: Resolução de problemas potenciais soluções para problemas de distorção de dados utilizando ferramentas de lago de dados Azure para estúdio visual.
services: data-lake-analytics
author: yanancai
ms.author: yanacai
ms.reviewer: jasonwhowell
ms.service: data-lake-analytics
ms.topic: conceptual
ms.date: 12/16/2016
ms.openlocfilehash: 9ff7ba5f04a8c1862f8ef136f8f3f6900f00a431
ms.sourcegitcommit: 6a4fbc5ccf7cca9486fe881c069c321017628f20
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/27/2020
ms.locfileid: "71802559"
---
# <a name="resolve-data-skew-problems-by-using-azure-data-lake-tools-for-visual-studio"></a>Resolver problemas de distorção de dados com as Ferramentas do Azure Data Lake para Visual Studio

## <a name="what-is-data-skew"></a>O que é distorção de dados?

Brevemente declarado, o enviesamento de dados é um valor sobre-representado. Imagine que atribuiu 50 fiscais a 50 examinadores fiscais para auditar declarações fiscais, um examinador para cada estado dos EUA. O examinador de Wyoming, porque a população lá é pequena, tem pouco a fazer. Na Califórnia, no entanto, o examinador é mantido muito ocupado por causa da grande população do estado.
    ![Exemplo de problema sonuoso de dados](./media/data-lake-analytics-data-lake-tools-data-skew-solutions/data-skew-problem.png)

No nosso cenário, os dados são distribuídos de forma desigual por todos os examinadores fiscais, o que significa que alguns examinadores devem trabalhar mais do que outros. No seu próprio trabalho, experimenta frequentemente situações como o exemplo do tax-examiner aqui. Em termos mais técnicos, um vértice obtém muito mais dados do que os seus pares, uma situação que faz com que o vértice funcione mais do que os outros e que acaba por atrasar um trabalho inteiro. O pior é que o trabalho pode falhar, porque os vértices podem ter, por exemplo, uma limitação de tempo de execução de 5 horas e uma limitação de memória de 6 GB.

## <a name="resolving-data-skew-problems"></a>Resolução de problemas de distorção de dados

As Ferramentas do Lago de Dados Azure para o Estúdio Visual podem ajudar a detetar se o seu trabalho tem um problema de distorção de dados. Se existir um problema, pode resolvê-lo experimentando as soluções nesta secção.

## <a name="solution-1-improve-table-partitioning"></a>Solução 1: Melhorar a partilha de mesa

### <a name="option-1-filter-the-skewed-key-value-in-advance"></a>Opção 1: Filtrar antecipadamente o valor-chave distorcido

Se não afetar a sua lógica de negócio, pode filtrar antecipadamente os valores de maior frequência. Por exemplo, se houver um monte de 000-000-000 na coluna GUID, talvez não queira agregar esse valor. Antes de agregar, pode escrever "WHERE GUID != "000-000-000" para filtrar o valor de alta frequência.

### <a name="option-2-pick-a-different-partition-or-distribution-key"></a>Opção 2: Escolha uma chave de partição ou distribuição diferente

No exemplo anterior, se quiser apenas verificar a carga de trabalho de auditoria fiscal em todo o país/região, pode melhorar a distribuição de dados selecionando o número de identificação como chave. Escolher uma chave de partição ou distribuição diferente pode por vezes distribuir os dados de forma mais homolativa, mas é preciso ter a certeza de que esta escolha não afeta a sua lógica de negócio. Por exemplo, para calcular a soma de impostos para cada estado, é melhor designar o _Estado_ como a chave de partição. Se continuar a experimentar este problema, tente utilizar a Opção 3.

### <a name="option-3-add-more-partition-or-distribution-keys"></a>Opção 3: Adicionar mais chaves de partição ou distribuição

Em vez de usar apenas o _Estado_ como chave de partição, pode usar mais do que uma chave para a partilha. Por exemplo, considere adicionar _o Código POSTAL_ como uma chave adicional de partição para reduzir os tamanhos da partilha de dados e distribuir os dados de forma mais homolética.

### <a name="option-4-use-round-robin-distribution"></a>Opção 4: Utilizar a distribuição de robin redondo

Se não encontrar uma chave adequada para a partilha e distribuição, pode tentar utilizar a distribuição de robin redondo. A distribuição de robin redondo trata todas as linhas de forma igual e aleatória coloca-as em baldes correspondentes. Os dados são distribuídos uniformemente, mas perde informação sobre a localização, um inconveniente que também pode reduzir o desempenho do trabalho em algumas operações. Além disso, se estiver a fazer agregação para a chave distorcida de qualquer forma, o problema de distorção de dados persistirá. Para saber mais sobre a distribuição de robin sarna, consulte a secção de distribuição de mesa U-SQL em [TABELA CREATE (U-SQL): Criação de uma tabela com Schema](/u-sql/ddl/tables/create/managed/create-table-u-sql-creating-a-table-with-schema#dis_sch).

## <a name="solution-2-improve-the-query-plan"></a>Solução 2: Melhorar o plano de consulta

### <a name="option-1-use-the-create-statistics-statement"></a>Opção 1: Utilize a declaração CREATE STATISTICS

A U-SQL fornece a declaração de ESTATÍSTICAS CREATE nas tabelas. Esta declaração dá mais informações ao otimizador de consultas sobre as características dos dados, como a distribuição de valor, que são armazenadas numa tabela. Para a maioria das consultas, o optimizador de consultas já gera as estatísticas necessárias para um plano de consulta de alta qualidade. Ocasionalmente, poderá ser necessário melhorar o desempenho da consulta, criando estatísticas adicionais com estatísticas create ou modificando o design de consulta. Para mais informações, consulte a página [CREATE STATISTICS (U-SQL).](/u-sql/ddl/statistics/create-statistics)

Exemplo de código:

    CREATE STATISTICS IF NOT EXISTS stats_SampleTable_date ON SampleDB.dbo.SampleTable(date) WITH FULLSCAN;

>[!NOTE]
>As informações estatísticas não são atualizadas automaticamente. Se atualizar os dados numa tabela sem recriar as estatísticas, o desempenho da consulta poderá diminuir.

### <a name="option-2-use-skewfactor"></a>Opção 2: Utilizar o SKEWFACTOR

Se quiser sumir o imposto para cada Estado, deve utilizar o GRUPO POR Estado, uma abordagem que não evite o problema da distorção de dados. No entanto, pode fornecer uma dica de dados na sua consulta para identificar dados distorcidos em teclas para que o optimizador possa preparar um plano de execução para si.

Normalmente, pode definir o parâmetro como 0,5 e 1, com 0,5 significando pouco desvirtuado e 1 que significa distorção pesada. Uma vez que a dica afeta a otimização do plano de execução para a declaração atual e todas as declarações a jusante, certifique-se de adicionar a dica antes da potencial agregação em termos de chave distorcida.

    SKEWFACTOR (columns) = x

    Provides a hint that the given columns have a skew factor x from 0 (no skew) through 1 (very heavy skew).

Exemplo de código:

    //Add a SKEWFACTOR hint.
    @Impressions =
        SELECT * FROM
        searchDM.SML.PageView(@start, @end) AS PageView
        OPTION(SKEWFACTOR(Query)=0.5)
        ;

    //Query 1 for key: Query, ClientId
    @Sessions =
        SELECT
            ClientId,
            Query,
            SUM(PageClicks) AS Clicks
        FROM
            @Impressions
        GROUP BY
            Query, ClientId
        ;

    //Query 2 for Key: Query
    @Display =
        SELECT * FROM @Sessions
            INNER JOIN @Campaigns
                ON @Sessions.Query == @Campaigns.Query
        ;   

### <a name="option-3-use-rowcount"></a>Opção 3: Utilizar a CONTAGEM DE LINHA  
Além do SKEWFACTOR, para casos específicos de adesão de chave enviesada, se souber que o outro conjunto de linhas aderiu é pequeno, pode dizer ao optimizador adicionando uma dica rowCOUNT na declaração u-SQL antes da JOIN. Desta forma, o optimizador pode escolher uma estratégia de adesão de transmissão para ajudar a melhorar o desempenho. Esteja ciente de que o ROWCOUNT não resolve o problema da distorção de dados, mas pode oferecer alguma ajuda adicional.

    OPTION(ROWCOUNT = n)

    Identify a small row set before JOIN by providing an estimated integer row count.

Exemplo de código:

    //Unstructured (24-hour daily log impressions)
    @Huge   = EXTRACT ClientId int, ...
                FROM @"wasb://ads@wcentralus/2015/10/30/{*}.nif"
                ;

    //Small subset (that is, ForgetMe opt out)
    @Small  = SELECT * FROM @Huge
                WHERE Bing.ForgetMe(x,y,z)
                OPTION(ROWCOUNT=500)
                ;

    //Result (not enough information to determine simple broadcast JOIN)
    @Remove = SELECT * FROM Bing.Sessions
                INNER JOIN @Small ON Sessions.Client == @Small.Client
                ;

## <a name="solution-3-improve-the-user-defined-reducer-and-combiner"></a>Solução 3: Melhorar o redutor e o combinador definidos pelo utilizador

Por vezes, pode escrever um operador definido pelo utilizador para lidar com uma lógica de processo complicada, e um redutor e combinador bem escritopode mitigar um problema de distorção de dados em alguns casos.

### <a name="option-1-use-a-recursive-reducer-if-possible"></a>Opção 1: Utilize um redutor recursivo, se possível

Por predefinição, um redutor definido pelo utilizador funciona em modo não recursivo, o que significa que reduzir o trabalho para uma chave é distribuído num único vértice. Mas se os seus dados forem distorcidos, os enormes conjuntos de dados podem ser processados num único vértice e funcionar durante muito tempo.

Para melhorar o desempenho, pode adicionar um atributo no seu código para definir o redutor para funcionar em modo recursivo. Em seguida, os enormes conjuntos de dados podem ser distribuídos para múltiplos vértices e executados em paralelo, o que acelera o seu trabalho.

Para alterar um redutor não recursivo para recursivo, precisa de se certificar de que o seu algoritmo é associativo. Por exemplo, a soma é associativa, e a mediana não é. Também precisa de se certificar de que a entrada e a saída para o redutor mantêm o mesmo esquema.

Atributo de redutor recursivo:

    [SqlUserDefinedReducer(IsRecursive = true)]

Exemplo de código:

    [SqlUserDefinedReducer(IsRecursive = true)]
    public class TopNReducer : IReducer
    {
        public override IEnumerable<IRow>
            Reduce(IRowset input, IUpdatableRow output)
        {
            //Your reducer code goes here.
        }
    }

### <a name="option-2-use-row-level-combiner-mode-if-possible"></a>Opção 2: Utilize o modo combinador de nível de linha, se possível

Semelhante à sugestão rowcount para casos específicos de união de chave distorcida, o modo combinador tenta distribuir enormes conjuntos de valor de chave distorcida para múltiplos vértices para que o trabalho possa ser executado simultaneamente. O modo combinador não pode resolver problemas de distorção de dados, mas pode oferecer alguma ajuda adicional para enormes conjuntos de valor enviesados.

Por predefinição, o modo combinador está cheio, o que significa que o conjunto de linha esquerda e o conjunto da linha direita não podem ser separados. A definição do modo de esquerda/direita/interior permite a adesão ao nível da linha. O sistema separa os conjuntos de linhas correspondentes e distribui-os em múltiplos vértices que funcionam em paralelo. No entanto, antes de configurar o modo combinador, tenha cuidado para garantir que os conjuntos de linhas correspondentes podem ser separados.

O exemplo que se segue mostra um conjunto de fila esquerda separado. Cada linha de saída depende de uma única linha de entrada a partir da esquerda, e potencialmente depende de todas as linhas da direita com o mesmo valor-chave. Se definir o modo combinador como esquerdo, o sistema separa a enorme linha esquerda definida em pequenas e atribui-as a múltiplos vértices.

![Ilustração do modo combinador](./media/data-lake-analytics-data-lake-tools-data-skew-solutions/combiner-mode-illustration.png)

>[!NOTE]
>Se definir o modo combinador errado, a combinação é menos eficiente e os resultados podem estar errados.

Atributos do modo combinador:

- SqlUserDefinedCombiner (Mode=CombinerMode.Full): Cada linha de saída depende potencialmente de todas as linhas de entrada da esquerda e da direita com o mesmo valor-chave.

- SqlUserDefinedCombiner (Mode=CombinerMode.Left): Cada linha de saída depende de uma única linha de entrada a partir da esquerda (e potencialmente todas as linhas da direita com o mesmo valor-chave).

- qlUserDefinedCombiner (Mode=CombinerMode.Right): Cada linha de saída depende de uma única linha de entrada da direita (e potencialmente todas as linhas da esquerda com o mesmo valor-chave).

- SqlUserDefinedCombiner (Mode=CombinerMode.Inner): Cada linha de saída depende de uma única linha de entrada da esquerda e da direita com o mesmo valor.

Exemplo de código:

    [SqlUserDefinedCombiner(Mode = CombinerMode.Right)]
    public class WatsonDedupCombiner : ICombiner
    {
        public override IEnumerable<IRow>
            Combine(IRowset left, IRowset right, IUpdatableRow output)
        {
        //Your combiner code goes here.
        }
    }
