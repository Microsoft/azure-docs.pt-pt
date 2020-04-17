---
title: Apache Ambari para otimizar configurações de cluster - Azure HDInsight
description: Utilize a UI web Apache Ambari para configurar e otimizar os clusters Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 04/16/2020
ms.openlocfilehash: c88882175ff256300dee486e680a9b63e9a65c99
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81532537"
---
# <a name="use-apache-ambari-to-optimize-hdinsight-cluster-configurations"></a>Use Apache Ambari to optimize HDInsight cluster configurations (Utilizar o Apache Ambari para otimizar as configurações do cluster do HDInsight)

O HDInsight fornece clusters [Apache Hadoop](./hadoop/apache-hadoop-introduction.md) para aplicações de processamento de dados em larga escala. Gerir, monitorizar e otimizar estes complexos clusters multi-nó pode ser um desafio. [Apache Ambari](https://ambari.apache.org/) é uma interface web para gerir e monitorizar os clusters HDInsight Linux.  Para os clusters Windows, utilize a [Ambari REST API](hdinsight-hadoop-manage-ambari-rest-api.md).

Para uma introdução à utilização do Ambari Web UI, consulte [gerir os clusters HDInsight utilizando o Apache Ambari Web UI](hdinsight-hadoop-manage-ambari.md)

Faça login em `https://CLUSTERNAME.azurehdidnsight.net` Ambari com as suas credenciais de cluster. O ecrã inicial apresenta um painel de visão geral.

![Painel de instrumentos de utilizador Apache Ambari apresentado](./media/hdinsight-changing-configs-via-ambari/apache-ambari-dashboard.png)

O Ambari web UI é usado para gerir anfitriões, serviços, alertas, configurações e vistas. Ambari não pode ser usado para criar um cluster HDInsight, ou serviços de upgrade. Também não pode gerir pilhas e versões, desativar ou recomissar anfitriões, ou adicionar serviços ao cluster.

## <a name="manage-your-clusters-configuration"></a>Gerencie a configuração do seu cluster

As definições de configuração ajudam a afinar um determinado serviço. Para modificar as definições de configuração de um serviço, selecione o serviço a partir da barra lateral dos **Serviços** (à esquerda). Em seguida, navegue para o separador **Configs** na página de detalhe de serviço.

![Barra lateral dos Serviços Apache Ambari](./media/hdinsight-changing-configs-via-ambari/ambari-services-sidebar.png)

### <a name="modify-namenode-java-heap-size"></a>Modificar nomeNode tamanho monte java

O tamanho da pilha NameNode Java depende de muitos fatores, como a carga no cluster. Além disso, o número de ficheiros e os números de blocos. O tamanho padrão de 1 GB funciona bem com a maioria dos clusters, embora algumas cargas de trabalho possam requerer mais ou menos memória.

Para modificar o tamanho da pilha NameNode Java:

1. Selecione **HDFS** da barra lateral dos Serviços e navegue para o separador **Configs.**

    ![Configuração Apache Ambari HDFS](./media/hdinsight-changing-configs-via-ambari/ambari-apache-hdfs-config.png)

1. Encontre a definição **NomeNode Java heap size**. Também pode utilizar a caixa de texto do **filtro** para escrever e encontrar uma determinada definição. Selecione o ícone da **caneta** ao lado do nome de definição.

    ![Apache Ambari NameNode Java heap tamanho](./media/hdinsight-changing-configs-via-ambari/ambari-java-heap-size.png)

1. Digite o novo valor na caixa de texto e, em seguida, prima **Enter** para salvar a alteração.

    ![Ambari Editar NameNode Java heap size1](./media/hdinsight-changing-configs-via-ambari/java-heap-size-edit1.png)

1. O tamanho da pilha NameNode Java é alterado para 1 GB a partir de 2 GB.

    ![Nome editado Nome Node Java heap tamanho2](./media/hdinsight-changing-configs-via-ambari/java-heap-size-edited.png)

1. Guarde as suas alterações clicando no botão **Verde Guardar** na parte superior do ecrã de configuração.

    !['Apache Ambari salvar configurações'](./media/hdinsight-changing-configs-via-ambari/ambari-save-changes1.png)

## <a name="apache-hive-optimization"></a>Otimização da Colmeia Apache

As seguintes secções descrevem opções de configuração para otimizar o desempenho global da Apache Hive.

1. Para modificar os parâmetros de configuração da Hive, **selecione Hive** da barra lateral dos Serviços.
1. Navegue para o separador **Configs.**

### <a name="set-the-hive-execution-engine"></a>Desloque o motor de execução da Colmeia

A Hive fornece dois motores de execução: [Apache Hadoop MapReduce](https://hadoop.apache.org/docs/r1.2.1/mapred_tutorial.html) e [Apache TEZ](https://tez.apache.org/). Tez é mais rápido que MapReduce. Os clusters HDInsight Linux têm tez como motor de execução padrão. Para alterar o motor de execução:

1. No separador Hive **Configs,** digite o motor de **execução** na caixa de filtro.

    ![Motor de execução de pesquisa Apache Ambari](./media/hdinsight-changing-configs-via-ambari/ambari-search-execution.png)

1. O valor padrão da propriedade **de Otimização** é **Tez.**

    ![Otimização - Motor Apache Tez](./media/hdinsight-changing-configs-via-ambari/optimization-apache-tez.png)

### <a name="tune-mappers"></a>Mappers de melodia

Hadoop tenta dividir *(mapa)* um único ficheiro em vários ficheiros e processar os ficheiros resultantes em paralelo. O número de mappers depende do número de divisões. Os dois parâmetros de configuração seguintes conduzem o número de divisões para o motor de execução Tez:

* `tez.grouping.min-size`: Limite inferior ao tamanho de uma divisão agrunada, com um valor predefinido de 16 MB (16.777.216 bytes).
* `tez.grouping.max-size`: Limite superior do tamanho de uma divisão agrupada, com um valor predefinido de 1 GB (1.073.741.824 bytes).

Como orientação de desempenho, baixe ambos estes parâmetros para melhorar a latência, aumente para mais rendimento.

Por exemplo, para definir quatro tarefas mapper para um tamanho de dados de 128 MB, definiria ambos os parâmetros para 32 MB cada (33.554.432 bytes).

1. Para modificar os parâmetros de limite, navegue para o separador **Configs** do serviço Tez. Expanda **General** o painel geral `tez.grouping.max-size` `tez.grouping.min-size` e localize os e parâmetros.

1. Coloque ambos os parâmetros em **33.554.432** bytes (32 MB).

    ![Tamanhos de agrupamento Apache Ambari Tez](./media/hdinsight-changing-configs-via-ambari/apache-tez-grouping-size.png)

Estas mudanças afetam todos os empregos da Tez em todo o servidor. Para obter um resultado ideal, escolha os valores adequados do parâmetro.

### <a name="tune-reducers"></a>Redutores de melodia

[Apache ORC](https://orc.apache.org/) e [Snappy](https://google.github.io/snappy/) oferecem alto desempenho. No entanto, a Hive pode ter muito poucos redutores por padrão, causando estrangulamentos.

Por exemplo, diga que tem um tamanho de dados de entrada de 50 GB. Esses dados em formato ORC com compressão Snappy são de 1 GB. A Hive estima o número de redutores necessários como: (número `hive.exec.reducers.bytes.per.reducer`de bytes entradas para mappers / ).

Com as definições predefinidas, este exemplo são quatro redutores.

O `hive.exec.reducers.bytes.per.reducer` parâmetro especifica o número de bytes processados por redutor. O valor predefinido é de 64 MB. A sintonização deste valor aumenta o paralelismo e pode melhorar o desempenho. Ajustá-lo demasiado baixo também pode produzir demasiados redutores, afetando potencialmente o desempenho. Este parâmetro baseia-se nos seus requisitos específicos de dados, configurações de compressão e outros fatores ambientais.

1. Para modificar o parâmetro, navegue para o separador Hive **Configs** e encontre o parâmetro **Data per Reducer** na página Definições.

    ![Apache Ambari Data por Redutor](./media/hdinsight-changing-configs-via-ambari/ambari-data-per-reducer.png)

1. Selecione **Editar** para modificar o valor para 128 MB (134.217.728 bytes) e, em seguida, prima **Enter** para poupar.

    ![Ambari Data por Redutor - editado](./media/hdinsight-changing-configs-via-ambari/data-per-reducer-edited.png)
  
    Dado um tamanho de entrada de 1.024 MB, com 128 MB de dados por redutor, existem oito redutores (1024/128).

1. Um valor incorreto para o parâmetro **Data per Reducer** pode resultar num grande número de redutores, afetando negativamente o desempenho da consulta. Para limitar o número máximo de `hive.exec.reducers.max` redutores, fixado sumo a um valor adequado. O valor padrão é 1009.

### <a name="enable-parallel-execution"></a>Permitir a execução paralela

Uma consulta da Colmeia é executada em uma ou mais fases. Se as etapas independentes puderem ser executadas em paralelo, isso aumentará o desempenho da consulta.

1. Para permitir a execução de consulta paralela, navegue `hive.exec.parallel` até ao separador Hive **Config** e procure a propriedade. O valor predefinido é false. Mude o valor para verdadeiro e, em seguida, pressione **Enter** para salvar o valor.

1. Para limitar o número de postos de `hive.exec.parallel.thread.number` trabalho a funcionar em paralelo, modifique a propriedade. O valor padrão é 8.

    ![Exibição paralela do executivo da Colmeia Apache](./media/hdinsight-changing-configs-via-ambari/apache-hive-exec-parallel.png)

### <a name="enable-vectorization"></a>Ativar a vectorização

A colmeia processa dados de linha a linha. A vectorização direciona a Hive para processar dados em blocos de 1.024 linhas em vez de uma linha de cada vez. A vectorização só é aplicável ao formato de ficheiro ORC.

1. Para permitir uma execução de consulta vetorial, navegue até `hive.vectorized.execution.enabled` ao separador Hive **Configs** e procure o parâmetro. O valor predefinido é verdadeiro para a Colmeia 0.13.0 ou mais tarde.

1. Para permitir a execução vetorialpara o `hive.vectorized.execution.reduce.enabled` lado de redução da consulta, defina o parâmetro como verdadeiro. O valor predefinido é false.

    ![Execução vetorial apache hive](./media/hdinsight-changing-configs-via-ambari/hive-vectorized-execution.png)

### <a name="enable-cost-based-optimization-cbo"></a>Ativar a otimização baseada nos custos (CBO)

Por padrão, a Hive segue um conjunto de regras para encontrar um plano de execução de consulta ideal. A otimização baseada em custos (CBO) avalia vários planos para executar uma consulta. E atribui um custo a cada plano, e ntão determina o plano mais barato para executar uma consulta.

Para ativar o CBO, navegue para as**definições** da **Hive** > **Configs** > e encontre o **Optimizador baseado em custos de ativação**e, em seguida, mude o botão de alternância para **On**.

![Otimizador baseado em custos HDInsight](./media/hdinsight-changing-configs-via-ambari/hdinsight-cbo-config.png)

Os seguintes parâmetros de configuração adicionais aumentam o desempenho da consulta da Hive quando o CBO está ativado:

* `hive.compute.query.using.stats`

    Quando é verdade, a Hive utiliza estatísticas armazenadas na `count(*)`sua metaloja para responder a simples consultas como .

    ![Consulta computacional apache hive usando estatísticas](./media/hdinsight-changing-configs-via-ambari/hive-compute-query-using-stats.png)

* `hive.stats.fetch.column.stats`

    As estatísticas das colunas são criadas quando o CBO está ativado. A Hive utiliza estatísticas de colunas, que são armazenadas na metaloja, para otimizar consultas. Buscar estatísticas de colunas para cada coluna demora mais tempo quando o número de colunas é elevado. Quando definido como falso, esta definição desativa as estatísticas das colunas da metaloja.

    ![Estatísticas da Colmeia Apache definir estatísticas de colunas](./media/hdinsight-changing-configs-via-ambari/hive-stats-fetch-column-stats.png)

* `hive.stats.fetch.partition.stats`

    Estatísticas básicas de partição, tais como o número de linhas, tamanho de dados e tamanho de ficheiro são armazenadas na metaloja. Se for em realidade, as estatísticas de partição são recolhidas na metaloja. Quando falso, o tamanho do ficheiro é recolhido do sistema de ficheiros. E o número de filas é recolhido do esquema da fila.

    ![Estatísticas da colmeia definir estatísticas de partição](./media/hdinsight-changing-configs-via-ambari/hive-stats-fetch-partition-stats.png)

### <a name="enable-intermediate-compression"></a>Ativar a compressão intermédia

As tarefas do mapa criam ficheiros intermédios que são utilizados pelas tarefas de redução. A compressão intermédia diminui o tamanho do ficheiro intermédio.

Os empregos de hadoop são geralmente engarrafados. Os dados comprimidos podem acelerar a transferência geral de E/S e a transferência global da rede.

Os tipos de compressão disponíveis são:

| Formato | Ferramenta | Algoritmo | Extensão de ficheiros | Mesa dividida? |
| --- | --- | --- | --- | --- |
| Gzip | Gzip | ESVAZIAR | `.gz` | Não |
| Bzip2 | Bzip2 | Bzip2 |`.bz2` | Sim |
| LZO | `Lzop` | LZO | `.lzo` | Sim, se indexado |
| Estaladiço | N/D | Estaladiço | Estaladiço | Não |

Regra geral, ter o método de compressão splittable é importante, caso contrário poucos mappers serão criados. Se os dados de `bzip2` entrada forem texto, é a melhor opção. Para o formato ORC, o Snappy é a opção de compressão mais rápida.

1. Para permitir a compressão intermédia, navegue até ao `hive.exec.compress.intermediate` separador Hive **Configs** e, em seguida, coloque o parâmetro verdadeiro. O valor predefinido é false.

    !['Hive exec compress intermediário'](./media/hdinsight-changing-configs-via-ambari/hive-exec-compress-intermediate.png)

    > [!NOTE]  
    > Para comprimir ficheiros intermédios, escolha um código de compressão com um custo de CPU mais baixo, mesmo que o codec não tenha uma saída de alta compressão.

1. Para definir o código de compressão `mapred.map.output.compression.codec` intermédio, `hive-site.xml` `mapred-site.xml` adicione a propriedade personalizada ao ou ficheiro.

1. Para adicionar uma definição personalizada:

    a. Navegue para **hive** > **Configs** > **avançado** > local de**colmeia.**

    b. **Selecione Adicionar Propriedade...** na parte inferior do painel personalizado do site da colmeia.

    c. Na janela Adicionar Propriedade, `mapred.map.output.compression.codec` introduza `org.apache.hadoop.io.compress.SnappyCodec` como chave e como valor.

    d. Selecione **Adicionar**.

    !['Apache Hive personalizado adicionar'](./media/hdinsight-changing-configs-via-ambari/hive-custom-property.png)

    Esta definição comprime o ficheiro intermédio utilizando a compressão Snappy. Uma vez adicionada a propriedade, aparece no painel personalizado do site da colmeia.

    > [!NOTE]  
    > Este procedimento modifica `$HADOOP_HOME/conf/hive-site.xml` o ficheiro.

### <a name="compress-final-output"></a>Comprimir a saída final

A saída final da Colmeia também pode ser comprimida.

1. Para comprimir a saída final da Colmeia, navegue para o `hive.exec.compress.output` separador Hive **Configs** e, em seguida, coloque o parâmetro verdadeiro. O valor predefinido é false.

1. Para escolher o codec de `mapred.output.compression.codec` compressão de saída, adicione a propriedade personalizada ao painel personalizado do site da colmeia, conforme descrito no passo 3 da secção anterior.

    ![Apache Hive personalizado adicionar2](./media/hdinsight-changing-configs-via-ambari/hive-custom-property2.png)

### <a name="enable-speculative-execution"></a>Permitir a execução especulativa

A execução especulativa lança um certo número de tarefas duplicadas para detetar e negar a lista do rastreador de tarefas de execução lenta. Ao mesmo tempo que melhora a execução global do trabalho, otimizando os resultados individuais da tarefa.

A execução especulativa não deve ser ligada para tarefas mapeadas de longa duração Com grandes quantidades de entrada.

* Para permitir a execução especulativa, navegue para o `hive.mapred.reduce.tasks.speculative.execution` separador Hive **Configs** e, em seguida, coloque o parâmetro verdadeiro. O valor predefinido é false.

    !["Colmeia mapeada reduz em tarefas de execução especulativa"](./media/hdinsight-changing-configs-via-ambari/hive-mapred-reduce-tasks-speculative-execution.png)

### <a name="tune-dynamic-partitions"></a>Afinar divisórias dinâmicas

A Hive permite criar divisórias dinâmicas ao inserir registos numa mesa, sem pré-definir todas as divisórias. Esta habilidade é uma característica poderosa. Embora possa resultar na criação de um grande número de divisórias. E um grande número de ficheiros para cada partição.

1. Para que a Hive faça `hive.exec.dynamic.partition` divisórias dinâmicas, o valor do parâmetro deve ser verdadeiro (o padrão).

1. Mude o modo dinâmico de partição para *rigoroso*. Em modo rigoroso, pelo menos uma partição tem de ser estática. Esta definição impede consultas sem o filtro de partição na cláusula WHERE, isto é, *impede rigorosamente* as consultas que digitalizam todas as divisórias. Navegue para o separador Hive `hive.exec.dynamic.partition.mode` **Configs** e, em seguida, desloque-se para **rigoroso**. O valor predefinido não é **restrito**.

1. Para limitar o número de divisórias `hive.exec.max.dynamic.partitions` dinâmicas a criar, modifique o parâmetro. O valor padrão é 5000.

1. Para limitar o número total de divisórias dinâmicas por nó, modifique `hive.exec.max.dynamic.partitions.pernode`. O valor padrão é 2000.

### <a name="enable-local-mode"></a>Ativar o modo local

O modo local permite que a Hive faça todas as tarefas de um trabalho numa única máquina. Ou às vezes num único processo. Esta definição melhora o desempenho da consulta se os dados de entrada forem pequenos. E a sobrecarga das tarefas de lançamento de consultas consome uma percentagem significativa da execução total da consulta.

Para ativar o `hive.exec.mode.local.auto` modo local, adicione o parâmetro ao painel personalizado do sítio da colmeia, conforme explicado no passo 3 da secção de [compressão intermédia Enable.](#enable-intermediate-compression)

![Apache Hive exec modo automóvel local](./media/hdinsight-changing-configs-via-ambari/hive-exec-mode-local-auto.png)

### <a name="set-single-mapreduce-multigroup-by"></a>Definir mapa únicoReduzir MultiGRUPO POR

Quando esta propriedade está definida para ser verdadeira, uma consulta multigrupo BY com teclas comuns de grupo gera um único trabalho MapReduce.  

Para ativar este comportamento, adicione o `hive.multigroupby.singlereducer` parâmetro ao painel personalizado do sítio da colmeia, como explicado no passo 3 da secção de [compressão intermédia Enable.](#enable-intermediate-compression)

![Conjunto de colmeia único MapReduce MultiGROUP BY](./media/hdinsight-changing-configs-via-ambari/hive-multigroupby-singlereducer.png)

### <a name="additional-hive-optimizations"></a>Otimizações adicionais da Colmeia

As seguintes secções descrevem otimizações adicionais relacionadas com a Colmeia que pode definir.

#### <a name="join-optimizations"></a>Junte-se a otimizações

O tipo de união padrão na Colmeia é uma *mistura de shuffle*. Na Colmeia, os mapeadores especiais lêem a entrada e emitem um par chave/valor de juntar-se a um ficheiro intermédio. Hadoop classifica e funde estes pares numa fase de baralhar. Esta fase de baralhar é cara. A seleção da união certa com base nos seus dados pode melhorar significativamente o desempenho.

| Tipo de adesão | Quando | Como | Configurações da colmeia | Comentários |
| --- | --- | --- | --- | --- |
| Shuffle Join | <ul><li>Escolha por defeito</li><li>Sempre funciona</li></ul> | <ul><li>Lê de parte de uma das mesas</li><li>Baldes e tipos na chave Juntar</li><li>Envia um balde para cada redução</li><li>A adesão é feita no lado da Redução</li></ul> | Não é necessária uma definição significativa da Colmeia | Funciona sempre |
| Reunião do mapa | <ul><li>Uma mesa pode caber na memória</li></ul> | <ul><li>Lê pequena tabela na mesa de hash de memória</li><li>Streams através de parte do grande arquivo</li><li>Junta-se a cada disco da mesa hash</li><li>As juntas são só pelo mapper</li></ul> | `hive.auto.confvert.join=true` | Rápido, mas limitado |
| Ordenar balde de fusão | Se ambas as mesas forem: <ul><li>Ordenado o mesmo</li><li>Baldedo da mesma forma</li><li>Juntando-se à coluna ordenada/balde</li></ul> | Cada processo: <ul><li>Lê um balde de cada mesa</li><li>Processa a linha com o valor mais baixo</li></ul> | `hive.auto.convert.sortmerge.join=true` | Eficiente |

#### <a name="execution-engine-optimizations"></a>Otimizações do motor de execução

Recomendações adicionais para otimizar o motor de execução da Colmeia:

| Definição | Recomendado | Padrão HDInsight |
| --- | --- | --- |
| `hive.mapjoin.hybridgrace.hashtable` | Verdade = mais seguro, mais lento; falso = mais rápido | false |
| `tez.am.resource.memory.mb` | 4-GB superior bound para a maioria | Auto-Sintonizado |
| `tez.session.am.dag.submit.timeout.secs` | 300+ | 300 |
| `tez.am.container.idle.release-timeout-min.millis` | 20000+ | 10000 |
| `tez.am.container.idle.release-timeout-max.millis` | 40000+ | 20 000 |

## <a name="apache-pig-optimization"></a>Otimização do Porco Apache

As propriedades do [Apache Pig](https://pig.apache.org/) podem ser modificadas a partir da UI web ambari para afinar consultas de porco. Modificar as propriedades do porco de Ambari modifica `/etc/pig/2.4.2.0-258.0/pig.properties` diretamente as propriedades do Porco no ficheiro.

1. Para modificar as propriedades do porco, navegue até ao separador Pig **Configs** e, em seguida, expanda o painel **avançado de propriedades de porco.**

1. Encontre, não comente e altere o valor do imóvel que pretende modificar.

1. Selecione **Guardar** no lado superior direito da janela para economizar o novo valor. Algumas propriedades podem necessitar de um reinício do serviço.

    ![Propriedades avançadas de porco apache](./media/hdinsight-changing-configs-via-ambari/advanced-pig-properties.png)

> [!NOTE]  
> Quaisquer configurações de nível `pig.properties` de sessão sobrepõem os valores de propriedade no ficheiro.

### <a name="tune-execution-engine"></a>Motor de execução de melodia

Dois motores de execução estão disponíveis para executar scripts de porco: MapReduce e Tez. Tez é um motor otimizado e é muito mais rápido que mapReduce.

1. Para modificar o motor de execução, no painel `exectype`avançado de propriedades de **porco,** encontre a propriedade .

1. O valor padrão é **MapReduce**. Mude para **Tez.**

### <a name="enable-local-mode"></a>Ativar o modo local

Semelhante à Hive, o modo local é usado para acelerar os trabalhos com quantidades relativamente menores de dados.

1. Para ativar o `pig.auto.local.enabled` modo local, ajuste-se a **verdade.** O valor predefinido é false.

1. Os empregos com uma dimensão de dados de entrada inferior ao valor da `pig.auto.local.input.maxbytes` propriedade são considerados pequenos empregos. O valor padrão é de 1 GB.

### <a name="copy-user-jar-cache"></a>Copiar cache de jarro de utilizador

O porco copia os ficheiros JAR exigidos pelos UDFs a uma cache distribuída para os disponibilizar para os nódosos de tarefa. Estes frascos não mudam com frequência. Se ativada, `pig.user.cache.enabled` a definição permite que os frascos sejam colocados numa cache para os reutilizar em trabalhos geridos pelo mesmo utilizador. Esta definição resulta num pequeno aumento do desempenho do trabalho.

1. Para permitir, `pig.user.cache.enabled` definido para verdade. O padrão é falso.

1. Para definir o caminho base dos frascos em cache, coloque-o `pig.user.cache.location` no caminho base. A predefinição é `/tmp`.

### <a name="optimize-performance-with-memory-settings"></a>Otimizar o desempenho com definições de memória

As seguintes definições de memória podem ajudar a otimizar o desempenho do script do Porco.

* `pig.cachedbag.memusage`: A quantidade de memória dada a um saco. Um saco é uma coleção de tuples. Um tuple é um conjunto ordenado de campos, e um campo é um pedaço de dados. Se os dados de um saco estão para além da memória dada, é derramado para o disco. O valor padrão é de 0,2, o que representa 20% da memória disponível. Esta memória é partilhada em todos os sacos numa aplicação.

* `pig.spill.size.threshold`: Os sacos maiores do que este limiar de tamanho de derrame (in bytes) são derramados em disco. O valor padrão é de 5 MB.

### <a name="compress-temporary-files"></a>Comprimir ficheiros temporários

Porco gera ficheiros temporários durante a execução do emprego. A compressão dos ficheiros temporários resulta num aumento de desempenho ao ler ou escrever ficheiros para disco. As seguintes definições podem ser utilizadas para comprimir ficheiros temporários.

* `pig.tmpfilecompression`: Quando for verdade, permite a compressão temporária dos ficheiros. O valor predefinido é false.

* `pig.tmpfilecompression.codec`: O códice de compressão a utilizar para comprimir os ficheiros temporários. Os códigos de compressão recomendados são [LZO](https://www.oberhumer.com/opensource/lzo/) e Snappy para uma utilização mais baixa do CPU.

### <a name="enable-split-combining"></a>Ativar a combinação de divisão

Quando ativados, pequenos ficheiros são combinados para menos tarefas de mapa. Esta definição melhora a eficiência dos postos de trabalho com muitos pequenos ficheiros. Para permitir, `pig.noSplitCombination` definido para verdade. O valor predefinido é false.

### <a name="tune-mappers"></a>Mappers de melodia

O número de mappers é controlado `pig.maxCombinedSplitSize`modificando a propriedade. Esta propriedade especifica o tamanho dos dados a serem processados por uma única tarefa de mapa. O valor predefinido é o tamanho do bloco padrão do sistema de ficheiros. Aumentar este valor resulta num número mais baixo de tarefas mapeadas.

### <a name="tune-reducers"></a>Redutores de melodia

O número de redutores é calculado `pig.exec.reducers.bytes.per.reducer`com base no parâmetro . O parâmetro especifica o número de bytes processados por redutor, por predefinição de 1 GB. Para limitar o número máximo de `pig.exec.reducers.max` redutores, defino a propriedade, por padrão 999.

## <a name="apache-hbase-optimization-with-the-ambari-web-ui"></a>Otimização Apache HBase com a UI web ambari

A configuração [Apache HBase](https://hbase.apache.org/) é modificada a partir do separador **HBase Configs.** As seguintes secções descrevem algumas das definições de configuração importantes que afetam o desempenho da HBase.

### <a name="set-hbase_heapsize"></a>Definir HBASE_HEAPSIZE

O tamanho da pilha HBase especifica a quantidade máxima de pilha a ser usada em megabytes por *região* e servidores *principais.* O valor padrão é de 1.000 MB. Este valor deve ser ajustado para a carga de trabalho do cluster.

1. Para modificar, navegue para o painel **Avançado HBase-env** no separador `HBASE_HEAPSIZE` HBase **Configs** e, em seguida, encontre a definição.

1. Alterar o valor padrão para 5.000 MB.

    !['Apache Ambari HBase heapsize'](./media/hdinsight-changing-configs-via-ambari/ambari-hbase-heapsize.png)

### <a name="optimize-read-heavy-workloads"></a>Otimizar cargas de trabalho pesadas

As seguintes configurações são importantes para melhorar o desempenho das cargas de trabalho pesadas.

#### <a name="block-cache-size"></a>Tamanho da cache do bloco

A cache do bloco é a cache de leitura. O seu tamanho `hfile.block.cache.size` é controlado pelo parâmetro. O valor padrão é de 0,4, que é 40 por cento da memória total do servidor da região. Quanto maior for o tamanho da cache do bloco, mais rápido será a leitura aleatória.

1. Para modificar este parâmetro, navegue para o separador **Definições** no separador HBase **Configs** e, em seguida, localize **% do RegionalServer Alocado para ler tampão**.

    ![Tamanho do bloco de cache do bloco de memória Apache HBase](./media/hdinsight-changing-configs-via-ambari/hbase-block-cache-size.png)

1. Para alterar o valor, selecione o ícone **Editar.**

#### <a name="memstore-size"></a>Tamanho da loja

Todas as edições são armazenadas no amortecedor de memória, chamado *Memstore*. Este tampão aumenta a quantidade total de dados que podem ser escritos em disco numa única operação. Também acelera o acesso às edições recentes. O tamanho da Memstore é definido pelos dois parâmetros seguintes:

* `hbase.regionserver.global.memstore.UpperLimit`: Define a percentagem máxima do servidor da região que a Memstore combinada pode utilizar.

* `hbase.regionserver.global.memstore.LowerLimit`: Define a percentagem mínima do servidor da região que a Memstore combinada pode utilizar.

Para otimizar para leituras aleatórias, pode reduzir os limites superiores e inferiores da Memstore.

#### <a name="number-of-rows-fetched-when-scanning-from-disk"></a>Número de linhas recolhidas ao digitalizar do disco

A `hbase.client.scanner.caching` definição define o número de linhas `next` lidas a partir do disco quando o método é chamado num scanner.  O valor predefinido é 100. Quanto maior for o número, menos chamadas remotas feitas do cliente para o servidor da região, resultando em exames mais rápidos. No entanto, esta definição também aumentará a pressão de memória sobre o cliente.

![Número de linhas Apache HBase recolhidas](./media/hdinsight-changing-configs-via-ambari/hbase-num-rows-fetched.png)

> [!IMPORTANT]  
> Não detete o valor de tal forma que o tempo entre a invocação do próximo método num scanner seja maior do que o tempo de tempo do scanner. A duração do tempo `hbase.regionserver.lease.period` limite do scanner é definida pela propriedade.

### <a name="optimize-write-heavy-workloads"></a>Otimizar cargas de trabalho pesadas

As seguintes configurações são importantes para melhorar o desempenho de cargas de trabalho pesadas.

#### <a name="maximum-region-file-size"></a>Tamanho máximo de arquivo da região

A HBase armazena dados num formato interno de ficheiros, chamado *HFile*. A `hbase.hregion.max.filesize` propriedade define o tamanho de um único HFile para uma região.  Uma região é dividida em duas regiões se a soma de todos os HFiles numa região for maior do que este cenário.

!['Apache HBase HRegion max filesize'](./media/hdinsight-changing-configs-via-ambari/hbase-hregion-max-filesize.png)

Quanto maior o tamanho dos ficheiros da região, menor o número de divisões. Pode aumentar o tamanho do ficheiro para determinar um valor que resulte no desempenho máximo da escrita.

#### <a name="avoid-update-blocking"></a>Evite o bloqueio de atualizações

* A `hbase.hregion.memstore.flush.size` propriedade define o tamanho a que a Memstore é lavada ao disco. O tamanho padrão é de 128 MB.

* O multiplicador de blocos `hbase.hregion.memstore.block.multiplier`da região HBase é definido por . O valor predefinido é 4. O máximo permitido é 8.

* HBase bloqueia atualizações se a`hbase.hregion.memstore.flush.size` * `hbase.hregion.memstore.block.multiplier`Memstore for () bytes.

    Com os valores predefinidos do tamanho do flush e do multiplicador de blocos, as atualizações são bloqueadas quando a Memstore tem 128 * 4 = 512 MB de tamanho. Para reduzir a contagem de bloqueio `hbase.hregion.memstore.block.multiplier`de atualização, aumente o valor de .

![Multiplicador de bloco de região De Apache HBase](./media/hdinsight-changing-configs-via-ambari/hbase-hregion-memstore-block-multiplier.png)

### <a name="define-memstore-size"></a>Definir tamanho Memstore

O tamanho da loja `hbase.regionserver.global.memstore.UpperLimit` é `hbase.regionserver.global.memstore.LowerLimit` definido pelos parâmetros e parâmetros. A definição destes valores iguais entre si reduz as pausas durante as escritas (também causando uma descarga mais frequente) e resulta num aumento do desempenho da escrita.

### <a name="set-memstore-local-allocation-buffer"></a>Definir tampão de alocação local Memstore

A utilização do tampão de `hbase.hregion.memstore.mslab.enabled`alocação local Memstore é determinada pela propriedade. Quando ativado (verdadeiro), esta definição impede a fragmentação da pilha durante o funcionamento da escrita pesada. O valor predefinido é true.

![hbase.hregion.memstore.mslab.habilitado](./media/hdinsight-changing-configs-via-ambari/hbase-hregion-memstore-mslab-enabled.png)

## <a name="next-steps"></a>Passos seguintes

* [Gerir clusters HDInsight com a UI web Apache Ambari](hdinsight-hadoop-manage-ambari.md)
* [Apache Ambari REST API](hdinsight-hadoop-manage-ambari-rest-api.md)
