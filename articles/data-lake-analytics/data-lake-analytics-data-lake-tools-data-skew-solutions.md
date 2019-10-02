---
title: Resolver dados-distorção-Ferramentas do Azure Data Lake para Visual Studio
description: Solução de problemas de soluções potenciais para problema de distorção de dados usando Ferramentas do Azure Data Lake para Visual Studio.
services: data-lake-analytics
author: yanancai
ms.author: yanacai
ms.reviewer: jasonwhowell
ms.service: data-lake-analytics
ms.topic: conceptual
ms.date: 12/16/2016
ms.openlocfilehash: 9ff7ba5f04a8c1862f8ef136f8f3f6900f00a431
ms.sourcegitcommit: 4f3f502447ca8ea9b932b8b7402ce557f21ebe5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/02/2019
ms.locfileid: "71802559"
---
# <a name="resolve-data-skew-problems-by-using-azure-data-lake-tools-for-visual-studio"></a>Resolver problemas de distorção de dados usando Ferramentas do Azure Data Lake para Visual Studio

## <a name="what-is-data-skew"></a>O que é distorção de dados?

Declarado brevemente, a distorção de dados é um valor superrepresentado. Imagine que você atribuiu 50 examinadores de impostos para auditar Devoluções de imposto, um examinador para cada Estado dos EUA. O Wyoming Examiner, porque a população que há é pequena, tem pouco a ver. Na Califórnia, no entanto, o examinador é mantido muito ocupado devido à grande população do estado.
    ![Exemplo de problema de distorção de dados](./media/data-lake-analytics-data-lake-tools-data-skew-solutions/data-skew-problem.png)

Em nosso cenário, os dados são distribuídos desuniformemente entre todos os examinadores de impostos, o que significa que alguns examinadores devem trabalhar mais do que outros. Em seu próprio trabalho, você frequentemente experimenta situações como o exemplo de exame de imposto aqui. Em termos mais técnicos, um vértice obtém muito mais dados do que seus colegas, uma situação que faz com que o vértice funcione mais do que os outros e que eventualmente reduz um trabalho inteiro. O que é pior, o trabalho pode falhar, porque os vértices podem ter, por exemplo, uma limitação de tempo de execução de 5 horas e uma limitação de 6 GB de memória.

## <a name="resolving-data-skew-problems"></a>Resolvendo problemas de distorção de dados

Ferramentas do Azure Data Lake para Visual Studio pode ajudar a detectar se o seu trabalho tem um problema de distorção de dados. Se houver um problema, você poderá resolvê-lo experimentando as soluções nesta seção.

## <a name="solution-1-improve-table-partitioning"></a>Solução 1: Melhorar o particionamento de tabela

### <a name="option-1-filter-the-skewed-key-value-in-advance"></a>Opção 1: Filtrar o valor da chave distorcida com antecedência

Se não afetar sua lógica de negócios, você poderá filtrar os valores de frequência mais alta com antecedência. Por exemplo, se houver muito 000-000-000 em GUID de coluna, talvez você não queira agregar esse valor. Antes de agregar, você pode gravar "WHERE GUID! =" 000-000-000 "" para filtrar o valor de alta frequência.

### <a name="option-2-pick-a-different-partition-or-distribution-key"></a>Opção 2: Escolha uma partição ou chave de distribuição diferente

No exemplo anterior, se você quiser apenas verificar a carga de trabalho de auditoria de imposto em todo o país/região, você pode melhorar a distribuição de dados selecionando o número de ID como sua chave. A escolha de uma partição ou chave de distribuição diferente pode, às vezes, distribuir os dados mais uniformemente, mas você precisa certificar-se de que essa opção não afete sua lógica de negócios. Por exemplo, para calcular a soma de impostos para cada Estado, talvez você queira designar o _estado_ como a chave de partição. Se você continuar a experimentar esse problema, tente usar a opção 3.

### <a name="option-3-add-more-partition-or-distribution-keys"></a>Opção 3: Adicionar mais chaves de partição ou distribuição

Em vez de usar apenas o _estado_ como uma chave de partição, você pode usar mais de uma chave para o particionamento. Por exemplo, considere adicionar o _CEP_ como uma chave de partição adicional para reduzir tamanhos de partição de dados e distribuir os dados mais uniformemente.

### <a name="option-4-use-round-robin-distribution"></a>Opção 4: Usar a distribuição Round Robin

