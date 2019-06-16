---
title: O Apache Sqoop com o Apache Hadoop - Azure HDInsight
description: Saiba como utilizar o Apache Sqoop para importar e exportar entre o Apache Hadoop no HDInsight e uma base de dados do SQL do Azure.
keywords: hadoop sqoop,sqoop
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 04/15/2019
ms.openlocfilehash: 6dcb6853daf34fede590011d165c0ba9001cbac6
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "64721628"
---
# <a name="use-apache-sqoop-to-import-and-export-data-between-apache-hadoop-on-hdinsight-and-sql-database"></a>Utilizar o Apache Sqoop para importar e exportar dados entre o Apache Hadoop no HDInsight e a base de dados SQL

[!INCLUDE [sqoop-selector](../../../includes/hdinsight-selector-use-sqoop.md)]

Saiba como utilizar o Apache Sqoop para importar e exportar entre um cluster do Apache Hadoop no HDInsight do Azure e base de dados de base de dados do Azure SQL ou o Microsoft SQL Server. Os passos neste documentam uso o `sqoop` comando diretamente a partir do nó principal do cluster de Hadoop. Utilizar o SSH para ligar ao nó principal e execute os comandos neste documento. Este artigo é uma continuação de [utilizar o Apache Sqoop com o Hadoop no HDInsight](./hdinsight-use-sqoop.md).

## <a name="prerequisites"></a>Pré-requisitos

* Conclusão da [configurar o ambiente de teste](./hdinsight-use-sqoop.md#create-cluster-and-sql-database) partir [utilização Apache Sqoop com o Hadoop no HDInsight](./hdinsight-use-sqoop.md).

* Um cliente para consultar a base de dados SQL do Azure. Considere a utilização [SQL Server Management Studio](../../sql-database/sql-database-connect-query-ssms.md) ou [Visual Studio Code](../../sql-database/sql-database-connect-query-vscode.md).

* Um cliente SSH. Para obter mais informações, consulte [ligar ao HDInsight (Apache Hadoop) através de SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="sqoop-export"></a>Exportação de Sqoop

Do ramo de registo para o SQL Server.

1. Utilize o SSH para ligar ao cluster do HDInsight. Substitua `CLUSTERNAME` com o nome do cluster, em seguida, introduza o comando:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

2. Substitua `MYSQLSERVER` com o nome do seu SQL Server. Para verificar que o Sqoop pode ver a base de dados SQL, introduza o comando abaixo em abrir a ligação SSH. Introduza a palavra-passe para o início de sessão do SQL Server quando lhe for pedido. Este comando devolve uma lista de bases de dados.

    ```bash
    sqoop list-databases --connect jdbc:sqlserver://MYSQLSERVER.database.windows.net:1433 --username sqluser -P
    ```

3. Substitua `MYSQLSERVER` com o nome do seu SQL Server, e `MYDATABASE` com o nome da base de dados SQL. Para exportar os dados da Colméia `hivesampletable` de tabela para o `mobiledata` de tabela na base de dados SQL, introduza o comando abaixo em abrir a ligação SSH. Introduza a palavra-passe para o início de sessão do SQL Server quando lhe for pedido

    ```bash
    sqoop export --connect 'jdbc:sqlserver://MYSQLSERVER.database.windows.net:1433;database=MYDATABASE' --username sqluser -P -table 'mobiledata' --hcatalog-table hivesampletable
    ```

4. Para verificar que foram exportados dados, utilize as seguintes consultas a partir do seu cliente SQL para ver os dados exportados:

    ```sql
    SELECT COUNT(*) FROM [dbo].[mobiledata] WITH (NOLOCK);
    SELECT TOP(25) * FROM [dbo].[mobiledata] WITH (NOLOCK);
    ```

## <a name="sqoop-import"></a>Sqoop import

Do SQL Server para o armazenamento do Azure.

1. Substitua `MYSQLSERVER` com o nome do seu SQL Server, e `MYDATABASE` com o nome da base de dados SQL. Introduza o comando abaixo na sua abrir a ligação SSH para importar dados a partir da `mobiledata` tabela na base de dados SQL, ao `wasb:///tutorials/usesqoop/importeddata` no HDInsight. Introduza a palavra-passe para o início de sessão do SQL Server quando lhe for pedido. Os campos de dados são separados por um caractere de tabulação e as linhas são terminadas por um caractere de nova linha.

    ```bash
    sqoop import --connect 'jdbc:sqlserver://MYSQLSERVER.database.windows.net:1433;database=MYDATABASE' --username sqluser -P --table 'mobiledata' --target-dir 'wasb:///tutorials/usesqoop/importeddata' --fields-terminated-by '\t' --lines-terminated-by '\n' -m 1
    ```

2. Depois de concluída a importação, introduza o seguinte comando na sua abrir a ligação SSH à lista os dados no novo diretório:

    ```bash
    hdfs dfs -text /tutorials/usesqoop/importeddata/part-m-00000
    ```

## <a name="limitations"></a>Limitações

* Exportação em massa - baseado em Linux com o HDInsight, o conector do Sqoop utilizado para exportar dados para o Microsoft SQL Server ou SQL Database do Azure não suporta inserções em massa.

* Criação de batches - com o HDInsight baseado em Linux, ao utilizar o `-batch` mude ao realizar inserções, Sqoop faz várias inserções em vez das operações de inserção de criação de batches.

## <a name="important-considerations"></a>Considerações importantes

* O HDInsight e o SQL Server tem de ser na mesma rede Virtual do Azure.

    Por exemplo, veja a [ligar o HDInsight à sua rede no local](./../connect-on-premises-network.md) documento.

    Para obter mais informações sobre como utilizar o HDInsight com uma rede Virtual do Azure, consulte a [estender o HDInsight com a rede Virtual do Azure](../hdinsight-extend-hadoop-virtual-network.md) documento. Para obter mais informações sobre a rede Virtual do Azure, consulte a [descrição geral da rede Virtual](../../virtual-network/virtual-networks-overview.md) documento.

* SQL Server tem de ser configurado para permitir a autenticação de SQL. Para obter mais informações, consulte a [escolher um modo de autenticação](https://msdn.microsoft.com/ms144284.aspx) documento.

* Poderá ter de configurar o SQL Server para aceitar ligações remotas. Para obter mais informações, consulte a [como resolver problemas a ligar ao motor de base de dados do SQL Server](https://social.technet.microsoft.com/wiki/contents/articles/2102.how-to-troubleshoot-connecting-to-the-sql-server-database-engine.aspx) documento.

## <a name="next-steps"></a>Passos Seguintes

Agora que aprendeu a utilizar o Sqoop. Para saber mais, consulte:

* [Utilizar o Apache Oozie com o HDInsight](../hdinsight-use-oozie-linux-mac.md): Utilize o Sqoop ação num fluxo de trabalho do Oozie.
* [Analisar dados de atraso de voo com o HDInsight](../hdinsight-analyze-flight-delay-data-linux.md): Utilizar o Apache Hive para analisar dados de atraso de voo e, em seguida, utilize o Sqoop para exportar dados para uma base de dados SQL do Azure.
* [Carregar dados para o HDInsight](../hdinsight-upload-data.md): Encontre outros métodos para carregar dados para o armazenamento de Blobs do HDInsight/do Azure.
