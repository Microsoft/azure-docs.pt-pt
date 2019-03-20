---
title: O Apache Sqoop com o Apache Hadoop - Azure HDInsight
description: Saiba como utilizar o Apache Sqoop para importar e exportar entre o Apache Hadoop no HDInsight e uma base de dados do SQL do Azure.
keywords: hadoop sqoop,sqoop
services: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 02/15/2019
ms.openlocfilehash: 265881ede035e8b854be1bd54a70fa8c7ef127ab
ms.sourcegitcommit: dec7947393fc25c7a8247a35e562362e3600552f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58200536"
---
# <a name="use-apache-sqoop-to-import-and-export-data-between-apache-hadoop-on-hdinsight-and-sql-database"></a>Utilizar o Apache Sqoop para importar e exportar dados entre o Apache Hadoop no HDInsight e a base de dados SQL

[!INCLUDE [sqoop-selector](../../../includes/hdinsight-selector-use-sqoop.md)]

Saiba como utilizar o Apache Sqoop para importar e exportar entre um cluster do Apache Hadoop no HDInsight do Azure e base de dados de base de dados do Azure SQL ou o Microsoft SQL Server. Os passos neste documentam uso o `sqoop` comando diretamente a partir do nó principal do cluster de Hadoop. Utilizar o SSH para ligar ao nó principal e execute os comandos neste documento.

> [!IMPORTANT]  
> Os passos neste documento funcionam apenas com clusters do HDInsight que utilizam o Linux. O Linux é o único sistema operativo utilizado na versão 3.4 ou superior do HDInsight. Para obter mais informações, veja [HDInsight retirement on Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement) (Desativação do HDInsight no Windows).

