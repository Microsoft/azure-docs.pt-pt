---
title: Soluções de armazenamento do Azure para serviços de ML no HDInsight – Azure
description: Saiba mais sobre as diferentes opções de armazenamento disponíveis com os serviços de ML no HDInsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/02/2020
ms.openlocfilehash: 1c79d0390a80a1358ddb09707fbabf6a5a2affdc
ms.sourcegitcommit: 51ed913864f11e78a4a98599b55bbb036550d8a5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/04/2020
ms.locfileid: "75660244"
---
# <a name="azure-storage-solutions-for-ml-services-on-azure-hdinsight"></a>Soluções de armazenamento do Azure para serviços de ML no Azure HDInsight

Os serviços de ML no HDInsight podem usar diferentes soluções de armazenamento para manter dados, código ou objetos que contêm resultados da análise. Essas soluções incluem as seguintes opções:

- [Blob do Azure](https://azure.microsoft.com/services/storage/blobs/)
- [Azure Data Lake Storage](https://azure.microsoft.com/services/storage/data-lake-storage/)
- [Armazenamento de arquivos do Azure](https://azure.microsoft.com/services/storage/files/)

Você também tem a opção de acessar várias contas de armazenamento do Azure ou contêineres com seu cluster HDInsight. O armazenamento de arquivos do Azure é uma opção de armazenamento de dados conveniente para uso no nó de borda que permite montar um compartilhamento de arquivos do armazenamento do Azure para, por exemplo, o sistema de arquivos do Linux. Mas os compartilhamentos de arquivos do Azure podem ser montados e usados por qualquer sistema que tenha um sistema operacional com suporte, como Windows ou Linux.

Ao criar um cluster Apache Hadoop no HDInsight, você especifica uma conta de **armazenamento do Azure** ou **Data Lake Storage**. Um contêiner de armazenamento específico dessa conta contém o sistema de arquivos para o cluster que você cria (por exemplo, o Sistema de Arquivos Distribuído do Hadoop). Para obter mais informações e diretrizes, consulte:

- [Usar o armazenamento do Azure com o HDInsight](../hdinsight-hadoop-use-blob-storage.md)
- [Usar Data Lake Storage com clusters do Azure HDInsight](../hdinsight-hadoop-use-data-lake-store.md)

## <a name="use-azure-blob-storage-accounts-with-ml-services-cluster"></a>Usar contas de armazenamento de BLOBs do Azure com o cluster de serviços am

Se você tiver especificado mais de uma conta de armazenamento ao criar o cluster de serviços ML, as instruções a seguir explicarão como usar uma conta secundária para acesso a dados e operações em um cluster de serviços am. Suponha as seguintes contas de armazenamento e contêiner: **storage1** e um contêiner padrão chamado **Container1**e **storage2** com **container2**.

> [!WARNING]  
> Para fins de desempenho, o cluster HDInsight é criado na mesma data center que a conta de armazenamento primária que você especificar. Não há suporte para o uso de uma conta de armazenamento em um local diferente do cluster HDInsight.

### <a name="use-the-default-storage-with-ml-services-on-hdinsight"></a>Usar o armazenamento padrão com serviços de ML no HDInsight

1. Usando um cliente SSH, conecte-se ao nó de borda do cluster. Para obter informações sobre como usar SSH com clusters HDInsight, consulte [usar SSH com HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).
  
2. Copie um arquivo de exemplo, mysamplefile. csv, para o diretório/share.

    ```bash
    hadoop fs –mkdir /share
    hadoop fs –copyFromLocal mycsv.scv /share
    ```

3. Alterne para o R Studio ou outro console do R e escreva o código R para definir o nó de nome como **padrão** e o local do arquivo que você deseja acessar.  

    ```R
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
    ```

Todas as referências de diretório e arquivo apontam para a conta de armazenamento `wasbs://container1@storage1.blob.core.windows.net`. Essa é a **conta de armazenamento padrão** associada ao cluster HDInsight.

### <a name="use-the-additional-storage-with-ml-services-on-hdinsight"></a>Usar o armazenamento adicional com os serviços de ML no HDInsight

Agora, suponha que você deseja processar um arquivo chamado mysamplefile1. csv que está localizado no diretório/Private de **container2** no **storage2**.

Em seu código R, aponte a referência de nó de nome para a conta de armazenamento **storage2** .

```R
myNameNode <- "wasbs://container2@storage2.blob.core.windows.net"
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
```

Todas as referências de diretório e arquivo agora apontam para a conta de armazenamento `wasbs://container2@storage2.blob.core.windows.net`. Este é o **nó de nome** que você especificou.

Configure o diretório `/user/RevoShare/<SSH username>` no **storage2** da seguinte maneira:

```bash
hadoop fs -mkdir wasbs://container2@storage2.blob.core.windows.net/user
hadoop fs -mkdir wasbs://container2@storage2.blob.core.windows.net/user/RevoShare
hadoop fs -mkdir wasbs://container2@storage2.blob.core.windows.net/user/RevoShare/<RDP username>
```

## <a name="use-azure-data-lake-storage-with-ml-services-cluster"></a>Usar Azure Data Lake Storage com cluster de serviços ML

Para usar Data Lake Storage com o cluster HDInsight, você precisa dar acesso ao cluster para cada Azure Data Lake Storage que você deseja usar. Para obter instruções sobre como usar o portal do Azure para criar um cluster HDInsight com uma conta de Azure Data Lake Storage como o armazenamento padrão ou como armazenamento adicional, consulte [criar um cluster hdinsight com data Lake Storage usando portal do Azure](../../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md).

Em seguida, você usa o armazenamento em seu script R de forma semelhante a você fez uma conta de armazenamento do Azure secundária, conforme descrito no procedimento anterior.

### <a name="add-cluster-access-to-your-azure-data-lake-storage"></a>Adicionar acesso de cluster ao seu Azure Data Lake Storage

Você acessa Data Lake Storage usando uma entidade de serviço do Azure Active Directory (Azure AD) que está associada ao cluster HDInsight.

1. Ao criar o cluster HDInsight, selecione **identidade AAD de cluster** na guia **fonte de dados** .

2. Na caixa de diálogo **identidade AAD do cluster** , em **selecionar entidade de serviço do AD**, selecione **criar novo**.

Depois de dar um nome à entidade de serviço e criar uma senha para ela, clique em **gerenciar acesso ADLS** para associar a entidade de serviço ao seu data Lake Storage.

Também é possível adicionar acesso de cluster a uma ou mais contas de armazenamento de Data Lake após a criação do cluster. Abra a entrada de portal do Azure para uma Data Lake Storage e vá para **Data Explorer > acessar > adicionar**.

### <a name="how-to-access-data-lake-storage-gen1-from-ml-services-on-hdinsight"></a>Como acessar Data Lake Storage Gen1 dos serviços de ML no HDInsight

Depois de ter concedido acesso ao Data Lake Storage Gen1, você poderá usar o armazenamento no cluster de serviços ML no HDInsight da forma como faria com uma conta de armazenamento do Azure secundária. A única diferença é que o prefixo **wasbs://** muda para **ADL://** da seguinte maneira:

```R
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
```

Os comandos a seguir são usados para configurar a conta de Data Lake Storage Gen1 com o diretório RevoShare e adicionar o arquivo. csv de exemplo do exemplo anterior:

```bash
hadoop fs -mkdir adl://rkadl1.azuredatalakestore.net/user
hadoop fs -mkdir adl://rkadl1.azuredatalakestore.net/user/RevoShare
hadoop fs -mkdir adl://rkadl1.azuredatalakestore.net/user/RevoShare/<user>

hadoop fs -mkdir adl://rkadl1.azuredatalakestore.net/share

hadoop fs -copyFromLocal /usr/lib64/R Server-7.4.1/library/RevoScaleR/SampleData/mysamplefile.csv adl://rkadl1.azuredatalakestore.net/share

hadoop fs –ls adl://rkadl1.azuredatalakestore.net/share
```

## <a name="use-azure-file-storage-with-ml-services-on-hdinsight"></a>Usar o armazenamento de arquivos do Azure com serviços de ML no HDInsight

Também há uma opção de armazenamento de dados conveniente para uso no nó de borda chamado [arquivos do Azure](https://azure.microsoft.com/services/storage/files/). Ele permite que você monte um compartilhamento de arquivos do armazenamento do Azure para o sistema de arquivos do Linux. Essa opção pode ser útil para armazenar arquivos de dados, scripts R e objetos de resultado que podem ser necessários mais tarde, especialmente quando faz sentido usar o sistema de arquivos nativo no nó de borda em vez de HDFS.

Uma grande vantagem dos arquivos do Azure é que os compartilhamentos de arquivos podem ser montados e usados por qualquer sistema que tenha um sistema operacional com suporte, como Windows ou Linux. Por exemplo, ele pode ser usado por outro cluster HDInsight que você ou alguém da sua equipe tem, por uma VM do Azure ou até mesmo por um sistema local. Para obter mais informações, veja:

- [How to use Azure File Storage with Linux (Como utilizar o Armazenamento de Ficheiros do Azure com o Linux)](../../storage/files/storage-how-to-use-files-linux.md)
- [Como usar o armazenamento de arquivos do Azure no Windows](../../storage/files/storage-dotnet-how-to-use-files.md)

## <a name="next-steps"></a>Passos seguintes

- [Visão geral do cluster de serviços do ML no HDInsight](r-server-overview.md)
- [Opções do contexto de cálculo para o cluster dos Serviços ML no HDInsight](r-server-compute-contexts.md)
- [Utilizar o Azure Data Lake Storage Gen2 com clusters do Azure HDInsight](../hdinsight-hadoop-use-data-lake-storage-gen2.md)
