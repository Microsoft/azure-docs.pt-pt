---
title: Definir configurações do Spark – Azure HDInsight
description: Como configurar o Spark para um cluster do Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/17/2019
ms.openlocfilehash: eb948aa2b683f426831e1b0d34b44f814eab6b9f
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/24/2019
ms.locfileid: "68441919"
---
# <a name="configure-apache-spark-settings"></a>Configurar as definições do Apache Spark

HDInsight An cluster Spark inclui uma instalação da biblioteca de [Apache Spark](https://spark.apache.org/) .  Cada cluster HDInsight inclui parâmetros de configuração padrão para todos os seus serviços instalados, incluindo o Spark.  Um aspecto importante do gerenciamento de um cluster Apache Hadoop do HDInsight é o monitoramento da carga de trabalho, incluindo trabalhos do Spark, para garantir que os trabalhos estejam sendo executados de maneira previsível. Para executar melhor os trabalhos do Spark, considere a configuração do cluster físico ao determinar como otimizar a configuração lógica do cluster.

O cluster padrão Apache Spark do HDInsight inclui os seguintes nós: três nós [Apache ZooKeeper](https://zookeeper.apache.org/) , dois nós de cabeçalho e um ou mais nós de trabalho:

![Arquitetura do Spark HDInsight](./media/apache-spark-settings/spark-hdinsight-arch.png)

O número de VMs e os tamanhos de VM para os nós em seu cluster HDInsight também podem afetar a configuração do Spark. Os valores de configuração do HDInsight não padrão geralmente exigem valores de configuração do Spark não padrão. Quando você cria um cluster HDInsight Spark, são mostrados tamanhos de VM sugeridos para cada um dos componentes. Atualmente [, os tamanhos de VM Linux com otimização de memória](../../virtual-machines/linux/sizes-memory.md) do Azure são D12 v2 ou superior.

## <a name="apache-spark-versions"></a>Versões do Apache Spark

Use a melhor versão do Spark para o cluster.  O serviço HDInsight inclui várias versões do Spark e do HDInsight em si.  Cada versão do Spark inclui um conjunto de configurações de cluster padrão.  

Quando você cria um novo cluster, há várias versões do Spark para escolher. Para ver a lista completa, [componentes e versões do HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning)


> [!NOTE]  
> A versão padrão do Apache Spark no serviço HDInsight pode ser alterada sem aviso prévio. Se você tiver uma dependência de versão, a Microsoft recomenda que você especifique essa versão específica ao criar clusters usando o SDK do .NET, o Azure PowerShell e a CLI clássica do Azure.

Apache Spark tem três locais de configuração do sistema:

* As propriedades do Spark controlam a maioria dos parâmetros do aplicativo e `SparkConf` podem ser definidas usando um objeto ou por meio de propriedades do sistema Java.
* As variáveis de ambiente podem ser usadas para definir configurações por computador, como o endereço IP, por meio `conf/spark-env.sh` do script em cada nó.
* O registro em log pode `log4j.properties`ser configurado por meio do.

Quando você seleciona uma versão específica do Spark, o cluster inclui as definições de configuração padrão.  Você pode alterar os valores de configuração padrão do Spark usando um arquivo de configuração personalizado do Spark.  Um exemplo é mostrado abaixo.

```
    spark.hadoop.io.compression.codecs org.apache.hadoop.io.compress.GzipCodec
    spark.hadoop.mapreduce.input.fileinputformat.split.minsize 1099511627776
    spark.hadoop.parquet.block.size 1099511627776
    spark.sql.files.maxPartitionBytes 1099511627776
    spark.sql.files.openCostInBytes 1099511627776
```

O exemplo mostrado acima substitui vários valores padrão para cinco parâmetros de configuração do Spark.  Esses são o codec de compactação, Apache Hadoop tamanho mínimo de divisão do MapReduce e tamanhos de bloco parquet e também os valores padrão da partição SQL e dos tamanhos de arquivo abertos.  Essas alterações de configuração são escolhidas porque os dados e trabalhos associados (neste exemplo, dados de genoma) têm características específicas, que serão executadas melhor usando essas definições de configuração personalizadas.

---

## <a name="view-cluster-configuration-settings"></a>Exibir definições de configuração de cluster

Verifique os parâmetros de configuração atuais do cluster HDInsight antes de executar a otimização de desempenho no cluster. Inicie o painel do HDInsight no portal do Azure clicando no link **painel** no painel do cluster Spark. Entre com o nome de usuário e a senha do administrador de cluster.

A interface do usuário da Web do Apache Ambari é exibida, com uma exibição de painel de métricas de utilização de recursos de cluster de chaves.  O painel do Ambari mostra a configuração de Apache Spark e outros serviços que você instalou. O painel inclui uma guia **histórico de configuração** , na qual você pode exibir informações de configuração para todos os serviços instalados, incluindo o Spark.

Para ver os valores de configuração para Apache Spark, selecione **histórico de configuração**e, em seguida, selecione **Spark2**.  Selecione a guia **configurações** e, em seguida, `Spark` selecione o `Spark2`link (ou, dependendo de sua versão) na lista de serviços.  Você verá uma lista de valores de configuração para o cluster:

![Configurações do Spark](./media/apache-spark-settings/spark-config.png)

Para ver e alterar valores de configuração individuais do Spark, selecione qualquer link com a palavra "Spark" no título do link.  As configurações do Spark incluem valores de configuração personalizados e avançados nessas categorias:

* Spark2 personalizados-padrões
* Spark2 personalizadas-métricas-Propriedades
* Spark2 avançados-padrões
* Spark2-env avançado
* Spark2 avançado-Hive-site-override

Se você criar um conjunto não padrão de valores de configuração, também poderá ver o histórico de suas atualizações de configuração.  Esse histórico de configuração pode ser útil para ver qual configuração não padrão tem o desempenho ideal.

> [!NOTE]  
> Para ver, mas não alterar, as definições de configuração de cluster Spark comuns, selecione a guia **ambiente** na interface de **interface do usuário do trabalho do Spark** de nível superior.

## <a name="configuring-spark-executors"></a>Configurando executores do Spark

O diagrama a seguir mostra os principais objetos Spark: o programa de driver e seu contexto do Spark associado e o Gerenciador de cluster e seus *n* nós de trabalho.  Cada nó de trabalho inclui um executor, um cache e *n* instâncias de tarefa.

![Objetos de cluster](./media/apache-spark-settings/spark-arch.png)

Os trabalhos do Spark usam recursos de trabalho, especialmente a memória, portanto, é comum ajustar os valores de configuração do Spark para executores de nó de trabalho.

Três parâmetros principais que geralmente são ajustados para ajustar as configurações do Spark para melhorar `spark.executor.instances`os `spark.executor.cores`requisitos do `spark.executor.memory`aplicativo são, e. Um executor é um processo iniciado para um aplicativo Spark. Um executor é executado no nó de trabalho e é responsável pelas tarefas do aplicativo. Para cada cluster, o número padrão de executores e os tamanhos do executor são calculados com base no número de nós de trabalho e no tamanho do nó de trabalho. Eles são armazenados no `spark-defaults.conf` nos nós de cabeçalho do cluster.  Você pode editar esses valores em um cluster em execução selecionando o link **personalizado Spark-defaults** na interface do usuário do amAmbari Web.  Depois de fazer alterações, você será solicitado pela interface do usuário para **reiniciar** todos os serviços afetados.

> [!NOTE]  
> Esses três parâmetros de configuração podem ser configurados no nível do cluster (para todos os aplicativos que são executados no cluster) e também especificados para cada aplicativo individual.

Outra fonte de informações sobre os recursos que estão sendo usados pelos executores do Spark é a interface do usuário do aplicativo Spark.  Na interface do usuário do Spark,  selecione a guia executores para exibir as exibições de resumo e detalhe da configuração e dos recursos consumidos pelos executores.  Essas exibições podem ajudá-lo a determinar se é para alterar os valores padrão para executores do Spark para todo o cluster ou um conjunto específico de execuções de trabalho.

![Executores do Spark](./media/apache-spark-settings/spark-executors.png)

Como alternativa, você pode usar a API REST do amAmbari para verificar programaticamente as definições de configuração do cluster HDInsight e Spark.  Mais informações estão disponíveis na [referência da API do Apache Ambari no GitHub](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md).

Dependendo da carga de trabalho do Spark, você pode determinar que uma configuração não padrão do Spark fornece execuções de trabalhos Spark mais otimizadas.  Você deve executar testes de benchmark com cargas de trabalho de exemplo para validar qualquer configuração de cluster não padrão.  Alguns dos parâmetros comuns que você pode considerar ajustar são:

* `--num-executors`define o número de executores.
* `--executor-cores`define o número de núcleos para cada executor. É recomendável usar executores de tamanho intermediário, pois outros processos também consomem alguma parte da memória disponível.
* `--executor-memory`controla o tamanho da memória (tamanho do heap) de cada executor em [Apache HADOOP yarn](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html), e você precisará deixar alguma memória para a sobrecarga de execução.

Aqui está um exemplo de dois nós de trabalho com valores de configuração diferentes:

![Configurações de dois nós](./media/apache-spark-settings/executor-config.png)

A lista a seguir mostra os principais parâmetros de memória do executor Spark.

* `spark.executor.memory`define a quantidade total de memória disponível para um executor.
* `spark.storage.memoryFraction`(padrão ~ 60%) define a quantidade de memória disponível para armazenar RDDs persistentes.
* `spark.shuffle.memoryFraction`(padrão ~ 20%) define a quantidade de memória reservada para ordem aleatória.
* `spark.storage.unrollFraction`e `spark.storage.safetyFraction` (totalizando aproximadamente 30% da memória total)-esses valores são usados internamente pelo Spark e não devem ser alterados.

YARN controla a soma máxima da memória usada pelos contêineres em cada nó do Spark. O diagrama a seguir mostra as relações por nó entre objetos de configuração YARN e objetos Spark.

![Gerenciamento de memória YARN Spark](./media/apache-spark-settings/yarn-spark-memory.png)

## <a name="change-parameters-for-an-application-running-in-jupyter-notebook"></a>Alterar parâmetros para um aplicativo em execução no Jupyter Notebook

Os clusters do Spark no HDInsight incluem vários componentes por padrão. Cada um desses componentes inclui valores de configuração padrão, que podem ser substituídos conforme necessário.

* Spark Core-Spark Core, Spark SQL, APIs de streaming do Spark, GraphX e Apache Spark MLlib.
* Anaconda-um Gerenciador de pacotes do Python.
* [Apache Livy](https://livy.incubator.apache.org/) -a API REST do Apache Spark, usada para enviar trabalhos remotos para um cluster HDInsight Spark.
* [Jupyter](https://jupyter.org/) e [Apache Zeppelin](https://zeppelin.apache.org/) notebooks-interface do usuário interativa baseada em navegador para interagir com o cluster Spark.
* Driver ODBC – conecta clusters Spark no HDInsight a ferramentas de business intelligence (BI), como Microsoft Power BI e tableau.

Para aplicativos em execução no notebook Jupyter, use o `%%configure` comando para fazer alterações de configuração no próprio bloco de anotações. Essas alterações de configuração serão aplicadas aos trabalhos do Spark executados na sua instância do bloco de anotações. Você deve fazer essas alterações no início do aplicativo antes de executar sua primeira célula de código. A configuração alterada é aplicada à sessão Livy quando ela é criada.

> [!NOTE]  
> Para alterar a configuração em um estágio posterior no aplicativo, use o `-f` parâmetro (Force). No entanto, todo o progresso no aplicativo será perdido.

O código a seguir mostra como alterar a configuração de um aplicativo em execução em um notebook Jupyter.

```
    %%configure
    {"executorMemory": "3072M", "executorCores": 4, "numExecutors":10}
```

## <a name="conclusion"></a>Conclusão

Há uma série de definições de configuração de núcleo que você precisa monitorar e ajustar para garantir que seus trabalhos do Spark sejam executados de forma previsível e de alto desempenho. Essas configurações ajudam a determinar a melhor configuração de cluster Spark para suas cargas de trabalho específicas.  Você também precisará monitorar a execução de execuções de trabalho do Spark de execução longa e/ou de consumo de recursos.  Os desafios mais comuns são o centro em relação à pressão de memória devido a configurações incorretas (especialmente executores de tamanho incorreto), operações de longa execução e tarefas, que resultam em operações cartesianas.

## <a name="next-steps"></a>Passos seguintes

* [Apache Hadoop componentes e versões disponíveis com o HDInsight?](../hdinsight-component-versioning.md)
* [Gerenciar recursos para um cluster Apache Spark no HDInsight](apache-spark-resource-manager.md)
* [Configurar clusters no HDInsight com Apache Hadoop, Apache Spark, Apache Kafka e muito mais](../hdinsight-hadoop-provision-linux-clusters.md)
* [Configuração de Apache Spark](https://spark.apache.org/docs/latest/configuration.html)
* [Executando Apache Spark em Apache Hadoop YARN](https://spark.apache.org/docs/latest/running-on-yarn.html)
