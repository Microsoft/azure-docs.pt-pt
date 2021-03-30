---
title: Resolver distorção de dados - Azure Data Lake Tools for Visual Studio
description: Resolução de problemas em potenciais soluções para problemas de distorção de dados utilizando ferramentas do Lago de Dados Azure para o Estúdio Visual.
ms.reviewer: jasonh
ms.service: data-lake-analytics
ms.topic: how-to
ms.date: 12/16/2016
ms.openlocfilehash: 0b82ce187c06afa69e54ea93931e1745f0d52674
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "92219195"
---
# <a name="resolve-data-skew-problems-by-using-azure-data-lake-tools-for-visual-studio"></a>Resolver problemas de distorção de dados com as Ferramentas do Azure Data Lake para Visual Studio

## <a name="what-is-data-skew"></a>O que é distorção de dados?

Dito brevemente, o distorção dos dados é um valor sobre-representado. Imagine que atribuiu 50 tax examiners para auditar declarações fiscais, um examinador para cada estado dos EUA. O examinador de Wyoming, porque a população lá é pequena, tem pouco a fazer. Na Califórnia, no entanto, o examinador é mantido muito ocupado por causa da grande população do estado.
    ![Exemplo de problema de distorção de dados](./media/data-lake-analytics-data-lake-tools-data-skew-solutions/data-skew-problem.png)

No nosso cenário, os dados são distribuídos de forma desigual em todos os examinadores fiscais, o que significa que alguns examinadores devem trabalhar mais do que outros. No seu próprio trabalho, experimenta frequentemente situações como o exemplo do fiscal-examinador aqui. Em termos mais técnicos, um vértice obtém muito mais dados do que os seus pares, uma situação que faz com que o vértice funcione mais do que os outros e que acaba por atrasar um trabalho inteiro. O pior é que o trabalho pode falhar, porque os vértices podem ter, por exemplo, uma limitação de 5 horas de tempo de execução e uma limitação de memória de 6 GB.

## <a name="resolving-data-skew-problems"></a>Resolução de problemas de distorção de dados

O Azure Data Lake Tools for Visual Studio pode ajudar a detetar se o seu trabalho tem um problema de distorção de dados. Se houver um problema, pode resolvê-lo experimentando as soluções nesta secção.

## <a name="solution-1-improve-table-partitioning"></a>Solução 1: Melhorar a divisão de mesa

### <a name="option-1-filter-the-skewed-key-value-in-advance"></a>Opção 1: Filtrar antecipadamente o valor da chave distorcida

Se não afetar a sua lógica de negócio, pode filtrar antecipadamente os valores de maior frequência. Por exemplo, se houver um monte de 000-000-000 na coluna GUID, você pode não querer agregar esse valor. Antes de agregar, pode escrever "WHERE GUID != "000-000-000"" para filtrar o valor de alta frequência.

### <a name="option-2-pick-a-different-partition-or-distribution-key"></a>Opção 2: Escolha uma chave de partição ou distribuição diferente

No exemplo anterior, se quiser apenas verificar a carga de trabalho de auditoria fiscal em todo o país/região, pode melhorar a distribuição de dados selecionando o número de identificação como chave. Escolher uma chave de partilha ou distribuição diferente pode, por vezes, distribuir os dados de forma mais uniforme, mas é preciso certificar-se de que esta escolha não afeta a lógica do seu negócio. Por exemplo, para calcular a soma de imposto para cada Estado, talvez queira designar o _Estado_ como chave de partição. Se continuar a sentir este problema, tente utilizar a Opção 3.

### <a name="option-3-add-more-partition-or-distribution-keys"></a>Opção 3: Adicionar mais chaves de partição ou distribuição

Em vez de usar apenas _o Estado_ como chave de partição, pode usar mais do que uma chave para a partilha. Por exemplo, considere adicionar _código ZIP_ como uma chave de partição adicional para reduzir os tamanhos de partição de dados e distribuir os dados de forma mais uniforme.

### <a name="option-4-use-round-robin-distribution"></a>Opção 4: Utilizar distribuição de rodapé

