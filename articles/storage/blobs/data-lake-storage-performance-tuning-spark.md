---
title: 'Performance de melodia: Spark, HDInsight & Azure Data Lake Storage Gen2 [ Microsoft Docs'
description: Diretrizes de afinação de desempenho do lago de dados azure Gen2 Spark
services: storage
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 11/18/2019
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: a70b8112af201a49e7eece8b689e75102ec55880
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/27/2020
ms.locfileid: "74327552"
---
# <a name="tune-performance-spark-hdinsight--azure-data-lake-storage-gen2"></a>Performance de sintonização: Spark, HDInsight & Azure Data Lake Storage Gen2

Ao afinar o desempenho no Spark, você precisa considerar o número de aplicações que estarão em execução no seu cluster.  Por predefinição, pode executar 4 aplicações em simultâneo no seu cluster HDI (Nota: a definição predefinida está sujeita a alterações).  Pode decidir utilizar menos aplicações para que possa anular as definições padrão e utilizar mais do cluster para essas aplicações.  

## <a name="prerequisites"></a>Pré-requisitos

* **Uma subscrição Azure.** Consulte [Obter versão de avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
* Uma conta De Armazenamento de **Lago Azure Gen2.** Para obter instruções sobre como criar uma, consulte [Quickstart: Crie uma conta](data-lake-storage-quickstart-create-account.md)de armazenamento de Gen2 de armazenamento de lago de dados Azure .
* **Cluster Azure HDInsight** com acesso a uma conta Gen2 de Armazenamento de Data Lake. Consulte a utilização do Armazenamento de [Lagos Azure Data Gen2 com clusters Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2). Certifique-se de que ativa o Ambiente de Trabalho Remoto para o cluster.
* **Executar cluster de faíscas em Data Lake Storage Gen2**.  Para mais informações, consulte [O cluster De faísca sondar O DSInsight para analisar dados em Data Lake Storage Gen2](https://docs.microsoft.com/azure/hdinsight/hdinsight-apache-spark-use-with-data-lake-store)
* **Diretrizes de afinação de desempenho sobre data lake storage Gen2**.  Para conceitos gerais de desempenho, consulte [Data Lake Storage Gen2 Performance Tuning Guidance](data-lake-storage-performance-tuning-guidance.md) 

## <a name="parameters"></a>Parâmetros

Ao executar trabalhos spark, aqui estão as configurações mais importantes que podem ser sintonizadas para aumentar o desempenho em Data Lake Storage Gen2:

* **Num-executores** - O número de tarefas simultâneas que podem ser executadas.

* **Memória do executor** - A quantidade de memória atribuída a cada executor.

* **Executor-núcleos** - O número de núcleos atribuídos a cada executor.                     

**Executores num** Os executores nºs definirão o número máximo de tarefas que podem ser executadas em paralelo.  O número real de tarefas que podem ser executadas em paralelo é limitado pela memória e recursos cpu disponíveis no seu cluster.

**Memória do executor** Esta é a quantidade de memória que está a ser atribuída a cada executor.  A memória necessária para cada executor depende do trabalho.  Para operações complexas, a memória tem de ser maior.  Para operações simples como ler e escrever, os requisitos de memória serão mais baixos.  A quantidade de memória para cada executor pode ser vista em Ambari.  Em Ambari, navegue até Spark e veja o separador Configs.  

**Executor-núcleos** Isto define o número de núcleos utilizados por executor, que determina o número de fios paralelos que podem ser executados por executor.  Por exemplo, se executor-núcleos = 2, então cada executor pode executar 2 tarefas paralelas no executor.  Os núcleos de executor necessários dependerão do trabalho.  Os trabalhos pesados de I/O não requerem uma grande quantidade de memória por tarefa para que cada executor possa lidar com tarefas mais paralelas.

Por padrão, dois núcleos de ARN virtuais são definidos para cada núcleo físico ao executar Spark no HDInsight.  Este número proporciona um bom equilíbrio de moeda e quantidade de contexto que muda de vários fios.  

## <a name="guidance"></a>Orientação

Durante a execução de cargas de trabalho analíticos Spark para trabalhar com dados em Data Lake Storage Gen2, recomendamos que use a versão Mais recente do HDInsight para obter o melhor desempenho com Data Lake Storage Gen2. Quando o seu trabalho é mais intensivo em I/O, então certos parâmetros podem ser configurados para melhorar o desempenho.  Data Lake Storage Gen2 é uma plataforma de armazenamento altamente escalável que pode lidar com alta energia.  Se o trabalho consistir principalmente em leitura ou escrita, então o aumento da moeda para I/O de e para data Lake Storage Gen2 poderia aumentar o desempenho.

Existem algumas formas gerais de aumentar a conmoeda para os postos de trabalho intensivos em I/O.

**Passo 1: Determine quantas aplicações estão a ser em execução no seu cluster** – Deve saber quantas aplicações estão a ser em execução no cluster, incluindo a atual.  Os valores predefinidos para cada definição de Spark pressupõem que existem 4 aplicações em funcionamento simultaneamente.  Portanto, você terá apenas 25% do cluster disponível para cada app.  Para obter um melhor desempenho, pode anular os incumprimentos alterando o número de executores.  

**Passo 2: Definir memória do executor** – A primeira coisa a definir é a memória do executor.  A memória vai depender do trabalho que vais gerir.  Pode aumentar a moeda atribuindo menos memória por executor.  Se vir fora das exceções da memória quando executa o seu trabalho, então deve aumentar o valor para este parâmetro.  Uma alternativa é obter mais memória usando um cluster que tem maiores quantidades de memória ou aumentando o tamanho do seu cluster.  Mais memória permitirá a sua vida a mais executores, o que significa mais conmoedação.

**Passo 3: Definir executor-núcleos** – Para cargas de trabalho intensivas de I/O que não têm operações complexas, é bom começar com um elevado número de executores-núcleos para aumentar o número de tarefas paralelas por executor.  Definir os núcleos do executor para 4 é um bom começo.   

    executor-cores = 4
Aumentar o número de executores-núcleos vai dar-lhe mais paralelismo para que possa experimentar com diferentes executor-núcleos.  Para empregos que tenham operações mais complexas, deve reduzir o número de núcleos por executor.  Se os núcleos executor espetados forem superiores a 4, então a recolha de lixo pode tornar-se ineficiente e degradar o desempenho.

**Passo 4: Determine a quantidade de memória de ARN em cluster** – Esta informação está disponível em Ambari.  Navegue para o YARN e veja o separador Configs.  A memória yARN é exibida nesta janela.  
Note enquanto estiver na janela, também pode ver o tamanho padrão do recipiente YARN.  O tamanho do recipiente YARN é o mesmo que a memória por parâmetro executor.

    Total YARN memory = nodes * YARN memory per node
**Passo 5: Calcular os executores num-executores**

**Calcular a restrição** de memória - O parâmetro num-executores é limitado quer pela memória quer pela CPU.  A restrição de memória é determinada pela quantidade de memória YARN disponível para a sua aplicação.  Devias pegar na memória total do ARN e dividi-lo pela memória do executor.  A restrição tem de ser descalcinada para o número de aplicações, pelo que dividimos pelo número de aplicações.

    Memory constraint = (total YARN memory / executor memory) / # of apps   
Calcular a **restrição do CPU** - A restrição do CPU é calculada como os núcleos virtuais totais divididos pelo número de núcleos por executor.  Existem 2 núcleos virtuais para cada núcleo físico.  À semelhança do constrangimento de memória, temos de dividir pelo número de aplicações.

    virtual cores = (nodes in cluster * # of physical cores in node * 2)
    CPU constraint = (total virtual cores / # of cores per executor) / # of apps
**Definir num-executores** – O parâmetro num-executoré é determinado tomando o mínimo da restrição de memória e da restrição de CPU. 

    num-executors = Min (total virtual Cores / # of cores per executor, available YARN memory / executor-memory)   
Definir um maior número de executores num não aumenta necessariamente o desempenho.  Deve considerar que adicionar mais executores adicionará despesas extra para cada executor adicional, o que pode potencialmente degradar o desempenho.  Os executores são limitados pelos recursos do cluster.    

## <a name="example-calculation"></a>Cálculo de exemplo

Digamos que tem atualmente um cluster composto por 8 nós D4v2 que está a executar 2 aplicações, incluindo a que vai executar.  

**Passo 1: Determine quantas aplicações estão a ser executadas no seu cluster** – sabe que tem 2 aplicações no seu cluster, incluindo a que vai executar.  

**Passo 2: Definir memória do executor** – para este exemplo, determinamos que 6GB de memória de executor será suficiente para trabalho intensivo de I/O.  

    executor-memory = 6GB
**Passo 3: Definir executor-núcleos** – Uma vez que este é um trabalho intensivo de I/O, podemos definir o número de núcleos para cada executor para 4.  A definição de núcleos por executor para maiores de 4 pode causar problemas na recolha de lixo.  

    executor-cores = 4
**Passo 4: Determine a quantidade de memória de ARN em cluster** – Navegamos até Ambari para descobrir que cada D4v2 tem 25GB de memória YARN.  Como existem 8 nós, a memória yARN disponível é multiplicada por 8.

    Total YARN memory = nodes * YARN memory* per node
    Total YARN memory = 8 nodes * 25GB = 200GB
**Passo 5: Calcular os executores num** – O parâmetro num-executoré é determinado tomando o mínimo de restrição de memória e a restrição de CPU dividida pelo # de aplicações em execução em Spark.    

**Calcular a restrição** de memória – A restrição de memória é calculada como a memória total do ARN dividida pela memória por executor.

    Memory constraint = (total YARN memory / executor memory) / # of apps   
    Memory constraint = (200GB / 6GB) / 2   
    Memory constraint = 16 (rounded)
Calcular a **restrição do CPU** - A restrição do CPU é calculada como os núcleos totais de fios divididos pelo número de núcleos por executor.
    
    YARN cores = nodes in cluster * # of cores per node * 2   
    YARN cores = 8 nodes * 8 cores per D14 * 2 = 128
    CPU constraint = (total YARN cores / # of cores per executor) / # of apps
    CPU constraint = (128 / 4) / 2
    CPU constraint = 16
**Definir num-executores**

    num-executors = Min (memory constraint, CPU constraint)
    num-executors = Min (16, 16)
    num-executors = 16    

