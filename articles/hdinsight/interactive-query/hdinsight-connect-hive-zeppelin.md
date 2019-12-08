---
title: 'Início rápido: Apache Hive no Azure HDInsight com Apache Zeppelin'
description: Neste guia de início rápido, você aprende a usar o Apache Zeppelin para executar Apache Hive consultas.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: quickstart
ms.custom: hdinsightactive
ms.date: 12/03/2019
ms.openlocfilehash: 915aca0e95fce05f74477b526de047c829c7f512
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/06/2019
ms.locfileid: "74890404"
---
# <a name="quickstart-execute-apache-hive-queries-in-azure-hdinsight-with-apache-zeppelin"></a>Início rápido: executar consultas Apache Hive no Azure HDInsight com o Apache Zeppelin

Neste guia de início rápido, você aprende a usar o Apache Zeppelin para executar [Apache Hive](https://hive.apache.org/) consultas no Azure HDInsight. Os clusters de consulta interativa do HDInsight incluem notebooks [Apache Zeppelin](https://zeppelin.apache.org/) que você pode usar para executar consultas interativas do hive.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

HDInsight An cluster de consulta interativa. Consulte [criar cluster](../hadoop/apache-hadoop-linux-tutorial-get-started.md#create-cluster) para criar um cluster HDInsight.  Certifique-se de escolher o tipo de cluster de **consulta interativa** .

## <a name="create-an-apache-zeppelin-note"></a>Criar uma nota do Apache Zeppelin

1. Substitua `CLUSTERNAME` pelo nome do cluster na URL a seguir `https://CLUSTERNAME.azurehdinsight.net/zeppelin`. Em seguida, insira a URL em um navegador da Web.

2. Insira o nome de usuário e a senha de logon do cluster. Na página Zeppelin, você pode criar uma nova anotação ou abrir as anotações existentes. **HiveSample** contém algumas consultas de hive de exemplo.  

    ![Zeppelin de consulta interativa do HDInsight](./media/hdinsight-connect-hive-zeppelin/hdinsight-hive-zeppelin.png)

3. Selecione **criar nova anotação**.

4. Na caixa de diálogo **criar nova anotação** , digite ou selecione os seguintes valores:

    - Nome da observação: Insira um nome para a nota.
    - Intérprete padrão: selecione **JDBC** na lista suspensa.

5. Selecione **criar Observação**.

6. Insira a seguinte consulta de Hive na seção de código e pressione **Shift + Enter**:

    ```hive
    %jdbc(hive)
    show tables
    ```

    ![Consulta interativa do HDInsight Zeppelin executa o Query](./media/hdinsight-connect-hive-zeppelin/hdinsight-hive-zeppelin-query.png)

    A instrução **% JDBC (Hive)** na primeira linha informa ao bloco de anotações para usar o intérprete JDBC do hive.

    A consulta deve retornar uma tabela de Hive chamada **hivesampletable**.

    A seguir estão duas consultas de Hive adicionais que você pode executar em relação a **hivesampletable**:

    ```hive
    %jdbc(hive)
    select * from hivesampletable limit 10

    %jdbc(hive)
    select ${group_name}, count(*) as total_count
    from hivesampletable
    group by ${group_name=market,market|deviceplatform|devicemake}
    limit ${total_count=10}
    ```

    Comparando com o hive tradicional, os resultados da consulta retornam mais rápido.

### <a name="additional-examples"></a>Exemplos adicionais

1. Crie uma tabela. Execute o código abaixo no notebook Zeppelin:

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

1. Carregar dados na nova tabela. Execute o código abaixo no notebook Zeppelin:

    ```hql
    %jdbc(hive)
    LOAD DATA
    INPATH 'wasbs:///example/data/sample.log'
    INTO TABLE log4jLogs;
    ```

1. Inserir um único registro. Execute o código abaixo no notebook Zeppelin:

    ```hql
    %jdbc(hive)
    INSERT INTO TABLE log4jLogs2
    VALUES ('A', 'B', 'C', 'D', 'E', 'F', 'G');
    ```

Examine o [manual de idioma do hive](https://cwiki.apache.org/confluence/display/Hive/LanguageManual) para obter uma sintaxe adicional.

## <a name="clean-up-resources"></a>Limpar recursos

Depois de concluir o início rápido, talvez você queira excluir o cluster. Com o HDInsight, seus dados são armazenados no armazenamento do Azure, para que você possa excluir um cluster com segurança quando ele não estiver em uso. Você também é cobrado por um cluster HDInsight, mesmo quando ele não está em uso. Como os encargos para o cluster são muitas vezes mais do que os encargos de armazenamento, ele faz sentido econômico excluir clusters quando eles não estiverem em uso.

Para excluir um cluster, consulte [excluir um cluster HDInsight usando seu navegador, o PowerShell ou o CLI do Azure](../hdinsight-delete-cluster.md).

## <a name="next-steps"></a>Passos seguintes

Neste guia de início rápido, você aprendeu a usar o Apache Zeppelin para executar Apache Hive consultas no Azure HDInsight. Para saber mais sobre consultas de Hive, o próximo artigo mostrará como executar consultas com o Visual Studio.

> [!div class="nextstepaction"]
> [Conectar-se ao Azure HDInsight e executar consultas de Apache Hive usando o Data Lake Tools para Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)
