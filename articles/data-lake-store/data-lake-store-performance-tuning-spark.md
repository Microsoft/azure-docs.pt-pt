---
title: Afinação de desempenho - Faísca com Azure Data Lake Storage Gen1
description: Saiba mais sobre as diretrizes de afinação de desempenho para Spark on Azure HDInsight e Azure Data Lake Storage Gen1.
author: stewu
ms.service: data-lake-store
ms.topic: how-to
ms.date: 12/19/2016
ms.author: stewu
ms.openlocfilehash: f604d1d054717e426fcb02271b3a2aa06c6489b6
ms.sourcegitcommit: 374e47efb65f0ae510ad6c24a82e8abb5b57029e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/28/2020
ms.locfileid: "85505261"
---
# <a name="performance-tuning-guidance-for-spark-on-hdinsight-and-azure-data-lake-storage-gen1"></a>Orientação de afinação de desempenho para Spark on HDInsight e Azure Data Lake Storage Gen1

Ao sintonizar o desempenho no Spark, é necessário considerar o número de aplicações que irão ser executadas no seu cluster. Por predefinição, pode executar quatro aplicações simultaneamente no seu cluster HDI (Nota: a definição predefinida está sujeita a alterações). Pode decidir utilizar menos aplicações para que possa anular as definições predefinimentadas e utilizar mais do cluster para essas aplicações.

## <a name="prerequisites"></a>Pré-requisitos

