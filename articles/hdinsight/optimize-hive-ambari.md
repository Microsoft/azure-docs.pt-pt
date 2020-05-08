---
title: Otimizar a Colmeia Apache com Apache Ambari em Azure HDInsight
description: Utilize a UI web Apache Ambari para configurar e otimizar a Colmeia Apache.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/04/2020
ms.openlocfilehash: ce3916ef1155224a91c0736c3dabe907ae8d2611
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/05/2020
ms.locfileid: "82796372"
---
# <a name="optimize-apache-hive-with-apache-ambari-in-azure-hdinsight"></a>Otimizar a Colmeia Apache com Apache Ambari em Azure HDInsight

Apache Ambari é uma interface web para gerir e monitorizar os clusters HDInsight. Para uma introdução ao Ambari Web UI, consulte [Os clusters Manage HDInsight utilizando o Apache Ambari Web UI](hdinsight-hadoop-manage-ambari.md).

As seguintes secções descrevem opções de configuração para otimizar o desempenho global da Apache Hive.

1. Para modificar os parâmetros de configuração da Hive, **selecione Hive** da barra lateral dos Serviços.
1. Navegue para o separador **Configs.**

## <a name="set-the-hive-execution-engine"></a>Desloque o motor de execução da Colmeia

A Hive fornece dois motores de execução: Apache Hadoop MapReduce e Apache TEZ. Tez é mais rápido que MapReduce. Os clusters HDInsight Linux têm tez como motor de execução padrão. Para alterar o motor de execução:

1. No separador Hive **Configs,** digite o motor de **execução** na caixa de filtro.

    ![Motor de execução de pesquisa Apache Ambari](./media/optimize-hive-ambari/ambari-search-execution.png)

1. O valor padrão da propriedade **de Otimização** é **Tez.**

    ![Otimização - Motor Apache Tez](./media/optimize-hive-ambari/optimization-apache-tez.png)

## <a name="tune-mappers"></a>Mappers de melodia

Hadoop tenta dividir *(mapa)* um único ficheiro em vários ficheiros e processar os ficheiros resultantes em paralelo. O número de mappers depende do número de divisões. Os dois parâmetros de configuração seguintes conduzem o número de divisões para o motor de execução Tez:

* `tez.grouping.min-size`: Limite inferior ao tamanho de uma divisão agrunada, com um valor predefinido de 16 MB (16.777.216 bytes).
* `tez.grouping.max-size`: Limite superior do tamanho de uma divisão agrupada, com um valor predefinido de 1 GB (1.073.741.824 bytes).

Como orientação de desempenho, baixe ambos estes parâmetros para melhorar a latência, aumente para mais rendimento.

Por exemplo, para definir quatro tarefas mapper para um tamanho de dados de 128 MB, definiria ambos os parâmetros para 32 MB cada (33.554.432 bytes).

1. Para modificar os parâmetros de limite, navegue para o separador **Configs** do serviço Tez. Expanda **General** o painel geral `tez.grouping.max-size` `tez.grouping.min-size` e localize os e parâmetros.

1. Coloque ambos os parâmetros em **33.554.432** bytes (32 MB).

    ![Tamanhos de agrupamento Apache Ambari Tez](./media/optimize-hive-ambari/apache-tez-grouping-size.png)

Estas mudanças afetam todos os empregos da Tez em todo o servidor. Para obter um resultado ideal, escolha os valores adequados do parâmetro.

## <a name="tune-reducers"></a>Redutores de melodia

Apache ORC e Snappy oferecem alto desempenho. No entanto, a Hive pode ter muito poucos redutores por padrão, causando estrangulamentos.

Por exemplo, diga que tem um tamanho de dados de entrada de 50 GB. Esses dados em formato ORC com compressão Snappy são de 1 GB. A Hive estima o número de redutores necessários como: (número `hive.exec.reducers.bytes.per.reducer`de bytes entradas para mappers / ).

Com as definições predefinidas, este exemplo são quatro redutores.

O `hive.exec.reducers.bytes.per.reducer` parâmetro especifica o número de bytes processados por redutor. O valor predefinido é de 64 MB. A sintonização deste valor aumenta o paralelismo e pode melhorar o desempenho. Ajustá-lo demasiado baixo também pode produzir demasiados redutores, afetando potencialmente o desempenho. Este parâmetro baseia-se nos seus requisitos específicos de dados, configurações de compressão e outros fatores ambientais.

1. Para modificar o parâmetro, navegue para o separador Hive **Configs** e encontre o parâmetro **Data per Reducer** na página Definições.

    ![Apache Ambari Data por Redutor](./media/optimize-hive-ambari/ambari-data-per-reducer.png)

