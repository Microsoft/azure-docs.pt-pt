---
title: Apache Sqoop com Apache Hadoop - Azure HDInsight
description: Saiba como usar o Apache Sqoop para importar e exportar entre a Apache Hadoop na HDInsight e a Azure SQL Database.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 11/28/2019
ms.openlocfilehash: 2d0d38dee15817e56c2784981365ea331b6a8459
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/28/2021
ms.locfileid: "98943157"
---
# <a name="use-apache-sqoop-to-import-and-export-data-between-apache-hadoop-on-hdinsight-and-azure-sql-database"></a>Utilize o Apache Sqoop para importar e exportar dados entre a Apache Hadoop na HDInsight e a Azure SQL Database

[!INCLUDE [sqoop-selector](../../../includes/hdinsight-selector-use-sqoop.md)]

Aprenda a usar o Apache Sqoop para importar e exportar entre um cluster Apache Hadoop em Azure HDInsight e Azure SQL Database ou Microsoft SQL Server. Os passos deste documento utilizam o `sqoop` comando diretamente a partir do cabeçanode do cluster Hadoop. Utilize o SSH para ligar ao nó de cabeça e executar os comandos neste documento. Este artigo é uma continuação de [Use Apache Sqoop com Hadoop in HDInsight](./hdinsight-use-sqoop.md).

## <a name="prerequisites"></a>Pré-requisitos