* **Uma assinatura Azure**. Consulte [Obter versão de avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Uma conta Azure Data Lake Storage Gen1**. Para instruções sobre como criar um, consulte [Começar com a Azure Data Lake Storage Gen1](data-lake-store-get-started-portal.md)
* **Cluster Azure HDInsight** com acesso a uma conta Gen1 de armazenamento de data lake. Consulte [Criar um cluster HDInsight com data lake storage gen1](data-lake-store-hdinsight-hadoop-use-portal.md). Certifique-se de que ativa o Ambiente de Trabalho Remoto para o cluster.
* **Executando o cluster Spark na Data Lake Storage Gen1**. Para obter mais informações, consulte [o cluster HDInsight Spark para analisar dados na Data Lake Storage Gen1](https://docs.microsoft.com/azure/hdinsight/hdinsight-apache-spark-use-with-data-lake-store)
* **Diretrizes de afinação de desempenho sobre data lake storage gen1**. Para conceitos de desempenho geral, consulte [data lake storage Gen1 Performance Afinação de Afinação](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-performance-tuning-guidance) 

## <a name="parameters"></a>Parâmetros

Ao executar trabalhos de Spark, aqui estão as configurações mais importantes que podem ser sintonizadas para aumentar o desempenho na Data Lake Storage Gen1:

* **Executores num -** O número de tarefas simultâneas que podem ser executadas.

* **Executor-memória** - A quantidade de memória atribuída a cada executor.

* **Executor-núcleos** - O número de núcleos atribuídos a cada executor.

**Executores numéricos** Os executores num-executores definirão o número máximo de tarefas que podem ser executadas em paralelo. O número real de tarefas que podem ser executadas paralelamente é limitado pelos recursos de memória e CPU disponíveis no seu cluster.

**Executor-memória** Esta é a quantidade de memória que está a ser atribuída a cada executor. A memória necessária para cada executor depende do trabalho. Para operações complexas, a memória tem de ser maior. Para operações simples como ler e escrever, os requisitos de memória serão mais baixos. A quantidade de memória para cada executor pode ser vista em Ambari. Em Ambari, navegue até Spark e veja o **separador Configs.**

**Executor-núcleos** Isto define a quantidade de núcleos utilizados por executor, que determina o número de fios paralelos que podem ser executados por executor. Por exemplo, se executor-núcleos = 2, então cada executor pode executar 2 tarefas paralelas no executor. Os núcleos de executor necessários dependerão do trabalho. Os trabalhos pesados de I/O não requerem uma grande quantidade de memória por tarefa para que cada executor possa lidar com tarefas mais paralelas.

Por padrão, dois núcleos de YARN virtuais são definidos para cada núcleo físico ao executar spark em HDInsight. Este número proporciona um bom equilíbrio de concordância e quantidade de mudança de contexto a partir de múltiplos fios.

## <a name="guidance"></a>Orientação

Enquanto executa cargas de trabalho analíticos spark para trabalhar com dados em Data Lake Storage Gen1, recomendamos que use a versão HDInsight mais recente para obter o melhor desempenho com Data Lake Storage Gen1. Quando o seu trabalho é mais intensivo em E/S, então certos parâmetros podem ser configurados para melhorar o desempenho. Data Lake Storage Gen1 é uma plataforma de armazenamento altamente escalável que pode lidar com alta produção. Se o trabalho consistir principalmente em ler ou escrever, então aumentar a concordância para i/O de e para data lake storage gen1 pode aumentar o desempenho.

Existem algumas formas gerais de aumentar a concordância para os trabalhos intensivos de I/O.

**Passo 1: Determine quantas aplicações estão a ser executando no seu cluster** – Deve saber quantas aplicações estão a ser executando no cluster, incluindo a atual. Os valores predefinidos para cada definição de Faísca pressupõe que existem 4 aplicações em execução em simultâneo. Portanto, terá apenas 25% do cluster disponível para cada aplicação. Para obter um melhor desempenho, pode anular os predefinidos alterando o número de executores.

**Passo 2: Definir memória executora** – a primeira coisa a definir é a memória do executor. A memória dependerá do trabalho que vais executar. Pode aumentar a concordância atribuindo menos memória por executor. Se vir exceções de memória quando executar o seu trabalho, então deve aumentar o valor para este parâmetro. Uma alternativa é obter mais memória usando um cluster que tenha maiores quantidades de memória ou aumentando o tamanho do seu cluster. Mais memória permitirá a utilização de mais executores, o que significa mais concordância.

**Passo 3: Definir executor-núcleos** – Para cargas de trabalho intensivas de I/O que não têm operações complexas, é bom começar com um elevado número de núcleos executor para aumentar o número de tarefas paralelas por executor. Definir os núcleos do executor para 4 é um bom começo.

    executor-cores = 4
Aumentar o número de núcleos de executor vai dar-lhe mais paralelismo para que possa experimentar com diferentes núcleos de executor. Para trabalhos que tenham operações mais complexas, deve reduzir o número de núcleos por executor. Se os núcleos de executor forem definidos acima de 4, então a recolha de lixo pode tornar-se ineficiente e degradar o desempenho.

**Passo 4: Determinar a quantidade de memória YARN no cluster** – Esta informação está disponível em Ambari. Navegue até YARN e veja o separador Contigs. A memória YARN é apresentada nesta janela.
Note que enquanto estiver na janela, também pode ver o tamanho padrão do recipiente YARN. O tamanho do recipiente YARN é o mesmo que a memória por parâmetro executor.

    Total YARN memory = nodes * YARN memory per node
**Passo 5: Calcular executores numéricos**

**Calcular a restrição** de memória - O parâmetro de executores num é limitado pela memória ou pelo CPU. A restrição de memória é determinada pela quantidade de memória YARN disponível para a sua aplicação. Pegue a memória total de YARN e divida-a por memória executora. O constrangimento precisa de ser desdimensionado para o número de apps, pelo que dividimos pelo número de apps.

    Memory constraint = (total YARN memory / executor memory) / # of apps
**Calcular a restrição do CPU** - A restrição do CPU é calculada como o núcleo virtual total dividido pelo número de núcleos por executor. Existem 2 núcleos virtuais para cada núcleo físico. À semelhança do constrangimento de memória, dividimos pelo número de aplicações.

    virtual cores = (nodes in cluster * # of physical cores in node * 2)
    CPU constraint = (total virtual cores / # of cores per executor) / # of apps
**Definir executores num** - O parâmetro de executores num é determinado tomando o mínimo da restrição de memória e da restrição do CPU. 

    num-executors = Min (total virtual Cores / # of cores per executor, available YARN memory / executor-memory)
Definir um maior número de executores num não aumenta necessariamente o desempenho. Deve considerar que adicionar mais executores adicionará despesas adicionais para cada executor adicional, o que pode potencialmente degradar o desempenho. Os executores numisãos são limitados pelos recursos do cluster.

## <a name="example-calculation"></a>Cálculo de exemplo

Digamos que atualmente tem um cluster composto por nós 8 D4v2 que está a executar duas aplicações, incluindo a que vai executar.

**Passo 1: Determine quantas aplicações estão a ser executadas no seu cluster** – sabe que tem duas aplicações no seu cluster, incluindo a que vai executar.

**Passo 2: Definir memória executora** – por exemplo, determinamos que 6GB de memória executor será suficiente para trabalho intensivo de I/O.

    executor-memory = 6GB
**Passo 3: Definir executor-núcleos** – Uma vez que este é um trabalho intensivo de I/O, podemos definir o número de núcleos para cada executor para quatro. A definição de núcleos por executor a maiores de quatro pode causar problemas na recolha de lixo.

    executor-cores = 4
**Passo 4: Determinar a quantidade de memória YARN em cluster** – Navegamos em Ambari para descobrir que cada D4v2 tem 25GB de memória YARN. Uma vez que existem 8 nós, a memória YARN disponível é multiplicada por 8.

    Total YARN memory = nodes * YARN memory* per node
    Total YARN memory = 8 nodes * 25 GB = 200 GB
**Passo 5: Calcular os executores num** - O parâmetro de executores num é determinado tomando o mínimo da restrição de memória e a restrição do CPU dividida pelo # das aplicações em execução em Spark.

**Calcular a restrição de memória** – A restrição de memória é calculada como a memória total de YARN dividida pela memória por executor.

    Memory constraint = (total YARN memory / executor memory) / # of apps 
    Memory constraint = (200 GB / 6 GB) / 2
    Memory constraint = 16 (rounded)
**Calcular a restrição do CPU** - A restrição do CPU é calculada como o núcleo total de fios dividido pelo número de núcleos por executor.
    
    YARN cores = nodes in cluster * # of cores per node * 2
    YARN cores = 8 nodes * 8 cores per D14 * 2 = 128
    CPU constraint = (total YARN cores / # of cores per executor) / # of apps
    CPU constraint = (128 / 4) / 2
    CPU constraint = 16
**Definir executores num-executores**

    num-executors = Min (memory constraint, CPU constraint)
    num-executors = Min (16, 16)
    num-executors = 16
