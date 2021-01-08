---
title: Copiar dados entre Data Lake Storage Gen1 e Azure SQL - Sqoop Microsoft Docs
description: Utilize o Sqoop para copiar dados entre a Base de Dados Azure SQL e a Azure Data Lake Storage Gen1
services: data-lake-store
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 07/30/2019
ms.author: twooley
ms.openlocfilehash: 9bb787138267fd8a9fab4dea233c1c828b457d67
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/08/2021
ms.locfileid: "92109192"
---
# <a name="copy-data-between-data-lake-storage-gen1-and-azure-sql-database-using-sqoop"></a>Copiar dados entre data lake storage Gen1 e Azure SQL Database usando Sqoop

Saiba como usar o Apache Sqoop para importar e exportar dados entre a Base de Dados Azure SQL e a Azure Data Lake Storage Gen1.

## <a name="what-is-sqoop"></a>O que é Sqoop?

As aplicações de big data são uma escolha natural para o processamento de dados não estruturados e semi-estruturados, tais como registos e ficheiros. No entanto, também pode ter necessidade de processar dados estruturados que são armazenados em bases de dados relacionais.

[Apache Sqoop](https://sqoop.apache.org/docs/1.4.4/SqoopUserGuide.html) é uma ferramenta projetada para transferir dados entre bases de dados relacionais e um grande repositório de dados, como data lake storage gen1. Pode usá-lo para importar dados de um sistema de gestão de bases de dados relacional (RDBMS) como a Base de Dados Azure SQL em Data Lake Storage Gen1. Em seguida, pode transformar e analisar os dados usando grandes cargas de trabalho de dados e, em seguida, exportar os dados de volta para um RDBMS. Neste artigo, utiliza uma base de dados na Base de Dados Azure SQL como base de dados relacional para importar/exportar.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, deve ter o seguinte:

* **Uma assinatura Azure**. Consulte [Obter versão de avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Uma conta Azure Data Lake Storage Gen1**. Para obter instruções sobre como criar a conta, consulte [Começar com a Azure Data Lake Storage Gen1](data-lake-store-get-started-portal.md)
* **Cluster Azure HDInsight** com acesso a uma conta Gen1 de armazenamento de data lake. Consulte [Criar um cluster HDInsight com data lake storage gen1](data-lake-store-hdinsight-hadoop-use-portal.md). Este artigo pressupõe que você tem um cluster HDInsight Linux com acesso à Data Lake Storage Gen1.
* **Base de Dados Azure SQL**. Para obter instruções sobre como criar uma base de dados na Base de Dados Azure SQL, consulte [Criar uma base de dados na Base de Dados Azure SQL](../azure-sql/database/single-database-create-quickstart.md)

## <a name="create-sample-tables-in-the-database"></a>Criar tabelas de amostras na base de dados

1. Para começar, crie duas tabelas de amostras na base de dados. Utilize [o SQL Server Management Studio](../azure-sql/database/connect-query-ssms.md) ou o Visual Studio para ligar à base de dados e, em seguida, executar as seguintes consultas.

    **Criar Quadro1**

    ```tsql
    CREATE TABLE [dbo].[Table1](
    [ID] [int] NOT NULL,
    [FName] [nvarchar](50) NOT NULL,
    [LName] [nvarchar](50) NOT NULL,
     CONSTRAINT [PK_Table_1] PRIMARY KEY CLUSTERED
           (
                  [ID] ASC
           )
    ) ON [PRIMARY]
    GO
    ```

    **Criar Quadro2**

    ```tsql
    CREATE TABLE [dbo].[Table2](
    [ID] [int] NOT NULL,
    [FName] [nvarchar](50) NOT NULL,
    [LName] [nvarchar](50) NOT NULL,
     CONSTRAINT [PK_Table_2] PRIMARY KEY CLUSTERED
           (
                  [ID] ASC
           )
    ) ON [PRIMARY]
    GO
    ```

1. Executar o seguinte comando para adicionar alguns dados da amostra ao **Quadro1**. Deixe **a Mesa2** vazia. Mais tarde, importará dados do **Quadro1** para a Data Lake Storage Gen1. Em seguida, você exportará dados da Data Lake Storage Gen1 para o **Quadro2.**

    ```tsql
    INSERT INTO [dbo].[Table1] VALUES (1,'Neal','Kell'), (2,'Lila','Fulton'), (3, 'Erna','Myers'), (4,'Annette','Simpson');
    ```

## <a name="use-sqoop-from-an-hdinsight-cluster-with-access-to-data-lake-storage-gen1"></a>Use sqoop de um cluster HDInsight com acesso a Data Lake Storage Gen1

Um cluster HDInsight já tem os pacotes Sqoop disponíveis. Se configurar o cluster HDInsight para utilizar a Data Lake Storage Gen1 como armazenamento adicional, pode utilizar o Sqoop (sem alterações de configuração) para importar/exportar dados entre uma base de dados relacional, como a Base de Dados SQL do Azure, e uma conta Gen1 de armazenamento de data lake.

1. Para este artigo, assumimos que criou um cluster Linux, pelo que deve usar o SSH para se conectar ao cluster. Consulte [Connect a um cluster HDInsight baseado em Linux](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md).

1. Verifique se pode aceder à conta Desempato de Armazenamento de Dados Gen1 a partir do cluster. Executar o seguinte comando a partir do pedido do SSH:

    ```console
    hdfs dfs -ls adl://<data_lake_storage_gen1_account>.azuredatalakestore.net/
    ```

   Este comando fornece uma lista de ficheiros/pastas na conta Da Gen1 de armazenamento de data lake.

### <a name="import-data-from-azure-sql-database-into-data-lake-storage-gen1"></a>Dados de importação da Base de Dados Azure SQL para data lake storage gen1

1. Navegue para o diretório onde os pacotes Sqoop estão disponíveis. Normalmente, esta localização `/usr/hdp/<version>/sqoop/bin` é.

1. Importe os dados do **Quadro1** para a conta da Data Lake Storage Gen1. Utilize a seguinte sintaxe:

    ```console
    sqoop-import --connect "jdbc:sqlserver://<sql-database-server-name>.database.windows.net:1433;username=<username>@<sql-database-server-name>;password=<password>;database=<sql-database-name>" --table Table1 --target-dir adl://<data-lake-storage-gen1-name>.azuredatalakestore.net/Sqoop/SqoopImportTable1
    ```

   O **espaço-suporte de um servidor de base de dados sql** representa o nome do servidor onde a base de dados está em funcionamento. **o espaço reservado para o nome da base de dados sql** representa o nome da base de dados real.

   Por exemplo,

    ```console
    sqoop-import --connect "jdbc:sqlserver://mysqoopserver.database.windows.net:1433;username=user1@mysqoopserver;password=<password>;database=mysqoopdatabase" --table Table1 --target-dir adl://myadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1
    ```

1. Verifique se os dados foram transferidos para a conta da Data Lake Storage Gen1. Execute o seguinte comando:

    ```console
    hdfs dfs -ls adl://hdiadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1/
    ```

   Deverá ver o seguinte resultado.

    ```console
    -rwxrwxrwx   0 sshuser hdfs          0 2016-02-26 21:09 adl://hdiadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1/_SUCCESS
    -rwxrwxrwx   0 sshuser hdfs         12 2016-02-26 21:09 adl://hdiadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1/part-m-00000
    -rwxrwxrwx   0 sshuser hdfs         14 2016-02-26 21:09 adl://hdiadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1/part-m-00001
    -rwxrwxrwx   0 sshuser hdfs         13 2016-02-26 21:09 adl://hdiadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1/part-m-00002
    -rwxrwxrwx   0 sshuser hdfs         18 2016-02-26 21:09 adl://hdiadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1/part-m-00003
    ```

   Cada **ficheiro parte-m-_** corresponde a uma linha na tabela de origem, _ *Table1**. Pode ver o conteúdo dos ficheiros part-m* para verificar.

### <a name="export-data-from-data-lake-storage-gen1-into-azure-sql-database"></a>Dados de exportação da Data Lake Storage Gen1 para a Base de Dados Azure SQL

1. Exporte os dados da conta Desarrumação gen1 do Data Lake para a tabela vazia, **Table2,** na Base de Dados Azure SQL. Utilize a seguinte sintaxe.

    ```console
    sqoop-export --connect "jdbc:sqlserver://<sql-database-server-name>.database.windows.net:1433;username=<username>@<sql-database-server-name>;password=<password>;database=<sql-database-name>" --table Table2 --export-dir adl://<data-lake-storage-gen1-name>.azuredatalakestore.net/Sqoop/SqoopImportTable1 --input-fields-terminated-by ","
    ```

   Por exemplo,

    ```console
    sqoop-export --connect "jdbc:sqlserver://mysqoopserver.database.windows.net:1433;username=user1@mysqoopserver;password=<password>;database=mysqoopdatabase" --table Table2 --export-dir adl://myadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1 --input-fields-terminated-by ","
    ```

1. Verifique se os dados foram enviados para a tabela sql Database. Utilize [o SQL Server Management Studio](../azure-sql/database/connect-query-ssms.md) ou o Visual Studio para ligar à Base de Dados Azure SQL e, em seguida, executar a seguinte consulta.

    ```tsql
    SELECT * FROM TABLE2
    ```

   Este comando deve ter a seguinte saída.

    ```output
     ID  FName    LName
    -------------------
    1    Neal     Kell
    2    Lila     Fulton
    3    Erna     Myers
    4    Annette  Simpson
    ```

## <a name="performance-considerations-while-using-sqoop"></a>Considerações de desempenho durante a utilização do Sqoop

Para obter informações sobre o desempenho que ajusta o seu trabalho sqoop para copiar dados para Data Lake Storage Gen1, consulte o post do [blog de desempenho sqoop](/archive/blogs/shanyu/performance-tuning-for-hdinsight-storm-and-microsoft-azure-eventhubs).

## <a name="next-steps"></a>Próximos passos

* [Copiar dados de Azure Storage Blobs para Data Lake Storage Gen1](data-lake-store-copy-data-azure-storage-blob.md)
* [Proteger dados no Armazenamento do Data Lake Ger1](data-lake-store-secure-data.md)
* [Use Azure Data Lake Analytics com data lake storage gen1](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Use Azure HDInsight com data lake storage gen1](data-lake-store-hdinsight-hadoop-use-portal.md)