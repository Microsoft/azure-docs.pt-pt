---
title: Copiar dados entre Data Lake Storage Gen1 e Azure SQL - Sqoop [ Microsoft Docs
description: Utilize o Sqoop para copiar dados entre a Base de Dados Azure SQL e o Azure Data Lake Storage Gen1
services: data-lake-store
author: twooley
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 07/30/2019
ms.author: twooley
ms.openlocfilehash: cf3893706afcb4c4cc5b90dd3d2431ecedc71d0a
ms.sourcegitcommit: 6a4fbc5ccf7cca9486fe881c069c321017628f20
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/27/2020
ms.locfileid: "73839066"
---
# <a name="copy-data-between-data-lake-storage-gen1-and-azure-sql-database-using-sqoop"></a>Copiar dados entre data lake storage Gen1 e Azure SQL Database usando Sqoop

Saiba como utilizar o Apache Sqoop para importar e exportar dados entre a Base de Dados Azure SQL e o Azure Data Lake Storage Gen1.

## <a name="what-is-sqoop"></a>O que é Sqoop?

As aplicações de big data são uma escolha natural para o processamento de dados não estruturados e semi-estruturados, tais como registos e ficheiros. No entanto, também pode ter a necessidade de processar dados estruturados que são armazenados em bases de dados relacionais.

[Apache Sqoop](https://sqoop.apache.org/docs/1.4.4/SqoopUserGuide.html) é uma ferramenta projetada para transferir dados entre bases de dados relacionais e um repositório de big data, como Data Lake Storage Gen1. Pode usá-lo para importar dados de um sistema de gestão relacional de bases de dados (RDBMS), como a Base de Dados Azure SQL em Data Lake Storage Gen1. Em seguida, pode transformar e analisar os dados usando cargas de trabalho de big data e, em seguida, exportar os dados de volta para um RDBMS. Neste artigo, utiliza uma base de dados Azure SQL como base de dados relacional para importar/exportar.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, deve ter o seguinte:

* **Uma subscrição Azure.** Consulte [Obter versão de avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
* Uma conta De Armazenamento de **Lago Azure Gen1.** Para obter instruções sobre como criar a conta, consulte [Começar com Azure Data Lake Storage Gen1](data-lake-store-get-started-portal.md)
* **Cluster Azure HDInsight** com acesso a uma conta Gen1 de Armazenamento de Data Lake. Consulte [Criar um cluster HDInsight com Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md). Este artigo assume que tem um cluster HDInsight Linux com acesso ao Data Lake Storage Gen1.
* **Base de Dados Azure SQL**. Para obter instruções sobre como criar uma, consulte Criar uma base de [dados Azure SQL](../sql-database/sql-database-get-started.md)

## <a name="create-sample-tables-in-the-azure-sql-database"></a>Criar tabelas de amostras na base de dados Azure SQL

1. Para começar, crie duas tabelas de amostras na base de dados Azure SQL. Utilize o Estúdio de Gestão de [Servidores SQL](../sql-database/sql-database-connect-query-ssms.md) ou o Estúdio Visual para se ligar à base de dados e, em seguida, executar as seguintes consultas.

    **Criar tabela1**

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

    **Criar tabela2**

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

1. Executar o seguinte comando para adicionar alguns dados da amostra ao **Quadro 1**. Deixe **a mesa 2** vazia. Mais tarde, importará dados da **Tabela 1** para data Lake Storage Gen1. Em seguida, você exportará dados do Data Lake Storage Gen1 para a **Tabela 2**.

       INSERT INTO [dbo].[Table1] VALUES (1,'Neal','Kell'), (2,'Lila','Fulton'), (3, 'Erna','Myers'), (4,'Annette','Simpson');

## <a name="use-sqoop-from-an-hdinsight-cluster-with-access-to-data-lake-storage-gen1"></a>Use Sqoop de um cluster HDInsight com acesso a Data Lake Storage Gen1

Um cluster HDInsight já tem os pacotes Sqoop disponíveis. Se configurar o cluster HDInsight para utilizar data Lake Storage Gen1 como armazenamento adicional, pode utilizar o Sqoop (sem alterações de configuração) para importar/exportar dados entre uma base de dados relacional, como a Base de Dados Azure SQL, e uma conta Data Lake Storage Gen1.

1. Para este artigo, assumimos que criou um cluster Linux para que você use SSH para ligar ao cluster. Consulte [a Ligação a um cluster HDInsight baseado em Linux](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md).

1. Verifique se pode aceder à conta Data Lake Storage Gen1 a partir do cluster. Executar o seguinte comando a partir do ssh solicitado:

       hdfs dfs -ls adl://<data_lake_storage_gen1_account>.azuredatalakestore.net/

   Este comando fornece uma lista de ficheiros/pastas na conta Data Lake Storage Gen1.

### <a name="import-data-from-azure-sql-database-into-data-lake-storage-gen1"></a>Dados de importação da Base de Dados Azure SQL para Data Lake Storage Gen1

1. Navegue para o diretório onde os pacotes Sqoop estão disponíveis. Tipicamente, este `/usr/hdp/<version>/sqoop/bin`local é .

1. Importar os dados do **Quadro 1** para a conta Gen1 de Armazenamento de Data Lake. Utilize a seguinte sintaxe:

       sqoop-import --connect "jdbc:sqlserver://<sql-database-server-name>.database.windows.net:1433;username=<username>@<sql-database-server-name>;password=<password>;database=<sql-database-name>" --table Table1 --target-dir adl://<data-lake-storage-gen1-name>.azuredatalakestore.net/Sqoop/SqoopImportTable1

   O espaço reservado para o servidor de base **de dados sql** representa o nome do servidor onde está em execução a base de dados Azure SQL. O espaço reservado para nomes de **base de dados sql** representa o nome real da base de dados.

   Por exemplo,

       sqoop-import --connect "jdbc:sqlserver://mysqoopserver.database.windows.net:1433;username=twooley@mysqoopserver;password=<password>;database=mysqoopdatabase" --table Table1 --target-dir adl://myadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1

1. Verifique se os dados foram transferidos para a conta Data Lake Storage Gen1. Execute o seguinte comando:

       hdfs dfs -ls adl://hdiadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1/

   Deverá ver o seguinte resultado.

       -rwxrwxrwx   0 sshuser hdfs          0 2016-02-26 21:09 adl://hdiadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1/_SUCCESS
       -rwxrwxrwx   0 sshuser hdfs         12 2016-02-26 21:09 adl://hdiadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1/part-m-00000
       -rwxrwxrwx   0 sshuser hdfs         14 2016-02-26 21:09 adl://hdiadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1/part-m-00001
       -rwxrwxrwx   0 sshuser hdfs         13 2016-02-26 21:09 adl://hdiadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1/part-m-00002
       -rwxrwxrwx   0 sshuser hdfs         18 2016-02-26 21:09 adl://hdiadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1/part-m-00003

   Cada ficheiro **part-m-*** corresponde a uma linha na tabela de origem, **Tabela 1**. Pode visualizar o conteúdo dos ficheiros part-m*para verificar.

### <a name="export-data-from-data-lake-storage-gen1-into-azure-sql-database"></a>Dados de exportação do Data Lake Storage Gen1 para a Base de Dados Azure SQL

1. Exportar os dados da conta Data Lake Storage Gen1 para a tabela vazia, **Tabela 2,** na Base de Dados Azure SQL. Utilize a seguinte sintaxe.

       sqoop-export --connect "jdbc:sqlserver://<sql-database-server-name>.database.windows.net:1433;username=<username>@<sql-database-server-name>;password=<password>;database=<sql-database-name>" --table Table2 --export-dir adl://<data-lake-storage-gen1-name>.azuredatalakestore.net/Sqoop/SqoopImportTable1 --input-fields-terminated-by ","

   Por exemplo,

       sqoop-export --connect "jdbc:sqlserver://mysqoopserver.database.windows.net:1433;username=twooley@mysqoopserver;password=<password>;database=mysqoopdatabase" --table Table2 --export-dir adl://myadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1 --input-fields-terminated-by ","

1. Verifique se os dados foram enviados para a tabela de bases de dados SQL. Utilize o Estúdio de Gestão de [Servidores SQL](../sql-database/sql-database-connect-query-ssms.md) ou o Estúdio Visual para se ligar à Base de Dados Azure SQL e, em seguida, executar a seguinte consulta.

       SELECT * FROM TABLE2

   Este comando deve ter a seguinte saída.

        ID  FName    LName
       -------------------
       1    Neal     Kell
       2    Lila     Fulton
       3    Erna     Myers
       4    Annette  Simpson

## <a name="performance-considerations-while-using-sqoop"></a>Considerações de desempenho ao usar sqoop

Para obter informações sobre o desempenho afinando o seu trabalho de Sqoop para copiar dados para Data Lake Storage Gen1, consulte o post do [blog de desempenho da Sqoop](https://blogs.msdn.microsoft.com/bigdatasupport/2015/02/17/sqoop-job-performance-tuning-in-hdinsight-hadoop/).

## <a name="next-steps"></a>Passos seguintes

* [Copiar dados de Blobs de Armazenamento Azure para Data Lake Storage Gen1](data-lake-store-copy-data-azure-storage-blob.md)
* [Proteger dados no Armazenamento do Data Lake Ger1](data-lake-store-secure-data.md)
* [Use Azure Data Lake Analytics com Data Lake Storage Gen1](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Use Azure HDInsight com Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)
