---
title: 'Quickstart: Apache Hive in Azure HDInsight with Apache Zeppelin'
description: Neste arranque rápido, aprende-se a usar o Apache Zeppelin para executar consultas de Colmeia Apache.
ms.service: hdinsight
ms.topic: quickstart
ms.custom: hdinsightactive
ms.date: 12/03/2019
ms.openlocfilehash: 1199fee82532eacf1f0ef41b877b970d7d9f2cf5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98941065"
---
# <a name="quickstart-execute-apache-hive-queries-in-azure-hdinsight-with-apache-zeppelin"></a>Quickstart: Execute consultas apache hive em Azure HDInsight com Apache Zeppelin

Neste arranque rápido, você aprende a usar Apache Zeppelin para executar consultas [apache Hive](https://hive.apache.org/) em Azure HDInsight. Os clusters de consultas interativas HDInsight incluem cadernos [Apache Zeppelin](https://zeppelin.apache.org/) que você pode usar para executar consultas interativas de Hive.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Um cluster de consulta interativa HDInsight. Consulte [criar cluster](../hadoop/apache-hadoop-linux-tutorial-get-started.md) para criar um cluster HDInsight.  Certifique-se de escolher o tipo de cluster **de consulta interativa.**

## <a name="create-an-apache-zeppelin-note"></a>Criar uma Nota De Zeppelin Apache

1. `CLUSTERNAME`Substitua-o pelo nome do seu cluster no seguinte URL `https://CLUSTERNAME.azurehdinsight.net/zeppelin` . Em seguida, insira o URL num navegador web.

2. Insira o nome de utilizador e a palavra-passe do seu cluster. A partir da página zeppelin, pode criar uma nova nota ou abrir notas existentes. **HiveSample** contém algumas consultas de hive amostra.  

    ![HDInsight Interactive Query zeppelin](./media/hdinsight-connect-hive-zeppelin/hdinsight-hive-zeppelin.png)

3. **Selecione Criar nova nota**.

4. A partir do **diálogo de notas nova,** escreva ou selecione os seguintes valores:

    - Nota: Introduza um nome para a nota.
    - Intérprete predefinido: Selecione **jdbc** da lista de drop-down.

5. Selecione **Criar Nota**.

6. Introduza a seguinte consulta de Colmeia na secção de código e, em seguida, prima **Shift + Enter**:

    ```hive
    %jdbc(hive)
    show tables
    ```

    ![HDInsight Interactive Query zeppelin executa consulta](./media/hdinsight-connect-hive-zeppelin/hdinsight-hive-zeppelin-query.png)

    A declaração **de %jdbc (colmeia)** na primeira linha diz ao caderno para usar o intérprete Hive JDBC.

    A consulta deve devolver uma mesa de colmeia chamada **hivesmpletable**.

    Seguem-se duas consultas adicionais de Colmeia que pode correr contra **o hivesampletable:**

    ```hive
    %jdbc(hive)
    select * from hivesampletable limit 10

    %jdbc(hive)
    select ${group_name}, count(*) as total_count
    from hivesampletable
    group by ${group_name=market,market|deviceplatform|devicemake}
    limit ${total_count=10}
    ```

    Comparando com a Colmeia tradicional, os resultados da consulta devem voltar mais rápido.

### <a name="additional-examples"></a>Exemplos adicionais

1. Criar uma mesa. Execute o código abaixo no Caderno Zeppelin:

    ```hql
    %jdbc(hive)
    CREATE EXTERNAL TABLE log4jLogs (
        t1 string,
        t2 string,
        t3 string,
        t4 string,
        t5 string,
        t6 string,
        t7 string)
    ROW FORMAT DELIMITED
    FIELDS TERMINATED BY ' '
    STORED AS TEXTFILE;
    ```

1. Carregue os dados na nova tabela. Execute o código abaixo no Caderno Zeppelin:

    ```hql
    %jdbc(hive)
    LOAD DATA
    INPATH 'wasbs:///example/data/sample.log'
    INTO TABLE log4jLogs;
    ```

1. Insira um único registo. Execute o código abaixo no Caderno Zeppelin:

    ```hql
    %jdbc(hive)
    INSERT INTO TABLE log4jLogs2
    VALUES ('A', 'B', 'C', 'D', 'E', 'F', 'G');
    ```

Reveja o [manual de linguagem Hive](https://cwiki.apache.org/confluence/display/Hive/LanguageManual) para obter sintaxe adicional.

## <a name="clean-up-resources"></a>Limpar os recursos

Depois de completar o arranque rápido, é possível que queira eliminar o cluster. Com o HDInsight, os seus dados são armazenados no Azure Storage, para que possa eliminar com segurança um cluster quando este não estiver a ser utilizado. Também é cobrado por um cluster HDInsight, mesmo quando não está a ser utilizado. Uma vez que as taxas para o cluster são muitas vezes mais do que os encargos de armazenamento, faz sentido económico apagar clusters quando não estão a ser utilizados.

Para eliminar um cluster, consulte [Eliminar um cluster HDInsight utilizando o seu navegador, PowerShell ou o Azure CLI](../hdinsight-delete-cluster.md).

## <a name="next-steps"></a>Passos seguintes

Neste quickstart, você aprendeu a usar Apache Zeppelin para executar consultas apache Hive em Azure HDInsight. Para saber mais sobre as consultas da Hive, o próximo artigo irá mostrar-lhe como executar consultas com o Visual Studio.

> [!div class="nextstepaction"]
> [Conecte-se ao Azure HDInsight e executar consultas de Colmeia Apache usando ferramentas do Lago de Dados para Estúdio Visual](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)
