---
title: Soluções de armazenamento Azure para serviços ML no HDInsight - Azure
description: Conheça as diferentes opções de armazenamento disponíveis com os Serviços ML no HDInsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/02/2020
ms.openlocfilehash: 1c79d0390a80a1358ddb09707fbabf6a5a2affdc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75660244"
---
# <a name="azure-storage-solutions-for-ml-services-on-azure-hdinsight"></a>Soluções de armazenamento Azure para serviços ML no Azure HDInsight

Os serviços ML no HDInsight podem utilizar diferentes soluções de armazenamento para persistir dados, códigos ou objetos que contenham resultados da análise. Estas soluções incluem as seguintes opções:

- [Blob do Azure](https://azure.microsoft.com/services/storage/blobs/)
- [Azure Data Lake Storage](https://azure.microsoft.com/services/storage/data-lake-storage/)
- [Armazenamento de arquivo sinuoso](https://azure.microsoft.com/services/storage/files/)

Também tem a opção de aceder a várias contas de armazenamento do Azure ou contentores com o seu cluster HDInsight. O armazenamento de ficheiros Azure é uma opção conveniente de armazenamento de dados para utilização no nó de borda que lhe permite montar uma partilha de ficheiros de armazenamento Azure para, por exemplo, o sistema de ficheiros Linux. Mas as ações do Ficheiro Azure podem ser montadas e utilizadas por qualquer sistema que tenha um sistema operativo suportado, como o Windows ou o Linux.

Quando cria um cluster Apache Hadoop no HDInsight, especifica uma conta **de Armazenamento Azure** ou armazenamento de **data lake**. Um recipiente de armazenamento específico dessa conta detém o sistema de ficheiros para o cluster que cria (por exemplo, o Sistema de Ficheiros Distribuídos Hadoop). Para mais informações e orientação, consulte:

- [Utilize o Armazenamento Azure com HDInsight](../hdinsight-hadoop-use-blob-storage.md)
- [Utilize armazenamento de data lake com clusters Azure HDInsight](../hdinsight-hadoop-use-data-lake-store.md)

## <a name="use-azure-blob-storage-accounts-with-ml-services-cluster"></a>Utilize contas de armazenamento Azure Blob com cluster ml services

Se especificou mais de uma conta de armazenamento ao criar o seu cluster ml Services, as seguintes instruções explicam como utilizar uma conta secundária para acesso a dados e operações num cluster de Serviços ML. Assuma as seguintes contas de armazenamento e recipiente: **armazenamento1** e um recipiente predefinido chamado **contentor1**, e **armazenamento2** com **recipiente2**.

> [!WARNING]  
> Para efeitos de desempenho, o cluster HDInsight é criado no mesmo centro de dados que a conta de armazenamento primária que especifica. A utilização de uma conta de armazenamento num local diferente do cluster HDInsight não é suportada.

### <a name="use-the-default-storage-with-ml-services-on-hdinsight"></a>Utilize o armazenamento predefinido com serviços ML no HDInsight

1. Utilizando um cliente SSH, ligue-se ao nó de borda do seu cluster. Para obter informações sobre a utilização de SSH com clusters HDInsight, consulte [Use SSH com HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).
  
2. Copie um ficheiro de amostra, mysamplefile.csv, para o diretório /share.

    ```bash
    hadoop fs –mkdir /share
    hadoop fs –copyFromLocal mycsv.scv /share
    ```

3. Mude para R Studio ou outra consola R e escreva código R para definir o nó de nome para **padrão** e localização do ficheiro a que pretende aceder.  

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

Todas as referências de diretório e `wasbs://container1@storage1.blob.core.windows.net`arquivo apontam para a conta de armazenamento . Esta é a conta de **armazenamento padrão** que está associada ao cluster HDInsight.

### <a name="use-the-additional-storage-with-ml-services-on-hdinsight"></a>Utilize o armazenamento adicional com serviços ML no HDInsight

Agora, suponha que queira processar um ficheiro chamado mysamplefile1.csv que está localizado no /diretório privado do **contentor2** no **armazenamento2**.

No seu código R, aponte a referência do nó de nome à conta de **armazenamento2.**

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

Todas as referências de diretório e arquivo `wasbs://container2@storage2.blob.core.windows.net`apontam agora para a conta de armazenamento . Este é o nó de **nome** que especificou.

Configure `/user/RevoShare/<SSH username>` o diretório no **armazenamento2** da seguinte forma:

```bash
hadoop fs -mkdir wasbs://container2@storage2.blob.core.windows.net/user
hadoop fs -mkdir wasbs://container2@storage2.blob.core.windows.net/user/RevoShare
hadoop fs -mkdir wasbs://container2@storage2.blob.core.windows.net/user/RevoShare/<RDP username>
```

## <a name="use-azure-data-lake-storage-with-ml-services-cluster"></a>Utilize o armazenamento de lagos de dados azure com cluster de serviços ML

Para utilizar o Armazenamento de Data Lake com o seu cluster HDInsight, precisa de dar ao seu cluster acesso a cada Armazenamento de Lago de Dados Azure que pretende utilizar. Para obter instruções sobre como usar o portal Azure para criar um cluster HDInsight com uma conta de Armazenamento de Lago de Dados Azure como armazenamento padrão ou como armazenamento adicional, consulte [Criar um cluster HDInsight com armazenamento](../../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md)de data lake usando o portal Azure .

Em seguida, utilize o armazenamento no seu script R tal como fez uma conta de armazenamento secundária do Azure, tal como descrito no procedimento anterior.

### <a name="add-cluster-access-to-your-azure-data-lake-storage"></a>Adicione acesso ao cluster ao seu Armazenamento de Lago de Dados Azure

Acede ao Data Lake Storage utilizando um Diretor ativo azure (Azure AD) diretor de serviço que está associado ao seu cluster HDInsight.

1. Quando criar o seu cluster HDInsight, selecione **Identidade AAD** cluster a partir do separador **Data Source.**

2. Na caixa de diálogo de **identidade AAD cluster,** sob o **comando do Serviço Select AD,** selecione **Criar novo**.

Depois de dar um nome ao Diretor de Serviço e criar uma senha para o mesmo, clique em Gerir o **Acesso ADLS** para associar o Diretor de Serviço ao seu Armazenamento de Data Lake.

Também é possível adicionar acesso ao cluster a uma ou mais contas de armazenamento do Data Lake após a criação do cluster. Abra a entrada do portal Azure para um Armazenamento de Data Lake e vá ao **Data Explorer > Access > Add**.

### <a name="how-to-access-data-lake-storage-gen1-from-ml-services-on-hdinsight"></a>Como aceder ao Data Lake Storage Gen1 a partir de serviços ML no HDInsight

Assim que tiver acesso ao Data Lake Storage Gen1, pode utilizar o armazenamento no cluster ML Services no HDInsight da forma como seria uma conta de armazenamento Secundária do Azure. A única diferença é que o prefixo **wasbs://** altera-se para **adl://** da seguinte forma:

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

Os seguintes comandos são utilizados para configurar a conta Data Lake Storage Gen1 com o diretório RevoShare e adicionar o ficheiro .csv da amostra do exemplo anterior:

```bash
hadoop fs -mkdir adl://rkadl1.azuredatalakestore.net/user
hadoop fs -mkdir adl://rkadl1.azuredatalakestore.net/user/RevoShare
hadoop fs -mkdir adl://rkadl1.azuredatalakestore.net/user/RevoShare/<user>

hadoop fs -mkdir adl://rkadl1.azuredatalakestore.net/share

hadoop fs -copyFromLocal /usr/lib64/R Server-7.4.1/library/RevoScaleR/SampleData/mysamplefile.csv adl://rkadl1.azuredatalakestore.net/share

hadoop fs –ls adl://rkadl1.azuredatalakestore.net/share
```

## <a name="use-azure-file-storage-with-ml-services-on-hdinsight"></a>Utilize o armazenamento de ficheiros Azure com serviços ML no HDInsight

Há também uma opção conveniente de armazenamento de dados para uso no nó de borda chamado [Ficheiros Azure](https://azure.microsoft.com/services/storage/files/). Permite-lhe montar uma partilha de ficheiros Azure Storage no sistema de ficheiros Linux. Esta opção pode ser útil para armazenar ficheiros de dados, scripts R e objetos de resultadoque podem ser necessários mais tarde, especialmente quando faz sentido usar o sistema de ficheiros nativo no nó de borda em vez de HDFS.

Um dos principais benefícios do Azure Files é que as ações de ficheiro podem ser montadas e utilizadas por qualquer sistema que tenha um SISTEMA suportado, como windows ou Linux. Por exemplo, pode ser usado por outro cluster HDInsight que você ou alguém da sua equipa tem, por um VM Azure, ou mesmo por um sistema no local. Para obter mais informações, consulte:

- [How to use Azure File Storage with Linux (Como utilizar o Armazenamento de Ficheiros do Azure com o Linux)](../../storage/files/storage-how-to-use-files-linux.md)
- [Como utilizar o armazenamento de ficheiros Azure no Windows](../../storage/files/storage-dotnet-how-to-use-files.md)

## <a name="next-steps"></a>Passos seguintes

- [Visão geral do cluster de serviços ML no HDInsight](r-server-overview.md)
- [Opções do contexto de cálculo para o cluster dos Serviços ML no HDInsight](r-server-compute-contexts.md)
- [Utilizar o Azure Data Lake Storage Gen2 com clusters do Azure HDInsight](../hdinsight-hadoop-use-data-lake-storage-gen2.md)
