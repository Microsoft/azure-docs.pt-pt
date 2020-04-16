---
title: Configurar definições de faísca - Azure HDInsight
description: Como visualizar e configurar as definições de Apache Spark para um cluster Azure HDInsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 04/15/2020
ms.openlocfilehash: e13390067f8767e8c07b9c189264444e6d999a7a
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81411304"
---
# <a name="configure-apache-spark-settings"></a>Configurar as definições do Apache Spark

Um cluster HDInsight Spark inclui uma instalação da biblioteca [Apache Spark.](https://spark.apache.org/)  Cada cluster HDInsight inclui parâmetros de configuração predefinidos para todos os seus serviços instalados, incluindo o Spark.  Um aspeto chave da gestão de um cluster HDInsight Apache Hadoop é monitorizar a carga de trabalho, incluindo Spark Jobs. Para melhor executar os trabalhos spark, considere a configuração do cluster físico ao determinar a configuração lógica do cluster.

O cluster padrão HDInsight Apache Spark inclui os seguintes nós: três nós [do ZooKeeper Apache,](https://zookeeper.apache.org/) dois nós de cabeça e um ou mais nós de trabalhador:

![Arquitetura Spark HDInsight](./media/apache-spark-settings/spark-hdinsight-arch.png)

O número de VMs e tamanhos VM, para os nós do seu cluster HDInsight pode afetar a configuração spark. Os valores de configuração HDInsight não predefinidos requerem frequentemente valores de configuração de Spark não predefinidos. Quando cria um cluster HDInsight Spark, é mostrado tamanhos vM sugeridos para cada um dos componentes. Atualmente os [tamanhos De VM Linux otimizados](../../virtual-machines/linux/sizes-memory.md) pela Memória para Azure são D12 v2 ou superiores.

## <a name="apache-spark-versions"></a>Versões Apache Spark

Use a melhor versão Spark para o seu cluster.  O serviço HDInsight inclui várias versões tanto da Spark como da própria HDInsight.  Cada versão do Spark inclui um conjunto de definições de cluster padrão.  

Quando cria um novo cluster, existem várias versões Spark para escolher. Para ver a lista completa, [Componentes e Versões HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning).

> [!NOTE]  
> A versão padrão do Apache Spark no serviço HDInsight pode ser alterada sem aviso prévio. Se tiver uma dependência de versão, a Microsoft recomenda especificar essa versão em particular quando cria clusters utilizando .NET SDK, Azure PowerShell e Azure Classic CLI.

Apache Spark tem três localizações de configuração do sistema:

* As propriedades da faísca controlam a maioria `SparkConf` dos parâmetros de aplicação e podem ser definidas usando um objeto, ou através das propriedades do sistema Java.
* As variáveis ambientais podem ser usadas para definir definições `conf/spark-env.sh` por máquina, como o endereço IP, através do script em cada nó.
* A exploração madeireira pode ser configurada através de `log4j.properties`.

Quando selecionar uma versão específica do Spark, o seu cluster inclui as definições de configuração predefinidas.  Pode alterar os valores de configuração de Spark predefinidos utilizando um ficheiro de configuração de Spark personalizado.  Apresentamos um exemplo abaixo.

```
spark.hadoop.io.compression.codecs org.apache.hadoop.io.compress.GzipCodec
spark.hadoop.mapreduce.input.fileinputformat.split.minsize 1099511627776
spark.hadoop.parquet.block.size 1099511627776
spark.sql.files.maxPartitionBytes 1099511627776
spark.sql.files.openCostInBytes 1099511627776
```

O exemplo acima sobrepõe-se a vários valores predefinidos para cinco parâmetros de configuração spark.  Estes valores são o codec de compressão, Mapa de Hadoop ApacheReduzir tamanho mínimo dividido e tamanhos de bloco de parquet. Além disso, a partição Spar SQL e os valores padrão de tamanhos de ficheiro abertos.  Estas alterações de configuração são escolhidas porque os dados e empregos associados (neste exemplo, dados genómicos) têm características particulares. Estas características serão melhores utilizando estas configurações de configuração personalizadas.

---

## <a name="view-cluster-configuration-settings"></a>Ver configurações de configuração de cluster

Verifique as definições de configuração de cluster HDInsight atuais antes de fazer a otimização do desempenho no cluster. Lance o Painel HDInsight a partir do portal Azure clicando no link **dashboard** no painel de cluster Spark. Inscreva-se com o nome de utilizador e senha do administrador do cluster.

O Apache Ambari Web UI aparece, com um painel de métricas de utilização de recursos de cluster chave.  O Painel Ambari mostra-lhe a configuração Apache Spark e outros serviços instalados. O Dashboard inclui um separador **Config History,** onde vê informações para serviços instalados, incluindo a Spark.

Para ver os valores de configuração da Apache Spark, selecione **Config History**e, em seguida, selecione **Spark2**.  Selecione o separador **Configs** e, em seguida, selecione o `Spark` link (ou `Spark2`, dependendo da sua versão) na lista de serviços.  Vê uma lista de valores de configuração para o seu cluster:

![Configurações de faíscas](./media/apache-spark-settings/spark-configurations.png)

Para ver e alterar os valores individuais de configuração da Spark, selecione qualquer ligação com "faísca" no título.  As configurações para Spark incluem valores de configuração personalizados e avançados nestas categorias:

* Padrão personalizado spark2
* Propriedades personalizadas de Spark2-metrics
* Falhas avançadas de Spark2
* Spark2-env avançado
* Avançado spark2-hive-site-override

Se criar um conjunto de valores de configuração não predefinido, o histórico da atualização é visível.  Este histórico de configuração pode ser útil para ver qual a configuração não predefinida com um desempenho ótimo.

> [!NOTE]  
> Para ver, mas não alterar, configurações comuns de configuração do cluster Spark, selecione o separador **Ambiente** na interface **UI** Spark Job de nível superior.

## <a name="configuring-spark-executors"></a>Configurar executores de faíscas

O diagrama seguinte mostra os principais objetos Spark: o programa do condutor e o seu contexto de faísca associado, e o gestor de cluster e os seus nós *n* worker.  Cada nó de trabalhador inclui um Executor, uma cache, e *n* instâncias de tarefa.

![Objetos de aglomerado](./media/apache-spark-settings/hdi-spark-architecture.png)

Os trabalhos de faísca usam recursos dos trabalhadores, particularmente a memória, por isso é comum ajustar os valores de configuração da Faísca para executores de nó do trabalhador.

Três parâmetros-chave que são frequentemente ajustados para afinar `spark.executor.instances` `spark.executor.cores`as `spark.executor.memory`configurações de Spark para melhorar os requisitos de aplicação são , e . Um Executor é um processo lançado para uma aplicação Spark. Um Executor corre no nó do trabalhador e é responsável pelas tarefas para a aplicação. O número de nós operários e do tamanho do nó de trabalhador determina o número de executores e tamanhos de executor. Estes valores são `spark-defaults.conf` armazenados nos nós da cabeça do cluster.  Pode editar estes valores num cluster de execução selecionando **padrãos de faíscapersonalizados** na UI web ambari.  Depois de fazer alterações, é solicitado pela UI para **reiniciar** todos os serviços afetados.

> [!NOTE]  
> Estes três parâmetros de configuração podem ser configurados ao nível do cluster (para todas as aplicações que funcionam no cluster) e também especificados para cada aplicação individual.

Outra fonte de informação sobre os recursos utilizados pelos Executores de Faíscas é a UI de Aplicação de Faíscas.  Na UI, os **Executores** exibem vistas sumárias e detalhadas da configuração e dos recursos consumidos.  Determine se altera os valores dos executores para todo o cluster, ou um conjunto particular de execuções de emprego.

![Executores de Faíscas](./media/apache-spark-settings/apache-spark-executors.png)

Ou pode utilizar a API Ambari REST para verificar programáticamente as definições de configuração do cluster HDInsight e Spark.  Mais informações estão disponíveis na [referência da API Apache Ambari no GitHub](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md).

Dependendo da carga de trabalho do Spark, pode determinar que uma configuração não padrão do Apache Spark permite execuções de tarefas do Apache Spark mais otimizadas.  Faça testes de benchmark com cargas de trabalho da amostra para validar quaisquer configurações de cluster não predefinidas.  Alguns dos parâmetros comuns que pode querer ajustar:

|Parâmetro |Descrição|
|---|---|
|--executores num-num|Define o número de executores.|
|--executor-núcleos|Define o número de núcleos para cada executor. Recomendamos o uso de executores de tamanho médio, uma vez que outros processos também consomem parte da memória disponível.|
|-- executor-memória|Controla o tamanho da memória (tamanho da pilha) de cada executor no [YARN Apache Hadoop,](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html)e terá de deixar alguma memória para execução.|

Aqui está um exemplo de dois nós operários com diferentes valores de configuração:

![Duas configurações de nó](./media/apache-spark-settings/executor-configuration.png)

A lista que se segue mostra os parâmetros de memória do executor de faíscas.

|Parâmetro |Descrição|
|---|---|
|spark.executor.memory|Define a quantidade total de memória disponível para um executor.|
|spark.storage.memoryFraction|(padrão ~60%) define a quantidade de memória disponível para armazenar RDDs persistidos.|
|spark.shuffle.memoryFraction|(padrão ~20%) define a quantidade de memória reservada para baralhar.|
|spark.storage.unrollFraction and spark.storage.safetyFraction|(totalizando ~30% da memória total) - estes valores são usados internamente pela Spark e não devem ser alterados.|

O ARN controla a soma máxima de memória utilizada pelos recipientes em cada nó de Faísca. O diagrama seguinte mostra as relações por nó entre objetos de configuração YARN e objetos de faísca.

![Gestão da memória de faísca de fios](./media/apache-spark-settings/hdi-yarn-spark-memory.png)

## <a name="change-parameters-for-an-application-running-in-jupyter-notebook"></a>Alterar parâmetros para uma aplicação em execução no caderno Jupyter

Os clusters de faíscas no HDInsight incluem uma série de componentes por padrão. Cada um destes componentes inclui valores de configuração predefinidos, que podem ser ultrapassados conforme necessário.

|Componente |Descrição|
|---|---|
|Núcleo de Faísca|Spark Core, Spark SQL, Spark streaming APIs, GraphX e Apache Spark MLlib.|
|Anaconda|Um gestor de pacotes python.|
|[Apache Livy](https://livy.incubator.apache.org/)|A Apache Spark REST API, usada para submeter trabalhos remotos a um cluster HDInsight Spark.|
|Cadernos [Jupyter](https://jupyter.org/) e [Apache Zeppelin](https://zeppelin.apache.org/)|UI interativo baseado no navegador para interagir com o seu cluster Spark.|
|Controlador ODBC|Conecta clusters De faíscas em HDInsight a ferramentas de inteligência empresarial (BI), como microsoft Power BI e Tableau.|

Para aplicações em execução no caderno `%%configure` Jupyter, utilize o comando para efazer alterações de configuração a partir do próprio caderno. Estas alterações de configuração serão aplicadas aos trabalhos spark executados a partir da sua instância de caderno. Faça tais alterações no início da aplicação, antes de executar a sua primeira célula de código. A configuração alterada é aplicada à sessão Livy quando é criada.

> [!NOTE]  
> Para alterar a configuração numa fase posterior `-f` da aplicação, utilize o parâmetro (força). No entanto, todos os progressos na aplicação serão perdidos.

O código abaixo mostra como alterar a configuração de uma aplicação em execução num caderno Jupyter.

```
%%configure
{"executorMemory": "3072M", "executorCores": 4, "numExecutors":10}
```

## <a name="conclusion"></a>Conclusão

Monitorize as definições de configuração do núcleo para garantir que os seus trabalhos spark funcionam de forma previsível e executada. Estas definições ajudam a determinar a melhor configuração do cluster Spark para as suas cargas de trabalho específicas.  Também terá de monitorizar a execução de execuções de emprego saneada e ou desgastante de recursos spark.  Os desafios mais comuns centram-se em torno da pressão da memória de configurações inadequadas, tais como executores de tamanho incorreto. Além disso, operações de longo prazo e tarefas, que resultam em operações cartesianas.

## <a name="next-steps"></a>Passos seguintes

* [Componentes e versões Apache Hadoop disponíveis com hDInsight?](../hdinsight-component-versioning.md)
* [Gerir recursos para um cluster Apache Spark no HDInsight](apache-spark-resource-manager.md)
* [Configurar clusters no HDInsight com Apache Hadoop, Apache Spark, Apache Kafka e muito mais](../hdinsight-hadoop-provision-linux-clusters.md)
* [Configuração de faísca apache](https://spark.apache.org/docs/latest/configuration.html)
* [Faísca Apache em Apache Hadoop YARN](https://spark.apache.org/docs/latest/running-on-yarn.html)
