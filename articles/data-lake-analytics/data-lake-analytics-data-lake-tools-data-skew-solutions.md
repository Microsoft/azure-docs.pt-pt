---
title: Resolver problemas de distorção de dados com o Azure Data Lake Tools para Visual Studio
description: Resolução de problemas possíveis soluções para problemas de distorção de dados com o Azure Data Lake Tools para Visual Studio.
services: data-lake-analytics
author: yanancai
ms.author: yanacai
ms.reviewer: jasonwhowell
ms.service: data-lake-analytics
ms.topic: conceptual
ms.date: 12/16/2016
ms.openlocfilehash: af55c161944447f2e6e2245fbb920803779984ca
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/11/2019
ms.locfileid: "59496202"
---
# <a name="resolve-data-skew-problems-by-using-azure-data-lake-tools-for-visual-studio"></a>Resolver problemas de distorção de dados com o Azure Data Lake Tools para Visual Studio

## <a name="what-is-data-skew"></a>O que há dados inclinar?

Brevemente indicado, distorção de dados é um valor de excesso representado. Imagine se atribuiu 50 examiners de imposto sobre a auditoria imposto de renda, um examinador para cada Estado dos Estados Unidos. Wyoming examiner, porque a população lá é pequena, tem muito a ver. Na Califórnia, no entanto, examinador é mantida muito ocupado devido a população do Estado de grandes.
    ![Exemplo de problema de distorção de dados](./media/data-lake-analytics-data-lake-tools-data-skew-solutions/data-skew-problem.png)

Em nosso cenário, os dados são uniformidade distribuídos por todos os examiners de imposto, que significa que alguns examiners tem de trabalhar mais do que outras pessoas. No seu próprio trabalho, com freqüência experiência situações como o exemplo de imposto sobre examiner aqui. Em termos mais técnicos, um vértice obtém dados muito mais do que seus usuários, uma situação que faz com que o vértice trabalhar mais do que os outros e que, eventualmente, mais lento um trabalho inteiro. O que é pior, a tarefa poderá falhar, uma vez que poderão ter vértices, por exemplo, uma limitação de tempo de execução de 5 horas e uma limitação de 6 GB de memória.

## <a name="resolving-data-skew-problems"></a>Resolução de problemas de distorção de dados

O Azure Data Lake Tools para Visual Studio pode ajudar a detetar se o trabalho tiver um problema de distorção de dados. Se existir um problema, pode resolvê-lo ao tentar as soluções nesta secção.

## <a name="solution-1-improve-table-partitioning"></a>Solução 1: Melhorar a criação de partições de tabela

### <a name="option-1-filter-the-skewed-key-value-in-advance"></a>Opção 1: Filtrar o valor da chave distorcido com antecedência

Se não afeta a lógica de negócio, pode filtrar os valores de freqüência mais alta com antecedência. Por exemplo, se houver muita 000 000 000 na coluna GUID, poderá não querer agregar esse valor. Antes de agregação, pode escrever "onde GUID! ="000-000 000"" para filtrar o valor de elevada frequência.

### <a name="option-2-pick-a-different-partition-or-distribution-key"></a>Opção 2: Escolher uma chave de partição ou de distribuição diferente

No exemplo anterior, se quiser apenas verificar a carga de trabalho de auditoria de imposto em todo o país, pode melhorar a distribuição de dados ao selecionar o número de ID como sua chave. Escolher uma partição diferente ou uma chave de distribuição pode por vezes, distribuir uniformemente os dados mais, mas precisa para se certificar de que essa opção não afeta a lógica de negócio. Por exemplo, para calcular a soma de imposto para cada Estado, poderá querer designar _estado_ como a chave de partição. Se continuar a ter este problema, tente utilizar a opção 3.

### <a name="option-3-add-more-partition-or-distribution-keys"></a>Opção 3: Adicionar mais chaves de partição ou de distribuição

Em vez de usar apenas _estado_ como uma chave de partição, pode utilizar mais do que uma chave para criação de partições. Por exemplo, considere adicionar _CEP_ como uma chave de partição adicional para reduzir o tamanho da partição de dados e distribuir os dados mais uniformemente.

### <a name="option-4-use-round-robin-distribution"></a>Opção 4: Utilizar a distribuição round robin

