---
title: 'Início rápido: Consulta Apache HBase no HDInsight do Azure - Shell do HBase'
description: Saiba como utilizar o Apache HBase Shell para executar consultas do Apache HBase.
keywords: hdinsight,hadoop,HBase
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: quickstart
ms.date: 05/08/2019
ms.author: hrasheed
ms.openlocfilehash: 41b16e63522a02cc16eb4dac2cbcc8e6540aceaf
ms.sourcegitcommit: f013c433b18de2788bf09b98926c7136b15d36f1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/13/2019
ms.locfileid: "65552087"
---
# <a name="quickstart-query-apache-hbase-in-azure-hdinsight-with-hbase-shell"></a>Início rápido: Consulta o Apache HBase no HDInsight do Azure com o Shell de HBase

Neste início rápido, irá aprender a utilizar o Apache HBase Shell para criar uma tabela de HBase, inserir dados e, em seguida, consultar a tabela.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

* Um cluster do Apache HBase. Ver [criar um cluster](../hadoop/apache-hadoop-linux-tutorial-get-started.md#create-cluster) para criar um cluster do HDInsight.  Certifique-se de que escolha o **HBase** tipo de cluster.

* Um cliente SSH. Para obter mais informações, consulte [ligar ao HDInsight (Apache Hadoop) através de SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="create-a-table-and-manipulate-data"></a>Criar uma tabela e manipular dados

Para a maioria das pessoas, os dados são apresentados no formato de tabela:

![HDInsight HBase tabular data](./media/query-hbase-with-hbase-shell/hdinsight-hbase-contacts-tabular.png)

No HBase (uma implementação do [Cloud BigTable](https://cloud.google.com/bigtable/)), os mesmos dados é semelhante a:

![HDInsight HBase BigTable data](./media/query-hbase-with-hbase-shell/hdinsight-hbase-contacts-bigtable.png)

Pode utilizar o SSH para ligar a HBase clusters e, em seguida, utilizar o Apache HBase Shell para criar tabelas de HBase, inserir dados e consultar dados.

1. Utilize `ssh` comando para ligar ao cluster do HBase. Editar o comando abaixo, substituindo `CLUSTERNAME` com o nome do cluster e, em seguida, introduza o comando:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

2. Utilize `hbase shell` comando para iniciar o shell interativo do HBase. Introduza o seguinte comando na sua ligação de SSH:

    ```bash
    hbase shell
    ```

3. Utilize `create` comando para criar uma tabela de HBase com duas famílias de coluna. Introduza o seguinte comando:

    ```hbase
    create 'Contacts', 'Personal', 'Office'
    ```

4. Utilize `list` command para listar todas as tabelas no HBase. Introduza o seguinte comando:

    ```hbase
    list
    ```

5. Utilize `put` comando para inserir valores numa coluna especificada numa linha especificada numa tabela específica. Introduza o seguinte comando:

    ```hbase
    put 'Contacts', '1000', 'Personal:Name', 'John Dole'
    put 'Contacts', '1000', 'Personal:Phone', '1-425-000-0001'
    put 'Contacts', '1000', 'Office:Phone', '1-425-000-0002'
    put 'Contacts', '1000', 'Office:Address', '1111 San Gabriel Dr.'
    ```

6. Uso `scan` comando para analisar e retornar o `Contacts` dados da tabela. Introduza o seguinte comando:

    ```hbase
    scan 'Contacts'
    ```

7. Utilize `get` comando para obter o conteúdo de uma linha. Introduza o seguinte comando:

    ```hbase
    get 'Contacts', '1000'
    ```

    Ver resultados semelhantes como utilizar o `scan` porque existe apenas uma linha de comando.

8. Utilize `delete` comando para eliminar um valor de célula numa tabela. Introduza o seguinte comando:

    ```hbase
    delete 'Contacts', '1000', 'Office:Address'
    ```

9. Utilize `disable` comando para desativar a tabela. Introduza o seguinte comando:

    ```hbase
    disable 'Contacts'
    ```

10. Utilize `drop` comando para remover uma tabela do HBase. Introduza o seguinte comando:

    ```hbase
    drop 'Contacts'
    ```

11. Utilize `exit` comando para parar o shell interativo do HBase. Introduza o seguinte comando:

    ```hbase
    exit
    ```

Para obter mais informações sobre o esquema de tabela do HBase, consulte [introdução ao Design do esquema HBase Apache](http://0b4af6cdc2f0c5998459-c0245c5c937c5dedcca3f1764ecc9b2f.r43.cf2.rackcdn.com/9353-login1210_khurana.pdf). Para obter mais comandos HBase, consulte [guia de referência Apache HBase](https://hbase.apache.org/book.html#quickstart).

## <a name="clean-up-resources"></a>Limpar recursos

Depois de concluir o início rápido, pode querer eliminar o cluster. Com o HDInsight, os dados são armazenados no Storage do Azure, pelo que pode eliminar um cluster em segurança quando este não está a ser utilizado. Também lhe é cobrado o valor de um cluster do HDInsight mesmo quando não o está a utilizar. Uma vez que os custos do cluster são muito superiores aos custos do armazenamento, faz sentido do ponto de vista económico eliminar os clusters quando não estiverem a ser utilizados.

Para eliminar um cluster, veja [eliminar um cluster do HDInsight com o seu browser, o PowerShell ou a CLI do Azure](../hdinsight-delete-cluster.md).

## <a name="next-steps"></a>Passos Seguintes

Neste início rápido, aprendeu como utilizar o Apache HBase Shell para criar uma tabela de HBase, inserir dados e, em seguida, consultar a tabela. Para saber mais sobre dados armazenados no HBase, o próximo artigo mostrarei como executar consultas com o Apache Spark.

> [!div class="nextstepaction"]
> [Utilizar o Apache Spark para ler e escrever dados de Apache HBase](../hdinsight-using-spark-query-hbase.md)