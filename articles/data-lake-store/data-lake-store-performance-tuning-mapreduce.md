---
title: Afinação de desempenho do Azure Data Lake Storage Gen1 - MapReduce
description: Mapa de armazenamento de lagos de dados azure Gen1 Reduzas diretrizes de afinação de desempenho
author: stewu
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 12/19/2016
ms.author: stewu
ms.openlocfilehash: a645049665bc1d51efa94a879b9d2e4e5529282f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "73904597"
---
# <a name="performance-tuning-guidance-for-mapreduce-on-hdinsight-and-azure-data-lake-storage-gen1"></a>Orientação de afinação de desempenho para MapReduce em HDInsight e Azure Data Lake Storage Gen1

## <a name="prerequisites"></a>Pré-requisitos

* **Uma subscrição Azure.** Consulte [Obter versão de avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
* Uma conta De Armazenamento de **Lago Azure Gen1.** Para obter instruções sobre como criar um, consulte [Começar com Azure Data Lake Storage Gen1](data-lake-store-get-started-portal.md)
* **Cluster Azure HDInsight** com acesso a uma conta Gen1 de Armazenamento de Data Lake. Consulte [Criar um cluster HDInsight com Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md). Certifique-se de que ativa o Ambiente de Trabalho Remoto para o cluster.
* **Utilização do MapReduce no HDInsight**. Para mais informações, consulte [Use MapReduce in Hadoop no HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-use-mapreduce)
* **Reveja as diretrizes de afinação**de desempenho para Data Lake Storage Gen1 . Para conceitos gerais de desempenho, consulte [Data Lake Storage Gen1 Performance Tuning Guidance](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-performance-tuning-guidance)

## <a name="parameters"></a>Parâmetros

Ao executar mapReduce empregos, aqui estão os parâmetros mais importantes que você pode configurar para aumentar o desempenho em Data Lake Storage Gen1:

|Parâmetro      | Descrição  |
|---------|---------|
|`Mapreduce.map.memory.mb`  |  A quantidade de memória para alocar a cada mapeador.  |
|`Mapreduce.job.maps`     |  O número de tarefas de mapa por trabalho.  |
|`Mapreduce.reduce.memory.mb`     |  A quantidade de memória a atribuir a cada redutor.  |
|`Mapreduce.job.reduces`    |   O número de tarefas de redução por trabalho.  |

### <a name="mapreducemapmemory--mapreducereducememory"></a>Mapreduce.map.memory / Mapreduce.reduce.memory

Ajuste este número com base na quantidade de memória necessária para o mapa e/ou reduza a tarefa. Pode visualizar os `mapreduce.map.memory` valores padrão de e `mapreduce.reduce.memory` em Ambari através da configuração Do Fio. Em Ambari, navegue para o YARN e veja o separador **Configs.** A memória yARN será exibida.

### <a name="mapreducejobmaps--mapreducejobreduces"></a>Mapreduce.job.maps / Mapreduce.job.reduz

Isto determina o número máximo de mapeadores ou redutores para criar. O número de divisões determina quantos mappers são criados para o trabalho MapReduce. Portanto, você pode obter menos mappers do que pediu se houver menos divisões do que o número de mappers solicitado.

## <a name="guidance"></a>Orientação

### <a name="step-1-determine-number-of-jobs-running"></a>Passo 1: Determinar o número de postos de trabalho em funcionamento

Por padrão, o MapReduce utilizará todo o cluster para o seu trabalho. Pode utilizar menos do cluster utilizando menos mapeadores do que os recipientes disponíveis. A orientação neste documento pressupõe que a sua aplicação é a única aplicação em execução no seu cluster.

### <a name="step-2-set-mapreducemapmemorymapreducereducememory"></a>Passo 2: Definir mapreduce.map.memory/mapreduce.reduce.memory

O tamanho da memória para o mapa e reduzir tarefas será dependente do seu trabalho específico. Pode reduzir o tamanho da memória se quiser aumentar a conmoeda. O número de tarefas em funcionamento em simultâneo depende do número de contentores. Diminuindo a quantidade de memória por mapeador ou redutor, mais recipientes podem ser criados, o que permite que mais mappers ou redutores possam funcionar simultaneamente. Diminuir demasiado a quantidade de memória pode fazer com que alguns processos se esgotem da memória. Se tiver um erro de amontoado ao executar o seu trabalho, aumente a memória por mapeador ou redutor. Considere que adicionar mais recipientes adiciona sobrecarga extra para cada recipiente adicional, o que pode potencialmente degradar o desempenho. Outra alternativa é obter mais memória usando um cluster que tem maiores quantidades de memória ou aumentando o número de nós no seu cluster. Mais memória permitirá a sua vida a mais contentores, o que significa mais conmoedação.

### <a name="step-3-determine-total-yarn-memory"></a>Passo 3: Determinar a memória total do ARN

Para afinar mapreduce.job.maps/mapreduce.job.reduz, considere a quantidade total de memória yARN disponível para utilização. Esta informação está disponível em Ambari. Navegue para o YARN e veja o separador **Configs.** A memória yARN é exibida nesta janela. Multiplique a memória yARN com o número de nós no seu cluster para obter a memória total do ARN.

`Total YARN memory = nodes * YARN memory per node`

Se estiver a usar um aglomerado vazio, então a memória pode ser a memória total do YARN para o seu cluster. Se outras aplicações estiverem a utilizar a memória, então pode optar por utilizar apenas uma parte da memória do seu cluster reduzindo o número de mappers ou redutores para o número de recipientes que pretende utilizar.

### <a name="step-4-calculate-number-of-yarn-containers"></a>Passo 4: Calcular o número de recipientes de ARN

Os recipientes de ARN ditam a quantidade de moeda disponível para o trabalho. Pegue a memória total do ARN e divida-a por mapreduce.map.memory.

`# of YARN containers = total YARN memory / mapreduce.map.memory`

### <a name="step-5-set-mapreducejobmapsmapreducejobreduces"></a>Passo 5: Definir mapreduce.job.maps/mapreduce.job.reduces.reduz

Definir mapreduce.job.maps/mapreduce.job.reduz para pelo menos o número de recipientes disponíveis. Pode experimentar ainda mais aumentando o número de mapeadores e redutores para ver se obtém um melhor desempenho. Tenha em mente que mais mappers terão despesas adicionais, por isso ter muitos mappers pode degradar o desempenho.

O agendamento do CPU e o isolamento do CPU são desligados por padrão, pelo que o número de contentores de ARN é limitado pela memória.

## <a name="example-calculation"></a>Cálculo de exemplo

Digamos que tem um aglomerado composto por 8 nós D14 e quer gerir um trabalho intensivo. Aqui estão os cálculos que deve fazer:

### <a name="step-1-determine-number-of-jobs-running"></a>Passo 1: Determinar o número de postos de trabalho em funcionamento

Pelo nosso exemplo, assumimos que o nosso trabalho é o único a correr.

### <a name="step-2-set-mapreducemapmemorymapreducereducememory"></a>Passo 2: Definir mapreduce.map.memory/mapreduce.reduce.memory

Para o nosso exemplo, está a executar um trabalho intensivo de I/O e decide que 3 GB de memória para tarefas de mapas é suficiente.

`mapreduce.map.memory = 3GB`

### <a name="step-3-determine-total-yarn-memory"></a>Passo 3: Determinar a memória total do ARN

`total memory from the cluster is 8 nodes * 96GB of YARN memory for a D14 = 768GB`

### <a name="step-4-calculate--of-yarn-containers"></a>Passo 4: Calcular # dos recipientes yARN

`# of YARN containers = 768 GB of available memory / 3 GB of memory = 256`

### <a name="step-5-set-mapreducejobmapsmapreducejobreduces"></a>Passo 5: Definir mapreduce.job.maps/mapreduce.job.reduces.reduz

`mapreduce.map.jobs = 256`

## <a name="limitations"></a>Limitações

**Estrangulamento de Armazenamento de Lago de Dados Gen1**

Como um serviço multi-inquilino, data Lake Storage Gen1 estabelece limites de largura de banda de nível de conta. Se atingirestes limites, começará a ver falhas de tarefa. Isto pode ser identificado observando erros de estrangulamento nos registos de tarefas. Se precisar de mais largura de banda para o seu trabalho, por favor contacte-nos.

Para verificar se está a ser estrangulado, tem de ativar o depurador de registo sinuoso do lado do cliente. Eis como podes fazer isso:

1. Coloque a seguinte propriedade nas propriedades log4j em Ambari > YARN > Config > Advanced yarn-log4j: log4j.logger.com.microsoft.azure.datalake.store=DEBUG

2. Reinicie todos os nós/serviço para que o config faça efeito.

3. Se estiver a ser estrangulado, verá o código de erro HTTP 429 no ficheiro de registo YARN. O ficheiro de registo YARN&lt;está&gt;em /tmp/ utilizador /yarn.log

## <a name="examples-to-run"></a>Exemplos para correr

Para demonstrar como o MapReduce funciona no Data Lake Storage Gen1, o seguinte é um código de amostra que foi executado num cluster com as seguintes definições:

* 16 nó D14v2
* Aglomerado de hadoop executando HDI 3.6

Para um ponto de partida, aqui estão alguns comandos de exemplo para executar MapReduce Teragen, Terasort e Teravalidate. Pode ajustar estes comandos com base nos seus recursos.

### <a name="teragen"></a>Teragen

```
yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teragen -Dmapreduce.job.maps=2048 -Dmapreduce.map.memory.mb=3072 10000000000 adl://example/data/1TB-sort-input
```

### <a name="terasort"></a>Terasort

```
yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar terasort -Dmapreduce.job.maps=2048 -Dmapreduce.map.memory.mb=3072 -Dmapreduce.job.reduces=512 -Dmapreduce.reduce.memory.mb=3072 adl://example/data/1TB-sort-input adl://example/data/1TB-sort-output
```

### <a name="teravalidate"></a>Teravalidado

```
yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teravalidate -Dmapreduce.job.maps=512 -Dmapreduce.map.memory.mb=3072 adl://example/data/1TB-sort-output adl://example/data/1TB-sort-validate
```
