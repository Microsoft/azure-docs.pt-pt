---
title: Configurar definições de faísca - Azure HDInsight
description: Como visualizar e configurar configurações apache spark para um cluster Azure HDInsight
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,seoapr2020
ms.date: 04/24/2020
ms.openlocfilehash: 25ff8611cc988c9777b52a313cfd74d4ee0a638c
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104870301"
---
# <a name="configure-apache-spark-settings"></a>Configurar as definições do Apache Spark

Um cluster HDInsight Spark inclui uma instalação da biblioteca Apache Spark.  Cada cluster HDInsight inclui parâmetros de configuração predefinidos para todos os seus serviços instalados, incluindo o Spark.  Um aspeto chave da gestão de um cluster HDInsight Apache Hadoop é monitorizar a carga de trabalho, incluindo spark jobs. Para gerir melhor os trabalhos de Spark, considere a configuração do cluster físico ao determinar a configuração lógica do cluster.

O cluster HDInsight Apache Spark padrão inclui os seguintes nóns: três nóns Apache ZooKeeper, dois nóns na cabeça e um ou mais nónsadores:

:::image type="content" source="./media/apache-spark-settings/spark-hdinsight-arch.png" alt-text="Arquitetura Spark HDInsight" border="false":::

O número de tamanhos de VM e VM para os nós no seu cluster HDInsight pode afetar a sua configuração de Faísca. Os valores de configuração hdinsight não padrão requerem frequentemente valores de configuração de faísca não padrão. Quando cria um cluster HDInsight Spark, é-lhe mostrado tamanhos VM sugeridos para cada um dos componentes. Atualmente, os [tamanhos Linux VM otimizados](../../virtual-machines/sizes-memory.md) para memória para Azure são D12 v2 ou superiores.

## <a name="apache-spark-versions"></a>Versões Apache Spark

Use a melhor versão Spark para o seu cluster.  O serviço HDInsight inclui várias versões tanto da Spark como da própria HDInsight.  Cada versão do Spark inclui um conjunto de definições de cluster predefinidos.  

Quando cria um novo cluster, existem várias versões Spark para escolher. Para ver a lista completa,  [Componentes e Versões HDInsight](../hdinsight-component-versioning.md).

> [!NOTE]  
> A versão padrão do Apache Spark no serviço HDInsight pode ser alterada sem aviso prévio. Se tiver uma dependência de versão, a Microsoft recomenda que especifique essa versão específica quando cria clusters utilizando .NET SDK, Azure PowerShell e Azure Classic CLI.

Apache Spark tem três localizações de configuração do sistema:

* As propriedades de faísca controlam a maioria dos parâmetros de aplicação e podem ser definidas usando um `SparkConf` objeto, ou através das propriedades do sistema Java.
* As variáveis ambientais podem ser usadas para definir as definições por máquina, como o endereço IP, através do `conf/spark-env.sh` script em cada nó.
* O registo madeireira pode ser configurado através `log4j.properties` de .

Quando seleciona uma versão específica do Spark, o seu cluster inclui as definições de configuração predefinidos.  Pode alterar os valores de configuração padrão do Spark utilizando um ficheiro de configuração de faísca personalizado.  Apresentamos um exemplo abaixo.

```
spark.hadoop.io.compression.codecs org.apache.hadoop.io.compress.GzipCodec
spark.hadoop.mapreduce.input.fileinputformat.split.minsize 1099511627776
spark.hadoop.parquet.block.size 1099511627776
spark.sql.files.maxPartitionBytes 1099511627776
spark.sql.files.openCostInBytes 1099511627776
```

O exemplo acima mostra vários valores predefinidos para cinco parâmetros de configuração de faíscas.  Estes valores são o codec de compressão, Apache Hadoop MapReduce tamanho mínimo dividido e tamanhos de blocos de parquet. Além disso, a partição Spar SQL e os tamanhos de ficheiro abertos são valores predefinidos.  Estas alterações de configuração são escolhidas porque os dados e empregos associados (neste exemplo, dados genômicos) têm características particulares. Estas características farão melhor utilizando estas definições de configuração personalizadas.

---

## <a name="view-cluster-configuration-settings"></a>Ver definições de configuração de cluster

Verifique as definições atuais de configuração do cluster HDInsight antes de fazer a otimização do desempenho no cluster. Lance o Painel HDInsight a partir do portal Azure clicando na ligação **dashboard** no painel de agrupamento Spark. Inscreva-se com o nome de utilizador e senha do administrador do cluster.

