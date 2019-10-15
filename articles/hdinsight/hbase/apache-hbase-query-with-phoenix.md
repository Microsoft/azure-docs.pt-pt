---
title: 'Início rápido: consultar o Apache HBase no Azure HDInsight-Apache Phoenix'
description: Neste guia de início rápido, você aprende a usar o Apache Phoenix no HDInsight. Além disso, saiba como instalar e configurar o sqlline em seu computador para se conectar a um cluster HBase no HDInsight.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: quickstart
ms.date: 06/12/2019
ms.author: hrasheed
ms.openlocfilehash: 20af6d32d03ae5d4fe37b1a37198ef1f2c50ec95
ms.sourcegitcommit: 9dec0358e5da3ceb0d0e9e234615456c850550f6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/14/2019
ms.locfileid: "72312202"
---
# <a name="quickstart-query-apache-hbase-in-azure-hdinsight-with-apache-phoenix"></a>Início rápido: consultar o Apache HBase no Azure HDInsight com Apache Phoenix

Neste guia de início rápido, você aprende a usar o Apache Phoenix para executar consultas do HBase no Azure HDInsight. Apache Phoenix é um mecanismo de consulta SQL para o Apache HBase. Está acessível como um controlador JDBC e permite a consulta e gestão de tabelas do HBase através de SQL. [Sqlline](http://sqlline.sourceforge.net/) é um utilitário de linha de comando para executar o SQL.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

* Um cluster do Apache HBase. Consulte [criar cluster](../hadoop/apache-hadoop-linux-tutorial-get-started.md#create-cluster) para criar um cluster HDInsight.  Certifique-se de escolher o tipo de cluster **HBase** .

* Um cliente SSH. Para obter mais informações, consulte [conectar-se ao HDInsight (Apache Hadoop) usando o ssh](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="identify-a-zookeeper-node"></a>Identificar um nó ZooKeeper

Ao se conectar a um cluster HBase, você precisa se conectar a um dos nós de Apache ZooKeeper. Cada cluster HDInsight tem três nós ZooKeeper. A ondulação pode ser usada para identificar rapidamente um nó ZooKeeper. Edite o comando de ondulação abaixo, substituindo `PASSWORD` e `CLUSTERNAME` pelos valores relevantes e, em seguida, insira o comando em um prompt de comando:

```cmd
curl -u admin:PASSWORD -sS -G https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER
```

Uma parte da saída será semelhante a:

```output
    {
      "href" : "http://hn1-brim.432dc3rlshou3ocf251eycoapa.bx.internal.cloudapp.net:8080/api/v1/clusters/myCluster/hosts/zk0-brim.432dc3rlshou3ocf251eycoapa.bx.internal.cloudapp.net/host_components/ZOOKEEPER_SERVER",
      "HostRoles" : {
        "cluster_name" : "myCluster",
        "component_name" : "ZOOKEEPER_SERVER",
        "host_name" : "zk0-brim.432dc3rlshou3ocf251eycoapa.bx.internal.cloudapp.net"
      }
```

Anote o valor de `host_name` para uso posterior.

## <a name="create-a-table-and-manipulate-data"></a>Criar uma tabela e manipular dados

Você pode usar o SSH para se conectar a clusters HBase e, em seguida, usar Apache Phoenix para criar tabelas HBase, inserir dados e consultar dados.

1. Use o comando `ssh` para se conectar ao cluster HBase. Edite o comando a seguir substituindo `CLUSTERNAME` pelo nome do cluster e, em seguida, digite o comando:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

2. Altere o diretório para o cliente Phoenix. Introduza o seguinte comando:

    ```bash
    cd /usr/hdp/current/phoenix-client/bin
    ```

3. Inicie o [sqlline](http://sqlline.sourceforge.net/). Edite o comando a seguir substituindo `ZOOKEEPER` pelo nó ZooKeeper identificado anteriormente e, em seguida, digite o comando:

    ```bash
    ./sqlline.py ZOOKEEPER:2181:/hbase-unsecure
    ```

4. Crie uma tabela do HBase. Introduza o seguinte comando:

    ```sql
    CREATE TABLE Company (company_id INTEGER PRIMARY KEY, name VARCHAR(225));
    ```

5. Use o comando sqlline `!tables` para listar todas as tabelas no HBase. Introduza o seguinte comando:

    ```sqlline
    !tables
    ```

6. Inserir valores na tabela. Introduza o seguinte comando:

    ```sql
    UPSERT INTO Company VALUES(1, 'Microsoft');
    UPSERT INTO Company VALUES(2, 'Apache');
    ```

7. Consulte a tabela. Introduza o seguinte comando:

    ```sql
    SELECT * FROM Company;
    ```

8. Excluir um registro. Introduza o seguinte comando:

    ```sql
    DELETE FROM Company WHERE COMPANY_ID=1;
    ```

9. Descarte a tabela. Introduza o seguinte comando:

    ```hbase
    DROP TABLE Company;
    ```

10. Use o comando sqlline `!quit` para sair de sqlline. Introduza o seguinte comando:

    ```sqlline
    !quit
    ```

## <a name="clean-up-resources"></a>Limpar recursos

Depois de concluir o início rápido, talvez você queira excluir o cluster. Com o HDInsight, os dados são armazenados no Storage do Azure, pelo que pode eliminar um cluster em segurança quando este não está a ser utilizado. Também lhe é cobrado o valor de um cluster do HDInsight mesmo quando não o está a utilizar. Uma vez que os custos do cluster são muito superiores aos custos do armazenamento, faz sentido do ponto de vista económico eliminar os clusters quando não estiverem a ser utilizados.

Para excluir um cluster, consulte [excluir um cluster HDInsight usando seu navegador, o PowerShell ou o CLI do Azure](../hdinsight-delete-cluster.md).

## <a name="next-steps"></a>Passos seguintes

Neste guia de início rápido, você aprendeu a usar o Apache Phoenix para executar consultas do HBase no Azure HDInsight. Para saber mais sobre Apache Phoenix, o próximo artigo fornecerá um exame mais aprofundado.

> [!div class="nextstepaction"]
> [Apache Phoenix no HDInsight](../hdinsight-phoenix-in-hdinsight.md)