Se não conseguir encontrar uma chave apropriada para a partição e a distribuição, pode tentar utilizar a distribuição round-robin. Distribuição round-robin trata igualmente a todas as linhas e os coloca aleatoriamente em buckets correspondentes. Os dados são distribuídos uniformemente, mas ele perde informações de localidade, uma desvantagem que também pode reduzir o desempenho de tarefa para algumas operações. Além disso, se estiver fazendo a agregação para a chave distorcida mesmo assim, o problema de distorção de dados serão mantidas. Para saber mais sobre a distribuição round robin, consulte a secção de distribuições de tabela U-SQL em [CREATE TABLE (U-SQL): Criar uma tabela com o esquema](/u-sql/ddl/tables/create/managed/create-table-u-sql-creating-a-table-with-schema#dis_sch).

## <a name="solution-2-improve-the-query-plan"></a>Solução 2: Melhorar o plano de consulta

### <a name="option-1-use-the-create-statistics-statement"></a>Opção 1: Utilize a instrução CREATE STATISTICS

U-SQL fornece a instrução CREATE STATISTICS em tabelas. Esta declaração fornece mais informações para o otimizador de consultas sobre as características dos dados, tais como distribuição de valor, que são armazenados numa tabela. Para a maioria das consultas, o otimizador de consultas já gera as estatísticas necessárias para um plano de consulta de alta qualidade. Ocasionalmente, poderá ter de melhorar o desempenho das consultas ao criar estatísticas adicionais com CREATE STATISTICS ou ao modificar a estrutura de consulta. Para obter mais informações, consulte a [CREATE STATISTICS (U-SQL)](/u-sql/ddl/statistics/create-statistics) página.

Exemplo de código:

    CREATE STATISTICS IF NOT EXISTS stats_SampleTable_date ON SampleDB.dbo.SampleTable(date) WITH FULLSCAN;

>[!NOTE]
>Informações de estatísticas não são atualizadas automaticamente. Se atualizar os dados numa tabela sem voltar a criar as estatísticas, pode recusar o desempenho de consulta.

### <a name="option-2-use-skewfactor"></a>Opção 2: Utilizar SKEWFACTOR

Se pretender que o imposto para cada Estado além disso, tem de utilizar Agrupar por Estado, uma abordagem que não evita o problema de distorção de dados. No entanto, pode fornecer uma dica de dados na sua consulta para identificar a distorção de dados em chaves, para que o otimizador pode preparar um plano de execução para.

Normalmente, pode definir o parâmetro como 0,5 e 1, com 0,5, ou seja, não muito skew pesada do significado distorção e 1. Porque a sugestão afeta a otimização de plano de execução para a instrução atual e todas as instruções de downstream, certifique-se de que adicionar a sugestão antes do potencial inclinados key-wise agregação.

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

### <a name="option-3-use-rowcount"></a>Opção 3: Utilizar contagem de linhas  
Além de SKEWFACTOR, para casos de associação de chave inclinados específico, se souber que o outro conjunto de linha associado ao é pequeno, pode dizer o otimizador de adicionando uma dica de contagem de linhas na instrução U-SQL antes de associação. Dessa forma, o otimizador pode escolher uma estratégia de associação de difusão para ajudar a melhorar o desempenho. Lembre-se de que a contagem de linhas não resolve o problema de distorção de dados, mas ele pode oferecer alguma ajuda adicional.

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

## <a name="solution-3-improve-the-user-defined-reducer-and-combiner"></a>Solução 3: Melhorar a reducer definidas pelo utilizador e a combinação

Por vezes, pode escrever um operador definido pelo utilizador para lidar com a lógica de processo complicado e um reducer bem escrita e a combinação podem reduzir um problema de distorção de dados em alguns casos.

### <a name="option-1-use-a-recursive-reducer-if-possible"></a>Opção 1: Utilizar um reducer recursiva, se possível

Por predefinição, um reducer definidas pelo utilizador é executado no modo de não recursiva, que significa que reduzir o trabalho para uma chave é distribuída num único vértice. Mas, se seus dados são desviados, os grandes conjuntos de dados possam ser processados num único vértice e são executados durante muito tempo.

Para melhorar o desempenho, pode adicionar um atributo em seu código para definir reducer sejam executadas em modo recursivo. Em seguida, os grandes conjuntos de dados podem ser distribuídos por vários vértices e executar em paralelo, o que acelera a tarefa.

Para alterar um reducer não recursiva para recursiva, tem de certificar-se de que o seu algoritmo é associativo. Por exemplo, a soma é associativa, e a mediana não é. Terá também de certificar-se de que a entrada e saída para reducer mantêm o mesmo esquema.

Atributo de reducer recursiva:

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

### <a name="option-2-use-row-level-combiner-mode-if-possible"></a>Opção 2: Utilizar o modo de combinação ao nível da linha, se possível

Assim como a sugestão de contagem de linhas para casos de associação de chave inclinados específico, modo de combinação tenta distribuir conjuntos de grande valor de chave inclinados para vários vértices, para que o trabalho pode ser executado em simultâneo. Modo de combinação não é possível resolver problemas de distorção de dados, mas ele pode oferecer alguma ajuda adicional para conjuntos de valores de chave inclinados enorme.

Por predefinição, o modo de combinação é completo, o que significa que o conjunto de linha à esquerda e o conjunto de linhas certo, não podem ser separados. Definir o modo como esquerda/direita/interna permite que a associação ao nível da linha. O sistema separa os conjuntos de linha correspondente e distribui-los em vários vértices que executam em paralelo. No entanto, antes de configurar o modo de combinação, tenha cuidado para garantir que os conjuntos de linhas correspondentes podem ser separados.

O exemplo a seguir mostra um conjunto de linha à esquerda separados. Cada linha da saída depende de uma única linha de entrada da esquerda e, potencialmente depende todas as linhas da direita com o mesmo valor de chave. Se definir o modo de combinação como à esquerda, o sistema separa a enorme esquerda da linha definida para os pequenos e atribui-los a vários vértices.

![Ilustração do modo de combinação](./media/data-lake-analytics-data-lake-tools-data-skew-solutions/combiner-mode-illustration.png)

>[!NOTE]
>Se definir o modo de combinação errado, a combinação é menos eficiente, e os resultados poderão ter algum problema.

Atributos do modo de combinação de:

- [SqlUserDefinedCombiner(Mode=CombinerMode.Full)]: Every output row potentially depends on all the input rows from left and right with the same key value.

- SqlUserDefinedCombiner(Mode=CombinerMode.Left): Todas as linhas de saída depende de uma única linha de entrada da esquerda (e potencialmente todas as linhas da direita com o mesmo valor de chave).

- qlUserDefinedCombiner(Mode=CombinerMode.Right): Todas as linhas de saída depende de uma única linha de entrada do lado direito (e potencialmente todas as linhas a partir da esquerda com o mesmo valor de chave).

- SqlUserDefinedCombiner(Mode=CombinerMode.Inner): Todas as linhas de saída depende de uma única linha de entrada da esquerda e à direita com o mesmo valor.

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
