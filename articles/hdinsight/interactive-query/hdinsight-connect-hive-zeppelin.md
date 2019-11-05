---
title: 'Início rápido: Apache Hive no Azure HDInsight com Apache Zeppelin'
description: Neste guia de início rápido, você aprende a usar o Apache Zeppelin para executar Apache Hive consultas.
keywords: hdinsight, Hadoop, Hive, consulta interativa, LLAP
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: quickstart
ms.date: 05/06/2019
ms.author: hrasheed
ms.openlocfilehash: 36d9e9b34deb4bc6cd5f599cfe2d09a12f680730
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73494293"
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

## <a name="clean-up-resources"></a>Limpar recursos

Depois de concluir o início rápido, talvez você queira excluir o cluster. Com o HDInsight, os dados são armazenados no Storage do Azure, pelo que pode eliminar um cluster em segurança quando este não está a ser utilizado. Também lhe é cobrado o valor de um cluster do HDInsight mesmo quando não o está a utilizar. Uma vez que os custos do cluster são muito superiores aos custos do armazenamento, faz sentido do ponto de vista económico eliminar os clusters quando não estiverem a ser utilizados.

Para excluir um cluster, consulte [excluir um cluster HDInsight usando seu navegador, o PowerShell ou o CLI do Azure](../hdinsight-delete-cluster.md).

## <a name="next-steps"></a>Passos seguintes

Neste guia de início rápido, você aprendeu a usar o Apache Zeppelin para executar Apache Hive consultas no Azure HDInsight. Para saber mais sobre consultas de Hive, o próximo artigo mostrará como executar consultas com o Visual Studio.

> [!div class="nextstepaction"]
> [Conectar-se ao Azure HDInsight e executar consultas de Apache Hive usando o Data Lake Tools para Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)
