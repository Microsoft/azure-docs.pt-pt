---
title: Otimizar a Colmeia Apache com Apache Ambari em Azure HDInsight
description: Use a UI da web Apache Ambari para configurar e otimizar a Colmeia Apache.
ms.service: hdinsight
ms.topic: how-to
ms.date: 05/04/2020
ms.openlocfilehash: 349f58720e6fff52191dfff65108cd1320e41eed
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/28/2021
ms.locfileid: "98939259"
---
# <a name="optimize-apache-hive-with-apache-ambari-in-azure-hdinsight"></a>Otimizar a Colmeia Apache com Apache Ambari em Azure HDInsight

Apache Ambari é uma interface web para gerir e monitorizar clusters HDInsight. Para uma introdução à Ambari Web UI, consulte [gerencie os clusters HDInsight utilizando o Apache Ambari Web UI](hdinsight-hadoop-manage-ambari.md).

As secções seguintes descrevem opções de configuração para otimizar o desempenho geral da Colmeia Apache.

1. Para modificar os parâmetros de configuração da Colmeia, **selecione Hive** da barra lateral de Serviços.
1. Navegue para o **separador Configs.**

## <a name="set-the-hive-execution-engine"></a>Definir o motor de execução da Colmeia

A Hive fornece dois motores de execução: Apache Hadoop MapReduce e Apache TEZ. Tez é mais rápido que MapReduce. Os clusters HDInsight Linux têm tez como motor de execução padrão. Para alterar o motor de execução:

1. No separador Hive **Configs,** digite **o motor de execução** na caixa do filtro.

    ![Apache Ambari Motor de execução de busca](./media/optimize-hive-ambari/ambari-search-execution.png)

1. O valor padrão da propriedade **otimização** é **Tez.**

    ![Otimização - Motor Apache Tez](./media/optimize-hive-ambari/optimization-apache-tez.png)

## <a name="tune-mappers"></a>Sintonizar mappers

Hadoop tenta dividir *(mapa)* um único ficheiro em vários ficheiros e processar os ficheiros resultantes em paralelo. O número de mappers depende do número de divisões. Os dois parâmetros de configuração seguintes conduzem o número de divisões para o motor de execução Tez:

* `tez.grouping.min-size`: Limite inferior ao tamanho de uma divisão agrupada, com um valor predefinido de 16 MB (16.777.216 bytes).
* `tez.grouping.max-size`: Limite superior do tamanho de uma divisão agrupada, com um valor predefinido de 1 GB (1.073.741.824 bytes).

Como uma orientação de desempenho, baixe ambos estes parâmetros para melhorar a latência, aumentar para mais produção.

Por exemplo, para definir quatro tarefas mapper para um tamanho de dados de 128 MB, você definiria ambos os parâmetros para 32 MB cada (33.554.432 bytes).

1. Para modificar os parâmetros-limite, navegue no **separador Configs** do serviço Tez. Expanda o painel **geral** e localize os `tez.grouping.max-size` parâmetros e `tez.grouping.min-size` parâmetros.

1. Definir ambos os parâmetros para **33.554.432** bytes (32 MB).

    ![Tamanhos de agrupamento Apache Ambari Tez](./media/optimize-hive-ambari/apache-tez-grouping-size.png)

Estas alterações afetam todos os trabalhos do Tez em todo o servidor. Para obter um resultado ideal, escolha os valores de parâmetro apropriados.

## <a name="tune-reducers"></a>Redutores de afinação

Apache ORC e Snappy oferecem um alto desempenho. No entanto, a Colmeia pode ter poucos redutores por defeito, causando estrangulamentos.

Por exemplo, digamos que tem um tamanho de dados de entrada de 50 GB. Os dados em formato ORC com compressão Snappy são de 1 GB. A Colmeia estima o número de redutores necessários: (número de bytes input para mappers / `hive.exec.reducers.bytes.per.reducer` ).

Com as definições predefinidos, este exemplo é de quatro redutores.

O `hive.exec.reducers.bytes.per.reducer` parâmetro especifica o número de bytes processados por redutor. O valor predefinido é de 64 MB. Afinação deste valor aumenta o paralelismo e pode melhorar o desempenho. Afiná-lo demasiado baixo também poderia produzir demasiados redutores, potencialmente afetando o desempenho. Este parâmetro baseia-se nos seus requisitos específicos de dados, configurações de compressão e outros fatores ambientais.

1. Para modificar o parâmetro, navegue no separador Hive **Configs** e encontre o parâmetro **Dados por Redução** na página Definições.

    ![Dados Apache Ambari por Redutor](./media/optimize-hive-ambari/ambari-data-per-reducer.png)

