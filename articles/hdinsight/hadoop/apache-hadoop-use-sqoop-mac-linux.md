---
title: Apache Sqoop com Apache Hadoop-HDInsight do Azure
description: Saiba como usar o Apache Sqoop para importar e exportar entre Apache Hadoop no HDInsight e um banco de dados SQL do Azure.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 11/28/2019
ms.openlocfilehash: 21bc903349876a76576fb742840e9899f9d94bcd
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74769392"
---
# <a name="use-apache-sqoop-to-import-and-export-data-between-apache-hadoop-on-hdinsight-and-sql-database"></a>Usar o Apache Sqoop para importar e exportar dados entre Apache Hadoop no HDInsight e no banco de dados SQL

[!INCLUDE [sqoop-selector](../../../includes/hdinsight-selector-use-sqoop.md)]

Saiba como usar o Apache Sqoop para importar e exportar entre um cluster Apache Hadoop no Azure HDInsight e banco de dados SQL do Azure ou banco de dados do Microsoft SQL Server. As etapas neste documento usam o comando `sqoop` diretamente do cabeçalho do cluster Hadoop. Você usa o SSH para se conectar ao nó principal e executar os comandos neste documento. Este artigo é uma continuação do [uso do Apache Sqoop com Hadoop no HDInsight](./hdinsight-use-sqoop.md).

## <a name="prerequisites"></a>Pré-requisitos

