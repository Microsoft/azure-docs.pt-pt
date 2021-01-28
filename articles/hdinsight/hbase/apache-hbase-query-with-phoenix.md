---
title: 'Quickstart: Apache HBase & Apache Phoenix - Azure HDInsight'
description: Neste arranque rápido, aprende-se a usar o Apache Phoenix em HDInsight. Além disso, aprenda a instalar e instale o SQLLine no seu computador para se conectar a um cluster HBase em HDInsight.
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: quickstart
ms.date: 06/12/2019
ms.openlocfilehash: 6b9196d9ad5cf07cd210726bc1af7121c08094a8
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/28/2021
ms.locfileid: "98935981"
---
# <a name="quickstart-query-apache-hbase-in-azure-hdinsight-with-apache-phoenix"></a>Quickstart: Consulta Apache HBase em Azure HDInsight com Apache Phoenix

Neste arranque rápido, você aprende a usar o Apache Phoenix para executar consultas HBase em Azure HDInsight. Apache Phoenix é um motor de consulta SQL para Apache HBase. Está acessível como um controlador JDBC e permite a consulta e gestão de tabelas do HBase através de SQL. [SQLLine](http://sqlline.sourceforge.net/) é um utilitário de linha de comando para executar SQL.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

* Um aglomerado Apache HBase. Consulte [criar cluster](../hadoop/apache-hadoop-linux-tutorial-get-started.md) para criar um cluster HDInsight.  Certifique-se de que escolhe o tipo de cluster **HBase.**

* Um cliente SSH. Para obter mais informações, veja [Ligar ao HDInsight (Apache Hadoop) através de SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="identify-a-zookeeper-node"></a>Identifique um nó ZooKeeper

Quando se liga a um cluster HBase, tem de se ligar a um dos nós apache zookeeper. Cada cluster HDInsight tem três nós ZooKeeper. O curl pode ser usado para identificar rapidamente um nó ZooKeeper. Edite o comando do caracol abaixo, substituindo `PASSWORD` e `CLUSTERNAME` pelos valores relevantes, e, em seguida, introduza o comando num pedido de comando:

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

Tome nota do valor `host_name` para utilização posterior.

## <a name="create-a-table-and-manipulate-data"></a>Criar uma tabela e manipular dados

Pode utilizar o SSH para ligar aos clusters HBase e, em seguida, utilizar o Apache Phoenix para criar tabelas HBase, inserir dados e dados de consulta.

1. Utilize `ssh` o comando para ligar ao seu cluster HBase. Edite o comando abaixo substituindo `CLUSTERNAME` pelo nome do seu cluster e, em seguida, insira o comando:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

2. Mude o diretório para o cliente Phoenix. Introduza o seguinte comando:

    ```bash
    cd /usr/hdp/current/phoenix-client/bin
    ```

3. Lançar [SQLLine](http://sqlline.sourceforge.net/). Editar o comando abaixo substituindo o `ZOOKEEPER` nó ZooKeeper identificado anteriormente e, em seguida, insira o comando:

    ```bash
    ./sqlline.py ZOOKEEPER:2181:/hbase-unsecure
    ```

4. Crie uma tabela HBase. Introduza o seguinte comando:

    ```sql
    CREATE TABLE Company (company_id INTEGER PRIMARY KEY, name VARCHAR(225));
    ```

5. Utilize o comando SQLLine `!tables` para listar todas as tabelas na Base H. Introduza o seguinte comando:

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

10. Utilize o comando SQLLine `!quit` para sair da SQLLine. Introduza o seguinte comando:

    ```sqlline
    !quit
    ```

## <a name="clean-up-resources"></a>Limpar os recursos

Depois de completar o arranque rápido, é possível que queira eliminar o cluster. Com o HDInsight, os dados são armazenados no Storage do Azure, pelo que pode eliminar um cluster em segurança quando este não está a ser utilizado. Também lhe é cobrado o valor de um cluster do HDInsight mesmo quando não o está a utilizar. Uma vez que os custos do cluster são muito superiores aos custos do armazenamento, faz sentido do ponto de vista económico eliminar os clusters quando não estiverem a ser utilizados.

Para eliminar um cluster, consulte [Eliminar um cluster HDInsight utilizando o seu navegador, PowerShell ou o Azure CLI](../hdinsight-delete-cluster.md).

## <a name="next-steps"></a>Próximos passos

Neste arranque rápido, aprendeu a usar o Apache Phoenix para executar consultas HBase em Azure HDInsight. Para saber mais sobre Apache Phoenix, o próximo artigo irá fornecer um exame mais profundo.

> [!div class="nextstepaction"]
> [Apache Phoenix no HDInsight](../hdinsight-phoenix-in-hdinsight.md)