Se não encontrar uma chave adequada para a partilha e distribuição, pode tentar utilizar a distribuição de rodadas. A distribuição de robin redondo trata todas as linhas de forma igual e aleatória coloca-as em baldes correspondentes. Os dados são distribuídos uniformemente, mas perdem informação de localidade, um inconveniente que também pode reduzir o desempenho do trabalho em algumas operações. Além disso, se estiver a fazer agregação para a chave distorcida de qualquer maneira, o problema de distorção de dados persistirá. Para saber mais sobre a distribuição de robin redondo, consulte a secção de distribuição de mesa u-SQL em [TABELA CREATE (U-SQL): Criação de uma tabela com Schema](/u-sql/ddl/tables/create/managed/create-table-u-sql-creating-a-table-with-schema#dis_sch).

## <a name="solution-2-improve-the-query-plan"></a>Solução 2: Melhorar o plano de consulta

### <a name="option-1-use-the-create-statistics-statement"></a>Opção 1: Utilizar a declaração DE ESTATÍSTICAS CREATE

A U-SQL fornece a declaração DE ESTATÍSTICAS CREATE sobre tabelas. Esta declaração dá mais informação ao otimizador de consultas sobre as características dos dados, como a distribuição de valor, que são armazenados numa tabela. Para a maioria das consultas, o otimizador de consultas já gera as estatísticas necessárias para um plano de consulta de alta qualidade. Ocasionalmente, poderá ter de melhorar o desempenho da consulta criando estatísticas adicionais com ESTATÍSTICAS CREATE ou modificando o design de consulta. Para obter mais informações, consulte a página [CREATE STATISTICS (U-SQL).](/u-sql/ddl/statistics/create-statistics)

Exemplo de código:

```usql
CREATE STATISTICS IF NOT EXISTS stats_SampleTable_date ON SampleDB.dbo.SampleTable(date) WITH FULLSCAN;
```

>[!NOTE]
>A informação estatística não é atualizada automaticamente. Se atualizar os dados numa tabela sem recriar as estatísticas, o desempenho da consulta poderá diminuir.

### <a name="option-2-use-skewfactor"></a>Opção 2: Utilizar SKEWFACTOR

Se quiser resumir o imposto para cada Estado, tem de utilizar o GRUPO A Estado, uma abordagem que não evite o problema da distorção de dados. No entanto, pode fornecer uma dica de dados na sua consulta para identificar dados distorcidos em teclas para que o otimizador possa preparar um plano de execução para si.

Normalmente, pode definir o parâmetro como 0.5 e 1, com 0,5 significa que não há muito skew e 1 significando distorção pesada. Como a dica afeta a otimização do plano de execução para a declaração atual e todas as declarações a jusante, certifique-se de adicionar a dica antes da potencial agregação de chave distorcida.

```usql
SKEWFACTOR (columns) = x
```

Fornece uma sugestão de que as colunas dadas têm um fator de distorção x de 0 (sem distorção) a 1 (distorção muito pesada).

Exemplo de código:

```usql
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
```

### <a name="option-3-use-rowcount"></a>Opção 3: Utilizar ROWCOUNT
Além do SKEWFACTOR, para casos específicos de junção de chave enviesada, se souber que o outro conjunto de linhas juntas é pequeno, pode dizer ao optimizador adicionando uma dica ROWCOUNT na declaração de U-SQL antes de JOIN. Desta forma, o optimizador pode escolher uma estratégia de junção de transmissão para ajudar a melhorar o desempenho. Esteja ciente de que rowcount não resolve o problema de distorção de dados, mas pode oferecer alguma ajuda adicional.

```usql
OPTION(ROWCOUNT = n)
```

Identifique uma pequena linha definida antes de JOIN, fornecendo uma contagem de linhas de inteiros estimada.

Exemplo de código:

```usql
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
```

## <a name="solution-3-improve-the-user-defined-reducer-and-combiner"></a>Solução 3: Melhorar o redutor e o combinador definidos pelo utilizador

Por vezes, pode escrever um operador definido pelo utilizador para lidar com uma lógica de processo complicada, e um redutor e combinador bem escrito pode mitigar um problema de distorção de dados em alguns casos.

### <a name="option-1-use-a-recursive-reducer-if-possible"></a>Opção 1: Utilize um redutor recursivo, se possível

Por predefinição, um redutor definido pelo utilizador funciona em modo não recursivo, o que significa que reduzir o trabalho para uma tecla é distribuído num único vértice. Mas se os seus dados forem distorcidos, os enormes conjuntos de dados podem ser processados num único vértice e funcionar durante muito tempo.

Para melhorar o desempenho, pode adicionar um atributo no seu código para definir o redutor para executar em modo recursivo. Em seguida, os enormes conjuntos de dados podem ser distribuídos por vários vértices e executados em paralelo, o que acelera o seu trabalho.

Para alterar um redutor não recursivo para recursivo, é necessário certificar-se de que o seu algoritmo é associativo. Por exemplo, a soma é associativa, e a mediana não é. Também é necessário certificar-se de que a entrada e a saída para o redutor mantêm o mesmo esquema.

Atributo do redutor recursivo:

```usql
[SqlUserDefinedReducer(IsRecursive = true)]
```

Exemplo de código:

```usql
[SqlUserDefinedReducer(IsRecursive = true)]
public class TopNReducer : IReducer
{
    public override IEnumerable<IRow>
        Reduce(IRowset input, IUpdatableRow output)
    {
        //Your reducer code goes here.
    }
}
```

### <a name="option-2-use-row-level-combiner-mode-if-possible"></a>Opção 2: Utilize o modo de combinação ao nível da linha, se possível

Semelhante à sugestão rowcount para casos específicos de junção de chave enviesada, o modo combiner tenta distribuir enormes conjuntos de valor distorcido para múltiplos vértices para que o trabalho possa ser executado simultaneamente. O modo Combiner não consegue resolver problemas de distorção de dados, mas pode oferecer alguma ajuda adicional para enormes conjuntos de valor distorcido.

Por predefinição, o modo de combinação é Full, o que significa que o conjunto da linha esquerda e a linha direita não podem ser separados. Definir o modo como Esquerda/Direita/Interior permite a junção ao nível da linha. O sistema separa os conjuntos de linha correspondentes e distribui-os em múltiplos vértices que funcionam em paralelo. No entanto, antes de configurar o modo combiner, tenha cuidado para que os conjuntos de linha correspondentes possam ser separados.

O exemplo que se segue mostra um conjunto de linha esquerda separado. Cada linha de saída depende de uma única linha de entrada a partir da esquerda, e potencialmente depende de todas as linhas da direita com o mesmo valor chave. Se definir o modo de combinação como esquerdo, o sistema separa o enorme conjunto da linha esquerda em pequenos e atribui-os a vários vértices.

![Ilustração do modo combiner](./media/data-lake-analytics-data-lake-tools-data-skew-solutions/combiner-mode-illustration.png)

>[!NOTE]
>Se definir o modo combinador errado, a combinação é menos eficiente, e os resultados podem estar errados.

Atributos do modo combiner:

- SqlUserDefinedCombiner (Mode=CombinerMode.Full): Todas as linhas de saída dependem potencialmente de todas as linhas de entrada da esquerda e da direita com o mesmo valor de chave.

- SqlUserDefinedCombiner (Mode=CombinerMode.Left): Cada linha de saída depende de uma única linha de entrada a partir da esquerda (e potencialmente todas as linhas da direita com o mesmo valor de chave).

- qlUserDefinedCombiner (Mode=CombinerMode.Right): Cada linha de saída depende de uma única linha de entrada da direita (e potencialmente todas as linhas a partir da esquerda com o mesmo valor de chave).

- SqlUserDefinedCombiner (Mode=CombinerMode.Inner): Cada linha de saída depende de uma única linha de entrada da esquerda e da direita com o mesmo valor.

Exemplo de código:

```usql
[SqlUserDefinedCombiner(Mode = CombinerMode.Right)]
public class WatsonDedupCombiner : ICombiner
{
    public override IEnumerable<IRow>
        Combine(IRowset left, IRowset right, IUpdatableRow output)
    {
    //Your combiner code goes here.
    }
}
```
