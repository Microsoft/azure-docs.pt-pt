---
title: Azure Data Lake Gen2 MapReduce o desempenho do armazenamento diretrizes de ajuste | Documentos da Microsoft
description: Azure Data Lake Gen2 MapReduce o desempenho do armazenamento diretrizes de ajuste
services: storage
author: swums
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: stewu
ms.openlocfilehash: 1df7765f4f006d3d79f867fbf23929f6b9b0cf08
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "62127162"
---
# <a name="performance-tuning-guidance-for-mapreduce-on-hdinsight-and-azure-data-lake-storage-gen2"></a>Guia para o MapReduce no HDInsight e geração 2 de armazenamento do Azure Data Lake de sintonização de desempenho

Compreenda os fatores que deve considerar quando ajustar o desempenho das tarefas de reduzir o mapa. Este artigo cobre uma variedade de diretrizes de ajuste de desempenho.

## <a name="prerequisites"></a>Pré-requisitos

* **Uma subscrição do Azure**. Consulte [Obter uma avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Uma conta de geração 2 de armazenamento do Azure Data Lake**. Para obter instruções sobre como criar um, consulte [início rápido: Criar uma conta de armazenamento de geração 2 de armazenamento do Azure Data Lake](data-lake-storage-quickstart-create-account.md).
* **Cluster de HDInsight do Azure** com acesso a uma conta de geração 2 de armazenamento do Data Lake. Consulte [clusters de utilização do Azure Data Lake Storage Gen2 com o Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2)
* **Utilizar o MapReduce no HDInsight**.  Para obter mais informações, consulte [utilizar o MapReduce no Hadoop no HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-use-mapreduce)
* **Diretrizes de geração 2 de armazenamento do Data Lake de ajuste de desempenho**.  Para os conceitos gerais de desempenho, consulte [Data Lake Storage Gen2 ajuste orientação de desempenho](data-lake-storage-performance-tuning-guidance.md)

## <a name="parameters"></a>Parâmetros

Ao executar tarefas de MapReduce, aqui estão os parâmetros que pode configurar para melhorar o desempenho na geração 2 de armazenamento do Data Lake:

* **Mapreduce.Map.Memory.MB** – a quantidade de memória para atribuir a cada mapeador de pontos
* **Mapreduce.job.Maps** – o número de tarefas de mapa por tarefa
* **Mapreduce.reduce.Memory.MB** – a quantidade de memória para atribuir a cada reducer
* **Mapreduce.job.reduces** – o número de tarefas de reduza por tarefa

**Mapreduce.Map.Memory / Mapreduce.reduce.memory** este número deve ser ajustado com base na quantidade de memória é necessária para o mapa e/ou reduzir a tarefa.  Os valores predefinidos de mapreduce.map.memory e mapreduce.reduce.memory podem ser visualizados no Ambari através da configuração do Yarn.  No Ambari, navegue para o YARN e ver a guia configurações.  Será apresentada a memória YARN.  

**Mapreduce.job.Maps / Mapreduce.job.reduces** isso determinará o número máximo de mapeadores ou redutores a ser criada.  O número de divisões de irá determinar quantos mapeadores serão criados para a tarefa de MapReduce.  Por conseguinte, poderá obter menos mapeadores que pediu se existem menos divisões que o número de mapeadores solicitado.       

## <a name="guidance"></a>Orientação

> [!NOTE]
> As orientações neste documento parte do princípio de que seu aplicativo é o único aplicativo em execução no seu cluster.

**Passo 1: Determinar o número de tarefas em execução**

Por predefinição, o MapReduce irá utilizar todo o cluster para a sua tarefa.  Pode utilizar o menor de cluster, utilizando menos mapeadores que existem contentores disponíveis.        

**Passo 2: Definir mapreduce.map.memory/mapreduce.reduce.memory**

O tamanho da memória para o mapa e reduzir tarefas irão estar dependentes de seu trabalho específico.  Se pretender aumentar a simultaneidade, pode reduzir o tamanho da memória.  O número de tarefas em execução em simultâneo depende do número de contentores.  Ao reduzir a quantidade de memória por mapeador ou reducer, podem ser criadas mais contentores, que permitem mais mapeadores ou redutores para execução simultânea.  Diminuir a quantidade de memória muito pode fazer com que alguns processos a ficar sem memória.  Se obtiver um erro de área dinâmica para dados ao executar seu trabalho, deve aumentar a memória por mapeador ou reducer.  Deve considerar que a adição de mais contentores irá adicionar extra gerais para cada contentor adicional, que potencialmente pode degradar o desempenho.  Outra alternativa é obter mais memória ao utilizar um cluster com a maior quantidade de memória ou aumentar o número de nós do cluster.  Mais memória permitirá mais contentores a ser usado, que significa mais simultaneidade.  

**Passo 3: Determinar a memória de Total YARN**

Para otimizar mapreduce.job.maps/mapreduce.job.reduces, deve considerar a quantidade de memória YARN total disponível para utilização.  Esta informação está disponível em Ambari.  Navegue para o YARN e ver a guia configurações.  A memória YARN é apresentada nesta janela.  Deve multiplicar a memória YARN com o número de nós do cluster para obter a memória YARN total.

    Total YARN memory = nodes * YARN memory per node

Se estiver a utilizar um cluster vazio, memória pode ser o total de memória YARN do seu cluster.  Se outros aplicativos estiver usando a memória, em seguida, pode optar por utilizar apenas uma parte da memória do seu cluster, reduzindo o número de mapeadores ou redutores para o número de contentores que pretende utilizar.  

**Passo 4: Calcular o número de contentores YARN**

Contentores YARN ditam a quantidade de simultaneidade disponível para a tarefa.  Tire total de memória YARN e divido isso por mapreduce.map.memory.  

    # of YARN containers = total YARN memory / mapreduce.map.memory

**Passo 5: Definir mapreduce.job.maps/mapreduce.job.reduces**

Definir mapreduce.job.maps/mapreduce.job.reduces para, pelo menos, o número de contentores disponíveis.  Pode experimentar adicional do aumento do número de mapeadores e redutores para ver se consegue obter um melhor desempenho.  Tenha em atenção que mais mapeadores terão a sobrecarga adicional, de modo a ter demasiadas mapeadores pode degradar o desempenho.  

Agendamento de CPU e o isolamento de CPU são desativados por predefinição, para que o número de contentores YARN é restrita por memória.

## <a name="example-calculation"></a>Cálculo de exemplo

Vamos supor que temos um composto por 8 D14 nós de cluster, e queremos executar uma tarefa intensiva da e/s.  Seguem-se os cálculos que deve fazer:

**Passo 1: Determinar o número de tarefas em execução**

Neste exemplo, vamos supor que o nosso trabalho é a única tarefa que está a executar.  

**Passo 2: Definir mapreduce.map.memory/mapreduce.reduce.memory**

Neste exemplo, estiver a executar uma tarefa intensiva da e/s e decidir que 3GB de memória para tarefas de mapa será suficiente.

    mapreduce.map.memory = 3GB

**Passo 3: Determinar a memória de Total YARN**

    Total memory from the cluster is 8 nodes * 96GB of YARN memory for a D14 = 768GB
**Passo 4: Calcular o n. º de contentores YARN**

    # of YARN containers = 768GB of available memory / 3 GB of memory =   256

**Passo 5: Definir mapreduce.job.maps/mapreduce.job.reduces**

    mapreduce.map.jobs = 256

## <a name="examples-to-run"></a>Exemplos para executar

Para demonstrar como o MapReduce é executado na geração 2 de armazenamento do Data Lake, segue-se alguns exemplos de código que foi executado num cluster com as seguintes definições:

* nó de 16 D14v2
* Cluster do Hadoop em execução HDI 3.6

Para um ponto de partida, eis alguns comandos de exemplo para executar o MapReduce Teragen Terasort e Teravalidate.  Pode ajustar esses comandos com base nos seus recursos.

**Teragen**

    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teragen -Dmapreduce.job.maps=2048 -Dmapreduce.map.memory.mb=3072 10000000000 abfs://example/data/1TB-sort-input

**Terasort**

    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar terasort -Dmapreduce.job.maps=2048 -Dmapreduce.map.memory.mb=3072 -Dmapreduce.job.reduces=512 -Dmapreduce.reduce.memory.mb=3072 abfs://example/data/1TB-sort-input abfs://example/data/1TB-sort-output

**Teravalidate**

    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teravalidate -Dmapreduce.job.maps=512 -Dmapreduce.map.memory.mb=3072 abfs://example/data/1TB-sort-output abfs://example/data/1TB-sort-validate
