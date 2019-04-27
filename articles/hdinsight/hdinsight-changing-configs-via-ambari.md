---
title: Otimizar as configurações de cluster com o Apache Ambari - Azure HDInsight
description: Utilize a IU web do Apache Ambari para configurar e otimizar clusters do HDInsight.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 03/26/2019
ms.author: hrasheed
ms.openlocfilehash: f0db36fa380d0d1bb7f2b581c4bf8fa1abfaadaf
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60698993"
---
# <a name="use-apache-ambari-to-optimize-hdinsight-cluster-configurations"></a>Utilizar o Apache Ambari para otimizar as configurações de cluster do HDInsight

HDInsight fornece [Apache Hadoop](https://hadoop.apache.org/) clusters para aplicações de processamento de dados em grande escala. Gestão, monitorização e otimizar esses clusters com vários nós complexos podem ser um desafio. [Apache Ambari](https://ambari.apache.org/) é uma interface web para gerir e monitorizar clusters do Linux de HDInsight.  Para os clusters do Windows, utilize o [API REST do Ambari](hdinsight-hadoop-manage-ambari-rest-api.md).

Para uma introdução ao utilizar a interface do Usuário da Web de Ambari, consulte [HDInsight gerir clusters com a IU do Apache Ambari Web](hdinsight-hadoop-manage-ambari.md)

Inicie sessão no Ambari em `https://CLUSTERNAME.azurehdidnsight.net` com as suas credenciais de cluster. A tela inicial exibe um dashboard de descrição geral.

![Dashboard do Ambari](./media/hdinsight-changing-configs-via-ambari/ambari-dashboard.png)

A IU web do Ambari pode ser utilizada para gerir anfitriões, serviços, alertas, configurações e vistas. Não é possível utilizar Ambari para criar um cluster do HDInsight, serviços de atualização, gerir pilhas e versões, desativar ou recommission anfitriões ou adicionar serviços ao cluster.

## <a name="manage-your-clusters-configuration"></a>Gerir a configuração do seu cluster

Definições de configuração ajudam a otimizar um serviço específico. Para modificar as definições de configuração de um serviço, selecione o serviço a partir da **serviços** barra lateral (no lado esquerdo) e, em seguida, navegue para o **configurações** separador na página de detalhes do serviço.

![Serviços de barra lateral](./media/hdinsight-changing-configs-via-ambari/services-sidebar.png)

### <a name="modify-namenode-java-heap-size"></a>Modificar o tamanho do heap NameNode Java

O tamanho da pilha NameNode Java depende de vários fatores, como a carga no cluster, o número de ficheiros e os números de blocos. O tamanho predefinido de 1 GB funciona bem com a maior parte dos clusters, embora algumas cargas de trabalho podem exigir mais ou menos memória. 

Para modificar o tamanho da pilha NameNode Java:

1. Selecione **HDFS** na barra lateral serviços e navegue para o **configurações** separador.

    ![Configuração do HDFS](./media/hdinsight-changing-configs-via-ambari/hdfs-config.png)

1. Encontrar a definição **tamanho de heap de NameNode Java**. Também pode utilizar o **filtro** caixa de texto digitar e encontrar uma definição específica. Selecione o **caneta** ícone ao lado do nome de definição.

    ![Tamanho do heap NameNode Java](./media/hdinsight-changing-configs-via-ambari/java-heap-size.png)

1. Escreva o novo valor na caixa de texto e, em seguida, prima **Enter** para guardar a alteração.

    ![Editar o tamanho do heap NameNode Java](./media/hdinsight-changing-configs-via-ambari/java-heap-size-edit.png)

1. O tamanho da pilha NameNode Java é alterado para 1 GB de 2 GB.

    ![Editar o tamanho do heap NameNode Java](./media/hdinsight-changing-configs-via-ambari/java-heap-size-edited.png)

1. Guardar as alterações ao clicar no verde **guardar** botão na parte superior do ecrã de configuração.

    ![Guardar alterações](./media/hdinsight-changing-configs-via-ambari/save-changes.png)

## <a name="apache-hive-optimization"></a>Otimização do Apache Hive

As secções seguintes descrevem as opções de configuração para otimizar o desempenho geral do Apache Hive.

1. Para modificar os parâmetros de configuração do Hive, selecione **Hive** na barra lateral serviços.
1. Navegue para o **configurações** separador.

### <a name="set-the-hive-execution-engine"></a>Defina o motor de execução do Hive

Hive fornece dois mecanismos de execução: [Apache Hadoop MapReduce](https://hadoop.apache.org/docs/r1.2.1/mapred_tutorial.html) e [Apache TEZ](https://tez.apache.org/). Tez é mais rápido que MapReduce. Os clusters do Linux de HDInsight tem Tez como motor de execução padrão. Para alterar o motor de execução:

1. Na colmeia **configurações** separador, escreva **motor de execução** na caixa Filtro.

    ![Motor de execução de pesquisa](./media/hdinsight-changing-configs-via-ambari/search-execution.png)

1. O **otimização** valor de predefinido da propriedade é **Tez**.

    ![Otimização - Tez](./media/hdinsight-changing-configs-via-ambari/optimization-tez.png)

### <a name="tune-mappers"></a>Otimizar mapeadores

Hadoop tenta dividir (*mapa*) um único arquivo em vários ficheiros e o processo resultante ficheiros em paralelo. O número de mapeadores depende do número de divisões de. Os seguintes parâmetros de configuração de dois unidade o número de divisões para o motor de execução Tez:

* `tez.grouping.min-size`: Limite inferior ao tamanho de uma divisão de agrupados, com um valor padrão de 16 MB (16,777,216 bytes).
* `tez.grouping.max-size`: Limite superior no tamanho de uma divisão de agrupados, com um valor predefinido de 1 GB (1,073,741,824 bytes).

Como uma desempenho regra prática, diminua a ambos os parâmetros para melhorar a latência, aumentar para obter mais débito.

Por exemplo, para definir quatro tarefas de mapeador de pontos para um tamanho de dados de 128 MB, definiria ambos os parâmetros para 32 MB cada (bytes 33,554,432).

1. Para modificar os parâmetros de limite, navegue para o **configurações** Guia do Tez serviço. Expanda a **gerais** painel e localize a `tez.grouping.max-size` e `tez.grouping.min-size` parâmetros.

1. Definir ambos os parâmetros **33,554,432** bytes (32 MB).

    ![Tamanhos de agrupamento do Tez](./media/hdinsight-changing-configs-via-ambari/tez-grouping-size.png)
 
Essas alterações afetam todas as tarefas do Tez todo o servidor. Para obter um resultado ideal, introduza os valores adequados.

### <a name="tune-reducers"></a>Otimizar redutores

[Apache ORC](https://orc.apache.org/) e [Snappy](https://google.github.io/snappy/) ambos oferecem de alto desempenho. No entanto, ramo de registo pode ter poucos redutores por predefinição, causar afunilamentos.

Por exemplo, digamos que tem um tamanho de dados de entrada de 50 GB. Que dados em ORC formatar com compressão quanto é 1 GB. O número de redutores necessários como as estimativas de ramo de registo: (o número de entrada de bytes para mapeadores / `hive.exec.reducers.bytes.per.reducer`).

Com as configurações padrão, esse exemplo é 4 redutores.

O `hive.exec.reducers.bytes.per.reducer` parâmetro especifica o número de bytes processados por reducer. O valor predefinido é de 64 MB. Este valor de otimização para baixo aumenta o paralelismo e pode melhorar o desempenho. Ajuste demasiado baixo também poderia produzir redutores demasiados, potencialmente prejudicando o desempenho. Este parâmetro se baseia em seus requisitos de dados em particular, as definições de compressão e outros fatores ambientais.

1. Para modificar o parâmetro, navegue para a colmeia **configurações** separador e encontre a **dados por Reducer** parâmetro na página Definições.

    ![Dados por Reducer](./media/hdinsight-changing-configs-via-ambari/data-per-reducer.png)
 
1. Selecione **edite** para modificar o valor de 128 MB (134,217,728 bytes) e, em seguida, prima **Enter** para guardar.

    ![Dados por Reducer - editado](./media/hdinsight-changing-configs-via-ambari/data-per-reducer-edited.png)
  
    Tendo em conta um tamanho de entrada de 1.024 MB, com 128 MB de dados por reducer, existem 8 redutores (1024/128).

1. Um valor incorreto para o **dados por Reducer** parâmetro pode resultar num grande número de redutores, afetar negativamente o desempenho da consulta. Para limitar o número máximo de redutores, defina `hive.exec.reducers.max` para um valor adequado. O valor predefinido é 1009.

### <a name="enable-parallel-execution"></a>Ativar a execução paralela

Uma consulta do Hive for executada nas fases de um ou mais. Se as fases independentes podem ser executadas em paralelo, que irá aumentar o desempenho da consulta.

1.  Para ativar a execução paralela da consulta, navegue para a colmeia **Config** separador e procure o `hive.exec.parallel` propriedade. O valor predefinido é false. Altere o valor como true e, em seguida, prima **Enter** para guardar o valor.
 
1.  Para limitar o número de tarefas para executar em paralelo, modifique o `hive.exec.parallel.thread.number` propriedade. O valor predefinido é 8.

    ![Hive paralelo exec](./media/hdinsight-changing-configs-via-ambari/hive-exec-parallel.png)


### <a name="enable-vectorization"></a>Ativar a vetorização

Hive processa dados linha por linha. Vetorização direciona o Hive para processar dados em blocos de 1.024 linhas em vez de uma linha por vez. Vetorização só é aplicável para o formato de ficheiro ORC.

1. Para ativar uma execução de consulta de rato vetorizado, navegue para a colmeia **configurações** separador e procure o `hive.vectorized.execution.enabled` parâmetro. O valor predefinido é verdadeiro para o Hive 0.13.0 ou posterior.
 
1. Para ativar a execução de rato vetorizada para o lado da redução da consulta, defina o `hive.vectorized.execution.reduce.enabled` parâmetro como true. O valor predefinido é false.

    ![Execução do Hive vetorizada](./media/hdinsight-changing-configs-via-ambari/hive-vectorized-execution.png)

### <a name="enable-cost-based-optimization-cbo"></a>Ativar a otimização baseada no custo (CBO)

Por predefinição, o Hive segue um conjunto de regras para localizar um plano de execução de consulta ideal. Com base no custo otimização (CBO) avalia a vários esquemas para executar uma consulta e atribui um custo a cada plano, e determina o plano mais barato executar uma consulta.

Para ativar CBO, navegue para a colmeia **configurações** separador e procure `parameter hive.cbo.enable`, em seguida, mude o botão de alternância para **no**.

![Configuração CBO](./media/hdinsight-changing-configs-via-ambari/cbo.png)

Os seguintes parâmetros de configuração adicionais aumentam o desempenho das consultas do Hive quando CBO está ativado:

* `hive.compute.query.using.stats`

    Quando definido como true, ramo de registo utiliza estatísticas armazenadas em seu metastore para responder a consultas simples como `count(*)`.

    ![Estatísticas CBO](./media/hdinsight-changing-configs-via-ambari/hive-compute-query-using-stats.png)

* `hive.stats.fetch.column.stats`

    Estatísticas de coluna são criadas quando CBO está ativada. Ramo de registo utiliza as estatísticas de coluna, que são armazenadas na metastore, para otimizar as consultas. A obter estatísticas de coluna para cada coluna demora mais tempo quando o número de colunas é elevado. Quando definido como false, esta definição desativa a obter estatísticas de coluna do metastore.

    ![Estatísticas de coluna do conjunto de estatísticas de ramo de registo](./media/hdinsight-changing-configs-via-ambari/hive-stats-fetch-column-stats.png)

* `hive.stats.fetch.partition.stats`

    Estatísticas da partição básica, tais como o número de linhas, o tamanho dos dados e o tamanho do ficheiro são armazenadas em metastore. Quando definida como true, a partição estatísticas são obtidas a partir do metastore. Quando falso, o tamanho do ficheiro é obtido a partir do sistema de arquivos e o número de linhas é obtido a partir do esquema de linha.

    ![Estatísticas de partição de conjunto de estatísticas de ramo de registo](./media/hdinsight-changing-configs-via-ambari/hive-stats-fetch-partition-stats.png)

### <a name="enable-intermediate-compression"></a>Ativar a compressão intermédio

Tarefas de mapa criam arquivos intermediários que são utilizados pelas tarefas reducer. Intermediário compressão diminui o tamanho de arquivo intermediário.

Tarefas do Hadoop são geralmente um afunilamento de e/s. A compressão de dados pode acelerar as e/s e a transferência de rede geral.

Os tipos de compactação disponíveis são:

| Formato | Ferramenta | Algoritmo | Extensão de ficheiro | Divisível? |
| -- | -- | -- | -- | -- |
| Gzip | Gzip | DEFLATE | .gz | Não |
| Bzip2 | Bzip2 | Bzip2 |.bz2 | Sim |
| LZO | Lzop | LZO | .lzo | Sim, se indexada |
| Snappy | N/A | Snappy | Snappy | Não |

Como regra geral, é importante ter o método de compressão divisível, caso contrário, serão criados mapeadores muito poucos. Se os dados de entrada forem texto, `bzip2` é a melhor opção. Para o formato ORC, Snappy é a opção de compressão mais rápida.

1. Para ativar a compressão intermediário, navegue para a colmeia **configurações** separador e, em seguida, defina o `hive.exec.compress.intermediate` parâmetro como true. O valor predefinido é false.

    ![Hive exec compress intermédio](./media/hdinsight-changing-configs-via-ambari/hive-exec-compress-intermediate.png)

    > [!NOTE]  
    > Para compactar arquivos intermediários, escolha um codec de compressão com CPU mais baixa custo, mesmo que o codec não tiver uma saída de compressão elevada.

1. Para definir o codec de compressão intermediários, adicione a propriedade personalizada `mapred.map.output.compression.codec` para o `hive-site.xml` ou `mapred-site.xml` ficheiro.

1. Para adicionar uma definição personalizada:

    a. Navegue para a colmeia **configurações** separador e selecione o **avançadas** separador.

    b. Sob o **avançadas** separador, localize e expanda o **personalizado. o ramo de registo-site** painel.

    c. Clique no link **Adicionar propriedade** na parte inferior do painel de site do ramo de registo personalizado.

    d. Na janela de propriedade de adicionar, introduza `mapred.map.output.compression.codec` como a chave e `org.apache.hadoop.io.compress.SnappyCodec` como o valor.

    e. Clique em **Adicionar**.

    ![Propriedade personalizada do Hive](./media/hdinsight-changing-configs-via-ambari/hive-custom-property.png)

    Isto irá compactar o arquivo intermediário usando a compactação Snappy. Quando é adicionada a propriedade, aparecerá no painel de site do ramo de registo personalizado.

    > [!NOTE]  
    > Este procedimento modifica o `$HADOOP_HOME/conf/hive-site.xml` ficheiro.

### <a name="compress-final-output"></a>Comprimir a saída final

Também pode ser comprimida a saída final do Hive.

1. Para comprimir a saída final do ramo de registo, navegue para a colmeia **configurações** separador e, em seguida, defina o `hive.exec.compress.output` parâmetro como true. O valor predefinido é false.

1. Para escolher o codec de compressão de saída, adicione o `mapred.output.compression.codec` propriedade personalizada para o painel de site do ramo de registo personalizado, conforme descrito no passo 3 da secção anterior.

    ![Propriedade personalizada do Hive](./media/hdinsight-changing-configs-via-ambari/hive-custom-property2.png)

### <a name="enable-speculative-execution"></a>Ativar a execução especulativa

A execução especulativa inicia um determinado número de tarefas duplicadas para detetar e a lista de proibições o controlador de tarefa de execução lenta, ainda assim aumentando a execução de tarefa geral ao otimizar os resultados de tarefas individuais.

A execução especulativa não deve ser ativada para tarefas de MapReduce de execução longa com grandes quantidades de entrada.

* Para ativar a execução especulativa, navegue para a colmeia **configurações** separador e, em seguida, defina o `hive.mapred.reduce.tasks.speculative.execution` parâmetro como true. O valor predefinido é false.

    ![Hive mapred reduzir a execução especulativa tarefas](./media/hdinsight-changing-configs-via-ambari/hive-mapred-reduce-tasks-speculative-execution.png)

### <a name="tune-dynamic-partitions"></a>Otimizar as partições dinâmicas

Hive permite a criação de partições dinâmicas ao inserir os registros numa tabela, sem a definição prévia de cada partição. Este é um recurso poderoso, embora poderá resultar na criação de um grande número de partições e um grande número de ficheiros para cada partição.

1. Para o Hive fazer partições dinâmicas, o `hive.exec.dynamic.partition` valor do parâmetro deve ser verdadeiro (predefinição).

1. Alterar o modo de partição dinâmico para *strict*. No modo strict, pelo menos uma partição tem de ser estáticos. Isto impede que consultas sem o filtro de partição na cláusula WHERE, ou seja, *strict* impede a consultas que analisar todas as partições. Navegue para a colmeia **configurações** separador e, em seguida, defina `hive.exec.dynamic.partition.mode` para **strict**. O valor predefinido é **nonstrict**.
 
1. Para limitar o número de partições dinâmicas a criar, modificar o `hive.exec.max.dynamic.partitions` parâmetro. O valor predefinido é de 5000.
 
1. Para limitar o número total de partições dinâmicas por nó, modificar `hive.exec.max.dynamic.partitions.pernode`. O valor predefinido é 2000.

### <a name="enable-local-mode"></a>Ativar o modo de local

Modo de local permite que o Hive executar todas as tarefas de uma tarefa numa única máquina ou, às vezes num único processo. Isto melhora o desempenho de consulta se os dados de entrada são pequenos e a sobrecarga de iniciar tarefas para consultas consome uma porcentagem significativa da execução de consulta global.

Para ativar o modo de local, adicione a `hive.exec.mode.local.auto` parâmetro para o painel de site do ramo de registo personalizado, conforme explicado no passo 3 do [ativar a compressão intermediário](#enable-intermediate-compression) secção.

![Hive exec modo local automática](./media/hdinsight-changing-configs-via-ambari/hive-exec-mode-local-auto.png)

### <a name="set-single-mapreduce-multigroup-by"></a>Conjunto único MapReduce MultiGROUP por

Quando esta propriedade está definida como true, uma consulta MultiGROUP por com chaves de agrupar por comum gera uma única tarefa de MapReduce.  

Para habilitar esse comportamento, adicione a `hive.multigroupby.singlereducer` parâmetro para o painel de site do ramo de registo personalizado, conforme explicado no passo 3 do [ativar a compressão intermediário](#enable-intermediate-compression) secção.

![Hive definir único MapReduce MultiGROUP](./media/hdinsight-changing-configs-via-ambari/hive-multigroupby-singlereducer.png)

### <a name="additional-hive-optimizations"></a>Otimizações de ramo de registo adicionais

As secções seguintes descrevem otimizações adicionais relacionados com o Hive que pode definir.

#### <a name="join-optimizations"></a>Junte-se a otimizações

O tipo de associação padrão no Hive é um *shuffle associação*. No ramo de registo, mapeadores especiais a entrada de leitura e emitem um par de chave/valor de associação a um arquivo intermediário. Hadoop ordena e intercala estes pares numa fase de shuffle. Neste estágio shuffle é Caro. Selecionar a associação correta com base nos seus dados pode melhorar significativamente o desempenho.

| Tipo de União | Quando | Como | Definições do Hive | Comentários |
| -- | -- | -- | -- | -- |
| Shuffle associação | <ul><li>Escolha do padrão</li><li>Sempre funciona</li></ul> | <ul><li>Lê a partir da parte de uma das tabelas</li><li>Buckets e ordena na chave de junção</li><li>Envia um bucket para cada reduce</li><li>A associação é feita no lado do Reduza</li></ul> | Não existem Hive significativo definição necessários | Funciona sempre |
| Associação de mapa | <ul><li>Uma tabela se encaixa na memória</li></ul> | <ul><li>Lê a pequena tabela para tabela de hash de memória</li><li>Fluxos por meio da parte do arquivo grande</li><li>Associa cada registo da tabela de hash</li><li>As associações são pelo mapeador autónomo</li></ul> | `hive.auto.confvert.join=true` | Muito rápida, mas limitado |
| Bucket de intercalação de ordenação | Se ambas as tabelas são: <ul><li>Ordenado o mesmo</li><li>Registados com o mesmo</li><li>Ingressar na coluna ordenada/registados</li></ul> | Cada processo: <ul><li>Lê um registo de cada tabela</li><li>Processa a linha com o valor mais baixo</li></ul> | `hive.auto.convert.sortmerge.join=true` | Muito eficiente |

#### <a name="execution-engine-optimizations"></a>Otimizações de motor de execução

Recomendações adicionais para otimizar o motor de execução do Hive:

| Definição | Recomendado | Predefinição do HDInsight |
| -- | -- | -- |
| `hive.mapjoin.hybridgrace.hashtable` | = TRUE mais seguro, mais lento; FALSE = mais rapidamente | false |
| `tez.am.resource.memory.mb` | Limite superior de 4 GB para a maioria | Otimizadas |
| `tez.session.am.dag.submit.timeout.secs` | 300+ | 300 |
| `tez.am.container.idle.release-timeout-min.millis` | 20000+ | 10000 |
| `tez.am.container.idle.release-timeout-max.millis` | 40000+ | 20000 |

## <a name="apache-pig-optimization"></a>Otimização de Apache Pig

[Apache Pig](https://pig.apache.org/) propriedades podem ser modificadas na interface do Usuário a otimização das consultas do Pig de web do Ambari. Modificar as propriedades de Pig a partir do Ambari diretamente modifica as propriedades de Pig no `/etc/pig/2.4.2.0-258.0/pig.properties` ficheiro.

1. Para modificar as propriedades de Pig, navegue para o Pig **configurações** separador e, em seguida, expanda o **pig propriedades avançadas** painel.

1. Localizar, anule os comentários e altere o valor da propriedade que pretende modificar.

1. Selecione **guardar** no lado superior direito da janela para salvar o novo valor. Algumas propriedades podem exigir um reinício do serviço.

    ![Propriedades avançadas do pig](./media/hdinsight-changing-configs-via-ambari/advanced-pig-properties.png)
 
> [!NOTE]  
> As definições de nível de sessão substituem valores de propriedade no `pig.properties` ficheiro.

### <a name="tune-execution-engine"></a>Otimizar o motor de execução

Dois mecanismos de execução estão disponíveis para executar Pig scripts: MapReduce e Tez. Tez é um motor de otimizada e é muito mais rápido que MapReduce.

1. Para modificar o motor de execução, no **pig propriedades avançadas** painel, localizar a propriedade `exectype`.

1. O valor predefinido é **MapReduce**. Altere-o para **Tez**.


### <a name="enable-local-mode"></a>Ativar o modo de local

À semelhança do Hive, de modo local é utilizada para acelerar as tarefas com quantidades relativamente pequenas de dados.

1. Para ativar o modo de local, defina `pig.auto.local.enabled` para **true**. O valor predefinido é false.

1. Tarefas com um tamanho de dados de entrada inferior ao `pig.auto.local.input.maxbytes` valor da propriedade são consideradas como pequenas tarefas. O valor predefinido é 1 GB.


### <a name="copy-user-jar-cache"></a>Copie o cache de jar do utilizador

PIg copia os ficheiros JAR exigidos por UDFs, para uma cache distribuída, para que fiquem disponíveis para nós de tarefa. Estes jars não são alterados com frequência. Se estiver ativada, o `pig.user.cache.enabled` definição permite jars sejam colocados em cache para reutilizá-los para tarefas executadas pelo mesmo utilizador. Isso resulta num pequeno aumento no desempenho de tarefa.

1. Para ativar, defina `pig.user.cache.enabled` como true. A predefinição é falso.

1. Para definir o caminho de base do Intune em cache, definir `pig.user.cache.location` para o caminho de base. A predefinição é `/tmp`.


### <a name="optimize-performance-with-memory-settings"></a>Otimizar o desempenho com as definições de memória

As seguintes definições de memória podem ajudar a otimizar o desempenho do script Pig.

* `pig.cachedbag.memusage`: A quantidade de memória alocada a uma matriz. Uma matriz é o conjunto de cadeias de identificação. Uma cadeia de identificação é um conjunto ordenado de campos e um campo é um conjunto de dados. Se os dados numa sacola estão além da memória alocada, é derramou no disco. O valor predefinido é 0,2, que representa a 20 por cento da memória disponível. Esta memória é compartilhada entre todos os recipientes num aplicativo.

* `pig.spill.size.threshold`: Recipientes de maiores do que este limiar de tamanho vaze (em bytes) são derramou no disco. O valor predefinido é 5 MB.


### <a name="compress-temporary-files"></a>Comprimir ficheiros temporários

PIg gera ficheiros temporários durante a execução de tarefa. Comprimir os ficheiros temporários resulta num aumento de desempenho quando ler ou gravar ficheiros no disco. As seguintes definições podem servir para comprimir ficheiros temporários.

* `pig.tmpfilecompression`: Quando verdadeiro, permite a compactação de arquivo temporário. O valor predefinido é false.

* `pig.tmpfilecompression.codec`: O codec de compressão a utilizar para comprimir os ficheiros temporários. São os codecs de compressão recomendada [LZO](https://www.oberhumer.com/opensource/lzo/) e Snappy mais baixa utilização da CPU.

### <a name="enable-split-combining"></a>Ativar a combinação de divisão

Quando ativada, os arquivos pequenos são combinados para menos de mapa de tarefas. Isto melhora a eficiência de tarefas com muitos arquivos pequenos. Para ativar, defina `pig.noSplitCombination` como true. O valor predefinido é false.


### <a name="tune-mappers"></a>Otimizar mapeadores

O número de mapeadores é controlado ao modificar a propriedade `pig.maxCombinedSplitSize`. Esta ação Especifica o tamanho dos dados a ser processado por uma tarefa de mapa único. O valor predefinido é o tamanho do bloco do sistema de ficheiros predefinido. Aumentar este valor resulta numa diminuição do número de tarefas de mapeador de pontos.


### <a name="tune-reducers"></a>Otimizar redutores

O número de redutores é calculado com base no parâmetro `pig.exec.reducers.bytes.per.reducer`. O parâmetro especifica o número de bytes processados por reducer, por padrão, 1 GB. Para limitar o número máximo de redutores, defina o `pig.exec.reducers.max` propriedade, por predefinição 999.


## <a name="apache-hbase-optimization-with-the-ambari-web-ui"></a>Otimização do HBase do Apache com a IU web do Ambari

[Apache HBase](https://hbase.apache.org/) configuração seja alterada a partir de **configurações de HBase** separador. As secções seguintes descrevem algumas das definições de configuração importantes que afetam o desempenho de HBase.

### <a name="set-hbaseheapsize"></a>Set HBASE_HEAPSIZE

O tamanho da pilha de HBase Especifica a quantidade máxima de heap a ser utilizada em megabytes por *região* e *mestre* servidores. O valor predefinido é 1000 MB. Isso deve ser ajustado para a carga de trabalho do cluster.

1. Para modificar, navegue para o **HBase-env avançada** painel no HBase **configurações** separador e, em seguida, localize o `HBASE_HEAPSIZE` definição.

1. Altere o valor predefinido para 5000 MB.

    ![HBASE_HEAPSIZE](./media/hdinsight-changing-configs-via-ambari/hbase-heapsize.png)


### <a name="optimize-read-heavy-workloads"></a>Otimizar cargas de trabalho de leitura intensiva

As seguintes configurações são importantes para melhorar o desempenho das cargas de trabalho de leitura intensiva.

#### <a name="block-cache-size"></a>Tamanho da cache de bloco

A cache de bloco é o cache de leitura. O tamanho é controlado pelo `hfile.block.cache.size` parâmetro. O valor predefinido é 0.4, que é 40 por cento da memória de servidor de região total. Quanto maior for o tamanho de cache de bloco, será mais rápidas leituras aleatórias.

1. Para modificar este parâmetro, navegue para o **configurações** separador no HBase **configurações** separador e, em seguida, localize **% do RegionServer alocadas para os Buffers de leitura**.

    ![Tamanho de cache do bloco de HBase](./media/hdinsight-changing-configs-via-ambari/hbase-block-cache-size.png)
 
1. Para alterar o valor, selecione o **editar** ícone.


#### <a name="memstore-size"></a>Tamanho do Memstore

Todas as edições são armazenadas na memória intermédia, chamada um *Memstore*. Isso aumenta a quantidade total de dados que podem ser gravados em disco numa única operação, e ela acelera a posterior acesso para as edições recentes. O tamanho do Memstore é definido pelos dois parâmetros seguintes:

* `hbase.regionserver.global.memstore.UpperLimit`: Define a percentagem máxima do servidor de região que pode utilizar Memstore combinado.

* `hbase.regionserver.global.memstore.LowerLimit`: Define a percentagem mínima de servidor de região que Memstore combinado pode utilizar.

Para otimizar para leituras aleatórias, pode reduzir os limites do Memstore superiores e inferiores.


#### <a name="number-of-rows-fetched-when-scanning-from-disk"></a>Número de linhas obtido quando efetua a análise do disco

O `hbase.client.scanner.caching` definição define o número de linhas a ler a partir do disco quando o `next` método é chamado num scanner.  O valor predefinido é 100. Maior o número, menos as chamadas remotas feitas do cliente para o servidor de região, resultando em análises mais rápidos. No entanto, isso também irá aumentar a pressão de memória no cliente.

![Número de HBase de linhas obtidas](./media/hdinsight-changing-configs-via-ambari/hbase-num-rows-fetched.png)

> [!IMPORTANT]  
> Não defina o valor, de modo a que o tempo entre a invocação do método seguinte num scanner é maior do que o tempo limite do scanner. A duração de tempo limite do scanner é definida pelo `hbase.regionserver.lease.period` propriedade.


### <a name="optimize-write-heavy-workloads"></a>Otimizar cargas de trabalho pesadas de escrita

As seguintes configurações são importantes para melhorar o desempenho das cargas de trabalho de escrita intensiva.


#### <a name="maximum-region-file-size"></a>Tamanho de ficheiro máximo de região

HBase armazena dados num formato de arquivo interno, chamado *HFile*. A propriedade `hbase.hregion.max.filesize` define o tamanho de um único HFile para uma região.  Uma região está dividida em duas regiões se a soma de todos os HFiles numa região for maior que esta definição.
 
![Filesize máx. de HRegion de HBase](./media/hdinsight-changing-configs-via-ambari/hbase-hregion-max-filesize.png)

Quanto maior for o tamanho do ficheiro de região, menor será o número de divisões de. Pode aumentar o tamanho do ficheiro para determinar um valor resulta no máximo desempenho de escrita.


#### <a name="avoid-update-blocking"></a>Evitar o bloqueio de atualização

* A propriedade `hbase.hregion.memstore.flush.size` define o tamanho no qual Memstore é descarregado no disco. O tamanho predefinido é de 128 MB.

* O multiplicador de bloco de região Hbase é definido por `hbase.hregion.memstore.block.multiplier`. O valor predefinido é 4. O máximo permitido é 8.

* HBase bloqueia as atualizações, se for o Memstore (`hbase.hregion.memstore.flush.size` * `hbase.hregion.memstore.block.multiplier`) bytes.

    Com os valores padrão de descarregamento de tamanho e o multiplicador de bloco, as atualizações são bloqueadas quando Memstore é 128 * 4 = 512 MB de tamanho. Para reduzir a contagem de bloqueio de atualização, aumentar o valor de `hbase.hregion.memstore.block.multiplier`.

![Multiplicador de blocos da região do HBase](./media/hdinsight-changing-configs-via-ambari/hbase-hregion-memstore-block-multiplier.png)


### <a name="define-memstore-size"></a>Definir o tamanho do Memstore

Tamanho do Memstore é definido pela `hbase.regionserver.global.memstore.UpperLimit` e `hbase.regionserver.global.memstore.LowerLimit` parâmetros. Definição com um valor igual a cada outro reduz coloca em pausa durante escreve (também causar liberá-lo mais freqüentes) e resulta na escrita de maior desempenho.


### <a name="set-memstore-local-allocation-buffer"></a>Definir a memória intermédia do Memstore alocação local

Utilização de memória intermédia de alocação local do Memstore é determinada pela propriedade `hbase.hregion.memstore.mslab.enabled`. Quando ativada (true), isto impede a fragmentação da heap durante a operação de escrita intensivas. O valor predefinido é verdadeiro.
 
![hbase.hregion.memstore.mslab.enabled](./media/hdinsight-changing-configs-via-ambari/hbase-hregion-memstore-mslab-enabled.png)


## <a name="next-steps"></a>Passos Seguintes

* [Gerir clusters do HDInsight com a IU web do Apache Ambari](hdinsight-hadoop-manage-ambari.md)
* [API de REST do Apache Ambari](hdinsight-hadoop-manage-ambari-rest-api.md)
