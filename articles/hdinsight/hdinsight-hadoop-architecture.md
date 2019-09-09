---
title: Arquitetura de Apache Hadoop-Azure HDInsight
description: Descreve Apache Hadoop armazenamento e processamento em clusters HDInsight do Azure.
author: ashishthaps
ms.author: ashishth
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/27/2019
ms.openlocfilehash: d41d671cf773bdab20c3f105c7d1abb6c7bde840
ms.sourcegitcommit: fa4852cca8644b14ce935674861363613cf4bfdf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/09/2019
ms.locfileid: "70810243"
---
# <a name="apache-hadoop-architecture-in-hdinsight"></a>Arquitetura do Apache Hadoop no HDInsight

O [Apache Hadoop](https://hadoop.apache.org/) inclui dois componentes principais: o [HDFS (Apache Hadoop sistema de arquivos distribuído](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsUserGuide.html) ) que fornece armazenamento e [Apache HADOOP outro recurso negociador (Yarn)](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html) que fornece processamento. Com os recursos de armazenamento e processamento, um cluster torna-se capaz de executar programas [MapReduce](https://hadoop.apache.org/docs/current/hadoop-mapreduce-client/hadoop-mapreduce-client-core/MapReduceTutorial.html) para executar o processamento de dados desejado.

> [!NOTE]  
> Um HDFS normalmente não é implantado no cluster HDInsight para fornecer armazenamento. Em vez disso, uma camada de interface compatível com HDFS é usada por componentes do Hadoop. O recurso de armazenamento real é fornecido pelo armazenamento do Azure ou Azure Data Lake Storage. Para o Hadoop, trabalhos do MapReduce em execução no cluster HDInsight são executados como se um HDFS estivesse presente e, portanto, não exigem alterações para dar suporte às suas necessidades de armazenamento. No Hadoop no HDInsight, o armazenamento é terceirizado, mas o processamento de YARN permanece como um componente principal. Para obter mais informações, consulte [introdução ao Azure HDInsight](hadoop/apache-hadoop-introduction.md).

Este artigo apresenta o YARN e como ele coordena a execução de aplicativos no HDInsight.

## <a name="apache-hadoop-yarn-basics"></a>Noções básicas do YARN Apache Hadoop 

O YARN rege e orquestra o processamento de dados no Hadoop. O YARN tem dois serviços principais que são executados como processos em nós no cluster: 

* ResourceManager 
* NodeManager

O ResourceManager concede recursos de computação de cluster a aplicativos como trabalhos MapReduce. O ResourceManager concede esses recursos como contêineres, onde cada contêiner consiste em uma alocação de núcleos de CPU e memória RAM. Se você combinar todos os recursos disponíveis em um cluster e, em seguida, distribuir os núcleos e a memória em blocos, cada bloco de recursos será um contêiner. Cada nó no cluster tem uma capacidade para um determinado número de contêineres, portanto, o cluster tem um limite fixo no número de contêineres disponíveis. A alocação de recursos em um contêiner é configurável. 

Quando um aplicativo MapReduce é executado em um cluster, o ResourceManager fornece ao aplicativo os contêineres a serem executados. O ResourceManager rastreia o status dos aplicativos em execução, a capacidade de cluster disponível e controla os aplicativos à medida que eles são concluídos e libera seus recursos. 

O ResourceManager também executa um processo de servidor Web que fornece uma interface de usuário da Web para monitorar o status dos aplicativos.

Quando um usuário envia um aplicativo MapReduce para ser executado no cluster, o aplicativo é enviado para o ResourceManager. Por sua vez, o ResourceManager aloca um contêiner nos nós NodeManager disponíveis. Os nós NodeManager são onde o aplicativo é realmente executado. O primeiro contêiner alocado executa um aplicativo especial chamado de aplicativo. Esse aplicativo é responsável por adquirir recursos, na forma de contêineres subsequentes, necessários para executar o aplicativo enviado. O aplicativo de nível examina os estágios do aplicativo, como o estágio de mapa e o estágio de redução, e os fatores de quantos dados precisam ser processados. Em seguida, o aplicativo solicita (*negocia*) os recursos do ResourceManager em nome do aplicativo. O ResourceManager, por sua vez, concede recursos do NodeManagers no cluster para o aplicativo a ser usado na execução do aplicativo. 

O NodeManagers executa as tarefas que compõem o aplicativo e, em seguida, relata seu progresso e status de volta para o aplicativo. O aplicativo, por sua vez, relata o status do aplicativo de volta para o ResourceManager. O ResourceManager retorna todos os resultados para o cliente.

## <a name="yarn-on-hdinsight"></a>YARN no HDInsight

Todos os tipos de cluster HDInsight implantam YARN. O ResourceManager é implantado para alta disponibilidade com uma instância primária e secundária, que é executada no primeiro e segundo nós de cabeçalho no cluster, respectivamente. Somente a instância do ResourceManager está ativa por vez. As instâncias do NodeManager são executadas nos nós de trabalho disponíveis no cluster.

![YARN no HDInsight](./media/hdinsight-hadoop-architecture/yarn-on-hdinsight.png)

## <a name="next-steps"></a>Passos seguintes

* [Utilizar o MapReduce no Apache Hadoop no HDInsight](hadoop/hdinsight-use-mapreduce.md)
* [Introdução ao Azure HDInsight](hadoop/apache-hadoop-introduction.md)
