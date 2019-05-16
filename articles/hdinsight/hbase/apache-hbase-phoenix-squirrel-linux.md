---
title: 'Início rápido: Consulta Apache HBase no HDInsight do Azure - Apache Phoenix'
description: Saiba como utilizar o Apache Phoenix no HDInsight. Além disso, saiba como instalar e configurar SQLLine no seu computador para ligar a um cluster do HBase no HDInsight.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: quickstart
ms.date: 05/08/2019
ms.author: hrasheed
ms.openlocfilehash: 46606a991ce878a3335c2c605a4040c9520d5128
ms.sourcegitcommit: 1fbc75b822d7fe8d766329f443506b830e101a5e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/14/2019
ms.locfileid: "65596196"
---
# <a name="quickstart-query-apache-hbase-in-azure-hdinsight-with-apache-phoenix"></a>Início rápido: Consulta Apache HBase no HDInsight do Azure com o Apache Phoenix

Neste início rápido, irá aprender a utilizar o Apache Phoenix para executar consultas de HBase no HDInsight do Azure. O Apache Phoenix é um motor de consulta SQL para o Apache HBase. Está acessível como um controlador JDBC e permite a consulta e gestão de tabelas do HBase através de SQL. [SQLLine](http://sqlline.sourceforge.net/) é um utilitário de linha de comandos para executar o SQL.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

* Um cluster do Apache HBase. Ver [criar um cluster](../hadoop/apache-hadoop-linux-tutorial-get-started.md#create-cluster) para criar um cluster do HDInsight.  Certifique-se de que escolha o **HBase** tipo de cluster.

* Um cliente SSH. Para obter mais informações, consulte [ligar ao HDInsight (Apache Hadoop) através de SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="identify-a-zookeeper-node"></a>Identificar um nó do ZooKeeper

Quando se liga a um cluster do HBase, tem de ligar a um de nós do Apache ZooKeeper. Cada cluster do HDInsight tem três nós ZooKeeper. Curl pode ser utilizado para identificar rapidamente um nó do ZooKeeper. Editar o comando curl abaixo, substituindo `PASSWORD` e `CLUSTERNAME` com os valores relevantes e, em seguida, introduza o comando numa linha de comandos:

```cmd
curl -u admin:PASSWORD -sS -G https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER
```

Uma parte da saída terá um aspeto semelhante a:

```output
    {
      "href" : "http://hn1-brim.432dc3rlshou3ocf251eycoapa.bx.internal.cloudapp.net:8080/api/v1/clusters/myCluster/hosts/zk0-brim.432dc3rlshou3ocf251eycoapa.bx.internal.cloudapp.net/host_components/ZOOKEEPER_SERVER",
      "HostRoles" : {
        "cluster_name" : "myCluster",
        "component_name" : "ZOOKEEPER_SERVER",
        "host_name" : "zk0-brim.432dc3rlshou3ocf251eycoapa.bx.internal.cloudapp.net"
      }
```

Tome nota do valor para `host_name` para utilização posterior.

## <a name="create-a-table-and-manipulate-data"></a>Criar uma tabela e manipular dados

Pode utilizar o SSH para ligar a HBase clusters e, em seguida, utilizar o Apache Phoenix para criar tabelas de HBase, inserir dados e consultar dados.

1. Utilize `ssh` comando para ligar ao cluster do HBase. Editar o comando abaixo, substituindo `CLUSTERNAME` com o nome do cluster e, em seguida, introduza o comando:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

2. Altere o diretório para o cliente de Phoenix. Introduza o seguinte comando:

    ```bash
    cd /usr/hdp/current/phoenix-client/bin
    ```

3. Inicie [SQLLine](http://sqlline.sourceforge.net/). Editar o comando abaixo, substituindo `ZOOKEEPER` com o nó do ZooKeeper identificado anteriormente, em seguida, introduza o comando:

    ```bash
    ./sqlline.py ZOOKEEPER:2181:/hbase-unsecure
    ```

4. Crie uma tabela de HBase. Introduza o seguinte comando:

    ```sql
    CREATE TABLE Company (company_id INTEGER PRIMARY KEY, name VARCHAR(225));
    ```

5. Utilizar o SQLLine `!tables` command para listar todas as tabelas no HBase. Introduza o seguinte comando:

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

8. Elimine um registo. Introduza o seguinte comando:

    ```sql
    DELETE FROM Company WHERE COMPANY_ID=1;
    ```

9. Remova a tabela. Introduza o seguinte comando:

    ```hbase
    DROP TABLE Company;
    ```

10. Utilizar o SQLLine `!quit` comando para sair SQLLine. Introduza o seguinte comando:

    ```sqlline
    !quit
    ```

## <a name="clean-up-resources"></a>Limpar recursos

Depois de concluir o início rápido, pode querer eliminar o cluster. Com o HDInsight, os dados são armazenados no Storage do Azure, pelo que pode eliminar um cluster em segurança quando este não está a ser utilizado. Também lhe é cobrado o valor de um cluster do HDInsight mesmo quando não o está a utilizar. Uma vez que os custos do cluster são muito superiores aos custos do armazenamento, faz sentido do ponto de vista económico eliminar os clusters quando não estiverem a ser utilizados.

Para eliminar um cluster, veja [eliminar um cluster do HDInsight com o seu browser, o PowerShell ou a CLI do Azure](../hdinsight-delete-cluster.md).

## <a name="next-steps"></a>Passos Seguintes

Neste início rápido, aprendeu a utilizar o Apache Phoenix para executar consultas de HBase no HDInsight do Azure. Para saber mais sobre o Apache Phoenix, o próximo artigo fornecerá uma análise mais aprofundada.

> [!div class="nextstepaction"]
> [Apache Phoenix no HDInsight](../hdinsight-phoenix-in-hdinsight.md)

## <a name="see-also"></a>Consultar Também

* [Manual de SQLLine](http://sqlline.sourceforge.net/#manual).
* [Gramática de Apache Phoenix](https://phoenix.apache.org/language/index.html).
* [Apache Phoenix em 15 minutos ou menos](https://phoenix.apache.org/Phoenix-in-15-minutes-or-less.html)
* [Descrição geral do HBase do HDInsight](./apache-hbase-overview.md)
