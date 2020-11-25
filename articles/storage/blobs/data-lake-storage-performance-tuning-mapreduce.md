---
title: 'Desempenho da sintonização: MapReduce, HDInsight & Azure Data Lake Storage Gen2 [ Microsoft Docs'
description: Compreenda as diretrizes para afinar o desempenho do Map Reduza os empregos na Azure Data Lake Storage Gen2.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: how-to
ms.date: 11/18/2019
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: b95d37e1725940799750dbd3c29174d9855390d6
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "95912931"
---
# <a name="tune-performance-mapreduce-hdinsight--azure-data-lake-storage-gen2"></a>Desempenho da sintonização: MapReduce, HDInsight & Azure Data Lake Storage Gen2

Compreenda os fatores que deve considerar ao sintonizar o desempenho dos trabalhos de Redução de Mapas. Este artigo abrange uma série de diretrizes de afinação de desempenho.

## <a name="prerequisites"></a>Pré-requisitos

* **Uma assinatura Azure**. Consulte [Obter versão de avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Uma conta Azure Data Lake Storage Gen2**. Para obter instruções sobre como criar um, consulte [Quickstart: Crie uma conta de armazenamento Azure Data Lake Storage Gen2](../common/storage-account-create.md).
* **Cluster Azure HDInsight** com acesso a uma conta Gen2 de armazenamento de data lake. Ver [Utilizar Azure Data Lake Storage Gen2 com clusters Azure HDInsight](../../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2.md)
* **Utilização do MapReduce no HDInsight**.  Para obter mais informações, consulte [Use MapReduce in Hadoop on HDInsight](../../hdinsight/hadoop/hdinsight-use-mapreduce.md)
* **Diretrizes de afinação de desempenho sobre data lake storage gen2**.  Para conceitos de desempenho geral, consulte [data lake storage Gen2 Performance Afinação de Afinação](data-lake-storage-performance-tuning-guidance.md)

## <a name="parameters"></a>Parâmetros

Ao executar os trabalhos do MapReduce, aqui estão os parâmetros que pode configurar para aumentar o desempenho na Data Lake Storage Gen2:

* **Mapreduce.map.memory.mb** – A quantidade de memória a alocar a cada mapper
* **Mapreduce.job.maps** – O número de tarefas do mapa por trabalho
* **Mapreduce.reduce.memory.mb** – A quantidade de memória a alocar a cada redutor
* **Mapreduce.job.reduz** - O número de tarefas de redução por trabalho

**Mapreduce.map.memory / Mapreduce.reduce.memory** Este número deve ser ajustado com base na quantidade de memória necessária para o mapa e/ou reduzir a tarefa.  Os valores predefinidos de mapreduce.map.memory e mapreduce.reduce.memory podem ser vistos em Ambari através da configuração yarn.  Em Ambari, navegue até YARN e veja o separador Configs.  A memória YARN será exibida.  

**Mapreduce.job.maps / Mapreduce.job.reduz** Isto determinará o número máximo de mappers ou redutores a criar.  O número de divisões determinará quantos mappers serão criados para o trabalho mapReduce.  Portanto, pode obter menos mappers do que pediu se houver menos divisões do que o número de mappers solicitados.       

## <a name="guidance"></a>Orientação

> [!NOTE]
> A orientação neste documento pressupõe que a sua aplicação é a única aplicação em execução no seu cluster.

**Passo 1: Determinar o número de postos de trabalho em execução**

Por predefinição, o MapReduce utilizará todo o cluster para o seu trabalho.  Pode utilizar menos do cluster usando menos mappers do que os recipientes disponíveis.        

**Passo 2: Definir mapreduce.map.memory/mapreduce.reduce.reduce.memory**

O tamanho da memória para mapa e reduzir tarefas será dependente do seu trabalho específico.  Pode reduzir o tamanho da memória se quiser aumentar a conúcência.  O número de tarefas em execução simultânea depende do número de contentores.  Ao diminuir a quantidade de memória por mapeador ou redutor, podem ser criados mais contentores, que permitem que mais mappers ou redutores sejam executados simultaneamente.  Diminuir demasiado a quantidade de memória pode fazer com que alguns processos fiquem sem memória.  Se tiver um erro de heap ao executar o seu trabalho, deve aumentar a memória por mapeador ou redutor.  Deve considerar que a adição de mais recipientes irá adicionar uma sobrecarga extra para cada recipiente adicional, o que pode potencialmente degradar o desempenho.  Outra alternativa é obter mais memória usando um cluster que tenha maiores quantidades de memória ou aumentando o número de nós no seu cluster.  Mais memória permitirá a utilização de mais recipientes, o que significa mais concordância.  

**Passo 3: Determinar a memória total do YARN**

Para sintonizar mapreduce.job.maps/mapreduce.job.reduz, deve considerar a quantidade total de memória YARN disponível para utilização.  Esta informação está disponível em Ambari.  Navegue até YARN e veja o separador Configs.  A memória YARN é apresentada nesta janela.  Deve multiplicar a memória YARN com o número de nós no seu agrupamento para obter a memória total do YARN.

Total da memória YARN = nós * memória YARN por nó

Se estiver a utilizar um aglomerado vazio, então a memória pode ser a memória total do YARN para o seu cluster.  Se outras aplicações estiverem a usar a memória, então pode optar por utilizar apenas uma parte da memória do seu cluster, reduzindo o número de mappers ou redutores para o número de recipientes que pretende utilizar.  

**Passo 4: Calcular o número de contentores YARN**

Os contentores de YARN ditam a quantidade de concordância disponível para o trabalho.  Pegue a memória total do YARN e divida-a por mapreduce.map.memory.  

\# de contentores YARN = memória total de YARN / mapreduce.map.memory

**Passo 5: Definir mapreduce.job.maps/mapreduce.job.reduz**

Definir mapreduce.job.maps/mapreduce.job.reduz para pelo menos o número de contentores disponíveis.  Você pode experimentar mais, aumentando o número de mappers e redutores para ver se você obtém um melhor desempenho.  Tenha em mente que mais mappers terão sobrecargas adicionais para que ter muitos mappers possa degradar o desempenho.  

O agendamento do CPU e o isolamento da CPU são desligados por defeito, pelo que o número de contentores de YARN é limitado pela memória.

## <a name="example-calculation"></a>Cálculo de exemplo

Vamos assumir que temos um cluster composto por nós 8 D14, e queremos executar um trabalho intensivo de E/S.  Aqui estão os cálculos que deve fazer:

**Passo 1: Determinar o número de postos de trabalho em execução**

Neste exemplo, vamos assumir que o nosso trabalho é o único trabalho que está a funcionar.  

**Passo 2: Definir mapreduce.map.memory/mapreduce.reduce.reduce.memory**

Neste exemplo, estamos a executar um trabalho intensivo de E/S e decidimos que 3GB de memória para tarefas de mapas serão suficientes.

mapreduce.map.memory = 3GB

**Passo 3: Determinar a memória total do YARN**

A memória total do cluster é de 8 nóns * 96GB de memória YARN para um D14 = 768GB

**Passo 4: Calcular # de recipientes YARN**

\# de recipientes YARN = 768GB de memória disponível / 3 GB de memória = 256

**Passo 5: Definir mapreduce.job.maps/mapreduce.job.reduz**

mapreduce.map.jobs = 256

## <a name="examples-to-run"></a>Exemplos a executar

Para demonstrar como o MapReduce funciona na Data Lake Storage Gen2, abaixo está um código de amostra que foi executado num cluster com as seguintes definições:

* 16 nó D14v2
* Aglomerado de Hadoop executando HDI 3.6

Para um ponto de partida, aqui estão alguns comandos de exemplo para executar MapReduce Teragen, Terasort e Teravalidate.  Pode ajustar estes comandos com base nos seus recursos.

**Teragen**

```cmd
yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teragen -Dmapreduce.job.maps=2048 -Dmapreduce.map.memory.mb=3072 10000000000 abfs://example/data/1TB-sort-input
```

**Rio Terasort**

```cmd
yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar terasort -Dmapreduce.job.maps=2048 -Dmapreduce.map.memory.mb=3072 -Dmapreduce.job.reduces=512 -Dmapreduce.reduce.memory.mb=3072 abfs://example/data/1TB-sort-input abfs://example/data/1TB-sort-output
```

**Teravalidate**

```cmd
yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teravalidate -Dmapreduce.job.maps=512 -Dmapreduce.map.memory.mb=3072 abfs://example/data/1TB-sort-output abfs://example/data/1TB-sort-validate
```