---
title: 'Desempenho de sintonização: MapReduce, HDInsight & Azure Data Lake Storage Gen2 [ Microsoft Docs'
description: Mapa de armazenamento de lagos de dados azure Gen2 Reduzas diretrizes de afinação de desempenho
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 11/18/2019
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: a3ea6858355d6cb921f629bf36134d96371f6244
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74327916"
---
# <a name="tune-performance-mapreduce-hdinsight--azure-data-lake-storage-gen2"></a>Desempenho de sintonização: MapReduce, HDInsight & Azure Data Lake Storage Gen2

Compreenda os fatores que deve considerar quando afinar o desempenho do Map Reduce jobs. Este artigo abrange uma série de diretrizes de afinação de desempenho.

## <a name="prerequisites"></a>Pré-requisitos

* **Uma subscrição Azure.** Consulte [Obter versão de avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
* Uma conta De Armazenamento de **Lago Azure Gen2.** Para obter instruções sobre como criar uma, consulte [Quickstart: Crie uma conta](data-lake-storage-quickstart-create-account.md)de armazenamento de Gen2 de armazenamento de lago de dados Azure .
* **Cluster Azure HDInsight** com acesso a uma conta Gen2 de Armazenamento de Data Lake. Ver [Utilização Azure Data Lake Storage Gen2 com clusters Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2)
* **Utilização do MapReduce no HDInsight**.  Para mais informações, consulte [Use MapReduce in Hadoop no HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-use-mapreduce)
* **Diretrizes de afinação de desempenho sobre data lake storage Gen2**.  Para conceitos gerais de desempenho, consulte [Data Lake Storage Gen2 Performance Tuning Guidance](data-lake-storage-performance-tuning-guidance.md)

## <a name="parameters"></a>Parâmetros

Ao executar mapReduce empregos, aqui estão os parâmetros que você pode configurar para aumentar o desempenho em Data Lake Storage Gen2:

* **Mapreduce.map.memory.mb** – A quantidade de memória a atribuir a cada mapper
* **Mapreduce.job.maps** – O número de tarefas de mapas por trabalho
* **Mapreduce.reduce.memory.mb** – A quantidade de memória a atribuir a cada redutor
* **Mapreduce.job.reduz** – O número de tarefas de redução por trabalho

**Mapreduce.map.memory / Mapreduce.reduce.memory** Este número deve ser ajustado com base na quantidade de memória necessária para o mapa e/ou reduzir a tarefa.  Os valores padrão de mapreduce.map.memory e mapreduce.reduce.memory podem ser vistos em Ambari através da configuração do Fio.  Em Ambari, navegue para o YARN e veja o separador Configs.  A memória yARN será exibida.  

**Mapreduce.job.maps / Mapreduce.job.reduz** Isto determinará o número máximo de mapeadores ou redutores a criar.  O número de divisões determinará quantos mappers serão criados para o trabalho MapReduce.  Portanto, você pode obter menos mappers do que pediu se houver menos divisões do que o número de mappers solicitado.       

## <a name="guidance"></a>Orientação

> [!NOTE]
> A orientação neste documento pressupõe que a sua aplicação é a única aplicação em execução no seu cluster.

**Passo 1: Determinar o número de postos de trabalho em funcionamento**

Por padrão, o MapReduce utilizará todo o cluster para o seu trabalho.  Pode utilizar menos do cluster utilizando menos mapeadores do que os recipientes disponíveis.        

**Passo 2: Definir mapreduce.map.memory/mapreduce.reduce.memory**

O tamanho da memória para o mapa e reduzir tarefas será dependente do seu trabalho específico.  Pode reduzir o tamanho da memória se quiser aumentar a conmoeda.  O número de tarefas em funcionamento em simultâneo depende do número de contentores.  Diminuindo a quantidade de memória por mapeador ou redutor, mais recipientes podem ser criados, o que permite que mais mappers ou redutores possam funcionar simultaneamente.  Diminuir demasiado a quantidade de memória pode fazer com que alguns processos se esgotem da memória.  Se tiver um erro de amontoado ao executar o seu trabalho, deve aumentar a memória por mapeador ou redutor.  Deve considerar que adicionar mais contentores irá adicionar sobrecarga extra para cada recipiente adicional, o que pode potencialmente degradar o desempenho.  Outra alternativa é obter mais memória usando um cluster que tem maiores quantidades de memória ou aumentando o número de nós no seu cluster.  Mais memória permitirá a sua vida a mais contentores, o que significa mais conmoedação.  

**Passo 3: Determinar a memória total do FIO**

Para afinar mapreduce.job.maps/mapreduce.job.reduz, deve considerar a quantidade total de memória yARN disponível para utilização.  Esta informação está disponível em Ambari.  Navegue para o YARN e veja o separador Configs.  A memória yARN é exibida nesta janela.  Deve multiplicar a memória yARN com o número de nós no seu cluster para obter a memória total do ARN.

    Total YARN memory = nodes * YARN memory per node

Se estiver a usar um aglomerado vazio, então a memória pode ser a memória total do ARN para o seu cluster.  Se outras aplicações estiverem a utilizar a memória, então pode optar por utilizar apenas uma parte da memória do seu cluster reduzindo o número de mappers ou redutores para o número de recipientes que pretende utilizar.  

**Passo 4: Calcular o número de recipientes de ARN**

Os recipientes de ARN ditam a quantidade de moeda disponível para o trabalho.  Pegue a memória total do ARN e divida-a por mapreduce.map.memory.  

    # of YARN containers = total YARN memory / mapreduce.map.memory

**Passo 5: Definir mapreduce.job.maps/mapreduce.job.reduces.reduz**

Definir mapreduce.job.maps/mapreduce.job.reduz para pelo menos o número de recipientes disponíveis.  Pode experimentar ainda mais aumentando o número de mapeadores e redutores para ver se obtém um melhor desempenho.  Tenha em mente que mais mappers terão despesas adicionais, por isso ter muitos mappers pode degradar o desempenho.  

O agendamento do CPU e o isolamento do CPU são desligados por padrão, pelo que o número de contentores de ARN é limitado pela memória.

## <a name="example-calculation"></a>Cálculo de exemplo

Vamos supor que temos um aglomerado composto por 8 nós D14, e queremos executar um trabalho intensivo de I/O.  Aqui estão os cálculos que deve fazer:

**Passo 1: Determinar o número de postos de trabalho em funcionamento**

Neste exemplo, vamos supor que o nosso trabalho é o único trabalho que está a funcionar.  

**Passo 2: Definir mapreduce.map.memory/mapreduce.reduce.memory**

Neste exemplo, estamos a executar um trabalho intensivo de I/O e decidimos que 3GB de memória para tarefas de mapas serão suficientes.

    mapreduce.map.memory = 3GB

**Passo 3: Determinar a memória total do FIO**

    Total memory from the cluster is 8 nodes * 96GB of YARN memory for a D14 = 768GB
**Passo 4: Calcular # dos recipientes yARN**

    # of YARN containers = 768GB of available memory / 3 GB of memory =   256

**Passo 5: Definir mapreduce.job.maps/mapreduce.job.reduces.reduz**

    mapreduce.map.jobs = 256

## <a name="examples-to-run"></a>Exemplos para correr

Para demonstrar como o MapReduce funciona no Data Lake Storage Gen2, abaixo está algum código de amostra que foi executado em um cluster com as seguintes configurações:

* 16 nó D14v2
* Aglomerado de hadoop executando HDI 3.6

Para um ponto de partida, aqui estão alguns comandos de exemplo para executar MapReduce Teragen, Terasort e Teravalidate.  Pode ajustar estes comandos com base nos seus recursos.

**Teragen**

    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teragen -Dmapreduce.job.maps=2048 -Dmapreduce.map.memory.mb=3072 10000000000 abfs://example/data/1TB-sort-input

**Terasort**

    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar terasort -Dmapreduce.job.maps=2048 -Dmapreduce.map.memory.mb=3072 -Dmapreduce.job.reduces=512 -Dmapreduce.reduce.memory.mb=3072 abfs://example/data/1TB-sort-input abfs://example/data/1TB-sort-output

**Teravalidado**

    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teravalidate -Dmapreduce.job.maps=512 -Dmapreduce.map.memory.mb=3072 abfs://example/data/1TB-sort-output abfs://example/data/1TB-sort-validate