Se você não encontrar uma chave apropriada para partição e distribuição, poderá tentar usar a distribuição Round Robin. A distribuição Round Robin trata todas as linhas igualmente e as coloca aleatoriamente nos buckets correspondentes. Os dados são distribuídos uniformemente, mas perdem informações de localidade, uma desvantagem que também pode reduzir o desempenho do trabalho para algumas operações. Além disso, se você estiver fazendo agregação para a chave distorcida de qualquer forma, o problema de distorção de dados persistirá. Para saber mais sobre a distribuição Round Robin, consulte a seção distribuições de tabela do U- [SQL em CREATE TABLE (U-SQL): Criando uma tabela com esquema](/u-sql/ddl/tables/create/managed/create-table-u-sql-creating-a-table-with-schema#dis_sch).

## <a name="solution-2-improve-the-query-plan"></a>Solução 2: Melhorar o plano de consulta

### <a name="option-1-use-the-create-statistics-statement"></a>Opção 1: Usar a instrução CREATE STATISTICs

O U-SQL fornece a instrução CREATE STATISTICs em tabelas. Essa instrução fornece mais informações ao otimizador de consulta sobre as características de dados, como distribuição de valor, que são armazenadas em uma tabela. Para a maioria das consultas, o otimizador de consulta já gera as estatísticas necessárias para um plano de consulta de alta qualidade. Ocasionalmente, talvez seja necessário melhorar o desempenho da consulta criando estatísticas adicionais com CREATE STATISTICs ou modificando o design da consulta. Para obter mais informações, consulte a página [CREATE STATISTICS (U-SQL)](/u-sql/ddl/statistics/create-statistics) .

Exemplo de código:

    CREATE STATISTICS IF NOT EXISTS stats_SampleTable_date ON SampleDB.dbo.SampleTable(date) WITH FULLSCAN;

>[!NOTE]
>As informações de estatísticas não são atualizadas automaticamente. Se você atualizar os dados em uma tabela sem recriar as estatísticas, o desempenho da consulta poderá ser recusado.

### <a name="option-2-use-skewfactor"></a>Opção 2: Usar SKEWFACTOR

Se você quiser somar o imposto para cada Estado, deverá usar GROUP BY State, uma abordagem que não evita o problema de distorção de dados. No entanto, você pode fornecer uma dica de dados em sua consulta para identificar a distorção de dados em chaves para que o otimizador possa preparar um plano de execução para você.

Normalmente, você pode definir o parâmetro como 0,5 e 1, com 0,5, o que significa que não há muita distorção e 1 significa distorção pesada. Como a dica afeta a otimização do plano de execução para a instrução atual e todas as instruções de downstream, certifique-se de adicionar a dica antes da possível agregação de chave distorcida.

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

### <a name="option-3-use-rowcount"></a>Opção 3: Usar número de linhas  
Além de SKEWFACTOR, para casos de junção de chave distorcido específica, se você souber que o outro conjunto de linhas associado é pequeno, você pode informar ao otimizador adicionando uma dica de ROWCOUNT na instrução U-SQL antes de JOIN. Dessa forma, o otimizador pode escolher uma estratégia de junção de difusão para ajudar a melhorar o desempenho. Lembre-se de que ROWCOUNT não resolve o problema de distorção de dados, mas pode oferecer ajuda adicional.

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

## <a name="solution-3-improve-the-user-defined-reducer-and-combiner"></a>Solução 3: Melhorar o redutor e o combinador definidos pelo usuário

Às vezes, você pode escrever um operador definido pelo usuário para lidar com a lógica de processo complicada e um redutor e um combinador bem escritos podem mitigar um problema de distorção de dados em alguns casos.

### <a name="option-1-use-a-recursive-reducer-if-possible"></a>Opção 1: Usar um redutor recursivo, se possível

Por padrão, um redutor definido pelo usuário é executado no modo não recursivo, o que significa que a redução do trabalho para uma chave é distribuída em um único vértice. Mas se os dados estiverem distorcidos, os grandes conjuntos de dados poderão ser processados em um único vértice e executados por muito tempo.

Para melhorar o desempenho, você pode adicionar um atributo em seu código para definir redutor para execução no modo recursivo. Em seguida, os grandes conjuntos de dados podem ser distribuídos para vários vértices e executados em paralelo, o que acelera o trabalho.

Para alterar um redutor não recursivo para recursivo, você precisa certificar-se de que seu algoritmo seja associativo. Por exemplo, a soma é associativa e a mediana não. Você também precisa certificar-se de que a entrada e a saída para redutor manter o mesmo esquema.

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

### <a name="option-2-use-row-level-combiner-mode-if-possible"></a>Opção 2: Use o modo combinador de nível de linha, se possível

Semelhante à dica ROWCOUNT para casos de junção de chave distorcida específicos, o modo combinador tenta distribuir grandes conjuntos de valores de chave distorcido para vários vértices para que o trabalho possa ser executado simultaneamente. O modo combinador não pode resolver problemas de distorção de dados, mas pode oferecer alguma ajuda adicional para grandes conjuntos de valores de chave distorcido.

Por padrão, o modo combinador está cheio, o que significa que o conjunto de linhas à esquerda e o conjunto de linhas à direita não podem ser separados. A definição do modo como esquerda/direita/interna habilita a junção em nível de linha. O sistema separa os conjuntos de linhas correspondentes e os distribui em vários vértices que são executados em paralelo. No entanto, antes de configurar o modo combinador, tenha cuidado para garantir que os conjuntos de linhas correspondentes possam ser separados.

O exemplo a seguir mostra um conjunto de linhas à esquerda separado. Cada linha de saída depende de uma única linha de entrada da esquerda e, potencialmente, depende de todas as linhas da direita com o mesmo valor de chave. Se você definir o modo combinador como esquerdo, o sistema separa o enorme conjunto de linhas à esquerda em pequenos e os atribui a vários vértices.

![Ilustração do modo combinador](./media/data-lake-analytics-data-lake-tools-data-skew-solutions/combiner-mode-illustration.png)

>[!NOTE]
>Se você definir o modo de combinação incorreto, a combinação será menos eficiente e os resultados poderão estar errados.

Atributos do modo combinador:

- SqlUserDefinedCombiner (Mode = CombinerMode. Full): Cada linha de saída pode depender de todas as linhas de entrada da esquerda e da direita com o mesmo valor de chave.

- SqlUserDefinedCombiner(Mode=CombinerMode.Left): Cada linha de saída depende de uma única linha de entrada da esquerda (e potencialmente de todas as linhas da direita com o mesmo valor de chave).

- qlUserDefinedCombiner(Mode=CombinerMode.Right): Cada linha de saída depende de uma única linha de entrada da direita (e potencialmente de todas as linhas da esquerda com o mesmo valor de chave).

- SqlUserDefinedCombiner(Mode=CombinerMode.Inner): Cada linha de saída depende de uma única linha de entrada da esquerda e da direita com o mesmo valor.

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
