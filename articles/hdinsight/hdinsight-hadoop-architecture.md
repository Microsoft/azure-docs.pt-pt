---
title: Arquitetura Apache Hadoop - Azure HDInsight
description: Descreve o armazenamento e processamento da Apache Hadoop nos clusters Azure HDInsight.
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 02/07/2020
ms.openlocfilehash: 6f291e5aa440a3e6e45a1dcdb872e18c8d4557ce
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/28/2021
ms.locfileid: "98945910"
---
# <a name="apache-hadoop-architecture-in-hdinsight"></a>Arquitetura do Apache Hadoop no HDInsight

[O Apache Hadoop](https://hadoop.apache.org/) inclui dois componentes principais: o [Sistema de Ficheiros Distribuídos Apache Hadoop (HDFS)](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsUserGuide.html) que fornece armazenamento, e [o Apache Hadoop Yet Another Resource Negotiator (YARN)](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html) que fornece o processamento. Com capacidades de armazenamento e processamento, um cluster torna-se capaz de executar programas [MapReduce](https://hadoop.apache.org/docs/current/hadoop-mapreduce-client/hadoop-mapreduce-client-core/MapReduceTutorial.html) para executar o processamento de dados pretendido.

> [!NOTE]  
> Um HDFS não é normalmente implantado dentro do cluster HDInsight para fornecer armazenamento. Em vez disso, uma camada de interface compatível com HDFS é utilizada por componentes Hadoop. A capacidade de armazenamento real é fornecida pelo Azure Storage ou pelo Azure Data Lake Storage. Para Hadoop, os trabalhos da MapReduce que executam no cluster HDInsight funcionam como se um HDFS estivesse presente e, portanto, não exigir alterações para suportar as suas necessidades de armazenamento. Em Hadoop em HDInsight, o armazenamento é subcontratado, mas o processamento de YARN continua a ser um componente central. Para obter mais informações, consulte [Introdução ao Azure HDInsight](hadoop/apache-hadoop-introduction.md).

Este artigo introduz o YARN e como coordena a execução de aplicações no HDInsight.

## <a name="apache-hadoop-yarn-basics"></a>Básicos de Apache Hadoop YARN

O YARN governa e orquestra o processamento de dados em Hadoop. O YARN tem dois serviços centrais que funcionam como processos em nóns no cluster:

* ResourceManager
* NodeManager

O ResourceManager concede recursos de cálculo de cluster a aplicações como os empregos mapReduce. O ResourceManager concede estes recursos como contentores, onde cada contentor consiste numa alocação de núcleos de CPU e memória RAM. Se combinar todos os recursos disponíveis num cluster e depois distribuir os núcleos e a memória em blocos, cada bloco de recursos é um recipiente. Cada nó no aglomerado tem capacidade para um certo número de contentores, pelo que o cluster tem um limite fixo no número de contentores disponíveis. O loteamento de recursos num contentor é configurável.

Quando uma aplicação MapReduce funciona num cluster, o ResourceManager fornece à aplicação os recipientes para executar. O ResourceManager acompanha o estado das aplicações de execução, a capacidade de cluster disponível e rastreia as aplicações à medida que completam e libertam os seus recursos.

O ResourceManager também executa um processo de servidor web que fornece uma interface de utilizador web para monitorizar o estado das aplicações.

Quando um utilizador submete uma aplicação MapReduce para executar no cluster, a aplicação é submetida ao ResourceManager. Por sua vez, o ResourceManager atribui um contentor nos nós NodeManager disponíveis. Os nós NodeManager são onde a aplicação realmente executa. O primeiro contentor atribuído executa uma aplicação especial chamada ApplicationMaster. Este ApplicationMaster é responsável pela aquisição de recursos, sob a forma de contentores subsequentes, necessários para executar a aplicação submetida. O ApplicationMaster examina as fases da aplicação, como a fase do mapa e reduz a fase, e fatores na quantidade de dados que precisam de ser processados. O ApplicationMaster solicita então (*negoceia*) os recursos do ResourcesManager em nome da aplicação. O ResourceManager, por sua vez, concede recursos dos NodeManagers no cluster ao ApplicationMaster para que possa utilizar na execução da aplicação.

Os NodeManagers executam as tarefas que compõem a aplicação, reportam o seu progresso e estatuto de volta ao ApplicationMaster. O ApplicationMaster, por sua vez, informa o estado da aplicação de volta ao ResourceManager. O ResourceManager devolve quaisquer resultados ao cliente.

## <a name="yarn-on-hdinsight"></a>YARN em HDInsight

Todos os tipos de cluster HDInsight implementam YARN. O ResourceManager é implantado para alta disponibilidade com uma instância primária e secundária, que funciona nos nós de primeira e segunda cabeças dentro do cluster, respectivamente. Apenas um caso do ResourceManager está ativo de cada vez. Os casos nodeManager atravessam os nós de trabalhadores disponíveis no cluster.

![Apache YARN em Azure HDInsight](./media/hdinsight-hadoop-architecture/apache-yarn-on-hdinsight.png)

## <a name="soft-delete"></a>Eliminação recuperável

Para desembrulhá-lo da sua Conta de Armazenamento, consulte:

### <a name="azure-storage"></a>Storage do Azure

* [Eliminação de forma recuperável dos blobs do Armazenamento do Microsoft Azure](../storage/blobs/soft-delete-blob-overview.md)
* [Undelete Blob](/rest/api/storageservices/undelete-blob)

### <a name="azure-data-lake-storage-gen-1"></a>Azure Data Lake Storage Gen 1

[Restaurar-AzDataStoreDeletedItem](/powershell/module/az.datalakestore/restore-azdatalakestoredeleteditem)

### <a name="azure-data-lake-storage-gen-2"></a>Azure Data Lake Storage Gen2

[Problemas conhecidos com Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-known-issues.md)

## <a name="trash-purging"></a>Purga de lixo

A `fs.trash.interval` propriedade do **hdfs**  >  **Advanced core-site** deve permanecer no valor padrão porque você não deve armazenar `0` quaisquer dados no sistema de ficheiros local. Este valor não afeta as contas de armazenamento remoto (WASB, ADLS GEN1, ABFS)

## <a name="next-steps"></a>Próximos passos

* [Utilizar o MapReduce no Apache Hadoop no HDInsight](hadoop/hdinsight-use-mapreduce.md)
* [Introdução ao Azure HDInsight](hadoop/apache-hadoop-introduction.md)