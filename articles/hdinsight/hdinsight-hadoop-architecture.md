---
title: Arquitetura Apache Hadoop - Azure HDInsight
description: Descreve o armazenamento e processamento apache Hadoop em clusters Azure HDInsight.
author: ashishthaps
ms.author: ashishth
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 02/07/2020
ms.openlocfilehash: 3feacd94558ba275c81469827993aef106ae633c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77162213"
---
# <a name="apache-hadoop-architecture-in-hdinsight"></a>Arquitetura do Apache Hadoop no HDInsight

O [Apache Hadoop](https://hadoop.apache.org/) inclui dois componentes principais: o Sistema de [Ficheiros Distribuídos Apache Hadoop (HDFS)](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsUserGuide.html) que fornece armazenamento, e [apache Hadoop Yet Another Resource Negotiator (YARN)](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html) que fornece processamento. Com capacidades de armazenamento e processamento, um cluster torna-se capaz de executar programas [MapReduce](https://hadoop.apache.org/docs/current/hadoop-mapreduce-client/hadoop-mapreduce-client-core/MapReduceTutorial.html) para realizar o processamento de dados desejado.

> [!NOTE]  
> Um HDFS não é normalmente implantado dentro do cluster HDInsight para fornecer armazenamento. Em vez disso, uma camada de interface compatível com HDFS é usada por componentes Hadoop. A capacidade real de armazenamento é fornecida pelo Armazenamento Azure ou pelo Armazenamento do Lago De Dados Azure. Para hadoop, mapReduce empregos executados no cluster HDInsight funcionam como se um HDFS estivesse presente e, por isso, não exigiria alterações para suportar as suas necessidades de armazenamento. Em Hadoop no HDInsight, o armazenamento é subcontratado, mas o processamento de ARN continua a ser um componente central. Para mais informações, consulte [Introdução ao Azure HDInsight](hadoop/apache-hadoop-introduction.md).

Este artigo introduz o ARN e como coordena a execução de aplicações no HDInsight.

## <a name="apache-hadoop-yarn-basics"></a>Fundamentos do YARN de Hadoop Apache

O YARN governa e orquestra o processamento de dados em Hadoop. A YARN tem dois serviços centrais que funcionam como processos em nós no cluster:

* ResourceManager
* NodeManager

O ResourceManager concede recursos de cálculo de cluster a aplicações como mapReduce jobs. O Gestor de Recursos concede estes recursos como contentores, onde cada contentor consiste numa alocação de núcleos de CPU e memória RAM. Se combinar todos os recursos disponíveis num cluster e depois distribuir os núcleos e a memória em blocos, cada bloco de recursos é um recipiente. Cada nó do cluster tem capacidade para um certo número de contentores, pelo que o cluster tem um limite fixo no número de contentores disponíveis. O loteamento de recursos num recipiente é configurável.

Quando uma aplicação MapReduce funciona num cluster, o Gestor de Recursos fornece à aplicação os recipientes para executar. O ResourceManager rastreia o estado das aplicações de execução, capacidade de cluster disponível e rastreia aplicações à medida que completam e libertam os seus recursos.

O ResourceManager também executa um processo de servidor web que fornece uma interface de utilizador web para monitorizar o estado das aplicações.

Quando um utilizador submete uma aplicação MapReduce para executar no cluster, a aplicação é submetida ao Gestor de Recursos. Por sua vez, o Gestor de Recursos atribui um recipiente nos nós disponíveis do NodeManager. Os nódeManager os nódeos são onde a aplicação realmente executa. O primeiro contentor atribuído executa uma aplicação especial chamada ApplicationMaster. Esta ApplicationMaster é responsável pela aquisição de recursos, sob a forma de recipientes subsequentes, necessários para executar a aplicação submetida. O ApplicationMaster examina as fases da aplicação, como a fase do mapa e reduz a fase, e fatores na quantidade de dados necessários para serem processados. O ApplicationMaster solicita então (*negoceia)* os recursos do Gestor de Recursos em nome da aplicação. O Gestor de Recursos, por sua vez, concede recursos dos NodeManagers no cluster ao ApplicationMaster para que utilize na execução da aplicação.

Os NodeManagers executam as tarefas que compõem a aplicação, reportando o seu progresso e o seu estado de volta ao ApplicationMaster. O ApplicationMaster, por sua vez, reporta o estado da aplicação ao Gestor de Recursos. O Gestor de Recursos devolve quaisquer resultados ao cliente.

## <a name="yarn-on-hdinsight"></a>Arn em HDInsight

Todos os tipos de cluster HDInsight implantam o ARN. O ResourceManager é implantado para alta disponibilidade com uma instância primária e secundária, que funciona nos nós de primeira e segunda cabeças dentro do cluster, respectivamente. Apenas uma instância do Gestor de Recursos está ativa de cada vez. As instâncias noDeManager atravessam os nós de trabalhador disponíveis no cluster.

![YARN Apache em Azure HDInsight](./media/hdinsight-hadoop-architecture/apache-yarn-on-hdinsight.png)

## <a name="soft-delete"></a>Eliminação suave

Para desapagar um ficheiro da sua Conta de Armazenamento, consulte:

### <a name="azure-storage"></a>Storage do Azure

* [Eliminação de forma recuperável dos blobs do Armazenamento do Microsoft Azure](../storage/blobs/storage-blob-soft-delete.md)
* [Não excluir blob](https://docs.microsoft.com/rest/api/storageservices/undelete-blob)

### <a name="azure-data-lake-storage-gen-1"></a>Armazenamento de lagos azure data gen 1

[Restaurar-AzDataLakeStoreDeletedItem](https://docs.microsoft.com/powershell/module/az.datalakestore/restore-azdatalakestoredeleteditem)

### <a name="azure-data-lake-storage-gen-2"></a>Azure Data Lake Storage Gen2

[Questões conhecidas com Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-known-issues.md)

## <a name="trash-purging"></a>Purga do lixo

A `fs.trash.interval` propriedade do **hDFS** > **Advanced core-site** `0` deve permanecer no valor padrão porque você não deve armazenar quaisquer dados no sistema de ficheiros local. Este valor não afeta as contas de armazenamento remoto (WASB, ADLS GEN1, ABFS)

## <a name="next-steps"></a>Passos seguintes

* [Utilizar o MapReduce no Apache Hadoop no HDInsight](hadoop/hdinsight-use-mapreduce.md)
* [Introdução ao Azure HDInsight](hadoop/apache-hadoop-introduction.md)
