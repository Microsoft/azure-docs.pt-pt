---
title: Apache Ambari para otimizar as configurações de cluster – Azure HDInsight
description: Use a interface do usuário da Web do Apache amAmbari para configurar e otimizar clusters do Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/15/2019
ms.openlocfilehash: 15a2c75a7619a815655be0fd9fd3044d86acd057
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/17/2019
ms.locfileid: "74150112"
---
# <a name="use-apache-ambari-to-optimize-hdinsight-cluster-configurations"></a>Usar o Apache Ambari para otimizar as configurações do cluster HDInsight

O HDInsight fornece clusters [Apache Hadoop](https://hadoop.apache.org/) para aplicativos de processamento de dados em larga escala. O gerenciamento, o monitoramento e a otimização desses clusters complexos de vários nós podem ser desafiadores. O [Apache Ambari](https://ambari.apache.org/) é uma interface da Web para gerenciar e monitorar clusters HDInsight do Linux.  Para clusters do Windows, use a [API REST do amAmbari](hdinsight-hadoop-manage-ambari-rest-api.md).

Para obter uma introdução ao uso da interface do usuário da Web do amAmbari, consulte [gerenciar clusters HDInsight usando a interface do usuário da Web do Apache Ambari](hdinsight-hadoop-manage-ambari.md)

Faça logon no Ambari em `https://CLUSTERNAME.azurehdidnsight.net` com suas credenciais de cluster. A tela inicial exibe um painel de visão geral.

![Painel do usuário do Apache Ambari exibido](./media/hdinsight-changing-configs-via-ambari/apache-ambari-dashboard.png)

A interface do usuário da Web do amAmbari pode ser usada para gerenciar hosts, serviços, alertas, configurações e exibições. Ambari não pode ser usado para criar um cluster HDInsight, atualizar serviços, gerenciar pilhas e versões, encerrar ou recomissionar hosts ou adicionar serviços ao cluster.

## <a name="manage-your-clusters-configuration"></a>Gerenciar a configuração do cluster

As definições de configuração ajudam a ajustar um serviço específico. Para modificar as definições de configuração de um serviço, selecione o serviço na barra lateral **Serviços** (à esquerda) e, em seguida, navegue até a guia **configurações** na página de detalhes do serviço.

![Barra lateral dos serviços Apache Ambari](./media/hdinsight-changing-configs-via-ambari/ambari-services-sidebar.png)

### <a name="modify-namenode-java-heap-size"></a>Modificar o tamanho do heap do NameNode Java

O tamanho do heap do NameNode Java depende de muitos fatores, como a carga no cluster, os números de arquivos e os números de blocos. O tamanho padrão de 1 GB funciona bem com a maioria dos clusters, embora algumas cargas de trabalho possam exigir mais ou menos memória.

Para modificar o tamanho do heap do Java NameNode:

1. Selecione **HDFS** na barra lateral serviços e navegue até a guia **configurações** .

    ![Configuração do Ambari HDFS do Apache](./media/hdinsight-changing-configs-via-ambari/ambari-apache-hdfs-config.png)

1. Localize a configuração **NameNode o tamanho do heap do Java**. Você também pode usar a caixa de texto de **filtro** para digitar e encontrar uma configuração específica. Selecione o ícone de **caneta** ao lado do nome da configuração.

    ![Tamanho do heap de Java do Apache Ambari NameNode](./media/hdinsight-changing-configs-via-ambari/ambari-java-heap-size.png)

1. Digite o novo valor na caixa de texto e pressione **Enter** para salvar a alteração.

    ![Ambari editar heap NameNode Java Size1](./media/hdinsight-changing-configs-via-ambari/java-heap-size-edit1.png)

1. O tamanho do heap do Java NameNode é alterado para 1 GB de 2 GB.

    ![Size2 de heap do NameNode editado](./media/hdinsight-changing-configs-via-ambari/java-heap-size-edited.png)

1. Salve as alterações clicando no botão verde **salvar** na parte superior da tela de configuração.

    ![Ambari Ambari salvar configurações](./media/hdinsight-changing-configs-via-ambari/ambari-save-changes1.png)

## <a name="apache-hive-optimization"></a>Otimização de Apache Hive

As seções a seguir descrevem as opções de configuração para otimizar o desempenho geral de Apache Hive.

1. Para modificar parâmetros de configuração do hive, selecione **Hive** na barra lateral serviços.
1. Navegue até a guia **configurações** .

### <a name="set-the-hive-execution-engine"></a>Definir o mecanismo de execução do hive

O hive fornece dois mecanismos de execução: [Apache Hadoop MapReduce](https://hadoop.apache.org/docs/r1.2.1/mapred_tutorial.html) e o [Apache tez](https://tez.apache.org/). O tez é mais rápido do que o MapReduce. Os clusters Linux do HDInsight têm tez como o mecanismo de execução padrão. Para alterar o mecanismo de execução:

1. Na guia **configurações** do hive, digite **mecanismo de execução** na caixa filtro.

    ![Mecanismo de execução de pesquisa do Apache Ambari](./media/hdinsight-changing-configs-via-ambari/ambari-search-execution.png)

1. O valor padrão da propriedade **Optimization** é **tez**.

    ![Otimização-mecanismo de Apache Tez](./media/hdinsight-changing-configs-via-ambari/optimization-apache-tez.png)

### <a name="tune-mappers"></a>Ajustar Mapeadores

O Hadoop tenta dividir (*mapear*) um único arquivo em vários arquivos e processar os arquivos resultantes em paralelo. O número de Mapeadores depende do número de divisões. Os dois parâmetros de configuração a seguir orientam o número de divisões para o mecanismo de execução tez:

* `tez.grouping.min-size`: limite inferior do tamanho de uma divisão agrupada, com um valor padrão de 16 MB (16.777.216 bytes).
* `tez.grouping.max-size`: limite superior do tamanho de uma divisão agrupada, com um valor padrão de 1 GB (1.073.741.824 bytes).

Como regra de desempenho geral, diminua esses dois parâmetros para melhorar a latência, aumentar para obter mais taxa de transferência.

Por exemplo, para definir quatro tarefas de mapeador para um tamanho de dados de 128 MB, você definiria ambos os parâmetros como 32 MB cada (33.554.432 bytes).

1. Para modificar os parâmetros de limite, navegue até a guia **configurações** do serviço tez. Expanda o painel **geral** e localize os parâmetros `tez.grouping.max-size` e `tez.grouping.min-size`.

1. Defina os dois parâmetros como **33.554.432** bytes (32 MB).

    ![Tamanhos de agrupamento do Apache Ambari tez](./media/hdinsight-changing-configs-via-ambari/apache-tez-grouping-size.png)

Essas alterações afetam todos os trabalhos do tez no servidor. Para obter um resultado ideal, escolha os valores de parâmetro apropriados.

### <a name="tune-reducers"></a>Ajustar redutores

O [Apache Orc](https://orc.apache.org/) e o [snapshot](https://google.github.io/snappy/) oferecem alto desempenho. No entanto, o hive pode ter poucos redutores por padrão, causando afunilamentos.

Por exemplo, digamos que você tenha um tamanho de dados de entrada de 50 GB. Esses dados no formato ORC com compactação de instantâneo são 1 GB. O hive estima o número de redutores necessário como: (número de bytes de entrada para Mapeadores/`hive.exec.reducers.bytes.per.reducer`).

Com as configurações padrão, este exemplo é 4 redutores.

O parâmetro `hive.exec.reducers.bytes.per.reducer` especifica o número de bytes processados por redutor. O valor padrão é 64 MB. Ajustar esse valor diminui o paralelismo e pode melhorar o desempenho. O ajuste muito baixo também pode produzir muitos redutores, o que pode afetar o desempenho de forma negativa. Esse parâmetro se baseia em seus requisitos de dados específicos, configurações de compactação e outros fatores ambientais.

1. Para modificar o parâmetro, navegue até a guia Configurações do hive e localize os **dados por parâmetro redutor** na página **de configuração.**

    ![Dados do Apache Ambari por redutor](./media/hdinsight-changing-configs-via-ambari/ambari-data-per-reducer.png)

1. Selecione **Editar** para modificar o valor de 128 MB (134.217.728 bytes) e pressione **Enter** para salvar.

    ![Dados de Ambari por redutor-editado](./media/hdinsight-changing-configs-via-ambari/data-per-reducer-edited.png)
  
    Dado um tamanho de entrada de 1.024 MB, com 128 MB de dados por redutor, há 8 redutores (1024/128).

1. Um valor incorreto para os **dados por parâmetro redutor** pode resultar em um grande número de redutores, o que afeta negativamente o desempenho da consulta. Para limitar o número máximo de redutores, defina `hive.exec.reducers.max` como um valor apropriado. O valor padrão é 1009.

### <a name="enable-parallel-execution"></a>Habilitar execução paralela

Uma consulta do hive é executada em um ou mais estágios. Se as etapas independentes puderem ser executadas em paralelo, isso aumentará o desempenho da consulta.

1. Para habilitar a execução de consulta paralela, navegue até a guia **configuração** do hive e procure a propriedade `hive.exec.parallel`. O valor predefinido é false. Altere o valor para true e, em seguida, pressione **Enter** para salvar o valor.

1. Para limitar o número de trabalhos a serem executados em paralelo, modifique a propriedade `hive.exec.parallel.thread.number`. O valor padrão é 8.

    ![Exibição paralela Apache Hive exec](./media/hdinsight-changing-configs-via-ambari/apache-hive-exec-parallel.png)

### <a name="enable-vectorization"></a>Habilitar vetorização

O hive processa dados linha por linha. A vetorização direciona o hive para processar dados em blocos de 1.024 linhas em vez de uma linha por vez. A vetorização é aplicável somente ao formato de arquivo ORC.

1. Para habilitar uma execução de consulta vetorizada, navegue até a guia **configurações** do hive e procure o parâmetro `hive.vectorized.execution.enabled`. O valor padrão é true para o hive 0.13.0 ou posterior.

1. Para habilitar a execução vetorizada para o lado de redução da consulta, defina o parâmetro `hive.vectorized.execution.reduce.enabled` como true. O valor predefinido é false.

    ![Apache Hive execução vetorizada](./media/hdinsight-changing-configs-via-ambari/hive-vectorized-execution.png)

### <a name="enable-cost-based-optimization-cbo"></a>Habilitar a otimização baseada em custo (CBO)

Por padrão, o hive segue um conjunto de regras para encontrar um plano de execução de consulta ideal. A otimização baseada em custo (CBO) avalia vários planos para executar uma consulta e atribui um custo a cada plano e, em seguida, determina o plano mais barato para executar uma consulta.

Para habilitar o CBO, navegue até **Hive** > **configs** > **configurações** e localize **habilitar otimizador baseado em custo**e, em seguida, alterne o botão de alternância para **ativado**.

![Otimizador baseado em custo do HDInsight](./media/hdinsight-changing-configs-via-ambari/hdinsight-cbo-config.png)

Os parâmetros de configuração adicionais a seguir aumentam o desempenho de consulta do hive quando o CBO está habilitado:

* `hive.compute.query.using.stats`

    Quando definido como true, o hive usa estatísticas armazenadas em seu metastore para responder a consultas simples, como `count(*)`.

    ![Apache Hive consulta de computação usando estatísticas](./media/hdinsight-changing-configs-via-ambari/hive-compute-query-using-stats.png)

* `hive.stats.fetch.column.stats`

    As estatísticas de coluna são criadas quando o CBO está habilitado. O hive usa estatísticas de coluna, que são armazenadas no metastore, para otimizar consultas. A busca de estatísticas de coluna para cada coluna leva mais tempo quando o número de colunas é alto. Quando definido como false, essa configuração desabilita a busca de estatísticas de coluna do metastore.

    ![Estatísticas de Apache Hive definir estatística de coluna](./media/hdinsight-changing-configs-via-ambari/hive-stats-fetch-column-stats.png)

* `hive.stats.fetch.partition.stats`

    As estatísticas básicas de partição, como o número de linhas, o tamanho dos dados e o tamanho do arquivo, são armazenadas no metastore. Quando definido como true, as estatísticas de partição são buscadas do metastore. Quando for falso, o tamanho do arquivo será obtido do sistema de arquivos e o número de linhas será buscado do esquema de linha.

    ![Estatísticas do hive definem estatísticas de partição](./media/hdinsight-changing-configs-via-ambari/hive-stats-fetch-partition-stats.png)

### <a name="enable-intermediate-compression"></a>Habilitar compactação intermediária

Tarefas de mapeamento criam arquivos intermediários que são usados pelas tarefas redutor. A compactação intermediária reduz o tamanho do arquivo intermediário.

Os trabalhos do Hadoop geralmente são com gargalos de e/s. A compactação de dados pode acelerar a e/s e a transferência de rede geral.

Os tipos de compactação disponíveis são:

| Formato | Ferramenta | Algoritmo | Extensão de arquivo | Divisíveis? |
| -- | -- | -- | -- | -- |
| Gzip | Gzip | DEFLATE | .gz | Não |
| Bzip2 | Bzip2 | Bzip2 |.bz2 | Sim |
| LZO | Lzop | LZO | .lzo | Sim, se indexado |
| Snappy | N/D | Snappy | Snappy | Não |

Como regra geral, ter o método de compactação divisões é importante, caso contrário, poucos Mapeadores serão criados. Se os dados de entrada forem texto, `bzip2` será a melhor opção. Para o formato ORC, o instantâneo é a opção de compactação mais rápida.

1. Para habilitar a compactação intermediária, navegue até a guia **configurações** do hive e, em seguida, defina o parâmetro `hive.exec.compress.intermediate` como true. O valor predefinido é false.

    ![Do hive exec compactar intermediário](./media/hdinsight-changing-configs-via-ambari/hive-exec-compress-intermediate.png)

    > [!NOTE]  
    > Para compactar arquivos intermediários, escolha um codec de compactação com menor custo de CPU, mesmo que o codec não tenha uma saída de alta compactação.

1. Para definir o codec de compactação intermediária, adicione a propriedade personalizada `mapred.map.output.compression.codec` ao `hive-site.xml` ou `mapred-site.xml` arquivo.

1. Para adicionar uma configuração personalizada:

    a. Navegue até **hive** > **configurações** > **avançado** > **Hive personalizado-site**.

    b. Selecione **Adicionar Propriedade...** na parte inferior do painel Hive-site personalizado.

    c. Na janela Adicionar Propriedade, insira `mapred.map.output.compression.codec` como a chave e `org.apache.hadoop.io.compress.SnappyCodec` como o valor.

    d. Selecione **Adicionar**.

    ![Adicionar Apache Hive propriedade personalizada](./media/hdinsight-changing-configs-via-ambari/hive-custom-property.png)

    Isso compactará o arquivo intermediário usando a compactação de instantâneo. Depois que a propriedade for adicionada, ela aparecerá no painel Hive-site personalizado.

    > [!NOTE]  
    > Esse procedimento modifica o arquivo de `$HADOOP_HOME/conf/hive-site.xml`.

### <a name="compress-final-output"></a>Compactar saída final

A saída final do hive também pode ser compactada.

1. Para compactar a saída final do hive, navegue até a guia **configurações** do hive e, em seguida, defina o parâmetro `hive.exec.compress.output` como true. O valor predefinido é false.

1. Para escolher o codec de compactação de saída, adicione a propriedade personalizada `mapred.output.compression.codec` ao painel Hive-site personalizado, conforme descrito na etapa 3 da seção anterior.

    ![Apache Hive propriedade personalizada ADD2](./media/hdinsight-changing-configs-via-ambari/hive-custom-property2.png)

### <a name="enable-speculative-execution"></a>Habilitar a execução especulativa

A execução especulativa inicia um determinado número de tarefas duplicadas para detectar e adicionar à lista de bloqueios o rastreador de tarefas de execução lenta, ao mesmo tempo em que melhora a execução geral do trabalho, otimizando os resultados de tarefas individuais.

A execução especulativa não deve ser ativada para tarefas MapReduce de execução longa com grandes quantidades de entrada.

* Para habilitar a execução especulativa, navegue até a guia **configurações** do hive e, em seguida, defina o parâmetro `hive.mapred.reduce.tasks.speculative.execution` como true. O valor predefinido é false.

    ![Execução especulativa de tarefas de redução de mapred do hive](./media/hdinsight-changing-configs-via-ambari/hive-mapred-reduce-tasks-speculative-execution.png)

### <a name="tune-dynamic-partitions"></a>Ajustar partições dinâmicas

O hive permite a criação de partições dinâmicas ao inserir registros em uma tabela, sem predefinir cada partição. Esse é um recurso poderoso, embora possa resultar na criação de um grande número de partições e em um grande número de arquivos para cada partição.

1. Para que o hive faça partições dinâmicas, o valor do parâmetro `hive.exec.dynamic.partition` deve ser true (o padrão).

1. Altere o modo de partição dinâmica para *estrito*. No modo estrito, pelo menos uma partição deve ser estática. Isso impede consultas sem o filtro de partição na cláusula WHERE, ou seja, *Strict* impede consultas que verificam todas as partições. Navegue até a guia **configurações** do hive e defina `hive.exec.dynamic.partition.mode` como **estrito**. O valor padrão é não **estrito**.

1. Para limitar o número de partições dinâmicas a serem criadas, modifique o parâmetro `hive.exec.max.dynamic.partitions`. O valor padrão é 5000.

1. Para limitar o número total de partições dinâmicas por nó, modifique `hive.exec.max.dynamic.partitions.pernode`. O valor padrão é 2000.

### <a name="enable-local-mode"></a>Habilitar modo local

O modo local permite que o hive execute todas as tarefas de um trabalho em um único computador ou, às vezes, em um único processo. Isso melhora o desempenho da consulta se os dados de entrada forem pequenos e a sobrecarga de iniciar tarefas para consultas consumir uma porcentagem significativa da execução geral da consulta.

Para habilitar o modo local, adicione o parâmetro `hive.exec.mode.local.auto` ao painel Hive-site personalizado, conforme explicado na etapa 3 da seção [Habilitar compactação intermediária](#enable-intermediate-compression) .

![Modo Apache Hive exec local automático](./media/hdinsight-changing-configs-via-ambari/hive-exec-mode-local-auto.png)

### <a name="set-single-mapreduce-multigroup-by"></a>Definir um único MapReduce Multigrupo por

Quando essa propriedade é definida como true, uma consulta de multigroup BY com chaves Group-by comuns gera um único trabalho MapReduce.  

Para habilitar esse comportamento, adicione o parâmetro `hive.multigroupby.singlereducer` ao painel Hive-site personalizado, conforme explicado na etapa 3 da seção [Habilitar compactação intermediária](#enable-intermediate-compression) .

![O hive definiu um único MapReduce Multigrupo por](./media/hdinsight-changing-configs-via-ambari/hive-multigroupby-singlereducer.png)

### <a name="additional-hive-optimizations"></a>Otimizações adicionais do hive

As seções a seguir descrevem otimizações adicionais relacionadas a Hive que você pode definir.

#### <a name="join-optimizations"></a>Otimizações de junção

O tipo de junção padrão no hive é uma *junção em ordem aleatória*. No hive, os Mapeadores especiais lêem a entrada e emitem um par de chave/valor de junção para um arquivo intermediário. O Hadoop classifica e mescla esses pares em um estágio de ordem aleatória. Esse estágio de ordem aleatória é caro. Selecionar a junção correta com base em seus dados pode melhorar significativamente o desempenho.

| Tipo de junção | Ao | Qual | Configurações do hive | Comentários |
| -- | -- | -- | -- | -- |
| Junção em ordem aleatória | <ul><li>Opção padrão</li><li>Sempre funciona</li></ul> | <ul><li>Lê de parte de uma das tabelas</li><li>Buckets e classificações na chave de junção</li><li>Envia um Bucket para cada redução</li><li>A junção é feita no lado da redução</li></ul> | Nenhuma configuração de Hive significativa necessária | Funciona toda vez |
| Junção de mapa | <ul><li>Uma tabela pode caber na memória</li></ul> | <ul><li>Lê a tabela pequena na tabela de hash de memória</li><li>Fluxos por parte do arquivo grande</li><li>Une cada registro da tabela de hash</li><li>Junções são apenas pelo mapeador</li></ul> | `hive.auto.confvert.join=true` | Muito rápido, mas limitado |
| Classificar Bucket de mesclagem | Se ambas as tabelas forem: <ul><li>Classificados da mesma</li><li>O mesmo Bucket</li><li>Unindo na coluna classificada/empilhada</li></ul> | Cada processo: <ul><li>Lê um Bucket de cada tabela</li><li>Processa a linha com o valor mais baixo</li></ul> | `hive.auto.convert.sortmerge.join=true` | Muito eficiente |

#### <a name="execution-engine-optimizations"></a>Otimizações do mecanismo de execução

Recomendações adicionais para otimizar o mecanismo de execução do hive:

| Definição | Recomendado | Padrão do HDInsight |
| -- | -- | -- |
| `hive.mapjoin.hybridgrace.hashtable` | True = mais seguro, mais lento; false = mais rápido | false |
| `tez.am.resource.memory.mb` | limite superior de 4 GB para a maioria | Ajustado automaticamente |
| `tez.session.am.dag.submit.timeout.secs` | 300+ | 300 |
| `tez.am.container.idle.release-timeout-min.millis` | 20000+ | 10000 |
| `tez.am.container.idle.release-timeout-max.millis` | 40000+ | 20000 |

## <a name="apache-pig-optimization"></a>Otimização do Apache Pig

As propriedades do [Apache Pig](https://pig.apache.org/) podem ser modificadas na interface do usuário da Web do amAmbari para ajustar as consultas do Pig. Modificar as propriedades Pig de Ambari modifica diretamente as propriedades Pig no arquivo `/etc/pig/2.4.2.0-258.0/pig.properties`.

1. Para modificar as propriedades de Pig, navegue até a guia **configurações** do Pig e, em seguida, expanda o painel **Pig-Propriedades avançadas** .

1. Localize, remova a marca de comentário e altere o valor da propriedade que você deseja modificar.

1. Selecione **salvar** no lado superior direito da janela para salvar o novo valor. Algumas propriedades podem exigir uma reinicialização do serviço.

    ![Propriedades avançadas do Apache Pig](./media/hdinsight-changing-configs-via-ambari/advanced-pig-properties.png)

> [!NOTE]  
> As configurações de nível de sessão substituem os valores de propriedade no arquivo `pig.properties`.

### <a name="tune-execution-engine"></a>Ajustar mecanismo de execução

Dois mecanismos de execução estão disponíveis para executar scripts do Pig: MapReduce e tez. O tez é um mecanismo otimizado e é muito mais rápido do que o MapReduce.

1. Para modificar o mecanismo de execução, no painel **Propriedades avançadas de Pig** , localize a propriedade `exectype`.

1. O valor padrão é **MapReduce**. Altere-o para **tez**.

### <a name="enable-local-mode"></a>Habilitar modo local

Semelhante ao Hive, o modo local é usado para acelerar trabalhos com quantidades relativamente menores de dados.

1. Para habilitar o modo local, defina `pig.auto.local.enabled` como **true**. O valor predefinido é false.

1. Trabalhos com um tamanho de dados de entrada menor que o valor da propriedade `pig.auto.local.input.maxbytes` são considerados trabalhos pequenos. O valor padrão é 1 GB.

### <a name="copy-user-jar-cache"></a>Copiar cache jar do usuário

Pig copia os arquivos JAR exigidos por UDFs para um cache distribuído para disponibilizá-los para nós de tarefa. Esses jars não são alterados com frequência. Se habilitada, a configuração `pig.user.cache.enabled` permite que os jars sejam colocados em um cache para reutilizá-los para trabalhos executados pelo mesmo usuário. Isso resulta em um aumento secundário no desempenho do trabalho.

1. Para habilitar, defina `pig.user.cache.enabled` como true. O padrão é false.

1. Para definir o caminho base dos Jars armazenados em cache, defina `pig.user.cache.location` como o caminho base. A predefinição é `/tmp`.

### <a name="optimize-performance-with-memory-settings"></a>Otimizar o desempenho com as configurações de memória

As configurações de memória a seguir podem ajudar a otimizar o desempenho do script Pig.

* `pig.cachedbag.memusage`: a quantidade de memória alocada a um recipiente. Um recipiente é uma coleção de tuplas. Uma tupla é um conjunto ordenado de campos e um campo é um dado. Se os dados em um recipiente estiverem além da memória alocada, eles serão despejados no disco. O valor padrão é 0,2, que representa 20% da memória disponível. Essa memória é compartilhada entre todas as bolsas em um aplicativo.

* `pig.spill.size.threshold`: os pacotes maiores que esse limite de tamanho de despejo (em bytes) são despejados no disco. O valor padrão é 5 MB.

### <a name="compress-temporary-files"></a>Compactar arquivos temporários

O Pig gera arquivos temporários durante a execução do trabalho. A compactação dos arquivos temporários resulta em um aumento de desempenho ao ler ou gravar arquivos no disco. As configurações a seguir podem ser usadas para compactar arquivos temporários.

* `pig.tmpfilecompression`: Quando true, habilita a compactação de arquivo temporário. O valor predefinido é false.

* `pig.tmpfilecompression.codec`: o codec de compressão a ser usado para compactar os arquivos temporários. Os codecs de compactação recomendados são [LZO](https://www.oberhumer.com/opensource/lzo/) e encaixados para reduzir a utilização da CPU.

### <a name="enable-split-combining"></a>Habilitar combinação de divisão

Quando habilitado, os arquivos pequenos são combinados para menos tarefas de mapeamento. Isso melhora a eficiência dos trabalhos com muitos arquivos pequenos. Para habilitar, defina `pig.noSplitCombination` como true. O valor predefinido é false.

### <a name="tune-mappers"></a>Ajustar Mapeadores

O número de Mapeadores é controlado pela modificação da propriedade `pig.maxCombinedSplitSize`. Isso especifica o tamanho dos dados a serem processados por uma única tarefa de mapa. O valor padrão é o tamanho do bloco padrão do sistema de arquivos. Aumentar esse valor resulta em uma diminuição do número de tarefas do mapeador.

### <a name="tune-reducers"></a>Ajustar redutores

O número de redutores é calculado com base no parâmetro `pig.exec.reducers.bytes.per.reducer`. O parâmetro especifica o número de bytes processados por redutor, por padrão, 1 GB. Para limitar o número máximo de redutores, defina a propriedade `pig.exec.reducers.max`, por padrão, 999.

## <a name="apache-hbase-optimization-with-the-ambari-web-ui"></a>Otimização do Apache HBase com a interface do usuário da Web do amAmbari

A configuração do [Apache HBase](https://hbase.apache.org/) é modificada na guia **configurações do HBase** . As seções a seguir descrevem algumas das definições de configuração importantes que afetam o desempenho do HBase.

### <a name="set-hbase_heapsize"></a>Definir HBASE_HEAPSIZE

O tamanho do heap do HBase especifica a quantidade máxima de heap a ser usada em megabytes por *região* e servidores *mestres* . O valor padrão é 1.000 MB. Isso deve ser ajustado para a carga de trabalho do cluster.

1. Para modificar, navegue até o painel **HBase-env avançado** na guia **configurações** do HBase e localize a configuração `HBASE_HEAPSIZE`.

1. Altere o valor padrão para 5.000 MB.

    ![Memória heapsize do Apache Ambari HBase](./media/hdinsight-changing-configs-via-ambari/ambari-hbase-heapsize.png)

### <a name="optimize-read-heavy-workloads"></a>Otimizar cargas de trabalho com uso intenso de leitura

As configurações a seguir são importantes para melhorar o desempenho de cargas de trabalho com uso intenso de leitura.

#### <a name="block-cache-size"></a>Tamanho do cache de blocos

O cache de blocos é o cache de leitura. Seu tamanho é controlado pelo parâmetro `hfile.block.cache.size`. O valor padrão é 0,4, que é 40% da memória total do servidor de região. Quanto maior o tamanho do cache do bloco, mais rápida será as leituras aleatórias.

1. Para modificar esse parâmetro, navegue até a guia **configurações** na guia **configuração** do HBase e localize **% de RegionServer alocados para buffers de leitura**.

    ![Tamanho do cache do bloco de memória do Apache HBase](./media/hdinsight-changing-configs-via-ambari/hbase-block-cache-size.png)

1. Para alterar o valor, selecione o ícone **Editar** .

#### <a name="memstore-size"></a>Tamanho do Memstore

Todas as edições são armazenadas no buffer de memória, chamado de *Memstore*. Isso aumenta a quantidade total de dados que podem ser gravados em disco em uma única operação e acelera o acesso subsequente às edições recentes. O tamanho de Memstore é definido pelos dois parâmetros a seguir:

* `hbase.regionserver.global.memstore.UpperLimit`: define o percentual máximo do servidor de região que o Memstore combinado pode usar.

* `hbase.regionserver.global.memstore.LowerLimit`: define o percentual mínimo do servidor de região que o Memstore combinado pode usar.

Para otimizar as leituras aleatórias, você pode reduzir os limites superiores e inferiores do Memstore.

#### <a name="number-of-rows-fetched-when-scanning-from-disk"></a>Número de linhas buscadas durante a verificação do disco

A configuração `hbase.client.scanner.caching` define o número de linhas lidas do disco quando o método `next` é chamado em um scanner.  O valor predefinido é 100. Quanto maior o número, menos as chamadas remotas feitas do cliente para o servidor de região, resultando em verificações mais rápidas. No entanto, isso também aumentará a pressão de memória no cliente.

![Número de linhas do Apache HBase buscadas](./media/hdinsight-changing-configs-via-ambari/hbase-num-rows-fetched.png)

> [!IMPORTANT]  
> Não defina o valor de modo que o tempo entre a invocação do próximo método em um scanner seja maior do que o tempo limite do scanner. A duração do tempo limite do scanner é definida pela propriedade `hbase.regionserver.lease.period`.

### <a name="optimize-write-heavy-workloads"></a>Otimizar cargas de trabalho pesadas de gravação

As configurações a seguir são importantes para melhorar o desempenho de cargas de trabalho com gravação pesada.

#### <a name="maximum-region-file-size"></a>Tamanho máximo do arquivo de região

O HBase armazena dados em um formato de arquivo interno, chamado *HFile*. A propriedade `hbase.hregion.max.filesize` define o tamanho de um único HFile para uma região.  Uma região será dividida em duas regiões se a soma de todos os HFiles em uma região for maior que essa configuração.

![Tamanho máximo do Apache HBase HRegion](./media/hdinsight-changing-configs-via-ambari/hbase-hregion-max-filesize.png)

Quanto maior o tamanho do arquivo de região, menor o número de divisões. Você pode aumentar o tamanho do arquivo para determinar um valor que resulta no desempenho máximo de gravação.

#### <a name="avoid-update-blocking"></a>Evitar bloqueio de atualização

* A propriedade `hbase.hregion.memstore.flush.size` define o tamanho no qual o Memstore é liberado para o disco. O tamanho padrão é 128 MB.

* O multiplicador de bloco de região do HBase é definido por `hbase.hregion.memstore.block.multiplier`. O valor predefinido é 4. O máximo permitido é 8.

* O HBase bloqueará atualizações se o Memstore for (`hbase.hregion.memstore.flush.size` * `hbase.hregion.memstore.block.multiplier`) bytes.

    Com os valores padrão de tamanho de liberação e multiplicador de bloco, as atualizações são bloqueadas quando Memstore é 128 * 4 = 512 MB de tamanho. Para reduzir a contagem de bloqueios de atualização, aumente o valor de `hbase.hregion.memstore.block.multiplier`.

![Multiplicador de bloco de região do Apache HBase](./media/hdinsight-changing-configs-via-ambari/hbase-hregion-memstore-block-multiplier.png)

### <a name="define-memstore-size"></a>Definir tamanho do Memstore

O tamanho de Memstore é definido pelos parâmetros `hbase.regionserver.global.memstore.UpperLimit` e `hbase.regionserver.global.memstore.LowerLimit`. Definir esses valores como iguais uns aos outros reduz as pausas durante as gravações (também causando liberação mais frequente) e resulta em um desempenho de gravação maior.

### <a name="set-memstore-local-allocation-buffer"></a>Definir o buffer de alocação local do Memstore

O uso do buffer de alocação local do Memstore é determinado pela propriedade `hbase.hregion.memstore.mslab.enabled`. Quando habilitado (true), isso impede a fragmentação de heap durante uma operação de gravação pesada. O valor predefinido é verdadeiro.

![hbase.hregion.memstore.mslab.enabled](./media/hdinsight-changing-configs-via-ambari/hbase-hregion-memstore-mslab-enabled.png)

## <a name="next-steps"></a>Passos seguintes

* [Gerenciar clusters HDInsight com a interface do usuário da Web do Apache amAmbari](hdinsight-hadoop-manage-ambari.md)
* [API REST do Apache Ambari](hdinsight-hadoop-manage-ambari-rest-api.md)