1. **Selecione Editar** para modificar o valor para 128 MB (134.217.728 bytes) e, em seguida, prima **Enter** para guardar.

    ![Dados Ambari por Redutor - editado](./media/optimize-hive-ambari/data-per-reducer-edited.png)
  
    Dado um tamanho de entrada de 1.024 MB, com 128 MB de dados por redutor, existem oito redutores (1024/128).

1. Um valor incorreto para o parâmetro **Data per Reducer** pode resultar num grande número de redutores, afetando negativamente o desempenho da consulta. Para limitar o número máximo de redutores, definir `hive.exec.reducers.max` para um valor apropriado. O valor predefinido é 1009.

## <a name="enable-parallel-execution"></a>Permitir a execução paralela

Uma consulta de Colmeia é executada em uma ou mais fases. Se as etapas independentes puderem ser executadas em paralelo, isso aumentará o desempenho da consulta.

1. Para permitir a execução de consultas paralelas, navegue até ao separador Hive **Config** e procure a `hive.exec.parallel` propriedade. O valor predefinido é false. Altere o valor para verdadeiro e, em seguida, prima **Enter** para guardar o valor.

1. Para limitar o número de postos de trabalho a executar em paralelo, modifique o `hive.exec.parallel.thread.number` imóvel. O valor predefinido é 8.

    ![Exibição paralela do executivo da Colmeia Apache](./media/optimize-hive-ambari/apache-hive-exec-parallel.png)

## <a name="enable-vectorization"></a>Permitir a vectorização

A Colmeia processa os dados linha a linha. Vectorização direciona a Colmeia para processar dados em blocos de 1.024 linhas em vez de uma linha de cada vez. A vectorização só é aplicável ao formato de ficheiro ORC.

1. Para ativar uma execução de consulta vectorizada, navegue até ao separador Hive **Configs** e procure o `hive.vectorized.execution.enabled` parâmetro. O valor padrão é válido para a Hive 0.13.0 ou mais tarde.

1. Para permitir a execução vectorizada para o lado redutor da consulta, desaprote o `hive.vectorized.execution.reduce.enabled` parâmetro para o verdadeiro. O valor predefinido é false.

    ![Execução vetorizada da Colmeia Apache](./media/optimize-hive-ambari/hive-vectorized-execution.png)

## <a name="enable-cost-based-optimization-cbo"></a>Permitir a otimização baseada em custos (CBO)

Por padrão, a Hive segue um conjunto de regras para encontrar um plano de execução de consulta ideal. A otimização baseada em custos (CBO) avalia vários planos para executar uma consulta. E atribui um custo a cada plano, e depois determina o plano mais barato para executar uma consulta.

Para ativar o CBO, navegue nas definições **de Hive**  >  **Configs**  >  e encontre o **Optimizador Baseado em Custos,** em seguida, altere o botão de alternação para **On**.

![Otimizador baseado em custos HDInsight](./media/optimize-hive-ambari/hdinsight-cbo-config.png)

Os seguintes parâmetros de configuração adicionais aumentam o desempenho da consulta da Hive quando o CBO está ativado:

* `hive.compute.query.using.stats`

    Quando definida como verdadeira, a Hive utiliza estatísticas armazenadas na sua meta-loja para responder a perguntas simples como `count(*)` .

    ![Consulta de computação apache hive usando estatísticas](./media/optimize-hive-ambari/hive-compute-query-using-stats.png)

* `hive.stats.fetch.column.stats`

    As estatísticas das colunas são criadas quando o CBO está ativado. A Hive utiliza estatísticas de colunas, que são armazenadas na meta-loja, para otimizar consultas. A procura de estatísticas de colunas para cada coluna demora mais tempo quando o número de colunas é elevado. Quando definido como falso, esta definição desativa as estatísticas das colunas da metásta.

    ![Estatísticas de estatísticas da Colmeia Apache](./media/optimize-hive-ambari/hive-stats-fetch-column-stats.png)

* `hive.stats.fetch.partition.stats`

    As estatísticas básicas de partição, tais como o número de linhas, o tamanho dos dados e o tamanho do ficheiro são armazenadas na metastore. Se for verdade, as estatísticas de partição são recolhidas a partir de uma metastore. Quando falso, o tamanho do ficheiro é recolhido do sistema de ficheiros. E o número de filas é recolhido do esquema da linha.

    ![Estatísticas de colmeia definem estatísticas de partição](./media/optimize-hive-ambari/hive-stats-fetch-partition-stats.png)

## <a name="enable-intermediate-compression"></a>Ativar a compressão intermédia

As tarefas do mapa criam ficheiros intermédios que são utilizados pelas tarefas redutores. A compressão intermédia diminui o tamanho do ficheiro intermédio.

Os empregos de hadoop são geralmente engarrafamentos. A compressão de dados pode acelerar a E/S e a transferência global da rede.

Os tipos de compressão disponíveis são:

