---
title: 'Ajustar o desempenho: MapReduce, HDInsight & Azure Data Lake Storage Gen2 | Microsoft Docs'
description: Diretrizes de ajuste de desempenho Azure Data Lake Storage Gen2 MapReduce
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 11/18/2019
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: a3ea6858355d6cb921f629bf36134d96371f6244
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74327916"
---
# <a name="tune-performance-mapreduce-hdinsight--azure-data-lake-storage-gen2"></a>Ajustar o desempenho: MapReduce, & HDInsight Azure Data Lake Storage Gen2

Entenda os fatores que você deve considerar ao ajustar o desempenho dos trabalhos de redução de mapa. Este artigo aborda uma variedade de diretrizes de ajuste de desempenho.

## <a name="prerequisites"></a>Pré-requisitos

* **Uma subscrição do Azure**. Consulte [Obter versão de avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Uma conta de Azure data Lake Storage Gen2**. Para obter instruções sobre como criar uma, consulte [início rápido: criar uma conta de armazenamento Azure data Lake Storage Gen2](data-lake-storage-quickstart-create-account.md).
* **Cluster HDInsight do Azure** com acesso a uma conta de data Lake Storage Gen2. Confira [usar o Azure data Lake Storage Gen2 com clusters do Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2)
* **Usando o MapReduce no HDInsight**.  Para obter mais informações, consulte [usar o MapReduce no Hadoop no HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-use-mapreduce)
* **Diretrizes de ajuste de desempenho em data Lake Storage Gen2**.  Para obter conceitos gerais de desempenho, consulte [diretrizes de ajuste de desempenho data Lake Storage Gen2](data-lake-storage-performance-tuning-guidance.md)

## <a name="parameters"></a>Parâmetros

Ao executar trabalhos do MapReduce, aqui estão os parâmetros que você pode configurar para aumentar o desempenho no Data Lake Storage Gen2:

* **MapReduce. map. Memory. MB** – a quantidade de memória a ser alocada a cada mapeador
* **MapReduce. Job. Maps** – o número de tarefas de mapa por trabalho
* **MapReduce. Reduza. Memory. MB** – a quantidade de memória a ser alocada a cada redutor
* **MapReduce. Job. reduz** – o número de tarefas de redução por trabalho

**MapReduce. map. Memory/MapReduce. reduzte. Memory** Esse número deve ser ajustado com base na quantidade de memória necessária para o mapa e/ou para a tarefa de redução.  Os valores padrão de MapReduce. map. Memory e MapReduce. reduzte. Memory podem ser exibidos em Ambari por meio da configuração do yarn.  Em Ambari, navegue até YARN e exiba a guia Configurações.  A memória YARN será exibida.  

**MapReduce. Job. Maps/MapReduce. Job. reduz** Isso determinará o número máximo de Mapeadores ou redutores a serem criados.  O número de divisões determinará quantos Mapeadores serão criados para o trabalho MapReduce.  Portanto, você pode obter menos Mapeadores do que o solicitado se houver menos divisões do que o número de Mapeadores solicitados.       

## <a name="guidance"></a>Orientação

> [!NOTE]
> As diretrizes neste documento pressupõem que seu aplicativo é o único aplicativo em execução no cluster.

**Etapa 1: determinar o número de trabalhos em execução**

Por padrão, o MapReduce usará o cluster inteiro para seu trabalho.  Você pode usar menos do cluster usando menos Mapeadores do que os contêineres disponíveis.        

**Etapa 2: definir MapReduce. map. Memory/MapReduce. reduzte. Memory**

O tamanho da memória para tarefas de mapeamento e redução será dependente de seu trabalho específico.  Você pode reduzir o tamanho da memória se quiser aumentar a simultaneidade.  O número de tarefas em execução simultânea depende do número de contêineres.  Ao reduzir a quantidade de memória por mapeador ou redutor, mais contêineres podem ser criados, o que permite que mais Mapeadores ou redutores sejam executados simultaneamente.  Diminuir a quantidade de memória pode fazer com que alguns processos fique sem memória.  Se você receber um erro de heap ao executar seu trabalho, deverá aumentar a memória por mapeador ou redutor.  Você deve considerar que a adição de mais contêineres adicionará sobrecarga extra a cada contêiner adicional, o que pode prejudicar o desempenho.  Outra alternativa é obter mais memória usando um cluster com quantidades maiores de memória ou aumentando o número de nós no cluster.  Mais memória permitirá que mais contêineres sejam usados, o que significa mais simultaneidade.  

**Etapa 3: determinar a memória total do YARN**

Para ajustar MapReduce. Job. Maps/MapReduce. Job. Reduces, você deve considerar a quantidade total de memória YARN disponível para uso.  Essas informações estão disponíveis em Ambari.  Navegue até YARN e exiba a guia Configurações.  A memória YARN é exibida nesta janela.  Você deve multiplicar a memória YARN com o número de nós no seu cluster para obter a memória total do YARN.

    Total YARN memory = nodes * YARN memory per node

Se você estiver usando um cluster vazio, a memória poderá ser a memória YARN total para o cluster.  Se outros aplicativos estiverem usando memória, você poderá optar por usar apenas uma parte da memória do cluster reduzindo o número de Mapeadores ou redutores para o número de contêineres que você deseja usar.  

**Etapa 4: calcular o número de contêineres YARN**

Os contêineres YARN ditam a quantidade de simultaneidade disponível para o trabalho.  Pegue a memória YARN total e divida-a por MapReduce. map. Memory.  

    # of YARN containers = total YARN memory / mapreduce.map.memory

**Etapa 5: definir MapReduce. Job. Maps/MapReduce. Job. Reduces**

Defina MapReduce. Job. Maps/MapReduce. Job. reduz para pelo menos o número de contêineres disponíveis.  Você pode fazer experiências mais adiante aumentando o número de Mapeadores e redutores para ver se você obtém um melhor desempenho.  Tenha em mente que mais Mapeadores terão sobrecarga adicional, portanto, ter muitos Mapeadores pode prejudicar o desempenho.  

O agendamento da CPU e o isolamento da CPU são desativados por padrão, portanto, o número de contêineres YARN é restrito pela memória.

## <a name="example-calculation"></a>Exemplo de cálculo

Vamos supor que temos um cluster composto de 8 nós D14 e queremos executar um trabalho com uso intensivo de e/s.  Aqui estão os cálculos que você deve fazer:

**Etapa 1: determinar o número de trabalhos em execução**

Neste exemplo, vamos supor que nosso trabalho é o único trabalho que está em execução.  

**Etapa 2: definir MapReduce. map. Memory/MapReduce. reduzte. Memory**

Neste exemplo, estamos executando um trabalho com uso intensivo de e/s e decidimos que 3GB de memória para tarefas de mapeamento serão suficientes.

    mapreduce.map.memory = 3GB

**Etapa 3: determinar a memória total do YARN**

    Total memory from the cluster is 8 nodes * 96GB of YARN memory for a D14 = 768GB
**Etapa 4: calcular o n º de contêineres YARN**

    # of YARN containers = 768GB of available memory / 3 GB of memory =   256

**Etapa 5: definir MapReduce. Job. Maps/MapReduce. Job. Reduces**

    mapreduce.map.jobs = 256

## <a name="examples-to-run"></a>Exemplos a serem executados

Para demonstrar como o MapReduce é executado em Data Lake Storage Gen2, abaixo está um código de exemplo que foi executado em um cluster com as seguintes configurações:

* 16 nós D14v2
* Cluster Hadoop executando HDI 3,6

Para um ponto de partida, aqui estão alguns comandos de exemplo para executar MapReduce Teragen, Terasort e Teravalidate.  Você pode ajustar esses comandos com base em seus recursos.

**Teragen**

    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teragen -Dmapreduce.job.maps=2048 -Dmapreduce.map.memory.mb=3072 10000000000 abfs://example/data/1TB-sort-input

**Terasort**

    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar terasort -Dmapreduce.job.maps=2048 -Dmapreduce.map.memory.mb=3072 -Dmapreduce.job.reduces=512 -Dmapreduce.reduce.memory.mb=3072 abfs://example/data/1TB-sort-input abfs://example/data/1TB-sort-output

**Teravalidate**

    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teravalidate -Dmapreduce.job.maps=512 -Dmapreduce.map.memory.mb=3072 abfs://example/data/1TB-sort-output abfs://example/data/1TB-sort-validate
