---
title: 'Início rápido: Consultar o Apache HBase no Azure HDInsight-shell do HBase'
description: Neste guia de início rápido, você aprende a usar o Shell do Apache HBase para executar consultas do Apache HBase.
keywords: hdinsight, Hadoop, HBase
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: quickstart
ms.date: 06/12/2019
ms.author: hrasheed
ms.openlocfilehash: 033227f085cd23c5fa26313cb4a2816070676560
ms.sourcegitcommit: 8ef0a2ddaece5e7b2ac678a73b605b2073b76e88
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/17/2019
ms.locfileid: "71076433"
---
# <a name="quickstart-query-apache-hbase-in-azure-hdinsight-with-hbase-shell"></a>Início rápido: Consultar o Apache HBase no Azure HDInsight com o Shell do HBase

Neste guia de início rápido, você aprende a usar o Shell do Apache HBase para criar uma tabela do HBase, inserir dados e, em seguida, consultar a tabela.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

* Um cluster do Apache HBase. Consulte [criar cluster](../hadoop/apache-hadoop-linux-tutorial-get-started.md#create-cluster) para criar um cluster HDInsight.  Certifique-se de escolher o tipo de cluster **HBase** .

* Um cliente SSH. Para obter mais informações, consulte [conectar-se ao HDInsight (Apache Hadoop) usando o ssh](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="create-a-table-and-manipulate-data"></a>Criar uma tabela e manipular dados

Para a maioria das pessoas, os dados são apresentados no formato de tabela:

![Dados de tabela do Apache HBase do HDInsight](./media/query-hbase-with-hbase-shell/hdinsight-hbase-contacts-tabular.png)

No HBase (uma implementação de [BigTable de nuvem](https://cloud.google.com/bigtable/)), os mesmos dados são semelhantes a:

![Dados de BigTable do Apache HBase do HDInsight](./media/query-hbase-with-hbase-shell/hdinsight-hbase-contacts-bigtable.png)

Você pode usar o SSH para se conectar a clusters HBase e, em seguida, usar o Shell do Apache HBase para criar tabelas HBase, inserir dados e consultar dados.

1. Use `ssh` o comando para se conectar ao cluster HBase. Edite o comando a seguir `CLUSTERNAME` substituindo pelo nome do cluster e, em seguida, digite o comando:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

2. Use `hbase shell` o comando para iniciar o shell interativo do HBase. Digite o seguinte comando em sua conexão SSH:

    ```bash
    hbase shell
    ```

3. Use `create` o comando para criar uma tabela do HBase com famílias de duas colunas. Introduza o seguinte comando:

    ```hbase
    create 'Contacts', 'Personal', 'Office'
    ```

4. Use `list` o comando para listar todas as tabelas no HBase. Introduza o seguinte comando:

    ```hbase
    list
    ```

5. Use `put` o comando para inserir valores em uma coluna especificada em uma linha especificada em uma tabela específica. Introduza o seguinte comando:

    ```hbase
    put 'Contacts', '1000', 'Personal:Name', 'John Dole'
    put 'Contacts', '1000', 'Personal:Phone', '1-425-000-0001'
    put 'Contacts', '1000', 'Office:Phone', '1-425-000-0002'
    put 'Contacts', '1000', 'Office:Address', '1111 San Gabriel Dr.'
    ```

6. Use `scan` o comando para verificar e retornar `Contacts` os dados da tabela. Introduza o seguinte comando:

    ```hbase
    scan 'Contacts'
    ```

7. Use `get` o comando para buscar o conteúdo de uma linha. Introduza o seguinte comando:

    ```hbase
    get 'Contacts', '1000'
    ```

    Você vê resultados semelhantes ao usar o `scan` comando porque há apenas uma linha.

8. Use `delete` o comando para excluir um valor de célula em uma tabela. Introduza o seguinte comando:

    ```hbase
    delete 'Contacts', '1000', 'Office:Address'
    ```

9. Use `disable` o comando para desabilitar a tabela. Introduza o seguinte comando:

    ```hbase
    disable 'Contacts'
    ```

10. Use `drop` o comando para remover uma tabela do HBase. Introduza o seguinte comando:

    ```hbase
    drop 'Contacts'
    ```

11. Use `exit` o comando para interromper o shell interativo do HBase. Introduza o seguinte comando:

    ```hbase
    exit
    ```

Para obter mais informações sobre o esquema de tabela do HBase, consulte [introdução ao design de esquema do Apache HBase](http://0b4af6cdc2f0c5998459-c0245c5c937c5dedcca3f1764ecc9b2f.r43.cf2.rackcdn.com/9353-login1210_khurana.pdf). Para obter mais comandos do HBase, consulte [Guia de referência do Apache HBase](https://hbase.apache.org/book.html#quickstart).

## <a name="clean-up-resources"></a>Limpar recursos

Depois de concluir o início rápido, talvez você queira excluir o cluster. Com o HDInsight, os dados são armazenados no Storage do Azure, pelo que pode eliminar um cluster em segurança quando este não está a ser utilizado. Também lhe é cobrado o valor de um cluster do HDInsight mesmo quando não o está a utilizar. Uma vez que os custos do cluster são muito superiores aos custos do armazenamento, faz sentido do ponto de vista económico eliminar os clusters quando não estiverem a ser utilizados.

Para excluir um cluster, consulte [excluir um cluster HDInsight usando seu navegador, o PowerShell ou o CLI do Azure](../hdinsight-delete-cluster.md).

## <a name="next-steps"></a>Passos seguintes

Neste guia de início rápido, você aprendeu a usar o Shell do Apache HBase para criar uma tabela do HBase, inserir dados e, em seguida, consultar a tabela. Para saber mais sobre os dados armazenados no HBase, o próximo artigo mostrará como executar consultas com Apache Spark.

> [!div class="nextstepaction"]
> [Usar Apache Spark para ler e gravar dados do Apache HBase](../hdinsight-using-spark-query-hbase.md)