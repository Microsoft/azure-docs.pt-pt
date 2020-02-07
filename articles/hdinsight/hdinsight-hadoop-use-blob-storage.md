---
title: Consultar dados a partir do armazenamento do Azure compatível com HDFS - Azure HDInsight
description: Saiba como consultar dados do armazenamento do Azure e do Armazenamento do Lago Azure Data para armazenar os resultados da sua análise.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 11/01/2019
ms.openlocfilehash: 55cddf5317938dea353517cde7260a1aa531d1df
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/07/2020
ms.locfileid: "77061263"
---
# <a name="use-azure-storage-with-azure-hdinsight-clusters"></a>Utilizar o armazenamento do Azure com clusters do Azure HDInsight

Para analisar dados no cluster HDInsight, pode armazenar os dados no [Azure Storage,](../storage/common/storage-introduction.md) [Azure Data Lake Storage Gen 1](../data-lake-store/data-lake-store-overview.md)/[Azure Data Lake Storage Gen 2,](../storage/blobs/data-lake-storage-introduction.md)ou uma combinação. Estas opções de armazenamento permitem-lhe eliminar com segurança os clusters HDInsight que são utilizados para a computação sem perder dados do utilizador.

Apache Hadoop suporta uma noção do sistema de ficheiros padrão. O sistema de ficheiros predefinido implica um esquema e uma autoridade predefinidos. Também pode ser utilizado para resolver caminhos relativos. Durante o processo de criação de cluster HDInsight, pode especificar um recipiente blob no Armazenamento Azure como sistema de ficheiros padrão, ou com hDInsight 3.6, pode selecionar o Armazenamento Azure ou o Armazenamento do Lago Azure Gen 1/ Azure Data Lake Storage Gen 2 como o sistema de ficheiros predefinido com algumas exceções. Para a capacidade de suporte da utilização do Data Lake Storage Gen 1 como armazenamento padrão e armazenamento ligado, consulte [disponibilidade para cluster HDInsight](./hdinsight-hadoop-use-data-lake-store.md#availability-for-hdinsight-clusters).

Neste artigo, ficará a saber como o Armazenamento do Azure funciona com clusters do HDInsight. Para saber como funciona o Data Lake Storage Gen 1 com os clusters HDInsight, consulte o Armazenamento do [Lago De dados Azure com clusters Azure HDInsight](hdinsight-hadoop-use-data-lake-store.md). Para obter mais informações sobre a criação de um cluster HDInsight, consulte [Create Apache Hadoop clusters em HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

> [!IMPORTANT]  
> O tipo de conta de armazenamento **BlobStorage** só pode ser usado como armazenamento secundário para clusters HDInsight.

| Tipo de conta de armazenamento | Serviços suportados | Níveis de desempenho com suporte | Níveis de acesso com suporte |
|----------------------|--------------------|-----------------------------|------------------------|
| StorageV2 (general-purpose v2)  | Blobs     | Standard                    | Hot, Cool, Archive\*   |
| Armazenamento (v1 de uso geral)   | Blobs     | Standard                    | N/A                    |
| BlobStorage                    | Blobs     | Standard                    | Hot, Cool, Archive\*   |

Não recomendamos que utilize o recipiente de blob predefinido para armazenar dados de negócio. Eliminar o contentor de blobs predefinido depois de cada utilização para reduzir o custo de armazenamento é uma prática recomendada. O recipiente predefinido contém registos de aplicação e sistema. Certifique-se de que obtém os registos antes de eliminar o contentor.

Partilhar um recipiente de bolha como o sistema de ficheiros padrão para vários clusters não é suportado.

> [!NOTE]  
> O nível de acesso do Arquivo é um nível offline que tem uma latência de recuperação de várias horas e não é recomendado para uso com HDInsight. Para mais informações, consulte o [nível de acesso do Arquivo.](../storage/blobs/storage-blob-storage-tiers.md#archive-access-tier)

## <a name="access-files-from-the-cluster"></a>Aceder a ficheiros a partir do cluster

Existem várias formas de aceder aos ficheiros no Armazenamento de Data Lake a partir de um cluster HDInsight. O esquema URI fornece acesso não encriptado (com o prefixo *wasb:* ) e acesso encriptado por SSL (com *wasbs*). Recomendamos a utilização de *wasbs* sempre que possível, mesmo ao aceder a dados que se encontrem dentro da mesma região no Azure.

* **Utilizar o nome completamente qualificado**. Com esta abordagem, fornece o caminho completo para o ficheiro ao qual pretende aceder.

    ```
    wasb://<containername>@<accountname>.blob.core.windows.net/<file.path>/
    wasbs://<containername>@<accountname>.blob.core.windows.net/<file.path>/
    ```

* **Utilizando o formato de caminho abreviado**. Com essa abordagem, você substitui o caminho até a raiz do cluster por:

    ```
    wasb:///<file.path>/
    wasbs:///<file.path>/
    ```

* **Utilizar o caminho relativo**. Com esta abordagem, fornece apenas o caminho relativo para o ficheiro ao qual pretende aceder.

    ```
    /<file.path>/
    ```

### <a name="data-access-examples"></a>Exemplos de acesso a dados

Exemplos são baseados numa [ligação ssh](./hdinsight-hadoop-linux-use-ssh-unix.md) ao nó da cabeça do cluster. Os exemplos usam todos os três esquemas de URI. Substitua `CONTAINERNAME` e `STORAGEACCOUNT` com os valores relevantes

#### <a name="a-few-hdfs-commands"></a>Alguns comandos do HDFS

1. Crie um arquivo simples no armazenamento local.

    ```bash
    touch testFile.txt
    ```

1. Crie diretórios no armazenamento de cluster.

    ```bash
    hdfs dfs -mkdir wasbs://CONTAINERNAME@STORAGEACCOUNT.blob.core.windows.net/sampledata1/
    hdfs dfs -mkdir wasbs:///sampledata2/
    hdfs dfs -mkdir /sampledata3/
    ```

1. Copie dados do armazenamento local para o armazenamento de cluster.

    ```bash
    hdfs dfs -copyFromLocal testFile.txt  wasbs://CONTAINERNAME@STORAGEACCOUNT.blob.core.windows.net/sampledata1/
    hdfs dfs -copyFromLocal testFile.txt  wasbs:///sampledata2/
    hdfs dfs -copyFromLocal testFile.txt  /sampledata3/
    ```

1. Liste o conteúdo do diretório no armazenamento de cluster.

    ```bash
    hdfs dfs -ls wasbs://CONTAINERNAME@STORAGEACCOUNT.blob.core.windows.net/sampledata1/
    hdfs dfs -ls wasbs:///sampledata2/
    hdfs dfs -ls /sampledata3/
    ```

> [!NOTE]  
> Ao trabalhar com blobs fora do HDInsight, a maioria dos utilitários não reconhece o formato WASB e, em vez disso, espera um formato de caminho básico, tal como `example/jars/hadoop-mapreduce-examples.jar`.

#### <a name="creating-a-hive-table"></a>Criando uma tabela Hive

Três locais de arquivo são mostrados para fins ilustrativos. Para a execução real, utilize apenas uma das entradas `LOCATION`.

```hql
DROP TABLE myTable;
CREATE EXTERNAL TABLE myTable (
    t1 string,
    t2 string,
    t3 string,
    t4 string,
    t5 string,
    t6 string,
    t7 string)
ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
STORED AS TEXTFILE
LOCATION 'wasbs://CONTAINERNAME@STORAGEACCOUNT.blob.core.windows.net/example/data/';
LOCATION 'wasbs:///example/data/';
LOCATION '/example/data/';
```

## <a name="identify-storage-path-from-ambari"></a>Identifique o caminho de armazenamento de Ambari

* Para identificar o caminho completo para a loja predefinida configurada, navegue para:

    **HDFS** > **Configs** e introduza `fs.defaultFS` na caixa de entrada do filtro.

* Para verificar se a loja wasb está configurada como armazenamento secundário, navegue para:

    **HDFS** > **Configs** e introduza `blob.core.windows.net` na caixa de entrada do filtro.

## <a name="blob-containers"></a>Recipientes blob

Para utilizar bolhas, cria-se primeiro uma [conta de Armazenamento Azure.](../storage/common/storage-create-storage-account.md) Como parte deste processo, deve especificar uma região do Azure onde será criada a conta de armazenamento. O cluster e a conta do Storage têm de estar alojados na mesma região. A base de dados do Servidor SQL da metaloja Hive e a base de dados do Servidor SQL da metastore Apache Oozie também devem estar localizadas na mesma região.

Independentemente do local onde se encontre, cada blob que criar pertence a um contentor na sua conta do Storage do Azure. Este contentor pode ser um blob existente que tenha sido criado fora do HDInsight ou um contentor criado para um cluster do HDInsight.

O contentor de blobs predefinido armazena informações específicas do cluster, como o histórico de tarefas e os registos. Não partilhe um contentor de blobs predefinido com vários clusters do HDInsight. Isto pode danificar o histórico de tarefas. Recomenda-se a utilização de um recipiente diferente para cada cluster e a colocação de dados partilhados numa conta de armazenamento ligada especificada na implementação de todos os clusters relevantes em vez da conta de armazenamento predefinida. Para obter mais informações sobre a configuração de contas de armazenamento ligadas, consulte [Create HDInsight clusters](hdinsight-hadoop-provision-linux-clusters.md). No entanto, pode reutilizar um contentor de armazenamento predefinido depois de o cluster do HDInsight original ser eliminado. Para os clusters HBase, pode realmente manter o esquema e os dados da tabela HBase através da criação de um novo cluster HBase utilizando o recipiente de bolha padrão que é usado por um cluster HBase que foi eliminado.

[!INCLUDE [secure-transfer-enabled-storage-account](../../includes/hdinsight-secure-transfer.md)]

## <a name="interacting-with-azure-storage"></a>Interagindo com o armazenamento Azure

A Microsoft fornece as seguintes ferramentas para trabalhar com o Armazenamento Azure:

| Ferramenta | Linux | OS X | Portal do |
| --- |:---:|:---:|:---:|
| [Portal do Azure](../storage/blobs/storage-quickstart-blobs-portal.md) |✔ |✔ |✔ |
| [CLI do Azure](../storage/blobs/storage-quickstart-blobs-cli.md) |✔ |✔ |✔ |
| [Azure PowerShell](../storage/blobs/storage-quickstart-blobs-powershell.md) | | |✔ |
| [AZCopy](../storage/common/storage-use-azcopy-v10.md) |✔ | |✔ |

## <a name="use-additional-storage-accounts"></a>Utilizar contas de armazenamento adicionais

Ao criar um cluster do HDInsight, especifica a conta de armazenamento do Azure que quer associar ao mesmo. Além desta conta de armazenamento, pode adicionar mais contas de armazenamento da mesma subscrição do Azure ou de diferentes subscrições do Azure durante o processo de criação ou depois de um cluster ter sido criado. Para obter instruções sobre como adicionar mais contas do Storage, consulte [Create HDInsight clusters (Criar clusters do HDInsight)](hdinsight-hadoop-provision-linux-clusters.md).

> [!WARNING]  
> Não é suportado utilizar uma conta de armazenamento adicional numa localização diferente do cluster do HDInsight.

## <a name="next-steps"></a>Passos seguintes

Neste artigo, aprendeu a utilizar o armazenamento do Azure compatível com HDFS através do HDInsight. Isto permite-lhe criar soluções de aquisição de dados para arquivo de longo prazo dimensionáveis e utilizar o HDInsight para aceder às informações contidas nos dados estruturados e não estruturados armazenados.

Para obter mais informações, consulte:

* [Começar com Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [Começar com o Armazenamento do Lago De Dados Azure](../data-lake-store/data-lake-store-get-started-portal.md)
* [Enviar dados para HDInsight](hdinsight-upload-data.md)
* [Use a Colmeia Apache com HDInsight](hadoop/hdinsight-use-hive.md)
* [Utilize assinaturas de acesso partilhado de armazenamento Azure para restringir o acesso a dados com hDInsight](hdinsight-storage-sharedaccesssignature-permissions.md)
* [Utilizar o Azure Data Lake Storage Gen2 com clusters do Azure HDInsight](hdinsight-hadoop-use-data-lake-storage-gen2.md)
* [Tutorial: Extrair, transformar e carregar dados usando consulta interativa no Azure HDInsight](./interactive-query/interactive-query-tutorial-analyze-flight-data.md)
