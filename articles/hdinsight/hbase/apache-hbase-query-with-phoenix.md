---
title: 'Quickstart: Apache HBase & Apache Phoenix - Azure HDInsight'
description: Neste arranque rápido, aprende-se a usar a Apache Phoenix no HDInsight. Além disso, aprenda a instalar e configurar o SQLLine no seu computador para se ligar a um cluster HBase no HDInsight.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: quickstart
ms.date: 06/12/2019
ms.author: hrasheed
ms.openlocfilehash: 1c400e41c4c10023d2595bde8c0d62e26184cf05
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "79370326"
---
# <a name="quickstart-query-apache-hbase-in-azure-hdinsight-with-apache-phoenix"></a>Quickstart: Consulta Apache HBase em Azure HDInsight com Apache Phoenix

Neste arranque rápido, aprende-se a usar o Apache Phoenix para executar consultas De HBase em Azure HDInsight. Apache Phoenix é um motor de consulta SQL para Apache HBase. Está acessível como um controlador JDBC e permite a consulta e gestão de tabelas do HBase através de SQL. [SQLLine](http://sqlline.sourceforge.net/) é um utilitário de linha de comando para executar SQL.

Se não tiver uma subscrição Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

* Um aglomerado Apache HBase. Consulte [o cluster Create](../hadoop/apache-hadoop-linux-tutorial-get-started.md) para criar um cluster HDInsight.  Certifique-se de que escolhe o tipo de cluster **HBase.**

* Um cliente SSH. Para mais informações, consulte [Connect to HDInsight (Apache Hadoop) utilizando O SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="identify-a-zookeeper-node"></a>Identifique um nó ZooKeeper

Quando se conecta a um cluster HBase, precisa de se ligar a um dos nós do ZooKeeper Apache. Cada cluster HDInsight tem três nós zookeeper. O caracol pode ser usado para identificar rapidamente um nó ZooKeeper. Editar o comando de `PASSWORD` caracóis abaixo substituindo e `CLUSTERNAME` com os valores relevantes, e, em seguida, entrar no comando num pedido de comando:

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

Tome nota do `host_name` valor para posterior utilização.

## <a name="create-a-table-and-manipulate-data"></a>Criar uma tabela e manipular dados

Pode utilizar o SSH para se ligar aos clusters HBase e, em seguida, utilizar o Apache Phoenix para criar tabelas HBase, inserir dados e consultar dados.

1. Utilize `ssh` o comando para se ligar ao seu cluster HBase. Edite o comando `CLUSTERNAME` abaixo substituindo pelo nome do seu cluster e, em seguida, introduza o comando:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

2. Mude o diretório para o cliente Phoenix. Introduza o seguinte comando:

    ```bash
    cd /usr/hdp/current/phoenix-client/bin
    ```

3. Lançamento [SQLline](http://sqlline.sourceforge.net/). Editar o comando abaixo `ZOOKEEPER` substituindo o nó ZooKeeper identificado anteriormente e, em seguida, entrar no comando:

    ```bash
    ./sqlline.py ZOOKEEPER:2181:/hbase-unsecure
    ```

4. Crie uma tabela HBase. Introduza o seguinte comando:

    ```sql
    CREATE TABLE Company (company_id INTEGER PRIMARY KEY, name VARCHAR(225));
    ```

5. Utilize o comando `!tables` SQLLine para listar todas as tabelas em HBase. Introduza o seguinte comando:

    ```sqlline
    !tables
    ```

6. Insira os valores na tabela. Introduza o seguinte comando:

    ```sql
    UPSERT INTO Company VALUES(1, 'Microsoft');
    UPSERT INTO Company VALUES(2, 'Apache');
    ```

7. Consulta a mesa. Introduza o seguinte comando:

    ```sql
    SELECT * FROM Company;
    ```

8. Apagar um disco. Introduza o seguinte comando:

    ```sql
    DELETE FROM Company WHERE COMPANY_ID=1;
    ```

9. Larga a mesa. Introduza o seguinte comando:

    ```hbase
    DROP TABLE Company;
    ```

10. Utilize o comando `!quit` SQLLine para sair do SQLLine. Introduza o seguinte comando:

    ```sqlline
    !quit
    ```

## <a name="clean-up-resources"></a>Limpar recursos

Depois de completar o arranque rápido, poderá querer eliminar o cluster. Com o HDInsight, os dados são armazenados no Storage do Azure, pelo que pode eliminar um cluster em segurança quando este não está a ser utilizado. Também lhe é cobrado o valor de um cluster do HDInsight mesmo quando não o está a utilizar. Uma vez que os custos do cluster são muito superiores aos custos do armazenamento, faz sentido do ponto de vista económico eliminar os clusters quando não estiverem a ser utilizados.

Para eliminar um cluster, consulte [Eliminar um cluster HDInsight utilizando o seu navegador, PowerShell ou o Azure CLI](../hdinsight-delete-cluster.md).

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, aprendeu a usar o Apache Phoenix para executar consultas De HBase em Azure HDInsight. Para saber mais sobre Apache Phoenix, o próximo artigo fornecerá um exame mais profundo.

> [!div class="nextstepaction"]
> [Apache Phoenix no HDInsight](../hdinsight-phoenix-in-hdinsight.md)
