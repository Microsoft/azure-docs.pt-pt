---
title: 'Quickstart: Apache Hive in Azure HDInsight com Apache Zeppelin'
description: Neste arranque rápido, aprende-se a usar o Apache Zeppelin para fazer consultas à Apache Hive.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: quickstart
ms.custom: hdinsightactive
ms.date: 12/03/2019
ms.openlocfilehash: 49b576fd511d17616880e5d981fd3f649de797df
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2020
ms.locfileid: "79367929"
---
# <a name="quickstart-execute-apache-hive-queries-in-azure-hdinsight-with-apache-zeppelin"></a>Quickstart: Execute consultas de Hive Apache em Azure HDInsight com Apache Zeppelin

Neste arranque rápido, aprende-se a usar o Apache Zeppelin para executar consultas [apache hive](https://hive.apache.org/) em Azure HDInsight. Os clusters de consulta interativa HDInsight incluem os cadernos [Apache Zeppelin](https://zeppelin.apache.org/) que pode usar para executar consultas interativas da Hive.

Se não tiver uma subscrição Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Um cluster de consulta interativa HDInsight. Consulte [o cluster Create](../hadoop/apache-hadoop-linux-tutorial-get-started.md) para criar um cluster HDInsight.  Certifique-se de escolher o tipo de cluster **de consulta interativa.**

## <a name="create-an-apache-zeppelin-note"></a>Crie uma Nota Apache Zeppelin

1. Substitua-a `CLUSTERNAME` com o nome `https://CLUSTERNAME.azurehdinsight.net/zeppelin`do seu cluster no seguinte URL . Em seguida, introduza o URL num navegador web.

2. Introduza o nome de utilizador e a palavra-passe do seu cluster. A partir da página zeppelin, você pode criar uma nova nota ou abrir notas existentes. **A HiveSample** contém algumas consultas de colmeia.  

    ![HDInsight Interactive Query Zeppelin](./media/hdinsight-connect-hive-zeppelin/hdinsight-hive-zeppelin.png)

3. Selecione **Criar uma nova nota.**

4. A partir do novo diálogo de **nota Criar,** digitar ou selecionar os seguintes valores:

    - Nota Nome: Introduza um nome para a nota.
    - Intérprete predefinido: Selecione **jdbc** da lista de lançamentos.

5. Selecione **Criar Nota**.

6. Introduza a seguinte consulta da Colmeia na secção de código e, em seguida, prima **Shift + Enter**:

    ```hive
    %jdbc(hive)
    show tables
    ```

    ![HDInsight Interactive Query Zeppelin executa consulta](./media/hdinsight-connect-hive-zeppelin/hdinsight-hive-zeppelin-query.png)

    A declaração **%jdbc (colmeia)** na primeira linha diz ao caderno para usar o intérprete Hive JDBC.

    A consulta deve devolver uma mesa de colmeia chamada **colmeia.**

    Seguem-se duas consultas adicionais da Hive que pode correr contra a **colmeia:**

    ```hive
    %jdbc(hive)
    select * from hivesampletable limit 10

    %jdbc(hive)
    select ${group_name}, count(*) as total_count
    from hivesampletable
    group by ${group_name=market,market|deviceplatform|devicemake}
    limit ${total_count=10}
    ```

    Comparando com a colmeia tradicional, os resultados da consulta voltam mais rápido.

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

1. Insira um único disco. Execute o código abaixo no Caderno Zeppelin:

    ```hql
    %jdbc(hive)
    INSERT INTO TABLE log4jLogs2
    VALUES ('A', 'B', 'C', 'D', 'E', 'F', 'G');
    ```

Reveja o manual de [linguagem da Colmeia](https://cwiki.apache.org/confluence/display/Hive/LanguageManual) para obter sintaxe adicional.

## <a name="clean-up-resources"></a>Limpar recursos

Depois de completar o arranque rápido, poderá querer eliminar o cluster. Com o HDInsight, os seus dados são armazenados no Armazenamento Azure, para que possa eliminar com segurança um cluster quando não estiver a ser utilizado. Também é cobrado por um cluster HDInsight, mesmo quando não está a ser utilizado. Uma vez que as taxas para o cluster são muitas vezes mais do que as taxas de armazenamento, faz sentido económico apagar clusters quando não estão em uso.

Para eliminar um cluster, consulte [Eliminar um cluster HDInsight utilizando o seu navegador, PowerShell ou o Azure CLI](../hdinsight-delete-cluster.md).

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, aprendeu a usar Apache Zeppelin para executar consultas apache hive em Azure HDInsight. Para saber mais sobre as consultas da Hive, o próximo artigo irá mostrar-lhe como executar consultas com o Visual Studio.

> [!div class="nextstepaction"]
> [Ligue-se ao Azure HDInsight e execute consultas apache hive usando ferramentas de data lake para estúdio visual](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)