1. Selecione **Editar** para modificar o valor para 128 MB (134.217.728 bytes) e, em seguida, prima **Enter** para poupar.

    ![Ambari Data por Redutor - editado](./media/optimize-hive-ambari/data-per-reducer-edited.png)
  
    Dado um tamanho de entrada de 1.024 MB, com 128 MB de dados por redutor, existem oito redutores (1024/128).

1. Um valor incorreto para o parâmetro **Data per Reducer** pode resultar num grande número de redutores, afetando negativamente o desempenho da consulta. Para limitar o número máximo de `hive.exec.reducers.max` redutores, fixado sumo a um valor adequado. O valor padrão é 1009.

## <a name="enable-parallel-execution"></a>Permitir a execução paralela

Uma consulta da Colmeia é executada em uma ou mais fases. Se as etapas independentes puderem ser executadas em paralelo, isso aumentará o desempenho da consulta.

1. Para permitir a execução de consulta paralela, navegue `hive.exec.parallel` até ao separador Hive **Config** e procure a propriedade. O valor predefinido é false. Mude o valor para verdadeiro e, em seguida, pressione **Enter** para salvar o valor.

1. Para limitar o número de postos de `hive.exec.parallel.thread.number` trabalho a funcionar em paralelo, modifique a propriedade. O valor padrão é 8.

    ![Exibição paralela do executivo da Colmeia Apache](./media/optimize-hive-ambari/apache-hive-exec-parallel.png)

## <a name="enable-vectorization"></a>Ativar a vectorização

A colmeia processa dados de linha a linha. A vectorização direciona a Hive para processar dados em blocos de 1.024 linhas em vez de uma linha de cada vez. A vectorização só é aplicável ao formato de ficheiro ORC.

1. Para permitir uma execução de consulta vetorial, navegue até `hive.vectorized.execution.enabled` ao separador Hive **Configs** e procure o parâmetro. O valor predefinido é verdadeiro para a Colmeia 0.13.0 ou mais tarde.

1. Para permitir a execução vetorialpara o `hive.vectorized.execution.reduce.enabled` lado de redução da consulta, defina o parâmetro como verdadeiro. O valor predefinido é false.

    ![Execução vetorial apache hive](./media/optimize-hive-ambari/hive-vectorized-execution.png)

## <a name="enable-cost-based-optimization-cbo"></a>Ativar a otimização baseada nos custos (CBO)

Por padrão, a Hive segue um conjunto de regras para encontrar um plano de execução de consulta ideal. A otimização baseada em custos (CBO) avalia vários planos para executar uma consulta. E atribui um custo a cada plano, e ntão determina o plano mais barato para executar uma consulta.

Para ativar o CBO, navegue para as**definições** da **Hive** > **Configs** > e encontre o **Optimizador baseado em custos de ativação**e, em seguida, mude o botão de alternância para **On**.

![Otimizador baseado em custos HDInsight](./media/optimize-hive-ambari/hdinsight-cbo-config.png)

Os seguintes parâmetros de configuração adicionais aumentam o desempenho da consulta da Hive quando o CBO está ativado:

* `hive.compute.query.using.stats`

    Quando é verdade, a Hive utiliza estatísticas armazenadas na `count(*)`sua metaloja para responder a simples consultas como .

    ![Consulta computacional apache hive usando estatísticas](./media/optimize-hive-ambari/hive-compute-query-using-stats.png)

* `hive.stats.fetch.column.stats`

    As estatísticas das colunas são criadas quando o CBO está ativado. A Hive utiliza estatísticas de colunas, que são armazenadas na metaloja, para otimizar consultas. Buscar estatísticas de colunas para cada coluna demora mais tempo quando o número de colunas é elevado. Quando definido como falso, esta definição desativa as estatísticas das colunas da metaloja.

    ![Estatísticas da Colmeia Apache definir estatísticas de colunas](./media/optimize-hive-ambari/hive-stats-fetch-column-stats.png)

* `hive.stats.fetch.partition.stats`

    Estatísticas básicas de partição, tais como o número de linhas, tamanho de dados e tamanho de ficheiro são armazenadas na metaloja. Se for em realidade, as estatísticas de partição são recolhidas na metaloja. Quando falso, o tamanho do ficheiro é recolhido do sistema de ficheiros. E o número de filas é recolhido do esquema da fila.

    ![Estatísticas da colmeia definir estatísticas de partição](./media/optimize-hive-ambari/hive-stats-fetch-partition-stats.png)

## <a name="enable-intermediate-compression"></a>Ativar a compressão intermédia

As tarefas do mapa criam ficheiros intermédios que são utilizados pelas tarefas de redução. A compressão intermédia diminui o tamanho do ficheiro intermédio.

Os empregos de hadoop são geralmente engarrafados. Os dados comprimidos podem acelerar a transferência geral de E/S e a transferência global da rede.

Os tipos de compressão disponíveis são:

