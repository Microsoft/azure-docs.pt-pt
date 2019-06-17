---
title: Copiar dados entre Gen1 de armazenamento do Azure Data Lake e base de dados SQL do Azure com o Sqoop | Documentos da Microsoft
description: Utilize o Sqoop para copiar dados entre a base de dados do Azure SQL e de geração 1 de armazenamento do Azure Data Lake
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.assetid: 3f914b2a-83cc-4950-b3f7-69c921851683
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 7d3283b03d15278d1f7fd42a72b154dab1a442b4
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60878771"
---
# <a name="copy-data-between-azure-data-lake-storage-gen1-and-azure-sql-database-using-sqoop"></a>Copiar dados entre Gen1 de armazenamento do Azure Data Lake e base de dados SQL do Azure com o Sqoop
Saiba como utilizar o Apache Sqoop para importar e exportar dados entre a base de dados do Azure SQL e de geração 1 de armazenamento do Azure Data Lake.

## <a name="what-is-sqoop"></a>O que é o Sqoop?
Aplicações de macrodados são uma opção natural para processamento de dados não estruturados e semiestruturados, tais como registos e ficheiros. No entanto, também pode haver a necessidade de processar dados estruturados que são armazenados em bases de dados relacionais.

[Apache Sqoop](https://sqoop.apache.org/docs/1.4.4/SqoopUserGuide.html) é uma ferramenta projetada para transferir dados entre bases de dados relacionais e um repositório de grandes volumes de dados, como a geração 1 de armazenamento do Data Lake. Pode usá-lo para importar dados a partir de um sistema de gerenciamento de banco de dados relacional (RDBMS), como a base de dados do Azure SQL para geração 1 de armazenamento do Data Lake. Pode, em seguida, transformar e analisar os dados com cargas de trabalho de grandes volumes de dados e, em seguida, exportar os dados de volta para um RDBMS. Neste tutorial, vai utilizar uma base de dados do SQL do Azure como a base de dados relacional para importar/exportar do.

## <a name="prerequisites"></a>Pré-requisitos
Antes de começar este artigo, tem de ter o seguinte:

* **Uma subscrição do Azure**. Consulte [Obter uma avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Uma conta de geração 1 de armazenamento do Azure Data Lake**. Para obter instruções sobre como criar um, consulte [introdução ao Azure Data Lake Storage Gen1](data-lake-store-get-started-portal.md)
* **Cluster de HDInsight do Azure** com acesso a uma conta de geração 1 de armazenamento do Data Lake. Ver [criar um cluster do HDInsight com Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md). Este artigo pressupõe que tem um cluster Linux de HDInsight com acesso de geração 1 de armazenamento do Data Lake.
* **Base de Dados SQL do Azure**. Para obter instruções sobre como criar um, consulte [criar uma base de dados SQL do Azure](../sql-database/sql-database-get-started.md)

## <a name="do-you-learn-fast-with-videos"></a>Aprende depressa com vídeos?
[Assista a este vídeo](https://mix.office.com/watch/1butcdjxmu114) sobre como copiar dados entre os Blobs de armazenamento do Azure e de geração 1 do Data Lake Storage utilizar o DistCp.

## <a name="create-sample-tables-in-the-azure-sql-database"></a>Criar tabelas de exemplo na base de dados SQL do Azure
1. Para começar, crie duas tabelas de exemplo na base de dados do SQL Azure. Uso [SQL Server Management Studio](../sql-database/sql-database-connect-query-ssms.md) ou o Visual Studio para ligar à base de dados SQL do Azure e, em seguida, execute as seguintes consultas.

    **Criar Table1**

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

    **Criar Table2**

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
2. Na **Table1**, adicione alguns dados de exemplo. Deixe **Table2** vazio. Iremos importar dados a partir **Table1** na geração 1 de armazenamento do Data Lake. Em seguida, vamos exportar dados de geração 1 de armazenamento do Data Lake em **Table2**. Execute o seguinte fragmento.

        INSERT INTO [dbo].[Table1] VALUES (1,'Neal','Kell'), (2,'Lila','Fulton'), (3, 'Erna','Myers'), (4,'Annette','Simpson');


## <a name="use-sqoop-from-an-hdinsight-cluster-with-access-to-data-lake-storage-gen1"></a>Utilize o Sqoop de um cluster do HDInsight com acesso para a geração 1 de armazenamento do Data Lake
Um cluster do HDInsight já tem os pacotes de Sqoop disponíveis. Se tiver configurado o cluster do HDInsight para utilizar a geração 1 de armazenamento do Data Lake como armazenamento adicional, pode utilizar Sqoop (sem quaisquer alterações de configuração) para importar/exportar dados entre uma base de dados relacional (neste exemplo, a base de dados do Azure SQL) e um Data Lake Conta de armazenamento de geração 1.

1. Para este tutorial, partimos do princípio de que criou um cluster do Linux, deve usar o SSH para ligar ao cluster. Ver [ligar a um cluster do HDInsight baseado em Linux](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md).
2. Certifique-se de que se pode aceder a conta de geração 1 de armazenamento do Data Lake do cluster. Execute o seguinte comando da linha de comandos da SSH:

        hdfs dfs -ls adl://<data_lake_storage_gen1_account>.azuredatalakestore.net/

    Isso deve fornecer uma lista de ficheiros/pastas na conta de geração 1 de armazenamento do Data Lake.

### <a name="import-data-from-azure-sql-database-into-data-lake-storage-gen1"></a>Importar dados de base de dados do Azure SQL para geração 1 de armazenamento do Data Lake
1. Navegue para o diretório onde o Sqoop pacotes estão disponíveis. Normalmente, este será em `/usr/hdp/<version>/sqoop/bin`.
2. Importar os dados a partir **Table1** para a conta de geração 1 de armazenamento do Data Lake. Utilize a seguinte sintaxe:

        sqoop-import --connect "jdbc:sqlserver://<sql-database-server-name>.database.windows.net:1433;username=<username>@<sql-database-server-name>;password=<password>;database=<sql-database-name>" --table Table1 --target-dir adl://<data-lake-storage-gen1-name>.azuredatalakestore.net/Sqoop/SqoopImportTable1

    Tenha em atenção que **sql-base de dados-server-name** marcador de posição representa o nome do servidor onde a base de dados SQL do Azure está em execução. **nome da base de dados de SQL** marcador de posição representa o nome do banco de dados real.

    Por exemplo,


        sqoop-import --connect "jdbc:sqlserver://mysqoopserver.database.windows.net:1433;username=twooley@mysqoopserver;password=<password>;database=mysqoopdatabase" --table Table1 --target-dir adl://myadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1

1. Certifique-se de que os dados foi transferidos para a conta de geração 1 de armazenamento do Data Lake. Execute o seguinte comando:

        hdfs dfs -ls adl://hdiadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1/

    Deverá ver o resultado seguinte.


        -rwxrwxrwx   0 sshuser hdfs          0 2016-02-26 21:09 adl://hdiadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1/_SUCCESS
        -rwxrwxrwx   0 sshuser hdfs         12 2016-02-26 21:09 adl://hdiadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1/part-m-00000
        -rwxrwxrwx   0 sshuser hdfs         14 2016-02-26 21:09 adl://hdiadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1/part-m-00001
        -rwxrwxrwx   0 sshuser hdfs         13 2016-02-26 21:09 adl://hdiadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1/part-m-00002
        -rwxrwxrwx   0 sshuser hdfs         18 2016-02-26 21:09 adl://hdiadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1/part-m-00003

    Cada **parte-m -** * ficheiro corresponde a uma linha na tabela de origem, **Table1**. Pode ver o conteúdo da parte - m-* arquivos para verificar.


### <a name="export-data-from-data-lake-storage-gen1-into-azure-sql-database"></a>Exportar dados de geração 1 de armazenamento do Data Lake para base de dados do Azure SQL
1. Exportar os dados da conta do Data Lake Storage Gen1 para a tabela vazia, **Table2**, na base de dados do SQL Azure. Utilize a seguinte sintaxe.

        sqoop-export --connect "jdbc:sqlserver://<sql-database-server-name>.database.windows.net:1433;username=<username>@<sql-database-server-name>;password=<password>;database=<sql-database-name>" --table Table2 --export-dir adl://<data-lake-storage-gen1-name>.azuredatalakestore.net/Sqoop/SqoopImportTable1 --input-fields-terminated-by ","

    Por exemplo,


        sqoop-export --connect "jdbc:sqlserver://mysqoopserver.database.windows.net:1433;username=twooley@mysqoopserver;password=<password>;database=mysqoopdatabase" --table Table2 --export-dir adl://myadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1 --input-fields-terminated-by ","

1. Certifique-se de que os dados foi carregados para a tabela de base de dados SQL. Uso [SQL Server Management Studio](../sql-database/sql-database-connect-query-ssms.md) ou o Visual Studio para ligar à base de dados SQL do Azure e, em seguida, execute a seguinte consulta.

        SELECT * FROM TABLE2

    Isso deve ter o seguinte resultado.

         ID  FName   LName
        ------------------
        1    Neal    Kell
        2    Lila    Fulton
        3    Erna    Myers
        4    Annette    Simpson

## <a name="performance-considerations-while-using-sqoop"></a>Considerações sobre o desempenho ao utilizar o Sqoop

Para o seu trabalho Sqoop para copiar dados para a geração 1 de armazenamento do Data Lake de ajuste de desempenho, consulte [documento de desempenho de Sqoop](https://blogs.msdn.microsoft.com/bigdatasupport/2015/02/17/sqoop-job-performance-tuning-in-hdinsight-hadoop/).

## <a name="see-also"></a>Consulte também
* [Copiar dados dos Blobs de armazenamento do Azure para a geração 1 de armazenamento do Data Lake](data-lake-store-copy-data-azure-storage-blob.md)
* [Proteger dados no Armazenamento do Data Lake Ger1](data-lake-store-secure-data.md)
* [Utilizar o Azure Data Lake Analytics com o Data Lake Storage Gen1](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Utilizar o Azure HDInsight com Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)
