---
title: 'Ajustar o desempenho: Spark, HDInsight & Azure Data Lake Storage Gen2 | Microsoft Docs'
description: Diretrizes de ajuste de desempenho do Azure Data Lake Storage Gen2 Spark
services: storage
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 11/18/2019
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: a70b8112af201a49e7eece8b689e75102ec55880
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74327552"
---
# <a name="tune-performance-spark-hdinsight--azure-data-lake-storage-gen2"></a>Ajustar o desempenho: Spark, HDInsight & Azure Data Lake Storage Gen2

Ao ajustar o desempenho no Spark, você precisa considerar o número de aplicativos que serão executados no cluster.  Por padrão, você pode executar 4 aplicativos simultaneamente em seu cluster HDI (Observação: a configuração padrão está sujeita a alterações).  Você pode decidir usar menos aplicativos para poder substituir as configurações padrão e usar mais do cluster para esses aplicativos.  

## <a name="prerequisites"></a>Pré-requisitos

* **Uma subscrição do Azure**. Consulte [Obter versão de avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Uma conta de Azure data Lake Storage Gen2**. Para obter instruções sobre como criar uma, consulte [início rápido: criar uma conta de armazenamento Azure data Lake Storage Gen2](data-lake-storage-quickstart-create-account.md).
* **Cluster HDInsight do Azure** com acesso a uma conta de data Lake Storage Gen2. Consulte [usar Azure data Lake Storage Gen2 com clusters do Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2). Certifique-se de habilitar Área de Trabalho Remota para o cluster.
* **Executando o cluster Spark no data Lake Storage Gen2**.  Para obter mais informações, consulte [usar o cluster HDInsight Spark para analisar dados no data Lake Storage Gen2](https://docs.microsoft.com/azure/hdinsight/hdinsight-apache-spark-use-with-data-lake-store)
* **Diretrizes de ajuste de desempenho em data Lake Storage Gen2**.  Para obter conceitos gerais de desempenho, consulte [diretrizes de ajuste de desempenho data Lake Storage Gen2](data-lake-storage-performance-tuning-guidance.md) 

## <a name="parameters"></a>Parâmetros

Ao executar trabalhos do Spark, aqui estão as configurações mais importantes que podem ser ajustadas para aumentar o desempenho no Data Lake Storage Gen2:

* **Num-eXecutors** -o número de tarefas simultâneas que podem ser executadas.

* **Executor-Memory** -a quantidade de memória alocada para cada executor.

* **Executor-núcleos** -o número de núcleos alocados para cada executor.                     

**Número-executores** Num-eXecutors definirá o número máximo de tarefas que podem ser executadas em paralelo.  O número real de tarefas que podem ser executadas em paralelo é limitado pelos recursos de memória e CPU disponíveis no cluster.

**Executor-memória** Essa é a quantidade de memória que está sendo alocada para cada executor.  A memória necessária para cada executor depende do trabalho.  Para operações complexas, a memória precisa ser maior.  Para operações simples, como leitura e gravação, os requisitos de memória serão menores.  A quantidade de memória para cada executor pode ser exibida em Ambari.  Em Ambari, navegue até Spark e exiba a guia Configurações.  

**Executor-núcleos** Isso define o número de núcleos usados por executor, que determina o número de threads paralelos que podem ser executados por executor.  Por exemplo, se executor-núcleos = 2, cada executor pode executar duas tarefas paralelas no executor.  Os núcleos de executor necessários serão dependentes do trabalho.  Trabalhos pesados de e/s não exigem uma grande quantidade de memória por tarefa para que cada executor possa lidar com mais tarefas paralelas.

Por padrão, dois núcleos de YARN virtuais são definidos para cada núcleo físico ao executar o Spark no HDInsight.  Esse número fornece um bom equilíbrio entre a simultaneidade e a quantidade de alternância de contexto de vários threads.  

## <a name="guidance"></a>Orientação

Ao executar cargas de trabalho analíticas do Spark para trabalhar com dados em Data Lake Storage Gen2, recomendamos que você use a versão mais recente do HDInsight para obter o melhor desempenho com Data Lake Storage Gen2. Quando seu trabalho está com uso intensivo de mais e/s, alguns parâmetros podem ser configurados para melhorar o desempenho.  Data Lake Storage Gen2 é uma plataforma de armazenamento altamente escalonável que pode lidar com alta taxa de transferência.  Se o trabalho consistir principalmente em leituras ou gravações, aumentar a simultaneidade de e/s para e de Data Lake Storage Gen2 poderá aumentar o desempenho.

Há algumas maneiras gerais de aumentar a simultaneidade para trabalhos com uso intensivo de e/s.

**Etapa 1: determinar quantos aplicativos estão em execução no cluster** – você deve saber quantos aplicativos estão em execução no cluster, incluindo o atual.  Os valores padrão para cada configuração do Spark pressupõem que há quatro aplicativos em execução simultaneamente.  Portanto, você terá apenas 25% do cluster disponível para cada aplicativo.  Para obter um melhor desempenho, você pode substituir os padrões alterando o número de executores.  

**Etapa 2: definir o executor-Memory** – a primeira coisa a ser definida é o executor-Memory.  A memória será dependente do trabalho que você vai executar.  Você pode aumentar a simultaneidade alocando menos memória por executor.  Se você vir exceções de memória insuficiente ao executar o trabalho, deverá aumentar o valor desse parâmetro.  Uma alternativa é obter mais memória usando um cluster com quantidades maiores de memória ou aumentando o tamanho do cluster.  Mais memória permitirá que mais executores sejam usados, o que significa mais simultaneidade.

**Etapa 3: definir executor-cores** – para cargas de trabalho intensivas de e/s que não têm operações complexas, é bom começar com um número alto de núcleos de executor para aumentar o número de tarefas paralelas por executor.  Definir executor-núcleos como 4 é um bom começo.   

    executor-cores = 4
Aumentar o número de núcleos de executor fornecerá mais paralelismo para que você possa experimentar diferentes núcleos de executor.  Para trabalhos que têm operações mais complexas, você deve reduzir o número de núcleos por executor.  Se o executor-núcleos for definido como maior que 4, a coleta de lixo poderá se tornar ineficiente e prejudicar o desempenho.

**Etapa 4: determinar a quantidade de memória yarn no cluster** – essas informações estão disponíveis em Ambari.  Navegue até YARN e exiba a guia Configurações.  A memória YARN é exibida nesta janela.  
Observe que, enquanto estiver na janela, você também poderá ver o tamanho do contêiner YARN padrão.  O tamanho do contêiner YARN é o mesmo que a memória por parâmetro de executor.

    Total YARN memory = nodes * YARN memory per node
**Etapa 5: calcular num-eXecutors**

**Calcular restrição de memória** -o parâmetro num-eXecutors é restrito por memória ou por CPU.  A restrição de memória é determinada pela quantidade de memória YARN disponível para seu aplicativo.  Você deve pegar a memória YARN total e dividi-la por executor-Memory.  A restrição precisa ser desdimensionada para o número de aplicativos, de modo que dividimos pelo número de aplicativos.

    Memory constraint = (total YARN memory / executor memory) / # of apps   
**Calcular restrição de CPU** -a restrição de CPU é calculada como o total de núcleos virtuais dividido pelo número de núcleos por executor.  Há dois núcleos virtuais para cada núcleo físico.  Semelhante à restrição de memória, precisamos dividir pelo número de aplicativos.

    virtual cores = (nodes in cluster * # of physical cores in node * 2)
    CPU constraint = (total virtual cores / # of cores per executor) / # of apps
**Definir num-eXecutors** – o parâmetro num-eXecutors é determinado pela obtenção do mínimo da restrição de memória e da restrição de CPU. 

    num-executors = Min (total virtual Cores / # of cores per executor, available YARN memory / executor-memory)   
A definição de um número mais alto de executores núm não necessariamente aumenta o desempenho.  Você deve considerar que adicionar mais executores adicionará sobrecarga extra para cada executor adicional, o que pode prejudicar o desempenho.  Num-eXecutors é limitado pelos recursos de cluster.    

## <a name="example-calculation"></a>Exemplo de cálculo

Digamos que você tenha um cluster composto por 8 nós D4v2 que esteja executando 2 aplicativos, incluindo aquele que você vai executar.  

**Etapa 1: determinar quantos aplicativos estão em execução no cluster** – você sabe que tem dois aplicativos no cluster, incluindo aquele que você vai executar.  

**Etapa 2: definir executor-Memory** – para este exemplo, determinamos que 6 GB de executor-Memory será suficiente para o trabalho com uso intensivo de e/s.  

    executor-memory = 6GB
**Etapa 3: definir executor-cores** – como esse é um trabalho com uso intensivo de e/s, podemos definir o número de núcleos para cada executor como 4.  A definição de núcleos por executor como maior que 4 pode causar problemas de coleta de lixo.  

    executor-cores = 4
**Etapa 4: determinar a quantidade de memória yarn no cluster** – navegamos para Ambari para descobrir que cada D4V2 tem 25 GB de memória yarn.  Como há 8 nós, a memória YARN disponível é multiplicada por 8.

    Total YARN memory = nodes * YARN memory* per node
    Total YARN memory = 8 nodes * 25GB = 200GB
**Etapa 5: calcular num-eXecutors** – o parâmetro num-eXecutors é determinado pela obtenção do mínimo da restrição de memória e da restrição de CPU dividida pelo número de aplicativos em execução no Spark.    

**Calcular restrição de memória** – a restrição de memória é calculada como a memória yarn total dividida pela memória por executor.

    Memory constraint = (total YARN memory / executor memory) / # of apps   
    Memory constraint = (200GB / 6GB) / 2   
    Memory constraint = 16 (rounded)
**Calcular restrição de CPU** -a restrição de CPU é calculada como o total de núcleos yarn dividido pelo número de núcleos por executor.
    
    YARN cores = nodes in cluster * # of cores per node * 2   
    YARN cores = 8 nodes * 8 cores per D14 * 2 = 128
    CPU constraint = (total YARN cores / # of cores per executor) / # of apps
    CPU constraint = (128 / 4) / 2
    CPU constraint = 16
**Definir num-eXecutors**

    num-executors = Min (memory constraint, CPU constraint)
    num-executors = Min (16, 16)
    num-executors = 16    