> [!WARNING]  
> Os passos neste documento partem do princípio de que já criou uma base de dados de SQL do Azure com o nome `sqooptest`.
>
> Este documento fornece instruções T-SQL que são utilizadas para criar e consultar uma tabela na base de dados SQL. Existem muitos clientes, que pode utilizar estas instruções com a base de dados SQL. Recomendamos os seguintes clientes:
>
> * [SQL Server Management Studio](../../sql-database/sql-database-connect-query-ssms.md)
> * [Visual Studio Code](../../sql-database/sql-database-connect-query-vscode.md)
> * O [sqlcmd](https://docs.microsoft.com/sql/tools/sqlcmd-utility) utilitário

## <a name="create-the-table-in-sql-database"></a>Criar a tabela na base de dados SQL

> [!IMPORTANT]  
> Se estiver a utilizar o cluster do HDInsight e base de dados SQL criada na [criar o cluster e a base de dados SQL](hdinsight-use-sqoop.md), ignore os passos nesta secção. A base de dados e a tabela foram criados como parte dos passos da [criar o cluster e a base de dados SQL](hdinsight-use-sqoop.md) documento.

Utilizar um cliente do SQL para ligar para o `sqooptest` base de dados na base de dados SQL. Em seguida, utilize o T-SQL seguinte para criar uma tabela chamada `mobiledata`:

```sql
CREATE TABLE [dbo].[mobiledata](
[clientid] [nvarchar](50),
[querytime] [nvarchar](50),
[market] [nvarchar](50),
[deviceplatform] [nvarchar](50),
[devicemake] [nvarchar](50),
[devicemodel] [nvarchar](50),
[state] [nvarchar](50),
[country] [nvarchar](50),
[querydwelltime] [float],
[sessionid] [bigint],
[sessionpagevieworder] [bigint])
GO
CREATE CLUSTERED INDEX mobiledata_clustered_index on mobiledata(clientid)
GO
```

## <a name="sqoop-export"></a>Exportação de Sqoop

1. Utilize o SSH para ligar ao cluster do HDInsight. Por exemplo, o comando seguinte liga ao nó principal primário de um cluster com o nome `mycluster`:

    ```bash
    ssh mycluster-ssh.azurehdinsight.net
    ```

    Para obter mais informações, veja [Utilizar SSH com o HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. Para verificar que o Sqoop pode ver a base de dados SQL, utilize o seguinte comando:

    ```bash
    sqoop list-databases --connect jdbc:sqlserver://<serverName>.database.windows.net:1433 --username <adminLogin> -P
    ```
    Quando lhe for pedido, introduza a palavra-passe para o início de sessão da base de dados SQL.

    Este comando devolve uma lista de bases de dados, incluindo o **sqooptest** base de dados utilizada anteriormente.

3. Para exportar os dados da Colméia **hivesampletable** da tabela para o **mobiledata** tabela na base de dados SQL, utilize o seguinte comando:

    ```bash
    sqoop export --connect 'jdbc:sqlserver://<serverName>.database.windows.net:1433;database=sqooptest' --username <adminLogin> -P -table 'mobiledata' --hcatalog-table hivesampletable
    ```

4. Para verificar que foram exportados dados, utilize a seguinte consulta a partir do seu cliente SQL para ver os dados exportados:

    ```sql
    SET ROWCOUNT 50;
    SELECT * FROM mobiledata;
    ```

    Este comando lista 50 linhas que foram importadas para a tabela.

## <a name="sqoop-import"></a>Sqoop import

1. Utilize o seguinte comando para importar dados a partir do **mobiledata** tabela na base de dados SQL, para o **wasb: / / / tutoriais/usesqoop/importeddata** no HDInsight:

    ```bash
    sqoop import --connect 'jdbc:sqlserver://<serverName>.database.windows.net:1433;database=sqooptest' --username <adminLogin> -P --table 'mobiledata' --target-dir 'wasb:///tutorials/usesqoop/importeddata' --fields-terminated-by '\t' --lines-terminated-by '\n' -m 1
    ```

    Os campos de dados são separados por um caractere de tabulação e as linhas são terminadas por um caractere de nova linha.

    > [!IMPORTANT]  
    > O `wasb:///` caminho funciona com clusters que utilizam o armazenamento do Azure como o armazenamento de cluster predefinido. Para clusters que utilizam a geração 2 de armazenamento do Azure Data Lake, utilize `abfs:///` em vez disso. Para clusters que utilizam o Azure Data Lake Storage Gen1, utilize `adl:///` em vez disso.

2. Depois de concluída a importação, utilize o seguinte comando para listar os dados no novo diretório:

    ```bash
    hdfs dfs -text /tutorials/usesqoop/importeddata/part-m-00000
    ```

## <a name="using-sql-server"></a>Utilizar o SQL Server

Também pode utilizar o Sqoop para importar e exportar dados do SQL Server. As diferenças entre a utilização da base de dados SQL e o SQL Server são:

* O HDInsight e o SQL Server tem de ser na mesma rede Virtual do Azure.

    Por exemplo, veja a [ligar o HDInsight à sua rede no local](./../connect-on-premises-network.md) documento.

    Para obter mais informações sobre como utilizar o HDInsight com uma rede Virtual do Azure, consulte a [estender o HDInsight com a rede Virtual do Azure](../hdinsight-extend-hadoop-virtual-network.md) documento. Para obter mais informações sobre a rede Virtual do Azure, consulte a [descrição geral da rede Virtual](../../virtual-network/virtual-networks-overview.md) documento.

* SQL Server tem de ser configurado para permitir a autenticação de SQL. Para obter mais informações, consulte a [escolher um modo de autenticação](https://msdn.microsoft.com/ms144284.aspx) documento.

* Poderá ter de configurar o SQL Server para aceitar ligações remotas. Para obter mais informações, consulte a [como resolver problemas a ligar ao motor de base de dados do SQL Server](https://social.technet.microsoft.com/wiki/contents/articles/2102.how-to-troubleshoot-connecting-to-the-sql-server-database-engine.aspx) documento.

* Utilize as seguintes declarações de Transact-SQL para criar o **mobiledata** tabela:

    ```sql
    CREATE TABLE [dbo].[mobiledata](
    [clientid] [nvarchar](50),
    [querytime] [nvarchar](50),
    [market] [nvarchar](50),
    [deviceplatform] [nvarchar](50),
    [devicemake] [nvarchar](50),
    [devicemodel] [nvarchar](50),
    [state] [nvarchar](50),
    [country] [nvarchar](50),
    [querydwelltime] [float],
    [sessionid] [bigint],
    [sessionpagevieworder] [bigint])
    ```

* Quando ligar ao SQL Server a partir do HDInsight, poderá ter de utilizar o endereço IP do SQL Server. Por exemplo:

    ```bash
    sqoop import --connect 'jdbc:sqlserver://10.0.1.1:1433;database=sqooptest' --username <adminLogin> -P -table 'mobiledata' --target-dir 'wasb:///tutorials/usesqoop/importeddata' --fields-terminated-by '\t' --lines-terminated-by '\n' -m 1
    ```

## <a name="limitations"></a>Limitações

* Exportação em massa - baseado em Linux com o HDInsight, o conector do Sqoop utilizado para exportar dados para o Microsoft SQL Server ou SQL Database do Azure não suporta inserções em massa.

* Criação de batches - com o HDInsight baseado em Linux, ao utilizar o `-batch` mude ao realizar inserções, Sqoop faz várias inserções em vez das operações de inserção de criação de batches.

## <a name="next-steps"></a>Passos Seguintes

Agora que aprendeu a utilizar o Sqoop. Para saber mais, consulte:

* [Utilizar o Apache Oozie com o HDInsight](../hdinsight-use-oozie.md): Utilize o Sqoop ação num fluxo de trabalho do Oozie.
* [Analisar dados de atraso de voo com o HDInsight](../hdinsight-analyze-flight-delay-data-linux.md): Utilizar o Apache Hive para analisar dados de atraso de voo e, em seguida, utilize o Sqoop para exportar dados para uma base de dados SQL do Azure.
* [Carregar dados para o HDInsight](../hdinsight-upload-data.md): Encontre outros métodos para carregar dados para o armazenamento de Blobs do HDInsight/do Azure.

[hdinsight-versions]:  ../hdinsight-component-versioning.md
[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-get-started]:apache-hadoop-linux-tutorial-get-started.md
[hdinsight-storage]: ../hdinsight-hadoop-use-blob-storage.md
[hdinsight-submit-jobs]:submit-apache-hadoop-jobs-programmatically.md
[sqldatabase-get-started]: ../sql-database-get-started.md

[powershell-start]: https://technet.microsoft.com/library/hh847889.aspx
[powershell-install]: /powershell/azureps-cmdlets-docs
[powershell-script]: https://technet.microsoft.com/library/ee176949.aspx

[sqoop-user-guide-1.4.4]: https://sqoop.apache.org/docs/1.4.4/SqoopUserGuide.html
