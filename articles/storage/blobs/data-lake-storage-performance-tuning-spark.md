---
title: Azure Data Lake Gen2 Spark desempenho do armazenamento diretrizes de ajuste | Documentos da Microsoft
description: Azure Data Lake Gen2 Spark desempenho do armazenamento diretrizes de ajuste
services: storage
author: swums
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: stewu
ms.openlocfilehash: bfd6e715e907925a1ba3db3b60c0d3eccb637930
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61481994"
---
# <a name="performance-tuning-guidance-for-spark-on-hdinsight-and-azure-data-lake-storage-gen2"></a>Guia para o Spark no HDInsight e geração 2 de armazenamento do Azure Data Lake de sintonização de desempenho

Quando o ajuste de desempenho no Spark, precisa considerar o número de aplicações que serão executados no seu cluster.  Por predefinição, pode executar 4 aplicações em simultâneo em seu cluster do HDI (Nota: a predefinição está sujeita a alterações).  Poderá optar por utilizar menos aplicações para que possa substituir as predefinições e utilizar mais do cluster para essas aplicações.  

## <a name="prerequisites"></a>Pré-requisitos

* **Uma subscrição do Azure**. Consulte [Obter uma avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Uma conta de geração 2 de armazenamento do Azure Data Lake**. Para obter instruções sobre como criar um, consulte [início rápido: Criar uma conta de armazenamento de geração 2 de armazenamento do Azure Data Lake](data-lake-storage-quickstart-create-account.md).
* **Cluster de HDInsight do Azure** com acesso a uma conta de geração 2 de armazenamento do Data Lake. Ver [Use Azure Data Lake armazenamento Gen2 com o Azure HDInsight clusters](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2). Certifique-se de que ativar o ambiente de trabalho remoto para o cluster.
* **A executar o cluster do Spark no ger2 de armazenamento do Data Lake**.  Para obter mais informações, consulte [cluster utilizar o Spark do HDInsight para analisar dados de geração 2 de armazenamento do Data Lake](https://docs.microsoft.com/azure/hdinsight/hdinsight-apache-spark-use-with-data-lake-store)
* **Diretrizes de geração 2 de armazenamento do Data Lake de ajuste de desempenho**.  Para os conceitos gerais de desempenho, consulte [Data Lake Storage Gen2 ajuste orientação de desempenho](data-lake-storage-performance-tuning-guidance.md) 

## <a name="parameters"></a>Parâmetros

Quando executar tarefas de Spark, seguem-se as definições mais importantes que podem ser otimizadas para melhorar o desempenho na geração 2 de armazenamento do Data Lake:

* **Núm. executores** -o número de tarefas simultâneas que podem ser executadas.

* **Memória de executor** -a quantidade de memória alocada a cada executor.

* **Núcleos de executor** -o número de núcleos alocados para cada executor.                     

**Núm. executores** Num executores irão definir o número máximo de tarefas que podem ser executadas em paralelo.  O número real de tarefas que podem ser executadas em paralelo é vinculado de acordo com a memória e recursos de CPU disponíveis no seu cluster.

**Memória de executor** esta é a quantidade de memória que está a ser atribuído a cada executor.  A memória necessária para cada executor é dependente da tarefa.  Para operações complexas, a memória tem de ser superior.  Para obter operações simples, como a leitura e escrita, requisitos de memória será inferiores.  A quantidade de memória para cada executor pode ser visualizada no Ambari.  No Ambari, navegue para o Spark e ver a guia configurações.  

**Núcleos de executor** isso define o número de núcleos utilizado por executor, que determina o número de threads paralelos que podem ser executadas por executor.  Por exemplo, se núcleos de executor = 2, em seguida cada executor pode executar tarefas paralelas 2 no executor.  Os núcleos de executor necessitam será dependentes da tarefa.  Tarefas pesadas de e/s não necessitam de uma grande quantidade de memória por tarefas, para que cada executor pode processar mais tarefas paralelas.

Por predefinição, os dois núcleos YARN virtuais são definidos para cada núcleo físico ao executar o Spark no HDInsight.  Este número fornece um bom equilíbrio de simultaneidade e a quantidade de alternância de vários threads de contexto.  

## <a name="guidance"></a>Orientação

Ao executar o Spark cargas de trabalho de análise para trabalhar com dados de geração 2 de armazenamento do Data Lake, recomendamos que utilize a versão mais recente do HDInsight para obter o melhor desempenho com geração 2 de armazenamento do Data Lake. Quando a tarefa é mais intensivas de e/s, determinados parâmetros podem ser configurados para melhorar o desempenho.  Geração 2 de armazenamento do Data Lake é uma plataforma de armazenamento altamente escalável que pode processar um débito elevado.  Se a tarefa consiste principalmente de leitura ou escrita, em seguida, aumenta a simultaneidade de e/s para e de geração 2 de armazenamento do Data Lake pode aumentar o desempenho.

Existem algumas formas gerais para aumentar a simultaneidade para tarefas intensivas de e/s.

**Passo 1: Determinar quantos aplicativos estão em execução no seu cluster** – deve saber quantos aplicativos estão em execução no cluster, incluindo atual.  Os valores predefinidos para cada Spark definição assume que existem 4 aplicações em execução em simultâneo.  Por conseguinte, apenas terá 25% do cluster estão disponíveis para cada aplicação.  Para obter um melhor desempenho, pode substituir as predefinições, alterar o número de executores.  

**Passo 2: Definir a memória de executor** – a primeira coisa que definir é a memória de executor.  A memória será dependente da tarefa que pretende executar.  Pode aumentar a simultaneidade ao alocar menos memória por executor.  Se vir exceções de memória insuficiente quando executar o seu trabalho, em seguida, deve aumentar o valor para este parâmetro.  Uma alternativa é obter mais memória ao utilizar um cluster com a maior quantidade de memória ou aumentar o tamanho do cluster.  Mais memória irá permitir executores mais a ser usado, o que significa mais simultaneidade.

**Passo 3: Definir o executor núcleos** – e/s para cargas de trabalho intensivas que não têm operações complexas, é bom começar com um elevado número de núcleos executor para aumentar o número de tarefas paralelas por executor.  Definição de 4 núcleos de executor é um bom começo.   

    executor-cores = 4
Aumentar o número de núcleos de executor lhe dará mais paralelismo para que pode experimentar com diferentes de núcleos de executor.  Para as tarefas que têm operações mais complexas, deve reduzir o número de núcleos por executor.  Se executor núcleos é definido como superior a 4, em seguida, coleta de lixo pode tornar-se ineficiente e degradar o desempenho.

**Passo 4: Determinar a quantidade de memória YARN em cluster** – essas informações estão disponíveis no Ambari.  Navegue para o YARN e ver a guia configurações.  A memória YARN é apresentada nesta janela.  
Tenha em atenção enquanto estiver na janela, também pode ver o tamanho de contentor predefinido do YARN.  O tamanho de contentor do YARN é o mesmo que a memória por parâmetro de executor.

    Total YARN memory = nodes * YARN memory per node
**Passo 5: Calcular executores NÚM.**

**Calcular a restrição de memória** -o parâmetro num executores é limitado pela memória ou por CPU.  A restrição de memória é determinada pela quantidade de memória YARN disponível para a sua aplicação.  Deve tirar total de memória YARN e dividi-lo com a memória de executor.  A restrição tem de ser anulado dimensionado para o número de aplicações para que o dividimos pelo número de aplicações.

    Memory constraint = (total YARN memory / executor memory) / # of apps   
**Calcular a restrição de CPU** -restrição a CPU é calculado como o total de núcleos virtuais dividido pelo número de núcleos por executor.  Existem 2 núcleos virtuais para cada núcleo físico.  Assim como a restrição de memória, temos a divisão pelo número de aplicações.

    virtual cores = (nodes in cluster * # of physical cores in node * 2)
    CPU constraint = (total virtual cores / # of cores per executor) / # of apps
**Definir num executores** – o parâmetro num executores é determinado ao tirar o mínimo da restrição de memória e a restrição de CPU. 

    num-executors = Min (total virtual Cores / # of cores per executor, available YARN memory / executor-memory)   
Configurar um número maior de executores núm não necessariamente aumentar o desempenho.  Deve considerar que a adição de mais executores irá adicionar extra gerais para cada executor adicional, que potencialmente pode degradar o desempenho.  Núm. executores é vinculado de acordo com os recursos do cluster.    

## <a name="example-calculation"></a>Cálculo de exemplo

Digamos que tenha atualmente um composto por 8 D4v2 nós de cluster que executa 2 aplicações, incluindo o que pretende executar.  

**Passo 1: Determinar quantos aplicativos estão em execução no seu cluster** – sabe que tem 2 aplicações no seu cluster, incluindo o que pretende executar.  

**Passo 2: Definir a memória de executor** – neste exemplo, podemos determinar que 6 GB de memória de executor será suficiente para a tarefa de e/s intensiva.  

    executor-memory = 6GB
**Passo 3: Definir o executor núcleos** – uma vez que esse é um trabalho intensivo de e/s, podemos definir o número de núcleos para cada executor para 4.  Na definição de núcleos por executor maiores do que 4 pode causar problemas de coleta de lixo.  

    executor-cores = 4
**Passo 4: Determinar a quantidade de memória YARN em cluster** –, navegar para Ambari para descobrir o que cada D4v2 tem 25 GB de memória YARN.  Uma vez que existem 8 nós, a memória YARN disponível é multiplicada por 8.

    Total YARN memory = nodes * YARN memory* per node
    Total YARN memory = 8 nodes * 25GB = 200GB
**Passo 5: Calcular o núm executores** – o parâmetro num executores é determinado ao tirar o mínimo da restrição de memória e a restrição de CPU dividido pelo número de aplicações em execução no Spark.    

**Calcular a restrição de memória** – a restrição de memória é calculada como a memória total do YARN, dividida pela memória por executor.

    Memory constraint = (total YARN memory / executor memory) / # of apps   
    Memory constraint = (200GB / 6GB) / 2   
    Memory constraint = 16 (rounded)
**Calcular a restrição de CPU** -restrição a CPU é calculada como os núcleos de total yarn divididos pelo número de núcleos por executor.
    
    YARN cores = nodes in cluster * # of cores per node * 2   
    YARN cores = 8 nodes * 8 cores per D14 * 2 = 128
    CPU constraint = (total YARN cores / # of cores per executor) / # of apps
    CPU constraint = (128 / 4) / 2
    CPU constraint = 16
**Núm. de conjunto-executores**

    num-executors = Min (memory constraint, CPU constraint)
    num-executors = Min (16, 16)
    num-executors = 16    