| Formato | Ferramenta | Algoritmo | Extensão de ficheiros | Mesa dividida? |
| --- | --- | --- | --- | --- |
| Gzip | Gzip | ESVAZIAR | `.gz` | No |
| Bzip2 | Bzip2 | Bzip2 |`.bz2` | Sim |
| LZO | `Lzop` | LZO | `.lzo` | Sim, se indexado |
| Estaladiço | N/D | Estaladiço | Estaladiço | No |

Regra geral, ter o método de compressão splittable é importante, caso contrário poucos mappers serão criados. Se os dados de `bzip2` entrada forem texto, é a melhor opção. Para o formato ORC, o Snappy é a opção de compressão mais rápida.

1. Para permitir a compressão intermédia, navegue até ao `hive.exec.compress.intermediate` separador Hive **Configs** e, em seguida, coloque o parâmetro verdadeiro. O valor predefinido é false.

    !['Hive exec compress intermediário'](./media/optimize-hive-ambari/hive-exec-compress-intermediate.png)

    > [!NOTE]  
    > Para comprimir ficheiros intermédios, escolha um código de compressão com um custo de CPU mais baixo, mesmo que o codec não tenha uma saída de alta compressão.

1. Para definir o código de compressão `mapred.map.output.compression.codec` intermédio, `hive-site.xml` `mapred-site.xml` adicione a propriedade personalizada ao ou ficheiro.

1. Para adicionar uma definição personalizada:

    a. Navegue para **hive** > **Configs** > **avançado** > local de**colmeia.**

    b. **Selecione Adicionar Propriedade...** na parte inferior do painel personalizado do site da colmeia.

    c. Na janela Adicionar Propriedade, `mapred.map.output.compression.codec` introduza `org.apache.hadoop.io.compress.SnappyCodec` como chave e como valor.

    d. Selecione **Adicionar**.

    !['Apache Hive personalizado adicionar'](./media/optimize-hive-ambari/hive-custom-property.png)

    Esta definição comprime o ficheiro intermédio utilizando a compressão Snappy. Uma vez adicionada a propriedade, aparece no painel personalizado do site da colmeia.

    > [!NOTE]  
    > Este procedimento modifica `$HADOOP_HOME/conf/hive-site.xml` o ficheiro.

## <a name="compress-final-output"></a>Comprimir a saída final

A saída final da Colmeia também pode ser comprimida.

1. Para comprimir a saída final da Colmeia, navegue para o `hive.exec.compress.output` separador Hive **Configs** e, em seguida, coloque o parâmetro verdadeiro. O valor predefinido é false.

1. Para escolher o codec de `mapred.output.compression.codec` compressão de saída, adicione a propriedade personalizada ao painel personalizado do site da colmeia, conforme descrito no passo 3 da secção anterior.

    ![Apache Hive personalizado adicionar2](./media/optimize-hive-ambari/hive-custom-property2.png)

## <a name="enable-speculative-execution"></a>Permitir a execução especulativa

A execução especulativa lança um certo número de tarefas duplicadas para detetar e negar a lista do rastreador de tarefas de execução lenta. Ao mesmo tempo que melhora a execução global do trabalho, otimizando os resultados individuais da tarefa.

A execução especulativa não deve ser ligada para tarefas mapeadas de longa duração Com grandes quantidades de entrada.

* Para permitir a execução especulativa, navegue para o `hive.mapred.reduce.tasks.speculative.execution` separador Hive **Configs** e, em seguida, coloque o parâmetro verdadeiro. O valor predefinido é false.

    !["Colmeia mapeada reduz em tarefas de execução especulativa"](./media/optimize-hive-ambari/hive-mapred-reduce-tasks-speculative-execution.png)

## <a name="tune-dynamic-partitions"></a>Afinar divisórias dinâmicas

A Hive permite criar divisórias dinâmicas ao inserir registos numa mesa, sem pré-definir todas as divisórias. Esta habilidade é uma característica poderosa. Embora possa resultar na criação de um grande número de divisórias. E um grande número de ficheiros para cada partição.

1. Para que a Hive faça `hive.exec.dynamic.partition` divisórias dinâmicas, o valor do parâmetro deve ser verdadeiro (o padrão).

1. Mude o modo dinâmico de partição para *rigoroso*. Em modo rigoroso, pelo menos uma partição tem de ser estática. Esta definição impede consultas sem o filtro de partição na cláusula WHERE, isto é, *impede rigorosamente* as consultas que digitalizam todas as divisórias. Navegue para o separador Hive `hive.exec.dynamic.partition.mode` **Configs** e, em seguida, desloque-se para **rigoroso**. O valor predefinido não é **restrito**.

1. Para limitar o número de divisórias `hive.exec.max.dynamic.partitions` dinâmicas a criar, modifique o parâmetro. O valor padrão é 5000.