* Conclusão da [configuração do ambiente de teste](./hdinsight-use-sqoop.md#create-cluster-and-sql-database) de [usar o Apache Sqoop com o Hadoop no HDInsight](./hdinsight-use-sqoop.md).

* Um cliente SSH. Para obter mais informações, consulte [conectar-se ao HDInsight (Apache Hadoop) usando o ssh](../hdinsight-hadoop-linux-use-ssh-unix.md).

* Familiaridade com o Sqoop. Para obter mais informações, consulte [Guia do usuário do Sqoop](https://sqoop.apache.org/docs/1.4.7/SqoopUserGuide.html).

## <a name="set-up"></a>Configuração

1. Use o [comando ssh](../hdinsight-hadoop-linux-use-ssh-unix.md) para se conectar ao cluster. Edite o comando a seguir substituindo CLUSTERname pelo nome do cluster e, em seguida, digite o comando:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Para facilitar o uso, defina as variáveis. Substitua `PASSWORD`, `MYSQLSERVER`e `MYDATABASE` pelos valores relevantes e, em seguida, insira os comandos abaixo:

    ```bash
    export password='PASSWORD'
    export sqlserver="MYSQLSERVER"
    export database="MYDATABASE"


    export serverConnect="jdbc:sqlserver://$sqlserver.database.windows.net:1433;user=sqluser;password=$password"
    export serverDbConnect="jdbc:sqlserver://$sqlserver.database.windows.net:1433;user=sqluser;password=$password;database=$database"
    ```

## <a name="sqoop-export"></a>Sqoop exportar

De Hive para SQL Server.

1. Para verificar se o Sqoop pode ver seu banco de dados SQL, insira o comando abaixo em sua conexão Open SSH. Esse comando retorna uma lista de bancos de dados.

    ```bash
    sqoop list-databases --connect $serverConnect
    ```

1. Digite o seguinte comando para ver uma lista de tabelas para o banco de dados especificado:

    ```bash
    sqoop list-tables --connect $serverDbConnect
    ```

1. Para exportar dados da tabela de `hivesampletable` do hive para a tabela `mobiledata` no banco de dados SQL, digite o comando abaixo em sua conexão Open SSH:

    ```bash
    sqoop export --connect $serverDbConnect \
    -table mobiledata \
    --hcatalog-table hivesampletable
    ```

1. Para verificar se os dados foram exportados, use as seguintes consultas de sua conexão SSH para exibir os dados exportados:

    ```bash
    sqoop eval --connect $serverDbConnect \
    --query "SELECT COUNT(*) from dbo.mobiledata WITH (NOLOCK)"


    sqoop eval --connect $serverDbConnect \
    --query "SELECT TOP(10) * from dbo.mobiledata WITH (NOLOCK)"
    ```

## <a name="sqoop-import"></a>Importação de Sqoop

De SQL Server para o armazenamento do Azure.

1. Digite o comando a seguir em sua conexão Open SSH para importar dados da tabela `mobiledata` no SQL Database, para o diretório `wasbs:///tutorials/usesqoop/importeddata` no HDInsight. Os campos nos dados são separados por um caractere de tabulação e as linhas são terminadas por um caractere de nova linha.

    ```bash
    sqoop import --connect $serverDbConnect \
    --table mobiledata \
    --target-dir 'wasb:///tutorials/usesqoop/importeddata' \
    --fields-terminated-by '\t' \
    --lines-terminated-by '\n' -m 1
    ```

1. Como alternativa, você também pode especificar uma tabela Hive:

    ```bash
    sqoop import --connect $serverDbConnect \
    --table mobiledata \
    --target-dir 'wasb:///tutorials/usesqoop/importeddata2' \
    --fields-terminated-by '\t' \
    --lines-terminated-by '\n' \
    --create-hive-table \
    --hive-table mobiledata_imported2 \
    --hive-import -m 1
    ```

1. Depois que a importação for concluída, digite o seguinte comando em sua conexão SSH aberta para listar os dados no novo diretório:

    ```bash
    hadoop fs -tail /tutorials/usesqoop/importeddata/part-m-00000
    ```

1. Use [beeline](./apache-hadoop-use-hive-beeline.md) para verificar se a tabela foi criada no hive.

    1. Ligar

        ```bash
        beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http'
        ```

    1. Execute cada consulta abaixo uma de cada vez e examine a saída:

        ```hql
        show tables;
        describe mobiledata_imported2;
        SELECT COUNT(*) FROM mobiledata_imported2;
        SELECT * FROM mobiledata_imported2 LIMIT 10;
        ```

    1. Saia do beeline com `!exit`.

## <a name="limitations"></a>Limitações

* Exportação em massa-com o HDInsight baseado em Linux, o conector do Sqoop usado para exportar dados para o Microsoft SQL Server ou o Azure SQL Database não dá suporte a inserções em massa.

* Envio em lote com o HDInsight baseado em Linux, ao usar a opção de `-batch` ao executar inserções, o Sqoop faz várias inserções em vez de fazer o lote das operações de inserção.

## <a name="important-considerations"></a>Considerações importantes

* O HDInsight e o SQL Server devem estar na mesma rede virtual do Azure.

    Para obter um exemplo, consulte o documento [conectar o HDInsight ao seu local de rede](./../connect-on-premises-network.md) .

    Para obter mais informações sobre como usar o HDInsight com uma rede virtual do Azure, consulte o documento [estender o hdinsight com a rede virtual do Azure](../hdinsight-plan-virtual-network-deployment.md) . Para obter mais informações sobre a rede virtual do Azure, consulte o documento [visão geral da rede virtual](../../virtual-network/virtual-networks-overview.md) .

* SQL Server deve ser configurado para permitir a autenticação do SQL. Para obter mais informações, consulte o documento [escolher um modo de autenticação](https://msdn.microsoft.com/ms144284.aspx) .

* Talvez seja necessário configurar SQL Server para aceitar conexões remotas. Para obter mais informações, consulte o documento [como solucionar problemas de conexão com o mecanismo de banco de dados SQL Server](https://social.technet.microsoft.com/wiki/contents/articles/2102.how-to-troubleshoot-connecting-to-the-sql-server-database-engine.aspx) .

## <a name="next-steps"></a>Passos seguintes

Agora você aprendeu a usar o Sqoop. Para saber mais, consulte:

* [Usar o Apache Oozie com o HDInsight](../hdinsight-use-oozie-linux-mac.md): Use a ação Sqoop em um fluxo de trabalho Oozie.
* [Analisar dados de atraso de vôo usando o HDInsight](../interactive-query/interactive-query-tutorial-analyze-flight-data.md): Use a consulta interativa para analisar dados de atraso de vôo e, em seguida, use Sqoop para exportar dados para um banco de dado SQL do Azure.
* [Carregar dados no hdinsight](../hdinsight-upload-data.md): Encontre outros métodos para carregar dados no hdinsight/armazenamento de BLOBs do Azure.
