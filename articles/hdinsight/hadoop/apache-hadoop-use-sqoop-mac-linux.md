---
title: Apache Sqoop com Apache Hadoop - Azure HDInsight
description: Aprenda a usar apache Sqoop para importar e exportar entre Apache Hadoop em HDInsight e uma Base de Dados Azure SQL.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 11/28/2019
ms.openlocfilehash: 21bc903349876a76576fb742840e9899f9d94bcd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74769392"
---
# <a name="use-apache-sqoop-to-import-and-export-data-between-apache-hadoop-on-hdinsight-and-sql-database"></a>Utilizar o Apache Sqoop para importar e exportar dados entre o Apache Hadoop no HDInsight e a Base de Dados SQL

[!INCLUDE [sqoop-selector](../../../includes/hdinsight-selector-use-sqoop.md)]

Saiba como utilizar o Apache Sqoop para importar e exportar entre um cluster Apache Hadoop em Azure HDInsight e Azure SQL Database ou Microsoft SQL Server. Os passos deste `sqoop` documento utilizam o comando diretamente a partir do nódoo do aglomerado hadoop. Usa o SSH para se ligar ao nó da cabeça e executar os comandos neste documento. Este artigo é uma continuação do [Use Apache Sqoop com Hadoop em HDInsight](./hdinsight-use-sqoop.md).

## <a name="prerequisites"></a>Pré-requisitos

* Conclusão do ambiente de teste de [configuração](./hdinsight-use-sqoop.md#create-cluster-and-sql-database) a partir de [Use Apache Sqoop com Hadoop em HDInsight](./hdinsight-use-sqoop.md).

* Um cliente SSH. Para mais informações, consulte [Connect to HDInsight (Apache Hadoop) utilizando O SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

* Familiaridade com Sqoop. Para mais informações, consulte o [Guia do Utilizador da Sqoop](https://sqoop.apache.org/docs/1.4.7/SqoopUserGuide.html).

## <a name="set-up"></a>Configurar

1. Utilize [o comando ssh](../hdinsight-hadoop-linux-use-ssh-unix.md) para se ligar ao seu cluster. Editar o comando abaixo substituindo CLUSTERNAME pelo nome do seu cluster e, em seguida, introduzir o comando:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Para facilitar a utilização, detete as variáveis. Substitua, `PASSWORD` `MYSQLSERVER` `MYDATABASE` e com os valores relevantes, e, em seguida, introduza os comandos abaixo:

    ```bash
    export password='PASSWORD'
    export sqlserver="MYSQLSERVER"
    export database="MYDATABASE"


    export serverConnect="jdbc:sqlserver://$sqlserver.database.windows.net:1433;user=sqluser;password=$password"
    export serverDbConnect="jdbc:sqlserver://$sqlserver.database.windows.net:1433;user=sqluser;password=$password;database=$database"
    ```

## <a name="sqoop-export"></a>Exportação de Sqoop

Da Colmeia ao Servidor SQL.

1. Para verificar se o SQoop pode ver a sua Base de Dados SQL, introduza o comando abaixo na sua ligação SSH aberta. Este comando devolve uma lista de bases de dados.

    ```bash
    sqoop list-databases --connect $serverConnect
    ```

1. Insira o seguinte comando para ver uma lista de tabelas para a base de dados especificada:

    ```bash
    sqoop list-tables --connect $serverDbConnect
    ```

1. Para exportar dados da `hivesampletable` tabela `mobiledata` Hive para a tabela na Base de Dados SQL, insira o comando abaixo na sua ligação SSH aberta:

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

Do SQL Server ao armazenamento Azure.

1. Introduza o comando abaixo na sua ligação `mobiledata` SSH aberta para importar `wasbs:///tutorials/usesqoop/importeddata` dados da tabela na Base de Dados SQL, para o diretório no HDInsight. Os campos dos dados são separados por um caracteres de separador, e as linhas são terminadas por um personagem de linha nova.

    ```bash
    sqoop import --connect $serverDbConnect \
    --table mobiledata \
    --target-dir 'wasb:///tutorials/usesqoop/importeddata' \
    --fields-terminated-by '\t' \
    --lines-terminated-by '\n' -m 1
    ```

1. Em alternativa, também pode especificar uma tabela da Colmeia:

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

1. Utilize a linha de [apição](./apache-hadoop-use-hive-beeline.md) para verificar se a mesa foi criada na Colmeia.

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

    1. Sai com `!exit`.

## <a name="limitations"></a>Limitações

* Exportação a granel - Com o HDInsight baseado em Linux, o conector Sqoop utilizado para exportar dados para o Microsoft SQL Server ou para a Base de Dados Azure SQL não suporta inserções a granel.

* Loteamento - Com hDInsight baseado em `-batch` Linux, ao utilizar o interruptor ao executar inserções, o Sqoop faz várias inserções em vez de encher as operações de inserção.

## <a name="important-considerations"></a>Considerações importantes

* Tanto o HDInsight como o SQL Server devem estar na mesma Rede Virtual Azure.

    Por exemplo, consulte o [Connect HDInsight no seu](./../connect-on-premises-network.md) documento de rede no local.

    Para obter mais informações sobre a utilização do HDInsight com uma Rede Virtual Azure, consulte o Extend HDInsight com o documento [da Rede Virtual Azure.](../hdinsight-plan-virtual-network-deployment.md) Para obter mais informações sobre a Rede Virtual Azure, consulte o documento [de visão geral da rede virtual.](../../virtual-network/virtual-networks-overview.md)

* O Servidor SQL deve ser configurado para permitir a autenticação SQL. Para mais informações, consulte o documento ['Modo de Autenticação'.](https://msdn.microsoft.com/ms144284.aspx)

* Pode ter de configurar o SQL Server para aceitar ligações remotas. Para mais informações, consulte o ["Como resolver problemas" ao](https://social.technet.microsoft.com/wiki/contents/articles/2102.how-to-troubleshoot-connecting-to-the-sql-server-database-engine.aspx) documento do motor de base de dados SQL Server.

## <a name="next-steps"></a>Passos seguintes

Agora aprendeste a usar o Sqoop. Para saber mais, consulte:

* [Utilize o Apache Oozie com hDInsight](../hdinsight-use-oozie-linux-mac.md): Use a ação Sqoop num fluxo de trabalho Oozie.
* [Analise os dados de atraso de voo usando o HDInsight](../interactive-query/interactive-query-tutorial-analyze-flight-data.md): Use a Consulta Interativa para analisar os dados de atraso de voo e, em seguida, use sqoop para exportar dados para uma base de dados Azure SQL.
* [Upload de dados para HDInsight](../hdinsight-upload-data.md): Encontre outros métodos para o upload de dados para armazenamento HDInsight/Azure Blob.
