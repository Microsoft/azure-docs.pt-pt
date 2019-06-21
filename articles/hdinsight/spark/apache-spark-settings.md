---
title: Configurar as definições do Spark - Azure HDInsight
description: Como configurar o Spark para um cluster do HDInsight do Azure.
author: maxluk
ms.author: maxluk
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/17/2019
ms.openlocfilehash: d5dca3f4517f3d24b765652f420a38c258ad9a2e
ms.sourcegitcommit: 156b313eec59ad1b5a820fabb4d0f16b602737fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/18/2019
ms.locfileid: "67190925"
---
# <a name="configure-apache-spark-settings"></a>Configurar as definições do Apache Spark

Um cluster do Spark do HDInsight inclui uma instalação do [Apache Spark](https://spark.apache.org/) biblioteca.  Cada cluster do HDInsight inclui parâmetros de configuração padrão para todos os seus serviços instalados, incluindo Spark.  Um aspecto fundamental de gerir um cluster do Apache Hadoop do HDInsight está a monitorizar cargas de trabalho, incluindo tarefas do Spark, para se certificar de que as tarefas estão em execução de maneira previsível. Para melhor executar tarefas do Spark, considere a configuração de cluster físico ao determinar como otimizar a configuração do cluster de lógica.

O cluster de HDInsight Apache Spark padrão inclui os seguintes nós: três [Apache ZooKeeper](https://zookeeper.apache.org/) nós, dois nós principais e um ou mais nós de trabalho:

![Arquitetura do HDInsight Spark](./media/apache-spark-settings/spark-hdinsight-arch.png)

O número de VMs e os tamanhos VM, para os nós no cluster do HDInsight também podem afetar a sua configuração de Spark. Valores de configuração do HDInsight não predefinidos, muitas vezes, necessitam de valores de configuração de Spark não predefinido. Quando cria um cluster do Spark do HDInsight, são apresentadas sugeridos tamanhos de VM para cada um dos componentes. Atualmente o [tamanhos de VM do Linux com otimização de memória](../../virtual-machines/linux/sizes-memory.md) para o Azure são D12 v2 ou superior.

## <a name="apache-spark-versions"></a>Versões do Apache Spark

Utilize a versão de Spark melhor para o seu cluster.  O serviço HDInsight inclui várias versões do Spark e o HDInsight em si.  Cada versão do Spark inclui um conjunto de predefinições de cluster.  

Quando cria um novo cluster, há várias versões de Spark à sua escolha. Para ver a lista completa, [versões e componentes do HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning)


> [!NOTE]  
> A versão predefinida do Apache Spark no HDInsight serviço podem ser alteradas sem aviso prévio. Se tiver uma dependência de versão, a Microsoft recomenda que especifica que a versão específica ao criar clusters com o .NET SDK do Azure PowerShell e CLI clássica do Azure.

Apache Spark tem três locais de configuração de sistema:

* Propriedades de Spark controlar a maioria dos parâmetros da aplicação e pode ser definidas utilizando um `SparkConf` objeto, ou por meio de propriedades do sistema de Java.
* Variáveis de ambiente podem ser utilizadas para definir configurações de por máquina, como o endereço IP, por meio do `conf/spark-env.sh` script em cada nó.
* O registo pode ser configurado através de `log4j.properties`.

Quando seleciona uma versão específica do Spark, o seu cluster inclui as definições de configuração padrão.  Pode alterar os valores de configuração de Spark padrão usando um arquivo de configuração personalizado do Spark.  Um exemplo é mostrado abaixo.

```
    spark.hadoop.io.compression.codecs org.apache.hadoop.io.compress.GzipCodec
    spark.hadoop.mapreduce.input.fileinputformat.split.minsize 1099511627776
    spark.hadoop.parquet.block.size 1099511627776
    spark.sql.files.maxPartitionBytes 1099511627776
    spark.sql.files.openCostInBytes 1099511627776
```

O exemplo mostrado acima substitui vários valores predefinidos para cinco parâmetros de configuração de Spark.  Estes são o codec de compressão, Apache Hadoop MapReduce dividir o tamanho mínimo e tamanhos de bloco parquet e também a partição de Spar SQL e tamanhos de abrir o ficheiro de valores predefinidos.  Estas alterações de configuração são escolhidas porque os dados associados e tarefas (neste exemplo, dados de genoma) têm características específicas, que executarão a melhor com as seguintes definições de configuração personalizada.

---

## <a name="view-cluster-configuration-settings"></a>Ver definições de configuração de cluster

Verifique se as definições de configuração de cluster do HDInsight atuais antes de executar a otimização de desempenho no cluster. Iniciar o Dashboard de HDInsight no portal do Azure ao clicar o **Dashboard** ligação no painel do cluster do Spark. Inicie sessão com o nome de utilizador e palavra-passe do administrador de cluster.

É apresentada a interface do Usuário do Apache Ambari Web, com uma vista de dashboard de métricas de utilização de recursos principais do cluster.  O Dashboard de Ambari mostra-lhe a configuração do Apache Spark e a outros serviços que tenha instalado. O Dashboard inclui um **histórico de Config** separador, onde pode ver informações de configuração para todos os serviços instalados, incluindo Spark.

Para ver os valores de configuração para o Apache Spark, selecione **Config histórico**, em seguida, selecione **Spark2**.  Selecione o **configurações** separador, em seguida, selecione a `Spark` (ou `Spark2`, consoante a sua versão) link na lista de serviços.  É apresentada uma lista de valores de configuração para o seu cluster:

![Configurações do Spark](./media/apache-spark-settings/spark-config.png)

Para ver e alterar os valores de configuração individuais do Spark, selecione qualquer ligação com a palavra "spark" no título de ligação.  As configurações para o Spark incluem ambos os valores de configuração personalizado e avançado nestas categorias:

* Padrões de Spark2 personalizados
* Propriedades de métricas de Spark2 personalizadas
* Predefinições de Spark2 avançadas
* Spark2-env avançada
* Avançadas spark2-hive-site – ignorar

Se criar um conjunto de não-padrão de valores de configuração, em seguida, também pode ver o histórico de atualizações de configuração.  Esse histórico de configuração pode ser útil para ver a configuração não predefinida tem um desempenho ideal.

> [!NOTE]  
> Para ver, mas não alterar, definições de configuração do cluster de Spark comuns, selecione o **ambiente** separador no nível superior **IU de tarefa do Spark** interface.

## <a name="configuring-spark-executors"></a>Configurar o executor do Spark

O diagrama seguinte mostra os objetos principais do Spark: o programa de driver e o contexto de Spark associado e o Gestor de cluster e a respetiva *n* nós de trabalho.  Cada nó de trabalho inclui um Executor, uma cache, e *n* instâncias de tarefas.

![Objetos de cluster](./media/apache-spark-settings/spark-arch.png)

Tarefas do Spark utilizam recursos de trabalho, particularmente memória, portanto, é comum para ajustar os valores de configuração do Spark para o nó de trabalho executores.

São três parâmetros de chave que são, muitas vezes, ajustados para ajustar as configurações do Spark para melhorar os requisitos de aplicação `spark.executor.instances`, `spark.executor.cores`, e `spark.executor.memory`. Um Executor é um processo iniciado para uma aplicação de Spark. Um Executor é executado no nó de trabalho e é responsável por tarefas para a aplicação. Para cada cluster, o número predefinido de executores e os tamanhos de executor, é calculado com base no número de nós de trabalho e o tamanho de nó de trabalho. Eles são armazenados no `spark-defaults.conf` sobre os nós principais do cluster.  Pode editar estes valores num cluster em execução, selecionando o **personalizado spark-predefinições** link na IU da web Ambari.  Depois de efetuar alterações, lhe for pedido, a interface do Usuário **reiniciar** todos os serviços afetados.

> [!NOTE]  
> Esses parâmetros de configuração de três podem ser configurados ao nível do cluster (para todas as aplicações executadas no cluster) e também são especificados para cada aplicativo individual.

Outra fonte de informações sobre os recursos a ser utilizado pelo executor do Spark é a interface do Usuário de aplicativo do Spark.  Na IU do Spark, selecione o **executores** separador para apresentar exibições de resumo e detalhes da configuração e os recursos consumidos pelo executor.  Estas vistas podem ajudá-lo a determinar se deve alterar os valores predefinidos para o executor do Spark para todo o cluster ou um determinado conjunto de execuções de tarefas.

![Executor do Spark](./media/apache-spark-settings/spark-executors.png)

Em alternativa, pode utilizar a API de REST do Ambari para verificar programaticamente definições de configuração de cluster do HDInsight e o Spark.  Estão disponíveis em mais informações a [referência de API do Apache Ambari no GitHub](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md).

Dependendo da sua carga de trabalho do Spark, pode determinar que uma configuração de Spark não-padrão fornece Spark mais otimizada execuções de tarefas.  Deve realizar testes com cargas de trabalho de exemplo para validar as configurações de cluster não-padrão de benchmark.  Alguns dos parâmetros comuns que pode considerar a ajustar-se:

* `--num-executors` Define o número de executor.
* `--executor-cores` Define o número de núcleos para cada executor. Recomendamos que utilize executores middle-sized, como outros processos também consumam uma parte da memória disponível.
* `--executor-memory` controla o tamanho de memória (tamanho da pilha) de cada executor no [Apache Hadoop YARN](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html), e precisará deixar alguma memória para a sobrecarga de execução.

Eis um exemplo de dois nós de trabalho com valores de configuração diferentes:

![Duas configurações de nó](./media/apache-spark-settings/executor-config.png)

A lista seguinte mostra os parâmetros de memória de chave executor do Spark.

* `spark.executor.memory` Define a quantidade total de memória disponível para um executor.
* `spark.storage.memoryFraction` (predefinição ~ 60%) Define a quantidade de memória disponível para armazenamento persistentes RDDs.
* `spark.shuffle.memoryFraction` (predefinição aproximadamente 20%) Define a quantidade de memória reservada para shuffle.
* `spark.storage.unrollFraction` e `spark.storage.safetyFraction` (totalizando aproximadamente 30% do total de memória) - estes valores são usados internamente pelo Spark e não deve ser alterados.

YARN controla a soma de máxima de memória utilizada pelos contentores em cada nó do Spark. O diagrama seguinte mostra as relações de por nó entre objetos de configuração do YARN e do Spark.

![Gerenciamento de memória do Spark YARN](./media/apache-spark-settings/yarn-spark-memory.png)

## <a name="change-parameters-for-an-application-running-in-jupyter-notebook"></a>Alterar os parâmetros para uma aplicação em execução no bloco de notas do Jupyter

Clusters do Spark no HDInsight incluem uma série de componentes, por predefinição. Cada um desses componentes inclui valores de configuração padrão, o que podem ser substituídos, conforme necessário.

* Spark Core - núcleo do Spark, Spark SQL, APIs, GraphX e Apache Spark MLlib transmissão em fluxo do Spark.
* Anaconda - Gestor de pacotes de uma python.
* [Apache Livy](https://livy.incubator.apache.org/) -da API de REST Apache Spark, utilizado para submeter tarefas remotas para um cluster do Spark do HDInsight.
* [Jupyter](https://jupyter.org/) e [Apache Zeppelin](https://zeppelin.apache.org/) blocos de notas - interativa baseada no browser da interface do Usuário para interagir com o seu cluster do Spark.
* Controlador ODBC - liga-se a clusters do Spark no HDInsight para as ferramentas de business intelligence (BI), como o Microsoft Power BI e o Tableau.

Para aplicativos em execução no bloco de notas Jupyter, use o `%%configure` comando para fazer a configuração é alterado de dentro do bloco de notas em si. Estas alterações de configuração serão aplicadas a executar a partir de sua instância do bloco de notas as tarefas do Spark. Deve fazer essas alterações no início do aplicativo, antes de executar a sua primeira célula do código. A configuração alterada é aplicada à sessão Livy quando é criada.

> [!NOTE]  
> Para alterar a configuração num estágio posterior na aplicação, utilize o `-f` parâmetro (force). No entanto, todo o progresso na aplicação serão perdido.

O código abaixo mostra como alterar a configuração para uma aplicação em execução num bloco de notas do Jupyter.

```
    %%configure
    {"executorMemory": "3072M", "executorCores": 4, "numExecutors":10}
```

## <a name="conclusion"></a>Conclusão

Existem várias definições de configuração de núcleos que precisa de monitorizar e ajustar para garantir que as tarefas do Spark são executadas de forma previsível e de elevado desempenho. Estas definições ajudam a determinar a melhor configuração de cluster do Spark para cargas de trabalho específicas.  Também terá de monitorizar a execução de longa execução e/ou consomem recursos execuções de tarefas do Spark.  O Centro de desafios mais comuns em torno de pressão de memória devido a configurações impróprias (executores particularmente incorretamente porte), longa as operações e tarefas, que resultam em operações Cartesianos.

## <a name="next-steps"></a>Passos Seguintes

* [Componentes do Apache Hadoop e versões disponíveis com o HDInsight?](../hdinsight-component-versioning.md)
* [Gerir os recursos de um cluster do Apache Spark no HDInsight](apache-spark-resource-manager.md)
* [Configurar clusters no HDInsight com Apache Hadoop, Apache Spark, Apache Kafka e muito mais](../hdinsight-hadoop-provision-linux-clusters.md)
* [Configuração do Apache Spark](https://spark.apache.org/docs/latest/configuration.html)
* [Em execução do Apache Spark no Apache Hadoop YARN](https://spark.apache.org/docs/latest/running-on-yarn.html)
