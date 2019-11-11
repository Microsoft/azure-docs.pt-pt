---
title: Ajuste de desempenho Azure Data Lake Storage Gen1 – MapReduce
description: Diretrizes de ajuste de desempenho Azure Data Lake Storage Gen1 MapReduce
author: stewu
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 12/19/2016
ms.author: stewu
ms.openlocfilehash: a645049665bc1d51efa94a879b9d2e4e5529282f
ms.sourcegitcommit: bc193bc4df4b85d3f05538b5e7274df2138a4574
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/10/2019
ms.locfileid: "73904597"
---
# <a name="performance-tuning-guidance-for-mapreduce-on-hdinsight-and-azure-data-lake-storage-gen1"></a>Diretrizes de ajuste de desempenho para MapReduce no HDInsight e Azure Data Lake Storage Gen1

## <a name="prerequisites"></a>Pré-requisitos

* **Uma subscrição do Azure**. Consulte [Obter versão de avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Uma conta de Azure data Lake Storage Gen1**. Para obter instruções sobre como criar uma, consulte Introdução [ao Azure data Lake Storage Gen1](data-lake-store-get-started-portal.md)
* **Cluster HDInsight do Azure** com acesso a uma conta de data Lake Storage Gen1. Consulte [criar um cluster HDInsight com data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md). Certifique-se de habilitar Área de Trabalho Remota para o cluster.
* **Usando o MapReduce no HDInsight**. Para obter mais informações, consulte [usar o MapReduce no Hadoop no HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-use-mapreduce)
* **Examine as diretrizes de ajuste de desempenho para data Lake Storage Gen1**. Para obter conceitos gerais de desempenho, consulte [diretrizes de ajuste de desempenho data Lake Storage Gen1](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-performance-tuning-guidance)

## <a name="parameters"></a>Parâmetros

Ao executar trabalhos do MapReduce, aqui estão os parâmetros mais importantes que você pode configurar para aumentar o desempenho no Data Lake Storage Gen1:

|Parâmetro      | Descrição  |
|---------|---------|
|`Mapreduce.map.memory.mb`  |  A quantidade de memória a ser alocada a cada mapeador.  |
|`Mapreduce.job.maps`     |  O número de tarefas de mapa por trabalho.  |
|`Mapreduce.reduce.memory.mb`     |  A quantidade de memória a ser alocada a cada redutor.  |
|`Mapreduce.job.reduces`    |   O número de tarefas de redução por trabalho.  |

### <a name="mapreducemapmemory--mapreducereducememory"></a>MapReduce. map. Memory/MapReduce. reduzte. Memory

Ajuste esse número com base na quantidade de memória necessária para o mapa e/ou para a tarefa de redução. Você pode exibir os valores padrão de `mapreduce.map.memory` e `mapreduce.reduce.memory` em Ambari por meio da configuração do yarn. Em Ambari, navegue até YARN e exiba a guia **configurações** . A memória YARN será exibida.

### <a name="mapreducejobmaps--mapreducejobreduces"></a>MapReduce. Job. Maps/MapReduce. Job. reduz

Isso determina o número máximo de Mapeadores ou redutores a serem criados. O número de divisões determina quantos Mapeadores são criados para o trabalho MapReduce. Portanto, você pode obter menos Mapeadores do que o solicitado se houver menos divisões do que o número de Mapeadores solicitados.

## <a name="guidance"></a>Orientações

### <a name="step-1-determine-number-of-jobs-running"></a>Etapa 1: determinar o número de trabalhos em execução

Por padrão, o MapReduce usará o cluster inteiro para seu trabalho. Você pode usar menos do cluster usando menos Mapeadores do que os contêineres disponíveis. As diretrizes neste documento pressupõem que seu aplicativo é o único aplicativo em execução no cluster.

### <a name="step-2-set-mapreducemapmemorymapreducereducememory"></a>Etapa 2: definir MapReduce. map. Memory/MapReduce. reduzte. Memory

O tamanho da memória para tarefas de mapeamento e redução será dependente de seu trabalho específico. Você pode reduzir o tamanho da memória se quiser aumentar a simultaneidade. O número de tarefas em execução simultânea depende do número de contêineres. Ao reduzir a quantidade de memória por mapeador ou redutor, mais contêineres podem ser criados, o que permite que mais Mapeadores ou redutores sejam executados simultaneamente. Diminuir a quantidade de memória pode fazer com que alguns processos fique sem memória. Se você receber um erro de heap ao executar seu trabalho, aumente a memória por mapeador ou redutor. Considere que a adição de mais contêineres aumenta a sobrecarga extra para cada contêiner adicional, o que pode prejudicar o desempenho. Outra alternativa é obter mais memória usando um cluster com quantidades maiores de memória ou aumentando o número de nós no cluster. Mais memória permitirá que mais contêineres sejam usados, o que significa mais simultaneidade.

### <a name="step-3-determine-total-yarn-memory"></a>Etapa 3: determinar a memória total do YARN

Para ajustar MapReduce. Job. Maps/MapReduce. Job. Reduces, considere a quantidade total de memória YARN disponível para uso. Essas informações estão disponíveis em Ambari. Navegue até YARN e exiba a guia **configurações** . A memória YARN é exibida nesta janela. Multiplique a memória YARN com o número de nós no cluster para obter a memória total do YARN.

`Total YARN memory = nodes * YARN memory per node`

Se você estiver usando um cluster vazio, a memória poderá ser a memória YARN total para o cluster. Se outros aplicativos estiverem usando memória, você poderá optar por usar apenas uma parte da memória do cluster reduzindo o número de Mapeadores ou redutores para o número de contêineres que você deseja usar.

### <a name="step-4-calculate-number-of-yarn-containers"></a>Etapa 4: calcular o número de contêineres YARN

Os contêineres YARN ditam a quantidade de simultaneidade disponível para o trabalho. Pegue a memória YARN total e divida-a por MapReduce. map. Memory.

`# of YARN containers = total YARN memory / mapreduce.map.memory`

### <a name="step-5-set-mapreducejobmapsmapreducejobreduces"></a>Etapa 5: definir MapReduce. Job. Maps/MapReduce. Job. Reduces

Defina MapReduce. Job. Maps/MapReduce. Job. reduz para pelo menos o número de contêineres disponíveis. Você pode fazer experiências mais adiante aumentando o número de Mapeadores e redutores para ver se você obtém um melhor desempenho. Tenha em mente que mais Mapeadores terão sobrecarga adicional, portanto, ter muitos Mapeadores pode prejudicar o desempenho.

O agendamento da CPU e o isolamento da CPU são desativados por padrão, portanto, o número de contêineres YARN é restrito pela memória.

## <a name="example-calculation"></a>Exemplo de cálculo

Digamos que você tenha um cluster composto por 8 nós D14 e queira executar um trabalho com uso intensivo de e/s. Aqui estão os cálculos que você deve fazer:

### <a name="step-1-determine-number-of-jobs-running"></a>Etapa 1: determinar o número de trabalhos em execução

Para nosso exemplo, supomos que nosso trabalho é o único em execução.

### <a name="step-2-set-mapreducemapmemorymapreducereducememory"></a>Etapa 2: definir MapReduce. map. Memory/MapReduce. reduzte. Memory

Para nosso exemplo, você está executando um trabalho com uso intensivo de e/s e decide que 3 GB de memória para tarefas de mapeamento são suficientes.

`mapreduce.map.memory = 3GB`

### <a name="step-3-determine-total-yarn-memory"></a>Etapa 3: determinar a memória total do YARN

`total memory from the cluster is 8 nodes * 96GB of YARN memory for a D14 = 768GB`

### <a name="step-4-calculate--of-yarn-containers"></a>Etapa 4: calcular o n º de contêineres YARN

`# of YARN containers = 768 GB of available memory / 3 GB of memory = 256`

### <a name="step-5-set-mapreducejobmapsmapreducejobreduces"></a>Etapa 5: definir MapReduce. Job. Maps/MapReduce. Job. Reduces

`mapreduce.map.jobs = 256`

## <a name="limitations"></a>Limitações

**Limitação de Data Lake Storage Gen1**

Como um serviço multilocatário, o Data Lake Storage Gen1 define os limites de largura de banda no nível da conta. Se você atingir esses limites, começará a ver falhas de tarefas. Isso pode ser identificado observando-se os erros de limitação nos logs de tarefas. Se precisar de mais largura de banda para seu trabalho, entre em contato conosco.

Para verificar se você está ficando limitado, você precisa habilitar o log de depuração no lado do cliente. Veja como você pode fazer isso:

1. Coloque a propriedade a seguir nas propriedades Log4J em Ambari > YARN > config > avançado yarn-Log4J: Log4J. Logger. com. Microsoft. Azure. datalake. Store = DEBUG

2. Reinicie todos os nós/serviços para que a configuração entre em vigor.

3. Se estiver ficando limitado, você verá o código de erro HTTP 429 no arquivo de log YARN. O arquivo de log YARN está em/tmp/&lt;usuário&gt;/yarn.log

## <a name="examples-to-run"></a>Exemplos a serem executados

Para demonstrar como o MapReduce é executado em Data Lake Storage Gen1, o seguinte é um código de exemplo que foi executado em um cluster com as seguintes configurações:

* 16 nós D14v2
* Cluster Hadoop executando HDI 3,6

Para um ponto de partida, aqui estão alguns comandos de exemplo para executar MapReduce Teragen, Terasort e Teravalidate. Você pode ajustar esses comandos com base em seus recursos.

### <a name="teragen"></a>Teragen

```
yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teragen -Dmapreduce.job.maps=2048 -Dmapreduce.map.memory.mb=3072 10000000000 adl://example/data/1TB-sort-input
```

### <a name="terasort"></a>Terasort

```
yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar terasort -Dmapreduce.job.maps=2048 -Dmapreduce.map.memory.mb=3072 -Dmapreduce.job.reduces=512 -Dmapreduce.reduce.memory.mb=3072 adl://example/data/1TB-sort-input adl://example/data/1TB-sort-output
```

### <a name="teravalidate"></a>Teravalidate

```
yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teravalidate -Dmapreduce.job.maps=512 -Dmapreduce.map.memory.mb=3072 adl://example/data/1TB-sort-output adl://example/data/1TB-sort-validate
```