| Formato | Ferramenta | Algoritmo | Extensão de arquivo | Dividi-se? |
| --- | --- | --- | --- | --- |
| Rio Gzip | Rio Gzip | ESVAZIAR | `.gz` | No |
| Rio Bzip2 | Rio Bzip2 | Rio Bzip2 |`.bz2` | Yes |
| LZO | `Lzop` | LZO | `.lzo` | Sim, se indexado |
| Snappy | N/D | Snappy | Snappy | No |

Regra geral, ter o método de compressão splittable é importante, caso contrário poucos mappers serão criados. Se os dados de entrada forem texto, `bzip2` é a melhor opção. Para o formato ORC, snappy é a opção de compressão mais rápida.

1. Para ativar a compressão intermédia, navegue até ao separador Hive **Configs** e, em seguida, desfase o `hive.exec.compress.intermediate` parâmetro para o caso. O valor predefinido é false.

    !['Hive exec compress intermédio'](./media/optimize-hive-ambari/hive-exec-compress-intermediate.png)

    > [!NOTE]  
    > Para comprimir ficheiros intermédios, escolha um codec de compressão com um custo cpu mais baixo, mesmo que o codec não tenha uma saída de alta compressão.

1. Para definir o codec de compressão intermédia, adicione a propriedade personalizada `mapred.map.output.compression.codec` ao `hive-site.xml` ou `mapred-site.xml` arquivo.

1. Para adicionar uma definição personalizada:

    a. Navegue para **hive**  >  **Configs**  >    >  **Advanced Custom hive-site**.

    b. **Selecione Adicionar Propriedade...** na parte inferior do painel personalizado do site da colmeia.

    c. Na janela Add Property, insira `mapred.map.output.compression.codec` como chave e como `org.apache.hadoop.io.compress.SnappyCodec` valor.

    d. Selecione **Adicionar**.

    !['Apache Hive propriedade personalizada adicionar'](./media/optimize-hive-ambari/hive-custom-property.png)

    Esta definição comprima o ficheiro intermédio utilizando a compressão Snappy. Uma vez que a propriedade é adicionada, aparece no painel personalizado do local da colmeia.

    > [!NOTE]  
    > Este procedimento modifica o `$HADOOP_HOME/conf/hive-site.xml` ficheiro.

## <a name="compress-final-output"></a>Comprimir a saída final

A saída final da Colmeia também pode ser comprimida.

1. Para comprimir a saída final da Colmeia, navegue até ao separador Hive **Configs** e, em seguida, desafie o `hive.exec.compress.output` parâmetro para o verdadeiro. O valor predefinido é false.

1. Para escolher o codec de compressão de saída, adicione a `mapred.output.compression.codec` propriedade personalizada ao painel personalizado do site da colmeia, conforme descrito no passo 3 da secção anterior.

    ![Apache Hive propriedade personalizada adicionar2](./media/optimize-hive-ambari/hive-custom-property2.png)

## <a name="enable-speculative-execution"></a>Permitir a execução especulativa

A execução especulativa lança um certo número de tarefas duplicadas para detetar e negar a lista do rastreador de tarefas em marcha lenta. Ao mesmo tempo que melhora a execução global do trabalho, otimizando os resultados individuais da tarefa.

A execução especulativa não deve ser ligada para tarefas de MapReduce de longa duração com grandes quantidades de entrada.

* Para permitir a execução especulativa, navegue até ao separador Hive **Configs** e, em seguida, desfase o `hive.mapred.reduce.tasks.speculative.execution` parâmetro para o caso. O valor predefinido é false.

    !['Hive mapeado reduzir tarefas execução especulativa'](./media/optimize-hive-ambari/hive-mapred-reduce-tasks-speculative-execution.png)

## <a name="tune-dynamic-partitions"></a>Sintonize divisórias dinâmicas

A colmeia permite criar divisórias dinâmicas ao inserir registos numa tabela, sem predefinir todas as divisórias. Esta capacidade é uma característica poderosa. Embora possa resultar na criação de um grande número de divisórias. E um grande número de ficheiros para cada divisória.

1. Para a Colmeia fazer divisórias dinâmicas, o valor do `hive.exec.dynamic.partition` parâmetro deve ser verdadeiro (o padrão).

1. Mude o modo de partição dinâmica para *rigoroso*. Em modo rigoroso, pelo menos uma divisória tem de ser estática. Esta definição evita consultas sem o filtro de partição na cláusula WHERE, isto é, *rigorosamente* impede consultas que digitalizam todas as divisórias. Navegue até ao separador Hive **Configs** e, em seguida, desafie-o. `hive.exec.dynamic.partition.mode`  O valor **predefinido é não-esticado.**

1. Para limitar o número de divisórias dinâmicas a criar, modifique o `hive.exec.max.dynamic.partitions` parâmetro. O valor predefinido é 5000.