O Apache Ambari Web UI aparece, com um dashboard de métricas de utilização de recursos de cluster chave.  O Dashboard Ambari mostra-lhe a configuração Apache Spark e outros serviços instalados. O Dashboard inclui um separador **Config History,** onde vê informações para serviços instalados, incluindo o Spark.

Para ver os valores de configuração para Apache Spark, selecione **Config History**, em seguida, selecione **Spark2**.  Selecione o **separador Configs** e, em seguida, selecione o `Spark` link `Spark2` (ou, dependendo da sua versão) na lista de serviços.  Vê uma lista de valores de configuração para o seu cluster:

:::image type="content" source="./media/apache-spark-settings/spark-configurations.png" alt-text="Configurações de faíscas" border="true":::

Para ver e alterar os valores individuais de configuração do Spark, selecione qualquer ligação com "faísca" no título.  As configurações para Spark incluem valores de configuração personalizados e avançados nestas categorias:

* Padrão personalizado de Spark2
* Propriedades personalizadas de métricas spark2
* Avançados padrão spark2
* Spark2-env avançado
* Faísca avançada 2-colmeia-site-override

Se criar um conjunto não padrão de valores de configuração, o histórico de atualização é visível.  Este histórico de configuração pode ser útil para ver qual a configuração não padrão tem o melhor desempenho.

> [!NOTE]  
> Para ver, mas não alterar, configurações comuns de configuração do cluster Spark, selecione o **separador Ambiente** na interface de **UI spark job** de nível superior.

## <a name="configuring-spark-executors"></a>Configurar executores de faíscas

O diagrama que se segue mostra os principais objetos spark: o programa de condutor e o seu Contexto de Faísca associado, e o gestor de cluster e os seus nós de trabalhadores *n.*  Cada nó de trabalhador inclui um Executor, um cache e *n* instâncias de tarefa.

:::image type="content" source="./media/apache-spark-settings/hdi-spark-architecture.png" alt-text="Objetos de cluster" border="false":::

Os empregos de faísca usam recursos dos trabalhadores, particularmente a memória, por isso é comum ajustar os valores de configuração do Spark para os executadores de nóiros.

Três parâmetros-chave que são frequentemente ajustados para sintonizar as configurações do Spark para melhorar os requisitos de aplicação são `spark.executor.instances` `spark.executor.cores` , e `spark.executor.memory` . Um Executor é um processo lançado para uma aplicação Spark. Um Executor funciona no nó do trabalhador e é responsável pelas tarefas da aplicação. O número de nós de trabalhadores e o tamanho do nó do trabalhador determina o número de executores e tamanhos do executor. Estes valores são armazenados `spark-defaults.conf` nos nós da cabeça do cluster.  Pode editar estes valores num cluster de execução selecionando **padrão de faíscas personalizados** na UI web Ambari.  Depois de fazer alterações, é solicitado pela UI para **reiniciar** todos os serviços afetados.

> [!NOTE]  
> Estes três parâmetros de configuração podem ser configurados ao nível do cluster (para todas as aplicações que funcionam no cluster) e também especificados para cada aplicação individual.

Outra fonte de informação sobre os recursos utilizados pelos Executores spark é a UI da Aplicação Spark.  Na UI,  **os Executores** exibem vistas sumárias e detalhadas da configuração e dos recursos consumidos.  Determinar se altera os valores dos executores para todo o cluster, ou conjunto particular de execuções de emprego.

:::image type="content" source="./media/apache-spark-settings/apache-spark-executors.png" alt-text="Executores de Faíscas" border="true":::

Ou pode utilizar a API Ambari REST para verificar programáticamente as definições de configuração do cluster HDInsight e Spark.  Mais informações estão disponíveis na [referência Apache Ambari API no GitHub.](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md)

Dependendo da carga de trabalho do Spark, pode determinar que uma configuração não padrão do Apache Spark permite execuções de tarefas do Apache Spark mais otimizadas.  Faça testes de benchmark com cargas de trabalho de amostra para validar quaisquer configurações de cluster não padrão.  Alguns dos parâmetros comuns que pode querer ajustar:

|Parâmetro |Descrição|
|---|---|
|--executores num-|Define o número de executores.|
|--executor-núcleos|Define o número de núcleos para cada executor. Recomendamos a utilização de executores de tamanho médio, já que outros processos também consomem parte da memória disponível.|
|--executor-memória|Controla o tamanho da memória (tamanho da pilha) de cada executor em Apache Hadoop YARN, e terá de deixar alguma memória para a execução.|

Aqui está um exemplo de dois nóns de trabalhador com diferentes valores de configuração:

:::image type="content" source="./media/apache-spark-settings/executor-configuration.png" alt-text="Duas configurações de nó" border="false":::

A lista a seguir mostra os parâmetros de memória do executor de faíscas.

|Parâmetro |Descrição|
|---|---|
|spark.executor.memória|Define a quantidade total de memória disponível para um executor.|
|spark.storage.memoryFraction|(padrão ~60%) define a quantidade de memória disponível para armazenar RDDs persistidos.|
|faísca.shuffle.memoryFraction|(padrão ~20%) define a quantidade de memória reservada para baralhar.|
|spark.storage.unrollFraction and spark.storage.safetyFraction|(totalizando ~30% da memória total) - estes valores são utilizados internamente pela Spark e não devem ser alterados.|

O YARN controla a soma máxima de memória utilizada pelos recipientes em cada nó de faísca. O diagrama seguinte mostra as relações por nó entre objetos de configuração YARN e objetos spark.

:::image type="content" source="./media/apache-spark-settings/hdi-yarn-spark-memory.png" alt-text="Gestão da memória de faíscas de YARN" border="false":::

## <a name="change-parameters-for-an-application-running-in-jupyter-notebook"></a>Alterar parâmetros para uma aplicação em execução no Caderno Jupyter

Os clusters de faíscas em HDInsight incluem uma série de componentes por padrão. Cada um destes componentes inclui valores de configuração predefinidos, que podem ser ultrapassados conforme necessário.

|Componente |Descrição|
|---|---|
|Núcleo de Faísca|Spark Core, Spark SQL, Spark streaming APIs, GraphX e Apache Spark MLlib.|
|Anaconda|Um gestor de pacotes de python.|
|Apache Livy|A Apache Spark REST API, usada para submeter trabalhos remotos a um cluster HDInsight Spark.|
|Cadernos Jupyter e Cadernos Apache Zeppelin|UI baseado em navegador interativo para interagir com o seu cluster Spark.|
|Controlador ODBC|Conecta clusters Spark em HDInsight a ferramentas de inteligência empresarial (BI), como o Microsoft Power BI e o Tableau.|

Para aplicações em execução no Bloco de Notas do Jupyter, utilize o `%%configure` comando para escorrê-lo a partir do próprio caderno. Estas alterações de configuração serão aplicadas aos trabalhos spark executados a partir da sua instância de caderno. Faça tais alterações no início da aplicação, antes de executar a sua primeira célula de código. A configuração alterada é aplicada na sessão Livy quando é criada.

> [!NOTE]  
> Para alterar a configuração numa fase posterior da aplicação, utilize o `-f` parâmetro (força). No entanto, todos os progressos no pedido serão perdidos.

O código abaixo mostra como alterar a configuração de uma aplicação em execução num Bloco de Notas de Jupyter.

```
%%configure
{"executorMemory": "3072M", "executorCores": 4, "numExecutors":10}
```

## <a name="conclusion"></a>Conclusão

Monitorize as definições de configuração do núcleo para garantir que os seus trabalhos de Spark funcionam de forma previsível e performante. Estas definições ajudam a determinar a melhor configuração do cluster Spark para as suas cargas de trabalho específicas.  Também terá de monitorizar a execução de execuções de empregos de longa duração e de consumo de recursos.  Os desafios mais comuns centram-se em torno da pressão da memória de configurações impróprias, tais como executores de tamanho incorreto. Além disso, operações de longa duração, e tarefas, que resultam em operações cartesianas.

## <a name="next-steps"></a>Passos seguintes

* [Componentes e versões Apache Hadoop disponíveis com HDInsight?](../hdinsight-component-versioning.md)
* [Gerir recursos para um cluster Apache Spark em HDInsight](apache-spark-resource-manager.md)
* [Configuração de faíscas apache](https://spark.apache.org/docs/latest/configuration.html)
* [Executando Apache Spark em Apache Hadoop YARN](https://spark.apache.org/docs/latest/running-on-yarn.html)