* Conclusão do ambiente de teste de [configuração](./hdinsight-use-sqoop.md#create-cluster-and-sql-database) da [Utilização de Apache Sqoop com Hadoop em HDInsight](./hdinsight-use-sqoop.md).

* Um cliente SSH. Para obter mais informações, veja [Ligar ao HDInsight (Apache Hadoop) através de SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

* Familiaridade com Sqoop. Para mais informações, consulte [o Guia do Utilizador sqoop.](https://sqoop.apache.org/docs/1.4.7/SqoopUserGuide.html)

## <a name="set-up"></a>Configurar

1. Utilize [o comando ssh](../hdinsight-hadoop-linux-use-ssh-unix.md) para ligar ao seu cluster. Edite o comando abaixo substituindo o CLUSTERNAME pelo nome do seu cluster e, em seguida, insira o comando:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Para facilitar a utilização, desate as variáveis. `PASSWORD` `MYSQLSERVER` Substitua, e pelos `MYDATABASE` valores relevantes, e, em seguida, insira os comandos abaixo:

    ```bash
    export password='PASSWORD'
    export sqlserver="MYSQLSERVER"
    export database="MYDATABASE"


    export serverConnect="jdbc:sqlserver://$sqlserver.database.windows.net:1433;user=sqluser;password=$password"
    export serverDbConnect="jdbc:sqlserver://$sqlserver.database.windows.net:1433;user=sqluser;password=$password;database=$database"
    ```

## <a name="sqoop-export"></a>Exportação de Sqoop

Da Colmeia ao SQL.

1. Para verificar se o Sqoop pode ver a sua base de dados, insira o comando abaixo na sua ligação SSH aberta. Este comando devolve uma lista de bases de dados.

    ```bash
    sqoop list-databases --connect $serverConnect
    ```

1. Introduza o seguinte comando para ver uma lista de tabelas para a base de dados especificada:

    ```bash
    sqoop list-tables --connect $serverDbConnect
    ```

1. Para exportar dados da tabela Hive `hivesampletable` para a tabela na sua base de `mobiledata` dados, insira o comando abaixo na sua ligação SSH aberta:

    ```bash
    sqoop export --connect $serverDbConnect \
    -table mobiledata \
    --hcatalog-table hivesampletable
    ```

1. Para verificar se os dados foram exportados, utilize as seguintes consultas da sua ligação SSH para visualizar os dados exportados:

    ```bash
    sqoop eval --connect $serverDbConnect \
    --query "SELECT COUNT(*) from dbo.mobiledata WITH (NOLOCK)"


    sqoop eval --connect $serverDbConnect \
    --query "SELECT TOP(10) * from dbo.mobiledata WITH (NOLOCK)"
    ```

## <a name="sqoop-import"></a>Importação de Sqoop

Do SQL ao armazenamento Azure.

1. Introduza o comando abaixo na sua ligação SSH aberta para importar dados da `mobiledata` tabela em SQL, para o `wasbs:///tutorials/usesqoop/importeddata` diretório em HDInsight. Os campos dos dados são separados por um caractere de aba, e as linhas são terminadas por um personagem de nova linha.

    ```bash
    sqoop import --connect $serverDbConnect \
    --table mobiledata \
    --target-dir 'wasb:///tutorials/usesqoop/importeddata' \
    --fields-terminated-by '\t' \
    --lines-terminated-by '\n' -m 1
    ```

1. Em alternativa, também pode especificar uma tabela De Colmeia:

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

1. Uma vez concluída a importação, insira o seguinte comando na sua ligação SSH aberta para listar os dados no novo diretório:

    ```bash
    hadoop fs -tail /tutorials/usesqoop/importeddata/part-m-00000
    ```

1. Utilize [a beeline](./apache-hadoop-use-hive-beeline.md) para verificar se a tabela foi criada na Colmeia.

    1. Ligar

        ```bash
        beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http'
        ```

    1. Execute cada consulta abaixo de uma de cada vez e reveja a saída:

        ```hql
        show tables;
        describe mobiledata_imported2;
        SELECT COUNT(*) FROM mobiledata_imported2;
        SELECT * FROM mobiledata_imported2 LIMIT 10;
        ```

    1. Saída da beeline com `!exit` .

## <a name="limitations"></a>Limitações

* Exportação a granel - Com o HDInsight baseado em Linux, o conector Sqoop utilizado para exportar dados para o SQL não suporta inserções a granel.

* Loteamento - Com o HDInsight baseado em Linux, quando utiliza o `-batch` interruptor ao efetuar inserções, a Sqoop faz múltiplas inserções em vez de lotar as operações de inserção.

## <a name="important-considerations"></a>Considerações importantes

* Tanto o HDInsight como o SQL Server devem estar na mesma Rede Virtual Azure.

    Por exemplo, consulte o [Connect HDInsight no documento de rede no local.](./../connect-on-premises-network.md)

    Para obter mais informações sobre a utilização do HDInsight com uma Rede Virtual Azure, consulte o [documento Extend HDInsight com Azure Virtual Network.](../hdinsight-plan-virtual-network-deployment.md) Para obter mais informações sobre a Rede Virtual Azure, consulte o documento [de visão geral da rede](../../virtual-network/virtual-networks-overview.md) virtual.

* O SQL Server tem de ser configurado para permitir a autenticação SQL. Para mais informações, consulte o documento [Escolha um Modo de Autenticação.](/sql/relational-databases/security/choose-an-authentication-mode)

* Poderá ter de configurar o SQL Server para aceitar ligações remotas. Para obter mais informações, consulte a [resolução de problemas da ligação ao documento do motor do motor de base de dados do SQL Server.](https://social.technet.microsoft.com/wiki/contents/articles/2102.how-to-troubleshoot-connecting-to-the-sql-server-database-engine.aspx)

## <a name="next-steps"></a>Próximos passos

Agora aprendeste a usar o Sqoop. Para saber mais, veja:

* [Use Apache Oozie com HDInsight](../hdinsight-use-oozie-linux-mac.md): Use a ação Sqoop num fluxo de trabalho Oozie.
* [Analise os dados de atraso de voo usando HDInsight](../interactive-query/interactive-query-tutorial-analyze-flight-data.md): Use a Consulta Interativa para analisar os dados do atraso de voo e, em seguida, use o Sqoop para exportar dados para uma base de dados em Azure.
* [Faça upload de dados para HDInsight](../hdinsight-upload-data.md): Encontre outros métodos para enviar dados para o armazenamento HDInsight/Azure Blob.