1. Para limitar o número total de divisórias dinâmicas por nó, modifique `hive.exec.max.dynamic.partitions.pernode` . O valor predefinido é 2000.

## <a name="enable-local-mode"></a>Ativar o modo local

O modo local permite à Hive fazer todas as tarefas de um trabalho numa única máquina. Ou às vezes num único processo. Esta definição melhora o desempenho da consulta se os dados de entrada forem pequenos. E a sobrecarga de lançamento de tarefas para consultas consome uma percentagem significativa da execução global de consultas.

Para ativar o modo local, adicione o `hive.exec.mode.local.auto` parâmetro ao painel personalizado do local da colmeia, conforme explicado no passo 3 da secção de [compressão intermédia Ativa.](#enable-intermediate-compression)

![Apache Hive modo animal local](./media/optimize-hive-ambari/hive-exec-mode-local-auto.png)

## <a name="set-single-mapreduce-multigroup-by"></a>Definir único MapReduce MultiGROUP BY

Quando esta propriedade é definida como verdadeira, uma consulta MultiGROUP BY com teclas comuns de grupo-by gera um único trabalho MapReduce.  

Para ativar este comportamento, adicione o `hive.multigroupby.singlereducer` parâmetro ao painel personalizado do local da colmeia, conforme explicado no passo 3 da secção de [compressão intermédia Enable.](#enable-intermediate-compression)

![Conjunto de colmeia único MapReduce MultiGROUP BY](./media/optimize-hive-ambari/hive-multigroupby-singlereducer.png)

## <a name="additional-hive-optimizations"></a>Otimizações adicionais da Colmeia

As secções seguintes descrevem otimizações adicionais relacionadas com a Colmeia que pode definir.

### <a name="join-optimizations"></a>Junte-se a otimizações

O tipo de junção padrão na Colmeia é uma *mistura de baralhar.* Na Hive, os mappers especiais lêem a entrada e emitem um par de chaves/valor de união a um ficheiro intermédio. Hadoop classifica e funde estes pares numa fase de baralhar. Este palco de baralhar é caro. Selecionar a junção certa com base nos seus dados pode melhorar significativamente o desempenho.

| Aderir tipo | Quando | Como | Configurações da colmeia | Comentários |
| --- | --- | --- | --- | --- |
| Shuffle Join | <ul><li>Escolha padrão</li><li>Sempre funciona</li></ul> | <ul><li>Leituras de parte de uma das mesas</li><li>Baldes e sorteios na chave de juntar</li><li>Envia um balde para cada reduzir</li><li>A junção é feita no lado reduzir</li></ul> | Não é necessária uma definição significativa de Colmeia | Funciona sempre |
| Aderir ao mapa | <ul><li>Uma mesa pode caber na memória</li></ul> | <ul><li>Lê pequena mesa na mesa de haxixe de memória</li><li>Flui através de parte do grande arquivo</li><li>Junta-se a cada disco da hash table</li><li>As juntas são só pelo mapper</li></ul> | `hive.auto.confvert.join=true` | Rápido, mas limitado |
| Sort Merge Bucket | Se ambas as mesas forem: <ul><li>Classificado o mesmo</li><li>Baldeu o mesmo</li><li>Juntando-se à coluna ordenada/balde</li></ul> | Cada processo: <ul><li>Lê um balde de cada mesa</li><li>Processa a linha com o valor mais baixo</li></ul> | `hive.auto.convert.sortmerge.join=true` | Eficiente |

### <a name="execution-engine-optimizations"></a>Otimização do motor de execução

Recomendações adicionais para otimizar o motor de execução da Colmeia:

| Definição | Recomendado | Padrão HDInsight |
| --- | --- | --- |
| `hive.mapjoin.hybridgrace.hashtable` | Verdadeiro = mais seguro, mais lento; falso = mais rápido | false |
| `tez.am.resource.memory.mb` | 4-GB limite superior para a maioria | Afinado automático |
| `tez.session.am.dag.submit.timeout.secs` | 300+ | 300 |
| `tez.am.container.idle.release-timeout-min.millis` | 20000+ | 10000 |
| `tez.am.container.idle.release-timeout-max.millis` | 40000+ | 20 000 |

## <a name="next-steps"></a>Próximos passos

* [Gerir clusters HDInsight com a Web UI Apache Ambari](hdinsight-hadoop-manage-ambari.md)
* [Apache Ambari REST API](hdinsight-hadoop-manage-ambari-rest-api.md)
* [Optimize Apache Hive queries in Azure HDInsight](./hdinsight-hadoop-optimize-hive-query.md) (Otimizar as consultas do Apache Hive no Azure HDInsight)
* [Otimizar clusters](./optimize-hive-ambari.md)
* [Otimizar Apache HBase](./optimize-hbase-ambari.md)
* [Otimizar Apache Pig](./optimize-pig-ambari.md)
