---
title: Soluções de armazenamento do Azure para serviços de ML no HDInsight - Azure
description: Saiba mais sobre as opções de armazenamento diferentes disponíveis com os serviços de ML no HDInsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/27/2018
ms.openlocfilehash: 8c1624ba6780fc45e3e663cadc032e253ce9545f
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67448881"
---
# <a name="azure-storage-solutions-for-ml-services-on-azure-hdinsight"></a>Soluções de armazenamento do Azure para serviços de ML no Azure HDInsight

Serviços de ML no HDInsight pode utilizar uma variedade de soluções de armazenamento para manter os dados, código ou objetos que contêm os resultados da análise. Estas incluem as seguintes opções:

- [BLOBs do Azure](https://azure.microsoft.com/services/storage/blobs/)
- [Armazenamento do Azure Data Lake](https://azure.microsoft.com/services/storage/data-lake-storage/)
- [Armazenamento de ficheiros do Azure](https://azure.microsoft.com/services/storage/files/)

Tem também a opção de aceder a várias contas de armazenamento do Azure ou contentores com o seu cluster do HDInsight. Armazenamento de ficheiros do Azure é uma opção de armazenamento de dados conveniente para utilização no nó de extremidade que lhe permite montar uma partilha de ficheiros de armazenamento do Azure para, por exemplo, o sistema de ficheiros do Linux. Mas, partilhas de ficheiros do Azure podem ser montadas e utilizadas por qualquer sistema que tenha um sistema operativo suportado como Windows ou Linux. 

Quando cria um cluster do Apache Hadoop no HDInsight, especifica um uma **armazenamento do Azure** conta ou **armazenamento do Data Lake**. Um contentor de armazenamento específicos a partir dessa conta contém o sistema de ficheiros para o cluster que criou (por exemplo, o sistema de ficheiros distribuído do Hadoop). Para obter mais informações e orientações, veja:

- [Utilizar o armazenamento do Azure com o HDInsight](../hdinsight-hadoop-use-blob-storage.md)
- [Utilizar o armazenamento do Data Lake com clusters do HDInsight do Azure](../hdinsight-hadoop-use-data-lake-store.md)

## <a name="use-azure-blob-storage-accounts-with-ml-services-cluster"></a>Utilizar contas de armazenamento de Blobs do Azure com o cluster de serviços de ML

Se tiver especificado mais de uma conta de armazenamento ao criar o cluster de serviços de ML, as instruções seguintes explicam como utilizar uma conta secundária para acesso a dados e operações num cluster de serviços de ML. Suponha que as seguintes contas de armazenamento e o contentor: **storage1** e um contentor predefinido chamado **container1**, e **storage2** com **container2**.

> [!WARNING]  
> Para fins de desempenho, o cluster do HDInsight é criado no mesmo centro de dados como a conta de armazenamento primário que especificar. Não é suportada a utilizar uma conta de armazenamento numa localização diferente do que o cluster do HDInsight.

### <a name="use-the-default-storage-with-ml-services-on-hdinsight"></a>Utilizar o armazenamento predefinido com os serviços de ML no HDInsight

1. Utilizar um cliente SSH, ligue ao nó de extremidade do cluster. Para obter informações sobre como utilizar o SSH com clusters do HDInsight, consulte [utilizar o SSH com HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).
  
2. Copie um ficheiro de exemplo, mysamplefile.csv, para o diretório de /share. 

        hadoop fs –mkdir /share
        hadoop fs –copyFromLocal mycsv.scv /share  

3. Mude para o R Studio ou outra consola de R e escrever o código de R ao definir o nó de nome para o **predefinição** e a localização do ficheiro que pretende aceder.  

        myNameNode <- "default"
        myPort <- 0

        #Location of the data:  
        bigDataDirRoot <- "/share"  

        #Define Spark compute context:
        mySparkCluster <- RxSpark(nameNode=myNameNode, consoleOutput=TRUE)

        #Set compute context:
        rxSetComputeContext(mySparkCluster)

        #Define the Hadoop Distributed File System (HDFS) file system:
        hdfsFS <- RxHdfsFileSystem(hostName=myNameNode, port=myPort)

        #Specify the input file to analyze in HDFS:
        inputFile <-file.path(bigDataDirRoot,"mysamplefile.csv")

Todas as referências de diretório e arquivo apontam para a conta de armazenamento `wasb://container1@storage1.blob.core.windows.net`. Este é o **predefinido a conta de armazenamento** que está associada ao cluster do HDInsight.

### <a name="use-the-additional-storage-with-ml-services-on-hdinsight"></a>Utilizar o armazenamento adicional com os serviços de ML no HDInsight

Agora, suponha que queira processar um ficheiro chamado mysamplefile1.csv onde está localizado o /private diretório dos **container2** na **storage2**.

No seu código de R, aponte a referência de nó de nome para o **storage2** conta de armazenamento.

    myNameNode <- "wasb://container2@storage2.blob.core.windows.net"
    myPort <- 0

    #Location of the data:
    bigDataDirRoot <- "/private"

    #Define Spark compute context:
    mySparkCluster <- RxSpark(consoleOutput=TRUE, nameNode=myNameNode, port=myPort)

    #Set compute context:
    rxSetComputeContext(mySparkCluster)

    #Define HDFS file system:
    hdfsFS <- RxHdfsFileSystem(hostName=myNameNode, port=myPort)

    #Specify the input file to analyze in HDFS:
    inputFile <-file.path(bigDataDirRoot,"mysamplefile1.csv")

Todas as referências de diretório e arquivo direcionar para a conta de armazenamento `wasb://container2@storage2.blob.core.windows.net`. Este é o **nome do nó** especificado.

Tem de configurar o `/user/RevoShare/<SSH username>` diretório **storage2** da seguinte forma:


    hadoop fs -mkdir wasb://container2@storage2.blob.core.windows.net/user
    hadoop fs -mkdir wasb://container2@storage2.blob.core.windows.net/user/RevoShare
    hadoop fs -mkdir wasb://container2@storage2.blob.core.windows.net/user/RevoShare/<RDP username>

## <a name="use-azure-data-lake-storage-with-ml-services-cluster"></a>Utilizar o armazenamento do Azure Data Lake com o cluster de serviços de ML 

Para utilizar o armazenamento do Data Lake com o seu cluster do HDInsight, terá de conceder o acesso de cluster para cada armazenamento do Azure Data Lake que pretende utilizar. Para obter instruções sobre como utilizar o portal do Azure para criar um cluster do HDInsight com uma conta de armazenamento do Azure Data Lake como armazenamento predefinido ou como armazenamento adicional, consulte [criam um cluster do HDInsight com o armazenamento do Data Lake com o portal do Azure](../../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md).

Em seguida, utilize o armazenamento no seu script R muito como fez uma conta de armazenamento do Azure secundária, conforme descrito no procedimento anterior.

### <a name="add-cluster-access-to-your-azure-data-lake-storage"></a>Adicionar acesso de cluster para o armazenamento do Azure Data Lake
Aceder ao armazenamento do Data Lake ao utilizar um Principal de serviço do Azure Active Directory (Azure AD) associada ao seu cluster do HDInsight.

1. Ao criar o cluster do HDInsight, selecione **identidade do AAD de Cluster** partir a **origem de dados** separador.

2. Na **identidade do AAD de Cluster** caixa de diálogo em **selecionar Principal de serviço do AD**, selecione **criar novo**.

Depois que dê um nome ao Principal de serviço e cria uma palavra-passe para o mesmo, clique em **gerir acesso de ADLS** para associar o Principal de serviço com o armazenamento do Data Lake.

Também é possível adicionar o acesso de cluster para uma ou mais contas de armazenamento do Data Lake após a criação do cluster. Abra a entrada do portal do Azure para um armazenamento do Data Lake e aceda à **Explorador de dados > acesso > Adicionar**. 

### <a name="how-to-access-data-lake-storage-gen1-from-ml-services-on-hdinsight"></a>Como aceder a geração 1 de armazenamento do Data Lake dos serviços de ML no HDInsight

Assim que concedeu acesso para a geração 1 de armazenamento do Data Lake, pode utilizar o armazenamento num cluster de serviços de ML no HDInsight a forma que faria com uma conta de armazenamento do Azure secundária. A única diferença é que o prefixo **wasb: / /** é alterado para **adl: / /** da seguinte forma:


    # Point to the ADL Storage (e.g. ADLtest)
    myNameNode <- "adl://rkadl1.azuredatalakestore.net"
    myPort <- 0

    # Location of the data (assumes a /share directory on the ADL account)
    bigDataDirRoot <- "/share"  

    # Define Spark compute context
    mySparkCluster <- RxSpark(consoleOutput=TRUE, nameNode=myNameNode, port=myPort)

    # Set compute context
    rxSetComputeContext(mySparkCluster)

    # Define HDFS file system
    hdfsFS <- RxHdfsFileSystem(hostName=myNameNode, port=myPort)

    # Specify the input file in HDFS to analyze
    inputFile <-file.path(bigDataDirRoot,"mysamplefile.csv")

Os comandos seguintes são utilizados para configurar a conta de geração 1 de armazenamento do Data Lake com o diretório de RevoShare e adicione o ficheiro. csv de exemplo do exemplo anterior:


    hadoop fs -mkdir adl://rkadl1.azuredatalakestore.net/user
    hadoop fs -mkdir adl://rkadl1.azuredatalakestore.net/user/RevoShare
    hadoop fs -mkdir adl://rkadl1.azuredatalakestore.net/user/RevoShare/<user>

    hadoop fs -mkdir adl://rkadl1.azuredatalakestore.net/share

    hadoop fs -copyFromLocal /usr/lib64/R Server-7.4.1/library/RevoScaleR/SampleData/mysamplefile.csv adl://rkadl1.azuredatalakestore.net/share

    hadoop fs –ls adl://rkadl1.azuredatalakestore.net/share


## <a name="use-azure-file-storage-with-ml-services-on-hdinsight"></a>Utilizar o armazenamento de ficheiros do Azure com serviços de ML no HDInsight

Há também uma opção de armazenamento de dados conveniente para utilização no nó de extremidade chamado [ficheiros do Azure](https://azure.microsoft.com/services/storage/files/). Permite-lhe montar uma partilha de ficheiros de armazenamento do Azure para o sistema de ficheiros do Linux. Esta opção pode ser útil para armazenar ficheiros de dados, os R scripts e objetos de resultado que poderão ser necessário posteriormente, especialmente quando faz sentido usar o sistema de arquivos nativo no nó de extremidade em vez de HDFS. 

Uma vantagem principal do serviço ficheiros do Azure é que as partilhas de ficheiros podem ser montadas e utilizadas por qualquer sistema que tenha um SO suportado, como o Windows ou Linux. Por exemplo, pode ser utilizado por outro cluster do HDInsight que ou alguém de sua equipe tem, por uma VM do Azure ou até mesmo por um sistema no local. Para obter mais informações, consulte:

- [How to use Azure File Storage with Linux (Como utilizar o Armazenamento de Ficheiros do Azure com o Linux)](../../storage/files/storage-how-to-use-files-linux.md)
- [Como utilizar o armazenamento de ficheiros do Azure no Windows](../../storage/files/storage-dotnet-how-to-use-files.md)


## <a name="next-steps"></a>Passos Seguintes

* [Descrição geral do cluster de serviços de ML no HDInsight](r-server-overview.md)
* [Opções do contexto de cálculo para o cluster dos Serviços ML no HDInsight](r-server-compute-contexts.md)
* [Utilize a geração 2 de armazenamento do Azure Data Lake com clusters do HDInsight do Azure](../hdinsight-hadoop-use-data-lake-storage-gen2.md)
