---
title: Apache Sqoop com Apache Hadoop-HDInsight do Azure
description: Saiba como usar o Apache Sqoop para importar e exportar entre Apache Hadoop no HDInsight e um banco de dados SQL do Azure.
keywords: Hadoop sqoop, sqoop
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 04/15/2019
ms.openlocfilehash: c839aeae77d7e75fb30d82c410c331d21f5868ae
ms.sourcegitcommit: 9dc7517db9c5817a3acd52d789547f2e3efff848
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/23/2019
ms.locfileid: "68406039"
---
# <a name="use-apache-sqoop-to-import-and-export-data-between-apache-hadoop-on-hdinsight-and-sql-database"></a>Usar o Apache Sqoop para importar e exportar dados entre Apache Hadoop no HDInsight e no banco de dados SQL

[!INCLUDE [sqoop-selector](../../../includes/hdinsight-selector-use-sqoop.md)]

Saiba como usar o Apache Sqoop para importar e exportar entre um cluster Apache Hadoop no Azure HDInsight e banco de dados SQL do Azure ou banco de dados do Microsoft SQL Server. As etapas neste documento usam o `sqoop` comando diretamente do cabeçalho do cluster Hadoop. Você usa o SSH para se conectar ao nó principal e executar os comandos neste documento. Este artigo é uma continuação do [uso do Apache Sqoop com Hadoop no HDInsight](./hdinsight-use-sqoop.md).

## <a name="prerequisites"></a>Pré-requisitos

* Conclusão da [configuração do ambiente de teste](./hdinsight-use-sqoop.md#create-cluster-and-sql-database) de [usar o Apache Sqoop com o Hadoop no HDInsight](./hdinsight-use-sqoop.md).

* Um cliente para consultar o banco de dados SQL do Azure. Considere usar [SQL Server Management Studio](../../sql-database/sql-database-connect-query-ssms.md) ou [Visual Studio Code](../../sql-database/sql-database-connect-query-vscode.md).

* Um cliente SSH. Para obter mais informações, consulte [conectar-se ao HDInsight (Apache Hadoop) usando o ssh](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="sqoop-export"></a>Sqoop exportar

De Hive para SQL Server.

1. Use o SSH para se conectar ao cluster HDInsight. Substitua `CLUSTERNAME` pelo nome do cluster e, em seguida, digite o comando:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

2. Substitua `MYSQLSERVER` pelo nome do seu SQL Server. Para verificar se o Sqoop pode ver seu banco de dados SQL, insira o comando abaixo em sua conexão Open SSH. Insira a senha para o logon de SQL Server quando solicitado. Esse comando retorna uma lista de bancos de dados.

    ```bash
    sqoop list-databases --connect jdbc:sqlserver://MYSQLSERVER.database.windows.net:1433 --username sqluser -P
    ```

3. Substitua `MYSQLSERVER` pelo nome do seu SQL Server e `MYDATABASE` pelo nome do seu banco de dados SQL. Para exportar dados da tabela Hive `hivesampletable` para a `mobiledata` tabela no banco de dados SQL, digite o comando a seguir em sua conexão Open SSH. Insira a senha para o logon de SQL Server quando solicitado

    ```bash
    sqoop export --connect 'jdbc:sqlserver://MYSQLSERVER.database.windows.net:1433;database=MYDATABASE' --username sqluser -P -table 'mobiledata' --hcatalog-table hivesampletable
    ```

4. Para verificar se os dados foram exportados, use as seguintes consultas do seu cliente SQL para exibir os dados exportados:

    ```sql
    SELECT COUNT(*) FROM [dbo].[mobiledata] WITH (NOLOCK);
    SELECT TOP(25) * FROM [dbo].[mobiledata] WITH (NOLOCK);
    ```

## <a name="sqoop-import"></a>Importação de Sqoop

De SQL Server para o armazenamento do Azure.

1. Substitua `MYSQLSERVER` pelo nome do seu SQL Server e `MYDATABASE` pelo nome do seu banco de dados SQL. Digite o comando a seguir em sua conexão aberta do SSH para importar dados `mobiledata` da tabela no SQL Database, para `wasb:///tutorials/usesqoop/importeddata` o diretório no HDInsight. Insira a senha para o logon de SQL Server quando solicitado. Os campos nos dados são separados por um caractere de tabulação e as linhas são terminadas por um caractere de nova linha.

    ```bash
    sqoop import --connect 'jdbc:sqlserver://MYSQLSERVER.database.windows.net:1433;database=MYDATABASE' --username sqluser -P --table 'mobiledata' --target-dir 'wasb:///tutorials/usesqoop/importeddata' --fields-terminated-by '\t' --lines-terminated-by '\n' -m 1
    ```

2. Depois que a importação for concluída, digite o seguinte comando em sua conexão SSH aberta para listar os dados no novo diretório:

    ```bash
    hdfs dfs -text /tutorials/usesqoop/importeddata/part-m-00000
    ```

## <a name="limitations"></a>Limitações

* Exportação em massa-com o HDInsight baseado em Linux, o conector do Sqoop usado para exportar dados para o Microsoft SQL Server ou o Azure SQL Database não oferece suporte a inserções em massa.

* Envio em lote com o HDInsight baseado em Linux, ao usar `-batch` a opção ao executar inserções, o Sqoop faz várias inserções em vez de realizar o envio em lote das operações de inserção.

## <a name="important-considerations"></a>Considerações importantes

* O HDInsight e o SQL Server devem estar na mesma rede virtual do Azure.

    Para obter um exemplo, consulte o documento [conectar o HDInsight ao seu local de rede](./../connect-on-premises-network.md) .

    Para obter mais informações sobre como usar o HDInsight com uma rede virtual do Azure, consulte o documento [estender o hdinsight com a rede virtual do Azure](../hdinsight-plan-virtual-network-deployment.md) . Para obter mais informações sobre a rede virtual do Azure, consulte o documento [visão geral da rede virtual](../../virtual-network/virtual-networks-overview.md) .

* SQL Server deve ser configurado para permitir a autenticação do SQL. Para obter mais informações, consulte o documento [escolher um modo de autenticação](https://msdn.microsoft.com/ms144284.aspx) .

* Talvez seja necessário configurar SQL Server para aceitar conexões remotas. Para obter mais informações, consulte o documento [como solucionar problemas de conexão com o mecanismo de banco de dados SQL Server](https://social.technet.microsoft.com/wiki/contents/articles/2102.how-to-troubleshoot-connecting-to-the-sql-server-database-engine.aspx) .

## <a name="next-steps"></a>Passos seguintes

Agora você aprendeu a usar o Sqoop. Para saber mais, consulte:

* [Usar o Apache Oozie com o HDInsight](../hdinsight-use-oozie-linux-mac.md): Use a ação Sqoop em um fluxo de trabalho do Oozie.
* [Analisar dados de atraso de vôo usando o HDInsight](../interactive-query/interactive-query-tutorial-analyze-flight-data.md): Use a consulta interativa para analisar dados de atraso de voo e, em seguida, use Sqoop para exportar dados para um banco de dado SQL do Azure.
* [Carregar dados no HDInsight](../hdinsight-upload-data.md): Encontre outros métodos para carregar dados no HDInsight/armazenamento de BLOBs do Azure.
