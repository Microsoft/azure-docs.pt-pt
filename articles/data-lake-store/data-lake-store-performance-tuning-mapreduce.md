---
title: Azure Data Lake Storage Gen1 afinação de desempenho - MapReduce
description: Saiba mais sobre afinação de desempenho para MapReduce em Azure Data Lake Storage Gen1, incluindo parâmetros, orientação, um cálculo de exemplo e limitações.
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 12/19/2016
ms.author: twooley
ms.openlocfilehash: 2549413241e422fb1e0e5e1f079c287e0b7cf005
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "97723733"
---
# <a name="performance-tuning-guidance-for-mapreduce-on-hdinsight-and-azure-data-lake-storage-gen1"></a>Orientação de afinação de desempenho para MapReduce em HDInsight e Azure Data Lake Storage Gen1

## <a name="prerequisites"></a>Pré-requisitos

* **Uma assinatura Azure**. Consulte [Obter versão de avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Uma conta Azure Data Lake Storage Gen1**. Para instruções sobre como criar um, consulte [Começar com a Azure Data Lake Storage Gen1](data-lake-store-get-started-portal.md)
* **Cluster Azure HDInsight** com acesso a uma conta Gen1 de armazenamento de data lake. Consulte [Criar um cluster HDInsight com data lake storage gen1](data-lake-store-hdinsight-hadoop-use-portal.md). Certifique-se de que ativa o Ambiente de Trabalho Remoto para o cluster.
* **Utilização do MapReduce no HDInsight**. Para obter mais informações, consulte [Use MapReduce in Hadoop on HDInsight](../hdinsight/hadoop/hdinsight-use-mapreduce.md)
* **Reveja as diretrizes de afinação de desempenho para data lake storage gen1**. Para conceitos de desempenho geral, consulte [data lake storage Gen1 Performance Afinação de Afinação](./data-lake-store-performance-tuning-guidance.md)

## <a name="parameters"></a>Parâmetros

Ao executar os trabalhos do MapReduce, aqui estão os parâmetros mais importantes que pode configurar para aumentar o desempenho na Data Lake Storage Gen1:

|Parâmetro      | Descrição  |
|---------|---------|
|`Mapreduce.map.memory.mb`  |  A quantidade de memória a alocar a cada mapper.  |
|`Mapreduce.job.maps`     |  O número de tarefas de mapa por trabalho.  |
|`Mapreduce.reduce.memory.mb`     |  A quantidade de memória a alocar a cada redutor.  |
|`Mapreduce.job.reduces`    |   O número de tarefas de redução por trabalho.  |

### <a name="mapreducemapmemory--mapreducereducememory"></a>Mapreduce.map.memory / Mapreduce.reduce.memory

Ajuste este número com base na quantidade de memória necessária para o mapa e/ou reduza a tarefa. Pode ver os valores padrão de `mapreduce.map.memory` e `mapreduce.reduce.memory` em Ambari através da configuração yarn. Em Ambari, navegue até YARN e veja o **separador Configs.** A memória YARN será exibida.

### <a name="mapreducejobmaps--mapreducejobreduces"></a>Mapreduce.job.maps / Mapreduce.job.reduz

Isto determina o número máximo de mappers ou redutores para criar. O número de divisões determina quantos mappers são criados para o trabalho mapReduce. Portanto, pode obter menos mappers do que pediu se houver menos divisões do que o número de mappers solicitados.

## <a name="guidance"></a>Orientação

### <a name="step-1-determine-number-of-jobs-running"></a>Passo 1: Determinar o número de postos de trabalho em execução

Por predefinição, o MapReduce utilizará todo o cluster para o seu trabalho. Pode utilizar menos do cluster usando menos mappers do que os contentores disponíveis. A orientação neste documento pressupõe que a sua aplicação é a única aplicação em execução no seu cluster.

### <a name="step-2-set-mapreducemapmemorymapreducereducememory"></a>Passo 2: Definir mapreduce.map.memory/mapreduce.reduce.reduce.memory

O tamanho da memória para mapa e reduzir tarefas será dependente do seu trabalho específico. Pode reduzir o tamanho da memória se quiser aumentar a conúcência. O número de tarefas em execução simultânea depende do número de contentores. Ao diminuir a quantidade de memória por mapeador ou redutor, podem ser criados mais contentores, que permitem que mais mappers ou redutores sejam executados simultaneamente. Diminuir demasiado a quantidade de memória pode fazer com que alguns processos fiquem sem memória. Se tiver um erro de heap ao executar o seu trabalho, aumente a memória por mapeador ou redutor. Considere que adicionar mais recipientes adiciona sobrecarga extra para cada recipiente adicional, o que pode potencialmente degradar o desempenho. Outra alternativa é obter mais memória usando um cluster que tenha maiores quantidades de memória ou aumentando o número de nós no seu cluster. Mais memória permitirá a utilização de mais recipientes, o que significa mais concordância.

### <a name="step-3-determine-total-yarn-memory"></a>Passo 3: Determinar a memória total do YARN

Para sintonizar mapreduce.job.maps/mapreduce.job.reduz, considere a quantidade total de memória YARN disponível para utilização. Esta informação está disponível em Ambari. Navegue até YARN e veja o **separador Configs.** A memória YARN é apresentada nesta janela. Multiplique a memória YARN com o número de nós no seu cluster para obter a memória total de YARN.

`Total YARN memory = nodes * YARN memory per node`

Se estiver a usar um aglomerado vazio, então a memória pode ser a memória total do YARN para o seu cluster. Se outras aplicações estiverem a usar a memória, então pode optar por utilizar apenas uma parte da memória do seu cluster, reduzindo o número de mappers ou redutores para o número de recipientes que pretende utilizar.

### <a name="step-4-calculate-number-of-yarn-containers"></a>Passo 4: Calcular o número de contentores YARN

Os contentores de YARN ditam a quantidade de concordância disponível para o trabalho. Pegue a memória total do YARN e divida-a por mapreduce.map.memory.

`# of YARN containers = total YARN memory / mapreduce.map.memory`

### <a name="step-5-set-mapreducejobmapsmapreducejobreduces"></a>Passo 5: Definir mapreduce.job.maps/mapreduce.job.reduz

Definir mapreduce.job.maps/mapreduce.job.reduz para pelo menos o número de contentores disponíveis. Você pode experimentar mais, aumentando o número de mappers e redutores para ver se você obtém um melhor desempenho. Tenha em mente que mais mappers terão sobrecargas adicionais para que ter muitos mappers possa degradar o desempenho.

O agendamento do CPU e o isolamento da CPU são desligados por defeito, pelo que o número de contentores de YARN é limitado pela memória.

## <a name="example-calculation"></a>Cálculo de exemplo

Digamos que tem um cluster composto por nós 8 D14 e quer executar um trabalho intensivo de E/S. Aqui estão os cálculos que deve fazer:

### <a name="step-1-determine-number-of-jobs-running"></a>Passo 1: Determinar o número de postos de trabalho em execução

Pelo nosso exemplo, assumimos que o nosso trabalho é o único a correr.

### <a name="step-2-set-mapreducemapmemorymapreducereducememory"></a>Passo 2: Definir mapreduce.map.memory/mapreduce.reduce.reduce.memory

Para o nosso exemplo, está a executar um trabalho intensivo de E/S e decide que 3 GB de memória para tarefas de mapa é suficiente.

`mapreduce.map.memory = 3GB`

### <a name="step-3-determine-total-yarn-memory"></a>Passo 3: Determinar a memória total do YARN

`total memory from the cluster is 8 nodes * 96GB of YARN memory for a D14 = 768GB`

### <a name="step-4-calculate--of-yarn-containers"></a>Passo 4: Calcular # de recipientes YARN

`# of YARN containers = 768 GB of available memory / 3 GB of memory = 256`

### <a name="step-5-set-mapreducejobmapsmapreducejobreduces"></a>Passo 5: Definir mapreduce.job.maps/mapreduce.job.reduz

`mapreduce.map.jobs = 256`

## <a name="limitations"></a>Limitações

**Estrangulamento da Gen1 de armazenamento de dados**

Como um serviço multi-inquilino, data lake storage Gen1 define limites de largura de banda de nível de conta. Se atingir estes limites, começará a ver falhas de tarefas. Isto pode ser identificado observando erros de estrangulamento em registos de tarefas. Se precisar de mais largura de banda para o seu trabalho, contacte-nos.

Para verificar se está a ser estrangulado, tem de ativar o registo de depuragem do lado do cliente. Eis como podes fazer isso:

1. Coloque a seguinte propriedade nas propriedades log4j em Ambari > YARN > Config > Advanced yarn-log4j: log4j.logger.com.microsoft.azure.datalake.store=DEBUG

2. Reinicie todos os nós/serviço para que o config entre em vigor.

3. Se estiver a ser estrangulado, verá o código de erro HTTP 429 no ficheiro de registo YARN. O ficheiro de registo YARN está em /tmp/ &lt; user &gt; /yarn.log

## <a name="examples-to-run"></a>Exemplos a executar

Para demonstrar como o MapReduce funciona na Data Lake Storage Gen1, o seguinte é algum código de amostra que foi executado num cluster com as seguintes definições:

* 16 nó D14v2
* Aglomerado de Hadoop executando HDI 3.6

Para um ponto de partida, aqui estão alguns comandos de exemplo para executar MapReduce Teragen, Terasort e Teravalidate. Pode ajustar estes comandos com base nos seus recursos.

### <a name="teragen"></a>Teragen

```
yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teragen -Dmapreduce.job.maps=2048 -Dmapreduce.map.memory.mb=3072 10000000000 adl://example/data/1TB-sort-input
```

### <a name="terasort"></a>Rio Terasort

```
yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar terasort -Dmapreduce.job.maps=2048 -Dmapreduce.map.memory.mb=3072 -Dmapreduce.job.reduces=512 -Dmapreduce.reduce.memory.mb=3072 adl://example/data/1TB-sort-input adl://example/data/1TB-sort-output
```

### <a name="teravalidate"></a>Teravalidate

```
yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teravalidate -Dmapreduce.job.maps=512 -Dmapreduce.map.memory.mb=3072 adl://example/data/1TB-sort-output adl://example/data/1TB-sort-validate
```