1. Para limitar o número total de divisórias dinâmicas por nó, modifique `hive.exec.max.dynamic.partitions.pernode`. O valor padrão é 2000.

## <a name="enable-local-mode"></a>Ativar o modo local

O modo local permite que a Hive faça todas as tarefas de um trabalho numa única máquina. Ou às vezes num único processo. Esta definição melhora o desempenho da consulta se os dados de entrada forem pequenos. E a sobrecarga das tarefas de lançamento de consultas consome uma percentagem significativa da execução total da consulta.

Para ativar o `hive.exec.mode.local.auto` modo local, adicione o parâmetro ao painel personalizado do sítio da colmeia, conforme explicado no passo 3 da secção de [compressão intermédia Enable.](#enable-intermediate-compression)

![Apache Hive exec modo automóvel local](./media/optimize-hive-ambari/hive-exec-mode-local-auto.png)

## <a name="set-single-mapreduce-multigroup-by"></a>Definir mapa únicoReduzir MultiGRUPO POR

Quando esta propriedade está definida para ser verdadeira, uma consulta multigrupo BY com teclas comuns de grupo gera um único trabalho MapReduce.  

Para ativar este comportamento, adicione o `hive.multigroupby.singlereducer` parâmetro ao painel personalizado do sítio da colmeia, como explicado no passo 3 da secção de [compressão intermédia Enable.](#enable-intermediate-compression)

![Conjunto de colmeia único MapReduce MultiGROUP BY](./media/optimize-hive-ambari/hive-multigroupby-singlereducer.png)

## <a name="additional-hive-optimizations"></a>Otimizações adicionais da Colmeia

As seguintes secções descrevem otimizações adicionais relacionadas com a Colmeia que pode definir.

### <a name="join-optimizations"></a>Junte-se a otimizações

O tipo de união padrão na Colmeia é uma *mistura de shuffle*. Na Colmeia, os mapeadores especiais lêem a entrada e emitem um par chave/valor de juntar-se a um ficheiro intermédio. Hadoop classifica e funde estes pares numa fase de baralhar. Esta fase de baralhar é cara. A seleção da união certa com base nos seus dados pode melhorar significativamente o desempenho.

| Tipo de adesão | Quando | Como | Configurações da colmeia | Comentários |
| --- | --- | --- | --- | --- |
| Shuffle Join | <ul><li>Escolha por defeito</li><li>Sempre funciona</li></ul> | <ul><li>Lê de parte de uma das mesas</li><li>Baldes e tipos na chave Juntar</li><li>Envia um balde para cada redução</li><li>A adesão é feita no lado da Redução</li></ul> | Não é necessária uma definição significativa da Colmeia | Funciona sempre |
| Reunião do mapa | <ul><li>Uma mesa pode caber na memória</li></ul> | <ul><li>Lê pequena tabela na mesa de hash de memória</li><li>Streams através de parte do grande arquivo</li><li>Junta-se a cada disco da mesa hash</li><li>As juntas são só pelo mapper</li></ul> | `hive.auto.confvert.join=true` | Rápido, mas limitado |
| Ordenar balde de fusão | Se ambas as mesas forem: <ul><li>Ordenado o mesmo</li><li>Baldedo da mesma forma</li><li>Juntando-se à coluna ordenada/balde</li></ul> | Cada processo: <ul><li>Lê um balde de cada mesa</li><li>Processa a linha com o valor mais baixo</li></ul> | `hive.auto.convert.sortmerge.join=true` | Eficiente |

### <a name="execution-engine-optimizations"></a>Otimizações do motor de execução

Recomendações adicionais para otimizar o motor de execução da Colmeia:

| Definição | Recomendado | Padrão HDInsight |
| --- | --- | --- |
| `hive.mapjoin.hybridgrace.hashtable` | Verdade = mais seguro, mais lento; falso = mais rápido | false |
| `tez.am.resource.memory.mb` | 4-GB superior bound para a maioria | Auto-Sintonizado |
| `tez.session.am.dag.submit.timeout.secs` | 300+ | 300 |
| `tez.am.container.idle.release-timeout-min.millis` | 20000+ | 10000 |
| `tez.am.container.idle.release-timeout-max.millis` | 40000+ | 20 000 |

## <a name="next-steps"></a>Passos seguintes

* [Gerir clusters HDInsight com a UI web Apache Ambari](hdinsight-hadoop-manage-ambari.md)
* [Apache Ambari REST API](hdinsight-hadoop-manage-ambari-rest-api.md)
* [Optimize Apache Hive queries in Azure HDInsight](./hdinsight-hadoop-optimize-hive-query.md) (Otimizar as consultas do Apache Hive no Azure HDInsight)
* [Otimizar aglomerados](./optimize-hive-ambari.md)
* [Otimizar Apache HBase](./optimize-hbase-ambari.md)
* [Otimizar o Porco Apache](./optimize-pig-ambari.md)